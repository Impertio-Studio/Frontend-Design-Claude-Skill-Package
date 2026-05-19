# Masterplan : Frontend Design

> Status : Phase 1 raw : pre-research
> Generated : 2026-05-19
> Owner : OpenAEC Foundation

## Scope

- Technology : Frontend Design : framework-agnostic modern web standards
- Target output : HTML5, modern CSS (Cascade Layers, container queries, :has, color-mix, OKLCH, View Transitions, Popover API), vanilla JavaScript ES2024, TypeScript strict
- Explicit non-target : framework-specific code (React/Vue/Solid/Svelte/etc.). Framework integration belongs in companion packages.
- Versions : `evergreen-2026` baseline. Targets browsers that have shipped Baseline-2024 features.
- Languages : HTML, CSS, JavaScript (ES2024), TypeScript
- Prefix : `frontend`
- License : MIT
- Discovery : `agentskills` topic, `package.json agents.skills[]`, `agents/openai.yaml`

## Audience

- Claude (primary). Skills are deterministic instructions for code generation.
- Open-source LLMs that lack deep frontend training.
- Developers using Claude Code who want production-grade, non-AI-generic frontends.

## Design philosophy guide-rails

- Native web platform over polyfill/library.
- Progressive enhancement over runtime JavaScript-only.
- Accessibility built-in, not bolted on.
- Performance budget : <1.5s LCP target, no layout shift, no jank.
- Distinctive visual aesthetic : avoid generic AI-component look (default rounded-md gray cards).

## Identified Topics (raw, pre-research)

Eight working categories. Final categorization decided in Phase 3 refinement (may MERGE/DROP/SPLIT).

### core/ : architecture + platform model (4 estimated)

- `frontend-core-architecture` : modern frontend stack 2026, layered approach (markup/style/behavior/animation), build vs runtime trade-offs
- `frontend-core-web-standards` : W3C/WHATWG specs surface, evergreen vs Baseline vs stable, where to look up authoritative behavior
- `frontend-core-browser-baseline` : Baseline 2024/2025/2026, feature detection patterns, progressive enhancement decisions
- `frontend-core-rendering-model` : DOM/CSSOM, layout-paint-composite pipeline, what triggers reflow vs repaint vs composite

### syntax/ : modern HTML/CSS/JS surface (9 estimated)

- `frontend-syntax-html5-semantic` : semantic elements (article/section/nav/aside), landmarks, native ARIA mapping
- `frontend-syntax-html5-form` : modern form controls, input types, constraint validation API
- `frontend-syntax-css-cascade-layers` : `@layer`, predictable cascade, third-party CSS isolation
- `frontend-syntax-css-container-queries` : `@container`, container-type, intrinsic responsive components
- `frontend-syntax-css-has-selector` : `:has()` parent/sibling selection, conditional styling without JS
- `frontend-syntax-css-color-modern` : `color-mix()`, `oklch()`, `light-dark()`, relative color syntax, P3/Rec2020 gamut
- `frontend-syntax-css-grid-subgrid` : grid-template, subgrid, masonry, named lines
- `frontend-syntax-css-nesting` : native CSS nesting (no Sass needed), scope, `&` selector
- `frontend-syntax-js-es2024` : ES2024 features useful for DOM (Object.groupBy, Promise.withResolvers, Iterator helpers, structuredClone)

### impl/ : end-to-end implementation workflows (8 estimated)

- `frontend-impl-design-tokens` : W3C Design Tokens spec, CSS custom properties, naming conventions, runtime theme switching
- `frontend-impl-responsive-layout` : mobile-first, container queries, fluid typography with `clamp()`, no breakpoint cliff
- `frontend-impl-typography-system` : modular scale, variable fonts, `font-feature-settings`, font loading strategy
- `frontend-impl-popover-api` : `popover` attribute, `dialog` element, top-layer, light dismiss, anchor positioning
- `frontend-impl-view-transitions` : View Transitions API, cross-document, named transitions
- `frontend-impl-form-design` : accessible forms, native validation, error messaging, autofill
- `frontend-impl-web-components` : custom elements, shadow DOM, slots, declarative shadow DOM, when to use vs not
- `frontend-impl-scroll-driven-animations` : scroll-timeline, view-timeline, animation-timeline

### errors/ : real-world frontend bugs + recovery (5 estimated)

