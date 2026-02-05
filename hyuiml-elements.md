# HYUIML - Elements

Complete reference of all HYUIML elements, their attributes, and builder mappings.

## Layout Elements

### `<div>`

Maps to `GroupBuilder` by default. Used for layout and containers.

**Special Classes**:

* `page-overlay` - `PageOverlayBuilder` - Full-screen overlay
* `panel` - `PanelBuilder` - Panel container
* `container` - `ContainerBuilder` - Framed container
* `decorated-container` - `ContainerBuilder.decoratedContainer()` - Styled framed container
* `tab-content` - `TabContentBuilder` - Tab content container
* `item-grid` - `ItemGridBuilder` - Item grid display

**Container-Specific Child Classes**:

* `container-title` - Elements placed in container title area
* `container-contents` - Elements placed in container content area

**Attributes**:

* `id` - Element identifier for events/queries
* `class` - CSS classes
* `style` - Inline CSS
* `data-hyui-title` - Container title text (for `container` class)
* `data-hyui-scrollbar-style` - Scrollbar style reference (e.g., `"Common.ui" "DefaultScrollbarStyle"`)
* `data-hyui-tooltiptext` - Tooltip text
* `data-hyui-flexweight` - Flex weight for layout
* `data-hyui-style` - Raw style properties
* `data-hyui-hover-style` - Hover state styles
* `data-hyui-tab-id` - Tab ID (for `tab-content` class)
* `data-hyui-tab-nav` - Tab navigation ID (for `tab-content` class)

**Item Grid Attributes** (for `item-grid` class):

* `data-hyui-background-mode` - Background mode
* `data-hyui-render-item-quality-background` - Show item quality backgrounds
* `data-hyui-are-items-draggable` - Enable item dragging
* `data-hyui-keep-scroll-position` - Maintain scroll position
* `data-hyui-show-scrollbar` - Display scrollbar
* `data-hyui-slots-per-row` - Number of slots per row

### `<div class="item-grid-slot">`

Maps to `ItemGridSlot`. Defines a slot entry within an item grid.

**Attributes**:

* `data-hyui-name` - Slot label/name
* `data-hyui-description` - Slot description
* `data-hyui-item-incompatible` - Mark as incompatible
* `data-hyui-item-uncraftable` - Mark as uncraftable
* `data-hyui-activatable` - Enable activation
* `data-hyui-skip-item-quality-background` - Skip quality background
* `data-hyui-slot-background` - Background patch style reference
* `data-hyui-slot-overlay` - Overlay patch style reference
* `data-hyui-slot-icon` - Icon patch style reference

## Text Elements

### `<p>`, `<label>`

Maps to `LabelBuilder`. Standard text display.

**Attributes**:

* `id`, `class`, `style` - Standard attributes
* All styling attributes (see CSS documentation)

**Example**:

```html
<p class="title">Hello World</p>
<label style="color: #ff0000;">Error Message</label>
```

### `<timer>`, `<span class="timer">`

Maps to `TimerLabelBuilder`. Displays formatted time values.

**Attributes**:

* `value` - Time in milliseconds
* `data-hyui-time-seconds` - Time in seconds
* `format` - Display format: `hms`, `ms`, `seconds`, `s`, `human`, `human-readable`, `milliseconds`, `ms-full`
* `prefix` - Text before the time
* `suffix` - Text after the time

**Example**:

```html
<timer value="60000" format="hms"></timer>
<timer data-hyui-time-seconds="120" format="human" prefix="Time: "></timer>
```

### `<hotkey-label>`, `<hyui-hotkey-label>`

Maps to `HotkeyLabelBuilder`. Displays input binding labels.

**Attributes**:

* `data-hyui-input-binding-key` or `input-binding-key` - The binding key
* `data-hyui-input-binding-key-prefix` or `input-binding-key-prefix` - Prefix text

**Example**:

```html
<hotkey-label input-binding-key="Jump" input-binding-key-prefix="Press "></hotkey-label>
```

## Button Elements

### `<button>`

Maps to `ButtonBuilder` variants based on classes and attributes.

**Button Types (via classes)**:

* `back-button` - `ButtonBuilder.backButton()`
* `secondary-button` - `ButtonBuilder.secondaryTextButton()`
* `small-secondary-button` - `ButtonBuilder.smallSecondaryTextButton()`
* `tertiary-button` - `ButtonBuilder.tertiaryTextButton()`
* `small-tertiary-button` - `ButtonBuilder.smallTertiaryTextButton()`
* `raw-button` - `ButtonBuilder.rawButton()` - For custom content
* `custom-textbutton` - `CustomButtonBuilder.customTextButton()`
* `custom-button` - `CustomButtonBuilder.customButton()`
* `action-button` - `ActionButtonBuilder.actionButton()`
* `toggle-button` - `ToggleButtonBuilder.toggleButton()`
* `item-slot-button` - `ItemSlotButtonBuilder.itemSlotButton()`

**Standard Attributes**:

* `id`, `class`, `style` - Standard attributes
* `disabled` or `data-hyui-disabled` - Disable button
* `data-hyui-overscroll` - Enable overscroll handling

**Custom Button Attributes** (`custom-textbutton`, `custom-button`):

* `data-hyui-default-label-style` - Default state label style (text buttons only)
* `data-hyui-hovered-label-style` - Hover state label style
* `data-hyui-pressed-label-style` - Pressed state label style
* `data-hyui-disabled-label-style` - Disabled state label style
* `data-hyui-default-bg` - Default state background
* `data-hyui-hovered-bg` - Hover state background
* `data-hyui-pressed-bg` - Pressed state background
* `data-hyui-disabled-bg` - Disabled state background

**Action Button Attributes** (`action-button`):

* `data-hyui-action-name` or `data-hyui-action` - Action name
* `data-hyui-key-binding-label` - Key binding label
* `data-hyui-action-button-alignment` - Alignment value

**Item Slot Button Attributes** (`item-slot-button`):

* `data-hyui-layout-mode` or `layout-mode` - Layout mode

**Examples**:

```html
<button id="submit">Submit</button>
<button class="back-button">Back</button>
<button class="secondary-button">Cancel</button>

<button class="custom-textbutton"
        data-hyui-default-label-style="@MyLabel"
        data-hyui-default-bg="@MyBg">
    Custom
</button>

<button class="action-button" data-hyui-action="Submit">Submit</button>
```

### `<input type="submit">`, `<input type="reset">`

Map to button builders. `reset` creates `ButtonBuilder.cancelTextButton()`.

## Form Input Elements

### `<input type="text">`

Maps to `TextFieldBuilder.textInput()`.

**Attributes**:

* `value` - Initial text value
* `placeholder` - Placeholder text
* `maxlength` - Maximum character limit
* `readonly` - Make read-only

**Example**:

```html
<input type="text" id="username" placeholder="Enter username" maxlength="20" />
```

### `<input type="number">`

Maps to `NumberFieldBuilder.numberInput()`.

**Attributes**:

* `value` - Initial numeric value
* `format` - Number format string
* `min`, `data-hyui-min` - Minimum value
* `max`, `data-hyui-max` - Maximum value
* `step`, `data-hyui-step` - Step increment
* `data-hyui-max-decimal-places` - Maximum decimal places

**Example**:

```html
<input type="number" value="10" min="0" max="100" step="5" />
```

### `<input type="range">`

Maps to slider builders. Type determined by classes:

* No class - `SliderBuilder.gameSlider()`
* `slider-number-field` - `SliderNumberFieldBuilder.sliderNumberField()`
* `float-slider` - `FloatSliderBuilder.floatSlider()`
* `float-slider-number-field` - `FloatSliderNumberFieldBuilder.floatSliderNumberField()`

**Attributes**:

* `value`, `data-hyui-value` - Initial value
* `min`, `data-hyui-min` - Minimum value
* `max`, `data-hyui-max` - Maximum value
* `step`, `data-hyui-step` - Step increment

**Slider Number Field Attributes**:

* `data-hyui-number-field-max-decimal-places` - Max decimal places
* `data-hyui-number-field-anchor-*` - Anchor properties for number field

**Example**:

```html
<input type="range" min="0" max="100" value="50" step="1" />
<input type="range" class="slider-number-field" min="0" max="100" value="50" />
```

### `<input type="checkbox">`

Maps to `CheckBoxBuilder`.

**Attributes**:

* `checked` - Initially checked
* `value` - Boolean value string

**Example**:

```html
<input type="checkbox" id="agree" checked />
```

### `<labeled-checkbox>`, `<hyui-labeled-checkbox>`

Maps to `LabeledCheckBoxBuilder`. Checkbox with integrated label.

**Example**:

