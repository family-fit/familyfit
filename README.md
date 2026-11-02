# Family Fit: online shop

*সচেতন জীবন, সুস্থ পরিবার* (Aware living, healthy family)

Family Fit is a mobile-first online shop. It sells baby toothbrushes, a newborn package and upcoming 3D fridge magnets (pre-order). It is a **static website**: one HTML file plus image and video folders. There is no server, database or build step.

See [PLAN.md](PLAN.md) for suggested improvements and the roadmap: GitHub Pages, custom domain, Messenger orders and admin login.

---

## Folder structure

```
Familyfit/
├── index.html                 # The whole app: HTML + CSS + JavaScript (≈87 KB)
├── assets/
│   ├── images/
│   │   ├── brand/             # logo.png (header), logo-footer.jpg
│   │   ├── cute-dinosaur/     # d1–d5.jpg, thumb.jpg
│   │   ├── icecream-soft-brush/
│   │   ├── giraffe/
│   │   ├── laura-bear/
│   │   ├── baby-rabbit/
│   │   ├── potato-duoli-do/
│   │   ├── meimei-rabbit/
│   │   ├── newborn-baby-package/
│   │   └── fridge-magnets/    # thumb-*.jpg (card), full-*.jpg (product page)
│   └── videos/                # one .mp4 per product
├── README.md                  # This file
└── PLAN.md                    # Improvements and roadmap
```

> Images and videos used to be embedded inside `index.html` as base64, which made it 9.2 MB. They now live in `assets/`, so the page loads in about 87 KB and the browser caches each image separately.

## How to run it locally

Open `index.html` in a browser. Or, to behave exactly like the live site:

```bash
python -m http.server 8000
# then open http://localhost:8000
```

## How the code is organised (inside `index.html`)

| Section in `<script>` | What it holds |
|---|---|
| **1. SETTINGS** | `PREORDER_PRICE`, `DISTRICTS`, `DELIVERY` fees/days, `CONTACT` (phone, WhatsApp, email, Facebook), `SHOW_RATINGS` |
| **2. PRODUCT DATA** | `ASSET` (image/video paths), `PRODUCTS` (built with `mk(...)`), `HIGH_SELECTION`, `SUPER_DEALS`, `CATEGORIES`, `UPCOMING` (built with `mkUp(...)`) |
| **3. HELPERS** | `$`, `esc` (HTML escaping), `taka` (৳ formatting), SVG icons, placeholder image |
| **4–5. STATE & VIEWS** | `viewHome`, `viewProduct` (gallery + video), `viewOrder` (colour/quantity), `viewCart`, `viewCheckout`, `viewOrderPage`, `viewMe`, `viewInfo` (About/FAQ/Returns/Privacy/Terms) |
| **Admin (prototype)** | `viewAdminOrders` (status changes), `viewAdminProducts` (hide/show, out of stock), `viewAdminForm` (add/edit products with photo upload) |
| **6. ROUTER** | Hash routes: `#/`, `#/product/<slug>`, `#/order/<slug>`, `#/cart`, `#/checkout`, `#/placed/<no>`, `#/track/<no>`, `#/me`, `#/messages`, `#/info/<page>`, `#/admin[...]` |

### Where data is saved

Everything is saved in the visitor's browser (`localStorage`):

| Key | Contents |
|---|---|
| `ff_cart` | Cart lines `{slug, color, q}` |
| `ff_orders` | Orders placed on this device |
| `ff_products` | Products added in Admin |
| `ff_hidden` | Product slugs hidden in Admin |
| `ff_stock` | Out-of-stock overrides |

⚠️ **This means orders never reach Family Fit.** An order exists only on the customer's phone. PLAN.md, Phase 2, fixes this.

## Common edits

**Change a price or delivery fee:** edit `PRODUCTS` or `DELIVERY` near the top of the `<script>`.

**Add a product:**
1. Make a folder `assets/images/<product-slug>/` and put the photos in it (square `thumb.jpg` ≈ 480 px, gallery photos ≈ 800 px wide).
2. Add the paths to `ASSET`, e.g. `xy1:"assets/images/<slug>/xy1.jpg"`.
3. Add a line to `PRODUCTS`: `mk(9,"<slug>","Name",99,{colors:[...], images:[{src:ASSET.xy1, alt:"…"}], thumb:ASSET.xyT, description:"…"})`.

**Change contact details:** edit `CONTACT`. WhatsApp must be `880` plus the number, with no `+`.
