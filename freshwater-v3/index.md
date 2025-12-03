---
title: "Freshwater v3 Theme Docs"
layout: default
---

{% raw %}
# Freshwater v3.1.0

**Based on Dawn 15.4.0**

Freshwater is a custom Shopify theme built on top of Dawn 15.4.0. This theme maintains compatibility with Dawn's core functionality while adding custom features, sections, and styling.

## 🎯 Philosophy

**Important:** This theme is designed to be easily upgradable to new versions of Dawn. To achieve this:

- **DO NOT** edit native Dawn files directly
- **DO** copy Dawn files and rename them with a `0-` prefix for customizations
- **DO** reference the `0-` prefixed versions in JSON includes and Liquid renders
- **DO** keep all custom code in `0-` prefixed files

This approach allows you to:
- Upgrade Dawn without losing customizations
- Easily identify which files are custom vs. native Dawn
- Maintain a clean separation between base theme and customizations

---

## 📋 Key Differences from Dawn 15.4.0

### Modified Dawn Files

These native Dawn files have been modified and should be tracked for upgrades:

1. **`layout/theme.liquid`**
   - Replaced Dawn theme snippets with `0-theme-dawn-*` versions
   - Added `0-theme-freshwater-1` and `0-theme-freshwater-2` snippets
   - Added custom body classes (`fresh`, template-specific classes)
   - Changed `{% sections 'footer-group' %}` to `{% section '0-footer' %}`

2. **`sections/header.liquid`**
   - Changed `render 'header-mega-menu'` to `render '0-header-mega-menu'`

3. **`config/settings_schema.json`**
   - Added extensive custom settings for:
     - Button styling (3 button styles)
     - Color scheme extensions (header, subheader, icons colors)
     - Typography settings
     - Custom section/block settings
   - Updated theme version to show "15.4.0, Freshwater 3.1.0"

### Custom Files (0- Prefixed)

#### Assets
- `0-freshwater.js.liquid` - Core Freshwater JavaScript with debug utility (vanilla JS, no jQuery dependency)
- `0-freshwater.css.liquid` - Core Freshwater CSS
- `0-client.js.liquid` - Client-specific JavaScript (editable)
- `0-client.css.liquid` - Client-specific CSS (editable)
- `0-tiny-slider.min.js` / `0-tiny-slider.css` - Tiny-Slider carousel library (vanilla JS)
- `0-lazyload.min.js` - Vanilla Lazyload library
- `0-bootstrap.min-1.css` - Bootstrap CSS
- `0-magnify.js` - Image magnify library

#### Snippets
- `0-theme-dawn-1.liquid` through `0-theme-dawn-4.liquid` - Split Dawn theme.liquid head/body sections
- `0-theme-freshwater-1.liquid` - Freshwater head includes (fonts, Bootstrap, Tiny-Slider, custom CSS)
- `0-theme-freshwater-2.liquid` - Freshwater body includes (Lazyload, custom JS)
- `0-header-mega-menu.liquid` - Custom mega menu with submenu support (adds `{% render '0-theme-submenu', link: link %}`)
- `0-header-freshwater-menu.liquid` - Custom Freshwater menu with empty dropdowns (adds `{% render '0-theme-submenu-freshwater', link: link %}`)
- `0-theme-submenu.liquid` - Custom submenu renderer for mega menu (displays `mega_menu_nav_item` blocks)
- `0-theme-submenu-freshwater.liquid` - Custom submenu renderer for Freshwater menu (hardcoded conditionals per menu handle)
- `0-header-drawer.liquid` - Mobile drawer navigation renderer
- `0-header-drawer-freshwater.liquid` - Custom Freshwater mobile drawer menu placeholder
- `0-footer-freshwater.liquid` - Custom Freshwater footer content renderer (placeholder by default)
- `0-json-ld-org.liquid` - Organization structured data
- `0-json-ld-prod.liquid` - Product structured data
- `0-icon-1.liquid` - Custom icon renderer
- `0-block-*` snippets - Custom block types for sections:
  - `0-block-accordion-1.liquid`
  - `0-block-body-1.liquid`
  - `0-block-button-1.liquid`
  - `0-block-graphic-1.liquid`
  - `0-block-header-1.liquid`
  - `0-block-html-1.liquid`
  - `0-block-liquid-1.liquid`
  - `0-block-list-1.liquid`
  - `0-block-rating-1.liquid`
  - `0-block-video-1.liquid`
