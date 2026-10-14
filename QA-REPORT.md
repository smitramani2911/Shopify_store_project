# SNEAKROW QA report

Test date: 12 October 2026. Branch: `sneakrow`. Theme: Horizon 4.1.5 (confirmed in
`config/settings_schema.json`). Environment: Shopify CLI development theme #131083337774 on
`i8yvqh-se.myshopify.com`, served locally at `http://127.0.0.1:9292`. Nothing was published or
pushed during this pass. The theme was published on 14 October 2026; see `SNEAKROW-SETUP.md`
section 1 for the current state.

## 1. What the test environment can and cannot prove

- At test time the store held 30 products from its previous apparel catalogue and none of the
  SNEAKROW collections, menus, pages or policies. Everything wired to SNEAKROW data is hidden on
  the storefront by design until that data exists.
- To exercise product grids, cards, filters, sorting, the product page and the cart, the tests used
  those apparel products, before the catalogue guard existed. Populated homepage layouts were checked through a **temporary, uncommitted**
  template (`templates/index.qa.json`, viewed at `/?view=qa`). It pointed the same sections at
  existing collections and was deleted after testing.
- The store is password-protected. The checkout button reaches Shopify, which redirects to
  `/password`, so the checkout page itself could not be loaded.
- No newsletter sign-ups, contact messages or orders were submitted, because each one would write
  data into the store.
- Console errors from Shopify platform scripts are counted separately. On `127.0.0.1` they are
  blocked by CORS: `origin_trials`, the shop.app login embed, and a Storefront API call. They are
  environmental, not theme errors.

## 2. Automated checks

| Check | Tool | Result |
| --- | --- | --- |
| Theme Check (Liquid, schema, JSON templates, locales, assets) | `shopify theme check` | **Pass**: 0 errors, 6 warnings. All 6 are pre-existing in Horizon: header has 42 settings (limit 40), and the `divider` snippet declares 5 unused doc params. |
| Liquid render errors | HTML scan for `Liquid error` / `translation missing`, re-run after the final changes | **Pass**: 0 on all 16 routes. `/collections/sneakers` and `/does-not-exist` correctly return the SNEAKROW 404 page. |
| Locale parity | Theme Check `MatchingTranslations` | **Pass**: every new key exists in all 57 locale files, with English fallbacks |
| Functional, accessibility and SEO suite | Playwright 1.49 with Microsoft Edge, desktop 1440×900 and iPhone 13 emulation | **51 pass, 0 fail, 3 blocked** (details in section 4) |
| Responsive capture | Playwright, 8 viewports × 15 routes = 120 full-page captures | See section 3 |
| Lighthouse | Lighthouse against the local dev server | See section 5 |

## 3. Responsive testing

Viewports: 320×568, 375×812, 430×932, 768×1024, 1024×768, 1366×768, 1440×900 and 1920×1080. The
four smallest use touch emulation.

Routes:

- home (`/`) and the populated QA home (`/?view=qa`);
- an existing collection and product page;
- search with results (a term matching existing products) and with none (`/search?q=zzqqxx`);
- cart (`/cart`), contact (`/pages/contact`) and 404 (`/does-not-exist`);
- FAQ, About, Size guide, Brands and Sneaker Edit (`/pages/contact?view=faq|about|size-guide|brands|sneaker-edit`);
- privacy policy (`/policies/privacy-policy`).

Each capture checked:

- document and page-wrapper horizontal overflow;
- broken images;
- theme console errors;
- a visual review of the screenshots.

| Viewport | Routes captured | Horizontal overflow | Broken images | Theme console errors (excluding the account-menu notice) |
| --- | --- | --- | --- | --- |
| 320×568 | 15 | 0 | 0 | 0 |
| 375×812 | 15 | 0 | 0 | 0 |
| 430×932 | 15 | 0 | 0 | 0 |
| 768×1024 | 15 | 0 | 0 | 0 |
| 1024×768 | 15 | 0 | 0 | 0 |
| 1366×768 | 15 | 0 | 0 | 0 |
| 1440×900 | 15 | 0 | 0 | 0 |
| 1920×1080 | 15 | 0 | 0 | 0 |

