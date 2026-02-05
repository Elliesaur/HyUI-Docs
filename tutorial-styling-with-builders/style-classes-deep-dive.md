# Style Classes Deep Dive

Now we open the hood. Style classes are not magic—they are structured data that gets serialized to BSON and sent to Hytale's UI system. Understanding how they work will help you customize them and build your own.

In this tutorial, we will:

* Understand the `HyUIBsonSerializable` interface
* Examine how style classes are structured
* Learn about state-based styling
* Build a custom button style from scratch
* Explore backgrounds, sounds, and patch styles

{% stepper %}
{% step %}
### The `HyUIBsonSerializable` Interface

All style classes implement `HyUIBsonSerializable`:

```java
public interface HyUIBsonSerializable {
    void applyTo(BsonDocumentHelper doc);

    default BsonDocument toBsonDocument() {
        BsonDocumentHelper doc = new BsonDocumentHelper();
        applyTo(doc);
        return doc.toDocument();
    }
}
```

When you call `.withStyle(someStyle)`, HyUI serializes it to a BSON document and sends it to the game client. The client reads the structure and applies it to the UI element.

This is why `.withStyle()` accepts `BsonSerializable`—it can be *any* class that knows how to serialize itself to BSON.
{% endstep %}

{% step %}
### Anatomy of `ButtonStyle`

Let's look at how `ButtonStyle` is structured:

```java
public class ButtonStyle implements HyUIBsonSerializable {
    private ButtonStyleState def;      // Default appearance
    private ButtonStyleState hovered;   // Hover appearance
    private ButtonStyleState pressed;   // Pressed appearance
    private ButtonStyleState disabled;  // Disabled appearance
    private ButtonSounds sounds;        // Sound effects

    public ButtonStyle withDefault(ButtonStyleState def) {
        this.def = def;
        return this;
    }

    public ButtonStyle withHovered(ButtonStyleState hovered) {
        this.hovered = hovered;
        return this;
    }

    // ... more withers ...

    @Override
    public void applyTo(BsonDocumentHelper doc) {
        if (def != null) doc.set("Default", def.toBsonDocument());
        if (hovered != null) doc.set("Hovered", hovered.toBsonDocument());
        if (pressed != null) doc.set("Pressed", pressed.toBsonDocument());
        if (disabled != null) doc.set("Disabled", disabled.toBsonDocument());
        if (sounds != null) doc.set("Sounds", sounds.toBsonDocument());
    }
}
```

Each state (default, hovered, pressed, disabled) is its own object. When serialized, it becomes:

```json
{
  "Default": { "Background": { ... } },
  "Hovered": { "Background": { ... } },
  "Pressed": { "Background": { ... } },
  "Disabled": { "Background": { ... } },
  "Sounds": { ... }
}
```

This structure tells the game client: "When the button is in this state, render it like this."
{% endstep %}

{% step %}
### State Objects: `ButtonStyleState`

Each state has its own appearance definition:

```java
public class ButtonStyleState implements HyUIBsonSerializable {
    private HyUIPatchStyle background;

    public ButtonStyleState withBackground(HyUIPatchStyle background) {
        this.background = background;
        return this;
    }

    @Override
    public void applyTo(BsonDocumentHelper doc) {
        if (background != null) doc.set("Background", background.toBsonDocument());
    }
}
```

For text buttons, `TextButtonStyleState` adds label styling:

```java
public class TextButtonStyleState extends ButtonStyleState {
    private HyUIStyle labelStyle;

    public TextButtonStyleState withLabelStyle(HyUIStyle labelStyle) {
        this.labelStyle = labelStyle;
        return this;
    }

    @Override
    public void applyTo(BsonDocumentHelper doc) {
        super.applyTo(doc);  // Apply background
        if (labelStyle != null) doc.set("Label", labelStyle.toBsonDocument());
    }
}
```

This lets you style both the button background *and* the text label for each state.
{% endstep %}

{% step %}
### Building a Button Style from Scratch

Let's create a custom button style step-by-step:

```java
// Create background patch styles for each state
HyUIPatchStyle defaultBg = new HyUIPatchStyle()
    .setTexturePath("Custom/MyButtonDefault.png")
    .setBorder(12);

HyUIPatchStyle hoveredBg = new HyUIPatchStyle()
    .setTexturePath("Custom/MyButtonHover.png")
    .setBorder(12);

HyUIPatchStyle pressedBg = new HyUIPatchStyle()
    .setTexturePath("Custom/MyButtonPressed.png")
    .setBorder(12);

HyUIPatchStyle disabledBg = new HyUIPatchStyle()
    .setColor("#666666")
    .setBorder(12);

// Create the button style with all states
ButtonStyle customButtonStyle = new ButtonStyle()
    .withDefault(new ButtonStyleState()
        .withBackground(defaultBg))
    .withHovered(new ButtonStyleState()
        .withBackground(hoveredBg))
    .withPressed(new ButtonStyleState()
        .withBackground(pressedBg))
    .withDisabled(new ButtonStyleState()
        .withBackground(disabledBg))
    .withSounds(DefaultStyles.buttonSounds());

// Use it
ButtonBuilder.customButton()
    .withId("custom-btn")
    .withStyle(customButtonStyle);
```

This button will:
- Show `MyButtonDefault.png` normally
- Show `MyButtonHover.png` on hover
- Show `MyButtonPressed.png` when pressed
- Show gray color when disabled
- Play Hytale's default button sounds
{% endstep %}

{% step %}
### Text Button Style with Label Styling

For text buttons, we use `TextButtonStyle` and `TextButtonStyleState`:

```java
// Label styles for each state
HyUIStyle defaultLabel = new HyUIStyle()
    .setTextColor("#ffffff")
    .setFontSize(16)
    .setRenderBold(true)
    .setHorizontalAlignment(Alignment.Center)
    .setVerticalAlignment(Alignment.Center);

HyUIStyle hoveredLabel = new HyUIStyle()
    .setTextColor("#ffff00")  // Yellow on hover
    .setFontSize(16)
    .setRenderBold(true)
    .setHorizontalAlignment(Alignment.Center)
    .setVerticalAlignment(Alignment.Center);

HyUIStyle disabledLabel = new HyUIStyle()
    .setTextColor("#888888")  // Gray when disabled
    .setFontSize(16)
    .setRenderBold(true)
    .setHorizontalAlignment(Alignment.Center)
    .setVerticalAlignment(Alignment.Center);

// Create text button style
TextButtonStyle customTextButtonStyle = new TextButtonStyle()
    .withDefault((TextButtonStyleState) new TextButtonStyleState()
        .withBackground(defaultBg)
        .withLabelStyle(defaultLabel))
    .withHovered((TextButtonStyleState) new TextButtonStyleState()
        .withBackground(hoveredBg)
        .withLabelStyle(hoveredLabel))
    .withPressed((TextButtonStyleState) new TextButtonStyleState()
        .withBackground(pressedBg)
        .withLabelStyle(defaultLabel))  // Same as default
    .withDisabled((TextButtonStyleState) new TextButtonStyleState()
        .withBackground(disabledBg)
        .withLabelStyle(disabledLabel))
    .withSounds(DefaultStyles.buttonSounds());

// Use it
ButtonBuilder.textButton()
    .withId("my-text-btn")
    .withText("Custom Text Button")
    .withStyle(customTextButtonStyle);
```

Now the button's text changes color when you hover or disable it.
{% endstep %}

{% step %}
### Backgrounds: `HyUIPatchStyle`

`HyUIPatchStyle` represents a 9-patch background (a texture with stretchable borders):

```java
// Texture-based background
HyUIPatchStyle textureBg = new HyUIPatchStyle()
    .setTexturePath("Custom/MyTexture.png")
    .setBorder(16);  // All borders 16px

// Texture with different horizontal/vertical borders
HyUIPatchStyle textureBg2 = new HyUIPatchStyle()
    .setTexturePath("Custom/MyTexture.png")
    .setVerticalBorder(12)
    .setHorizontalBorder(20);

// Solid color background
HyUIPatchStyle colorBg = new HyUIPatchStyle()
    .setColor("#ff0000");  // Red

// Color with transparency
HyUIPatchStyle transparentBg = new HyUIPatchStyle()
    .setColor("#00000080");  // 50% black

// Texture + tint color
HyUIPatchStyle tintedBg = new HyUIPatchStyle()
    .setTexturePath("Custom/MyTexture.png")
    .setColor("#ff000040")  // Red tint with transparency
    .setBorder(16);
```

The texture path is relative to your mod's resources. Remember to use `@2x.png` suffix for high-res textures.
{% endstep %}

{% step %}
### Sounds: `ButtonSounds` and `SoundStyle`

Buttons (and dropdowns) can have sound effects:

```java
ButtonSounds sounds = new ButtonSounds()
    .withActivate(new SoundStyle()
        .withSoundPath("Sounds/ButtonsLightActivate.ogg")
        .withMinPitch(-0.2f)
        .withMaxPitch(0.2f)
        .withVolume(2))
    .withMouseHover(new SoundStyle()
        .withSoundPath("Sounds/ButtonsLightHover.ogg")
        .withVolume(6));

ButtonStyle styleWithSounds = new ButtonStyle()
    .withDefault(new ButtonStyleState().withBackground(someBg))
    .withSounds(sounds);
```

Or use Hytale's default sounds:

```java
ButtonStyle style = new ButtonStyle()
    .withDefault(new ButtonStyleState().withBackground(someBg))
    .withSounds(DefaultStyles.buttonSounds());
```

Dropdowns have three sound events:

```java
DropdownBoxSounds dropdownSounds = new DropdownBoxSounds()
    .withActivate(new SoundStyle()
        .withSoundPath("Sounds/TickActivate.ogg")
        .withVolume(6))
    .withMouseHover(new SoundStyle()
        .withSoundPath("Sounds/ButtonsLightHover.ogg")
        .withVolume(6))
    .withClose(new SoundStyle()
        .withSoundPath("Sounds/UntickActivate.ogg")
        .withVolume(6));
```
{% endstep %}

{% step %}
### Dropdown Styles: Complex but Powerful

`DropdownBoxStyle` is one of the most complex style classes because dropdowns have many visual parts:

- The dropdown button (with arrow)
- The popup panel (with background and scrollbar)
- Individual entries (with hover/pressed states)
- Label styles

Here's a simplified example:

```java
DropdownBoxStyle customDropdown = new DropdownBoxStyle()
    // Dropdown button appearance
    .withDefaultBackground(new HyUIPatchStyle()
        .setTexturePath("Custom/DropdownDefault.png")
        .setBorder(16))
    .withHoveredBackground(new HyUIPatchStyle()
        .setTexturePath("Custom/DropdownHover.png")
        .setBorder(16))
    .withPressedBackground(new HyUIPatchStyle()
        .setTexturePath("Custom/DropdownPressed.png")
        .setBorder(16))

    // Arrow icon
    .withDefaultArrowTexturePath("Custom/ArrowDown.png")
    .withHoveredArrowTexturePath("Custom/ArrowDownHover.png")
    .withPressedArrowTexturePath("Custom/ArrowUp.png")
    .withArrowWidth(12)
    .withArrowHeight(7)

    // Label styling
    .withLabelStyle(new HyUIStyle()
        .setTextColor("#96a9be")
        .setFontSize(14))
    .withEntryLabelStyle(new HyUIStyle()
        .setTextColor("#b7cedd")
        .setFontSize(14))

    // Popup panel
    .withPanelBackground(DefaultStyles.panelBackground())
    .withPanelPadding(6)
    .withPanelAlign("Right")
    .withPanelOffset(7)
    .withEntryHeight(30)
    .withEntriesInViewport(10)

    // Entry hover/pressed states
    .withHoveredEntryBackground(new HyUIPatchStyle().setColor("#0a0f17"))
    .withPressedEntryBackground(new HyUIPatchStyle().setColor("#0f1621"))

    // Sounds
    .withSounds(DefaultStyles.dropdownBoxSounds())
    .withEntrySounds(DefaultStyles.buttonSounds());

DropdownBoxBuilder.dropdownBox()
    .addOption("Option 1", "opt1")
    .addOption("Option 2", "opt2")
    .withStyle(customDropdown);
```

This is why `DefaultStyles.defaultDropdownBoxStyle()` exists—it's verbose to write manually.
{% endstep %}
{% endstepper %}

## Key Takeaways

1. **Style classes are structured data** - They serialize to BSON and tell the game client how to render elements.
2. **State-based styling is the core concept** - Different visual appearance for different interaction states.
3. **`HyUIPatchStyle` handles backgrounds** - Textures, colors, tints, and 9-patch borders.
4. **Sounds enhance interactivity** - Use `SoundStyle` to add audio feedback.
5. **Start with `DefaultStyles`** - Only build custom styles when you need them.
6. **Style classes compose** - Buttons contain states, states contain backgrounds and labels, etc.

Next up: [Custom Styles](custom-styles.md) - We'll build complete custom style systems for practical use cases.
