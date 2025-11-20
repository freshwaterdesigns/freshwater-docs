---
title: "Freshwater v3 Theme Docs"
layout: default
---

{% raw %}
# Freshwater v3.0.0

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
   - Line 37: Changed `{% sections 'footer-group' %}` to `{% section '0-footer' %}`

2. **`sections/header.liquid`**
   - Line 188: Changed `render 'header-mega-menu'` to `render '0-header-mega-menu'`

3. **`config/settings_schema.json`**
   - Added extensive custom settings for:
     - Button styling (3 button styles)
     - Color scheme extensions (header, subheader, icons colors)
     - Typography settings
     - Custom section/block settings
   - Updated theme version to show "15.4.0, Freshwater 3.0.0"

### Custom Files (0- Prefixed)

#### Assets
- `0-freshwater.js.liquid` - Core Freshwater JavaScript with debug utility
- `0-freshwater.css.liquid` - Core Freshwater CSS
- `0-client.js.liquid` - Client-specific JavaScript (editable)
- `0-client.css.liquid` - Client-specific CSS (editable)
- `0-jquery.js` - jQuery library (noConflict)
- `0-slick.min.js` / `0-slick.css` / `0-slick-theme.css.liquid` - Slick carousel library
- `0-lazyload.min.js` - Vanilla Lazyload library
- `0-bootstrap.min-1.css` - Bootstrap CSS
- `0-magnify.js` - Image magnify library
- Slick font files (`0-slick.eot`, `0-slick.woff`, `0-slick.ttf`, `0-slick.svg`)
- `0-ajax-loader.gif` - Slick loader image

#### Snippets
- `0-theme-dawn-1.liquid` through `0-theme-dawn-4.liquid` - Split Dawn theme.liquid head/body sections
- `0-theme-freshwater-1.liquid` - Freshwater head includes (fonts, jQuery, Bootstrap, Slick, custom CSS)
- `0-theme-freshwater-2.liquid` - Freshwater body includes (Lazyload, custom JS)
- `0-header-mega-menu.liquid` - Custom mega menu with submenu support (adds `{% render '0-theme-submenu', link: link %}`)
- `0-header-freshwater-menu.liquid` - Custom Freshwater menu with empty dropdowns (adds `{% render '0-theme-submenu-freshwater', link: link %}`)
- `0-theme-submenu.liquid` - Custom submenu renderer for mega menu (displays `mega_menu_nav_item` blocks)
- `0-theme-submenu-freshwater.liquid` - Custom submenu renderer for Freshwater menu (hardcoded conditionals per menu handle)
- `0-header-drawer.liquid` - Mobile drawer navigation renderer
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
- `0-hero-1.liquid` / `0-hero-2.liquid` - Custom hero sections
- `0-main-product.liquid` - Custom product page section
- `0-marquee-1.liquid` - Custom marquee section
- `0-multi-column-1.liquid` - Custom multi-column section
- `0-one-column-1.liquid` - Custom one-column section
- `0-two-column-1.liquid` - Custom two-column section

---

## 🚀 Conversion Guide: Dawn 15.4.0 → Freshwater v3.0.0

Follow these steps to convert a fresh Dawn 15.4.0 installation to Freshwater v3.0.0:

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
- `0-jquery.js`
- `0-slick.min.js`
- `0-slick.css`
- `0-slick-theme.css.liquid`
- `0-lazyload.min.js`
- `0-bootstrap.min-1.css`
- `0-magnify.js`
- Slick font files and loader image

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
- Footer section uses `0-footer` instead of `footer-group` (line 37)
- Added custom body classes

### Step 6: Modify `layout/theme.liquid`

Update line 37 in `layout/theme.liquid`:

**Find:**
```liquid
{% sections 'footer-group' %}
```

**Replace with:**
```liquid
{% section '0-footer' %}
```

### Step 7: Modify `sections/header.liquid`