- `0-slide-1.liquid` / `0-slide-2.liquid` - Custom slide templates

#### Sections
- `0-header.liquid` - Custom header section with Freshwater menu options and mobile drawer footer toggle
- `0-hero-1.liquid` / `0-hero-2.liquid` - Custom hero sections
- `0-main-product.liquid` - Custom product page section
- `0-marquee-1.liquid` - Custom marquee section
- `0-multi-column-1.liquid` - Custom multi-column section
- `0-one-column-1.liquid` - Custom one-column section
- `0-two-column-1.liquid` - Custom two-column section
- `0-footer.liquid` - Custom footer section with Freshwater footer toggle

---

## 🚀 Conversion Guide: Dawn 15.4.0 → Freshwater v3.1.0

Follow these steps to convert a fresh Dawn 15.4.0 installation to Freshwater v3.1.0:

### Step 1: Backup Your Dawn Installation

```bash
# Create a backup of your current Dawn theme
cp -r dawn-theme dawn-theme-backup
```

### Step 2: Add Custom Assets

Copy all `0-*` files from Freshwater's `assets/` directory:

```bash
# Copy all Freshwater custom assets
cp freshwater-v3/assets/0-* your-dawn-theme/assets/
```

**Key assets to add:**
- `0-freshwater.js.liquid`
- `0-freshwater.css.liquid`
- `0-client.js.liquid`
- `0-client.css.liquid`
- `0-tiny-slider.min.js`
- `0-tiny-slider.css`
- `0-lazyload.min.js`
- `0-bootstrap.min-1.css`
- `0-magnify.js`

### Step 3: Add Custom Snippets

Copy all `0-*` snippets from Freshwater's `snippets/` directory:

```bash
# Copy all Freshwater custom snippets
cp freshwater-v3/snippets/0-* your-dawn-theme/snippets/
```

**Key snippets:**
- `0-theme-dawn-1.liquid` through `0-theme-dawn-4.liquid`
- `0-theme-freshwater-1.liquid`
- `0-theme-freshwater-2.liquid`
- `0-header-mega-menu.liquid`
- `0-header-freshwater-menu.liquid`
- `0-theme-submenu.liquid`
- `0-theme-submenu-freshwater.liquid`
- `0-header-drawer.liquid`
- `0-footer-freshwater.liquid`
- `0-json-ld-org.liquid`
- `0-json-ld-prod.liquid`
- All `0-block-*` snippets
- `0-slide-1.liquid` and `0-slide-2.liquid`
- `0-icon-1.liquid`

### Step 4: Add Custom Sections

Copy all `0-*` sections from Freshwater's `sections/` directory:

```bash
# Copy all Freshwater custom sections
cp freshwater-v3/sections/0-* your-dawn-theme/sections/
```

**Key sections:**
- `0-hero-1.liquid`
- `0-hero-2.liquid`
- `0-main-product.liquid`
- `0-marquee-1.liquid`
- `0-multi-column-1.liquid`
- `0-one-column-1.liquid`
- `0-two-column-1.liquid`
- `0-footer.liquid` - Custom footer section with Freshwater footer toggle

### Step 5: Modify `layout/theme.liquid`

Replace the entire `layout/theme.liquid` file with Freshwater's version:

```bash
cp freshwater-v3/layout/theme.liquid your-dawn-theme/layout/theme.liquid
```

**Key changes:**
- Head section uses `0-theme-dawn-1`, `0-theme-dawn-2`, and `0-theme-freshwater-1`
- Body section uses `0-theme-dawn-3`, `0-theme-dawn-4`, and `0-theme-freshwater-2`
- Footer section uses `{% section '0-footer' %}` instead of `{% sections 'footer-group' %}`
- Added custom body classes

**Important:** After copying the file, verify that `{% section '0-footer' %}` is used instead of `{% sections 'footer-group' %}`.

### Step 7: Modify `sections/header.liquid`

In `sections/header.liquid`, find and replace:

**Find:**
```liquid
render 'header-mega-menu'
```

**Replace with:**
```liquid
render '0-header-mega-menu'
```

