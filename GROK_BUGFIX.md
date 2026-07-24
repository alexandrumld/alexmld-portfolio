# GROK 4.5 BUG FIX — VISUAL BUGS + TRIPLE VERIFICATION

## GOAL
The user has reported visual bugs after 10 design passes. Your job is to find and fix ALL bugs, then verify the site THREE TIMES before reporting. No new features, no design changes — ONLY bug fixes.

## REPORTED BUGS
1. "The Path" section only displays the line — the steps (FRAMES, CANVAS, CODE, OULU) are not visible
2. The hero name "lags" — likely a performance or timing issue with the char reveal

## KNOWN SUSPECT AREAS (from code review)

### BUG A: Steps may be invisible — multiple potential causes
1. **Steps are `.reveal` elements AND get custom GSAP treatment.** Line 2262: `if (el.classList.contains('step')) return;` — steps are SKIPPED from the generic `.reveal` handler. Then line 2270-2284: steps get their own `gsap.set(pathSteps, { y: 64, opacity: 0, filter: 'blur(6px)' })` + scroll-triggered reveal. BUT: the `.step` elements also have class `reveal` in the HTML (line 1483-1504). The generic `.reveal` handler at line 2260 SKIPS steps (returns early). So steps are ONLY revealed by the custom step handler at line 2270-2284. If that ScrollTrigger doesn't fire (e.g., because the path section is too tall or the start position is wrong), steps stay invisible.

2. **The `.reveal` class on steps may conflict.** Line 2007: `gsap.set('.reveal', { y: 72, opacity: 0 })` — this sets ALL `.reveal` elements (including steps) to opacity:0. Then line 2262 skips steps from the reveal handler. So steps are set to opacity:0 by the initial `gsap.set('.reveal')` but then the step handler at line 2272 does `gsap.set(pathSteps, { y: 64, opacity: 0, filter: 'blur(6px)' })` which also sets opacity:0. The step reveal tween at line 2273 should animate them to opacity:1. BUT: if the ScrollTrigger `start: 'top 86%'` doesn't fire for the first step, ALL steps stay invisible.

3. **The `onComplete` at line 2280 does `gsap.set(pathSteps, { clearProps: 'filter,opacity,transform' })`** — this CLEARS the opacity property. If the tween hasn't finished yet (e.g., user hasn't scrolled to trigger), the clearProps runs and steps become visible. But if the tween NEVER fires (ScrollTrigger misconfigured), steps stay at opacity:0.

4. **Possible fix:** Remove `reveal` class from `.step` elements in HTML (they have custom handling), OR ensure the step reveal ScrollTrigger always fires.

### BUG B: Hero name "lags"
1. **`defer` on scripts + `DOMContentLoaded` boot.** The GSAP/ScrollTrigger/Lenis scripts have `defer` (lines 29-31). The main script at line 1651 is NOT deferred — it's a regular inline script. But it uses `__portfolioBoot()` which is called on `DOMContentLoaded` (line 3012) or immediately if DOM is already loaded (line 3014). With `defer` on the CDN scripts, they execute BEFORE `DOMContentLoaded`. So `gsap` should be defined when `__portfolioBoot` runs. BUT: if there's a race condition where the inline script runs before the deferred scripts complete, `typeof gsap === 'undefined'` would be true and `showAllContent()` would fire, making the name visible without animation — which could look like a "lag" or jump.

2. **Hero name has `gsap.set(allChars, { yPercent: 125, opacity: 0, filter: 'blur(10px)', rotateX: 14 })`** — 17 chars (ALEXANDRU=9, MOLDOVAN=8) with blur(10px) + rotateX. This is GPU-heavy. On slower devices, this could cause visible lag during the intro timeline.

3. **The intro timeline has many sequential steps** — counter (1.65s) + preloader exit (1.15s) + glow (1.75s) + label (0.9s) + name line 1 (1.35s) + name line 2 (1.35s) + name settle (1.2s) + role (0.95s) + location (0.9s) + scroll cue (0.85s) + nav (1.0s). Total: ~10s. That's very long. The name doesn't appear until ~4s after page load (after counter + preloader exit). This could feel like "lagging."

4. **Possible fix:** Speed up the preloader (counter + bar should be faster), reduce blur to 6px, ensure the name appears sooner.

### BUG C: FRIENDBY description shows OLD text on live site
The web_extract showed "Next.js, Supabase, Mapbox" instead of "React Native, Expo, Supabase, MapLibre." This could be:
1. GitHub Pages CDN cache not yet updated (likely — it was just pushed)
2. The text wasn't actually updated (verify in the file)

### BUG D: Potential `defer` script race condition
The inline script at line 1650 is NOT wrapped in a `defer` or `type="module"` — it's a regular inline script. Inline scripts execute immediately during HTML parsing, BEFORE deferred scripts. But `__portfolioBoot` is deferred to `DOMContentLoaded`. So the inline script sets up the boot function, then deferred CDN scripts load, then `DOMContentLoaded` fires and `__portfolioBoot` runs. This should work. BUT: if the CDN scripts fail or are slow, `DOMContentLoaded` might fire before they're ready. The `typeof gsap === 'undefined'` check at line 1739 handles this, but it falls back to `showAllContent()` which would make everything visible without animation.

## YOUR TASK

1. Read the ENTIRE index.html
2. Find ALL visual bugs — not just the reported ones, but any you can find from code analysis
3. Fix ALL bugs
4. Verify the fix THREE TIMES:
   - Check 1: Read the full file and verify all CSS/JS is valid, no truncated code
   - Check 2: Trace every animation path and verify it fires correctly
   - Check 3: Verify all fallback paths (reduced-motion, noscript, GSAP-missing, touch)
5. Report what was broken and what you fixed

## RULES
1. SINGLE FILE
2. ONLY bug fixes — NO new features, NO design changes
3. Keep silver accent, GSAP/ST/Lenis/Inter stack
4. Keep ALL features working
5. Reduced-motion + noscript + touch fallbacks MUST work
6. Production-ready for GitHub Pages
7. TRIPLE VERIFY before reporting

## PROCESS
1. Read index.html fully
2. List all bugs found
3. Fix each bug
4. Verification pass 1: full file read, check CSS/JS validity
5. Verification pass 2: trace all animation triggers
6. Verification pass 3: trace all fallback paths
7. Report all bugs found + fixes + verification results