- `frontend-errors-cascade-conflicts` : specificity wars, layer ordering bugs, `!important` traps
- `frontend-errors-layout-pitfalls` : flexbox/grid common bugs, intrinsic-vs-extrinsic sizing, overflow surprises
- `frontend-errors-a11y-violations` : focus trap bugs, ARIA conflicts, missing labels, color contrast failures
- `frontend-errors-units-rendering` : em/rem/vh/dvh quirks, mobile viewport bugs, retina-vs-css pixels
- `frontend-errors-animation-jank` : layout-triggering animations, repaint storms, will-change misuse

### theming/ : color systems + dark mode (3 estimated)

- `frontend-theming-color-palette` : OKLCH systematic palette generation, contrast guarantees, brand-to-system mapping
- `frontend-theming-dark-light` : `color-scheme`, `light-dark()`, `prefers-color-scheme`, no flash of unstyled theme
- `frontend-theming-distinctive-aesthetic` : how to avoid AI-generic look, asymmetric layouts, intentional color, type-as-design-element

### visual-effects/ : modern visual treatments (5 estimated)

- `frontend-visual-glassmorphism` : `backdrop-filter`, layered translucency, contrast preservation
- `frontend-visual-gradients` : conic/radial gradients, mesh gradients via SVG, animated gradient backgrounds
- `frontend-visual-micro-interactions` : hover/focus/press transitions, easing curves, motion choreography
- `frontend-visual-scroll-effects` : sticky behavior, parallax via scroll-timeline, scroll-snap
- `frontend-visual-particle-canvas` : Canvas2D particle systems, requestAnimationFrame budget, fallback to CSS

### accessibility/ : WCAG + ARIA + assistive tech (4 estimated)

- `frontend-a11y-aria-patterns` : WAI-ARIA Authoring Practices Guide, when NOT to use ARIA, native-over-role
- `frontend-a11y-focus-management` : `focus-visible`, `focus-within`, programmatic focus, restore on close
- `frontend-a11y-keyboard-nav` : tabindex, roving tabindex, arrow-key patterns, escape handling
- `frontend-a11y-motion-contrast` : `prefers-reduced-motion`, `prefers-contrast`, WCAG 2.2 contrast (AA/AAA), APCA preview

### performance/ : runtime + render budget (3 estimated)

- `frontend-perf-css-optimization` : `contain`, `content-visibility`, critical CSS, `@property` for animatable customs
- `frontend-perf-render-budget` : Core Web Vitals (LCP/CLS/INP), layout shift prevention, image budgets
- `frontend-perf-animation-gpu` : `will-change`, transform/opacity-only, compositor layers, `animation-timeline` perf

### component-patterns/ : reusable UI building-blocks (5 estimated)

- `frontend-component-modal-system` : `dialog` element, popover, focus restoration, scroll lock
- `frontend-component-toast-notifications` : popover-based toast, `aria-live`, queue management, auto-dismiss
- `frontend-component-card-layouts` : modern card patterns, container-queried, accessible link cards
- `frontend-component-command-palette` : `cmd+k` pattern, search + keyboard nav, focus trap, ARIA combobox
- `frontend-component-data-tables` : accessible tables, sticky header, sortable, mobile reflow

### agents/ : cross-skill validation + orchestration (3 estimated)

- `frontend-agents-design-system-validator` : verify component uses tokens not hardcodes, no orphan colors/spacings
- `frontend-agents-a11y-auditor` : cross-skill accessibility check (keyboard, contrast, focus, ARIA)
- `frontend-agents-cross-skill-consistency` : naming, frontmatter, structure consistency across pkg

## Estimated Skill Count (pre-research)

| Category | Estimated |
|----------|-----------|
| core | 4 |
| syntax | 9 |
| impl | 8 |
| errors | 5 |
| theming | 3 |
| visual-effects | 5 |
| accessibility | 4 |
| performance | 3 |
| component-patterns | 5 |
| agents | 3 |
| **Total** | **~49** |

Phase 3 refinement will likely consolidate to ~30-35 after MERGE decisions (visual-effects + component-patterns may partially fold into impl, depending on research findings).

## Archive baseline (informational)

Previous Frontend Design package iteration is archived at `/home/freek/GitHub/_archive/Frontend-Design-pre-bootstrap-2026-05-19/` with 18 skills under `fd-*` prefix scheme. Diff against new pkg happens after Phase 5 (separate user-checkpoint, NOT auto-merged).

## Next : Phase 2 Deep Research

After this raw masterplan is committed, dispatch single opus research-agent for `docs/research/vooronderzoek-frontend.md`. Minimum 2000 words, WebFetch-verified, SOURCES.md updated with Last-Verified=2026-05-19.
