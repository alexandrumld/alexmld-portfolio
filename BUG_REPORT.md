# BUG REPORT — alexmld-portfolio index.html
# Diagnostic data from Playwright headless Chromium, 1440×900 viewport

## CONFIRMED BUG #1: Gallery horizontal scroll doesn't reach the end

### Measured data:
- Track scrollWidth: 2715px (5 panels × 473px + 4 gaps × 40px + padding 192px)
- Viewport width: 1440px
- Required X translation to show last panel fully: -(2715 - 1440) = -1275px
- At scrollY = 4500px (mid-gallery): track transform = -849px (should be ~-737px based on linear progress)
- At scrollY = end of gallery: track right edge = 1865px, left edge = -849px
  - Track right edge (1865) > viewport width (1440), so last panel is CUT OFF by ~425px
  - The track only reaches -849px instead of -1275px

### Root cause analysis:
The gallery wrapper is `height: 500vh` (4500px). The ScrollTrigger animates from `start: 'top top'` to `end: 'bottom bottom'`. The scroll range is:
- Gallery top in document: ~2420px (after hero-spacer 900px + path ~800px + about ~720px)
- Gallery bottom: ~6920px
- ScrollTrigger start: scrollY = 2420 (gallery top hits viewport top)
- ScrollTrigger end: scrollY = 6020 (gallery bottom hits viewport bottom)
- Scroll range: 3600px

But the track only needs to move 1275px. At 57.8% through the range, the track is at -849px. At 100%, it should reach -1275px. BUT the measurement at scrollY=4500 (57.8%) shows -849px which is MORE than expected (-737px). This suggests `scrub: 1.2` smoothing is lagging OR there's a calculation issue.

The REAL problem: `getScrollDistance` returns `-(track.scrollWidth - window.innerWidth)` but this doesn't account for the padding on the track. The track has `padding: 0 6rem` (0 96px). `scrollWidth` includes the padding, so the calculation should be correct. BUT — `window.innerWidth` includes scrollbar width. Since scrollbar is hidden (`::-webkit-scrollbar { display: none }`), innerWidth = clientWidth = 1440px. So that's fine.

POSSIBLE ROOT CAUSE: The `gallery-wrapper` height of `500vh` may be too tall. The track only needs 1275px of X movement, but has 3600px of scroll to do it in. This means the track moves at 0.354px per pixel of scroll. At the end, it SHOULD reach -1275px. But maybe the ScrollTrigger end calculation is off because of the hero being `position: fixed` affecting document flow.

CHECK: The hero is `position: fixed` and there's a `hero-spacer` div with `height: 100dvh`. This spacer pushes content down. But does ScrollTrigger account for this correctly? The hero-spacer is 900px, then path ~800px, then about ~720px. The gallery wrapper starts at ~2420px. ScrollTrigger uses document scroll position, so this should be fine.

ALTERNATIVE ROOT CAUSE: Maybe the `invalidateOnRefresh: true` + function-based `x` value is recalculating at the wrong time. Or maybe Lenis smooth scroll is interfering with ScrollTrigger's position calculations.

## OTHER POTENTIAL BUGS TO INVESTIGATE:

