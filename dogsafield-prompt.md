# Prompt for DogsAfield Website Generation

---

## Project Overview

Create a premium landing page for **DogsAfield**, a mobile app that helps dog owners organize and discover local meetups, playdates, and outdoor adventures with other dogs and their humans. The site must follow 2026 Awwwards winning criteria (scroll-driven narrative, performance, accessibility, bold craft) while excluding 3D/WebGL entirely. Output a single responsive HTML file with embedded CSS and JavaScript.

---

## Design Philosophy

**Single strong visual concept:** "The path" — a continuous horizontal line that weaves and transforms across the page as the user scrolls, symbolizing the journey of discovery and connection. This line starts thin and abstract, gradually becoming a leash, then a trail map outline, then connecting dots representing people and dogs meeting. One concept, carried consistently, never mixed with competing effects.

**Restraint over spectacle.** No particle systems, no generic gradients, no template feel. Every element earns its place.

---

## Color & Typography

- **Palette:** Exactly 4 colors: warm sage `#6B7F5E` (dominant brand tone), off-white `#F5F2ED` (background), deep charcoal `#1A1A1A` (text), and vibrant amber `#E8A838` (CTA accent only).
- **Contrast usage rules (non-negotiable):**
  - Body text is always charcoal on off-white (passes 4.5:1)
  - Sage is used only for large text (24px+ bold), decorative elements, and the path line — never for body copy on off-white (it fails 4.5:1)
  - Amber CTA buttons always use charcoal text (amber on off-white fails contrast; charcoal on amber passes)
- **Typography:** System font stack only (`system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`) to keep the page a true single file with zero network font requests and zero font-induced layout shift. Hero text at 8–12vw using bold weight (700–800). Body at 16–18px. Create hierarchy through weight and size variation only — no decorative fonts, no font mixing. The word "DogsAfield" should feel iconic through sheer scale, weight, and letter-spacing alone.

---

## Page Structure (Scroll-Driven Narrative)

The page tells a **4-chapter story** as the user scrolls. Each chapter occupies a minimum of one viewport height (chapters may grow taller on mobile — never cram content to fit). The horizontal "path" line animates continuously across chapters, morphing at chapter transitions.

### Header
- Minimal fixed-position header: wordmark "DogsAfield" (text, top-left) and one anchor link to the final CTA section (top-right)
- Transparent background over the hero; gains a subtle off-white backdrop-blur once scrolled past the hero
- This is the `<header>` landmark; keep it under 60px tall

### Chapter 1: "Every walk begins somewhere"
- Full-viewport hero section
- The path line draws itself from left to right as user scrolls
- Large typography: headline + one-line subheadline
- Single CTA button (App Store / Google Play). All store buttons across the page use placeholder `href="#download"` with descriptive `aria-label`s — do not invent real store URLs
- Subtle parallax on the path line (moves at 0.6x scroll speed)
- **Pacing:** Slow easing — content reveals gradually, no rush

### Chapter 2: "Find your pack"
- Two-column layout (stacked on mobile): large bold statement on left, 3 feature cards on right
- Cards: "Discover Meetups", "Join Walks", "Create Events"
- Each card has a simple SVG icon (not generic — custom-designed, geometric, consistent stroke weight)
- Cards stagger-animate in as they enter viewport (translateY + opacity, 200ms offset between each)
- The path line continues through this section, now forming small dots at each card position

### Chapter 3: "Built for the trail"
- Single-column centered layout
- 3 scroll-triggered stats: "12,000+ meetups created", "48 countries", "98% would recommend" (placeholder figures — replace with real data before launch; mark with an HTML comment)
- Numbers count up from 0 when entering viewport (respecting `prefers-reduced-motion`)
- The path line now connects the three stats like waypoints on a trail map
- Subtle background texture (paper grain or linen, CSS-only, very low opacity)

### Chapter 4: "Your next adventure starts now"
- Full-viewport CTA section
- Split layout: left side has bold headline + download buttons, right side has a simple phone mockup built as an **inline SVG** (no raster images, no 3D — an SVG keeps the page a true single file)
- Phone mockup has a single CSS animation: a gentle floating effect (translateY oscillation, 3s ease-in-out infinite, paused when `prefers-reduced-motion: reduce`)
- Final path line terminates at the phone, connecting visually to the app

---

## Interactions & Micro-Interactions

- **Scroll-linked line animation:** The horizontal path line draws progressively using `stroke-dashoffset` tied to scroll progress. Prefer CSS scroll-driven animations (`animation-timeline: scroll()` / `view()`) where supported, with a fallback using a **passive** scroll listener + `requestAnimationFrame` (note: `IntersectionObserver` alone cannot drive continuous progress — it only fires at thresholds; use it only for discrete triggers like the stat counters and card reveals). Smooth, no jank.
- **Hover states on buttons:** Background color shifts 10% darker, slight scale (1.02), 150ms ease. No overshoot, no bounce.
- **Card hover:** Subtle lift (translateY -4px + soft box-shadow increase), 200ms ease.
- **Stat counter:** Animate number from 0 to target when in viewport. Use `IntersectionObserver` with threshold 0.5. Skip animation entirely when `prefers-reduced-motion: reduce`.
- **Loading state:** No loading screen. Content should render immediately with CSS `@layer` for critical styles. The line animation is the first thing visible.
- **Smooth scrolling:** Use `scroll-behavior: smooth` only for anchor links. Main scroll is native — never prevent, throttle, or remap the user's scroll (no scroll-jacking, no `preventDefault` on wheel/touch). Passive read-only scroll listeners for the path line and parallax are fine.

