# GROK 4.5 PASS 2 — MOTION CHOREOGRAPHY & INTERACTION REFINEMENT

## GOAL
This is Pass 2 of 4. Pass 1 already fixed typography hierarchy, atmosphere, mobile type scale, and overall design quality. This pass focuses EXCLUSIVELY on motion — easing curves, scroll-driven storytelling, cursor/magnetic refinement, gallery animation timing, and micro-interaction polish. Make the motion feel choreographed, not decorative.

## WHAT EXISTS (current state after Pass 1)
- 1637-line single-file portfolio at /root/projects/alexmld-portfolio/index.html
- GSAP 3.12.5 + ScrollTrigger + Lenis 1.1.14 + Inter font
- Colors: --bg:#070707, --accent:#b8b8b8 (silver), --text:#f0efe9
- Features: preloader with shimmer bar, hero char-split reveal (blur 10px, yPercent 120, stagger .032, expo.out 1.55s), cycling roles with blur transitions, hero fade on scroll, section reveals (y:80, 1.45s power4.out), timeline fill (scrub .6), horizontal gallery (pin, scrub 1, clip-path entrance), 3D panel tilt (quickTo, 10deg max), custom cursor (ring+dot+label, velocity tilt, quickTo), magnetic links (quickTo, 38% pull, elastic return), page transition wipe, scroll progress bar, nav active states
- All continuous mouse tracking already uses gsap.quickTo() — good
- Mobile: stacked gallery, touch auto-cycle for timeline, no cursor/grain

## WHAT TO DEEP-THINK AND FIX

### 1. EASING CURVE DISCIPLINE
The Opal Tadpole / Guglieri lineage uses a full easing token system. Currently the site uses a mix of power2, power3, power4, expo.out, and elastic — but without a disciplined system. 

Audit every easing choice:
- Is `cubic-bezier(.19,1,.22,1)` (easeOutExpo, the signature --ease) used consistently for primary reveals?
- Are entrance eases different from hover/interaction eases?
- Should the hero char reveal use a different ease than section reveals?
- Is the timeline fill scrub smooth enough?
- Are the elastic returns on magnetic links too bouncy or just right?
- Consider adding the full 6-step Opal Tadpole ease token system as CSS variables

### 2. SCROLL-DRIVEN STORYTELLING
The site has scroll reveals, but do they tell a story? Awwwards sites choreograph the scroll experience:
- Hero fade: currently fades everything at once (y:-70, opacity:0). Should elements fade in sequence? Name first, then role, then location, then scroll cue?
- Section transitions: currently sections just appear (reveal class). Should there be clip-path reveals, mask reveals, or staggered child reveals?
- Path section: the timeline line fills on scroll (good). But should the steps have a more dramatic entrance? Stagger their reveals with the line fill?
- About section: text appears as one block. Should individual lines reveal sequentially as you scroll? (Split by <br> and stagger)
- Gallery: clip-path entrance from bottom (good). Should the heading "SELECTED WORK" have a more dramatic reveal? The line animation is good but could be more cinematic.
- Contact: email and links stagger in (good). Should the email have a character-by-character reveal like the hero name?

### 3. GALLERY ANIMATION TIMING
- The horizontal scroll uses scrub:1 — is this the right feel? Too laggy? Too instant?
- Panel entrance: clip-path inset(100% 0 0 0) → inset(0), stagger .12, expo.out 1.7s — is this dramatic enough? Should it be faster/slower?
- Parallax: yPercent:10 on images — is this noticeable? Should it be stronger?
- Should there be a subtle scale on panels as they enter the viewport center?
- The gallery heading fades out as the pinned track takes over — is this transition smooth?
- When you scroll back up, does the gallery reverse cleanly?

### 4. CURSOR REFINEMENT
- Ring follows with 0.5s ease (power3.out) — is this the right feel? Premium sites use .4-.6s
- Dot follows with 0.08s — instant feel, good
- Velocity tilt: max 6deg — is this noticeable? Should it be 8deg?
- Link hover: ring grows to 64px, opacity .55 — should the transition be smoother?
- Panel hover: ring grows to 88px, fills with text "VIEW" — is this dramatic enough?
- Should the cursor have a idle state? (e.g., subtle pulse when stationary for 2s)
- Should the cursor label change based on context? ("VIEW" for panels with URL, "SOON" for panels without)

### 5. MAGNETIC LINK REFINEMENT
- Pull strength: 38% — is this right? Too much? Too little?
- Return ease: elastic.out(1.15, 0.32) — is the bounce right?
- Should the pull be stronger for the contact email (the main CTA) vs nav links?
- Should there be a subtle scale on magnetic elements during pull?

### 6. HERO ENTRANCE CHOREOGRAPHY
- Preloader: bar fills 1.9s, then slides up 1.25s — is this too long? Too short?
- Hero reveal: label → chars (stagger .032, 1.55s) → role → location → scroll cue → nav
- Is the timing between elements right? Should there be more breathing room?
- The blur(10px) → 0 on chars — is this too heavy? Too subtle?
- Should the hero glow have a more dramatic entrance? (currently just breathes)
- Should the role text cycle have a more dramatic transition? (currently blur(4px) + y shift)

### 7. MICRO-INTERACTION POLISH
- Nav links: underline scaleX animation — should there also be a subtle y-shift on hover?
- Contact email: underline + color change — should the email also have a magnetic pull?
- Step dots: scale + glow on hover/active — should the glow be stronger?
- Project panel hover: border + shadow + image filter + info reveal — is the sequence right? Should info appear before or after the image transitions to color?
- Scroll progress bar: should it have a glow that intensifies near the end?
- Should there be a subtle haptic-like visual feedback on click? (scale down briefly)

### 8. PERFORMANCE OF MOTION
- Are all animations on transform/opacity only? (no width/height/margin on hot paths)
- Is the blur filter on hero chars causing jank? (16 chars × blur(10px) → blur(0))
- Should panel entrance use will-change: clip-path?
- Is Lenis duration (1.7) right? Too slow? Too fast?
- Are there any layout thrashing opportunities? (reading offsetWidth/scrollWidth during animation)

## RULES
1. SINGLE FILE: everything stays in index.html
2. Keep ALL features working — don't break anything
3. Keep the silver accent #b8b8b8
4. Only use: GSAP, ScrollTrigger, Lenis, Google Fonts Inter
5. Full reduced-motion + noscript + touch fallbacks MUST remain functional
6. Use gsap.quickTo() for ALL continuous mouse tracking
7. Only animate transform and opacity for 60fps (blur is acceptable for hero entrance only)
8. Don't change the HTML structure or copy — only motion/timing/interaction
9. Don't change colors, fonts, or layout — only motion
10. Production-ready, deployable to GitHub Pages

## PROCESS
1. Read the entire index.html
2. Audit every animation, transition, and interaction for Awwwards-tier quality
3. Fix all motion issues — make everything feel choreographed, not random
4. Verify the file is valid and complete
5. Verify all features still work
6. Report what you changed and why