# RasanBook — User Manual

Welcome to RasanBook, a simple app to keep track of your monthly grocery purchases and payments to your local grocery merchants (kirana stores, vegetable vendors, dairies, etc.).

This manual walks through everything you can do in the app.

---

## 1. Getting Started

RasanBook works right in your browser — there's no account to create and nothing to install. Just open the app.

All your data (merchants, purchases, and payments) is saved automatically on your own device, in your browser's local storage. It is **not** sent anywhere else. This means:
- You don't need an internet connection to use the app after it first loads.
- Your data stays private to your device and browser.
- If you clear your browser's site data, or switch to a different browser/device, your history will not carry over (there is currently no backup/export feature).

The app has three main screens, reachable from the bottom navigation bar:
- **Dashboard** — your monthly summary.
- **Purchase** — where you build and save a grocery purchase.
- **Merchants** — manage the list of stores/vendors you buy from.

---

## 2. Adding Your First Merchant

Before logging a purchase or payment, add at least one merchant:

1. Tap the **Merchants** tab at the bottom.
2. Tap **+ Add Merchant**.
3. Type the merchant's name (e.g., "Sharma Kirana") and save.

The merchant will now appear in the merchant dropdowns anywhere in the app. You can also add a new merchant on the fly from the Purchase or Add Payment screens by choosing **+ Add New Merchant** in the merchant dropdown.

To remove a merchant later, go to **Merchants**, and tap **Remove** on their card. This only removes them from the active list — any past purchases or payments recorded under their name are kept.

---

## 3. Logging a Purchase

1. Go to the **Purchase** tab.
2. Choose the **Merchant** you bought from.
3. Choose the **Date** of the purchase.
4. Under **Items**, enter each item's name, quantity, unit (kg, L, pcs, or packet), and price.
   - Tap **+ Add another item** to add more rows.
   - Tap the **✕** on a row to remove it.
5. The **Total Bill** updates automatically as you enter prices.
6. Tap **Save Purchase** when done.

Your purchase will now appear under **Recent Purchases** on the same screen, and the total will be reflected on your Dashboard for that month.

**Tip:** Your in-progress purchase is saved automatically as you type, so if you navigate away or accidentally close the app, your draft will still be there when you come back. Use **Clear / Start New** if you want to discard the current draft.

### Editing or Deleting a Purchase
In the **Recent Purchases** list, each entry has **Edit** and **Delete** buttons:
- **Edit** loads that purchase back into the editor above so you can change it, then save it again.
- **Delete** removes it permanently, after you confirm.

---

## 4. Recording a Payment

When you pay a merchant (in full or in part), record it so your balance stays accurate:

1. From the **Dashboard**, tap **+ Add Payment**.
2. Choose the **Merchant** you paid.
3. Enter the **Amount** you paid.
4. Choose the **Date** of the payment.
5. Tap **Done**.

Your payment is now recorded, and your Dashboard's **Paid** total and **Remaining** amount will update immediately.

To review all payments made in a given month, go to the **Purchase** tab and tap **View Payment History**.

---

## 5. Understanding the Dashboard

The Dashboard gives you a snapshot of the currently viewed month:

- **Bill** — total value of all purchases logged this month.
- **Paid** — total of all payments recorded this month.
- **Remaining** — the amount still due for this month, taking into account:
  - This month's bill and payments, **plus**
  - Any balance carried forward from previous months.

You'll also see helpful notes below the "Remaining" figure:
- *"Includes ₹X carried forward from last month"* — you had an unpaid balance entering this month.
- *"Includes ₹X credit carried forward from last month"* — you had overpaid last month, and that credit has been applied here.
- *"You have ₹X credit for next month"* — you've paid more than you owe this month; that extra will roll forward automatically.

Use the **‹** and **›** arrows above the summary to move between months. This also changes which month you see when you switch to the Purchase tab.

**Note:** Balances always carry forward automatically — you don't need to do anything manually to roll over a due or a credit into the next month.

---

## 6. Reviewing Merchant History

Go to the **Merchants** tab to see, for each merchant, a month-by-month breakdown of how much you've spent there. This is useful for comparing spend across different stores over time.

---

## 7. Sharing a Grocery List

While building a purchase draft on the **Purchase** tab, you can share your item list with someone (e.g., to send to the store ahead of time) without saving it as a completed purchase yet:

1. Add your items in the **Current Purchase** editor (prices are optional for this — quantities and names are what get shared).
2. Tap **Share Grocery List**.
3. Choose how you'd like to share it:
   - **WhatsApp** — opens WhatsApp with your list pre-filled as a message, ready to send.
   - **Image** — downloads a clean PNG image of your list.
   - **Copy Text** — copies the list as plain text to your clipboard, so you can paste it anywhere. If your browser blocks clipboard access, you'll see the text in a box so you can copy it manually.

---

## 8. Frequently Asked Questions

**Q: Will I lose my data if I close the browser tab?**
No. Everything is saved automatically to your browser's local storage as you go.

**Q: Can I use RasanBook on more than one device and have it stay in sync?**
Not currently. Each browser/device keeps its own separate data. There is no cloud sync in this version.

**Q: What happens if I remove a merchant?**
They're removed from the merchant list (so they won't show up when adding a new purchase or payment), but any purchases or payments already recorded under their name stay in your history and calculations.

**Q: I paid a merchant, but bought from them twice in the same month — will the app split the payment between both visits?**
No — currently, a payment is tracked per merchant per month, not per individual purchase. If you visit the same merchant twice in one month, both purchase entries will show the same combined "paid" total for that merchant that month, rather than the payment being split between the two visits.

**Q: Does RasanBook work without an internet connection?**
Yes, for all core features (logging purchases, payments, viewing balances). An internet connection is only needed the first time the page loads (to fetch the app itself and its font), and for the optional WhatsApp sharing option.

**Q: Can I back up or export my data?**
Not in the current version. This is a planned improvement — see the project's `Architecture.md` for notes on this.

---

## 9. Getting Help

If you run into unexpected behavior or have suggestions, please open an issue on the project's GitHub repository.
