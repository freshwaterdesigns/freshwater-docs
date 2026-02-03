---
title: "Freshwater v3 Theme Docs"
layout: default
---

{% raw %}
# Freshwater v3.2.0

**Based on Dawn 15.4.0**

Freshwater is a custom Shopify theme built on top of Dawn 15.4.0. This theme maintains compatibility with Dawn's core functionality while adding custom features, sections, and styling.

## 📑 Table of Contents

- [Philosophy](#-philosophy)
- [Key Differences from Dawn](#-key-differences-from-dawn-1540)
- [Conversion Guide](#-conversion-guide-dawn-1540--freshwater-v320)
- [File Structure](#-file-structure-overview)
- [Key Features](#-key-features)
- [CSS Architecture](#-css-architecture)
- [Customizing Navigation](#-customizing-navigation)
  - [Mobile Drawer Navigation](#mobile-drawer-navigation)
  - [Desktop Mega Menu Navigation](#desktop-mega-menu-navigation)
  - [Desktop Freshwater Menu Navigation](#desktop-freshwater-menu-navigation)
  - [Freshwater Footer](#freshwater-footer)
  - [Freshwater Modal System](#freshwater-modal-system)
  - [Inline Anchor Links (Smooth Scrolling)](#inline-anchor-links-smooth-scrolling)
- [Important Notes](#️-important-notes)
- [Version History](#-version-history)
- [GitHub Repository Management](#-github-repository-management)
- [Contributing](#-contributing)
- [Support](#-support)

---

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
   - Changed `{% section '0-footer' %}` to `{% sections 'footer-group' %}` (enables footer section group support)

2. **`sections/header.liquid`**
   - Changed `render 'header-mega-menu'` to `render '0-header-mega-menu'`

3. **`config/settings_schema.json`**
   - Added extensive custom settings for:
     - Button styling (3 button styles)
     - Color scheme extensions (header, subheader, icons colors)
     - Typography settings
     - Custom section/block settings
   - Updated theme version to show "15.4.0, Freshwater 3.2.0"

### Custom Files (0- Prefixed)

#### Assets
- `0-freshwater.js.liquid` - Core Freshwater JavaScript with debug utility (vanilla JS, no jQuery dependency)
- `0-freshwater.css.liquid` - Core Freshwater CSS
- `0-client.js.liquid` - Client-specific JavaScript (editable)
- `0-client.css.liquid` - Client-specific CSS (editable)
- `0-component-cart-drawer.css` - Cart drawer styling (customized copy of Dawn)
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
- `0-cart-drawer.liquid` - Cart drawer markup (customized copy of Dawn)
- `0-block-*` snippets - Custom block types for sections:
  - `0-block-accordion-1.liquid`
  - `0-block-body-1.liquid`
  - `0-block-button-1.liquid`
- `0-block-featured-collection-1.liquid`
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

## 🚀 Conversion Guide: Dawn 15.4.0 → Freshwater v3.2.0

Follow these steps to convert a fresh Dawn 15.4.0 installation to Freshwater v3.4.0:

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
- Footer section uses `{% sections 'footer-group' %}` (enables footer section group support)
- Added custom body classes

**Important:** After copying the file, verify that `{% sections 'footer-group' %}` is used (not `{% section '0-footer' %}`).

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
   "theme_version": "15.4.0, Freshwater 3.2.0"
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
   - Should use `{% sections 'footer-group' %}` (not `{% section '0-footer' %}`)

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

**Wiring-only Dawn edits (allowed exception):**
- If you must minimally edit a Dawn file **only** to wire in a new `0-` Freshwater file (e.g., swap a render/include to a `0-` version), add that file to **Modified Dawn Files** above and keep the change documented in this upgrade step list.

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

### Overlay Positioning {#overlay-positioning-classes}

The `.fresh-overlay` class is used in Multi-Column section slides to position content over media (images or videos). The `.fresh-overlay` wrapper div is automatically added by the theme and spans the full media area, providing a positioning context for your content.

**Positioning with Inline CSS:**

Use inline CSS styles directly in the "Overlay" Liquid field to position your content:

**Bottom-Left Positioning:**

```liquid
<div style="position: absolute; bottom: 0; left: 0;">
  <p>Your overlay content here</p>
</div>
```

**Bottom-Right Positioning:**

```liquid
<div style="position: absolute; bottom: 0; right: 0;">
  <p>Your overlay content here</p>
</div>
```

**Center Positioning:**

```liquid
<div style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);">
  <p>Your overlay content here</p>
</div>
```

**Top-Left Positioning:**

```liquid
<div style="position: absolute; top: 0; left: 0;">
  <p>Your overlay content here</p>
</div>
```

**Note:** The `.fresh-overlay` wrapper div is automatically added by the theme and spans the full media area. Your content inside must use `position: absolute` with appropriate `top`, `bottom`, `left`, `right`, and/or `transform` values to position it within the overlay area.

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
- `layout/theme.liquid` - Updated to render `{% sections 'footer-group' %}` (enables footer section group support)

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

### Freshwater Modal System

The Freshwater Modal System allows you to create modals that can be triggered by buttons. The modal content can be created using HTML or Liquid blocks, and JavaScript automatically wraps the content in the proper modal structure.

**Features:**
- **Simple Setup**: Just add a class to your button and a class to your content block
- **Auto-Wrap**: JavaScript automatically wraps content in modal structure (no manual HTML needed)
- **Automatic Close**: Built-in close button (X), overlay click, and ESC key support
- **Accessible**: Focus management and keyboard navigation
- **Responsive**: Mobile-optimized styling
- **Customizable**: Full control over modal content using Liquid/HTML

**How It Works:**

1. Create a modal content block (HTML or Liquid block) and add the `fresh-modal-content` class
2. Set a unique ID for the block (either custom or auto-generated)
3. Configure a button with the `fresh-modal-button` class and set its URL to match the modal ID (e.g., `#my-section`)
4. JavaScript automatically wraps the content in the modal structure when the page loads

**Step-by-Step Setup:**

### Method 1: Auto-Wrap (Recommended - Simplest)

This is the easiest way to create modals. Just add the `fresh-modal-content` class to your HTML or Liquid block.

1. **Create the Modal Content Block**

Add an **HTML** or **Liquid** block to your section:

**For HTML Block:**
- Go to your section → Add block → **HTML**
- In the HTML field, add your content:
  ```html
  <h2>Modal Title</h2>
  <p>Your modal content goes here...</p>
  ```
- In **Custom Class Names**, add: `fresh-modal-content`
- In **Unique ID** (optional), set a custom ID like: `my-section`
  - If you don't set a custom ID, one will be auto-generated (e.g., `freshHTML--123456789`)

**For Liquid Block:**
- Go to your section → Add block → **Liquid**
- In the Liquid field, add your content:
  ```liquid
  <h2>{{ product.title }}</h2>
  <p>{{ product.description }}</p>
  ```
- In **Custom Class Names**, add: `fresh-modal-content`
- In **Unique ID** (optional), set a custom ID like: `product-info`
  - If you don't set a custom ID, one will be auto-generated (e.g., `freshliquid--123456789`)

**Important Notes:**
- The `fresh-modal-content` class tells JavaScript to automatically wrap your content in the modal structure
- JavaScript will automatically add:
  - Modal wrapper (`<div class="fresh-modal">`)
  - Overlay backdrop (`<div class="fresh-modal__overlay">`)
  - Content container (`<div class="fresh-modal__content">`)
  - Close button (`<button class="fresh-modal__close">`)
- The block's ID (custom or auto-generated) becomes the modal ID
- You only need to write your actual content - no modal HTML structure required!

2. **Create the Trigger Button**

Add a **Button** block to your section:

- **Button Text**: "Open Modal" (or whatever you want)
- **Button URL**: Set to `#my-section` (matching your modal's ID)
  - If using auto-generated ID: `#freshHTML--123456789` (check the block's ID in the rendered HTML)
  - If using custom ID: `#my-section` (your custom ID)
- **Custom Class Names**: Add `fresh-modal-button`

3. **That's It!**

JavaScript automatically handles the rest:
- Wraps your content in the modal structure on page load
- Opens the modal when the button is clicked
- Handles closing (X button, overlay click, ESC key)

### Method 2: Manual Structure (Advanced)

If you need full control over the modal structure, you can manually create it:

1. **Create the Modal Content**

Add an **HTML** or **Liquid** block with the full modal structure:

```liquid
<div id="my-section" class="fresh-modal">
    <div class="fresh-modal__overlay"></div>
    <div class="fresh-modal__content">
        <button class="fresh-modal__close" aria-label="Close modal"></button>
        
        <!-- Your custom content here -->
        <h2>Modal Title</h2>
        <p>Your modal content goes here...</p>
    </div>
</div>
```

**Important Notes:**
- The `id` attribute must be unique (e.g., `my-section`)
- The outer `div` must have the `fresh-modal` class (not `fresh-modal-content`)
- The `fresh-modal__overlay` div creates the backdrop
- The `fresh-modal__content` div contains your actual content
- The `fresh-modal__close` button is automatically styled as an X button

2. **Create the Trigger Button**

Same as Method 1:
- **Button URL**: `#my-section`
- **Custom Class Names**: `fresh-modal-button`

**When to Use Method 2:**
- You need custom modal structure
- You want to add additional elements outside the content wrapper
- You're migrating from an older implementation

**ID Handling:**

The modal system intelligently handles IDs from HTML/Liquid blocks:

1. **Custom ID** (Recommended): Set in block settings → **Unique ID**
   - Example: `my-section`
   - Use in button URL: `#my-section`

2. **Auto-Generated ID**: If no custom ID is set, blocks generate one automatically
   - HTML blocks: `freshHTML--123456789` (where numbers are the block ID)
   - Liquid blocks: `freshliquid--123456789`
   - Use in button URL: `#freshHTML--123456789`

3. **ID from First Class**: If no ID attribute exists, JavaScript checks the first class name
   - If it's a custom name (not auto-generated pattern), it uses that as the ID
   - This allows using class names as IDs for convenience

**Closing the Modal:**

The modal can be closed in three ways:

1. **Close Button**: Click the X button in the top-right corner
2. **Overlay Click**: Click outside the modal content (on the dark overlay)
3. **ESC Key**: Press the Escape key on your keyboard

**Customization:**

### Styling the Modal Content

You can customize the modal content using standard CSS. The modal content container uses:
- `background-color: rgb(var(--color-background))` - Uses theme background color
- `color: rgba(var(--color-foreground), 1)` - Uses theme foreground color
- `max-width: 90%` - Responsive width
- `max-height: 90vh` - Responsive height with scrolling

### Custom CSS Overrides

You can override modal styles in `0-client.css.liquid`:

```css
/* Customize modal content */
.fresh-modal__content {
    max-width: 600px; /* Override default 90% */
    padding: 3rem; /* Override default padding */
}

/* Customize close button */
.fresh-modal__close {
    top: 1.5rem;
    right: 1.5rem;
    width: 3rem;
    height: 3rem;
}

/* Customize overlay */
.fresh-modal__overlay {
    background-color: rgba(0, 0, 0, 0.85); /* Darker overlay */
}
```

**Advanced Usage:**

### Multiple Modals

You can create multiple modals on the same page. Each modal needs:
- A unique ID
- A button with `fresh-modal-button` class pointing to that ID

**Example:**
```liquid
<!-- Modal 1 - Auto-wrap method -->
<div class="fresh-modal-content" id="contact-modal">
    <h2>Contact Us</h2>
    <p>Contact form here...</p>
</div>

<!-- Modal 2 - Auto-wrap method -->
<div class="fresh-modal-content" id="info-modal">
    <h2>More Info</h2>
    <p>Info content here...</p>
</div>
```

### Using Liquid in Modal Content

Since the modal content is a Liquid block, you can use any Liquid code:

**Auto-wrap method:**
```liquid
<!-- In Liquid block with fresh-modal-content class -->
<h2>{{ product.title }}</h2>
<p>{{ product.description }}</p>
<p>Price: {{ product.price | money }}</p>
```

**Manual method:**
```liquid
<div id="product-info" class="fresh-modal">
    <div class="fresh-modal__overlay"></div>
    <div class="fresh-modal__content">
        <button class="fresh-modal__close" aria-label="Close modal"></button>
        
        <h2>{{ product.title }}</h2>
        <p>{{ product.description }}</p>
        <p>Price: {{ product.price | money }}</p>
    </div>
</div>
```

### Dynamic Modal IDs

You can use Liquid to generate dynamic IDs:

**Auto-wrap method:**
- Set **Unique ID** in block settings to: `modal-{{ section.id }}`
- Reference in button URL: `#modal-{{ section.id }}`

**Manual method:**
```liquid
<div id="modal-{{ section.id }}" class="fresh-modal">
    <!-- ... -->
</div>
```

**Technical Details:**

### CSS Classes

- `.fresh-modal` - Modal wrapper (hidden by default, added by auto-wrap)
- `.fresh-modal-content` - Content class that triggers auto-wrap (add this to your block)
- `.fresh-modal--active` - Active state (added when modal is open)
- `.fresh-modal__overlay` - Backdrop/overlay (added by auto-wrap)
- `.fresh-modal__content` - Content container (added by auto-wrap)
- `.fresh-modal__close` - Close button (added by auto-wrap)
- `.fresh-modal-button` - Button trigger class
- `body.fresh-modal-open` - Added to body when modal is open (prevents scrolling)

### JavaScript Functions

- `freshHandleModal(button)` - Handles modal opening from button click
- `freshOpenModal(modalElement)` - Opens a modal
- `freshCloseModal(modalElement)` - Closes a modal
- `freshInitModalHandlers()` - Initializes event handlers for closing
- `freshInitModalAutoWrap()` - Automatically wraps `.fresh-modal-content` elements in modal structure

### Event Handlers

The system automatically handles:
- Auto-wrapping elements with `.fresh-modal-content` class on page load
- Click events on `.fresh-modal-button` buttons
- Click events on `.fresh-modal__close` buttons
- Click events on `.fresh-modal__overlay`
- ESC key press events

**Troubleshooting:**

### Modal Doesn't Open

1. **Check the ID**: Ensure the button URL (e.g., `#my-section`) matches the modal's ID exactly
   - For auto-generated IDs, check the rendered HTML to see the actual ID
   - IDs are case-sensitive
2. **Check the Class**: Ensure the button has the `fresh-modal-button` class
3. **Check the Modal Class**: 
   - For auto-wrap: Ensure the block has `fresh-modal-content` class
   - For manual: Ensure the modal wrapper has the `fresh-modal` class
4. **Check Console**: Open browser console to see any error messages (add `?debug_mode=true` to URL)

### Modal Doesn't Close

1. **Check Structure**: 
   - For auto-wrap: Ensure JavaScript ran (check console for "Auto-wrapped" message)
   - For manual: Ensure the modal has the correct structure with `fresh-modal__overlay` and `fresh-modal__close`
2. **Check JavaScript**: Ensure `0-freshwater.js.liquid` is loaded
3. **Check Console**: Open browser console for errors

### Modal Content Not Visible

1. **Check Z-Index**: Ensure no other elements have a higher z-index than the modal (9999)
2. **Check CSS**: Ensure the modal has `display: flex` when active
3. **Check Content**: Ensure content is inside `fresh-modal__content` (auto-wrap handles this)

### Auto-Wrap Not Working

1. **Check Class**: Ensure you added `fresh-modal-content` (not `fresh-modal`)
2. **Check Timing**: Auto-wrap runs on page load - ensure your block is in the DOM
3. **Check Console**: Look for "Auto-wrapped" log message (requires `?debug_mode=true`)
4. **Check ID**: Ensure the block has an ID (custom or auto-generated)

**Browser Support:**

- Modern browsers (Chrome, Firefox, Safari, Edge)
- Mobile browsers (iOS Safari, Chrome Mobile)
- Requires JavaScript enabled

**Accessibility:**

- Close button has `aria-label="Close modal"`
- Focus is managed (focus moves to close button when modal opens)
- ESC key support for keyboard navigation
- Overlay click support for mouse users

**Examples:**

### Simple Text Modal (Auto-Wrap Method)

**HTML Block:**
- **HTML Content**: 
  ```html
  <h2>Welcome!</h2>
  <p>Thank you for visiting our site.</p>
  ```
- **Custom Class Names**: `fresh-modal-content`
- **Unique ID**: `welcome-modal`

**Button Block:**
- **Button Text**: "Open Welcome Modal"
- **Button URL**: `#welcome-modal`
- **Custom Class Names**: `fresh-modal-button`

### Form Modal (Auto-Wrap Method)

**Liquid Block:**
- **Liquid Content**: 
  ```liquid
  <h2>Contact Us</h2>
  <form>
      <input type="text" placeholder="Name">
      <input type="email" placeholder="Email">
      <textarea placeholder="Message"></textarea>
      <button type="submit">Send</button>
  </form>
  ```
- **Custom Class Names**: `fresh-modal-content`
- **Unique ID**: `contact-form-modal`

**Button Block:**
- **Button Text**: "Contact Us"
- **Button URL**: `#contact-form-modal`
- **Custom Class Names**: `fresh-modal-button`

### Product Info Modal (Auto-Wrap with Liquid)

**Liquid Block:**
- **Liquid Content**: 
  ```liquid
  <h2>{{ product.title }}</h2>
  <p>{{ product.description }}</p>
  <p>Price: {{ product.price | money }}</p>
  ```
- **Custom Class Names**: `fresh-modal-content`
- **Unique ID**: `product-info`

**Button Block:**
- **Button Text**: "View Product Details"
- **Button URL**: `#product-info`
- **Custom Class Names**: `fresh-modal-button`

**Files Modified:**

- `assets/0-freshwater.css.liquid` - Added modal CSS styles
- `assets/0-freshwater.js.liquid` - Added modal JavaScript functionality and auto-wrap feature
- `snippets/0-block-html-1.liquid` - Fixed ID attribute handling
- `snippets/0-block-liquid-1.liquid` - Fixed ID attribute handling

**Version:**

This documentation applies to Freshwater Modal System v1.1.0

### Inline Anchor Links (Smooth Scrolling)

The Freshwater theme includes automatic smooth scrolling for inline anchor links. This allows you to create buttons that smoothly scroll to specific sections on the same page.

**Features:**
- **Automatic Smooth Scrolling**: Works with any anchor link (URL starting with `#`)
- **Accessible**: Respects user's reduced motion preferences
- **Simple Setup**: Just set a Custom ID on your section and link to it from a button

**How It Works:**

1. Set a Custom ID on the target section (in Global Settings)
2. Set the button URL to `#your-section-id`
3. When clicked, the button smoothly scrolls to that section

**Step-by-Step Setup:**

1. **Set the Target Section ID**

   Go to the section you want to scroll to:
   - Scroll to the bottom of the section settings
   - Find **GLOBAL SETTINGS**
   - In **Custom ID**, enter a unique ID (e.g., `my-section`, `contact-form`, `product-details`)
     - Use lowercase letters, numbers, and hyphens
     - No spaces or special characters
     - Must be unique on the page

2. **Configure the Button**

   Go to your button block settings:
   - **Button URL**: Set to `#my-section` (matching the section's Custom ID, with `#` prefix)
   - The button can be in any section on the same page

3. **That's It!**

   When the button is clicked:
   - The page smoothly scrolls to the target section
   - Respects accessibility preferences (instant scroll for reduced motion)
   - Works automatically - no additional configuration needed

**Important Notes:**

- The Custom ID must match exactly (case-sensitive)
- The button URL must start with `#` (e.g., `#my-section`)
- Both the button and target section must be on the same page
- The target section must have a Custom ID set in Global Settings
- Smooth scrolling works with any link that has `href="#something"`, not just buttons

**Examples:**

**Example 1: Scroll to Contact Section**

**Target Section (Contact Form):**
- Go to **Contact Form** section → **GLOBAL SETTINGS**
- **Custom ID**: `contact-form`

**Button Block:**
- **Button Text**: "Contact Us"
- **Button URL**: `#contact-form`

**Example 2: Scroll to Product Details**

**Target Section (Product Details):**
- Go to **Product Details** section → **GLOBAL SETTINGS**
- **Custom ID**: `product-info`

**Button Block:**
- **Button Text**: "Learn More"
- **Button URL**: `#product-info`

**Example 3: Scroll to Hero Section**

**Target Section (Hero):**
- Go to **Hero** section → **GLOBAL SETTINGS**
- **Custom ID**: `hero-section`

**Button Block:**
- **Button Text**: "Back to Top"
- **Button URL**: `#hero-section`

**Technical Details:**

### How Smooth Scrolling Works

The theme automatically detects anchor links (`href="#id"`) and:
1. Finds the target element by ID using `document.getElementById()`
2. Prevents the default browser jump behavior
3. Smoothly animates the scroll to the target
4. Respects `prefers-reduced-motion` for accessibility (instant scroll if enabled)

### JavaScript Function

- `freshSmoothScrollToInlineAnchors()` - Automatically initializes smooth scrolling for all anchor links on page load

### CSS Classes

No special CSS classes needed - smooth scrolling works automatically with any anchor link.

**Troubleshooting:**

### Button Doesn't Scroll

1. **Check the ID**: Ensure the button URL (e.g., `#my-section`) matches the section's Custom ID exactly
   - IDs are case-sensitive
   - Must include the `#` in the URL
2. **Check the Section ID**: Ensure the target section has a Custom ID set in Global Settings
3. **Check the Page**: Ensure both button and target section are on the same page
4. **Check Console**: Open browser console to see any error messages (add `?debug_mode=true` to URL)

### Scroll is Jumpy (Not Smooth)

1. **Check Reduced Motion**: User may have reduced motion enabled (this is intentional for accessibility)
2. **Check JavaScript**: Ensure `0-freshwater.js.liquid` is loaded
3. **Check Console**: Open browser console for errors

**Browser Support:**

- Modern browsers (Chrome, Firefox, Safari, Edge)
- Mobile browsers (iOS Safari, Chrome Mobile)
- Requires JavaScript enabled

**Accessibility:**

- Automatically respects `prefers-reduced-motion` media query
- Instant scroll (no animation) for users who prefer reduced motion
- Smooth scroll (500ms animation) for users without motion preferences
- WCAG 2.2 compliant

**Files Modified:**

- `assets/0-freshwater.js.liquid` - Added smooth scrolling functionality

**Documentation:**

For more information on inline anchors and smooth scrolling, see the [Freshwater Inline Anchors documentation](https://freshwaterdesigns.github.io/freshwater-docs/freshwater-v3/#inline-anchor-links-smooth-scrolling).

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

For a detailed changelog of all changes, see [CHANGELOG.md](CHANGELOG.md) or view it on the [documentation site](https://freshwaterdesigns.github.io/freshwater-docs/freshwater-v3/changelog).

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
5. **Always** document changes in [CHANGELOG.md](CHANGELOG.md)

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

**Dawn Base Version:** 15.4.0  
**Freshwater Version:** 3.2.0

{% endraw %}
