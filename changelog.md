# Changelog

<details>

<summary><strong>0.8.5 - 14 Feb 2026</strong></summary>

* Fix: Dynamic images being broken upon page load
* Feature: Caching images per player with the new overloads for PngDownloadUtils, this will send the asset to the client which results in a zero load time for the page.
* Fix: Spaces between tooltips/textspans were not rendered, adapted to render these and any other whitespace.
* Fix: If a bar is set on a progressbar, do not set defaults for bar and effect texturepaths.

NOTE: This release is designed for the "Release" stream of Hytale, NOT pre-release. Pre-release will NOT work with this.

</details>

<details>

<summary><strong>0.8.4 - 10 Feb 2026</strong></summary>

* Feature: Buttons can now have background images on style states.

</details>

<details>

<summary><strong>0.8.3 - 9 Feb 2026</strong></summary>

* Fix: Label Builder now supports padding again...

</details>


<details>

<summary><strong>0.8.2 - 8 Feb 2026</strong></summary>

* Fix: Events should work better for Mouse Released/Dismissing/Validating.
</details>

<details>

<summary><strong>0.8.1 - 8 Feb 2026</strong></summary>

* Fix: Item Grid Section Id wrong data type, now Integer.
</details>

<details>

<summary><strong>0.8.0 - 7 Feb 2026</strong></summary>

* Docs: Add native tab navigation and dynamic pane documentation and examples.
* Feature: Document native tab navigation and dynamic pane builders with full usage examples.
* Feature: Expanded HYUIML element coverage for action buttons, sliders, color pickers, dropdowns, item grids/slots, block selectors, and native timer labels.
* Feature: Parity with released official documentation with the addition of several new element types.
* Feature: Item grids now have inventory section id support and source differentiation.
* Feature: Add support for tooltip textspans (multiple).
* Feature: Add support for label textspans, check the HYUIML docs or use builders.

</details>

<details>

<summary><strong>0.7.0 - 5 Feb 2026</strong></summary>

* Rework styles and how they are applied. Please see the documentation on HYUIML (major update).
* Add Hywind support, thanks to @kelpycode. You can now add the style through the new `.fromHtml(..., UIType.HYWIND)` overload on Page/Hud builders.
* Add page dismissal/close listener.
* Fix: anchor null on height for NumberFieldBuilder.

</details>

<details>

<summary><strong>0.6.1 - 3 Feb 2026</strong></summary>

* Add getter for template processor on the UI context.
* Add "margin" aliases for anchor-\*.
* Remove clip children.

</details>

<details>

<summary><strong>0.6.0 - 1 Feb 2026</strong></summary>

* Add per-player image caching - see the [docs](home/dynamic-images.md) for more info.
* Add async image loading (not the best).
* Add page refreshing at a set rate with full page and UI context access, similar to HUD refreshes.
* Add the ability to register template components from resources - `registerComponentFromFile(name, resourcePath)`

</details>

<details>

<summary><strong>0.5.11 - 31 Jan 2026</strong></summary>

* Fixed layout mode being applied to buttons and not group surrounding button.
* Refactor: use supplier instead of direct value - thank you @Farrael!
* Feature: Allow negative anchors - thank you @WolverinDEV!
* Changed from ARR to LGPLv3 license.

</details>

<details>

<summary><strong>0.5.10 - 30 Jan 2026</strong></summary>

* Fix field access checks.

</details>

<details>

<summary><strong>0.5.9 - 30 Jan 2026</strong></summary>

* Fix tab navigation builder not supporting template runtime interactions.
* Add scrollbar style support for divs, same way as the textarea.
* Add sub-components to Template Processor.
* Add "hot-reload" of HYUIML (html) from project resources. This assumes one of the candidate paths exists (`src/main/resources`, `build/resources/main`, `../src/main/resources`, or `../build/resources/main`).

</details>

<details>

<summary><strong>0.5.8 - 29 Jan 2026</strong></summary>

* Rework internally how logging occurs, it is now enabled in releases, at Finest level only.
* Add helper methods to ItemGridBuilder to update/remove/get all slots, the tutorial has also been updated to showcase this.
* Add support for replacing button builders for tab navigation - this complements the existing selected/unselected styling. Use `TabNavigationBuilder.addTab(id, label, contentId, buttonBuilder)` , this means every tab's button can have its own builder. By default, tabs are built with secondary/primary button builders. You can of course update/remove tabs as well.
* Add support for `CustomButtonBuilder` for tab navigation.
* Add [tutorial](tutorials/tutorial-tab-navigation/) for Tab Navigation.

</details>

<details>

<summary><strong>0.5.7 - 29 Jan 2026</strong></summary>

* Add events for ItemGrid. See tutorial: [here](tutorial-working-with-item-grids.md).
* Add Multi-Line Text Field (textarea).
* Ensure store in HUDs is always called with a 'fresh' store. This means there is no need to pass a store to a HUD on creation, or opening. These are marked as deprecated. Thanks to Willem for this!

</details>

<details>

<summary><strong>0.5.6 - 28 Jan 2026</strong></summary>

* Fix button text not being set.
* Fix progressbar texture paths not working as expected.

</details>

<details>

<summary><strong>0.5.5 - 27 Jan 2026</strong></summary>

* Update validation rules according to schema.
* Add helper method to cast a value to a particular type (UIContext.getValueAs).
* Optimize jar size.
* Add custom button support.

</details>

<details>

<summary><strong>0.5.4 - 26 Jan 2026</strong></summary>

* Add template runtime evaluation.
* Add helper method to get a builder as a casted type.
* CHANGE: Do not force clearing a page when reloading an image, allow caller to decide: `reloadImage(String dynamicImageElementId, boolean shouldClearPage)`

</details>

<details>

<summary><strong>0.5.3 - 25 Jan 2026</strong></summary>

* Add decorated container.
* Add a per-player image limit of 10.
* Add Hyvatar.io component.
* Add small secondary and tertiary buttons.
* Fix text alignment issues.
* Remove support for font size on buttons (client crash).

</details>

<details>

<summary><strong>0.5.2 - 24 Jan 2026</strong></summary>

* Add access to custom styles from HYUIML.
* Add dynamic images from remote sources.
* Add circular progress bar support.

</details>

<details>

<summary><strong>0.5.1 - 24 Jan 2026</strong></summary>

* Add default events to all elements that handle value changes, this allows us to always capture data updates.
* Add numberfield support for MinValue, MaxValue, Step, MaxDecimalPlaces.
* Fix bug with padding not being applied.
* Add conditionals, logical operators and string comparison to template.
* Add loops to template processor.

</details>

<details>

<summary><strong>0.5.0 - 23 Jan 2026</strong></summary>

* Added `ItemGrid`.
* Added `ItemSlot`.
* Added `TabNavigation`.
* Added timer label builder (multiple formats).
* Added `TemplateProcessor` for HYUIML, including reusable components with variables/default values.
* BREAKING CHANGE: `flex-weight` now applies to the outer wrapping group (all elements except `Group`/`Label` are wrapped). This can change layout behavior compared to earlier versions.

</details>
