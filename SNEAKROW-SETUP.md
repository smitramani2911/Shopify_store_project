# SNEAKROW — setup, verification and launch checklist

This branch (`sneakrow`) turns the Horizon 4.1.5 theme in this repository into the SNEAKROW
multi-brand sneaker storefront. It is a theme only: it does not create products, collections,
menus, pages or policies. Everything in **Shopify admin setup** below must be done in the admin
of the store that will run SNEAKROW.

## 1. Where this should run

- The theme was built and previewed on the CLI development theme of `i8yvqh-se.myshopify.com`,
  which is the LUMERA store. Nothing was published and the live LUMERA theme was not touched.
- For a real SNEAKROW launch use a **separate Shopify store** (a free Partner development store is
  enough). Search, `/collections/all`, checkout branding, the store name in page titles and
  emails, currency, policies and customer accounts are store-wide and cannot be split between two
  brands in one store. Push this branch to the new store with
  `shopify theme push --store <sneakrow-store>.myshopify.com --unpublished`, then follow the checklist.
- Do not merge `sneakrow` into `main` (LUMERA) and do not publish without an explicit decision.

## 2. Shopify admin setup

Handles must match exactly; the theme templates reference them. Everything is also editable later
in the theme editor.

### Collections (Products > Collections)

| Title | Handle | Used by | Suggested rule |
| --- | --- | --- | --- |
| Shop all sneakers | `sneakers` | "Shop sneakers" CTAs, 404, empty cart | Automated: product type equals `Sneakers` |
| Men | `men` | Shop by category | Automated: tag `men` |
| Women | `women` | Shop by category | Automated: tag `women` |
| New arrivals | `new-in` | Home "New arrivals", announcement link | Automated: tag `new`, sorted newest first |
| Best sellers | `best-sellers` | Home "Best sellers", 404 and cart suggestions | Manual, or automated by tag |
| Trending now | `trending` | Home "Trending now" | Manual |
| Running | `running` | Shop by category | Automated: tag `running` |
| Lifestyle | `lifestyle` | Shop by category, editorial CTA | Automated: tag `lifestyle` |
| Basketball | `basketball` | Shop by category | Automated: tag `basketball` |
| Retro | `retro` | Shop by category | Automated: tag `retro` |
| One collection per brand | brand name | Home "Featured brands" (pick them in the editor) | Automated: vendor equals the brand |

Upload a collection image for each (specs in `ASSET-MANIFEST.md`). `new-in` is used instead of
`new-arrivals` because that handle already belongs to LUMERA on the shared dev store.

### Products

