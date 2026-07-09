# Software Requirements Specification (SRS)
## RasanBook

**Version:** 1.0
**Status:** Draft
**Conforms loosely to:** IEEE 830 structure

---

## 1. Introduction

### 1.1 Purpose
This document specifies the functional and non-functional requirements for RasanBook, a single-page web application for tracking household grocery purchases and payments made to local merchants on a running credit basis.

### 1.2 Scope
RasanBook runs entirely client-side in a web browser. It allows a user to:
- Maintain a list of merchants.
- Record grocery purchases (itemized) against a merchant and date.
- Record payments made to a merchant.
- View computed balances (amount due or credit) per month, including amounts carried forward from prior months.
- Review and edit historical purchase and payment data.
- Share a grocery list externally (WhatsApp, clipboard, image download).

All data is persisted locally in the browser via the Web Storage API (`localStorage`). There is no server-side component in this version.

### 1.3 Definitions, Acronyms, and Abbreviations

| Term | Definition |
|------|------------|
| Merchant | A store or vendor the user buys groceries from (e.g., a kirana store). |
| Purchase | A single recorded shopping visit: a merchant, a date, and one or more line items with prices. |
| Payment | A single recorded amount paid to a merchant on a given date. |
| Draft | An in-progress, unsaved purchase being built on the Current Purchase screen. |
| Month key | Internal identifier for a calendar month, in `YYYY-MM` format. |
| Due | Amount still owed by the user for a given month (or carried overall balance). |
| Credit | A negative due — i.e., the user has overpaid and that amount is credited to the next month. |
| Carry-forward | The process of rolling a month's ending balance into the next month's opening balance. |

### 1.4 Intended Audience
Developers maintaining or extending RasanBook, and technical reviewers evaluating the system design.

### 1.5 Overview
Section 2 describes the product context and constraints. Section 3 details specific functional and non-functional requirements. Section 4 describes external interface requirements. Section 5 lists data requirements.

---

## 2. Overall Description

