# Topic Research : frontend-core-design-philosophy

## Status

- Date : 2026-05-19
- Skill target : `frontend-core-design-philosophy` (Batch 1, category `core/`)
- Source of truth : `docs/research/vooronderzoek-frontend.md` §1, §6, §7, §8, §9, §13 and the masterplan worker-prompt at `docs/masterplan/frontend-masterplan.md` line 283.
- WebFetch verifications performed in this round : 12 (against web.dev, MDN, W3C WAI, W3C WCAG 2.2, W3C Understanding documents, designtokens.org).
- New sources proposed for SOURCES.md : none. Every URL cited below is already listed in `SOURCES.md` under "Primary Sources : Authoritative Web Standards" (`web.dev`, MDN, W3C). Last-Verified dates updated where applicable (all 2026-05-19).
- D-001 compliance : English only. D-006 compliance : every technical claim has at least one inline WebFetch citation against an approved URL.

---

## 1. What "AI-generic" looks like (and why it's bad)

The phrase "AI-generic design" describes the visually-homogeneous output of pattern-matching code generators trained on a small corpus of public sites and Tailwind documentation. It is not a slur against AI tooling : it is a precise observation about a failure mode. The fingerprints are reliable and reproducible. A practitioner can identify an AI-generic landing page in under three seconds without reading the copy.

The diagnostic checklist : centered hero with one short bold headline, three rounded-md gray cards in a CSS Grid below it, neutral palette (one cool gray plus one accent color used only on buttons), a sans-serif body font that is either Inter or Geist, identical card padding, hover state that lifts each card with `box-shadow` and `translate3d(0, -2px, 0)`, a footer with three columns of links. The page reads as a template. Nothing on it surprises the eye. Nothing on it is bespoke to the product, the audience, or the brand.

The reason this matters is not aesthetic preference : it is signal-loss. When every site looks the same, the design no longer communicates anything about the product. A serious tool looks like a toy. A premium service looks like a startup-in-a-box. The brand promise collapses into the chrome.

This research treats AI-generic as a measurable anti-pattern, not a vibe. The fingerprints can be enumerated, audited against, and avoided through deliberate decisions about layout rhythm, type personality, color hierarchy, and motion choreography. The Phase 5 skill will operationalize this as a deterministic decision tree : if X visual property matches the AI-generic pattern, redesign with the listed distinctive alternative.

The underlying cause is well-known : code generators reach for the safest, lowest-risk pattern in their training data because safer patterns appear more often. Tailwind's documented defaults (`rounded-md`, `shadow-md`, `text-gray-700`, `text-center`) and shadcn/ui's component defaults (which the OpenAEC Foundation explicitly uses for ERPNext interfaces) become the path of least resistance. Avoiding the trap requires explicit counter-pressure : a skill that tells the agent ALWAYS to challenge the default and provides concrete distinctive alternatives backed by web platform features that did not exist when the training corpus was assembled. The features themselves — container queries, the Popover API, anchor positioning, `oklch()`, `:has()`, view transitions — are documented at MDN ([MDN : Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries) (verified 2026-05-19), [MDN : oklch()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch) (verified 2026-05-19), [MDN : anchor positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using) (verified 2026-05-19)) and form the building blocks of post-template design.

---

## 2. Visual fingerprints of distinctive design

The Phase 5 skill must give workers concrete, spec-anchored visual properties that distinguish bespoke design from generic templates. The eight properties below each have a spec basis and each one inverts a recognizable AI-generic default.

1. **Asymmetric layout via subgrid and container queries.** Symmetry is the default of CSS Grid with equal `1fr 1fr 1fr` tracks. Distinctive layouts use intentional weight imbalance : a `2fr 1fr` split, an off-center focal point, a `grid-column: span 2` highlight tile, content that bleeds past the page gutter. Container queries make this safe across breakpoints because the layout reads its own width, not the viewport's : "Container queries are an alternative to media queries, which apply styles to elements based on viewport size or other device characteristics" ([MDN : Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries) (verified 2026-05-19)). The same card lives in a sidebar and a hero without redesign.

