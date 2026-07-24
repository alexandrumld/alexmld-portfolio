# GROK 4.5 DESIGN BRIEF — ALEXMLD.COM AWWWARDS TIER

## GOAL
Make this portfolio Awwwards Site of the Day quality. Not "nice" — godly. The site is a designer/developer's personal portfolio. The work is the art; the page is the gallery wall.

## WHAT EXISTS
- `/root/projects/alexmld-portfolio/index.html` — single file, 1607 lines. Dark portfolio.
- Already has: preloader with shimmer bar, hero with char-split name + cycling roles, horizontal scroll gallery (GSAP ScrollTrigger pin), 3D panel tilt, custom cursor (ring+dot+label), magnetic links, film grain, vignette, scroll progress bar, timeline with scroll-fill, about section, contact with stagger links.
- Stack: GSAP 3.12.5, ScrollTrigger 3.12.5, Lenis 1.1.14, Google Fonts Inter (200-900)
- Colors: --bg:#080808, --accent:#b8b8b8 (silver/platinum), --text:#f0efe9
- Real project images in assets/projects/: gentsclub.jpg, delirat.jpg, whumple.jpg, nowa-silueta.jpg
- Mobile: stacked vertical gallery, no cursor, no grain, touch auto-cycle for timeline

## DESIGN DNA — Guglieri / Opal Tadpole editorial lineage (Pole B)
Reference sites (all Awwards winners):
- guglieri.com — pure black, gold accent, Inter, one-word-per-line display type, full-bleed case studies. SOTY 2024 nominee.
- cydstumpel.nl — dark, fluid type, scroll-driven sections, minimal palette, tactile micro-interactions. SOTD.
- romanjeanelie.com — dark, creative developer, hover-the-steps timeline, full-bleed project images. SOTD.
- hsmkrt1996.com — dark, experimental type, scroll storytelling. SOTD.
- imarketina.com — portfolio with awards grid, project thumbnails, personal photo. Multiple SOTDs.

## THE AUDIT DIMENSIONS — score each 1-10 and fix everything below 8

### 1. TYPOGRAPHY HIERARCHY
- Is the type scale dramatic enough? Hero name is clamp(3.5rem,14.5vw,11rem) — is this dramatic enough or should it push further?
- Are labels/micro-text small enough? Currently .625rem (10px) with .32em tracking — good but verify
- Is there enough contrast between display/body/label sizes?
- Line-height: .84 for hero name (good), 1.65 for body — verify per-size
- About text: clamp(1.25rem,3vw,2.15rem) — is this big enough for a statement section?
- Contact email: clamp(1.85rem,7.5vw,5.5rem) — dramatic enough for a closing CTA?
- Section labels: .625rem — should they be bigger or have more presence?

### 2. WHITESPACE & BREATHING ROOM
- Sections use --section-y:clamp(7rem,14vh,12rem) — is this enough?
- Is there enough space between sections? Does each section feel like a museum gallery?
- Path section: padding 5.5rem top on mobile, 7rem+ on desktop — enough?
- About: min-height:90vh — good but check if it feels cramped
- Gallery: panels are 78vh tall with clamp(1.75rem,3vw,3rem) gap — premium enough?

