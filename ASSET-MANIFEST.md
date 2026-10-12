# SNEAKROW asset manifest

This manifest lists the visual assets the SNEAKROW theme uses, where each one comes from, its
licence, and what the store owner still has to supply. Theme assets sit flat in `assets/`, because
Shopify does not support nested asset folders. Product photography and the merchant's own campaign
imagery are uploaded through the Shopify admin and are not committed to this repository.

## 1. Photographs bundled with the theme (documented placeholders)

Six CC0 (public domain) photographs are bundled so that the storefront is not left with empty
image areas before the merchant's own photography exists. They are **illustrative campaign
placeholders**. They do not show SNEAKROW products, and they never appear on product cards or
product pages. Each one renders only where a section or block has its **Bundled photo** setting
set, and only while no merchant image is chosen. Replace them with your own photography before
launch by picking an image in the theme editor or setting **Bundled photo** to "None".

All six were obtained from Wikimedia Commons, where they are published under **CC0 1.0** (public
domain dedication; originally released on Unsplash under CC0). Attribution is not required but is
recorded here. Before use, each photo was checked at full resolution for visible brand marks.
Where small third-party trademark patches were visible, they were retouched out, which CC0
permits, so no brand is implied.

| Name | Files in `assets/` | Used in | Source (creator, page) | Licence | Edits | Status |
| --- | --- | --- | --- | --- | --- | --- |
| `street` | `sneakrow-photo-street-{1200,1800,2400}.jpg` (3:2), `sneakrow-photo-street-portrait-{600,750,900,1200}.jpg` (4:5) | Home hero; Sneaker Edit campaign | Nadine Shaabana, [File:Nadine Shaabana 2017 (Unsplash).jpg](https://commons.wikimedia.org/wiki/File:Nadine_Shaabana_2017_(Unsplash).jpg) | CC0 1.0 | Two tongue-label trademark patches retouched out; resized and cropped | Placeholder, replace before launch |
| `wire` | `sneakrow-photo-wire-{1000,1600}.jpg`, `sneakrow-photo-wire-portrait-{600,750,900,1200}.jpg` | Home "Move different." editorial campaign | Josh Calabrese, [File:Hosier Lane sneakers (Unsplash).jpg](https://commons.wikimedia.org/wiki/File:Hosier_Lane_sneakers_(Unsplash).jpg) | CC0 1.0 | Resized and cropped | Placeholder |
| `lifestyle` | `sneakrow-photo-lifestyle-portrait-{480,720,960}.jpg` | Home "Find your style": Lifestyle tile; Sneaker Edit card | Nick Miller, [File:Sneaks1 (Unsplash).jpg](https://commons.wikimedia.org/wiki/File:Sneaks1_(Unsplash).jpg) | CC0 1.0 | Resized and cropped | Placeholder |
| `running` | `sneakrow-photo-running-portrait-{480,720,960}.jpg` | Running tile; Sneaker Edit card | William Stitt, [File:The Runner (Unsplash).jpg](https://commons.wikimedia.org/wiki/File:The_Runner_(Unsplash).jpg) | CC0 1.0 | Small apparel logo retouched out; resized and cropped | Placeholder |
| `basketball` | `sneakrow-photo-basketball-portrait-{480,720,960}.jpg` | Basketball tile; Sneaker Edit card | Steven Wei, [File:Outdoor basketball court (Unsplash).jpg](https://commons.wikimedia.org/wiki/File:Outdoor_basketball_court_(Unsplash).jpg) | CC0 1.0 | Resized and cropped | Placeholder |
| `retro` | `sneakrow-photo-retro-portrait-{480,720,960}.jpg` | Retro tile; Sneaker Edit card | Manki Kim, [File:Simple Sneakers (Unsplash).jpg](https://commons.wikimedia.org/wiki/File:Simple_Sneakers_(Unsplash).jpg) | CC0 1.0 | Resized and cropped | Placeholder |

Technical notes:

- The files total about 2.2 MB across 25 JPEGs, saved as progressive mozjpeg at quality 70.
- `snippets/bundled-photo.liquid` renders a `<picture>` with `srcset` and `sizes`. It serves a
  landscape crop on desktop and a 4:5 crop below 750px, so phones get an intentional crop, not a
  squeezed one.
- Every image carries explicit `width` and `height` to prevent layout shift.
- The hero photo loads eagerly with `fetchpriority="high"` when the hero is the first section.
  All other bundled photos lazy-load.
- Alt text comes from `content.bundled_photo_alt.*` in `locales/en.default.json`. Tile and card
  photos are decorative (`alt=""`) because the collection title is the link text.

## 2. Other theme assets

| Element | Source | Notes |
| --- | --- | --- |
| Wordmark (header, footer, © line) | Rendered text: `snippets/wordmark.liquid`, `blocks/logo.liquid` | Archivo 800 via Shopify Fonts, with an accent-colour mark. The text comes from Theme settings > Logo > **Wordmark text** (currently `SNEAKROW`). Uploading a logo image replaces it. No logo file exists. |
| Typography | Shopify Fonts: Archivo 800/600 and Inter 400/600 | Loaded by Horizon's `snippets/fonts.liquid`; no font files in the repo. |
| Icons (ruler, truck, return, chat bubble, lightning bolt) | Horizon's built-in icon set | Inline SVG with `aria-hidden`. |
| Payment icons | `shop.enabled_payment_types` | Only show providers actually enabled on the store. |
| Placeholder artwork (Shopify SVGs) | `placeholder_svg_tag` | Shown **only inside the theme editor**, never on the storefront. |

## 3. Imagery the store owner must supply

| Where | How to add | Recommended spec |
| --- | --- | --- |
| Product photography (all cards, product pages, search, cart) | Admin > Products > Media | Square or 4:5, consistent background, at least 2048px on the long edge. The first image is the card image; the second shows on hover on desktop. Use only photos of the actual product sold. |
| Homepage hero | Theme editor > Home > Hero > Image 1 (and **Custom mobile media** > Image 1 for a portrait crop) | Desktop 2400×1350 or wider; mobile 1200×1500 (4:5). Keep the top-left area calm for the headline. Then set **Bundled photo** to "None". |
| Editorial campaign "Move different." | Theme editor > Home > Editorial campaign > Image | 1600×2000 (4:5) or larger; street or studio campaign photography |
| Category tiles (Lifestyle, Running, Basketball, Retro) | Collection image (Admin > Collections), or the tile's own image in the theme editor | 1200×1500 (4:5) |
| Brand logos (optional) | Theme editor > Home > Brand list > Brand block > Logo | SVG or PNG on transparent background. **Only logos you are authorised to use.** Without one, the brand name is set in the theme typeface. |
| Sneaker Edit campaign | Theme editor > Sneaker Edit page > Editorial campaign | 1600×2000 |
| Favicon | Theme settings > Logo and favicon | 512×512 PNG |
| Social sharing image | Admin > Online Store > Preferences | 1200×630 |

## 4. Not used on purpose

- No AI-generated imagery, so nothing can be mistaken for an actual product.
- No images or logos copied from other stores or brand websites, and no hotlinked images.
- No brand logos are embedded in the theme. Brand presence comes from the merchant's catalogue
  (vendors) and from logos the merchant is licensed to use.