---

## Performance Requirements

- **Lighthouse mobile score:** 90+ on all metrics (Performance, Accessibility, Best Practices, SEO)
- **Total page weight:** The single HTML file must be under 200KB. No external JS frameworks. Vanilla JS only.
- **CSS:** Single embedded `<style>` block. Use CSS `@layer` for specificity management. Do not use `content-visibility: auto` — the page is only ~4 viewports tall and it can break scroll-linked animations and cause scrollbar jumps.
- **Images:** No raster images anywhere. All visuals (icons, phone mockup, path line, textures) are inline SVG or pure CSS. Every SVG gets explicit `width`/`height` or a sized container to reserve space.
- **Fonts:** System font stack only — no webfonts, no `@font-face`, no preloads. This eliminates font-swap layout shift entirely.
- **No layout shift:** Reserve exact space for all media and dynamic content (e.g., stat counters must not reflow as digits change — use `font-variant-numeric: tabular-nums` and a fixed min-width). Target CLS < 0.1, as close to 0 as possible.
- **JS:** Vanilla only. No libraries. All scripts in a single `<script>` tag at end of `<body>`. Use `IntersectionObserver` for discrete scroll-triggered effects (reveals, counters); use CSS scroll-driven animations or passive scroll listener + `requestAnimationFrame` for continuous scroll-linked effects.

---

## Accessibility Requirements

- All content readable without CSS or JS (semantic HTML first)
- All interactive elements keyboard-navigable with visible focus indicators (2px solid accent color, offset 2px)
- `prefers-reduced-motion: reduce` — disable all animations, show stat numbers at final values immediately
- Color contrast: minimum 4.5:1 for body text, 3:1 for large text
- Skip-to-content link as first focusable element
- All SVGs have `aria-hidden="true"` if decorative, or `role="img"` with `<title>` if meaningful
- ARIA landmarks: `<header>`, `<main>`, `<footer>` properly used
- Language attribute set on `<html>`
- No auto-playing media, no flashing elements

---

## SEO & Meta

- `<title>`: "DogsAfield — Meetups for Dog Owners & Their Dogs"
- Meta description: "Discover dog meetups, walks, and adventures near you. DogsAfield connects dog owners for real-world gatherings."
- Open Graph tags; `og:image` points to placeholder path `/og-image.jpg` (1200x630 — asset produced separately, cannot live inside a single HTML file; mark with an HTML comment)
- Structured data (JSON-LD): `MobileApplication` schema (correct type for an app-store product). Do not fabricate an `aggregateRating`
- `<link rel="canonical">`
- Semantic heading hierarchy (single `<h1>`, logical `<h2>` through `<h4>`)

---

## Code Quality

- Valid HTML5 with no warnings
- Consistent 2-space indentation
- No comments unless strictly necessary for non-obvious logic
- CSS custom properties for all colors and spacing (design tokens)
- All class names BEM or similarly consistent convention
- Mobile-first CSS (base styles = mobile, `@media (min-width: 768px)` for tablet, `@media (min-width: 1024px)` for desktop)
- Footer with copyright, social links (SVG icons), and a small "Made with love for dogs" tagline

---

## Tone & Copy

- Warm, inviting, slightly playful but not childish
- Short sentences. No corporate jargon. Write like you're talking to a friend who loves dogs.
- Example hero copy: "Every walk is better with friends." / "Find dog meetups near you."
- Example stat copy: Not "Leverage our global network" but "12,000+ meetups and counting"
- CTA text: "Download the app" (not "Get started" or "Sign up")
- Avoid entirely: exclamation marks, generic phrases ("revolutionize", "game-changer", "seamless")

---

## Constraints

- Single HTML file, all CSS and JS inline/embedded — zero network requests beyond the document itself
- No external dependencies (no CDN links for fonts, scripts, or styles)
- No 3D, no WebGL, no canvas, no `<video>` elements, no raster images
- Works perfectly in Chrome, Safari, Firefox, Edge (latest 2 versions); scroll-driven CSS animations must degrade gracefully where unsupported
- Responsive: 320px to 2560px width
- No horizontal scrolling at any viewport width

---

## Final Self-Audit (perform before delivering)

Verify every item and fix failures before output:

1. Zero network requests: no `http(s)://` references except the canonical/OG placeholder URLs in `<head>`
2. Contrast: charcoal on off-white for body, charcoal on amber for CTAs, sage only at large sizes
3. `prefers-reduced-motion: reduce` disables all animation, counters show final values, floating phone is static
4. Heading order: exactly one `<h1>`, no skipped levels
5. Keyboard: tab through every interactive element — visible focus ring on each, skip link first
6. No horizontal overflow at 320px, 768px, 1440px, 2560px (check hero vw-typography and the path SVG)
7. Stat counters use `tabular-nums` and reserved width — no reflow while counting
8. HTML validates (proper nesting, required attributes, `lang` on `<html>`)
9. File size under 200KB
10. Page still reads as a coherent document with JS disabled
