# Atlantic Career Web — CSS System Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the current scattered, duplicated CSS across all Atlantic career portal pages with a single systematic `atl-global.css` file and rewritten per-page HTML using the new `atl-` component classes.

**Architecture:** Layered global CSS (tokens → reset → typography → grid → components → utilities → language overrides) injected via SAP SuccessFactors Global CSS field. Per-page HTML is rewritten as custom code components with section comments. Farmacia page-specific styles (theme override + unique components) live in that page's custom code block only.

**Tech Stack:** Plain CSS (custom properties, CSS Grid, `clamp()`), vanilla JS for accordion and carousel, SAP SuccessFactors Career Site Builder. Local preview via Python HTTP server. No build tools.

---

## File Structure

```
css/
  atl-global.css          ← SAP Global CSS field (all pages)
pages/
  benefiti.html           ← SAP Custom Code component content, Benefiti page
  okusi-ukljucenosti.html
  okusi-dobrostanja.html
  okusi-izvrsnosti.html
  okusi-prizanja.html
  okusi-povezanosti.html
  rad-u-atlanticu.html
  farmacia.html           ← includes <style> block for Farmacia theme at top
  sezona.html
  unapredjivac-prodaje.html
  skladistar.html
  kako-zaposljavamo.html
preview.html              ← living style guide, local only
```

**SAP deployment note:** `css/atl-global.css` is copy-pasted into Career Site Builder → Global CSS. Each `pages/*.html` file is copy-pasted into the corresponding page's Custom Code component. The Farmacia `<style>` block at the top of `pages/farmacia.html` handles the page theme — it does NOT go into global CSS.

---

## Task 1: Project setup + preview skeleton

**Files:**
- Create: `preview.html`
- Create: `css/atl-global.css`

- [ ] **Step 1: Create project directories**

```bash
cd "C:/Users/slavk/OneDrive/Documents/Projects/Atlantic/Career Web"
mkdir css pages
```

- [ ] **Step 2: Create `css/atl-global.css` with section stubs**

```css
/* ============================================================
   ATLANTIC GRUPA — CAREER PORTAL
   Global CSS — injected via SAP Career Site Builder
   ============================================================ */

/* === TOKENS === */

/* === RESET === */

/* === TYPOGRAPHY === */

/* === GRID === */

/* === COMPONENTS === */

/* === UTILITIES === */

/* === LANGUAGE OVERRIDES === */
```

- [ ] **Step 3: Create `preview.html`**

```html
<!DOCTYPE html>
<html lang="hr-HR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Atlantic CSS Preview</title>
  <style>
    @font-face {
      font-family: 'custom1cdc72a1280c488ba8954';
      src: url('https://rmkcdn.successfactors.com/fbe9912e/1cdc72a1-280c-488b-a895-4.otf');
    }
    @font-face {
      font-family: 'custom7a590e3d1f3945ba91932';
      src: url('https://rmkcdn.successfactors.com/fbe9912e/7a590e3d-1f39-45ba-9193-2.otf');
    }
    @font-face {
      font-family: 'customa4cf9070d1c841e28b809';
      src: url('https://rmkcdn.successfactors.com/fbe9912e/a4cf9070-d1c8-41e2-8b80-9.otf');
    }
    /* Preview chrome — not part of atl-global.css */
    .preview-label {
      font-family: monospace; font-size: 11px; color: #999;
      text-transform: uppercase; letter-spacing: 1px;
      padding: 32px 0 8px; border-top: 1px solid #eee; margin-top: 32px;
    }
    .preview-label:first-child { border-top: none; }
    .preview-bg-dark { background: #333; padding: 32px; }
  </style>
  <link rel="stylesheet" href="css/atl-global.css">
</head>
<body>
  <p style="font-family:monospace;padding:16px;background:#f5f5f5;margin:0">
    Atlantic CSS Preview — resize window to test breakpoints
  </p>

  <!-- Components will be added here as tasks complete -->

</body>
</html>
```

- [ ] **Step 4: Start local preview server**

```bash
cd "C:/Users/slavk/OneDrive/Documents/Projects/Atlantic/Career Web"
python -m http.server 8080
```

Open http://localhost:8080/preview.html — should show the preview chrome bar.

- [ ] **Step 5: Commit**

```bash
git init
git add css/atl-global.css preview.html
git commit -m "feat: project scaffold and preview setup"
```

---

## Task 2: Tokens + Reset

**Files:**
- Modify: `css/atl-global.css`

- [ ] **Step 1: Add tokens to `css/atl-global.css`**

Replace the `/* === TOKENS === */` stub with:

```css
/* === TOKENS === */

:root {
  /* Colors */
  --atl-red:    #ED1C2B;
  --atl-dark:   #333333;
  --atl-bg:     #F5F5F5;
  --atl-border: #D2D5D7;
  --atl-white:  #FFFFFF;
  --atl-accent: #ED1C2B;

  /* Fonts — SAP platform registers these font-family names via @font-face */
  --atl-font-book:   'custom1cdc72a1280c488ba8954', sans-serif;
  --atl-font-medium: 'customa4cf9070d1c841e28b809', sans-serif;
  --atl-font-black:  'custom7a590e3d1f3945ba91932', sans-serif;

  /* Spacing scale (8px grid) */
  --atl-sp-1:  8px;
  --atl-sp-2:  16px;
  --atl-sp-3:  24px;
  --atl-sp-4:  32px;
  --atl-sp-6:  48px;
  --atl-sp-8:  64px;
  --atl-sp-12: 96px;
  --atl-sp-16: 128px;

  /* Layout */
  --atl-container-max: 1440px;
  --atl-gap: 32px;

  /* Breakpoints — reference comments only, cannot be used in @media */
  /* --atl-bp-md: 768px  (tablet)  */
  /* --atl-bp-lg: 1280px (desktop) */
}
```

- [ ] **Step 2: Add reset**

Replace the `/* === RESET === */` stub with:

```css
/* === RESET === */

*,
*::before,
*::after {
  box-sizing: border-box;
}

/* Override SAP/Bootstrap h1-h4 color injection */
h1, h2, h3, h4, h5, h6 {
  font-family: var(--atl-font-black);
  color: var(--atl-red);
  margin: 0;
}

img {
  max-width: 100%;
  display: block;
}

a {
  color: inherit;
}
```

- [ ] **Step 3: Add token smoke test to `preview.html`**

Add inside `<body>`, after the preview chrome bar:

```html
<div style="padding:32px">
  <p class="preview-label">Tokens — color swatches</p>
  <div style="display:flex;gap:8px;flex-wrap:wrap">
    <div style="width:80px;height:80px;background:var(--atl-red);border-radius:6px"></div>
    <div style="width:80px;height:80px;background:var(--atl-dark);border-radius:6px"></div>
    <div style="width:80px;height:80px;background:var(--atl-bg);border-radius:6px;border:1px solid #ddd"></div>
    <div style="width:80px;height:80px;background:var(--atl-border);border-radius:6px"></div>
    <div style="width:80px;height:80px;background:var(--atl-accent);border-radius:6px"></div>
  </div>
</div>
```

- [ ] **Step 4: Verify in browser**

Reload http://localhost:8080/preview.html.  
Expected: 5 colour swatches — red, dark grey, light grey, border grey, red (accent = red by default).

- [ ] **Step 5: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add design tokens and reset"
```

---

## Task 3: Typography

**Files:**
- Modify: `css/atl-global.css`
- Modify: `preview.html`

- [ ] **Step 1: Add typography to `css/atl-global.css`**

Replace `/* === TYPOGRAPHY === */` stub with:

```css
/* === TYPOGRAPHY === */

