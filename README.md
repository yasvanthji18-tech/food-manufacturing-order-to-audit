# Order to Audit

A local-first operations application for a small packaged-snack manufacturer. One entry at each business source flows into the order queue, invoice, stock ledger, production costing, collections and date-filtered audit exports.

This is the full project in a separate folder from the earlier prototype. It uses a real SQLite database, server-side authentication, transaction boundaries, role checks, CSV/PDF outputs and a responsive web interface. No npm packages or network downloads are required.

## Run the application

Requirements: **Node.js 22.5 or newer**. Node 22.5+ provides the built-in `node:sqlite` database module.

From this folder:

```powershell
npm start
```

Then open <http://127.0.0.1:45671>. The first start creates `data.sqlite` and seeds the benchmark users, customers, product, recipe, opening balances and stock ledger. To create the initial database without starting the web server, run `npm run seed`.

To restart the demo from the beginning, stop the server and remove `data.sqlite`, `data.sqlite-shm` and `data.sqlite-wal` from this project folder; the next start re-seeds opening data. The active database is intentionally ignored by Git.

### Demo accounts

| Role | Username | Password |
| --- | --- | --- |
| Factory admin | `admin` | `admin123` |
| Salesman A | `salesman_a` | `sales123` |
| Salesman B | `salesman_b` | `sales123` |
| Production supervisor | `production` | `prod123` |
| Accountant / auditor | `auditor` | `audit123` |
| Owner | `owner` | `owner123` |

These credentials are seeded for a private local demonstration. The owner or admin can create additional accounts in Masters & Access.

## Run the required benchmark

Use a fresh database if the benchmark has already been completed.

1. Sign in as admin. In **Inventory**, record a flour purchase of **60 kg at ₹45/kg**.
2. Sign in as production. Post one Murukku 200 g run: flour **40 kg**, oil **10.5 L**, spice mix **1.2 kg**, pouches **194** (188 good packets plus 6 damaged pouches), cartons **9**, good output **188**, labour **₹1,200**, gas and power **₹600**. Enter 6 in the pouch damaged field; the total consumed quantity is 194.
3. Sign in as Salesman A and submit Retailer A's order for **150 packets**.
4. Sign in as admin, review and approve the order. The order becomes a posted GST invoice. Open **Orders** again and record dispatch as a separate stock transaction. Download the invoice PDF from **Invoices & payments**.
5. Sign in as Salesman B and submit Distributor B's order for **120 packets**. The system flags a shortage of **32 packets**.
6. Sign in as admin and record **₹4,000** against Retailer A's invoice.
7. Open **Audit reports**, select the date range containing the transactions, and review the sales/GST, production, receivables and stock reports. Download the CSVs or use **Print / Save PDF**.

### Expected benchmark outcomes

| Check | Expected result |
| --- | ---: |
| Flour | 120 kg closing |
| Oil | 19.5 L closing |
| Spice mix | 3.8 kg closing |
| Pouches | 306 closing |
| Cartons | 21 closing |
| Finished Murukku after production | 238 packets |
| Finished Murukku after dispatch | 88 packets |
| Yield variance | −6%, flagged beyond 3% |
| Oil consumption variance | +5%, flagged beyond 3% |
| Retailer A taxable invoice | ₹7,500 |
| CGST / SGST | ₹187.50 each |
| Retailer A invoice total | ₹7,875 |
| Retailer A balance after payment | ₹3,875 |
| Distributor B shortage | 32 packets |

The production run cost uses weighted-average flour valuation. Flour costs ₹43.125/kg after the opening 100 kg at ₹42/kg and the 60 kg receipt at ₹45/kg, so 40 kg costs ₹1,725. The remaining run inputs cost ₹1,470 for oil, ₹240 for spices, ₹485 for 194 pouches and ₹135 for 9 cartons. Add ₹1,800 overhead: total run cost is **₹5,855**, or **₹31.14 per good packet**. The ₹43.125 weighted rate is kept as a rational amount for costing; the ledger displays the nearest paise per-unit rate. Damaged pouch cost is charged to the run. Margin against the ₹50 selling price is about ₹18.86 per packet before other operating costs.

