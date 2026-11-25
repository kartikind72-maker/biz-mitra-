# BizMitra V2 Final — Cloud Edition

Responsive business-management web app for Cloudflare Pages, connected to the existing BizMitra Supabase project.

## Included
- Supabase email/password signup, login, reset-password flow and session restore
- User-scoped cloud data (RLS already enabled in the linked Supabase project)
- Business profile, contact details, GSTIN, tax rate, currency, invoice prefix, payment terms and logo
- Customer CRUD; product/service CRUD; stock levels and low-stock thresholds
- Multi-line invoices, four invoice styles, tax-exclusive / tax-inclusive calculations
- Invoice preview, print / browser Save as PDF, share summary
- Partial payments and payment history; invoice status updates
- Manual / WhatsApp / email payment-reminder preparation (user reviews and sends)
- Inventory in/out/adjustment log
- Dashboard totals, six-month revenue/expense graph, cash overview
- GST invoice summary CSV, invoice and expense CSV exports
- Full JSON export and restore
- Dark mode, mobile navigation, responsive layout
- Plans displayed at the agreed prices: Free ₹0, Pro Monthly ₹39, Pro Yearly ₹299, Lifetime ₹999
- Footer: © 2026 BizMitra | A Product of WAYD

## Supabase connection
This package is preconfigured with the BizMitra project URL and its public publishable key. A publishable key is designed for browser use; no service-role or secret key is included. The project already has these tables and RLS policies: profiles, businesses, customers, products, invoices, invoice_items, expenses, subscriptions, inventory_movements, payment_reminders, payment_records.

If signup appears to do nothing, open Supabase Dashboard → Authentication → URL Configuration and add your deployed Cloudflare Pages domain to Site URL / Redirect URLs. Also check Authentication → Providers → Email and the project's email confirmation settings. The app shows a confirmation notice if Supabase requires email verification.

## Deploy to Cloudflare Pages
1. Extract this ZIP.
2. Upload the contents of `BizMitra_V2_Final` to your GitHub repository (or connect the repository to Cloudflare Pages).
3. Build command: leave blank. Build output directory: `/` (the folder containing `index.html`).
4. Deploy. Add the deployed Pages domain to Supabase Auth's allowed redirect URLs.
5. Test signup, email confirmation, login, creating a customer, product, invoice, payment, expense, backup export/restore, and mobile layout.

## Important boundaries (transparent)
- Live payment gateway is **not connected**. The paid plans are informational; requesting an upgrade does not activate a subscription. Do not collect payment manually through this UI or represent a paid plan as active. Live checkout needs a compliant provider account and a server-side payment verification/webhook before subscriptions can be activated.
- Automatic scheduled reminders are not enabled; this app creates a reminder record and opens a prefilled WhatsApp/email composer for the user to review and send.
- Invoice PDF is produced through the browser's Print → Save as PDF flow; it is not a server-generated PDF attachment.
- GST reports are management summaries/CSV exports, not statutory GST returns or tax advice. Verify figures with a qualified accountant before filing.
- Product stock update and movement-log insert are two client requests, not a single database transaction. Review movement history after adjustments.
- Import restore replaces records in this signed-in workspace. Export a backup before restoring.

## Security
Never add a Supabase `service_role` or secret key to this frontend. Keep RLS enabled. Each app row is filtered by `auth.uid()` through the existing owner policies. The connected project's security advisor currently reports leaked-password protection as disabled; enable it in Supabase Auth settings before public launch. Do not share account passwords or authentication codes.


## Barcode + PWA update
- Product records now include a barcode field (also accepts SKU).
- Invoice creation supports camera scanning where the browser supports `BarcodeDetector`, and manual barcode/SKU entry as fallback. Repeated scans of a product increase its invoice quantity. Unknown codes can be used to create a product.
- Install prompt, web manifest, app icons and service-worker app-shell caching are included. The app shell may open offline, but Supabase login and cloud data require an internet connection.

### Database updates
The connected BizMitra Supabase project already has the barcode column/index applied. It also has database-side Free-plan enforcement for invoice/customer/product limits, invoice template selection, and reminder channels. The subscription table is read-only to normal signed-in users; users cannot grant themselves Pro by editing their own plan.

## Plan restrictions (implemented in the app)

**Free**: up to 3 invoices per calendar month, 25 customers and 25 products/services; Classic invoice design only; manual reminders; no camera barcode scan or CSV/GST exports. Full JSON backup remains available on Free.

**Pro Monthly / Pro Yearly / Lifetime**: unlimited invoices, customers and products; all 4 invoice designs; camera barcode scanning; invoice/expense CSV and GST-summary CSV exports; WhatsApp/email reminder preparation.

The app checks these limits before the relevant actions and routes Free users to Plans & billing when a Pro feature is requested. The connected Supabase database also enforces invoice/customer/product caps, Classic-only invoices, and manual-only reminders for Free accounts. Pro status requires an active, non-expired subscription row; normal users have read-only access to subscriptions. Paid checkout is still not connected, so a user cannot activate Pro by clicking Request upgrade. Camera barcode scanning and CSV/GST exports are client-side Pro gates; the camera itself is not a database operation. A future verified payment webhook/admin workflow is required to activate paid subscriptions safely.
