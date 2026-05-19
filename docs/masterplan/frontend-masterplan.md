# Masterplan : Frontend Design (Phase 3 refined)

## Status

- Date : 2026-05-19
- Status : Phase 3 refined : ready for user-checkpoint
- Source : refined from `docs/masterplan/frontend-masterplan-raw.md` against `docs/research/vooronderzoek-frontend.md` (41 WebFetch verifications, 545 lines)
- Target final count : 30-36 skills (LOCKED : 36)
- Owner : OpenAEC Foundation
- License : MIT
- Prefix : `frontend`

## Scope

- Technology : Frontend Design : framework-agnostic modern web platform (HTML5, CSS modern, JavaScript ES2024, TypeScript strict, Web APIs, WCAG 2.2, Core Web Vitals)
- Target output : HTML5, modern CSS (Cascade Layers, container queries, `:has`, `color-mix`, OKLCH, View Transitions, Popover API, Anchor Positioning, `@scope`, scroll-driven animations), vanilla JavaScript ES2024, TypeScript strict
- Explicit non-target : framework-specific code (React, Vue, Solid, Svelte, Angular, Qwik, etc.). Framework integration belongs in companion packages.
- Versions : `evergreen-2026` baseline. Targets browsers that have shipped Baseline 2024 features. Baseline 2025 features MUST be gated with `@supports` or JS feature detection.
- Languages : HTML, CSS, JavaScript (ES2024), TypeScript
- Discovery : `agentskills` GitHub topic, `package.json` `agents.skills[]`, `agents/openai.yaml`

## Refinement Decisions

| ID | Decision | Action | Rationale | Source |
|----|----------|--------|-----------|--------|
| D-R01 | Merge `frontend-core-browser-baseline` into `frontend-core-web-standards` as one combined skill `frontend-core-web-standards-baseline` | MERGE | Baseline taxonomy is part of the web standards lookup discipline; splitting them creates artificial cross-references | vooronderzoek §1, §11 |
| D-R02 | Merge `frontend-core-rendering-model` content into `frontend-core-architecture` (architecture skill covers the layered model AND the layout/paint/composite pipeline) | MERGE | Architecture skill needs the rendering pipeline anyway to justify the layer model; two skills would duplicate diagrams | vooronderzoek §1 |
| D-R03 | Drop `frontend-visual-particle-canvas` standalone skill | DROP | Niche, framework-agnostic Canvas particle systems are widely covered elsewhere; risk of scope creep without measurable user demand | vooronderzoek §13 |
| D-R04 | Drop `frontend-component-card-layouts` standalone skill, fold worked example into `frontend-impl-responsive-layout-fluid` | DROP | Card pattern is one composition of responsive + container queries; not deep enough for a dedicated skill | vooronderzoek §13 |
| D-R05 | Drop `frontend-component-toast-notifications` standalone skill, fold into `frontend-component-modal-toast-system` | MERGE | Toasts share the popover top-layer + aria-live mechanics with modal/dialog work; combined coverage avoids duplication | vooronderzoek §2, §6 |
| D-R06 | Add `frontend-syntax-css-cascade-layers-scope` covering both `@layer` and `@scope` in one skill | MERGE+ADD | `@scope` is a Phase 3 mandatory add from §12; bundling with cascade-layers reflects the shared mental model (predictable cascade discipline) | vooronderzoek §3, §12 |
| D-R07 | Add `frontend-syntax-css-nesting-logical-properties` combining native CSS nesting and CSS Logical Properties | MERGE+ADD | Logical properties were a Phase 3 mandatory add from §12; nesting is foundational; both belong to the modern-CSS authoring layer | vooronderzoek §3, §12 |
| D-R08 | Add `frontend-syntax-js-es2024-ts-dom` combining ES2024 DOM-relevant features and TypeScript DOM patterns (lib.dom.d.ts narrowing) | MERGE+ADD | TS DOM strict-mode quirks were a Phase 3 mandatory add from §13; pairing with ES2024 keeps one JS-surface skill | vooronderzoek §4, §13 |
| D-R09 | Split `frontend-impl-popover-api` into single richer skill `frontend-impl-popover-dialog-anchor` covering Popover API, `<dialog>`, anchor positioning, `closedby`, `position-try-fallbacks`, AND `transition-behavior: allow-discrete` + `@starting-style` for enter/exit animations | SPLIT+MERGE | Vooronderzoek §13 recommended splitting anchor off; testing showed all four sub-topics share mental model (top-layer, position-anchor, discrete transitions) and authors always need them together; one cohesive skill prevents cross-skill juggling | vooronderzoek §2, §5, §12, §13 |
| D-R10 | Drop standalone `frontend-impl-speculation-rules` skill, fold a "navigation prefetch / prerender" sub-section into `frontend-perf-core-web-vitals-inp` | MERGE | Speculation Rules API is Limited Availability (Chromium-only) per §11; not Baseline-eligible for evergreen-2026; ship as advanced perf optimization rather than dedicated skill | vooronderzoek §5, §11 |
| D-R11 | Merge `frontend-impl-scroll-driven-animations` into `frontend-impl-view-transitions-scroll-animations` (combined animation-timeline-driven skill) | MERGE | View Transitions and scroll-driven animations share the timeline/keyframe authoring model and the same `prefers-reduced-motion` gating discipline | vooronderzoek §5, §9 |
| D-R12 | Drop `frontend-impl-form-design` standalone skill, fold accessible-form workflow into `frontend-syntax-html5-form` (turning syntax skill into syntax+workflow) | MERGE | Constraint Validation API + native form controls already in syntax-html5-form; separate impl skill duplicates 70% of the content | vooronderzoek §2 |
| D-R13 | Drop `frontend-visual-scroll-effects` standalone skill, fold scroll-snap and parallax content into `frontend-impl-view-transitions-scroll-animations` | MERGE | Scroll-snap, scroll-driven animations, and view transitions form one cohesive timeline-and-snap chapter | vooronderzoek §5, §9 |
| D-R14 | Move `frontend-theming-distinctive-aesthetic` to `core/` as `frontend-core-design-philosophy` | MOVE | Design philosophy directs project-wide aesthetic decisions; belongs with architecture, not theming-as-color | vooronderzoek §13 |
| D-R15 | Merge `frontend-errors-a11y-violations` into `frontend-a11y-aria-patterns` (cross-reference, do not duplicate) | MERGE | A11y bugs are best taught in the a11y skill where the correct pattern is also defined; pure error catalog risks teaching anti-patterns without the cure | vooronderzoek §6, §10 |
| D-R16 | Merge `frontend-a11y-focus-management` and `frontend-a11y-keyboard-nav` into `frontend-a11y-focus-keyboard-inert` (added `inert` attribute per §12) | MERGE+ADD | Focus and keyboard navigation are inseparable in production a11y; `inert` attribute (Phase 3 mandatory add) is the modern foundation for both | vooronderzoek §6, §12 |
| D-R17 | Merge `frontend-a11y-motion-contrast` with dedicated WCAG 2.2 compliance content into `frontend-a11y-motion-contrast-wcag22` | MERGE | WCAG 2.2 new SCs §13 add are best taught alongside motion/contrast which is the most common compliance battleground | vooronderzoek §6, §13 |
| D-R18 | Merge `frontend-perf-css-optimization` and `frontend-perf-animation-gpu` into `frontend-perf-animation-gpu-containment` | MERGE | `contain`, `content-visibility`, and GPU-friendly animation share the compositor/layout-isolation mental model | vooronderzoek §7 |
| D-R19 | Merge `frontend-agents-a11y-auditor` and `frontend-agents-cross-skill-consistency` into `frontend-agents-a11y-perf-consistency-auditor` (covers a11y + perf cross-skill checks AND consistency) | MERGE | One auditor agent skill is enough; both auditors apply the same cross-skill-check methodology | vooronderzoek §6, §7 |

Summary : 19 refinement decisions producing 8 MERGE, 4 ADD (via combined skills), 3 DROP, 1 SPLIT (D-R09 merged-split), 1 MOVE. Net delta from raw 49 topics → 36 final skills.

## Final Category Architecture

| Category | Path-prefix | Count | Naming pattern | Depends on |
|----------|-------------|-------|----------------|------------|
| core | `skills/source/core/` | 3 | `frontend-core-{topic}` | none |
| syntax | `skills/source/syntax/` | 9 | `frontend-syntax-{topic}` | core |
| impl | `skills/source/impl/` | 6 | `frontend-impl-{topic}` | core, syntax |
| errors | `skills/source/errors/` | 4 | `frontend-errors-{topic}` | core, syntax, impl |
| theming | `skills/source/theming/` | 2 | `frontend-theming-{topic}` | core, syntax (color-modern), impl (design-tokens) |
| visual-effects | `skills/source/visual-effects/` | 3 | `frontend-visual-{topic}` | core, syntax (color-modern, has-selector), impl (view-transitions-scroll-animations) |
| accessibility | `skills/source/accessibility/` | 3 | `frontend-a11y-{topic}` | core, syntax (html5-semantic, html5-form) |
| performance | `skills/source/performance/` | 2 | `frontend-perf-{topic}` | core, impl (web-components, view-transitions-scroll-animations) |
| component-patterns | `skills/source/component-patterns/` | 2 | `frontend-component-{topic}` | impl (popover-dialog-anchor), a11y (aria-patterns, focus-keyboard-inert) |
| agents | `skills/source/agents/` | 2 | `frontend-agents-{topic}` | ALL prior |
| **Total** | | **36** | | |

Note on existing directories : the bootstrap created `skills/source/{agents, core, errors, impl, syntax}`. Phase 5 orchestrator MUST `mkdir -p` for the new five categories (theming, visual-effects, accessibility, performance, component-patterns) on first batch that touches each.

## Execution Plan : Batches

Dependency chain visualization :

```
Batch 1 (core)
  -> Batch 2,3,4 (syntax)
       -> Batch 5 (a11y)        Batch 6 (perf + errors-anim)
            -> Batch 7 (theming + visual-1)
                 -> Batch 8 (visual + impl-1)
                      -> Batch 9 (impl-2)
                           -> Batch 10 (impl-3 + component)
                                -> Batch 11 (errors-2)
                                     -> Batch 12 (component + agents)
```

| Batch | Skills (exactly 3) | Dependencies | Files-scope per worker | Estimated effort |
|-------|--------------------|--------------|--------------------------|------------------|
| 1 | frontend-core-architecture / frontend-core-web-standards-baseline / frontend-core-design-philosophy | none | w1: `skills/source/core/frontend-core-architecture/**` , w2: `skills/source/core/frontend-core-web-standards-baseline/**` , w3: `skills/source/core/frontend-core-design-philosophy/**` | 25 min |
| 2 | frontend-syntax-html5-semantic / frontend-syntax-html5-form / frontend-syntax-css-cascade-layers-scope | batch 1 | w1: `skills/source/syntax/frontend-syntax-html5-semantic/**` , w2: `skills/source/syntax/frontend-syntax-html5-form/**` , w3: `skills/source/syntax/frontend-syntax-css-cascade-layers-scope/**` | 25 min |
| 3 | frontend-syntax-css-container-queries / frontend-syntax-css-has-selector / frontend-syntax-css-color-modern | batch 1, 2 | w1: `.../frontend-syntax-css-container-queries/**` , w2: `.../frontend-syntax-css-has-selector/**` , w3: `.../frontend-syntax-css-color-modern/**` | 25 min |
| 4 | frontend-syntax-css-grid-subgrid / frontend-syntax-css-nesting-logical-properties / frontend-syntax-js-es2024-ts-dom | batch 1, 2 | w1: `.../frontend-syntax-css-grid-subgrid/**` , w2: `.../frontend-syntax-css-nesting-logical-properties/**` , w3: `.../frontend-syntax-js-es2024-ts-dom/**` | 25 min |
| 5 | frontend-a11y-aria-patterns / frontend-a11y-focus-keyboard-inert / frontend-a11y-motion-contrast-wcag22 | batch 1, 2 | w1: `skills/source/accessibility/frontend-a11y-aria-patterns/**` , w2: `.../frontend-a11y-focus-keyboard-inert/**` , w3: `.../frontend-a11y-motion-contrast-wcag22/**` | 25 min |
| 6 | frontend-perf-core-web-vitals-inp / frontend-perf-animation-gpu-containment / frontend-errors-animation-jank | batch 1, 2, 4 | w1: `skills/source/performance/frontend-perf-core-web-vitals-inp/**` , w2: `.../frontend-perf-animation-gpu-containment/**` , w3: `skills/source/errors/frontend-errors-animation-jank/**` | 25 min |
| 7 | frontend-theming-color-palette-oklch / frontend-theming-dark-light-mode / frontend-visual-glassmorphism-backdrop | batch 1, 3 (color-modern) | w1: `skills/source/theming/frontend-theming-color-palette-oklch/**` , w2: `.../frontend-theming-dark-light-mode/**` , w3: `skills/source/visual-effects/frontend-visual-glassmorphism-backdrop/**` | 25 min |
| 8 | frontend-visual-gradients / frontend-visual-micro-interactions / frontend-impl-design-tokens | batch 1, 3, 7 | w1: `.../frontend-visual-gradients/**` , w2: `.../frontend-visual-micro-interactions/**` , w3: `skills/source/impl/frontend-impl-design-tokens/**` | 25 min |
| 9 | frontend-impl-responsive-layout-fluid / frontend-impl-typography-system / frontend-impl-popover-dialog-anchor | batch 1-4 | w1: `.../frontend-impl-responsive-layout-fluid/**` , w2: `.../frontend-impl-typography-system/**` , w3: `.../frontend-impl-popover-dialog-anchor/**` | 30 min |
| 10 | frontend-impl-view-transitions-scroll-animations / frontend-impl-web-components / frontend-component-modal-toast-system | batch 1-4, 5, 9 | w1: `.../frontend-impl-view-transitions-scroll-animations/**` , w2: `.../frontend-impl-web-components/**` , w3: `skills/source/component-patterns/frontend-component-modal-toast-system/**` | 30 min |
| 11 | frontend-errors-cascade-conflicts / frontend-errors-layout-pitfalls / frontend-errors-units-rendering-viewport | batch 2-4 | w1: `skills/source/errors/frontend-errors-cascade-conflicts/**` , w2: `.../frontend-errors-layout-pitfalls/**` , w3: `.../frontend-errors-units-rendering-viewport/**` | 25 min |
| 12 | frontend-component-data-tables-command-palette / frontend-agents-design-system-validator / frontend-agents-a11y-perf-consistency-auditor | all prior | w1: `skills/source/component-patterns/frontend-component-data-tables-command-palette/**` , w2: `skills/source/agents/frontend-agents-design-system-validator/**` , w3: `.../frontend-agents-a11y-perf-consistency-auditor/**` | 30 min |

Total : 12 batches, 36 skills, estimated 5.5 hours of worker-time at 3-worker parallelism.


## Per-Skill Agent Prompts

### Skill : frontend-core-architecture

**Batch** : 1
**Category** : core
**Depends on** : none

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/core/frontend-core-architecture/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility for evergreen-2026, metadata.author=OpenAEC-Foundation, metadata.version="1.0")
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, sections §1 (Architecture and Platform Model) and §11 (Version Matrix)
  - docs/research/topic-research/frontend-core-architecture-research.md (will be produced by orchestrator BEFORE this prompt is sent, unless skip-criteria met per Phase 4 strategy)

Scope (binding bullets) :
  - The four authoring layers : markup (HTML5) / style (CSS modules) / behavior (JS + Web APIs) / animation-timeline (CSS animations + View Transitions + scroll-driven)
  - Spec lookup discipline : WHATWG Living Standard for HTML/DOM, W3C TR for CSS, MDN as canonical secondary, web.dev for Chrome-team-authored applied guidance
  - Browser rendering pipeline : DOM + CSSOM -> render tree -> style computation -> layout -> paint -> compositing; which CSS properties trigger which stage
  - Build-step vs runtime trade-offs : zero-build defaults (native ESM, native CSS nesting, native cascade layers); when a build step IS justified (TypeScript, tree-shaking, code splitting across many modules)
  - When to escape to compositor-only animation (transform / opacity / filter) and why other properties cause jank

Out-of-scope (binding) :
  - NO framework-specific code (React, Vue, Solid, Svelte, Angular, Qwik)
  - NO opinionated tooling recommendations (no "use Vite", no "use Rollup"); cover trade-offs only
  - NO build-tool installation instructions

Required references per SKILL.md frontmatter Keywords :
  - Technical : DOM, CSSOM, render tree, layout, paint, composite, will-change, contain, WHATWG, W3C, ESM, native modules, transform, opacity
  - Symptom-based : slow page, scroll stutter, jank, frame drop, layout shift, paint storm, choppy animation
  - Plain-language : how do I start a frontend project, what frontend stack should I use, do I need a build step, what is the browser rendering pipeline, why is my animation laggy

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://html.spec.whatwg.org/multipage/
  - https://developer.mozilla.org/en-US/docs/Web/CSS/contain
  - https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility
  - https://web.dev/baseline

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Do I need a build step for this project?"
    Branch : zero-build (native ESM + native CSS nesting + cascade layers) if no TS / no tree-shaking / no module bundling across >20 files
    Branch : build-required (Vite or esbuild) if TS / tree-shaking / many small modules / PostCSS for stage-1 specs
  - Question : "Which layer does this requirement belong to?"
    Branch : structure / semantics -> markup layer (HTML5)
    Branch : visual / interaction styling -> style layer (CSS)
    Branch : behavior / data fetching -> behavior layer (JS + Web APIs)
    Branch : choreography over time -> animation-timeline layer

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : "Sass to compile native CSS nesting" : symptom : extra build step for no benefit : root cause : believing native nesting is not stable : fix : native CSS nesting is Baseline; ship raw .css with `&` selector
  - Anti-pattern 2 : "Bundling vanilla site that imports 4 modules" : symptom : 3-second build for 200 lines of code : root cause : reflex bundling : fix : `<script type="module">` + native ESM
  - Anti-pattern 3 : "Polyfilling Baseline features" : symptom : 40 KB of unneeded code : root cause : stale browser-support knowledge : fix : verify Baseline status before adding polyfill
  - Anti-pattern 4 : "Animating width to slide a panel" : symptom : scroll jank, missed frames : root cause : width animation triggers layout each frame : fix : use `transform: translateX(...)` on compositor
  - Anti-pattern 5 : "Reading layout in a scroll handler" : symptom : layout thrash : root cause : forced sync layout via `offsetTop` / `getBoundingClientRect` mid-frame : fix : batch reads, use IntersectionObserver
  - Anti-pattern 6 : "Treating MDN as authoritative for spec disagreements" : symptom : code that follows MDN but fails interop test : root cause : MDN tracks BCD, not spec : fix : when MDN and spec disagree, spec wins; log discrepancy

Renderable HTML fragment required in references/examples.md : NO

Quality rules :
  - English only (D-001)
  - Deterministic language : ALWAYS / NEVER / MUST / SHOULD (no "you might consider")
  - License : MIT in frontmatter
  - compatibility : "Designed for Claude Code. Requires Frontend Design evergreen-2026."
  - Section headings use `:` not em-dash
  - No README.md inside skill folder
  - All code WebFetch-verified against the approved sources listed above
  - Cross-reference related skills with `[[frontend-core-web-standards-baseline]]` Markdown links inside SKILL.md

Quality gate after completion (worker self-runs) :
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-frontmatter.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-line-count.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-structure.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-language.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-emdash.js .
  - All must exit 0. Failure : root-cause-fix in skill, not workaround.

Commit after worker self-validates green :
  feat(skill): frontend-core-architecture

Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-core-web-standards-baseline

**Batch** : 1
**Category** : core
**Depends on** : none

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/core/frontend-core-web-standards-baseline/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility for evergreen-2026, metadata.author=OpenAEC-Foundation, metadata.version="1.0")
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, sections §1 (Baseline vs evergreen vs stable) and §11 (Version Matrix)
  - docs/research/topic-research/frontend-core-web-standards-baseline-research.md (will be produced by orchestrator BEFORE this prompt is sent, unless skip-criteria met)

Scope (binding bullets) :
  - Baseline taxonomy : Limited Availability, Newly Available, Widely Available (30-month rule), authoritative source web-platform-dx + web.dev/baseline
  - Spec families and how to look up authoritative behavior : WHATWG (HTML, DOM, Fetch, URL), W3C CSS WG (CSS modules), W3C WAI (WCAG, ARIA), TC39 (ECMAScript)
  - Feature detection patterns : CSS `@supports` (with `not` and `selector()` forms); JS detection via `in` operator, capability sniffing (NEVER UA sniffing)
  - Progressive enhancement decision flow : Baseline 2024 = ship; Baseline 2025 = gate with @supports + fallback; Limited = behind explicit opt-in
  - Reading caniuse.com and Baseline status tables : how to interpret "partial support", "needs prefix", "behind flag"

Out-of-scope (binding) :
  - NO browser version-specific advice (Chrome 124 vs 125 differences); use Baseline status instead
  - NO recommendations for specific polyfill libraries
  - NO user-agent string parsing patterns

