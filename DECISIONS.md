# Architectural Decisions

Numbered decisions (D-XXX) with rationale. Immutable once recorded — new decisions can supersede but never delete old ones.

---

## D-001: English-Only Skills

- **Date**: 2026-05-19
- **Decision**: All skill content MUST be in English only.
- **Rationale**: Skills are instructions FOR Claude, not for end users. Claude reads English and responds in the user's language. Bilingual skills double maintenance with zero functional benefit. Proven in ERPNext (28 skills) and Blender-Bonsai (73 skills).
- **Consequence**: No translations needed. All descriptions, code comments, and documentation in English.

---

## D-002: MIT License

- **Date**: 2026-05-19
- **Decision**: Project uses MIT License.
- **Rationale**: Most permissive license, maximizes adoption. Consistent with OpenAEC Foundation standards.
- **Consequence**: No commercial restrictions. Community-friendly.

---

## D-003: SKILL.md Under 500 Lines

- **Date**: 2026-05-19
- **Decision**: SKILL.md files MUST be under 500 lines.
- **Rationale**: Keeps main skill focused on decision trees and quick reference. Heavy content belongs in references/ directory. Proven optimal in ERPNext (180-427 lines per skill).
- **Consequence**: Complex topics split between SKILL.md (quick reference + patterns) and references/ (complete API, examples, anti-patterns).

---

## D-004: 7-Phase Research-First Methodology

- **Date**: 2026-05-19
- **Decision**: Follow the 7-phase research-first development methodology.
- **Rationale**: Proven in ERPNext (28 skills), Blender-Bonsai (73 skills), and Tauri 2 (27 skills). Research prevents hallucination. Deterministic skills require deep understanding.
- **Consequence**: No skill creation without prior deep research. Phases are sequential with defined exit criteria.

---

## D-005: ROADMAP.md as Single Source of Truth

- **Date**: 2026-05-19
- **Decision**: ROADMAP.md is the ONLY place for project status.
- **Rationale**: Multiple status locations cause drift and "which is current?" confusion. Single source of truth enables reliable session recovery.
- **Consequence**: Never duplicate status in CLAUDE.md or other files. All status references point to ROADMAP.md.

---

## D-006: WebFetch for Research Verification

- **Date**: 2026-05-19
- **Decision**: Use WebFetch to verify all code examples against latest official documentation.
- **Rationale**: Technology APIs evolve. Training data may be stale. WebFetch ensures latest official docs are consulted, not outdated cached knowledge.
- **Consequence**: All code examples must be verified against current official documentation before inclusion in skills.

---

## D-007: GitHub Publication Under OpenAEC Foundation

- **Date**: 2026-05-19
- **Decision**: Publish all skill packages under the OpenAEC Foundation GitHub organization.
- **Rationale**: Centralized, consistent branding. Community ownership. Discoverability.
- **Consequence**: All repos follow OpenAEC naming conventions and include social preview banners with OpenAEC branding.

---

## D-008: Phase 3 Categorization (36 skills locked across 10 categories)

- **Date**: 2026-05-19
- **Decision**: Final skill inventory locked at 36 skills across 10 categories, applying 19 refinement decisions D-R01..D-R19 documented in `docs/masterplan/frontend-masterplan.md`.
- **Categories kept (10)**: core (3), syntax (9), impl (6), errors (4), theming (2), visual-effects (3), accessibility (3), performance (2), component-patterns (2), agents (2).
- **Decisions summary**:
  - **8 MERGE** : core-rendering-model into core-architecture (D-R02), core-browser-baseline into core-web-standards (D-R01), impl-form-design into syntax-html5-form (D-R12), impl-scroll-driven-animations into impl-view-transitions-scroll-animations (D-R11), visual-scroll-effects into impl-view-transitions-scroll-animations (D-R13), errors-a11y-violations into a11y-aria-patterns (D-R15), a11y-focus-management + a11y-keyboard-nav + inert into a11y-focus-keyboard-inert (D-R16), perf-css-optimization + perf-animation-gpu into perf-animation-gpu-containment (D-R18), agents-a11y-auditor + agents-cross-skill-consistency into agents-a11y-perf-consistency-auditor (D-R19), impl-speculation-rules into perf-core-web-vitals-inp (D-R10), component-toast-notifications into component-modal-toast-system (D-R05).
  - **4 ADD (via combined skills)** : syntax-css-cascade-layers-scope adds @scope (D-R06), syntax-css-nesting-logical-properties adds Logical Properties (D-R07), syntax-js-es2024-ts-dom adds TS DOM patterns (D-R08), a11y-motion-contrast-wcag22 adds WCAG 2.2 compliance content (D-R17).
  - **3 DROP** : visual-particle-canvas (D-R03 niche), component-card-layouts (D-R04 folded into impl-responsive-layout-fluid), separate component-toast-notifications (D-R05 merged).
  - **1 SPLIT (combined-split)** : impl-popover-api expanded into impl-popover-dialog-anchor covering popover + dialog + anchor + discrete-transitions + closedby + position-try-fallbacks (D-R09).
  - **1 MOVE** : theming-distinctive-aesthetic relocated to core-design-philosophy (D-R14).
- **Rationale**: Pre-research estimate was ~49 topics; vooronderzoek §13 recommended ~52 with mandatory adds. Phase 3 prioritizes deterministic, maintainable skills over inventory inflation. 36 is at the upper bound of the 30-36 user-target range, preserving topical depth while consolidating overlapping mechanics (popover + dialog + anchor; focus + keyboard + inert; perf-containment + animation-gpu).
- **Consequence**: 12 batches of 3 workers each in tmux-orchestration; 11 / 36 skills require Phase 4 topic-research; remaining 25 skip per skip-criteria (vooronderzoek coverage sufficient). Full execution plan with file-scope-disjoint worker assignments and ready-to-paste worker prompts in `docs/masterplan/frontend-masterplan.md`.
