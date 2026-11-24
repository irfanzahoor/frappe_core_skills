---
name: frappe-erpnext-modules
description: Core ERPNext module flows - selling, buying, stock, manufacturing and accounts. Use when building or debugging an ERP flow, when stock or ledger balances look wrong, or when deciding which document owns a change.
---

# ERPNext Module Flows

## Document status

Every transactional document has `docstatus`: `0` = Draft (editable),
`1` = Submitted (final, locked), `2` = Cancelled. State changes happen by
submitting or cancelling the owning document — never by setting fields or
writing to the database directly. To correct a submitted document, cancel it
and amend it; amending creates a new draft linked by `amended_from`.

## Selling

Quotation → Sales Order → Delivery Note → Sales Invoice → Payment Entry.

- A Delivery Note moves stock out; a Sales Invoice books the sale.
- An invoice can be created from the order without a delivery (service sales).
- Returns use a Sales Invoice with `is_return = 1` against the original.

## Buying

Material Request → Purchase Order → Purchase Receipt → Purchase Invoice → Payment Entry.

- A Purchase Receipt moves stock in and books the stock value.
- A Purchase Invoice books the supplier liability; it can arrive before or
  after the receipt.
- Returns use a Purchase Invoice with `is_return = 1`.

## Stock

- A **Stock Ledger Entry (SLE)** is created automatically when a stock
  transaction is submitted (Delivery Note, Purchase Receipt, Stock Entry).
  **Never insert SLE rows directly.**
- Stock Entry types: Material Issue, Material Receipt, Material Transfer,
  Manufacture, Repack, Material Transfer for Manufacture.
- Stock balances live on the **Bin** (per Item + Warehouse): `actual_qty`,
  `projected_qty`, `reserved_qty`. Read quantities from Bin or from a stock
  report — never by summing SLE rows in Python.
- Wrong balance? Use **Stock Reconciliation** to correct it, with a posting
  date and a reason. Never edit Bin or SLE by hand.

## Manufacturing

BOM → Work Order → Stock Entry (Manufacture) → finished goods in stock.

- The BOM lists raw materials and operations for one finished item.
- A Work Order tracks the required raw materials and the produced quantity.
- The Manufacture Stock Entry consumes raw materials and adds finished goods.
- Track time with Job Cards / Timesheets where the process needs it.

## Accounts

- A **GL Entry** is created automatically when an invoice, payment or journal
  is submitted. **Never insert GL rows directly.**
- Payment Entry records money in/out against invoices; Journal Entry is for
  manual adjustments between accounts.
- Balances come from the General Ledger report, not from summing GL Entry in
  code — and never from a document's own totals after submit.
- Every transaction posts to a Company and a Cost Center; a document without
  the right company posts to the wrong books.

## Golden rules

1. Business state changes only through submit/cancel of the owning document.
2. Never insert Stock Ledger Entry or GL Entry directly.
3. Never edit a submitted document's fields — cancel and amend instead.
4. Quantities come from Bin / reports; money comes from the ledger reports.
5. One document, one job: a Delivery Note moves stock, an Invoice books money.