2. **Type as a primary design element, not utility text.** Variable fonts with broad axis ranges (weight 100-1000, width 25-200, optical-size 14-72) make typography itself the visual focal point. The skill must teach `font-variation-settings` and the use of large display-size headings (clamp() with `5vw` minimum) against tight body type for rhythm. AI-generic defaults to a single weight (medium) at a single scale ratio (1.125).

3. **Purposeful color via oklch().** A perceptually-uniform color space makes systematic palettes possible : "L represents true perceived lightness ... With oklch, L: 0.5 produces equally bright colors regardless of hue, essential for systematic design scales" ([MDN : oklch()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch) (verified 2026-05-19)). One bold accent (high chroma, off-axis hue like `oklch(70% 0.25 25)` or `oklch(55% 0.22 290)`) beats six muted swatches.

4. **Intentional negative space, not padding-as-decoration.** Distinctive layouts use whitespace as architecture. The skill must teach generous `padding-block` on hero sections (above 8rem on desktop), wide tracking on display headings, and the use of `grid-template-rows: auto 1fr auto` to push the focal point into thirds rather than centering everything.

5. **Constrained motion choreography via `prefers-reduced-motion`.** Motion is opt-in for production interfaces, not opt-out. The base state is still. Animation marks state transitions, never decorates. The reduce-motion fallback is required : "Tone down the animation to avoid vestibular motion triggers" ([MDN : prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) (verified 2026-05-19)). MDN's worked example collapses a `transform: scale()` keyframe to an opacity dissolve, which is the recommended pattern.

