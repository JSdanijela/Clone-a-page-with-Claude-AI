# Notes

Full inventory of the reference page: see `inventory.md` (to be merged here during groundwork).

## Page shell (header and footer)

Both are part of the page shell, not CMS blocks. They live in `index.html` outside `<main>` and are styled in `css/page.css`.

Content runs full width with no max-width, the same as the original.

### Reference layouts

The reference has three layouts for both header and footer, not two. A check at 375 and 1024px alone misses the middle one.

| Width | Header | Footer |
|---|---|---|
| ≤600px | Logo and button side by side. Both shrink together as the screen narrows. The button text wraps. | Copyright and links centered, 12px / 14px |
| 601–800px | **Stacked.** The logo sits 24px further in, with a wide button below it. The button starts 16px from the left but stops 40px from the right. That asymmetry comes from the builder's column padding; it's kept to match. | **Stacked.** Copyright left-aligned, links right-aligned below it at 16px |
| ≥801px | Logo left, button right | Two equal columns: copyright left, links right |

Within ≥801px, the header logo is vertically centered up to 1000px (34px from the top) and top-aligned from 1001px (30px from the top).

**Layout check (rule 2.6), 2026-10-19:** both pages were measured in the browser at 320, 375, 414, 600, 601, 768, 800, 900, 1024 and 1440px. The rebuild was first measured by injecting `page.css` into a `data:` preview. It was re-measured on 2026-10-19, served from `python3 -m http.server 8000` with all real files loaded, at 375, 600, 768, 900, 1024, 1440 and 1920px. The results were unchanged.

Element order, columns, visibility and alignment match at every width, and sizes are within 1px, with these exceptions:

- **320px:** the logo is 103px wide in the rebuild and 118px on the reference. The reference squeezes the button narrower than the word "Beratungsgespräch", so the word spills out of the button. The rebuild never lets the button get narrower than that word. This is a bug fix.

At 375–600px the header button has `flex-shrink: 1.2`. On the reference its padding sits inside a wrapper column and counts toward shrinking; 1.2 reproduces that. The logo and button sizes now match exactly at 375, 414 and 600px.

Correction: an earlier note said the reference footer links are 14px at 801–1000px. On a fresh page load they are 16px. Only the ≤600px CSS rule makes them 14px. The 14px reading came from the builder's JS keeping its phone state after the window was resized.

### Deviations from the original

- **Font:** since 2026-10-19 the rebuild uses the reference's own font files, Helvetica Neue LT Pro Medium and Bold, from `assets/fonts/`. They're declared in `base.css` as `cc-helvetica-neue` at weights 500 and 700. `page.css` uses the same family.

  This replaces a short-lived workaround: the system Helvetica Neue with `ascent-override`, `descent-override` and `line-gap-override`. That was needed because the system font sits text about 0.16em lower in each line (ascent 0.975 vs 0.714).

  Measured on localhost against the reference at 375, 1024 and 1440px, every text element in header, hero and footer matches on font, size, line height, glyph box, first-line offset and text width. The only line-break differences come from the two deliberate changes in the hero: the title hyphenation and the bold "Wichtig:".
- **Logo alt text:** the original has `alt=""`. The logo is the only brand mark in the header, so it now has `alt="Digistore24"`.
- **Not carried over:** the footer's hidden hamburger menu (it never displays on the original) and the `maximum-scale=1` zoom lock.
- **Focus outline:** links now have a visible `:focus-visible` outline. The original has none.

## cc-hero

Files: `index.html` (between `<!-- cc-hero -->` comments), `css/blocks/hero.css`, `css/base.css` (tokens and `.cc-button`), and three images: `assets/img/hero-photo.webp`, `hero-animation.svg`, `trust-badges.webp`.

### Reference layouts

| Width | Layout |
|---|---|
| ≤600px | One column, text only, 16px inset. Mobile type sizes: title 42, lead 16, note 15. |
| 601–800px | Same, with the text inset 40px and 20px more space above it. The button keeps desktop padding but stays full width. |
| 801–991px | Desktop type: title 48, lead 30, note 14. The button is natural width. The illustration (photo, animated screen, badges) sits below the text. |
| ≥992px | Two columns, text 5/12 and illustration 7/12 |
| ≥1551px | The photo is centered in its column; below that it's left-aligned |

Up to 800px the reference shows the photo and badges in a separate mobile-only block between "Was dieser Service nicht umfasst" and the next section. Decision (2026-10-19): rebuild that as its own mobile-only CMS block in the same position, built when the build order reaches it. `cc-hero` hides its illustration up to 800px, as the reference does.

**Layout check (rule 2.6), 2026-10-19:** the reference's breakpoints were found by sweeping 320 to 1920px. Both pages were then measured at 375, 600, 768, 900, 1024, 1440 and 1920px. The rebuild was served from `http://localhost:8000` with all stylesheets and images loading. Element order, columns, visibility and alignment match at every width. Offsets are within 1px. At 900 and 1024px the columns differ by about 15px because the reference has a scrollbar.

### Deviations from the original

