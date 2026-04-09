---
title: "Freshwater v3 Changelog"
layout: default
---

{% raw %}
# Changelog

All notable changes to Freshwater v3 will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [3.6.0] - Unreleased

**Development branch:** active work toward this release (not yet tagged).

### Added
- **Multi Column (FW) — slide link & layout:** With **Link URL** set on a slide, the **whole slide** is clickable via a stretched overlay link (nested links stay usable). Section **Carousel** settings add **Pin last content block to slide bottom** (desktop and mobile, default off); with **Make slides same height** on, the last `.fresh-block` in each slide gets `margin-top: auto`. Same-height slides now stretch `.fresh-slide-id-container` in **carousel and grid** layouts. `snippets/0-slide-1.liquid`, `sections/0-multi-column-1.liquid`.
- **Hero (FW) & Hero Carousel (FW) — desktop text box max width:** Under **Hero Box → Desktop settings**, **Text Box Max Width** lets merchants cap the content box with **None**, **Pixels (px)** (0–800, step 8; Shopify range settings allow at most 101 steps), or **Percent (%)** (0–100). **Text Box Width** still sets `width` as a percentage; when a max is set and the value is greater than zero, CSS adds `max-width` at `min-width: 768px` only. Missing or legacy JSON defaults to no max. Hero (FW): `sections/0-hero-1.liquid`. Hero Carousel (FW): slide block schema in `sections/0-hero-2.liquid` and styles in `snippets/0-slide-2.liquid`.
- **Two Column (FW) — video mode:** New **“Use video instead of image?”** toggle (`fresh_two_col_use_video`). When off, the media column always uses images; when on, uploaded videos are used when set. Existing sections without the new key keep prior behavior (video when sources exist). Video picker and autoplay sit behind the toggle in the theme editor.
- **Hero (FW) — video mode:** Same pattern as Two Column — **`fresh_hero_use_video`** replaces the old “Video Settings” toggle label; storefront output respects the toggle with legacy-friendly handling when the setting is unset.
- **Carousel navigation (Multi Column FW & Hero Carousel FW):** Single **“Carousel Navigation”** checkbox per breakpoint replaces separate **Carousel Arrows** and **Carousel Dots** toggles. All arrow- and dot-related controls sit behind that group; `visible_if` uses `!= false` so older JSON without the new keys still shows the controls.

### Removed
- **Legacy marquee section** (`sections/0-marquee-1.liquid`): The old `fresh-marquee` implementation is removed. The scrolling marquee is **`0-marquee-2.liquid` only**, still the section type in JSON. **Marquee (FW)** in the theme editor is this implementation (the “2” was dropped from the display name only). Themes whose JSON still references `0-marquee-1` must be fixed manually: remove the broken section or replace it with **Marquee (FW)** and reconfigure.