- **Vendor** = the brand. It is shown on product cards, the product page and in the cart.
- **Options**: `Size` (sizes in the brand's own scale). Add `Color` to get swatches on cards and
  the product page; swatch colours come from the Shopify category/colour metafield.
- **Compare-at price** drives the "Sale" badge; the **"New" badge** shows for products created in
  the last 30 days (theme setting) or tagged `new`. Sold-out badges and "In stock / Low stock"
  come from inventory tracking.
- **Media**: square or 4:5 photography; the second image shows on hover.
- Add `men` / `women` / category tags to feed the automated collections above.

### Menus (Online Store > Navigation)

| Menu title | Handle | Items |
| --- | --- | --- |
| SNEAKROW main menu | `sneakrow-main-menu` | Shop All → `/collections/sneakers` (nest Running, Lifestyle, Basketball, Retro and brand collections under it for the mega menu), Men, Women, New Arrivals → `/collections/new-in`, Best Sellers |
| SNEAKROW footer: Shop | `sneakrow-footer-shop` | Shop all, Men, Women, New arrivals, Best sellers, Running, Lifestyle, Basketball, Retro |
| SNEAKROW footer: Help | `sneakrow-footer-help` | Contact, FAQ, Size guide, Shipping policy, Refund policy, Track your order (→ account login) |

The header mega menu is set to "collection images", so collection links under Shop All show their
collection image automatically. Until these menus exist the header shows no links.

### Pages (Online Store > Pages)

| Page | Handle | Theme template to assign | Notes |
| --- | --- | --- | --- |
| About | `about` | `page.about` | Page body = intro; the story and values sections are in the template. Replace the bracketed founding-story placeholder in the theme editor. |
| FAQ | `faq` | `page.faq` | Page body = optional intro; the accordion lives in the template. Replace the bracketed authenticity answer. |
| Size guide | `size-guide` | `page.size-guide` | Page body = the size chart tables. **Keep the handle `size-guide`**: the product page "Size guide" drawer loads this page. |
| Contact | `contact` | `page.contact` | Page body = short intro; the form is Shopify's contact form. |

### Policies (Settings > Policies)

Write shipping, refund, privacy and terms policies. The product page accordion ("Shipping",
"Returns & exchanges"), the FAQ rows and the footer policy list read these directly; rows whose
policy is empty are hidden on the storefront.

### Apps and store settings

- **Search & Discovery** (free Shopify app): enable filters (Availability, Price, Size, Color,
  Brand/Vendor, Product type) and product recommendations. The collection and search pages
  pick them up automatically.
- **Settings > Store details**: store name (appears in `<title>` and the © line), currency and
  markets, shipping rates, payment providers (drives the payment icons).
- **Customer accounts**: on (used for "Where is my order?").
- **Theme settings** (theme editor): Logo and favicon (upload a logo image or keep the text
  wordmark; favicon), Colors > Accent colour, Badges > "New" badge window, announcement text,
  social links (footer utilities block), hero and editorial imagery, Featured brands collections.

## 3. Preview and test

```sh
git switch sneakrow
shopify theme dev --store i8yvqh-se.myshopify.com --port 9292   # or the SNEAKROW store
shopify theme check                                             # 6 pre-existing Horizon warnings only
```

Open `http://127.0.0.1:9292`. Useful views on a store without SNEAKROW pages yet:
`/pages/<any-page>?view=about`, `?view=faq`, `?view=size-guide`.

Theme editor: `shopify theme dev` prints the editor link (press `e`). Empty sections show
placeholders only in the editor; on the storefront they are hidden until a collection is set.

## 4. Verification status (dev theme on the LUMERA dev store, 11 Oct 2026)

Verified = exercised on the rendered storefront (Playwright/Edge, desktop 1440×900 and iPhone 13
emulation) or by rendered-HTML checks. The dev store only has LUMERA products, so product data
checks used those products.

| Item | Status |
| --- | --- |
| Theme Check (`shopify theme check`) | Verified: 6 warnings, all pre-existing in Horizon (header settings count, divider doc params) |
| All templates / section groups valid JSON, every route renders without Liquid errors | Verified |
| Header: wordmark, search, account, cart; mobile drawer opens and closes with Escape | Verified (menu links need the `sneakrow-main-menu` menu) |
| Homepage sections render; empty sections hidden on storefront | Verified |
| No horizontal overflow at 390px on home, collection, product, FAQ, cart | Verified |
| Collection page: filters, sorting, grid density, result count, product cards with vendor, badges, price, quick add | Verified with LUMERA products |
| Product page: gallery with thumbnails, vendor, title, price/compare-at, size buttons, size-guide drawer, stock status, quantity, add to cart, Buy it now, details accordion, recommendations | Verified |
| Variant selection updates URL/price; add to cart opens drawer; cart count; quantity change; subtotal | Verified |
| Checkout hand-off | Implemented, partially verified: the Checkout button navigates to the store domain, which redirected to `/password` because the dev store is password-protected. Check on the launch store. |
| Predictive search and search results | Verified |
| Cart page, 404 page, contact form rendering | Verified rendering; form submission not exercised |
| Newsletter signup (Shopify customer form) | Implemented, not submitted during testing |
| "New" / "Sale" / "Sold out" badges | Verified (New and Sale on LUMERA data; Sold out logic unchanged from Horizon) |
| Store-policy block, accordion rows hiding when empty | Verified on dev store (policies empty → rows hidden); content display needs policies on the launch store |
| Mega menu with collection images, footer menus | Implemented, blocked until menus exist |
| Shop by category / Featured brands / New arrivals / Trending / Best sellers with real products | Implemented, blocked until collections exist |
| Colour swatches on cards and product page | Implemented (Horizon swatches), blocked until products have a Color option |
| Theme editor configuration pass | Not performed (no browser access to the editor from this environment); settings are standard Horizon schemas |
| Non-default locale / RTL | Not tested; new strings have English fallbacks in all locale files |
| Wishlist | Not implemented (not available in Horizon, no app installed) |
| Track your order | Not implemented as a page; footer link points to the customer account |

## 5. Placeholders to complete before launch

- About page story: "[Add your founding story here …]" (theme editor > About page > Story section).
- FAQ: "Are your sneakers authentic?" answer (theme editor > FAQ page > accordion row).
- Announcement bar text ("Find Your Next Move.") – replace with a real shipping/promo message if wanted.
- Social links, hero/editorial imagery, collection images, favicon (see `ASSET-MANIFEST.md`).
- Translations: the new strings (`content.product_badge_new`, `blocks.store_policy.*`, new
  theme-setting labels) were added to the other locale files in English.

## 6. What changed in the theme (summary)

- Settings: `logo_text` (text wordmark), `color_accent` (emits `--color-accent`), "New" badge
  settings; SNEAKROW palette, Archivo/Inter typography, square geometry in `config/settings_data.json`.
- New: `snippets/wordmark.liquid`, `snippets/product-badge-type.liquid`, `blocks/store-policy.liquid`,
  templates `page.about`, `page.faq`, `page.size-guide`.
- Extended: `hide_when_empty` on product-list, collection-list and collection-links; "New" badge in
  `_product-card-gallery` / `card-gallery`; hero skips placeholder art on an opaque background;
  placeholder artwork and placeholder page copy are editor-only (`resource-image`, `_image`, `page`);
  accordion rows hide when their content is empty; header logo / logo block fall back to the wordmark text.
- Rewritten templates: `index`, `collection`, `product`, `search`, `404`, `cart`, `page.contact`,
  `sections/header-group.json`, `sections/footer-group.json`.