Update line 188 in `sections/header.liquid`:

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
   "theme_version": "15.4.0, Freshwater 3.0.0"
   ```

**Option B: Replace Entire File (Easier but loses any custom settings)**
```bash
cp freshwater-v3/config/settings_schema.json your-dawn-theme/config/settings_schema.json
```

**Important Freshwater Settings Added:**
- `0 Button` - Button styling settings (3 button styles)
- Color scheme extensions (header, subheader, icons)
- Typography settings
- Custom section/block settings

### Step 9: Verify File References

Check that all references point to `0-` prefixed files:

1. **In `layout/theme.liquid`:**
   - Should reference `0-theme-dawn-*` and `0-theme-freshwater-*` snippets
   - Should reference `0-footer` section (line 37)

2. **In `sections/header.liquid`:**
   - Should conditionally reference `0-header-mega-menu` or `0-header-freshwater-menu` based on menu type setting

3. **In `sections/0-footer.liquid`:**
   - Should reference `0-footer-freshwater` snippet when Freshwater footer is enabled

4. **In `snippets/0-header-mega-menu.liquid`:**
   - Should reference `0-theme-submenu`

5. **In `snippets/0-header-freshwater-menu.liquid`:**
   - Should reference `0-theme-submenu-freshwater`

6. **In `snippets/0-theme-freshwater-1.liquid`:**
   - Should reference `0-jquery.js`, `0-slick.*`, `0-bootstrap.*`, `0-freshwater.css`, `0-client.css`

7. **In `snippets/0-theme-freshwater-2.liquid`:**
   - Should reference `0-lazyload.min.js`, `0-freshwater.js`, `0-client.js`

### Step 10: Test the Installation

1. Upload the theme to your Shopify store
2. Test key functionality:
   - Header menus (mega menu, Freshwater menu, dropdown menu)
   - Mobile drawer navigation with custom blocks
   - Custom sections (hero, marquee, multi-column)
   - JavaScript functionality (carousels, lazy loading)
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
   - Configure mobile drawer blocks if using custom navigation
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

**Note:** Only `0-` prefixed files use this utility. Native Dawn files use standard `console.log/error`.

### JavaScript Libraries

- **jQuery** (`0-jquery.js`) - Loaded with noConflict as `$0`
- **Slick Carousel** (`0-slick.*`) - For carousel functionality
- **Vanilla Lazyload** (`0-lazyload.min.js`) - For lazy loading images
- **Bootstrap** (`0-bootstrap.min-1.css`) - CSS framework
- **Magnify** (`0-magnify.js`) - Image zoom functionality

### Custom Sections

All custom sections support:
- Custom block types (`0-block-*`)
- Responsive settings (mobile/desktop)
- Color scheme integration
- Custom CSS classes and IDs

---

## 🧭 Customizing Navigation

### Mobile Drawer Navigation

The mobile drawer navigation uses custom blocks that can be added through the Shopify theme editor.

**File:** `snippets/0-header-drawer.liquid`

**How to Update:**

1. **Via Theme Editor (Recommended):**
   - Go to **Theme Editor** → **Header** section
   - Scroll to the **Blocks** section
   - Click **Add block** → Select **Header**
   - Configure the header block with text, styling, and layout options
   - Reorder blocks by dragging

2. **Block Types Available:**
   All blocks are prefixed with "Mobile Nav - " in the theme editor for clarity:
   - **Mobile Nav - Header** - Header block for displaying titles/text
   - **Mobile Nav - Body** - Rich text content block
   - **Mobile Nav - Button** - Button with URL and styling options
   - **Mobile Nav - HTML** - Raw HTML content
   - **Mobile Nav - Liquid** - Liquid template code
   - **Mobile Nav - Graphic** - Image block with optional link
   - **Mobile Nav - Video** - Video block with optional link
   - **Mobile Nav - Accordion** - Expandable accordion content
   - **Mobile Nav - Rating** - Star/heart rating display
   - **Mobile Nav - List** - List with icons/images
   
   Each block type supports width options (100%, 50%, 33.3%) for masonry-style layouts.

3. **Fallback Behavior:**
   - If no blocks are added (`section.blocks.size == 0`), the drawer falls back to the default Dawn menu navigation
   - The custom navigation is rendered in the `fresh-mobile-drawer` container
   - Default navigation uses the menu set in **Header** → **Menu** setting

4. **Styling Overrides:**
   - Header blocks use the standard header block styling (see `snippets/0-block-header-1.liquid` for available settings)
   - Base styles for `.fresh-mobile-drawer` container are defined in `assets/0-freshwater.css.liquid` (lines 452-511)
   - To customize the mobile drawer appearance, override styles in `assets/0-client.css.liquid`
   - The `.fresh-mobile-drawer` container wraps all blocks in the mobile drawer

**Note:** The drawer is rendered by `sections/0-header.liquid` which calls `render '0-header-drawer'` on line 142.

### Desktop Mega Menu Navigation

The desktop mega menu uses a block-based system that can be configured through the Shopify theme editor.

**Files:**
- `sections/0-header.liquid` - Header section that renders the mega menu (line 188: `render '0-header-mega-menu'`)
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
   - On line 70, it calls `{% render '0-theme-submenu', link: link %}` to inject custom submenu content
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
- `sections/0-header.liquid` - Header section that conditionally renders the Freshwater menu (line 188: `render '0-header-freshwater-menu'`)
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
   - On line 30, it calls `{% render '0-theme-submenu-freshwater', link: link %}` to inject custom content
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

The Freshwater footer provides a customizable footer system that can completely replace the default Dawn footer.

**Files:**
- `sections/0-footer.liquid` - Custom footer section that conditionally renders Freshwater footer or default footer
- `snippets/0-footer-freshwater.liquid` - Custom Freshwater footer content renderer (placeholder by default)
- `layout/theme.liquid` - Updated to render `0-footer` section instead of `footer-group` (line 37)

**How to Update:**

1. **Enable Freshwater Footer:**
   - Go to **Theme Editor** → **Footer** section (or **0-footer** section)
   - Check the **Enable Freshwater Footer** checkbox
   - The default footer will be replaced with the Freshwater footer

2. **Add Blocks to Footer:**
   - In the **Theme Editor** → **Footer** section (or **0-footer** section), click **Add block**
   - Available block types:
     - **Footer - Header** - Custom header text with styling options
     - **Footer - Body** - Rich text content
     - **Footer - Button** - Call-to-action buttons
     - **Footer - HTML** - Custom HTML code
     - **Footer - Liquid** - Custom Liquid code
     - **Footer - Graphic** - Images with optional links
     - **Footer - Video** - Video content with optional links
     - **Footer - Accordion** - Collapsible content sections
     - **Footer - Rating** - Star/heart ratings
     - **Footer - List** - Custom lists with icons
   - Each block supports width options (100%, 75%, 50%, 33.3%, 25%) for desktop masonry layout
   - On mobile, all blocks automatically stack at 100% width

3. **Customize Footer Content:**
   - Blocks are rendered via `snippets/0-footer-freshwater.liquid`
   - Each block type uses its corresponding `0-block-*` snippet (e.g., `0-block-header-1`, `0-block-body-1`)
   - Blocks are wrapped in `.fresh-footer-blocks__item` containers with width classes
   - All blocks maintain their desktop and mobile schema settings

4. **How It Works:**
   - `0-footer.liquid` checks if `section.settings.fresh_footer_enable` is `true`
   - If enabled, it renders the footer container and calls `{% render '0-footer-freshwater', section: section %}`
   - `0-footer-freshwater.liquid` iterates through `section.blocks` and renders each block based on its type
   - Blocks are wrapped in divs with width classes (e.g., `fresh-footer-blocks__item--width-50`)
   - If disabled (default), it renders the standard Dawn footer with all blocks and settings
   - The footer section maintains all original Dawn footer settings (newsletter, social, payment, etc.) even when Freshwater footer is enabled

5. **Styling:**
   - Desktop masonry layout: Blocks flow naturally based on their width settings (100%, 75%, 50%, 33.3%, 25%)
   - Mobile layout: All blocks stack vertically at 100% width
   - CSS is located in `assets/0-freshwater.css.liquid` (lines 689-730)
   - Customize footer styling in `assets/0-client.css.liquid`
   - The footer container uses the `.footer` class and supports separate desktop (`color_scheme--md`) and mobile (`color_scheme--sm`) color scheme settings
   - The footer section has `id="freshSection--{{ section.id }}"` for block-specific CSS targeting
   - Block-specific CSS should use `#freshSection--{{ section.id }}` selectors (same pattern as other custom sections)