.atl-heading-1 {
  font-family: var(--atl-font-black);
  font-size: clamp(3rem, 6vw, 7.2rem);
  color: var(--atl-red);
  line-height: 1.05;
  margin: 0;
}

.atl-heading-2 {
  font-family: var(--atl-font-black);
  font-size: clamp(2.4rem, 4vw, 5rem);
  color: var(--atl-red);
  line-height: 1.1;
  margin: 0;
}

.atl-heading-3 {
  font-family: var(--atl-font-black);
  font-size: clamp(2rem, 3vw, 3.6rem);
  color: var(--atl-red);
  line-height: 1.15;
  margin: 0;
}

.atl-heading-4 {
  font-family: var(--atl-font-black);
  font-size: clamp(1.6rem, 2vw, 2.4rem);
  color: var(--atl-dark);
  line-height: 1.2;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  margin: 0;
}

.atl-subheading {
  font-family: var(--atl-font-medium);
  font-size: clamp(1.2rem, 1.5vw, 1.8rem);
  color: var(--atl-dark);
  line-height: 1.3;
  margin: 0;
}

.atl-lead {
  font-family: var(--atl-font-book);
  font-size: clamp(1.4rem, 2vw, 2.4rem);
  color: var(--atl-dark);
  line-height: 1.5;
  margin: 0;
}

.atl-body {
  font-family: var(--atl-font-book);
  font-size: clamp(1rem, 1.2vw, 1.6rem);
  color: var(--atl-dark);
  line-height: 1.7;
  margin: 0;
}

.atl-caption {
  font-family: var(--atl-font-book);
  font-size: 1.2rem;
  color: #666;
  line-height: 1.5;
  margin: 0;
}

.atl-label {
  font-family: var(--atl-font-medium);
  font-size: 1.1rem;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: var(--atl-dark);
  margin: 0;
}
```

- [ ] **Step 2: Add typography preview to `preview.html`**

Add after the token swatches section:

```html
<div style="padding:32px">
  <p class="preview-label">Typography</p>
  <div style="display:flex;flex-direction:column;gap:24px">
    <p class="atl-heading-1">Benefiti</p>
    <p class="atl-heading-2">Privlačenje i uključivost</p>
    <p class="atl-heading-3">Učenje i razvoj</p>
    <p class="atl-heading-4">Što je uvođenje u posao?</p>
    <p class="atl-subheading">Gradimo autentične lidere kroz mentorstvo i razvoj</p>
    <p class="atl-lead">Zaviri u našu bogatu smočnicu poslova, saznaj kako zapošljavamo i otkrij naš recept za uspjeh.</p>
    <p class="atl-body">U Atlantic Grupi vjerujemo da je razvoj zaposlenika temelj poslovnog uspjeha. Nudimo programe mentorstva, interne akademije i mogućnosti rotacije kroz različite poslovne jedinice.</p>
    <p class="atl-caption">– Ivančica Drča, farm. teh.</p>
    <p class="atl-label">Pročitaj više</p>
  </div>
</div>
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html. Check:
- ClanPro-Black renders for headings (bold, slightly condensed)
- ClanPro-Book renders for body/lead (lighter weight)
- Heading 1 is large and red, heading 4 is uppercase dark
- Resize window — font sizes scale fluidly with no jumps

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add typography system"
```

---

## Task 4: Grid

**Files:**
- Modify: `css/atl-global.css`
- Modify: `preview.html`

- [ ] **Step 1: Add grid to `css/atl-global.css`**

Replace `/* === GRID === */` stub with:

```css
/* === GRID === */

.atl-container {
  width: 100%;
  max-width: var(--atl-container-max);
  margin-left: auto;
  margin-right: auto;
  padding-left: var(--atl-gap);
  padding-right: var(--atl-gap);
  box-sizing: border-box;
}

.atl-row {
  display: grid;
  gap: var(--atl-gap);
  grid-template-columns: repeat(4, 1fr);
}

@media (min-width: 768px) {
  .atl-row {
    grid-template-columns: repeat(8, 1fr);
  }
}

@media (min-width: 1280px) {
  .atl-row {
    grid-template-columns: repeat(12, 1fr);
  }
}

/* Column spans */
.atl-col-1  { grid-column: span 1; }
.atl-col-2  { grid-column: span 2; }
.atl-col-3  { grid-column: span 3; }
.atl-col-4  { grid-column: span 4; }
.atl-col-6  { grid-column: span 6; }
.atl-col-8  { grid-column: span 8; }
.atl-col-12 { grid-column: span 12; }

/* Wider cols collapse to full width on mobile */
@media (max-width: 767px) {
  .atl-col-6,
  .atl-col-8,
  .atl-col-12 { grid-column: span 4; }
  .atl-col-3  { grid-column: span 4; }
}

/* Adjust spans on tablet */
@media (min-width: 768px) and (max-width: 1279px) {
  .atl-col-12 { grid-column: span 8; }
  .atl-col-6  { grid-column: span 4; }
  .atl-col-3  { grid-column: span 4; }
}

/* Sections */
.atl-section {
  padding-top: var(--atl-sp-12);
  padding-bottom: var(--atl-sp-12);
}

.atl-section--alt {
  padding-top: var(--atl-sp-12);
  padding-bottom: var(--atl-sp-12);
  background-color: var(--atl-bg);
}

.atl-section--flush {
  padding: 0;
}

/* Vertical stacks */
.atl-stack-2 { display: flex; flex-direction: column; gap: var(--atl-sp-2); }
.atl-stack-4 { display: flex; flex-direction: column; gap: var(--atl-sp-4); }
.atl-stack-6 { display: flex; flex-direction: column; gap: var(--atl-sp-6); }
.atl-stack-8 { display: flex; flex-direction: column; gap: var(--atl-sp-8); }
```

- [ ] **Step 2: Add grid preview to `preview.html`**

```html
<div style="padding:32px">
  <p class="preview-label">Grid — 2 col (col-6 + col-6)</p>
  <div class="atl-row">
    <div class="atl-col-6" style="background:#ED1C2B22;border:1px solid #ED1C2B66;padding:16px;border-radius:4px;text-align:center">col-6</div>
    <div class="atl-col-6" style="background:#33333322;border:1px solid #33333366;padding:16px;border-radius:4px;text-align:center">col-6</div>
  </div>
  <p class="preview-label" style="margin-top:24px">Grid — 3 col (col-4 × 3)</p>
  <div class="atl-row">
    <div class="atl-col-4" style="background:#ED1C2B22;border:1px solid #ED1C2B66;padding:16px;border-radius:4px;text-align:center">col-4</div>
    <div class="atl-col-4" style="background:#33333322;border:1px solid #33333366;padding:16px;border-radius:4px;text-align:center">col-4</div>
    <div class="atl-col-4" style="background:#ED1C2B22;border:1px solid #ED1C2B66;padding:16px;border-radius:4px;text-align:center">col-4</div>
  </div>
</div>
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html. Verify:
- Desktop (>1280px): 2-col shows side by side, 3-col shows 3 columns
- Tablet (768–1279px): 2-col still side by side (col-4 of 8), 3-col wraps to 2+1
- Mobile (<768px): everything is full width, stacked

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add grid system"
```

---

## Task 5: Components — atl-hero, atl-btn, atl-rule

**Files:**
- Modify: `css/atl-global.css`
- Modify: `preview.html`

- [ ] **Step 1: Add hero, button, and rule CSS**

Replace `/* === COMPONENTS === */` stub with:

```css
/* === COMPONENTS === */

/* ---- atl-hero ---- */