### 3. ATMOSPHERE & DEPTH
- Current: vignette (radial gradient), film grain (SVG turbulence, .045 opacity), hero glow (breathing animation)
- Is the dark rich enough? Does it feel like a space or just a color?
- Should there be more layering? Subtle gradients between sections?
- The hero glow breathes — is this too much or just right?
- Are section backgrounds (--bg:#080808, --bg-alt:#0c0c0c) distinct enough?

### 4. SECTION TRANSITIONS
- How do sections flow? Path has a top border + gradient. About has top/bottom hairlines.
- Are transitions cinematic or abrupt?
- Should there be scroll-driven clip-path reveals between sections?
- The hero fades to black as you scroll (autoAlpha:0) — does this transition feel smooth into the path section?

### 5. HERO IMPACT
- Does the hero stop you in your tracks?
- Char-split reveal with blur(8px)→0, yPercent 110→0, stagger .028, expo.out, 1.45s — is this dramatic enough?
- The "DESIGNER · DEVELOPER · BUILDER" label with flanking lines — good but verify presence
- Cycling roles (Designer→Developer→Builder→Creator every 2.8s) — smooth? Dramatic?
- Scroll cue at bottom — is it elegant?
- Location "Târgu Mureș → Oulu, Finland" — is this placement right?

### 6. PROJECT PANELS (GALLERY)
- Horizontal scroll with pin — does the last panel reach full visibility? (Previous bug: it didn't)
- Panels: aspect-ratio 3/4, height min(78vh,820px), grayscale→color on hover, 3D tilt
- Info overlay slides up on hover — title, meta, desc with staggered transitions
- Panel numbers "01 / 05" in silver — prominent enough?
- Is the hover state dramatic enough? Does it feel premium?
- Parallax on images (yPercent:9) — subtle or noticeable?
- Gallery heading "SELECTED WORK" with animated line — does it have enough presence?
- Mobile: stacked vertical, always-visible info — does mobile feel designed or squeezed?

### 7. MICRO-INTERACTIONS
- Custom cursor: ring (40px, mix-blend-difference), dot (5px), velocity tilt (6deg max), link hover (64px), panel hover (88px filled with "VIEW" label)
- Magnetic links: 35% pull toward cursor, elastic spring back
- Nav links: underline scaleX animation, color transition
- Contact email: underline animation, color shift to accent on hover
- Are there enough micro-interactions? Is every interactive element tactile?
- Focus states: 1.5px solid accent outline — good but verify visibility

### 8. DETAIL POLISH
- Scroll progress bar: 1.5px, gradient, mix-blend-difference — elegant?
- Preloader: name + shimmer bar, slides up after 1.8s — premium enough?
- Selection color: accent bg, bg text — good
- Scrollbar hidden — good for cinematic feel
- Page transition wipe on panel click (scaleY 0→1→0) — smooth?
- Are there any missing details? Loading states? Empty states? Error states?

### 9. MOBILE EXPERIENCE
- Type proportions: hero name clamp(2.85rem,13.5vw,4.75rem) — is this RELATIVELY BIGGER than desktop? (Desktop is 14.5vw, mobile is 13.5vw — mobile should be BIGGER vw multiplier per the mobile-first rule)
- About text: clamp(1.2rem,5.2vw,1.55rem) — is 5.2vw enough on a 375px screen? (That's ~19.5px — readable but not dramatic)
- Contact email: clamp(1.6rem,8.5vw,2.4rem) — 8.5vw on 375px = ~31px — is this big enough for a closing statement?
- Timeline: vertical on mobile with auto-cycle — does it feel designed?
- Gallery: stacked, always-visible info — good but does it feel premium?
- Touch targets: are they all ≥44px?
- Nav: smaller font, blur background — readable? Touch-friendly?
- Does mobile feel like a DIFFERENT design or a squeezed desktop?

### 10. COLOR DISCIPLINE
- Silver accent #b8b8b8 — is it working? It's colder/more technical than gold
- --bg:#080808, --bg-alt:#0c0c0c, --bg-elevated:#111 — is the distinction enough?
- --text:#f0efe9 (warm white) vs --accent:#b8b8b8 (silver) — is the contrast right?
- --muted:rgba(240,239,233,.42), --muted-dim:rgba(240,239,233,.18) — are these levels right?
- Borders: rgba(240,239,233,.1) and .16 — subtle enough? Too subtle?
- Is the overall palette disciplined? Only silver + warm white + black variations?

## KNOWN ISSUES TO FIX
1. FRIENDBY description already updated to "React Native, Expo, Supabase, MapLibre. Blind Roll Calls in Circles — plans without planning."
2. X/Twitter link inconsistency: hero doesn't link to X, contact links to x.com/alexandrumld — verify this is correct
3. Instagram links to delirat.events (brand account) not personal — verify if this is intentional
4. No favicon — add an inline SVG favicon
5. "Târgu Mureș" — the ă character renders correctly but verify in the live extract it showed "Trgu" (missing special char) — check encoding

## RULES
1. SINGLE FILE: everything stays in index.html (CSS in `<style>`, JS in `<script>`)
2. Keep ALL features working: cursor, grain, gallery, preloader, timeline, magnetic links, 3D tilt, page transition wipe, scroll progress
3. Don't break the scroll mechanics (pin, scrub, Lenis integration)
4. Keep the existing copy/narrative — don't rewrite the story
5. Keep the silver accent #b8b8b8 — don't switch to gold
6. Only use: GSAP, ScrollTrigger, Lenis, Google Fonts Inter (weights 200-900)
7. Full reduced-motion + noscript + touch fallbacks MUST remain functional
8. Mobile-first responsive — type must be RELATIVELY BIGGER on mobile, not smaller
9. Images at assets/projects/*.jpg (gentsclub.jpg, delirat.jpg, whumple.jpg, nowa-silueta.jpg)
10. Production-ready, deployable to GitHub Pages
11. Use gsap.quickTo() for ALL continuous mouse tracking — never gsap.to per mousemove
12. Guard all plugin registrations
13. Only animate transform and opacity for 60fps
14. 100dvh not 100vh for hero
15. The hero is position:fixed so gallery scrolls over it — keep this mechanic
16. The gallery uses ScrollTrigger pin — keep this but verify the last panel shows fully

## PROCESS
1. Read the entire index.html
2. Score every dimension 1-10
3. Identify every gap between current state and Awwwards SOTD
4. Fix ALL gaps — don't leave anything at "okay"
5. Verify the file is valid and complete (no truncated CSS/JS)
6. Verify all features still work (don't break anything)
7. Report what you changed and why, with before/after scores for each dimension