- **`<strong>Wichtig:</strong>`:** the original disclaimer has no bold. CLAUDE.md §5 asks for `<p>` with `<strong>`, so "Wichtig:" is now bold. This is a visible change.
- **Title line breaks:** match the original exactly. Like the reference, the title has `overflow-wrap: break-word` and no auto-hyphenation, so "Einrichtungsservice" breaks without a hyphen where it doesn't fit:

  | Width | Break |
  |---|---|
  | 320px | Einrichtungsse / rvice |
  | 375px | Einrichtungsservi / ce |
  | 1024px | Einrichtungsser / vice |

  An earlier `hyphens: auto` was removed (2026-10-19). It added hyphens that aren't in the original, and split the word badly ("Einrichtungss- / ervice"). The only `-` at line ends on the original are hyphens already in the copy: "Umzugs-", "Einrichtungs-Service", "Marketing-Beratung".
- **Hero line breaks, checked 2026-10-19** at 320, 375, 600, 768, 900, 1024 and 1440px against the reference. Title, lead and intro break identically at every width. The note differs in two places:
  - **1024px:** the bold "Wichtig:" is wider, so "für" moves to the next line.
  - **320px:** the reference ends "Texterstellung." with a trailing `&nbsp;` that pushes the word onto a fifth line. The rebuild drops that builder whitespace, so the note is four lines.
- **Animation:** rebuilt as one self-contained SVG (`hero-animation.svg`) with the same geometry and keyframes, instead of about 40 empty `<div>`s. Under `prefers-reduced-motion` it shows a still frame: the finished product grid. The counter uses the system font rather than Helvetica Neue LT Pro. It loops forever, as the original does. That fails WCAG 2.2.2 (pause, stop, hide) for anyone who hasn't turned on reduced motion; it's flagged here, not fixed.
- **CTA arrow:** an inline SVG that uses `currentColor`, 19×14px, 20px from the right edge. On phones the original squashes it to about 13px wide; here it keeps its size.
  - **Vertical position:** the arrow is centered on the last line of button text. Its bottom offset is the bottom padding plus `(1lh − 14px) / 2`. That's the original's intent: from 601px its `bottom: 25px` equals 21px padding + 4px.
    - A first attempt (2026-10-19) centered the arrow in the whole button. That differed from the original on two-line buttons, about 11px too high at 1024px, and was replaced.
  - **Measured against the reference** (arrow center minus last-line center):

    | Width | Reference | Rebuild |
    |---|---|---|
    | 1440 | −0.5px | 0 |
    | 1024 | −0.5px | 0 |
    | 768 | −0.5px | 0 |
    | 600 | +9.5px | 0 |
    | 375 | +9.5px | 0 |

    Up to 600px the original's arrow hangs 9.5px below the text, because it uses `bottom: 14px` while the padding is 20px. That's fixed here and is the only intended difference.
  - **Right padding on phones:** the button keeps 50px free for the arrow at every width (`--cc-button-icon-space`), as the original does from 601px. With the original's 20px phone padding, a one-line button around 470–500px wide would run its text under the arrow. The arrow is at least 11px clear of the text at every width checked. Text moves at most 3px left compared with the original (at 375px). Button heights and line counts are unchanged.
- **Spacing:** the original gets its 81px spacing on desktop partly from invisible 1px rows. The rebuild uses 80px.
- **Images:** WebP since 2026-10-20 (see "Image formats" at the end): photo 37 KB (was a 455 KB PNG), badges 32 KB lossless (was 54 KB).
- **Mobile download:** the hero images aren't lazy-loaded (CLAUDE.md §5). Up to 800px they're hidden but still downloaded.

## cc-scope

Files: `index.html` (between `<!-- cc-scope -->` comments), `css/blocks/scope.css`, `assets/img/scope-included.webp` (720×720, 53 KB) and `scope-excluded.webp` (720×700, 34 KB). `base.css` gained one token, `--cc-font-icons`.

Structure: two parts in one block, each with its own `<h2>`. The lists are `<ul>`, and the keywords are `<strong>` as on the original. The photos are decorative (`alt=""`).

The list icons are FontAwesome ✔ (U+F00C) and ✖ (U+F00D) from `assets/fonts/fontawesome-webfont.woff2`, the same glyphs as the original. They use `content: "…" / ""`, so screen readers skip them.

The button uses `.cc-button` with the block overriding the button colour tokens: `#161515` background, white text and arrow. The focus outline is black here, because the section is white.

### Reference layouts

| Width | Layout |
|---|---|
| ≤800px | Each part stacked: photo on top, text below with 40px / 16px padding. Part 1's title is 30px, part 2's 36px. Note 15px. Button full width. |
| ≥801px | Each part 50/50, 700px tall, text vertically centered. Part 1 has its photo on the left, part 2 on the right. Titles 36px, note 14px, button natural width. Text inset 15px. |
| ≥1001px | Text inset 124px left, 120px right |

The reference is still stacked at exactly 801px (`max-width: 801px`, one pixel past every other breakpoint). The rebuild switches at 801 like the rest of the page.

**Layout check (rule 2.6), 2026-10-19:** checked on localhost at 375, 600, 800, 991, 1024, 1440 and 1920px against the reference.