.atl-hero {
  position: relative;
  background-size: cover;
  background-position: center;
  min-height: 480px;
  display: flex;
  align-items: flex-end;
  padding: var(--atl-sp-8);
}

.atl-hero__content {
  max-width: 55%;
  display: flex;
  flex-direction: column;
  gap: var(--atl-sp-3);
}

@media (max-width: 767px) {
  .atl-hero {
    min-height: 320px;
    padding: var(--atl-sp-4);
  }
  .atl-hero__content {
    max-width: 100%;
  }
}

/* ---- atl-btn ---- */

.atl-btn {
  display: inline-block;
  font-family: var(--atl-font-medium);
  font-size: 16px;
  padding: 16px 32px;
  border-radius: 40px;
  border: none;
  cursor: pointer;
  text-decoration: none !important;
  line-height: 1;
  transition: opacity 0.2s ease;
}

.atl-btn:hover {
  opacity: 0.85;
}

.atl-btn--primary {
  background-color: var(--atl-accent);
  color: var(--atl-white);
}

.atl-btn--outline {
  background-color: transparent;
  color: var(--atl-dark);
  border: 2px solid var(--atl-dark);
}

.atl-btn--ghost {
  background-color: transparent;
  color: var(--atl-white);
  border: 2px solid var(--atl-white);
}

/* ---- atl-rule ---- */

.atl-rule {
  border: none;
  border-top: 1px solid var(--atl-border);
  margin: 0;
}
```

- [ ] **Step 2: Add preview sections to `preview.html`**

```html
<div style="padding:32px 0">
  <div class="preview-label" style="padding-left:32px">Hero banner</div>
  <div class="atl-hero" style="background-image: linear-gradient(to right, rgba(0,0,0,0.6) 40%, rgba(0,0,0,0.1)), url('https://rmkcdn.successfactors.com/fbe9912e/91ce96f5-197f-4355-860a-3.jpg')">
    <div class="atl-hero__content">
      <h1 class="atl-heading-1">Zdravlje i balans</h1>
      <a class="atl-btn atl-btn--ghost">Saznaj više</a>
    </div>
  </div>
</div>

<div style="padding:32px">
  <p class="preview-label">Buttons</p>
  <div style="display:flex;gap:16px;align-items:center;flex-wrap:wrap">
    <a class="atl-btn atl-btn--primary">Prijavi se</a>
    <a class="atl-btn atl-btn--outline">Saznaj više</a>
  </div>
  <div style="background:#333;padding:24px;margin-top:16px;border-radius:6px;display:flex;gap:16px">
    <a class="atl-btn atl-btn--ghost">Ghost button</a>
  </div>
  <p class="preview-label">Rule</p>
  <hr class="atl-rule">
</div>
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html. Check:
- Hero fills width, text sits at bottom left with gradient overlay
- On mobile: hero shorter, text fills full width
- Three button variants render correctly with correct fonts
- Rule is a thin grey line

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add hero, button, and rule components"
```

---

## Task 6: Component — atl-two-col

**Files:**
- Modify: `css/atl-global.css`
- Modify: `preview.html`

- [ ] **Step 1: Add two-col CSS** (append inside `/* === COMPONENTS === */`)

```css
/* ---- atl-two-col ---- */

.atl-two-col {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--atl-gap);
  align-items: center;
}

.atl-two-col img {
  width: 100%;
  height: auto;
}

.atl-two-col--reverse {
  direction: rtl;
}

.atl-two-col--reverse > * {
  direction: ltr;
}

@media (max-width: 767px) {
  .atl-two-col {
    grid-template-columns: 1fr;
  }
  .atl-two-col--reverse {
    direction: ltr;
  }
}
```

- [ ] **Step 2: Add two-col preview**

```html
<div class="atl-section">
  <div class="atl-container">
    <p class="preview-label">Two column — image + text</p>
    <div class="atl-two-col">
      <img src="https://rmkcdn.successfactors.com/fbe9912e/0fcc274c-6457-4bb1-ba1f-9.jpg" alt="">
      <div class="atl-stack-4">
        <h2 class="atl-heading-2">Privlačenje i uključivost</h2>
        <p class="atl-body">Vjerujemo da raznolikost donosi novu vrijednost. Naši programi uključivosti osiguravaju jednake prilike za sve Atlantikovce.</p>
        <div><a class="atl-btn atl-btn--primary">Read more</a></div>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html. Check:
- Desktop: image and text side by side, vertically centred
- Mobile: image stacks above text, both full width
- `atl-stack-4` creates correct vertical gap between heading, body, button

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add two-col component"
```

---

## Task 7: Component — atl-accordion

**Files:**
- Modify: `css/atl-global.css`
- Modify: `preview.html`

- [ ] **Step 1: Add accordion CSS** (append inside `/* === COMPONENTS === */`)

```css
/* ---- atl-accordion ---- */

.atl-accordion__item {
  border-bottom: 1px solid rgba(51, 51, 51, 0.2);
}

.atl-accordion__trigger {
  width: 100%;
  background: transparent;
  border: none;
  text-align: left;
  padding: 24px 0;
  font-family: var(--atl-font-black);
  font-size: clamp(1.4rem, 2vw, 2rem);
  color: var(--atl-dark);
  cursor: pointer;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.atl-accordion__trigger::after {
  content: '+';
  font-size: 2rem;
  line-height: 1;
  flex-shrink: 0;
}

.atl-accordion__trigger.is-open {
  border-bottom: 1px solid transparent;
}

.atl-accordion__trigger.is-open::after {
  content: '−';
}

.atl-accordion__panel {
  display: none;
  padding: 0 0 24px;
  font-family: var(--atl-font-book);
  font-size: clamp(1rem, 1.2vw, 1.6rem);
  line-height: 1.7;
  color: var(--atl-dark);
  border-bottom: 1px solid rgba(51, 51, 51, 0.2);
}

.atl-accordion__panel.is-open {
  display: block;
}
```

- [ ] **Step 2: Add accordion preview + JS**

```html
<div class="atl-section atl-section--alt">
  <div class="atl-container">
    <p class="preview-label">Accordion</p>
    <div class="atl-accordion">
      <div class="atl-accordion__item">
        <button class="atl-accordion__trigger is-open">Što je uvođenje u posao i zašto je važno?</button>
        <div class="atl-accordion__panel is-open">
          Svaki novi Atlantikovac dobiva Buddy mentora koji ga uvodi u kulturu i procese kompanije tokom prvih 90 dana.
        </div>
      </div>
      <div class="atl-accordion__item">
        <button class="atl-accordion__trigger">Tko je Buddy?</button>
        <div class="atl-accordion__panel">
          Buddy je iskusan kolega koji pomaže novom zaposleniku da se snađe u prvim tjednima rada.
        </div>
      </div>
      <div class="atl-accordion__item">
        <button class="atl-accordion__trigger">Kako izgleda onboarding plan?</button>
        <div class="atl-accordion__panel">
          Onboarding plan traje 90 dana i uključuje upoznavanje s kompanijom, timom i procesima rada.
        </div>
      </div>
    </div>
  </div>
</div>

<script>
  document.querySelectorAll('.atl-accordion__trigger').forEach(function(trigger) {
    trigger.addEventListener('click', function() {
      var panel = this.nextElementSibling;
      var isOpen = this.classList.contains('is-open');
      // Close all in same accordion
      var accordion = this.closest('.atl-accordion');
      accordion.querySelectorAll('.atl-accordion__trigger').forEach(function(t) {
        t.classList.remove('is-open');
        t.nextElementSibling.classList.remove('is-open');
      });
      // Open clicked if it was closed
      if (!isOpen) {
        this.classList.add('is-open');
        panel.classList.add('is-open');
      }
    });
  });
</script>
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html. Check:
- First item open by default, shows `−` icon
- Clicking a closed item opens it and closes the previous
- Clicking open item closes it
- Panel text uses ClanPro-Book, trigger uses ClanPro-Black

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add accordion component"
```

---

## Task 8: Component — atl-carousel

**Files:**
- Modify: `css/atl-global.css`
- Modify: `preview.html`

- [ ] **Step 1: Add carousel CSS** (append inside `/* === COMPONENTS === */`)

```css
/* ---- atl-carousel ---- */

.atl-carousel {
  position: relative;
  overflow: hidden;
}

.atl-carousel__track {
  display: flex;
  transition: transform 0.4s ease-in-out;
}

.atl-carousel__slide {
  flex: 0 0 100%;
  max-width: 100%;
  box-sizing: border-box;
  padding: var(--atl-sp-12) 10vw;
}

.atl-carousel__nav {
  position: absolute;
  bottom: var(--atl-sp-4);
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  justify-content: center;
  align-items: center;
  gap: var(--atl-sp-2);
}

.atl-carousel__prev,
.atl-carousel__next {
  background: transparent;
  border: none;
  font-size: 2rem;
  cursor: pointer;
  color: var(--atl-dark);
  padding: 4px 8px;
  line-height: 1;
}

.atl-carousel__dots {
  display: flex;
  gap: 8px;
}

.atl-carousel__dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #ccc;
  border: none;
  cursor: pointer;
  padding: 0;
}

