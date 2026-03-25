# Portfolio Redesign - "The Reveal" Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Rewrite the portfolio hub index.html from a colorful glassmorphism design to a dark minimalist narrative-driven page inspired by epiminds.com.

**Architecture:** Complete rewrite of index.html. Single file, inline CSS/JS, no build tools. Same deployment (GitHub Pages). Case study sub-pages unchanged. All copy must be processed through the humanizer skill (C:\Users\paolo\Downloads\humanizer.skill - extracted at /tmp/humanizer_extracted/humanizer/SKILL.md).

**Tech Stack:** HTML, CSS (custom properties, grid, animations), vanilla JS (Intersection Observer, Canvas API, requestAnimationFrame). Google Fonts: Instrument Serif, Instrument Sans, Satoshi.

**Design doc:** `docs/plans/2026-03-09-portfolio-redesign-design.md`

---

### Task 1: Backup and scaffold

**Files:**
- Copy: `index.html` -> `index-backup.html`
- Create: new `index.html` (scaffold only)

**Step 1: Create backup**

```bash
cd C:/Users/paolo/Desktop/Portfolio/portfolio-hub
cp index.html index-backup.html
```

**Step 2: Write HTML scaffold with CSS variables and fonts**

Write new `index.html` with:
- DOCTYPE, meta tags, viewport, title "Paolo Giannitti | Creative Strategist"
- Google Fonts link: Instrument Serif (regular, italic), Instrument Sans (regular, medium), Satoshi (medium) via CDN
- CSS reset and custom properties:
  ```css
  :root {
    --bg: #000000;
    --text: #FFFFFF;
    --text-muted: rgba(255, 255, 255, 0.5);
    --text-dim: rgba(255, 255, 255, 0.25);
    --separator: rgba(255, 255, 255, 0.08);
  }
  ```
- Body styled: black bg, white text, Instrument Sans default font
- Empty semantic sections: `<div id="loader">`, `<canvas id="grain">`, `<nav>`, `<main>` with sections for hero, philosophy, work, about, contact, `<footer>`
- `<a href="#main" class="skip-link">Skip to content</a>` for accessibility

**Step 3: Open in browser and verify black page with no errors**

```bash
# Open in default browser to check
start C:/Users/paolo/Desktop/Portfolio/portfolio-hub/index.html
```

**Step 4: Commit**

```bash
git add index-backup.html index.html
git commit -m "scaffold: backup old design and create new index.html shell"
```

---

### Task 2: Loader - "The curtain"

**Files:**
- Modify: `index.html`

**Step 1: Add loader HTML**

Inside `<div id="loader">`:
```html
<div class="loader-name"></div>
```

**Step 2: Add loader CSS**

```css
#loader {
  position: fixed;
  inset: 0;
  z-index: 1000;
  background: #000;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: opacity 0.8s ease;
}

#loader.done {
  opacity: 0;
  pointer-events: none;
}

.loader-name {
  font-family: 'Instrument Serif', serif;
  font-size: clamp(32px, 6vw, 64px);
  color: var(--text);
  letter-spacing: 0.02em;
}
```

**Step 3: Add loader JS**

```javascript
const loaderEl = document.getElementById('loader');
const loaderName = loaderEl.querySelector('.loader-name');
const fullName = 'Paolo Giannitti';
let i = 0;

function typeName() {
  if (i <= fullName.length) {
    loaderName.textContent = fullName.slice(0, i);
    i++;
    setTimeout(typeName, 100);
  } else {
    setTimeout(() => {
      loaderEl.classList.add('done');
      document.body.classList.add('loaded');
    }, 600);
  }
}

// Start after fonts load
document.fonts.ready.then(() => typeName());
```

Body starts with `overflow: hidden` until `.loaded` class removes it.

**Step 4: Verify in browser**

Open page. Should see black screen, name types out over ~1.6s, then fades away.

**Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add name typewriter loader"
```

---

### Task 3: Canvas grain background - "Breathing dark"

**Files:**
- Modify: `index.html`

**Step 1: Add canvas CSS**

```css
#grain {
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  opacity: 0.04;
}
```

**Step 2: Add grain JS**

```javascript
const canvas = document.getElementById('grain');
const ctx = canvas.getContext('2d');

function resize() {
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
}
resize();
window.addEventListener('resize', resize);

