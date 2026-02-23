# HYUIML - Limitations

While HYUIML looks like HTML and CSS, it is **not a full browser engine**. It's a lightweight syntax that maps to Hytale's UI system with some conveniences. Understanding these limitations will help you avoid frustration and build better UIs.

## Core Limitations

### 1. Not True HTML/CSS

HYUIML is a declarative syntax that **looks like** HTML/CSS but maps to Hytale's native UI builders. Think of it as "HTML-inspired" rather than "HTML-compatible."

**What this means**:

* No DOM manipulation
* No CSS cascade (specificity rules are simplified)
* No browser APIs (querySelector, etc.)
* CSS properties only work if they map to Hytale's styling system

### 2. No JavaScript / Scripting

`<script>` tags are completely ignored. All UI logic must be handled in Java via event listeners.

```html
<!-- ❌ This does nothing -->
<script>
    console.log("Hello");
</script>

<!-- ✅ Handle events in Java -->
<button id="my-button">Click Me</button>
```

```java
// Java code
builder.addEventListener("my-button", CustomUIEventBindingType.Activating, (ignored, ctx) -> {
    // Your logic here
});
```

### 3. ID Sanitization

Hytale only permits alphanumeric IDs. HyUI automatically sanitizes your IDs.

**Important**: Always use your **original** ID when referencing elements in Java, not the sanitized version.

```html
<button id="my-button">Click</button>
<button id="submit_form">Submit</button>
```

```java
// ✅ Use original IDs
builder.addEventListener("my-button", ...);
builder.addEventListener("submit_form", ...);
builder.getById("my-button");

// ❌ Don't try to use sanitized IDs
builder.getById("HYUUIDmybutton0"); // Wrong!
```

## CSS Limitations

### 1. Limited Property Support

Only CSS properties that map to Hytale's styling system are supported. See [HYUIML CSS](hyuiml-css.md) for the complete list.

**Not supported**:

* `position` (absolute, relative, fixed, sticky)
* `z-index`
* `transform`
* `animation` / `transition`
* `box-shadow`
* `border` (use `background-image` with border values instead)
* Most box model properties (use `anchor-*` and `padding-*` instead)

### 2. No CSS Units

CSS units are **stripped** from values because unit conversion is not supported.

```css
/* ❌ Units are ignored */
.element {
    font-size: 16px;
    padding: 1rem;
    width: 50%;
}

/* ✅ Use raw numbers */
.element {
    font-size: 16;
    padding: 10;
    anchor-width: 100;
}
```

All values are interpreted as pixels in Hytale's coordinate system.

### 3. Limited Cascade and Specificity

CSS specificity works differently than in browsers:

* Style precedence: Inline `style` > CSS rules > Default styles
* Later rules override earlier rules for the same selector
* No complex specificity calculations (ID > class > element)

```css
/* Both target the same element */
.button {
    color: red;
}

#my-button {
    color: blue;
}
```

In a browser, `#my-button` would win. In HYUIML, **the last rule wins** (blue in this case).

### 4. No CSS Pseudo-Selectors (Except :hover)

Only `:hover` is supported for hover states.

```css
/* ✅ Supported */
.button:hover {
    background-color: #1a1a1a;
}

/* ❌ Not supported */
.button:active { }
.button:focus { }
.button::before { }
.list-item:nth-child(2) { }
```

### 5. Background Borders Are Not Standard CSS

`background-image` and `background-color` support border values, but this is a HyUI extension, not standard CSS:

```css
/* HyUI extension */
.box {
    background-image: url('texture.png') 4 6; /* horizontal, vertical borders */
    background-color: rgba(0, 0, 0, 0.8) 4;  /* border on all sides */
}
```

This maps to Hytale's 9-patch / patch-style system.

## Layout Limitations

### 1. "Flexbox-ish" ≠ Flexbox

HYUIML's layout system approximates flexbox but is actually Hytale's `LayoutMode` system.

**Supported flexbox-like properties**:

* `flex-direction` (maps to LayoutMode)
* `justify-content` (maps to horizontal alignment)
* `align-items` (maps to vertical alignment)
* `flex-weight` (for distribution)

**Not supported**:

* `flex-wrap`
* `flex-grow`, `flex-shrink`, `flex-basis`
* `order`
* `align-self`
* `align-content`
* `gap` (use padding/margins instead)

**From v0.5.0**: `flex-weight` applies to the wrapping group for non-Group/Label elements, changing layout behavior from earlier versions.

### 2. No Grid Layout