- **1440 and 1920px:** every position and size is identical, including the centered text (part 1 heading at y 137, part 2 at y 802), list items, button and note.
- **991px:** within 3px; the reference's Bootstrap columns there aren't exactly 50/50.
- **Every width:** list items wrap onto the same number of lines.
- **Fonts and colours:** computed font, size, line height and colour match for headings, list text, bold keywords, icons, button and note.
- **Copy:** all 14 texts match word for word.

### Photo framing (reviewer request, 2026-10-20)

The rebuild first showed the photos anchored top-left at their natural scale. The reference frames them differently, so this was changed to match.

**How the reference does it.** The photos are CSS backgrounds (`background-size: cover`, `background-position: 0 0`), loaded from the image server as `?o=<box width>&height=<box height>&fit=crop`, in device pixels. Probing the server showed:
- it rounds the width **up to the next 100px** (300→400, 375→400, 400→500, 512→600, 720→800, 750→800);
- it returns a **centred** crop of the photo at that width : height ratio, scaled down if larger than the source (800×700 → 720×630).

That crop is wider than the box, so `cover` fits it to the box height and shows its **left** part. The result is a slight zoom (1440px: 800/720 = 11%), vertically centred, cut off on the right.

**How the rebuild reproduces it** (`scope.css`, no JS, no extra images):
- The photo is as wide as the rounded-up crop: `round(down, box, step) + step`.
- It uses `object-fit: cover` and `object-position: center`.
- A negative end margin keeps the grid cell at the box width, and `clip-path` cuts it at the box edge.
- `box` is `100cqi` on phones and `50cqi` from 801px (the part is a size container). `step` is 100 device pixels in CSS px: 100px, 66.7px from 1.5dppx, 50px from 2dppx, 33.3px from 3dppx.
- `overflow-x: clip` on the part stops the cut-off strip from causing sideways scroll.
- The rule is inside `@supports (inline-size: round(down, 1cqi, 1px))`. Older browsers get a plain centred cover.

**Check, 2026-10-20.** The visible source region was computed from the reference's actual requests and boxes, and from the rebuild's rendered geometry:

| Width (DPR) | Photo | Reference shows (source px) | Rebuild shows |
|---|---|---|---|
| 1440 (1) | included | x 0–648, y 45–675 | x 0–648, y 45–675 |
| 1440 (1) | excluded | x 0–648, y 35–665 | x 0–648, y 35–665 |
| 1024 (1) | included | x 51.4–578.1, y 0–720 | x 51.4–578.1, y 0–720 |
| 1024 (1) | excluded | x 60–572, y 0–700 | x 92–549, y 0–700 |
| 375 (2) | included | x 0–675, y 156–564 | x 0–675, y 191–529 |
| 375 (2) | excluded | x 0–675, y 146–554 | x 0–675, y 181–519 |

A side-by-side screenshot at 1440px looks the same. No sideways scroll at 320, 375, 800, 1024, 1440 and 1920px, and section heights are unchanged.

**Remaining differences, both caused by the bug fixes below.** The framing rule is identical, but the box is a different height:
- **1024px, excluded photo:** the part is 783.5px tall instead of 700, because the text no longer overflows. That's slightly tighter left and right.
- **Phones:** the photo is 2:1 (187.5px at 375px) instead of the reference's 227px. It shows the same horizontal range and zoom, centred on the same point, with a narrower vertical band.

**Caveat:** the reference's framing depends on the device pixel ratio and the width the page was loaded at (it doesn't re-crop on resize). The rebuild follows the current width.

### Deviations from the original (bug fixes)

- **No fixed height.** The original locks each part to exactly 700px. Wherever the text needs more room, it spills out:
  - On phones the heading starts inside the photo (375px: heading at y 167, photo ends at y 227), and the price note runs into the next part.
    - **The price note is invisible on phones** (checked 2026-10-20 at 375px). Part 1 ends at y 1421; the note sits at y 1424–1496, inside part 2. Part 2's photo comes later in the page and is positioned, so it paints over the note; at every point of the note, the topmost element is part 2's photo. The note isn't hidden on purpose (no `display: none`, `visibility` or mobile-only class); it's covered by mistake. The rebuild keeps it visible below the button, because it's the footnote for the "*" price and hiding it would copy the bug.
  - At 1001–about 1200px, part 2's 261px-wide text column is taller than 700px. Its heading starts above the section (1024px: y 658) and the list runs past the bottom.

  The rebuild uses `min-block-size: 700px` on desktop. That's identical wherever the text fits, and the part grows where it doesn't (1024px: part 2 is 815px tall).
- **Phone photo height.** The original's photo height on phones is a side effect of the overflowing fixed-height layout (375px: 227px; 600px: 276px; 785px: 376–408px). The rebuild uses a 2:1 aspect ratio, which gives 188, 300 and 393px at those widths.
- **First photo loads eagerly (performance, reviewer-approved 2026-10-20).** CLAUDE.md §5 says only the hero is non-lazy, but on phones the hero photo is hidden and this photo is the largest element in the first screen. A Lighthouse mobile run flagged it as the LCP element with `loading="lazy"` (LCP 3.2s simulated). It now has no `loading` attribute (eager) and `fetchpriority="high"`; the second scope photo stays lazy. In the browser it's now requested at the same moment as the hero images (65ms after navigation on localhost) instead of after layout. Trade-off: on desktop, where it's below the fold, it competes slightly with the hero photo for bandwidth.
- **Price note weight.** The original marks the price note `<strong>`, but a builder rule forces it back to regular weight. The rebuild keeps the `<strong>` (CLAUDE.md §5) and renders it at regular weight, so it looks the same.