function renderGrain() {
  const w = canvas.width;
  const h = canvas.height;
  const imageData = ctx.createImageData(w, h);
  const data = imageData.data;

  for (let i = 0; i < data.length; i += 4) {
    const v = Math.random() * 255;
    data[i] = v;
    data[i + 1] = v;
    data[i + 2] = v;
    data[i + 3] = 255;
  }

  ctx.putImageData(imageData, 0, 0);
}

// Throttle to ~12fps for performance
let grainFrame = 0;
function grainLoop() {
  grainFrame++;
  if (grainFrame % 5 === 0) renderGrain();
  requestAnimationFrame(grainLoop);
}
grainLoop();
```

**Step 3: Verify in browser**

Should see subtle, barely-visible film grain shifting on the black background. Check performance - should be smooth.

**Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add canvas film grain background"
```

---

### Task 4: Navigation

**Files:**
- Modify: `index.html`

**Step 1: Add nav HTML**

```html
<nav id="navbar">
  <a href="#" class="nav-name">Paolo Giannitti</a>
  <div class="nav-links">
    <a href="#work">Work</a>
    <a href="#contact">Contact</a>
  </div>
</nav>
```

**Step 2: Add nav CSS**

```css
nav {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 24px 48px;
  background: transparent;
  transition: padding 0.4s ease, background 0.4s ease;
}

nav.scrolled {
  padding: 16px 48px;
  background: rgba(0, 0, 0, 0.9);
  backdrop-filter: blur(8px);
}

.nav-name {
  font-family: 'Instrument Serif', serif;
  font-size: 18px;
  color: var(--text);
  text-decoration: none;
}

.nav-links a {
  font-family: 'Instrument Sans', sans-serif;
  font-size: 14px;
  color: var(--text-muted);
  text-decoration: none;
  margin-left: 32px;
  transition: color 0.3s ease;
}

.nav-links a:hover {
  color: var(--text);
}
```

**Step 3: Add scroll detection JS**

```javascript
const navbar = document.getElementById('navbar');
window.addEventListener('scroll', () => {
  navbar.classList.toggle('scrolled', window.scrollY > 50);
});
```

**Step 4: Verify in browser**

Nav visible, transparent at top, compresses and gets backdrop on scroll.

**Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add minimal fixed navigation"
```

---

### Task 5: Hero section

**Files:**
- Modify: `index.html`

**Step 1: Add hero HTML**

```html
<section class="hero" id="hero">
  <h1 class="hero-name">Paolo Giannitti</h1>
  <p class="hero-tagline">Most brands communicate. Few connect. I close that gap.</p>
</section>
```

NOTE: The tagline copy above is a draft. Apply the humanizer skill to finalize. The voice is "direct storyteller" - conversational, pointed, no AI-isms.

**Step 2: Add hero CSS**

```css
.hero {
  position: relative;
  z-index: 1;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 0 24px;
}

.hero-name {
  font-family: 'Instrument Serif', serif;
  font-size: clamp(48px, 8vw, 96px);
  font-weight: 400;
  letter-spacing: -0.02em;
  line-height: 1.1;
  opacity: 0;
  transform: translateY(20px);
  animation: fadeUp 1.5s ease forwards;
  animation-delay: 0.2s;
}

.hero-tagline {
  font-family: 'Instrument Sans', sans-serif;
  font-size: clamp(16px, 2vw, 20px);
  color: var(--text-muted);
  margin-top: 24px;
  max-width: 500px;
  opacity: 0;
  transform: translateY(20px);
  animation: fadeUp 1.5s ease forwards;
  animation-delay: 0.6s;
}