CSS Grid (`display: grid`) is not supported. Use nested `<div>` elements with `LayoutMode` or item grids.

### 3. No Absolute/Fixed Positioning

There's no `position: absolute` or `position: fixed`. Elements are positioned by:

* Their parent's `LayoutMode`
* `anchor-*` properties (which work more like constraints than positioning)
* `flex-weight` for space distribution

## Element Limitations

### 1. Nesting Restrictions

While most elements can nest, some Hytale UI macros have unexpected behavior when deeply nested:

```html
<!-- ⚠️ May behave unexpectedly -->
<div>
    <div>
        <div>
            <button class="action-button">Action</button>
        </div>
    </div>
</div>

<!-- ✅ Better -->
<div>
    <button class="action-button">Action</button>
</div>
```

Specialized buttons (`action-button`, `toggle-button`, etc.) work best with minimal nesting.

### 2. Container Child Structure

Containers (`.container`, `.decorated-container`) expect specific child structure:

```html
<!-- ✅ Correct structure -->
<div class="container">
    <div class="container-title">
        <!-- Title elements -->
    </div>
    <div class="container-contents">
        <!-- Content elements -->
    </div>
</div>

<!-- ⚠️ Works but less controlled -->
<div class="container">
    <!-- Direct children go to content area by default -->
    <p>This goes to content</p>
</div>
```

### 3. Raw Buttons Need Specific Content

`raw-button` class is for buttons with custom child content:

```html
<!-- ✅ Correct -->
<button class="raw-button">
    <span class="item-icon" data-hyui-item-id="minecraft:diamond"></span>
</button>

<!-- ❌ Don't use raw-button with text -->
<button class="raw-button">Click Me</button> <!-- Use normal button instead -->
```

### 4. Event Listeners Require IDs

Only elements with `id` attributes can have event listeners attached:

```html
<!-- ✅ Can add event listener -->
<button id="submit">Submit</button>

<!-- ❌ Cannot add event listener -->
<button>Submit</button>
```

### 5. UI File Parsing Is Experimental (0.9.0+)

Use `fromUIFile(...)` to parse `.ui` files into HyUI elements so you can call `getById(...)` and attach events. This feature is **experimental**, and may change as it matures.

If you use `fromFile(...)`, the UI is loaded as raw assets (no parsing), so element access is limited to low-level commands like `.editElement(...)`.

## Image Limitations

### 1. Dynamic Image Limits

Dynamic images (including Hyvatar) are limited:

* **25 dynamic images** per page, per player
* Downloaded PNGs cached for **15 seconds**
* No support for animated images (GIF, APNG, etc.)

```html
<!-- ⚠️ Only 25 of these allowed per page -->
<img class="dynamic-image" src="https://example.com/image.png" />
```

File path support (0.9.0+), relative to `mods`:

```html
<img class="dynamic-image" src="ModGroup_ModName/avatars/head/Elyra.png" />
```

This resolves to `mods/ModGroup_ModName/avatars/head/Elyra.png`.

### 2. Image Paths Are Relative

All `src` paths are relative to `Common/UI/Custom`:

```html
<!-- ❌ Absolute paths don't work -->
<img src="/Common/UI/Custom/logo.png" />

<!-- ✅ Use relative paths -->
<img src="logo.png" />
<img src="subfolder/icon.png" />
```

## Style Reference Limitations

### 1. Style References Override Other Properties

When using `hyui-style-reference`, other CSS properties in the same block may be ignored:

```css
.my-element {
    color: #ff0000;                                    /* May be ignored */
    font-size: 20;                                     /* May be ignored */
    hyui-style-reference: "Common.ui" "DefaultLabel";  /* This takes precedence */
}
```

Apply overrides after the reference or use `data-hyui-style` for specific properties.

### 2. Default Styles Override Partially

Elements with `default-style` class apply Hytale's default styling, but CSS can override:

```html
<!-- Applies default styles, then overrides color -->
<label class="default-style" style="color: #ff0000;">Red Label</label>
```

However, not all properties may override correctly depending on the element type.

## Form Input Limitations

### 1. Input Values Need Tracking

Form inputs don't automatically track their values. You must handle value changes:

```html
<input type="text" id="username" value="initial" />
```

```java
builder.addEventListener("username", CustomUIEventBindingType.TextValueChanged, (event, ctx) -> {
    String newValue = event.getValue(); // Track value changes
    // Store or process value
});
```

### 2. No Native Form Submission

There's no `<form>` element or native form submission. Implement your own submission logic:

