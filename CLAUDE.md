# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A Shopify Online Store 2.0 theme for the Lumera store, based on Shopify's **Horizon 4.1.5** theme (see `theme_info` in `config/settings_schema.json`). The repo is the runnable theme package itself. It has no `package.json`, bundler, build step, or test suite, and files in `assets/` ship to the CDN exactly as written.

**`AGENTS.md` is the authoritative authoring guide.** It covers settings, schemas, Liquid composition, CSS ownership and subsetting, the `Component` JS framework, section rendering and hydration, accessibility, and verification. Read it before making non-trivial changes. This file only adds repo-specific facts that `AGENTS.md` doesn't state.

## Commands

Shopify CLI is installed globally (`shopify` 4.8.x). Run these from the repo root:

```sh
shopify theme dev --store <store>.myshopify.com     # local preview with hot reload on a dev theme
shopify theme check                                 # Theme Check lint (Liquid, schema, locales, assets)
shopify theme check -o json                         # machine-readable output; filter by "check" to isolate one rule
shopify theme check --list                          # list enabled checks (no .theme-check.yml, so defaults apply)
shopify theme push --unpublished                    # upload as a new unpublished theme
shopify theme pull --store <store>.myshopify.com    # sync merchant-edited JSON back from the store
```

JavaScript is type-checked through JSDoc. `assets/jsconfig.json` enables `checkJs` with strict null checks and maps `@theme/*` to `assets/*` and `@shopify/events` to `assets/standard-events.d.ts`. Type errors show in the editor. TypeScript isn't installed locally, so a one-off check needs `npx -p typescript tsc -p assets/jsconfig.json --noEmit`.

## Repo-specific facts

- **No `schemas/` directory.** Section and block schemas are edited directly in the `{% schema %}` JSON inside each Liquid file. There is no generation step.
- **Merchant-owned JSON.** `config/settings_data.json`, `templates/*.json`, and the section groups `sections/header-group.json` and `sections/footer-group.json` hold configuration saved from the theme editor. Edits in the admin overwrite them, so pull before editing them and keep block and section IDs stable.
- **Layout entry point.** `layout/theme.liquid` renders global snippets in `<head>` in this order: `stylesheets`, `fonts`, `scripts` (the import map), `theme-styles-variables`, and `color-palette`. It then renders the header group, `content_for_layout`, the footer group, and the global overlays: `cart-drawer`, `theme-drawer`, `search-modal`, `quick-add-modal`, and `chat-drawer`. Global UI belongs here or in those snippets.
- **JS modules.** These are native ES modules resolved through the import map in `snippets/scripts.liquid`. A new `@theme/*` module needs an entry there, and also a path in `jsconfig.json` if its specifier isn't covered by the wildcard. Ambient types live in `assets/global.d.ts`, `standard-events.d.ts`, `standard-actions.d.ts`, and `view-event-elements.d.ts`.
- **Cart drawer behavior.** `assets/standard-actions-override.js` registers Horizon's cart-drawer behavior against Shopify's standard-actions bundle. Read it before changing cart-drawer behavior.
- **Locales.** `locales/en.default.json` and `locales/en.default.schema.json` are the source of truth. The other ~55 locale files are translations: add new keys to the English defaults, and only touch the others when you're deliberately translating.
- **Internal blocks.** Blocks whose names start with `_` (about 50 of the ~95 in `blocks/`) are internal building blocks used by specific sections. They are not merchant-addable top-level blocks.

## SNEAKROW branch (`sneakrow`)

`main` is the LUMERA ethnic-wear store; `sneakrow` is a separate sneaker storefront built on the same theme. Keep them apart: never merge `sneakrow` into `main` or publish from it without being told to. The Shopify admin checklist is in `SNEAKROW-SETUP.md`, imagery and licences are in `ASSET-MANIFEST.md`, and test results are in `QA-REPORT.md`.

- Theme additions on this branch: `settings.logo_text` (text wordmark, rendered by `snippets/wordmark.liquid` and used by `blocks/_header-logo.liquid` / `blocks/logo.liquid`), `settings.color_accent` (emitted as `--color-accent` in `snippets/color-palette.liquid`), the data-backed "New" badge (`snippets/product-badge-type.liquid`, used by `_product-card-gallery` and `card-gallery`), `blocks/store-policy.liquid` (renders real `shop.*_policy` content), and a `hide_when_empty` setting on product-list, collection-list and collection-links.
- SNEAKROW sections: `category-tiles` (+ `_category-tile`), `brand-list` (+ `_brand`; brands come from `collection.all_vendors`), `editorial-campaign`, `shopping-confidence` (+ `_confidence-item`), and the `breadcrumbs` block. They hide themselves by capturing `content_for 'blocks'` and checking for blank output. `section.blocks` does not expose theme-block settings, so don't count blocks through it.
- Bundled CC0 photos: `assets/sneakrow-photo-*.jpg`, rendered by `snippets/bundled-photo.liquid` through each section's "Bundled photo" setting. They are placeholders and must never be used as product imagery.
- Storefront rule: placeholder artwork and placeholder copy appear only in the theme editor (`request.design_mode`). Keep that behaviour when touching `resource-image`, `_image`, `page`, the hero, or accordion rows.
- Templates reference SNEAKROW collection handles (`sneakers`, `men`, `women`, `new-in`, `best-sellers`, `trending`, `running`, `lifestyle`, `basketball`, `retro`), pages (`about`, `faq`, `size-guide`, `contact`, `brands`, `sneaker-edit`) and menus (`sneakrow-main-menu`, `sneakrow-footer-shop`, `sneakrow-footer-help`, `sneakrow-footer-about`). None of the collections and menus exist on the dev store, so those sections are hidden on the storefront until they do.
- Catalogue guard: `settings.catalog_product_types` (comma-separated product types) is checked through `snippets/catalog-guard.liquid` (per product) and `snippets/catalog-page-state.liquid` (product and collection pages, used by `layout/theme.liquid` and `snippets/meta-tags.liquid`). Any new product loop must apply it, or products from another brand on a shared store will appear. Store-name text uses `settings.logo_text | default: shop.name`.
- Above 990px Horizon scrolls inside `.page-wrapper`, not the window. Browser tests must scroll that element, or override its height, for full-page screenshots.
- New runtime/schema locale keys were added to every locale file with English fallback values; keep that parity or Theme Check reports `MatchingTranslations` errors. The English defaults contain `//` comments, so edit them textually rather than re-serialising them.