@keyframes fadeUp {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Wait for loader to finish */
body:not(.loaded) .hero-name,
body:not(.loaded) .hero-tagline {
  animation-play-state: paused;
}
```

**Step 3: Verify in browser**

After loader finishes, hero fades in. Name large and centered, tagline below in muted color.

**Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add hero section with fade-in animation"
```

---

### Task 6: Philosophy section

**Files:**
- Modify: `index.html`

**Step 1: Add philosophy HTML**

```html
<section class="philosophy" id="philosophy">
  <p class="philosophy-text">
    <!-- Each word wrapped in a span for word-by-word reveal -->
  </p>
</section>
```

The text content (apply humanizer skill when writing):
Draft: "I'm a creative strategist who thinks in systems and feels in stories. After a decade of visual storytelling across five countries, I landed on a simple truth: strategy without emotion is forgettable. Emotion without strategy is noise. The work that lasts lives where the two meet."

Each word wrapped in `<span class="word">word</span>` for the scroll reveal effect.

**Step 2: Add philosophy CSS**

```css
.philosophy {
  position: relative;
  z-index: 1;
  padding: 160px 24px;
  display: flex;
  justify-content: center;
}

.philosophy-text {
  font-family: 'Instrument Serif', serif;
  font-style: italic;
  font-size: clamp(20px, 2.5vw, 28px);
  line-height: 1.7;
  max-width: 640px;
  text-align: center;
  color: var(--text);
}

.philosophy-text .word {
  display: inline-block;
  opacity: 0.15;
  transition: opacity 0.4s ease;
}

.philosophy-text .word.visible {
  opacity: 1;
}
```

**Step 3: Add word-by-word scroll reveal JS**

```javascript
// Word reveal on scroll
const philosophySection = document.querySelector('.philosophy');
const words = document.querySelectorAll('.philosophy-text .word');

function revealWords() {
  const sectionRect = philosophySection.getBoundingClientRect();
  const sectionTop = sectionRect.top;
  const sectionHeight = sectionRect.height;
  const windowHeight = window.innerHeight;

  // Progress: 0 when section enters viewport, 1 when it's about to leave
  const progress = Math.max(0, Math.min(1,
    (windowHeight - sectionTop) / (windowHeight + sectionHeight)
  ));

  const revealCount = Math.floor(progress * words.length * 1.5);

  words.forEach((word, index) => {
    word.classList.toggle('visible', index < revealCount);
  });
}

window.addEventListener('scroll', revealWords);
```

**Step 4: Verify in browser**

Scroll down past hero. Words should reveal progressively as you scroll through the section.

**Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add philosophy section with word-by-word scroll reveal"
```

---

### Task 7: Projects section

**Files:**
- Modify: `index.html`

**Step 1: Add projects HTML**

```html
<section class="work" id="work">
  <span class="section-label">Work</span>

  <div class="project-row" data-direction="left">
    <span class="project-badge">FAN ENGAGEMENT</span>
    <h2 class="project-name">
      <a href="atletico-case-study/">Atletico Madrid</a>
    </h2>
    <p class="project-desc">A stadium full of fans, but no conversation between them.</p>
    <a href="atletico-case-study/" class="project-link">View case study &rarr;</a>
  </div>

  <div class="project-row" data-direction="right">
    <span class="project-badge">BRAND AUDIT</span>
    <h2 class="project-name">
      <a href="glovo-audit/">Glovo</a>
    </h2>
    <p class="project-desc">A brand everyone recognized but nobody could describe.</p>
    <a href="glovo-audit/" class="project-link">View analysis &rarr;</a>
  </div>

  <div class="project-row" data-direction="left">
    <span class="project-badge">BRAND IDENTITY</span>
    <h2 class="project-name">
      <a href="beanventure-brand/">Beanventure</a>
    </h2>
    <p class="project-desc">Coffee culture meets adventure — a brand that needed its own language.</p>
    <a href="beanventure-brand/" class="project-link">View brand guide &rarr;</a>
  </div>

  <div class="project-row" data-direction="right">
    <span class="project-badge">SPONSORSHIP</span>
    <h2 class="project-name">
      <a href="esbs-sponsorship/">ESBS Madrid FC</a>
    </h2>
    <p class="project-desc">Two worlds — coffee and football — that had more in common than anyone thought.</p>
    <a href="esbs-sponsorship/" class="project-link">View proposal &rarr;</a>
  </div>
</section>
```

NOTE: All project descriptions are drafts. Apply humanizer skill to finalize. Each should frame the *tension* the project resolved, not the deliverable.

**Step 2: Add projects CSS**

```css
.work {
  position: relative;
  z-index: 1;
  padding: 80px 48px 120px;
  max-width: 900px;
  margin: 0 auto;
}

.section-label {
  font-family: 'Satoshi', sans-serif;
  font-size: 12px;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.15em;
  color: var(--text-muted);
  display: block;
  margin-bottom: 64px;
}

.project-row {
  padding: 48px 0;
  border-top: 1px solid var(--separator);
  opacity: 0;
  transform: translateX(-30px);
  transition: opacity 0.8s ease, transform 0.8s ease;
}

.project-row[data-direction="right"] {
  transform: translateX(30px);
}

.project-row.visible {
  opacity: 1;
  transform: translateX(0);
}

.project-row:last-child {
  border-bottom: 1px solid var(--separator);
}

.project-badge {
  font-family: 'Satoshi', sans-serif;
  font-size: 11px;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.15em;
  color: var(--text-muted);
}

.project-name {
  font-family: 'Instrument Serif', serif;
  font-size: clamp(32px, 5vw, 56px);
  font-weight: 400;
  margin: 12px 0 16px;
  line-height: 1.1;
}

.project-name a {
  color: var(--text);
  text-decoration: none;
  transition: opacity 0.3s ease;
}

.project-name a:hover {
  opacity: 0.7;
}

.project-desc {
  font-family: 'Instrument Sans', sans-serif;
  font-size: 16px;
  color: var(--text-muted);
  max-width: 480px;
  line-height: 1.6;
}

.project-link {
  display: inline-block;
  margin-top: 20px;
  font-family: 'Instrument Sans', sans-serif;
  font-size: 14px;
  color: var(--text-muted);
  text-decoration: none;
  position: relative;
  transition: color 0.3s ease;
}

.project-link::after {
  content: '';
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 0;
  height: 1px;
  background: var(--text);
  transition: width 0.3s ease;
}

.project-link:hover {
  color: var(--text);
}

.project-link:hover::after {
  width: 100%;
}
```

**Step 3: Add project scroll reveal JS**

```javascript
const projectRows = document.querySelectorAll('.project-row');
const projectObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
    }
  });
}, { threshold: 0.15, rootMargin: '0px 0px -60px 0px' });

