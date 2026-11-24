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

## [Unreleased]

### Added
- **Theme setting: Load carousel library on every page** (**0 Misc**, on by default). Tiny-Slider (`0-tiny-slider.min.js`, 32 KB) loads on every page from `0-theme-freshwater-1`, whether or not the page has a carousel. Turned off, the layout no longer loads it. `0-freshwater.js` loads it the first time a page needs a Freshwater carousel enabled at the current width, and the carousel sections (`0-hero-2`, `0-multi-column-1`) preload it so init isn't delayed. Pages without carousels (most collection, product, cart and content pages) then make no Tiny-Slider request at all. **Leave it on for stores whose own code calls `tns()`.** Natrava and Avantera both patch Tiny-Slider in `1-fresh-script.js` and expect it to be loaded globally. With the setting on (the default, and the value every upgraded store gets), nothing changes. The Tiny-Slider CSS stays global either way (2 KB), so the stylesheet order doesn't change. (`config/settings_schema.json`, `snippets/0-theme-freshwater-1.liquid`, `assets/0-freshwater.js.liquid`, `sections/0-hero-2.liquid`, `sections/0-multi-column-1.liquid`)

### Changed
- **Carousel init skips pages without carousels before reading the layout.** `freshInitCarouselsTiny()` read `window.innerWidth`, which forces a style recalculation, before checking for carousels. On pages without carousels it now returns first. (`assets/0-freshwater.js.liquid`)

### Fixed
- **Mobile drawer — "Account" and "Search" labels were English-only:** The labels next to the account and search icons in the mobile menu were hard-coded. They now use Dawn's translated strings (`customer.account_fallback`, `general.search.search`), which exist in every locale the theme ships, so they follow the storefront language. English output is unchanged. (`snippets/0-header-drawer.liquid`, `snippets/0-menu-drawer-search-trigger.liquid`)
- **Video block — posters of below-the-fold videos loaded on page load:** Browsers fetch a `<video>` poster as soon as it's set (up to 1600 px wide). Since 3.7.3 the video itself waits until it's needed, but every poster still loaded on page load. In sections below the fold (`section.index > 2`), the poster is now set when the video comes within about one screen of the viewport, so it's ready before it scrolls into view. Sections 1–2, and the theme editor (where `section.index` is nil), still set it straight away, so a hero video's poster isn't delayed. (`snippets/0-block-video-1.liquid`)

---

## [3.7.3] - 2026-11-23

A patch release: performance and video fixes, no settings changes and no merchant JSON migration. Stores upgrading from 3.5.x, 3.6.x or 3.7.x should go straight to 3.7.3. Upgrade notes, detailed in the entries below:

- **Hero Carousel markup:** the slide `<ul>` now sits inside pre-rendered `.fresh-tns-ow > .fresh-tns-mw > .fresh-tns-iw` wrappers, which Tiny-Slider adopts on init (they become `.tns-outer` / `.tns-ovh` / `.tns-inner` as before). Check any `1-` CSS that targets the hero's `<ul>` as a direct child of its column before init.
- **`0-tiny-slider.min.js` is patched again** (wrapper adoption, polyfills removed). Take upstream's file; never replace it with a stock Tiny-Slider build.
- **New asset `0-image-placeholder.svg`:** keep it with the theme, because `0-freshwater.css` now references it.
- **Video blocks load nothing until needed** (`preload="none"`), with the HLS source first. Below-the-fold section videos no longer make requests on page load.
- **Account icon:** if the store has no "Customer account main menu", the icon uses Shopify's default account links without logging an error.

### Fixed
- **Video block — below-the-fold videos loaded on page load, and phones got the full MP4:** `0-block-video-1` built every video with `preload="metadata"`, so each video on the page made a request on page load, even far below the fold. It also output only the 1080p MP4, so phones downloaded the full file instead of an adaptive stream. Videos now use `preload="none"`: the poster covers the first paint and the aspect ratio comes from Liquid, so autoplay videos load when they scroll into view and click-to-play videos load on play. The HLS (`.m3u8`) source now comes first, as in `0-product-video`, so Safari, iOS and Android Chrome get adaptive bitrate, with the MP4 as the fallback. Source URLs only get `https:` added when they're protocol-relative. Prompted by Runamok, where a 43 MB autoplaying section video hurt mobile landing-page views from Meta ads. (`snippets/0-block-video-1.liquid`)
- **`0-freshwater.css` — 69 KB placeholder illustration moved out of the stylesheet:** The `.fresh-image__placeholder` background (the illustration behind graphic blocks that have no image) was a 69 KB data-URI SVG inside `0-freshwater.css`. That's about 22 KB of the file's 26.5 KB compressed size, downloaded by every page in the render-blocking head CSS, though it only shows where a merchant hasn't picked an image. It's now its own asset, `assets/0-image-placeholder.svg`, referenced with `asset_url`. `0-freshwater.css` drops to about 4 KB transferred, and only pages that show a placeholder fetch the SVG. This was Lighthouse's "~25 KB unused CSS" on every page. The placeholder renders pixel-identical. (`assets/0-freshwater.css.liquid`, `assets/0-image-placeholder.svg`)
- **Tiny-Slider — legacy polyfills removed (patched `0-tiny-slider.min.js`):** The library shipped polyfills for `Object.keys` and `Element.prototype.remove`, which every browser the theme supports has had natively for over a decade. Lighthouse flagged the file under "Legacy JavaScript" (estimated 10 KiB). Both are gone (246 bytes). The file no longer appears in that audit, and the audit's estimated savings on freshwater-dev home dropped from 25 KiB to 14 KiB; the rest is Shopify's scripts.
- **Header account icon — console error on stores without a customer account menu:** The header and the mobile drawer both rendered `<shopify-account menu="customer-account-main-menu">`. On a store without that menu, Shopify's `account.js` queried the Storefront API for it on every page and logged `[shopify-account] Menu "customer-account-main-menu" not found` twice, which also cost Lighthouse Best Practices points. The `menu` attribute is now only output when `linklists['customer-account-main-menu']` has links. Stores without the menu get Shopify's default account links as before, with no error and no menu query. Stores with the menu are unchanged. (`sections/0-header.liquid`, `snippets/0-header-drawer.liquid`)
- **Hero Carousel — LCP logged at carousel init instead of first paint (desktop):** The first slide's hero image painted early (eager, `fetchpriority="high"`), but Chrome logged it again as a new LCP entry when Tiny-Slider started, about 1–2 s later on desktop. Tiny-Slider moves the slide list into its wrapper divs on init, and Chrome records a moved image as a new LCP candidate whenever its visible area has grown since its first paint. Two things made it grow:
  - **Pre-init layout:** before init the first slide was a flex row, so its content shrank to the image's size (823 px wide in a 1440 px hero on freshwater-dev) and jumped to full width at init. The first slide now uses the same block / flex-column layout before init as after, so there's no jump. (`sections/0-hero-2.liquid`)
  - **Sub-pixel shifts above the hero:** a fraction of a pixel of movement above the hero between first paint and init (e.g. a font swap) was enough. Hero Carousel now renders Tiny-Slider's outer/overflow/inner wrappers in Liquid (`.fresh-tns-ow > .fresh-tns-mw > .fresh-tns-iw`), and the patched `0-tiny-slider.min.js` adopts them instead of creating its own, so init no longer moves the slides. The patch only applies when those wrappers are present, so other carousels are unchanged. `destroy()` still restores the plain list, and a re-init takes the normal path.

  freshwater-dev home, 1350×940 (Lighthouse desktop): LCP is the hero's first paint, about 0.8–1.0 s instead of 1.8 s. Mobile LCP already happened before init and is unchanged.
- **Product gallery video — buffering and re-downloading:** The variant gallery's video used `video_tag`, which outputs only Shopify's 1080p MP4 (about 2.5 Mbps) with no HLS stream. Leaving the video also deleted the `<video>`, so every return downloaded it again from the start. The video now comes from a new `0-product-video` snippet: a hand-written `<video>` with the HLS (`.m3u8`) source first, which Safari, iOS and Android Chrome play with adaptive bitrate, and the MP4 as the fallback. Its `aria-label` drops the `Value |` variant prefix (`video_tag` used the raw alt). Each video element is created once and kept, so switching to an image only pauses and hides it, and coming back resumes without re-buffering. Hovering or focusing the video thumbnail starts buffering before the click. Ported from hume-supernatural PR #3. (`snippets/0-product-video.liquid`, `snippets/0-product-gallery-variant-1.liquid`, `assets/0-product-gallery-1.js`)

---

## [3.7.2] - 2026-11-23

A patch release: carousel fixes, no settings changes and no merchant JSON migration. Stores upgrading from 3.5.x, 3.6.x or 3.7.x should go straight to 3.7.2.

Upgrade note: **Carousels:** parts of the Tiny-Slider CSS were silently ignored before 3.6.0. 3.6.0–3.7.1 applied them in full, which switched sliders to no-subpixel mode and removed `.tns-outer` padding. This release restores the pre-3.6 behaviour. Clients whose custom carousels broke on 3.6.0–3.7.1 (stacked or blank slides, dots overlapping slides) should be fixed by this release with no client changes. Revert any `!important` workarounds added for it.

### Fixed
- **Carousels — every slider fell back to Tiny-Slider's no-subpixel layout (3.6.0+, patched `0-tiny-slider.min.js`):** On startup, Tiny-Slider checks for subpixel layout support by laying out 70 empty `<div>`s inside `.tns-t-subp2 > .tns-t-ct` and comparing positions. Dawn's `base.css` hides `div:empty`, so the probe divs had no box and the check failed. Every slider then got `tns-no-subpixel` (floated items with `margin-right: -100%`) instead of `tns-subpixel` (inline-block). Before 3.6.0 the probe's helper CSS was invalid SCSS and was dropped, so the check passed by accident. Client carousels styled for subpixel mode stacked every slide or rendered blank on a visitor's first page view. The probe divs now get `display: block` from JS, the same fix as the 3.6.0 width-probe patch, so both probe fixes live in the one patched file. Tiny-Slider caches the result in `localStorage` keyed by user agent, so the cache key now has a `|fw-tPL` suffix. That clears the stale `tPL=false` once for visitors who saw a 3.6.0–3.7.1 store. Found during the Natrava 3.7.1 upgrade.
- **Carousels — `.tns-outer { padding: 0 !important }` overrode client padding (3.6.0+, `0-tiny-slider.css`):** The pre-3.6 file started with `// Version: 2.9.4`, which isn't a CSS comment, so the browser dropped the first rule. The compiled file applied it, and with `!important` client CSS couldn't give `.tns-outer` bottom padding for dots and arrows. The rule is removed. Stores have only ever run without it, and no Freshwater section sets `.tns-outer` padding. `0-tiny-slider.css` is otherwise the official compiled 2.9.4 file.
- **Carousel dot labels (Multi Column and Hero Carousel):** Tiny Slider labelled every dot "Carousel Page N" (and the active one "Carousel Page N (Current Slide)") in hard-coded English, the last untranslated carousel label, and called the dots pages even with **One Dot Per Slide** on. Each dot now says which slide it brings in, in Dawn's translated slideshow wording ("Load slide 3 of 12"), and the active dot has `aria-current="true"`. In page mode the number is the first slide of that page (3 per view: 1, 4, 7, 10 of 12). The English-only "Carousel Pagination" label on the dot wrapper, which screen readers ignore on a plain `div`, is removed. (`assets/0-freshwater.js.liquid`, `snippets/0-theme-dawn-4.liquid`)

