# Best Practices

You have learned the tools. Now we discuss the craft: how to structure styling systems that scale, how to avoid common pitfalls, and how to write UI code that your future self will thank you for.

In this tutorial, we will cover:

* Organizing style code
* Performance considerations
* Testing and debugging styles
* Common mistakes to avoid
* When to use builders vs. HYUIML
* Future-proofing your styles

{% stepper %}
{% step %}
### Organize Style Code by Purpose

Don't dump all styles into one giant class. Organize by purpose:

```
src/main/java/com/example/mymod/styles/
├── MyModStyles.java              // Entry point, basic palette
├── ButtonStyles.java             // All button styles
├── InputStyles.java              // Text fields, checkboxes, sliders
├── DropdownStyles.java           // Dropdown and select styles
├── PanelStyles.java              // Containers, panels, overlays
├── QuestStyles.java              // Quest-specific theming
└── ShopStyles.java               // Shop-specific theming
```

Each class focuses on one aspect:

```java
// ButtonStyles.java
public final class ButtonStyles {
    // Primary actions
    public static TextButtonStyle primary() { ... }
    public static TextButtonStyle primarySmall() { ... }

    // Secondary actions
    public static TextButtonStyle secondary() { ... }

    // Danger actions
    public static TextButtonStyle danger() { ... }

    // Icon buttons
    public static ButtonStyle iconButton() { ... }
}

// QuestStyles.java
public final class QuestStyles {
    public static HyUIPatchStyle questPanel() { ... }
    public static HyUIStyle questTitle() { ... }
    public static TextButtonStyle acceptButton() { ... }
    public static TextButtonStyle declineButton() { ... }
}
```

Usage:

```java
import static com.example.mymod.styles.ButtonStyles.*;
import static com.example.mymod.styles.QuestStyles.*;

ButtonBuilder.textButton()
    .withText("Accept Quest")
    .withStyle(acceptButton());
```
{% endstep %}

{% step %}
### Use Color Palettes

Define colors in a central palette class:

```java
public final class ColorPalette {
    private ColorPalette() {}

    // Primary colors
    public static final String PRIMARY = "#4a90e2";
    public static final String PRIMARY_DARK = "#2e5a8f";
    public static final String PRIMARY_LIGHT = "#7ab3f5";

    // Semantic colors
    public static final String SUCCESS = "#4caf50";
    public static final String WARNING = "#ff9800";
    public static final String DANGER = "#f44336";
    public static final String INFO = "#2196f3";

    // Neutrals
    public static final String WHITE = "#ffffff";
    public static final String BLACK = "#000000";
    public static final String GRAY_LIGHT = "#e0e0e0";
    public static final String GRAY = "#9e9e9e";
    public static final String GRAY_DARK = "#424242";

    // Rarity colors
    public static final String COMMON = "#ffffff";
    public static final String UNCOMMON = "#4caf50";
    public static final String RARE = "#2196f3";
    public static final String EPIC = "#9c27b0";
    public static final String LEGENDARY = "#ff9800";

    // UI-specific
    public static final String TEXT_PRIMARY = "#ffffff";
    public static final String TEXT_SECONDARY = "#b0b0b0";
    public static final String TEXT_DISABLED = "#666666";
    public static final String BACKGROUND_DARK = "#00000080";
    public static final String BACKGROUND_LIGHT = "#ffffff20";
}
```

Then reference colors by name:

```java
new HyUIStyle()
    .setTextColor(ColorPalette.PRIMARY)
    .setFontSize(16);

new HyUIPatchStyle()
    .setColor(ColorPalette.DANGER);
```

Benefits:
- Change colors globally
- Consistent naming
- Easy to read code
- IDE autocomplete helps discovery
{% endstep %}

{% step %}
### Cache Style Objects

Style objects are expensive to construct. Cache them:

