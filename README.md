:warning: This project is archived and the repository is no longer maintained. 

# HTML Video Element: 
**Proposal For Reintroduction of Media (and srcset/sizes) in Source Elements**

The web is often too slow and data intensive for users because it's difficult for developers to correctly deliver an appropriate video size to different devices.  According to the HTTP Archive, [video delivery size is **heavier** on mobile devices (1897.3 KB) than desktop devices (1592.1 KB)](https://httparchive.org/reports/page-weight#bytesVideo). We need better and simpler tools to deliver video responsively.

The following explainer summarizes the problems caused by HTML's lack of support for offering multiple video source resolutions.

## Problem statement

The HTML `video` element allows authors to specify multiple source files for a video based on their file type (eg, mp4 and webm), but it does not offer any means of specifying multiple resolutions, orientations, or aspect ratios of a video based on the needs of a device and a video's surrounding page layout. Due to this limitation, users who visit websites that contain standard HTML video are at risk of downloading files that may be larger or smaller than their device needs. Videos that are larger than a screen's resolution will decrease page performance and data consumption, while videos that are smaller than a screen's resolution will appear low-quality, and videos that are merely the wrong orientation can not make adequate use of a device's available viewport space.

In several browsers, the video element [formerly supported `media` attributes on its `source` elements](https://www.w3.org/TR/2014/REC-html5-20141028/embedded-content-0.html#attr-source-media), which allowed authors to specify a CSS media type or query where the source should be selected by a user agent. Despite being implemented in multiple browsers, the feature was removed from browsers and the HTML specification, without any proposed replacement for the functionality it once provided. One exception is the feature was never removed from Webkit, so it still works in Safari browsers, which is great. 

Due to that removal, there is no current cross-browser way to deliver an appropriately sized video across devices using HTML video alone, leaving authors to resort rely on more complex alternatives, such as using JavaScript to analyze user agent conditions in order to modify a video element's available sources, or other alternatives like server-side detection or third party video embedding. As a complimentary feature, it’s worth noting that there is a [DASH protocol](https://developer.mozilla.org/en-US/docs/Web/Media/DASH_Adaptive_Streaming_for_HTML_5_Video) for streaming video based on speed and network capabilities, but no parallel option to manage size for literal dimension in a layout. 

Reintroducing the ability to offer multiple source resolutions for a video will offer:
1. users a more contextual, more performant, and less bandwidth-costly browsing experience,
2. authors better control over the source sizes they offer users, and more flexibility for art direction
3. site owners savings in hosting costs by saving bytes where possible

## Proposed Solution

HTML `video` `source` elements should offer a set of flexible features for source selection that are consistent with that of the HTML `img` element and [the HTML `picture` element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture); namely, the `media`, `srcset` and `sizes` attributes. 

Primarily of concern, as with `picture`, video sources could be specified using `media` in cases where an author would like a particular source to be used as long as the media conditions match (the "art direcion" use case). As a stretch goal, it would be ideal if `video` also supported the `srcset` and `sizes` attributes for cases where the author would like to offer the user agent various sizes of a video and allow it to choose the most appropriate given the browsing conditions.

## Picture and Img Element Examples for Inspiration

An example of the standard `picture` element using `media`, `srcset`, and `sizes` features (note: code examples credit to [TutsPlus](https://webdesign.tutsplus.com/tutorials/quick-tip-how-to-use-html5-picture-for-responsive-images--cms-21015)):

```html
<picture>
    <source media="(min-width: 1200px)" srcset="image-large.png 1200w">
    <source media="(min-width: 800px)" srcset="image-medium.png 800w">
    <img src="image-small.png" alt="Image description">
</picture>
```

An example of `picture` using a combination of `media` and `srcset`:

```html
<picture>
    <source
        media="(orientation: landscape)"
        srcset="image-small.png 320w,
                image-medium.png 800w,
                image-large.png 1200w"
        sizes="(min-width: 60rem) 80vw,
               (min-width: 40rem) 90vw,
               100vw">
    <source
        srcset="image-small-portrait.png 160w,
                image-medium-portrait.png 400w,
                image-large-portrait.png 600w"
        sizes="(min-width: 60rem) 80vw,
               (min-width: 40rem) 90vw,
               100vw">
    <img src="image-small.png" alt="Image description">
</picture>
```

Additionally, the `img` element offers `srcset` and `sizes` directly, for cases when `picture` and `media` attributes aren't necessary:

```html
<img src="image-small.png"
    srcset="image-small.png 320w, image-medium.png 800w, image-large.png 1200w"
    sizes="(min-width: 60rem) 80vw,
           (min-width: 40rem) 90vw,
           100vw"
    alt="Image description">
```

## Proposed Code Examples using video

Following the `picture` examples above, here's how the `video` element could look:

```html
<video>
    <source media="(min-width: 1200px)" src="video-large.webm 1200w" type="video/webm">
    <source media="(min-width: 1200px)" src="video-large.mp4 1200w" type="video/mp4">
    <source media="(min-width: 800px)" src="video-medium.webm 800w" type="video/webm">
    <source media="(min-width: 800px)" src="video-medium.mp4 800w" type="video/mp4">
    <source src="video-small.webm" type="video/webm">
    <source src="video-small.mp4" type="video/mp4">
</video>
```

### Stretch-Goal: srcset and sizes, like picture supports

A video using a combination of `media` and `srcset` to factor in device orientation for the video.

```html
<video>
  <source
        type="video/webm"
        media="(orientation: landscape)"
        srcset="video-small.webm 320w,
                video-medium.webm 800w,
                video-large.webm 1200w"
        sizes="(min-width: 60rem) 80vw,
               (min-width: 40rem) 90vw,
               100vw">
    <source
        type="video/mp4"
        media="(orientation: landscape)"
        srcset="video-small.mp4 320w,
                video-medium.mp4 800w,
                video-large.mp4 1200w"
        sizes="(min-width: 60rem) 80vw,
               (min-width: 40rem) 90vw,
               100vw">
  
    <source
        type="video/webm"
        srcset="video-portrait-small.webm 320w,
                video-portrait-medium.webm 800w,
                video-portrait-large.webm 1200w"
        sizes="(min-width: 60rem) 80vw,
               (min-width: 40rem) 90vw,
               100vw">
    <source
        type="video/mp4"
        srcset="video-portrait-small.mp4 320w,
                video-portrait-medium.mp4 800w,
                video-portrait-large.mp4 1200w"
        sizes="(min-width: 60rem) 80vw,
               (min-width: 40rem) 90vw,
               100vw">
</video>
```

Like `img`, a `video` element could offer `srcset` and `sizes` directly, for cases when only one file type is available, and `media` attributes aren't necessary. 

```html
<video src="video-small.mp4"
    srcset="video-small.mp4 320w, video-medium.mp4 800w, video-large.mp4 1200w"
    sizes="(min-width: 60rem) 80vw,
           (min-width: 40rem) 90vw,
           100vw"></video>
```

### Existing Alternatives

Multiple patterns exist today that allow us to serve contextual video size and quality to devices, but they are more complicated than an HTML-only solution could provide. Here are some examples:

- Generating HTML video sources with JavaScript, after querying `media` conditions (implementations may use DOM scripting, framework templating, and more):
 ```html
<video id="myvideo"></video>
<script>
    document.getElementById("myvideo").src = 'path/to/video-' + (matchMedia( "(min-width: 400px)" ).matches ? 'lrg' : 'sml') + '.mp4';
</script>
 ```
- The future DASH standard offers a server-side solution for conditioning video streams based on speed and network capabilities. Once supported, it could nicely complement an HTML-driven responsive video approach, but it requires server-based coordination that may not be available to authors and does not allow for fine clientside control over delivering video to particular media breakpoints and variations. https://developer.mozilla.org/en-US/docs/Web/Media/DASH_Adaptive_Streaming_for_HTML_5_Video
- Various popular third-party video embedding services (Youtube, Vimeo, etc) offer a solution to delivering video with contextual quality and size, typically using iframes or client/server scripting. These do not typically give authors HTML control over varying video delivery by clientside media conditions.


### Possible Alternatives
The following suggestions could address some but not all concerns addressed by this proposal. 

- [Yoav Weiss suggested](https://twitter.com/yoavweiss/status/1346910292479918086) that perhaps `picture` could instead gain better support for selecting multiple video sources, assuming they are silent. This of course only covers ambient "background" videos, but would not be a great solution for videos that require controls, audio, vtt tracks, etc.
- Similar to Yoav's suggestion, if CSS gained support for video formats as background-images, that could potentially improve our control for silent ambient videos in layout.


### Challenges, and Differences from `Picture`

Some differences in how and when sources are selected with `video` and with `picture` are likely necessary. 

- One of these that comes to mind is whether or not sources are re-evaluated for matches when UA or viewport conditions change. It seems reasonable to expect that a srcset/sizes driven selection could be selected just once when first parsed, whereas sources that are specified for particular `media` types and queries should probably be re-evaluated throughout a session, so that changes to device orientation, layout, or viewport size are respected.
- Per [Simon Pieters](https://twitter.com/zcorpan/status/1346924394480869379), changing a video source is complicated for reasons beyond download size: 
 > "Loading a new resource is "destructive" (resets currentTime etc)." "How would you change video loading to make media="" useful? Is it ok to destructively load a different resource when you rotate your phone? Or do you want a seamless switch-over à la DASH?"

## Discussion Encouraged!

Please feel free to [add an issue to the tracker](https://github.com/filamentgroup/html-video-responsive/issues) with any questions or comments. We aim to improve this summary over time until it (hopefully) finds a better home.

Also, please feel free to share the article that pairs with this explainer: [http://filamentgroup.com/lab/video-with-sizes/](http://filamentgroup.com/lab/video-with-sizes/)

Thanks!
Scott Jehl
Filament Group, Inc
http://filamentgroup.com
