# Tutorial: Styling with Builders

Welcome to the world of builder-based styling. This is where you graduate from stringing together `.withStyle(new HyUIStyle().setTextColor("#ff0000"))` and enter a realm where buttons have opinions about their hover states and checkboxes know when they are checked.

We will cover:

* The `.withStyle(BsonSerializable)` method and why it exists
* Style classes in the `au.ellie.hyui.types` package
* Using `DefaultStyles` for consistent Hytale-native appearance
* Building your own style classes
* When to use style classes vs. `HyUIStyle`

{% stepper %}
{% step %}
### What is `.withStyle(BsonSerializable)`?

Every builder that supports styling has a `.withStyle()` method. It accepts any class that implements `BsonSerializable`, which means it can be serialized to BSON (the data format Hytale's UI system uses).

There are two main types of styles you'll use:

1. **`HyUIStyle`** - A general-purpose style for text, colors, alignment, etc.
2. **Style classes** - Specialized classes like `ButtonStyle`, `CheckBoxStyle`, `DropdownBoxStyle`, etc.

```java
// General styling
LabelBuilder.label()
    .withText("Hello")
    .withStyle(new HyUIStyle()
        .setTextColor("#ff0000")
        .setFontSize(20)
        .setRenderBold(true));

// Specialized button styling
ButtonBuilder.textButton()
    .withText("Click Me")
    .withStyle(ButtonStyle.primaryStyle());

```
{% endstep %}

{% step %}
### Why Style Classes?

Style classes solve a specific problem: **stateful styling**. A button needs to look different when it's hovered, pressed, or disabled. A checkbox needs different visuals when checked vs unchecked.

`HyUIStyle` can handle simple cases, but when you need multiple states with different backgrounds, labels, and sounds, you need a style class:

```java
ButtonStyle style = new ButtonStyle()
    .withDefault(new ButtonStyleState()
        .withBackground(new HyUIPatchStyle().setTexturePath("button_default.png")))
    .withHovered(new ButtonStyleState()
        .withBackground(new HyUIPatchStyle().setTexturePath("button_hover.png")))
    .withPressed(new ButtonStyleState()
        .withBackground(new HyUIPatchStyle().setTexturePath("button_pressed.png")))
    .withDisabled(new ButtonStyleState()
        .withBackground(new HyUIPatchStyle().setTexturePath("button_disabled.png")))
    .withSounds(DefaultStyles.buttonSounds());

ButtonBuilder.textButton()
    .withText("Styled Button")
    .withStyle(style);
```

This is verbose, but it gives you complete control. Fortunately, `DefaultStyles` provides pre-built styles for common cases.
{% endstep %}

{% step %}
### The `DefaultStyles` Class

`DefaultStyles` is your friend. It contains pre-configured style definitions that match Hytale's native UI appearance:

```java
// Use Hytale's primary button style
ButtonBuilder.textButton()
    .withText("Primary")
    .withStyle(DefaultStyles.primaryTextButtonStyle());

// Use Hytale's destructive button style
ButtonBuilder.textButton()
    .withText("Delete")
    .withStyle(DefaultStyles.destructiveTextButtonStyle());

// Use Hytale's default dropdown style
DropdownBoxBuilder.dropdownBox()
    .addOption("Option 1", "opt1")
    .withStyle(DefaultStyles.defaultDropdownBoxStyle());
```

Many style classes also provide static convenience methods:

```java
// These are equivalent
ButtonStyle.primaryStyle()
DefaultStyles.primaryButtonStyle()

```
{% endstep %}

{% step %}
### Quick Reference: Available Style Classes

Here's what lives in `au.ellie.hyui.types`:

**Button Styles:**
- `ButtonStyle` - For regular buttons (icon buttons, raw buttons, etc.)
- `TextButtonStyle` - For text buttons with label states
- `ButtonSounds` - Sound effects for button interactions

**Input Styles:**
- `InputFieldStyle` - Text fields and number fields
- `InputFieldDecorationStyle` - Decorative borders/backgrounds for input fields
- `CheckBoxStyle` - Checkbox appearance (checked/unchecked states)
- `SliderStyle` - Slider appearance
- `ColorPickerStyle` - Color picker appearance
- `ColorPickerDropdownBoxStyle` - Dropdown-style color picker

**Dropdown Styles:**
- `DropdownBoxStyle` - Standard dropdown boxes
- `FileDropdownBoxStyle` - File selection dropdowns
- `DropdownBoxSounds` - Sound effects for dropdown interactions

**Navigation Styles:**
- `TabStyle` - Individual tab appearance
- `TabNavigationStyle` - Tab navigation bar
- `TabStateStyle` - Tab state indicators

**Other Styles:**
- `TextTooltipStyle` - Tooltip appearance
- `PopupMenuLayerStyle` - Popup menu styling
- `MenuItemStyle` - Menu item appearance
- `SubMenuItemStyle` - Submenu item appearance
- `ItemGridStyle` - Item grid appearance
- `BlockSelectorStyle` - Block selector appearance
- `LabeledCheckBoxStyle` - Checkbox with integrated label
- `ScrollbarStyle` - Scrollbar appearance
- `SoundStyle` - Individual sound effect definitions
- `SpriteFrame` - Sprite animation frames

**Backgrounds & Decorations:**
- `HyUIPatchStyle` - 9-patch backgrounds with borders
- `HyUIStyle` - General text and alignment styles
{% endstep %}

{% step %}
### When to Use What?

**Use `HyUIStyle` when:**
- You need simple text styling (color, size, bold, etc.)
- You're styling labels, text, or single-state elements
- You don't need hover/pressed/disabled states

**Use Style Classes when:**
- You need multiple visual states (hover, pressed, disabled, etc.)
- You're working with interactive elements (buttons, labeled checkboxes, dropdowns)
- You want sounds on interactions
- You need precise control over backgrounds, borders, and layouts

**Use `DefaultStyles` when:**
- You want your UI to match Hytale's look and feel
- You're prototyping and need something that "just works"
- You're building standard UI components
{% endstep %}
{% endstepper %}

## Tutorial Structure

1. **[Basic Styling](basic-styling.md)** - Apply default styles and simple customization
2. **[Style Classes Deep Dive](style-classes-deep-dive.md)** - Understand how style classes work
3. **[Custom Styles](custom-styles.md)** - Build your own style definitions
4. **[Best Practices](best-practices.md)** - Patterns for maintainable styling

Let's dive in.