```html
<labeled-checkbox id="option1">I agree to terms</labeled-checkbox>
```

### `<input type="color">`

Maps to `ColorPickerBuilder`.

**Attributes**:

* `value` - Initial color value

**Example**:

```html
<input type="color" value="#ff0000" />
```

### `<color-picker-dropdown-box>`, `<color-picker-dropdown>`, `<hyui-color-picker-dropdown-box>`

Maps to `ColorPickerDropdownBoxBuilder`. Color picker with dropdown interface.

**Attributes**:

* `data-hyui-format` or `format` - Color format: `rgba`, `rgb`
* `data-hyui-display-text-field` or `display-text-field` - Show text field (boolean)

**Example**:

```html
<color-picker-dropdown-box format="rgba" display-text-field="true"></color-picker-dropdown-box>
```

### `<textarea>`

Maps to `TextFieldBuilder.multilineTextField()`.

**Attributes**:

* `value` - Initial text value
* `rows` - Visible rows (maps to max visible lines)
* `data-hyui-max-visible-lines` - Max visible lines
* `data-hyui-auto-grow` - Enable auto-grow
* `data-hyui-scrollbar-style` - Scrollbar style reference
* `data-hyui-background` - Background reference
* `data-hyui-placeholder-style` - Placeholder style reference
* `data-hyui-content-padding` - Content padding (e.g., `(Horizontal:10,Vertical:8)`)

**Example**:

```html
<textarea rows="5" placeholder="Enter description..."></textarea>
<textarea data-hyui-auto-grow="true" data-hyui-max-visible-lines="10"></textarea>
```

### `<select>`

Maps to `DropdownBoxBuilder`. Use `<option>` children for entries.

**Attributes**:

* `data-hyui-allowunselection` - Allow deselecting items
* `data-hyui-maxselection` - Maximum selectable items
* `data-hyui-entryheight` - Height of each entry
* `data-hyui-showlabel` - Show or hide label

**Example**:

```html
<select id="difficulty">
    <option value="easy">Easy</option>
    <option value="medium" selected>Medium</option>
    <option value="hard">Hard</option>
</select>
```

## Image Elements

### `<img>`

Maps to `ImageBuilder` or `DynamicImageBuilder` (with `dynamic-image` class).

**Attributes**:

* `src` - Image path (relative to `Common/UI/Custom`)
* `width` - Image width (maps to anchor-width)
* `height` - Image height (maps to anchor-height)
* `class="dynamic-image"` - Enable dynamic image loading from URL

**Examples**:

```html
<img src="logo.png" width="128" height="128" />
<img class="dynamic-image" src="https://example.com/image.png" />
```

**Note**: Static images require `@2x.png` suffix in filename.

### `<hyvatar>`

Maps to `HyvatarImageBuilder`. Renders Hyvatar avatars as dynamic images.

**Attributes**:

* `username` - Hyvatar username to render
* `render` - Render type: `head`, `full`, `cape`
* `size` - Image size (64-2048)
* `rotate` - Rotation angle (0-360 degrees)
* `cape` - Cape override for `render="cape"`
* `width`, `height` - Override dimensions

**Example**:

```html
<hyvatar username="Elyra" render="full" size="256" rotate="45"></hyvatar>
<hyvatar username="PlayerName" render="head" size="128"></hyvatar>
```

### `<sprite>`

Maps to `SpriteBuilder`. Animated sprite display.

**Attributes**:

* `src` - Sprite sheet path
* `data-hyui-frame-width` - Width of each frame
* `data-hyui-frame-height` - Height of each frame
* `data-hyui-frame-per-row` - Frames per row in sprite sheet
* `data-hyui-frame-count` - Total frame count
* `data-hyui-fps` - Animation speed (frames per second)

**Example**:

```html
<sprite src="animations/spin.png"
        data-hyui-frame-width="32"
        data-hyui-frame-height="32"
        data-hyui-frame-per-row="8"
        data-hyui-frame-count="16"
        data-hyui-fps="24"></sprite>
```

## Progress Elements

### `<progress>`

Maps to `ProgressBarBuilder` or `CircularProgressBar` (with `circular-progress` class).

**Standard Attributes**:

* `value` - Current progress value
* `max` - Maximum value
* `data-hyui-bar-texture-path` - Fill texture path
* `data-hyui-effect-texture-path` - Effect texture path
* `data-hyui-effect-width` - Effect width
* `data-hyui-effect-height` - Effect height
* `data-hyui-effect-offset` - Effect offset
* `data-hyui-direction` - Fill direction: `start`, `end`
* `data-hyui-alignment` - Orientation: `horizontal`, `vertical`