```html
<input type="text" id="username" />
<input type="password" id="password" />
<button id="submit">Login</button>
```

```java
builder.addEventListener("submit", CustomUIEventBindingType.Activating, (ignored, ctx) -> {
    // Manually collect and process form values
});
```

## Tab Limitations

### 1. Tab Content Must Use Specific IDs

Tab content blocks must use `data-hyui-tab-id` that matches the tab ID:

```html
<!-- Tab IDs must match -->
<nav class="tabs" data-tabs="home:Home,about:About" data-selected="home"></nav>

<!-- ✅ Correct - IDs match -->
<div class="tab-content" data-hyui-tab-id="home">Home content</div>
<div class="tab-content" data-hyui-tab-id="about">About content</div>

<!-- ❌ Wrong - ID mismatch -->
<div class="tab-content" data-hyui-tab-id="homepage">Won't show</div>
```

### 2. Multiple Tab Navs Need Explicit Links

When using multiple tab navigations on one page, use `data-hyui-tab-nav`:

```html
<nav id="nav1" class="tabs" data-tabs="a:A,b:B" data-selected="a"></nav>
<nav id="nav2" class="tabs" data-tabs="x:X,y:Y" data-selected="x"></nav>

<!-- Link content to specific nav -->
<div class="tab-content" data-hyui-tab-id="a" data-hyui-tab-nav="nav1">...</div>
<div class="tab-content" data-hyui-tab-id="x" data-hyui-tab-nav="nav2">...</div>
```

## Performance Considerations

### 1. Page Updates Can Be Expensive

Calling `updatePage()` or rebuilding UIs frequently can impact performance. Cache built UIs when possible and only update when necessary.

### 2. Deep Nesting Affects Performance

Deeply nested element structures increase build time and memory usage. Keep hierarchies as flat as practical.

### 3. Many Dynamic Images Hurt Performance

Each dynamic image is a network request and texture upload. Minimize usage and cache where possible.

## Common Mistakes

### 1. Using HTML/CSS You Know

```css
/* ❌ Common mistakes from web development */
.box {
    display: flex;           /* Use layout-mode instead */
    border: 1px solid black; /* Use background-image with borders */
    margin: 10px;            /* Use anchor-* properties */
    opacity: 0.5;            /* Use rgba() in colors instead */
}

/* ✅ HYUIML equivalents */
.box {
    layout-mode: Left;
    background-color: rgba(0, 0, 0, 0.8) 1;
    anchor-left: 10;
    anchor-right: 10;
}
```

### 2. Expecting Browser Behavior

```java
// ❌ No DOM APIs
Element button = builder.querySelector("#my-button");

// ✅ Use HyUI APIs
builder.addEventListener("my-button", ...);
builder.getById("my-button");
```

### 3. Forgetting ID Sanitization

```java
// ❌ Using sanitized ID
builder.addEventListener("HYUUIDmybutton0", ...);

// ✅ Using original ID
builder.addEventListener("my-button", ...);
```

### 4. Overusing Custom Buttons

Custom buttons (`custom-textbutton`, `custom-button`) require state style definitions. For simple styled buttons, use regular buttons with CSS:

```html
<!-- ✅ Simple case - use regular button -->
<button style="background-color: #333;">Click Me</button>

<!-- ✅ Complex case - use custom button -->
<button class="custom-textbutton"
        data-hyui-default-bg="@DefaultBg"
        data-hyui-hovered-bg="@HoverBg"
        data-hyui-pressed-bg="@PressedBg">
    Click Me
</button>
```

## Tips for Success

1. **Start simple** - Use standard elements before reaching for custom components
2. **Test frequently** - HYUIML doesn't have browser DevTools, so test changes often
3. **Read the element reference** - Many elements have specific attribute requirements
4. **Use default styles** - Add `class="default-style"` to get consistent Hytale styling
5. **Keep hierarchies flat** - Avoid deep nesting when possible
6. **Cache values** - Track form input values in your own data structures
7. **Use IDs strategically** - Only add IDs where you need event listeners or queries
8. **Check the CSS docs** - Don't assume CSS properties work—verify they're supported

## When to Use Raw Builders Instead

Consider using HyUI's Java builders directly when:

* You need complex dynamic behavior
* You're building reusable components
* Performance is critical
* You need fine-grained control

HYUIML is best for:

* Static or semi-static layouts
* Rapid prototyping
* Declarative UIs with simple logic
* When HTML/CSS familiarity speeds development