.atl-carousel__dot.is-active {
  background: var(--atl-accent);
}
```

- [ ] **Step 2: Add carousel preview + JS**

```html
<div class="atl-section">
  <div class="preview-label" style="padding-left:32px">Carousel (testimonials)</div>
  <div class="atl-carousel" style="background:var(--atl-bg)">
    <div class="atl-carousel__track">
      <div class="atl-carousel__slide">
        <p class="atl-lead">"Farmacia mi je pružila prostor za stručni rast i razvoj koji nisam mogla pronaći nigdje drugdje."</p>
        <p class="atl-caption" style="margin-top:16px">– Lara Ismailovski, mag. pharm.</p>
      </div>
      <div class="atl-carousel__slide">
        <p class="atl-lead">"Svaki dan je nova prilika za učenje. Ovdje zaista osjećam da me kompanija podržava."</p>
        <p class="atl-caption" style="margin-top:16px">– Mirela Dimitrović, mag. pharm.</p>
      </div>
      <div class="atl-carousel__slide">
        <p class="atl-lead">"Atlantic mi je dao krila — razvila sam se od magistre farmacije do voditeljice tima."</p>
        <p class="atl-caption" style="margin-top:16px">– Ivančica Drča, farm. teh.</p>
      </div>
    </div>
    <div class="atl-carousel__nav">
      <button class="atl-carousel__prev">&#8592;</button>
      <div class="atl-carousel__dots"></div>
      <button class="atl-carousel__next">&#8594;</button>
    </div>
  </div>
</div>

<script>
  (function() {
    var track = document.querySelector('.atl-carousel__track');
    var slides = document.querySelectorAll('.atl-carousel__slide');
    var dotsWrapper = document.querySelector('.atl-carousel__dots');
    var current = 0;

    function render() {
      track.style.transform = 'translateX(-' + (current * 100) + '%)';
      dotsWrapper.querySelectorAll('.atl-carousel__dot').forEach(function(d, i) {
        d.classList.toggle('is-active', i === current);
      });
    }

    function buildDots() {
      slides.forEach(function(_, i) {
        var dot = document.createElement('button');
        dot.className = 'atl-carousel__dot';
        dot.addEventListener('click', function() { current = i; render(); });
        dotsWrapper.appendChild(dot);
      });
    }

    document.querySelector('.atl-carousel__prev').addEventListener('click', function() {
      current = (current - 1 + slides.length) % slides.length;
      render();
    });

    document.querySelector('.atl-carousel__next').addEventListener('click', function() {
      current = (current + 1) % slides.length;
      render();
    });

    buildDots();
    render();
  })();
</script>
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html. Check:
- Slides transition horizontally on prev/next click
- Dots render and update active state on navigation
- Clicking a dot navigates directly to that slide

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add carousel component"
```

---

## Task 9: Component — atl-cta

**Files:**
- Modify: `css/atl-global.css`
- Modify: `preview.html`

- [ ] **Step 1: Add CTA CSS** (append inside `/* === COMPONENTS === */`)

```css
/* ---- atl-cta ---- */

.atl-cta {
  background-color: var(--atl-bg);
  display: grid;
  grid-template-columns: 1fr 1fr;
}

.atl-cta__cell {
  padding: var(--atl-sp-8);
  display: flex;
  flex-direction: column;
  gap: var(--atl-sp-3);
  align-items: flex-start;
}

.atl-cta__cell + .atl-cta__cell {
  border-left: 1px solid var(--atl-border);
}

@media (max-width: 767px) {
  .atl-cta {
    grid-template-columns: 1fr;
  }
  .atl-cta__cell + .atl-cta__cell {
    border-left: none;
    border-top: 1px solid var(--atl-border);
  }
  .atl-cta__cell {
    padding: var(--atl-sp-6) var(--atl-sp-4);
  }
}
```

- [ ] **Step 2: Add CTA preview**

```html
<div class="preview-label" style="padding:32px 32px 8px">CTA banner</div>
<div class="atl-cta">
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Pregledaj otvorene pozicije</h3>
    <p class="atl-body">Pronađi svoju sljedeću priliku u Atlantic Grupi.</p>
    <a class="atl-btn atl-btn--primary">Browse jobs</a>
  </div>
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Prijavi se u bazu kandidata</h3>
    <p class="atl-body">Nema otvorene pozicije? Ostavi nam podatke.</p>
    <a class="atl-btn atl-btn--outline">Register</a>
  </div>
</div>
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html. Check:
- Two cells side by side on desktop, separated by thin grey line
- On mobile: cells stack vertically, separator becomes horizontal
- Both buttons align to left edge of their cell

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css preview.html
git commit -m "feat: add cta component"
```

---

## Task 10: Utilities + Language Overrides stub

**Files:**
- Modify: `css/atl-global.css`

- [ ] **Step 1: Add utilities**

Replace `/* === UTILITIES === */` stub with:

```css
/* === UTILITIES === */

.atl-center { text-align: center; }
.atl-left   { text-align: left; }
.atl-right  { text-align: right; }

.atl-color-red   { color: var(--atl-red) !important; }
.atl-color-dark  { color: var(--atl-dark) !important; }
.atl-color-white { color: var(--atl-white) !important; }

.atl-bg-alt   { background-color: var(--atl-bg); }
.atl-bg-white { background-color: var(--atl-white); }

.atl-hide-mobile {
  display: none;
}
@media (min-width: 768px) {
  .atl-hide-mobile  { display: revert; }
  .atl-hide-desktop { display: none; }
}

