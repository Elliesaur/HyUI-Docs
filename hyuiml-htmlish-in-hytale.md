# HYUIML - HTMLish in Hytale

HYUIML is an HTML-like markup language for defining Hytale UIs using a familiar syntax. It is parsed by HyUI and converted into fluent builder API calls under the hood.

## Overview

HYUIML provides a declarative way to build UIs that feels familiar to web developers, while mapping directly to Hytale's UI system. It supports:

- HTML-like tags for common UI elements
- CSS-style styling (with limitations)
- A "flexbox-ish" layout system that approximates modern web layouts
- Custom attributes for Hytale-specific features

**Important**: HYUIML is not a full HTML/CSS engine. It's a lightweight syntax that maps to Hytale's UI primitives with some default styling and layout conveniences.

## Quick Start

```java
String html = """
    <div class="page-overlay">
        <div class="container" data-hyui-title="Hello">
            <div class="container-contents">
                <p>Hello from HYUIML!</p>
            </div>
        </div>
    </div>
    """;
PageBuilder.pageForPlayer(playerRef)
    .fromHtml(html)
    .open(store);
```

## Documentation Structure

- **[CSS Styling](hyuiml-css.md)** - CSS properties, units, and styling system
- **[Elements Reference](hyuiml-elements.md)** - Complete element and attribute reference
- **[Limitations & Gotchas](hyuiml-limitations.md)** - Important constraints and differences from HTML/CSS

## Core Concepts

### Layout System: "Flexbox-ish"

HYUIML uses Hytale's `LayoutMode` system, which provides a simplified flexbox-like behavior. Unlike true CSS flexbox:

- Elements are laid out using `LayoutMode` (Top, Bottom, Left, Right, Center, etc.)
- `flex-weight` controls how elements grow to fill space
- CSS flexbox properties (`flex-direction`, `justify-content`, `align-items`) are mapped to Hytale's system where possible
- Not all flexbox features are supported (no flex-wrap, flex-basis, etc.)

**From v0.5.0**: `flex-weight` applies to the wrapping group for non-Group/Label elements, which affects layout behavior.

### Default Styles

Some elements have default styles applied when using the `default-style` class or `data-hyui-default-style` attribute:

- `<label>` / `<p>` - Default label styling from Hytale
- `<input>` fields - Default input field styling
- Sliders - Default slider appearance
- Checkboxes - Default checkbox styling
- Color pickers - Default color picker styling

These defaults can be overridden with CSS or style attributes.

### Event Handling

Elements require IDs to attach event listeners:

```java
builder.addEventListener("my-button", CustomUIEventBindingType.Activating, (ignored, ctx) -> {
    playerRef.sendMessage(Message.raw("Button clicked!"));
});
```

**Note**: IDs are sanitized internally (e.g., `my-button` becomes `HYUUIDmybutton0`). Always use your original ID when referencing elements.

## Quick Reference

For complete details, see the linked documentation pages:

### Common Elements

- `<div>` - Layout container (GroupBuilder)
- `<div class="page-overlay">` - Full-screen overlay
- `<div class="container">` - Framed container with title
- `<div class="panel">` - Panel container
- `<p>` / `<label>` - Text labels
- `<button>` - Buttons (various styles via classes)
- `<input>` - Form inputs (text, number, range, checkbox, color)
- `<textarea>` - Multiline text input
- `<img>` - Static images
- `<select>` - Dropdown menus
- `<progress>` - Progress bars

### Specialized Elements

- `<hyvatar>` - Dynamic Hyvatar avatar rendering
- `<timer>` - Time display labels
- `<sprite>` - Animated sprites
- `<nav class="tabs">` - Tab navigation
- `<block-selector>` - Block selection UI
- `<color-picker-dropdown-box>` - Color picker with dropdown
- `<hotkey-label>` - Input binding display
- `<menu-item>` - Menu item component
- `<labeled-checkbox>` - Checkbox with integrated label
- `<reorderable-list-grip>` - Drag handle for reorderable lists
- `<scene-blur>` - Scene blur effect

### Item-Related Elements

- `<span class="item-icon">` - Item icon display
- `<span class="item-slot">` - Full item slot
- `<div class="item-grid">` - Item grid container
- `<div class="item-grid-slot">` - Item grid slot entry

## Image Assets

All image paths (in `src` for `<img>` or `url()` for `background-image`) are relative to your mod's `Common/UI/Custom` folder.

**Important**: Hytale requires image assets to have a name ending in `@2x.png` for high-resolution support. For example, if you use `<img src="lizard.png"/>`, you must have a file named `lizard@2x.png` located in `src/main/resources/Common/UI/Custom/lizard@2x.png`.

### Dynamic Images

Use `class="dynamic-image"` on `<img>` to download a PNG at runtime:

```html
<img class="dynamic-image" src="https://hyvatar.io/render/Elyra" />
```

File path support (0.9.0+): you can load dynamic images from the `mods` directory using a relative path:

```html
<img class="dynamic-image" src="ModGroup_ModName/avatars/head/Elyra.png" />
```

This resolves to `mods/ModGroup_ModName/avatars/head/Elyra.png`.

**Limits**: 25 dynamic images per page/player. Downloaded PNGs are cached for 15 seconds.

### Hyvatar Images

Use `<hyvatar>` to render Hyvatar avatars as dynamic images:

```html
<hyvatar username="Elyra" render="full" size="256" rotate="45"></hyvatar>
```

Attributes: `username`, `render` (head/full/cape), `size` (64-2048), `rotate` (0-360), `cape` (override).

Thanks to Hyvatar.io for their fantastic work on the rendering service.

## Common Patterns

### Containers with Titles

```html
<div class="page-overlay">
    <div class="container" data-hyui-title="My Settings">
        <div class="container-title">
            <button id="help-btn">?</button>
        </div>
        <div class="container-contents">
            <p>Settings content goes here...</p>
        </div>
    </div>
</div>
```

- `.page-overlay` - Full-screen overlay root
- `.container` / `.decorated-container` - Framed containers
- `.container-title` - Elements in title area
- `.container-contents` - Elements in content area

### Tabs

```html
<nav class="tabs" data-tabs="tab1:Tab 1,tab2:Tab 2" data-selected="tab1"></nav>

<div class="tab-content" data-hyui-tab-id="tab1">
    <p>Tab 1 content</p>
</div>

<div class="tab-content" data-hyui-tab-id="tab2">
    <p>Tab 2 content</p>
</div>
```

### Custom Buttons

```html
<style>
    @MyButtonLabel {
        color: #ffffff;
        font-weight: bold;
    }
    @MyButtonBg {
        background-color: #0c0c0c;
    }
</style>

<button class="custom-textbutton"
        data-hyui-default-label-style="@MyButtonLabel"
        data-hyui-default-bg="@MyButtonBg">
    Custom Button
</button>
```

### Item Grids

```html
<div class="item-grid" data-hyui-slots-per-row="5">
    <div class="item-grid-slot" data-hyui-name="Slot 1"></div>
    <div class="item-grid-slot" data-hyui-name="Slot 2"></div>
</div>
```

## Comments

HTML comments `<!-- comment -->` are supported. In CSS, both `/* */` and `//` are supported.
