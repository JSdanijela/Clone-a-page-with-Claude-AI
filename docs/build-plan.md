# Build plan: Einrichtungsservice static rebuild

See `inventory.md` for the source analysis.

## Target output

```
index.html          one page, semantic sections, lang="de"
css/styles.css      one stylesheet: tokens → base → components → sections
js/                 (expected: none)
assets/img/         optimized AVIF/WebP + fallback, 1x/2x
assets/fonts/       Helvetica Neue LT Pro Md/Bd woff2 (if licensed), else fallback stack
assets/icons/       logo, favicon, arrow, quote, check/cross/plus SVGs
```

No framework, no build step. The goal is **zero JavaScript**.

## Order of work

1. **Foundation**
   - Copy and optimize the assets.
   - Set up `@font-face`.
   - Define CSS custom properties: colors, type scale with `clamp()`, spacing, and the 600/800/1200 breakpoints.
   - Add the reset and base typography.
   - Build a `.container` and a two-column `.split` layout primitive.
2. **Shared components**
   - Buttons: the light and dark variants, with the arrow.
   - Icon list: the ✔ and ✖ variants.
   - Section wrapper: the dark and light variants.
3. **Sections, in page order.** Each one is checked at 375, 800 and 1280px before moving on.
   1. Header and hero text, without the animation for now
   2. "Was wir für dich einrichten" (split, photo on the left)
   3. "Was dieser Service nicht umfasst" (split, photo on the right). This reuses #2.
   4. Photo bands (one component, used 3×) and "Komplett-Service" (centered, dark)
   5. Testimonials (two cards)
   6. FAQ: `<details>/<summary>`
   7. Final CTA with the wordmark, and the footer
4. **Hero animation.** This is last because it is the most self-contained and the most fiddly. Port the 8s CSS keyframe mockup cleanly and add `prefers-reduced-motion`. Show it inline on mobile instead of duplicating the markup in a separate mobile-only block.
5. **Polish and QA**
   - Alt text, focus styles, contrast and heading order.
   - Meta and Open Graph tags, canonical and favicon.
   - Lighthouse.
   - Side-by-side visual comparison with the live page.

## JS vs. HTML/CSS

| Feature | Approach |
|---|---|
| FAQ accordion | `<details>`/`<summary>` + CSS (optional `::details-content` transition) |
| Hero animation | CSS `@keyframes` + inline SVG |
| CTAs | Plain `<a>` to the Typeform URL |
| Lazy images | `loading="lazy"`, `<picture>` |
| Responsive variants | Media queries / grid; no duplicated mobile-only markup |
| **Needs JS** | Nothing, unless we want the Typeform **popup** instead of a link (then load `embed.js` on click only) |

## Risks / open questions

1. **Font licence.** Helvetica Neue LT Pro is a commercial MyFonts webfont. It is tracked by the `hello.myfonts.net` counter, and its files sit on DS24's CDN. Can we use it, and do we keep the counter? If not, the fallback is `"Helvetica Neue", Helvetica, Arial` or a free alternative such as Inter. The page will look slightly different with the fallback.
2. **Asset hosting.** Should we self-host the images and fonts, or hotlink `cch-files.edge.live.ds25.io`? I recommend self-hosting.
3. **Faithful vs. fixed.** The original has bugs:
   - The H1 breaks mid-word at about 1024px.
   - The mobile heading overlaps the photo in section 2.
   - The CTA arrow is clipped on mobile.
   - `lang="en_US"` on a German page.
   - Zoom is disabled.
   - All images have empty alt text.

   I plan to **fix** these rather than replicate them.
4. **Typeform.** Should the CTAs stay plain links (as they work today), or open the popup? The hidden button points to a *different* form ID (`yGFTaOQT` vs `jg2QdVwY`).
5. **Photo bands.** Three 640px decorative image strips with negative-margin overlaps. Should we keep them as-is or slim them down? They add about 2.5 MB and no content.
6. **Hero animation fidelity.** It has about 30 keyframes with hard-coded percentages. Matching it pixel for pixel takes time, but a simplified version is quick. Which do you want?
7. **Tracking and legal.** Nothing is active today (no GTM, no cookie banner). Should we add any tracking or a consent banner? I'm assuming no.
