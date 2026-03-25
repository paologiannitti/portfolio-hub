# Portfolio hub redesign - "The Reveal"

## Core narrative

"Connecting strategy to emotion" - every element builds toward this feeling.
Voice: Direct storyteller. Copy processed through humanizer skill.

## Design principles

1. Intentionality - every element has a reason
2. Emotional resonance over aesthetics
3. Visual hierarchy as narrative (contrast = drama, whitespace = pause, scale = emphasis)
4. Subconscious communication - meaning through shape/color/layout before reading
5. Movement and vitality - the design breathes, moves, confesses

## Reference

Target aesthetic: https://epiminds.com
Dark minimalist luxury. Serif + sans-serif typography. Massive whitespace. Restraint.

## Scope

Main hub page only (`index.html`). Case study sub-pages unchanged.

---

## Sections

### 1. Navigation

Fixed, black background. Name (Instrument Serif) left, "Work" + "Contact" text links right.
No glass, no buttons, no hamburger. Compresses slightly on scroll.

### 2. Hero - the opening line

Full viewport, pure black, centered.
Name in large Instrument Serif, white.
Below: one line in sans-serif -
"Most brands communicate. Few connect. I close that gap."
No CTAs, no scroll indicators. Fade-in over ~1.5s.

### 3. Philosophy - the setup

Centered text block, max-width ~600px, generous vertical padding.
Instrument Serif italic, ~22px, good line height.
Content: a short paragraph about connecting strategy to emotion.
No heading, no label. Word-by-word scroll reveal.

### 4. Projects - the evidence

Section label: "Work" in small uppercase sans-serif, left-aligned, muted.
Sequential full-width rows, separated by 1px lines (rgba(255,255,255,0.08)).

Each row:
- Badge: small uppercase tag (e.g. FAN ENGAGEMENT)
- Heading: project name in large Instrument Serif
- Description: one sentence framing the tension, not the deliverable
- Link: "View case study ->" with hover underline

Fade-in on scroll, staggered. Tooltip on hover with outcome stat.
No images, no thumbnails. Rows slide in from alternating sides.

### 5. About - the person

Large Instrument Serif italic quote, centered, ~28px.
Below: avatar (circular, ~80px), name, badge pills (MSc Sports Business, Madrid / Remote, Creative Strategist).
Quote scales 0.95->1.0 on scroll entry.

### 6. Contact - the invitation

Single centered line in Instrument Serif: conversational CTA.
Below: three text links in a row (Email, LinkedIn, Phone), muted with hover brightening.

### 7. Footer

1px separator. Copyright left, "Top" link right. Minimal.
Skip link for accessibility.

---

## Typography

| Role | Font | Weight | Size |
|---|---|---|---|
| Display/headlines | Instrument Serif | Regular/Italic | 48-72px |
| Body/descriptions | Instrument Sans | Regular | 16-18px |
| Labels/badges | Satoshi | Medium | 11-13px uppercase tracked |
| Nav links | Instrument Sans | Regular | 14px |

## Color palette

| Token | Value | Use |
|---|---|---|
| --bg | #000000 | Page background |
| --text | #FFFFFF | Primary text |
| --text-muted | rgba(255,255,255,0.5) | Secondary text, labels |
| --text-dim | rgba(255,255,255,0.25) | Separators, subtle elements |

Monochrome only. No accent colors.

## Effects

### Loader - "The curtain"
Black screen, name types out letter by letter in Instrument Serif (~3s).
Screen fades to reveal hero.

### Canvas background - "Breathing dark"
Subtle slow-moving film grain/noise on canvas behind all content.
Almost imperceptible. Lightweight, no libraries.

### Scroll reveals
- Philosophy: word-by-word reveal on scroll
- Projects: rows slide in from alternating left/right
- About quote: scales 0.95->1.0 on entry
- General: elements fade in with translateY(20px->0), 0.8s

### Parallax depth
Section headings and hero name scroll at slightly slower rate than body.
Pure CSS or lightweight JS.

### Cursor glow
Faint radial gradient (~200px, rgba(255,255,255,0.03)) follows cursor on desktop.
Disabled on mobile.

### Reduced motion
All animations disabled when prefers-reduced-motion is set.

## Components used

- Navigation (fixed, minimal)
- Hero (full viewport)
- Quote/blockquote (philosophy, about)
- Separator (between project rows)
- Badge (project tags, about pills)
- Heading (project names, section labels)
- Link (nav, project CTAs, contact)
- Avatar (about photo)
- Tooltip (project hover stats)
- Footer (minimal)
- Skip link (accessibility)
