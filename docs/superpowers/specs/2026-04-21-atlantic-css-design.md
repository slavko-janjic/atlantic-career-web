# Atlantic Career Web — CSS System Design

**Date:** 2026-04-21  
**Status:** Approved  
**Pages in scope:** Benefiti, Okusi-* (6), Rad-u-Atlanticu, Farmacia, Sezona, Kako-zaposljavamo  
**Out of scope:** Header, SAP job listing components

---

## Context

The Atlantic Grupa career portal runs on SAP SuccessFactors Career Site Builder. Custom HTML/CSS is injected via "Custom Code" components on each page. A global CSS field is available and runs on all pages.

The current state has CSS duplicated across every page, 5+ breakpoints per component, magic padding numbers, two conflicting red color values, and a `#call { background: #f00 }` debug leftover.

---

## Architecture: Option B — Layered Global CSS

One global file (`atl-global.css`) organized in clearly commented layers. Farmacia page-specific styles go in that page's custom code component, not the global file.

### File layout

**`atl-global.css`** → SAP Admin → Career Site Builder → Global CSS

```
/* === TOKENS === */
/* === RESET === */
/* === TYPOGRAPHY === */
/* === GRID === */
/* === COMPONENTS === */
/* === UTILITIES === */
```

**Farmacia custom code block** (top of page, CSS only)

```css
/* Farmacia theme */
:root { --atl-accent: #23BE55; }

/* Page-specific components */
.atl-intro { ... }
.atl-categories { ... }
.atl-roles { ... }
.atl-apply { ... }
.atl-pharma-logos { ... }
```

**JS** (accordion, carousel) stays at the bottom of its own page's custom code component. No global JS file.

---

## Section 1: Tokens

```css
:root {
  /* Colors */
  --atl-red:    #ED1C2B;
  --atl-dark:   #333333;
  --atl-bg:     #F5F5F5;
  --atl-border: #D2D5D7;
  --atl-white:  #FFFFFF;
  --atl-accent: #ED1C2B; /* overridden per-page for themed pages */

  /* Fonts */
  --atl-font-book:   'ClanPro-Book',   sans-serif; /* custom1cdc72a1280c488ba8954 */
  --atl-font-medium: 'ClanPro-Medium', sans-serif; /* customa4cf9070d1c841e28b809 */
  --atl-font-black:  'ClanPro-Black',  sans-serif; /* custom7a590e3d1f3945ba91932 */

  /* Spacing scale (8px grid) */
  --atl-sp-1:  8px;
  --atl-sp-2:  16px;
  --atl-sp-3:  24px;
  --atl-sp-4:  32px;
  --atl-sp-6:  48px;
  --atl-sp-8:  64px;
  --atl-sp-12: 96px;
  --atl-sp-16: 128px;

  /* Breakpoints (reference only — cannot use vars in @media) */
  /* --atl-bp-md: 768px  (tablet) */
  /* --atl-bp-lg: 1280px (desktop) */
  --atl-container-max: 1440px;
  --atl-gap: 32px;
}
```

**Color consolidation (removes duplicates):**
- `#F4253D` → `#ED1C2B`
- `#2a2a2a`, `#444` → `#333333`
- `#F1F4F7`, `#f2f4f7` → `#F5F5F5`
- `#D9D8D8`, `#d8d8d8` → `#D2D5D7`

---

## Section 2: Typography

All heading classes use `clamp()` for fluid scaling — eliminates the 5-breakpoint font-size pattern currently on `.wide`, `.heading`, `.h3` etc.

| Class | Font | Size (clamp) | Color | Notes |
|---|---|---|---|---|
| `.atl-heading-1` | ClanPro-Black | clamp(3rem, 6vw, 7.2rem) | `--atl-red` | Page hero titles |
| `.atl-heading-2` | ClanPro-Black | clamp(2.4rem, 4vw, 5rem) | `--atl-red` | Section headings |
| `.atl-heading-3` | ClanPro-Black | clamp(2rem, 3vw, 3.6rem) | `--atl-red` | Sub-section headings |
| `.atl-heading-4` | ClanPro-Black | clamp(1.6rem, 2vw, 2.4rem) | `--atl-dark` | uppercase, letter-spaced |
| `.atl-subheading` | ClanPro-Medium | clamp(1.2rem, 1.5vw, 1.8rem) | `--atl-dark` | |
| `.atl-lead` | ClanPro-Book | clamp(1.4rem, 2vw, 2.4rem) | `--atl-dark` | Lead paragraphs |
| `.atl-body` | ClanPro-Book | clamp(1rem, 1.2vw, 1.6rem) | `--atl-dark` | Body text |
| `.atl-caption` | ClanPro-Book | 1.2rem (fixed) | `#666` | Author names, captions |
| `.atl-label` | ClanPro-Medium | 1.1rem, uppercase | `--atl-dark` | Tags, labels |

---

## Section 3: Grid

Mobile-first, CSS Grid. No Bootstrap dependency.

**Breakpoints:**
- Mobile default: < 768px → 4 columns
- Tablet: ≥ 768px → 8 columns  
- Desktop: ≥ 1280px → 12 columns
- Container max-width: 1440px (covers XL screens without an extra breakpoint)

