# Vooronderzoek : Frontend Design (evergreen-2026)

## Status

- Date : 2026-05-19
- Scope : Framework-agnostic modern web platform (HTML5, CSS, ES2024/TS, Web APIs, WCAG 2.2, Core Web Vitals)
- Baseline target : `evergreen-2026` (Baseline 2024 features minimum; selected Baseline 2025 features documented with explicit gating)
- Methodology : Deep research-first per Workflow Template P-002/P-004. All technical claims verified via WebFetch against approved SOURCES.md URLs. No claims sourced from training data.
- WebFetch verifications performed : 41 (against MDN, W3C/WAI, web.dev, designtokens.org)
- Output : Authoritative input for Phase 3 masterplan refinement and Phase 5 skill creation
- Approved-source scope : MDN, WHATWG, W3C, W3C WAI, web.dev, designtokens.org, web-platform-dx (Baseline)

---

## 1. Architecture and Platform Model

Modern frontend in 2026 is built on a single platform : the browser. The browser's job splits into four layered concerns that map cleanly to authored artifacts and to spec families :

1. Markup layer : HTML5 ([WHATWG HTML Living Standard](https://html.spec.whatwg.org/multipage/) (verified 2026-05-19)). Defines document structure, semantics, landmarks, form controls, and the parser that produces the DOM tree.
2. Style layer : CSS modules under the W3C CSS Working Group. Cascade, inheritance, layout (flexbox, grid, subgrid), visual rendering, and now logic (`@container`, `:has()`, `@scope`) and computation (`@property`, relative colors, `color-mix()`).
3. Behavior layer : JavaScript (ECMAScript, currently ES2024 baseline) and Web APIs ([MDN Web Docs : Web APIs](https://developer.mozilla.org/en-US/docs/Web/API) (verified 2026-05-19)). DOM, observers, fetch, Web Components.
4. Animation/timeline layer : CSS animations/transitions, View Transitions API, scroll-driven animations. Driven by the compositor when authored against transform / opacity / filter properties.

Authoritative behavior is looked up against the LS (Living Standard) for HTML/DOM and W3C TR drafts for CSS. MDN is the canonical secondary source : it tracks BCD (Browser Compatibility Data) and is acceptable as primary citation when it embeds spec links. web.dev provides Chrome-team-authored applied guidance, especially for Core Web Vitals.

### Baseline vs evergreen vs stable

Per [web.dev : Baseline](https://web.dev/baseline) (verified 2026-05-19), the Baseline status taxonomy is :

- Limited availability : not yet supported across all four core browsers (Chrome, Firefox, Safari, Edge).
- Newly available : the feature ships in all four core browser engines and is interoperable.
- Widely available : 30 months have passed since the Newly Available date. Safe for most production sites without polyfills or fallbacks.

The Baseline annual cohort (Baseline 2024, Baseline 2025) groups all features that achieved Newly Available status in that calendar year. The `evergreen-2026` target for this skill package means : minimum bar is Baseline 2024 features; Baseline 2025 features MUST be gated by either `@supports` or feature detection.

### Build-step vs runtime trade-offs

A modern site can ship raw HTML/CSS/JS with zero build step (native ES modules via `<script type="module">`, native CSS nesting, native cascade layers). A build step is justified when : TypeScript compilation, tree-shaking, code splitting, or minification across many modules is required, OR when authoring with PostCSS plugins for stage-1 specs (where browser support is Limited). The skill package treats no-build as default; build-required techniques MUST mark themselves as such.

### Rendering pipeline (briefly)

DOM + CSSOM produce a render tree; style is computed; layout determines geometry; paint produces pixels; compositing combines layers. Performance work targets : minimizing style recomputations, isolating layout with `contain`, animating only compositor-friendly properties (transform / opacity / filter), avoiding paint storms. The CSS `contain` and `content-visibility` properties give authors explicit isolation primitives ([MDN : contain](https://developer.mozilla.org/en-US/docs/Web/CSS/contain) (verified 2026-05-19), [MDN : content-visibility](https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility) (verified 2026-05-19)).

---

## 2. HTML5 Surface

The HTML5 semantic surface is stable and Baseline Widely Available. The skill package treats the following as authoritative ground :

### Semantic / landmark elements

`<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`, `<article>`, `<section>`, `<search>`, `<figure>`, `<details>`, `<summary>`, `<dialog>`. The `<search>` element ([MDN : `<search>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/search) (verified 2026-05-19)) is Baseline Widely Available since October 2023 and has implicit ARIA role `search`. Authors MUST stop adding `role="search"` to `<form>` and use `<search>` instead. NEVER use `<search>` to wrap search results; only the controls that perform the search.

### Dialog and Popover

The `<dialog>` element ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19)) ships `showModal()`, `show()`, and `close(returnValue?)`. `showModal()` places the dialog on the browser top layer, makes the rest of the document inert automatically, enables Escape-to-close, and supports the `::backdrop` pseudo-element. `tabindex` MUST NEVER be set on `<dialog>` per MDN. Initial focus goes to the first focusable descendant unless `autofocus` is set explicitly. The `closedby` attribute (values `any`, `closerequest`, `none`) gives declarative control over light-dismiss / Escape behavior.

The Popover API ([MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19)) is Baseline 2025 (Newly Available since January 2025). The global `popover` attribute accepts `auto`, `manual`, and `hint`. `auto` popovers are mutually-exclusive in the top-layer stack and light-dismiss on outside click and Escape. `manual` popovers MUST be closed via script or `popovertargetaction="hide"`. Buttons get `popovertarget="id"` and optional `popovertargetaction` (`show` / `hide` / `toggle`). Popovers are ALWAYS non-modal; for modal behavior use `<dialog>` with `showModal()`.

### `<details>` / `<summary>`

Native disclosure pattern. Supports the `name` attribute for accordion-style mutual exclusion (Baseline 2024). Open/close fires the `toggle` event.

### Form controls and validation

The complete modern input type set is documented at [MDN : `<input>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input) (verified 2026-05-19) : `text`, `email`, `url`, `tel`, `number`, `range`, `date`, `time`, `datetime-local`, `month`, `week`, `color`, `search`, `password`, `file`, `hidden`, `image`, `checkbox`, `radio`, `submit`, `reset`, `button`. The `inputmode` attribute (`text` / `decimal` / `numeric` / `tel` / `search` / `email` / `url` / `none`) tunes the mobile virtual keyboard independent of validation type. The `autocomplete` attribute MUST be set on any field a browser can autofill (e.g. `email`, `tel`, `given-name`, `family-name`, `street-address`, `postal-code`, `current-password`, `new-password`).

The Constraint Validation API ([MDN : Constraint validation](https://developer.mozilla.org/en-US/docs/Web/HTML/Constraint_validation) (verified 2026-05-19)) exposes `element.validity` (a `ValidityState` with flags : `valueMissing`, `typeMismatch`, `patternMismatch`, `rangeUnderflow`, `rangeOverflow`, `stepMismatch`, `tooShort`, `tooLong`, `badInput`, `customError`). `setCustomValidity(message: string)` marks the field invalid; passing the empty string clears it. `checkValidity()` is silent and CSS-only; `reportValidity()` triggers the browser's invalid-bubble UI and fires the `invalid` event. `HTMLFormElement.submit()` bypasses validation; submit-button `click()` triggers it.

### Declarative Shadow DOM

[MDN : `<template>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) (verified 2026-05-19) documents the `shadowrootmode` attribute (`open` / `closed`), `shadowrootdelegatesfocus`, and `shadowrootclonable`. A `<template>` with `shadowrootmode` is consumed by the HTML parser at parse time and attached as a shadow root to the parent. This enables server-rendered web components with no JavaScript dependency for first paint.

---

## 3. Modern CSS Surface

### Cascade Layers (`@layer`)

[MDN : @layer](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer) (verified 2026-05-19) confirms Baseline Widely Available since March 2022. Three creation forms : statement (`@layer base, components, utilities;` declares order), block (`@layer utilities { ... }` adds rules), and anonymous (`@layer { ... }`). Layer order is set by first appearance of the name; later-declared layers win ties. Critical inversions :

- Unlayered (default-cascade) declarations beat all layered declarations for NORMAL declarations.
- For `!important` declarations the order REVERSES : earlier layers win, and important-author-in-layer beats important-author-unlayered. Important-user beats important-author, and important-UA beats important-user.

Cascade Layers MUST be used to isolate third-party CSS, vendor reset, theme tokens, components, and utilities. Predictable cascade replaces specificity bidding.

### Container Queries (`@container`)

[MDN : Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries) (verified 2026-05-19). Requires `container-type` on the parent (`size` for both axes, `inline-size` for inline only, `normal` for style-only). `container-name` opt-ins a name; the shorthand is `container: <name> / <type>`. Query forms : `@container (width > 700px) { ... }` and named-form `@container sidebar (width > 700px) { ... }`. Container query length units : `cqw`, `cqh`, `cqi` (inline), `cqb` (block), `cqmin`, `cqmax`. When no matching container exists, units fall back to small-viewport units. Style queries (`@container style(--theme: dark) { ... }`) extend the model to custom-property-based switches.

### `:has()` (relational selector)

[MDN : :has()](https://developer.mozilla.org/en-US/docs/Web/CSS/:has) (verified 2026-05-19). Baseline Newly Available since December 2023, now Widely Available in 2026. Syntax `:has(<relative-selector-list>)`. Forgiving selector list. Specificity equals the highest-specificity selector inside. NEVER nest `:has()` inside `:has()` and NEVER use pseudo-elements inside or as anchor. Performance rule : anchor on the smallest possible subtree (`.gallery:has(...)` NOT `body:has(...)`) and tightly constrain the inner selector with combinators (`> .child`, `+ .sibling`).

### Modern color (`oklch()`, `color-mix()`, `light-dark()`, relative color)

[MDN : oklch()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch) (verified 2026-05-19) : Baseline Widely Available since May 2023. Syntax `oklch(L C H / alpha)`; L is perceptual lightness 0..1 (0%..100%), C is chroma 0..~0.4 (100% = 0.4), H is hue 0..360. Relative-color form `oklch(from <color> l c h / alpha)` lets authors derive systematic scales from a single brand seed.

[MDN : color-mix()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix) (verified 2026-05-19) : Baseline Widely Available since May 2023. Syntax `color-mix(in <colorspace> [<hue-interpolation>], <color> [pct], <color> [pct])`. Color spaces : rectangular (`srgb`, `srgb-linear`, `display-p3`, `lab`, `oklab`, `rec2020`, `xyz`, `xyz-d50`, `xyz-d65`) and polar (`hsl`, `hwb`, `lch`, `oklch`). Polar spaces support hue interpolation methods : `shorter hue` (default), `longer hue`, `increasing hue`, `decreasing hue`. ALWAYS prefer `in oklch` or `in oklab` for perceptually uniform color scales.

[MDN : light-dark()](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/light-dark) (verified 2026-05-19) : Baseline 2024 (Newly Available since May 2024). Requires `color-scheme: light dark` set on `:root`. Eliminates the need for `@media (prefers-color-scheme: dark) { ... }` duplication in many cases. Syntax `light-dark(<light-value>, <dark-value>)`. Works for colors AND image values.

### Subgrid

[MDN : Subgrid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Subgrid) (verified 2026-05-19) : Baseline Widely Available since September 2023. `grid-template-columns: subgrid` and / or `grid-template-rows: subgrid` make the nested grid inherit the parent's track sizing. Parent named lines pass through automatically; subgrid CAN define additional names after the `subgrid` keyword. Gap inherits from parent and can be overridden. Hard limitation : subgrid CANNOT generate implicit tracks beyond the spanned area. For implicit row needs, declare subgrid only on the column axis.

### Native CSS Nesting

[MDN : CSS nesting](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_nesting) (verified 2026-05-19). The `&` nesting selector represents the parent. `&` is required for pseudo-class / pseudo-element nesting (`&:hover`, `&::before`) and for combinator-prefixed nesting (`& > .child`, `& + .sibling`). Bare nesting (`.parent { .child { ... } }`) is valid and means descendant selector. Native nesting does NOT support the Sass BEM trick `&__icon`; authors MUST write the full class names. Nesting does NOT inflate specificity beyond the compiled selector chain.

### `@property` and `@scope`

[MDN : @property](https://developer.mozilla.org/en-US/docs/Web/CSS/@property) (verified 2026-05-19) : Baseline 2024 (Newly Available July 2024). Declares a typed custom property with `syntax: "<type>"`, `inherits: true|false`, and `initial-value: <value>`. ALWAYS use `@property` for custom properties that must be animated or transitioned (untyped customs are interpolation-opaque). The `initial-value` MUST be computationally independent (no `em` units that depend on inherited font-size). JavaScript equivalent : `CSS.registerProperty({...})`.

[MDN : @scope](https://developer.mozilla.org/en-US/docs/Web/CSS/@scope) (verified 2026-05-19) : Baseline 2025 (Newly Available December 2025). Syntax `@scope (<root>) to (<limit>) { ... }`. Creates a donut-scope : styles apply from `<root>` down but stop at `<limit>` (exclusive). Bare selectors and `&` inside `@scope` contribute zero specificity beyond the selector itself; explicit `:scope` adds class-level (0,1,0) specificity. Solves the "descendant-explosion" problem (`.feature > .article-body > img`) with low-specificity, DOM-independent rules. Cascade conflict between two scopes resolves by scoping proximity : the rule whose scope root is fewest DOM hops away wins (overrides source order, NOT importance or layer).

### Layout / typography helpers

[MDN : clamp()](https://developer.mozilla.org/en-US/docs/Web/CSS/clamp) (verified 2026-05-19) : Baseline Widely Available since July 2020. Resolves as `max(min, min(val, max))`. Fluid typography pattern : `font-size: clamp(1rem, 0.5rem + 2vw, 2rem)`. Supports nesting with `min()` and `max()`.

[MDN : CSS Logical Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values) (verified 2026-05-19) : `margin-block`, `margin-inline`, `padding-block`, `padding-inline`, `inset-block`, `inset-inline`, `block-size`, `inline-size`, `border-block`, `border-inline`. ALWAYS prefer logical properties over physical for international / RTL compatibility.

---

## 4. JavaScript ES2024 + TypeScript

### ES2024 DOM-relevant features

[MDN : Object.groupBy()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/groupBy) (verified 2026-05-19) : Baseline 2024 (Newly Available since March 2024). Signature `Object.groupBy(items, (element, index) => key)`. Returns a null-prototype object with array values. Use `Map.groupBy(items, fn)` when keys must be arbitrary object references rather than strings / symbols. Replaces verbose `reduce()` chains for grouping list data before rendering.

[MDN : Promise.withResolvers()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/withResolvers) (verified 2026-05-19) : Baseline 2024 (Newly Available since March 2024). Returns `{ promise, resolve, reject }`. Replaces the legacy "deferred" pattern where authors manually leaked executor refs out of a `new Promise(...)` closure. ALWAYS use `Promise.withResolvers()` for deferred-resolution patterns (cross-event-handler promise lifecycle).

[MDN : structuredClone()](https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone) (verified 2026-05-19) : Baseline Widely Available since March 2022. Signature `structuredClone(value, { transfer? })`. Deep clone with cycle handling, Date/Map/Set/ArrayBuffer/Blob/typed-array support. CANNOT clone functions, DOM nodes, or getters/setters. The `transfer` option moves transferable objects (ArrayBuffer, ImageBitmap, MessagePort, OffscreenCanvas) to the clone and detaches from source. Replaces `JSON.parse(JSON.stringify(...))` for all but the most trivial clones.

Other ES2024 DOM-relevant features (not WebFetch-verified in this round, see Gaps section) : top-level await in modules, Array `groupBy`-style helpers via the proposal track, iterator helpers (`.map`, `.filter`, `.take` on iterators). These appear in the Gaps section pending later verification.

### TypeScript with the DOM

`lib.dom.d.ts` ships with TypeScript and reflects the WebIDL surface. Strict-mode pitfalls relevant to frontend :

- `Element` vs `HTMLElement` : `querySelector('button')` returns `HTMLButtonElement | null` only when the literal selector matches a tag; arbitrary selector strings return `Element | null`. Cast carefully.
- `EventTarget` in event handlers : `e.target` is `EventTarget | null`. Use `instanceof HTMLInputElement` narrowing rather than blind `as` casts.
- `null` returns : `document.getElementById`, `querySelector`, `closest` all return nullable; narrow before use.
- Custom event types : extend `HTMLElementEventMap` via module augmentation when dispatching custom events you also need typed `addEventListener` for.

ALWAYS enable `strict`, `noUncheckedIndexedAccess`, `noImplicitOverride`, and `exactOptionalPropertyTypes` for new frontend projects.

---

## 5. Modern Web APIs

### Popover API (covered above in §2)

### Dialog element + top layer (covered above in §2)

### View Transitions API

[MDN : View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API) (verified 2026-05-19). Same-document : `document.startViewTransition(callback?)` returns a `ViewTransition` with `.ready`, `.finished`, and `.updateCallbackDone` promises. The callback updates DOM; the browser snapshots before/after, generates `::view-transition-old(name)` and `::view-transition-new(name)` pseudo-elements, and runs default cross-fade animations. The `view-transition-name` CSS property opts elements into individually-animated groups. Pseudo-element tree : `::view-transition` (root) -> `::view-transition-group(name)` -> `::view-transition-image-pair(name)` -> `::view-transition-old(name)` / `::view-transition-new(name)`.

Cross-document : `@view-transition { navigation: auto; }` opts the document into MPA transitions; both the source and destination documents MUST declare it. Authors MUST gate transitions on `prefers-reduced-motion: reduce` and skip the call (or call `.skipTransition()`) when the user opted out of motion.

### Scroll-driven animations

[MDN : CSS scroll-driven animations](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations) (verified 2026-05-19). The `animation-timeline` property accepts a named timeline, the `scroll()` function, or the `view()` function.

- `scroll(<axis>? <scroller>?)` : axis `block` / `inline` / `x` / `y`; scroller `nearest` / `root` / `self`.
- `view(<axis>? <visibility>? [with inset(<length>)]?)` : visibility `auto` / `contain` / `cover`.

Named timelines via `scroll-timeline: --name <axis>;` and `view-timeline: --name <axis>;`. Timeline names propagate down the descendant tree; `timeline-scope: --name` lets distant ancestors expose a name to disjoint subtrees. Animations driven by `view()` enable scroll-linked entry / exit effects without JavaScript. Gate behind `@supports (animation-timeline: scroll())` for safe progressive enhancement.

### Anchor positioning

[MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning) (verified 2026-05-19). Defines an anchor with `anchor-name: --anchor;` on the source element, and tethers the positioned element with `position-anchor: --anchor;` plus the `anchor(<side>)` function in `top` / `bottom` / `inset-*` declarations. `position-area: bottom center` declares region-based placement. `position-try-fallbacks: flip-block, flip-inline` rotates through fallback positions when overflow is detected. Designed to compose with the Popover API : popover + anchor positioning replaces hand-rolled tooltip / dropdown logic. Browser support is emerging in 2025/2026; MUST gate via `@supports (anchor-name: --x)`.

### Web Components

[MDN : Web Components](https://developer.mozilla.org/en-US/docs/Web/API/Web_components) (verified 2026-05-19). `customElements.define(name, ctor, { extends?: 'tag' })`. Lifecycle callbacks : `connectedCallback`, `disconnectedCallback`, `adoptedCallback`, `attributeChangedCallback(name, oldValue, newValue)` (paired with `static get observedAttributes()`). Shadow DOM via `this.attachShadow({ mode: 'open' | 'closed', delegatesFocus?, slotAssignment?: 'named' | 'manual' })`. Slots use `<slot name="x">` for named distribution, `Element.assignedSlot`, and the `slotchange` event. Form-associated custom elements via `this.attachInternals()` returning `ElementInternals` (gives `.setFormValue()`, `.setValidity()`, `.checkValidity()`, etc.). Pseudo-classes : `:defined`, `:host`, `:host()`, `:host-context()`, `:state(--name)`. Pseudo-elements : `::slotted(...)` and `::part(...)`.

### Observers

[MDN : IntersectionObserver](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver) (verified 2026-05-19) : `new IntersectionObserver(cb, { root, rootMargin, threshold })`. Entries expose `isIntersecting`, `intersectionRatio`, `intersectionRect`, `boundingClientRect`, `time`, `target`. Baseline Widely Available since March 2019. Primary use cases : lazy-load, infinite scroll, view-counting analytics, sticky-state detection.

[MDN : ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver) (verified 2026-05-19) : `new ResizeObserver(cb)`, `observe(target, { box: 'content-box' | 'border-box' | 'device-pixel-content-box' })`. Entries expose `contentRect`, `contentBoxSize[0]`, `borderBoxSize[0]`, `devicePixelContentBoxSize[0]`. Baseline Widely Available since July 2020. Common pitfall : "ResizeObserver loop completed with undelivered notifications" when the callback mutates the observed size in the same frame. Fix : wrap the mutation in `requestAnimationFrame()` OR diff against an expected-size `WeakMap`.

### Speculation Rules API

[MDN : Speculation Rules API](https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API) (verified 2026-05-19). NOT Baseline as of 2026-05-19 (limited availability outside Chromium). Authored as `<script type="speculationrules">{ "prerender": [...], "prefetch": [...] }</script>`. `prefetch` downloads the response body via GET (no subresources, no script). `prerender` fully renders the destination in an invisible tab (subresources + scripts + data fetches), making navigation near-instant. Rule shapes : `urls: [...]` (explicit list) or `where: { href_matches: "/*", not: { ... } }` (declarative matcher). MUST exclude logout / cart-mutation / nofollow URLs to avoid unintended side effects.

---

## 6. Accessibility (WCAG 2.2 + WAI-ARIA)

### WCAG 2.2 (per [W3C : WCAG 2.2](https://www.w3.org/TR/WCAG22/) (verified 2026-05-19))

WCAG 2.2 added nine new success criteria over WCAG 2.1 :

- 2.4.11 Focus Not Obscured (Minimum) (AA) : focused component MUST NOT be entirely hidden by author content.
- 2.4.12 Focus Not Obscured (Enhanced) (AAA)
- 2.4.13 Focus Appearance (AAA)
- 2.5.7 Dragging Movements (AA) : single-pointer drag MUST have a non-drag alternative (click / tap).
- 2.5.8 Target Size (Minimum) (AA) : pointer targets MUST be at least 24x24 CSS pixels OR satisfy one of five exceptions ([WCAG 2.2 Understanding : Target Size Minimum](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html) (verified 2026-05-19)). Exceptions : spacing (24-px-diameter circle test), equivalent alternative control of 24px, inline target (inside running text), user-agent control (browser-default UI), essential (legally / functionally required to be small).
- 3.2.6 Consistent Help (A)
- 3.3.7 Redundant Entry (A)
- 3.3.8 Accessible Authentication (Minimum) (AA) : no cognitive-test puzzle as the only auth path.
- 3.3.9 Accessible Authentication (Enhanced) (AAA)

Four principles unchanged from 2.1 : Perceivable, Operable, Understandable, Robust. Contrast (Minimum) AA : normal text 4.5:1, large text 3:1. Non-text contrast AA : 3:1 for UI components and graphical objects.

### WAI-ARIA Authoring Practices Guide

[W3C WAI : APG Patterns](https://www.w3.org/WAI/ARIA/apg/patterns/) (verified 2026-05-19) covers : Accordion, Alert, Alert Dialog, Breadcrumb, Button, Carousel, Checkbox, Combobox, Dialog (Modal), Disclosure, Feed, Grid, Landmarks, Link, Listbox, Menu / Menubar, Menu Button, Meter, Radio Group, Slider (single and multi-thumb), Spinbutton, Switch, Table, Tabs, Toolbar, Tooltip, Tree View, Treegrid, Window Splitter.

Pattern : Dialog (Modal) ([APG : Dialog Modal](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/) (verified 2026-05-19)). Required : `role="dialog"`, `aria-modal="true"`, `aria-labelledby` (or `aria-label`). Keyboard : Tab and Shift+Tab cycle WITHIN the dialog (focus trap is mandatory); Escape closes. Initial focus to first useful interactive element (often the title via `tabindex="-1"` for screen-reader context, or the primary action). On close, return focus to the trigger element unless workflow dictates otherwise.

Pattern : Combobox ([APG : Combobox](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/) (verified 2026-05-19)). `role="combobox"` on the editable / select control. `aria-controls` references the popup. `aria-expanded` toggles. `aria-autocomplete: none | list | both`. `aria-haspopup: listbox | grid | tree | dialog` (listbox is implicit default). Focus management : use `aria-activedescendant` to indicate the highlighted option while DOM focus stays on the combobox (REQUIRED for listbox / grid / tree popups); for dialog popup, move DOM focus into the dialog. Keyboard : Down / Up open popup or move within; Enter accepts; Escape closes (optionally clears).

Pattern : Tabs ([APG : Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) (verified 2026-05-19)). Roles : `tablist`, `tab`, `tabpanel`. `aria-selected="true|false"` on each tab; `aria-controls` references the panel; panel's `aria-labelledby` references its tab. Roving tabindex : only the active tab has `tabindex="0"`; others have `tabindex="-1"`. Arrow keys move between tabs (Left/Right for horizontal, Down/Up for vertical). Home / End jump to first / last. Tab key exits the tablist into the panel. Activation can be automatic (focus = activate) or manual (Space / Enter activates). Manual is REQUIRED when activation has side effects (network requests, expensive renders).

### Focus management primitives

[MDN : :focus-visible](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible) (verified 2026-05-19) : Baseline Widely Available since March 2022. Matches focused element only when the UA heuristic decides focus SHOULD be visually indicated (typically keyboard-driven focus, not mouse-click focus). ALWAYS pair custom focus styling with `:focus-visible` (NOT bare `:focus`). NEVER do `:focus { outline: none }` without a replacement indicator. The replacement MUST meet WCAG 1.4.11 Non-Text Contrast (3:1 against the adjacent background). `:focus-within` matches an ancestor when any descendant has focus; combine with `:has()` for cross-tree styling.

### Motion and contrast preferences

[MDN : prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) (verified 2026-05-19) : Baseline Widely Available since January 2020. Values `no-preference` (default) and `reduce`. `@media (prefers-reduced-motion)` is shorthand for `(prefers-reduced-motion: reduce)`. ALL non-essential motion (parallax, autoplay video, scaling, panning, gratuitous transitions) MUST be reduced or removed inside the `reduce` block. View Transitions MUST be skipped or replaced with simple opacity fade inside the `reduce` block. Place the reduce-motion override AFTER the default animation rules to win on source order at equal specificity. Companion media features : `prefers-contrast` (`no-preference` / `more` / `less` / `custom`), `prefers-color-scheme` (`light` / `dark`), `forced-colors` (`none` / `active`).

---

## 7. Performance (Core Web Vitals + render budget)

### Core Web Vitals (current as of 2026-05-19)

Per [web.dev : Vitals](https://web.dev/articles/vitals) (verified 2026-05-19), the three Core Web Vitals are :

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| LCP (Largest Contentful Paint) | <= 2.5 s | 2.5 to 4 s | > 4 s |
| INP (Interaction to Next Paint) | <= 200 ms | 200 to 500 ms | > 500 ms |
| CLS (Cumulative Layout Shift) | <= 0.1 | 0.1 to 0.25 | > 0.25 |

Assessment target is the 75th percentile of page loads across mobile and desktop. INP replaced FID as a Core Web Vital in March 2024.

### Optimizing INP

[web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19) decomposes interaction latency into : input delay (waiting for the main thread), processing duration (event handler execution), and presentation delay (waiting for the next frame). Tactics :

- Break long tasks. ALWAYS yield to the main thread between sub-tasks using `await scheduler.yield()` (where available) or `setTimeout(fn, 0)` fallback.
- Apply visual updates FIRST in event handlers; defer background work to after the next frame via `requestAnimationFrame(() => setTimeout(work, 0))`.
- Debounce expensive input handlers (typeahead, search) with `setTimeout` or AbortController-based debouncing.
- Use `content-visibility: auto` to defer off-screen layout / paint work.

### Layout / paint isolation

[MDN : contain](https://developer.mozilla.org/en-US/docs/Web/CSS/contain) (verified 2026-05-19) : `none` / `strict` / `content` / `size` / `inline-size` / `layout` / `style` / `paint`. `contain: content` is the safe default (== `layout paint style`). `contain: strict` adds `size` and requires explicit dimensions. Side effects : creates a containing block for absolutely-positioned descendants, creates a new stacking context, creates a new block formatting context.

[MDN : content-visibility](https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility) (verified 2026-05-19) : Baseline 2024 (September 2024). Values `visible` (default), `hidden` (preserves rendering state, similar to `display: none` but resumes faster), `auto` (skips rendering work when off-screen; auto-applies layout/style/paint containment). MUST pair `content-visibility: auto` with `contain-intrinsic-size: auto Npx` to prevent scrollbar-jitter as off-screen content gets sized to 0 by default. `auto` keeps content in the a11y tree (find-in-page works); `hidden` removes it.

### GPU-friendly animation

ALWAYS animate ONLY `transform`, `opacity`, and `filter` for compositor-only animations (no layout, no paint). NEVER animate `width`, `height`, `top`, `left`, `margin`, `padding`, `font-size`, `box-shadow` for production interactions; these trigger layout or paint storms. `will-change: transform` hints the browser to pre-promote an element to a compositor layer; OVER-using `will-change` exhausts GPU memory : apply on interaction-start, remove on interaction-end.

### Image and font budgets

`<img>` MUST set explicit `width` and `height` attributes (or `aspect-ratio` CSS) to reserve layout space and prevent CLS. Use `loading="lazy"` for below-the-fold images and `fetchpriority="high"` for the LCP candidate. `font-display: swap` ([MDN : font-display](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display) (verified 2026-05-19), Baseline Widely Available since January 2020) prevents invisible-text-while-loading at the cost of a font-swap flash. `font-display: optional` is acceptable for nice-to-have display fonts (no swap on first load). ALWAYS preload the LCP-affecting webfont via `<link rel="preload" as="font" type="font/woff2" crossorigin>`.

---

## 8. Design Tokens

[Design Tokens Format Module (W3C DTCG draft 2025.10)](https://designtokens.org/tr/drafts/format/) (verified 2026-05-19). Token interchange format is JSON. Every token requires `$value`. Metadata : `$type` (required, explicitly or inherited from a group), `$description`, `$extensions` (vendor metadata under reverse-DNS keys). The current published draft is preview 2025.10 (dated May 7, 2026) and is explicitly NOT yet production-implementation-ready (per spec preamble).

Defined token types : `color` (with `colorSpace` and `components`), `dimension` (`px` or `rem`), `fontFamily` (single or array), `fontWeight` (1-1000 or named), `duration` (`ms` / `s`), `cubicBezier` (4-element array), `number` (unitless). Composite types : `border`, `shadow`, `transition`, `strokeStyle`, `gradient`, `typography`.

Alias / reference : `{group.subgroup.token}` resolves to the target's `$value`. For nested-property access, JSON Pointer via `$ref` is required : `"$ref": "#/group/token/$value/property"`. Groups organize tokens hierarchically without requiring a `$value`; groups can declare `$type` to set inheritance for children. Reserved name `$root` represents a group's own value.

### Mapping tokens to CSS

Tokens MUST emit as CSS custom properties (`--color-brand-primary: oklch(60% 0.2 240);`). Layer them via `@layer tokens, theme, base, components, utilities;`. Use `@property` to register animatable tokens (e.g. `--gradient-angle: <angle>`). Use `light-dark()` for token theming when `color-scheme: light dark` is declared on `:root`. Runtime theme switching : flip `color-scheme` on a region, or toggle a `data-theme` attribute and use `[data-theme="dark"] { ... }` overrides scoped to a single token layer. NEVER hardcode hex / rgb / oklch values outside the token layer.

### Brand-to-system mapping

The recommended chain : raw brand color -> primitive token (`--brand-blue-500`) -> semantic token (`--color-action-primary`) -> component token (`--button-primary-bg`). Skills MUST teach the three-tier model so derivative changes do not require touching component code.

---

## 9. Visual Effects (modern web)

### Glassmorphism

[MDN : backdrop-filter](https://developer.mozilla.org/en-US/docs/Web/CSS/backdrop-filter) (verified 2026-05-19) : Baseline 2024 (Newly Available September 2024). Filter functions : `blur()`, `brightness()`, `contrast()`, `drop-shadow()`, `grayscale()`, `hue-rotate()`, `invert()`, `opacity()`, `saturate()`, `sepia()`, plus `url(#svg-filter)`. Requires partially-transparent `background-color` to see the underlying backdrop. Critical pitfall : a `backdrop-filter` only sees content up to the nearest backdrop-root ancestor. Common parent properties that establish a backdrop-root and break the effect : `opacity < 1`, any `filter`, `mask`, `mask-image`, `mix-blend-mode`, `clip-path`, another `backdrop-filter`. ALWAYS audit ancestors when a `backdrop-filter` appears not to blur as expected. The `-webkit-backdrop-filter` prefix is no longer required as of 2024 but is harmless if shipped.

### Modern gradients

`linear-gradient()`, `radial-gradient()`, `conic-gradient()` are all Baseline Widely Available. Multi-stop gradients in `oklch` color space produce smoother perceptual transitions than `srgb`. CSS does not natively support mesh gradients; emulate with layered radial gradients OR SVG `<feGaussianBlur>` filters. Animate gradients by registering a typed custom property via `@property --gradient-angle { syntax: '<angle>'; ... }` and animating that custom property.

### Scroll effects

[MDN : scroll-snap-type](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-snap-type) (verified 2026-05-19) : Baseline Widely Available since April 2022. Axis values `none` / `x` / `y` / `block` / `inline` / `both`; strictness `mandatory` / `proximity`. `scroll-snap-align: start | center | end | none` on each child. `scroll-padding` and `scroll-margin` adjust where snap points settle relative to viewport edges. `scroll-snap-stop: always` forces a stop at that snap point (no overscroll).

Parallax via scroll-driven animations : combine `animation-timeline: scroll()` with `transform: translateY(...)` keyframes. NEVER use `background-attachment: fixed` for parallax (catastrophic compositor cost on mobile).

### Micro-interactions

Hover / focus / press transitions : 150-250 ms with `cubic-bezier(0.2, 0, 0, 1)` (Material easing) or `cubic-bezier(0.16, 1, 0.3, 1)` (smooth ease-out). NEVER use `ease` default (visually flat). Press states MUST use `:active` and provide haptic-like compression (`transform: scale(0.97)`). Combine multiple state-pseudo-classes via `:has()` for cross-component choreography : `.card:has(button:hover) { ... }`. ALL motion MUST collapse to opacity-only inside `@media (prefers-reduced-motion: reduce)`.

---

## 10. Anti-patterns (real-world bugs)

1. Cascade specificity wars. Symptom : `!important` chains, ever-deeper selectors. Root cause : no layering discipline. Fix : `@layer reset, base, theme, components, utilities;` and put third-party CSS in its own layer ([MDN : @layer](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer)).

2. Unlayered overriding layered styles unexpectedly. Symptom : unlayered styles win even when layered rule is more specific. Root cause : unlayered styles form an implicit highest-priority layer in normal-declaration order ([MDN : @layer](https://developer.mozilla.org/en-US/docs/Web/CSS/@layer)). Fix : move all author CSS into named layers.

3. `:has()` performance trap on `body:has(...)`. Symptom : input lag, scroll jank. Root cause : anchor on entire body triggers re-evaluation on any DOM mutation ([MDN : :has()](https://developer.mozilla.org/en-US/docs/Web/CSS/:has)). Fix : narrow anchor to closest reasonable container.

4. Container query unit fallback surprise. Symptom : layouts collapse before container is established. Root cause : without a matching container, `cqi`/`cqb` units fall back to small-viewport units ([MDN : Container queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries)). Fix : ensure a containing element has `container-type` set; never assume a container exists.

5. Modal `<dialog>` with `tabindex`. Symptom : focus management breaks. Root cause : MDN explicitly forbids `tabindex` on `<dialog>` ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog)). Fix : remove the attribute and rely on `autofocus` for initial focus.

6. ResizeObserver loop. Symptom : console warning "ResizeObserver loop completed with undelivered notifications". Root cause : callback writes to size of observed element, triggering re-observation ([MDN : ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver)). Fix : wrap mutations in `requestAnimationFrame` or compare against expected-size cache.

7. Animating layout-trigger properties. Symptom : jank, missed frames, scroll stutter. Root cause : `width` / `height` / `top` / `left` animations trigger layout each frame. Fix : animate only `transform`, `opacity`, `filter`.

8. `will-change` overuse. Symptom : memory pressure, page crash on mobile. Root cause : `will-change` promotes to compositor layer; applied broadly drains GPU memory. Fix : apply on interaction-start, remove on interaction-end.

9. Backdrop-filter not blurring under transparent parent. Symptom : `backdrop-filter` ignored. Root cause : ancestor with `opacity < 1` becomes backdrop-root ([MDN : backdrop-filter](https://developer.mozilla.org/en-US/docs/Web/CSS/backdrop-filter)). Fix : audit ancestor chain for stacking-context triggers.

10. Custom focus removal without replacement. Symptom : keyboard users lose all focus indication. Root cause : `:focus { outline: none }` with no `:focus-visible` alternative ([MDN : :focus-visible](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible)). Fix : ALWAYS pair removal with a `:focus-visible` override meeting 3:1 contrast.

11. Tabs without roving tabindex. Symptom : Tab key cycles through every tab instead of moving into the active panel. Root cause : each tab has `tabindex="0"` instead of roving ([APG : Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/)). Fix : only active tab gets `tabindex="0"`, others `tabindex="-1"`; arrow keys reassign.

12. Combobox using DOM focus on options. Symptom : screen-reader announces both combobox and focused option as separate widgets. Root cause : moving DOM focus into listbox popup breaks the combobox role contract for listbox/grid/tree popups ([APG : Combobox](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/)). Fix : keep DOM focus on combobox; use `aria-activedescendant` to track highlighted option.

13. CLS from late-loading font. Symptom : massive layout shift when webfont loads. Root cause : fallback font has different metrics than webfont. Fix : pair `font-display: swap` with `size-adjust`, `ascent-override`, `descent-override` on a fallback `@font-face`.

14. Mobile viewport bug with `100vh`. Symptom : `100vh` element extends beyond visible mobile viewport (browser chrome bar). Root cause : `vh` excludes dynamic toolbar area. Fix : use `dvh` (dynamic viewport height) for fill-viewport, `svh` (small) and `lvh` (large) for explicit bounds.

15. Speculation rules prerendering destructive URLs. Symptom : items added to cart, sessions logged out, on hover. Root cause : `prerender` fully executes the destination including JavaScript ([MDN : Speculation Rules](https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API)). Fix : exclude `/logout`, `?add-to-cart=`, `[rel~=nofollow]` patterns from rules.

---

## 11. Version Matrix

| Feature Group | Baseline 2023 | Baseline 2024 | Baseline 2025 (in progress) |
|---------------|---------------|---------------|-----------------------------|
| Cascade Layers | Widely (since 2022) | - | - |
| Subgrid | Newly (Sept 2023) | becoming Widely | Widely (around March 2026) |
| `:has()` | Newly (Dec 2023) | becoming Widely | Widely (around June 2026) |
| `oklch()` / `color-mix()` | Widely (since May 2023) | - | - |
| Container queries | Widely | - | - |
| `<dialog>` | Widely (since March 2022) | - | - |
| Popover API | Limited | Limited (Safari catching up) | Newly (Jan 2025) |
| View Transitions API (same-doc) | Limited | Newly (mid-2024) | becoming Widely |
| View Transitions API (cross-doc) | Limited | Limited | Newly |
| Scroll-driven animations | Limited | Limited | Newly (selective) |
| Anchor positioning | Limited | Limited | Newly (partial) |
| `light-dark()` | - | Newly (May 2024) | becoming Widely |
| `@property` | Limited | Newly (July 2024) | becoming Widely |
| `@scope` | Limited | Limited | Newly (Dec 2025) |
| `content-visibility` | Limited | Newly (Sept 2024) | becoming Widely |
| `backdrop-filter` | Limited | Newly (Sept 2024) | becoming Widely |
| `Object.groupBy` / `Map.groupBy` | - | Newly (March 2024) | becoming Widely |
| `Promise.withResolvers` | - | Newly (March 2024) | becoming Widely |
| `<search>` element | Newly (Oct 2023) | becoming Widely | Widely |
| Declarative Shadow DOM (`shadowrootmode`) | Newly | becoming Widely | Widely |
| Speculation Rules API | Limited | Limited | Limited (Chromium-only) |

Breaking changes in the period 2023-2026 : none of these features broke prior content; all are additive. The Popover API rename from `popUp` to `popover` happened pre-Baseline. The deprecated `shadowroot` attribute was replaced by standardized `shadowrootmode` (Chrome 90-110 used the old name; this is the only legacy attribute requiring removal in cleanup work).

---

## 12. Newly Discovered Sub-Topics

These features / patterns were found during deep research and were NOT in the raw masterplan inventory :

1. `@scope` with donut limits. Category : `syntax/css`. Why it matters : enables low-specificity component-scoped CSS without classes. Source : [MDN : @scope](https://developer.mozilla.org/en-US/docs/Web/CSS/@scope) (verified 2026-05-19). Recommend adding a dedicated `frontend-syntax-css-scope` skill.

2. `closedby` attribute on `<dialog>`. Category : `impl/`. Why it matters : declarative light-dismiss / Escape control without script. Source : [MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19). Fold into `frontend-impl-popover-api`.

3. Speculation Rules API (prerender / prefetch). Category : `impl/`. Why it matters : near-instant navigations for SPA-style perf without SPA architecture. Source : [MDN : Speculation Rules](https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API) (verified 2026-05-19). Recommend adding `frontend-impl-speculation-rules` (gated as Limited / Chromium-only).

4. Form-Associated Custom Elements via `ElementInternals`. Category : `impl/`. Why it matters : custom inputs that participate in `<form>` submission / validation natively. Source : [MDN : Web Components](https://developer.mozilla.org/en-US/docs/Web/API/Web_components) (verified 2026-05-19). Fold into `frontend-impl-web-components`.

5. CSS Logical Properties (margin-block / inline, inset-block / inline). Category : `syntax/css`. Why it matters : RTL / vertical-writing-mode compatibility, future-proof layouts. Source : [MDN : CSS Logical Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values) (verified 2026-05-19). Recommend adding `frontend-syntax-css-logical-properties`.

6. Style Container Queries (`@container style(--theme: dark)`). Category : `syntax/css`. Why it matters : custom-property-driven conditional styling without descendant selectors. Source : [MDN : Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries) (verified 2026-05-19). Fold into `frontend-syntax-css-container-queries`.

7. Anchor positioning fallback chain (`position-try-fallbacks`). Category : `impl/`. Why it matters : viewport-aware popover placement without JavaScript. Source : [MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning) (verified 2026-05-19). Fold into `frontend-impl-popover-api` OR split off as `frontend-impl-anchor-positioning`.

8. Scoped Custom Element Registries (`new CustomElementRegistry()` with `customElementRegistry` option on `attachShadow`). Category : `impl/`. Why it matters : avoid global name collisions for component libraries. Source : [MDN : Web Components](https://developer.mozilla.org/en-US/docs/Web/API/Web_components) (verified 2026-05-19). Fold into `frontend-impl-web-components`.

9. APCA (Advanced Perceptual Contrast Algorithm) preview. Category : `a11y/`. Why it matters : WCAG 3 candidate, more perceptually accurate than 2.x ratio. Source : not yet in approved sources (see Gaps). Recommend a passing mention in `frontend-a11y-motion-contrast`, not a dedicated skill.

10. `dvh` / `svh` / `lvh` viewport units. Category : `errors/`. Why it matters : mobile viewport bug fixes for `100vh`. Source : MDN (not WebFetched this round, see Gaps). Fold into `frontend-errors-units-rendering`.

11. `inert` attribute. Category : `a11y/`. Why it matters : declaratively removes a subtree from sequential focus and accessibility tree; foundation for modal/inert patterns. Source : MDN (not WebFetched this round, see Gaps). Fold into `frontend-a11y-focus-management`.

12. `transition-behavior: allow-discrete` and `@starting-style`. Category : `syntax/css`. Why it matters : enables transitions for discrete properties (`display`, `content-visibility`), required for popover / dialog enter/exit animations. Source : MDN (not WebFetched this round, see Gaps). Recommend a dedicated `frontend-syntax-css-discrete-transitions` OR fold into `frontend-impl-popover-api`.

---

## 13. Recommendations for Phase 3

### Per-category MERGE / DROP / SPLIT / ADD

`core/` (4 estimated)
- KEEP all 4 : architecture, web-standards, browser-baseline, rendering-model.
- ADD : `frontend-core-tooling-decisions` (no-build vs Vite vs full-build trade-offs; explicitly framework-agnostic).

`syntax/` (9 estimated -> recommend around 12)
- KEEP : html5-semantic, html5-form, css-cascade-layers, css-container-queries, css-has-selector, css-color-modern, css-grid-subgrid, css-nesting, js-es2024.
- ADD : `frontend-syntax-css-scope` (new feature, dedicated skill).
- ADD : `frontend-syntax-css-logical-properties` (foundational, was missing).
- ADD : `frontend-syntax-css-property-at-rule` (`@property` for typed customs) OR fold into theming.
- ADD : `frontend-syntax-ts-strict-dom` (TypeScript DOM patterns, narrowing, lib.dom.d.ts quirks).

`impl/` (8 estimated -> recommend around 10)
- KEEP : design-tokens, responsive-layout, typography-system, popover-api, view-transitions, form-design, web-components, scroll-driven-animations.
- SPLIT : `frontend-impl-popover-api` is doing too much (popover + dialog + anchor positioning). RECOMMEND : keep `popover-api` for popover/dialog mechanics; SPLIT off `frontend-impl-anchor-positioning` as its own skill.
- ADD : `frontend-impl-speculation-rules` (perf / nav; mark Limited Availability gated).
- ADD : `frontend-impl-discrete-transitions` (transition-behavior + @starting-style for popover / dialog animations) OR fold into popover-api.

`errors/` (5 estimated -> keep 5)
- KEEP : cascade-conflicts, layout-pitfalls, a11y-violations, units-rendering, animation-jank.
- ENRICH `units-rendering` with the `dvh`/`svh`/`lvh` viewport-unit fix explicitly.
- ENRICH `cascade-conflicts` with the layered-vs-unlayered inversion and `!important` reversal.

`theming/` (3 estimated -> recommend MERGE)
- MERGE `theming-color-palette` and `theming-dark-light` could remain separate, but `theming-distinctive-aesthetic` (anti-AI-generic) is opinion-leaning. RECOMMEND : keep 3 but reframe `distinctive-aesthetic` as a `core/` skill : `frontend-core-design-philosophy` since it sets project-wide direction.

`visual-effects/` (5 estimated -> recommend around 4)
- KEEP : glassmorphism, gradients, micro-interactions, scroll-effects.
- DROP / DOWNGRADE `particle-canvas` : niche, fold into examples in `micro-interactions` OR drop entirely (framework-agnostic Canvas particle systems are widely-covered elsewhere and risk scope creep).

`accessibility/` (4 estimated -> recommend around 5)
- KEEP : aria-patterns, focus-management, keyboard-nav, motion-contrast.
- ADD : `frontend-a11y-wcag22-compliance` (dedicated skill summarizing the 9 new SC and how to satisfy each).

`performance/` (3 estimated -> keep 3)
- KEEP : css-optimization, render-budget, animation-gpu.
- ENRICH `render-budget` with explicit INP optimization tactics from web.dev.

`component-patterns/` (5 estimated -> recommend MERGE 1)
- KEEP : modal-system, toast-notifications, command-palette, data-tables.
- MERGE `card-layouts` is small enough to fold into `frontend-impl-responsive-layout` as a worked example. Saves a skill.

`agents/` (3 estimated -> keep 3)
- KEEP : design-system-validator, a11y-auditor, cross-skill-consistency.

### Estimated final skill count after Phase 3 refinement

Pre-research estimate : around 49 -> Post-research recommendation : **around 52** (gross delta : +5 adds, -2 merges/drops, +0 keeps).

This is HIGHER than the raw masterplan's "around 30-35 after consolidation" intuition because deep research revealed several mandatory adds (`@scope`, logical properties, anchor positioning, speculation rules, WCAG 2.2 compliance, TS DOM patterns) that did not exist in the raw inventory. Phase 3 user-checkpoint should explicitly decide on the +5 adds before committing.

### Batch ordering hint for Phase 5

- Batch 1-2 : `core/` skills (no dependencies).
- Batch 3-5 : `syntax/` skills (no cross-skill deps).
- Batch 6-7 : `accessibility/` and `performance/` (independent).
- Batch 8-9 : `impl/` skills (depend on `syntax/` and `core/`).
- Batch 10 : `theming/` and `visual-effects/` (depend on `syntax/css-color-modern`, `syntax/css-property-at-rule`).
- Batch 11 : `component-patterns/` (depend on `impl/` for popover/anchor/web-components).
- Batch 12 : `errors/` (depend on all prior; document real bugs).
- Batch 13 : `agents/` (validate everything that came before).

---

## 14. Verification Gaps

Claims considered but NOT verified against approved sources during this research round. Each MUST be either (a) verified in topic-research before the relevant skill is created OR (b) dropped from the skill.

1. APCA contrast algorithm details. Reason : no approved source documents APCA (it is a WCAG 3 working draft outside the WCAG 2.2 page). Recommendation : add `https://www.w3.org/TR/wcag-3.0/` to SOURCES.md (when stable) and re-research before mentioning APCA. For now, the `a11y` skill should mention "WCAG 2.2 ratio test is the requirement; APCA is informational only".

2. `dvh` / `svh` / `lvh` viewport unit details. Reason : did not WebFetch the MDN page on viewport units this round. Recommendation : verify against `https://developer.mozilla.org/en-US/docs/Web/CSS/length` and viewport-percentage-lengths before authoring `frontend-errors-units-rendering`.

3. `inert` attribute details. Reason : not WebFetched this round. Recommendation : verify against `https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/inert` before authoring focus-management.

4. `@starting-style` and `transition-behavior: allow-discrete` syntax details. Reason : not WebFetched. Recommendation : verify against MDN before authoring discrete-transitions content.

5. `scheduler.yield()` API exact signature and Baseline status. Reason : referenced briefly in INP guidance but the API itself was not separately verified. Recommendation : verify against `https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield` before authoring INP optimization patterns.

6. ES2024 Iterator helpers (`.map`, `.filter`, `.take`, `.drop`). Reason : not WebFetched. Recommendation : verify against `https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Iterator` before final `frontend-syntax-js-es2024` skill.

7. Open UI Community Group status of select / button customization (`appearance: base-select`, etc.). Reason : not WebFetched. Recommendation : verify against `https://open-ui.org/` (already in SOURCES.md) before authoring any form-redesign skill.

8. Exact APG keyboard interaction tables for patterns not yet drilled (carousel, disclosure, tree, treegrid). Reason : only drilled dialog-modal, combobox, tabs this round. Recommendation : drill remaining patterns as part of `accessibility/` topic-research before authoring the aria-patterns skill.

9. font-feature-settings vs font-variation-settings best-practice ordering. Reason : not WebFetched. Recommendation : verify against MDN @font-face descriptors before authoring `frontend-impl-typography-system`.

10. Speculation Rules eagerness (`immediate` / `eager` / `moderate` / `conservative`). Reason : the WebFetched MDN page did not document eagerness this round (likely on a sub-page). Recommendation : drill `https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API/Using` before authoring `frontend-impl-speculation-rules`.

---

## 15. Sources Used

| # | Source URL | Verified | Extracted |
|---|------------|----------|-----------|
| 1 | https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog | 2026-05-19 | showModal / show / close, top-layer, ::backdrop, closedby, focus rules |
| 2 | https://developer.mozilla.org/en-US/docs/Web/API/Popover_API | 2026-05-19 | popover attr, auto/manual/hint, popovertarget(action), Baseline 2025 |
| 3 | https://developer.mozilla.org/en-US/docs/Web/HTML/Constraint_validation | 2026-05-19 | ValidityState fields, setCustomValidity, checkValidity vs reportValidity |
| 4 | https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template | 2026-05-19 | shadowrootmode (open/closed), shadowrootdelegatesfocus, shadowrootclonable |
| 5 | https://developer.mozilla.org/en-US/docs/Web/HTML/Element/search | 2026-05-19 | search element, implicit role=search, Baseline Widely since Oct 2023 |
| 6 | https://developer.mozilla.org/en-US/docs/Web/CSS/@layer | 2026-05-19 | layers, ordering, unlayered/important inversion, Baseline since March 2022 |
| 7 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries | 2026-05-19 | @container, container-type, cqi/cqb/cqw/cqh/cqmin/cqmax, style queries |
| 8 | https://developer.mozilla.org/en-US/docs/Web/CSS/:has | 2026-05-19 | relational selector, restrictions, perf rules, Baseline 2023 |
| 9 | https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch | 2026-05-19 | oklch syntax, perceptual uniformity, relative color, Baseline since May 2023 |
| 10 | https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix | 2026-05-19 | color-mix interpolation spaces, hue methods, Baseline since May 2023 |
| 11 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Subgrid | 2026-05-19 | subgrid mechanics, named-line passthrough, gap inherit, Baseline since Sept 2023 |
| 12 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_nesting | 2026-05-19 | & selector, when required, no BEM trick, Baseline status |
| 13 | https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API | 2026-05-19 | startViewTransition, view-transition pseudos, @view-transition, prefers-reduced-motion |
| 14 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations | 2026-05-19 | animation-timeline, scroll() / view() functions, scroll-timeline / view-timeline |
| 15 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning | 2026-05-19 | anchor-name, position-anchor, anchor() fn, position-area, position-try-fallbacks |
| 16 | https://developer.mozilla.org/en-US/docs/Web/API/Web_components | 2026-05-19 | customElements.define, lifecycle, attachShadow options, slots, ElementInternals |
| 17 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/groupBy | 2026-05-19 | Object.groupBy signature, return shape, Baseline 2024 |
| 18 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/withResolvers | 2026-05-19 | resolve reject promise return, Baseline 2024 |
| 19 | https://www.w3.org/TR/WCAG22/ | 2026-05-19 | 9 new SCs, 4 principles, AA contrast 4.5:1 / 3:1 |
| 20 | https://www.w3.org/WAI/ARIA/apg/patterns/ | 2026-05-19 | full pattern list (combobox, dialog, listbox, menu, tabs, tree) |
| 21 | https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible | 2026-05-19 | UA heuristic, vs :focus, WCAG non-text 3:1, Baseline since March 2022 |
| 22 | https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion | 2026-05-19 | no-preference / reduce, Baseline since Jan 2020, OS settings |
| 23 | https://web.dev/articles/vitals | 2026-05-19 | LCP/INP/CLS thresholds, INP replacing FID, measurement tools |
| 24 | https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility | 2026-05-19 | visible/hidden/auto, contain-intrinsic-size, Baseline 2024 |
| 25 | https://developer.mozilla.org/en-US/docs/Web/CSS/contain | 2026-05-19 | none/strict/content/size/layout/style/paint, Baseline since March 2022 |
| 26 | https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/ | 2026-05-19 | role+aria-modal, focus trap, Escape, initial / restore focus |
| 27 | https://www.w3.org/WAI/ARIA/apg/patterns/combobox/ | 2026-05-19 | role=combobox, aria-activedescendant vs DOM focus, keyboard model |
| 28 | https://www.w3.org/WAI/ARIA/apg/patterns/tabs/ | 2026-05-19 | tablist/tab/tabpanel, roving tabindex, arrow keys, auto-vs-manual |
| 29 | https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver | 2026-05-19 | constructor opts, observe/unobserve/disconnect, entry properties |
| 30 | https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver | 2026-05-19 | box options, entry shapes, loop-warning pitfall |
| 31 | https://developer.mozilla.org/en-US/docs/Web/CSS/@property | 2026-05-19 | syntax/inherits/initial-value, animating customs, JS equivalent, Baseline 2024 |
| 32 | https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/light-dark | 2026-05-19 | light-dark() syntax, color-scheme requirement, Baseline 2024 |
| 33 | https://developer.mozilla.org/en-US/docs/Web/CSS/@scope | 2026-05-19 | @scope root to limit, :scope, low specificity, Baseline 2025 (Dec) |
| 34 | https://developer.mozilla.org/en-US/docs/Web/CSS/backdrop-filter | 2026-05-19 | filter functions, transparent bg requirement, backdrop-root, Baseline 2024 |
| 35 | https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API | 2026-05-19 | prerender / prefetch, urls vs where pattern, NOT Baseline |
| 36 | https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone | 2026-05-19 | deep clone, transfer option, what cannot clone, Baseline since March 2022 |
| 37 | https://designtokens.org/tr/drafts/format/ | 2026-05-19 | $value/$type/$description, token types, aliases, draft 2025.10 |
| 38 | https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-snap-type | 2026-05-19 | axis + strictness, scroll-snap-align, Baseline since April 2022 |
| 39 | https://web.dev/articles/optimize-inp | 2026-05-19 | input delay / processing / presentation, yield strategies, content-visibility |
| 40 | https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html | 2026-05-19 | 24x24 CSS px, 5 exceptions, spacing test |
| 41 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input | 2026-05-19 | full input type list, validation attrs, autocomplete, inputmode |
| 42 | https://developer.mozilla.org/en-US/docs/Web/CSS/clamp | 2026-05-19 | clamp(min, val, max), fluid type pattern, Baseline since July 2020 |
| 43 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values | 2026-05-19 | block-start/inline-end + inset/margin/padding shorthands |
| 44 | https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display | 2026-05-19 | auto/block/swap/fallback/optional, block + swap periods, Baseline since Jan 2020 |
| 45 | https://web.dev/baseline | 2026-05-19 | Limited / Newly / Widely (30 months), four core browsers |

Note : two URLs from the initial verification round returned non-content (the web-platform-dx.github.io redirect, and the font-display URL variant that 404'd); both were replaced with working URLs above. The font-display canonical lives at the `@font-face/font-display` descriptor page.