**Circular Progress Attributes** (with `circular-progress` class):

* `data-hyui-mask-texture-path` - Mask texture path
* `data-hyui-color` - Tint color (hex)

**Examples**:

```html
<progress value="75" max="100"></progress>
<progress class="circular-progress" value="50" max="100" data-hyui-color="#00ff00"></progress>
```

## Item Elements

### `<span class="item-icon">`

Maps to `ItemIconBuilder`. Displays an item icon.

**Attributes**:

* `data-hyui-item-id` - Item ID to display

**Example**:

```html
<span class="item-icon" data-hyui-item-id="minecraft:diamond"></span>
```

### `<span class="item-slot">`

Maps to `ItemSlotBuilder`. Displays a full item slot with background.

**Attributes**:

* `data-hyui-item-id` - Item ID to display
* `data-hyui-show-quality-background` - Show item quality background
* `data-hyui-show-quantity` - Show item quantity

**Example**:

```html
<span class="item-slot"
      data-hyui-item-id="minecraft:diamond_sword"
      data-hyui-show-quality-background="true"
      data-hyui-show-quantity="true"></span>
```

## Navigation Elements

### `<nav class="tabs">`

Maps to `TabNavigationBuilder`. Tab navigation bar.

**Attributes**:

* `data-tabs` - Tab definition in format: `tabId:Label` or `tabId:Label:contentId`, comma-separated
* `data-selected` - Initially selected tab ID

**Children**: Can contain `<button>` elements with `data-tab` and `data-tab-content` attributes.

**Examples**:

```html
<nav class="tabs"
     data-tabs="home:Home,settings:Settings,about:About"
     data-selected="home"></nav>

<nav class="tabs" data-selected="tab1">
    <button data-tab="tab1" data-tab-content="content1">Tab 1</button>
    <button data-tab="tab2" data-tab-content="content2">Tab 2</button>
</nav>
```

## Game-Specific Elements

### `<block-selector>`, `<hyui-block-selector>`

Maps to `BlockSelectorBuilder`. Block selection UI.

**Attributes**:

* `data-hyui-capacity` or `capacity` - Maximum capacity

**Example**:

```html
<block-selector capacity="64"></block-selector>
```

### `<menu-item>`, `<hyui-menu-item>`

Maps to `MenuItemBuilder`. Menu item component.

**Attributes**:

* `data-hyui-text` - Menu item text (or use text content)
* `data-hyui-text-tooltip-style` - Tooltip style reference
* `data-hyui-popup-style` - Popup style reference
* `data-hyui-icon` - Icon texture path
* `data-hyui-icon-border` - Icon border size
* `data-hyui-icon-anchor-*` - Icon anchor properties (left, right, top, bottom, width, height, full, horizontal, vertical)

**Example**:

```html
<menu-item data-hyui-text="Save" data-hyui-icon="icons/save.png"></menu-item>
```

### `<reorderable-list-grip>`, `<hyui-reorderable-list-grip>`

Maps to `ReorderableListGripBuilder`. Drag handle for reorderable lists.

**Example**:

```html
<reorderable-list-grip></reorderable-list-grip>
```

### `<scene-blur>`, `<hyui-scene-blur>`

Maps to `SceneBlurBuilder`. Applies scene blur effect.

**Example**:

```html
<scene-blur></scene-blur>
```

## Common Attributes

All elements support these common attributes:

* `id` - Element identifier for events and queries
* `class` - CSS classes (space-separated)
* `style` - Inline CSS styles
* `data-hyui-tooltiptext` - Tooltip text
* `data-hyui-flexweight` - Flex weight for layout
* `data-hyui-style` - Raw HyUIStyle properties (e.g., `SlotSpacing: 6; SlotSize: 64`)
* `data-hyui-hover-style` - Hover state CSS styles
* `data-hyui-default-style` - Apply default Hytale styling

## CSS Class Support

Apply default Hytale styles with the `default-style` class:

```html
<label class="default-style">Styled Label</label>
<input type="text" class="default-style" />
<input type="checkbox" class="default-style" />
```

Supported on: labels, input fields, sliders, checkboxes, color pickers, toggle buttons, color picker dropdown boxes.