### 2.1 Product Perspective
RasanBook is a standalone, static, client-side web application. It has no backend server, no database server, and no third-party API dependency for its core functionality (the WhatsApp share feature opens an external `wa.me` link, and image sharing uses the browser's native Canvas API).

### 2.2 Product Functions (Summary)
- Merchant management (create, list, remove).
- Purchase entry (multi-item cart, save, edit, delete).
- Payment entry.
- Automatic bill/due/credit calculation with cross-month carry-forward.
- Purchase and payment history browsing by month.
- Grocery list sharing (WhatsApp / copy / image export).

### 2.3 User Classes and Characteristics
There is a single user class: the household member managing grocery purchases. No technical expertise is required. The interface is optimized for mobile-sized viewports (simulating a phone frame) but works in any modern browser.

### 2.4 Operating Environment
- Any modern desktop or mobile web browser with JavaScript enabled and support for `localStorage`, the Canvas API, and (optionally) the Clipboard API.
- No installation, build tools, or server runtime required.

### 2.5 Design and Implementation Constraints
- Pure HTML, CSS, and vanilla JavaScript — no external JS frameworks and no build step.
- A single external resource dependency: the "Inter" font, loaded from Google Fonts.
- All application state is held in a single in-memory JavaScript object and persisted as a single JSON blob under one `localStorage` key.

### 2.6 Assumptions and Dependencies
- The browser's `localStorage` is available and not disabled/cleared by the user or browser privacy settings.
- The device has a way to open `wa.me` links (for WhatsApp sharing) — otherwise this feature has no effect other than opening a browser tab.

---

## 3. Specific Requirements

### 3.1 Functional Requirements

#### FR-1: Merchant Management
- FR-1.1: The system shall allow the user to add a new merchant by name.
- FR-1.2: The system shall prevent duplicate merchants (case-insensitive match on name).
- FR-1.3: The system shall allow the user to remove a merchant from the active list.
- FR-1.4: Removing a merchant shall not delete historical purchases or payments already recorded against that merchant name.
- FR-1.5: The system shall display, per merchant, the total purchase amount for each month in which purchases exist.

#### FR-2: Purchase Draft (Current Purchase)
- FR-2.1: The system shall let the user select a merchant (from the existing list or by adding a new one inline) for the current draft.
- FR-2.2: The system shall let the user select a date for the current draft, defaulting to today's date if the currently viewed month is the current calendar month, or the first day of the viewed month otherwise.
- FR-2.3: The system shall let the user add, edit, and remove line items in the draft. Each item has: name, quantity, unit (kg, L, pcs, packet), and price.
- FR-2.4: The system shall auto-calculate and display the running total bill as the sum of item prices while the draft is being edited.
- FR-2.5: The system shall persist the draft automatically (to local storage) on every change, so it survives a page reload or accidental navigation.
- FR-2.6: The system shall allow the user to clear the current draft after confirmation.

#### FR-3: Saving a Purchase
- FR-3.1: The system shall require a valid merchant selection before saving a purchase.
- FR-3.2: The system shall require a valid date before saving a purchase.
- FR-3.3: The system shall require at least one item with a non-empty name before saving a purchase.
- FR-3.4: The system shall compute the purchase total as the sum of all item prices (missing/invalid prices treated as 0).
- FR-3.5: On save, the system shall add the purchase to the month (derived from the purchase date) purchase list, add the merchant to the merchant list if not already present, and clear the draft.
- FR-3.6: Purchases within a month shall be displayed most-recent-date first.

#### FR-4: Editing and Deleting Purchases
- FR-4.1: The system shall allow the user to edit an existing purchase from the history list; editing shall load that purchase's data back into the draft editor and remove it from the saved history until re-saved.
- FR-4.2: The system shall allow the user to delete an existing purchase after explicit confirmation.

#### FR-5: Payments
- FR-5.1: The system shall allow the user to record a payment consisting of: merchant, amount, and date.
- FR-5.2: The system shall reject a payment with no merchant selected.
- FR-5.3: The system shall reject a payment with an amount that is not a positive number.
- FR-5.4: The system shall reject a payment with an invalid date.
- FR-5.5: The system shall file the payment under the month derived from the payment date.
- FR-5.6: The system shall display a chronological payment history for the currently viewed month.

#### FR-6: Balance Calculation
- FR-6.1: For each month, the system shall calculate the total bill as the sum of that month's purchase totals.
- FR-6.2: For each month, the system shall calculate the total paid as the sum of that month's payment amounts.
- FR-6.3: The system shall calculate each month's opening balance as the closing balance of the immediately preceding month that has data, defaulting to 0 for the earliest month.
- FR-6.4: The system shall calculate each month's closing balance as: opening balance + total bill − total paid.
- FR-6.5: If the closing balance for the currently viewed month is positive, the system shall display it as an amount "due."
- FR-6.6: If the closing balance for the currently viewed month is negative, the system shall display a remaining due of ₹0 and separately indicate the credit amount available for the next month.
- FR-6.7: If a month has a positive opening balance (carried-forward due), the system shall display a note indicating the carried-forward amount.
- FR-6.8: If a month has a negative opening balance (carried-forward credit), the system shall display a note indicating the carried-forward credit amount.
- FR-6.9: Per-purchase "paid" amount displayed in history shall be computed as the sum of all payments in that month attributed to the same merchant (payments are not split across multiple visits to the same merchant within a month).

#### FR-7: Navigation
- FR-7.1: The system shall provide a bottom navigation bar to switch between Dashboard, Current Purchase, and Merchants views.
- FR-7.2: The system shall provide previous/next month controls on both the Dashboard and Current Purchase views, keeping the viewed month synchronized between them.

#### FR-8: Sharing a Grocery List
- FR-8.1: The system shall allow the user to share the current draft's item list (name, quantity, unit) via a WhatsApp deep link pre-filled with the list as text.
- FR-8.2: The system shall allow the user to copy the current draft's item list to the clipboard as plain text, with a fallback manual-copy dialog if clipboard access is unavailable.
- FR-8.3: The system shall allow the user to generate and download a PNG image of the current draft's item list.
- FR-8.4: The system shall prevent sharing an empty list and inform the user accordingly.

#### FR-9: In-App Dialogs and Feedback
- FR-9.1: The system shall use in-app modal dialogs for confirmations and text prompts rather than native browser `confirm()`/`prompt()` dialogs.
- FR-9.2: The system shall show transient toast notifications for validation errors and confirmations (e.g., "Copied to clipboard!").

#### FR-10: Data Persistence and Migration
- FR-10.1: The system shall persist all application data (settings, merchants, months, draft) as a single JSON object in `localStorage`.
- FR-10.2: The system shall load and parse existing stored data on startup, defaulting to an empty data structure if none exists or parsing fails.
- FR-10.3: The system shall detect legacy (unversioned) data shapes and migrate them to the current versioned schema without data loss where possible.

### 3.2 Non-Functional Requirements

#### NFR-1: Usability
- The interface shall be optimized for a mobile-sized viewport (phone-frame layout) and remain usable on wider desktop viewports.
- Core actions (add payment, save purchase) shall be reachable within 2 taps/clicks from the Dashboard.

#### NFR-2: Performance
- All UI updates shall be near-instantaneous (no network round-trips required for core functionality), since data operations are in-memory and local-storage based.

#### NFR-3: Reliability
- Data entered by the user shall not be lost due to app navigation; the draft purchase auto-saves on every field change.
- Invalid or partial input shall be rejected with a clear message rather than silently corrupting stored data.

#### NFR-4: Portability
- The application shall run unmodified in any evergreen desktop or mobile browser without installation or build steps, as it is a single static HTML file.

#### NFR-5: Privacy & Security
- No data shall be transmitted to any server; all data remains on the user's device in browser local storage.
- The only outbound network interaction is the optional WhatsApp share link (user-initiated) and the initial load of the Google Fonts stylesheet.

#### NFR-6: Maintainability
- Since the app is implemented as plain HTML/CSS/JS in a single file, code shall be organized into clearly commented sections (data layer, dialogs, view switching, per-view logic) to ease future maintenance.

---

## 4. External Interface Requirements

### 4.1 User Interfaces
- A phone-frame-styled single page with three primary views (Dashboard, Current Purchase, Merchants) and several modal overlays (Add Payment, Payment History, Share Grocery List, Confirm dialog, Text Prompt dialog).

### 4.2 Hardware Interfaces
- None. Standard input devices (touch, mouse, keyboard) only.

### 4.3 Software Interfaces
- Web Storage API (`localStorage`) for persistence.
- Canvas API for generating the shareable grocery list image.
- Clipboard API (`navigator.clipboard`) for copy-to-clipboard, with a manual fallback.
- `window.open` to `https://wa.me/` for WhatsApp sharing.

### 4.4 Communications Interfaces
- HTTPS request to Google Fonts (`fonts.googleapis.com`) to load the Inter typeface. No other network communication is required for the app to function.

---

## 5. Data Requirements

See `Architecture.md` for the full data model. In summary, the persisted object includes:
- `version`: schema version number.
- `settings`: reserved for future use.
- `merchants`: array of merchant name strings.
- `months`: object keyed by `YYYY-MM`, each containing `purchases[]` and `payments[]`.
- `draft`: the in-progress, unsaved purchase currently being edited.

---

## 6. Appendix: Traceability to PRD

| SRS Requirement Group | PRD Feature Reference |
|------------------------|------------------------|
| FR-1 | Add/remove merchants |
| FR-2, FR-3, FR-4 | Purchase draft & save, edit, delete |
| FR-5 | Record payment |
| FR-6 | Dashboard due/credit + carry-forward |
| FR-7 | Month navigation |
| FR-8 | Share grocery list |
| FR-9 | In-app dialogs |
| FR-10 | Data persistence |
