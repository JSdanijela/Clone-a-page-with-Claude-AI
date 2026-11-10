# CLAUDE.md

Rebuild a legacy marketing page as a clean static page whose sections can later be moved into the CMS "Custom Code" component.

## 1. Context

- **Reference page:** https://marketing.digistore24.com/einrichtungsservice (German).
- Only the public page is available.
- **Stack:** semantic HTML, modern vanilla CSS, vanilla JS only where HTML/CSS can't do the job. No frameworks (including Astro), no Tailwind, no jQuery, no build step. Don't install any tools or packages and don't add a `package.json`; only use what's already on this machine. The live site runs Astro + Sanity, but that's background only; don't scaffold it here.

## 2. Rules

1. All assets local. No tracking, analytics, cookie banners, external fonts or CDNs.
2. No page-builder leftovers: no generated classes, inline styles, empty elements or unused CSS/JS.
3. Copy stays verbatim. Log typos or broken content in `docs/notes.md` instead of fixing them.
4. Mobile-first and responsive.
5. **One section at a time** (see §10). Never edit an approved section while working on another one.
6. **Match the reference layout at every screen size.** At each of the reference's layouts (mobile ≤800px, desktop ≥801px; check at 375, 1024 and 1440px), the rebuild must have the same structure as the reference: element order, column count and arrangement, what is shown or hidden, and alignment. Measure the reference in the browser; don't guess. Fixing a bug in the original or meeting an accessibility rule is the only allowed reason to differ, and every difference goes in `docs/notes.md`.

## 3. Mistakes to avoid

- Div soup. Every wrapper must earn its place.
- Clickable `div`s. Navigates → `<a href>`; acts on the page → `<button type="button">`.
- Headings chosen by size or split in two (the original's "Wann ist der Komplett-Service / perfekt für dich?" is one heading).
- Lists not marked up as lists.
- Wrong `alt` text: decorative → `alt=""`, content → real description.
- Unnecessary ARIA where a native element already works.
- Invented content, links or images.
- Magic numbers instead of tokens; deep selectors; `!important`; fixed heights; `overflow: hidden` to hide bugs.
- Removing focus outlines; ignoring `prefers-reduced-motion`.
- Saying something is done or tested when it wasn't checked.

## 4. Files

```
index.html          # all sections' HTML, in one file
css/base.css        # shared tokens + shared primitives (e.g. CTA button)
css/page.css        # page shell only (body, stand-in header/footer); never migrated
css/blocks/<name>.css
js/blocks/<name>.js # only for sections that need JS
assets/img/
assets/manifest.json
docs/notes.md       # inventory, content issues
```

- All HTML lives in `index.html`, one `<section>` per block, marked with `<!-- cc-hero -->` … `<!-- /cc-hero -->`. No per-section HTML files or includes.
- Each section's CSS and JS live in their own files and only touch that section. Shared values come from `base.css`; if two sections need something, it goes there.

## 5. HTML

- `<html lang="de">`, one `<h1>` (hero), every other section starts with `<h2>`.
- Inside each `<section>`, one root element carries the block class: `<div class="cc-hero">`.
- Classes: `cc-` prefix, BEM-style (`.cc-faq__item`). JS hooks: `data-cc-*` attributes.
- Images: `width`, `height`, `alt`, `loading="lazy"` (except hero), `decoding="async"`.
- Element choices: lists → `<ul>`; testimonials → `<figure>`/`<blockquote>`/`<figcaption>`; FAQ → `<details>`/`<summary>` (no JS); CTAs → `<a href>`; price note and disclaimer → `<p>` with `<strong>`.

## 6. Assets

- Download only images actually shown. Rename to kebab-case. Optimise (WebP, max 2× rendered size) only with tools already on the machine; if none are available, keep the originals and note it.

## 7. CSS

- Fluid (`clamp()`, `auto-fit` grids) over many breakpoints.
- Logical properties, low specificity (`:where()`), visible `:focus-visible`, WCAG AA contrast.
- Tokens are declared on block roots in `base.css`:

```css
:where(.cc-hero, .cc-scope, .cc-fit, .cc-testimonials, .cc-faq, .cc-cta) {
  --cc-color-brand: #…;
  --cc-space-m: clamp(…);
}
```

- Each block root sets its own font, colour and line-height; no global reset.

## 8. Custom Code rules

**Scope:** these apply only to what's inside the `cc-` section roots, `base.css`, `css/blocks/` and `js/blocks/`. The page shell (`index.html` outside the section roots, and `page.css`) is a normal HTML page and uses `<head>`, `<link>`, `<script defer>`, `<body>`, `<main>` etc. as usual.

- **HTML:** no `<html>`, `<head>`, `<body>`, `<main>`, `<style>`, `<script>`, `<link>`, `<meta>`, `<title>`. The CMS wraps each block in its own `<section>`.
- **CSS:** no `html`, `body`, `:root`, `*` or bare element selectors. Every selector starts with the block's root class. Nothing affects anything outside the block.
- **JS:** runs as a classic script next to other blocks' scripts. Wrap in an IIFE, no globals, no inline `onclick`, query only inside the block root, initialise every instance of the block, guard against double init, don't rely on load order.

## 9. Build order

| #   | Section           | Content                                                                                  |
| --- | ----------------- | ---------------------------------------------------------------------------------------- |
| 0   | Groundwork        | Inspect page, write `docs/notes.md`, page shell, `base.css`, `page.css`, `manifest.json` |
| 1   | `cc-hero`         | `h1`, intro, CTA, disclaimer, illustration, trust badges                                 |
| 2   | `cc-scope`        | "Was wir für dich einrichten", price note, "Was dieser Service nicht umfasst"            |
| 2b  | `cc-hero-mobile`  | Mobile-only (≤800px) hero photo + trust badges, after `cc-scope`; hidden from 801px      |
| 3   | `cc-fit`          | "Wann ist der Komplett-Service perfekt für dich?", copy, CTA                             |
| 4   | `cc-testimonials` | 2 testimonials                                                                           |
| 5   | `cc-faq`          | 5 `<details>` items                                                                      |
| 6   | `cc-cta`          | Closing CTA                                                                              |

## 10. Each round

- **Preview:** always serve the project with `python3 -m http.server 8000` and open http://localhost:8000. Never load index.html as a file or data: URL, because relative paths break.

1. Build the section (assets, HTML, CSS, JS if needed).
2. Check it (I do the visual comparison, screenshots and keyboard checks myself; you measure the layout but don't judge how it looks):
   - **Headings:** list the page's headings and confirm one `h1` and no skipped levels.
   - **Scope:** read the section's CSS/JS files and confirm every selector starts with the section's root class, and nothing targets another section.
   - **Copy:** compare the section's text with the reference page word for word.
   - **Layout:** at 375, 1024 and 1440px, measure the reference with the browser tools: element order, columns, visibility, alignment. Compare against the rebuild's CSS (rule 2.6) and list every difference. If the rebuild can't be loaded in the browser, say so; don't claim the layout matches.
3. **Stop** and summarise: files changed, notable element choices, differences from the original, what was checked and what couldn't be (and why). List what I need to check myself.
4. Wait for an explicit "next" before starting the following section.

After the last section, run the same checks across the whole page.
