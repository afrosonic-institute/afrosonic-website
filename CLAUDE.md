# CLAUDE.md — Afrosonic Website
## Read this file before writing any code for this project.
## All decisions marked [PROPOSED] are confirmed direction but not yet signed off by the full team.
## All decisions marked [DECIDED] are locked and should not be changed without explicit instruction.

---

## WHAT THIS PROJECT IS

Building the MVP website for Afrosonic — a heritage music non-profit based in Abuja, Nigeria. Public name: Afrosonic. Legal name: IPAHM (Initiative for Preservation and Archiving of Historical Music).

The site is plain HTML, CSS, and minimal vanilla JavaScript. No frameworks. No build tools. No package.json. Files open directly in a browser and deploy to Vercel as a static site.

**The site's job:** Present Afrosonic as a preservation institution (not an events company) to three audiences simultaneously — event attendees on mobile, institutional readers (grant bodies, press), and community members.

---

## PAGES [DECIDED]

Seven pages. Build in this order:

1. `index.html` — Homepage
2. `rawa.html` — RAWA programme page
3. `editorial.html` — Magazine + interview series
4. `archive.html` — Oral history + physical archive
5. `about.html` — Team, mission, recognition
6. `support.html` — Volunteer, donate, partner
7. `film-club.html` — Film Club programme page

Navigation order: Home · RAWA · Film Club · Editorial · Archive · About · Support Us

Store page is scoped but NOT built in this phase. Do not create store.html.

---

## DESIGN SYSTEM [PROPOSED]

### Palette

```css
:root {
  --cream:        #F4EFE4;   /* primary background */
  --cream-alt:    #EAE4D6;   /* secondary background */
  --cream-deep:   #DED6C4;   /* tertiary, image placeholders */
  --ink:          #1C1814;   /* primary text */
  --ink-mid:      #6B6358;   /* body text, secondary */
  --ink-faint:    #9A9385;   /* captions, footnotes */
  --olive:        #6B6B28;   /* section labels, accents */
  --orange:       #C85A1A;   /* CTAs, pull quote borders, kickers */
  --brown:        #1E1006;   /* primary dark background — replaces charcoal */
  --rule:         #C8C0B0;   /* borders, dividers on light sections */
  --rule-dark:    rgba(244,239,228,0.08); /* dividers on dark sections */
}
```

Three-colour system for the dark register: cream + tobacco brown + burnt orange. This is intentional — it replicates the NTA broadcast palette (vintage Nigerian institutional aesthetic).

### Typography [PROPOSED]

```html
<!-- Paste in <head> of every page -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Serif:wght@700&family=Source+Serif+4:wght@400;600&family=IBM+Plex+Sans:wght@400;500&display=swap" rel="stylesheet">
```

```css
:root {
  --serif-display: 'IBM Plex Serif', Georgia, serif;     /* headings only, bold */
  --serif-body:    'Source Serif 4', Georgia, serif;      /* body text, articles */
  --sans:          'IBM Plex Sans', 'Helvetica Neue', sans-serif; /* nav, labels, buttons */
}
```

**Assignment rules:**
- `var(--serif-display)` + `font-weight: 700` → all h1, h2, h3, pull quotes
- `var(--serif-body)` + `font-weight: 400` → all body paragraphs, article prose, card descriptions
- `var(--serif-body)` + `font-weight: 600` → card titles, sub-headings within body text
- `var(--sans)` + `font-weight: 400/500` → nav items, section labels, buttons, input fields, captions

**Scale:**

```css
h1          { font-size: clamp(38px, 5.5vw, 72px); }
h2          { font-size: clamp(28px, 3.5vw, 48px); }
h3          { font-size: clamp(22px, 2.5vw, 32px); }
.body-text  { font-size: 14px; line-height: 1.8; }     /* mobile */
.label      { font-size: 10px; letter-spacing: 0.22em; text-transform: uppercase; font-family: var(--sans); }
```

At 768px+: body-text scales to 15px.

### Border Radius [PROPOSED]

```css
/* Global reset — no exceptions */
*, *::before, *::after {
  border-radius: 0 !important;
}
```

Sharp corners everywhere. Cards, buttons, inputs, images, programme cards, video cards. This is intentional and reflects the vintage print register. Do not add border-radius anywhere even if it looks harsh — the palette softens it.

### Section Separation — The "Floating" Effect [PROPOSED]

No scroll animations. No JavaScript entrance effects. No transforms. The visual weight of each section comes from:

1. Alternating background colours: `--cream` → `--cream-alt` → `--brown` → `--cream`
2. Generous padding: `80px` top and bottom on desktop, `56px` on mobile
3. Thin 1px rules between sections: `var(--rule)` on light, `var(--rule-dark)` on dark

```css
section {
  padding: 56px 24px;       /* mobile */
}
@media (min-width: 768px) {
  section {
    padding: 80px 60px;     /* desktop */
  }
}
```

If a card needs subtle lift: `box-shadow: 0 2px 4px rgba(30,16,6,0.15)` — warm brown tinted, 2–3px only. Use sparingly.

