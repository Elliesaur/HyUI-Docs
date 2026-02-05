# HYUIML - CSS

HYUIML supports CSS-style styling through `<style>` blocks and inline `style` attributes. The CSS support is limited to properties that map to Hytale's UI styling system.

## Basic Usage

Include a `<style>` block at the beginning of your HYUIML:

```html
<style>
    .header {
        color: #ff0000;
        font-weight: bold;
        font-size: 24;
    }

    #my-button {
        flex-weight: 1;
        anchor-height: 40;
    }

    .container {
        background-color: rgba(0, 0, 0, 0.8);
        padding: 10;
    }
</style>

<p class="header">Title</p>
<button id="my-button">Click Me</button>
<div class="container">Content</div>
```

## Supported CSS Properties

### Text Styling

| Property                              | Values                                     | Description              |
| ------------------------------------- | ------------------------------------------ | ------------------------ |
| `color`                               | Hex colors (e.g., `#FFFFFF`)               | Text color               |
| `font-size`                           | Numeric value                              | Font size (no units)     |
| `font-weight`                         | `bold`, `normal`, or numeric (600+ = bold) | Font weight              |
| `font-style`                          | `italic`, `normal`                         | Font style               |
| `text-transform`                      | `uppercase`, `none`                        | Text transformation      |
| `letter-spacing`                      | Numeric value                              | Space between characters |
| `white-space`                         | `nowrap`, `wrap`, `normal`                 | Text wrapping behavior   |
| `font-family`, `font-name`            | Font name string                           | Custom font family       |
| `outline-color`, `text-outline-color` | Hex color                                  | Text outline color       |

### Alignment

| Property           | Values                          | Description               |
| ------------------ | ------------------------------- | ------------------------- |
| `text-align`       | `left`, `right`, `center`, etc. | Horizontal text alignment |
| `vertical-align`   | `top`, `bottom`, `center`       | Vertical alignment        |
| `horizontal-align` | `left`, `right`, `center`       | Horizontal alignment      |
| `align`            | `center`, etc.                  | Combined alignment        |

### Layout

| Property                | Values                                                    | Description                     |
| ----------------------- | --------------------------------------------------------- | ------------------------------- |
| `layout-mode`, `layout` | See LayoutMode values                                     | Layout direction for containers |
| `flex-direction`        | `row`, `row-reverse`, `column`, `column-reverse`          | Maps to LayoutMode              |
| `justify-content`       | `flex-start`, `flex-end`, `center`, `space-between`, etc. | Maps to horizontal alignment    |
| `align-items`           | `flex-start`, `flex-end`, `center`, `stretch`, etc.       | Maps to vertical alignment      |
| `flex-weight`           | Numeric value                                             | Weight for layout distribution  |

**Note**: From v0.5.0, `flex-weight` applies to the wrapping group for non-Group/Label elements.

### Visibility

| Property     | Values            | Description               |
| ------------ | ----------------- | ------------------------- |
| `visibility` | `hidden`, `shown` | Element visibility        |
| `display`    | `none`, `block`   | Alternative to visibility |

### Anchors (Positioning)

| Property            | Values        | Description               |
| ------------------- | ------------- | ------------------------- |
| `anchor-left`       | Numeric value | Left anchor position      |
| `anchor-right`      | Numeric value | Right anchor position     |
| `anchor-top`        | Numeric value | Top anchor position       |
| `anchor-bottom`     | Numeric value | Bottom anchor position    |
| `anchor-width`      | Numeric value | Fixed width               |
| `anchor-height`     | Numeric value | Fixed height              |
| `anchor-full`       | Numeric value | All sides (padding-like)  |
| `anchor-horizontal` | Numeric value | Left and right            |
| `anchor-vertical`   | Numeric value | Top and bottom            |
| `anchor-min-width`  | Numeric value | Minimum width             |
| `anchor-max-width`  | Numeric value | Maximum width             |
| `margin-*`          | Numeric value | Alternative to `anchor-*` |

### Padding

| Property         | Values            | Description                                            |
| ---------------- | ----------------- | ------------------------------------------------------ |
| `padding-left`   | Numeric value     | Left padding                                           |
| `padding-right`  | Numeric value     | Right padding                                          |
| `padding-top`    | Numeric value     | Top padding                                            |
| `padding-bottom` | Numeric value     | Bottom padding                                         |
| `padding`        | One or two values | Shorthand: `10` (all) or `10 20` (vertical horizontal) |

### Backgrounds

| Property           | Values                          | Description           |
| ------------------ | ------------------------------- | --------------------- |
| `background-image` | `url('path.png')` or `path.png` | Background image path |
| `background-color` | Hex color, `rgb()`, `rgba()`    | Background color      |

**Background with borders**:

```css
.box {
    /* border all sides */
    background-image: url('texture.png') 4;

    /* horizontal and vertical borders */
    background-image: url('texture.png') 4 6;

    /* color with borders */
    background-color: rgba(255, 0, 0, 0.5) 4;
}
```

### Style References

