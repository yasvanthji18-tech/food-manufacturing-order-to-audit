# Demo walkthrough under eight minutes

## Preparation

Start with a fresh database and leave the app at the sign-in screen. Use a narrow browser window or phone viewport for the two salesman steps. The seed already contains the fictional Retailer A and Distributor B customers and opening balances. The flour receipt and production run are entered during the walkthrough.

## Script

| Time | Account and action | Show |
| --- | --- | --- |
| 0:00–0:35 | Intro | Explain the single ledger and role-specific views. |
| 0:35–1:10 | Admin · Inventory | Record the 60 kg flour purchase at ₹45/kg. Point out the receipt source reference. |
| 1:10–2:20 | Production · Production | Enter the 40 kg flour, 10.5 L oil, 1.2 kg spice, 194 pouches including six damaged, nine cartons, 188 good packets, ₹1,200 labour and ₹600 energy. Post the run. |
| 2:20–2:55 | Production · Run details and Inventory | Show expected 200 versus actual 188, the −6% yield alert, +5% oil alert, ₹5,855 run cost and ₹31.14 unit cost. Show stock movements. |
| 2:55–3:35 | Salesman A · Overview | In the mobile layout, select Retailer A and submit 150 Murukku packets. Show order receipt and status. |
| 3:35–4:35 | Admin · Orders and invoice | Review the order, show price/quantity approval, create the GST invoice, verify ₹7,500 + ₹187.50 CGST + ₹187.50 SGST, then record dispatch separately. Download/open the PDF invoice. |
| 4:35–5:15 | Salesman B · Overview | Submit a 120 packet Distributor B order. Show the shortage of 32 packets and no invoice being created. |
| 5:15–5:45 | Admin · Invoices & payments | Record ₹4,000 on Retailer A. Show ₹3,875 outstanding. |
| 5:45–7:20 | Auditor · Audit reports | Select the activity date range. Review outward GST, production variance, stock summary, receivables ageing, customer/product sales; download CSV and print the report to PDF. |
| 7:20–7:50 | Wrap-up | State local run command, demo role credentials and known deployment limitations. |

For an offline-order segment, sign in as a salesman, disconnect the test browser from the network, submit an order, reconnect and press **Sync queued orders**. The order remains on that device until the server accepts it.
