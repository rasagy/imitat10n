# Imitat10n — Gallery Website Spec

## Overview

A single `index.html` file, fully self-contained, hosted on GitHub Pages. Showcases 16 student recreations of paintings as a fullscreen snap-scroll gallery. One assignment per screen. Museum-gallery aesthetic: light, warm, airy — with a sense of quiet elegance.

---

## Project Structure

```
/
├── index.html          ← entire site lives here
└── images/
    ├── painting-01.jpg
    ├── painting-02.jpg
    └── ... (16 images total)
```

No build tools, no npm, no server. Pure HTML + CSS + JS.

---

## Data Layer

All assignment data is hardcoded as a JavaScript array inside a `<script>` tag in `index.html`. This is derived from the CSV — paste it in once, and the page renders from it.

### CSV columns (source of truth)
```
student_name, artist_name, painting_name, image_name, url, doc_url, linkedin_url
```

### JS data structure inside the HTML
```js
const assignments = [
  {
    student_name: "Priya Sharma",
    artist_name: "Wassily Kandinsky",
    painting_name: "Composition VIII",
    image_name: "composition-viii.jpg",        // filename inside /images/
    url: "https://...",                         // interactive version link
    doc_url: "https://...",                     // documentation link (can be "" if none)
    linkedin_url: "https://linkedin.com/in/..."
  },
  // ... 16 entries
];
```

> **Note:** If `doc_url` is an empty string, the Documentation link should not render at all.

---

## Page Structure

### Slide 0 — Intro Card
The first thing the visitor sees. Full screen, no artwork image — just type and atmosphere.