projectRows.forEach(row => projectObserver.observe(row));
```

**Step 4: Add tooltip for project hover stats**

Each `.project-row` gets a `data-tooltip` attribute with an outcome stat. CSS-only tooltip on hover:

```css
.project-row {
  position: relative;
}

.project-row::before {
  content: attr(data-tooltip);
  position: absolute;
  top: 16px;
  right: 0;
  font-family: 'Satoshi', sans-serif;
  font-size: 11px;
  color: var(--text-dim);
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
}

.project-row:hover::before {
  opacity: 1;
}
```

**Step 5: Verify in browser**

Projects appear sequentially on scroll, alternating slide direction. Hover shows tooltip, link underline animates.

**Step 6: Commit**

```bash
git add index.html
git commit -m "feat: add projects section with scroll reveals and tooltips"
```

---

### Task 8: About section

**Files:**
- Modify: `index.html`

**Step 1: Add about HTML**

```html
<section class="about" id="about">
  <blockquote class="about-quote">
    Ten years of telling stories across five countries taught me one thing: the best work happens when strategy stops talking and starts feeling.
  </blockquote>
  <div class="about-person">
    <img src="paolo-photo.jpg" alt="Paolo Giannitti" class="about-avatar" />
    <span class="about-name">Paolo Giannitti</span>
    <div class="about-badges">
      <span class="badge">MSc Sports Business</span>
      <span class="badge">Madrid / Remote</span>
      <span class="badge">Creative Strategist</span>
    </div>
  </div>
</section>
```

NOTE: Quote is a draft. Apply humanizer skill to finalize.

**Step 2: Add about CSS**

```css
.about {
  position: relative;
  z-index: 1;
  padding: 160px 24px;
  text-align: center;
}

.about-quote {
  font-family: 'Instrument Serif', serif;
  font-style: italic;
  font-size: clamp(22px, 3vw, 32px);
  line-height: 1.6;
  max-width: 700px;
  margin: 0 auto 64px;
  color: var(--text);
  transform: scale(0.95);
  opacity: 0;
  transition: transform 0.8s ease, opacity 0.8s ease;
}

.about-quote.visible {
  transform: scale(1);
  opacity: 1;
}

.about-person {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.6s ease 0.3s, transform 0.6s ease 0.3s;
}

.about-person.visible {
  opacity: 1;
  transform: translateY(0);
}

.about-avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  border: 2px solid rgba(255, 255, 255, 0.1);
  object-fit: cover;
}

