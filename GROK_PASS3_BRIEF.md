# GROK 4.5 PASS 3 — MOBILE-FIRST DEEP PASS + ACCESSIBILITY + PERFORMANCE

## GOAL
This is Pass 3 of 4. Pass 1 fixed typography/atmosphere/mobile type scale. Pass 2 refined motion choreography. This pass focuses EXCLUSIVELY on: mobile experience quality, accessibility (WCAG 2.1 AA), and performance (Core Web Vitals). Make the site feel native on mobile, accessible to all users, and fast enough to score 95+ on Lighthouse.

## WHAT EXISTS (current state after Passes 1-2)
- 1637+ line single-file portfolio at /root/projects/alexmld-portfolio/index.html
- GSAP 3.12.5 + ScrollTrigger + Lenis 1.1.14 + Inter font (200-900)
- Colors: --bg:#070707, --accent:#b8b8b8 (silver), --text:#f0efe9
- Mobile: stacked gallery, no cursor/grain, touch auto-cycle timeline, type scale with higher vw on mobile
- Reduced motion: CSS overrides + early JS return + showAllContent()
- Noscript: fallback styles for all animated elements
- Touch detection: ontouchstart + maxTouchPoints
- Images: 4 JPEGs at assets/projects/ (gentsclub.jpg, delirat.jpg, whumple.jpg, nowa-silueta.jpg), loading="lazy", width/height attributes

## WHAT TO DEEP-THINK AND FIX

### 1. MOBILE EXPERIENCE (mobile-first, not squeezed desktop)
The #1 killer of Awwwards sites on mobile is desktop-first design squeezed down. Check:

- **Type proportions**: Mobile vw multipliers should be HIGHER than desktop. Currently:
  - Hero name: desktop 15.5vw, mobile 16.5vw ✓ (but is 16.5vw enough on a 320px screen? That's ~52px — dramatic enough?)
  - About text: desktop 3.4vw, mobile 6.8vw ✓ (but 6.8vw on 320px = ~21px — is this statement-scale?)
  - Contact email: desktop 8.5vw, mobile 10.5vw ✓ (10.5vw on 320px = ~33px — dramatic enough for a closing CTA?)
  - Panel titles: desktop 3vw, mobile 7.5vw ✓
  - Consider: should there be a 320px breakpoint for even smaller screens?

- **Touch targets**: All interactive elements ≥44px. Currently nav links have min-height:44px, contact links have min-height:44px. But:
  - Are the timeline step dots ≥44px touch target? (they're 14px visual, but the .step container should be large enough)
  - Are the project panels fully tappable on mobile? (they should be — the whole panel is the target)
  - Is the scroll cue tappable? (should it be? On mobile, scrolling is obvious)

- **Spacing on mobile**: Section padding should be smaller on mobile but still feel spacious
  - --section-y: clamp(8.5rem,18vh,15rem) — on mobile this might be too much? Or just right?
  - Gallery gap: 2rem on mobile — enough between cards?
  - Nav padding: 1rem — enough for thumb navigation?

- **Mobile-specific interactions**:
  - Should mobile have a different preloader? (simpler, faster?)
  - Should the timeline auto-cycle be more prominent on mobile?
  - Should mobile gallery cards have a different hover/tap state? (currently no hover on mobile — just always-visible info)
  - Should there be a subtle haptic-like visual feedback on tap? (scale down briefly)

- **iOS Safari edge cases**:
  - 100dvh used for hero ✓ — but does it work correctly with the URL bar show/hide?
  - env(safe-area-inset-top) for notched phones — is this handled? (currently not)
  - Does the fixed nav conflict with the iOS Safari URL bar?
  - Does the horizontal gallery pin work on iOS? (ScrollTrigger pin can be flaky)

- **Mobile performance**:
  - Are there too many ScrollTrigger instances on mobile? (each .reveal, each .step, each panel)
  - Should some ScrollTriggers be disabled on mobile?
  - Is Lenis smooth scroll beneficial or harmful on mobile? (native scroll might be better)

### 2. ACCESSIBILITY (WCAG 2.1 AA)

- **Color contrast**:
  - --text:#f0efe9 on --bg:#070707 — ratio? (should be 15:1+ — excellent)
  - --muted:rgba(240,239,233,.48) on --bg — ratio? (should be ≥4.5:1 for body text)
  - --muted-dim:rgba(240,239,233,.22) on --bg — ratio? (this is for decorative text, but verify)
  - --accent:#b8b8b8 on --bg — ratio? (should be ≥3:1 for large text, ≥4.5:1 for body)
  - Project panel desc: rgba(240,239,233,.62) on rgba(7,7,7,.97) — ratio?

- **Keyboard navigation**:
  - All interactive elements reachable via Tab? (nav links ✓, project panels with data-url have tabindex=0 ✓)
  - FRIENDBY and WHUMPLE panels don't have data-url — are they focusable? Should they be?
  - Focus order: does it follow visual order? (nav → hero → path → about → gallery → contact)
  - Focus visible: outline:2px solid var(--accent) with 6px offset — is this visible enough on all backgrounds?
  - Skip to content link? (Awwwards sites don't always have this, but WCAG requires it)

- **Screen reader support**:
  - aria-label on sections ✓
  - aria-label on project panels ✓
  - The hero name is split into chars — does the aria-label still read "ALEXANDRU MOLDOVAN"? (splitIntoChars sets aria-label ✓)
  - The role text changes dynamically — should it have an aria-live region?
  - The timeline steps — are they labeled correctly for screen readers?
  - The scroll progress bar — aria-hidden ✓
  - The custom cursor — aria-hidden ✓

- **Reduced motion**:
  - CSS media query overrides ✓
  - JS early return ✓
  - But: does the preloader still show in reduced motion? (it's display:none in CSS ✓)
  - Does the gallery still work in reduced motion? (should be stacked, no pin)
  - Are there any animations that slip through the reduced-motion guard?

### 3. PERFORMANCE (Core Web Vitals)

- **LCP (Largest Contentful Paint)**:
  - The hero name is the LCP element — it's text, so it should be fast
  - But: it starts at opacity:0 and animates in — does this delay LCP?
  - Should the hero text be visible immediately and animate in without hiding? (progressive enhancement)
  - The preloader covers the screen for ~3s — does this block LCP?

- **CLS (Cumulative Layout Shift)**:
  - Images have width/height attributes ✓ — good
  - But: images are lazy-loaded — do they cause layout shift when they load?
  - The gallery panels have aspect-ratio:3/4 — should prevent CLS ✓
  - Does the hero going from position:fixed to autoAlpha:0 cause any shift?

- **FID/INP (Interaction to Next Paint)**:
  - Are there any long tasks blocking interaction?
  - The GSAP timeline setup — is it deferred? (it's in the script at the bottom ✓)
  - Lenis RAF loop — is it efficient? (gsap.ticker.add ✓)
  - Should there be a requestIdleCallback for non-critical setup?

- **Image optimization**:
  - Images are JPEGs with loading="lazy" ✓
  - But: are they optimized? (should be <200KB each for a portfolio)
  - Should there be WebP versions? (GitHub Pages doesn't do automatic conversion)
  - Should images have fetchpriority="high" for the first panel?
  - Should there be a placeholder/skeleton for images while loading?

- **Font loading**:
  - Inter is loaded from Google Fonts with display=swap ✓
  - But: 8 weights (200-900) are loaded — is this too many? (only 300, 500, 800 are used)
  - Should we reduce to only the weights actually used?
  - Should there be a font-display:optional for repeat visits?

- **JavaScript budget**:
  - GSAP (32KB gz) + ScrollTrigger (12KB gz) + Lenis (8KB gz) = ~52KB gzipped
  - Is this acceptable? (Awwwards sites often have 100KB+ JS)
  - Should any JS be deferred? (currently all in one script block at the bottom)

## RULES
1. SINGLE FILE: everything stays in index.html
2. Keep ALL features working — don't break anything
3. Keep the silver accent #b8b8b8
4. Only use: GSAP, ScrollTrigger, Lenis, Google Fonts Inter
5. Full reduced-motion + noscript + touch fallbacks MUST remain functional
6. Don't change the HTML structure or copy
7. Don't change colors or fonts — only mobile/a11y/perf improvements
8. Production-ready, deployable to GitHub Pages
9. Verify font weights — remove unused weights from the Google Fonts URL if possible

## PROCESS
1. Read the entire index.html
2. Audit mobile experience, accessibility, and performance against the criteria above
3. Fix ALL issues found
4. Verify the file is valid and complete
5. Verify all features still work
6. Report what you changed and why