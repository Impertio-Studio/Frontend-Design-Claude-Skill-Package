# Archive Diff : 18 old `fd-*` skills vs 36 new `frontend-*` skills

> Generated : 2026-05-19
> Old archive : `/home/freek/GitHub/_archive/Frontend-Design-pre-bootstrap-2026-05-19/`
> Verdict : every old skill is either SUPERSEDED by a deeper new skill or MERGED into a richer combined skill. No salvage required (new content is research-first, WebFetch-verified, framework-agnostic, deterministic; old content predates the 7-phase methodology and lacks per-skill WebFetch citations).

## Mapping matrix

| # | Old skill (fd-*) | New skill (frontend-*) | Action | Reason |
|---|------------------|------------------------|--------|--------|
| 1 | fd-accessibility/aria-patterns | frontend-a11y/frontend-a11y-aria-patterns | SUPERSEDED | New skill drilled APG patterns (Carousel/Disclosure/Listbox/Menu/Radio/Tree/Treegrid) via WebFetch + WAI-APG citations |
| 2 | fd-accessibility/focus-management | frontend-a11y/frontend-a11y-focus-keyboard-inert | SUPERSEDED | New skill adds `inert` (Baseline 2023), roving tabindex vs aria-activedescendant decision, 6-effect inert deep-dive |
| 3 | fd-component-patterns/card-layouts | frontend-impl/frontend-impl-responsive-layout-fluid | MERGED | Per D-R04 masterplan decision : card is a composition of responsive + container queries, not standalone |
| 4 | fd-component-patterns/command-palette | frontend-component/frontend-component-data-tables-command-palette | MERGED | New skill combines command palette + accessible data tables (shared APG combobox + roving tabindex mechanics) |
| 5 | fd-component-patterns/modal-system | frontend-component/frontend-component-modal-toast-system | MERGED | Per D-R05 masterplan decision : modal + toast share top-layer + aria-live mechanics |
| 6 | fd-component-patterns/toast-notifications | frontend-component/frontend-component-modal-toast-system | MERGED | Same as #5 |
| 7 | fd-css-foundations/design-tokens | frontend-impl/frontend-impl-design-tokens | SUPERSEDED | New skill cites W3C DTCG draft 2025.10 explicitly, gates pre-release status, adds @property registration patterns |
| 8 | fd-css-foundations/responsive-layout | frontend-impl/frontend-impl-responsive-layout-fluid | SUPERSEDED | New skill adds container queries vs media queries decision, dvh/svh/lvh viewport units, fluid-clamp() patterns |
| 9 | fd-css-foundations/typography-system | frontend-impl/frontend-impl-typography-system | SUPERSEDED | New skill drills §14 gap : font-feature-settings vs font-variation-settings ordering, text-wrap: balance + pretty |
| 10 | fd-performance/animation-performance | frontend-perf/frontend-perf-animation-gpu-containment + frontend-errors/frontend-errors-animation-jank | SUPERSEDED (split) | New pkg splits "how to do it right" (perf skill) from "how to fix it when broken" (errors skill) |
| 11 | fd-performance/css-optimization | frontend-perf/frontend-perf-animation-gpu-containment | SUPERSEDED | Per D-R18 masterplan decision : merged with animation-gpu (shared compositor/containment mental model) |
| 12 | fd-theming/color-palette-generator | frontend-theming/frontend-theming-color-palette-oklch | SUPERSEDED | New skill uses OKLCH systematically (perceptual uniformity) with WCAG contrast guarantees |
| 13 | fd-theming/dark-light-toggle | frontend-theming/frontend-theming-dark-light-mode | SUPERSEDED | New skill uses native `light-dark()` + `color-scheme` (Baseline 2024) instead of JS class-toggle |
| 14 | fd-visual-effects/glassmorphism | frontend-visual/frontend-visual-glassmorphism-backdrop | SUPERSEDED | New skill cites backdrop-root requirement explicitly, WCAG contrast preservation, mobile GPU budget |
| 15 | fd-visual-effects/gradient-animations | frontend-visual/frontend-visual-gradients | SUPERSEDED | New skill adds color-space hints (oklch interpolation) for banding-free gradients, @property animation |
| 16 | fd-visual-effects/micro-interactions | frontend-visual/frontend-visual-micro-interactions | SUPERSEDED | New skill adds `@starting-style` + `transition-behavior: allow-discrete` (Baseline 2024) for display transitions |
| 17 | fd-visual-effects/particle-effects | (none) | DROP-OBSOLETE | Per D-R03 masterplan decision : niche, framework-agnostic Canvas particle systems are widely covered elsewhere |
| 18 | fd-visual-effects/scroll-animations | frontend-impl/frontend-impl-view-transitions-scroll-animations | MERGED | Per D-R13 masterplan decision : scroll-snap + scroll-driven + view-transitions form one cohesive timeline chapter |

## Summary

| Action | Count | Notes |
|--------|-------|-------|
| SUPERSEDED | 12 | Direct 1:1 replacement with deeper content |
| MERGED | 5 | Folded into multi-topic skill per D-R refinement |
| DROP-OBSOLETE | 1 | Out of scope for evergreen-2026 framework-agnostic pkg |
| **Total** | **18** | All 18 archive skills accounted for |

## Salvage assessment

NONE required. Sample inspection of `fd-css-foundations/design-tokens/SKILL.md` (the most-promising archive candidate) shows :
- Predates WebFetch verification policy (D-006) : no inline `(verified 2026-05-19)` citations
- Predates AGENT-SKILLS-STANDARD (Keywords mix not present)
- Predates Phase 4 topic-research methodology
- Uses generic "modern browser (2020+)" compatibility marker, not `evergreen-2026`
- Body content less specific than new skill (no DTCG draft callout, no @property registration)

The new 36-skill corpus replaces the archive completely. Archive stays at `/home/freek/GitHub/_archive/Frontend-Design-pre-bootstrap-2026-05-19/` as historical reference.

## Decision record

This diff was produced after Phase 5 completion per user instruction in Prompt-1 ("Na Phase 5 : STOP. Daarna : diff-fase tegen archief"). User then directed continuation through Phase 7 ("werk maak uit geheel tot phase 7"), accepting the SUPERSEDED-by-default verdict.

No further user action required for archive handling. Proceed to Phase 6 validation + Phase 7 publication.
