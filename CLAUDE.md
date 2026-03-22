# CLAUDE.md — Levante Marketing

This file describes the codebase structure, conventions, and development workflow for AI assistants working on this repository.

## Project Overview

**Levante Marketing** is a single-founder performance marketing agency website. The site is intentionally minimal: a single-file static HTML website with no build system, no package manager, and zero runtime JavaScript dependencies.

- **Live stack:** HTML5 + embedded CSS3 + minimal vanilla ES5 JavaScript
- **Fonts:** Google Fonts (`Bebas Neue`, `DM Sans`) loaded via `<link>` in `<head>`
- **Booking integration:** Cal.com embedded modal (`jake-lubrano-cw7yuu/30min`)
- **No framework, no bundler, no transpilation**

---

## Repository Structure

```
levante-marketing/
├── index.html      # Entire website (~916 lines)
├── sitemap.xml     # SEO sitemap with page priorities
└── CLAUDE.md       # This file
```

Everything — HTML structure, CSS styles, and JavaScript — lives in `index.html`. There is no `src/` directory, no `package.json`, and no build step.

---

## index.html Architecture

### Head (lines 1–357)
- `<meta>` tags: charset, viewport, Open Graph, Twitter Cards, Google Search Console verification
- Google Fonts `<link>` preconnect/load
- Single `<style>` block containing all CSS (~275 lines)
- Cal.com embed script (inline `<script>`)
- Schema.org JSON-LD structured data for SEO
- Base64-encoded favicons and logo embedded as data URIs

### Body (lines 359–915)
Sections are hidden/shown via JavaScript `show()` — only one section is visible at a time.

| Section ID    | Content                                |
|---------------|----------------------------------------|
| `home`        | Hero / primary landing page            |
| `about`       | Founder bio and experience             |
| `realestate`  | Real estate marketing services         |
| `trades`      | Trades business marketing services     |
| `ecom`        | E-commerce marketing services          |
| `privacy`     | Privacy policy                         |

**Fixed/persistent UI elements (always visible):**
- `.cap-banner` — top "currently at capacity" banner
- `<nav>` — fixed navigation bar (desktop + mobile hamburger)
- `.mobile-nav` — full-screen mobile navigation overlay
- `.sticky-cta` — sticky bottom CTA that appears after the hero scrolls out of view

---

## CSS Conventions

### Design Tokens (CSS variables in `:root`)
```css
--gold:    #c9a96e    /* primary brand accent */
--gold2:   #dbbe8a    /* hover/lighter gold */
--black:   #0a0a0a    /* page background */
--card:    #111       /* card/surface background */
--border:  rgba(201,169,110,.15)   /* gold tint border */
--border2: rgba(255,255,255,.07)   /* subtle white border */
--white:   #fff
--dim:     rgba(255,255,255,.78)   /* secondary text */
--mid:     rgba(255,255,255,.38)   /* muted text */
--banner-h: 36px
--nav-h:    60px
```

### Responsive Breakpoints
- Mobile-first; desktop styles applied at `@media(min-width:769px)`

### Typography
- `clamp()` used throughout for fluid responsive font sizes
- Headings: `Bebas Neue` (display/uppercase)
- Body: `DM Sans` (300–600 weight range)

### Naming Conventions
- Flat BEM-like class names: `.cap-banner`, `.nav-desktop`, `.mobile-nav-cta`, `.sticky-cta`
- Section wrapper classes follow pattern: `.{section}-hero`, `.{section}-grid`, `.{section}-card`

---

## JavaScript Conventions

All JS is inline in `index.html`. The codebase is intentionally minimal ES5-compatible vanilla JS.

### Key Global Functions

| Function        | Purpose                                                    |
|-----------------|------------------------------------------------------------|
| `show(id)`      | Hides all sections, shows section with given ID, scrolls to top |
| `navTo(id)`     | Calls `show(id)` + closes mobile menu                      |
| `toggleMenu()`  | Opens/closes full-screen mobile nav overlay                |
| `toBook()`      | Opens Cal.com booking modal via `Cal.ns["30min"].open()`   |

### Event Handling
- All event handlers are inline `onclick` attributes in HTML
- Scroll listener uses `{passive: true}` for performance
- The sticky CTA appears/disappears based on `window.scrollY > heroBottom`

---

## Content & Copy Guidelines

- **Brand voice:** Premium, direct, capacity-constrained ("selective intake")
- **Color palette:** Dark luxury — near-black backgrounds, gold accents, white text
- **CTAs:** All booking actions call `toBook()` which opens Cal.com modal
- **Capacity messaging:** Site prominently communicates limited availability; do not remove or soften this messaging

---

## Development Workflow

### Making Changes
1. Edit `index.html` directly — there is no build step
2. Open in a browser to verify changes
3. Commit and push

### No Build Process
```bash
# No npm install, no build commands
# Just edit index.html and commit
git add index.html
git commit -m "describe your change"
git push -u origin <branch>
```

### Testing
There is no automated test suite. Manual browser testing across desktop and mobile (≥769px breakpoint) is the only verification method.

---

## SEO & Metadata

- `sitemap.xml` lists all navigable sections with priorities (`1.0` for home, `0.8` for service pages)
- Schema.org `LocalBusiness` + `ProfessionalService` JSON-LD is embedded in `<head>`
- Open Graph and Twitter Card meta tags are set for social sharing
- Google Search Console verified via `<meta name="google-site-verification">`

---

## External Integrations

| Service          | Purpose               | Config location                    |
|------------------|-----------------------|------------------------------------|
| Cal.com          | Booking modal         | Inline `<script>` in `<head>`; calendar slug `jake-lubrano-cw7yuu/30min` |
| Google Fonts     | Typography            | `<link>` in `<head>`               |

---

## Important Constraints

- **Do not introduce a build system or package manager** unless explicitly requested — the zero-dependency architecture is intentional
- **Do not split the file** into separate CSS/JS files without explicit instruction
- **Preserve all base64-encoded assets** (logo, favicons) — there are no external image files
- **Keep the capacity messaging** — the "currently at capacity" banner and related copy are intentional brand signals
- **Mobile-first** — always verify responsive behavior at the 769px breakpoint