### Changed
- **Multi Column (FW) — slide overlay:** The **Overlay** Liquid field has **no** schema default (Shopify does not allow an empty default on Liquid settings); storefront output is wrapped in `<div class="fresh-overlay">` in `snippets/0-slide-1.liquid`. README explains section-wide styling via **Global Settings → Custom Class Names** and `assets/1-client.css.liquid`, while keeping inline positioning examples and the overlay docs anchor `#overlay-positioning-classes`.
- **Accordion & List blocks — SVG icon stroke:** In `snippets/0-block-accordion-1.liquid` and `snippets/0-block-list-1.liquid`, icon `svg path` elements again use **both** `fill` and `stroke` (theme icon color or CSS variable) so icons such as **plus** and **caret** match prior visual weight. **Arrow** and **arrow-circle** are exceptions: paths under `svg.fresh-icon--arrow` and `svg.fresh-icon--arrow-circle` set `stroke: none` and `stroke-width: 0` so filled chevrons are not outlined twice (classes are set on those SVGs in `snippets/0-icon-1.liquid`).
- **Theme typography defaults:** Sub-header and body **letter spacing** (desktop and mobile) now default to **0** in `config/settings_schema.json` (header and standard button letter spacing were already **0**).
- **Accordion block (One Column / Two Column / Hero / Main Product FW):** Question (header) and answer (body) each have separate **desktop** and **mobile** controls for text color, alignment, font size, font weight, and line height. Icon color, text/icon spacing, and margin bottom are unchanged. When a new field is left at default (transparent color, **Inherit** alignment, or `0` for size/weight/line height), the snippet falls back to the previous shared typography settings. Those legacy settings remain in the schema as hidden fields (`visible_if: false`) so existing block JSON is preserved on save.
- **Marquee (FW):** Theme editor label is **Marquee (FW)** (was **Marquee 2 (FW)**); filenames and internal classes (e.g. `fresh-section__marquee-2`) are unchanged.
- **Multi Column (FW) & Hero Carousel (FW) — section schema labels:** Device toggles in the carousel and block groups now use concise **Desktop** / **Mobile** labels under explicit **CAROUSEL** and **BLOCK** headings. Square dividers (**■**) appear between major groups (not between Desktop and Mobile) for quicker scanning in the theme editor.
- **Theme editor separators:** Shortened decorative separator strings so they fit the narrower Shopify section sidebar: black square rows (one **■** removed), colon rows (`:`), and degree-symbol rows (`°`) in affected sections and in **Typography** groups in `config/settings_schema.json`.
- **Hero Carousel (FW) — carousel UI:** **CAROUSEL ARROWS** / **CAROUSEL DOTS** labels removed in favor of the unified **Carousel Navigation** toggle; redundant separators between those blocks removed.
- **Two Column (FW):** Removed duplicate colon separator row directly under **“Use video instead of image?”** (one divider before Link URL remains).

### Fixed
- **Accordion block — icon disappears when the question is opened:** `0-icon-1.liquid` hides the alternate state SVG (`.icon2`) with inline `style="display: none"`, which beat the accordion’s `display: inline` rule. Rules for `.accordion__toggle:checked ~ .accordion__question .icon1` / `.icon2` now use `display: none !important` and `display: inline !important` so the icon swap works when the panel is expanded.

---

## [3.4.3] - 2026-03-19

### Added
- **Metafield Title block:** New block type for single-line titles that support **Connect to dynamic source** (e.g. page title, product title, metafields). Available in One Column (FW), Two Column (FW), Hero (FW), and Main Product (FW) sections. Uses the same styling options as the Header block (style, tag type h1–h6/div/span, colors, alignment, typography). Snippet: `0-block-metafield-title-1.liquid`. On Hero Carousel (FW) and Multi-Column (FW), the Slide block has an optional **Title (dynamic source)** text setting that, when set, shows instead of the multiline Header.
- **Default page template (One Column FW):** The default page template (`templates/page.json`) now uses the **One Column (FW)** section instead of the native Page section. Two blocks are pre-configured: a **Metafield Title** block and a **Body** block whose content is resolved from the current page’s title and content when the template stores the Liquid placeholders `{{ page.title }}` and `{{ page.content }}`. Merchants can add, remove, or reorder blocks (Header, Body, Button, Graphic, Video, etc.) and adjust section settings as with any One Column section.

### Changed
- **Default page template:** Replaced `main-page` section with `0-one-column-1` in `templates/page.json`. Section settings use schema defaults (no overrides in the template).

---

## [3.4.2] - 2026-03-18

### Added
- **Hero (FW) and Hero Carousel (FW) width controls:** Replaced the "Hero image full width" checkbox with desktop and mobile **Width** dropdowns (X Small, Small, Standard, Large, X Large, Full Width, Full Bleed), matching the pattern used in Two Column and other sections. Full Bleed spans edge-to-edge beyond page width; Full Width uses container-fluid within page width (distinct from X Large).

### Changed
- **Hero section layout:** When width is not full-bleed, the hero figure is centered on the page. Hero content box is capped at page-width only when the hero is Full Bleed at that breakpoint; when the hero image is narrower, the text box is constrained to the hero column and positioned using the existing overlay settings within that area. Full Width now uses container-fluid for the figure constraint (consistent with Two Column) so it is visually wider than X Large.