### Pattern Backgrounds [PROPOSED]

Amina Gimba's circular motifs used as repeating tile backgrounds via `::before` pseudo-element.

```css
.section-with-pattern {
  position: relative;
  background-color: var(--brown);
}
.section-with-pattern::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: url('/images/pattern-vinyl.png');
  background-repeat: repeat;
  background-size: 80px 80px;     /* mobile */
  opacity: 0.15;
  pointer-events: none;
  z-index: 0;
}
@media (min-width: 768px) {
  .section-with-pattern::before {
    background-size: 120px 120px; /* desktop */
  }
}
/* All direct children need position: relative; z-index: 1 to sit above the pattern */
```

Pattern assignment by section:

| Pattern file | Sections | Background | Opacity |
|---|---|---|---|
| `pattern-vinyl.png` | Hero, RAWA hero, newsletter | `--brown` | 12–18% |
| `pattern-vinyl.png` (subtle) | Programme cards area | `--cream` | 5–7% |
| `pattern-cd.png` | Credibility bar, recognition | `--cream-alt` or `--brown` | 8–12% |
| `pattern-cassette.png` | About header, origin story | `--cream-alt` | 5–8% |
| `pattern-logo.png` | Support Us, footer watermark | `--cream` or `--brown` | 5–8% |

Until Amina's actual PNG files arrive, generate inline SVG concentric circle patterns. Swap the `background-image` URL when the real files arrive — no other code changes needed.

---

## MOBILE-FIRST BUILD RULES [DECIDED]

Write CSS for mobile first. Use `min-width` media queries for larger screens.

**Three breakpoints:**
- Mobile: default (no query) — 320px–767px
- Tablet: `@media (min-width: 768px)`
- Desktop: `@media (min-width: 1200px)`

**Navigation:** hamburger menu on mobile. JavaScript toggle required (~10 lines). Logo top-left, burger icon top-right. All nav items hidden in a slide-down panel on mobile. Horizontal on tablet+.

**Layout grids — mobile → tablet → desktop:**
- Programme cards: 1 col → 2 col → 4 col
- Credibility bar: 2 col (number left, text right) → 3 col → 3 col
- Two-column splits (featured, about, mission): 1 col stacked → 2 col
- Footer: 1 col → 4 col

**CTA buttons:** stacked full-width on mobile, side by side on tablet+.

**Decorative elements** (large vinyl circle, off-canvas decorations): `display: none` on mobile. Visible on tablet+.

**Minimum tap target:** 44×44px on all interactive elements.

---

## FILE STRUCTURE [DECIDED]

```
afrosonic-website/
├── CLAUDE.md               ← this file
├── index.html
├── rawa.html
├── film-club.html
├── editorial.html
├── archive.html
├── about.html
├── support.html
├── style.css               ← shared across all pages
├── script.js               ← hamburger menu only
├── events.json             ← next event data (see JSON system below)
├── archive.json            ← oral history, RAWA editions, credibility figures
├── articles.json           ← editorial article publishing schedule
├── sitemap.xml
├── images/
│   ├── pattern-vinyl.png   ← from Amina (transparent background)
│   ├── pattern-cd.png
│   ├── pattern-cassette.png
│   ├── pattern-logo.png
│   ├── og-home.jpg         ← Open Graph images (1200×630px)
│   ├── og-rawa.jpg
│   ├── rawa-011-poster.webp
│   ├── rawa-010-poster.webp
│   └── [all images as WebP]
└── editorial/
    ├── copyright-disputes-nigeria.html
    ├── drum-magazine.html
    └── [one file per article]
```

---

## THE JSON CONTENT SYSTEM [PROPOSED]

Three JSON files control all frequently updated content. HTML pages read these files via `fetch()` and render content dynamically. One JSON edit + one GitHub push = live update across all affected pages.

### events.json — updated monthly by Senami

Controls the "next event" block on Home, RAWA, and Film Club pages.

```json
{
  "upcoming": {
    "rawa": {
      "name": "RAWA 012",
      "date": "2026-05-31",
      "date_display": "Saturday 31 May 2026",
      "venue": "Pickle Social House",
      "tixx_url": "https://tixx.africa/rawa-012",
      "poster_image": "/images/rawa-012-poster.webp"
    },
    "afc": {
      "name": "Afrosonic Film Club",
      "film": "[FILM TITLE]",
      "date": "2026-05-23",
      "date_display": "Saturday 23 May 2026",
      "venue": "Alien Prose Studios",
      "tixx_url": "https://tixx.africa/afc-may"
    }
  }
}
```

### archive.json — updated after each RAWA, each new interview, quarterly for figures

Controls: homepage credibility bar figures, homepage featured content block, archive page interview cards, RAWA page editions grid.