.about-name {
  font-family: 'Instrument Sans', sans-serif;
  font-size: 16px;
  color: var(--text);
}

.about-badges {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
  justify-content: center;
}

.badge {
  font-family: 'Satoshi', sans-serif;
  font-size: 11px;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: var(--text-muted);
  padding: 6px 14px;
  border: 1px solid var(--separator);
  border-radius: 100px;
}
```

**Step 3: Add about scroll reveal JS**

```javascript
const aboutQuote = document.querySelector('.about-quote');
const aboutPerson = document.querySelector('.about-person');

const aboutObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
    }
  });
}, { threshold: 0.2 });

aboutObserver.observe(aboutQuote);
aboutObserver.observe(aboutPerson);
```

**Step 4: Verify in browser**

Quote scales up on scroll entry, person info fades in after.

**Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add about section with quote scale-up animation"
```

---

### Task 9: Contact and footer

**Files:**
- Modify: `index.html`

**Step 1: Add contact HTML**

```html
<section class="contact" id="contact">
  <p class="contact-line">Got a brand that needs to feel something? Let's talk.</p>
  <div class="contact-links">
    <a href="mailto:paolog00@icloud.com">paolog00@icloud.com</a>
    <a href="https://www.linkedin.com/in/paolo-giannitti-1bbb2b21b" target="_blank">LinkedIn</a>
    <a href="tel:+393298197813">+39 329 819 7813</a>
  </div>
</section>
```

NOTE: Contact line is a draft. Apply humanizer skill to finalize.

**Step 2: Add footer HTML**

```html
<footer>
  <span>&copy; 2026 Paolo Giannitti</span>
  <a href="#hero" class="back-to-top">&uarr; Top</a>
</footer>
```

**Step 3: Add contact and footer CSS**

```css
.contact {
  position: relative;
  z-index: 1;
  padding: 120px 24px;
  text-align: center;
}

.contact-line {
  font-family: 'Instrument Serif', serif;
  font-size: clamp(20px, 3vw, 28px);
  color: var(--text);
  margin-bottom: 32px;
}

.contact-links {
  display: flex;
  justify-content: center;
  gap: 32px;
  flex-wrap: wrap;
}

.contact-links a {
  font-family: 'Instrument Sans', sans-serif;
  font-size: 14px;
  color: var(--text-muted);
  text-decoration: none;
  transition: color 0.3s ease;
}

.contact-links a:hover {
  color: var(--text);
}

footer {
  position: relative;
  z-index: 1;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 24px 48px;
  border-top: 1px solid var(--separator);
  font-family: 'Instrument Sans', sans-serif;
  font-size: 13px;
  color: var(--text-dim);
}

.back-to-top {
  color: var(--text-dim);
  text-decoration: none;
  transition: color 0.3s ease;
}

.back-to-top:hover {
  color: var(--text);
}
```

**Step 4: Verify in browser**

Contact section with centered invitation line and links. Footer with copyright and back-to-top.

**Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add contact section and minimal footer"
```

---

### Task 10: Parallax depth and cursor glow

**Files:**
- Modify: `index.html`

**Step 1: Add parallax JS**

```javascript
// Parallax on hero name and section labels
const parallaxElements = document.querySelectorAll('.hero-name, .section-label');

window.addEventListener('scroll', () => {
  const scrollY = window.scrollY;
  parallaxElements.forEach(el => {
    const speed = 0.15;
    const rect = el.getBoundingClientRect();
    const offset = (rect.top + scrollY) * speed;
    el.style.transform = `translateY(${scrollY * speed - offset}px)`;
  });
});
```

Note: the parallax should be carefully tested and adjusted so it doesn't conflict with the fade-in transforms. May need to use a wrapper element for parallax.

**Step 2: Add cursor glow CSS and JS**

```css
.cursor-glow {
  position: fixed;
  width: 200px;
  height: 200px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(255,255,255,0.03) 0%, transparent 70%);
  pointer-events: none;
  z-index: 1;
  transform: translate(-50%, -50%);
  transition: opacity 0.3s ease;
}

@media (hover: none) {
  .cursor-glow { display: none; }
}
```

```javascript
const glow = document.createElement('div');
glow.className = 'cursor-glow';
document.body.appendChild(glow);

