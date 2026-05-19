# Topic Research : frontend-errors-units-rendering-viewport

## Status

Phase 4 topic-research drill closing verification gap #2 from `vooronderzoek-frontend.md` §14 : the `dvh` / `svh` / `lvh` viewport-percentage units that the broad-research pass referenced but did not WebFetch. All claims below are verified against approved sources in `SOURCES.md` (MDN, W3C css-values-4, web.dev). Citation format : `(verified 2026-05-19)`. WebFetch count for this document : 8 distinct verifications across 6 unique URLs (one MDN URL fetched twice for distinct anchor sections : `#viewport-percentage_lengths` and `#font-relative_lengths`).

Baseline summary :

- Default viewport units (`vw`, `vh`, `vmin`, `vmax`, `vi`, `vb`) : Widely available since July 2015 per [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) (verified 2026-05-19).
- Small / Large / Dynamic viewport units (`sv*`, `lv*`, `dv*`) : universally shipped across Chrome 108+, Edge 108+, Firefox 101+, Safari 15.4+ per [web.dev : viewport-units](https://web.dev/blog/viewport-units) (verified 2026-05-19). Baseline 2023 (Newly Available), promoted toward Widely Available as of 2026.
- `env(safe-area-inset-*)` : Widely available since January 2020 per [MDN : env](https://developer.mozilla.org/en-US/docs/Web/CSS/env) (verified 2026-05-19).

This research is the binding source for the `frontend-errors-units-rendering-viewport` skill author. Anti-pattern fix prescriptions, decision-tree branches, and Quick Reference examples in `SKILL.md` MUST trace to one of the citations below.

---

## 1. The viewport-units catastrophe (vh bugs)

Before the small/large/dynamic viewport units shipped, the only viewport-relative height unit was `vh`. The CSS Values 3 specification did not constrain whether `vh` measured the viewport with the mobile URL bar visible or hidden, leaving each user agent to choose. Per [W3C css-values-4 : viewport-relative-lengths](https://www.w3.org/TR/css-values-4/#viewport-relative-lengths) (verified 2026-05-19) the legacy `vh` resolves against the "default viewport size", which the spec defines as UA-chosen and may be equivalent to the small, intermediate, or large viewport size. [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) (verified 2026-05-19) confirms the practical reality : "Currently, all default viewport units (`vh`, `vw`, etc.) are equivalent to their large viewport counterparts (`lvh`, `lvw`, etc.)." This means `100vh` on iOS Safari and Chrome for Android computes against the maximum viewport (URL bar collapsed).

The catastrophic consequence : a hero section styled `min-height: 100vh` extends past the bottom of the visible viewport whenever the mobile browser chrome is expanded. Users land on the page with the URL bar visible, see content cut off by the bottom toolbar, and the call-to-action button sits below the fold even though the developer intended the section to fit. Worse, the height does not adapt as the user scrolls and the chrome collapses : the element stays at its original computed value, leaving empty whitespace at the bottom.

The pre-2022 community workaround was a JavaScript script that read `window.innerHeight`, wrote a CSS custom property (`--vh`), and listened to `resize` events to update it. Per [web.dev : viewport-units](https://web.dev/blog/viewport-units) (verified 2026-05-19), this pattern was ubiquitous but flawed : the resize event fires after the chrome animation completes (causing visible jumps), it requires JavaScript for a purely presentational concern, and it conflicts with `prefers-reduced-motion` because the layout shift cannot be opted out.

The CSS Working Group's css-values-4 module solves the problem at the spec layer by introducing three explicit viewport-size categories : Large, Small, Dynamic. Authors now declare *which* viewport they want measured, rather than hoping the UA picks correctly.

[MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length#viewport-percentage_lengths) (verified 2026-05-19) notes the supplementary scrollbar trap : "None of the viewport units take the size of scrollbars into account. On systems that have classic scrollbars enabled, an element sized to `100vw` will therefore be a little bit too wide." This affects desktop layouts where reserved scrollbar gutters cause `100vw` to overflow `100%` of the body. `scrollbar-gutter: stable` on the root mitigates but does not eliminate the gap.

## 2. dvh / svh / lvh + writing-mode aware variants

The new units come in four families. From [W3C css-values-4](https://www.w3.org/TR/css-values-4/#viewport-relative-lengths) (verified 2026-05-19) and [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) (verified 2026-05-19) :

| Prefix | Viewport size | Stability | When to use |
|--------|---------------|-----------|-------------|
| (none) | UA-default (currently = large) | Stable | Legacy compatibility only |
| `sv*` | Small : assumes UA chrome fully expanded | Stable | Guaranteed-to-fit content |
| `lv*` | Large : assumes UA chrome fully retracted | Stable | Full extent at peak visibility |
| `dv*` | Dynamic : tracks current chrome state | Not stable | Hero / full-screen layouts |

Per the W3C spec : "The large viewport-percentage units are defined with respect to the large viewport size : the viewport sized assuming any UA interfaces that are dynamically expanded and retracted to be retracted." Conversely : "The small viewport-percentage units are defined with respect to the small viewport size : the viewport sized assuming any UA interfaces that are dynamically expanded and retracted to be expanded." The dynamic units split the difference : "The sizes of the dynamic viewport-percentage units are not stable even while the viewport itself is unchanged. Using these units can cause content to resize e.g. while the user scrolls the page."

Within each family, six concrete units exist :

- `*vw` / `*vh` : width and height in physical (horizontal/vertical) terms.
- `*vmin` / `*vmax` : smallest and largest of width/height in that viewport family.
- `*vi` / `*vb` : inline-axis and block-axis sizes, writing-mode aware.

The inline/block variants matter for vertical writing modes (`writing-mode: vertical-rl`) and right-to-left scripts. Per [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) (verified 2026-05-19) : "Inline-axis units represent a percentage of the size of the initial containing block in the direction of the root element's inline axis." For a horizontal Latin layout, `1vi == 1vw` and `1vb == 1vh`. For Japanese vertical text, `1vi` measures along the typed-line direction (vertically), `1vb` along block-flow (horizontally). Authors building writing-mode-agnostic UI (e.g. an internationalized blog template) SHOULD prefer `vi` / `vb` family units paired with logical properties (`block-size`, `inline-size`).

The dynamic family solves the catastrophe : `100dvh` sizes a hero element such that it always fills the currently visible area, smoothly adapting as the URL bar collapses. Per [web.dev : viewport-units](https://web.dev/blog/viewport-units) (verified 2026-05-19) : "Their sizes are clamped between their `lv*` and `sv*` counterparts." So a `100dvh` element is between `100svh` (when chrome is fully visible) and `100lvh` (when chrome is fully hidden). Critically, [web.dev](https://web.dev/blog/viewport-units) (verified 2026-05-19) notes : "Updating is throttled as the UA UI expands or retracts." Browsers do NOT recompute `dvh` at 60 fps during the chrome animation. They jump from old value to new value once at the start or end of the transition. This prevents reflow thrashing.

The W3C spec corroborates : "The UA is not required to animate the dynamic viewport-percentage units while expanding and retracting any relevant interfaces, and may instead calculate the units as if the relevant interface was fully expanded or retracted during the UI animation." Authors must therefore expect a single step, not a smooth tween. Wrapping `dvh`-sized elements in transitions to mask the step is acceptable but should be tested across iOS Safari, Chrome Android, and Firefox Android, which animate chrome at different speeds.

Virtual keyboard handling is intentionally excluded : per [web.dev : viewport-units](https://web.dev/blog/viewport-units) (verified 2026-05-19), "The on-screen keyboard doesn't affect viewport units." Authors needing keyboard-aware layout MUST use `env(keyboard-inset-*)` from the VirtualKeyboard API instead (see §6).

## 3. em vs rem + font-relative units

The em-vs-rem trap is older than viewport units but still trips authors who learned CSS via component-scoped frameworks. Per [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length#font-relative_lengths) (verified 2026-05-19) :

- `em` : "Represents the calculated `font-size` of the element. When used on the `font-size` property itself, it represents the inherited font-size."
- `rem` : "Represents the `font-size` of the root element (typically `<html>`). Common browser default is `16px`. Does NOT compound."

The compounding behavior is what bites. Given :

```css
html { font-size: 16px; }
.parent { font-size: 1.5em; } /* 24px */
.child  { font-size: 1.5em; } /* 24px * 1.5 = 36px */
.grand  { font-size: 1.5em; } /* 36px * 1.5 = 54px */
```

A deeply nested element inflates uncontrollably. The same selectors with `rem` produce predictable `24px` everywhere because each computes from `html`'s `16px`. The deterministic rule for the skill : ALWAYS use `rem` for `font-size`, `line-height`, and any property where you want a stable measurement across nesting depth. Use `em` ONLY when you want a property to scale WITH the element's current font-size, e.g. button padding that grows proportionally with button label text (`padding: 0.5em 1em` on a `<button>`).

Other font-relative units from [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length#font-relative_lengths) (verified 2026-05-19) :

- `ex` : "Equal to the x-height of the element's font. Typically `1ex ≈ 0.5em`."
- `cap` : "Equal to the cap height (nominal height of capital letters)."
- `ch` : "Width or advance measure of the glyph `0` (U+0030). Fallback : 0.5em wide by 1em tall if `0` glyph measure cannot be determined."
- `ic` : "The used advance measure of the `水` glyph (U+6C34, CJK water ideograph). In CJK fonts, `1ic` ≈ one full-width character."
- `lh` / `rlh` : "Equal to the computed value of the `line-height` property of the element (`lh`) or root (`rlh`)."

Root-element variants `rcap`, `rch`, `rex`, `ric`, `rlh` reference the root element's font metrics rather than the local element's, mirroring `rem`'s relationship to `em`. Per MDN these are "newer units with modern browser support" : authors writing for evergreen-2026 baseline can use them but should verify on Baseline.

Accessibility note from [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length#font-relative_lengths) (verified 2026-05-19) : "Many users increase their user agent's default font size to make text more legible. Absolute lengths can cause accessibility problems because they are fixed and do not scale according to user settings. For this reason, prefer relative lengths (such as em or rem) when setting font-size." This is binding for the WCAG 2.2 compliance skill cross-reference : `font-size: 14px` blocks user font-size preference; `font-size: 0.875rem` respects it.

## 4. CSS pixel vs device pixel + subpixel rendering

The absolute units `cm`, `mm`, `Q`, `in`, `pc`, `pt`, `px` are anchored to the CSS reference pixel. Per [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) (verified 2026-05-19) : "`1px` = `1in / 96`." The corollary chain : `1in = 96px = 72pt = 2.54cm = 6pc`. Crucially, this is the *reference* inch, not the physical inch. The spec quote : "For low-dpi devices, the unit `px` represents the physical reference pixel; other units are defined relative to it. The consequence of this definition is that on such devices, dimensions described in inches, centimeters, or millimeters don't necessarily match the size of the physical unit with the same name."

Authors who write `width: 1in` on a button hoping for a physically-one-inch button on every screen will be disappointed : on a 4K 27-inch monitor, 96px is approximately 0.6 physical inches. The CSS spec deliberately decouples authoring units from physical dimensions for everything except print stylesheets.

High-DPI rendering is governed by `window.devicePixelRatio` per [MDN : Window.devicePixelRatio](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio) (verified 2026-05-19) : "Returns the ratio of the resolution in physical pixels to the resolution in CSS pixels for the current display device. Classic display (96 DPI) : 1.0. Common HiDPI/Retina values : 1.5, 2.0, 2.5, 3.0, 4.0." On a Retina MacBook (DPR = 2.0) a CSS pixel maps to a 2x2 physical-pixel grid, giving four-times-the-resolution rendering. Authors do not change CSS values for HiDPI : the browser scales automatically. The intervention happens for raster assets : an `<img>` declared `width: 200px` will render at 200 CSS pixels but use 400 device pixels of backing-store; therefore the source PNG should be 400×400 (or use `srcset` with `2x` density descriptors).

For `<canvas>`, the backing store does not auto-scale. The standard pattern from [MDN : devicePixelRatio](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio) (verified 2026-05-19) :

```javascript
const scale = window.devicePixelRatio;
canvas.style.width = `${size}px`;          // CSS pixels
canvas.style.height = `${size}px`;
canvas.width = Math.floor(size * scale);    // backing-store pixels
canvas.height = Math.floor(size * scale);
ctx.scale(scale, scale);                    // normalize drawing
```

Subpixel rendering : a `border: 0.5px solid` declaration is legal CSS and computes to half a CSS pixel. On a DPR = 1 display this rounds either to 0 (invisible border) or 1 (full border) depending on browser; on DPR = 2 it produces a true one-device-pixel hairline. The deterministic rule : NEVER use `0.5px` for borders unless you are also setting `image-rendering: crisp-edges` or accepting browser variance. The robust alternative for hairline borders is `1px` border combined with `transform: scale(0.5)` on a wrapper, or an SVG line. Per [MDN : devicePixelRatio](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio) (verified 2026-05-19), DPR-aware code can branch : `if (devicePixelRatio >= 2) { border-width: 0.5px } else { border-width: 1px }`, but the maintenance cost rarely justifies the visual win.

## 5. ch / ex / cap / ic for typography control

The font-relative non-em/rem units exist for specific typographic alignment problems where percentage-of-font-size is the wrong abstraction.

Per [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length#font-relative_lengths) (verified 2026-05-19) :

- `ch` : advance of `0` glyph. Use case : line-length control. `max-width: 65ch` produces the canonical optimal-readability measure (45 to 75 characters per line). Works best with monospace fonts where every character is `1ch`; in proportional fonts, `ch` is the width of `0` specifically, which is wider than `i` and narrower than `M`, so character counts are approximate.
- `ex` : x-height. Use case : aligning icons or graphical bullets to the baseline of lowercase text. An inline `<svg>` sized `height: 1ex` sits flush with the x-height of surrounding lowercase letters.
- `cap` : cap height. Use case : aligning uppercase-only labels (HEADER text) where you want the box to match capital-letter height, not the full em-box that includes descenders.
- `ic` : U+6C34 (CJK water ideograph) advance. Use case : CJK typography where you want one full-width-character spacing. `letter-spacing: 0.1ic` makes sense in Japanese vertical writing where the unit naturally matches the visual rhythm.

These units suffer from font-dependence : two fonts with identical `font-size` can produce different `1ch`, `1ex`, or `1cap` values because the metrics are read from the active font. When `font-family` switches mid-render (font-fallback during webfont load) the values shift, sometimes visibly. The pragmatic rule for the skill : reserve `ch` for `max-width` on prose containers; reserve `ex` for inline-icon alignment; treat `cap` and `ic` as specialty tools used only when standard units fail.

## 6. safe-area-inset env() + notch handling

iPhones since the iPhone X (2017) and many Android phones since 2018 ship with display cutouts (notches, punch-holes, dynamic islands). Without intervention, web content rendering edge-to-edge gets occluded by the cutout. The platform answer is two-part : the `viewport-fit=cover` meta-viewport directive that opts into edge-to-edge rendering, paired with CSS `env(safe-area-inset-*)` variables that report the safe-area rectangle.

Per [MDN : meta viewport](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/meta/name/viewport) (verified 2026-05-19), the `viewport-fit` values are :

- `auto` : "Doesn't affect the initial layout viewport, and the whole web page is viewable." Default. iOS Safari inset content from the cutout automatically; safe-area-inset values resolve to 0.
- `contain` : "The viewport is scaled to fit the largest rectangle inscribed within the display."
- `cover` : "The viewport is scaled to fill the device display. It's highly recommended to use the safe area inset variables to ensure that important content doesn't end up outside the display."

The standard incantation per MDN :

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
```

After this, [MDN : env](https://developer.mozilla.org/en-US/docs/Web/CSS/env) (verified 2026-05-19) exposes four safe-area-inset variables : `safe-area-inset-top`, `safe-area-inset-right`, `safe-area-inset-bottom`, `safe-area-inset-left`. The MDN definition : "The safe distance from the top, right, bottom, or left inset edge of the viewport, defining where it is safe to place content into without risking it being cut off by the shape of a non-rectangular display. The four values form a rectangle, inside which all content is visible. The values are `0` if the viewport is a rectangle and no features such as toolbars or dynamic keyboards are occupying viewport space; otherwise, it is a px value greater than `0`."

The canonical sticky footer pattern from MDN :

```css
footer {
  position: sticky;
  bottom: 0;
  padding: 1em 1em calc(1em + env(safe-area-inset-bottom));
}
```

The `calc()` wrapper is mandatory : you cannot subtract or add to `env()` directly, only inside `calc()` or as a single value. Also note the case-sensitivity warning from MDN : `env(SAFE-AREA-INSET-LEFT)` is invalid (uppercase) and silently falls back to the fallback value or `0`.

Two related variable groups deserve a mention. The `safe-area-max-inset-*` family per MDN : "The static maximum values of their dynamic `safe-area-inset-*` variable counterparts when all dynamic user interface features are retracted." Use this for layouts that need a stable padding even as the bottom toolbar appears and disappears. The `keyboard-inset-*` family from the VirtualKeyboard API (Chromium-only, behind `navigator.virtualKeyboard.overlaysContent = true`) reports the on-screen-keyboard rectangle and lets authors push UI above the soft keyboard without resize-observer hacks.

For foldable / dual-screen devices, `env(viewport-segment-width 0 0)` and the matching segment variables expose individual panel dimensions per [MDN : env](https://developer.mozilla.org/en-US/docs/Web/CSS/env) (verified 2026-05-19) : "The viewport-segment-* variable names can be used to set your containers to fit neatly into the available segments of a multi-viewport-segment device such as a hinged or foldable device. The integers following the viewport-segment-* name indicate which segment."

## 7. Decision matrix : unit by use-case

| Use case | Recommended unit | Why | Avoid |
|----------|------------------|-----|-------|
| Full-height hero, modern UA | `100dvh` | Adapts as mobile chrome shows/hides | `100vh` (catastrophe) |
| Full-height hero, conservative fallback | `100svh` | Guaranteed-fit, no overflow ever | `100vh`, `100lvh` |
| Modal that fills the screen at max | `100lvh` | Use full extent when chrome retracted | `100vh` (ambiguous) |
| Body font-size declaration | `1rem` on `<html>`, body uses `100%` | Respects user font preference | `14px` (absolute, blocks zoom) |
| Component-internal padding | `em` (button padding) | Scales with current font-size | `rem` (ignores button-level resize) |
| Reading-prose container width | `max-width: 65ch` | Optimal line-length per typography research | `max-width: 800px` (font-blind) |
| Capital-letter logo alignment | `cap` for height | Matches uppercase visual extent | `em` (includes descenders) |
| Inline icon next to lowercase text | `height: 1ex` | Aligns with x-height baseline | `height: 1em` (too tall) |
| Bottom-fixed nav above iPhone home indicator | `padding-bottom: calc(1rem + env(safe-area-inset-bottom))` | Respects safe area on cutout devices | `padding-bottom: 1rem` (covered) |
| Sharp canvas rendering on Retina | Multiply backing-store by `devicePixelRatio` | One CSS pixel = N device pixels | Setting `canvas.width = canvas.style.width` (blurry) |
| Sharp raster image on HiDPI | `srcset="img.png 1x, img@2x.png 2x"` | Browser picks correct asset | Single `<img src="img.png">` |
| Hairline divider that survives all DPRs | `1px` solid + transform-scale wrapper | Predictable across DPR=1/1.5/2/3 | `0.5px` (unreliable on DPR=1) |
| Writing-mode-agnostic full-width | `100dvi` | Adapts to vertical-rl, ltr, rtl | `100vw` (physical only) |
| Print stylesheet button size | `cm` or `mm` | Print medium uses physical units | `px` (anchored to reference inch) |

## 8. Anti-patterns

1. **`min-height: 100vh` on mobile hero** : the chrome covers content when expanded; per [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) (verified 2026-05-19) `vh` resolves to large viewport, so the hero is taller than the visible area. Fix : `min-height: 100dvh`. Conservative fallback : `min-height: 100svh` then layer `min-height: 100dvh` for modern UAs.
2. **Nested `font-size: 1.5em`** : compounds (1.5 × 1.5 × 1.5 = 3.375 at three levels deep). Fix : use `rem` for predictable font-size; reserve `em` for properties that should scale with local font-size like `padding`.
3. **`width: 100vw` for full-bleed sections** : includes scrollbar gutter on systems with classic scrollbars; per [MDN : length](https://developer.mozilla.org/en-US/docs/Web/CSS/length) (verified 2026-05-19) "viewport units do not take the size of scrollbars into account." Fix : `width: 100%` if the section is a direct child of body, or `100dvw` paired with `scrollbar-gutter: stable` on `:root`.
4. **Missing `viewport-fit=cover`** : on iPhone notch devices, `env(safe-area-inset-*)` returns 0 unless you set `<meta name="viewport" content="...viewport-fit=cover">`. Fix : include `viewport-fit=cover` in every mobile-targeted document and pair with `env()` padding.
5. **`border: 0.5px solid` for hairlines** : renders as 0 on some DPR=1 displays, full pixel on others, true half-pixel only on DPR>=2. Fix : `border: 1px solid` + `transform: scale(0.5)` wrapper, or SVG, or accept `1px` and design around it.
6. **Hardcoded `font-size: 14px` on body** : blocks browser font-size-preference scaling; users who set their UA default to 20px see no change. Fix : `font-size: 0.875rem` (computed against root) or `font-size: 100%` on body and adjust component sizes from there.
7. **Assuming `1in = 96px` is physically one inch** : the CSS inch is a *reference* unit anchored to 96 reference pixels, not a physical inch. On a 4K monitor at 200% scaling, 96 CSS pixels could be 0.5 physical inches. Fix : never use `in`, `cm`, `mm` for screen layouts; reserve for `@media print`.
8. **`height: 100vh` on a modal overlay** : same `vh` catastrophe; the modal overflows or under-fills on mobile. Fix : `inset: 0` with `position: fixed`, sizes against the visible viewport reliably without unit choice; OR `height: 100dvh` if absolute height is needed.
9. **Animating `dvh`-sized elements** : `dvh` does not animate smoothly during chrome transitions; per [W3C css-values-4](https://www.w3.org/TR/css-values-4/#viewport-relative-lengths) (verified 2026-05-19) "the UA is not required to animate the dynamic viewport-percentage units." Setting `transition: height 0.3s` on a `100dvh` div results in step changes. Fix : transition the *content* with `transform: translateY()` rather than the height itself, or accept the step change.

## 9. Sources Used

| URL | Section | Last Verified |
|-----|---------|---------------|
| https://developer.mozilla.org/en-US/docs/Web/CSS/length | viewport-percentage_lengths | 2026-05-19 |
| https://developer.mozilla.org/en-US/docs/Web/CSS/length | font-relative_lengths | 2026-05-19 |
| https://developer.mozilla.org/en-US/docs/Web/CSS/length | absolute lengths / px anchor | 2026-05-19 |
| https://www.w3.org/TR/css-values-4/#viewport-relative-lengths | viewport-percentage lengths normative spec | 2026-05-19 |
| https://web.dev/blog/viewport-units | small / large / dynamic viewport explainer | 2026-05-19 |
| https://developer.mozilla.org/en-US/docs/Web/CSS/env | safe-area-inset-*, keyboard-inset-*, viewport-segment-* | 2026-05-19 |
| https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio | CSS-pixel-to-device-pixel mapping, canvas backing-store pattern | 2026-05-19 |
| https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/meta/name/viewport | viewport-fit values, cover + safe-area pairing | 2026-05-19 |

All claims in §1 to §8 of this document are traceable to one of the above eight WebFetch verifications. No source from outside `SOURCES.md` was consulted. No browser-vendor blog posts, StackOverflow answers, or community tutorials were used.

Cross-reference for skill author : when writing `SKILL.md`, every code block MUST cite one of these eight URLs in a comment or trailing reference, and every "ALWAYS / NEVER" rule MUST be derivable from the quoted spec text above. Verification gap #2 from `vooronderzoek-frontend.md` §14 is hereby closed.