---

## [3.7.1] - 2026-11-21

A patch release for client repos: no theme code changes. Stores upgrading from 3.5.x or 3.6.x should go straight to 3.7.1 instead of 3.7.0.

### Fixed
- **GitHub workflows no longer run in client repos.** Client repos pick up `.github/workflows/` through upstream merges, and both workflows ran there too. `prevent-shopify-bot-commits` failed every `shopify[bot]` commit, which on a client whose `main` is GitHub-connected to the live theme is most commits, and `publish-readme-to-docs` failed every push to `main` for lack of the docs token. Neither blocked anything, but client `main` would show red permanently. Both jobs now run only in `freshwaterdesigns/freshwater-v3`.
- **Docs site publishing:** the `publish-readme-to-docs` workflow had failed on every merge since 2026-10-13. `freshwater-docs` only accepts changes through pull requests, and the workflow pushed straight to its `main`. It now pushes a `sync/freshwater-v3-<sha>` branch and opens a PR there, closing any older sync PR it replaces. Merge that PR to publish. The sync also deletes the stale hand-made `freshwater-v3/changelog.md` page (stuck at 3.1.0), and the README's "documentation site" changelog link now points to the generated `freshwater-v3/CHANGELOG` page.

---

## [3.7.0] - 2026-11-20

A minor release: new settings, no merchant JSON migration (no setting, block or section ID was renamed or removed since v3.6.0; five settings were added). Stores still on v3.5.x also pick up everything in 3.6.0, including the header z-index fix for linked heroes over dropdown menus and the video **Show Mute Button** setting. Upgrade notes, detailed in the entries below:

- **Multi Column dots:** carousels switch to one dot per slide. Untick **One Dot Per Slide** to keep one dot per page.
- **Carousel settings moved:** arrow/dot settings are relabelled and reordered, and Multi Column's Slides Displayed / Space Between Slides / Text Max-Width moved into Grid. Values carry over; tell merchants where things went.
- **"Dots" arrow placement:** arrows move outward where the dot row would have overlapped them. Carousels whose dots fit don't move.
- **Video embeds:** YouTube and Vimeo iframes in rich text, HTML and Liquid blocks now fill the column at 16:9. An embed that was deliberately smaller (or a vertical Shorts embed) will change size; check pages that embed video.

### Added
- **One Column (FW): Padding Left / Padding Right** (desktop and mobile, 0–100px, default 0). Insets the content on top of the column's normal gutter. At 0 nothing is output, so existing sections don't change.
- **Responsive video embeds:** YouTube (`youtube.com/embed`, `youtube-nocookie.com/embed`) and Vimeo (`player.vimeo.com`) iframes pasted into rich text, an HTML block or a Liquid block now fill the column at 16:9. No wrapper class needed, and the pasted `width`/`height` are overridden. Other iframes are untouched. Stores using a hand-rolled wrapper (like `.freshwater-video-wrapper`) keep working, and the wrapper can come out.
- **Multi Column (FW): One Dot Per Slide** (`fresh_carousel_dots_per_slide`, on by default). Carousel dots now match the number of slides instead of the number of "pages" of visible slides (6 slides showing 3 at a time used to give 2 dots). Clicking dot *n* moves slide *n* to the front. Uses Tiny Slider's `navAsThumbnails`, so it applies to desktop and mobile together. **Upgrade note:** existing multi-column carousels switch to per-slide dots on upgrade. Untick the setting to get the old behaviour back.

### Changed
- **Carousel settings layout (Multi Column and Hero Carousel):** arrow and dot settings are grouped and renamed so it's clear what each one controls: Arrow Placement / Color / Size / Thickness, Dot Color / Size, then a **POSITION** group with Arrow Vertical Offset, Arrow Horizontal Offset and Dot Vertical Offset (previously "Vertical Alignment", "Horizontal Alignment" and a dots "Alignment" further down). Labels and order only. Setting IDs are unchanged, so saved values carry over.
- **Multi Column (FW):** Slides Displayed, Space Between Slides and Text Max-Width moved from Section Settings into the Grid group, under "Turn on carousel". IDs unchanged.
- **Default homepage:** removed the hard-coded "v3.5.5" line from the Welcome hero in `templates/index.json`. The theme name below it already identifies the theme, and the version line went stale every release. Only affects new installs; existing stores keep their own homepage content.

### Fixed
- **Carousel arrows with "dots" placement no longer overlap the dots.** The arrows sat at a fixed 35% (desktop) / 25% (mobile) from the edge, so a long row of dots (many slides, or any mobile carousel with more than 8 slides) ran into them. The arrows now sit at whichever is further out: that same position, or just outside the dot row. Carousels whose dots already fit are unchanged.
- **Header, metafield title and body blocks:** the block's scoped `<style>` tag was rendered inside the `<h1>`–`<h6>` (and inside the body `<div>`), so anything reading the heading's text (SEO audit tools, crawlers that use `textContent`) saw the CSS as part of the heading. The style tag now follows the closing tag. No visual change and no setting changes. (`snippets/0-block-header-1.liquid`, `0-block-metafield-title-1.liquid`, `0-block-body-1.liquid`)

### Docs
- README › On-the-Fly Functionality Buttons rewritten as a how-to: setting one up (Button blocks already carry `fresh-button`; leave the link empty), where to find variant, selling plan and Klaviyo form IDs and scroll targets (section **Custom ID**), examples, and gotchas (one action per click, silent typos, sold-out variants).
- `docs/RELEASING.md`: new step to grep for the previous version number before tagging. v3.6.0 shipped with 3.5.5/3.5.6 still in the README, the guidelines, the CSS file header and the Welcome hero.

---

## [3.6.0] - 2026-11-20

A minor release: new features and behaviour changes to check on upgrade, but no merchant JSON migration (no setting, block or section ID was renamed or removed since v3.5.5). Upgrade notes, detailed in the entries below:

- **Layout head order:** take the new `layout/theme.liquid` head (critical CSS above `content_for_header`). App CSS now comes after the Bootstrap grid and wins ties on grid class names.
- **Structured data:** shipping and return claims are off by default. Stores that want them in search results must turn them on under **0 Structured Data** and enter real values.
- **Link colour:** text links now use each scheme's Secondary button label colour instead of inheriting the text colour. Check that setting per scheme.
- **Google Fonts removed** from the shared head (Montserrat, Rethink Sans). A store that uses either must load it from its `1-` layer.
- **Bundle builder snippet removed** (`0-bundle-builder-1`). tone-body renders its own copy: keep it on the modify/delete conflict, ideally renamed to a `1-` file.
- **`.fresh-button--force-standard-1`** no longer uses `!important`, so app CSS that does can beat it. Override in the `1-` layer.
- **`config.yml` is no longer tracked.** Client repos that track one will see it deleted on merge; restore it locally.
- **JavaScript:** `0-freshwater.js` runs in an IIFE with an API on `window.Freshwater`. All old `fresh*` globals and `lazyLoadInstance` still work; only internal state variables left `window`.


### Added
- **Product gallery (variant filter) — video media:** Shopify-hosted product videos now appear in the thumbnail strip with a play-icon overlay. Clicking one swaps the main image for a native `<video>` with controls (no autoplay). Picking an image pauses and removes it. Videos follow the same `Color | alt` convention; no prefix means universal. The video is cloned from a `<template>` on click, so nothing loads up front. Ported from hume-supernatural.
- **Button blocks — second button option:** All button blocks (one-column, two-column, multi-column, hero) now support an optional second button. Enable via `Enable Second Button` toggle; configure text, URL, type, scheme, full-width, transparent BG, and font settings (family, size, weight, line-height) per breakpoint. Pair layout direction (Side by Side / Stacked) and gap (px) are separately controllable per breakpoint under `B1: PAIR LAYOUT`. Block alignment from Button 1 applies to both the outer container and pair positioning.
- **Video blocks — play button overlay:** New global `Show Play Overlay` toggle (default off) and `Play Button Background` / `Play Button Arrow` color pickers on all video blocks. When enabled, a circular SVG play icon (YouTube-style) is injected via JS after the video content div (ensuring correct stacking) and hides once the video begins playing. Available in: hero, hero-2, multi-column, two-column, one-column, main-product sections.
- **Two Column (FW) — section video controls:** The section-level video (via "Use video instead of image?" toggle) now exposes the same settings as video blocks: Show Mute Button, Show Play Overlay + button colors, Video Alignment, Max-Width (% and px), and Margin Bottom per breakpoint. All settings are gated behind the Use Video toggle via `visible_if`. Routed through the existing `0-block-video-1.liquid` snippet which already reads these keys from `settings_data`.
- **Utility classes — `.hide-mobile` / `.hide-desktop`:** Added to `0-freshwater.css.liquid`. `.hide-mobile` hides an element on viewports ≤767px; `.hide-desktop` hides on ≥768px. Both use `display: none !important` to override any inline or component-level display rules.