/* Link that fills its parent (e.g. card wrapper) */
.atl-fill-link {
  display: flex;
  height: 100%;
  width: 100%;
  text-decoration: none !important;
}
```

- [ ] **Step 2: Add language overrides stub**

Replace `/* === LANGUAGE OVERRIDES === */` stub with:

```css
/* === LANGUAGE OVERRIDES ===
   Use html[lang] selectors to scope layout changes to specific locales.
   Populated as differences are discovered across HR/EN/SL/SR.
   Example:
   html[lang="sl-SI"] .atl-hero__content { max-width: 65%; }
*/
```

- [ ] **Step 3: Verify in browser**

Reload http://localhost:8080/preview.html — no visual change expected. Open browser DevTools → Elements, confirm `atl-global.css` loads without errors.

- [ ] **Step 4: Commit**

```bash
git add css/atl-global.css
git commit -m "feat: add utilities and language override stub"
```

---

## Task 11: Farmacia — theme override + page-specific components

**Files:**
- Create: `pages/farmacia.html`

This task writes the Farmacia-specific CSS (theme + unique components). The actual page HTML structure is in Task 17.

- [ ] **Step 1: Create `pages/farmacia.html` with the theme block**

The `<style>` block at the top goes into Farmacia's custom code component in SAP.

```html
<style>
/* ============================================================ */
/* FARMACIA THEME — paste this style block at the top of the   */
/* Farmacia custom code component (before all HTML)             */
/* ============================================================ */

/* Override accent to Farmacia green */
:root {
  --atl-accent: #23BE55;
  --atl-pharma-green: #23BE55;
  --atl-pharma-grey: #F1F4F7;
}

/* ---- atl-intro (Farmacia hero) ---- */
.atl-intro {
  background: var(--atl-pharma-grey);
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 90vh;
  padding: 0;
  position: relative;
  overflow: hidden;
}

.atl-intro__container {
  width: 80vw;
  max-width: var(--atl-container-max);
  display: flex;
  align-items: stretch;
  gap: var(--atl-sp-8);
  height: 100%;
}

.atl-intro__content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: var(--atl-sp-2);
  padding: var(--atl-sp-12) 0;
}

.atl-intro__image {
  width: 50%;
  flex-shrink: 0;
  display: flex;
  align-items: flex-end;
}

.atl-intro__image img {
  width: 100%;
  height: auto;
  object-fit: contain;
}

@media (max-width: 1024px) {
  .atl-intro {
    min-height: auto;
    padding: var(--atl-sp-8) 0;
  }
  .atl-intro__container {
    flex-direction: column;
    align-items: center;
    width: 90vw;
  }
  .atl-intro__image {
    width: 100%;
  }
}

/* ---- atl-categories (2×2 grid of category links) ---- */
.atl-categories {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: var(--atl-sp-8);
  padding: var(--atl-sp-12) 10vw;
  max-width: var(--atl-container-max);
  margin: 0 auto;
}

@media (max-width: 767px) {
  .atl-categories {
    grid-template-columns: 1fr;
    padding: var(--atl-sp-8) var(--atl-sp-4);
  }
}

.atl-category {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  text-decoration: none !important;
  color: inherit;
  transition: transform 0.1s ease;
  gap: var(--atl-sp-4);
}

.atl-category:hover {
  transform: scale(1.02);
}

.atl-category img {
  width: 100%;
  max-width: 400px;
  height: auto;
}

.atl-category__title {
  font-family: var(--atl-font-black);
  font-size: clamp(1.8rem, 2.5vw, 3rem);
  color: var(--atl-pharma-green);
}

/* ---- atl-roles (image + text pairs) ---- */
.atl-roles {
  padding: var(--atl-sp-12) 0;
}

.atl-roles__container {
  max-width: 80vw;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: var(--atl-sp-12);
}

.atl-roles__item {
  display: flex;
  align-items: center;
  gap: var(--atl-sp-8);
}

.atl-roles__item img {
  width: 100%;
  max-width: 560px;
  height: auto;
}

.atl-roles__content {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: var(--atl-sp-3);
}

@media (max-width: 1024px) {
  .atl-roles__item {
    flex-direction: column;
  }
  .atl-roles__container {
    max-width: 90vw;
  }
}

/* ---- atl-apply (application CTA row) ---- */
.atl-apply-wrapper {
  background: var(--atl-pharma-grey);
  padding: var(--atl-sp-8) 0;
}

.atl-apply-divider {
  height: 1px;
  background: var(--atl-border);
  width: 80vw;
  margin: var(--atl-sp-4) auto;
}

.atl-apply {
  display: block;
  text-decoration: none !important;
  color: inherit;
}

.atl-apply__container {
  max-width: 80vw;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--atl-sp-8);
}

.atl-apply__text {
  font-family: var(--atl-font-black);
  font-size: clamp(2rem, 3vw, 4rem);
  color: var(--atl-dark);
  display: flex;
  flex-wrap: wrap;
  gap: var(--atl-sp-1);
}

.atl-apply__text .atl-apply__email {
  color: var(--atl-pharma-green);
  text-decoration: underline;
}

.atl-apply__arrow img {
  max-height: 64px;
  width: auto;
  transition: opacity 0.2s ease;
}

.atl-apply:hover .atl-apply__arrow img {
  opacity: 0.7;
}

@media (max-width: 767px) {
  .atl-apply__container {
    flex-direction: column;
    align-items: flex-start;
    max-width: 90vw;
  }
  .atl-apply__text { font-size: 2rem; }
}

/* ---- atl-pharma-logos ---- */
.atl-pharma-logos {
  padding: var(--atl-sp-8) 0 0;
}

.atl-pharma-logos__container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: var(--atl-sp-8);
  max-width: 80vw;
  margin: 0 auto;
}

.atl-pharma-logos__container img {
  max-height: 64px;
  width: auto;
}

@media (max-width: 767px) {
  .atl-pharma-logos__container {
    flex-direction: column;
    gap: var(--atl-sp-4);
  }
}
</style>

<!-- HTML sections added in Task 17 -->
```

- [ ] **Step 2: Commit**

```bash
git add pages/farmacia.html
git commit -m "feat: add Farmacia theme and page-specific components"
```

---

## Task 12: Page HTML — Benefiti

**Files:**
- Create: `pages/benefiti.html`

Copy the live page content from https://careers.atlanticgrupa.com/content/Benefiti/?locale=hr_HR and restructure using `atl-` classes.

- [ ] **Step 1: Create `pages/benefiti.html`**

```html
<!-- ============================================================ -->
<!-- PAGE: Benefiti                                               -->
<!-- SAP: Custom Code component                                   -->
<!-- ============================================================ -->

<!-- ============================================================ -->
<!-- SECTION: Hero Banner                                         -->
<!-- ============================================================ -->
<div class="atl-hero" style="background-image: url('https://rmkcdn.successfactors.com/fbe9912e/[HERO_IMAGE_ID].jpg')">
  <div class="atl-hero__content">
    <h1 class="atl-heading-1">Benefiti rada u Atlantic Grupi</h1>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Benefits grid — 5 benefit cards                    -->
<!-- ============================================================ -->
<div class="atl-section">
  <div class="atl-container">
    <div class="atl-row">

      <div class="atl-col-6">
        <div class="atl-two-col">
          <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
          <div class="atl-stack-4">
            <h2 class="atl-heading-2">Privlačenje i uključivost</h2>
            <p class="atl-body">[Excerpt text from live page]</p>
            <div><a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/content/Okusi-ukljucenosti/?locale=hr_HR">Pročitaj više</a></div>
          </div>
        </div>
      </div>

      <div class="atl-col-6">
        <div class="atl-two-col">
          <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
          <div class="atl-stack-4">
            <h2 class="atl-heading-2">Zdravlje i balans</h2>
            <p class="atl-body">[Excerpt text from live page]</p>
            <div><a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/content/Okusi-dobrostanja/?locale=hr_HR">Pročitaj više</a></div>
          </div>
        </div>
      </div>

      <div class="atl-col-6">
        <div class="atl-two-col">
          <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
          <div class="atl-stack-4">
            <h2 class="atl-heading-2">Učenje i razvoj</h2>
            <p class="atl-body">[Excerpt text from live page]</p>
            <div><a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/content/Okusi-izvrsnosti/?locale=hr_HR">Pročitaj više</a></div>
          </div>
        </div>
      </div>

      <div class="atl-col-6">
        <div class="atl-two-col">
          <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
          <div class="atl-stack-4">
            <h2 class="atl-heading-2">Priznanja i nagrade</h2>
            <p class="atl-body">[Excerpt text from live page]</p>
            <div><a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/content/Okusi-prizanja/?locale=hr_HR">Pročitaj više</a></div>
          </div>
        </div>
      </div>

      <div class="atl-col-6">
        <div class="atl-two-col">
          <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
          <div class="atl-stack-4">
            <h2 class="atl-heading-2">Mobilnost i povezanost</h2>
            <p class="atl-body">[Excerpt text from live page]</p>
            <div><a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/content/Okusi-povezanosti/?locale=hr_HR">Pročitaj više</a></div>
          </div>
        </div>
      </div>

    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: CTA                                                 -->