### BUG #2: Hero `position: fixed` + gallery `z-index: 3` interaction
The hero is `position: fixed; z-index: 2` and the gallery wrapper is `z-index: 3`. The hero fades out via ScrollTrigger (`autoAlpha: 0`) as you scroll. But if the hero is fixed and the gallery is z-index 3 (above hero), the gallery content should appear ON TOP of the hero. But the hero is fixed — when you scroll, the gallery scrolls UP over the fixed hero. This is the intended effect (hero → black → gallery reveals). But check: does the hero actually fade to black, or does it just become transparent? If `autoAlpha: 0` makes it transparent, the body background (#0a0a0a) shows through, which is correct. But if the hero's background is `radial-gradient(...) + var(--bg)`, the gradient might show briefly during the transition.

### BUG #3: Panel entrance animation conflict
Panels have TWO animations:
1. `gsap.set(panel, { clipPath: 'inset(100% 0% 0% 0%)', opacity: 0, y: 60 })` + reveal on scroll
2. The horizontal scroll `gsap.to(track, { x: getScrollDistance })`

The clip-path entrance reveals panels at `start: 'top 60%'` of gallery wrapper. But the horizontal scroll starts at `start: 'top top'`. If `top 60%` fires before `top top`, the panels reveal before the horizontal scroll starts, which is correct. But the `clipPath: 'inset(100% 0% 0% 0%)'` sets the clip to hide the panel from the top. This might interact poorly with the horizontal transform.

### BUG #4: `isMobile()` function vs matchMedia
The code uses `var isMobile = function () { return window.innerWidth <= 768; }` but also uses `gsap.matchMedia().add('(min-width: 769px)', ...)`. These should be consistent. At 768px exactly, `isMobile()` returns true but matchMedia 769px doesn't match. At 769px, `isMobile()` returns false but matchMedia 769px matches. This 1px gap could cause issues.

### BUG #5: ScrollTrigger refresh on image load
Images use `loading="lazy"`. When images load lazily, they might change the track's scrollWidth (if the panel sizes change). But panels have fixed `aspect-ratio: 3/4` and `height: 70vh`, so their layout size shouldn't change. Still, ScrollTrigger.refresh() should be called after all images load. Currently it's only called on resize (debounced 250ms). Add a `window.addEventListener('load', ...)` or image load handler.

### BUG #6: Contact section z-index and visibility
Contact is `z-index: 3` same as gallery. After the gallery (500vh), the contact section should appear. But the gallery wrapper is `z-index: 3` and contact is also `z-index: 3`. If they overlap (they shouldn't since they're sequential), the later one in DOM order wins. Check if contact is actually visible after scrolling past the gallery.

### BUG #7: `hero-spacer` pointer events
`.hero-spacer { height: 100dvh; pointer-events: none }` — this prevents clicks in the hero spacer area. But the hero itself is `position: fixed` and should receive pointer events. When the hero fades out (`pointerEvents: 'none'` at 0.7 progress), the spacer is still there with `pointer-events: none`. This means there's a 100dvh dead zone where nothing is clickable. Check if this affects the path/timeline section (which comes after the spacer).

### BUG #8: Timeline line animation on mobile
On mobile, `timeline-line` changes to vertical (`top: 0; bottom: 0; left: 6px; width: 1px; height: auto`). The `timeline-line-fill` changes to `height: 0% → 100%`. But `isMobile()` returns `window.innerWidth <= 768` while the matchMedia query is `max-width: 768px`. The `fillProps` is set once based on `isMobile()` at script execution time. If the user rotates from portrait to landscape, `isMobile()` won't update. Use matchMedia instead.

### BUG #9: Page transition wipe for `_blank` links
`navigateWithWipe` uses `window.open(url, '_blank', 'noopener')` which opens a new tab. The wipe animation plays, then the new tab opens. But the wipe stays at `scaleY: 1` until `onComplete`, then resets. If the new tab opens before the wipe completes, the user sees the wipe on the original tab. This is fine, but the reset logic (`gsap.set(transitionOverlay, { scaleY: 0, transformOrigin: 'top' })` then `gsap.set(transitionOverlay, { transformOrigin: 'bottom' })`) might cause a flash.

### BUG #10: ScrollTrigger.create for steps — `onLeaveBack` only for `!isTouch`
```js
onLeaveBack: function () { if (!isTouch) step.classList.remove('active'); }
```
On touch devices, steps never deactivate when scrolling back. This means once a step is activated, it stays active forever on mobile. Is this intentional? The auto-cycle (3s interval) handles mobile display, but the `active` class from scroll might conflict.

### BUG #11: Gallery heading fade-out on desktop
```js
if (!isMobile()) {
  gsap.to(galleryHeading, {
    opacity: 0, y: -10, ease: 'none',
    scrollTrigger: {
      trigger: galleryWrap,
      start: 'top top',
      end: '15% top',
      scrub: 0.4
    }
  });
}
```
This fades the heading out between 0% and 15% of the gallery. But the heading reveal happens between `top 80%` and `top 30%`. There might be a gap where the heading is visible, then fades, then the gallery scrolls. Check the timing.

### BUG #12: Lenis + ScrollTrigger integration
Lenis is initialized, and `lenis.on('scroll', ScrollTrigger.update)` is called. But Lenis uses `gsap.ticker.add(function (time) { lenis.raf(time * 1000); })`. This is correct. But if Lenis fails to load (CDN issue), the fallback should still work. The code checks `typeof Lenis !== 'undefined'` before creating the instance. If Lenis is undefined, `lenis` stays null, and `ScrollTrigger` uses native scroll. But the nav click handler checks `if (lenis) lenis.scrollTo(t)` else `t.scrollIntoView()`. This is fine.

### BUG #13: `filter: blur()` in GSAP — performance
The hero char reveal uses `filter: 'blur(12px)' → 'blur(0px)'`. CSS filter blur is GPU-intensive. With 16 characters (ALEXANDRU = 9 + MOLDOVAN = 8), that's 16 simultaneous blur animations. On low-end devices, this could cause jank. Consider reducing blur to 6px or using a different reveal effect.

## TASK FOR GROK 4.5:
1. Deeply analyze the entire index.html for ALL bugs — not just the ones listed above
2. The MAIN confirmed bug is: gallery horizontal scroll doesn't show the last panel completely
3. Fix ALL bugs found
4. Pay special attention to:
   - ScrollTrigger start/end calculations for the horizontal gallery
   - The `getScrollDistance` function and whether it correctly calculates the needed translation
   - Whether the gallery wrapper height (500vh) provides the right amount of scroll distance
   - Image lazy-loading affecting ScrollTrigger calculations
   - Any z-index/stacking context issues
   - Mobile responsiveness edge cases
   - Performance optimizations
5. After fixing, verify the file is valid and complete
6. Keep ALL existing features working — don't break anything
7. The fix should make the gallery scroll all the way to show the last panel (NOWA SILUETA) completely

## FORMULA FOR GALLERY HEIGHT (if recalculating):
The ideal gallery wrapper height should be:
```
galleryHeight = (trackScrollWidth - viewportWidth) + viewportHeight
```
This ensures: scrollDistance = trackScrollWidth - viewportWidth (the X movement needed), plus one viewport of scroll for the sticky track to stay pinned. Currently 500vh might be too much or too little depending on track width.