### Step 8: Update `config/settings_schema.json`

**Option A: Merge Settings (Recommended)**
1. Open both `freshwater-v3/config/settings_schema.json` and your Dawn `config/settings_schema.json`
2. Copy the custom settings from Freshwater (all settings with `"name": "0 *"` or custom Freshwater settings)
3. Add them to your Dawn settings_schema.json
4. Update the theme version in the `theme_info` section:
   ```json
   "theme_version": "15.4.0, Freshwater 3.1.0"
   ```
5. **Update `page_width` setting:** The `page_width` range has changed. If you're upgrading an existing Dawn theme, you must migrate the setting value first (see migration steps below).

**Option B: Replace Entire File (Easier but loses any custom settings)**
```bash
cp freshwater-v3/config/settings_schema.json your-dawn-theme/config/settings_schema.json
```

**⚠️ Important: `page_width` Migration Required**

💡 **Note:** *Dawn's initial value for the `page_width` is 1200px. Since that value is not in the new slider range (which uses 640px increments: 640, 1280, 1920, 2560, 3200, 3840), the original slider must be updated in several increments first to avoid Shopify CLI errors. Use the following steps:*

1. *Change the initial `step` value from `100` to `200`*
2. *Change the initial `max` value from `1600` to `3200`*
3. *Change the initial `default` value from `1200` to `3200`*
4. *Set the `page_width` to `3200` in your theme settings (via Theme Editor)*
5. *Then update to the final Freshwater values: `min: 640, max: 3840, step: 640, default: 1920`*

*If you skip these migration steps, you'll get a Shopify CLI error: "New schema is incompatible with the current setting value. Setting 'page_width' must be a step in the range"*

**Important Freshwater Settings Added:**
- `0 Button` - Button styling settings (3 button styles)
- Color scheme extensions (header, subheader, icons)
- Typography settings
- Custom section/block settings

### Step 9: Verify File References

Check that all references point to `0-` prefixed files:

1. **In `layout/theme.liquid`:**
   - Should reference `0-theme-dawn-*` and `0-theme-freshwater-*` snippets
   - Should use `{% section '0-footer' %}` instead of `{% sections 'footer-group' %}`

2. **In `sections/header.liquid`:**
   - Should conditionally reference `0-header-mega-menu` or `0-header-freshwater-menu` based on menu type setting

3. **In `sections/0-footer.liquid`:**
   - Should reference `0-footer-freshwater` snippet when `fresh_footer_enable` setting is `true`

4. **In `snippets/0-header-mega-menu.liquid`:**
   - Should reference `0-theme-submenu`

5. **In `snippets/0-header-freshwater-menu.liquid`:**
   - Should reference `0-theme-submenu-freshwater`

6. **In `snippets/0-theme-freshwater-1.liquid`:**
   - Should reference `0-tiny-slider.*`, `0-bootstrap.*`, `0-freshwater.css`, `0-client.css`

7. **In `snippets/0-theme-freshwater-2.liquid`:**
   - Should reference `0-lazyload.min.js`, `0-freshwater.js`, `0-client.js`

### Step 10: Test the Installation

1. Upload the theme to your Shopify store
2. Test key functionality:
   - Header menus (mega menu, Freshwater menu, dropdown menu)
   - Mobile drawer navigation (default Dawn menu)
   - Custom sections (hero, marquee, multi-column)
   - JavaScript functionality (Tiny-Slider carousels, lazy loading)
   - Console logs (add `?debug_mode=true` to URL)
3. Check browser console for errors
4. Verify all custom sections appear in theme editor

### Step 11: Update Theme Settings

After installation, configure:
1. **Color Schemes:** Set custom header, subheader, and icon colors
2. **Button Styles:** Configure the 3 button style options
3. **Typography:** Set custom font families and weights
4. **Menu:** 
   - Select desktop menu type (Dropdown, Mega menu, Drawer, or Freshwater)
   - Add mega menu nav item blocks if using mega menu
   - Customize Freshwater menu dropdowns in `0-theme-submenu-freshwater.liquid` if using Freshwater menu

---

## 📁 File Structure Overview

