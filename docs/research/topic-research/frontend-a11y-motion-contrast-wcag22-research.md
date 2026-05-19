# Topic Research : frontend-a11y-motion-contrast-wcag22

## Status

- Research date : 2026-05-19
- Skill target : `frontend-a11y-motion-contrast-wcag22` (batch 5, accessibility category)
- Source : drills §14 gap #1 (APCA + WCAG 3 status), expands §6 (motion / contrast preferences)
- WebFetch citations : 12 (8 primary spec / MDN + 1 WCAG 3 working draft + 3 supplementary)
- Word count target : >= 1800 words
- Sources used : all 9 mandatory URLs from the masterplan and the topic-research prompt, plus extra MDN pages for `prefers-reduced-data`, `prefers-reduced-transparency`, and the APCA repository for the experimental-status confirmation.

## 1. WCAG 2.2 new success criteria (9 total)

WCAG 2.2 was published 5 October 2023 as a W3C Recommendation. It adds nine new Success Criteria over WCAG 2.1 and removes one (4.1.1 Parsing). All nine apply only to web content authored by the page author : user-agent default UI and browser-provided rendering are out of scope. The exact normative text per [WCAG 2.2](https://www.w3.org/TR/WCAG22/) (verified 2026-05-19) :

**2.4.11 Focus Not Obscured (Minimum) — AA.** "When a user interface component receives keyboard focus, the component is not entirely hidden due to author-created content." The operative word is *entirely* : partial obscuring (a sticky header covering half of a focused button) passes AA, full obscuring fails. The criterion targets sticky headers, sticky footers, cookie banners, chat-widget overlays, and any author-positioned floating element that hides the focused control. Recommended technique per [Understanding 2.4.11](https://www.w3.org/WAI/WCAG22/Understanding/focus-not-obscured-minimum.html) (verified 2026-05-19) : apply `scroll-padding-top` and `scroll-padding-bottom` to the scroll container so that programmatic scrolling-on-focus offsets the sticky overlap.

**2.4.12 Focus Not Obscured (Enhanced) — AAA.** "When a user interface component receives keyboard focus, no part of the component is hidden by author-created content." Zero tolerance, even for one-pixel overlap. Mostly relevant for AAA-conforming sites (government, public sector in EU jurisdictions).

**2.4.13 Focus Appearance — AAA.** The focus indicator MUST (a) be at least as large as the area of a 2-CSS-pixel thick perimeter of the unfocused component AND (b) have a contrast ratio of at least 3:1 between focused and unfocused states. This is stricter than 1.4.11 Non-text Contrast (which only requires 3:1 against the adjacent background). Default browser focus rings can fail 2.4.13 when overridden by author styles.

**2.5.7 Dragging Movements — AA.** "All functionality that uses a dragging movement for operation can be achieved by a single pointer without dragging, unless dragging is essential or the functionality is determined by the user agent and not modified by the author" (per [Understanding 2.5.7](https://www.w3.org/WAI/WCAG22/Understanding/dragging-movements.html) (verified 2026-05-19)). Covers sliders, drag-and-drop reorder, kanban boards, color wheels, signature pads, map panning. The alternative MUST not require dragging but may use click / tap / arrow keys. Native browser scrolling is exempt (user agent default). Pull-to-refresh is exempt. Author-implemented carousel-by-swipe is NOT exempt and requires next / previous buttons.

**2.5.8 Target Size (Minimum) — AA.** "The size of the target for pointer inputs is at least 24 by 24 CSS pixels," with five exceptions ([Understanding 2.5.8](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html) (verified 2026-05-19)). The measurement is geometric : "A solid 24-by-24 CSS pixel square, aligned to the horizontal and vertical axis," must fit completely inside the target. Exceptions :

1. **Spacing exception** : Targets smaller than 24×24 pass if, when a 24-CSS-pixel-diameter circle is centered on each target's bounding box, the circles do not intersect another target or another undersized target's circle.
2. **Equivalent control** : Function reachable via a different control on the same page that meets 24×24.
3. **Inline** : Target is within a sentence or its size is constrained by the line-height of non-target text (e.g., inline link in body copy).
4. **User-agent control** : Size is determined by the user agent and unchanged by author (native `<input type="date">` calendar picker).
5. **Essential** : Particular presentation is essential or legally required (map pins at precise GPS coordinates, paper-form replica required by law).

**3.2.6 Consistent Help — A.** If a Help mechanism is available (contact info, FAQ link, support chat, etc.), it MUST appear in the same relative order across pages within a related set. Stops the pattern where Help moves between header, footer, and burger menu.

**3.3.7 Redundant Entry — A.** If information was provided in an earlier step of a process, the user MUST not be required to re-enter it. Exception : when it is essential (security verification re-entry), or the previously-entered info is no longer valid (correction step). Pre-filling, copy-from-billing-to-shipping, "use previous answer" options all satisfy this.

**3.3.8 Accessible Authentication (Minimum) — AA.** "A cognitive function test (such as remembering a password or solving a puzzle) is not required for any step in an authentication process" unless an exception applies (per [Understanding 3.3.8](https://www.w3.org/WAI/WCAG22/Understanding/accessible-authentication-minimum.html) (verified 2026-05-19)). Exceptions : (1) alternative non-cognitive method available, (2) a mechanism (password manager autofill, paste support) reduces the burden, (3) the test is object recognition (basic CAPTCHA identifying common items at AA only), (4) the test is identifying personal content. Implications : blocking paste in password fields fails this SC. Blocking `autocomplete="current-password"` fails. Text or math CAPTCHA without alternative fails. Passkeys / WebAuthn satisfy as biometric / PIN ≠ cognitive function test. SMS one-time codes pass IF paste is permitted; fail if user must transcribe digit-by-digit.

**3.3.9 Accessible Authentication (Enhanced) — AAA.** Same as 3.3.8 but exceptions 3 (object recognition) and 4 (personal content) are removed. Only "alternative method" and "mechanism" remain. Most CAPTCHA approaches fail at AAA.

**4.1.1 Parsing was REMOVED in 2.2.** WCAG 2.1's parsing criterion is obsolete because all modern user agents tolerate malformed HTML gracefully and ARIA / DOM validity is covered by 4.1.2 Name, Role, Value. Existing 2.1 audits that flagged 4.1.1 violations should NOT be carried forward into 2.2 audits.

## 2. Contrast SCs (1.4.3, 1.4.6, 1.4.11) + measurement

WCAG 2.x contrast is defined via the relative-luminance ratio `(L1 + 0.05) / (L2 + 0.05)` where `L1` is the lighter of the two and `L2` the darker. Relative luminance is the sRGB-gamma-corrected weighted sum `0.2126·R + 0.7152·G + 0.0722·B`.

**1.4.3 Contrast (Minimum) — AA.** Normal text MUST have contrast ratio of at least 4.5:1 against its background. Large-scale text and images of large-scale text have at least 3:1. Large text is defined as "at least 18 point or 14 point bold or a font size that would yield equivalent size for CJK fonts." 18 pt ≈ 24 CSS pixels at typical 1× zoom. 14 pt bold ≈ 18.66 CSS pixels at typical zoom. The 4.5:1 threshold is what most "AA-compliant" audits enforce.

**1.4.6 Contrast (Enhanced) — AAA.** Normal text 7:1, large text 4.5:1. The `prefers-contrast: more` user preference is the CSS signal that the user *needs* enhanced contrast. Design systems that target AAA should provide alternative tokens behind `@media (prefers-contrast: more)`.

**1.4.11 Non-text Contrast — AA.** UI components and graphical objects MUST have contrast ratio of at least 3:1 against adjacent colors. This covers : focus indicators (vs adjacent unfocused state and vs background), button borders, form-input borders, custom checkbox / radio indicators, the active-state indicator on tabs, icon-only buttons. Note : 1.4.11 explicitly does NOT require 4.5:1 for icon-only buttons (those are non-text), only 3:1, but if the icon contains glyphic text it falls back to 1.4.3 normal-text.

**Measurement tools** : Chrome DevTools accessibility panel reports the ratio inline. The W3C/WAI Color Contrast Analyser desktop tool is the reference implementation. WebAIM Contrast Checker is widely used but is not normative. Critical : measurements use the visible foreground / background pair AT RENDER TIME. If you use opacity, semi-transparent backgrounds, gradients, or backdrop-filter, the actual rendered color must meet the ratio, not the declared color.

## 3. APCA + WCAG 3 status

APCA (Accessible Perceptual Contrast Algorithm) is a new contrast algorithm by Andrew Somers that produces Lc (Lightness contrast) values from -108 to +106 instead of WCAG 2.x luminance ratios. Per the [SAPC-APCA repository](https://github.com/Myndex/SAPC-APCA) (verified 2026-05-19), APCA accounts for perceptual factors that the linear luminance ratio ignores : (a) polarity (dark-on-light vs light-on-dark differ in perceived contrast), (b) spatial frequency (thin text needs more contrast than thick text), (c) font weight. Typical Lc thresholds : 60 for basic readability of body text, 75 for enhanced accessibility, 90 for "high-impact" text.

**WCAG 3 status** : per the [WCAG 3.0 Working Draft](https://www.w3.org/TR/wcag-3.0/) (verified 2026-05-19, dated 3 March 2026), WCAG 3 is a **Working Draft, NOT a Recommendation**. The spec includes its own stability disclaimer : "This is a draft document and may be updated, replaced, or obsoleted by other documents at any time. It is inappropriate to cite this document as other than a work in progress." The conformance model is incomplete (bronze / silver / gold rating proposal is still under discussion) and the current draft contains many "Developing" requirements where "details have been added, but are yet to be worked out." The working group has explicitly warned : "The final set of requirements in WCAG 3 will be different from what is in this draft. Requirements are likely to be added, combined, and removed."

**APCA normative status as of 2026-05-19** : **NOT normative anywhere.** The Working Draft does not include APCA, contrast algorithms, or specific metrics in stable form. The APCA Readability Criterion is described in supporting documents as "a work in progress." A backward-compatible variant called **Bridge PCA** exists, designed so that values can be back-mapped to WCAG 2.x ratios for gradual migration, but Bridge PCA is also informational. Recommendation for skill content : **WCAG 2.2 ratio test (4.5:1 / 3:1 / 7:1) is the binding requirement.** APCA can be mentioned as a forward-looking informational tool; designs that pass both 2.x and APCA-Lc-60 are future-safe. NEVER tell a user that APCA passes mean WCAG conformance. Until WCAG 3 reaches at least Candidate Recommendation, APCA stays informational.

## 4. prefers-reduced-motion patterns

Per [MDN : prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) (verified 2026-05-19), `prefers-reduced-motion` has been Baseline Widely Available since January 2020. Values : `no-preference` (default) and `reduce`. The shorthand `@media (prefers-reduced-motion)` is equivalent to `@media (prefers-reduced-motion: reduce)`. OS mapping : Windows 11 Settings > Accessibility > Visual Effects > Animation Effects ; macOS System Settings > Accessibility > Display > Reduce motion ; iOS Settings > Accessibility > Motion ; Android 9+ Settings > Accessibility > Remove animations ; GNOME Settings > Accessibility > Seeing > Reduced animation.

**Implementation pattern A : opt-in to animation.** This is the safest pattern. Default state is no animation. Animation rules live INSIDE `@media (prefers-reduced-motion: no-preference) { ... }`. Users who never expressed a preference get the animated experience; users who set "reduce" never see motion because the rule simply does not apply.

**Implementation pattern B : reduce to crossfade.** Author writes full animation as default and then INSIDE `@media (prefers-reduced-motion: reduce)` REPLACES the keyframes with `opacity` or `color` transitions instead of `transform`. Vestibular triggers are `scale`, `rotate`, `translate` with significant distance (>20 % of viewport), and parallax. Safe replacements : `opacity` crossfade, `color` transition, instant state swap.

**View Transitions interplay.** Per the masterplan cross-reference to `frontend-impl-view-transitions-scroll-animations`, View Transitions MUST also respect `prefers-reduced-motion`. Inside `@media (prefers-reduced-motion: reduce) { ::view-transition-group(*) { animation: none; } }` disables the default cross-fade. Alternatively, skip the call to `document.startViewTransition()` entirely and update the DOM synchronously when the preference is `reduce`.

**Anti-pattern : the `* { animation-duration: 0.01ms !important }` blanket override.** This nukes ALL animations including ones the user benefits from (loading spinners, progress indicators). Use it as last-resort defensive code only; per MDN, the recommended approach is targeted replacement per component.

**JS detection :** `window.matchMedia('(prefers-reduced-motion: reduce)').matches` returns boolean. Attach `addEventListener('change', ...)` to react to live OS-setting changes without page reload. Critical for SPAs that compose animation imperatively (GSAP, Framer Motion).

## 5. prefers-contrast + forced-colors

These are two distinct media features with different jobs. Per [MDN : prefers-contrast](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-contrast) (verified 2026-05-19), `prefers-contrast` reports a *preference* (more / less contrast) and has been Baseline Widely Available since May 2022. Values : `no-preference` (default), `more`, `less`, `custom`. The `custom` value matches when the user has configured a specific forced-colors palette that does not align with simple more / less semantics; it is the bridge to `forced-colors: active`.

`prefers-contrast: more` is the cue to swap your palette to high-contrast tokens (background to pure white / pure black, borders from 1 px subtle to 2 px solid, text from medium-gray to black, drop shadows replaced by outlines for definition). `prefers-contrast: less` is rarer but signals a user with photosensitivity / migraines who wants reduced contrast.

Per [MDN : forced-colors](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/forced-colors) (verified 2026-05-19), `forced-colors` is a STATE not a preference. It signals that the user agent is currently overriding author colors with a limited system palette (Windows High Contrast Mode is the primary use case; macOS does not have a directly equivalent forced-colors mode but `prefers-contrast: more` is set). Values : `none`, `active`. Baseline Widely Available since September 2022.

**System color keywords forced under `forced-colors: active`** : `Canvas` (background surface), `CanvasText` (normal text), `LinkText` (unvisited links), `VisitedText`, `ActiveText`, `ButtonFace`, `ButtonText`, `ButtonBorder`, `Field` (input background), `FieldText` (input text), `Highlight` (selection background), `HighlightText`, `SelectedItem`, `SelectedItemText`, `Mark`, `MarkText`, `GrayText` (disabled text), `AccentColor`, `AccentColorText`.

**Properties forced to system colors** : `color`, `background-color`, `border-color`, `outline-color`, `text-decoration-color`, `text-emphasis-color`, `column-rule-color`, SVG `fill`, SVG `stroke`. **Properties forced to none** : `box-shadow`, `text-shadow`, and non-URL `background-image` values. `color-scheme` is forced to `light dark`. `scrollbar-color` is forced to `auto`. The browser automatically adds *backplates* behind text overlaid on images so the text stays legible.

**`forced-color-adjust` property** : `auto` (default; system colors apply), `none` (opt out; author CSS remains, backplates disabled), `preserve-parent-color` (limited use; behaves like `none` when color does not inherit).

**Canonical pattern** : let forced-colors do its work, then inside `@media (forced-colors: active)` add an explicit BORDER (since `box-shadow` is forced to `none`) so the component still has a visible boundary. Use system color keywords (`ButtonText`, `CanvasText`) for the border-color value so it matches whatever palette the user has chosen.

**Semantic-matters rule** : Per MDN, the user agent chooses system colors based on NATIVE element semantics, not ARIA roles. `<div role="button">` will NOT get `ButtonText` forced; `<button>` will. Yet another reason native HTML beats div-soup.

**Deprecated `-ms-high-contrast`** : Microsoft's legacy proprietary media query. NEVER use in new code. `forced-colors` is the standard replacement.

## 6. Other user-preference media features

**`prefers-color-scheme: light | dark`** : Baseline Widely Available. The OS-level dark / light mode signal. Cross-references `frontend-theming-dark-light-mode` skill ; only a brief mention here. Note interplay : `@media (prefers-color-scheme: dark) and (prefers-contrast: more)` is the high-contrast-dark-mode combo, often needs separate token overrides.

**`prefers-reduced-data: no-preference | reduce`** : Per [MDN : prefers-reduced-data](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-data) (verified 2026-05-19), **Limited Availability — NO browser currently implements this feature**. The related `Save-Data` HTTP client hint is the production-ready equivalent. When implemented, intended use : conditional font loading, lower-res images, skipping decorative video. Code pattern uses `@media (prefers-reduced-data: no-preference) { @font-face { ... } }` so heavy font assets only load when user has not requested reduced data.

**`prefers-reduced-transparency: no-preference | reduce`** : Per [MDN : prefers-reduced-transparency](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-transparency) (verified 2026-05-19), **Limited Availability** (Experimental). OS mappings : Windows 10/11 Settings > Personalization > Colors > Transparency effects ; macOS System Settings > Accessibility > Display > Reduce transparency ; iOS Settings > Accessibility > Display & Text Size > Reduce Transparency. When set to `reduce`, replace glassmorphism (semi-transparent + `backdrop-filter: blur(...)`) with solid backgrounds and remove backdrop-filter. Cross-reference : `frontend-visual-glassmorphism-backdrop` skill MUST opt-out under this preference.

## 7. Decision matrix : SC by component / interaction

| SC | Trigger scenario | Pass condition | Fail condition | Fix pattern |
|----|------------------|----------------|----------------|-------------|
| 2.4.11 Focus Not Obscured (Min) AA | Tab to a link / button positioned behind a sticky header | Any part of the focused control visible | Entire focused control hidden under header | `scroll-padding-top: <header-height>` on root or scroll container |
| 2.4.12 Focus Not Obscured (Enh) AAA | Tab to control near sticky element | Zero pixels of focused control hidden | Any pixel covered | Avoid sticky overlays, use scroll-margin, ensure dynamic offset on focus |
| 2.4.13 Focus Appearance AAA | Custom focus ring on button | >= 2-px perimeter area and 3:1 contrast with unfocused state | Thin 1-px focus ring or low-contrast color | `:focus-visible { outline: 2px solid var(--focus); outline-offset: 2px }` with high-contrast token |
| 2.5.7 Dragging Movements AA | Slider that only responds to drag | Click-on-track moves thumb | Drag-only | Add click-on-track event ; also Arrow-key support |
| 2.5.8 Target Size (Min) AA | 16-px-square icon button | 24×24 target OR one of 5 exceptions | 16×16 with adjacent dense controls | Increase padding to 24×24 or apply spacing exception (24-px-diameter circle test) |
| 3.2.6 Consistent Help A | Help link in header on page A, in footer on page B | Same relative order across page-set | Position changes per page | Use a shared layout component with Help always in same slot |
| 3.3.7 Redundant Entry A | Multi-step checkout asks for shipping address twice | Pre-fill from earlier step | User must retype same info | Persist form state across steps ; offer "copy from billing" |
| 3.3.8 Accessible Auth (Min) AA | Password field with paste blocked | Paste allowed, autocomplete supported | Paste blocked, autofill blocked, math CAPTCHA only | Remove `onpaste="return false"`, support `autocomplete="current-password"`, offer passkey |
| 3.3.9 Accessible Auth (Enh) AAA | Login flow uses image-recognition CAPTCHA | Passkey, paste-friendly OTP, or mechanism | Any CAPTCHA, even object-recognition | Passkey / WebAuthn primary path |

## 8. Anti-patterns

1. **Animate by default, no `prefers-reduced-motion` check.** Auto-playing carousels, parallax scrolling, scale-on-load animations without a `@media (prefers-reduced-motion: reduce)` override. Fix : either gate the entire animation behind `(prefers-reduced-motion: no-preference)` or write a reduced-intensity variant inside the `reduce` block (opacity crossfade instead of slide).

2. **16×16 icon button.** Tiny X-close buttons, hamburger toggles, table-row delete icons at 16 or 20 px square. Fails 2.5.8 unless the spacing exception or inline exception applies. Fix : increase padding to 24×24 CSS pixels total, or place the icon inside a larger clickable hit area via `::before` / `padding`.

3. **4.4:1 body text contrast.** Designer chose `#767676` text on `#ffffff` background; ratio is 4.48:1, just below 4.5:1. Audit tool flags AA fail. Fix : darken to `#757575` (4.54:1) or beyond. Token contract should ALWAYS test the rendered foreground-background pair, not the abstract value.

4. **Focus indicator obscured by sticky header.** User tabs through a long form; focus moves to a field behind the sticky page header. WCAG 2.4.11 violation. Fix : `html { scroll-padding-top: var(--header-height); }` so programmatic scroll-on-focus offsets the sticky bar. Also useful : `:target` styles get the same padding.

5. **CAPTCHA as only auth path.** Sign-up form requires solving a math captcha or transcribing distorted text. Fails 3.3.8. Even reCAPTCHA v2 image-grid fails at AAA (3.3.9). Fix : passkey / WebAuthn primary, with object-recognition or audio CAPTCHA only as a fallback for the rare case of suspected bot.

6. **Color-only error indication on form.** Red border on invalid field with no icon or text. Fails 1.4.1 (Use of Color) regardless of 2.2. Fix : red border + warning icon + explicit error text via `aria-errormessage`.

7. **Glassmorphism without `prefers-reduced-transparency` opt-out.** Frosted-glass nav bar with `backdrop-filter: blur(20px)` and 30 % background alpha. Users who set Reduce Transparency see no opt-out. Fix : inside `@media (prefers-reduced-transparency: reduce)` set `backdrop-filter: none` and `background: var(--surface-solid)`.

8. **Custom focus ring stripped without replacement.** `*:focus { outline: none }` with no `:focus-visible` replacement. Fails 2.4.7 (Focus Visible) AND 2.4.13 (Focus Appearance AAA). Fix : never strip outline without immediately re-defining a 2-px-or-thicker high-contrast ring via `:focus-visible`.

9. **Drag-only kanban board.** Cards can only be moved between columns via drag. Fails 2.5.7. Fix : add "Move to..." menu button on each card OR keyboard arrow-key navigation between columns.

10. **Forced-colors ignored, custom component invisible.** Author writes a div-based custom button with `background: #007aff; color: white; box-shadow: 0 2px 4px rgba(0,0,0,0.2);`. Under `forced-colors: active`, `box-shadow` is forced to `none`, background may be `Canvas` (white), color may be `CanvasText` (black), result : invisible button shape. Fix : `@media (forced-colors: active) { .custom-button { border: 2px solid ButtonText; } }`.

## 9. Sources Used

| # | URL | Verified | Extracted |
|---|-----|----------|-----------|
| 1 | https://www.w3.org/TR/WCAG22/ | 2026-05-19 | 9 new SCs (exact text), 4.1.1 Parsing removal, contrast ratios 1.4.3 / 1.4.6 / 1.4.11 |
| 2 | https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html | 2026-05-19 | 24-CSS-pixel square measurement test, 5 exceptions (spacing 24-px-diameter circle, equivalent, inline, UA, essential) |
| 3 | https://www.w3.org/WAI/WCAG22/Understanding/focus-not-obscured-minimum.html | 2026-05-19 | "Entirely hidden" definition, sticky-header failure scenarios, scroll-padding remediation, AA vs AAA difference |
| 4 | https://www.w3.org/WAI/WCAG22/Understanding/dragging-movements.html | 2026-05-19 | Single-pointer alternative requirement, examples (sliders, kanban, color wheels), UA-default exception |
| 5 | https://www.w3.org/WAI/WCAG22/Understanding/accessible-authentication-minimum.html | 2026-05-19 | Cognitive-function-test definition, 4 exceptions, paste / autofill / passkey guidance, AA vs AAA delta |
| 6 | https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion | 2026-05-19 | `no-preference` / `reduce`, Baseline Jan 2020, OS mappings, opt-in vs reduce patterns, blanket-override anti-pattern |
| 7 | https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-contrast | 2026-05-19 | `no-preference` / `more` / `less` / `custom`, Baseline May 2022, palette-swap patterns, interplay with prefers-color-scheme |
| 8 | https://developer.mozilla.org/en-US/docs/Web/CSS/@media/forced-colors | 2026-05-19 | `none` / `active`, Baseline Sept 2022, full system-color keyword list, forced properties, `forced-color-adjust` |
| 9 | https://www.w3.org/TR/wcag-3.0/ | 2026-05-19 | Working Draft status (3 Mar 2026), stability disclaimer, APCA not normative, conformance model still developing |
| 10 | https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-data | 2026-05-19 | Limited Availability (no browser implements), `Save-Data` HTTP alternative, conditional font-loading pattern |
| 11 | https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-transparency | 2026-05-19 | Limited Availability (Experimental), OS mappings, glassmorphism opt-out pattern, `backdrop-filter: none` |
| 12 | https://github.com/Myndex/SAPC-APCA | 2026-05-19 | APCA defined, Lc-60 / Lc-75 / Lc-90 thresholds, "work in progress" status, Bridge PCA backward-compatibility |
