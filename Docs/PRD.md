# Product Requirements Document (PRD)
## RasanBook — Monthly Grocery & Kirana Credit Tracker

**Version:** 1.0
**Status:** Draft
**Owner:** Project Maintainer

---

## 1. Overview

RasanBook is a lightweight, offline-first web app that helps households track grocery purchases made on credit ("udhaar") from local kirana/grocery merchants, and the payments made against those purchases. It replaces the common practice of tracking monthly grocery bills in a physical notebook (*bahi khata*) or ad-hoc phone notes.

The name "Rasan" (राशन) refers to household grocery/ration supplies in Hindi/Urdu, and "Book" refers to the traditional ledger book kirana stores and households use to track running credit balances.

## 2. Problem Statement

In many households — particularly in South Asia — groceries are bought on running credit from a trusted local merchant throughout the month, and settled with one or more lump-sum payments. This creates a few recurring pain points:

- It's hard to remember exactly what was bought, when, and from which merchant.
- It's easy to lose track of how much is still owed versus how much has already been paid.
- Unpaid balances (or overpayments/credit) need to carry over from one month to the next, and manual tracking of this is error-prone.
- Sharing a grocery list with a merchant (in person, by phone, or via WhatsApp) is a repeated, manual task.
- Households often deal with more than one merchant (vegetable vendor, kirana store, dairy, etc.) and need per-merchant visibility.

## 3. Goals

- Give users a fast, simple way to log a purchase (items + prices) against a merchant.
- Give users a fast way to record a payment made to a merchant.
- Automatically compute what is due (or in credit) for the current month, including balances carried forward from previous months.
- Let users review purchase and payment history per month.
- Let users manage a list of merchants they regularly buy from, with historical spend per merchant.
- Let users share a grocery list with a merchant via WhatsApp, clipboard copy, or a downloadable image.
- Work fully offline, with no account creation, backend, or internet dependency for core functionality.

## 4. Non-Goals

- This version does not support multi-device sync or cloud backup.
- This version does not support multi-user/shared households (single local data store only).
- This version does not integrate with real payment gateways or banks — "payments" are self-reported records, not transactions.
- This version does not provide analytics/charts/reporting beyond simple per-merchant, per-month totals.
- This version does not support currencies other than INR (₹) formatting.

## 5. Target Users / Personas

**Primary persona — "Household Manager"**
A person (often a parent or homemaker) responsible for the household's day-to-day grocery shopping, who buys on credit from 1–3 regular local merchants and settles the bill monthly.

**Secondary persona — "Budget-conscious individual"**
A person living independently who wants a simple, no-signup way to track monthly grocery spend and dues without adopting a full personal-finance app.

## 6. Key User Stories

1. As a user, I want to add a merchant I regularly buy from, so I can track purchases and payments against them.
2. As a user, I want to build a running list of items I'm buying this trip (with quantity, unit, and price), so I can record the full purchase in one go.
3. As a user, I want to save a completed purchase, so it's added to this month's bill and history.
4. As a user, I want to record a payment I made to a merchant, so my outstanding balance updates.
5. As a user, I want to see, at a glance, how much I still owe (or how much credit I have) for the current month.
6. As a user, I want unpaid balances or overpayments to automatically roll into the next month, so I don't have to calculate this manually.
7. As a user, I want to browse previous months to review what I bought and paid.
8. As a user, I want to edit or delete a purchase I logged incorrectly.
9. As a user, I want to share my current grocery list with a merchant over WhatsApp, as a copyable text, or as a downloadable image — for example, to send ahead of a delivery.
10. As a user, I want to see how much I've spent at each merchant, month by month.
11. As a user, if I accidentally close the app mid-entry, I want my in-progress purchase draft to still be there when I come back.

## 7. Functional Requirements Summary

| # | Feature | Priority |
|---|---------|----------|
| 1 | Add / remove merchants | Must have |
| 2 | Build a purchase draft (multi-item cart) and save it as a purchase | Must have |
| 3 | Auto-calculate purchase total from item prices | Must have |
| 4 | Record a payment against a merchant | Must have |
| 5 | Dashboard: current month bill, paid, and remaining due/credit | Must have |
| 6 | Balance carry-forward across months (dues and credit) | Must have |
| 7 | Month navigation (previous/next) on Dashboard and Purchase views | Must have |
| 8 | Purchase history list per month, with edit and delete | Must have |
| 9 | Payment history modal per month | Must have |
| 10 | Per-merchant historical spend view | Should have |
| 11 | Share grocery list via WhatsApp link | Should have |
| 12 | Copy grocery list as text | Should have |
| 13 | Export grocery list as a downloadable PNG image | Could have |
| 14 | Draft auto-save (persist in-progress purchase) | Should have |
| 15 | In-app confirm/prompt dialogs (no reliance on native browser dialogs) | Should have |

## 8. Success Metrics

Since this is a personal/offline utility rather than a hosted product, success is best measured qualitatively:

- A user can log a full purchase (merchant, date, items, prices) in under 30 seconds.
- A user can determine their current amount due within one glance at the Dashboard.
- Balances carried forward match manual calculation with 100% accuracy.
- No data loss occurs across app reloads (data persists via local storage).

## 9. Assumptions & Constraints

- Single currency: Indian Rupee (₹).
- Single-user, single-device usage; data lives in the browser's local storage.
- No authentication or backend server is required or provided.
- The app is a static HTML/CSS/JS file that can be opened directly or hosted on any static file host (e.g., GitHub Pages).

## 10. Future Considerations (Out of Scope for v1)

- Cloud sync / multi-device support.
- Export/import of data (backup as JSON/CSV).
- Charts and spending trends.
- Splitting payments across multiple purchases within the same merchant/month.
- Reminders/notifications for upcoming or overdue payments.
- Multi-currency support.