All 120 captures were clean. One capture at 1366×768 hit a test-harness error, a CSP framing error
while injecting the capture stylesheet; it was re-run and passed. A later regression pass, after the
accessibility fixes in section 6, re-checked home, product, collection, search and policy at
320, 375, 768 and 1440: no overflow.

Visual review of the screenshots found these defects, all fixed (see section 6):

- the editorial headline broke mid-word on desktop;
- the editorial photo was too tall on tablets;
- the eyebrow was unreadable on light backgrounds;
- the newsletter section merged into the footer;
- page headers were misaligned with the sections below;
- content stretched across 1920px screens;
- the privacy policy overflowed at 320px.

Full-page screenshots above 990px leave the footer blank. That is a capture artefact, because
Horizon scrolls inside `.page-wrapper`; a native scroll capture confirmed the footer renders
correctly.

## 4. Functional results (Playwright, Edge)

| Area | Check | Result |
| --- | --- | --- |
| Accessibility | First Tab reaches "Skip to content"; visible focus outline; activates `#MainContent` | Pass |
| Accessibility | `prefers-reduced-motion: reduce` stops the ticker animation | Pass |
| Accessibility | Size selection is a real checked input, not colour alone | Pass |
| Home | Hero photo: eager, `fetchpriority="high"`, width/height set, descriptive alt | Pass |
| Home | Editorial photo lazy-loads | Pass |
| Home | Hero CTAs point to `/collections/sneakers` and `/collections/new-in` | Pass. The links 404 on the dev store until the collections exist. |
| Search | Predictive search shows 8 product suggestions with prices | Pass |
| Search | Clear button empties the field; Escape closes the modal | Pass |
| Search | Results page lists products; no-results message for unknown terms | Pass |
| Collection | Real product count ("13 items") and breadcrumbs | Pass |
| Collection | Availability filter updates URL and results; clear-filters control appears | Pass |
| Collection | Price low-to-high sort orders the grid | Pass |
| Collection | Infinite scroll loads the next page (24 → 30 products) | Pass |
| Collection | Filters matching nothing show "No products found" with a clear-filters link | Pass |
| Product | Vendor, title, price, compare-at price, stock status | Pass |
| Product | Size selection updates `?variant=` in the URL | Pass |
| Product | Size guide opens in a dialog; Escape closes it | Pass |
| Product | JSON-LD: one `ProductGroup` (Shopify's format for products with variants), one `BreadcrumbList`, valid JSON | Pass |
| Product | Title, meta description, canonical and `og:title` present | Pass |
| Cart | Add to cart opens the drawer; header count updates to 1 | Pass |
| Cart | Drawer line shows title, size and price; quantity + updates to 2; subtotal Rs. 7,998.00 = 2 × Rs. 3,999.00 | Pass |
| Cart | Remove shows "Your cart is empty" with a continue-shopping link | Pass |
| Cart | Cart page lists the item with its size; checkout button present | Pass |
| Cart | Checkout hand-off reaches Shopify checkout | **Blocked**: store password page (`/password`) |
| Forms | Newsletter email has a label, is required, and rejects invalid input client-side | Pass |
| Forms | Contact fields all labelled; email `required` and type `email` | Pass |
| Forms | Newsletter and contact submission | **Blocked**: not submitted, because it would write to the store |
| Links | 25 internal header, footer and template links checked; none broken unexpectedly | Pass |
| Links | `/collections/sneakers`, `/collections/new-in`, `/pages/sneaker-edit` | **Blocked**: 404 until created in Admin |
| Mobile | Menu drawer opens and closes with Escape | Pass |
| Mobile | Touch targets: header icons 44px, size buttons 52px, quantity 44px, add to cart 52px | Pass |
| Mobile | Product cards show vendor, title and price without hover | Pass |

Verified by code reading only, because there is no matching data on the dev store:

- A sold-out variant renders a disabled "Sold out" button, and an unavailable combination a
  disabled "Unavailable" button (`blocks/buy-buttons.liquid`, `snippets/add-to-cart-button.liquid`).
- Colour swatches appear when products have a Color option.
- Shipping and returns rows appear once policies exist.

## 5. Performance

Lighthouse 12.6.1 ran in Microsoft Edge against the **local development proxy**
(`http://127.0.0.1:9292`). That proxy serves files uncompressed and unminified, and its server
response time is 1.0–1.3s. Production Shopify serves the same theme from its CDN with compression,
so the performance numbers below are not production numbers.

| Page | Form factor | Performance | Accessibility | Best practices | SEO | LCP | CLS | TBT |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Home | Mobile (simulated slow 4G) | 48 | 100 | 57 | 92 | 11.5 s | 0.017 | 460 ms |
| Home | Desktop | 79 | 100 | 56 | 92 | 2.8 s | 0.01 | 20 ms |
| Product | Mobile | 34 | 100 | 75 | 100 | – | 0.001 | – |
| Collection | Mobile | 32 | 100 | 57 | 100 | – | 0 | – |

Remaining Lighthouse findings and their causes:

- **SEO 92 on home.** No meta description, because the store's home page description is empty.
  Set it in Admin > Online Store > Preferences.
- **Best practices.** HTTP on localhost, a third-party cookie, and console errors from Shopify
  platform scripts blocked on localhost. All are environmental.
- **Performance opportunities.**
  - The proxy has no text compression and no minification, which accounts for about 1.1 MB of the
    savings Lighthouse reports. Shopify's CDN compresses in production.
  - Lighthouse also flags unused CSS and JS from Horizon's base bundles; those bundles are unchanged
    by this work.
  - The 1.3s server response is the dev proxy.
- **Run Lighthouse again on the launch store** (password removed, real product images) for numbers
  that can be quoted.

Implemented performance measures:

- The hero photo loads eagerly with `fetchpriority="high"` when it is the first section; every other image lazy-loads.
- Images use responsive `srcset`/`sizes` and explicit width and height, so no layout shift.
- Bundled photos are 25–400 KB progressive JPEGs.
- New CSS sits in each section's `{% stylesheet %}`, so Shopify only delivers it where the section
  renders.
- No new JavaScript, libraries or third-party scripts were added.

## 6. Issues found and fixed during this pass

| Issue | Fix |
| --- | --- |
| Section counting through `section.blocks` returned no settings for theme blocks, so sections hid themselves | Sections now capture the rendered `content_for 'blocks'` output and hide only when it is empty; tile numbers use CSS counters |
| "MOVE DIFFERENT." headline broke mid-word in the desktop column | Headline sized with container query units (`14cqi`), `overflow-wrap: break-word` |
| Editorial button had no visible fill on the dark background | Lime accent button with an inverted hover |
| Editorial photo was about 960px tall on tablets | 3:2 crop between 750 and 989px |
| Lime eyebrow text unreadable on light backgrounds | Foreground-colour text with a lime marker |
| Black newsletter section merged into the black footer | Newsletter on off-white |
| Page headers used a narrower column than the sections below (FAQ, About, Size guide, Brands) | Page headers are page-width sections with breadcrumbs |
| Content stretched to 1920px on wide screens | Theme page width set to "narrow" (1440px content) |
| Mobile menu button was only 14px wide | 44×44px minimum touch target |
| Contact email was not `required` | Added `required` |
| Privacy policy overflowed at 320px; Shopify's policy CSS overrode the theme | Higher-specificity container rule, long-word wrapping, smaller policy headings |
| Page-view analytics event logged an error on policy pages (empty template name) | `data-template` falls back to `request.page_type` |
| Product-list "view all" arrow rendered as "92" (escape mangled) | Literal arrow character |
| Lighthouse (product page, accessibility 83): slideshow dots used `aria-selected` on buttons and were 18px | Dots use `aria-current` (JS and CSS updated) with a 24px touch target; accessibility now 100 |
| Lighthouse: recommendation loading skeletons had `aria-label` on role-less divs | Skeletons are `aria-hidden` |
| Lighthouse: mobile sticky add-to-cart button had no accessible name (label hidden with `display: none`) | Label visually hidden instead, so it stays readable to screen readers |
| Lighthouse (collection): product card titles jumped from h1 to heading level 3 | Card titles are level 2 on collection and search-result grids, level 3 under section headings |
| Lighthouse: hero image on phones served a 900px crop where about 720px is needed | Added 750px portrait crops for the hero and editorial photos |
| Footer columns showed bare headings when their menu is missing | Menu block hides on the storefront when its menu has no links |
| An empty collection said "use fewer filters" | Dedicated "No pairs here yet" empty state |
| Empty search fell back to all store products | Fallback set to the `best-sellers` collection (theme setting `empty_state_collection`) |
| FAQ showed a bracketed placeholder answer and lacked payments and availability topics | Placeholder removed; rows for orders, tracking, sizing, shipping, returns, payments, availability and contact |
| About page showed a bracketed founding-story placeholder | Neutral brand copy with no invented facts; founding story listed as a merchant task |
| © line showed the store's old name | Uses the wordmark text setting (falls back to the store name) |

## 7. Known remaining issues and limitations

- **Admin data.** SNEAKROW collections, menus, pages, policies and products do not exist on the dev
  store. Header navigation, footer menus and these sections stay hidden until they do: New
  Arrivals, Find your style, Brands, Most Wanted, Sneaker Edit cards, and the shipping and returns
  items.
- **Previous catalogue.** At test time search and `/collections/all` listed the store's older apparel
  products. The catalogue guard added afterwards (`SNEAKROW-SETUP.md` section 5) keeps them off the
  storefront. The pages `contact`, `faq` and `size-guide`, the privacy policy and the store name
  still need SNEAKROW content in Admin.
- **Account menu.** The header account popover logs a console notice because the
  `customer-account-main-menu` menu does not exist on this store.
- **Bundled photography.** The six CC0 photos are placeholders. Replace them with the merchant's own
  campaign photography.
- **Not tested here:**
  - the checkout page itself;
  - newsletter and contact submissions;
  - the Theme Editor UI, because there is no browser session for the admin;
  - non-English locales and RTL, where new strings fall back to English;
  - real VoiceOver or TalkBack screen-reader sessions;
  - Safari and Firefox engines, since tests used Chromium-based Edge.
- **Transient error.** One "Failed to fetch" page error appeared once during parallel captures and
  did not reproduce in two reruns. It was an aborted request under load.

## 8. Manual Shopify Admin steps still required

See `SNEAKROW-SETUP.md` section 2. In short:

1. Replace the old pages, privacy policy and store name with SNEAKROW content.
2. Add products with brand vendors, Size and Color options, tags and inventory.
3. Create the collections: `sneakers`, `men`, `women`, `new-in`, `best-sellers`, `trending`,
   `lifestyle`, `running`, `basketball`, `retro`, and brand collections.
4. Create the menus: `sneakrow-main-menu` and the three `sneakrow-footer-*` menus.
5. Create the pages: `about`, `faq`, `size-guide`, `contact`, `brands`, `sneaker-edit`.
6. Write the policies.
7. Install Search & Discovery and enable filters.
8. Set the store name, currency, shipping and payments.
9. Remove the storefront password.
10. Review the live theme in the Theme Editor and replace the bundled photos.
