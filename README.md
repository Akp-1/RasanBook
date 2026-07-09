# RasanBook 🛒📒

**A simple, offline-first web app to track monthly grocery purchases and payments to your local merchants — no backend, no sign-up, no build step.**

RasanBook replaces the traditional notebook (*bahi khata*) households use to track grocery credit ("rasan") from local kirana stores. Log what you bought, record what you paid, and let RasanBook automatically carry your balance — due or credit — into next month.

> 💡 **Privacy by design:** RasanBook stores everything locally in your browser. No account, no server, no data collection.

---

## ✨ Features

- **Dashboard** — see this month's bill, amount paid, and what's still due (or your credit) at a glance.
- **Automatic carry-forward** — unpaid dues or overpaid credit roll into next month automatically.
- **Purchase tracking** — build an itemized cart (name, quantity, unit, price) per merchant visit, with an auto-calculated total.
- **Payment tracking** — record payments per merchant and date; view full payment history per month.
- **Merchant management** — maintain your list of regular stores/vendors, with per-merchant, per-month spend history.
- **Draft auto-save** — your in-progress purchase is saved as you type, so you never lose it mid-entry.
- **Share your grocery list** — send it via WhatsApp, copy as text, or download as an image, before you even finish checking out.
- **Zero setup** — a single static HTML file. Open it locally or host it anywhere.

---

## 📱 Screens

| Dashboard | Current Purchase | Merchants |
|---|---|---|
| Monthly bill, paid, and remaining due/credit, with carry-forward notes | Build your cart, save purchases, browse history | Manage merchants and see per-merchant spend |

*(Add screenshots here once available — see [`docs/User Manual.md`](docs/User%20Manual.md) for a full walkthrough of each screen.)*

---

## 🚀 Getting Started

RasanBook is a single self-contained HTML file — there is nothing to install and no dependencies to fetch beyond a web font.

### Option 1 — Open it directly
Download or clone this repository and open `index.html` in any modern browser.

```bash
git clone https://github.com/<your-username>/RasanBook.git
cd RasanBook
open index.html      # macOS
# or just double-click index.html
```

### Option 2 — Serve it locally
Some browsers restrict certain APIs (like clipboard access) on `file://` pages. If you run into that, serve it locally instead:

```bash
cd RasanBook
python3 -m http.server 8000
# then visit http://localhost:8000
```

### Option 3 — Host it on GitHub Pages
1. Push this repository to GitHub.
2. In **Settings → Pages**, set the source to the `main` branch, root folder.
3. Your app will be live at `https://<your-username>.github.io/RasanBook/`.

---

## 🧱 Project Structure

```
RasanBook/
│
├── index.html              # The entire application (HTML + CSS + JS)
├── README.md                # You are here
├── LICENSE
└── docs/
    ├── PRD.md                # Product Requirements Document
    ├── SRS.md                 # Software Requirements Specification
    ├── Architecture.md        # Technical architecture & data model
    └── User Manual.md         # End-user guide
```

---

## 📚 Documentation

| Document | Description |
|----------|--------------|
| [`docs/PRD.md`](docs/PRD.md) | Why RasanBook exists, who it's for, and what it does |
| [`docs/SRS.md`](docs/SRS.md) | Detailed functional & non-functional requirements |
| [`docs/Architecture.md`](docs/Architecture.md) | Tech stack, data model, rendering approach, and design decisions |
| [`docs/User Manual.md`](docs/User%20Manual.md) | Step-by-step guide for end users |

---

## 🛠️ Tech Stack

- **HTML5 / CSS3** — no frameworks, no preprocessors.
- **Vanilla JavaScript (ES6+)** — no libraries, no bundler, no build step.
- **`localStorage`** — for all data persistence, entirely client-side.
- **Canvas API** — for generating a downloadable grocery-list image.
- Optional integrations: WhatsApp share link (`wa.me`), Clipboard API.

See [`docs/Architecture.md`](docs/Architecture.md) for full technical details, including the data model and known limitations.

---

## 🗺️ Roadmap

RasanBook is intentionally minimal today. Some natural next steps (see [`docs/Architecture.md`](docs/Architecture.md#12-extensibility-notes) for more):

- [ ] Data export / import (JSON backup & restore)
- [ ] Per-purchase (not just per-merchant) payment allocation
- [ ] Spending charts / trends
- [ ] Multi-device sync
- [ ] Payment due reminders

Contributions and ideas are welcome — feel free to open an issue.

---

## 🤝 Contributing

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/my-feature`).
3. Since the app is a single static file with no build step, you can test changes just by opening `index.html` in a browser.
4. Commit your changes and open a Pull Request.

Please keep the project dependency-free (no frameworks/build tools) unless there's a strong reason to introduce one — this is a core design goal of RasanBook.

---

## 📄 License

This project is licensed under the MIT License — see [`LICENSE`](LICENSE) for details.

---

## 🙏 Acknowledgements

Built for households who track their monthly "rasan" (grocery) credit the traditional way — and wanted a faster, digital version of the same trusted notebook.