### Fixed
- **Hero image on narrow mobile width (e.g. X Small):** Hero image no longer disappeared or appeared clipped when mobile width was set to X Small or other narrow options. Fixes include: constraining image and picture to the column (min-width: 0, width chain), flex-shrink: 0 and align-self: flex-start so the figure sizes to the image, and on mobile when the content box is set to "above" or "below" (not "overlay"), the box wrapper now stacks in the flex layout instead of overlaying the image so the image remains visible.

---

## [3.4.1] - 2026-02-12

### Fixed
- **List block icon scaling:** List block icons (SVGs) now scale correctly with the "Icon Width" setting. Added `viewBox` to all list-compatible icons in `snippets/0-icon-1.liquid` (checkmark, arrow, caret, plus variants). Updated CSS in `snippets/0-block-list-1.liquid` so SVG icons fill the icon container when icon width is set (uses `body.fresh` specificity, no `!important`).

### Changed
- **Cart drawer CSS:** Replaced `!important` in `assets/0-component-cart-drawer.css` dynamic checkout layout rules with `body.fresh cart-drawer` selector specificity (AI guideline compliance).

---

## [3.4.0] - 2026-02-03

### Added
- **Cart drawer subscription upsell:**
  - New theme setting toggle for “Subscription Upgrade in Slideout Cart”
  - Cart drawer now shows a subscription upgrade button for one-time items with selling plans
  - Button label pulls percentage discounts for single-plan items
  - Added subscription frequency dropdown when multiple plans exist
- **Cart drawer free shipping bar:**
  - New theme setting `fresh_free_shipping_threshold` to control the threshold
  - Progress bar shows remaining amount and completion state
  - Styles use Freshwater color variables in cart drawer

### Changed
- **Cart drawer customization files:**
  - Added `snippets/0-cart-drawer.liquid` for cart drawer markup
  - Added `assets/0-component-cart-drawer.css` for cart drawer styling
  - Cart drawer references updated to use the new `0-` prefixed files
  - Updated cart drawer upsell styling and select control presentation
  - Added guard to ignore subscription select changes in cart quantity validation
- **Header whitespace trimming:**
  - Trimmed header whitespace to fix multiline support

## [3.3.0] - 2026-01-27

### Added
- **Modal System Auto-Wrap Feature:**
  - Added `freshInitModalAutoWrap()` function to automatically wrap content blocks with `.fresh-modal-content` class in modal structure
  - Users can now create modals by simply adding `fresh-modal-content` class to HTML/Liquid blocks
  - JavaScript automatically adds modal wrapper, overlay, content container, and close button
  - Eliminates need to manually write modal HTML structure
  - Supports both custom IDs and auto-generated IDs from block settings

### Changed
- **Modal System Improvements:**
  - Improved modal initialization to run auto-wrap on page load
  - Enhanced ID handling to support custom IDs, auto-generated IDs, and class-based IDs
  - Updated modal documentation to include auto-wrap method as recommended approach

### Fixed
- **HTML and Liquid Block ID Handling:**
  - Fixed `snippets/0-block-html-1.liquid` to properly set `id` attribute on wrapper div
  - Fixed `snippets/0-block-liquid-1.liquid` to properly set `id` attribute on wrapper div
  - IDs now correctly use custom `fresh_html_block_unique_id` or `fresh_liquid_block_unique_id` settings
  - Auto-generated IDs now properly formatted (e.g., `freshHTML--123456789`, `freshliquid--123456789`)
  - Enables proper modal targeting and other ID-based functionality
 - **Featured collection block snippet:**
  - Added `snippets/0-block-featured-collection-1.liquid` to encapsulate Dawn’s featured collection rendering with Freshwater block styling.
 - **0- sections integration:**
  - Enabled `featured_collection` blocks in `sections/0-one-column-1.liquid`, `sections/0-two-column-1.liquid`, `sections/0-hero-1.liquid`, and `sections/0-hero-2.liquid`.
  - Enabled `featured_collection_fw` block in `sections/0-main-product.liquid`.