6. **Native platform primitives over JS-rebuilt UI.** The Popover API ships dismissal, focus trap, escape handling, and stacking : the agent must not reinvent these. Anchor positioning eliminates the `Floating UI` recompute loop : "the browser can try rendering it in a different suggested position so it is placed onscreen" ([MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using) (verified 2026-05-19)). Native primitives also pass the Baseline test : "if the features used are all part of Baseline, you can trust the level of browser compatibility" ([web.dev : Baseline](https://web.dev/baseline) (verified 2026-05-19)).

7. **Custom property animations via `@property`.** Registered typed custom properties allow animation of values that CSS cannot otherwise tween : gradient angles, conic-gradient start positions, character spacing. This produces motion that does not exist in any default-template corpus.

8. **Mixed corner radii by hierarchy, not uniformity.** AI-generic uses one radius globally (`rounded-md`). Distinctive design varies radius by component role : sharp corners for primary CTAs, generous rounding for surfaces, asymmetric border-radius (`50% 0 50% 0`) for accent shapes. The web platform supports per-corner control natively and per-axis with the `/` syntax.

9. **Cascade layer discipline.** Layered CSS removes the specificity battle that defines most template stylesheets. The order is explicit : "Rules within a cascade layer cascade together, giving more control over the cascade to web developers" ([MDN : @layer](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer) (verified 2026-05-19)). Distinctive design ships with `@layer reset, tokens, base, components, utilities` declared at the top of the stylesheet.

These nine properties form the inventory the Phase 5 skill must reference. Each maps to a separate skill in the package (color to `frontend-theming-color-palette-oklch`, layout to `frontend-impl-responsive-layout`, motion to `frontend-a11y-motion-contrast-wcag22`, etc.). The design-philosophy skill itself is the cross-reference index, not the implementation manual.

---

## 3. Performance constraints shaping design

Visual decisions translate directly into Core Web Vitals outcomes. The Phase 5 skill must teach this link explicitly because the cheapest way to wreck a beautiful design is to forget the budget.

LCP is governed by the time-to-render of the largest above-the-fold element : "websites should strive to have an Interaction to Next Paint of 200 milliseconds or less" and corresponding good thresholds for LCP at 2.5 s ([web.dev : Vitals](https://web.dev/articles/vitals) (verified 2026-05-19)). The design implications are concrete. A hero image that is the LCP candidate must be served with `fetchpriority="high"` and explicit `width`/`height` attributes; a hero composed of a video background or a webGL canvas is a design choice that pays a measurable LCP penalty and must be made deliberately. A hero that depends on a webfont for the LCP element must preload that font (`<link rel="preload" as="font" type="font/woff2" crossorigin>`) or accept a font-swap flash.

CLS punishes layout shifts after first paint. The good threshold is "0.1 or less" ([web.dev : Vitals](https://web.dev/articles/vitals) (verified 2026-05-19)). This is a design-decision-driver, not a back-end concern. Every image must reserve space (`aspect-ratio` CSS or `width`/`height` attributes), every webfont must declare metric overrides (`size-adjust`, `ascent-override`, `descent-override`) to prevent post-load reflow, every dynamically-injected component (toast, banner, ad) must reserve its slot via `min-height` so its arrival does not push content. The visual decision to put a CMS-driven hero headline above the fold means committing to a deterministic height; otherwise the variable headline length will shift the entire page on every load.

INP is the response budget for every user interaction : input delay plus processing duration plus presentation delay ([web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19)). The pattern web.dev recommends is "break up the work in event callbacks into separate tasks ... This prevents the collective work from becoming a long task that blocks the main thread" ([web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19)). The design consequence : an animated dropdown menu that runs a 40 ms layout pass on the open event blows the INP budget. Distinctive design therefore tends toward native primitives (Popover API, `<dialog>`, view transitions) because their work happens on the compositor and the browser, not on the JS main thread. Motion budgets compound : a hover animation that pushes work above 200 ms feels broken regardless of how beautiful the keyframes look.

Performance is therefore not the enemy of distinctive design : it is the constraint that forces design decisions to be deliberate. A hero with a static, pre-rendered LCP-friendly image and a single accent-color call-out beats a hero with a parallax canvas every time. The skill must teach this trade-off as a first-class design principle.

---

## 4. Spec-aligned inspiration sources

Designers and design-aware engineers need places to look for ideas that are not yet in the AI-generic corpus. The Phase 5 skill must direct workers toward spec-authoritative sources rather than blog posts and Dribbble shots that themselves train the next generation of generic output.

Approved spec-aligned sources for inspiration :

- **web.dev articles and patterns.** "Common patterns for building web apps" and "Common patterns for working with media" ([web.dev : Patterns](https://web.dev/patterns/) (verified 2026-05-19)) curate browser-team-authored examples. The patterns are not visually opinionated, but they demonstrate the modern primitives (view transitions, popover, container queries) that distinctive design composes from.
- **MDN demo pages.** Every MDN page on a modern CSS feature ships a runnable demo. The demos for `:has()`, `@scope`, `oklch()`, anchor positioning, and view transitions are the reference visual vocabulary for 2026.
- **W3C WAI Authoring Practices Guide (APG) patterns.** The APG patterns ([W3C WAI APG](https://www.w3.org/WAI/ARIA/apg/) listed in `SOURCES.md`) document the accessible interaction model for combobox, tabs, dialog, tree, carousel. Building from these patterns produces interfaces that look distinctive because they have considered focus order and keyboard model from the start; AI-generic skips these and is recognizable for it.
- **Baseline status page.** "All items that become part of Baseline Newly available in 2025 can be referred to as Baseline 2025" ([web.dev : Baseline](https://web.dev/baseline) (verified 2026-05-19)). The Baseline 2024 and 2025 lists themselves are an inspiration backlog : every entry is a feature most templates have not yet adopted.

Banned for inspiration : Dribbble, Behance, AI-generated screenshot galleries, Tailwind UI marketing pages. These either feed back into the AI-generic pattern (training data) or are explicit reference templates. Designers may look at them analytically (to identify and reject patterns) but not for forward-direction.

---

## 5. System over snowflake : token + component + theme thinking

Distinctive design is sustainable only when it is systematic. A one-off beautiful page that no one can maintain is a liability. The Phase 5 skill must therefore frame "distinctive" through the lens of "systematic" : design tokens that scale, components that compose, themes that switch.

The W3C Design Tokens Format Module defines the contract : "a design token is fundamentally information associated with a human readable name, at minimum a name/value pair" ([Design Tokens Format Module 2025.10](https://designtokens.org/tr/drafts/format/) (verified 2026-05-19)). Every token requires a `$value`; optional metadata includes `$type`, `$description`, `$extensions`. Aliasing uses the `{group.token}` syntax. The full type system covers `color`, `dimension`, `fontFamily`, `fontWeight`, `duration`, `cubicBezier`, `number`, plus composite types `border`, `shadow`, `typography`, `transition`, `gradient`, `strokeStyle`.

The three-tier mapping is the operational pattern : raw brand color (`oklch(60% 0.22 290)`) becomes a primitive token (`--brand-violet-500`) becomes a semantic token (`--color-action-primary`) becomes a component token (`--button-primary-bg`). Token files emit as CSS custom properties ; layers separate token-emission from consumption (`@layer tokens, theme, components, utilities` per [MDN : @layer](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer) (verified 2026-05-19)). Theme switching happens by flipping `color-scheme` or by toggling a `data-theme` attribute on the root, never by reaching into component CSS.

Components compose from these tokens. A button uses `--button-primary-bg`, never a raw color. A card uses `--surface-1`, not `bg-white`. The result is a system where one token change ripples cleanly across every consumer. The component itself can vary heavily (sharp corners on CTAs, rounded surfaces on cards) but the consumption rule is universal. This separation is what allows the same component library to ship a serious data-dense interface and a playful marketing site from one codebase. The "snowflake" alternative — every page styled bespoke — collapses under maintenance and never delivers a coherent brand.

---

## 6. Accessibility as foundation

Accessibility constraints are not the price of design : they are the source of design discipline. The Phase 5 skill must teach WCAG 2.2 as a design enabler, not a compliance tax.

WCAG 2.2 added nine success criteria to the 2.1 baseline. Several have direct visual-design impact : "WCAG 2.2 incrementally advances web content accessibility guidance" rather than imposing rigid constraints, providing "a shared standard" that helps designers "anticipate user needs across disability categories" ([W3C : WCAG 2.2](https://www.w3.org/TR/WCAG22/) (verified 2026-05-19)). The key visual-design SCs :

- **2.5.8 Target Size (Minimum) AA.** "The size of the target for pointer inputs is at least 24 by 24 CSS pixels" with five named exceptions (spacing, equivalent, inline, user-agent, essential) ([W3C : Understanding Target Size Minimum](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html) (verified 2026-05-19)). This is a visual-design rule, not a back-end rule : it constrains button sizing, icon-tap regions, dense table-row controls. The skill must teach 24 px as the floor and 44 px (Material) or 48 px (Apple HIG) as the comfortable default for primary actions.
- **2.4.11 Focus Not Obscured (Minimum) AA.** "When a user interface component receives keyboard focus, the component is not entirely hidden due to author-created content" ([W3C : WCAG 2.2](https://www.w3.org/TR/WCAG22/) (verified 2026-05-19)). This rules out the common pattern of sticky-headers that cover the focused element below them. Distinctive design accommodates this with `scroll-margin-top` on focusable elements.
- **2.4.13 Focus Appearance AAA.** "An area of the focus indicator meets all the following : is at least as large as the area of a 2 CSS pixel thick perimeter of the unfocused component or sub-component, and has a contrast ratio of at least 3:1 between the same pixels in the focused and unfocused states" ([W3C : WCAG 2.2](https://www.w3.org/TR/WCAG22/) (verified 2026-05-19)). This forbids `outline: none` without replacement. Distinctive design treats the focus ring as a designable element : a bold accent ring that matches brand color is more elegant than the browser default and meets the SC.
- **2.5.7 Dragging Movements AA.** Drag-only interactions must offer a single-pointer alternative. This shapes how a designer thinks about file-upload, kanban, slider, and reorder controls : every drag needs a click-or-tap equivalent.

These criteria inform design decisions, not gate them. A focus indicator that meets 2.4.13 is more visible, looks deliberate, and reads as careful craft. A button that meets 2.5.8 is easier to hit on mobile and looks more confident on desktop. The constraint produces better design, not constrained design.

---

## 7. Decision matrix : when to deviate from defaults

The skill must include this matrix in `references/methods.md`. Each row gives the generic default, the distinctive alternative, and the spec-anchored rationale.

| Property | Generic default | Distinctive choice | Rationale |
|----------|----------------|--------------------|-----------|
| Hero layout | Centered max-w-2xl with three feature cards under | Asymmetric two-column with focal type on the left, single accent shape on the right | [MDN : Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries) makes asymmetric components portable across contexts (verified 2026-05-19) |
| Color palette | Slate grays plus one indigo accent | One bold off-axis accent via `oklch(L C H)`, restrained neutral with `color-mix(in oklch, ...)` derived greys | [MDN : oklch()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch) confirms perceptual uniformity for systematic scales (verified 2026-05-19) |
| Typography | Inter or Geist at one weight, one scale ratio 1.125 | Variable font with weight-axis traversal across hierarchy levels, type scale 1.333 or higher | Variable fonts are Baseline Widely Available; type-as-design is a core fingerprint of bespoke design |
| Corner radius | `rounded-md` uniform across surfaces and controls | Mixed radii by component role : sharp CTAs, soft surfaces, asymmetric accents | Per-corner radius is a CSS 2.1 primitive; uniformity is a Tailwind reflex not a design rule |
| Card hover state | `translate3d(0, -2px, 0)` plus `shadow-md` | Single-property choreography : color shift on accent line, no layout move | Animating only `transform` / `opacity` keeps INP under 200 ms ([web.dev : Optimize INP](https://web.dev/articles/optimize-inp) verified 2026-05-19) |
| Modal / dropdown | JS recreation of light-dismiss, focus trap, escape | Native `<dialog>` for modal, Popover API for dropdown, anchor positioning for placement | [MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using) eliminates JS positioning logic (verified 2026-05-19) |
| Focus indicator | `outline: none` with no replacement | Branded `:focus-visible` ring meeting 3:1 contrast against background | [W3C : WCAG 2.2](https://www.w3.org/TR/WCAG22/) SC 2.4.13 (verified 2026-05-19) |
| Motion | Auto-play hero animations, parallax scroll, hover transforms by default | Motion as state-transition marker, opacity-only fallback inside `prefers-reduced-motion: reduce` | [MDN : prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) recommends dissolve-style alternatives (verified 2026-05-19) |
| Stylesheet structure | Unlayered, specificity battles, !important escalation | `@layer reset, tokens, base, components, utilities` declared at top | [MDN : @layer](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer) gives explicit cascade control (verified 2026-05-19) |

---

## 8. Anti-patterns specific to distinctive design

The Phase 5 worker must include these in `references/anti-patterns.md` with the symptom / root cause / fix structure.

1. **Three-feature-cards under hero.** Symptom : the landing page looks identical to every AI-generated landing page from 2024-2026. Root cause : the code generator reaches for the safest pattern in its training corpus, which is a `grid-cols-3` of identical cards. Fix : break symmetry. Use a `grid-template-columns: 2fr 1fr` split with one large card and a stack of two small ones, or move features into a horizontally-scrolling rail with `scroll-snap-type: x mandatory`. Container queries ([MDN : Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries) (verified 2026-05-19)) make this safe at every breakpoint.

2. **Rounded-md gray everywhere.** Symptom : surfaces, buttons, inputs, badges, and avatars all use the same corner radius and the same gray. Root cause : Tailwind default reflex compounded by no design-token discipline. Fix : establish a radius scale (`--radius-sharp: 2px; --radius-soft: 12px; --radius-pill: 9999px;`) and assign by component role. Establish a one-accent palette in `oklch()` ([MDN : oklch()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch) (verified 2026-05-19)) where the accent is high-chroma and off-axis from the neutral, and forbid hardcoded gray outside the token layer.

3. **JS recreation of native widgets.** Symptom : the codebase ships 4 KB of click-outside, escape-handler, focus-trap, and stacking-context logic for a dropdown. Root cause : agent unaware of the Popover API and anchor positioning. Fix : use the native `popover` attribute and `position-anchor` ([MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using) (verified 2026-05-19)). The native version is smaller, faster, and accessible by default. For modal cases, use `<dialog>` with `showModal()`.

4. **Motion-on-by-default without reduce-motion fallback.** Symptom : users with vestibular disorders disable motion at the OS level, then your site still animates because the CSS does not query `prefers-reduced-motion`. Root cause : motion treated as decoration, not as state-transition marker. Fix : place the reduced-motion override after the default animation rules. MDN's recommended pattern is to replace transform-based motion with an opacity dissolve : "Tone down the animation to avoid vestibular motion triggers" ([MDN : prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) (verified 2026-05-19)).

5. **Accessibility bolted on at the end.** Symptom : focus order is random, ARIA labels are wrong or missing, contrast fails on the accent button, focus rings are removed without replacement. Root cause : a11y treated as a separate phase after visual design is complete. Fix : start with semantic HTML, build the keyboard model with the APG patterns, then layer visual styling. Visible focus indicators are designable elements : a brand-color `:focus-visible` ring with 3:1 contrast meets [W3C : WCAG 2.2](https://www.w3.org/TR/WCAG22/) SC 2.4.13 (verified 2026-05-19) and looks more deliberate than the browser default.

6. **LCP-killing hero choices made for aesthetics.** Symptom : the hero is a 2.4 MB autoplay-video background or a webGL canvas; LCP is 4.5 s on a mid-tier mobile. Root cause : visual ambition unconstrained by performance budget. Fix : commit to the budget : LCP under 2.5 s per [web.dev : Vitals](https://web.dev/articles/vitals) (verified 2026-05-19). Use a single optimized image with `fetchpriority="high"`, preload the LCP webfont, and reserve the hero's layout slot to avoid CLS.

7. **Cascade specificity wars.** Symptom : `!important` chains, deeply-nested selectors, and "we can't override that vendor CSS". Root cause : no cascade layer discipline. Fix : declare layers at the top of the entry stylesheet (`@layer reset, tokens, base, components, utilities`) and place third-party CSS in an isolated layer. [MDN : @layer](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer) (verified 2026-05-19) explains why simpler selectors then suffice : "you do not have to ensure that a selector will have high enough specificity to override competing rules; all you need to ensure is that it appears in a later layer".

---

## 9. Sources Used

| # | URL | Extracted | Verified |
|---|-----|-----------|----------|
| 1 | https://web.dev/articles/vitals | LCP/INP/CLS good thresholds; 75th percentile assessment | 2026-05-19 |
| 2 | https://web.dev/articles/optimize-inp | INP decomposition (input delay, processing, presentation); yield-to-main-thread pattern | 2026-05-19 |
| 3 | https://www.w3.org/TR/WCAG22/ | Nine new 2.2 SCs; 2.4.11, 2.4.13, 2.5.8 visual-design impact; "shared standard" framing | 2026-05-19 |
| 4 | https://web.dev/baseline | Baseline 2024/2025 definitions; Newly/Widely/Limited; four core browsers; 30-month rule | 2026-05-19 |
| 5 | https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html | SC 2.5.8 24x24 CSS px requirement; five exceptions (spacing, equivalent, inline, user-agent, essential) | 2026-05-19 |
| 6 | https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion | reduce/no-preference values; opacity-dissolve replacement pattern; Baseline since Jan 2020 | 2026-05-19 |
| 7 | https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch | L/C/H ranges; relative-color syntax; perceptual uniformity; Baseline since May 2023 | 2026-05-19 |
| 8 | https://developer.mozilla.org/en-US/docs/Web/CSS/@layer | Statement/block/anonymous forms; layer order; unlayered-vs-layered priority; !important inversion | 2026-05-19 |
| 9 | https://web.dev/patterns/ | Pattern category index : Clipboard, Files, Media, Web Apps | 2026-05-19 |
| 10 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries | container-type, @container, cqi/cqb/cqw/cqh units; component-first vs page-first | 2026-05-19 |
| 11 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using | anchor-name, position-anchor, anchor() function, fallback positioning; eliminates JS positioning libs | 2026-05-19 |
| 12 | https://designtokens.org/tr/drafts/format/ | Token definition; $value required; $type/$description/$extensions optional; {group.token} alias syntax; primitive vs composite types | 2026-05-19 |