document.addEventListener('mousemove', (e) => {
  glow.style.left = e.clientX + 'px';
  glow.style.top = e.clientY + 'px';
});
```

**Step 3: Add reduced motion support**

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
  #grain { display: none; }
  .cursor-glow { display: none; }
  .philosophy-text .word { opacity: 1; }
  .project-row { opacity: 1; transform: none; }
  .about-quote { opacity: 1; transform: none; }
  .about-person { opacity: 1; transform: none; }
}
```

**Step 4: Verify in browser**

Check parallax feels subtle. Check cursor glow is barely visible. Check reduced motion disables everything.

**Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add parallax, cursor glow, and reduced-motion support"
```

---

### Task 11: Responsive design

**Files:**
- Modify: `index.html`

**Step 1: Add mobile breakpoints**

```css
@media (max-width: 768px) {
  nav {
    padding: 16px 24px;
  }

  nav.scrolled {
    padding: 12px 24px;
  }

  .work {
    padding: 60px 24px 80px;
  }

  .section-label {
    margin-bottom: 40px;
  }

  .project-row {
    padding: 32px 0;
  }

  /* Disable alternating slide on mobile - just fade up */
  .project-row,
  .project-row[data-direction="right"] {
    transform: translateY(20px);
  }

  .project-row.visible {
    transform: translateY(0);
  }

  /* Hide tooltips on mobile */
  .project-row::before {
    display: none;
  }

  .contact-links {
    flex-direction: column;
    gap: 16px;
  }

  footer {
    padding: 24px;
    flex-direction: column;
    gap: 8px;
    text-align: center;
  }

  .about-badges {
    gap: 8px;
  }
}
```

**Step 2: Verify on mobile viewport**

Use browser dev tools to check 375px, 768px, 1024px widths.

**Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add responsive breakpoints"
```

---

### Task 12: Smooth scroll and final polish

**Files:**
- Modify: `index.html`

**Step 1: Add smooth scroll for anchor links**

```javascript
document.querySelectorAll('a[href^="#"]').forEach(anchor => {
  anchor.addEventListener('click', function(e) {
    e.preventDefault();
    const target = document.querySelector(this.getAttribute('href'));
    if (target) {
      target.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
  });
});
```

**Step 2: Add skip link CSS**

```css
.skip-link {
  position: fixed;
  top: -100px;
  left: 16px;
  z-index: 200;
  background: var(--text);
  color: var(--bg);
  padding: 8px 16px;
  font-family: 'Instrument Sans', sans-serif;
  font-size: 14px;
  text-decoration: none;
  border-radius: 4px;
  transition: top 0.3s ease;
}

.skip-link:focus {
  top: 16px;
}
```

**Step 3: Final review**

- Check all links to case study pages still work
- Check all animations are smooth
- Check page weight (no unnecessary assets)
- Check that `paolo-photo.jpg` is referenced correctly
- Check no console errors

**Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add smooth scroll, skip link, and final polish"
```

---

### Task 13: Humanize all copy

**Files:**
- Modify: `index.html`

**Step 1: Review and humanize all text**

Using the humanizer skill (reference: /tmp/humanizer_extracted/humanizer/SKILL.md), review every piece of text on the page:

- Hero tagline
- Philosophy paragraph
- Project descriptions (all 4)
- About quote
- Contact invitation line

Apply humanizer principles:
- Remove AI patterns (no "tapestry", "landscape", "foster", "delve")
- Add voice and personality (direct storyteller tone)
- Vary rhythm (short punchy + longer flowing)
- Be specific, not vague
- No sycophantic tone, no hedging, no filler

**Step 2: Verify copy reads naturally aloud**

Read each piece of text out loud. If it sounds like a LinkedIn post or a press release, rewrite it.

**Step 3: Commit**

```bash
git add index.html
git commit -m "refine: humanize all copy for direct storyteller voice"
```

---

### Task 14: Cross-browser test and cleanup

**Files:**
- Modify: `index.html`
- Delete: `index-backup.html` (only after confirming new page works)

**Step 1: Test in browser**

Open in Chrome, check:
- Loader plays correctly
- Grain canvas performs well
- All scroll animations fire
- All links work (case study pages)
- Mobile responsive
- No console errors

**Step 2: Remove backup (after confirmation)**

```bash
git add index.html
git rm index-backup.html
git commit -m "cleanup: remove backup, redesign complete"
```