<!-- ============================================================ -->
<div class="atl-cta">
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Pregledaj otvorene pozicije</h3>
    <p class="atl-body">Pronađi svoju sljedeću priliku u Atlantic Grupi.</p>
    <a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/go/Sve-poslovne-mogucnosti/9208201/">Pogledaj poslove</a>
  </div>
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Prijavi se u bazu kandidata</h3>
    <p class="atl-body">Ostavi nam podatke i javi se kada se otvori prava pozicija.</p>
    <a class="atl-btn atl-btn--outline" href="[candidate database URL]">Ostavi podatke</a>
  </div>
</div>
```

**Note on image IDs:** Fetch from live page source at https://careers.atlanticgrupa.com/content/Benefiti/?locale=hr_HR — look for `backgroundimage` and `customPlugin` sections. Copy the `rmkcdn.successfactors.com/fbe9912e/` URLs directly.

- [ ] **Step 2: Verify structure**

Open `pages/benefiti.html` directly in a browser (or via `http://localhost:8080/pages/benefiti.html`). The page won't have styles (it references global CSS only when embedded in SAP) but the structure should be readable and all section comments present.

- [ ] **Step 3: Commit**

```bash
git add pages/benefiti.html
git commit -m "feat: rewrite Benefiti page HTML"
```

---

## Task 13: Page HTML — Okusi-* pages (6 pages)

**Files:**
- Create: `pages/okusi-ukljucenosti.html`
- Create: `pages/okusi-dobrostanja.html`
- Create: `pages/okusi-izvrsnosti.html`
- Create: `pages/okusi-prizanja.html`
- Create: `pages/okusi-povezanosti.html`

All 6 Okusi pages share the same structure: hero + repeated `atl-two-col` sections (image+text) + CTA. Content sourced from respective live pages.

- [ ] **Step 1: Create all 5 Okusi pages using this template**

For each page, replace `[PAGE_TITLE]`, `[HERO_IMAGE_ID]`, and `[CONTENT_SECTIONS]` with content from the live page at `https://careers.atlanticgrupa.com/content/[slug]/?locale=hr_HR`.

```html
<!-- ============================================================ -->
<!-- PAGE: [PAGE_TITLE]                                           -->
<!-- ============================================================ -->

<!-- ============================================================ -->
<!-- SECTION: Hero Banner                                         -->
<!-- ============================================================ -->
<div class="atl-hero" style="background-image: url('https://rmkcdn.successfactors.com/fbe9912e/[HERO_IMAGE_ID].jpg')">
  <div class="atl-hero__content">
    <h1 class="atl-heading-1">[PAGE_TITLE]</h1>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: [SECTION_NAME]                                      -->
<!-- ============================================================ -->
<div class="atl-section">
  <div class="atl-container">
    <div class="atl-two-col">
      <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
      <div class="atl-stack-4">
        <h2 class="atl-heading-2">[HEADING]</h2>
        <p class="atl-body">[BODY TEXT]</p>
      </div>
    </div>
  </div>
</div>

<!-- Repeat atl-section block for each content section -->

<!-- ============================================================ -->
<!-- SECTION: CTA                                                 -->
<!-- ============================================================ -->
<div class="atl-cta">
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Pregledaj otvorene pozicije</h3>
    <p class="atl-body">Pronađi svoju sljedeću priliku u Atlantic Grupi.</p>
    <a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/go/Sve-poslovne-mogucnosti/9208201/">Pogledaj poslove</a>
  </div>
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Prijavi se u bazu kandidata</h3>
    <p class="atl-body">Ostavi nam podatke i javi se kada se otvori prava pozicija.</p>
    <a class="atl-btn atl-btn--outline" href="[candidate database URL]">Ostavi podatke</a>
  </div>
</div>
```

- [ ] **Step 2: Commit**

```bash
git add pages/okusi-*.html
git commit -m "feat: rewrite Okusi pages HTML"
```

---

## Task 14: Page HTML — Rad-u-Atlanticu

**Files:**
- Create: `pages/rad-u-atlanticu.html`

Page structure: hero + values section (RAST, STRAST, BRIGA, OTVORENOST) + testimonials section + CTA.

- [ ] **Step 1: Create `pages/rad-u-atlanticu.html`**

```html
<!-- ============================================================ -->
<!-- PAGE: Rad u Atlanticu                                        -->
<!-- ============================================================ -->

<!-- ============================================================ -->
<!-- SECTION: Hero Banner                                         -->
<!-- ============================================================ -->
<div class="atl-hero" style="background-image: url('https://rmkcdn.successfactors.com/fbe9912e/[HERO_IMAGE_ID].jpg')">
  <div class="atl-hero__content">
    <h1 class="atl-heading-1">Kreatori ideja</h1>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Core values                                         -->
<!-- ============================================================ -->
<div class="atl-section">
  <div class="atl-container">
    <div class="atl-row">
      <div class="atl-col-6">
        <div class="atl-stack-4">
          <h2 class="atl-heading-2">RAST</h2>
          <p class="atl-body">[Value description from live page]</p>
        </div>
      </div>
      <div class="atl-col-6">
        <div class="atl-stack-4">
          <h2 class="atl-heading-2">STRAST</h2>
          <p class="atl-body">[Value description from live page]</p>
        </div>
      </div>
      <div class="atl-col-6">
        <div class="atl-stack-4">
          <h2 class="atl-heading-2">BRIGA</h2>
          <p class="atl-body">[Value description from live page]</p>
        </div>
      </div>
      <div class="atl-col-6">
        <div class="atl-stack-4">
          <h2 class="atl-heading-2">OTVORENOST</h2>
          <p class="atl-body">[Value description from live page]</p>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Employee testimonials                               -->
<!-- ============================================================ -->
<div class="atl-section atl-section--alt">
  <div class="atl-container">
    <h2 class="atl-heading-2 atl-center">Što kažu naši Atlantikovci</h2>
    <div class="atl-row" style="margin-top:48px">
      <!-- Repeat for each testimonial from live page -->
      <div class="atl-col-6">
        <div class="atl-stack-4">
          <p class="atl-lead">"[Quote from live page]"</p>
          <p class="atl-caption">– [Name, role]</p>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: CTA                                                 -->
<!-- ============================================================ -->
<div class="atl-cta">
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Pregledaj otvorene pozicije</h3>
    <p class="atl-body">Pronađi svoju sljedeću priliku u Atlantic Grupi.</p>
    <a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/go/Sve-poslovne-mogucnosti/9208201/">Pogledaj poslove</a>
  </div>
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Prijavi se u bazu kandidata</h3>
    <p class="atl-body">Ostavi nam podatke i javi se kada se otvori prava pozicija.</p>
    <a class="atl-btn atl-btn--outline" href="[candidate database URL]">Ostavi podatke</a>
  </div>
</div>
```

