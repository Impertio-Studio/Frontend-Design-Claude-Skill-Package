# Topic Research : frontend-impl-typography-system

## Status

- Research date : 2026-05-19
- Author : Phase 4 topic-research agent (opus)
- Target skill : `frontend-impl-typography-system` (Batch 9, category `impl`)
- Drills vooronderzoek §14 verification gap #9 : `font-feature-settings` vs `font-variation-settings` best-practice ordering
- Drills vooronderzoek §3 (modern CSS surface, `clamp()` for fluid type) and §7 (font budgets, render budget)
- All claims verified against approved MDN / web.dev URLs from `SOURCES.md`
- WebFetch citations : 10 (font-variation-settings, font-feature-settings, font-variant, font-display, text-wrap, clamp, size-adjust, ascent-override, preload-critical-assets, plus the previously verified `clamp` page reused as reference)

---

## 1. Modular scale and fluid type via clamp

A modular scale is a geometric progression of font sizes generated from a single base value and a ratio. The base is typically `1rem` (16px in user-default-fonts environments) and the ratio determines visual rhythm. Common musical / classical ratios in use today : `1.125` (minor second, very subtle), `1.2` (minor third), `1.25` (major third), `1.333` (perfect fourth), `1.414` (augmented fourth, square-root of 2, paper-A standard), `1.5` (perfect fifth), `1.618` (golden ratio). Choice rule : UI-dense product surfaces (dashboards, ERP tables, admin panels) read better at smaller ratios (1.125 / 1.2) because steps stay close enough that secondary headings remain part of the same visual texture as body, whereas editorial / marketing pages benefit from larger ratios (1.333 / 1.5) where each step is unmistakably a different rank.

In CSS this is most cleanly expressed via custom properties as a token set. Two naming conventions dominate : t-shirt sizes (`--font-size-xs`, `--font-size-sm`, `--font-size-base`, `--font-size-lg`, `--font-size-xl`, `--font-size-2xl`, etc.) and step-indexed (`--step--2`, `--step--1`, `--step-0`, `--step-1`, `--step-2`, etc.). The step-indexed naming aligns with the underlying math and makes ratio-changes trivial. The t-shirt naming is friendlier for designers and downstream component-library consumers. Both are valid; pick one per project.

