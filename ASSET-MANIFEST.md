# SNEAKROW asset manifest

Inventory of the visual assets the SNEAKROW theme uses, where each one comes from, and what
the store owner still has to supply. Theme assets live flat in `assets/` (Shopify does not
support nested asset folders); campaign imagery and product photography are uploaded through
the Shopify admin, not committed to this repository.

## Assets added to the repository

None. The SNEAKROW build adds no image, font or media files to `assets/`.

| Element | Source | Notes |
| --- | --- | --- |
| Wordmark (header, footer) | Rendered text, `snippets/wordmark.liquid` + `blocks/logo.liquid` | Archivo 800 via Shopify Fonts, with an accent-colour mark. Text comes from the **Wordmark text** theme setting (`settings.logo_text`, currently `SNEAKROW`). No logo image file exists; uploading one in Theme settings > Logo replaces the text automatically. |
| Typography | Shopify Fonts: Archivo (800/600), Inter (400/600) | Loaded by Horizon's `snippets/fonts.liquid`; no font files in the repo. |
| Icons (`shoe`, `ruler`, `lock`, `chat_bubble`, `lightning_bolt`) | Horizon's built-in `icon` block set | Inline SVG, `aria-hidden`. |
| Payment icons | `shop.enabled_payment_types` via Horizon's `payment-icons` block | Appear only for payment providers enabled on the store. |
| Placeholder artwork (apparel SVGs) | Shopify `placeholder_svg_tag` | Now shown **only inside the theme editor**. On the storefront, sections without media render colour blocks or are hidden (`hide_when_empty`). |

## Imagery the store owner must supply (via Shopify admin)

| Where | How to add | Recommended spec |
| --- | --- | --- |
| Homepage hero (image or video) | Theme editor > Home > Hero > Image 1 / Video 1 (optional Image 2, mobile variants) | 2400×1350 or larger, campaign photography of sneakers; keep the lower-left clear for the headline, or switch the content alignment. Until an image is set the hero is a typographic composition on the brand black. |
| Shop-by-category tiles (Men, Women, Running, Lifestyle, Basketball, Retro) | Admin > Products > Collections > *collection* > Collection image | 1200×1500 (4:5). Without an image the card falls back to the collection's first product image, then to an off-white tile with the title. |
| Featured brand tiles | Collection image on each brand collection | 1200×1200 (1:1). Only use brand marks you are authorised to use; a lifestyle shot of the brand's product is a safe alternative. |
| Editorial campaign block (homepage) | Theme editor > Home > Editorial > left group > Background media | 1600×1600 or larger. Currently a lime colour block with a statement. |
| About page visual | Theme editor > About page > Story section > left group > Background media | 1600×1600. Currently a lime colour block. |
| Product photography | Admin > Products > *product* > Media | Square or 4:5, consistent background. Product cards use a square crop; the product gallery is square with thumbnails. The second image is shown on hover. |
| Favicon | Theme settings > Logo and favicon > Favicon | 512×512 PNG. |
| Social sharing image | Admin > Online store > Preferences > Social sharing image | 1200×630. |

## Not used on purpose

- No stock or AI-generated sneaker photography was added: nothing authorised exists in the
  repository or the store, and the brief forbids unlicensed brand imagery.
- No brand logos are embedded in the theme; brand presence comes from collections the
  merchant creates and images they are licensed to use.
