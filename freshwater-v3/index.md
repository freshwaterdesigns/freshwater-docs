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
- `0-theme-submenu.liquid` - Custom submenu renderer
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
- `0-theme-submenu.liquid`
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

### Step 5: Modify `layout/theme.liquid`

Replace the entire `layout/theme.liquid` file with Freshwater's version:

```bash
cp freshwater-v3/layout/theme.liquid your-dawn-theme/layout/theme.liquid
```

**Key changes:**
- Head section uses `0-theme-dawn-1`, `0-theme-dawn-2`, and `0-theme-freshwater-1`
- Body section uses `0-theme-dawn-3`, `0-theme-dawn-4`, and `0-theme-freshwater-2`
- Added custom body classes

### Step 6: Modify `sections/header.liquid`

Update line 188 in `sections/header.liquid`:

**Find:**
```liquid
render 'header-mega-menu'
```

**Replace with:**
```liquid
render '0-header-mega-menu'
```

### Step 7: Update `config/settings_schema.json`

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

### Step 8: Verify File References

Check that all references point to `0-` prefixed files:

1. **In `layout/theme.liquid`:**
   - Should reference `0-theme-dawn-*` and `0-theme-freshwater-*` snippets

2. **In `sections/header.liquid`:**
   - Should reference `0-header-mega-menu`

3. **In `snippets/0-header-mega-menu.liquid`:**
   - Should reference `0-theme-submenu`

4. **In `snippets/0-theme-freshwater-1.liquid`:**
   - Should reference `0-jquery.js`, `0-slick.*`, `0-bootstrap.*`, `0-freshwater.css`, `0-client.css`

5. **In `snippets/0-theme-freshwater-2.liquid`:**
   - Should reference `0-lazyload.min.js`, `0-freshwater.js`, `0-client.js`

### Step 9: Test the Installation

1. Upload the theme to your Shopify store
2. Test key functionality:
   - Header mega menu
   - Custom sections (hero, marquee, multi-column)
   - JavaScript functionality (carousels, lazy loading)
   - Console logs (add `?debug_mode=true` to URL)
3. Check browser console for errors
4. Verify all custom sections appear in theme editor

### Step 10: Update Theme Settings

After installation, configure:
1. **Color Schemes:** Set custom header, subheader, and icon colors
2. **Button Styles:** Configure the 3 button style options
3. **Typography:** Set custom font families and weights
4. **Menu:** Ensure mega menu is selected in header settings

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
│   ├── 0-*                   # Freshwater custom sections
│   ├── header.liquid         # Modified (references 0-header-mega-menu)
│   └── [Dawn sections]       # Unmodified Dawn sections
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
   - Click **Add block** → Select **Mobile Nav Item** or **Mobile Nav 2 Items**
   - Configure each block:
     - **Mobile Nav Item:** Single item with image (optional) and text
     - **Mobile Nav 2 Items:** Two items side-by-side, each with image (optional) and text
   - Set URL, image, text, and overlay text options for each item
   - Reorder blocks by dragging

2. **Block Types Available:**
   - `mobile_nav_item` - Single navigation item
   - `mobile_nav_item_2` - Two navigation items side-by-side

3. **Fallback Behavior:**
   - If no blocks are added (`section.blocks.size == 0`), the drawer falls back to the default Dawn menu navigation
   - The custom navigation is rendered in the `fresh-mobile-drawer` container
   - Default navigation uses the menu set in **Header** → **Menu** setting

4. **Styling Overrides:**
   - Base styles for `.fresh-mobile-drawer` and related classes are defined in `assets/0-freshwater.css.liquid` (lines 452-511)
   - To customize the mobile drawer appearance, override these styles in `assets/0-client.css.liquid`
   - Available classes to override:
     - `.fresh-mobile-drawer` - Container padding and text alignment
     - `.fresh-mobile-drawer__items`, `.fresh-mobile-drawer__item` - List styling
     - `.fresh-mobile-drawer__item` - Item spacing and font reset
     - `.fresh-mobile-drawer__item--double` - Grid layout for two-item blocks
     - `.fresh-mobile-drawer__a` - Link styling (background, color, padding)
     - `.fresh-mobile-drawer__img` - Image sizing
     - `.fresh-mobile-drawer__item--overlay-text` - Overlay text container positioning
     - `.fresh-mobile-drawer__text` - Text styling
     - `.fresh-mobile-drawer__item--overlay-text .fresh-mobile-drawer__text` - Overlay text specific styling
   - Example override in `0-client.css.liquid`:
     ```css
     @media (max-width: 767px) {
         .fresh-mobile-drawer__a {
             background-color: #your-color;
             padding: 10px 15px;
         }
         .fresh-mobile-drawer__text {
             font-size: 1.6rem;
         }
     }
     ```

**Note:** The drawer is rendered by `sections/0-header.liquid` which calls `render '0-header-drawer'` on line 142.

### Desktop Mega Menu Navigation

The desktop mega menu uses a custom submenu system that requires manual code updates.

**Files:**
- `snippets/0-header-mega-menu.liquid` - Main mega menu structure
- `snippets/0-theme-submenu.liquid` - Custom submenu content (manually edited)

**How to Update:**

1. **Main Menu Structure:**
   - The main menu structure is controlled by `snippets/0-header-mega-menu.liquid`
   - This file renders the standard Dawn mega menu structure
   - On line 70, it calls `{% render '0-theme-submenu', link: link %}` to inject custom submenu content

2. **Custom Submenu Content:**
   - Edit `snippets/0-theme-submenu.liquid` to add custom submenu content
   - The file uses a `case` statement based on the menu link handle
   - Example structure:
     ```liquid
     {%- case link_handle -%}
         {%- when 'catalog' -%}
             <!-- Custom HTML for catalog submenu -->
         {%- when 'contact' -%}
             <!-- Custom HTML for contact submenu -->
     {%- endcase -%}
     ```

3. **Adding a New Submenu:**
   - Determine the menu link handle (usually the lowercase, hyphenated version of the menu item name)
   - Add a new `when` case in `0-theme-submenu.liquid`
   - Add your custom HTML structure within that case
   - Use Bootstrap classes (`container`, `row`, `column`, `col-*`) for layout
   - Use `mega-menu__link` class for links to match styling

4. **Example:**
   ```liquid
   {%- when 'products' -%}
       <div class="container">
           <div class="row">
               <div class="column col-4">
                   <a href="/collections/example" class="mega-menu__link">
                       <img src="https://example.com/image.jpg" alt="Example">
                       <br>Example Text
                   </a>
               </div>
           </div>
       </div>
   ```

**Note:** The mega menu is only displayed when **Header** → **Desktop menu type** is set to **Mega menu** in the theme settings.

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
5. **DO** test thoroughly after upgrading

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

**Last Updated:** 2025-01-27  
**Dawn Base Version:** 15.4.0  
**Freshwater Version:** 3.0.0