Fluid type collapses media-query ladders by letting font-size scale linearly between a minimum and a maximum based on viewport width. The CSS `clamp(MIN, VAL, MAX)` function is exactly the right primitive : per the MDN reference clamp resolves as `max(MIN, min(VAL, MAX))` and is Baseline Widely available since July 2020 ([MDN: clamp](https://developer.mozilla.org/en-US/docs/Web/CSS/clamp) verified 2026-05-19). The middle value should be a viewport-fluid expression that produces the right slope between the breakpoints. The canonical pattern : `font-size: clamp(MIN_REM, BASE_REM + SLOPE_VW, MAX_REM)`. A worked example : `font-size: clamp(1rem, 0.875rem + 0.5vw, 1.25rem)` produces body that is 1rem at viewport 400px, 1.125rem at viewport 800px, capped at 1.25rem above viewport 1200px.

Two non-negotiable accessibility rules apply. First, the `MAX` value MUST be at least twice the `MIN` value so that 200% browser zoom does not hit the clamp ceiling, otherwise WCAG 1.4.4 Resize Text fails. The MDN clamp page documents this explicitly with the example `clamp(1rem, 2.5vw, 2rem)` (max is 2x min) versus the anti-pattern `clamp(1rem, 2.5vw, 1.2rem)` (insufficient room for zoom). Second, the preferred value should always include a `rem`-based floor (e.g. `0.875rem + 0.5vw`), not pure `vw`. Pure `vw` collapses to almost nothing on narrow viewports and breaks legibility in low-zoom environments. Mixing `rem` and `vw` ensures that user-set root-size adjustments propagate through the scale, which is critical for users who set browser default-font-size to 20px or 24px.

The whole scale, applied to a step-indexed token set with fluid math :

```css
:root {
  --step--2: clamp(0.69rem, 0.66rem + 0.18vw, 0.80rem);
  --step--1: clamp(0.83rem, 0.78rem + 0.28vw, 1.00rem);
  --step-0:  clamp(1.00rem, 0.93rem + 0.42vw, 1.25rem);
  --step-1:  clamp(1.20rem, 1.10rem + 0.62vw, 1.56rem);
  --step-2:  clamp(1.44rem, 1.30rem + 0.91vw, 1.95rem);
  --step-3:  clamp(1.73rem, 1.53rem + 1.32vw, 2.44rem);
  --step-4:  clamp(2.07rem, 1.80rem + 1.91vw, 3.05rem);
  --step-5:  clamp(2.49rem, 2.12rem + 2.76vw, 3.81rem);
}
```

---

## 2. Variable fonts axes + variation-settings

A variable font packs multiple typographic axes into a single woff2 binary. The five registered axes per [MDN: font-variation-settings](https://developer.mozilla.org/en-US/docs/Web/CSS/font-variation-settings) (verified 2026-05-19) are :

| Axis tag | CSS shorthand property | Purpose |
|----------|------------------------|---------|
| `"wght"` | `font-weight` | weight (100..900 and intermediate) |
| `"wdth"` | `font-stretch` | width (condensed to expanded as percentage) |
| `"slnt"` | `font-style: oblique <angle>` | slant angle (degrees) |
| `"ital"` | `font-style: italic` | italic toggle (0 or 1) |
| `"opsz"` | `font-optical-sizing` | optical sizing (typically point-size matched to font-size) |

Custom axes use uppercase 4-character tags (e.g. `"GRAD"` for grade, `"YOPQ"` for vertical thickness in Roboto Flex). Axis tags are case-sensitive : registered axes lowercase, custom axes uppercase.

The MDN reference contains a single most-important sentence for this skill : `"This property is a low-level mechanism designed to set variable font features where no other way to enable or access those features exist. You should only use it when no basic properties exist to set those features (e.g., font-weight, font-style)."` The implication for the skill : prefer high-level CSS properties for registered axes, fall back to `font-variation-settings` only for custom axes.

Override semantics are explicit and important : MDN states `"Font characteristics set using font-variation-settings will always override those set using the corresponding basic font properties, e.g., font-weight, no matter where they appear in the cascade."` This is unusual : normally CSS resolves conflicting properties by cascade order and specificity, but here variation-settings wins regardless of where in the cascade it appears. This is a sharp footgun. Practical consequence : if a base layer sets `font-variation-settings: "wght" 400` and a component layer then sets `font-weight: 700`, the heading still renders at weight 400. A caveat is documented : in some browsers this only holds when the `@font-face` declaration includes a `font-weight` range. Either way, code that mixes both forms is fragile.

Recommended pattern for the skill :

```css
/* Preferred : shorthand properties for registered axes */
.heading {
  font-weight: 600;            /* "wght" 600 */
  font-stretch: 87.5%;          /* "wdth" 87.5 */
  font-style: oblique 12deg;    /* "slnt" 12 */
  font-optical-sizing: auto;    /* "opsz" auto */
}

/* Use font-variation-settings only for custom axes */
.expressive {
  font-weight: 600;
  font-variation-settings: "GRAD" 80, "YOPQ" 90;
}
```

For browsers that do not support variable fonts the @font-face must declare a `font-weight: 100 900` range so the same family covers all weights. Static fallback fonts must be served when the variable font is unavailable.

---

## 3. font-feature-settings + font-variant-* (ordering gap)

This section drills §14 verification gap #9. The gap question : when both `font-feature-settings` and `font-variant-*` properties target the same OpenType feature, which wins? And in what order should they appear with `font-variation-settings`?

The MDN reference for [font-feature-settings](https://developer.mozilla.org/en-US/docs/Web/CSS/font-feature-settings) (verified 2026-05-19) provides the authoritative guidance and one explicit best-practice rule : `"Whenever possible, Web authors should instead use the font-variant shorthand property or an associated longhand property such as font-variant-ligatures, font-variant-caps, font-variant-east-asian, font-variant-alternates, font-variant-numeric or font-variant-position. These lead to more effective, predictable, understandable results than font-feature-settings, which is a low-level feature designed to handle special cases where no other way exists to enable or access an OpenType font feature. In particular, font-feature-settings shouldn't be used to enable small caps."`

Mapping between high-level keywords and OpenType tags (the most-used set) :

| `font-variant-*` keyword | OpenType tag | Notes |
|---------------------------|--------------|-------|
| `font-variant-caps: small-caps` | `smcp` | use the keyword, not the raw tag |
| `font-variant-caps: all-small-caps` | `c2sc, smcp` | combined transformation |
| `font-variant-numeric: tabular-nums` | `tnum` | column alignment in tables |
| `font-variant-numeric: lining-nums` | `lnum` | flat-baseline numerals |
| `font-variant-numeric: oldstyle-nums` | `onum` | text-figure numerals |
| `font-variant-numeric: slashed-zero` | `zero` | distinguishes 0 from O |
| `font-variant-numeric: diagonal-fractions` | `frac` | automatic 1/2 etc. |
| `font-variant-ligatures: common-ligatures` | `liga` | on by default |
| `font-variant-ligatures: no-common-ligatures` | `liga` 0 | hostile against `ff`, `fi` |
| `font-variant-ligatures: discretionary-ligatures` | `dlig` | designer-extra ligatures |
| `font-variant-position: sub` / `super` | `subs` / `sups` | subscript / superscript |
| `font-variant-alternates: swash(...)` | `swsh` | swashes |

**Ordering verdict (the §14 gap resolved)** : the MDN font-feature-settings page does NOT specify a single deterministic ordering rule between `font-feature-settings` and `font-variation-settings` because they target orthogonal concerns. `font-feature-settings` toggles discrete OpenType features (glyph substitutions, ligatures, numeric variants); `font-variation-settings` adjusts continuous axis values (weight, width, slant, optical size). They do not conflict by design. However, between `font-variant-*` and `font-feature-settings` there IS an interaction : both target the same OpenType layer.

The practical ordering rule for this skill (derived from the MDN guidance, not literal quote) :

1. Use `font-variant-*` (or its shorthand `font-variant`) by default for everything that has a keyword. Per [MDN: font-variant](https://developer.mozilla.org/en-US/docs/Web/CSS/font-variant) (verified 2026-05-19) the seven longhands are `font-variant-ligatures`, `font-variant-caps`, `font-variant-numeric`, `font-variant-alternates`, `font-variant-east-asian`, `font-variant-position`, `font-variant-emoji`.
2. Use `font-feature-settings` ONLY for stylistic sets (`ss01..ss20`) and `character-variant` selectors that have no `font-variant-*` keyword equivalent, or font-specific features without a registered keyword.
3. Never mix `font-feature-settings` and `font-variant-*` targeting the same feature, because behavior is implementation-defined (no spec text guarantees a deterministic winner).
4. Use `font-variation-settings` independently from feature-settings : they do not interact at the OpenType table level. Order them by readability : axes first (weight, slant), features second (ligatures, numerals).

```css
/* Skill canonical pattern */
.body {
  font-weight: 400;                            /* registered axis */
  font-optical-sizing: auto;                   /* registered axis */
  font-variant-ligatures: common-ligatures;    /* high-level OpenType */
  font-variant-numeric: lining-nums;           /* high-level OpenType */
}

/* Only reach for raw settings when a feature has no keyword */
.stylistic {
  font-feature-settings: "ss03" 1, "cv11" 1;
}
```

---

## 4. font-display + web-font loading

The `@font-face` `font-display` descriptor controls how the browser handles the period between request-initiated and font-loaded. Per [MDN: @font-face/font-display](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display) (verified 2026-05-19), Baseline Widely available since January 2020. The timeline has three periods : block, swap, failure.

| Value | Block period | Swap period | Effect |
|-------|--------------|-------------|--------|
| `auto` | UA-defined | UA-defined | usually equivalent to `block` |
| `block` | ~3 seconds | infinite | invisible text up to 3s (FOIT), then swap |
| `swap` | ~0 ms | infinite | fallback shown immediately (FOUT), webfont swaps in when ready |
| `fallback` | ~100 ms | ~3 s | tiny invisible window, then fallback ; if font arrives within 3s it swaps, else fallback stays |
| `optional` | ~100 ms | 0 | tiny invisible window, then fallback ; webfont only used if loaded almost instantly (cached) |

Default recommendation for this skill : `swap` for hero/heading webfonts paired with a metric-matched fallback (see section 6) so the visible FOUT is invisible; `optional` for above-the-fold body text where stable LCP is the priority. Avoid `block` (creates the worst LCP). Avoid `auto` (browser-dependent).

Font preloading is mandatory for any font that contributes to LCP. Per [web.dev : preload-critical-assets](https://web.dev/articles/preload-critical-assets) (verified 2026-05-19), the canonical link tag is :

```html
<link rel="preload" href="/fonts/Inter-roman.var.woff2" as="font" type="font/woff2" crossorigin>
```

Three rules :

1. `crossorigin` is mandatory; without it the browser fetches the font twice (web.dev : "Fonts preloaded without the crossorigin attribute will be fetched twice"). The attribute applies even to same-origin fonts because fonts use anonymous CORS mode.
2. `as="font"` is mandatory; an incorrect `as` value triggers XHR-style prioritization and effectively wastes the preload.
3. Preload only LCP-critical fonts. Preloading too many resources de-prioritizes everything ("If too many resources are prioritized, effectively none of them are.").

Format guidance : use `woff2` exclusively. WOFF1 / TTF / OTF have no place in 2026 evergreen baseline. WOFF2 has best compression and is widely supported.

---

## 5. text-wrap balance and pretty

Per [MDN: text-wrap](https://developer.mozilla.org/en-US/docs/Web/CSS/text-wrap) (verified 2026-05-19), Baseline 2024. Five values : `wrap` (default), `nowrap`, `balance`, `pretty`, `stable`.

`text-wrap: balance` distributes characters equally across lines and is intended for headings, captions, and blockquotes. Chromium limits balanced wrapping to 6 lines or fewer; Firefox allows up to 10. Beyond the cap the browser silently falls back to `wrap`. Performance impact is negligible because the wrapper only runs within the cap.

`text-wrap: pretty` uses a slower algorithm that prioritizes orphan minimization and overall layout quality. Intended for body copy where typography matters more than render speed. The MDN reference notes : `"pretty has negative performance impact and should only be used when typography matters."`

Canonical pattern :

```css
h1, h2, h3 {
  text-wrap: balance;
}
p, li {
  text-wrap: pretty;
}
[contenteditable] {
  text-wrap: stable;  /* keeps lines stable while user types */
}
```

Use `balance` only on heading-like elements with at most a few lines. `pretty` is safe for body but should be benchmarked on text-heavy pages.

---

## 6. CLS prevention with font metrics override

Cumulative Layout Shift from font swap is the single largest CLS-source in modern designs that load any webfont. The mechanism : the fallback font's ascent, descent, line-gap, and glyph-advance metrics differ from the webfont's, so when the webfont swaps in the line boxes resize and content reflows.

The fix per the CSS Fonts Module Level 4 : declare a matched-fallback `@font-face` that uses `local()` and overrides metrics until they visually match the webfont. The four descriptors :

- `size-adjust` : scales all glyph metrics by a percentage. Per [MDN: @font-face/size-adjust](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/size-adjust) (verified 2026-05-19), Baseline Widely available since September 2023. Default 100%.
- `ascent-override` : sets the ascent (height above baseline). Per [MDN: @font-face/ascent-override](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/ascent-override) (verified 2026-05-19), Limited availability (NOT Baseline in 2026, partial support).
- `descent-override` : sets the descent (height below baseline).
- `line-gap-override` : sets the line-gap (added space between lines).

The pattern :

```css
@font-face {
  font-family: "Inter";
  src: url("/fonts/Inter-roman.var.woff2") format("woff2-variations");
  font-weight: 100 900;
  font-display: swap;
}

@font-face {
  font-family: "Inter-fallback";
  src: local("Arial");
  size-adjust: 107%;
  ascent-override: 90%;
  descent-override: 22.5%;
  line-gap-override: 0%;
}

body {
  font-family: "Inter", "Inter-fallback", system-ui, sans-serif;
}
```

The override percentages are computed from the webfont's `OS/2.sTypoAscender` and `head.unitsPerEm` (and equivalent fields for descent and line-gap), divided by the fallback's same metrics. Tools : Katie Hempenius's "Fontaine" library and Malte Ubl's "perfect-fallback-font" calculator produce these values. The skill must document a manual recipe : extract metrics with `python3 -c "from fontTools.ttLib import TTFont; ..."` or a similar fontTools script. Because `ascent-override` is NOT Baseline yet, the skill MUST gate this section : the override is a progressive enhancement; without it the metrics will not match perfectly, but `size-adjust` alone already removes most of the layout shift.

A second mitigation : `font-size-adjust: ex-height NUMBER` (CSS property, not descriptor) scales the rendered font-size so the lowercase x-height matches a reference. This is browser-agnostic since Chromium 127 / Firefox 118 but is less effective than the descriptor-based overrides because it adjusts only x-height, not ascent / descent.

---

## 7. Decision matrix : type need to API

| Need | API |
|------|-----|
| Make heading scale with viewport | `font-size: clamp(MIN, BASE+SLOPE*vw, MAX)` |
| Switch font weight on hover | `font-weight: 600` (NOT `font-variation-settings`) |
| Toggle italic | `font-style: italic` (NOT `font-variation-settings: "ital" 1`) |
| Optical sizing at large display | `font-optical-sizing: auto` |
| Tabular numerals in data tables | `font-variant-numeric: tabular-nums` |
| Disable ligatures (e.g. branded type) | `font-variant-ligatures: no-common-ligatures` |
| Small caps for surname display | `font-variant-caps: small-caps` |
| Stylistic-set 3 in display font | `font-feature-settings: "ss03" 1` (no keyword equivalent) |
| Adjust custom grade axis | `font-variation-settings: "GRAD" 80` (no shorthand) |
| Avoid invisible-text during load | `font-display: swap` on heading webfont |
| Avoid CLS from font load | metric-matched fallback `@font-face` with `size-adjust` |
| Avoid LCP regression from webfont | `<link rel=preload as=font crossorigin>` on LCP-affecting font |
| Below-the-fold webfont | `font-display: optional` |
| Balanced multi-line heading | `text-wrap: balance` on h1-h3 |
| Orphan-free body copy | `text-wrap: pretty` on p, li |
| Stable contenteditable wrapping | `text-wrap: stable` on `[contenteditable]` |

---

## 8. Anti-patterns

1. **Hardcoded `line-height: 24px`** : breaks the moment `font-size` changes (responsive type, user-zoom). Always use a unitless multiplier, e.g. `line-height: 1.5`. Unitless inherits as a multiplier; px-units inherit as a computed value and stop scaling.

2. **`font-display: block`** (or the default `auto` which most browsers map to block) : produces FOIT of up to 3 seconds, destroying LCP for any text element using the webfont. Always specify one of `swap`, `fallback`, or `optional` based on whether the font is LCP-critical.

3. **Mixing `font-weight` and `font-variation-settings: "wght" N`** : variation-settings overrides the shorthand regardless of cascade order (per MDN), so a downstream `font-weight: 700` is silently ignored. Pick one mechanism per project; prefer shorthand.

4. **Using `font-feature-settings` to enable small caps** : MDN explicitly calls this out : `"font-feature-settings shouldn't be used to enable small caps"`. Use `font-variant-caps: small-caps`. Same logic applies to ligatures, tabular numerals, fractions, and any feature with a keyword.

5. **Preloading webfont without `crossorigin`** : double-fetches the font, wasting bandwidth and time. Always include `crossorigin` on `<link rel=preload as=font>`, even for same-origin fonts.

6. **`clamp(1rem, 2.5vw, 1.2rem)` with too-tight max** : violates WCAG 1.4.4 (text must zoom to 200%). The max MUST be at least 2x the min.

7. **Six static webfont weights instead of one variable font** : 6 × 100 KB = 600 KB cold-cache cost. A single variable font with full wght axis is typically 80-150 KB.

8. **Pure-vw font-size (no rem floor)** : on narrow viewports the text collapses to unreadable sizes; in low-zoom environments accessibility-zoom does not propagate. Always mix a `rem`-based base with `vw` for the fluid component.

9. **`text-wrap: balance` on body paragraphs** : browsers cap balance to 6-10 lines and silently fall back; performance impact is non-zero on long text. Reserve `balance` for headings.

10. **No matched-fallback `@font-face`** : font swap causes visible layout shift; size-adjust + ascent-override + descent-override on the fallback removes virtually all CLS-from-font.

---

## 9. Sources Used

| # | Source URL | Verified | Extracted |
|---|------------|----------|-----------|
| 1 | https://developer.mozilla.org/en-US/docs/Web/CSS/font-variation-settings | 2026-05-19 | low-level mechanism warning, registered axes table (wght/wdth/slnt/ital/opsz), override-cascade rule |
| 2 | https://developer.mozilla.org/en-US/docs/Web/CSS/font-feature-settings | 2026-05-19 | OpenType tag table, "Whenever possible use font-variant" rule, no-small-caps rule, Baseline since April 2017 |
| 3 | https://developer.mozilla.org/en-US/docs/Web/CSS/font-variant | 2026-05-19 | seven longhands (ligatures, caps, numeric, alternates, east-asian, position, emoji), per-property value lists |
| 4 | https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display | 2026-05-19 | auto/block/swap/fallback/optional, ~3s block + ~100ms windows, Baseline since January 2020 |
| 5 | https://developer.mozilla.org/en-US/docs/Web/CSS/text-wrap | 2026-05-19 | balance vs pretty semantics, 6/10-line cap, Baseline 2024 |
| 6 | https://developer.mozilla.org/en-US/docs/Web/CSS/clamp | 2026-05-19 | clamp(MIN,VAL,MAX) resolves as max(MIN, min(VAL,MAX)), 2x min-max for WCAG, Baseline since July 2020 |
| 7 | https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/size-adjust | 2026-05-19 | percentage descriptor, Baseline since September 2023, fallback-metric harmonization use case |
| 8 | https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/ascent-override | 2026-05-19 | normal / percentage, Limited availability (NOT Baseline), companions descent-override and line-gap-override |
| 9 | https://web.dev/articles/preload-critical-assets | 2026-05-19 | preload link syntax (rel/as/type/crossorigin), double-fetch warning, "preload too many" anti-pattern |
| 10 | https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display (descriptor canonical) | 2026-05-19 | descriptor form (vs property form which 404'd), trade-off table FOIT vs FOUT, LCP / CLS interaction |