### Content issues

- **Typo in the price note:** "…und **wir** dir im kostenfreien Beratungsgespräch bestätigt." should be "wird". It's kept verbatim.
- **Trailing `&nbsp;`:** the original heading "Was wir für dich einrichten&nbsp;" ends with one. Dropped as builder whitespace; it has no visible effect.

## cc-hero-mobile (mobile only)

Renamed from `cc-hero-media` on 2026-10-20 (reviewer decision), so the name says when the block shows. Markup, styles and layout are unchanged.

Files: `index.html` (between `<!-- cc-hero-mobile -->` comments, after `cc-scope`) and `css/blocks/hero-mobile.css`. It reuses `hero-photo.webp` and `trust-badges.webp`, so there are no new assets. `base.css` adds `.cc-hero-mobile` to the token roots.

Why it exists: up to 800px the reference shows the hero photo and trust badges here, between "Was dieser Service nicht umfasst" and the next section, instead of in the hero. The decision of 2026-10-19 was to rebuild that as its own block. From 801px it's hidden (`display: none`), and `cc-hero` shows the images instead. They're never visible twice. Both images use `loading="lazy"`, so desktop doesn't download them for this block.

Markup:
- **Photo:** decorative (`alt=""`).
- **Badges:** the same descriptive alt text as in `cc-hero`.
- **No heading.** CLAUDE.md §5 wants every section to start with an `<h2>`, but this block has no text on the reference. Decision (2026-10-19, reviewer): don't invent content; if the original doesn't have it, skip it. The same applies to any later image-only block, such as the photo strips.

### Reference layout (≤800px)

- **Photo:** starts 20px down, full width, at most 500px and centered.
- **Badge strip:** inset 16px on each side. It overlaps the bottom 90px of the photo: row margin −100px, plus 10px padding.
- **Bottom spacing:** 10px below the badges.

**Layout check (rule 2.6), 2026-10-19,** on localhost at 320, 375, 600, 767, 800 and 801px. Photo and badge positions and sizes are identical to the reference at 320, 375 and 600px; at 600px the block height is identical too (421px). The block is hidden at 801px. No sideways scroll.

### Deviations from the original (bug fixes)

- **The photo stays inside the block.** Below about 490px the reference's badge strip is shorter than the 90px overlap. Its section then ends above the photo's bottom edge, so the photo pokes into the next section: 12px at 375px, 24px at 320px. The rebuild stacks the images on a three-row grid (photo minus overlap / overlap / rest of badges), so the block grows to contain the photo. It's 307px instead of 285 at 375px, and 267 instead of 233 at 320px.
- **768–800px: the badges still overlap.** From 768px Bootstrap floats both image columns, and a float can't move up beside an earlier full-width float. The −100px margin then stops working and the badges drop below the photo (800px: badges at y 390 instead of 300, block 548px instead of 458). That's a float side effect, not a design choice; the overlap is kept at every width up to 800px.

## cc-fit

Files: `index.html` (between `<!-- cc-fit -->` comments), `css/blocks/fit.css` and `assets/img/fit-strip.webp` (1440×580, 40 KB).

### Structure

- **Photo strip.** The reference has a text-free photo strip (its own builder section) directly before this one. This section starts 75px (40px on phones) above the strip's bottom edge. The strip is built as the first element of `cc-fit`, with `alt=""` and no heading of its own (reviewer decision: don't invent content).
  - **Correction (2026-10-19, reviewer).** The section's dark background does **not** cover the strip. On the reference the strip is `position: relative`, so it paints above the section background, and the photo shows at its full 640 / 235px height. The heading's row is positioned too and comes later, so the heading text paints **over** the bottom of the photo: 55px on desktop ("Wann ist der" sits on the photo), 20px on phones.
  - The rebuild's content box has the same negative top margin, `position: relative` and **no background**. The block's own `#161515` background stays behind both.
  - Checked at 1440px by comparing screenshots of the two pages: same overlap (55px) and same photo framing. The original's CDN request (`?o=1425&height=640&fit=crop`) returns a 1359×580 center crop. Scaled to cover the strip, that shows source pixels 74–1366, the same range `object-fit: cover` shows from the full 1440px image. Note: the reference only loads the right variant on a fresh page load; after resizing it keeps the crop from the width it loaded at.
- **One heading.** The reference splits the heading into two `<h2>`s: "Wann ist der / Komplett-Service" and "perfekt für dich?". The rebuild uses one `<h2>` with two `display: block` spans, as CLAUDE.md §3 requires. The hard `<br>` after "Wann ist der" is copy formatting and is kept. A plain space before it (the original uses `&nbsp;`) keeps the heading's text from reading "derKomplett".
- **Two paragraphs.** The reference's single `<p>` fakes two paragraphs with `<br><br>`. The rebuild uses two `<p>`, with one empty line between them (`1lh`). The text is unchanged.
- **No spacer row.** On desktop the reference adds space before the copy with an empty spacer row (550px wide, 21px tall, 30px margin). The rebuild reproduces that gap with a token instead: 76px on desktop, 25px on phones.