### Changed
- **Featured collection block settings:**
  - Removed header/description settings from featured collection blocks to align with Freshwater block styling.
  - Added desktop/mobile controls for product title, price, and copy styling (color, font size/weight/family, line height, margin-bottom).

### Removed
- **Featured collection block options:**
  - Removed `featured_collection` blocks from `sections/0-marquee-1.liquid`, `sections/0-multi-column-1.liquid`, `sections/0-header.liquid`, and `sections/0-footer.liquid`.

---

## [3.2.0] - 2025-01-27

### Changed
- **Documentation updates:**
  - Updated version references across all documentation files to v3.2.0
  - Updated `.ai-development-guidelines.md` header to reflect v3.2.0
  - Updated `assets/0-freshwater.css.liquid` header comment to v3.2.0

---

## [3.1.0] - 2024-12-12

### Added
- **Footer section group support:**
  - Changed `layout/theme.liquid` from `{% section '0-footer' %}` to `{% sections 'footer-group' %}`
  - Footer now uses section group system (matching header behavior)
  - Enables "Add Section" functionality in Shopify theme editor for footer area
  - Allows adding compatible sections to footer via theme editor

### Changed
- **PDP and Cart button text-transform overrides:**
  - Overridden text-transform styling for product detail page and cart buttons
  - Ensures consistent button text appearance across theme
- **Dawn font color opacity overrides:**
  - Overridden Dawn's default 75% opacity font color settings
  - Improved text readability and contrast throughout theme

### Fixed
- **Cart product options display:**
  - Fixed display issues with cart product options
  - Improved cart item information presentation

---

## [3.1.0] - 2024-12-08

### Changed
- **Dawn 2nd and 3rd button styling:**
  - Dawn's secondary and tertiary buttons now follow Freshwater 2nd and 3rd standard color styling
  - Consistent button appearance across all button types
- **Cart options:**
  - Price display hidden from cart options by default
- **PDP variant pills styling:**
  - Product detail page variant selection pills now use standard button two styling by default
  - Improved visual consistency in product variant selection

---

## [3.1.0] - 2024-12-04

### Changed
- **Dawn PDP buttons alternative color schemes:**
  - Updated Dawn product detail page buttons to work with Freshwater alternative color schemes
  - Improved color scheme compatibility across product pages

---

## [3.1.0] - 2024-12-03