Required references per SKILL.md frontmatter Keywords :
  - Technical : Baseline, @supports, CSS.supports, feature detection, evergreen, polyfill, caniuse, WHATWG, W3C, BCD
  - Symptom-based : feature does not work in Safari, missing CSS property, JS error in Firefox, browser compatibility issue
  - Plain-language : how do I check browser support, what is Baseline, when can I use CSS feature X, how to feature-detect a CSS property

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://web.dev/baseline
  - https://web-platform-dx.github.io/web-features/
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@supports

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Can I use this CSS feature without a fallback?"
    Branch : Widely Available (>=30 months Baseline) -> yes, no gate needed
    Branch : Newly Available (Baseline current/prev year) -> gate with `@supports` and provide fallback
    Branch : Limited Availability -> behind explicit opt-in, document as experimental
  - Question : "How do I feature-detect a CSS property?"
    Branch : property+value -> `@supports (prop: value) { ... }`
    Branch : selector -> `@supports selector(:has(*)) { ... }`
    Branch : at-rule -> `@supports at-rule(@scope) { ... }` (where supported)
  - Question : "How do I feature-detect a JS API?"
    Branch : global -> `'ResizeObserver' in window`
    Branch : instance method -> `'groupBy' in Object`
    Branch : NEVER use try/catch as feature detection (masks bugs)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : "UA sniffing" : symptom : breaks on next browser version : root cause : navigator.userAgent string parsing : fix : feature detection via @supports or `in`
  - Anti-pattern 2 : "Assuming Baseline = Safari support" : symptom : feature works in Chrome only : root cause : confusing Chromium-only features with Baseline : fix : Baseline requires all four core engines (Chromium, Gecko, WebKit) interop
  - Anti-pattern 3 : "Polyfilling :has()" : symptom : 30 KB JS for a CSS selector : root cause : :has() reached Baseline 2023 Newly Available, Widely 2026; ship native : fix : remove the polyfill, gate with @supports if you need pre-2024 fallback
  - Anti-pattern 4 : "Using try/catch to feature-detect" : symptom : silent failures hide real bugs : root cause : try/catch swallows non-detection errors too : fix : explicit `'feature' in scope` checks
  - Anti-pattern 5 : "Treating MDN BCD as Baseline" : symptom : shipping features one browser still lacks : root cause : BCD shows per-version support but not interop-stability : fix : check Baseline status on web-platform-dx OR `web-features` package
  - Anti-pattern 6 : "Adding `-webkit-` prefix to a 2024 property" : symptom : 2 KB of dead CSS : root cause : muscle memory from 2010-era CSS : fix : modern properties (backdrop-filter, content-visibility, gap in flexbox) no longer require prefixes in evergreen browsers

Renderable HTML fragment required in references/examples.md : NO

Quality rules :
  - English only (D-001)
  - Deterministic language : ALWAYS / NEVER / MUST / SHOULD
  - License : MIT in frontmatter
  - compatibility : "Designed for Claude Code. Requires Frontend Design evergreen-2026."
  - Section headings use `:` not em-dash
  - No README.md inside skill folder
  - All code WebFetch-verified
  - Cross-reference `[[frontend-core-architecture]]` and `[[frontend-syntax-css-cascade-layers-scope]]`

Quality gate after completion (worker self-runs) :
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-frontmatter.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-line-count.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-structure.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-language.js .
  - node /home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-emdash.js .
  - All must exit 0.

Commit after worker self-validates green :
  feat(skill): frontend-core-web-standards-baseline

Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-core-design-philosophy

**Batch** : 1
**Category** : core
**Depends on** : none

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/core/frontend-core-design-philosophy/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility for evergreen-2026, metadata.author=OpenAEC-Foundation, metadata.version="1.0")
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, sections §9 (Visual Effects) and §13 (Recommendations for Phase 3 — theming distinctive-aesthetic relocation)
  - docs/research/topic-research/frontend-core-design-philosophy-research.md

Scope (binding bullets) :
  - Avoid AI-generic visual signals : default rounded-md gray cards, identical neutral palettes, centered hero with three-feature-grid, generic gradients
  - Distinctive aesthetic principles : intentional asymmetry, typographic personality (variable fonts, strong scale), purposeful color (one accent, restrained palette), generous whitespace, considered motion
  - Native-platform-first principle : prefer browser primitives (`<dialog>`, popover, anchor positioning, view-transitions) over JS-recreated UI
  - Progressive enhancement principle : ship working HTML/CSS first, layer JS for delight
  - Performance budget as design constraint : <1.5s LCP, no CLS, no INP > 200ms — design decisions MUST be measured against these
  - Accessibility-first principle : WCAG 2.2 AA is the floor, not the ceiling; never bolt-on a11y

Out-of-scope (binding) :
  - NO specific brand-style guidance (this is meta-philosophy, not brand identity)
  - NO color-palette generation (deferred to `frontend-theming-color-palette-oklch`)
  - NO typography-system details (deferred to `frontend-impl-typography-system`)
  - NO framework-specific patterns

Required references per SKILL.md frontmatter Keywords :
  - Technical : design philosophy, native-first, progressive enhancement, performance budget, LCP, INP, CLS, WCAG 2.2
  - Symptom-based : looks like a chatbot, generic AI design, boring template, looks the same as every other site, missing personality
  - Plain-language : how do I make my site look unique, why does my AI-generated UI look the same as everyone else, design principles for modern web, what is good frontend design

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://web.dev/articles/vitals
  - https://www.w3.org/TR/WCAG22/
  - https://web.dev/baseline

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Is this design pattern AI-generic?"
    Branch : rounded-md gray cards in 3-column grid -> YES, redesign with asymmetric layout
    Branch : identical neutral palette across all components -> YES, introduce purposeful accent
    Branch : `text-center` hero with 3 feature cards -> YES, vary rhythm
    Branch : centered max-w-2xl prose -> ACCEPTABLE if typographic hierarchy is strong
  - Question : "Should this be JS or native platform?"
    Branch : dropdown / popover / tooltip -> native Popover API + Anchor Positioning
    Branch : modal / dialog -> native `<dialog>` element
    Branch : page navigation -> native View Transitions API
    Branch : layout responsive to container -> CSS Container Queries
    Branch : custom interactive widget without native equivalent -> JS, but progressive-enhance from working HTML

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : "Three feature cards under hero" : symptom : looks like every AI-generated landing page : root cause : trained tendency to reach for 3-column grid : fix : asymmetric layout, single hero focal-point, vary card sizing via grid `span`
  - Anti-pattern 2 : "Rounded-md gray everywhere" : symptom : indistinguishable from generic components : root cause : Tailwind-default reflex : fix : intentional varied corner radii (sharp / soft / mixed), color hierarchy with one bold accent
  - Anti-pattern 3 : "Centered max-width prose with no typographic personality" : symptom : reads like documentation, not branded experience : root cause : safe default : fix : variable font weight axis, scale ratio >1.25, intentional asymmetry between heading and body
  - Anti-pattern 4 : "JS recreates native popover" : symptom : 4 KB of click-outside / Escape / focus-trap code : root cause : unaware of Popover API : fix : native `popover` attribute + Anchor Positioning
  - Anti-pattern 5 : "Adding motion without checking prefers-reduced-motion" : symptom : VR-sick users disabled motion and your page still animates : root cause : motion-on-by-default : fix : @media (prefers-reduced-motion: reduce) override removing or replacing motion with opacity-only crossfade
  - Anti-pattern 6 : "Bolting a11y on at the end" : symptom : focus traps broken, ARIA labels wrong, contrast failing : root cause : a11y as a separate phase : fix : semantic HTML first, ARIA second, custom JS last; verify keyboard nav at every checkpoint

Renderable HTML fragment required in references/examples.md : YES — include one self-contained HTML fragment (around 100 lines, with `<style>` inline) showing an asymmetric hero layout WITHOUT centered max-w-2xl + 3-card-grid, demonstrating "non-generic" baseline (e.g., split-screen with strong type scale, single accent color, intentional whitespace rhythm).

Quality rules :
  - English only (D-001)
  - Deterministic language : ALWAYS / NEVER / MUST / SHOULD
  - License : MIT in frontmatter
  - compatibility : "Designed for Claude Code. Requires Frontend Design evergreen-2026."
  - Section headings use `:` not em-dash
  - No README.md inside skill folder
  - All code WebFetch-verified
  - Cross-reference `[[frontend-core-architecture]]`, `[[frontend-theming-color-palette-oklch]]`, `[[frontend-impl-typography-system]]`, `[[frontend-a11y-motion-contrast-wcag22]]`

Quality gate after completion (worker self-runs) :
  - All 5 validate-*.js scripts exit 0.

Commit after worker self-validates green :
  feat(skill): frontend-core-design-philosophy

Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-syntax-html5-semantic

**Batch** : 2
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-html5-semantic/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility for evergreen-2026, metadata.author=OpenAEC-Foundation, metadata.version="1.0")
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §2 (HTML5 Surface) and §6 (Accessibility — landmark roles)
  - docs/research/topic-research/frontend-syntax-html5-semantic-research.md

Scope (binding bullets) :
  - Landmark elements : `<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`, `<article>`, `<section>`, `<search>` (Baseline Widely since Oct 2023 with implicit role=search)
  - Document outline : `<article>` / `<section>` heading hierarchy, h1-h6 strategy
  - `<dialog>` element basics (full dialog/popover mechanics in `[[frontend-impl-popover-dialog-anchor]]`)
  - `<details>` / `<summary>` native disclosure with `name` attribute for accordion (Baseline 2024)
  - Declarative Shadow DOM via `<template shadowrootmode="open|closed">`, `shadowrootdelegatesfocus`, `shadowrootclonable`
  - When NOT to use ARIA : native semantic elements ALWAYS preferred over `role="..."`

Out-of-scope (binding) :
  - NO form controls (deferred to `[[frontend-syntax-html5-form]]`)
  - NO popover-attribute / dialog-showModal mechanics (deferred to `[[frontend-impl-popover-dialog-anchor]]`)
  - NO custom elements / web components (deferred to `[[frontend-impl-web-components]]`)
  - NO ARIA patterns (deferred to `[[frontend-a11y-aria-patterns]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : header, nav, main, aside, footer, article, section, search element, dialog, details, summary, declarative shadow dom, shadowrootmode, landmark roles
  - Symptom-based : screen reader does not announce sections, missing landmarks, content does not show in document outline, search field has no role
  - Plain-language : what HTML element should I use, when to use article vs section, how to make a navigation accessible, what is the search element

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://html.spec.whatwg.org/multipage/
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/search
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Which landmark element fits this region?"
    Branch : top-of-page identification -> `<header>` (one per document)
    Branch : primary navigation -> `<nav>`
    Branch : main page content (unique to this page) -> `<main>` (exactly one)
    Branch : tangentially-related content -> `<aside>`
    Branch : footer info -> `<footer>` (one per document)
    Branch : self-contained syndicatable item -> `<article>`
    Branch : thematic grouping with heading -> `<section>`
    Branch : search controls (not results) -> `<search>` (Baseline 2023)
  - Question : "Do I need role=... ?"
    Branch : there is a native element -> NEVER add role; use the element
    Branch : no native equivalent (live region, custom widget) -> use ARIA per `[[frontend-a11y-aria-patterns]]`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `<form role="search">` -> use `<search>` element (Baseline Widely since Oct 2023)
  - Anti-pattern 2 : `<div class="header">` -> use `<header>` element
  - Anti-pattern 3 : multiple `<main>` per page -> exactly one `<main>` per document
  - Anti-pattern 4 : `<section>` without a heading -> add `<h2>`-`<h6>`, or use `<div>`
  - Anti-pattern 5 : `<search>` wrapping search RESULTS -> use only for the controls that perform the search
  - Anti-pattern 6 : `tabindex` on `<dialog>` -> MDN explicitly forbids; rely on `autofocus`

Renderable HTML fragment required in references/examples.md : YES — one HTML file (around 80 lines) showing a fully-landmarked page (header / nav / main with article / aside / footer / search) that passes axe-core landmarks audit.

Quality rules :
  - English only (D-001)
  - Deterministic language : ALWAYS / NEVER / MUST / SHOULD
  - License : MIT in frontmatter
  - compatibility : "Designed for Claude Code. Requires Frontend Design evergreen-2026."
  - Section headings use `:` not em-dash
  - No README.md inside skill folder
  - All code WebFetch-verified
  - Cross-reference `[[frontend-a11y-aria-patterns]]`, `[[frontend-syntax-html5-form]]`, `[[frontend-impl-popover-dialog-anchor]]`

Quality gate after completion (worker self-runs) :
  - All 5 validate-*.js scripts exit 0.

Commit : feat(skill): frontend-syntax-html5-semantic
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-syntax-html5-form

**Batch** : 2
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-html5-form/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility for evergreen-2026, metadata.author=OpenAEC-Foundation, metadata.version="1.0")
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §2 (Form controls and validation) and §6 (a11y for forms)
  - docs/research/topic-research/frontend-syntax-html5-form-research.md

Scope (binding bullets) :
  - Complete `<input>` type set : text / email / url / tel / number / range / date / time / datetime-local / month / week / color / search / password / file / hidden / image / checkbox / radio / submit / reset / button
  - `inputmode` attribute (text / decimal / numeric / tel / search / email / url / none) for mobile keyboard tuning independent of validation type
  - `autocomplete` attribute taxonomy (email, tel, given-name, family-name, street-address, postal-code, current-password, new-password) — MUST be set on every autofillable field
  - Constraint Validation API : `element.validity` (ValidityState flags), `setCustomValidity(msg)`, `checkValidity()` (silent), `reportValidity()` (UI + invalid event), `HTMLFormElement.submit()` bypasses validation; button click triggers it
  - Workflow : accessible form design, native error messaging, `:user-invalid` vs `:invalid`, error-on-blur pattern
  - `<label>` association (explicit `for` and `id`), `<fieldset>` + `<legend>` for grouping

Out-of-scope (binding) :
  - NO popover-based form widgets (deferred to `[[frontend-impl-popover-dialog-anchor]]`)
  - NO form-associated custom elements (deferred to `[[frontend-impl-web-components]]`)
  - NO framework form libraries (out of scope per project)
  - NO server-side validation patterns

Required references per SKILL.md frontmatter Keywords :
  - Technical : input, type, inputmode, autocomplete, ValidityState, setCustomValidity, checkValidity, reportValidity, label, fieldset, legend, :user-invalid, :invalid, required, pattern, minlength, maxlength
  - Symptom-based : form does not validate, error message wrong, mobile keyboard wrong, autofill not working, form submits invalid data, screen reader does not announce error
  - Plain-language : how do I make an accessible form, how do I validate a form in vanilla HTML, how to show form errors, mobile-friendly form input

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Constraint_validation
  - https://html.spec.whatwg.org/multipage/

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Which input type for this data?"
    Branch : email address -> `type="email"` + `autocomplete="email"` + `inputmode="email"`
    Branch : phone -> `type="tel"` + `autocomplete="tel"` + `inputmode="tel"`
    Branch : numeric (no semantics) -> `type="text"` + `inputmode="numeric"` + `pattern="[0-9]*"`
    Branch : decimal currency -> `type="text"` + `inputmode="decimal"` + custom validation
    Branch : date input -> `type="date"` (Baseline Widely) — note locale UI differs per browser
  - Question : "How do I validate this field?"
    Branch : built-in constraint exists (required / pattern / min / max / minlength / maxlength) -> declare HTML attrs, browser handles UI
    Branch : custom rule -> `oninput` handler calls `setCustomValidity(msg)` then `''` when valid
    Branch : show errors only after user interacts -> use `:user-invalid` pseudo (Baseline 2023), NEVER bare `:invalid`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `:invalid` styling visible on empty form -> use `:user-invalid` (only after interaction)
  - Anti-pattern 2 : `type="text"` for email -> use `type="email"` (mobile keyboard + free validation)
  - Anti-pattern 3 : no `<label>` element -> wrap input with `<label>` or pair via `for=`/`id=`
  - Anti-pattern 4 : `autocomplete="off"` on password field -> use `autocomplete="current-password"` or `"new-password"` (off breaks password managers)
  - Anti-pattern 5 : `HTMLFormElement.submit()` to submit -> bypasses validation; click submit button or `requestSubmit()` instead
  - Anti-pattern 6 : custom error message via JS overlay without `setCustomValidity()` -> field still validates; assistive tech announces stale state; use Constraint Validation API

Renderable HTML fragment required in references/examples.md : YES — accessible signup form (around 100 lines) with email + password + phone + date, using `inputmode`, `autocomplete`, native validation, and a custom-validity rule (e.g., "username must not contain spaces"), styled with `:user-invalid`.

Quality rules :
  - English only (D-001)
  - Deterministic language : ALWAYS / NEVER / MUST / SHOULD
  - License : MIT in frontmatter
  - compatibility : "Designed for Claude Code. Requires Frontend Design evergreen-2026."
  - Section headings use `:` not em-dash
  - No README.md inside skill folder
  - All code WebFetch-verified
  - Cross-reference `[[frontend-syntax-html5-semantic]]`, `[[frontend-a11y-aria-patterns]]`, `[[frontend-impl-web-components]]`

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-html5-form
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-syntax-css-cascade-layers-scope

**Batch** : 2
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-css-cascade-layers-scope/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility for evergreen-2026, metadata.author=OpenAEC-Foundation, metadata.version="1.0")
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Cascade Layers, @scope) and §10 (Anti-patterns 1, 2)
  - docs/research/topic-research/frontend-syntax-css-cascade-layers-scope-research.md

Scope (binding bullets) :
  - `@layer` three creation forms : statement (`@layer base, components, utilities;`), block (`@layer utilities { ... }`), anonymous (`@layer { ... }`)
  - Layer order : set by first appearance of the name; later-declared wins ties
  - Critical inversions : unlayered beats layered for NORMAL declarations; for `!important` the order REVERSES (earlier layers win, important-author-in-layer beats important-author-unlayered)
  - Discipline : `@layer reset, base, theme, components, utilities;` as canonical author order; third-party CSS goes in its own layer
  - `@scope` syntax `@scope (<root>) to (<limit>) { ... }` : Baseline 2025 (Dec 2025); MUST be gated with `@supports at-rule(@scope)` or `@supports selector(:scope)` fallback
  - `@scope` specificity : bare selectors and `&` contribute zero beyond the selector itself; `:scope` adds class-level (0,1,0)
  - `@scope` proximity cascade : the rule whose scope root is fewest DOM hops wins (overrides source order, NOT importance or layer)

Out-of-scope (binding) :
  - NO CSS nesting (deferred to `[[frontend-syntax-css-nesting-logical-properties]]`)
  - NO container queries (deferred to `[[frontend-syntax-css-container-queries]]`)
  - NO @property typed customs (deferred to `[[frontend-theming-color-palette-oklch]]`)
  - NO BEM / SUIT / utility-class methodologies

Required references per SKILL.md frontmatter Keywords :
  - Technical : @layer, cascade layers, @scope, :scope, donut scope, layer order, unlayered, !important inversion, proximity cascade
  - Symptom-based : my CSS does not override, !important everywhere, specificity war, layer not winning, descendant selector too broad
  - Plain-language : how do I organize CSS, what is @layer, why does !important not work as expected, how to scope styles to a component without classes

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@layer
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@scope

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Where should this CSS go?"
    Branch : third-party reset -> `@layer reset { @import ... }`
    Branch : base typography / element defaults -> `@layer base { ... }`
    Branch : theme tokens (custom properties) -> `@layer theme { :root { --color-... } }`
    Branch : component-scoped rules -> `@layer components { ... }` OR `@scope (.component-root) to (.boundary) { ... }`
    Branch : single-purpose utility classes -> `@layer utilities { ... }`
  - Question : "Should I use @scope or @layer for this component?"
    Branch : need predictable order across many components -> `@layer components`
    Branch : need DOM-proximity-based override (closest ancestor wins) -> `@scope`
    Branch : want both : `@layer components { @scope (.card) to (.card .nested-card) { ... } }`
  - Question : "Why is my unlayered rule winning over my layered rule?"
    Branch : NORMAL declarations : unlayered beats layered by spec; move all author CSS into named layers
    Branch : `!important` reversed : earlier-declared layer wins for important; document the order

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : mixing unlayered author CSS with layered -> unlayered always wins normal cascade; move all author CSS into layers
  - Anti-pattern 2 : `!important` chain -> root cause is missing layer discipline; use `@layer` instead
  - Anti-pattern 3 : assuming `!important` follows the same order as normal -> reversed; document explicitly in code comment
  - Anti-pattern 4 : using `@scope` without `@supports` gate -> Baseline 2025 only; older browsers ignore the rule entirely (silently)
  - Anti-pattern 5 : `:scope` in a non-scoped selector -> adds (0,1,0) specificity unexpectedly; use bare selector if zero-specificity is intended
  - Anti-pattern 6 : `@layer` declared inside `@media` and expected to persist -> at-rules do not nest layers; declare layer order at the top level

Renderable HTML fragment required in references/examples.md : YES — one HTML file showing `@layer reset, base, theme, components, utilities;` declaration with `@scope (.card)` inside the components layer; demonstrate proximity-cascade with two nested `.card` instances having different overrides.

Quality rules : English-only, deterministic, MIT in frontmatter, compatibility string, `:` not em-dash, no README.md, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-container-queries]]`, `[[frontend-syntax-css-nesting-logical-properties]]`, `[[frontend-errors-cascade-conflicts]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-css-cascade-layers-scope
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-syntax-css-container-queries

**Batch** : 3
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline, frontend-syntax-css-cascade-layers-scope

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-css-container-queries/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Container Queries) and §12 (Style container queries newly discovered)
  - docs/research/topic-research/frontend-syntax-css-container-queries-research.md

Scope (binding bullets) :
  - `container-type` values : `size` (both axes), `inline-size` (inline only, default for most components), `normal` (style-only queries possible)
  - `container-name` opt-in naming; shorthand `container: <name> / <type>`
  - Query forms : `@container (width > 700px) { ... }` (anonymous) and `@container sidebar (width > 700px) { ... }` (named)
  - Container query length units : `cqw`, `cqh`, `cqi`, `cqb`, `cqmin`, `cqmax` — fallback to small-viewport units when no matching container
  - Style container queries : `@container style(--theme: dark) { ... }` (Baseline 2025, gate with @supports)
  - When to use container vs media queries : container = component-internal layout decision; media = device-level layout decision

Out-of-scope (binding) :
  - NO Grid / Subgrid (deferred to `[[frontend-syntax-css-grid-subgrid]]`)
  - NO cascade-layers / @scope (deferred to `[[frontend-syntax-css-cascade-layers-scope]]`)
  - NO fluid typography clamp() (deferred to `[[frontend-impl-responsive-layout-fluid]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : @container, container-type, container-name, container shorthand, cqw, cqh, cqi, cqb, cqmin, cqmax, style queries, container query unit
  - Symptom-based : component layout breaks at certain sizes, can not query parent width, layout depends on viewport when it should depend on parent
  - Plain-language : how do I make a component responsive to its container, what are container queries, container vs media query, what is container-type

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Should this rule use a container query or a media query?"
    Branch : layout depends on the component's parent / container width -> container query
    Branch : layout depends on the viewport (sidebar collapse on mobile) -> media query
    Branch : style choice depends on a custom property anywhere up the tree -> style container query (`style(--theme: dark)`)
  - Question : "What container-type should I set?"
    Branch : measuring inline only (most components) -> `inline-size`
    Branch : measuring both axes (cards in a grid with fixed aspect ratio) -> `size`
    Branch : style queries only, no size queries -> `normal`
  - Question : "Why are my cqi units huge?"
    Branch : no parent has matching container-type -> fallback to small-viewport units; add `container-type: inline-size` to the parent

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `container-type: size` without explicit height -> child needs intrinsic height; layout collapses; use `inline-size` unless both axes truly needed
  - Anti-pattern 2 : querying container by media-query syntax -> `@media` will not see container; use `@container`
  - Anti-pattern 3 : `cqi` units outside a containing query block -> fall back to viewport units; always pair component-internal cqi with a container-type ancestor
  - Anti-pattern 4 : nesting `container-type` ancestors (containing a containing container) -> innermost wins for that descendant; named containers disambiguate
  - Anti-pattern 5 : style container query without @supports gate -> Baseline 2025; older browsers silently drop the rule
  - Anti-pattern 6 : container-type on the element being queried -> self-query loops; container-type goes on a PARENT of the queried element

Renderable HTML fragment required in references/examples.md : YES — card grid where each card uses `@container (width > 400px) { ... }` to switch from vertical to horizontal layout, with `cqi` font-size scaling.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-grid-subgrid]]`, `[[frontend-syntax-css-cascade-layers-scope]]`, `[[frontend-impl-responsive-layout-fluid]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-css-container-queries
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-syntax-css-has-selector

**Batch** : 3
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-css-has-selector/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (:has selector) and §10 (Anti-pattern 3 :has perf trap)
  - docs/research/topic-research/frontend-syntax-css-has-selector-research.md

Scope (binding bullets) :
  - Syntax `:has(<relative-selector-list>)`, forgiving selector list, Baseline Widely Available 2026
  - Specificity equals the highest-specificity selector inside
  - Restrictions : NEVER nest `:has()` inside `:has()`; NEVER use pseudo-elements inside or as anchor
  - Performance rule : anchor on smallest possible subtree (`.gallery:has(...)` NOT `body:has(...)`); constrain inner selector with combinators (`> .child`, `+ .sibling`)
  - Use cases : parent selector for state-dependent styling, sibling-aware layout, form-state choreography (`form:has(input:invalid)`), conditional component styling without JS
  - Combination with `:focus-within` for cross-tree focus styling

Out-of-scope (binding) :
  - NO cascade-layers or scope (deferred to `[[frontend-syntax-css-cascade-layers-scope]]`)
  - NO JavaScript state-mutation patterns
  - NO framework-specific reactivity

Required references per SKILL.md frontmatter Keywords :
  - Technical : :has, relational selector, parent selector, forgiving selector list, :focus-within, sibling combinator
  - Symptom-based : need to style parent based on child, no parent selector in CSS, scroll jank with :has, input lag, :has not matching
  - Plain-language : how do I style a parent in CSS, what is the :has selector, why is :has slow, can I select a parent based on child state

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/:has

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Where should I anchor `:has()`?"
    Branch : closest reasonable container that semantically owns the state -> anchor there
    Branch : tempted to anchor on `body` or `:root` -> NEVER, performance disaster
    Branch : anchor on small reusable component -> ideal
  - Question : "Can I use `:has()` for this style?"
    Branch : style depends on existence of a descendant -> yes
    Branch : style depends on a sibling state -> yes via `:has(+ .sibling.active)`
    Branch : need pseudo-element inside -> NO, restriction
    Branch : need to nest `:has()` -> NO, restriction

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `body:has(.dialog-open)` -> re-evaluates on every DOM mutation; use `<html>` only with `inert` attr or anchor on a smaller container
  - Anti-pattern 2 : `:has(::before)` -> spec-prohibited; pseudo-elements cannot be the inner selector
  - Anti-pattern 3 : `.a:has(.b:has(.c))` -> nesting prohibited
  - Anti-pattern 4 : assuming :has() specificity is fixed -> specificity = highest of inner; use carefully against layered cascade
  - Anti-pattern 5 : using `:has()` for state that JS already tracks -> redundant; the JS-managed class is faster and more explicit
  - Anti-pattern 6 : missing `@supports selector(:has(*))` fallback for pre-2024 visitors -> gate with @supports for graceful degradation

Renderable HTML fragment required in references/examples.md : YES — example showing a card highlighting its parent when a button inside is hovered (`.card:has(button:hover) { ... }`), plus a form showing `form:has(input:invalid) [type=submit] { ... }` pattern.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-a11y-focus-keyboard-inert]]`, `[[frontend-syntax-html5-form]]`, `[[frontend-perf-animation-gpu-containment]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-css-has-selector
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-syntax-css-color-modern

**Batch** : 3
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-css-color-modern/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Modern color : oklch, color-mix, light-dark, relative color) and §8 (Design Tokens — color)
  - docs/research/topic-research/frontend-syntax-css-color-modern-research.md