### Reference layouts

| Width | Strip | Heading | Line 2 | Copy | Text inset | Button |
|---|---|---|---|---|---|---|
| ≤600px | 235px tall, 40px overlap | 72px | 36px | 21px | 16px | full width |
| 601–800px | 640px tall, 75px overlap | 72px | 36px | 21px | 36px | full width |
| ≥801px | 640px tall, 75px overlap | 120px | 50px | 30px | 60px | natural width, centered |

Everything is centered. The copy is at most 888px wide. There's 40px above the button and 80px below it.

**Layout check (rule 2.6), 2026-10-19,** on localhost at 320, 375, 600, 601, 790, 800, 1024, 1200, 1440 and 1920px. Strip, heading, second line, copy, button and section end are identical to the reference at every width; for example, at 1440px: heading y 585, copy y 951, button 339×65 at x 543, section end 1376. Title and copy line breaks are identical at 320, 375 and 1440px.

**Fixed during the check:** at 320px "Komplett-Service" overflowed sideways. The title now has `overflow-wrap: break-word`, like the reference, and breaks "Komplet / t- / Service" exactly as the reference does.

## cc-testimonials

Files: `index.html` (between `<!-- cc-testimonials -->` comments) and `css/blocks/testimonials.css`. Images in `assets/img/`:
- `testimonial-mareike-awe.webp` (848×526, 19 KB)
- `testimonial-dul-seo.webp` (425×260, 6 KB)
- `quote-marks.svg` (63×63)

### Structure

- **Heading:** one `<h2>`.
- **Cards:** each card is a `<figure>`: photo, quote-mark icon, `<blockquote><p>`, and `<figcaption>` with the name and role as two block-level spans.
- **Alt text:** the photos are portraits of the people named in the caption right below, so they get `alt=""` (same as the original) to avoid announcing each name twice. The quote mark is decorative (`alt=""`).
- **Wrappers:** none beyond the cards. The grid sits on the block root.

**Images aren't missing** (CLAUDE.md §6). On the reference, the `<img>`s start as placeholder SVGs, with the real files in `data-src`. The builder's lazy loader swaps them on real scroll events only; programmatic scrolling doesn't trigger it, which made them look empty in automated checks. With mouse-wheel scrolling all four images load. Both photos and the icon are used as-is.

### Reference layouts

| Width | Layout | Heading | Quote | Name | Role |
|---|---|---|---|---|---|
| ≤800px | Heading, then the cards stacked full width (16px inset), 40px apart | 36px | 18px / 1.2 | 21px | 16px |
| ≥801px | Three equal columns (heading, card, card) with 40px gaps; columns never narrower than their longest word; cards equal height | 48px | 21px / 1.2 | 30px | 18px |

- **Heading inset:** 16px up to 600px, 36px at 601–800, 60px from 801.
- **Cards:** black. The photo is full width at its natural aspect ratio. The white quote-mark square overlaps the photo by 30px and is inset 30px. All text is inset 30px, with 20px gaps and 30px of bottom padding.
- **Colours:** quote `#b2b2b2`, name white, role 80% white.
- **Spacing above and below the content:** 50px on phones. On desktop it's 91px, built from 40px padding plus empty 1px spacer rows.

**Layout check (rule 2.6), 2026-10-19,** on localhost at 375, 600, 790, 800, 1024, 1440 and 1920px:
- Heading, card, photo, quote-mark, quote, name and role positions are within 1px of the reference at every width except 801–1000, which is deliberately different (see below).
- At 1024px even the reference's uneven column widths (266 / 284 / 276px) match.
- Section heights match: 1401px at 375, 850px at 1440, and within 1px elsewhere.
- The quote mark is the top layer where it overlaps the photo.
- Colours and copy match exactly (all 7 texts).

### Deviations from the original (bug fix)

- **801–1000px: stacked instead of three columns.** On the reference the three columns can't shrink below their longest words, so below a 986px content width (about a 1000px window) the second card is cut off and the page scrolls sideways: 986px of content in a 786px window at 801px. The rebuild keeps the stacked phone layout there, with desktop type sizes and 40px insets. It switches to three columns at 1001px, the same breakpoint as `cc-scope`, where they fit.

### Content issues

- The name is written "dr. med. Mareike Awe" with a lowercase "dr.". It's probably meant to be "Dr. med.". Kept verbatim.

## cc-faq