## What is implemented

### Sales and invoicing

- Salesman order entry with customer assignment, multiple products and price-list prices.
- Orders can be queued on the device while offline and synced later. Browser assets are cached as an installable PWA shell.
- Admin quantity and price verification, stock availability checks, rejection reasons and status transitions.
- GST calculation by line and place of supply, FY invoice numbering, CGST/SGST for Tamil Nadu and IGST for other states.
- Invoice PDF download, separate dispatch posting, payment entries and customer balances.
- Full invoice cancellation through a credit note. Posted invoices stay in the database and audit history; returned goods can be received back into finished-goods stock. Credit notes are blocked after collections so the balance cannot silently go negative.

### Production and inventory

- Product, customer, supplier, material, packaging, account and bill-of-material masters.
- Recipe batch yield and tolerance settings, batch-count production runs, per-input planned and actual issue quantities, damaged quantities, good output and scrap.
- Mass and volume conversion between kg/g and litre/ml; finished-good carton orders convert to packet stock through the product's saved pack size, while packaging count units remain distinct.
- Stock balances are calculated from the signed movement ledger. Purchases, opening balances, production issues/output, dispatches, sales returns and adjustments all create source-linked movements.
- Weighted-average cost uses stored purchase rates. Material, labour, power and other run expenses feed cost per finished unit.
- Reorder thresholds, lot and expiry fields on receipts, production yield and per-material variance flags.

### Accounts and audit

- Date-filtered sales register and GST summary, product-wise and customer-wise sales, purchase register, stock ledger/summary, production costing, collections and receivables ageing.
- CSV exports for sales/GST, purchases, stock, production, receivables and credit notes; reports can be printed or saved as PDF from the browser.
- Time-stamped audit entries for orders, approvals, rejection, invoices, receipts, production, dispatch, payments, master creation, credit notes and adjustments.
- Role-specific pages and server-side route checks for owner, admin, salesman, production and auditor.

## Architecture and design choices

Node's built-in HTTP server serves a responsive browser application and JSON API. SQLite is the transaction database; `BEGIN IMMEDIATE` protects each multi-record posting. Monetary values are stored as integer paise, quantities as integer thousandths of the item's stock unit, and tax/cost calculations use integer arithmetic. Invoice numbers, source-document references, movement actors and audit records are persisted in the database.

The client is dependency-free JavaScript and CSS. The local app binds to `127.0.0.1` by default. The database stays in the project folder. Account passwords use Node's scrypt password KDF; browser sessions are `HttpOnly` and `SameSite=Strict` cookies.

See [DESIGN.md](DESIGN.md) for the entity model, ledger rules, costing logic and transaction sequence. See [DEMO.md](DEMO.md) for a timed walkthrough outline.

## Recorded demo

[Play the 77-second demo in browser](https://yasvanthji18-tech.github.io/food-manufacturing-order-to-audit/) or [download the H.264 MP4](DEMO.mp4). It shows the admin, production, salesman and auditor roles, including GST invoicing, the stock shortage, payment, and activity log. A WebM copy is also included as `DEMO.webm`.

## Limits and production hardening

- This is a complete local prototype, not a hosted, production-hardened service. Before exposing it to a network, add HTTPS, a durable session store, session revocation, backups, monitoring, rate limiting, database migration tooling, deployment secrets, account recovery and security review.
- Sessions live in process memory. Restarting the server signs everyone out. The offline PWA cache keeps only the app shell and a local copy of the last signed-in user's demo view; queued orders contain business data in local storage on that device.
- Batch and expiry data are captured on receipts and stock movements, but dispatch does not yet enforce FEFO lot selection.
- Partial dispatch, customer returns after payment, e-invoice/e-way bill, Tally export, GPS route tracking and Tamil interface are not included.
- PDF invoices are generated locally with a minimal embedded Helvetica PDF writer and standard demo seller data. Configure real registered business details and independently validate statutory invoice fields before real-world use.
- The benchmark's example GSTINs and customer details are fictional. Do not replace them with real data unless this application is properly secured and configured.