### Changed
- **JavaScript: no more `var`; `0-freshwater.js` wrapped in an IIFE with a `window.Freshwater` API:** All 139 `var`s in Freshwater JS (`0-freshwater.js`, `0-header-drawer-search.js`, the inline scripts in `0-hero-2`, `0-multi-column-1`, `0-header` and `layout/theme.liquid`) are now `const`/`let`, converted with ESLint's `no-var`/`prefer-const` fixers so block scoping can't change behaviour; the diff only swaps those keywords. `0-freshwater.js` now runs inside one IIFE and publishes its API on `window.Freshwater` (`carousel`, `cart`, `modal`, `video`, `links`, `accordion`, `buttons`, `megaMenu`, `waitForElement`, `extractClassValue`, `init`, `resize`, `state.lazyLoad`; see README › JavaScript API). **Every previous global is kept** as an alias of the same function (`freshAddProductToCart`, `freshWaitForElement`, `freshCloseModal`, `freshWaitForTinySlider`, …), plus a live `window.lazyLoadInstance` getter, because client section copies and `1-` files call them. The file-internal state variables (`freshWindowResizeTimeout`, `freshCartDrawerUpsellInitialized`, `freshCarouselIsDesktop`) are no longer on `window`.
- **`!important` removed from Freshwater code (58 of 60):** The `.fresh-button--force-standard-1` utility (41, plus 3 on the wallet-button custom properties), the Dawn button `::before`/`::after` hide rule, the cart remove-button reset, the drawer-search hide rules in `0-header`, and the accordion icon swap no longer use `!important`. Their existing `body.fresh` / section-ID selectors already outrank the rules they compete with. The accordion needed `!important` only to beat `style="display: none"` on `.icon2` in `0-icon-1.liquid`; that inline style is gone and `body.fresh .icon.icon2 { display: none; }` replaces it. The two `.hide-mobile` / `.hide-desktop` rules keep `!important` with a comment: a hide utility has to beat any `display`. Computed styles are identical before and after on 22 views (QA pages, a test page applying the force class to Dawn, Freshwater, payment and plain elements in three colour schemes at rest and on hover, PDP, cart with an item, open mobile drawer with drawer search on), 19,646 elements. **On upgrade:** app CSS that beat other rules with `!important` can now beat `.fresh-button--force-standard-1`; override those in the client's `1-` layer.
- **Code hygiene (no visual change):** `0-collection-variant-cards-1.liquid`, `0-fw-footer.liquid` and `0-header-drawer-search.js` reindented from 2 to 4 spaces (whitespace only). CSS properties alphabetised in 55 rule blocks across `0-freshwater.css.liquid`, `0-component-cart-drawer.css`, `0-section-featured-product.css` and `0-section-marquee.css`. Blocks with comments, Liquid tags or a shorthand that would end up after its longhand were left alone. Every sorted block expands to the same longhands in Chrome, and computed styles on 17,191 elements across 10 pages at desktop and mobile are identical before and after.
- **Head — critical CSS above `{{ content_for_header }}` (streamed HTML):** All theme CSS loaded after `content_for_header`, so Shopify's first streamed chunk carried none of it. The font faces, color-scheme and other CSS custom properties, and font preloads (moved from `0-theme-dawn-2` into `0-theme-dawn-1`), plus Freshwater's CSS variables, the Bootstrap grid and tiny-slider CSS (`0-theme-freshwater-1`, `head_part: 'critical'`), now render above it. `base.css`, the Dawn component CSS, `0-freshwater.css` and `1-client.css` stay below, so they still win equal-specificity ties against app and Shopify CSS (client `1-client.css` files override app styles; `0-freshwater.css` styles the unbranded payment button). `0-theme-freshwater-1` takes an optional `head_part` param (`critical` / `deferred`); with no param it outputs everything, so un-updated client layouts keep working. **On upgrade:** take the new `layout/theme.liquid` head order. Note that app CSS now comes after the Bootstrap grid, so an app that styles `.container`, `.row` or other Bootstrap grid class names wins ties against the grid (previously the grid won).
- **Structured data (JSON-LD) — one Product, one Organization, no hard-coded policy claims:** Product pages output two Product entities (Dawn's `{{ product | structured_data }}` in `0-main-product` plus `0-json-ld-prod`), and every page output Organization (and WebSite on the homepage) twice (`0-header` plus `0-json-ld-org`). The header copy also wrote empty strings into `sameAs`. Only `0-json-ld-prod` and `0-json-ld-org` remain. `0-json-ld-prod` claimed free shipping, 3-5 day transit and 30-day free returns for every store; shipping and returns now come from a new **0 Structured Data** theme settings group (shipping cost, handling/transit days, return window, fees, method), each behind an on/off toggle that defaults to **off**. They're built once instead of per variant, and the refund policy URL is now absolute. **On upgrade:** stores that rely on shipping or return details in search results must turn them on and enter their real values.
- **Guidelines — `!important` and `var` policy:** `.ai-development-guidelines.md` now treats `!important` as a last resort: specificity first, and only for third-party or app CSS that can't be out-specified, with a comment saying why. This replaces the old "no exceptions" wording that contradicted the README. JavaScript: no `var`. Scripts and section `<script>` tags are wrapped in IIFEs, intentional globals live on `window.Freshwater`, and legacy `fresh*` global names stay as deprecated aliases for client `1-` files.
- **Button blocks — second button UI:** Moved `Button 2 Text` and `Button 2 Link` fields to sit directly below the `Enable Second Button` toggle (top of block), gated with `visible_if` so they only appear when the toggle is on. Applies to one-column, two-column, multi-column, and hero button blocks.

### Removed
- **`snippets/0-bundle-builder-1.liquid` (bundle builder v1):** No Freshwater template or section renders it; it was built for one client. **Upgrade note:** tone-body renders it from `sections/deodorant-set-builder.liquid` and has its own edited copy, so merging this release there gives a modify/delete conflict on the snippet. Keep tone-body's copy (ideally renamed to a `1-` file, with the `render` updated) or the bundle page breaks.
- **Google Fonts in the shared head (`0-theme-freshwater-1.liquid`):** Montserrat and Rethink Sans (full variable weight and italic axes) loaded as a render-blocking stylesheet from `fonts.googleapis.com` on every store, with preconnects to two extra origins. Nothing in this repo or the client repos on file references either family; theme fonts come from `font_picker` settings. **On upgrade:** if a store's admin custom CSS or app uses Montserrat or Rethink Sans, load it from that store's `1-` layer (ideally self-hosted WOFF2 in `assets/`).

### Fixed
- **Hardcoded English labels moved to locale keys:** The visible Pause/Play text on carousel and video pause buttons, "Pause video", the video mute/unmute labels, the JS modal's dialog and close labels, the carousel region labels ("Hero slideshow", "Multi column"), the marquee, free-shipping progress bar and gallery thumbnail labels, and the variant gallery's "Showing images for …" status were English on every store. They now come from new `freshwater.accessibility.*` keys in `en.default.json` (plus Dawn's existing `accessibility.close` and slideshow keys). Liquid files use `t` directly; controls built by `0-freshwater.js` read `window.Freshwater.strings`, set in `0-theme-dawn-4`, through `Freshwater.string(key, fallback)`. Other locales fall back to English until translated. The modal close button now says "Close" (Dawn's key) instead of "Close modal".
- **Collection Variant Cards — nested variant loop and wrong eager images:** For each option value (e.g. each colour) the section looped through every variant of the product to find a matching one, inside the products loop. It now takes the variant from the option value itself (`color_value.variant`) and only loops when that combination doesn't exist. The eager/lazy check used the colour loop's index, so the first four colours of *every* product loaded eagerly (12 eager images on a 21-card dev-store grid, 4 expected), including in sections far down the page. Now only the first row (`columns_desktop` cards) loads eagerly, and only when the section is in the first two (`section.index > 2` is all lazy). Card URLs, titles and prices are unchanged on the dev store.
- **Carousels switched off at one breakpoint — no rebuild on resize:** A carousel enabled only on mobile or only on desktop was built at load for whichever breakpoint the page opened at. Crossing 768px afterwards left a full-width carousel on the breakpoint meant to show a grid (slides 1832px wide), or a grid where the carousel should be. `freshGlobalResize` now builds or destroys these carousels when the viewport crosses 768px, then refreshes lazy images and re-runs the section handlers (arrows, controls, equal heights). Carousels on at both breakpoints are still left to Tiny-Slider.
- **Accessibility (axe scan) — carousel and video pause button contrast:** The button was `opacity: 0.6` over a 40% black background, about 1.9:1 over a light slide. It's now fully opaque on 70% black, which keeps white text above 4.5:1 over any slide.
- **Accessibility — empty cart had no h1:** The empty-cart message is a `p.h1` (so the page never has two h1s), and Dawn hides the title row when the cart is empty, so the page had no h1. The "Your cart" h1 now stays in the accessibility tree, visually hidden, and only the duplicate "Continue shopping" link is hidden. Works on load and when the last item is removed without a reload.
- **Accessibility — PDP quantity input had no accessible name:** The label's only text is `aria-hidden` and Dawn put `aria-labelledby` on the label instead of the input. The input in `0-main-product` and `0-featured-product` now has `aria-labelledby` pointing at the visually hidden "Quantity" / "Quantity (n in cart)" text.
- **Accessibility — duplicate IDs in the country picker:** Header, drawer, announcement-bar and footer copies of the country picker shared IDs, so the footer's search label and live region pointed at the header's. IDs are now unique per position (Dawn modification, see README).
- **Accessibility — blog heading order:** Blog page cards were h3 directly under the page h1. `article-card` takes an optional `heading_tag`, and `main-blog` passes `h2` (Dawn modification, see README).
- **Lazy-loaded FW images — layout shift:** Graphic images with lazy load on (`class="lazy"`, `data-src`) rendered as 16x16 alt-text boxes until vanilla-lazyload set `src`, because Chrome ignores width, height and aspect-ratio on a src-less inline image. The page then jumped as each image loaded (about 1,500px on a long page), and `freshscroll-` buttons landed short of their target. `.fresh-figure .fresh-picture img` is now `inline-block`, so the box is reserved at the right size before load.
- **Carousels — `HierarchyRequestError` on every breakpoint resize (patched Tiny-Slider 2.9.4):** Tiny-Slider measures a container by appending an empty `<div>` and walking up the DOM until the probe has a width. Dawn's `base.css` hides `div:empty`, so the probe never has a width, the walk reaches `document`, and `appendChild` throws. This happened on every resize across a carousel breakpoint, from the library's own resize handler. The probe now gets `display: block`, and the walk stops at element nodes. This is likely the same error the README attributed to Tiny-Slider 2.9.5+. Verified headless: 24 exceptions per 12 resizes before, 0 after, and post-resize layout matches a fresh load.
- **Carousels — `fetchpriority="high"` duplicated onto the loop clone:** Tiny-Slider's loop mode clones the first slide with its attributes, so pages with a first-section carousel had two high-priority images. `freshCarouselA11y` now strips `fetchpriority` from cloned slides and sets them to `loading="lazy"`.
- **Hero-2 / Multi Column — `freshWaitForElement is not defined`:** A `resize` that fires before the deferred `0-freshwater.js` runs called the section handler too early. The handler now returns until the function exists; `DOMContentLoaded` runs it again.
- **Carousel dots — inactive dots below 3:1 contrast (WCAG 1.4.11):** Inactive dots were only a 30%-alpha fill of the dot color, which usually fails the 3:1 non-text contrast requirement against the background. They keep the soft fill and now also get a 1px inset ring in the full dot color (`box-shadow`, so the dot size doesn't change). The ring uses the same color as the active dot, so the outline has the active dot's contrast. Applied to hero-2 and multi-column, desktop and mobile, for both the color-scheme icon color and the custom dot color setting.
- **Button block — functional buttons unreachable by keyboard; `freshWaitForElement` observer leak:** A button with no URL but a Freshwater function class (`freshvariant-`, `freshklaviyo-`, `freshscroll-`) rendered as `<a role="link" aria-disabled="true">` with no `href`, so it couldn't be focused or activated by keyboard and was announced as disabled, although `freshAddFunctionalityButtons` made it work on click. It now renders as `<button type="button">` with the same classes (same size, font, padding and colors on the dev store). Blank-URL buttons without a function class keep the disabled link. The button style rules and multi-column's slide button min-width now match `<button>` too. `freshWaitForElement` started a new body-wide MutationObserver on every call and only disconnected when the element appeared; callers re-ask on every load/resize/section event (e.g. multi-column waiting for `.tns-slider` even when the carousel is disabled), so observers piled up. Calls for the same selector now share one observer, and an observer gives up after 10 seconds if the element never appears (the promise stays pending, as before).
- **Video block (`0-block-video-1`) — autoplay, loading and accessibility:** Autoplaying background videos looped with no way to stop them and ignored `prefers-reduced-motion` (WCAG 2.2.2); they now get a Pause/Play button (bottom-left, styled like the carousel button; the mute button stays bottom-right), and with reduced motion on they render as normal click-to-play videos with controls. Every video used `preload="auto"` and autoplay started immediately even below the fold; now videos preload metadata only, and autoplay videos play while at least 25% is on screen and pause when scrolled away (a video the shopper paused stays paused). The `src` was built by looping all MP4 renditions into one attribute, which concatenated URLs when a video had more than one; it now uses the first MP4 source. Posters were 600px wide (blurry on a hero); now 1600px desktop / 1100px mobile. Linked videos were always named "Slide"; the link and the video now use the video's alt text when set, and the play overlay label is translated (`products.product.media.play_video`). Each video block attached its own document listeners and editor MutationObserver, so a section with N videos re-initialized each one N times; the implementation now lives on `window.Freshwater.video` and attaches listeners once. `window.Freshwater` in `0-freshwater.js.liquid` now merges into an existing object instead of replacing it, so inline scripts can register namespaces before the deferred file loads.
- **Carousels (Tiny-Slider) — keyboard focus in hidden slides, missing pause button, region on the list:** Tiny-Slider marks off-screen slides and loop clones `aria-hidden` but their links and buttons stayed in the tab order; a new `freshCarouselA11y()` mirrors `aria-hidden` into `inert` (kept in sync with a MutationObserver), so only visible slides are tabbable. The play/pause button was created only when the top-level (desktop) `autoplay` was on, so hero-2 with mobile-only autoplay had no pause control (WCAG 2.2.2); it's now created when any breakpoint autoplays and hidden at breakpoints that don't. Autoplay settings are snapshotted before `tns()`, which rewrites `options.responsive`. The button's accessible names use the `sections.slideshow.pause_slideshow` / `play_slideshow` locale keys. `role="region"` / `aria-roledescription="carousel"` moved from the `<ul>` (where they replaced its list role) to the `.tns-outer` wrapper, added at init; the label comes from `data-carousel-label`. Applied to hero-2 and multi-column.
- **`assets/0-tiny-slider.css` — was uncompiled SCSS:** The file was tiny-slider 2.9.4's `src/tiny-slider.scss` run through a minifier (nested `&.tns-…` rules, `$width:310px;` variables), not the compiled CSS. Browsers without CSS nesting dropped every nested rule (including `.tns-outer [hidden]` and the horizontal-layout rules), and the `$` variables are invalid CSS everywhere. Replaced with the official compiled `tiny-slider.min.css` 2.9.4 from cdnjs. The old file matched the upstream SCSS exactly after whitespace normalization, so there were no Freshwater edits to carry over. The JS (`0-tiny-slider.min.js`, patched 2.9.4) is unchanged.
- **Hero / Hero-2 — text box hidden until JS, wrong in the editor:** The hero text box started hidden (`opacity: 0` in hero-1; `display: none` in hero-2) and was positioned by a script that ran 250ms after DOMContentLoaded and on every unthrottled `resize`, reading and writing layout for every slide and Tiny-Slider clone. A text LCP waited for that script, and both scripts declared top-level `const`s, so the theme editor hit the same re-run bug as #12. The X/Y position is now pure CSS: `left: X%; top: Y%; transform: translate(-X%, -Y%)`, which computes the same `(wrapper - box) × X%` as the script did (measured within 1px of the old JS on desktop and mobile). On mobile above/below layouts only the X offset applies, as before. Both scripts, the `data-x*/y*` attributes and the hidden start states are removed, and hero-2 hides empty boxes with `:empty`. Applied to `0-hero-1.liquid`, `0-slide-2.liquid` and `0-hero-2.liquid`.
- **Section wrappers — empty `id=""`; block unique-ID checks made consistent:** FW section wrappers (hero-1, hero-2, one-column, two-column, multi-column, marquee-2) always output `id="{{ section.settings.fresh_section_unique_id }}"`, which rendered an invalid empty `id=""` when no unique ID was set. `0-slide-2` did the same, unquoted. They now output `id` only when set, and the ID and classnames are escaped. `0-block-accordion-1`, `0-block-html-1` and `0-block-liquid-1` now check their unique-ID setting with `!= '' and != blank` like the other block snippets. This is defensive: testing on the dev store showed unset settings already fell back to the generated ID.
- **External links — `mailto:`/`tel:` broke, `noopener` didn't apply, no new-window cue:** `freshOpenExternalLinksInNewWindow` compared hostnames, so `mailto:`/`tel:`/`sms:` links (empty hostname) were treated as external and opened a blank tab. It then cancelled the click and called `window.open(href, '_blank')`, which ignores the `rel="noopener noreferrer"` it had just added, so the new page kept `window.opener`. External `http(s)` links are now marked with `target="_blank"`, `rel="noopener noreferrer"` (existing rel values kept) and `aria-describedby="a11y-new-window-message"` (Dawn's translated "Opens in a new window"), and the browser opens them natively. Links are marked on init, on `shopify:section:load`, and on focus/click for content added later. Links with an explicit `target` or `download` are left alone; the Judge.me exclusions and the optional `filter` param still work.
- **Skip link and in-page anchors — keyboard focus never moved (WCAG 2.4.1 / 2.4.3):** `freshSmoothScrollToInlineAnchors` calls `preventDefault` on every `a[href^="#"]`, which also cancels the browser's focus move and hash update, so after "Skip to content" keyboard focus stayed in the header. The handler now focuses the target (`preventScroll`; non-focusable targets get `tabindex="-1"`) and pushes the hash before animating the scroll. `name=` fallback lookups are now `CSS.escape`d. Also removed the duplicate skip link that `layout/theme.liquid` added on top of the one in `0-theme-dawn-3.liquid`.
- **Hero / first-section image — LCP loads first:** The first image in the first section loaded lazily in every case. With the lazy-load setting on (the default), only `data-src`/`data-srcset` was output, so the image waited for the deferred lazy-load JS. With it off, it got `loading="lazy"`. `0-block-graphic-1` now takes an optional `lcp_candidate` param, passed for the section-level image in hero-1 and two-column and for the first block or slide in one-column, multi-column and hero-2. When the section is first on the page (`section.index` not greater than 1), that image gets a real `src`/`srcset`, `loading="eager"` and `fetchpriority="high"`. Everything else is unchanged, so only one image per page gets high priority. The loading attributes are now built in one place and no longer run together in the output (`alt="…"class="lazy"`).
- **Theme Check — false `LiquidHTMLSyntaxError` in `0-freshwater.css.liquid`:** A CSS comment contained a literal `<h1 class="header__heading">`. Theme Check parses `.css.liquid` as HTML and reported it as an unclosed `h1`. The comment is reworded; no CSS changed. Theme Check and `scripts/prelaunch.sh` now exit 0.
- **Product variant options — swatch render syntax error (Dawn file):** `snippets/product-variant-options.liquid` passed `value: value | escape` to `render 'swatch-input'`. Shopify rejects filters on render arguments, so the file failed to upload on every theme push and Theme Check reported `LiquidSyntaxError` / `UnsupportedFilterArguments`. The escaped value is now assigned to `escaped_value` before the render. Same fix as upstream Shopify/dawn#3870 (first shipped in Dawn 15.4.1); added to README > Modified Dawn Files with an upgrade check.
- **Multi Column / Hero-2 carousel — arrows mispositioned in the theme editor until save:** In the editor, arrow placement followed whatever was saved when the page loaded, not the current setting. Switching from a saved Center of Image to another placement left the arrows at the image center, and switching to Center of Image from anything else left them at the top. The JS decided placement itself and wrote inline `top` values, so it depended on the editor re-running the script or firing a matching `shopify:section:load` event, and neither is reliable. Now JS never sets `top`/`bottom` and never reads the placement setting. It measures the visible slide's figure and stores its vertical center on `.tns-outer` as `--fresh-arrow-image-center`, and the Center of Image CSS rule uses `top: calc(var(--fresh-arrow-image-center, 50%) - <size/2>px + <alignment>px)`. Every placement is now positioned by CSS, which the editor always re-renders, so a measurement left over from an earlier render can't move arrows under another placement. The script is also wrapped in an IIFE so a re-run can't throw on duplicate `const` declarations, and its page-level listeners are attached once per section (`window.freshCarouselSections`). This replaces the `window.freshArrowPlacement` workaround. Slides displayed for the controls check is read from a `data-slides-*` attribute. Applied to `0-multi-column-1.liquid` and `0-hero-2.liquid`.
- **Hero-2 — mobile arrow Vertical Alignment range:** Expanded from ±50 step 1 to ±100 step 2 to match the other alignment sliders, and fixed the "Veritcal" label typo.
- **Link color — `--color-link` never applied; global link rule recolored button links:** `--color-link` is an RGB triplet (`0,31,63`, like Dawn's color variables), but all four link rules in `0-freshwater.css.liquid` used `color: var(--color-link)` without `rgb()`. That is invalid at computed-value time, so links silently inherited the parent's text color and the scheme's Secondary button label setting had no effect. All four are now `rgb(var(--color-link))`. The global `body.fresh a` rule also lacked the `.fresh-button` / `.fresh-button--force-standard-1` / `.button` exclusions the per-scheme rules have, so it overrode `.fresh-button { color }` on `<a>` buttons in client CSS. The exclusions are added inside `:where()`, keeping the rule at (0,1,2) specificity. **Visual change on upgrade:** text links will now take each scheme's Secondary button label color instead of the surrounding text color; check that setting per scheme when rolling this out to a store.
- **Utility classes — `.hide-desktop` visible on mobile / `.hide-mobile` hidden on desktop:** The unguarded base rule `.hide-desktop { display: none }` (no media query) was hiding those elements at all breakpoints. Removed that rule; the `@media (min-width: 768px)` block now handles it with `!important` and covers all three selectors (`.hide-desktop`, `.fresh-hide-desktop`, `.tns-item .fresh-hide-desktop`). The `.hide-mobile` base rule (`display: block`, no media query) is retained — snippet-scoped styles can set `display: none` on blocks, and this base rule ensures `.hide-mobile` elements are always restored to visible at desktop. Same selector expansion applied to the `@media (max-width: 767px)` block.
- **Multi Column / Hero-2 carousel — "Center of Image" timing and clone exclusion fix:** `SetArrowPosition` now uses a `findFigure()` helper that explicitly excludes Tiny Slider clone slides (`.tns-slide-cloned`) when selecting the slide to measure; clones are off-screen and return `offsetHeight = 0`. Each measurement attempt is wrapped in `requestAnimationFrame` so layout has fully settled before reading dimensions. Retry loop added (up to 20 attempts, 150ms apart) for cases where Tiny Slider has just initialized and layout hasn't resolved yet. `img.load`/`img.error` events wired if the image is not yet decoded, so arrow position is recalculated once the image is fully rendered and aspect-ratio height is known. Applied to both `0-multi-column-1.liquid` and `0-hero-2.liquid`.
- **Multi Column / Hero-2 carousel — arrow defaults and placement:** Default arrow size changed 20→15, thickness 3→2. Vertical and Horizontal Alignment ranges expanded from ±50 (step 1) to ±100 (step 2) for more placement flexibility. Default horizontal alignment changed from 10 to −30 so arrows start outside the slide container (beside slides) by default; push positive to overlay on full-width layouts. Fixed "Center of Image" JS reference-frame bug: arrow vertical position was computed relative to the section element instead of `.tns-outer` (the arrows' actual containing block), causing misalignment. Both `0-multi-column-1.liquid` and `0-hero-2.liquid` updated.
- **Multi Column / Hero-2 carousel — JS-based arrow vertical positioning for all placements:** The `SetArrowPosition` JS function now handles all three placement modes (Center of Slide, Center of Image, Below Carousel) via DOM measurement instead of early-returning for non-image modes. "Center of Slide" uses measured `.tns-outer` height; "Below Carousel" aligns with `.tns-nav` center; "Center of Image" finds the image bounding rect. Inline styles are explicitly cleared when switching placements, preventing the previous bug where switching from "Center of Image" left stale `style.top` that overrode CSS for other modes. Alignment offset (positive = down) applied consistently across all three modes.
- **Multi Column / Hero-2 carousel — arrow position coordinate system fix:** Replaced all `getBoundingClientRect()` calls with layout-unit methods (`clientHeight`, `offsetHeight`, `offsetTop`). `getBoundingClientRect()` returns viewport-scale-adjusted coordinates that are affected by CSS `transform`/`zoom` on ancestor elements (e.g. the Shopify theme-editor preview iframe), causing `style.top` — which operates in layout (CSS pixel) units — to receive a mismatched value and push arrows far below the section. Added `getLayoutOffsetTop(element, ancestor)` helper that walks the `offsetParent` chain for accurate layout-unit relative offsets. All three placement modes now compute vertical position in the same coordinate space as `style.top`.
- **Multi Column / Hero-2 carousel — arrow positioning event listener fixes:** `shopify:section:load` listener moved from `window` to `document` (Shopify dispatches editor events on `document`; they may not bubble to `window`). Added `window load` listener so arrow positions are recalculated after all images have loaded — `DOMContentLoaded` fires before images, so image-height-dependent container dimensions can be incorrect at that point.
- **Multi Column / Hero-2 carousel — JS arrow positioning scoped to image placement only:** The `SetArrowPosition` JS function previously computed and set `style.top` for all three placement modes. For `slide` and `dots`, CSS already positions arrows correctly via Liquid-baked `top`/`bottom` values (including the alignment offset). JS-computed values were overriding correct CSS with wrong measurements. JS now only sets inline `style.top` for `image` placement; all other modes call `clearArrowInlinePosition()` to remove any stale inline style and defer to CSS.
- **Multi Column / Hero-2 carousel — width-aware arrow horizontal positioning:** Arrow horizontal position now automatically adapts to section width. Non-full-width sections: arrows start outside the slide container (`calc(-arrow_size - 5px + offset)`). Full-width / full-bleed sections: arrows start 15px inside the image edge (`calc(15px + offset)`). The `Horizontal Alignment` setting (default changed −30→0) acts as a fine-tune offset from the automatic base position in both cases.
- **Multi Column (FW) — section missing from editor:** `{% capture 'fresh_data_slider' %}` used a quoted variable name (invalid Liquid syntax). Changed to `{% capture fresh_data_slider %}`. Same fix applied to `0-hero-2.liquid`.
- **Multi Column (FW) — second button block missing comma:** Pre-existing JSON schema error: the second button block's `fresh_button2_enabled` setting was missing a trailing comma. Added comma and inserted the missing `fresh_button2_text` / `fresh_button2_url` fields.
- **Collection product grid — Liquid pagination error:** `collection.products` is not paginateable outside a collection template context. Wrapped the `paginate` / `endpaginate` block with `{%- if collection -%}`.
- **Button pair — stacked full-width not filling column width:** In `flex-direction: column`, `align-items: center/flex-start/flex-end` causes children to size to intrinsic content width regardless of `width: 100%` set on individual items. Switched to `align-items: stretch` on the pair container when any button has Full Width enabled; this is the correct flex-native mechanism for filling the cross-axis. Single-button mode is unaffected.
- **Button pair — side-by-side full-width squishes buttons:** Pair container lacked `width: 100%` in row mode, so `flex: 1` on buttons had no space to fill (container was shrink-wrapping to content). Added `width: 100%` to the pair container in row mode when either button has Full Width enabled.
- **Button pair — stacked full-width only affects button 1:** When Full Width is on for button 1 in stacked layout, button 2 now also becomes full-width via the `align-items: stretch` on the pair container. Both buttons behave as a unit in stacked+full-width mode.
- **Blog Posts section — horizontal page scroll on mobile:** Dawn's `featured-blog` section uses `grid--peek` inside `slider-component.slider-mobile-gutter` to show the edge of the next card. The `grid--peek` overflow was escaping the `slider-component` and causing a page-level horizontal scrollbar. Added `overflow-x: clip` scoped to `.blog slider-component.slider-mobile-gutter` in `0-freshwater.css.liquid`; `clip` contains the overflow without establishing a new scroll container, so the internal `ul`'s native scroll behaviour is preserved.
- **Video block (one-column) — alignment and margin-bottom ignored:** The `0-block-video-1.liquid` snippet read the max-width settings but never read or output `fresh_graphic_alignment--md/sm` or `fresh_graphic_margin_bottom--md/sm`. Fixed by adding responsive margin-left/right (left/center/right) and margin-bottom CSS rules to the scoped `{%- style -%}` block.
- **Header — mega-menu dropout over linked sections:** Dawn sets `.section-header.shopify-section-group-header-group` to `z-index: 3`. Page-section stretch links (`0-block-graphic-1.liquid`) create root-level stacking contexts at `z-index: 3–4`; later DOM siblings win the tie, so mega-menu dropdowns paint under linked sections (hero, multi-column, etc.) and disappear on hover. Raised the header group to `z-index: 20` in `assets/0-freshwater.css.liquid` — clearly above all page-content stacking contexts while staying below the modal/drawer tier.
- **Video blocks — mute button visibility control:** Added global `Show Mute Button` toggle (default on) to all video block schemas. The JS checks `data-mute-button` on the container before injecting the mute button.
- **Video blocks — play overlay / mute toggles simplified to global:** Original implementation used per-breakpoint pairs (`--md`/`--sm`) but `fresh_video_autoplay` is a single global setting; the mute and play toggles are now global to match.
- **Multi Column (FW) — block margin bottom ignored:** `--fresh-block-margin-bottom` was set globally to `20px` on `.fresh-block` elements in `0-freshwater.css.liquid` and never overridden. Because CSS custom properties set directly on an element beat inherited ancestor values, the fix targets `.fresh-block` descendants within the section (`#freshSection-- .fresh-block { --fresh-block-margin-bottom: Xpx }`) to achieve specificity `(1,1,0)` over the global `(0,3,0)`. Both desktop and mobile media queries updated in `sections/0-multi-column-1.liquid`.
- **Homepage logo — extra margin below header:** Dawn wraps the homepage logo `<a>` in `<h1 class="header__heading">` for SEO. Browser default `<h1>` margin was causing a gap not present on other pages. Added `h1.header__heading { margin: 0 }` to `assets/0-freshwater.css.liquid`.
- **Multi Column (FW) — last row not centering:** Remainder items in the last row (e.g. 5 columns with 2 or 3 items remaining) were left-aligned instead of centered. Switched the disabled-carousel layout from a doubled-column CSS Grid to `display: flex; flex-wrap: wrap; justify-content: center` with Liquid-computed `flex-basis` per item in `sections/0-multi-column-1.liquid`. Also removed ~1 030 lines of now-dead nth-child `grid-column-end` centering rules from `assets/0-freshwater.css.liquid`.

### Security
- **`config.yml` no longer tracked:** It holds a Shopify Theme Access password for `freshwater-dev` and was committed to the repo, so every client merging a release would have received it. Added `.gitignore` with `config.yml`. Keep your local copy for `shopify theme dev`; get the password from LastPass. Client repos that track their own `config.yml` will see it deleted when they merge this release; restore it locally from LastPass after the merge.

---

## [3.5.5] - 2026-07-23

### Added
- **Hero sections — ADA screen reader text:** Both `sections/0-hero-1.liquid` and `sections/0-hero-2.liquid` now include an **ADA / ACCESSIBILITY** collapsible toggle (matching the GLOBAL SETTINGS pattern). When expanded, a `ada_text` textarea accepts copy describing the image and any text baked into it. Renders as `<div class="visually-hidden">` — announced by screen readers, invisible to sighted users. Addresses WCAG 1.1.1 (Non-text Content) and 1.4.5 (Images of Text) for heroes where text is part of the image asset.

### Fixed
- **Empty cart "Continue shopping" button — black-on-black text:** Freshwater's per-color-scheme link color rule (`a:not(.fresh-button):not(.fresh-button--force-standard-1)`) was also targeting Dawn's `<a class="button">` anchor, applying `--color-link` (black in dark schemes) on top of the black button background. Added `:not(.button)` to all three selectors (--md, --sm, legacy) in `assets/0-freshwater.css.liquid` so Dawn `.button` anchors inherit their own text color token.
- **Video mute button — skip native-controls videos:** `freshCreateVideoSoundButtons()` in `assets/0-freshwater.js.liquid` now skips any `<video>` element that has the `controls` attribute. Those videos already expose mute/unmute via the browser's native UI; injecting a second custom button was redundant and visually conflicting.
- **PDP variant gallery thumbnails — blurry on retina displays:** Thumbnail `<img>` tags in `snippets/0-product-gallery-variant-1.liquid` were rendering a flat `width: 200` with no `srcset` or `sizes`, causing blur on 2x/3x screens. Added `srcset` at 120w/200w/400w and `sizes="(min-width: 768px) 200px, 60px"` so the browser selects a sharp image for the actual rendered thumb size.

---

## [3.5.4] - 2026-07-06

### Added
- **Collection Variant Cards (FW):** New section (`sections/0-collection-variant-cards-1.liquid`) displays product variant cards in a collection grid. Cards show variant-specific images matched by alt text color prefix, with image ratio control, secondary hover image, and direct variant links.
- **Product gallery — variant image filtering:** `sections/0-main-product.liquid` adds a **Gallery type** setting (`gallery_type`) with two options: **Default (Dawn)** and **Variant filter (FW)**. The variant filter gallery (`snippets/0-product-gallery-variant-1.liquid`) filters product images by a configurable option (e.g. "Color") using the alt text convention `Color | Description`. Images without the prefix are universal. Includes thumbnail strip, desktop/mobile thumbnail size controls, and JS-driven filtering on variant change. Gallery type defaults to **Default (Dawn)** so existing products are unaffected.
- **On-the-fly functionality buttons — README documentation:** The `fresh-button` class system (`freshvariant`, `freshklaviyo`, `freshscroll`, `freshquantity`, `freshsellingplan`, `fresh-modal-button`) is now documented in the README under Key Features.

### Changed
- **Multi Column (FW) — standard blocks:** Header, Body, Button, and other standard Freshwater blocks (`0-block-*`) can now be added to Multi Column slides alongside the existing slide-specific content fields.
- **Rating block alignment — Auto option:** Added **Auto** to the rating block alignment dropdown across Hero Carousel (FW) and all other sections so ratings can inherit section text alignment.
- **List block justification — Auto option:** Added **Auto** to the list block justification dropdown across all sections.
- **Tiny-Slider autoplay button:** Replaced the built-in Tiny-Slider autoplay button (which caused `appendChild` errors) with a custom Freshwater implementation. Styled play/pause button with proper positioning.
- **Section naming:** All Freshwater sections now use the `(FW)` suffix at the end of the name (e.g. "Hero (FW)" not "(FW) Hero"). Names kept within 25-character limit.

### Fixed
- **Accessibility (ADA) — 6 passes:** Comprehensive accessibility overhaul across the theme:
  - Pass 1: Fixed `.sr-only` snippet, added skip-to-content link, rebuilt accordion for full keyboard/ARIA support.
  - Pass 2: Cart and product accessibility fixes.
  - Pass 3: Carousel `prefers-reduced-motion` support and motion accessibility.
  - Pass 4: Modal and drawer focus management and ARIA fixes.
  - Pass 5: Image alt text, video accessibility, link purpose, and landmark roles.
  - Pass 6: Minor accessibility polish.
- **Tiny-Slider v2.9.4 patches:** Fixed swipe NaN crash (viewport width fallback) and nav dot null guards. See 3.5.3 changelog for root cause details.
- **None button hover:** Hover state now keeps the same color as non-hover for none-style buttons (schemes 1 and 3).
- **Dawn foreground opacity 0.75:** Changed every instance of `rgba(var(--color-foreground), 0.75)` to `1` directly in Dawn source files (previously overridden in Freshwater CSS, but some elements still inherited translucency).
- **Variant gallery race condition:** Gallery reads selected variant from the form input rather than the URL, preventing stale state on rapid variant changes.
- **Schema fixes:** Removed blank `default` values (not allowed by Shopify), fixed section names exceeding 25-character limit, added `presets` arrays so new sections appear in the customizer picker.

### Removed
- **Standalone Product Gallery section:** `sections/0-product-gallery-1.liquid` removed. The gallery is now integrated into `0-main-product.liquid` via the `gallery_type` setting. The JS asset (`assets/0-product-gallery-1.js`) is retained (used by the integrated snippet).

## [3.5.3] - 2026-05-26

### Fixed
- **0-tiny-slider.min.js — Swipe fix (2026-06-24):** Two patches applied to tiny-slider v2.9.4 minified source:
  1. **Viewport width (`it`) NaN fallback:** Added `if(isNaN(it)){it=j.clientWidth||(E.clientWidth-(et?2*et-nt:0))}` before the swipe calculation `var t=-a*rt/(it+nt)`. The `un()` function returns NaN when the `appendChild` monkey-patch in `0-freshwater.js.liquid` interferes with DOM measurement. This caused all swipe/drag to break while arrows/dots continued working.
  2. **Nav dot null guards:** Wrapped `ji(t,...)` and `ji(e,...)` calls in `if(t){}` and `if(e){}` guards where `He!==Oe` triggers nav updates. Prevents "Cannot read properties of undefined (reading 'item')" crash when nav indices are NaN (cascading from the `it=NaN` bug above).
  - **Upstream status:** Not reported. Project is abandoned (no maintainer response since ~2022, 200+ open issues unresolved). Root cause diagnosis may be useful as a comment on [#497](https://github.com/ganlanyuan/tiny-slider/issues/497) or [#118](https://github.com/ganlanyuan/tiny-slider/issues/118).

### Changed
- **BREAKING — Button colors are now global theme settings.** Button scheme colors (1, 2, 3) moved from per-color-scheme settings to the global **0 Button** theme settings section. Button colors are now consistent regardless of which color scheme a section uses. Existing client button colors will reset to schema defaults on upgrade; re-enter in Theme Settings > 0 Button or migrate via `fw-upgrade.sh`. `config/settings_schema.json`, `snippets/0-theme-freshwater-1.liquid`, `assets/0-freshwater.css.liquid`.
- **Dawn button mapping simplified.** Dawn `.button` (primary) maps to Freshwater scheme 1, `.button--secondary` to scheme 2, `.button--tertiary` to scheme 3. Previously cycled through schemes per color scheme index.
- **Mobile drawer — Search & Account labels:** When search is shown in the drawer (**Move search into mobile menu**), the labels **Search** and **Account** appear next to the icons with the same look as primary drawer links; both rows stay fully clickable (account still opens the customer account UI via `shopify-account`). `snippets/0-menu-drawer-search-trigger.liquid`, `snippets/0-header-drawer.liquid`, `sections/0-header.liquid`.
- **Button blocks — transparent background override:** Added per-block **Transparent Background** toggles for desktop and mobile (`fresh_button_bg_transparent--md`, `fresh_button_bg_transparent--sm`) so merchants can quickly force transparent button backgrounds without changing global theme button settings. Implemented across Freshwater button block schemas and `snippets/0-block-button-1.liquid`.
- **Theme editor schema refactoring:** Section settings, block settings, and toggle groups across all FW sections now use consistent ALL CAPS headers (SECTION SETTINGS, CAROUSEL, BLOCK, GLOBAL SETTINGS, GRAPHIC SETTINGS, LAYOUT, TEXT BOX), concise Desktop / Mobile toggle labels, `°°°°°` dividers between Desktop/Mobile, and `■■■■` separators between major groups. Affected sections: Hero (FW), Hero Carousel (FW), Two Column (FW), Multi Column (FW), and others.
- **Button Block Alignment — Auto option:** Added **Auto** to the Block Alignment dropdown so buttons can inherit the section’s text alignment instead of requiring an explicit override.
- **Freshwater header typography:** `h1`–`h6` and `.h0`–`.h6` classes now use Freshwater header font styling globally. `assets/0-freshwater.css.liquid`.
- **Cart styling overrides:** Cart item name, price, and old price font sizes and weights are now explicitly styled. `assets/0-freshwater.css.liquid`.

### Fixed
- **Stretch link z-index:** Link URL overlay on Multi Column slides, Hero, Hero Carousel, Two Column media, and Graphic blocks was not clickable because the stretch link (`z-index: 1`) sat behind the slide content (`z-index: 2`). Bumped stretch link to `z-index: 3` and interactive elements inside to `z-index: 4`. `sections/0-multi-column-1.liquid`, `sections/0-hero-1.liquid`, `sections/0-hero-2.liquid`, `sections/0-two-column-1.liquid`, `snippets/0-block-graphic-1.liquid`.
- **Inverted button mobile background:** `.fresh-button__inverted--sm` referenced non-existent theme setting `fresh_standard_button_background_color_1`; now uses `var(--standard-button-background-color-1)`. `assets/0-freshwater.css.liquid`.
- **Mobile drawer — Search & Account icon layout:** Dawn’s **`.menu-drawer__menu-item > .svg-wrapper`** rule (absolute caret for submenu links) no longer applies in **`.menu-drawer__utility-links`**, so labeled Search/Account rows keep **icon + text** inline. `assets/0-freshwater.css.liquid`.
- **Mobile drawer — Account icon spacing:** **`margin-right: 1rem`** on **`.menu-drawer__account-icon`** matches the Search row spacing (**`gap: 1rem`**) because flex **gap** on **`::part(signed-out-avatar)`** does not reliably separate slotted nodes. `sections/0-header.liquid`.
- **Header — mobile grid columns:** On viewports **≤989px**, **`body.fresh .header`** sets **`grid-template-columns: 1fr 1fr 1fr`** (Dawn **`base.css`** uses **`1fr 2fr 1fr`**) so the logo stays visually centered when left/right content changes. `assets/0-freshwater.css.liquid`.
- **Button scheme font colors (2 and 3):** Link color inheritance from color schemes no longer overrides Freshwater button text colors after button styles were moved out of Dawn color-scheme scopes. `assets/0-freshwater.css.liquid`.
- **Hero text box pop-in:** Hero text box now fades in after JS positions it instead of showing a visible pop/jump. `sections/0-hero-1.liquid`.
- **Product JSON-LD priceValidUntil:** `priceValidUntil` is now dynamic (always 1 year from current date) instead of a hardcoded value. `snippets/0-json-ld-prod.liquid`.

## [3.5.2] - 2026-04-13

### Changed
- **Header — move search into mobile menu:** Drawer search row (`.menu-drawer__search-from-drawer`) uses **9px** left padding so the search icon lines up with the account row (replacing the prior 3rem / zero-left tweaks); the **Move search into mobile menu** checkbox has no theme-editor info text. `sections/0-header.liquid`.
- **Cart — free shipping bar:** New theme setting **Show free shipping bar** (`fresh_free_shipping_bar_enabled`, default on). When off, the drawer free shipping block is hidden. When on, **Free Shipping Threshold** can be `0` to show full progress and the unlocked message for any non-empty cart (no longer “0 hides the bar”). **Free Shipping Threshold** uses `visible_if` so it is hidden when the bar toggle is off. `config/settings_schema.json`, `snippets/0-cart-drawer.liquid`.
- **Theme settings — None button underline offsets:** Under **None Button Additional Styling**, **Font Underline Offset** (`fresh_none_button_text_underline_offset_1`) is now **0–10px** in **0.5px** steps (was 0–4px, 0.1px), matching **Font Hover Underline Offset** (`fresh_none_button_hover_text_underline_offset_1`). `config/settings_schema.json`.
- **Footer:** The “Powered by Shopify” line is removed from the storefront copyright row. Implemented in new `sections/0-footer.liquid` (Freshwater clone of Dawn `footer.liquid`); `sections/footer-group.json` and default `config/settings_data.json` use section type `0-footer`. Dawn `sections/footer.liquid` is unchanged.

### Added
- **Featured product (Freshwater):** `sections/0-featured-product.liquid` and `assets/0-section-featured-product.css` — clone of Dawn featured product; **Icon with text** uses `pairing_1` schema keys (aligned with **Main product**). Section type **`0-featured-product`** for JSON; Dawn `sections/featured-product.liquid` + `assets/section-featured-product.css` stay vanilla for upgrades.
- **Header — move search into mobile menu:** Under **Freshwater Mobile Drawer Menu**, **Move search into mobile menu** (`fresh_move_search_into_mobile_menu`, default off). When **Menu** is set and this is on, viewports under **990px** hide the header search icon and show a search control in the menu drawer (default Dawn drawer: above **Log in** in utility links; Freshwater drawer placeholder: at the bottom of the custom footer). The control opens the same `details-modal` search as the header (`assets/0-header-drawer-search.js`); closing search leaves the drawer open. `sections/0-header.liquid`, `snippets/0-header-drawer.liquid`, `snippets/0-menu-drawer-search-trigger.liquid`, `snippets/1-header-drawer-freshwater.liquid`.
- **Multi Column (FW) — slide button min-width:** Under **Button Settings**, after **Block Alignment**, **Slide button min-width** (desktop and mobile, 0–500px, step 5) defaults to **0** (theme Standard Button min-width applies). A **positive** value overrides `min-width` on `.fresh-button` links in slides for this section. Setting IDs: `fresh_slide_button_min_width--md`, `fresh_slide_button_min_width--sm`. `sections/0-multi-column-1.liquid`.

### Fixed
- **Theme Check / schema locale:** `locales/en.default.schema.json` adds `sections.main-product.blocks.icon_with_text.settings.content` (`label` / `info`) so stock Dawn `sections/featured-product.liquid` passes `ValidSchemaTranslations` when merchants keep the vanilla section.
- **Header — move search into mobile menu:** Drawer search control is a `<span role="button">` (not `<button>`) so Dawn `MenuDrawer` in `assets/global.js` does not attach `onCloseButtonClick` (which was closing the main drawer). Keyboard **Enter** / **Space** handled in `assets/0-header-drawer-search.js`. Opening the search modal is deferred with `setTimeout(0)` so the same click does not bubble to `document.body` after `details-modal.js` registers its close handler (which was closing search immediately). `snippets/0-menu-drawer-search-trigger.liquid`, `assets/0-header-drawer-search.js`.
- **Cart remove control:** Line-item remove in the cart drawer and on the main cart page no longer uses global standard button `min-width` or tertiary fill; the trash icon uses the **first** theme color scheme’s text color (wrapper + `cart-remove-button__icon-scheme` styles in `assets/0-freshwater.css.liquid`). **Cart drawer** line items use horizontal overflow hidden on `cart-drawer-items` (and `cart-drawer .drawer__inner` in the short-viewport breakpoint) in `assets/0-component-cart-drawer.css`. Markup: `snippets/0-cart-drawer.liquid` only (Dawn `snippets/cart-drawer.liquid` unchanged). Main cart: new `sections/0-main-cart-items.liquid` (Freshwater clone); `templates/cart.json` uses section type `0-main-cart-items`; `assets/0-cart.js` mirrors Dawn `cart.js` with `section_id=0-main-cart-items` for Section Rendering (loaded only from `0-main-cart-items` when cart is not drawer). Fixed duplicate `class` attribute on the drawer remove button when remove is disallowed.
- **Hero (FW) — Link URL:** **Link URL** (`fresh_video_url`) now drives a stretched overlay link so the hero is clickable on the storefront. The text box stacks above with `pointer-events` tuned so nested controls stay interactive (same pattern as linked Multi Column slides). `sections/0-hero-1.liquid`.
- **Hero Carousel (FW) — slide Link URL:** Each **Slide** block’s **Link URL** (`fresh_video_url`, or `fresh_slide_url` when that key exists on imported JSON) now renders the same stretched overlay pattern as Multi Column slides. Section styles in `sections/0-hero-2.liquid`; markup in `snippets/0-slide-2.liquid`. URL resolution avoids Liquid `| default:` so an empty `fresh_slide_url` string cannot hide `fresh_video_url` (Hero Carousel slide blocks only define the latter in schema).
- **Multi Column (FW):** Slide cards (`.fresh-slide`) use `overflow: hidden` so wide buttons or other blocks cannot paint outside the slide bounds in multi-column carousels. `sections/0-multi-column-1.liquid`.
- **Multi Column (FW) — slide links:** `snippets/0-slide-1.liquid` resolves **Link URL** with the same non-`default` fallback so an empty `fresh_slide_url` does not suppress `fresh_video_url`; `href` is escaped; slide aria fallback uses `sections.slideshow.slide`.
- **Two Column (FW) — media Link URL:** **Link URL** (`fresh_two_col_media_video_url`) now wraps the media column image or video area with a stretched overlay link (same pattern as Hero / Multi Column slides). Video controls stay above the overlay via `z-index` and `pointer-events`. Custom Liquid above/below the media block is outside the link. Schema **info** explains behavior. `sections/0-two-column-1.liquid`.
- **Graphic block — Link URL:** **Link URL** (`fresh_graphic_url`) on the **Graphic** block now renders a stretched overlay on the image (One Column, Two Column, Hero box graphic, list-block image, etc.). Accessible name uses image alt when present, otherwise `sections.slideshow.slide`. On **Multi Column** and **Hero Carousel** slides, when the slide **Link URL** is set, the graphic block link is omitted so the DOM does not contain nested anchors. `snippets/0-block-graphic-1.liquid`, `snippets/0-slide-1.liquid`, `snippets/0-slide-2.liquid`; schema **info** on **Graphic** blocks in `sections/0-one-column-1.liquid`, `sections/0-two-column-1.liquid`, `sections/0-hero-1.liquid`.
- **Video block — Link URL:** **Link URL** (`fresh_video_url`) is now implemented in `snippets/0-block-video-1.liquid` (stretched overlay; `aria-label` uses `sections.slideshow.slide`; stacking depends on autoplay — see follow-up bullet). **Hero** main media and **Two Column** media column pass `suppress_video_link` when the section stretch link is already set; **Multi Column** / **Hero Carousel** slides pass it when the slide link is set (same pattern as the graphic block). **Video** block schema gains **Link URL** where missing (`sections/0-hero-1.liquid`, `sections/0-two-column-1.liquid`); **info** text added on One Column, Main Product, Hero, and Two Column.
- **Video block — Link URL (follow-up):** Use `aria-label` only on the stretch link (no `video.alt` in the link body). When **Autoplay** is on, `pointer-events` + z-index route clicks: stretch link is the first child inside `.responsive-video-container`, `.responsive-video-content` uses `pointer-events: none` so clicks reach the link, while `.video-sound-button` keeps `pointer-events: auto`.
- **Video sound control:** The broken “sound” image was from `freshCreateVideoSoundButtons()` using `icon-sound-off.webp` / `icon-sound.webp`, which are not in the theme assets (404). Replaced with inline SVG (`FRESH_VIDEO_SOUND_SVG_OFF` / `ON` in `assets/0-freshwater.js.liquid`). Added positioning styles for `.video-sound-button` in `assets/0-freshwater.css.liquid` so the control sits over the video instead of in normal flow below it.
- **Video block — Link URL (click-through):** Autoplay is normalized in `snippets/0-block-video-1.liquid` so explicit `false` turns controls on; missing/nil falls back to the section media autoplay setting then **true** (matches schema defaults). Wrapper adds `fresh-video-wrap--linked-autoplay` when linked + autoplay. Stretch-link stacking and `pointer-events` moved to **class-based** rules in `assets/0-freshwater.css.liquid` (no per-instance `#id` block) so clicks reliably reach `.fresh-video-link--stretch` while the sound control keeps `pointer-events: auto`.

## [3.5.1] - 2026-04-09

### Added
- **Utility class `fresh-button--force-standard-1`:** Add this class alone (no `fresh-button` needed) to any storefront control to force Freshwater **standard scheme 1** look—typography, padding, border, and colors from `--standard-button-*-1` on the nearest color scheme—with `!important` so it wins over Dawn, secondary/tertiary variants, and typical app CSS. Responsive rules follow the same `768px` / `767px` split as other Freshwater buttons. **Accelerated checkout** (`shopify-accelerated-checkout`, `shopify-accelerated-checkout-cart`): put the class on the custom element or a wrapper; only [Shopify-supported custom properties](https://shopify.dev/docs/storefronts/themes/pricing-payments/accelerated-checkout) apply inside the closed shadow DOM (radius, shadow, block-size aligned to theme standard 1). See [README — Force standard scheme 1 button](README.md#force-standard-scheme-1-button-fresh-button--force-standard-1). `assets/0-freshwater.css.liquid`.
- **Pre-launch review:** [docs/LAUNCH-CHECKLIST.md](docs/LAUNCH-CHECKLIST.md) (printable checklist), [README](README.md#pre-launch-review-shareable-checklist) section for stakeholders (CTAs → SEO → accessibility; theme-only, third-party apps out of scope), and [`scripts/prelaunch.sh`](scripts/prelaunch.sh) to run Theme Check plus optional `href` grep hints locally.
- **Multi Column (FW) — slide link & layout:** With **Link URL** set on a slide, the **whole slide** is clickable via a stretched overlay link (nested links stay usable). Section **Carousel** settings add **Pin last content block to slide bottom** (desktop and mobile, default off); with **Make slides same height** on, the last `.fresh-block` in each slide gets `margin-top: auto`. Same-height slides now stretch `.fresh-slide-id-container` in **carousel and grid** layouts. `snippets/0-slide-1.liquid`, `sections/0-multi-column-1.liquid`.
- **Hero (FW) & Hero Carousel (FW) — desktop text box max width:** Under **Hero Box → Desktop settings**, **Text Box Max Width** lets merchants cap the content box with **None**, **Pixels (px)** (0–800, step 8; Shopify range settings allow at most 101 steps), or **Percent (%)** (0–100). **Text Box Width** still sets `width` as a percentage; when a max is set and the value is greater than zero, CSS adds `max-width` at `min-width: 768px` only. Missing or legacy JSON defaults to no max. Hero (FW): `sections/0-hero-1.liquid`. Hero Carousel (FW): slide block schema in `sections/0-hero-2.liquid` and styles in `snippets/0-slide-2.liquid`.
- **Two Column (FW) — video mode:** New **“Use video instead of image?”** toggle (`fresh_two_col_use_video`). When off, the media column always uses images; when on, uploaded videos are used when set. Existing sections without the new key keep prior behavior (video when sources exist). Video picker and autoplay sit behind the toggle in the theme editor.
- **Hero (FW) — video mode:** Same pattern as Two Column — **`fresh_hero_use_video`** replaces the old “Video Settings” toggle label; storefront output respects the toggle with legacy-friendly handling when the setting is unset.
- **Carousel navigation (Multi Column FW & Hero Carousel FW):** Single **“Carousel Navigation”** checkbox per breakpoint replaces separate **Carousel Arrows** and **Carousel Dots** toggles. All arrow- and dot-related controls sit behind that group; `visible_if` uses `!= false` so older JSON without the new keys still shows the controls.

### Removed
- **Legacy marquee section** (`sections/0-marquee-1.liquid`): The old `fresh-marquee` implementation is removed. The scrolling marquee is **`0-marquee-2.liquid` only**, still the section type in JSON. **Marquee (FW)** in the theme editor is this implementation (the “2” was dropped from the display name only). Themes whose JSON still references `0-marquee-1` must be fixed manually: remove the broken section or replace it with **Marquee (FW)** and reconfigure.

### Changed
- **Theme Check:** Added `.theme-check.yml` extending `theme-check:recommended` with Freshwater-tuned rules (disable noisy `VariableName`; downgrade locale/HTML checks; ignore `LiquidHTMLSyntaxError` on `assets/0-freshwater.js.liquid`). Prelaunch script should exit **0** when there are **no errors**; many **warnings** may remain. README and `docs/LAUNCH-CHECKLIST.md` document this.
- **Multi Column (FW) — slide overlay:** The **Overlay** Liquid field has **no** schema default (Shopify does not allow an empty default on Liquid settings); storefront output is wrapped in `<div class="fresh-overlay">` in `snippets/0-slide-1.liquid`. README explains section-wide styling via **Global Settings → Custom Class Names** and `assets/1-client.css.liquid`, while keeping inline positioning examples and the overlay docs anchor `#overlay-positioning-classes`.
- **Accordion & List blocks — SVG icon stroke:** In `snippets/0-block-accordion-1.liquid` and `snippets/0-block-list-1.liquid`, icon `svg path` elements again use **both** `fill` and `stroke` (theme icon color or CSS variable) so icons such as **plus** and **caret** match prior visual weight. **Arrow** and **arrow-circle** are exceptions: paths under `svg.fresh-icon--arrow` and `svg.fresh-icon--arrow-circle` set `stroke: none` and `stroke-width: 0` so filled chevrons are not outlined twice (classes are set on those SVGs in `snippets/0-icon-1.liquid`).
- **Theme typography defaults:** Sub-header and body **letter spacing** (desktop and mobile) now default to **0** in `config/settings_schema.json` (header and standard button letter spacing were already **0**).
- **Accordion block (One Column / Two Column / Hero / Main Product FW):** Question (header) and answer (body) each have separate **desktop** and **mobile** controls for text color, alignment, font size, font weight, and line height. Icon color, text/icon spacing, and margin bottom are unchanged. When a new field is left at default (transparent color, **Inherit** alignment, or `0` for size/weight/line height), the snippet falls back to the previous shared typography settings. Those legacy settings remain in the schema as hidden fields (`visible_if: false`) so existing block JSON is preserved on save.
- **Marquee (FW):** Theme editor label is **Marquee (FW)** (was **Marquee 2 (FW)**); filenames and internal classes (e.g. `fresh-section__marquee-2`) are unchanged.
- **Multi Column (FW) & Hero Carousel (FW) — section schema labels:** Device toggles in the carousel and block groups now use concise **Desktop** / **Mobile** labels under explicit **CAROUSEL** and **BLOCK** headings. Square dividers (**■**) appear between major groups (not between Desktop and Mobile) for quicker scanning in the theme editor.
- **Theme editor separators:** Shortened decorative separator strings so they fit the narrower Shopify section sidebar: black square rows (one **■** removed), colon rows (`:`), and degree-symbol rows (`°`) in affected sections and in **Typography** groups in `config/settings_schema.json`.
- **Hero Carousel (FW) — carousel UI:** **CAROUSEL ARROWS** / **CAROUSEL DOTS** labels removed in favor of the unified **Carousel Navigation** toggle; redundant separators between those blocks removed.
- **Two Column (FW):** Removed duplicate colon separator row directly under **“Use video instead of image?”** (one divider before Link URL remains).
- **Footer architecture:** Footer group now defaults to Dawn’s native `sections/footer.liquid`. Added separate `sections/0-fw-footer.liquid` for hardcoded Freshwater footer use, with a new **Menu Handle** setting (`fw_menu_handle`) that drives the SHOP link column in `snippets/1-footer-freshwater.liquid`. Legacy hybrid footer section `sections/0-footer.liquid` (and its `fresh_footer_enable` toggle path) was removed to avoid split behavior.

### Fixed
- **Dawn buttons and `h1` / `.h1` typography:** Native Dawn `.button` (and related controls) no longer inherit only the body font— they use **Standard Button → Font Family** from theme settings, so cart CTAs and other Dawn buttons match Freshwater button typography. `h1` and `.h1` use **Typography → Default Header Font Family**, aligning product titles, cart headings, and similar Dawn markup with Freshwater header blocks instead of Shopify’s separate heading font. `assets/0-freshwater.css.liquid`.
- **Dawn foreground opacity 0.75 killed at source.** Dawn sets `color: rgba(var(--color-foreground), 0.75)` on `body`/`.color-scheme-*` and ~25 component selectors across its CSS files. Previous approach used a growing override selector list in `0-freshwater.css.liquid` and `0-theme-freshwater-1.liquid` — but some elements still inherited the translucency (e.g. `p.product__text.inline-richtext` on PDP). Now every `0.75` foreground-color instance is changed to `1` directly in the Dawn source files: `snippets/0-theme-dawn-2.liquid`, `assets/base.css`, `assets/section-main-product.css`, `assets/section-footer.css`, `assets/section-blog-post.css`, `assets/component-card.css`, `assets/component-cart.css`, `assets/component-mega-menu.css`, `assets/component-price.css`, `assets/component-slider.css`, `assets/component-localization-form.css`, `assets/component-pickup-availability.css`, `assets/component-facets.css`, `assets/component-predictive-search.css`, `assets/component-model-viewer-ui.css`, `assets/component-volume-pricing.css`, `assets/quick-add.css`, `assets/customer.css`, `layout/password.liquid`, `snippets/1-footer-freshwater.liquid`. The old override block in `0-freshwater.css.liquid` and the `body.fresh`/`.color-*` overrides in `0-theme-freshwater-1.liquid` are removed. **On Dawn upgrades, re-apply this change** — see README → Modified Dawn Files → item 4.
- **Theme Check hygiene:** Replaced deprecated `img_url` with `image_url` for video posters in `snippets/0-block-video-1.liquid`. Moved product JSON-LD primary image logic into `snippets/0-json-ld-prod.liquid` (removes unused `seo_media` assigns in product sections). Cleared other `UnusedAssign` warnings: `sections/main-search.liquid` (unused `product_settings` capture), `snippets/0-block-rating-1.liquid` (unused breakpoint count assigns). Wrapped swatch `help_text` capture in `snippets/product-variant-options.liquid` with `theme-check-disable UnusedAssign` (Theme Check does not treat variables passed to `render` as used). Disabled `MatchingTranslations` in `.theme-check.yml` until locale parity is prioritized.
- **FW Footer — section padding ignored:** `snippets/1-footer-freshwater.liquid` had `body.fresh .section-…-padding { padding-top: 0; padding-bottom: 0; }`, which beat the FW Footer section’s `{%- style -%}` padding (higher specificity). Removed that override so **Top margin** / **Padding** in **FW Footer** apply as expected.
- **FW Footer — top margin appeared ineffective:** `{%- style -%}` used a global `.footer { margin-top }`, which can lose to another footer section’s `.footer` rule and is sensitive to margin collapse. Spacing is now applied on the section wrapper `#shopify-section-…` and padding is scoped under that wrapper so **FW Footer** controls only affect this section.
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

[3.5.5]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.5.5
[3.5.4]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.5.4
[3.5.3]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.5.3
[3.5.2]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.5.2
[3.5.1]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.5.1
[3.4.1]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.4.1
[3.4.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.4.0
[3.3.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.3.0
[3.2.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.2.0
[3.1.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.1.0
[3.0.0]: https://github.com/freshwaterdesigns/freshwater-v3/releases/tag/v3.0.0


{% endraw %}