### Added
- **Multi-Column Section Enhancements:**
  - **"Make images square" feature:**
    - New toggle (`fresh_images_square--md` and `fresh_images_square--sm`) in Multi Column section settings
    - Located below "Make slides same height" for both desktop and mobile
    - When enabled, crops all images to a square (1:1) aspect ratio using `object-fit: cover`
    - Images are centered within the square container
    - Default: disabled (off)
    - Uses CSS padding-bottom technique for responsive square containers
  - **"Overlay" feature for slide media:**
    - New Liquid schema field (`fresh_overlay`) in slide block settings under "OVERLAY" section
    - Content is automatically wrapped in a `.fresh-overlay` div
    - Overlay is positioned absolutely over the slide media (image or video)
    - Media wrapper has `position: relative` to contain the overlay
    - Default positioning: center-center
    - **Positioning classes available:**
      - `.fresh-overlay--bottom-left` - Positions overlay at bottom-left of media
      - `.fresh-overlay--bottom-right` - Positions overlay at bottom-right of media
    - Add these classes directly to the `.fresh-overlay` div in your Liquid code
    - See [Freshwater v3 documentation](https://freshwaterdesigns.github.io/freshwater-docs/freshwater-v3/) for detailed usage examples
  - **Two-column media liquid blocks:**
    - Enhanced multi-column section with two-column media block support
    - Improved layout flexibility for media content
- **Multi-Column overlay functionality adjustments:**
  - Refined overlay positioning and display logic
  - Improved overlay rendering performance
  - Enhanced overlay customization options

### Changed
- **Documentation improvements:**
  - Removed all line number references from README (replaced with descriptive text that won't drift over time)
  - Added "Quick Debug Recipe" section for easier debugging workflow
  - Updated all version references from v3.0.0 to v3.1.0
  - Added "CSS Architecture" section documenting Dawn button override strategy
  - Moved changelog to separate CHANGELOG.md file
- **Code improvements:**
  - Refactored `Freshwater.checkCarousels()` to use `window.Freshwater.console()` instead of raw `console.log/warn`
  - Now respects DEBUG_MODE flag (consistent with Freshwater logging guidelines)
  - Added fallback logger for defensive programming
- **CSS improvements:**
  - **Dawn Button Overrides:** All Dawn button styles now use Freshwater's standard scheme 1 styling
    - Colors applied via Dawn's color scheme classes (`.color-scheme-1`, `.color-scheme-2`, etc.)
    - Structural styles (border-radius, padding, font-size) applied within breakpoint media queries
    - Ensures consistent button appearance throughout the theme
    - Override in `assets/1-client.css.liquid` for customizations
- **Settings schema changes:**
  - **Updated `page_width` range:** Changed from `min: 1000, max: 1600, step: 100, default: 1200` to `min: 640, max: 3840, step: 640, default: 1920`
  - New range uses 640px increments: 640, 1280, 1920, 2560, 3200, 3840
  - **Migration required:** Dawn's default value (1200px) is not in the new range, so existing themes must be migrated using incremental steps (see conversion guide)
  - **Finalized settings_data values:** Completed default settings configuration

---

## [3.0.0] - 2024-12-02

### Added
- **Initial Release:**
  - Added debug utility
  - Standardized console logging
  - Performance optimizations
  - Event delegation improvements
  - Comprehensive documentation
  - **Migrated from Slick Carousel (jQuery) to Tiny-Slider (vanilla JS)**
    - Removed jQuery dependency
    - All carousel functionality now uses Tiny-Slider v2.9.4 (UMD build)
    - Using v2.9.4 specifically due to `HierarchyRequestError` bug in v2.9.5+
    - Improved performance and reduced bundle size

### Changed
- **Header and Footer Simplification:**
  - **Removed Mobile Nav custom blocks from header**
    - All custom Mobile Nav blocks (Header, Body, Button, HTML, Liquid, Graphic, Video, Accordion, Rating, List) removed from `sections/0-header.liquid`
    - Mobile drawer now uses default Dawn navigation (or Freshwater footer if enabled)
    - Only `mega_menu_nav_item` block remains for desktop mega menu
    - Updated `snippets/0-header-drawer.liquid` to conditionally render default Dawn navigation or Freshwater footer
  - **Added Mobile Drawer Footer Toggle**
    - Added toggle (`fresh_mobile_drawer_footer_enable`) in header settings under "Mobile logo position"
    - When enabled, replaces mobile drawer menu content with Freshwater footer placeholder
    - Created `snippets/1-header-drawer-freshwater.liquid` for custom mobile drawer footer HTML
    - Customize by editing `snippets/1-header-drawer-freshwater.liquid` directly with HTML
  - **Simplified Freshwater Footer**
    - Removed all custom footer blocks from `sections/0-footer.liquid`
    - Footer now uses simple HTML placeholder (`snippets/1-footer-freshwater.liquid`)
    - Toggle (`fresh_footer_enable`) switches between default Dawn footer and Freshwater placeholder
    - Customize footer by editing `snippets/1-footer-freshwater.liquid` directly with HTML
    - All original Dawn footer settings (newsletter, social, payment, etc.) are preserved

---

[3.6.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.6.0
[3.4.1]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.4.1
[3.4.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.4.0
[3.3.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.3.0
[3.2.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.2.0
[3.1.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.1.0
[3.0.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.0.0


{% endraw %}
