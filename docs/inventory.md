# Inventory: marketing.digistore24.com/einrichtungsservice

Captured 2026-10-19. Reference copy: `/tmp/ds24-ref/`
- `einrichtungsservice.html` is the raw page source (322 KB). Response headers are in `headers.txt`.
- `css/legacy.css` and `css/extensions.css` are the page-builder stylesheets.
- `assets/` holds every image, SVG and icon file the page uses.

The page is built in Digistore24's own page builder ("pgb", v28.4.1). It is a Vue SSR page that hydrates on load, uses a Bootstrap-style grid and loads legacy jQuery. Most of the markup is builder wrappers. The real content is small: one landing page, German copy, with a single call to action repeated five times.

---

## 1. Sections, top to bottom

Heights are at 1024px desktop width / 375px mobile width.

| # | Section | Background | Content | Notes |
|---|---|---|---|---|
| 0 | **Header bar** (inside the hero section) | black | Inverted Digistore24 logo on the left. White "Beratungsgespräch buchen" button on the right. | The bar has a 1px `rgba(255,255,255,.2)` border at the bottom. It is **not sticky**. |
| 1 | **Hero** | black | H1 "Umzugs- und Einrichtungsservice" (48px desktop / 42px mobile). Lead text at 30px. A small paragraph. The CTA "Kostenfreies Beratungsgespräch buchen →". A disclaimer: "Wichtig: Das ist ein technischer Einrichtungs-Service …". **Right column, desktop only:** a photo (`64cba59c07802_image.png`) overlaid with an **animated white "screen" mockup** (340×240) and a trust-badge strip (`64cba6155168c_trust.png`) below it. | The right column is hidden at ≤800px. At 1024px the H1 breaks mid-word ("Einrichtungsser-vice"), which is a bug in the original. |
| 2 | **"Was wir für dich einrichten"** | split: photo on the left (`64c26f31209fa_adobestock…png`, cover, 700px tall), white on the right | H2 (30px). A checklist of 4 items with ✔ icons and bold keywords: Vendor-Konto, 5 Produkte + Checkout-Seiten, Zahlungsarten + Fulfillment-Prozesse, Bestellbestätigungen + Lieferlogistik. A black CTA button. Price note: "*Einrichtungs-Service startet ab €699 …" | The row's height is fixed at 700px. On mobile the photo sits **behind** the heading, so the text overlaps the image (bug). |
| 3 | **"Was dieser Service nicht umfasst"** | split: white text on the left, photo on the right (`62c2beed0d862_image.png`) | H2 (36px). A list of 6 items with ✖ icons. | The layout is mirrored compared with section 2. On mobile the column order is `column-reverse`. |
| 3m | **Mobile-only image block** | – | The hero photo and trust strip again. | Shown only at ≤800px, as a replacement for the hero's right column. |
| 4 | **Photo band** | `#161515` + `62bedf17b89c6_image.png` (1440×580, cover) | No content. Purely decorative. | 640px tall, 235px on mobile. Uses a negative bottom margin (`-75px`) so the next section overlaps it. |
| 5 | **"Wann ist der Komplett-Service perfekt für dich?"** | `#161515` | A big H2 "Wann ist der Komplett-Service" (120px desktop / 50px), then "perfekt für dich?" (36px). Two centered paragraphs in grey `#b2b2b2`. A white CTA. | Everything in this section is centered. |
| 6 | **Testimonials: "Erfolgreiche Influencer & ihre Agenturen"** | white | H2 on the left. Two **black cards** on the right. Each card has a photo, a quote-mark icon, the quote, a name (bold) and a role. Card 1: Dr. med. Mareike Awe. Card 2: Dul Seo (OMR Jobs). | The cards are static, with no slider. Swiper CSS is loaded but **never used**. |
| 7 | **Photo band** | `62c2c37bb7d51_group-1030.png` (1440×580) | No content. | Same pattern as section 4. |
| 8 | **FAQ** | white | "FAQ" (120px) and "Häufige Fragen" on the left. A 5-item accordion on the right (question 21px, answer 18px, `+`/`−` icons, `#E6E9EC` dividers). | All items start collapsed. |
| 9 | **Photo band and final CTA** | `62cb46cd785bd_group-1034.png` (1440×700), with black below | "Starte jetzt mit" (36px), then the **"MORE SALES. LESS WORK."** wordmark (`62c2c72a89542_title.svg`, 884×226, white paths), then a white CTA. | The band uses `margin-bottom:-260px` so the CTA sits on top of the photo. |
| 10 | **Footer** | black | "© 2026 Digistore24 GmbH, alle Rechte vorbehalten" in `#b2b2b2`, 14px. Links: Datenschutz · Impressum · AGB. | The footer nav is a builder "OcbNavigation" with a CSS-checkbox burger menu, but the burger is `display:none` at every width. |