```
freshwater-v3/
├── assets/
│   ├── 0-*                    # All Freshwater custom assets
│   └── [Dawn assets]          # Unmodified Dawn assets
├── config/
│   ├── settings_schema.json   # Modified (added Freshwater settings)
│   └── settings_data.json     # May contain Freshwater defaults
├── layout/
│   └── theme.liquid          # Modified (uses 0- prefixed snippets)
├── sections/
│   ├── 0-*                   # Freshwater custom sections (including 0-footer.liquid)
│   ├── header.liquid         # Modified (references 0-header-mega-menu)
│   └── [Dawn sections]       # Unmodified Dawn sections (footer.liquid still exists but not used)
├── snippets/
│   ├── 0-*                   # Freshwater custom snippets
│   └── [Dawn snippets]       # Unmodified Dawn snippets (except header-mega-menu reference)
└── templates/
    └── [Dawn templates]      # Unmodified Dawn templates
```

---

## 🔧 Key Features

### Debug Utility

Freshwater includes a debug utility that only logs when `debug_mode=true` is in the URL:

```javascript
// Usage in custom files (0- prefixed)
if (window.Freshwater) {
  window.Freshwater.console('log', 'Your message');
  window.Freshwater.console('warn', 'Warning message');
  window.Freshwater.console('error', errorObject);
}
```

**Quick Debug Recipe:**

1. Open a storefront URL and append `?debug_mode=true`
2. Open DevTools console (F12 or right-click → Inspect → Console tab)
3. Run helpers like `Freshwater.checkCarousels()` for Tiny-Slider debugging
4. All Freshwater console logs will appear with the 🔹 Freshwater prefix

**Example:**
```
https://your-store.myshopify.com/?debug_mode=true
```

Then in console:
```javascript
Freshwater.checkCarousels()  // Check carousel initialization
```

**Note:** Only `0-` prefixed files use this utility. Native Dawn files use standard `console.log/error`.

### JavaScript Libraries

- **Tiny-Slider** (`0-tiny-slider.*`) - Vanilla JS carousel library (no jQuery dependency)
  - **Version:** 2.9.4 (UMD build)
  - **Note:** We use version 2.9.4 specifically due to a bug in newer versions (2.9.5+) that causes `HierarchyRequestError: Failed to execute 'appendChild' on 'Node': Only one element on document allowed` when initializing carousels. Version 2.9.4 is stable and works correctly with our implementation.
- **Vanilla Lazyload** (`0-lazyload.min.js`) - For lazy loading images
- **Bootstrap** (`0-bootstrap.min-1.css`) - CSS framework
- **Magnify** (`0-magnify.js`) - Image zoom functionality

**Note:** Freshwater uses vanilla JavaScript exclusively. All carousel functionality is handled by Tiny-Slider, which does not require jQuery.

### Custom Sections

All custom sections support:
- Custom block types (`0-block-*`)
- Responsive settings (mobile/desktop)
- Color scheme integration
- Custom CSS classes and IDs

---

## 🎨 CSS Architecture

### Dawn Button Overrides

Freshwater overrides all Dawn button styles to use Freshwater's standard scheme 1 styling. This ensures consistent button appearance throughout the theme.

**Implementation Details:**

- **Color Application:** Dawn buttons receive Freshwater standard scheme 1 colors via Dawn's color scheme classes (`.color-scheme-1`, `.color-scheme-2`, etc.) without breakpoint suffixes, matching Dawn's color scheme pattern.
- **Structural Styles:** Button structural properties (border-radius, padding, font-size, etc.) are applied within breakpoint media queries (`@media (min-width: 768px)` and `@media (max-width: 767px)`) for responsive behavior.
- **Affected Classes:** All Dawn button classes are overridden:
  - `.button`, `.button--secondary`, `.button--tertiary`
  - `.shopify-challenge__button`
  - `.customer button`
  - `button.shopify-payment-button__button--unbranded`
  - `.product-form__submit`
  - `.cart__checkout-button`

**File:** `assets/0-freshwater.css.liquid` (lines 323-537)

**Note:** To customize Dawn button styles, override them in `assets/0-client.css.liquid` rather than editing `0-freshwater.css.liquid`.

### Overlay Positioning Classes {#overlay-positioning-classes}

The `.fresh-overlay` class is used in Multi-Column section slides to position content over media (images or videos). By default, overlays are centered (center-center).

**Available Positioning Classes:**