- [ ] **Step 2: Commit**

```bash
git add pages/rad-u-atlanticu.html
git commit -m "feat: rewrite Rad-u-Atlanticu page HTML"
```

---

## Task 15: Page HTML — Kako-zaposljavamo (accordion page)

**Files:**
- Create: `pages/kako-zaposljavamo.html`

Page structure: hero + intro two-col + accordion section + CTA + accordion JS at bottom.

- [ ] **Step 1: Create `pages/kako-zaposljavamo.html`**

```html
<!-- ============================================================ -->
<!-- PAGE: Kako zapošljavamo                                      -->
<!-- ============================================================ -->

<!-- ============================================================ -->
<!-- SECTION: Hero Banner                                         -->
<!-- ============================================================ -->
<div class="atl-hero" style="background-image: url('https://rmkcdn.successfactors.com/fbe9912e/[HERO_IMAGE_ID].jpg')">
  <div class="atl-hero__content">
    <h1 class="atl-heading-1">Kako postati Atlantikovac?</h1>
    <p class="atl-lead">Zaviri u našu bogatu smočnicu poslova, saznaj kako zapošljavamo i otkrij naš recept za uspjeh.</p>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Intro                                               -->
<!-- ============================================================ -->
<div class="atl-section">
  <div class="atl-container">
    <div class="atl-two-col">
      <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
      <div class="atl-stack-4">
        <h2 class="atl-heading-2">[Section heading from live page]</h2>
        <p class="atl-body">[Body text from live page]</p>
      </div>
    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Hiring process accordion                            -->
<!-- ============================================================ -->
<div class="atl-section atl-section--alt">
  <div class="atl-container">
    <h2 class="atl-heading-2" style="margin-bottom:48px">Naš proces zapošljavanja</h2>
    <div class="atl-accordion">

      <!-- Repeat .atl-accordion__item for each step from live page -->
      <div class="atl-accordion__item">
        <button class="atl-accordion__trigger">[Step title from live page]</button>
        <div class="atl-accordion__panel">[Step description from live page]</div>
      </div>

    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: CTA                                                 -->
<!-- ============================================================ -->
<div class="atl-cta">
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Pregledaj otvorene pozicije</h3>
    <p class="atl-body">Pronađi svoju sljedeću priliku u Atlantic Grupi.</p>
    <a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/go/Sve-poslovne-mogucnosti/9208201/">Pogledaj poslove</a>
  </div>
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Prijavi se u bazu kandidata</h3>
    <p class="atl-body">Ostavi nam podatke i javi se kada se otvori prava pozicija.</p>
    <a class="atl-btn atl-btn--outline" href="[candidate database URL]">Ostavi podatke</a>
  </div>
</div>

<!-- ============================================================ -->
<!-- SCRIPT: Accordion                                            -->
<!-- ============================================================ -->
<script>
  document.querySelectorAll('.atl-accordion__trigger').forEach(function(trigger) {
    trigger.addEventListener('click', function() {
      var panel = this.nextElementSibling;
      var isOpen = this.classList.contains('is-open');
      var accordion = this.closest('.atl-accordion');
      accordion.querySelectorAll('.atl-accordion__trigger').forEach(function(t) {
        t.classList.remove('is-open');
        t.nextElementSibling.classList.remove('is-open');
      });
      if (!isOpen) {
        this.classList.add('is-open');
        panel.classList.add('is-open');
      }
    });
  });
</script>
```

- [ ] **Step 2: Commit**

```bash
git add pages/kako-zaposljavamo.html
git commit -m "feat: rewrite Kako-zaposljavamo page HTML with accordion"
```

---

## Task 16: Page HTML — Sezona + sub-pages

**Files:**
- Create: `pages/sezona.html`
- Create: `pages/unapredjivac-prodaje.html`
- Create: `pages/skladistar.html`

- [ ] **Step 1: Create `pages/sezona.html`**

```html
<!-- ============================================================ -->
<!-- PAGE: Sezona                                                 -->
<!-- ============================================================ -->

<!-- ============================================================ -->
<!-- SECTION: Hero Banner                                         -->
<!-- ============================================================ -->
<div class="atl-hero" style="background-image: url('https://rmkcdn.successfactors.com/fbe9912e/[HERO_IMAGE_ID].jpg')">
  <div class="atl-hero__content">
    <h1 class="atl-heading-1">[Page title from live page]</h1>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Intro                                               -->
<!-- ============================================================ -->
<div class="atl-section">
  <div class="atl-container">
    <div class="atl-two-col">
      <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
      <div class="atl-stack-4">
        <h2 class="atl-heading-2">[Heading from live page]</h2>
        <p class="atl-body">[Body text from live page]</p>
      </div>
    </div>
  </div>
</div>

<!-- Add sections as per live page content -->

<!-- ============================================================ -->
<!-- SECTION: Role cards (Unapređivač + Skladištar links)         -->
<!-- ============================================================ -->
<div class="atl-section atl-section--alt">
  <div class="atl-container">
    <div class="atl-row">
      <div class="atl-col-6">
        <a class="atl-fill-link" href="https://careers.atlanticgrupa.com/content/Unapredjivac-prodaje/">
          <div class="atl-stack-4">
            <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
            <h2 class="atl-heading-2">Unapređivač prodaje</h2>
          </div>
        </a>
      </div>
      <div class="atl-col-6">
        <a class="atl-fill-link" href="https://careers.atlanticgrupa.com/content/Skladistar/">
          <div class="atl-stack-4">
            <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
            <h2 class="atl-heading-2">Skladištar</h2>
          </div>
        </a>
      </div>
    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: CTA                                                 -->
<!-- ============================================================ -->
<div class="atl-cta">
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Pregledaj otvorene pozicije</h3>
    <p class="atl-body">Pronađi svoju sljedeću priliku u Atlantic Grupi.</p>
    <a class="atl-btn atl-btn--primary" href="https://careers.atlanticgrupa.com/go/Sve-poslovne-mogucnosti/9208201/">Pogledaj poslove</a>
  </div>
  <div class="atl-cta__cell">
    <h3 class="atl-heading-3">Prijavi se u bazu kandidata</h3>
    <p class="atl-body">Ostavi nam podatke i javi se kada se otvori prava pozicija.</p>
    <a class="atl-btn atl-btn--outline" href="[candidate database URL]">Ostavi podatke</a>
  </div>
</div>
```

- [ ] **Step 2: Create `pages/unapredjivac-prodaje.html` and `pages/skladistar.html`**

Same structure as Sezona (hero + two-col sections + CTA). Content from:
- https://careers.atlanticgrupa.com/content/Unapredjivac-prodaje/
- https://careers.atlanticgrupa.com/content/Skladistar/

- [ ] **Step 3: Commit**

```bash
git add pages/sezona.html pages/unapredjivac-prodaje.html pages/skladistar.html
git commit -m "feat: rewrite Sezona and sub-pages HTML"
```

---

## Task 17: Page HTML — Farmacia (complete HTML sections)

**Files:**
- Modify: `pages/farmacia.html`

Farmacia is the most complex page. The `<style>` block (Task 11) is already there. This task adds all the HTML sections.

- [ ] **Step 1: Append HTML to `pages/farmacia.html`** (after the closing `</style>` tag)

Fetch content from https://careers.atlanticgrupa.com/content/Farmacia/?locale=hr_HR

