# Custom Styles

Now we build. You have your own design language, your own textures, and a vision for how your UI should look. This tutorial shows you how to create reusable custom style systems.

In this tutorial, we will:

* Build a custom button theme
* Create a style manager class for consistency
* Build themed UI components
* Extend `DefaultStyles` patterns
* Handle style inheritance and variations

{% stepper %}
{% step %}
### The Problem: Consistency

When building a mod with multiple UIs, you want consistency. Every "confirm" button should look the same. Every "danger" action should use the same red. Every tooltip should have the same background.

Hard-coding styles in every builder is a maintenance nightmare:

```java
// ❌ Don't do this everywhere
ButtonBuilder.textButton()
    .withText("Confirm")
    .withStyle(new TextButtonStyle()
        .withDefault((TextButtonStyleState) new TextButtonStyleState()
            .withBackground(new HyUIPatchStyle()
                .setTexturePath("MyMod/GreenButton.png")
                .setBorder(12))
            .withLabelStyle(new HyUIStyle()
                .setTextColor("#ffffff")
                .setFontSize(16)))
        // ... more states ...
    );
```

Repeating this 50 times across your codebase is tedious and error-prone. The solution: **create a style manager**.
{% endstep %}

{% step %}
### Create a Style Manager

Make a class that holds all your custom styles in one place:

```java
package com.example.mymod.styles;

import au.ellie.hyui.builders.*;
import au.ellie.hyui.types.*;
import static au.ellie.hyui.types.DefaultStyles.*;

public final class MyModStyles {
    private MyModStyles() {}

    // Color palette
    private static final String PRIMARY_COLOR = "#4a90e2";
    private static final String SUCCESS_COLOR = "#4caf50";
    private static final String DANGER_COLOR = "#f44336";
    private static final String TEXT_COLOR = "#ffffff";
    private static final String DISABLED_COLOR = "#666666";

    // Texture paths
    private static final String BUTTON_DEFAULT = "MyMod/Buttons/Default.png";
    private static final String BUTTON_HOVER = "MyMod/Buttons/Hover.png";
    private static final String BUTTON_PRESSED = "MyMod/Buttons/Pressed.png";

    /**
     * Primary button style - for main actions (Save, Confirm, etc.)
     */
    public static TextButtonStyle primaryButton() {
        return new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(PRIMARY_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withHovered((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_HOVER)
                    .setColor(PRIMARY_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withPressed((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_PRESSED)
                    .setColor(PRIMARY_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withDisabled((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(DISABLED_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(DISABLED_COLOR)))
            .withSounds(buttonSounds());
    }

    /**
     * Success button style - for positive actions (Complete, Accept, etc.)
     */
    public static TextButtonStyle successButton() {
        return new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(SUCCESS_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withHovered((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_HOVER)
                    .setColor(SUCCESS_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withPressed((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_PRESSED)
                    .setColor(SUCCESS_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withDisabled((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(DISABLED_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(DISABLED_COLOR)))
            .withSounds(buttonSounds());
    }

    /**
     * Danger button style - for destructive actions (Delete, Cancel, etc.)
     */
    public static TextButtonStyle dangerButton() {
        return new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(DANGER_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withHovered((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_HOVER)
                    .setColor(DANGER_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withPressed((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_PRESSED)
                    .setColor(DANGER_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withDisabled((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(DISABLED_COLOR)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(DISABLED_COLOR)))
            .withSounds(buttonDestructiveSounds());
    }

    /**
     * Helper: Button label style
     */
    private static HyUIStyle buttonLabel(String color) {
        return new HyUIStyle()
            .setTextColor(color)
            .setFontSize(16)
            .setRenderBold(true)
            .setRenderUppercase(true)
            .setHorizontalAlignment(Alignment.Center)
            .setVerticalAlignment(Alignment.Center);
    }

    /**
     * Panel background
     */
    public static HyUIPatchStyle panelBackground() {
        return new HyUIPatchStyle()
            .setTexturePath("MyMod/Panel.png")
            .setBorder(16);
    }

    /**
     * Tooltip style
     */
    public static TextTooltipStyle tooltip() {
        return new TextTooltipStyle()
            .withBackground(new HyUIPatchStyle()
                .setTexturePath("MyMod/Tooltip.png")
                .setBorder(24))
            .withMaxWidth(400)
            .withLabelStyle(new HyUIStyle()
                .setWrap(true)
                .setFontSize(14)
                .setTextColor("#e0e0e0"))
            .withPadding(24);
    }
}
```

Now every UI in your mod can use:

```java
ButtonBuilder.textButton()
    .withText("Save")
    .withStyle(MyModStyles.primaryButton());

ButtonBuilder.textButton()
    .withText("Delete")
    .withStyle(MyModStyles.dangerButton());
```

Consistency achieved. Change the color palette in one place, and every button updates.
{% endstep %}

{% step %}
### Using Your Style Manager

Build a complete UI using your custom styles:

```java
PageBuilder.pageForPlayer(playerRef)
    .addElement(PageOverlayBuilder.pageOverlay()
        .addChild(ContainerBuilder.container()
            .withTitleText("Item Management")
            .withBackground(MyModStyles.panelBackground())
            .addContentChild(GroupBuilder.group()
                .withLayoutMode("Top")
                .withPadding(HyUIPadding.all(10))

                .addChild(LabelBuilder.label()
                    .withText("Are you sure you want to delete this item?")
                    .withStyle(new HyUIStyle()
                        .setTextColor("#ffffff")
                        .setFontSize(16))
                    .withTooltipStyle(MyModStyles.tooltip()))

                .addChild(GroupBuilder.group()
                    .withLayoutMode("Left")
                    .withAnchor(new HyUIAnchor().setTop(20))
                    .addChild(ButtonBuilder.textButton()
                        .withId("confirm-delete")
                        .withText("Delete")
                        .withStyle(MyModStyles.dangerButton()))
                    .addChild(ButtonBuilder.textButton()
                        .withId("cancel")
                        .withText("Cancel")
                        .withStyle(MyModStyles.primaryButton())
                        .withAnchor(new HyUIAnchor().setLeft(10)))
                )
            )
        ))
    .open(store);
```

Every element uses your custom styles. Your mod has a cohesive visual identity.
{% endstep %}

{% step %}
### Extending `DefaultStyles`

You don't have to reinvent the wheel. Extend `DefaultStyles` and override only what you need:

```java
public final class MyModStyles {
    /**
     * Use Hytale's button structure, but with our colors
     */
    public static TextButtonStyle brandedButton() {
        return new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(DefaultStyles.primaryButtonDefaultBackground())
                .withLabelStyle(customLabel()))
            .withHovered((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(DefaultStyles.primaryButtonHoveredBackground())
                .withLabelStyle(customLabel()))
            .withPressed((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(DefaultStyles.primaryButtonPressedBackground())
                .withLabelStyle(customLabel()))
            .withDisabled((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(DefaultStyles.primaryButtonDisabledBackground())
                .withLabelStyle(disabledLabel()))
            .withSounds(DefaultStyles.buttonSounds());
    }

    private static HyUIStyle customLabel() {
        return DefaultStyles.primaryButtonLabelStyle()
            .setTextColor("#ff6600");  // Override just the color
    }

    private static HyUIStyle disabledLabel() {
        return DefaultStyles.primaryButtonDisabledLabelStyle()
            .setTextColor("#666666");
    }

    /**
     * Use Hytale's checkbox, but with custom sounds
     */
    public static CheckBoxStyle customCheckBox() {
        return DefaultStyles.defaultCheckBoxStyle()
            .withChecked(new CheckBoxStyleState()
                .withDefaultBackground(new HyUIPatchStyle()
                    .setTexturePath("MyMod/CheckmarkCustom.png"))
                .withChangedSound(new SoundStyle()
                    .withSoundPath("MyMod/Sounds/Check.ogg")
                    .withVolume(6)));
    }
}
```

This gives you the best of both worlds: Hytale's visual polish with your branding.
{% endstep %}

{% step %}
### Style Variations

Create variations of the same style for different contexts:

```java
public final class MyModStyles {
    /**
     * Standard primary button
     */
    public static TextButtonStyle primaryButton() {
        return buildButton(PRIMARY_COLOR, buttonSounds());
    }

    /**
     * Small primary button (for toolbars, etc.)
     */
    public static TextButtonStyle smallPrimaryButton() {
        TextButtonStyle style = buildButton(PRIMARY_COLOR, buttonSounds());
        // Modify for smaller size
        return style
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT_SMALL)
                    .setColor(PRIMARY_COLOR)
                    .setBorder(8))
                .withLabelStyle(smallButtonLabel(TEXT_COLOR)))
            // ... other states with small variants ...
            ;
    }

    /**
     * Silent primary button (no sounds)
     */
    public static TextButtonStyle silentPrimaryButton() {
        return buildButton(PRIMARY_COLOR, null);  // No sounds
    }

    private static TextButtonStyle buildButton(String color, ButtonSounds sounds) {
        TextButtonStyle style = new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(color)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            // ... other states ...
            ;

        if (sounds != null) {
            style.withSounds(sounds);
        }

        return style;
    }

    private static HyUIStyle smallButtonLabel(String color) {
        return new HyUIStyle()
            .setTextColor(color)
            .setFontSize(12)  // Smaller font
            .setRenderBold(true)
            .setRenderUppercase(true)
            .setHorizontalAlignment(Alignment.Center)
            .setVerticalAlignment(Alignment.Center);
    }
}
```
{% endstep %}

{% step %}
### Advanced: Dynamic Styles

Sometimes you need to generate styles dynamically based on parameters:

```java
public final class MyModStyles {
    /**
     * Generate a button style with a custom color
     */
    public static TextButtonStyle coloredButton(String hexColor) {
        return new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_DEFAULT)
                    .setColor(hexColor)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            .withHovered((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath(BUTTON_HOVER)
                    .setColor(hexColor)
                    .setBorder(12))
                .withLabelStyle(buttonLabel(TEXT_COLOR)))
            // ... other states ...
            .withSounds(buttonSounds());
    }

    /**
     * Generate a rarity-based button style
     */
    public static TextButtonStyle rarityButton(ItemRarity rarity) {
        String color = switch (rarity) {
            case COMMON -> "#ffffff";
            case UNCOMMON -> "#4caf50";
            case RARE -> "#2196f3";
            case EPIC -> "#9c27b0";
            case LEGENDARY -> "#ff9800";
        };

        return coloredButton(color);
    }
}
```

Usage:

```java
// Use a specific rarity color
ButtonBuilder.textButton()
    .withText("Legendary Sword")
    .withStyle(MyModStyles.rarityButton(ItemRarity.LEGENDARY));

// Use any custom color
ButtonBuilder.textButton()
    .withText("Team Color")
    .withStyle(MyModStyles.coloredButton(teamColor));
```
{% endstep %}

{% step %}
### Complete Example: Themed Quest UI

Let's build a quest system with completely custom styling:

```java
// MyModStyles.java
public final class MyModStyles {
    private static final String QUEST_PRIMARY = "#d4af37";  // Gold
    private static final String QUEST_SECONDARY = "#8b4513";  // Brown
    private static final String QUEST_BG = "MyMod/QuestPanel.png";

    public static HyUIPatchStyle questPanelBackground() {
        return new HyUIPatchStyle()
            .setTexturePath(QUEST_BG)
            .setBorder(20);
    }

    public static HyUIStyle questTitleStyle() {
        return new HyUIStyle()
            .setTextColor(QUEST_PRIMARY)
            .setFontSize(24)
            .setRenderBold(true)
            .setRenderUppercase(true)
            .setHorizontalAlignment(Alignment.Center);
    }

    public static HyUIStyle questDescriptionStyle() {
        return new HyUIStyle()
            .setTextColor("#e0e0e0")
            .setFontSize(14)
            .setWrap(true);
    }

    public static TextButtonStyle acceptQuestButton() {
        return new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath("MyMod/Buttons/Quest.png")
                    .setColor(QUEST_PRIMARY)
                    .setBorder(12))
                .withLabelStyle(new HyUIStyle()
                    .setTextColor("#000000")
                    .setFontSize(18)
                    .setRenderBold(true)
                    .setHorizontalAlignment(Alignment.Center)
                    .setVerticalAlignment(Alignment.Center)))
            // ... other states ...
            .withSounds(new ButtonSounds()
                .withActivate(new SoundStyle()
                    .withSoundPath("MyMod/Sounds/QuestAccept.ogg")
                    .withVolume(8)));
    }
}

// Usage in quest UI
PageBuilder.pageForPlayer(playerRef)
    .addElement(PageOverlayBuilder.pageOverlay()
        .addChild(ContainerBuilder.container()
            .withTitleText("Quest: The Lost Artifact")
            .withBackground(MyModStyles.questPanelBackground())
            .addContentChild(GroupBuilder.group()
                .withLayoutMode("Top")
                .withPadding(HyUIPadding.all(15))

                .addChild(LabelBuilder.label()
                    .withText("The Lost Artifact")
                    .withStyle(MyModStyles.questTitleStyle()))

                .addChild(LabelBuilder.label()
                    .withText("Seek the ancient relic deep within the Forgotten Ruins. Beware of traps.")
                    .withStyle(MyModStyles.questDescriptionStyle())
                    .withAnchor(new HyUIAnchor().setTop(10).setWidth(400)))

                .addChild(LabelBuilder.label()
                    .withText("Rewards: 500 Gold, Rare Equipment")
                    .withStyle(new HyUIStyle()
                        .setTextColor(MyModStyles.QUEST_PRIMARY)
                        .setFontSize(14)
                        .setRenderBold(true))
                    .withAnchor(new HyUIAnchor().setTop(15)))

                .addChild(ButtonBuilder.textButton()
                    .withId("accept-quest")
                    .withText("Accept Quest")
                    .withStyle(MyModStyles.acceptQuestButton())
                    .withAnchor(new HyUIAnchor().setTop(20))
                    .addEventListener(CustomUIEventBindingType.Activating, (ignored, ctx) -> {
                        // Accept quest logic
                    }))
            )
        ))
    .open(store);
```

Your quest UI now has its own distinct visual identity that's consistent across your entire mod.
{% endstep %}
{% endstepper %}

## Key Takeaways

1. **Create a style manager class** - Centralize all custom styles in one place.
2. **Use color palettes** - Define colors as constants for consistency.
3. **Build style helpers** - Extract common patterns into private helper methods.
4. **Extend `DefaultStyles`** - Don't rebuild what Hytale already provides well.
5. **Create variations** - Small, large, silent, colored—build what you need.
6. **Dynamic styles for flexibility** - Generate styles based on runtime data when needed.
7. **Test your styles** - Build a style showcase UI to see all your styles in one place.

Next: [Best Practices](best-practices.md) - Patterns for maintainable, scalable styling systems.
