# Dynamic Images

Dynamic images let you download PNGs at runtime and show them in UI elements without bundling them in game assets. This guide covers:

* Building dynamic images
* Prefetching and caching per player
* Async loading and when to use it
* Hyvatar-specific usage

### Why cache per player?

Dynamic images are often personalized (player avatars, stats cards, inventory previews). A per-player cache lets you:

* Preload images on join or at key moments
* Control cache lifetime with a custom expiry
* Avoid blocking UI open calls while downloads happen

The cache is keyed by `(playerUuid, imageUrl)` and stores the PNG bytes with a custom TTL in seconds.

### Basic dynamic image usage

{% code title="Java" %}
```java
PageBuilder.pageForPlayer(playerRef)
    .loadHtml("Pages/Profile.html")
    .open(store);
```
{% endcode %}

In your HTML (HYUIML), mark an image as dynamic and give it an ID:

{% code title="HTML" %}
```html
<div class="dynamic-image" id="player-head-image"
     data-hyui-image-url="https://example.com/head.png"
     style="anchor-width: 96; anchor-height: 96;"></div>
```
{% endcode %}

**File path support (0.9.0+)**: Dynamic images can also load from file paths relative to the `mods` directory.

{% code title="HTML" %}
```html
<div class="dynamic-image" id="npc-head-image"
     data-hyui-image-url="ModGroup_ModName/avatars/head/Elyra.png"
     style="anchor-width: 96; anchor-height: 96;"></div>
```
{% endcode %}

The example above resolves to `mods/ModGroup_ModName/avatars/head/Elyra.png`.

### Prefetch and cache on player join

Use this when you already know which images a player will likely need.

{% code title="Java" %}
```java
UUID playerUuid = playerRef.getUuid();
String url = "https://example.com/cards/" + playerUuid + ".png";

// Download and cache for 5 minutes.
PngDownloadUtils.prefetchPngForPlayer(playerUuid, url, 300);
```
{% endcode %}

If you already have the image bytes:

{% code title="Java" %}
```java
PngDownloadUtils.cachePngForPlayer(playerUuid, url, bytes, 300);
```
{% endcode %}

### Async image loading (optional)

If you want the UI to open immediately and fill images after download, enable async loading:

{% code title="Java" %}
```java
PageBuilder.pageForPlayer(playerRef)
    .loadHtml("Pages/Profile.html")
    .enableAsyncImageLoading(true)
    .open(store);
```
{% endcode %}

{% hint style="info" %}
* Async loading will download images in the background.
* Once the image arrives, the page refreshes with the new texture.
* If you already prefetch/cache, **async loading is unnecessary.**
{% endhint %}

### Hyvatar usage with caching

Hyvatar renders are just URLs, so you can prefetch and cache them too.

{% code title="Java" %}
```java
UUID playerUuid = playerRef.getUuid();
String url = HyvatarUtils.buildRenderUrl(
    playerRef.getUsername(),
    HyvatarUtils.RenderType.HEAD,
    128,
    0,
    null
);

PngDownloadUtils.prefetchPngForPlayer(playerUuid, url, 300);
```
{% endcode %}

If you prefer to download via the Hyvatar helper and still benefit from the cache:

{% code title="Java" %}
```java
HyvatarUtils.downloadRenderPng(
    playerRef.getUsername(),
    HyvatarUtils.RenderType.FULL,
    256,
    45,
    null,
    playerRef.getUuid()
);
```
{% endcode %}

### Cache management

Remove all cached images for a player (for example, on disconnect):

{% code title="Java" %}
```java
PngDownloadUtils.clearCachedPngForPlayer(playerRef.getUuid());
```
{% endcode %}