| Property                          | Values                                       | Description                    |
| --------------------------------- | -------------------------------------------- | ------------------------------ |
| `hyui-style-reference`            | `"Document.ui" "StyleName"` or `"StyleName"` | Reference to a Hytale UI style |
| `hyui-entry-label-style`          | Style reference                              | Dropdown entry label style     |
| `hyui-selected-entry-label-style` | Style reference                              | Selected dropdown entry style  |
| `hyui-popup-style`                | Style reference                              | Dropdown popup style           |
| `hyui-number-field-style`         | Style reference                              | Number field style             |
| `hyui-checked-style`              | Style reference                              | Checkbox checked style         |
| `hyui-unchecked-style`            | Style reference                              | Checkbox unchecked style       |

**Note**: When using `hyui-style-reference`, other styling properties in the same block may be ignored.

## LayoutMode Values

When using `layout-mode` or `text-align` on `<div>` elements:

* `top`, `bottom`, `left`, `right`, `center`
* `topscrolling`, `bottomscrolling`
* `middlecenter`, `centermiddle`
* `leftcenterwrap`, `rightcenterwrap`
* `full`, `middle`

## CSS Units

**Important**: HYUIML strips CSS units from numeric values because unit conversion is not supported.

```css
/* ❌ Avoid */
.element {
    font-size: 16px;
    padding: 10rem;
    anchor-width: 100%;
}

/* ✅ Prefer */
.element {
    font-size: 16;
    padding: 10;
    anchor-width: 100;
}
```

All numeric values are treated as raw numbers in Hytale's coordinate system.

## Style Definitions (@-rules)

You can define reusable style definitions with `@Name` syntax for use with custom buttons:

```html
<style>
    @PrimaryLabel {
        color: #ffffff;
        font-weight: bold;
        font-size: 18;
    }

    @PrimaryBackground {
        background-color: #0c0c0c;
    }

    @HoverBackground {
        background-color: #1a1a1a;
    }
</style>

<button class="custom-textbutton"
        data-hyui-default-label-style="@PrimaryLabel"
        data-hyui-default-bg="@PrimaryBackground"
        data-hyui-hovered-bg="@HoverBackground">
    Custom Button
</button>
```

**Supported in @-rules for labels**:

* `color`, `font-size`, `font-weight`, `font-style`, `text-transform`, `letter-spacing`
* `white-space`, `font-family`, `font-name`
* `outline-color`, `text-outline-color`
* `vertical-align`, `horizontal-align`, `text-align`, `align`

**Supported in @-rules for backgrounds**:

* `background-image`
* `background-color`

## Pseudo-Classes

### :hover

Apply styles on hover:

```css
.button:hover {
    background-color: #1a1a1a;
}
```

The `:hover` pseudo-class is converted to `data-hyui-hover-style` attribute.

## Inline Styles

Use the `style` attribute for element-specific styles:

```html
<div style="anchor-width: 200; anchor-height: 100; background-color: #333;">
    Content
</div>
```

Inline styles override CSS rules from `<style>` blocks.

## Custom Style Properties

Use `data-hyui-style` for arbitrary style keys not exposed via CSS:

```html
<div class="item-grid" data-hyui-style="SlotSpacing: 6; SlotSize: 64"></div>
```

These map directly to `HyUIStyle.set(key, value)` and are applied alongside CSS-derived styles.

## Color Formats

### Hex Colors

```css
color: #FFFFFF;           /* White */
color: #ff0000;           /* Red */
color: #00ff00(0.5);      /* Green with 50% opacity */
```

### RGB/RGBA

Automatically converted to hex:

```css
background-color: rgb(255, 0, 0);           /* Red */
background-color: rgba(255, 0, 0, 0.5);     /* Red with 50% opacity */
```

## CSS Comments

Both styles are supported:

```css
/* Block comment */
.element {
    color: #fff; // Line comment
}
```

## Default Styles

Add the `default-style` class or `data-hyui-default-style` attribute to apply Hytale's default styling:

```html
<label class="default-style">Styled Label</label>
<input type="text" class="default-style" />
```

Elements with default styles:

* Labels / paragraphs - Default label styling
* Input fields - Default input field styling
* Sliders - Default slider appearance
* Checkboxes - Default checkbox styling
* Color pickers - Default color picker styling

Default styles can be overridden with additional CSS properties.

## Example: Complete Styled UI

```html
<style>
    .page-container {
        background-color: rgba(0, 0, 0, 0.9);
        padding: 20;
    }

    .title {
        color: #ffcc00;
        font-size: 24;
        font-weight: bold;
        text-align: center;
    }

    .button-primary {
        background-color: #0066cc;
        color: #ffffff;
        anchor-height: 40;
        anchor-width: 200;
    }

    .button-primary:hover {
        background-color: #0080ff;
    }

    .form-field {
        flex-weight: 1;
        anchor-height: 32;
    }
</style>

<div class="page-overlay">
    <div class="page-container">
        <p class="title">Welcome</p>

        <input type="text" class="form-field default-style" placeholder="Username" />
        <input type="text" class="form-field default-style" placeholder="Password" />

        <button class="button-primary">Login</button>
    </div>
</div>
```