- **`.fresh-overlay--bottom-left`** - Positions overlay at the bottom-left corner of the media
- **`.fresh-overlay--bottom-right`** - Positions overlay at the bottom-right corner of the media

**Usage:**

In the Multi-Column section slide's "Overlay" Liquid field, add the positioning class directly to your content:

```liquid
<div class="fresh-overlay fresh-overlay--bottom-left">
  <p>Your overlay content here</p>
</div>
```

Or for bottom-right:

```liquid
<div class="fresh-overlay fresh-overlay--bottom-right">
  <p>Your overlay content here</p>
</div>
```

**Note:** The `.fresh-overlay` wrapper div is automatically added by the theme, so you only need to add the positioning class modifier (e.g., `fresh-overlay--bottom-left`) to your content div.

**Files:**
- `assets/0-freshwater.css.liquid` - Overlay positioning CSS (lines 1911-1935)
- `sections/0-multi-column-1.liquid` - Section-specific overlay styles
- `snippets/0-slide-1.liquid` - Overlay rendering logic

**Documentation:** See [Freshwater v3 documentation](https://freshwaterdesigns.github.io/freshwater-docs/freshwater-v3/) for detailed usage examples and additional positioning options.

---

## 🧭 Customizing Navigation

### Mobile Drawer Navigation

The mobile drawer navigation can display either the default Dawn menu navigation or a custom Freshwater footer placeholder.

**Files:**
- `snippets/0-header-drawer.liquid` - Mobile drawer renderer
- `snippets/0-header-drawer-freshwater.liquid` - Custom Freshwater mobile drawer menu placeholder

**How It Works:**

1. **Default Navigation (Default):**
   - The mobile drawer displays the default Dawn menu navigation
   - Navigation uses the menu set in **Header** → **Menu** setting
   - The drawer supports all standard Dawn menu features (multi-level menus, submenus, etc.)

2. **Freshwater Footer (Optional):**
   - Go to **Theme Editor** → **Header** section
   - Under **Freshwater Mobile Drawer Menu**, check **Enable Freshwater Mobile Drawer Menu**
   - When enabled, the mobile drawer content is replaced with a custom Freshwater footer placeholder
   - Edit `snippets/0-header-drawer-freshwater.liquid` to customize the footer content with your HTML

**Note:** The drawer is rendered by `sections/0-header.liquid` which calls `render '0-header-drawer'`.

### Desktop Mega Menu Navigation

The desktop mega menu uses a block-based system that can be configured through the Shopify theme editor.

**Files:**
- `sections/0-header.liquid` - Header section that renders the mega menu (calls `render '0-header-mega-menu'`)
- `snippets/0-header-mega-menu.liquid` - Main mega menu structure that renders standard menu links and calls the submenu renderer
- `snippets/0-theme-submenu.liquid` - Custom submenu content renderer that displays `mega_menu_nav_item` blocks

**How to Update:**

1. **Via Theme Editor (Recommended):**
   - Go to **Theme Editor** → **Header** section
   - Scroll to the **Blocks** section
   - Click **Add block** → Select **Mega Menu Nav Item**
   - Configure each block:
     - **Parent menu item handle:** Enter the handle of the top-level menu item this card belongs to (e.g., `about`, `contact`, `catalog`)
       - To find the handle: Look at the menu item URL or use the lowercase, hyphenated version of the menu item name
     - **Image (optional):** Upload an image for the navigation card
     - **Text (optional):** Add text to display below the image
     - **URL:** Set the destination URL for the navigation card
   - Add multiple blocks with the same `menu_item_handle` to create multiple cards for that menu item
   - Reorder blocks by dragging

2. **How It Works:**
   - When a menu item has child links, `0-header-mega-menu.liquid` renders the standard Dawn menu structure
   - It calls `{% render '0-theme-submenu', link: link %}` to inject custom submenu content
   - `0-theme-submenu.liquid` accesses `section.blocks` directly (snippets have access to the section context when rendered from a section)
   - It loops through all `mega_menu_nav_item` blocks in the section
   - It matches blocks where `block.settings.menu_item_handle` equals the current menu item's handle (`link.handle`)
   - Matching blocks are rendered as navigation cards with images and text
   - The cards are wrapped in a `.mega-menu-submenu` container

3. **Block Structure:**
   - Each `mega_menu_nav_item` block creates a navigation card
   - Cards are displayed in a grid layout (styled via CSS in `0-freshwater.css.liquid`)
   - Each card can have:
     - An image (optional)
     - Text (optional)
     - A clickable URL

4. **Styling:**
   - Base styles for `.mega-menu-submenu` and `.mega-menu-submenu__item` are defined in `assets/0-freshwater.css.liquid` (lines 682-699)
   - To customize the appearance, override these styles in `assets/0-client.css.liquid`
   - Available classes:
     - `.mega-menu-submenu` - Container for all navigation cards
     - `.mega-menu-submenu__item` - Individual navigation card wrapper
     - `.mega-menu-submenu__link` - Link styling for each card

**Note:** 
- The mega menu is only displayed when **Header** → **Desktop menu type** is set to **Mega menu** in the theme settings
- The menu item must have child links for the mega menu dropdown to appear
- Custom navigation cards appear alongside the standard menu links in the dropdown

### Desktop Freshwater Menu Navigation

The Freshwater desktop menu provides a customizable dropdown system where each menu item can have completely custom content.

**Files:**
- `sections/0-header.liquid` - Header section that conditionally renders the Freshwater menu (calls `render '0-header-freshwater-menu'`)
- `snippets/0-header-freshwater-menu.liquid` - Main Freshwater menu structure that renders menu links with empty dropdowns
- `snippets/0-theme-submenu-freshwater.liquid` - Custom submenu content renderer with hardcoded conditionals for each menu handle

**How to Update:**

1. **Enable Freshwater Menu:**
   - Go to **Theme Editor** → **Header** section
   - Set **Desktop menu type** to **Freshwater**

2. **Customize Dropdown Content:**
   - Edit `snippets/0-theme-submenu-freshwater.liquid`
   - Add conditionals for each menu item handle:
     ```liquid
     {%- if link_handle == 'your-menu-handle' -%}
         Your custom content here
     {%- elsif link_handle == 'another-handle' -%}
         More custom content
     {%- endif -%}
     ```
   - The `link_handle` is automatically derived from the menu item's handle (e.g., `about`, `shop`, `contact`)
   - You can add any HTML, Liquid, or block content within each conditional

3. **How It Works:**
   - When a menu item has child links, `0-header-freshwater-menu.liquid` renders an empty dropdown container
   - It calls `{% render '0-theme-submenu-freshwater', link: link %}` to inject custom content
   - `0-theme-submenu-freshwater.liquid` receives the `link` object and checks `link.handle` against hardcoded conditionals
   - Matching conditionals render their custom content
   - Unlike the mega menu, the Freshwater menu does NOT render standard menu child links - only custom content

4. **Styling:**
   - The Freshwater menu uses the same CSS as the mega menu (`component-mega-menu.css`)
   - Customize dropdown content styling in `assets/0-client.css.liquid`
   - The dropdown container uses the `.mega-menu__content` class

**Note:** 
- The Freshwater menu is only displayed when **Header** → **Desktop menu type** is set to **Freshwater** in the theme settings
- The menu item must have child links for the dropdown to appear (even though child links aren't displayed)
- Each dropdown starts completely empty - all content must be added via `0-theme-submenu-freshwater.liquid`

### Freshwater Footer

The Freshwater footer provides a simple placeholder system that can replace the default Dawn footer with custom HTML.

**Files:**
- `sections/0-footer.liquid` - Custom footer section that conditionally renders Freshwater footer placeholder or default footer
- `snippets/0-footer-freshwater.liquid` - Custom Freshwater footer content renderer (HTML placeholder by default)
- `layout/theme.liquid` - Updated to render `{% section '0-footer' %}` instead of `{% sections 'footer-group' %}`

**How to Update:**

1. **Enable Freshwater Footer:**
   - Go to **Theme Editor** → **Footer** section (or **0-footer** section)
   - Check the **Enable Freshwater Footer** checkbox
   - The default footer will be replaced with the Freshwater footer placeholder

2. **Customize Footer Content:**
   - Edit `snippets/0-footer-freshwater.liquid` to add your custom HTML footer content
   - The placeholder file contains a simple structure that you can replace with your own HTML
   - The footer container uses the `.footer` class and supports separate desktop (`color_scheme--md`) and mobile (`color_scheme--sm`) color scheme settings
   - The footer section has `id="freshSection--{{ section.id }}"` for CSS targeting

3. **How It Works:**
   - `0-footer.liquid` checks if `section.settings.fresh_footer_enable` is `true`
   - If enabled, it renders the footer container and calls `{% render '0-footer-freshwater', section: section %}`
   - `0-footer-freshwater.liquid` renders your custom HTML content
   - If disabled (default), it renders the standard Dawn footer with all blocks and settings
   - The footer section maintains all original Dawn footer settings (newsletter, social, payment, etc.) even when Freshwater footer is enabled

4. **Styling:**
   - Customize footer styling in `assets/0-client.css.liquid`
   - The footer container uses the `.footer` class and supports separate desktop (`color_scheme--md`) and mobile (`color_scheme--sm`) color scheme settings
   - The footer section has `id="freshSection--{{ section.id }}"` for block-specific CSS targeting
   - Use `#freshSection--{{ section.id }}` selectors for section-specific styling

**Note:** 
- The Freshwater footer is disabled by default
- When enabled, it completely replaces the default footer content with a simple HTML placeholder
- All footer section settings (color scheme, padding, margin) still apply to the Freshwater footer
- The footer uses the same color scheme pattern as other custom sections (`color-{{ section.settings.color_scheme--md }}--md color-{{ section.settings.color_scheme--sm }}--sm`)
- To customize the footer, edit `snippets/0-footer-freshwater.liquid` directly with your HTML

---

## ⚠️ Important Notes

### Upgrading Dawn

When upgrading to a new Dawn version:

1. **DO NOT** overwrite your entire theme
2. **DO** compare Dawn files with your modified files:
   - `layout/theme.liquid`
   - `sections/header.liquid`
   - `config/settings_schema.json`
3. **DO** merge any new Dawn features into your modified files
4. **DO** keep all your `0-` prefixed files (they won't conflict)
5. **DO** update locale files (`locales/*.schema.json`) if you've added custom schema translations:
   - Compare your custom locale entries with the new Dawn locale files
   - Merge any new Dawn translations while preserving your custom entries
   - Example: If you added `options__4` with "Freshwater" to `en.default.schema.json`, ensure it's added to all other locale files as well
6. **DO** test thoroughly after upgrading

### Naming Convention

- All custom files use `0-` prefix
- All custom functions use `fresh` prefix (e.g., `freshGlobalInit`)
- All custom variables use `fresh` prefix (e.g., `freshWindowResizeTimeout`)
- Carousel initialization: `freshInitCarouselsTiny()` (uses Tiny-Slider, vanilla JS)

### Console Logging

- **Custom files (`0-*`):** Use `window.Freshwater.console()`
- **Dawn files:** Use standard `console.log/error/warn`

This separation allows you to control Freshwater logs independently while keeping Dawn's native logging intact.

---

## 📝 Version History

### v3.1.0 - Based on Dawn 15.4.0

**2025-12-03 - Documentation and Code Improvements:**
- **Documentation improvements:**
  - Removed all line number references from README (replaced with descriptive text that won't drift over time)
  - Added "Quick Debug Recipe" section for easier debugging workflow
  - Updated all version references from v3.0.0 to v3.1.0
  - Added "CSS Architecture" section documenting Dawn button override strategy
- **Code improvements:**
  - Refactored `Freshwater.checkCarousels()` to use `window.Freshwater.console()` instead of raw `console.log/warn`
  - Now respects DEBUG_MODE flag (consistent with Freshwater logging guidelines)
  - Added fallback logger for defensive programming
- **CSS improvements:**
  - **Dawn Button Overrides:** All Dawn button styles now use Freshwater's standard scheme 1 styling
    - Colors applied via Dawn's color scheme classes (`.color-scheme-1`, `.color-scheme-2`, etc.)
    - Structural styles (border-radius, padding, font-size) applied within breakpoint media queries
    - Ensures consistent button appearance throughout the theme
    - Override in `assets/0-client.css.liquid` for customizations
- **Settings schema changes:**
  - **Updated `page_width` range:** Changed from `min: 1000, max: 1600, step: 100, default: 1200` to `min: 640, max: 3840, step: 640, default: 1920`
  - New range uses 640px increments: 640, 1280, 1920, 2560, 3200, 3840
  - **Migration required:** Dawn's default value (1200px) is not in the new range, so existing themes must be migrated using incremental steps (see conversion guide)

**2025-12-03 - Multi-Column Section Enhancements:**
- **Added "Make images square" feature:**
  - New toggle (`fresh_images_square--md` and `fresh_images_square--sm`) in Multi Column section settings
  - Located below "Make slides same height" for both desktop and mobile
  - When enabled, crops all images to a square (1:1) aspect ratio using `object-fit: cover`
  - Images are centered within the square container
  - Default: disabled (off)
  - Uses CSS padding-bottom technique for responsive square containers
- **Added "Overlay" feature for slide media:**
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

### v3.0.0 - Based on Dawn 15.4.0

**Initial Release:**
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

**2025-12-02 - Header and Footer Simplification:**
- **Removed Mobile Nav custom blocks from header**
  - All custom Mobile Nav blocks (Header, Body, Button, HTML, Liquid, Graphic, Video, Accordion, Rating, List) removed from `sections/0-header.liquid`
  - Mobile drawer now uses default Dawn navigation (or Freshwater footer if enabled)
  - Only `mega_menu_nav_item` block remains for desktop mega menu
  - Updated `snippets/0-header-drawer.liquid` to conditionally render default Dawn navigation or Freshwater footer
- **Added Mobile Drawer Footer Toggle**
  - Added toggle (`fresh_mobile_drawer_footer_enable`) in header settings under "Mobile logo position"
  - When enabled, replaces mobile drawer menu content with Freshwater footer placeholder
  - Created `snippets/0-header-drawer-freshwater.liquid` for custom mobile drawer footer HTML
  - Customize by editing `snippets/0-header-drawer-freshwater.liquid` directly with HTML
- **Simplified Freshwater Footer**
  - Removed all custom footer blocks from `sections/0-footer.liquid`
  - Footer now uses simple HTML placeholder (`snippets/0-footer-freshwater.liquid`)
  - Toggle (`fresh_footer_enable`) switches between default Dawn footer and Freshwater placeholder
  - Customize footer by editing `snippets/0-footer-freshwater.liquid` directly with HTML
  - All original Dawn footer settings (newsletter, social, payment, etc.) are preserved

---

## 🔒 GitHub Repository Management

### Branch Protection Rules

This repository uses GitHub Rulesets to protect all branches from unauthorized commits. The ruleset configuration ensures that:

- **Only authorized users/apps can push to branches:**
  - Cursor App (`cursor`)
  - Organization admins
  
- **Shopify bot commits are blocked:**
  - The Shopify theme editor's GitHub integration (`shopify[bot]`) is **not** allowed to commit directly to any branch
  - This prevents accidental overwrites from theme editor changes
  
- **Protected rules:**
  - Restrict updates: Only users with bypass permission can update matching refs
  - Restrict deletions: Only users with bypass permission can delete matching refs
  - Block force pushes: Force pushes are prevented

**Important:** All changes must be made locally via Git and pushed through Cursor or by organization admins. Changes made through the Shopify theme editor will **not** be automatically synced to GitHub.

**To make changes:**
1. Make edits locally in Cursor or your preferred editor
2. Commit changes via Git
3. Push to GitHub through Cursor or as an org admin
4. If you need to sync Shopify theme editor changes, manually copy them to your local files and commit

---

## 🤝 Contributing

When adding new features:

1. **Always** create `0-` prefixed files for customizations
2. **Never** modify Dawn files directly
3. **Always** use `fresh` prefix for custom JavaScript functions/variables
4. **Always** use `window.Freshwater.console()` for logging in custom files
5. **Always** document changes in this README

---

## 📄 License

This theme is based on Dawn, which is licensed under the MIT License. Custom Freshwater additions maintain the same license.

---

## 🆘 Support

For issues or questions:
1. Check this README first
2. Review the code comments in `0-` prefixed files
3. Use `?debug_mode=true` in URL to enable debug logging
4. Check browser console for errors

---

**Last Updated:** 2025-12-03  
**Dawn Base Version:** 15.4.0  
**Freshwater Version:** 3.1.0

{% endraw %}