**Content:**
- Course name: *Reimagining popular paintings in code*
- Part of Creative Coding module at NID, Bengaluru by [Rasagy Sharma](https://www.linkedin.com/in/rasagy/)
- What would design students create if they explore code as a creative medium? This is the exhibition of their final project: recreating and reimagining popular paintings by adding simple interactivity through code.
- A subtle scroll prompt: `Scroll to explore ↓`

**Visual:**
- Background: warm cream `#F5EFE4`
- A very faint paper-grain texture overlay (CSS noise using SVG filter or a repeating PNG)
- Large display typography, centered, with generous line-height
- The scroll prompt animates gently (slow fade pulse)

---

### Slides 1–16 — Assignment Cards
Each slide is exactly `100vh × 100vw`. One assignment per slide.

#### Desktop Layout
```
┌──────────────────────────────────────────────────────┐
│                    [cream margin]                    │
│        ┌──────────────────────────────────┐          │
│        │                                  │          │
│        │   [Painting — object-fit: cover] │          │
│        │   slightly offset to the right   │          │
│        │                                  │          │
│  ┌─────┴───────┐                          │          │
│  │ Painting    │                          │          │
│  │ Name        │    ← text panel          │          │
│  │ Artist Name │      left-anchored       │          │
│  │             │      overlaps image      │          │
│  │             │      bottom-left         │          │
│  │ Recreated   │                          │          │
│  │ by [Student]│                          │          │
│  │             │                          │          │
│  │ [View ↗]    │                          │          │
│  │ [Docs ↗]    │                          │          │
│  └─────────────┘          [03 / 16] ──→   │          │
│                    [cream margin]                    │
└──────────────────────────────────────────────────────┘
```

- Image container: `width: 72%`, pushed right with `margin-left: auto`, centered vertically
- Image has a **frame border** (see Frame spec below)
- Text panel: positioned absolute, bottom-left of the image, overlapping slightly — `backdrop-filter: blur(16px)` + `background: rgba(245, 239, 228, 0.88)`
- Slide counter `03 / 16`: bottom-right of the image, small, spaced tracking

#### Mobile Layout
```
┌──────────────────────┐
│   [cream margin]     │
│  ┌────────────────┐  │
│  │   [Painting]   │  │
│  │   centered     │  │
│  └────────────────┘  │
│  ┌────────────────┐  │
│  │  Painting Name │  │
│  │  Artist Name   │  │
│  │  By [Student]  │  │
│  │  [View ↗]      │  │
│  │  [Docs ↗]      │  │
│  └────────────────┘  │
│   [cream margin]     │
└──────────────────────┘
```

- Image: `width: 90%`, centered, full width minus margins
- Text panel: below the image, full width, no blur (just a light cream background), text centered
- Slide counter: below text panel, centered, small

---

## Image Frame (Fake Museum Frame)

The painting image gets a simple but elegant frame — enough to feel like a physical artwork, not a website screenshot.

```css
.painting-frame {
  border: 12px solid #C8B49A;        /* warm tan/brown */
  outline: 2px solid #A89070;        /* slightly darker outer rule */
  outline-offset: -14px;             /* inner inset rule for depth */
  box-shadow:
    0 4px 12px rgba(80, 50, 20, 0.18),
    0 12px 40px rgba(80, 50, 20, 0.10),
    inset 0 0 0 2px rgba(255,245,235,0.4);
}
```

- On mobile, reduce border to `8px`
- Frame color should feel like aged wood/gilt — use the warm brown tones from the palette
- No ornate corners — keep it flat and clean, just layered borders + shadow

---

## Navigation

### Dot Indicators (Right Edge)
- Vertical column of dots on the right side of the viewport, vertically centered
- 17 dots total (1 intro + 16 assignments)
- Inactive: small circle, `rgba(100, 70, 40, 0.25)`
- Active: larger circle, filled `#7A4F2D` (dark warm brown)
- Smooth `transform: scale` transition on active state
- On mobile: hidden (too cramped)

### Keyboard Support
- `ArrowDown` / `ArrowUp` → next / previous slide
- `PageDown` / `PageUp` → next / previous slide

### Touch / Swipe (Mobile)
- Swipe up → next slide
- Swipe down → previous slide
- Implemented via touch event listeners (`touchstart` + `touchend`, delta threshold ~50px)

---

## Typography

Avoid common fonts. Use distinctive, characterful pairings from Google Fonts.

| Role | Font | Style | Notes |
|---|---|---|---|
| Intro headline / Painting name | **Cormorant Garamond** | Bold, display size | High-contrast serif, feels like letterpress |
| Artist name / Subhead | **Cormorant Garamond** | Light Italic | Same family, different weight |
| Student name | **Jost** | Medium, tracked | Clean geometric, contrasts the serif beautifully |
| Buttons / Labels | **Jost** | Uppercase, letter-spacing: 0.15em | Refined label feel |
| Slide counter | **Jost** | Light, monospaced numbers | |

**Import tag:**
```html
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,600;1,300;1,400&family=Jost:wght@300;400;500&display=swap" rel="stylesheet">
```

> **Rationale:** Cormorant Garamond has an unusually high x-height contrast that looks genuinely editorial and different from Playfair. Jost is a geometric sans that reads at small sizes without being as utilitarian as Inter.

---

## Color Palette

Warm cream and dark brown — no grays.

| Token | Value | Use |
|---|---|---|
| `--color-bg` | `#F5EFE4` | Page background, intro slide |
| `--color-panel` | `rgba(245, 239, 228, 0.88)` | Text panel background (with blur) |
| `--color-text-primary` | `#2C1A0E` | Painting name, main headings |
| `--color-text-secondary` | `#7A5C3E` | Artist name, captions |
| `--color-accent` | `#7A4F2D` | Links, active dot, hover states |
| `--color-frame` | `#C8B49A` | Image frame border |
| `--color-frame-dark` | `#A89070` | Image frame outline / shadow |
| `--color-dot-inactive` | `rgba(100, 70, 40, 0.25)` | Inactive nav dots |
| `--color-counter` | `#A89070` | Slide counter text |

---

## Scroll & Snap Behavior

### CSS Snap
```css
.gallery-container {
  height: 100vh;
  overflow-y: scroll;
  scroll-snap-type: y mandatory;
  scroll-behavior: smooth;
}

.slide {
  height: 100vh;
  scroll-snap-align: start;
}
```

### IntersectionObserver (for enter animations)
- Each slide registers an `IntersectionObserver` with `threshold: 0.6`
- When a slide enters the viewport:
  1. Text panel fades up: `opacity 0→1`, `translateY 24px→0`, duration `500ms`, easing `cubic-bezier(0.22, 1, 0.36, 1)`
  2. Image scales in: `scale(1.04)→scale(1.0)`, duration `700ms`, same easing
  3. Dot indicator updates to active state
- When a slide exits: reset animation state (so re-entering replays it)

---

## Slide Content — Text Panel

### Fields rendered:
1. **Painting name** — large, `Cormorant Garamond Bold`
2. **Artist name** — italic, below painting name, `Cormorant Garamond Light Italic`
3. **"Recreated by"** label — small uppercase `Jost`, muted
4. **Student name** — `Jost Medium`, links to `linkedin_url`, opens in new tab, underline on hover
5. **"View Interactive"** button/link → opens `url` in new tab
6. **"Documentation"** link → opens `doc_url` in new tab — **only rendered if `doc_url !== ""`**

### Link / Button styling:
- Both links styled as inline text links, not buttons
- `Jost`, uppercase, letter-spacing, with a `→` or `↗` glyph
- `color: var(--color-accent)`
- Hover: underline slides in from left (CSS `border-bottom` + `transform` trick)
- Separated by a thin `|` divider or just vertical spacing

---

## Accessibility

| Concern | Solution |
|---|---|
| Image alt text | Generated: `"[Painting Name] by [Artist Name], recreated by [Student Name]"` |
| Links | `aria-label` on icon-only or glyph-heavy links |
| Keyboard nav | Arrow key handling via `keydown` listener |
| Focus management | Visible focus ring on all interactive elements; don't suppress `outline` |
| Contrast | All text/panel combinations checked against WCAG AA |
| Reduced motion | `@media (prefers-reduced-motion)` → disable scale/translateY animations, keep opacity only |

---

## Performance (16 Students)

- All `<img>` tags use `loading="lazy"`
- Preload the next slide's image using JS when current slide becomes active
- Recommend image size: **max 250KB per image** (compress before adding to repo — use Squoosh or ImageOptim)
- Google Fonts loaded with `display=swap` to avoid FOIT
- No external JS libraries — vanilla JS only

---

## Responsive Breakpoints

| Breakpoint | Behavior |
|---|---|
| `> 1024px` (desktop) | Image offset right (~72% width), text panel overlaid bottom-left, dot nav visible |
| `768px – 1024px` (tablet) | Image slightly offset, text panel below image or still overlaid, dot nav visible |
| `< 768px` (mobile) | Image centered (90% width), text panel below image, centered text, dot nav hidden, swipe enabled |

---

## Build Checklist (When You're Ready to Code)

- [ ] Paste all 16 entries into the `assignments[]` array
- [ ] Add intro slide content (course name, tagline)
- [ ] Drop all images into `/images/` folder, filenames matching `image_name` in data
- [ ] Verify all `url`, `doc_url`, and `linkedin_url` links are correct
- [ ] Test snap scroll on desktop Chrome, Safari, Firefox
- [ ] Test swipe on iOS Safari and Android Chrome
- [ ] Test keyboard navigation
- [ ] Check that empty `doc_url` entries hide the Documentation link
- [ ] Run images through compression (target < 250KB each)
- [ ] Push to GitHub, enable Pages on `main` branch → `/ (root)`

---

## Open Questions / Decisions Still To Make

1. **Assignment count** — Confirmed 16. Some students will have both `url` and `doc_url`, but some will only have `url`
3. **Painting aspect ratios** — Ratio will be mixed