Hidden or unused markup that we will **not** carry over:
- A builder **Popup/modal**. It is empty and nothing triggers it.
- A hidden `<button id="typeFormBtn" data-tf-popup="yGFTaOQT" style="display:none">Launch me</button>`.
- A cookie banner config with `cookieConsent:false`, so it never renders.
- An affiliate badge with `active:false`.
- About 400 lines of site-wide custom CSS for other pages (pricing tables, "300+ winners", video buttons, Sven signature, and so on).

## 2. Interactive behavior

| Behavior | How it works today | Needs JS? |
|---|---|---|
| **FAQ accordion** (5 items) | Vue widget. Clicking toggles `display:none` and swaps the `+`/`−` icon. Several items can be open at once. | **No.** Use `<details>/<summary>`, with optional CSS animation. |
| **Hero "screen" animation** | Pure CSS. An 8s infinite loop with 3 slides: (1) a progress bar fills, (2) a 4×2 grid of product tiles gets ✔ success badges one by one, (3) a line chart draws itself via `stroke-dashoffset`, with a legend and an earnings block. About 30 `@keyframes`. | **No.** Pure CSS. Add `prefers-reduced-motion`. |
| **CTA buttons** (×5) | Plain `<a href="https://digistore24.typeform.com/to/jg2QdVwY">`. Same tab, no `target`. Typeform's `embed.js` is loaded, but no visible element uses it. | **No.** Plain links. |
| **Link hover** | `opacity .8` with a 0.3s transition on all links and buttons. | No. |
| **Lazy images** | The builder swaps `data-src` in for a placeholder SVG via JS. Photo-band backgrounds are lazy-loaded too. | **No.** Use native `loading="lazy"` and `decoding="async"`. |
| **Page background** | `background-attachment: fixed` on the builder wrappers. Visually it is just black or white. | No. Drop it, because it causes jank on iOS. |
| **Desktop/mobile variants** | `.pgb-desktop-only` and `.pgb-mobile-only` toggle at 800/801px. | No. Media queries. |

## 3. Fonts

| Family | Source | Used for |
|---|---|---|
| **HelveticaNeueLTPro-Md** | Self-hosted woff2/woff on `cch-files.edge.live.ds25.io` | Applied with `!important` to h1–h6, p, li, span, button and buttons. This is effectively the **only body/heading face**. |
| **HelveticaNeueLTPro-Bd** | same | `<strong>` (checklist keywords, testimonial names) |
| HelveticaNeueLTPro-Lt | same | Declared but not used on this page |
| Roboto 400/700 | `/assets/pgb/fonts/…` (builder default) | Fallback only. FAQ items inherit `--pgb-default-content-font`. |
| FontAwesome 4 | `/assets/pgb/page/fonts/fontawesome-webfont.woff2` | ✔ / ✖ list bullets, FAQ `+`/`−` icons. Replace with inline SVG. |

`@import url("//hello.myfonts.net/count/3be631")` is the **MyFonts licence page-view counter** for Helvetica Neue LT Pro.

Type rules: a global `line-height: 100%` on headings, p and span, with `120%`/`1.2em` for long paragraphs. Antialiased font smoothing. No letter-spacing tweaks.

Observed sizes (desktop): 120 (big display H2s), 48 (H1), 36, 30, 21 (list items, FAQ, line-height 1.5), 18, 16 (buttons), 14 (small print).

## 4. Main colors

| Token | Value | Use |
|---|---|---|
| black | `#000000` | Page background, hero, footer, testimonial cards, primary text on white |
| near-black | `#161515` | The "Komplett-Service" section and the first photo band's fallback |
| white | `#FFFFFF` | Text on dark, light sections, buttons on dark |
| grey text | `#B2B2B2` | Secondary text on dark (paragraphs, footer) |
| white 80% | `rgba(255,255,255,.8)` | Muted text on dark |
| white 20% | `rgba(255,255,255,.2)` | Header divider, progress-bar track |
| divider | `#E6E9EC` | FAQ item borders |
| mockup greys | `#F2F2F2`, `#D9D9D9` | Hero animation tiles and chart |

The palette is **strictly monochrome** and has no brand accent color.

Buttons:
- **On dark:** white background, black text, 16px, no border-radius.
- **On light:** black background, white text.
- **Padding:** `23px 50px 21px 20px`, or `17px 16px 15px` in the header.
- **Arrow:** a `→` SVG placed via `::after` (`62ce8c4eb8b3a_arrow.svg` is white, `62ce8ecbada87_arrow.svg` is black).

## 5. Breakpoints