Scope (binding bullets) :
  - `oklch(L C H / alpha)` : L is perceptual lightness 0..1, C is chroma 0..~0.4, H is hue 0..360; Baseline Widely since May 2023
  - Relative color : `oklch(from <color> l c h / alpha)` derives systematic scales from one seed
  - `color-mix(in <colorspace> [<hue-interp>], <c1> [pct], <c2> [pct])` ; colorspaces : srgb, srgb-linear, display-p3, lab, oklab, rec2020, xyz, hsl, hwb, lch, oklch; polar hue methods : `shorter hue` (default), `longer hue`, `increasing hue`, `decreasing hue`
  - ALWAYS prefer `in oklch` or `in oklab` for perceptually uniform mixing
  - `light-dark(<light-value>, <dark-value>)` : requires `color-scheme: light dark` on `:root`; works for colors AND image values; Baseline 2024
  - `@property` typed customs for animatable colors (covered in detail in `[[frontend-theming-color-palette-oklch]]`, mentioned here)
  - Wide-gamut (display-p3, rec2020) considerations

Out-of-scope (binding) :
  - NO systematic palette generation (deferred to `[[frontend-theming-color-palette-oklch]]`)
  - NO dark/light mode theming workflow (deferred to `[[frontend-theming-dark-light-mode]]`)
  - NO `@property` typed customs (deferred to `[[frontend-theming-color-palette-oklch]]`)
  - NO color-contrast WCAG ratio calculations (deferred to `[[frontend-a11y-motion-contrast-wcag22]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : oklch, oklab, color-mix, light-dark, color-scheme, relative color, display-p3, rec2020, lab, lch, hwb, hue interpolation
  - Symptom-based : colors look dull, gradient banding, dark mode not switching, color does not interpolate evenly
  - Plain-language : how do I use oklch, modern CSS color, how to mix colors in CSS, how to do light and dark theme without media queries, what is perceptually uniform color

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch
  - https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/color-mix
  - https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/light-dark

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Which color function should I use?"
    Branch : single solid color -> `oklch(L C H)` (perceptually uniform; predictable lightness)
    Branch : derive shade from seed -> `oklch(from var(--seed) l c h)` (relative color)
    Branch : blend two colors -> `color-mix(in oklch, color-a, color-b 30%)`
    Branch : per-theme value (light vs dark) -> `light-dark(<light>, <dark>)` (requires `color-scheme: light dark` on `:root`)
  - Question : "Which colorspace for color-mix?"
    Branch : perceptual gradient or shade ladder -> `in oklch` (polar) or `in oklab` (rectangular)
    Branch : web-safe / legacy compatibility -> `in srgb` (NEVER for new design systems)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `color-mix(in srgb, white, blue)` -> muddy intermediate; use `in oklab` or `in oklch`
  - Anti-pattern 2 : `light-dark()` without `color-scheme: light dark` on `:root` -> falls back to light value always; declare color-scheme
  - Anti-pattern 3 : hardcoded hex throughout codebase -> tokenize and emit as custom properties (see `[[frontend-impl-design-tokens]]`)
  - Anti-pattern 4 : assuming oklch L=50% is perceptually middle-gray of any hue -> close, but chroma affects perception; verify with contrast tools
  - Anti-pattern 5 : `oklch(from someColor calc(l - 10%) c h)` without typed @property -> works for static but not for transitions; use `@property --l { syntax: '<percentage>' }` when animating
  - Anti-pattern 6 : using `color()` function `display-p3` without sRGB fallback -> wide-gamut colors clip on sRGB displays; provide fallback via `@supports` or `color-mix` clamp

Renderable HTML fragment required in references/examples.md : YES — a swatch demo showing an oklch base color and its derived shade ladder (50, 100, 200, ... 900) via relative color syntax, plus a `light-dark()` themed background.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-theming-color-palette-oklch]]`, `[[frontend-theming-dark-light-mode]]`, `[[frontend-a11y-motion-contrast-wcag22]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-css-color-modern
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-syntax-css-grid-subgrid

**Batch** : 4
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-css-grid-subgrid/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Subgrid) and §10 (Anti-pattern 4 container query unit fallback — related grid pitfall)
  - docs/research/topic-research/frontend-syntax-css-grid-subgrid-research.md

Scope (binding bullets) :
  - CSS Grid basics : `grid-template-columns`, `grid-template-rows`, `grid-template-areas`, `gap`, `repeat()`, `minmax()`, `auto-fill` vs `auto-fit`, `fr` unit
  - Named lines : `[col-start]` / `[col-end]` syntax; named area placement
  - Subgrid : `grid-template-columns: subgrid;` and/or `grid-template-rows: subgrid;` makes nested grid inherit parent track sizing; Baseline Widely since Sept 2023
  - Parent named lines pass through subgrid automatically; subgrid CAN define additional names after the `subgrid` keyword
  - Gap inherits from parent; can be overridden on subgrid
  - Hard limitation : subgrid CANNOT generate implicit tracks beyond the spanned area; for implicit row needs, declare subgrid only on column axis
  - When to choose Grid vs Flexbox

Out-of-scope (binding) :
  - NO container queries (deferred to `[[frontend-syntax-css-container-queries]]`)
  - NO Flexbox-specific patterns (Flexbox is foundational; covered briefly only in decision tree)
  - NO masonry layout (still experimental; mention with @supports gate)
  - NO framework grid systems

Required references per SKILL.md frontmatter Keywords :
  - Technical : display: grid, grid-template-columns, grid-template-rows, grid-template-areas, subgrid, minmax, repeat, auto-fill, auto-fit, fr unit, named lines, gap
  - Symptom-based : nested items not aligned to outer grid, grid tracks do not align across rows, subgrid not working, implicit rows in subgrid not generated
  - Plain-language : how do I align nested grid items, what is subgrid, when to use grid vs flexbox, how to make a card layout with aligned baselines

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Subgrid
  - https://developer.mozilla.org/en-US/docs/Web/CSS/grid

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Grid or Flexbox?"
    Branch : two-dimensional layout (rows AND columns mattering simultaneously) -> Grid
    Branch : one-dimensional row or column with intrinsic sizing -> Flexbox
    Branch : both : Grid for outer, Flexbox within cells
  - Question : "Do I need subgrid?"
    Branch : need child grid cells aligned to parent grid lines (card-titles aligned across columns) -> subgrid on row axis
    Branch : variable row count per child grid -> subgrid on column axis only (avoid implicit-row trap)
  - Question : "Should I use `auto-fill` or `auto-fit`?"
    Branch : empty tracks should remain visible (always reserve space) -> `auto-fill`
    Branch : empty tracks should collapse and remaining items stretch -> `auto-fit`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `grid-template-columns: subgrid` on both axes when children need implicit rows -> spec limitation; switch to column-only subgrid
  - Anti-pattern 2 : assuming `auto-fit` and `auto-fill` are interchangeable -> they behave differently on under-filled containers
  - Anti-pattern 3 : nested grid without subgrid expecting alignment -> alignment is per-grid; use subgrid
  - Anti-pattern 4 : negative margin to align nested items -> brittle; use subgrid for spec-supported alignment
  - Anti-pattern 5 : `display: grid` on inline elements without `display: inline-grid` consideration -> width behavior unexpected
  - Anti-pattern 6 : using `grid-gap` (legacy) -> use `gap` (works on grid and flex; Baseline 2021+)

Renderable HTML fragment required in references/examples.md : YES — card layout where titles and meta align across cards (3-column outer grid, each card is subgrid for title / body / meta rows).

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-container-queries]]`, `[[frontend-impl-responsive-layout-fluid]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-css-grid-subgrid
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-syntax-css-nesting-logical-properties

**Batch** : 4
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-css-nesting-logical-properties/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Native CSS Nesting; Logical Properties) and §12 (Logical Properties newly discovered)
  - docs/research/topic-research/frontend-syntax-css-nesting-logical-properties-research.md

Scope (binding bullets) :
  - `&` nesting selector represents parent; required for pseudo-class / pseudo-element nesting and combinator-prefixed nesting
  - Bare nesting (`.parent { .child { ... } }`) is valid descendant selector (no Sass-like BEM `&__icon` trick)
  - Specificity : native nesting does NOT inflate beyond compiled selector chain
  - Sass / PostCSS NOT needed for nesting in evergreen-2026
  - CSS Logical Properties : `margin-block`, `margin-inline`, `padding-block`, `padding-inline`, `inset-block`, `inset-inline`, `block-size`, `inline-size`, `border-block`, `border-inline`
  - When to use logical : ALWAYS for new components targeting potential RTL or vertical-writing-mode contexts
  - Physical vs logical mapping table

Out-of-scope (binding) :
  - NO Sass / PostCSS plugin guidance (project is no-build by default per `[[frontend-core-architecture]]`)
  - NO cascade-layers / @scope (deferred to `[[frontend-syntax-css-cascade-layers-scope]]`)
  - NO container queries (deferred to `[[frontend-syntax-css-container-queries]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : CSS nesting, & selector, native nesting, logical properties, margin-block, margin-inline, inset-block, inset-inline, block-size, inline-size, writing-mode, RTL
  - Symptom-based : no parent selector trick like Sass __icon, layout broken in Arabic, vertical writing mode breaks margins, & not working
  - Plain-language : how do I nest CSS without Sass, what is the & selector, how to make CSS work in RTL, what are CSS logical properties

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_nesting
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Do I need `&` in this nesting context?"
    Branch : pseudo-class / pseudo-element nesting (`&:hover`, `&::before`) -> REQUIRED
    Branch : combinator-prefixed (`& > .child`, `& + .sibling`) -> REQUIRED
    Branch : bare descendant selector (`.parent { .child { ... } }`) -> NOT required; equivalent to `.parent .child`
  - Question : "Should I use logical or physical properties?"
    Branch : new code, any project -> logical (`margin-block-start` etc.)
    Branch : legacy codebase being maintained -> match local convention
    Branch : RTL or vertical-writing-mode in scope -> ALWAYS logical
  - Question : "Replicating Sass `&__icon` BEM trick?"
    Branch : native nesting does NOT support this; write full class name

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `&__icon` expecting BEM concatenation -> native nesting has no string-concat; write `.parent__icon` fully
  - Anti-pattern 2 : missing `&` for pseudo-class (`:hover { color: red; }` inside parent) -> invalid; use `&:hover`
  - Anti-pattern 3 : assuming Sass-style ampersand chaining in selectors lists -> behavior different from PostCSS-nesting
  - Anti-pattern 4 : `margin-left` in international product -> use `margin-inline-start`
  - Anti-pattern 5 : mixing logical and physical inset properties -> conflicts; pick one system per component
  - Anti-pattern 6 : assuming `inline-size` always equals `width` -> for vertical-writing-mode it maps to height; that is the point

Renderable HTML fragment required in references/examples.md : YES — one HTML file demonstrating a nested component (card with hover, focus-within, child elements) using only native nesting; plus an RTL test using `dir="rtl"` showing logical properties work correctly.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-cascade-layers-scope]]`, `[[frontend-syntax-css-has-selector]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-css-nesting-logical-properties
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-syntax-js-es2024-ts-dom

**Batch** : 4
**Category** : syntax
**Depends on** : frontend-core-architecture, frontend-core-web-standards-baseline

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/syntax/frontend-syntax-js-es2024-ts-dom/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §4 (ES2024 + TypeScript) and §14 (Verification Gaps : Iterator helpers, scheduler.yield)
  - docs/research/topic-research/frontend-syntax-js-es2024-ts-dom-research.md

Scope (binding bullets) :
  - ES2024 DOM-relevant features : `Object.groupBy(items, fn)`, `Map.groupBy(items, fn)` (Baseline 2024 March); `Promise.withResolvers()` (Baseline 2024 March); `structuredClone(value, { transfer? })` (Baseline Widely since March 2022)
  - Iterator helpers (`.map`, `.filter`, `.take`, `.drop`) — gate on @supports / feature-detect (Baseline 2025 transition); document with caution per §14 verification gap
  - TypeScript DOM strict-mode rules : `Element` vs `HTMLElement` narrowing; `querySelector('button')` returns `HTMLButtonElement | null` only with literal tag; `EventTarget` narrowing via `instanceof HTMLInputElement`
  - `null` returns of `getElementById`, `querySelector`, `closest` — ALWAYS narrow
  - Custom event types via `HTMLElementEventMap` module augmentation
  - Required tsconfig flags : `strict`, `noUncheckedIndexedAccess`, `noImplicitOverride`, `exactOptionalPropertyTypes`

Out-of-scope (binding) :
  - NO React / Vue / Solid / Svelte / Angular hook patterns
  - NO bundler config (build tooling is out of scope)
  - NO Node.js APIs
  - NO Web Components lifecycle (deferred to `[[frontend-impl-web-components]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : Object.groupBy, Map.groupBy, Promise.withResolvers, structuredClone, iterator helpers, lib.dom.d.ts, HTMLElementEventMap, instanceof, querySelector, EventTarget, ValidityState, strict mode, noUncheckedIndexedAccess
  - Symptom-based : Property does not exist on EventTarget, querySelector returns null, TypeScript yells at DOM, can not narrow event target, deferred promise pattern
  - Plain-language : how do I group an array in JavaScript, what is Promise.withResolvers, how to deep clone in JS, TypeScript with DOM, how to narrow event.target type

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/groupBy
  - https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/withResolvers
  - https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "How do I group a list of objects in vanilla JS?"
    Branch : string / symbol keys -> `Object.groupBy(items, fn)`
    Branch : arbitrary object refs as keys -> `Map.groupBy(items, fn)`
  - Question : "Deep clone an object?"
    Branch : DOM-cloneable data (Date / Map / Set / typed arrays / Blob) -> `structuredClone(value)`
    Branch : contains functions / getters -> manual clone or library
    Branch : with transferable (ArrayBuffer / OffscreenCanvas) -> `structuredClone(value, { transfer: [buf] })`
  - Question : "Narrow `event.target` to a specific element?"
    Branch : `if (e.target instanceof HTMLInputElement)` -> typed access inside
    Branch : prefer `currentTarget` for the element the handler is bound to (typed via generic)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `JSON.parse(JSON.stringify(x))` -> drops Date / Map / Set / undefined / non-enumerable; use `structuredClone`
  - Anti-pattern 2 : "deferred" pattern leaking executor refs out of `new Promise(...)` -> use `Promise.withResolvers()`
  - Anti-pattern 3 : `items.reduce((acc, item) => { (acc[item.key] ||= []).push(item); return acc; }, {})` -> use `Object.groupBy(items, x => x.key)`
  - Anti-pattern 4 : `e.target as HTMLInputElement` blind cast in TypeScript -> use `instanceof` narrowing
  - Anti-pattern 5 : `document.getElementById('x')!.value` non-null assertion -> use proper null narrow `if (!el) return;` first
  - Anti-pattern 6 : iterator helpers without feature detection -> Baseline 2025; gate or polyfill

Renderable HTML fragment required in references/examples.md : NO — but include a self-contained `.ts` snippet showing strict-mode tsconfig + narrowed DOM event handler + groupBy + Promise.withResolvers in a typed micro-app pattern.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-impl-web-components]]`, `[[frontend-syntax-html5-form]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-syntax-js-es2024-ts-dom
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-a11y-aria-patterns

**Batch** : 5
**Category** : accessibility
**Depends on** : frontend-core-architecture, frontend-syntax-html5-semantic

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/accessibility/frontend-a11y-aria-patterns/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §6 (WAI-ARIA Authoring Practices Guide; Dialog, Combobox, Tabs patterns) and §10 (Anti-patterns 11, 12)
  - docs/research/topic-research/frontend-a11y-aria-patterns-research.md
  - Phase 4 MUST drill remaining APG patterns (Accordion, Disclosure, Listbox, Menu, Radio Group, Tree, Treegrid) per §14 verification gap #8

Scope (binding bullets) :
  - When NOT to use ARIA : native semantic elements ALWAYS preferred (`<button>` over `<div role="button">`, `<nav>` over `<div role="navigation">`)
  - First rule of ARIA : do not use ARIA
  - Pattern : Dialog (Modal) : `role="dialog"`, `aria-modal="true"`, `aria-labelledby`, focus trap (Tab/Shift+Tab), Escape to close, restore focus on close
  - Pattern : Combobox : `role="combobox"` on editable/select, `aria-controls` references popup, `aria-expanded` toggle, `aria-autocomplete: none|list|both`, `aria-haspopup`; for listbox/grid/tree popups use `aria-activedescendant` (DOM focus stays on combobox); for dialog popup, move DOM focus
  - Pattern : Tabs : roles `tablist`, `tab`, `tabpanel`; `aria-selected`; `aria-controls` and `aria-labelledby` cross-reference; roving tabindex; arrow-key navigation; automatic vs manual activation (manual REQUIRED when activation has side effects)
  - Pattern : Accordion, Disclosure, Listbox, Menu, Radio Group (covered via APG references)
  - Live regions : `aria-live="polite|assertive"`, `aria-atomic`, `aria-relevant`
  - Labeling : `aria-label`, `aria-labelledby`, `aria-describedby` precedence

Out-of-scope (binding) :
  - NO focus / keyboard mechanics deep-dive (deferred to `[[frontend-a11y-focus-keyboard-inert]]`)
  - NO motion / contrast / WCAG 2.2 SC details (deferred to `[[frontend-a11y-motion-contrast-wcag22]]`)
  - NO popover/dialog implementation specifics (deferred to `[[frontend-impl-popover-dialog-anchor]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : role, aria-label, aria-labelledby, aria-describedby, aria-expanded, aria-controls, aria-haspopup, aria-activedescendant, aria-selected, aria-modal, aria-live, role=dialog, role=combobox, role=tablist, APG
  - Symptom-based : screen reader does not announce, focus jumps wrong place, screen reader says "two widgets" for combobox, missing accessible name, keyboard nav broken
  - Plain-language : how do I make a modal accessible, what role should this have, ARIA combobox pattern, accessible tabs implementation, how to announce a notification to screen readers

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://www.w3.org/WAI/ARIA/apg/patterns/
  - https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/
  - https://www.w3.org/WAI/ARIA/apg/patterns/combobox/
  - https://www.w3.org/WAI/ARIA/apg/patterns/tabs/

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Should I add `role=...` to this element?"
    Branch : native semantic element exists (`<button>`, `<nav>`, `<dialog>`) -> NEVER add role
    Branch : custom widget without native equivalent -> use ARIA per APG pattern
    Branch : redundant role (`<button role="button">`) -> NEVER
  - Question : "Combobox popup type?"
    Branch : listbox/grid/tree popup -> `aria-activedescendant` (DOM focus stays on input)
    Branch : dialog popup -> move DOM focus into dialog
  - Question : "Tabs activation mode?"
    Branch : tab content already in DOM, no side effects -> automatic (focus = activate)
    Branch : activation triggers fetch / expensive render -> manual (Space/Enter activates)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `<div role="button" onclick=...>` -> use `<button>`; div lacks keyboard, focus, type behavior
  - Anti-pattern 2 : `<nav role="navigation">` -> `<nav>` already implies role; remove the redundant role
  - Anti-pattern 3 : tabs with every tab `tabindex="0"` -> use roving tabindex per APG
  - Anti-pattern 4 : combobox listbox popup using DOM focus on options -> use `aria-activedescendant` instead
  - Anti-pattern 5 : `aria-label` on an element that already has visible text label -> redundant or worse, overrides; use `aria-labelledby` to reference the visible label
  - Anti-pattern 6 : `role="alert"` polled via setInterval -> alert role is for live changes only; for polling status, use `aria-live="polite"` on a status region
  - Anti-pattern 7 : missing focus restore on dialog close -> WCAG 3.2.1 violation; capture trigger element, restore focus on close

Renderable HTML fragment required in references/examples.md : YES — accessible tabs pattern (around 100 lines) implementing the APG tabs pattern WITH roving tabindex AND manual activation; second example showing a combobox listbox popup using `aria-activedescendant`.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-a11y-focus-keyboard-inert]]`, `[[frontend-a11y-motion-contrast-wcag22]]`, `[[frontend-impl-popover-dialog-anchor]]`, `[[frontend-syntax-html5-semantic]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-a11y-aria-patterns
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-a11y-focus-keyboard-inert

**Batch** : 5
**Category** : accessibility
**Depends on** : frontend-core-architecture, frontend-syntax-html5-semantic

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/accessibility/frontend-a11y-focus-keyboard-inert/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §6 (Focus management primitives), §10 (Anti-patterns 10, 11), §12 (inert attribute newly discovered)
  - docs/research/topic-research/frontend-a11y-focus-keyboard-inert-research.md
  - Phase 4 MUST drill `inert` attribute per §14 verification gap #3

Scope (binding bullets) :
  - `:focus-visible` vs `:focus` : UA heuristic decides when focus SHOULD be visually indicated (typically keyboard, not mouse); ALWAYS pair custom focus styling with `:focus-visible`
  - NEVER `:focus { outline: none }` without a replacement indicator
  - Replacement focus indicator MUST meet WCAG 1.4.11 Non-Text Contrast (3:1 against adjacent background)
  - `:focus-within` for ancestor styling when descendant has focus; combine with `:has()` for cross-tree
  - `tabindex` rules : `0` participates in tab order at default position; `-1` programmatically focusable but not in tab order; positive values NEVER (creates tab-order chaos)
  - Roving tabindex pattern for composite widgets (tabs, menubars, toolbars)
  - `inert` attribute : declaratively removes a subtree from sequential focus and accessibility tree; foundation for modal patterns; pairs with `<dialog>` (showModal sets inert automatically on rest)
  - Programmatic focus : `element.focus({ preventScroll?, focusVisible? })`
  - Restore focus pattern on dialog/popover close

Out-of-scope (binding) :
  - NO ARIA roles deep-dive (deferred to `[[frontend-a11y-aria-patterns]]`)
  - NO WCAG 2.2 SC text (deferred to `[[frontend-a11y-motion-contrast-wcag22]]`)
  - NO popover-specific focus rules (deferred to `[[frontend-impl-popover-dialog-anchor]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : :focus, :focus-visible, :focus-within, tabindex, inert, focus(), preventScroll, document.activeElement, roving tabindex, focus trap
  - Symptom-based : focus indicator missing, keyboard user lost, tab order wrong, focus escaped modal, focus on hidden element, focus jumps to top
  - Plain-language : how do I make focus visible only for keyboard, what is the inert attribute, how to trap focus in a modal, accessible focus styles, how to restore focus after dialog close

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/inert
  - https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "How should I style focus?"
    Branch : keyboard-only (most cases) -> `:focus-visible { outline: 2px solid var(--focus); outline-offset: 2px; }`
    Branch : all focus (including mouse-click on text inputs) -> `:focus` plus optional `:focus-visible` override
    Branch : never visible -> NEVER; WCAG violation
  - Question : "Remove subtree from focus and AT?"
    Branch : declarative -> `inert` attribute on the subtree root
    Branch : modal dialog -> `<dialog>` + `showModal()` (sets inert on rest automatically)
    Branch : conditional (background while overlay open) -> `inert` on `<main>` while overlay open
  - Question : "Composite widget keyboard model?"
    Branch : tabs / menubar / toolbar / radio group -> roving tabindex; arrow keys move within; Tab exits
    Branch : combobox -> `aria-activedescendant` (no DOM focus change)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `:focus { outline: none }` without replacement -> keyboard users lose all focus indication
  - Anti-pattern 2 : positive `tabindex` -> chaotic tab order; use 0 / -1 only
  - Anti-pattern 3 : focus trap in modal that does not allow Tab to escape to browser chrome -> Escape key MUST close dialog
  - Anti-pattern 4 : `aria-hidden="true"` on background instead of `inert` -> aria-hidden hides from AT but does NOT prevent focus; `inert` does both
  - Anti-pattern 5 : missing `tabindex="-1"` on programmatically-focused container -> `focus()` silently no-ops on non-focusable elements
  - Anti-pattern 6 : not restoring focus to trigger on dialog close -> screen-reader users disoriented; capture trigger, restore on close

Renderable HTML fragment required in references/examples.md : YES — focus-trapped modal using `<dialog>` + `showModal()` showing automatic `inert` on background, with proper `:focus-visible` styles meeting 3:1 contrast.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-a11y-aria-patterns]]`, `[[frontend-a11y-motion-contrast-wcag22]]`, `[[frontend-impl-popover-dialog-anchor]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-a11y-focus-keyboard-inert
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-a11y-motion-contrast-wcag22

**Batch** : 5
**Category** : accessibility
**Depends on** : frontend-core-architecture, frontend-syntax-css-color-modern (batch 3)

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/accessibility/frontend-a11y-motion-contrast-wcag22/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §6 (WCAG 2.2 + motion / contrast preferences) and §13 (Add WCAG 2.2 compliance skill)
  - docs/research/topic-research/frontend-a11y-motion-contrast-wcag22-research.md

Scope (binding bullets) :
  - WCAG 2.2 new success criteria (nine added over 2.1) : 2.4.11 Focus Not Obscured (Min) AA, 2.4.12 (Enh) AAA, 2.4.13 Focus Appearance AAA, 2.5.7 Dragging Movements AA, 2.5.8 Target Size (Min) 24x24 CSS pixels AA + five exceptions, 3.2.6 Consistent Help A, 3.3.7 Redundant Entry A, 3.3.8 Accessible Authentication (Min) AA, 3.3.9 (Enh) AAA
  - Contrast (Minimum) AA : normal text 4.5:1, large text 3:1; Non-text contrast AA 3:1
  - `prefers-reduced-motion: reduce` : ALL non-essential motion MUST be reduced or removed inside the reduce block; View Transitions MUST be skipped
  - `prefers-contrast: no-preference | more | less | custom`
  - `prefers-color-scheme: light | dark`
  - `forced-colors: none | active` (Windows high-contrast mode)
  - APCA (WCAG 3 candidate, informational only; WCAG 2.2 ratio is the requirement)
  - Target Size 24x24 CSS pixel test, including the 5 exceptions

Out-of-scope (binding) :
  - NO focus / keyboard mechanics (deferred to `[[frontend-a11y-focus-keyboard-inert]]`)
  - NO ARIA roles (deferred to `[[frontend-a11y-aria-patterns]]`)
  - NO performance INP measurement (deferred to `[[frontend-perf-core-web-vitals-inp]]`)
  - NO colour palette generation (deferred to `[[frontend-theming-color-palette-oklch]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : WCAG 2.2, prefers-reduced-motion, prefers-contrast, prefers-color-scheme, forced-colors, contrast ratio, 4.5:1, 3:1, target size, 24x24, APCA
  - Symptom-based : motion makes user sick, contrast fails audit, button too small, text unreadable, animation does not respect OS setting, target size warning
  - Plain-language : how do I respect reduced motion, WCAG 2.2 requirements, what contrast ratio, accessible color contrast, minimum target size for buttons

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://www.w3.org/TR/WCAG22/
  - https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Does this interactive target meet 2.5.8?"
    Branch : 24x24 CSS pixels or larger -> pass
    Branch : smaller but spaced (24px-circle test) -> pass (spacing exception)
    Branch : inline in running text -> pass (inline exception)
    Branch : equivalent 24x24 control elsewhere -> pass (equivalent exception)
    Branch : user-agent or essential -> pass with documentation
    Branch : none of the above -> fail, redesign
  - Question : "How do I handle motion?"
    Branch : decorative motion -> wrap in `@media (prefers-reduced-motion: no-preference)` so it only animates when user opts in
    Branch : functional motion (state change feedback) -> reduce intensity inside `@media (prefers-reduced-motion: reduce)`, opacity-only crossfade
  - Question : "Contrast ratio for this text?"
    Branch : normal text (under 18pt or 14pt bold) -> 4.5:1 (AA), 7:1 (AAA)
    Branch : large text (>=18pt or 14pt bold) -> 3:1 (AA), 4.5:1 (AAA)
    Branch : UI components / graphical objects -> 3:1 (AA non-text)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : animate by default, no `prefers-reduced-motion` check -> motion-sick users; gate or invert
  - Anti-pattern 2 : 16x16 icon button -> fails 2.5.8; minimum 24x24 with the 5-exception rules
  - Anti-pattern 3 : 4.4:1 body text contrast -> just below 4.5:1; fix to pass AA
  - Anti-pattern 4 : focus indicator obscured by sticky header -> WCAG 2.4.11 violation; use `scroll-margin-top` to offset
  - Anti-pattern 5 : CAPTCHA as only auth path -> WCAG 3.3.8 violation; provide non-cognitive alternative
  - Anti-pattern 6 : color-only error indication on form -> WCAG 1.4.1 violation; pair color with icon + text

Renderable HTML fragment required in references/examples.md : YES — demo with motion that respects `prefers-reduced-motion`, contrast-meeting text examples with `light-dark()`, and minimum-target-size buttons.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-a11y-aria-patterns]]`, `[[frontend-a11y-focus-keyboard-inert]]`, `[[frontend-theming-color-palette-oklch]]`, `[[frontend-impl-view-transitions-scroll-animations]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-a11y-motion-contrast-wcag22
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-perf-core-web-vitals-inp

**Batch** : 6
**Category** : performance
**Depends on** : frontend-core-architecture

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/performance/frontend-perf-core-web-vitals-inp/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §7 (Core Web Vitals; Optimizing INP) and §5 (Speculation Rules folded in per D-R10)
  - docs/research/topic-research/frontend-perf-core-web-vitals-inp-research.md
  - Phase 4 MUST drill scheduler.yield per §14 verification gap #5 and Speculation Rules eagerness per §14 gap #10

Scope (binding bullets) :
  - LCP : <=2.5s good, 2.5-4s improve, >4s poor; preload LCP image / webfont, `fetchpriority="high"`, explicit width/height to prevent CLS
  - INP : <=200ms good, 200-500ms improve, >500ms poor (replaced FID March 2024); 75th percentile threshold
  - INP decomposition : input delay (waiting for main thread), processing duration (handler execution), presentation delay (next frame)
  - INP tactics : break long tasks with `await scheduler.yield()` (or `setTimeout(fn, 0)` fallback); apply visual update FIRST in handler; debounce expensive handlers; `content-visibility: auto` for off-screen
  - CLS : <=0.1 good; explicit `width`/`height` on `<img>`, `aspect-ratio` CSS, reserve space for late-loading content; font-display strategy with `size-adjust`/`ascent-override`
  - Image budgets : `loading="lazy"` below-fold, `fetchpriority="high"` for LCP, modern formats (AVIF/WebP) with `<picture>`
  - Font budgets : `font-display: swap` + `size-adjust` matched fallback; preload LCP webfont
  - Speculation Rules API (Chromium-only, Limited Availability per §11) : `prefetch` / `prerender`; MUST exclude logout / cart-mutation / `[rel~=nofollow]` URLs

Out-of-scope (binding) :
  - NO animation-specific perf (deferred to `[[frontend-perf-animation-gpu-containment]]`)
  - NO CSS containment deep-dive (deferred to `[[frontend-perf-animation-gpu-containment]]`)
  - NO RUM tool integration (Datadog / Cloudflare / etc.)

Required references per SKILL.md frontmatter Keywords :
  - Technical : LCP, INP, CLS, Core Web Vitals, fetchpriority, loading=lazy, content-visibility, scheduler.yield, requestIdleCallback, Speculation Rules, prerender, prefetch, font-display, size-adjust, aspect-ratio
  - Symptom-based : page slow, click lag, layout shift, font flash, large image jank, INP failing
  - Plain-language : how do I improve LCP, what is INP, how to fix layout shift, core web vitals, speed up first contentful paint, how to lazy load images

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://web.dev/articles/vitals
  - https://web.dev/articles/optimize-inp
  - https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display
  - https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "What is hurting LCP?"
    Branch : hero image is LCP candidate -> `<img fetchpriority="high">` + `<link rel="preload" as="image">`
    Branch : webfont is in LCP element -> `<link rel="preload" as="font" type="font/woff2" crossorigin>` + `font-display: swap` + matched fallback metrics
    Branch : render-blocking JS -> defer or async
  - Question : "Why is INP > 200ms?"
    Branch : long synchronous handler -> yield with `await scheduler.yield()` or split into chunks
    Branch : layout thrashing in handler -> batch DOM reads then writes
    Branch : heavy off-screen content -> `content-visibility: auto` + `contain-intrinsic-size`
  - Question : "Add Speculation Rules?"
    Branch : Chromium-only acceptable, no destructive URLs in scope -> add `prerender` for likely-next pages
    Branch : non-Chromium critical -> use `prefetch` only (or skip)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `<img>` without width/height -> CLS as image loads; always reserve space
  - Anti-pattern 2 : `loading="lazy"` on LCP image -> defers LCP; use only for below-fold
  - Anti-pattern 3 : synchronous long task in click handler -> INP failure; yield via `scheduler.yield()` or split
  - Anti-pattern 4 : `font-display: block` -> long invisible-text period; use `swap` with size-adjust fallback
  - Anti-pattern 5 : Speculation Rules prerendering `/logout` -> destructive prerender; use `where: { not: { href_matches: "/logout" } }`
  - Anti-pattern 6 : `content-visibility: auto` without `contain-intrinsic-size` -> scrollbar jitter as off-screen items size to 0

Renderable HTML fragment required in references/examples.md : YES — index.html with optimised LCP image (fetchpriority, preload, explicit dimensions), Speculation Rules JSON in head excluding destructive paths, and a click handler that yields between sub-tasks.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-perf-animation-gpu-containment]]`, `[[frontend-impl-typography-system]]`, `[[frontend-errors-animation-jank]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-perf-core-web-vitals-inp
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-perf-animation-gpu-containment

**Batch** : 6
**Category** : performance
**Depends on** : frontend-core-architecture

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/performance/frontend-perf-animation-gpu-containment/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §7 (Layout/paint isolation; GPU-friendly animation) and §10 (Anti-patterns 7, 8)
  - docs/research/topic-research/frontend-perf-animation-gpu-containment-research.md

Scope (binding bullets) :
  - Compositor-only animation : ALWAYS animate ONLY `transform`, `opacity`, and `filter`
  - NEVER animate `width`, `height`, `top`, `left`, `margin`, `padding`, `font-size`, `box-shadow` in production interactions
  - `will-change` : apply on interaction-start (`onpointerenter`), remove on interaction-end (`onpointerleave` / `animationend`); over-use exhausts GPU memory
  - `contain` values : `none` / `strict` / `content` / `size` / `inline-size` / `layout` / `style` / `paint`; `contain: content` is safe default; side effects : new containing block / stacking context / BFC
  - `content-visibility: auto` for off-screen subtree skipping; MUST pair with `contain-intrinsic-size: auto Npx`
  - `@property` for typed customs to enable transitioning custom properties (covered briefly here, deeper in `[[frontend-theming-color-palette-oklch]]`)
  - Critical CSS strategy : inline first-paint-needed CSS; defer rest

Out-of-scope (binding) :
  - NO Core Web Vitals metrics (deferred to `[[frontend-perf-core-web-vitals-inp]]`)
  - NO animation choreography (deferred to `[[frontend-visual-micro-interactions]]`)
  - NO View Transitions API (deferred to `[[frontend-impl-view-transitions-scroll-animations]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : will-change, contain, content-visibility, contain-intrinsic-size, transform, opacity, filter, @property, GPU layer, compositor, critical CSS
  - Symptom-based : animation laggy, GPU memory exhausted, scroll stutter, paint storm, layout thrash, off-screen content slow
  - Plain-language : how do I make animation smooth, what is GPU-friendly animation, how to use CSS contain, when to use will-change, why is my page slow

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/contain
  - https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@property

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Can I animate this property?"
    Branch : `transform`, `opacity`, `filter` -> YES, compositor-only
    Branch : `width`, `height`, `top`, `left`, `margin`, `padding`, `font-size`, `box-shadow` -> NO, layout/paint trigger; rewrite using transform/opacity
    Branch : custom property -> use `@property` to type it; then animatable
  - Question : "Should I use `will-change`?"
    Branch : interaction known imminent -> add on pointerdown/enter, remove on end
    Branch : "just in case" -> NEVER; memory drain
    Branch : during a CSS transition -> already promoted; no need to add
  - Question : "What `contain` value?"
    Branch : need layout + paint + style isolation -> `contain: content`
    Branch : need + size isolation (explicit dimensions known) -> `contain: strict`
    Branch : only style scope -> `contain: style`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `transition: width 200ms` -> layout each frame; rewrite to `transform: scaleX(...)` or use grid template animation
  - Anti-pattern 2 : `will-change: transform` on every card -> GPU memory exhausted; apply on interaction only
  - Anti-pattern 3 : `content-visibility: auto` without `contain-intrinsic-size` -> scrollbar jitter and incorrect scroll-position
  - Anti-pattern 4 : `@keyframes ... { from { box-shadow: ...; } to { box-shadow: ...; } }` -> paint storm; use `transform` for shadow-like depth via inset filter or stack layered transformed pseudo-elements
  - Anti-pattern 5 : `contain: strict` on element without explicit size -> collapses to 0; provide size or use `contain: content`
  - Anti-pattern 6 : `requestAnimationFrame` infinite-loop animation -> use CSS animation; rAF only for state-tied animation

Renderable HTML fragment required in references/examples.md : YES — long scroll page with 100 items, each wrapped in `content-visibility: auto` + `contain-intrinsic-size`; show transform-only entrance animation.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-perf-core-web-vitals-inp]]`, `[[frontend-visual-micro-interactions]]`, `[[frontend-errors-animation-jank]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-perf-animation-gpu-containment
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-errors-animation-jank

**Batch** : 6
**Category** : errors
**Depends on** : frontend-core-architecture, frontend-perf-animation-gpu-containment (same batch)

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/errors/frontend-errors-animation-jank/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §7 (Layout/paint isolation; GPU-friendly animation), §9 (Micro-interactions easing), §10 (Anti-patterns 6, 7, 8)
  - docs/research/topic-research/frontend-errors-animation-jank-research.md

Scope (binding bullets) :
  - Diagnose jank : Chrome DevTools Performance panel, Layers panel, Paint Flashing
  - Common root causes : animating layout-trigger properties, paint storms, forced sync layout (layout thrashing), composite-layer overflow, blocking main thread
  - ResizeObserver loop ("ResizeObserver loop completed with undelivered notifications") : callback mutates observed size in same frame
  - Forced sync layout : reading `offsetTop` / `getBoundingClientRect` immediately after writing to DOM
  - `requestAnimationFrame` correct usage : one rAF callback per frame for state-tied animation; never inside event handler for one-off
  - `will-change` misuse leading to GPU memory pressure
  - Mobile-specific jank patterns : touchstart vs pointerdown, passive event listeners, scroll-linked effects

Out-of-scope (binding) :
  - NO Core Web Vitals (deferred to `[[frontend-perf-core-web-vitals-inp]]`)
  - NO compositor-only animation rules (deferred to `[[frontend-perf-animation-gpu-containment]]`)
  - NO scroll-snap behavior (deferred to `[[frontend-impl-view-transitions-scroll-animations]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : ResizeObserver loop, requestAnimationFrame, getBoundingClientRect, layout thrash, paint storm, will-change, composite layer, Performance panel, paint flashing, passive event listener
  - Symptom-based : animation choppy, scroll jank, frame drop, ResizeObserver warning, slow interaction, mobile lag, dropped frames
  - Plain-language : why is my animation laggy, how to fix scroll jank, ResizeObserver loop warning, how to find what is slow, debug animation performance

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver
  - https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame
  - https://developer.mozilla.org/en-US/docs/Web/CSS/contain

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Animation is laggy. Where do I look first?"
    Branch : check which property is animated -> if layout-trigger, rewrite to transform/opacity
    Branch : open DevTools Performance, record interaction -> look for purple (layout) and green (paint) bars
    Branch : enable Paint Flashing -> if entire page flashes, find the parent that triggers global paint
  - Question : "ResizeObserver loop warning"
    Branch : callback writes to observed element size -> wrap in `requestAnimationFrame` to defer to next frame
    Branch : callback writes to ancestor that resizes child -> use `WeakMap` of expected sizes; skip callback when delta is zero
  - Question : "Layout thrashing in handler"
    Branch : reading + writing repeatedly -> batch reads first, then all writes
    Branch : in scroll handler -> use IntersectionObserver / scroll-driven animation instead

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `el.style.height = el.offsetHeight + 10 + 'px'` in loop -> forced sync layout each iteration; cache value before loop
  - Anti-pattern 2 : ResizeObserver callback that resizes observed element -> loop warning; wrap in rAF
  - Anti-pattern 3 : touchstart handler synchronously updating DOM -> blocks scroll; use `{ passive: true }` for non-preventing handlers
  - Anti-pattern 4 : multiple `requestAnimationFrame` callbacks per frame -> sometimes okay; usually wasted work; consolidate
  - Anti-pattern 5 : `setInterval(animate, 16)` -> not synced to refresh rate; use `requestAnimationFrame` recursion
  - Anti-pattern 6 : `transition: all 200ms` -> animates unintended properties (incl. layout); list properties explicitly

Renderable HTML fragment required in references/examples.md : YES — before/after example : "bad" panel animation using `width` transition; "good" version using `transform: scaleX(...)` with `transform-origin: left`.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-perf-animation-gpu-containment]]`, `[[frontend-perf-core-web-vitals-inp]]`, `[[frontend-visual-micro-interactions]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-errors-animation-jank
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-theming-color-palette-oklch

**Batch** : 7
**Category** : theming
**Depends on** : frontend-core-architecture, frontend-syntax-css-color-modern

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/theming/frontend-theming-color-palette-oklch/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Modern color), §8 (Design Tokens — color), §13 (theming-color-palette kept)
  - docs/research/topic-research/frontend-theming-color-palette-oklch-research.md

Scope (binding bullets) :
  - Systematic palette generation from a single brand seed using `oklch(from var(--seed) calc(l + Nx) c h)` relative-color syntax
  - Standard shade ladder (50, 100, 200, ..., 900, 950) with predictable contrast at each step
  - Brand-to-system three-tier token chain : raw brand (`--brand-blue-500`) -> primitive token -> semantic token (`--color-action-primary`) -> component token (`--button-primary-bg`)
  - Contrast guarantees per shade : 50/100/200/300 paired with 700/800/900 for AA-passing combinations
  - Wide-gamut considerations (display-p3 / rec2020) and sRGB fallbacks via `color-mix` clamp
  - `@property` typed customs for animatable color tokens (`--gradient-angle`)
  - Token emission as CSS custom properties under `@layer tokens, theme, base, components, utilities;`

Out-of-scope (binding) :
  - NO `color-mix` / `oklch` syntax details (covered in `[[frontend-syntax-css-color-modern]]`)
  - NO dark/light mode mechanics (deferred to `[[frontend-theming-dark-light-mode]]`)
  - NO design tokens infrastructure (deferred to `[[frontend-impl-design-tokens]]`)
  - NO WCAG contrast measurement (deferred to `[[frontend-a11y-motion-contrast-wcag22]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : oklch, relative color, color scale, shade ladder, brand color, semantic token, component token, @property, perceptually uniform palette
  - Symptom-based : colors do not look balanced, gradient muddy, shade ladder uneven, hardcoded hex everywhere, brand-color change painful
  - Plain-language : how do I make a color palette, oklch shade generator, how to derive colors from one brand color, color tokens for a design system

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/oklch
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@property
  - https://www.designtokens.org/tr/drafts/format/

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Brand color or shade?"
    Branch : derive from one seed -> `oklch(from var(--brand-seed) calc(l - 0.4) c h)` for 700-shade
    Branch : need wide-gamut accent -> `oklch(75% 0.25 320)` directly in display-p3 range
  - Question : "Token tier?"
    Branch : raw brand -> primitive layer
    Branch : action / feedback / surface intent -> semantic layer
    Branch : component-specific override -> component layer (references semantic)
  - Question : "Should this custom property be `@property`-typed?"
    Branch : transitioned or animated -> ALWAYS @property
    Branch : static lookup -> regular --var is fine

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : hardcoded `#3b82f6` throughout codebase -> tokenize; emit as `--color-brand-500`
  - Anti-pattern 2 : HSL-derived shade ladder -> perceptually uneven; use OKLCH
  - Anti-pattern 3 : single-tier tokens (brand color = button color) -> brittle to brand change; use three-tier chain
  - Anti-pattern 4 : transitioning untyped `--color` -> interpolation-opaque; register with @property
  - Anti-pattern 5 : assuming oklch L=50% is the perceptual middle of every hue -> chroma affects perceived lightness; verify with tools
  - Anti-pattern 6 : missing sRGB fallback for display-p3 -> wide-gamut colors clip; provide fallback via `@supports (color: color(display-p3 1 0 0))`

Renderable HTML fragment required in references/examples.md : YES — a token swatch page showing one brand seed and its derived shade ladder (50-950), each with text-on-background contrast pairs labelled with AA/AAA pass/fail.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-color-modern]]`, `[[frontend-theming-dark-light-mode]]`, `[[frontend-impl-design-tokens]]`, `[[frontend-a11y-motion-contrast-wcag22]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-theming-color-palette-oklch
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-theming-dark-light-mode

**Batch** : 7
**Category** : theming
**Depends on** : frontend-core-architecture, frontend-syntax-css-color-modern

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/theming/frontend-theming-dark-light-mode/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (light-dark function) and §6 (prefers-color-scheme)
  - docs/research/topic-research/frontend-theming-dark-light-mode-research.md

Scope (binding bullets) :
  - `color-scheme: light dark` on `:root` enables UA-themed scrollbars and form controls
  - `light-dark(<light-value>, <dark-value>)` : eliminates duplication when `color-scheme` is set
  - `prefers-color-scheme: light | dark` media query for cases where `light-dark()` is insufficient (image swaps, complete overrides)
  - User-toggle pattern : `[data-theme="dark"]` attribute on `<html>`, persisted in localStorage, applied before first paint to avoid FOUC
  - No-flash-of-unstyled-theme : inline `<script>` in `<head>` reading localStorage and setting `data-theme` BEFORE CSS loads
  - System-only mode : let OS preference rule (no toggle); `color-scheme` + `light-dark()` cover everything
  - Three modes : system / light-forced / dark-forced (UI toggle)
  - Theme-specific images via `<picture>` with `<source media="(prefers-color-scheme: dark)">`

Out-of-scope (binding) :
  - NO oklch palette generation (deferred to `[[frontend-theming-color-palette-oklch]]`)
  - NO design tokens infrastructure (deferred to `[[frontend-impl-design-tokens]]`)
  - NO contrast checks (deferred to `[[frontend-a11y-motion-contrast-wcag22]]`)

Required references per SKILL.md frontmatter Keywords :
  -技术 (Technical) : color-scheme, light-dark, prefers-color-scheme, data-theme, FOUC, localStorage, theme toggle, picture media
  - Symptom-based : white flash on dark mode, scrollbar wrong color, form controls light in dark theme, theme toggle does not persist, dark mode flash on reload
  - Plain-language : how do I add dark mode, light dark function CSS, prevent flash of unstyled theme, dark mode toggle, what is color-scheme

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/light-dark
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme
  - https://developer.mozilla.org/en-US/docs/Web/CSS/color-scheme

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "User-toggle or system-only?"
    Branch : system follows OS -> `color-scheme: light dark` on `:root` + `light-dark()` everywhere; done
    Branch : user can override -> add toggle, `[data-theme="dark"]` attribute, persist
  - Question : "Avoiding flash on reload?"
    Branch : critical path -> inline `<script>` in `<head>` reading localStorage and setting `data-theme` BEFORE CSS loads
    Branch : tolerable flash -> CSS-only `@media (prefers-color-scheme: dark) { ... }`
  - Question : "Per-element dark image?"
    Branch : `<picture>` with `<source media="(prefers-color-scheme: dark)" srcset="...">`
    Branch : CSS background-image -> `light-dark(url(light.png), url(dark.png))`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `light-dark()` without `color-scheme: light dark` on `:root` -> always returns light value
  - Anti-pattern 2 : theme stored in localStorage but applied via `useEffect` -> flash; apply in head script before paint
  - Anti-pattern 3 : duplicating every rule in `@media (prefers-color-scheme: dark)` -> noise; use `light-dark()`
  - Anti-pattern 4 : ignoring `color-scheme` -> scrollbars and form controls remain light in dark theme
  - Anti-pattern 5 : dark mode toggle without aria-pressed -> screen reader users do not know toggle state
  - Anti-pattern 6 : forcing dark mode regardless of system -> respect user preference; offer override but default to system

Renderable HTML fragment required in references/examples.md : YES — complete demo with `color-scheme: light dark`, `light-dark()` used throughout, plus an accessible toggle button (system / light / dark) that persists to localStorage and applies via inline head script (no FOUC).

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-theming-color-palette-oklch]]`, `[[frontend-syntax-css-color-modern]]`, `[[frontend-a11y-motion-contrast-wcag22]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-theming-dark-light-mode
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-visual-glassmorphism-backdrop

**Batch** : 7
**Category** : visual-effects
**Depends on** : frontend-core-architecture, frontend-syntax-css-color-modern

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/visual-effects/frontend-visual-glassmorphism-backdrop/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §9 (Glassmorphism) and §10 (Anti-pattern 9 backdrop-filter not blurring)
  - docs/research/topic-research/frontend-visual-glassmorphism-backdrop-research.md

Scope (binding bullets) :
  - `backdrop-filter` syntax and filter functions : `blur()`, `brightness()`, `contrast()`, `drop-shadow()`, `grayscale()`, `hue-rotate()`, `invert()`, `opacity()`, `saturate()`, `sepia()`, `url(#svg-filter)`; Baseline 2024
  - Requires partially-transparent `background-color` (e.g. `rgb(255 255 255 / 0.1)`) to see the backdrop
  - Backdrop-root critical pitfall : `backdrop-filter` only sees content up to nearest backdrop-root ancestor; properties that establish backdrop-root : `opacity < 1`, any `filter`, `mask`, `mask-image`, `mix-blend-mode`, `clip-path`, another `backdrop-filter`
  - `-webkit-backdrop-filter` no longer required as of 2024 but harmless to ship
  - Contrast preservation : background-color opacity affects readability of text on glass; ALWAYS verify WCAG contrast against effective backdrop
  - When NOT to use : on top of arbitrary user content (text becomes unreadable); on mobile if compositor cost too high

Out-of-scope (binding) :
  - NO gradients (deferred to `[[frontend-visual-gradients]]`)
  - NO micro-interaction motion (deferred to `[[frontend-visual-micro-interactions]]`)
  - NO `light-dark()` theming details

Required references per SKILL.md frontmatter Keywords :
  - Technical : backdrop-filter, blur, saturate, glassmorphism, backdrop-root, stacking context, filter, contrast, WCAG 1.4.11
  - Symptom-based : backdrop-filter not blurring, glass effect not working, text unreadable on glass, blurred background not showing
  - Plain-language : how do I make a glass effect, frosted glass CSS, backdrop blur, glassmorphism in CSS, why is my backdrop-filter ignored

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/backdrop-filter

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Backdrop-filter not blurring. What is wrong?"
    Branch : background-color is fully opaque -> reduce alpha (e.g. `rgb(255 255 255 / 0.1)`)
    Branch : ancestor has `opacity < 1`, `filter`, `mask`, `mix-blend-mode`, `clip-path`, or another `backdrop-filter` -> ancestor became backdrop-root; remove or restructure
    Branch : browser too old -> Baseline 2024; gate with @supports
  - Question : "Glass on top of arbitrary content?"
    Branch : verify text-on-effective-backdrop contrast meets WCAG 1.4.3 (4.5:1 normal, 3:1 large)
    Branch : if contrast can not be guaranteed, add a semi-opaque overlay layer between content and glass

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : opaque background -> no backdrop visible; use rgba/oklch with alpha
  - Anti-pattern 2 : parent `opacity: 0.95` for "fade-in" -> establishes backdrop-root; use `transform: opacity` only on the glass child, not parent
  - Anti-pattern 3 : glass over user-generated content without contrast check -> WCAG violation
  - Anti-pattern 4 : `backdrop-filter` on a fixed sticky element at top of long scroll -> compositor cost; consider intersection-toggle
  - Anti-pattern 5 : missing `@supports (backdrop-filter: blur(1px))` for pre-2024 -> ungraceful degradation
  - Anti-pattern 6 : `-webkit-backdrop-filter` only (no unprefixed) -> works in Safari but broken in modern Firefox

Renderable HTML fragment required in references/examples.md : YES — sticky header with `backdrop-filter: blur(12px) saturate(180%)` over scrolling content; second example showing a glass card that breaks because parent has `opacity: 0.9` (illustrates backdrop-root pitfall) and the fix.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-visual-gradients]]`, `[[frontend-visual-micro-interactions]]`, `[[frontend-a11y-motion-contrast-wcag22]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-visual-glassmorphism-backdrop
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-visual-gradients

**Batch** : 8
**Category** : visual-effects
**Depends on** : frontend-syntax-css-color-modern, frontend-theming-color-palette-oklch

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/visual-effects/frontend-visual-gradients/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §9 (Modern gradients)
  - docs/research/topic-research/frontend-visual-gradients-research.md

Scope (binding bullets) :
  - `linear-gradient()`, `radial-gradient()`, `conic-gradient()` syntax, all Baseline Widely Available
  - Color interpolation : `linear-gradient(in oklab to right, ...)` and `linear-gradient(in oklch longer hue, ...)` for smoother perceptual transitions
  - Multi-stop gradients with explicit positions
  - Mesh-gradient emulation : layered radial gradients on the same element using `background: radial-gradient(...), radial-gradient(...)`; OR SVG `<feGaussianBlur>` filter on randomized circles
  - Animated gradients : `@property --gradient-angle { syntax: '<angle>'; inherits: false; initial-value: 0deg; }` then `transition: --gradient-angle 200ms`
  - Repeating gradients : `repeating-linear-gradient`, `repeating-conic-gradient` for patterns
  - Performance : large multi-stop gradients can paint slowly; use `will-change: background` on interaction only

Out-of-scope (binding) :
  - NO color palette systems (deferred to `[[frontend-theming-color-palette-oklch]]`)
  - NO modern color syntax (deferred to `[[frontend-syntax-css-color-modern]]`)
  - NO scroll-driven gradient animations

Required references per SKILL.md frontmatter Keywords :
  - Technical : linear-gradient, radial-gradient, conic-gradient, color interpolation, in oklch, in oklab, mesh gradient, @property, gradient angle, repeating gradient
  - Symptom-based : gradient banding, gradient muddy in middle, animated gradient flickers, mesh gradient not possible
  - Plain-language : how do I make a smooth gradient, animated gradient CSS, mesh gradient in browser, conic gradient examples

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/gradient/linear-gradient
  - https://developer.mozilla.org/en-US/docs/Web/CSS/gradient/conic-gradient
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@property

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Which gradient type?"
    Branch : two-color across an axis -> `linear-gradient(in oklch to right, ...)`
    Branch : from a point outward -> `radial-gradient(circle at 50% 50%, ...)`
    Branch : sweeping around a point (pie chart, color wheel) -> `conic-gradient(from 0deg, ...)`
    Branch : mesh-style multi-point blend -> stack multiple radial gradients OR SVG filter approach
  - Question : "Why does my gradient look muddy?"
    Branch : default `in srgb` interpolation -> add `in oklab` or `in oklch` colorspace
  - Question : "Animate a gradient?"
    Branch : rotate -> `@property --gradient-angle` + transition
    Branch : color shift -> transition CSS custom property of color (also requires @property)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : default sRGB interpolation between complementary colors -> muddy midpoint; use `in oklab`
  - Anti-pattern 2 : transitioning an untyped gradient custom property -> no interpolation; register with @property
  - Anti-pattern 3 : `background-attachment: fixed` for parallax gradient -> compositor disaster on mobile; use scroll-driven animation
  - Anti-pattern 4 : 30-stop gradient for "smooth" effect -> overkill; 3-5 well-placed stops with `in oklch` look smoother
  - Anti-pattern 5 : conic-gradient for pie chart without semantic role -> add `role="img"` + `aria-label` for screen readers
  - Anti-pattern 6 : animated gradient without `prefers-reduced-motion` check -> motion-sensitive users; gate

Renderable HTML fragment required in references/examples.md : YES — three gradients : a smooth two-color linear with `in oklch`, a conic gradient for a "loading ring", and a mesh-emulation using stacked radials.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-color-modern]]`, `[[frontend-theming-color-palette-oklch]]`, `[[frontend-visual-glassmorphism-backdrop]]`, `[[frontend-a11y-motion-contrast-wcag22]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-visual-gradients
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-visual-micro-interactions

**Batch** : 8
**Category** : visual-effects
**Depends on** : frontend-syntax-css-has-selector, frontend-perf-animation-gpu-containment

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/visual-effects/frontend-visual-micro-interactions/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §9 (Micro-interactions) and §6 (prefers-reduced-motion)
  - docs/research/topic-research/frontend-visual-micro-interactions-research.md

Scope (binding bullets) :
  - Hover / focus / press transitions : 150-250ms duration, `cubic-bezier(0.2, 0, 0, 1)` Material easing or `cubic-bezier(0.16, 1, 0.3, 1)` smooth ease-out
  - NEVER use bare `ease` keyword (visually flat)
  - `:active` press state with `transform: scale(0.97)` for haptic-like compression
  - Cross-component choreography via `:has()` : `.card:has(button:hover) { ... }`, `.card:has(:focus-visible) { outline: ... }`
  - ALL motion MUST collapse to opacity-only (or none) inside `@media (prefers-reduced-motion: reduce)`
  - Group animations : staggered children with `transition-delay: calc(var(--index) * 50ms)`
  - State machine via CSS classes for complex interactions (not the place for JS animations libraries)

Out-of-scope (binding) :
  - NO View Transitions API (deferred to `[[frontend-impl-view-transitions-scroll-animations]]`)
  - NO scroll-driven animations (deferred to `[[frontend-impl-view-transitions-scroll-animations]]`)
  - NO compositor-only deep dive (deferred to `[[frontend-perf-animation-gpu-containment]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : transition, cubic-bezier, easing, :hover, :focus-visible, :active, :has, prefers-reduced-motion, transform scale, transition-delay
  - Symptom-based : animation feels flat, hover not smooth, press feedback missing, motion does not respect setting, interaction not satisfying
  - Plain-language : how do I make a button feel good to click, hover transitions, easing curves CSS, micro-interactions, accessible motion

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/transition
  - https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Easing for this interaction?"
    Branch : standard ease-out (most UI) -> `cubic-bezier(0.2, 0, 0, 1)` 200ms
    Branch : smooth pop (entrance) -> `cubic-bezier(0.16, 1, 0.3, 1)` 250ms
    Branch : sharp / mechanical (system feedback) -> `linear` 100-150ms
    Branch : bouncy -> use spring with @keyframes or Web Animations API
  - Question : "Press feedback?"
    Branch : button -> `:active { transform: scale(0.97); }` 100ms
    Branch : card link -> `:active { transform: scale(0.99); }`
    Branch : NEVER use bare `ease` keyword
  - Question : "Cross-component reactive style?"
    Branch : style parent when child is hovered/focused -> `:has()` + tightly anchored selector
    Branch : style sibling -> `:has(+ .sibling:hover)`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `transition: all 200ms` -> animates unintended layout properties; list explicitly
  - Anti-pattern 2 : bare `ease` keyword -> looks robotic; use named cubic-bezier curves
  - Anti-pattern 3 : 500ms transition on hover -> sluggish; 150-250ms is the sweet spot
  - Anti-pattern 4 : animating `width` for slide-in -> layout jank; use `transform: translateX(-100%)` then 0
  - Anti-pattern 5 : motion without `prefers-reduced-motion` override -> accessibility violation
  - Anti-pattern 6 : `:hover` styles without keyboard `:focus-visible` equivalent -> keyboard users miss feedback

Renderable HTML fragment required in references/examples.md : YES — a button group, a card with `:has(button:hover)` parent-reactive styling, and a staggered list entrance using `--index` custom property + `transition-delay`; full `prefers-reduced-motion` reduce variant.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-perf-animation-gpu-containment]]`, `[[frontend-syntax-css-has-selector]]`, `[[frontend-a11y-motion-contrast-wcag22]]`, `[[frontend-impl-view-transitions-scroll-animations]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-visual-micro-interactions
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-impl-design-tokens

**Batch** : 8
**Category** : impl
**Depends on** : frontend-core-architecture, frontend-syntax-css-cascade-layers-scope, frontend-theming-color-palette-oklch

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/impl/frontend-impl-design-tokens/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §8 (Design Tokens — DTCG draft 2025.10, mapping to CSS, three-tier brand chain)
  - docs/research/topic-research/frontend-impl-design-tokens-research.md

Scope (binding bullets) :
  - W3C Design Tokens Format Module (DTCG draft 2025.10) : JSON interchange, `$value` required, `$type` (color / dimension / fontFamily / fontWeight / duration / cubicBezier / number, composite border / shadow / transition / strokeStyle / gradient / typography)
  - Aliases : `{group.subgroup.token}` resolves to target's `$value`
  - Groups organize tokens hierarchically; `$type` inherits to children
  - DTCG draft 2025.10 is NOT yet production-implementation-ready (per spec preamble); skill MUST note this and recommend transformation via Style Dictionary or similar
  - Mapping tokens to CSS : emit as custom properties (`--color-brand-primary: oklch(60% 0.2 240);`), layered via `@layer tokens, theme, base, components, utilities;`
  - Three-tier chain : raw brand -> primitive -> semantic -> component (see `[[frontend-theming-color-palette-oklch]]`)
  - `@property` to register animatable tokens
  - Runtime theme switching : flip `color-scheme` on a region, OR toggle `data-theme` attribute
  - NEVER hardcode hex / rgb / oklch values outside the token layer

Out-of-scope (binding) :
  - NO oklch palette generation (deferred to `[[frontend-theming-color-palette-oklch]]`)
  - NO dark/light implementation specifics (deferred to `[[frontend-theming-dark-light-mode]]`)
  - NO specific tooling (Style Dictionary, Tokens Studio) deep-dive — mentioned for reference only

Required references per SKILL.md frontmatter Keywords :
  - Technical : design tokens, DTCG, $value, $type, $description, JSON, custom properties, --color, @property, three-tier tokens, primitive, semantic, component token, alias
  - Symptom-based : hardcoded hex everywhere, brand change painful, design system fragmented, no single source of truth for colors
  - Plain-language : what are design tokens, how do I structure design tokens, three-tier token chain, DTCG W3C draft, design tokens to CSS

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://www.designtokens.org/tr/drafts/format/
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@property
  - https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Token tier for this value?"
    Branch : raw brand color / brand-specific font -> primitive
    Branch : intent ("action", "feedback success") -> semantic
    Branch : component-specific override -> component
  - Question : "Should this token use @property?"
    Branch : transitioned or animated -> ALWAYS @property
    Branch : static lookup -> regular --var
  - Question : "Theme switching at runtime?"
    Branch : OS preference only -> `color-scheme: light dark` + `light-dark()`
    Branch : user override -> `[data-theme="dark"]` attribute with overriding token layer

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : hardcoded `#3b82f6` -> tokenize; emit as `--color-brand-500`
  - Anti-pattern 2 : single-tier tokens (brand color directly = button bg) -> brittle; introduce semantic + component tiers
  - Anti-pattern 3 : not layering tokens -> tokens overridden by component CSS; place in `@layer tokens`
  - Anti-pattern 4 : transitioning untyped custom property -> no interpolation; register with @property
  - Anti-pattern 5 : exposing tokens with leak-prone names (`--my-bg`) -> use namespaced kebab-case (`--color-bg-surface`)
  - Anti-pattern 6 : skipping DTCG and inventing custom JSON shape -> migration pain later; follow DTCG even if tooling immature

Renderable HTML fragment required in references/examples.md : YES — small token JSON snippet + a CSS `@layer tokens { :root { --color-brand-500: oklch(60% 0.2 240); --color-action-primary: var(--color-brand-500); --button-primary-bg: var(--color-action-primary); } }` + button using the component token; show theme-switch via `[data-theme="dark"]` override.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-theming-color-palette-oklch]]`, `[[frontend-theming-dark-light-mode]]`, `[[frontend-syntax-css-cascade-layers-scope]]`, `[[frontend-impl-typography-system]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-impl-design-tokens
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-impl-responsive-layout-fluid

**Batch** : 9
**Category** : impl
**Depends on** : frontend-syntax-css-container-queries, frontend-syntax-css-grid-subgrid, frontend-syntax-css-nesting-logical-properties

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/impl/frontend-impl-responsive-layout-fluid/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (clamp; Container Queries) and §13 (card-layouts folded in per D-R04)
  - docs/research/topic-research/frontend-impl-responsive-layout-fluid-research.md

Scope (binding bullets) :
  - Mobile-first vs desktop-first : ALWAYS mobile-first (`min-width`, progressive enhancement)
  - Container queries replacing media queries for component-internal layout decisions; media queries for viewport-level only
  - Fluid typography with `clamp(min, preferred, max)` : pattern `font-size: clamp(1rem, 0.5rem + 2vw, 2rem)`
  - No breakpoint cliff : design fluidly between viewports, not at fixed breakpoints
  - Worked example : card layouts (folded in per D-R04) — container-queried card responding to its parent slot width
  - Grid + subgrid composition for content alignment
  - Logical properties throughout (RTL-safe)
  - Intrinsic sizing (`min-content`, `max-content`, `fit-content()`)

Out-of-scope (binding) :
  - NO `@container` syntax deep-dive (deferred to `[[frontend-syntax-css-container-queries]]`)
  - NO Grid syntax deep-dive (deferred to `[[frontend-syntax-css-grid-subgrid]]`)
  - NO modular type scale (deferred to `[[frontend-impl-typography-system]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : mobile-first, container query, @container, clamp, fluid typography, min-content, max-content, fit-content, subgrid, intrinsic sizing, card layout, grid template
  - Symptom-based : layout breaks at certain widths, breakpoint cliff, font too small mobile, container does not adapt, card wrapping wrong
  - Plain-language : how do I make a responsive layout, fluid font size, modern responsive design, container queries vs media queries, card grid layout

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries
  - https://developer.mozilla.org/en-US/docs/Web/CSS/clamp
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_logical_properties_and_values

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Container query or media query?"
    Branch : layout decision is about the component's own context -> container query
    Branch : layout decision is about the viewport / device -> media query
  - Question : "Should typography be fluid?"
    Branch : body / paragraph -> usually fixed; readability priority
    Branch : headings, display type -> fluid with `clamp()` for visual hierarchy across viewports
  - Question : "Card grid layout?"
    Branch : fixed columns by viewport -> `grid-template-columns: repeat(auto-fill, minmax(280px, 1fr))`
    Branch : cards adapt to slot width independently -> container-typed parent + container query in card

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : fixed pixel breakpoints (`@media (max-width: 768px)` everywhere) -> use fluid clamp + container queries instead
  - Anti-pattern 2 : container query without `container-type` on parent -> never matches; ensure parent has container-type
  - Anti-pattern 3 : `font-size: 18px` for all viewports -> use `clamp()` for headings
  - Anti-pattern 4 : `max-width: 100vw` overflow trap on mobile (scrollbar widens viewport) -> use `100dvw` or `100%`
  - Anti-pattern 5 : `flex-wrap` without `min-width: 0` on children -> children overflow; reset min-width
  - Anti-pattern 6 : physical margin properties in RTL-targeted product -> use `margin-inline-start` etc.

Renderable HTML fragment required in references/examples.md : YES — a card grid where each card is container-queried (vertical layout below 400px, horizontal above) AND fluid type with `clamp()`; verify in browser by resizing.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-container-queries]]`, `[[frontend-syntax-css-grid-subgrid]]`, `[[frontend-syntax-css-nesting-logical-properties]]`, `[[frontend-impl-typography-system]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-impl-responsive-layout-fluid
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-impl-typography-system

**Batch** : 9
**Category** : impl
**Depends on** : frontend-impl-design-tokens, frontend-perf-core-web-vitals-inp

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/impl/frontend-impl-typography-system/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (clamp), §7 (image / font budgets), §14 (font-feature-settings vs variation-settings gap)
  - docs/research/topic-research/frontend-impl-typography-system-research.md
  - Phase 4 MUST drill font-feature / variation-settings per §14 verification gap #9

Scope (binding bullets) :
  - Modular scale : choose a ratio (1.125 minor second / 1.2 minor third / 1.25 major third / 1.333 perfect fourth / 1.5 perfect fifth)
  - Fluid type with `clamp()` for headings : `font-size: clamp(2rem, 1rem + 4vw, 5rem)`
  - Variable fonts : `font-variation-settings: "wght" 500, "wdth" 100, "opsz" 16, "slnt" -10` (or `font-weight: 500; font-stretch: 100%;` for standard axes)
  - `font-feature-settings` for OpenType features : `"liga" 1, "kern" 1, "ss01" 1, "tnum" 1`
  - Font loading : preload LCP-affecting webfont via `<link rel="preload" as="font" type="font/woff2" crossorigin>`; `font-display: swap` + `size-adjust`/`ascent-override`/`descent-override` on matched fallback `@font-face` to eliminate CLS
  - Line-height : ALWAYS unitless multiplier (`line-height: 1.5` not `24px`)
  - Optical sizing (`opsz`) for legibility at different scales
  - System font stacks as fallback

Out-of-scope (binding) :
  - NO color tokens (deferred to `[[frontend-theming-color-palette-oklch]]`)
  - NO container queries (covered in `[[frontend-syntax-css-container-queries]]`)
  - NO LCP optimization beyond font-display (deferred to `[[frontend-perf-core-web-vitals-inp]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : modular scale, clamp, font-variation-settings, font-feature-settings, variable font, font-display, swap, size-adjust, ascent-override, descent-override, opsz, line-height, OpenType, ligatures
  - Symptom-based : font flash, CLS from late font, type scale uneven, fonts not loading, fallback font wrong size, headings not fluid
  - Plain-language : how do I make a type system, fluid typography, variable fonts, prevent font flash, modular scale, font loading best practice

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display
  - https://developer.mozilla.org/en-US/docs/Web/CSS/clamp
  - https://developer.mozilla.org/en-US/docs/Web/CSS/font-variation-settings

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Variable or static font?"
    Branch : need multiple weights + width axes -> variable, single woff2 file
    Branch : need only one weight + style -> static is fine; smaller payload
  - Question : "How to prevent CLS from font load?"
    Branch : LCP candidate -> preload + `font-display: swap` + matched fallback via `size-adjust`/`ascent-override`/`descent-override`
    Branch : below-fold -> `font-display: optional` (no swap if not cached)
  - Question : "Type scale?"
    Branch : editorial / generous -> ratio 1.333 or 1.5
    Branch : UI-dense -> ratio 1.125 or 1.2
    Branch : fluid across viewports -> `clamp(min, base + scale, max)`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `font-display: block` -> long invisible text; use `swap` with size-adjust
  - Anti-pattern 2 : no preload on LCP webfont -> font swap delays LCP; preload
  - Anti-pattern 3 : `line-height: 24px` -> breaks with font-size changes; use `1.5` unitless
  - Anti-pattern 4 : loading 6 weights of a webfont -> 600 KB; use variable font with full axis
  - Anti-pattern 5 : missing `font-feature-settings` for tabular numerals in tables -> numbers misalign; add `"tnum" 1`
  - Anti-pattern 6 : ignoring optical-size axis (`opsz`) on display heads -> coarse glyphs at large size; opt in

Renderable HTML fragment required in references/examples.md : YES — full type system : preload webfont, matched fallback font-face with size-adjust, modular scale via custom properties, fluid heading with clamp, body text with `"liga" 1, "kern" 1`.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-impl-design-tokens]]`, `[[frontend-impl-responsive-layout-fluid]]`, `[[frontend-perf-core-web-vitals-inp]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-impl-typography-system
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-impl-popover-dialog-anchor

**Batch** : 9
**Category** : impl
**Depends on** : frontend-syntax-html5-semantic, frontend-a11y-aria-patterns, frontend-a11y-focus-keyboard-inert

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/impl/frontend-impl-popover-dialog-anchor/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §2 (Dialog and Popover), §5 (Anchor positioning), §12 (closedby, position-try-fallbacks, transition-behavior allow-discrete, @starting-style)
  - docs/research/topic-research/frontend-impl-popover-dialog-anchor-research.md
  - Phase 4 MUST drill @starting-style + transition-behavior per §14 verification gap #4

Scope (binding bullets) :
  - `<dialog>` element : `showModal()` (top-layer + inert backdrop + Escape + ::backdrop), `show()` (non-modal), `close(returnValue?)`; NEVER set `tabindex` on `<dialog>`; `autofocus` for initial focus; `closedby` attribute (`any | closerequest | none`)
  - Popover API (Baseline 2025) : `popover="auto|manual|hint"`; `auto` is mutually exclusive in top-layer stack with light dismiss; `manual` MUST be closed via script or `popovertargetaction="hide"`; trigger via `<button popovertarget="id" popovertargetaction="show|hide|toggle">`
  - Popovers are ALWAYS non-modal; for modal use `<dialog>` + `showModal()`
  - Anchor positioning : `anchor-name: --anchor` on source; `position-anchor: --anchor` + `position-area: bottom center` on tethered; `anchor(<side>)` function in `top`/`bottom`/`inset-*`; `position-try-fallbacks: flip-block, flip-inline`
  - Discrete-property transitions : `transition-behavior: allow-discrete` + `@starting-style { ... }` enables enter/exit animations for `display`, `content-visibility` — REQUIRED for popover/dialog open/close animations
  - Combined popover + anchor + dialog : recommended pattern for tooltips, dropdowns, command palettes
  - Gate Baseline 2025 features with `@supports`

Out-of-scope (binding) :
  - NO modal-system component template (deferred to `[[frontend-component-modal-toast-system]]`)
  - NO ARIA roles deep-dive (deferred to `[[frontend-a11y-aria-patterns]]`)
  - NO focus mechanics deep-dive (deferred to `[[frontend-a11y-focus-keyboard-inert]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : dialog, showModal, popover, popovertarget, popovertargetaction, closedby, anchor-name, position-anchor, position-area, position-try-fallbacks, transition-behavior, allow-discrete, @starting-style, ::backdrop, top layer, inert
  - Symptom-based : popover not closing, dialog focus broken, tooltip wrong position, anchor positioning not supported, dialog animation snaps, popover stays after click outside
  - Plain-language : how do I make a modal in HTML, popover API, anchor positioning CSS, animated dialog open close, accessible tooltip, light dismiss popover

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog
  - https://developer.mozilla.org/en-US/docs/Web/API/Popover_API
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Modal or popover?"
    Branch : interrupts user workflow, requires action -> `<dialog>` + `showModal()`
    Branch : transient surface, dismissible by clicking outside -> popover with `popover="auto"`
    Branch : programmatic close only (settings menu staying open) -> popover with `popover="manual"`
  - Question : "How to position relative to trigger?"
    Branch : modern browser -> `anchor-name` + `position-anchor` + `position-area`
    Branch : pre-2025 fallback -> `@supports (anchor-name: --x)` gate with JS-positioning fallback
  - Question : "Animate enter/exit?"
    Branch : popover/dialog with `display: none` to `display: block` -> use `transition-behavior: allow-discrete` + `@starting-style`
    Branch : opacity-only -> regular transition works

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `tabindex` on `<dialog>` -> MDN forbids; rely on `autofocus`
  - Anti-pattern 2 : popover + `showModal()` combined -> popovers are non-modal; pick one model
  - Anti-pattern 3 : custom click-outside JS for popover -> use `popover="auto"` (built-in light dismiss)
  - Anti-pattern 4 : anchor positioning without @supports fallback -> pre-2025 browsers render misaligned
  - Anti-pattern 5 : missing `@starting-style` for popover open animation -> property switch happens before transition starts; no animation
  - Anti-pattern 6 : `position: fixed` tooltip with manual `getBoundingClientRect` -> use anchor positioning

Renderable HTML fragment required in references/examples.md : YES — three demos : (a) accessible modal with `<dialog>` + `showModal()` + `closedby`, (b) popover with anchor positioning + `position-try-fallbacks`, (c) animated popover open/close with `@starting-style` + `transition-behavior: allow-discrete`.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-html5-semantic]]`, `[[frontend-a11y-aria-patterns]]`, `[[frontend-a11y-focus-keyboard-inert]]`, `[[frontend-component-modal-toast-system]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-impl-popover-dialog-anchor
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-impl-view-transitions-scroll-animations

**Batch** : 10
**Category** : impl
**Depends on** : frontend-syntax-css-color-modern, frontend-perf-animation-gpu-containment, frontend-a11y-motion-contrast-wcag22

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/impl/frontend-impl-view-transitions-scroll-animations/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §5 (View Transitions; Scroll-driven animations) and §9 (Scroll effects)
  - docs/research/topic-research/frontend-impl-view-transitions-scroll-animations-research.md

Scope (binding bullets) :
  - View Transitions API same-document : `document.startViewTransition(callback?)` returns `ViewTransition` with `.ready`, `.finished`, `.updateCallbackDone` promises
  - Pseudo-element tree : `::view-transition` -> `::view-transition-group(name)` -> `::view-transition-image-pair(name)` -> `::view-transition-old(name)` / `::view-transition-new(name)`
  - `view-transition-name` CSS property opts elements into individually-animated groups
  - Cross-document : `@view-transition { navigation: auto; }` declared on BOTH source and destination
  - Scroll-driven animations : `animation-timeline: scroll(<axis>? <scroller>?)` (axis: block / inline / x / y; scroller: nearest / root / self)
  - `view(<axis>? <visibility>? [with inset(<length>)]?)` for element-entering-viewport animations
  - Named timelines via `scroll-timeline: --name <axis>` and `view-timeline: --name <axis>`; `timeline-scope: --name` for distant ancestors
  - Scroll-snap : `scroll-snap-type` axis + strictness, `scroll-snap-align`, `scroll-padding`, `scroll-snap-stop: always`
  - NEVER `background-attachment: fixed` for parallax (compositor disaster on mobile) — use scroll-driven animations instead
  - ALL transitions MUST gate on `@media (prefers-reduced-motion: reduce)` and skip / replace with opacity-only

Out-of-scope (binding) :
  - NO micro-interaction easing (deferred to `[[frontend-visual-micro-interactions]]`)
  - NO compositor-only animation rules (deferred to `[[frontend-perf-animation-gpu-containment]]`)
  - NO scroll-position JavaScript libraries

Required references per SKILL.md frontmatter Keywords :
  - Technical : View Transitions, startViewTransition, view-transition-name, @view-transition, navigation auto, scroll-driven animations, animation-timeline, scroll(), view(), scroll-timeline, view-timeline, timeline-scope, scroll-snap-type, scroll-snap-align
  - Symptom-based : page transition not animating, view transition only some elements, parallax stutter mobile, scroll-snap not snapping, cross-document transition silent
  - Plain-language : how do I animate page transitions, view transitions API, scroll-driven animation CSS, parallax without JavaScript, scroll snap CSS

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_scroll-driven_animations
  - https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-snap-type

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Same-document or cross-document transition?"
    Branch : SPA-style DOM swap -> `document.startViewTransition(() => updateDOM())`
    Branch : MPA across pages -> `@view-transition { navigation: auto; }` on BOTH source and dest
  - Question : "Scroll-driven progress?"
    Branch : whole-page scroll -> `animation-timeline: scroll(block root)`
    Branch : element entering viewport -> `animation-timeline: view()`
    Branch : named timeline shared across distant elements -> `scroll-timeline` + `timeline-scope`
  - Question : "Reduced motion?"
    Branch : ALWAYS gate; skip View Transitions via `transition.skipTransition()` OR replace with opacity-only inside `@media (prefers-reduced-motion: reduce)`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `background-attachment: fixed` for parallax -> mobile catastrophe; use scroll-driven animation with `transform: translateY(...)` keyframes on `view()` timeline
  - Anti-pattern 2 : `@view-transition` declared only on source -> cross-document transition silently no-ops; declare on both
  - Anti-pattern 3 : `startViewTransition` without `prefers-reduced-motion` gate -> motion-sensitive users; skip transition
  - Anti-pattern 4 : `view-transition-name` repeated across siblings -> spec collision; names MUST be unique per snapshot
  - Anti-pattern 5 : scroll-snap-type without scroll-snap-align on children -> nothing snaps; children opt in
  - Anti-pattern 6 : scroll-driven animation without `@supports (animation-timeline: scroll())` gate -> Limited Availability; falls back ungracefully

Renderable HTML fragment required in references/examples.md : YES — (a) same-document list-to-detail view transition with named transition, (b) scroll-driven fade-in for cards using `view()` timeline, (c) horizontal scroll-snap carousel. All with `prefers-reduced-motion` reduce variants.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-visual-micro-interactions]]`, `[[frontend-perf-animation-gpu-containment]]`, `[[frontend-a11y-motion-contrast-wcag22]]`, `[[frontend-impl-popover-dialog-anchor]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-impl-view-transitions-scroll-animations
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-impl-web-components

**Batch** : 10
**Category** : impl
**Depends on** : frontend-syntax-js-es2024-ts-dom, frontend-syntax-html5-semantic, frontend-syntax-html5-form

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/impl/frontend-impl-web-components/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §2 (Declarative Shadow DOM), §5 (Web Components — custom elements, ElementInternals, scoped registries), §12 (Form-Associated Custom Elements, Scoped Custom Element Registries newly discovered)
  - docs/research/topic-research/frontend-impl-web-components-research.md

Scope (binding bullets) :
  - `customElements.define(name, ctor, { extends?: 'tag' })` ; lifecycle : `connectedCallback`, `disconnectedCallback`, `adoptedCallback`, `attributeChangedCallback(name, oldValue, newValue)` paired with `static get observedAttributes()`
  - Shadow DOM via `this.attachShadow({ mode: 'open' | 'closed', delegatesFocus?, slotAssignment?: 'named' | 'manual' })`
  - Slots : `<slot name="x">` for named distribution, `Element.assignedSlot`, `slotchange` event
  - Form-associated custom elements : `static formAssociated = true`; `this.attachInternals()` returns `ElementInternals` with `.setFormValue()`, `.setValidity()`, `.checkValidity()`, `.reportValidity()`
  - Declarative Shadow DOM : `<template shadowrootmode="open|closed">` (Baseline 2024); SSR-friendly, no JS dependency for first paint
  - Pseudo-classes : `:defined`, `:host`, `:host()`, `:host-context()`, `:state(--name)`
  - Pseudo-elements : `::slotted(...)`, `::part(...)`
  - Scoped Custom Element Registries : `new CustomElementRegistry()`, `attachShadow({ customElementRegistry })` to avoid global name collisions
  - When NOT to use web components : when native HTML element exists; when framework-agnostic distribution not needed
  - Naming : kebab-case with hyphen (`my-card`)

Out-of-scope (binding) :
  - NO framework integration (React / Vue / Solid wrappers out of scope)
  - NO Lit / Stencil / framework-build patterns
  - NO TypeScript decorators (use plain JS or TS without decorators)

Required references per SKILL.md frontmatter Keywords :
  - Technical : customElements.define, observedAttributes, attributeChangedCallback, connectedCallback, attachShadow, shadow DOM, slot, slotchange, ElementInternals, attachInternals, formAssociated, setFormValue, setValidity, shadowrootmode, declarative shadow DOM, scoped registry, :host, ::slotted, ::part
  - Symptom-based : custom element not upgrading, slot not rendering, attribute change not detected, shadow DOM styles leaking, form not seeing custom input
  - Plain-language : how do I make a web component, custom element example, shadow DOM tutorial, form-associated custom element, declarative shadow DOM

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/API/Web_components
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Should this be a web component?"
    Branch : native element exists (`<details>`, `<dialog>`, `<input>`) -> NEVER reinvent
    Branch : need to ship UI across frameworks / no framework -> yes
    Branch : framework-internal abstraction -> use framework component
  - Question : "Shadow DOM open or closed?"
    Branch : style isolation + external querying allowed -> `open`
    Branch : strict encapsulation (rare, e.g. embedded payment widget) -> `closed`
  - Question : "Form-associated?"
    Branch : custom input participating in `<form>` -> `static formAssociated = true` + `attachInternals()`
    Branch : pure UI widget -> not needed

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : missing `observedAttributes` static getter -> `attributeChangedCallback` never fires
  - Anti-pattern 2 : DOM work in constructor -> element not yet connected; use `connectedCallback`
  - Anti-pattern 3 : single-word custom element name (`mycard`) -> spec requires hyphen; use `my-card`
  - Anti-pattern 4 : reading attributes in constructor -> not parsed yet; read in `connectedCallback`
  - Anti-pattern 5 : `innerHTML` in shadow root without sanitization for user content -> XSS surface; sanitize
  - Anti-pattern 6 : form custom element without `formAssociated` -> form submit ignores the value

Renderable HTML fragment required in references/examples.md : YES — one HTML file with a `<my-card>` custom element using declarative shadow DOM `<template shadowrootmode="open">`, slotted content, attribute reactivity, and a form-associated `<my-rating>` element with ElementInternals.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-html5-semantic]]`, `[[frontend-syntax-html5-form]]`, `[[frontend-syntax-js-es2024-ts-dom]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-impl-web-components
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-component-modal-toast-system

**Batch** : 10
**Category** : component-patterns
**Depends on** : frontend-impl-popover-dialog-anchor, frontend-a11y-aria-patterns, frontend-a11y-focus-keyboard-inert

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/component-patterns/frontend-component-modal-toast-system/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §2 (Dialog and Popover), §6 (Dialog Modal APG pattern), §13 (toast-notifications folded in per D-R05)
  - docs/research/topic-research/frontend-component-modal-toast-system-research.md

Scope (binding bullets) :
  - Modal pattern : `<dialog>` + `showModal()` + focus trap (Tab/Shift+Tab cycle within) + Escape to close + restore focus on close
  - Required ARIA : `role="dialog"`, `aria-modal="true"`, `aria-labelledby` (or `aria-label`)
  - Initial focus : `autofocus` on primary action OR first useful interactive
  - Scroll lock : `<dialog>` + `showModal()` automatically sets `<html>` to inert; no manual scroll-lock JS needed
  - Toast pattern : popover-based (`popover="manual"`) OR aria-live region; `aria-live="polite"` for non-urgent (form save), `"assertive"` for urgent (error)
  - Toast queue management : max N visible, auto-dismiss after 4-7 seconds, pause on hover/focus
  - Toast positioning via Anchor Positioning or fixed bottom-right
  - Confirm dialog pattern : `<dialog>` returning value via `close(value)` and reading via `dialog.returnValue`
  - Animated open/close via `transition-behavior: allow-discrete` + `@starting-style`

Out-of-scope (binding) :
  - NO popover / dialog API mechanics deep-dive (deferred to `[[frontend-impl-popover-dialog-anchor]]`)
  - NO ARIA roles deep-dive (deferred to `[[frontend-a11y-aria-patterns]]`)
  - NO custom focus trap library (use native `showModal()` instead)

Required references per SKILL.md frontmatter Keywords :
  - Technical : dialog, showModal, aria-modal, aria-labelledby, role=dialog, aria-live, polite, assertive, popover manual, toast, focus trap, scroll lock, returnValue, autofocus
  - Symptom-based : modal does not trap focus, body scrolls behind modal, toast not announced, focus lost after close, multiple toasts overlap, modal animation snaps
  - Plain-language : how do I make an accessible modal, toast notification HTML, confirm dialog pattern, accessible alert, scroll lock modal

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog
  - https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/
  - https://developer.mozilla.org/en-US/docs/Web/API/Popover_API

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Modal or non-modal surface?"
    Branch : interrupts user, requires response -> modal (`<dialog>` + `showModal()`)
    Branch : non-blocking notification -> toast (popover or aria-live region)
    Branch : confirm action -> modal with returnValue pattern
  - Question : "Toast urgency?"
    Branch : status / success -> `aria-live="polite"`
    Branch : error / warning -> `aria-live="assertive"` OR `role="alert"`
  - Question : "Toast dismiss behavior?"
    Branch : auto-dismiss with pause-on-hover -> use `setTimeout` cleared by pointerenter, restarted by pointerleave
    Branch : manual dismiss only -> popover="manual" with close button

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : custom focus trap JS for modal -> use `<dialog>` + `showModal()` (native inert + Escape + focus trap)
  - Anti-pattern 2 : `body { overflow: hidden }` JS scroll-lock for modal -> `showModal()` already inerts background
  - Anti-pattern 3 : missing focus restoration on close -> capture trigger before opening, restore on close
  - Anti-pattern 4 : `role="alert"` polled via setInterval -> alert is for live changes; use `aria-live="polite"` region for periodic
  - Anti-pattern 5 : toast popover without `popover="manual"` -> auto popovers are mutually exclusive in top-layer stack; one toast hides another
  - Anti-pattern 6 : `aria-live` region created after the message -> screen reader does not announce; region MUST exist in DOM before content insertion

Renderable HTML fragment required in references/examples.md : YES — full demo : (a) confirm modal with `<dialog>` + `showModal()` + return value, (b) toast queue using popover="manual" + aria-live region, (c) animated open/close with `@starting-style`. All keyboard-navigable.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-impl-popover-dialog-anchor]]`, `[[frontend-a11y-aria-patterns]]`, `[[frontend-a11y-focus-keyboard-inert]]`, `[[frontend-visual-micro-interactions]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-component-modal-toast-system
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-errors-cascade-conflicts

**Batch** : 11
**Category** : errors
**Depends on** : frontend-syntax-css-cascade-layers-scope, frontend-syntax-css-nesting-logical-properties

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/errors/frontend-errors-cascade-conflicts/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Cascade Layers — important inversion) and §10 (Anti-patterns 1, 2)
  - docs/research/topic-research/frontend-errors-cascade-conflicts-research.md

Scope (binding bullets) :
  - Specificity calculation : inline > IDs > classes/attrs/pseudo-classes > elements/pseudo-elements
  - Cascade order full chain : importance (UA > user > author) -> origin -> layers -> specificity -> source order
  - Critical inversion : unlayered author CSS beats layered author CSS for NORMAL declarations
  - `!important` reversal : in important origin, earlier-declared layers win, important-author-in-layer beats important-author-unlayered
  - `@scope` proximity-cascade : closest scope root wins; overrides source order (NOT importance or layer)
  - `:where()` to nullify specificity; `:is()` adopts highest specificity of inner
  - Debugging cascade : Chrome DevTools Computed tab + Active Inactive rules; `Cascade Layers` indicator

Out-of-scope (binding) :
  - NO @layer / @scope syntax (deferred to `[[frontend-syntax-css-cascade-layers-scope]]`)
  - NO :has() perf (deferred to `[[frontend-syntax-css-has-selector]]`)
  - NO performance of selectors

Required references per SKILL.md frontmatter Keywords :
  - Technical : specificity, cascade, @layer, @scope, !important, :where, :is, importance, origin, source order, layered, unlayered
  - Symptom-based : my CSS does not override, !important everywhere, specificity war, layer not winning, rule not applying, computed style wrong
  - Plain-language : how do I fix CSS overriding, specificity bug, why is my CSS ignored, when to use !important, debug CSS cascade

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@layer
  - https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Why is my rule not winning?"
    Branch : check origin (UA / user / author) -> any rule from higher origin wins
    Branch : check importance (`!important`) -> important reverses normal cascade order
    Branch : check layers : unlayered beats layered (normal); reversed for important
    Branch : check specificity -> higher wins
    Branch : check source order -> later wins at equal specificity
    Branch : check `@scope` proximity -> closest scope wins (overrides source order)
  - Question : "How do I lower specificity?"
    Branch : wrap selector in `:where(...)` -> zero specificity
    Branch : avoid IDs -> use classes
    Branch : use `@layer` to make rule order win regardless of specificity
  - Question : "When is `!important` acceptable?"
    Branch : utility-class layer (intentional override of components) -> acceptable
    Branch : everywhere else -> NEVER; root cause is missing layer discipline

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : mixing unlayered and layered CSS expecting source order -> unlayered always wins normal; move ALL author CSS into layers
  - Anti-pattern 2 : `!important` chain to win specificity battle -> root cause is missing layer; refactor
  - Anti-pattern 3 : assuming `!important` follows same layer order as normal -> reversed; document in code comment
  - Anti-pattern 4 : using `:where(:not(.exclude)) .selector` and surprised by zero specificity -> `:where` zeros the whole nested selector
  - Anti-pattern 5 : `:is(.a, .b#id)` -> takes highest inner specificity (the ID); use `:where` if zero specificity desired
  - Anti-pattern 6 : `@scope` rule expected to be defeated by deeper-DOM normal rule -> proximity overrides source order; restructure

Renderable HTML fragment required in references/examples.md : YES — debug-cascade example file demonstrating each rule (layered vs unlayered, important inversion, :where vs :is, @scope proximity) with comments explaining the expected winner.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-cascade-layers-scope]]`, `[[frontend-syntax-css-nesting-logical-properties]]`, `[[frontend-syntax-css-has-selector]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-errors-cascade-conflicts
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-errors-layout-pitfalls

**Batch** : 11
**Category** : errors
**Depends on** : frontend-syntax-css-grid-subgrid, frontend-syntax-css-container-queries, frontend-impl-responsive-layout-fluid

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/errors/frontend-errors-layout-pitfalls/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §3 (Subgrid limitation), §10 (Anti-pattern 4 container query unit fallback, anti-pattern 14 100vh)
  - docs/research/topic-research/frontend-errors-layout-pitfalls-research.md

Scope (binding bullets) :
  - Flexbox `min-width: 0` reset needed on children to allow shrinking below content size
  - Grid `1fr` track caveat : `1fr` equals `minmax(auto, 1fr)`; can refuse to shrink below content; use `minmax(0, 1fr)` for forceful equal split
  - Intrinsic vs extrinsic sizing : `min-content`, `max-content`, `fit-content()` ; when each applies
  - Overflow surprises : default `overflow: visible` allows children to escape; long words break layout (use `overflow-wrap: anywhere` or `word-break: break-word`)
  - Subgrid implicit-tracks limitation : CANNOT generate implicit rows on subgrid axis; declare subgrid only on column axis
  - Container query unit fallback : `cqi` / `cqb` fall back to small-viewport units when no matching container ancestor
  - `100vh` mobile bug : excludes dynamic toolbar; use `dvh`, `svh`, `lvh` as needed
  - `position: sticky` not sticking : ancestor has `overflow: hidden|auto|scroll`, or sticky element has no defined inset
  - z-index stacking-context surprises : stacking contexts created by `transform`, `opacity < 1`, `filter`, `will-change`, `position: fixed`, `position: sticky`

Out-of-scope (binding) :
  - NO @container syntax (deferred to `[[frontend-syntax-css-container-queries]]`)
  - NO subgrid syntax (deferred to `[[frontend-syntax-css-grid-subgrid]]`)
  - NO animation jank (deferred to `[[frontend-errors-animation-jank]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : min-width 0, minmax(0 1fr), min-content, max-content, fit-content, overflow-wrap, word-break, dvh, svh, lvh, position sticky, stacking context, z-index, subgrid implicit
  - Symptom-based : flex item overflows, grid columns uneven, sticky not sticking, layout broken on mobile, long word breaks layout, z-index not working, viewport 100vh wrong
  - Plain-language : flexbox overflow bug, why is my sticky element not sticking, grid 1fr not equal, mobile viewport bug, long URL breaks layout

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Subgrid
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries
  - https://developer.mozilla.org/en-US/docs/Web/CSS/position

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Flex/grid child overflows. Why?"
    Branch : flex item -> `min-width: 0` on the child
    Branch : grid 1fr column not splitting -> use `minmax(0, 1fr)`
    Branch : long word -> `overflow-wrap: anywhere` on the container
  - Question : "Sticky not sticking?"
    Branch : check ancestor `overflow` (must be `visible`); check inset (must be defined); check parent height
    Branch : header sticky over scroll-snap -> may interfere; test
  - Question : "Mobile viewport unit?"
    Branch : full-viewport always-fill (no toolbar excluded) -> `dvh`
    Branch : with toolbar visible (smallest) -> `svh`
    Branch : without toolbar (largest) -> `lvh`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `100vh` for hero on mobile -> includes browser chrome; use `100dvh`
  - Anti-pattern 2 : `flex: 1` child overflowing -> add `min-width: 0`
  - Anti-pattern 3 : `grid-template-columns: 1fr 1fr 1fr` columns uneven -> change to `minmax(0, 1fr)`
  - Anti-pattern 4 : `position: sticky` inside `overflow: hidden` parent -> never sticks; remove the overflow or restructure
  - Anti-pattern 5 : assuming z-index works across stacking contexts -> elements in different stacking contexts cannot be reordered; raise the parent context's z-index
  - Anti-pattern 6 : subgrid on row axis with implicit rows needed -> spec limitation; switch to column-only subgrid

Renderable HTML fragment required in references/examples.md : YES — before/after debugging examples for : (a) flex overflow with min-width 0 fix, (b) sticky in overflow:hidden, (c) 100vh vs 100dvh demo.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-syntax-css-grid-subgrid]]`, `[[frontend-syntax-css-container-queries]]`, `[[frontend-impl-responsive-layout-fluid]]`, `[[frontend-errors-units-rendering-viewport]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-errors-layout-pitfalls
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-errors-units-rendering-viewport

**Batch** : 11
**Category** : errors
**Depends on** : frontend-core-architecture, frontend-impl-responsive-layout-fluid

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/errors/frontend-errors-units-rendering-viewport/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §10 (Anti-pattern 14 100vh mobile bug), §12 (dvh/svh/lvh newly discovered)
  - docs/research/topic-research/frontend-errors-units-rendering-viewport-research.md
  - Phase 4 MUST drill dvh/svh/lvh per §14 verification gap #2

Scope (binding bullets) :
  - Length units overview : absolute (px, pt, pc, in, cm, mm, Q) ; font-relative (em, rem, ex, ch, ic, lh, rlh) ; viewport-percentage (vw, vh, vmin, vmax, vi, vb)
  - Dynamic viewport units : `dvh`, `dvw`, `dvmin`, `dvmax`, `dvi`, `dvb` — adapt to UA chrome show/hide
  - Small viewport units : `svh`, `svw` etc. — assume max chrome visible
  - Large viewport units : `lvh`, `lvw` etc. — assume chrome hidden
  - Container query units (covered briefly; deferred to `[[frontend-syntax-css-container-queries]]`)
  - em vs rem : em compounds in nested rules; rem references root font-size
  - Retina-vs-css pixels : `devicePixelRatio`; CSS pixels are the authoring unit; image asset planning uses both
  - Subpixel rendering : 0.5px borders fall on subpixels; may render uneven; use `.5px` only when intentional
  - Mobile viewport configuration : `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">` for proper safe-area handling

Out-of-scope (binding) :
  - NO container queries syntax (deferred to `[[frontend-syntax-css-container-queries]]`)
  - NO clamp typography (deferred to `[[frontend-impl-typography-system]]`)
  - NO animation jank (deferred to `[[frontend-errors-animation-jank]]`)

Required references per SKILL.md frontmatter Keywords :
  - Technical : px, em, rem, vw, vh, dvh, svh, lvh, vmin, vmax, vi, vb, devicePixelRatio, subpixel, meta viewport, safe-area-inset
  - Symptom-based : 100vh wrong on mobile, em compound, font too big nested, hero cut off mobile, browser chrome covers content, retina image blurry
  - Plain-language : what viewport units to use mobile, em vs rem, what is dvh, fix 100vh mobile, safe area iPhone notch, retina images

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://developer.mozilla.org/en-US/docs/Web/CSS/length
  - https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_values_and_units/Numeric_data_types

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Which viewport unit?"
    Branch : full-viewport adaptive (most common) -> `dvh` / `dvw`
    Branch : guaranteed-to-fit area always visible -> `svh`
    Branch : full extent without chrome -> `lvh`
    Branch : legacy support needed -> `vh` (acknowledge mobile bug; consider fallback)
  - Question : "em or rem for spacing?"
    Branch : space relative to current font-size (button padding scales with button text) -> em
    Branch : consistent space across nesting -> rem
    Branch : authoring base font -> declare on `:root` (default 16px in user-agent)
  - Question : "Safe area inset (iPhone notch)?"
    Branch : `<meta viewport viewport-fit=cover>` + `padding: env(safe-area-inset-top)` etc.

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `min-height: 100vh` on mobile hero -> chrome covers; use `100dvh`
  - Anti-pattern 2 : `font-size: 1em` nested inside `1em` -> compounds (1.5em*1.5em = 2.25em final); use rem for predictable
  - Anti-pattern 3 : `width: 100vw` -> includes scrollbar on desktop -> use `100%` or `100dvw`
  - Anti-pattern 4 : missing `<meta viewport viewport-fit=cover>` for safe-area -> safe-area-inset env vars return 0
  - Anti-pattern 5 : `0.5px` border for "thin" -> renders inconsistently across DPRs; use `1px` + `transform: scale(0.5)` if absolutely needed
  - Anti-pattern 6 : assuming `1in = 96px` always -> true in CSS, but physical inch varies on touch displays; never authoring measure

Renderable HTML fragment required in references/examples.md : YES — viewport unit comparison page : 100vh vs 100dvh vs 100svh stacked, with safe-area inset padding on iOS-style notch demo.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-impl-responsive-layout-fluid]]`, `[[frontend-impl-typography-system]]`, `[[frontend-errors-layout-pitfalls]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-errors-units-rendering-viewport
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


### Skill : frontend-component-data-tables-command-palette

**Batch** : 12
**Category** : component-patterns
**Depends on** : frontend-a11y-aria-patterns, frontend-a11y-focus-keyboard-inert, frontend-impl-popover-dialog-anchor, frontend-syntax-css-grid-subgrid

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/component-patterns/frontend-component-data-tables-command-palette/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §6 (APG Combobox, Tabs patterns) and §13 (data-tables + command-palette kept)
  - docs/research/topic-research/frontend-component-data-tables-command-palette-research.md

Scope (binding bullets) :
  - Data table : semantic `<table>` with `<thead>`, `<tbody>`, `<th scope="col|row">`, `<caption>`
  - Sticky header via `position: sticky; top: 0` inside a scrollable parent
  - Sortable columns : `<button>` inside `<th>` with `aria-sort="ascending|descending|none"`
  - Mobile reflow : `<table>` does not reflow naturally; use `display: block` overflow-x for horizontal scroll, OR fully restructure as definition-list cards via container queries
  - Selection : `<input type="checkbox">` per row, with `aria-label` describing the row; header checkbox for select-all (indeterminate state)
  - Command palette : `Cmd+K` / `Ctrl+K` shortcut, `<dialog>` + `showModal()` for focus trap, combobox + listbox pattern from APG with `aria-activedescendant`
  - Command palette keyboard model : Esc to close, Up/Down to navigate options, Enter to execute, typing filters
  - Command palette ALWAYS provides plain-text equivalent (no keyboard-only access)

Out-of-scope (binding) :
  - NO ARIA combobox deep-dive (deferred to `[[frontend-a11y-aria-patterns]]`)
  - NO popover / dialog mechanics (deferred to `[[frontend-impl-popover-dialog-anchor]]`)
  - NO virtualization (deferred to performance considerations; mention briefly)

Required references per SKILL.md frontmatter Keywords :
  - Technical : table, thead, tbody, th scope, caption, aria-sort, sticky header, command palette, Cmd+K, combobox, listbox, aria-activedescendant, indeterminate, dialog showModal
  - Symptom-based : table not accessible, screen reader does not announce sort, mobile table overflow, command palette focus broken, table sort breaks keyboard, header not sticky
  - Plain-language : how do I make an accessible data table, sortable table HTML, command palette pattern, Cmd K dialog, table on mobile, accessible table header

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://www.w3.org/WAI/ARIA/apg/patterns/combobox/
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/table
  - https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Mobile data table strategy?"
    Branch : preserve all columns -> `display: block; overflow-x: auto;` on `<table>` (horizontal scroll)
    Branch : restructure as cards on small screens -> container query switching from `display: table` to `display: grid` with row-as-card
  - Question : "Sortable column?"
    Branch : button inside `<th>` -> `<th><button aria-sort="...">Name</button></th>`; reorder rows on click
  - Question : "Command palette open?"
    Branch : `<dialog>` + `showModal()` for focus trap + Escape; combobox with `aria-activedescendant` for option highlight
    Branch : popover instead of dialog -> only if non-blocking (e.g., contextual quick-actions)

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `<div role="table">` for tabular data -> use native `<table>` for free a11y + semantics
  - Anti-pattern 2 : sortable button as `<div>` -> use `<button>` for keyboard + focus
  - Anti-pattern 3 : command palette using DOM focus on options -> use `aria-activedescendant` per APG combobox
  - Anti-pattern 4 : Cmd+K with no plain UI fallback -> keyboard-only access violates WCAG 2.1.1; provide a visible trigger button too
  - Anti-pattern 5 : sticky `<thead>` without scrollable parent -> never sticks; ensure parent has overflow-auto + max-height
  - Anti-pattern 6 : missing `<caption>` on data table -> screen-reader users lack context; add caption or `aria-label`

Renderable HTML fragment required in references/examples.md : YES — (a) accessible sortable data table with sticky header and mobile container-query reflow, (b) Cmd+K command palette using `<dialog>` + combobox with `aria-activedescendant`.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-a11y-aria-patterns]]`, `[[frontend-a11y-focus-keyboard-inert]]`, `[[frontend-impl-popover-dialog-anchor]]`, `[[frontend-syntax-css-grid-subgrid]]`, `[[frontend-impl-responsive-layout-fluid]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-component-data-tables-command-palette
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-agents-design-system-validator

**Batch** : 12
**Category** : agents
**Depends on** : ALL prior batches (validates against entire pkg)

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/agents/frontend-agents-design-system-validator/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §8 (Design Tokens — three-tier chain) and §13 (design-system-validator agent kept)
  - docs/research/topic-research/frontend-agents-design-system-validator-research.md

Scope (binding bullets) :
  - Validation rules (deterministic) :
    1. Verify component uses tokens (custom properties) NOT hardcoded color / spacing / font-size / radius values
    2. Verify three-tier token chain : component tokens reference semantic, semantic reference primitive
    3. Verify no orphan tokens : every defined token is referenced; every reference resolves
    4. Verify token naming convention : kebab-case, namespaced prefix (`--color-`, `--space-`, `--font-`, `--radius-`, `--shadow-`)
    5. Verify `@layer tokens, theme, base, components, utilities;` ordering present at project root CSS
    6. Verify `@property` registered for all animatable tokens
    7. Verify no `!important` outside utilities layer
  - Audit methodology : grep for `#[0-9a-f]{3,8}`, `rgb(`, `oklch(`, `hsl(` in CSS files NOT inside `@layer tokens` block — flag each
  - Audit methodology : parse CSS via PostCSS to detect declarations bypassing tokens
  - Output : findings report (path : line : violation : suggested fix), severity (error / warning / info)
  - Cross-reference each finding to relevant skill (`[[frontend-impl-design-tokens]]` etc.)

Out-of-scope (binding) :
  - NO a11y / perf checks (deferred to `[[frontend-agents-a11y-perf-consistency-auditor]]`)
  - NO framework-specific token integration
  - NO color-contrast measurement (use a11y auditor)

Required references per SKILL.md frontmatter Keywords :
  - Technical : design tokens, validator, @layer tokens, @property, custom properties, three-tier chain, semantic token, component token, audit, lint
  - Symptom-based : hardcoded color sneaking in, design system drift, design tokens not enforced, brand color override, tokens not loaded
  - Plain-language : how do I audit my CSS for token compliance, design system validator, find hardcoded colors in CSS, design token linting

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://www.designtokens.org/tr/drafts/format/
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@property
  - https://developer.mozilla.org/en-US/docs/Web/CSS/@layer

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Found hardcoded color value. Action?"
    Branch : inside `@layer tokens` block defining a primitive -> ALLOWED
    Branch : anywhere else -> ERROR; replace with token reference
  - Question : "Token references missing target?"
    Branch : `var(--undefined-token)` -> ERROR; either define or remove reference
    Branch : `var(--undefined-token, fallback)` -> WARNING; explicit fallback in tier-3 only
  - Question : "Component using primitive directly?"
    Branch : `.button { background: var(--brand-blue-500); }` -> WARNING; introduce semantic token (`--color-action-primary`)
    Branch : `.button { background: var(--button-primary-bg); }` -> ALLOWED

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `.button { background: #3b82f6 }` -> tokenize as `--button-primary-bg`
  - Anti-pattern 2 : `--button-bg: #3b82f6` (single-tier) -> introduce `--color-action-primary` semantic layer between primitive and component
  - Anti-pattern 3 : `.button { background: var(--brand-blue-500) }` (component uses primitive directly) -> add semantic tier
  - Anti-pattern 4 : `--color-1`, `--color-2` non-semantic names -> use namespaced semantic names (`--color-bg-surface`)
  - Anti-pattern 5 : `!important` in components layer -> indicates missing layer discipline
  - Anti-pattern 6 : transitioned `--color-bg` without @property registration -> no interpolation

Renderable HTML fragment required in references/examples.md : NO — but include a sample audit report (JSON or markdown) showing : (a) finding format, (b) example violations from a deliberately bad sample, (c) suggested-fix for each.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-impl-design-tokens]]`, `[[frontend-theming-color-palette-oklch]]`, `[[frontend-syntax-css-cascade-layers-scope]]`, `[[frontend-agents-a11y-perf-consistency-auditor]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-agents-design-system-validator
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```

### Skill : frontend-agents-a11y-perf-consistency-auditor

**Batch** : 12
**Category** : agents
**Depends on** : ALL prior batches (validates a11y + perf + cross-skill consistency)

**Worker prompt** (copy verbatim to tmux worker) :

```
Workspace : /home/freek/GitHub/Frontend-Design-Claude-Skill-Package/
Output dir : skills/source/agents/frontend-agents-a11y-perf-consistency-auditor/

Files to create :
  - SKILL.md (max 500 lines, YAML folded scalar `>`, "Use when..." opener, Keywords line, license:MIT, compatibility, metadata)
  - references/methods.md
  - references/examples.md
  - references/anti-patterns.md

Research input :
  - docs/research/vooronderzoek-frontend.md, §6 (WCAG 2.2 + ARIA), §7 (Core Web Vitals), §13 (auditor + consistency merged per D-R19)
  - docs/research/topic-research/frontend-agents-a11y-perf-consistency-auditor-research.md

Scope (binding bullets) :
  - A11y checks (deterministic, codified) :
    1. Every interactive element has accessible name (aria-label, aria-labelledby, or visible text content)
    2. `:focus { outline: none }` MUST have matching `:focus-visible { outline: ... }` with 3:1 contrast
    3. Modal `<dialog>` has `aria-labelledby` and uses `showModal()` (focus trap + inert background)
    4. Live regions exist in DOM before content insertion (not created on-the-fly)
    5. Target size meets 24x24 CSS px (WCAG 2.5.8) OR matches one of the five exceptions
    6. Color-contrast meets 4.5:1 (normal text), 3:1 (large / non-text)
    7. ALL animations have a `prefers-reduced-motion: reduce` reduced variant
    8. NO `tabindex` positive values
    9. Semantic HTML preferred over ARIA roles (NEVER `<div role="button">`)
  - Perf checks (deterministic) :
    1. `<img>` has explicit width/height OR aspect-ratio
    2. LCP image has `fetchpriority="high"` or preload
    3. Below-fold images have `loading="lazy"`
    4. Animations target ONLY `transform`, `opacity`, `filter` (no width/height/top/left)
    5. `will-change` removed after interaction (not permanently applied)
    6. Webfont files <= 100 KB woff2 per face
    7. Critical CSS inline OR `<link rel="preload" as="style">`
  - Consistency checks (cross-skill) :
    1. SKILL.md YAML frontmatter shape (name, description folded scalar, license:MIT, compatibility, metadata)
    2. All four files exist : SKILL.md + 3 reference files
    3. SKILL.md < 500 lines
    4. English-only content
    5. Section headings use `:` not em-dash
    6. Cross-references use `[[skill-name]]` Markdown link format
  - Output : prioritized findings list (severity : error / warning / info) with file:line and fix suggestion

Out-of-scope (binding) :
  - NO design-token enforcement (deferred to `[[frontend-agents-design-system-validator]]`)
  - NO automated visual regression testing
  - NO browser-rendering of audited code (static analysis only)

Required references per SKILL.md frontmatter Keywords :
  - Technical : a11y audit, perf audit, WCAG 2.2, Core Web Vitals, LCP, INP, CLS, aria-label, accessible name, focus-visible, prefers-reduced-motion, fetchpriority, loading lazy, will-change, validator, lint, cross-skill consistency
  - Symptom-based : missing alt text, focus invisible, animation does not respect setting, image without dimensions, custom button not keyboard accessible, slow page, skill format inconsistent
  - Plain-language : how do I audit my site for accessibility, performance audit, WCAG checker, find missing alt text, validate skill format, cross-skill consistency check

Approved sources to cite (SOURCES.md URLs, MUST WebFetch verify) :
  - https://www.w3.org/TR/WCAG22/
  - https://www.w3.org/WAI/ARIA/apg/patterns/
  - https://web.dev/articles/vitals
  - https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible

Decision tree required (for SKILL.md Quick Reference) :
  - Question : "Audit category for this finding?"
    Branch : a11y rule (WCAG / ARIA / focus / contrast) -> A11Y
    Branch : perf rule (CWV / animation / asset budget) -> PERF
    Branch : skill / docs / consistency rule -> CONSISTENCY
  - Question : "Severity?"
    Branch : violates WCAG 2.2 AA OR blocks LCP/INP/CLS thresholds -> ERROR
    Branch : missed best practice but not standard violation -> WARNING
    Branch : style inconsistency -> INFO
  - Question : "Found `<div role="button">`?"
    Branch : ERROR (semantic-HTML-first principle); fix to native `<button>`

Anti-patterns required (for references/anti-patterns.md, minimum 5) :
  - Anti-pattern 1 : `<div role="button" tabindex="0">` -> ERROR; use `<button>`
  - Anti-pattern 2 : `:focus { outline: none }` no `:focus-visible` override -> ERROR; WCAG 2.4.7
  - Anti-pattern 3 : `transition: all` on layout properties -> WARNING; specify only transform/opacity/filter
  - Anti-pattern 4 : `<img>` without dimensions -> ERROR; CLS guaranteed
  - Anti-pattern 5 : Skill SKILL.md > 500 lines -> ERROR; refactor to references/
  - Anti-pattern 6 : quoted YAML description (not folded scalar) -> ERROR; rewrite with `>`

Renderable HTML fragment required in references/examples.md : NO — include a sample audit report instead (JSON + markdown) showing how findings are formatted for parent agent consumption.

Quality rules : English-only, deterministic, MIT, compatibility, `:` headings, no README, WebFetch-verified.
Cross-reference : `[[frontend-a11y-aria-patterns]]`, `[[frontend-a11y-focus-keyboard-inert]]`, `[[frontend-a11y-motion-contrast-wcag22]]`, `[[frontend-perf-core-web-vitals-inp]]`, `[[frontend-perf-animation-gpu-containment]]`, `[[frontend-agents-design-system-validator]]`.

Quality gate : all 5 validate-*.js exit 0.
Commit : feat(skill): frontend-agents-a11y-perf-consistency-auditor
Report : `tmo task done T-<id> --output "<commit-sha>"`.
```


## Phase 4 Topic-Research Strategy

Per BOOTSTRAP-RUNBOOK §6.3, each batch interleaves Phase 4 topic-research (in-process opus agents) BEFORE the tmux-workers receive their batch prompts. Skip-criteria : skip topic-research when vooronderzoek already covers >40 doc-pages-equivalent + claims are non-ambiguous + zero new WebFetch verification needed.

| Skill | Needs Phase 4? | Reason |
|-------|----------------|--------|
| frontend-core-architecture | NO (skip) | vooronderzoek §1 + §11 fully covers; no new WebFetch |
| frontend-core-web-standards-baseline | NO (skip) | vooronderzoek §1 + §11 + SOURCES.md cover Baseline taxonomy |
| frontend-core-design-philosophy | YES | Aesthetic claims need WebFetch to web.dev articles on modern design + Core Web Vitals citation |
| frontend-syntax-html5-semantic | NO (skip) | vooronderzoek §2 covers all elements with WebFetch citations |
| frontend-syntax-html5-form | YES | §14 gap : Open UI status of select/button customization needs WebFetch |
| frontend-syntax-css-cascade-layers-scope | NO (skip) | vooronderzoek §3 fully verified |
| frontend-syntax-css-container-queries | NO (skip) | vooronderzoek §3 + §12 cover including style queries |
| frontend-syntax-css-has-selector | NO (skip) | vooronderzoek §3 + §10 cover |
| frontend-syntax-css-color-modern | NO (skip) | vooronderzoek §3 verified oklch / color-mix / light-dark |
| frontend-syntax-css-grid-subgrid | NO (skip) | vooronderzoek §3 covers including limitation |
| frontend-syntax-css-nesting-logical-properties | NO (skip) | vooronderzoek §3 + §12 cover |
| frontend-syntax-js-es2024-ts-dom | YES | §14 gap : Iterator helpers (Iterator.prototype) + scheduler.yield need WebFetch |
| frontend-a11y-aria-patterns | YES | §14 gap #8 : drill APG patterns Carousel, Disclosure, Listbox, Menu, Radio Group, Tree, Treegrid |
| frontend-a11y-focus-keyboard-inert | YES | §14 gap #3 : `inert` attribute needs WebFetch |
| frontend-a11y-motion-contrast-wcag22 | YES | §14 gap #1 : APCA + WCAG 3 status; full WCAG 2.2 SC drill-down |
| frontend-perf-core-web-vitals-inp | YES | §14 gap #5 : scheduler.yield + §14 gap #10 : Speculation Rules eagerness |
| frontend-perf-animation-gpu-containment | NO (skip) | vooronderzoek §7 covers |
| frontend-errors-animation-jank | NO (skip) | vooronderzoek §7 + §10 cover |
| frontend-theming-color-palette-oklch | NO (skip) | vooronderzoek §3 + §8 cover |
| frontend-theming-dark-light-mode | NO (skip) | vooronderzoek §3 covers `light-dark` + `color-scheme` |
| frontend-visual-glassmorphism-backdrop | NO (skip) | vooronderzoek §9 + §10 cover backdrop-root pitfall |
| frontend-visual-gradients | NO (skip) | vooronderzoek §9 covers; @property already verified |
| frontend-visual-micro-interactions | NO (skip) | vooronderzoek §9 covers easing + reduced-motion |
| frontend-impl-design-tokens | NO (skip) | vooronderzoek §8 + DTCG draft already WebFetched |
| frontend-impl-responsive-layout-fluid | NO (skip) | vooronderzoek §3 (clamp) + container-queries cover |
| frontend-impl-typography-system | YES | §14 gap #9 : font-feature vs font-variation-settings ordering |
| frontend-impl-popover-dialog-anchor | YES | §14 gap #4 : `@starting-style` + `transition-behavior: allow-discrete` details |
| frontend-impl-view-transitions-scroll-animations | NO (skip) | vooronderzoek §5 covers including cross-document |
| frontend-impl-web-components | NO (skip) | vooronderzoek §5 + §12 cover form-associated + scoped registries |
| frontend-component-modal-toast-system | NO (skip) | covered by `[[frontend-impl-popover-dialog-anchor]]` topic-research |
| frontend-errors-cascade-conflicts | NO (skip) | vooronderzoek §3 + §10 cover |
| frontend-errors-layout-pitfalls | NO (skip) | vooronderzoek §3 + §10 cover |
| frontend-errors-units-rendering-viewport | YES | §14 gap #2 : dvh / svh / lvh need WebFetch |
| frontend-component-data-tables-command-palette | NO (skip) | APG Combobox already WebFetched §6 |
| frontend-agents-design-system-validator | NO (skip) | builds on already-verified token spec |
| frontend-agents-a11y-perf-consistency-auditor | NO (skip) | aggregates already-verified a11y + perf material |

Total skills requiring Phase 4 topic-research : 11 / 36 (31%). Each Phase 4 agent runs in parallel with two siblings inside its batch; orchestrator waits for all three before sending to tmux-workers.

## Quality Gates

Per BOOTSTRAP-RUNBOOK §6.5 and AGENT-SKILLS-STANDARD.md, every batch runs the following validators after worker completion. Each validator MUST exit 0 ; failure triggers root-cause fix in the skill (NEVER bypass).

| Validator | Path | Exit-0 criterion |
|-----------|------|------------------|
| validate-frontmatter.js | `/home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-frontmatter.js` | YAML frontmatter present, folded scalar `>` for description, "Use when..." opener, Keywords line, license:MIT, compatibility quoted, metadata.author=OpenAEC-Foundation, metadata.version semver |
| validate-line-count.js | `/home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-line-count.js` | SKILL.md < 500 lines |
| validate-structure.js | `/home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-structure.js` | 3 reference files exist (methods.md, examples.md, anti-patterns.md); NO README.md inside skill folder |
| validate-language.js | `/home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-language.js` | English-only content; deterministic language (no "you might consider", no hedging) |
| validate-emdash.js | `/home/freek/GitHub/Skill-Package-Workflow-Template/scripts/validate-emdash.js` | Section headings use `:` not em-dash |

Orchestrator-level quality-gate cadence : EVERY worker reply (not sync-points), per BOOTSTRAP-RUNBOOK §6.1 answer "QG cadence : Every worker reply".

Worker self-runs ALL validators BEFORE `tmo task done`. If any validator fails : worker fixes root cause, re-runs validators, only then reports done. Orchestrator-side QG re-runs validators independently as cross-check.

## Risk Register

Per vooronderzoek §14 (Verification Gaps) and §11 (Version Matrix limitations) the following risks MUST be tracked during execution. Each risk has an owner-skill responsible for mitigation.

| Risk ID | Risk | Mitigation | Owner skill |
|---------|------|------------|-------------|
| RISK-01 | APCA contrast algorithm details not in approved sources (WCAG 3 draft) | Skill MUST state "WCAG 2.2 ratio test is the requirement; APCA is informational only"; defer until WCAG 3 published | frontend-a11y-motion-contrast-wcag22 |
| RISK-02 | `dvh` / `svh` / `lvh` viewport units not WebFetched in Phase 2 round | Phase 4 topic-research drills MDN viewport-percentage-lengths page BEFORE skill authoring | frontend-errors-units-rendering-viewport |
| RISK-03 | `inert` attribute details not WebFetched | Phase 4 topic-research drills MDN inert attribute page | frontend-a11y-focus-keyboard-inert |
| RISK-04 | `@starting-style` + `transition-behavior: allow-discrete` syntax not WebFetched | Phase 4 topic-research drills both MDN pages | frontend-impl-popover-dialog-anchor |
| RISK-05 | `scheduler.yield()` API signature + Baseline status not separately verified | Phase 4 topic-research drills MDN Scheduler/yield page | frontend-perf-core-web-vitals-inp |
| RISK-06 | ES2024 Iterator helpers not WebFetched | Phase 4 topic-research drills MDN Iterator page; if Baseline 2025 transition incomplete, gate skill content with @supports | frontend-syntax-js-es2024-ts-dom |
| RISK-07 | Open UI status of `appearance: base-select` for form customization unclear | Phase 4 topic-research drills open-ui.org; if unstable, defer mention in form skill | frontend-syntax-html5-form |
| RISK-08 | APG patterns for Carousel / Disclosure / Listbox / Menu / Radio / Tree / Treegrid only partially drilled in Phase 2 | Phase 4 topic-research drills remaining APG patterns BEFORE aria-patterns skill | frontend-a11y-aria-patterns |
| RISK-09 | font-feature-settings vs font-variation-settings best-practice ordering not WebFetched | Phase 4 topic-research drills MDN @font-face descriptors | frontend-impl-typography-system |
| RISK-10 | Speculation Rules eagerness modes not WebFetched on parent MDN page | Phase 4 topic-research drills MDN Speculation_Rules_API/Using sub-page; skill MUST cite Limited Availability | frontend-perf-core-web-vitals-inp |
| RISK-11 | DTCG draft 2025.10 explicitly NOT yet production-implementation-ready | Skill MUST disclose pre-release status; recommend Style Dictionary or similar transformation; document migration risk | frontend-impl-design-tokens |
| RISK-12 | Anchor Positioning still emerging in 2026; partial browser support | Skill MUST gate every feature with `@supports (anchor-name: --x)`; provide JS-positioning fallback example | frontend-impl-popover-dialog-anchor |
| RISK-13 | Speculation Rules API Chromium-only (Limited Availability) | Document as advanced/optional; never make Speculation Rules a Baseline requirement | frontend-perf-core-web-vitals-inp |

Top 3 risks (impact-weighted, blocking risk to Phase 5 if unaddressed) :

1. **RISK-08** (APG patterns incomplete) : blocks `frontend-a11y-aria-patterns` skill quality; Phase 4 drilling is mandatory.
2. **RISK-04** (`@starting-style` + `transition-behavior` syntax unverified) : blocks `frontend-impl-popover-dialog-anchor` renderable example; Phase 4 drilling mandatory.
3. **RISK-11** (DTCG draft pre-release) : design-tokens skill MUST disclose pre-release status; otherwise users will hit implementation gaps downstream.

