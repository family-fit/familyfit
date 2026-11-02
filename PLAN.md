# Family Fit: improvement plan

Status as of 2 November 2026.

## Done

- [x] Moved 65 images and 5 videos out of `index.html` into `assets/images/<product>/` and `assets/videos/`. The page went from **9.2 MB to 87 KB**. All 69 asset links were checked in a browser.
- [x] Fixed a crash: a stray `,,` after *Meimei Rabbit* left a hole in `PRODUCTS`. Admin → *Add 3 sample orders* crashed with `TypeError` when it looked up "Porcelain vase".
- [x] Wrote README.md (code documentation) and this plan.
- [x] Pushed to `github.com/family-fit/familyfit`.
- [x] **Cover section** on the home page: tagline, Shop and Message buttons, product photos, trust points (cash on delivery, delivery time, fee).
- [x] **Filters:** category chips with counts, availability (All / Ready to ship / Pre-order), sort (price, name), removable filter tags and "Clear all". Search now also matches category and description, and combines with the filters.
- [x] **Search bar:** icon, larger touch target, 16 px text (stops iPhone zooming in).
- [x] **New footer on every page:** contact cards first, then the logo, then delivery, how to order and info links. The public "Admin" link was removed; open `/#/admin` directly.
- [x] Domain `familyfitbd.com`: `CNAME` file, canonical link and Facebook preview (Open Graph) tags added.
- [x] Tested at phone (390 px) and desktop (1280 px) widths: no sideways scrolling, no console errors.

---

## What needs improving (review findings)

### Critical
1. **Orders never reach the shop.** Checkout saves the order to the customer's own browser (`localStorage`) and nothing else. Family Fit only hears about it if the customer also taps the WhatsApp button. → Phase 2.
2. **Admin sees only its own device.** The admin panel reads the same `localStorage`, so it can never show real customer orders. Products added in Admin are also invisible to customers. → Phases 3 and 4.
3. **Admin is open to everyone.** The footer links to `#/admin` and there is no login. → Phase 3.

### Important
4. **Images are still heavy (4.4 MB total).** Converting them to WebP at about 80% quality would cut this by roughly 50–70%, which matters on mobile data.
5. **No preview when shared on Facebook.** Add Open Graph tags (`og:title`, `og:description`, `og:image`) and a favicon. Most customers will arrive from Facebook, so this matters.
6. **"per piece" is hard-coded.** The Newborn Baby Package (`unit:"package"`) still shows "per piece" in the bottom order bar, the card label and the order step.
7. **Demo data.** Ratings and sold counts are made up. They are hidden while `SHOW_RATINGS = false`; keep it that way until real reviews exist.

### Nice to have
8. Remove the unused `SOON.messages / cart / me` entries. Those routes are handled earlier.
9. Move product data into `products.json` so it can be edited without touching code.
10. The FAQ says "track your order in the Me tab". That only works on the same phone until Phase 4.

---

## Phase 1: GitHub Pages and custom domain

1. GitHub → `family-fit/familyfit` → **Settings → Pages** → Source: *Deploy from a branch* → `main` / `(root)` → Save.
   The site will be at `https://family-fit.github.io/familyfit/`.
   *Note: free GitHub plans only publish Pages from **public** repositories.*
2. Domain: **familyfitbd.com** (the `CNAME` file is already in the repo).
3. In the domain's DNS settings add:

   | Type | Name | Value |
   |---|---|---|
   | A | @ | 185.199.108.153 |
   | A | @ | 185.199.109.153 |
   | A | @ | 185.199.110.153 |
   | A | @ | 185.199.111.153 |
   | CNAME | www | family-fit.github.io |

4. Back in **Settings → Pages → Custom domain**, enter the domain. GitHub adds a `CNAME` file to the repo. Wait for the DNS check, then tick **Enforce HTTPS**.
5. Optional: verify the domain under the organisation's **Settings → Pages**, so no one else can claim it.

**Needed from you:** add the DNS records above at your domain registrar, and turn on Pages (step 1).

## Phase 2: Send every order to Facebook Messenger

When the customer taps **Place order**:
1. Save the order as now and show the order number.
2. Build the order message: order number, items, colours, quantities, total, name, phone, address and district.
3. **Copy the message to the clipboard** and open `https://m.me/<PAGE_USERNAME>?text=<message>`.
4. The confirmation page shows: *"Messenger is open. If the message box is empty, tap and hold, then Paste, then Send."*
5. Keep **"Send on WhatsApp"** as a second button. WhatsApp fills in the text reliably; Messenger sometimes ignores `?text=`, which is why the clipboard step exists.

Settings to add: `CONTACT.messenger = "<page username>"`.

**Needed from you:** the Facebook Page username (from `facebook.com/<username>`). The `facebook.com/share/1Km1mM22pL/` link works for the footer's Facebook button, but `m.me` needs the username. To find it, open the Page, then **About → Page transparency**, or copy the address bar on a computer.

## Phase 3: Simple admin login (`admin` / `123`)

- `#/admin` shows a login form first. Correct credentials unlock it for the browser session (`sessionStorage`), with a **Log out** button.
- Remove the "Admin (prototype)" link from the public footer. The admin goes to `yourdomain/#/admin` directly.
- ⚠️ **Honest limit:** on a static site, the password sits in the page code, so anyone who opens *View source* can read it. It keeps casual visitors out, not a determined person. Today the admin can only change data on its own device, so the risk is low. Once Phase 4 connects a shared database, the password check **must** move to the server.
- Please choose something stronger than `123`.

## Phase 4 (recommended next): Real shared orders

Messenger messages are easy to miss. For a proper order list that every admin device sees, add a free backend:

| Option | Cost | Effort | Notes |
|---|---|---|---|
| **Google Sheet + Apps Script** (recommended) | Free | Low | Each order becomes a sheet row. The team works in Google Sheets on any phone. Admin reads and updates status from the sheet. |
| Firebase (Firestore + Auth) | Free tier | Medium | Real login, live updates, product images in Storage. |
| Supabase | Free tier | Medium | Postgres + Auth. Good if it grows. |

With Phase 4, the admin login moves to the backend (real security), admin-added products appear for all customers, and order tracking works on any phone.

---

## Suggested order of work

1. Phase 1 (hosting): today, once the repo is public.
2. Phase 2 (Messenger) and Phase 3 (admin login): one small update together.
3. Quick fixes: items 4–6 above.
4. Phase 4: when order volume grows.