```html
<!-- ============================================================ -->
<!-- SECTION: Intro hero                                          -->
<!-- ============================================================ -->
<div class="atl-intro">
  <div class="atl-intro__container">
    <div class="atl-intro__content">
      <h1 class="atl-heading-1" style="color:var(--atl-dark)">[Intro heading from live page]</h1>
      <p class="atl-lead">[Intro subtext from live page]</p>
    </div>
    <div class="atl-intro__image">
      <img src="https://rmkcdn.successfactors.com/fbe9912e/[INTRO_IMAGE_ID].jpg" alt="">
    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Pharma logos                                        -->
<!-- ============================================================ -->
<div class="atl-pharma-logos">
  <div class="atl-pharma-logos__container">
    <img src="https://rmkcdn.successfactors.com/fbe9912e/[LOGO_1_ID].png" alt="[Brand name]">
    <img src="https://rmkcdn.successfactors.com/fbe9912e/[LOGO_2_ID].png" alt="[Brand name]">
    <!-- Add logos from live page -->
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Categories grid                                     -->
<!-- ============================================================ -->
<div class="atl-categories">
  <a class="atl-category" href="[category URL]">
    <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
    <p class="atl-category__title">[Category name]</p>
    <button class="atl-btn atl-btn--primary">Saznaj više</button>
  </a>
  <!-- Repeat for all categories from live page -->
</div>

<!-- ============================================================ -->
<!-- SECTION: Job description                                     -->
<!-- ============================================================ -->
<section class="atl-section">
  <div class="atl-container">
    <div class="atl-stack-8">
      <h2 class="atl-heading-1" style="color:var(--atl-dark)">[Job section heading]</h2>
      <p class="atl-lead">[Description from live page]</p>
    </div>
  </div>
</section>

<!-- ============================================================ -->
<!-- SECTION: Benefits / perks grid                               -->
<!-- ============================================================ -->
<div class="atl-section atl-section--alt">
  <div class="atl-container">
    <h2 class="atl-heading-2" style="color:var(--atl-pharma-green)">[Benefits heading]</h2>
    <div class="atl-row" style="margin-top:48px">
      <!-- Two columns of benefit lists -->
      <div class="atl-col-6">
        <ul class="atl-stack-4" style="list-style:none;padding:0;margin:0">
          <li class="atl-body">[Benefit item from live page]</li>
          <!-- Repeat -->
        </ul>
      </div>
      <div class="atl-col-6">
        <ul class="atl-stack-4" style="list-style:none;padding:0;margin:0">
          <li class="atl-body">[Benefit item from live page]</li>
        </ul>
      </div>
    </div>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Roles                                               -->
<!-- ============================================================ -->
<div class="atl-roles">
  <div class="atl-roles__container">
    <div class="atl-roles__item">
      <img src="https://rmkcdn.successfactors.com/fbe9912e/[IMAGE_ID].jpg" alt="">
      <div class="atl-roles__content">
        <h3 class="atl-heading-3" style="color:var(--atl-pharma-green)">[Role name]</h3>
        <p class="atl-body">[Role description]</p>
      </div>
    </div>
    <!-- Repeat for each role -->
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Testimonial carousel                                -->
<!-- ============================================================ -->
<div class="atl-carousel" style="background:var(--atl-bg)">
  <div class="atl-carousel__track">
    <div class="atl-carousel__slide">
      <p class="atl-lead">"[Quote from live page]"</p>
      <p class="atl-caption" style="margin-top:16px">– [Name, title]</p>
    </div>
    <!-- Repeat for each testimonial -->
  </div>
  <div class="atl-carousel__nav">
    <button class="atl-carousel__prev">&#8592;</button>
    <div class="atl-carousel__dots"></div>
    <button class="atl-carousel__next">&#8594;</button>
  </div>
</div>

<!-- ============================================================ -->
<!-- SECTION: Apply CTA                                           -->
<!-- ============================================================ -->
<div class="atl-apply-wrapper">
  <div class="atl-apply-divider"></div>
  <a class="atl-apply" href="[job listing URL]">
    <div class="atl-apply__container">
      <div class="atl-apply__text">
        <span>[Application CTA text from live page — wrap email in:]</span>
        <span class="atl-apply__email">[email@atlanticgrupa.com]</span>
      </div>
      <div class="atl-apply__arrow">
        <img src="https://rmkcdn.successfactors.com/fbe9912e/[ARROW_ICON_ID].png" alt="">
      </div>
    </div>
  </a>
  <div class="atl-apply-divider"></div>
</div>

<!-- ============================================================ -->
<!-- SCRIPT: Carousel                                             -->
<!-- ============================================================ -->
<script>
  (function() {
    var track = document.querySelector('.atl-carousel__track');
    var slides = document.querySelectorAll('.atl-carousel__slide');
    var dotsWrapper = document.querySelector('.atl-carousel__dots');
    var current = 0;

    function render() {
      track.style.transform = 'translateX(-' + (current * 100) + '%)';
      dotsWrapper.querySelectorAll('.atl-carousel__dot').forEach(function(d, i) {
        d.classList.toggle('is-active', i === current);
      });
    }

    function buildDots() {
      slides.forEach(function(_, i) {
        var dot = document.createElement('button');
        dot.className = 'atl-carousel__dot';
        dot.addEventListener('click', function() { current = i; render(); });
        dotsWrapper.appendChild(dot);
      });
    }

    document.querySelector('.atl-carousel__prev').addEventListener('click', function() {
      current = (current - 1 + slides.length) % slides.length;
      render();
    });

    document.querySelector('.atl-carousel__next').addEventListener('click', function() {
      current = (current + 1) % slides.length;
      render();
    });

    buildDots();
    render();
  })();
</script>
```

- [ ] **Step 2: Commit**

```bash
git add pages/farmacia.html
git commit -m "feat: complete Farmacia page HTML with carousel"
```

---

## Task 18: Final review pass

**Files:**
- Review: `css/atl-global.css`
- Review: all `pages/*.html`

- [ ] **Step 1: Visual check — desktop**

Open http://localhost:8080/preview.html at full width (>1280px). Verify all components in sequence: hero → typography → grid → buttons → two-col → accordion → carousel → CTA.

- [ ] **Step 2: Visual check — tablet**

Resize browser to 900px wide. Verify:
- Grid columns adjust (col-6 → col-4 of 8, col-3 → col-4 of 8)
- Two-col stays two columns
- Carousel functions correctly

- [ ] **Step 3: Visual check — mobile**

Resize browser to 375px wide. Verify:
- All multi-column layouts stack to single column
- Hero text fills width
- CTA cells stack vertically with horizontal divider
- Accordion and carousel work correctly
- Buttons are tappable size

- [ ] **Step 4: Check SAP class conflicts**

Open https://careers.atlanticgrupa.com/content/Benefiti/?locale=hr_HR in DevTools. Search for any SAP-defined class that starts with `atl-`. Expected: none — SAP does not use the `atl-` prefix.

- [ ] **Step 5: Final commit**

```bash
git add -A
git commit -m "feat: complete Atlantic CSS system — ready for SAP deployment"
```

---

## SAP Deployment Checklist

After all tasks are complete, deploy in this order:

1. **Global CSS:** Copy contents of `css/atl-global.css` → SAP Admin → Career Site Builder → Global CSS → Save
2. **Per-page HTML:** For each page, copy `pages/[page].html` content → SAP → [Page] → Custom Code component → HTML tab → Save
3. **Farmacia:** Copy entire `pages/farmacia.html` including `<style>` block at top → Farmacia Custom Code component
4. **Test live:** Open each page on the live site, verify layout at desktop/tablet/mobile breakpoints
5. **Language check:** Open each page with `?locale=en_US` — confirm layouts hold, note any differences for language override section