| Breakpoint | Where it's used |
|---|---|
| **≤800 / ≥801px** | **Main switch.** Desktop-only and mobile-only blocks, columns stack, section side padding goes to 0, buttons go full width. |
| 768 / 992 / 1200px | Bootstrap grid (`col-sm/md/lg`) inside the builder |
| 600–801px, 801–1000px | Custom tweaks: drop the heading background image, stack the full-width image sections |
| ≤600, ≤400, ≤388, ≤320px | Small max-width fixes on specific text blocks |
| ≤1550px | Hero image margin reset |

Proposed simplified set for the rebuild: **mobile-first, with 600 / 800 / 1200px**, and fluid type via `clamp()` in between.

## 6. Images and icons

All of these are on `https://cch-files.edge.live.ds25.io/cch/v/c743e4a3-57b9-11ea-9823-4201c0a80011/files/` and are copied to `/tmp/ds24-ref/assets/`.

| File | Size | Use |
|---|---|---|
| `62b97c807e613_digistore24-logo-inv.svg` | 169×30 | Header logo (white) |
| `644b824d987cf_…-favicon.svg` | – | Favicon and apple-touch-icon |
| `64cba59c07802_image.png` | 660×488, 455 KB | Hero photo (two people with a tablet) |
| `64cba6155168c_trust.png` | 996×196, 54 KB, RGBA | Trust badges strip (awards, TÜV, Trustpilot, Made in Germany) |
| `64c26f31209fa_adobestock-127110878-preview-1.png` | 720×720, 870 KB | Section 2 photo |
| `62c2beed0d862_image.png` | 720×700, 337 KB | Section 3 photo (woman at a podcast mic) |
| `62bedf17b89c6_image.png` | 1440×580, 880 KB | Photo band 1 |
| `62c2c37bb7d51_group-1030.png` | 1440×580 | Photo band 2 |
| `62cb46cd785bd_group-1034.png` | 1440×700 | Photo band 3 (team at a table, behind the final CTA) |
| `673c5634e4d5e_whatsapp-image-…png` | 848×526, 465 KB | Testimonial 1 photo |
| `64c8fbb1f17fc_seo-dul-1.png` | 425×260, 125 KB | Testimonial 2 photo |
| `64c271c17fdd9_quote-marks.svg` | 63×63 | Quote icon on the testimonial cards |
| `62c2c72a89542_title.svg` | 884×226 | "MORE SALES. LESS WORK." wordmark |
| `62ce8c4eb8b3a_arrow.svg`, `62ce8ecbada87_arrow.svg` | 19×14 | Button arrows (white / black) |
| *(inline SVG)* | – | Hero animation ✔ badge and line chart |
| *(FontAwesome)* | – | ✔ check, ✖ times, `+` plus, `−` minus |

All `<img>` elements have **empty `alt`** attributes. The photos are PNG and heavy, about **4.5 MB in total**, so they should be converted to AVIF/WebP at 1x and 2x.

## 7. Third-party requests and scripts

| Request | Type | Needed for the rebuild? |
|---|---|---|
| `cch-files.edge.live.ds25.io` (13+ requests) | Images and fonts (Digistore24's CDN) | Self-host the assets in the rebuild (see risks) |
| `hello.myfonts.net/count/3be631` | MyFonts licence tracking pixel (CSS `@import`) | **Licence question.** It is probably required if we keep using Helvetica Neue LT Pro. |
| `embed.typeform.com/next/embed.js` and `/next/css/popup.css` | Typeform embed SDK, loaded render-blocking in the body | **No.** Its only consumer is the hidden "Launch me" button. The CTAs are plain links. |
| `unpkg.com/swiper/swiper-bundle.min.css` | Referenced inside custom HTML blocks (unversioned!) | **No.** No swiper is rendered. In practice it didn't even load. |
| `digistore24.typeform.com/to/jg2QdVwY` | CTA link target (navigation, not a request) | Keep as the link. |
| `digibiz24.com`, `digistore24.com/page/terms/de` | Footer legal links | Keep. |
| `checkout-ds24.com/…/affiliatebadge` | Affiliate badge config (inactive) | No. |
| First-party builder assets | `/assets/pgb/…`: about 20 JS/CSS files (legacy.css 95 KB, jQuery legacy, Vue `client.js`, widget `.mjs` per type, tippy.js, FontAwesome, Roboto) | **No.** All of it is replaced by one CSS file. |

No analytics, GTM, Facebook pixel or cookie consent is active (`trackingPixelGTM:""`, `cookieConsent:false`). The `<html lang>` is `en_US` even though the page is German (bug). The `<meta viewport>` has `maximum-scale=1`, which blocks zoom (accessibility issue).
