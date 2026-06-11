# CLAUDE.md

Guidance for working in the Settel Store repository.

## What this is

A single-vendor storefront for **Settel** — a home-based prepared-meal business
("No decisions. Just dinner."). Customers browse a weekly menu, add meals to a
cart, and submit an order. Inventory is tracked live and decremented on each
order.

The whole storefront is a **static site** — no build step, no framework, no
package manager. It is plain HTML/CSS/vanilla JS in a handful of files, deployed
to Netlify and fronted by a GoDaddy domain. Data lives in **Supabase** (queried
directly from the browser via its REST API), and order notifications go out via
**Formspree**.

## File map

```
index.html    The customer-facing storefront. ALL markup, CSS, and JS live
              inline in this one file (~1250 lines). This is where ~everything
              happens.
admin.html    Standalone order-management page (yoursite.com/admin.html).
              Reads orders from browser localStorage.
meals.json    Legacy/seed menu data. The live site now reads meals from
              Supabase, not this file (see "Data" below). Keep in mind it may
              be stale.
images/       Meal photos and settel-logo.png. Referenced by Supabase rows.
README.md     Owner-facing setup/operations guide (non-technical).
favicon.*     Site icons.
```

There is **no `src/`, no `node_modules`, no test suite, no CI**. Editing the
site means editing `index.html` directly.

## Architecture & conventions

- **One-file app.** `index.html` contains a `<style>` block (lines ~13–707) and
  a single `<script>` block (starts ~line 852). Match the existing structure:
  CSS grouped by section with `/* ─── SECTION ─── */` banners; JS organized as
  flat top-level `function`s wired up with inline `onclick=` handlers.
- **Vanilla JS only.** No frameworks, no imports, no bundler. Don't introduce
  build tooling or dependencies unless the user explicitly asks.
- **Tabs:** The UI is a tabbed single page (`Menu`, `Shop`, `About`,
  `Reheating`, `Feedback`) driven by `switchTab(name)` toggling
  `.tab-content.active`.
- **Cart:** in-memory `cart` object keyed by item id; helpers `getCartItems`,
  `getCartTotal`, `getCartCount`, `adjustQty`, `updateCartBar`.

## Data (Supabase)

The site reads/writes Supabase directly from the browser using a **publishable
(anon) key** — config is at the top of the `<script>` in `index.html`:

```js
const SUPABASE_URL = 'https://<project>.supabase.co';
const SUPABASE_KEY = 'sb_publishable_...';   // anon/publishable, safe for client
```

Tables in use:
- `meals` — weekly menu items (`loadMeals` → `supabaseGet('meals')`)
- `products` — the Shop tab (`loadProducts` → `supabaseGet('products')`)
- `site_settings` — e.g. the `announcement` banner

Each row has `id, name, description, price, quantity, image, active`. Only
`active=true` rows are fetched. On checkout, `submitOrder` decrements `quantity`
per item via `supabaseUpdate` **before** sending the order.

> Note: `meals.json` and the README describe an older "edit meals.json weekly"
> flow. The live code has moved to Supabase. When in doubt, the code in
> `index.html` is the source of truth — flag the discrepancy rather than
> trusting the README.

## Orders

`submitOrder` (in `index.html`) builds an order object and POSTs it to a
Formspree endpoint (`https://formspree.io/f/...`) for email notification, then
decrements Supabase inventory and shows a confirmation. `admin.html` reads
orders from `localStorage` for at-a-glance review.

## Mobile

Responsive rules are consolidated in a single `@media (max-width: 640px)` block
in `index.html`. The tab bar uses a two-row wrap layout on mobile (Menu/Shop/
About at 33%, Reheating/Feedback at 50%) to avoid label clipping. Keep mobile
overrides in that one block rather than scattering breakpoints.

## Working in this repo

- Make changes directly in `index.html` (or `admin.html`); there is nothing to
  build or compile. Open the file in a browser to verify.
- Preserve the existing inline-everything style and section-comment formatting.
- Secrets: the Supabase key in the client is intentionally the publishable/anon
  key. Never paste a Supabase **service-role** key or any private key into these
  client-side files.
- Deployment is automatic: pushing to `main` triggers a Netlify deploy.