**Classes:**
- `.atl-container` — max-width 1440px, centered, 32px horizontal padding
- `.atl-row` — CSS Grid, switches column count at breakpoints
- `.atl-col-{1,2,3,4,6,8,12}` — column spans; wider cols collapse to full width on mobile
- `.atl-section` — vertical section padding (96px top/bottom)
- `.atl-section--alt` — same + `--atl-bg` background
- `.atl-section--flush` — no padding (full-bleed images)
- `.atl-stack-{2,4,6,8}` — vertical gap between children
- `.atl-center` — text-align center
- `.atl-hide-mobile` / `.atl-hide-desktop` — visibility helpers

**Stacking rule:** 2 columns on desktop automatically stack to 2 rows on mobile via CSS Grid wrap — no extra CSS needed.

---

## Section 4: Components

### atl-hero
Replaces `.wide` (5 breakpoints, magic padding numbers).

```html
<div class="atl-hero" style="background-image: url('...')">
  <div class="atl-hero__content">
    <h1 class="atl-heading-1">Page Title</h1>
  </div>
</div>
```

### atl-two-col
Replaces `.twocol` + `.twocolumn` (duplicated on every page). Image left / text right, reverses on mobile.

```html
<div class="atl-section">
  <div class="atl-container">
    <div class="atl-two-col">
      <img src="..." alt="">
      <div>
        <h2 class="atl-heading-2">...</h2>
        <p class="atl-body">...</p>
        <a class="atl-btn atl-btn--primary">Read more</a>
      </div>
    </div>
  </div>
</div>
```

### atl-btn
Replaces `.btn` (was overriding Bootstrap). Three variants.

```html
<a class="atl-btn atl-btn--primary">Label</a>   <!-- red fill -->
<a class="atl-btn atl-btn--outline">Label</a>   <!-- dark border -->
<a class="atl-btn atl-btn--ghost">Label</a>     <!-- white border, for use on dark bg -->
```

**Sizing:** font-size 16px, padding 16px 32px, border-radius 40px, ClanPro-Medium.

### atl-accordion
Replaces `.accordion` + `.accpanel` + inline JS. JS stays in page custom code component.

```html
<div class="atl-accordion">
  <div class="atl-accordion__item">
    <button class="atl-accordion__trigger">Question</button>
    <div class="atl-accordion__panel">Answer</div>
  </div>
</div>
```

### atl-carousel
Replaces `.testimonial-carousel` + inline JS (Farmacia). JS stays in Farmacia custom code.

```html
<div class="atl-carousel">
  <div class="atl-carousel__track">
    <div class="atl-carousel__slide">
      <p class="atl-lead">Quote text</p>
      <p class="atl-caption">– Name, title</p>
    </div>
  </div>
  <div class="atl-carousel__nav">
    <button class="atl-carousel__prev">&#8592;</button>
    <div class="atl-carousel__dots"></div>
    <button class="atl-carousel__next">&#8594;</button>
  </div>
</div>
```

### atl-cta
Replaces `#call.twocol.cta.atlbg`. Removes the `#call { background: #f00 }` debug rule.

```html
<div class="atl-cta">
  <div class="atl-cta__cell">
    <p class="atl-heading-3">Job listings</p>
    <p class="atl-body">...</p>
    <a class="atl-btn atl-btn--primary">Browse jobs</a>
  </div>
  <div class="atl-cta__cell">
    <p class="atl-heading-3">Candidate database</p>
    <p class="atl-body">...</p>
    <a class="atl-btn atl-btn--outline">Register</a>
  </div>
</div>
```

### atl-rule
Replaces `.rule`.

```html
<hr class="atl-rule">
```

### Farmacia theme override
Page-specific CSS block (custom code component, top of Farmacia page).

```css
:root { --atl-accent: #23BE55; }
```

All accent-coloured elements (carousel dots, category headings, buttons) pick up green automatically.

---

## Section 5: HTML Page Structure

Each custom code component in SAP gets clearly commented sections:

```html
<!-- ============================================================ -->
<!-- SECTION: Hero Banner                                         -->
<!-- ============================================================ -->
...content...

<!-- ============================================================ -->
<!-- SECTION: Intro text                                          -->
<!-- ============================================================ -->
...content...
```

---

## Section 6: Language Variations

The platform sets `lang="hr-HR"` (or `en-US`, `sl-SI`, `sr-Latn-RS`) on the `<html>` element. Pages are mostly 1:1 across languages but some have minor layout differences (text length, image presence, section order).

**Approach:** Reserve the last section of `atl-global.css` for language overrides. Empty for now, filled in as differences are discovered.

```css
/* === LANGUAGE OVERRIDES === */
/* Use html[lang] selectors to scope changes to specific locales.   */
/* Example:                                                          */
/* html[lang="sl-SI"] .atl-hero__content { max-width: 60%; }       */
```

No separate file or system needed — SAP already provides the hook via the `lang` attribute.

---

## What Gets Removed

| Current | Reason |
|---|---|
| `#call { background: #f00 }` | Debug leftover |
| `.heading1` through `.heading5` | Replaced by `.atl-heading-1..4` |
| `.twocolumn` breakpoints (×5) | Replaced by CSS Grid auto-wrap |
| `.wide` breakpoints (×5) | `clamp()` handles it |
| `.atlred`, `.atldgray`, `.atlblack`, `.atlbg` | Replaced by token-based utilities |
| `.pad1`, `.pad2` | Replaced by `.atl-stack-*` / spacing tokens |
| `.alt` | Replaced by `.atl-section--alt` |
| All inline `font-family: custom...` style attributes | Typography classes handle it |
| Duplicate style blocks on every page | One global file |