```java
public final class ButtonStyles {
    // Cached instances
    private static TextButtonStyle primaryCached = null;
    private static TextButtonStyle secondaryCached = null;

    public static TextButtonStyle primary() {
        if (primaryCached == null) {
            primaryCached = buildPrimary();
        }
        return primaryCached;
    }

    public static TextButtonStyle secondary() {
        if (secondaryCached == null) {
            secondaryCached = buildSecondary();
        }
        return secondaryCached;
    }

    private static TextButtonStyle buildPrimary() {
        return new TextButtonStyle()
            .withDefault((TextButtonStyleState) new TextButtonStyleState()
                .withBackground(new HyUIPatchStyle()
                    .setTexturePath("MyMod/Buttons/Primary.png")
                    .setBorder(12))
                .withLabelStyle(new HyUIStyle()
                    .setTextColor(ColorPalette.TEXT_PRIMARY)
                    .setFontSize(16)))
            // ... more states ...
            .withSounds(DefaultStyles.buttonSounds());
    }

    private static TextButtonStyle buildSecondary() {
        // Similar construction
    }
}
```

Or use a static initializer:

```java
public final class ButtonStyles {
    public static final TextButtonStyle PRIMARY = buildPrimary();
    public static final TextButtonStyle SECONDARY = buildSecondary();
    public static final TextButtonStyle DANGER = buildDanger();

    private static TextButtonStyle buildPrimary() { ... }
    private static TextButtonStyle buildSecondary() { ... }
    private static TextButtonStyle buildDanger() { ... }
}
```

**Trade-off**: Static initialization loads all styles at startup. Lazy initialization loads on first use. Choose based on your mod's size.
{% endstep %}

{% step %}
### Don't Over-Customize

Not every element needs a custom style. Use `DefaultStyles` for common cases:

```java
// ✅ Good: Use defaults for standard elements
ButtonBuilder button = new ButtonBuilder()
    .withValue(false)
    .withStyle(ButtonStyle.primaryStyle());

SliderBuilder slider = SliderBuilder.slider()
    .withMin(0)
    .withMax(100)
    .withValue(50)
    .withStyle(SliderStyle.defaultStyle());
```

Only create custom styles when:
- You need branding consistency
- You're changing visual behavior
- You're adding unique textures or sounds
- `DefaultStyles` doesn't match your design
{% endstep %}

{% step %}
### Test Your Styles with a Showcase UI

Build a style showcase page that displays all your custom styles in one place:

```java
public class StyleShowcaseCommand extends AbstractAsyncCommand {
    @Override
    protected CompletableFuture<Void> executeAsync(CommandContext ctx) {
        // Get player...

        PageBuilder.pageForPlayer(playerRef)
            .addElement(PageOverlayBuilder.pageOverlay()
                .addChild(ContainerBuilder.decoratedContainer()
                    .withTitleText("Style Showcase")
                    .addContentChild(GroupBuilder.group()
                        .withLayoutMode("Top")
                        .withPadding(HyUIPadding.all(10))

                        // Button styles
                        .addChild(LabelBuilder.label()
                            .withText("BUTTONS")
                            .withStyle(new HyUIStyle()
                                .setRenderBold(true)
                                .setFontSize(18)))
                        .addChild(GroupBuilder.group()
                            .withLayoutMode("Left")
                            .addChild(ButtonBuilder.textButton()
                                .withText("Primary")
                                .withStyle(MyModStyles.primaryButton()))
                            .addChild(ButtonBuilder.textButton()
                                .withText("Secondary")
                                .withStyle(MyModStyles.secondaryButton())
                                .withAnchor(new HyUIAnchor().setLeft(10)))
                            .addChild(ButtonBuilder.textButton()
                                .withText("Danger")
                                .withStyle(MyModStyles.dangerButton())
                                .withAnchor(new HyUIAnchor().setLeft(10)))
                        )

                        // Input styles
                        .addChild(LabelBuilder.label()
                            .withText("INPUTS")
                            .withStyle(new HyUIStyle()
                                .setRenderBold(true)
                                .setFontSize(18))
                            .withAnchor(new HyUIAnchor().setTop(20)))
                        .addChild(SliderBuilder.slider()
                            .withMin(0)
                            .withMax(100)
                            .withValue(50)
                            .withStyle(MyModStyles.customSlider()))

                        // ... more showcase elements ...
                    )
                ))
            .open(store);

        return CompletableFuture.completedFuture(null);
    }
}
```

This lets you:
- See all styles in one place
- Test hover/pressed states
- Verify consistency
- Show designers your work
- Catch visual bugs early
{% endstep %}