Files: `index.html` (between `<!-- cc-faq -->` comments), `css/blocks/faq.css` and `assets/img/faq-strip.webp` (1440×580, 51 KB, from the reference's `group-1030.png`). No JS.

### Structure

- **Photo strip.** The reference has a second text-free photo strip (its own builder section) between the testimonials and the FAQ. Unlike the first one it doesn't overlap anything. It's built as the first element of `cc-faq`, like the strip that opens `cc-fit`, with `alt=""` and no heading (don't invent content).
- **One heading.** The reference uses two `<h2>`s: "FAQ" (big) and "Häufige Fragen" (small). The rebuild uses one `<h2>` with two `display: block` spans, as in `cc-fit` (CLAUDE.md §3: no headings split in two).
- **Questions:** five `<details>`/`<summary>`, no JS. All start closed, as on the reference. Several can be open at once, as on the reference (opening one doesn't close the others), so there's no `name` attribute.
- **Answers:** one `<p>` each. The reference's `<br>` line breaks are copy formatting and are kept.
- **Icon:** the FontAwesome plus (closed) and minus (open) as a `::after` on the summary, with empty alt text (`content: "\f067" / ""`). The open/closed state is announced by `<details>` itself.
- **Fonts:** questions in Roboto bold, answers in Roboto regular, as on the reference; the heading uses Helvetica Neue like the other blocks.
- **Wrappers:** `cc-faq__content` (padding and the two-column grid) and `cc-faq__list` (keeps the five items in one grid column).

### Reference layouts

| Width | Strip | Layout | "FAQ" | "Häufige Fragen" | Text inset | Question indent | Space below list |
|---|---|---|---|---|---|---|---|
| ≤600px | 235px | heading, then questions | 72px | 21px | 16px | 0 | 80px |
| 601–800px | 640px | same | 72px | 21px | 36px | 20px | 80px |
| 801–991px | 640px | same | 120px | 30px | 60px | 20px | 131px |
| ≥992px | 640px | two equal columns, 40px gap: heading left, questions right | 120px | 30px | 60px | 20px | 131px |

- **Heading:** 70px below the strip (50px padding + 20px margin). In the two-column layout the questions start at 50px, so 20px above the heading. Stacked, the questions start 40px below the heading.
- **Items:** 20px top and bottom padding, 30px right padding, 1px `#e6e9ec` bottom border. Question 21px / 1.5 bold with 20px right padding; answer 18px / 1.5, 10px below the question and 6px above the item padding.
- **Icon:** 21px FontAwesome, black. The reference positions a rotated 32px box so the glyph sits 3.5px above the first line's top and 3px past the end of the question box (the item's padding area). The rebuild places the glyph at the same spot with two tokens.
- **Space below the list:** 30px (an empty `<style>` component, see below) + 10px row padding + 40px section padding. From 801px the reference adds an empty desktop-only spacer row (51px).

**Layout check (rule 2.6), 2026-10-19,** on localhost at 320, 375, 700, 801, 1024 and 1440px, against fresh reference loads at 375, 700, 801, 991, 992, 1024 and 1440px. Strip, heading, sub-heading, all five items (position, width, height) and the section end are identical at every width, e.g. 1440px: heading 710px below the strip top, questions at x 732.5 / 632.5px wide, section 1215px including the strip. An open item ("Was muss ich vorbereiten?") is 196.5px tall on both, with the answer at the same position and the same line breaks. The +/− glyphs line up in side-by-side screenshots. No sideways scroll at 320px with all items open.

### Deviations from the original

- **Open/close animation (reviewer request, 2026-10-20).** The reference animates the answer's height in both directions: 0.3s, `cubic-bezier(0.33, 1, 0.68, 1)` (ease-out cubic), with the content clipped while it moves; the +/− swaps instantly on click. The rebuild does the same without JS: `::details-content` transitions `block-size` from 0 to `auto` (`interpolate-size: allow-keywords`), and `content-visibility` with `allow-discrete` keeps the answer visible until the closing transition ends. The answer's 10px / 6px gaps are padding instead of margin, so they're part of the animated box, as on the reference. Turned off under `prefers-reduced-motion`.
  - **Checked frame by frame at 1440px** ("Was muss ich vorbereiten?", item 72.5 → 196.5px). Opening: rebuild 102.9 / 127.6 / 163.0 / 189.5 / 196.5px at 24 / 51 / 104 / 184 / 291ms; reference 102.8 / 127.6 / 163.0 / 189.5 / 196.5px at the same moments.
  - **Closing:** same duration and curve. The reference first drops 6px instantly (a margin-collapsing glitch) and then animates the rest; the rebuild animates the full height smoothly, so mid-close it's up to about 12px taller.
  - **Browser support:** this needs `::details-content` and `interpolate-size`, which Chromium browsers (Chrome, Edge) support. Safari and Firefox don't support `interpolate-size` yet, so there the answers open and close instantly; the FAQ still works fully. Matching them too would need a small JS block, which CLAUDE.md §5 rules out for the FAQ.
- **Pointer cursor** on the questions (the reference shows the text cursor although the whole row is clickable).
- **Keyboard access and focus.** The reference's questions are `<div>`s with click handlers: they can't be reached or opened with the keyboard. `<summary>` is focusable and opens with Enter/Space, with a visible black focus outline.
- **Dropped builder leftover:** an inline `<style>` block with `!important` rules that swap icons for `.fa-caret-down`, a class the FAQ doesn't use. Its 30px top margin is the only thing it contributes; the rebuild keeps that space via padding.

## cc-cta

Files: `index.html` (between `<!-- cc-cta -->` comments), `css/blocks/cta.css`, `assets/img/cta-strip.webp` (1440×700, 77 KB, from the reference's `group-1034.png`) and `assets/img/cta-wordmark.svg` (884×226, the reference's `title.svg`: 19 white paths, no text, no scripts). The button reuses `.cc-button` from `base.css` unchanged (white with black text is its default).

### Structure

- **Photo strip.** The third text-free strip is the first element of `cc-cta`, `alt=""`, no heading of its own. The content moves up over its bottom part, and is `position: relative` so it paints over the photo, as on the reference.
- **Black background.** On the reference the CTA section is transparent; the black comes from the page's `<body>` background. The rebuild puts `#000` on the block root (CLAUDE.md §8: no `body` styles in blocks).
- **One heading, h2 instead of h1.** The reference marks "Starte jetzt mit" up as a second `<h1>`, followed by the "MORE SALES. LESS WORK." wordmark image. The rebuild uses one `<h2>` (CLAUDE.md §5: one `<h1>` per page) holding the text and the wordmark, so the heading reads "Starte jetzt mit MORE SALES. LESS WORK.".
- **Wordmark alt text.** The reference has `alt=""` on the wordmark, so screen readers hear only "Starte jetzt mit". The image is text, so the rebuild gives it `alt="MORE SALES. LESS WORK."` (accessibility fix).
- **Dropped builder leftovers:** an empty transparent 2px mobile-only divider (its space is kept via padding) and an empty `<style>` rule for the button.

### Reference layouts

| Width | Strip | Content overlaps the strip by | "Starte jetzt mit" | Text inset | Button |
|---|---|---|---|---|---|
| ≤600px | 235px | 260px (25px more than the strip) | 36px | 16px | full width |
| 601–800px | 640px | 260px | 36px | 36px | full width |
| ≥801px | 640px | 260px | 48px | 60px | natural width, centred |

- **Inside:** heading, wordmark 20px below it (full width, at most 884px, centred), button 40px below the wordmark, 20px of padding below the button.
- **Heading position:** 52px below the content's top edge up to 800px (padding plus the empty divider), 30px from 801px.

**Layout check (rule 2.6), 2026-10-20,** on localhost at 375, 700, 801 and 1440px against fresh reference loads. Strip, heading, wordmark, button and block height are identical at every width, e.g. 1440px: heading 410px below the strip top, wordmark 884×226 at x 270.5, button 338.8×65 at x 543.1, y 744, block 829px; 375px: heading at 27, wordmark 343×87.7 at 83, button 343×61 at 210.7, block 291.7px. A 1440px screenshot shows the same photo framing and the same text over the photo. No sideways scroll.

### Deviations from the original

- **The content doesn't reach into the FAQ.** Up to 600px the reference's 260px overlap is more than the 235px strip, so the CTA section starts 25px inside the FAQ section (only empty padding is there). The rebuild stops the overlap at the strip's top edge and adds those 25px to the top padding instead. Every visible element is at the same position.
- **h2 instead of h1, and alt text on the wordmark** (see above).

## Light line above the scope photo on desktop (2026-10-20, bug fix)

At desktop widths a 1px light line showed where the hero ends and the first scope photo starts. The hero's height is fractional (795.39px at 1440px), so the photo's top edge lands mid-pixel. The photo is cropped with `clip-path`, and Chromium anti-aliases a clip edge on a fractional pixel. That made the top row partly transparent, so the scope block's white background showed through. The clip now extends 1px past the photo's top and bottom (`inset(-1px … -1px 0)`). Only the right-hand crop is still clipped, and the photo itself is unchanged.

## Meta description (2026-10-20, reviewer decision)

The reference page has no meta description (Lighthouse SEO 91). At the reviewer's request the rebuild uses the description of the German Digistore24 home page (`https://www.digistore24.com/de/`), word for word:

> Die weltweit führende Affiliate- & Verkaufs-Plattform mit integriertem Online-Shop, Zahlungsabwicklung & Steuerverwaltung. Starte in unter 2 Minuten.

It sits in the page shell's `<head>` (not in a block), so in the CMS it belongs in the page's SEO field. It describes the platform rather than this service; a page-specific text from the copy owner would fit better. The title stays "Einrichtungsservice", as on the reference.

## Whole-page check (2026-10-20)

After the last section, per CLAUDE.md §10:

- **Headings:** one `h1` ("Umzugs- und Einrichtungsservice"), then seven `h2`s, no skipped levels.
- **Scope:** every selector in `css/blocks/*.css` starts with its own block's root class; `base.css` has only `@font-face`, `:where(.cc-…)` token and button rules. No `!important`, no inline styles or scripts in `index.html`, no CDN or external font requests (the only external URLs are the booking and legal links). Every class in the CSS is used in the HTML, every `--cc-` token is used and defined. Every image in `assets/img/` is used, and every manifest path exists.
- **Copy:** every text on the rebuild exists word for word on the reference. The only text on the reference that isn't in the rebuild is two hidden builder leftovers that never show: a "Launch me" `<button>` and the "×" of an unused image pop-up.
- **Layout:** landmark positions (each heading, the last FAQ question, the closing CTA heading, the footer) and page height compared on fresh loads.
  - **1440px:** every landmark within 3px. The drift comes from the reference's invisible 1px spacer rows (see `cc-hero`, "Spacing"); page 6574 vs 6577px.
  - **1024px:** from cc-scope part 2 on, the rebuild is 112px longer: the reference's fixed 700px parts overflow there (see `cc-scope`), the rebuild's grow. All later sections have identical spacing.
  - **375px:** the rebuild is 401px longer, all from the approved bug fixes in `cc-scope` (no fixed height, 2:1 phone photo) and `cc-hero-mobile` (photo stays inside the block). From `cc-fit` to the footer every gap is identical (constant 402px offset).

## Image formats (2026-10-20)

All raster images are WebP, encoded with `cwebp` 1.6.0 (installed by the reviewer with `brew install webp`). The source PNGs are the files downloaded from the reference; they were removed from `assets/img/` on 2026-10-20 after the reviewer approved the WebP versions (moved to the macOS Trash, so recoverable until it's emptied; the reference source file names are in `docs/inventory.md`).

- **Size:** no image was scaled. Each source is already at most 2× its largest rendered size (CLAUDE.md §6) and never shown larger than its source, so pixel dimensions and the `width`/`height` attributes are unchanged.
- **Photos** (8 files): lossy, `-q 82 -m 6 -sharp_yuv -metadata none`. The Mareike Awe portrait keeps its alpha channel; only its edge row is semi-transparent.
- **Trust badges:** lossless (`-lossless -z 9`). The image has real transparency (rounded corners) and small print ("TrustScore 4.7", "4,815 reviews"), which lossy compression could blur. Lossy would have been 15 KB instead of 32 KB.

| File | PNG | WebP |
|---|---|---|
| `hero-photo` | 455 KB | 37 KB |
| `trust-badges` | 54 KB | 32 KB (lossless) |
| `scope-included` | 870 KB | 53 KB (q75, see below) |
| `scope-excluded` | 337 KB | 34 KB |
| `fit-strip` | 880 KB | 40 KB |
| `testimonial-mareike-awe` | 465 KB | 19 KB |
| `testimonial-dul-seo` | 125 KB | 6 KB |
| `faq-strip` | 969 KB | 51 KB |
| `cta-strip` | 1.24 MB | 77 KB |
| **Total** | **5.39 MB** | **0.35 MB** |

**Quality check:**
- `cwebp` PSNR against the PNG: 40.6 dB (`scope-included`, the busiest photo) to 45.0 dB. Above 40 dB is normally indistinguishable by eye.
- Pixel comparison in the browser (both decoded on a canvas): mean difference 1.5–2.8 levels out of 255 per channel; at most 0.03% of values differ by more than 24. The badges are identical, and alpha is identical in both files that have it.
- On localhost every image loads at its natural size and the page height is unchanged (6574px at 1440px).

`index.html` and `assets/manifest.json` now reference the `.webp` files; no CSS references images.

**`scope-included` re-encoded at quality 75 (2026-10-20, reviewer decision):** Lighthouse flagged about 36 KB of possible savings on this image, which is the page's LCP element on phones. At q82 it was 74 KB (40.6 dB PSNR); at q75 it is 53 KB (38.4 dB). That is below the 40 dB rule of thumb above, but the two versions looked the same when compared side by side at full size. The other images stay at q82.

## Fonts downloaded (2026-10-19)

`assets/fonts/` holds the five font files the reference page actually loads. The `.woff` fallbacks and the declared-but-unused Light weight are left out.

All five are declared with `@font-face` in `base.css`. URLs are relative to the stylesheet (`../assets/fonts/…`) and will need updating when the blocks move into the CMS. Browsers only download a face when text uses it, so today only the two Helvetica files load. FontAwesome (`cc-fontawesome`) is used by the `cc-scope` list icons. Roboto (`cc-roboto`) isn't used yet; it's meant for the FAQ text.

| File | Source | Used on the reference for |
|---|---|---|
| `helvetica-neue-lt-pro-md.woff2` | `cch-files.edge.live.ds25.io/…/gtbvknetka9xfic1oc64-helveticaneueltpro-md.woff2` | Almost all text |
| `helvetica-neue-lt-pro-bd.woff2` | `cch-files.edge.live.ds25.io/…/4sfyxppssegr79k4bsyy-helveticaneueltpro-bd.woff2` | `<strong>` |
| `roboto-latin-400-normal.woff2`, `roboto-latin-700-normal.woff2` | `marketing.digistore24.com/assets/pgb/fonts/files/` | Builder default font, used by the FAQ |
| `fontawesome-webfont.woff2` | `marketing.digistore24.com/assets/pgb/page/fonts/files/` | Icon font: ✔ ✖ + − |

**Licence:** Helvetica Neue LT Pro is a commercial MyFonts font. The original counts page views with `hello.myfonts.net/count/3be631`, and CLAUDE.md doesn't allow that tracker. Confirm that the licence covers self-hosting without the counter before these files ship.

## Content issues (whole page)

- The copyright year "© 2026" is hard-coded text, kept verbatim. It won't update itself.
- The footer's Datenschutz and Impressum links go to `digibiz24.com`, not `digistore24.com`. Kept as-is.
