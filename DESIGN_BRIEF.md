# ALEXMLD.COM — GROK 4.5 DESIGN BRIEF
# Goal: Transform the current "okay start" portfolio into Awwwards-tier cinematic.

## WHAT EXISTS NOW
- `/root/projects/alexmld-v2/index.html` — single file, 448 lines. Dark theme (#0a0a0a), gold accent (#c4a668), Inter font, GSAP + Lenis + ScrollTrigger. Has: preloader, hero with cycling roles, path timeline, about, work sections (vertical, radial gradient backgrounds — PLACEHOLDER ENERGY), contact. Structurally sound but visually flat. No custom cursor, no film grain, no 3D tilt, no horizontal scroll, no page transitions, no real project imagery.
- `/root/projects/alexmld-v2/assets/projects/` — real screenshots: gentsclub.jpg, delirat.jpg, whumple.jpg, nowa-silueta.jpg

## REFERENCE CODE (for motion patterns)
- `/root/projects/alaxin-website/style.css` — 1285 lines. Has: custom cursor (ring+dot+label), film grain (SVG turbulence), 3D panel tilt, glassmorphism heading, scroll progress bar, panel reveal clip-path, hover-reveal image popup, reduced motion, responsive.
- `/root/projects/alaxin-website/script.js` — 782 lines. Has: Lenis smooth scroll, velocity-driven cursor tilt, magnetic links, 3D panel hover, horizontal scroll gallery (GSAP ScrollTrigger), clip-path panel reveals, page transition overlay wipe, scroll progress, contact footer reveal, parallax.

## DESIGN DNA — Guglieri/Opal Tadpole editorial lineage (Pole B)
- Background: pure black `#0a0a0a` (keep current)
- Accent: gold `#c4a668` (keep current) — used ONLY on: active states, key labels, scroll progress bar, hover states. Never as background fill.
- Font: Inter (already loaded, weights 200-900). Display: 800 weight, `letter-spacing: -.04em`, `line-height: .9`. Labels: 500 weight, `letter-spacing: .25-.3em`, uppercase.
- Type scale: fluid vw-based — `clamp(2.8rem, 12vw, 9rem)` for hero name, `clamp(2.2rem, 9vw, 7rem)` for project names.
- Film grain: SVG feTurbulence, opacity .03-.05, fixed position, pointer-events none.
- Easing: `cubic-bezier(.19, 1, .22, 1)` (easeOutExpo — the Opal Tadpole signature `--ease-o6`).

## THE REBUILD — what to change

### KEEP (structure is good):
- HTML structure: preloader → nav → hero → path → about → work → contact
- The narrative copy (it's good — modeling→design→code→Oulu story)
- The path timeline concept (4 steps: Frames, Canvas, Code, Oulu)
- The about section text
- Contact section structure
- The gold accent color
- Inter font
- Lenis smooth scroll
- GSAP + ScrollTrigger
- Reduced motion + noscript fallbacks
- Mobile responsive structure

### REBUILD — the work section (THIS IS THE PRIORITY):
Replace the 5 vertical full-page sections with radial gradients with a HORIZONTAL SCROLL GALLERY:

- Gallery wrapper: `height: 500vh`, `background: var(--bg)`, `margin-top: 100vh` (hero is fixed)
- Sticky track inside: `position: sticky; top: 0; height: 100vh; overflow: hidden`
- Horizontal track: `display: flex; gap: 2.5rem; padding: 0 6rem; will-change: transform`
- GSAP ScrollTrigger: translate track X from 0 to -(scrollWidth - innerWidth) as user scrolls vertically through the 500vh wrapper
- Each project panel: `flex-shrink: 0; height: 70vh; aspect-ratio: 3/4; overflow: hidden; border-radius: 4px`
- Panel background: REAL SCREENSHOT from `/assets/projects/` — `object-fit: cover; filter: grayscale(100%)` default, `grayscale(0%)` on hover
- Panel info overlay: title + meta + desc, hidden by default, revealed on hover with gradient from transparent to `rgba(10,10,10,.85)`
- Panel number: `01 / 05` style, positioned top, gold color, muted
- Panel hover: 3D tilt (rotateY/rotateX based on mouse position, max 8deg), scale 1.06, grayscale→color
- Panel click: page transition overlay wipe (black scaleY 0→1, then navigate to URL)
- Mobile: stack vertically (no horizontal scroll), always show info, no grayscale

### ADD — cinematic layer (the "godly" polish):

1. **Custom cursor** (desktop only):
   - Ring: 40px circle, 1.5px border, `mix-blend-mode: difference`
   - Dot: 6px filled circle
   - Velocity tilt: ring tilts based on mouse velocity (max 6deg)
   - Link hover: ring grows to 60px, opacity .5
   - Panel hover: ring grows to 80px, fills white, shows "VIEW" label
   - Use `gsap.quickTo()` for all tracking — NEVER `gsap.to` per mousemove
   - Dark sections: cursor border turns white
   - Touch devices: hide cursor entirely

2. **Film grain**: SVG feTurbulence fractalNoise, `baseFrequency: .9`, 3 octaves, `opacity: .03`, fixed position, `z-index: 9998`, `pointer-events: none`

3. **Scroll progress bar**: 2px gold bar at top, `mix-blend-mode: difference`, width animates with scroll

4. **Preloader upgrade**: Keep "ALEX MOLDOVAN" text but add a loading bar (120px wide, 1px height, fills over 1.8s with shimmer effect). Bar fills → preloader slides up (`yPercent: -100`, 1.2s, `power4.inOut`) → hero reveals.

5. **Hero entrance**: Split name into characters, reveal with `yPercent: 110% → 0`, `opacity: 0 → 1`, `filter: blur(12px) → blur(0)`, stagger .025, `expo.out`, 1.4s duration. Role text fades in after. Scroll cue pulses.

6. **Section reveals**: All `.reveal` elements: `y: 60 → 0`, `opacity: 0 → 1`, 1.2s, `power4.out`, triggered at `top 85%`.

7. **Magnetic links**: Nav links and contact email have `data-magnetic` — shift 35% toward cursor on mousemove, spring back on leave.

8. **Gallery heading**: "SELECTED WORK" label with a line that animates from 0% to 100% width, glassmorphism backdrop-filter blur, fades in when gallery enters.

9. **Contact footer reveal**: As you scroll to the bottom, the contact section should feel like a reveal — the fixed links slide up, the email is large and golden on hover.

10. **Parallax on project images**: Images shift `yPercent: 10` as gallery scrolls (depth effect).

### TIMELINE UPGRADE:
- Keep the 4-step horizontal timeline
- Add scroll-triggered reveal: steps fade in with stagger
- Gold dots should have a subtle glow (`box-shadow: 0 0 20px rgba(196,166,104,.3)`) on hover/active
- The connecting line should animate from left to right on scroll
- On mobile: vertical timeline (keep current behavior)

### PROJECTS (use real screenshots):
```
01 GENTSCLUB — Cinematic coworking · 2025 · Design + Development
   Dark-luxury web experience for a premium coworking space. Custom booking system, trilingual i18n, Firebase backend.
   URL: https://gentsclub.ro
   Image: assets/projects/gentsclub.jpg

02 DELIRAT — Event brand · 2025 · Design + Development  
   Brand identity and web presence for an event collective. GSAP animations, canvas effects, live at delirat.com.
   URL: https://delirat.com
   Image: assets/projects/delirat.jpg

03 FRIENDBY — Social discovery app · 2025 · Design + Development
   Location-based social app built for Oulu. Next.js, Supabase, Mapbox. From web to native with Capacitor.
   URL: (no live link yet)
   Image: assets/projects/delirat.jpg (reuse delirat or use a gradient placeholder — NO real screenshot yet)

04 WHUMPLE — Discord bot platform · 2024 · Development
   Discord bot with a real user base. discord.py, SQLite, multi-page documentation site. Built and shipped solo.
   URL: (link to whumple-website local or skip)
   Image: assets/projects/whumple.jpg

05 NOWA SILUETA — Web presence · 2025 · Design + Development
   Live website built and deployed. Clean, responsive, search-optimized.
   URL: https://nowa-silueta.ro (or local)
   Image: assets/projects/nowa-silueta.jpg
```

For FRIENDBY (no screenshot): use a CSS-only panel with a gradient background (subtle blue-ish) and the text overlaid. Don't fake a screenshot.

## CRITICAL RULES
1. SINGLE FILE: everything in one `index.html` (CSS in `<style>`, JS in `<script>`)
2. All CDNs: GSAP 3.12.5, ScrollTrigger 3.12.5, Lenis 1.1.14, Google Fonts Inter
3. `prefers-reduced-motion`: full support — strip all animations, instant reveals, no cursor, no grain
4. `<noscript>`: force all `.reveal` opacity:1, hide preloader, show content
5. Touch detection: `('ontouchstart' in window)` — hide custom cursor on touch
6. Guard all plugin registrations: `if (typeof ScrollTrigger !== 'undefined') gsap.registerPlugin(ScrollTrigger)`
7. Use `gsap.quickTo()` for ALL continuous mouse tracking
8. Only animate `transform` and `opacity` for 60fps
9. `100dvh` not `100vh` for hero
10. Mobile-first responsive: `@media(max-width:768px)` — type bigger on mobile, panels stack, no horizontal scroll, no cursor, no grayscale
11. The page must NOT render blank if any CDN fails — noscript + reduced-motion fallbacks
12. Keep the existing copy exactly as-is — don't rewrite the narrative
13. Image paths: `assets/projects/gentsclub.jpg` etc. (relative to index.html)
14. The hero should be `position: fixed` so the gallery scrolls over it (hero fades to black as you scroll, revealing the gallery underneath)

## OUTPUT
Rewrite `/root/projects/alexmld-v2/index.html` completely. The file should be production-ready, deployable to GitHub Pages as-is.