{% step %}
### Avoid Hardcoded Strings

Don't scatter texture paths and sound paths throughout your code:

```java
// ❌ Bad: Hardcoded paths everywhere
new HyUIPatchStyle()
    .setTexturePath("MyMod/Buttons/Primary.png")
    .setBorder(12);

new SoundStyle()
    .withSoundPath("MyMod/Sounds/ButtonClick.ogg")
    .withVolume(6);
```

Centralize paths:

```java
public final class AssetPaths {
    // Textures
    public static final String BUTTON_PRIMARY = "MyMod/Buttons/Primary.png";
    public static final String BUTTON_SECONDARY = "MyMod/Buttons/Secondary.png";
    public static final String PANEL_BG = "MyMod/Panel.png";

    // Sounds
    public static final String SOUND_BUTTON_CLICK = "MyMod/Sounds/ButtonClick.ogg";
    public static final String SOUND_CHECKBOX_TICK = "MyMod/Sounds/CheckboxTick.ogg";
}

// ✅ Good: Reference by constant
new HyUIPatchStyle()
    .setTexturePath(AssetPaths.BUTTON_PRIMARY)
    .setBorder(12);

new SoundStyle()
    .withSoundPath(AssetPaths.SOUND_BUTTON_CLICK)
    .withVolume(6);
```

Benefits:
- Typos are compile-time errors
- Easy to refactor asset names
- IDE autocomplete helps discovery
{% endstep %}

{% step %}
### Document Your Style System

Add Javadoc comments explaining when to use each style:

```java
/**
 * Button styles for My Mod.
 * <p>
 * Use {@link #primary()} for main actions (Save, Confirm, Accept).
 * Use {@link #secondary()} for alternative actions (Cancel, Back).
 * Use {@link #danger()} for destructive actions (Delete, Remove, Discard).
 */
public final class ButtonStyles {
    /**
     * Primary button style - for main call-to-action buttons.
     * <p>
     * Examples: "Save", "Confirm", "Accept Quest"
     */
    public static TextButtonStyle primary() { ... }

    /**
     * Secondary button style - for less prominent actions.
     * <p>
     * Examples: "Cancel", "Back", "Maybe Later"
     */
    public static TextButtonStyle secondary() { ... }

    /**
     * Danger button style - for destructive actions.
     * <p>
     * Examples: "Delete", "Remove", "Discard Changes"
     * <p>
     * Uses red color palette and destructive sound effects.
     */
    public static TextButtonStyle danger() { ... }
}
```

Your team (and future you) will appreciate it.
{% endstep %}

{% step %}
### When to Use Builders vs. HYUIML

**Use builders + style classes when:**
- You need fine-grained control
- Styles change based on runtime data
- You're building reusable components
- Performance is critical (builders are faster than parsing HTML)

**Use HYUIML when:**
- Layout is complex and nested
- You're prototyping quickly
- Content is mostly static
- Designers work in HTML/CSS

**Best of both worlds**: Use HYUIML for layout, builders for styling:

```html
<!-- layout.html -->
<div class="page-overlay">
    <div class="container" data-hyui-title="Settings">
        <div class="container-contents">
            <button id="save-btn">Save</button>
            <button id="cancel-btn">Cancel</button>
        </div>
    </div>
</div>
```

```java
PageBuilder.pageForPlayer(playerRef)
    .enablePersistentElementEdits(true)
    .loadHtml("Pages/layout.html")
    .editById("save-btn", ButtonBuilder.class, btn ->
        btn.withStyle(MyModStyles.primaryButton()))
    .editById("cancel-btn", ButtonBuilder.class, btn ->
        btn.withStyle(MyModStyles.secondaryButton()))
    .open(store);
```

HYUIML handles layout. Java handles styling and logic.
With persistent edits enabled, rebuilds and refreshes keep these changes.
{% endstep %}

{% step %}
### Version Your Styles

When updating styles, consider backwards compatibility:

```java
public final class ButtonStyles {
    /**
     * Primary button style (v2).
     * Replaces {@link #primaryV1()} with updated colors.
     */
    public static TextButtonStyle primary() {
        return buildPrimary(ColorPalette.PRIMARY_V2);
    }

    /**
     * Legacy primary button style (v1).
     * @deprecated Use {@link #primary()} instead.
     */
    @Deprecated
    public static TextButtonStyle primaryV1() {
        return buildPrimary(ColorPalette.PRIMARY_V1);
    }

    private static TextButtonStyle buildPrimary(String color) {
        // Build with the specified color
    }
}
```

This lets you:
- Update styles without breaking existing UIs
- Gradually migrate to new styles
- Provide upgrade paths for users
{% endstep %}

{% step %}
### Performance Tips

1. **Cache style objects** - Don't rebuild the same style repeatedly
2. **Reuse `HyUIPatchStyle` instances** - Backgrounds are expensive
3. **Avoid deep nesting** - Flat UI hierarchies perform better
4. **Use static styles** - Runtime-generated styles add overhead
5. **Profile your UIs** - If a page is slow, check style construction

Example of wasteful style construction:

```java
// ❌ Bad: Rebuilds style every time
public ButtonBuilder createButton(String text) {
    return ButtonBuilder.textButton()
        .withText(text)
        .withStyle(new TextButtonStyle()
            .withDefault(/* ... */)
            .withHovered(/* ... */)
            // This is slow!
        );
}

// ✅ Good: Reuses cached style
public ButtonBuilder createButton(String text) {
    return ButtonBuilder.textButton()
        .withText(text)
        .withStyle(MyModStyles.primaryButton());
}
```
{% endstep %}

{% step %}
### Common Mistakes

**1. Forgetting the `@2x.png` suffix**

```java
// ❌ Will fail to load
new HyUIPatchStyle()
    .setTexturePath("MyMod/Button.png");

// ✅ Actual file must be named Button@2x.png
// But reference it without the suffix
new HyUIPatchStyle()
    .setTexturePath("MyMod/Button.png");
```

**2. Mixing state objects**

```java
// ❌ Wrong: Using ButtonStyleState for a text button
TextButtonStyle style = new TextButtonStyle()
    .withDefault(new ButtonStyleState()  // Wrong type!
        .withBackground(...));

// ✅ Correct: Use TextButtonStyleState
TextButtonStyle style = new TextButtonStyle()
    .withDefault((TextButtonStyleState) new TextButtonStyleState()
        .withBackground(...)
        .withLabelStyle(...));
```

**3. Forgetting to set all states**

```java
// ⚠️ Incomplete: No hovered/pressed/disabled states
ButtonStyle incomplete = new ButtonStyle()
    .withDefault(new ButtonStyleState()
        .withBackground(...));

// ✅ Complete: All states defined
ButtonStyle complete = new ButtonStyle()
    .withDefault(...)
    .withHovered(...)
    .withPressed(...)
    .withDisabled(...);
```

**4. Not using sounds**

```java
// ⚠️ Silent button - no audio feedback
ButtonStyle silent = new ButtonStyle()
    .withDefault(...)
    .withHovered(...);

// ✅ With sounds - better UX
ButtonStyle withSound = new ButtonStyle()
    .withDefault(...)
    .withHovered(...)
    .withSounds(DefaultStyles.buttonSounds());
```
{% endstep %}
{% endstepper %}

## Summary

1. **Organize by purpose** - Multiple focused classes, not one giant file
2. **Use color palettes** - Define colors once, reference by name
3. **Cache style objects** - Don't rebuild what you can reuse
4. **Don't over-customize** - Use `DefaultStyles` when appropriate
5. **Test with showcases** - Build a page that displays all your styles
6. **Centralize asset paths** - No hardcoded strings scattered everywhere
7. **Document your styles** - Explain when and why to use each style
8. **Choose the right tool** - Builders for logic, HYUIML for layout
9. **Version carefully** - Consider backwards compatibility
10. **Profile and optimize** - Measure before you optimize

Building a good styling system takes time, but it pays dividends. Your UIs will look consistent, your code will be maintainable, and your mod will have a professional polish.

## What's Next?

You now have the tools to:
- Use `DefaultStyles` for instant Hytale-native appearance
- Build custom style systems that scale
- Organize style code for maintainability
- Avoid common pitfalls and performance issues

Go forth and style. Make your UIs beautiful.
