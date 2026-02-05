# Basic Styling

We start with the basics: applying default styles and making simple customizations. This is "I want a button that looks like a Hytale button" territory.

In this tutorial, we will:

* Use `DefaultStyles` for instant Hytale-native appearance
* Apply style classes to various elements
* Make simple customizations
* Combine `HyUIStyle` with style classes

{% stepper %}
{% step %}
### The Default Button

Let's create a button that looks like it belongs in Hytale:

```java
PageBuilder.pageForPlayer(playerRef)
    .addElement(PageOverlayBuilder.pageOverlay()
        .addChild(ContainerBuilder.container()
            .withTitleText("Styled Buttons")
            .addContentChild(ButtonBuilder.textButton()
                .withId("primary-btn")
                .withText("Primary Button")
                .withStyle(DefaultStyles.primaryTextButtonStyle())
                .addEventListener(CustomUIEventBindingType.Activating, (ignored, ctx) -> {
                    playerRef.sendMessage(Message.raw("Primary button clicked!"));
                }))
            .addContentChild(ButtonBuilder.textButton()
                .withId("secondary-btn")
                .withText("Secondary Button")
                .withStyle(DefaultStyles.secondaryTextButtonStyle()))
            .addContentChild(ButtonBuilder.textButton()
                .withId("destructive-btn")
                .withText("Delete")
                .withStyle(DefaultStyles.destructiveTextButtonStyle()))
        ))
    .open(store);
```

Three buttons, three different styles, zero texture paths to remember. `DefaultStyles` handles all the heavy lifting: backgrounds, hover states, press states, disabled states, sounds, and label styling.
{% endstep %}

{% step %}
### Static Convenience Methods

Most style classes provide static methods that call `DefaultStyles`:

```java
// These pairs are equivalent:
ButtonStyle.primaryStyle()
DefaultStyles.primaryButtonStyle()

TextButtonStyle.primaryStyle()
DefaultStyles.primaryTextButtonStyle()

SliderStyle.defaultStyle()
DefaultStyles.defaultSliderStyle()
```

Use whichever feels more natural. The static methods on the style classes are just shortcuts.

```java
// Short form
ButtonBuilder.textButton()
    .withText("Click Me")
    .withStyle(TextButtonStyle.primaryStyle());

// Equivalent long form
ButtonBuilder.textButton()
    .withText("Click Me")
    .withStyle(DefaultStyles.primaryTextButtonStyle());
```
{% endstep %}

{% step %}
### Sliders

Input elements work the same way:

```java
GroupBuilder.group()
    .withLayoutMode("Top")
    .addChild(SliderBuilder.slider()
        .withId("volume-slider")
        .withMin(0)
        .withMax(100)
        .withValue(75)
        .withStyle(SliderStyle.defaultStyle())
        .addEventListener(CustomUIEventBindingType.ValueChanged, (value, ctx) -> {
            playerRef.sendMessage(Message.raw("Volume: " + value));
        }));
```

Default styles give you:
- Proper hover and pressed states
- Sound effects on interaction
- Consistent sizing and spacing
- Hytale's visual design language
{% endstep %}

{% step %}
### Dropdowns

Dropdowns have more complex styling (panel backgrounds, scrollbars, entry heights, etc.), but `DefaultStyles` handles it:

```java
DropdownBoxBuilder.dropdownBox()
    .withId("difficulty-dropdown")
    .addOption("Easy", "easy")
    .addOption("Medium", "medium")
    .addOption("Hard", "hard")
    .withValue("medium")
    .withStyle(DefaultStyles.defaultDropdownBoxStyle())
    .addEventListener(CustomUIEventBindingType.ValueChanged, (value) -> {
        playerRef.sendMessage(Message.raw("Difficulty: " + value));
    });
```

You get:
- Dropdown button with arrow
- Popup panel with scrollbar
- Entry hover/pressed states
- Sound effects
- Proper label styling
{% endstep %}

{% step %}
### Combining Styles: Labels with Custom Colors

Sometimes you want default behavior but with a twist. You can combine `DefaultStyles` with custom `HyUIStyle`:

```java
LabelBuilder.label()
    .withText("Important Notice")
    .withStyle(DefaultStyles.defaultLabelStyle()
        .setTextColor("#ff0000")  // Override just the color
        .setRenderBold(true));     // And make it bold
```

Or for elements that don't have a default style:

```java
LabelBuilder.label()
    .withText("Custom Label")
    .withStyle(new HyUIStyle()
        .setTextColor("#00ff00")
        .setFontSize(24)
        .setRenderUppercase(true)
        .setHorizontalAlignment(Alignment.Center));
```
{% endstep %}

{% step %}
### Input Fields

Text fields and number fields use `InputFieldStyle`:

```java
TextFieldBuilder.textInput()
    .withId("username")
    .withPlaceholderText("Enter username...")
    .withStyle(InputFieldStyle.defaultStyle())
    .addEventListener(CustomUIEventBindingType.ValueChanged, (value) -> {
        playerRef.sendMessage(Message.raw("Username: " + value));
    });

NumberFieldBuilder.numberInput()
    .withId("level")
    .withValue(1.0)
    .withMinValue(1.0)
    .withMaxValue(100.0)
    .withStyle(InputFieldStyle.defaultStyle())
    .addEventListener(CustomUIEventBindingType.ValueChanged, (value) -> {
        playerRef.sendMessage(Message.raw("Level: " + value));
    });
```

Input field styling is simpler than buttons because text fields don't have as many interaction states.
{% endstep %}

{% step %}
### Color Pickers

Color pickers have their own style class:

```java
new ColorPickerBuilder()
    .withId("theme-color")
    .withValue("#ff6600")
    .withStyle(ColorPickerStyle.defaultStyle())
    .addEventListener(CustomUIEventBindingType.ValueChanged, (color) -> {
        playerRef.sendMessage(Message.raw("Color: " + color));
    });

// Or the dropdown variant
ColorPickerDropdownBoxBuilder.colorPickerDropdownBox()
    .withId("palette-color")
    .withFormat(ColorFormat.Rgba)
    .withDisplayTextField(true)
    .withStyle(ColorPickerDropdownBoxStyle.defaultStyle())
    .addEventListener(CustomUIEventBindingType.ValueChanged, (color) -> {
        playerRef.sendMessage(Message.raw("Palette: " + color));
    });
```
{% endstep %}

{% step %}
### Complete Example: Settings Panel

Let's build a complete settings panel using only default styles:

```java
PageBuilder.pageForPlayer(playerRef)
    .addElement(PageOverlayBuilder.pageOverlay()
        .addChild(ContainerBuilder.decoratedContainer()
            .withTitleText("Game Settings")
            .addContentChild(GroupBuilder.group()
                .withLayoutMode("Top")
                .withPadding(HyUIPadding.all(10))

                // Volume slider
                .addChild(LabelBuilder.label()
                    .withText("Master Volume")
                    .withStyle(DefaultStyles.defaultLabelStyle()))
                .addChild(SliderBuilder.slider()
                    .withId("volume")
                    .withMin(0)
                    .withMax(100)
                    .withValue(75)
                    .withStyle(SliderStyle.defaultStyle()))

                // Difficulty dropdown
                .addChild(LabelBuilder.label()
                    .withText("Difficulty")
                    .withStyle(DefaultStyles.defaultLabelStyle())
                    .withAnchor(new HyUIAnchor().setTop(10)))
                .addChild(DropdownBoxBuilder.dropdownBox()
                    .withId("difficulty")
                    .addOption("Easy", "easy")
                    .addOption("Normal", "normal")
                    .addOption("Hard", "hard")
                    .withValue("normal")
                    .withStyle(DefaultStyles.defaultDropdownBoxStyle()))

                // Enable subtitles
                .addChild(LabelBuilder.label()
                    .withText("Enable Subtitles")
                    .withStyle(DefaultStyles.defaultLabelStyle())
                    .withAnchor(new HyUIAnchor().setTop(10)))
                // Action buttons
                .addChild(GroupBuilder.group()
                    .withLayoutMode("Left")
                    .withAnchor(new HyUIAnchor().setTop(20))
                    .addChild(ButtonBuilder.textButton()
                        .withId("save")
                        .withText("Save")
                        .withStyle(TextButtonStyle.primaryStyle())
                        .addEventListener(CustomUIEventBindingType.Activating, (ignored, ctx) -> {
                            playerRef.sendMessage(Message.raw("Settings saved!"));
                        }))
                    .addChild(ButtonBuilder.textButton()
                        .withId("cancel")
                        .withText("Cancel")
                        .withStyle(TextButtonStyle.secondaryStyle())
                        .withAnchor(new HyUIAnchor().setLeft(10)))
                )
            )
        ))
    .open(store);
```

This entire UI uses `DefaultStyles` and looks completely native to Hytale. No texture paths, no manual state management, no sound file references.
{% endstep %}
{% endstepper %}

## Key Takeaways

1. **`DefaultStyles` is your starting point** - It provides Hytale-native appearance for all common elements.
2. **Static convenience methods exist** - `ButtonStyle.primaryStyle()` is shorter than `DefaultStyles.primaryButtonStyle()`.
3. **Style classes handle interaction states** - Hover, pressed, disabled, sounds, etc.
4. **You can mix and match** - Use default styles and override specific properties with `HyUIStyle`.
5. **Start simple** - Default styles cover 90% of use cases. Only build custom styles when you need them.

Next up: [Style Classes Deep Dive](style-classes-deep-dive.md) - We'll dissect how style classes work under the hood and build our own.
