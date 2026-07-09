# Architecture Document
## RasanBook

**Version:** 1.0

---

## 1. Architectural Overview

RasanBook is intentionally a **single-file, static, client-side web application**. There is no backend, no build pipeline, and no external application dependencies beyond a web font. The entire application — markup, styles, and logic — lives in `index.html`.

```
┌─────────────────────────────────────────────┐
│                  Browser                     │
│                                               │
│   ┌───────────────┐     ┌─────────────────┐  │
│   │  UI Layer      │◄───┤  State (in-mem)  │  │
│   │  (DOM render   │────►  `data` object   │  │
│   │   functions)   │     └────────┬────────┘  │
│   └───────────────┘              │            │
│                                    ▼            │
│                          ┌──────────────────┐  │
│                          │  localStorage    │  │
│                          │  key:            │  │
│                          │  'rasanbookData' │  │
│                          └──────────────────┘  │
└─────────────────────────────────────────────┘
```

This architecture was chosen to keep the app installable-by-copy (drop the HTML file anywhere, or host it on any static file server such as GitHub Pages) and to avoid any server/hosting cost or account requirement for the end user.

## 2. Technology Stack

| Layer | Technology |
|-------|------------|
| Markup | HTML5 |
| Styling | Plain CSS (embedded `<style>` block, no preprocessor, no framework) |
| Logic | Vanilla JavaScript (ES6+), no framework, no bundler |
| Persistence | Browser `localStorage` (Web Storage API) |
| Fonts | Google Fonts ("Inter"), loaded via `<link>` |
| Image export | HTML5 Canvas API |
| External share | `wa.me` WhatsApp deep link, Clipboard API |

There is no package.json, no npm dependency, and no compilation step. The file can be opened directly in a browser (`file://`) or served from any static host.

## 3. Application Structure (within `index.html`)

1. **`<head>`** — meta tags, font import, and a single `<style>` block containing all CSS.
2. **`<body>`**
   - `.phone` container simulating a mobile device frame.
     - `#view-dashboard` — Dashboard view.
     - `#view-purchase` — Current Purchase view (draft editor + history).
     - `#view-merchants` — Merchants view.
     - `.bottom-nav` — persistent bottom navigation bar.
   - Modal overlays (siblings of `.phone`, shown/hidden via a CSS `.active` class):
     - `#paymentModal` — Add Payment form.
     - `#historyModal` — Payment history list.
     - `#sharePopup` — Share Grocery List options.
     - `#confirmModal` — generic confirm dialog (replaces `window.confirm`).
     - `#textPromptModal` — generic text-input dialog (replaces `window.prompt`).
   - `#toast` — transient notification element.
   - `<script>` — all application logic (see Section 5).

## 4. View Routing

There is no URL-based router. All three views exist in the DOM simultaneously; only one carries the `.view.active` class at a time (`display:flex` vs `display:none`). Navigation is handled by `switchView(name)`, which:
1. Removes `.active` from all `.view` elements and adds it to the target view.
2. Updates the active state of the bottom nav buttons.
3. Triggers the appropriate render function for the newly active view (`renderDashboard`, `renderPurchaseHistory` + `hydrateDraftToDOM`, or `renderMerchants`).

Modals are toggled independently via `.modal-overlay.active`, and are not mutually exclusive with the active view.

## 5. Data Layer

### 5.1 Persisted Schema

All state is stored under a single `localStorage` key, `rasanbookData`, as a JSON-serialized object:

```js
{
  version: 1,
  settings: {},
  merchants: ["Sharma Kirana", "Green Valley Dairy", ...],
  months: {
    "2026-07": {
      purchases: [
        {
          id: "1751-...-a1b2",
          merchant: "Sharma Kirana",
          date: "2026-07-05",
          items: [
            { name: "Rice", qty: "5", unit: "kg", price: 350 },
            ...
          ],
          total: 350
        }
      ],
      payments: [
        { amount: 500, date: "2026-07-10", merchant: "Sharma Kirana" }
      ]
    }
  },
  draft: {
    merchant: "",
    date: "",
    items: []
  }
}
```

### 5.2 Loading, Defaulting, and Migration
- `loadData()` reads the raw JSON string from `localStorage`, parses it defensively (catching malformed JSON), and applies structural defaults for any missing top-level keys (`settings`, `merchants`, `months`, `draft`).
- If the parsed object has no `version` field, `migrateOldShape(old)` is invoked to convert a legacy/unversioned data shape (e.g., an older `inventory` array, or purchases without generated IDs) into the current schema, generating IDs and normalizing numeric fields along the way.
- All writes go through `saveData()`, which serializes the entire `data` object back to the single `localStorage` key. There is no partial/incremental persistence — every mutation triggers a full re-save.

### 5.3 In-Memory State
- `data` — the single source of truth, loaded once at startup and mutated directly by feature functions (e.g., pushing into `data.months[key].purchases`).
- `viewMonth` — a `YYYY-MM` string tracking which month is currently being viewed on the Dashboard and Current Purchase screens (independent of "today").
- `idCounter` — a monotonically increasing counter combined with a timestamp and random suffix (`genId()`) to generate unique purchase IDs without a UUID library.

## 6. Core Business Logic