6. **Block Schema Details:**
   - All blocks support desktop (`--md`) and mobile (`--sm`) settings for full customization
   - **Footer - Accordion** block includes full icon options (44+ icons) for both start and end icons
   - **Footer - Graphic** and **Footer - Video** blocks support optional link URLs
   - **Footer - Button** block supports full-width option for both desktop and mobile
   - Each block maintains all schema settings from their corresponding `0-one-column-1.liquid` block definitions

**Note:** 
- The Freshwater footer is disabled by default
- When enabled, it completely replaces the default footer content
- All footer section settings (color scheme, padding, margin) still apply to the Freshwater footer
- Blocks support both desktop (`--md`) and mobile (`--sm`) settings for full customization
- Width options are applied via flexbox masonry layout on desktop, with automatic stacking on mobile
- The footer uses the same color scheme pattern as other custom sections (`color-{{ section.settings.color_scheme--md }}--md color-{{ section.settings.color_scheme--sm }}--sm`)

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
- jQuery instance is `$0` (noConflict)

### Console Logging

- **Custom files (`0-*`):** Use `window.Freshwater.console()`
- **Dawn files:** Use standard `console.log/error/warn`

This separation allows you to control Freshwater logs independently while keeping Dawn's native logging intact.

---

## 📝 Version History

- **v3.0.0** - Based on Dawn 15.4.0
  - Added debug utility
  - Standardized console logging
  - Performance optimizations
  - Event delegation improvements
  - Comprehensive documentation

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

**Last Updated:** 2025-11-20  
**Dawn Base Version:** 15.4.0  
**Freshwater Version:** 3.0.0

{% endraw %}