```json
{
  "credibility": {
    "rawa_editions": 11,
    "oral_history_interviews": 3,
    "articles_published": 26,
    "newsletter_subscribers": 450
  },
  "featured_content": {
    "type": "interview",
    "title": "Still Searching: Afrosonic meets Bongos Ikwue",
    "description": "We spent an afternoon at the home of Bongos Ikwue — composer, bandleader, and one of the most original voices in Nigerian music history.",
    "quote": "The day you try to change yourself, I think you're gone — because you can only be you.",
    "attribution": "Bongos Ikwue, speaking to Afrosonic",
    "youtube_url": "https://youtube.com/watch?v=xxx",
    "transcript_url": null,
    "thumbnail": "/images/bongos-thumb.webp"
  },
  "oral_history": [
    {
      "title": "Still Searching: Afrosonic meets Bongos Ikwue",
      "slug": "bongos-ikwue",
      "type": "interview",
      "subject": "Bongos Ikwue",
      "runtime": "54 minutes",
      "format": "Video",
      "date": "2024-09",
      "youtube_url": "https://youtube.com/watch?v=xxx",
      "transcript_url": null,
      "thumbnail": "/images/bongos-thumb.webp",
      "description": "Composer, bandleader, and one of the most original voices in Nigerian music history. He spoke about growing up in Jos, the Groovy Train, and sixty years of staying exactly himself."
    }
  ],
  "rawa_editions": [
    {
      "edition": 11,
      "name": "The Shrine Edition",
      "date": "2026-04-25",
      "date_display": "April 2026",
      "venue": "Pickle Social House",
      "poster": "/images/rawa-011-poster.webp",
      "print_available": false
    }
  ],
  "physical_recordings": []
}
```

### articles.json — set up once, maintained as articles are written

Controls the Editorial page article grid. Only articles with `date_published` <= today are rendered.

```json
{
  "articles": [
    {
      "title": "A History of Music Copyright Disputes in Nigeria",
      "slug": "copyright-disputes-nigeria",
      "type": "Archive",
      "date_published": "2026-05-12",
      "excerpt": "From Onyeka Onwenu's hunger strike at the NTA to Veno Marioghae's courtroom victory — Nigerian artists have had to fight publicly for rights that should have been automatic."
    }
  ]
}
```

---

## SEO REQUIREMENTS [DECIDED]

Every page must include in `<head>`:

```html
<title>[Page-specific title] · Afrosonic</title>
<meta name="description" content="[Under 160 chars, page-specific]">
<meta name="robots" content="index, follow">
<meta property="og:title" content="[Same as title tag]">
<meta property="og:description" content="[Same as meta description]">
<meta property="og:image" content="https://afrosonic.org/images/og-[page].jpg">
<meta property="og:url" content="https://afrosonic.org/[path]">
<meta property="og:type" content="website">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:image" content="https://afrosonic.org/images/og-[page].jpg">
```

- Use semantic HTML throughout: `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>`
- One `<h1>` per page only
- Every image needs `alt` attribute (empty string `alt=""` for decorative images)
- All interactive elements minimum 44×44px tap target

---

## ANALYTICS [PROPOSED]

Plausible Analytics recommended over GA4 (no cookie banner required, NDPA-friendly). Install before launch so data collection starts from the first visitor.

```html
<!-- Paste in <head> of every page once account is set up -->
<script defer data-domain="afrosonic.org" src="https://plausible.io/js/script.js"></script>
```

If GA4 is chosen instead, use the gtag snippet with the G-XXXXXXXXXX measurement ID.

---

## DEPLOYMENT [DECIDED]

- Platform: Vercel (free Hobby tier)
- Repository: GitHub (`afrosonic-website`)
- Auto-deploy: every push to `main` branch deploys automatically
- Domain: afrosonic.org — DNS change by Ezim (CNAME + A record pointing to Vercel)
- SSL: automatic via Vercel, no configuration needed
- Do not take down the current WordPress site until DNS propagation is confirmed

---

## CONTENT STATUS — KEY PLACEHOLDERS

These must be resolved before the relevant page goes live:

- RAWA editions 001–010: dates and venues — **NOW AVAILABLE** (Senami has this)
- AFC past screenings list: film titles and dates — **PENDING from Suté**
- Paystack donation link — **PENDING setup**
- Privacy notice — **PENDING drafting**
- Intern names (Onyeche + Abiola) on About page — **PENDING NDPA consent**
- Amina's pattern PNG files — **PENDING request**
- Analytics account setup — **PENDING platform decision**

---

## REFERENCE DOCUMENTS

All source documents are in the Claude project folder. In case of conflict, this file takes precedence over older documents.

- Full page copy: `afrosonic_site_copy_v2.md`
- Build spec and prompt templates: `afrosonic_website_build_may2026.md`
- Org context: `afrosonic_context_update_may2026.md`
- Ops reference: `ipahm_ops_may2026.md`
- Notion decision register: https://www.notion.so/senamizamba/Website-Build-Afrosonic-MVP-357b5af91dc98182a927c701ce231e3e

If you need to check the status of a specific decision, search the Notion register for the Area name. Domain 2 — Visual Design contains all typography, colour, and layout decisions.