### 6.1 Month Keys
`monthKey(date)` derives a `YYYY-MM` string from a JS `Date`. `shiftMonth(key, delta)` adds/subtracts months from a key, handling year rollover. `monthLabel(key)` renders a human-readable label (e.g., "July 2026").

### 6.2 Balance / Carry-Forward Calculation
Implemented in `computeAllBalances()`:
1. All month keys present in `data.months` are sorted chronologically.
2. Starting from a running balance of 0, for each month in order:
   - `opening[k]` = running balance entering the month.
   - `bill` = sum of that month's purchase totals (`purchaseTotalSum`).
   - `paid` = sum of that month's payment amounts (`paymentsSum`).
   - `closing[k]` = `opening[k] + bill − paid`.
   - The running balance is updated to `closing[k]` for the next iteration.

This produces a simple, single running ledger across all months with data — conceptually equivalent to a chequebook register. A positive closing balance means money is owed; a negative closing balance means the household has overpaid and holds credit for the following month.

### 6.3 Per-Purchase Paid Amount (Known Simplification)
`purchasePaidAmount(md, purchase)` sums all payments in the same month attributed to the same merchant (case-insensitive name match), and applies that combined figure to **every** purchase from that merchant in that month. This is a deliberate simplification: the data model does not link individual payments to individual purchases, only to a merchant within a month. This is documented in-code and should be considered if extending the payment model (see Section 9).

## 7. Rendering Approach

RasanBook does not use a virtual DOM or component framework. Each view has one or more `render*()` functions (`renderDashboard`, `renderPurchaseHistory`, `renderMerchants`) that:
1. Recompute derived values from `data`.
2. Rebuild the relevant DOM subtree's `innerHTML` using template literals.
3. Rely on event delegation (listeners bound once on a stable parent element, e.g., `historyContainer.addEventListener('click', ...)`) to handle dynamically-generated buttons (edit/delete actions), rather than re-binding listeners on every render.

All dynamic text inserted via `innerHTML` is passed through `escapeHtml()` to prevent HTML injection from user-entered merchant/item names.

## 8. Key Modules / Function Inventory

| Function | Responsibility |
|----------|-----------------|
| `loadData` / `saveData` / `migrateOldShape` | Persistence layer |
| `genId` | Unique ID generation for purchases |
| `ensureMonth`, `getSortedMonthKeys` | Month bucket access helpers |
| `purchaseTotalSum`, `paymentsSum`, `purchasePaidAmount`, `computeAllBalances` | Financial calculations |
| `switchView`, `shiftViewMonth` | Navigation |
| `renderDashboard` | Dashboard rendering |
| `populateMerchantSelect`, `addMerchant` | Merchant selection & creation |
| `readDraftFromDOM`, `persistDraft`, `hydrateDraftToDOM`, `cartRowHtml`, `recalcCartTotal` | Purchase draft editor |
| `renderPurchaseHistory`, `editPurchase`, `deletePurchase` | Purchase history management |
| `draftListLines`, `generateListImage` | Grocery list sharing |
| `renderMerchants` | Merchants view |
| `showToast`, `showConfirm`, `showTextPrompt` | In-app dialog primitives |

## 9. Known Limitations / Technical Debt

- **Payments are merchant-scoped, not purchase-scoped.** If a merchant is visited more than once in the same month, all their purchases in that month display the same combined "paid" figure. A future schema could link payments to a specific purchase `id` or introduce partial-payment allocation.
- **No data export/import.** Data lives only in the browser's local storage; clearing browser data or switching browsers/devices loses history. Backup/restore (e.g., JSON export/import) is a natural extension.
- **Single global write.** Every save re-serializes the entire data object; this is fine at expected personal-use data volumes but would not scale to large multi-year datasets without change.
- **No automated tests.** Logic is currently verified manually; the pure calculation functions (`computeAllBalances`, `purchaseTotalSum`, etc.) are good candidates for unit tests if the project is extracted into modules.
- **Single-file structure.** Good for portability, but as the app grows, splitting CSS/JS into separate files (still without a build step, via plain `<script src>`/`<link>`) would improve maintainability.

## 10. Security & Privacy Considerations

- No user data ever leaves the device except via the user's own explicit action (WhatsApp share, clipboard copy, or image download).
- User-supplied strings (merchant names, item names) are HTML-escaped before insertion into the DOM to prevent stored/reflected script injection via `innerHTML`.
- There is no authentication because there is no server and no multi-user concept; anyone with access to the device/browser profile has access to the data.

## 11. Deployment

RasanBook requires no server-side runtime. Recommended deployment options:
- **GitHub Pages** — serve `index.html` directly from the repository.
- **Any static file host** (Netlify, Vercel static hosting, S3 + CloudFront, etc.).
- **Local use** — open `index.html` directly in a browser with no hosting at all.

There is no environment configuration, no secrets, and no CI/CD requirement beyond optionally deploying the static file on push.

## 12. Extensibility Notes

If evolving beyond a single-file app, a natural next step would be to:
1. Split `index.html` into `index.html`, `styles.css`, and one or more `.js` modules (data layer, views, dialogs) using ES modules — while still avoiding a build step.
2. Introduce a data export/import feature (JSON download/upload) as a stepping stone toward optional cloud sync.
3. Extract the pure calculation functions into a standalone module with unit tests.
