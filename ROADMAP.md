# ROADMAP : Frontend Design Skill Package

## Current Status

| Phase | Description | Status | Progress |
|-------|------------|--------|----------|
| Phase 1 | Raw Masterplan | DONE | 100% |
| Phase 2 | Deep Research (Vooronderzoek) | DONE | 100% |
| Phase 3 | Masterplan Refinement | DONE | 100% |
| Phase 4 | Topic-Specific Research | IN PROGRESS (interleaved) | 18% (2/11 drilled) |
| Phase 5 | Skill Creation | IN PROGRESS | 8% (3/36 done) |
| Phase 6 | Validation | PENDING | 0% |
| Phase 7 | Publication | PENDING | 0% |

**Overall Progress** : 50% (batch 1 of 12 complete, batch 2 dispatched)

## Active Batch

| Worker | Task | Skill | Status |
|--------|------|-------|--------|
| fd-worker-1 | T-4 | frontend-syntax-html5-semantic | in_progress |
| fd-worker-2 | T-6 | frontend-syntax-css-cascade-layers-scope | in_progress |
| fd-worker-3 | T-5 | frontend-syntax-html5-form | awaiting Phase 4 topic-research |

## Next Steps

1. Phase 4 research for `frontend-syntax-html5-form` completes (Open UI gap drill, async) -> inject T-5 to fd-worker-3
2. Batch-2 quality-gate loop : APPROVE / RE-INSTRUCT / REPLACE on each `done` event
3. Batch-2 commit + close, dispatch batch-3 (skills batch syntax CSS : container-queries, has-selector, color-modern)
4. Repeat batch loop through batch-12
5. Phase 6 validation + INDEX/README + GitHub release v1.0.0

## Skill Summary (Phase 3 refined, batch progress)

| Category | Planned | Created | Validated |
|----------|---------|---------|-----------|
| frontend-core | 3 | 3 | 3 |
| frontend-syntax | 9 | 0 (2 in-flight) | 0 |
| frontend-impl | 6 | 0 | 0 |
| frontend-errors | 4 | 0 | 0 |
| frontend-theming | 2 | 0 | 0 |
| frontend-visual-effects | 3 | 0 | 0 |
| frontend-accessibility | 3 | 0 | 0 |
| frontend-performance | 2 | 0 | 0 |
| frontend-component-patterns | 2 | 0 | 0 |
| frontend-agents | 2 | 0 | 0 |
| **Total** | **36** | **3** | **3** |

## Changelog

### Phase 5 : Batch 1 complete (2026-05-19)

- frontend-core-architecture (268 lines, ad341d4)
- frontend-core-web-standards-baseline (254 lines, 8ff9043)
- frontend-core-design-philosophy (266 lines, 94ee554)
- All 5 validators green (frontmatter / line-count / structure / language / emdash)
- Batch-1 quality-gate verdicts : T-1 + T-2 + T-3 all APPROVED
- L-001 + L-002 lessons captured : validate-structure.js requires `{prefix}-{cat}` dir convention. Workflow Template + bootstrap script need update.
- Fix commit a259f8c renamed all skill cat dirs and updated 55 masterplan path references in one pass.

### Phase 5 : Batch 2 dispatched (2026-05-19)

- T-4 fd-worker-1 frontend-syntax-html5-semantic (Phase 4 skip)
- T-5 fd-worker-3 frontend-syntax-html5-form (Phase 4 research async)
- T-6 fd-worker-2 frontend-syntax-css-cascade-layers-scope (Phase 4 skip)

### Phase 3 : Masterplan Refinement (2026-05-19)

- Refined masterplan written : `docs/masterplan/frontend-masterplan.md` (3000+ lines)
- 19 Refinement Decisions table (D-R01..D-R19) with MERGE / DROP / SPLIT / ADD / MOVE actions
- Final Category Architecture : 10 categories, 36 skills, dependency chain core -> syntax -> impl / a11y / perf -> theming / visual / errors -> component-patterns -> agents
- Execution Plan : 12 batches of 3 workers each, file-scope-disjoint per worker
- Per-Skill Agent Prompts : 36 complete tmux-worker-ready prompts (scope bullets, out-of-scope, keywords, approved sources, decision trees, anti-patterns, quality rules, validators)
- Phase 4 Topic-Research Strategy : 11 / 36 skills require Phase 4 research; 25 skip per skip-criteria
- Quality Gates : 5 validators per skill (frontmatter, line-count, structure, language, emdash)
- Risk Register : 13 risks tracked, top 3 (RISK-08 APG patterns, RISK-04 @starting-style, RISK-11 DTCG draft)
- DECISIONS.md D-008 added documenting Phase-3 categorization
- HANDOFF.md updated to Phase 3 done, Phase 4+5 next

### Phase 2 : Deep Research / Vooronderzoek (2026-05-19)

- `docs/research/vooronderzoek-frontend.md` written : 545 lines, 15 sections
- 41 WebFetch verifications against MDN / W3C-WAI / web.dev / designtokens.org
- Newly discovered sub-topics captured (§12) : @scope, closedby, Speculation Rules, ElementInternals, CSS Logical Properties, Style Container Queries, position-try-fallbacks, Scoped Custom Element Registries, APCA, dvh/svh/lvh, inert, @starting-style + transition-behavior
- Recommendations for Phase 3 captured (§13)
- Verification Gaps documented (§14) : 10 gaps for Phase 4 topic-research drilling
- SOURCES.md Last-Verified updated for primary verified URLs

### Phase 1 : Infrastructure + Raw Masterplan (2026-05-19)

- Repository structure created
- Core files initialized (CLAUDE.md, ROADMAP.md, REQUIREMENTS.md, DECISIONS.md, SOURCES.md, WAY_OF_WORK.md, LESSONS.md, CHANGELOG.md, HANDOFF.md, OPEN-QUESTIONS.md, INDEX.md)
- Skill category directories created
- Raw masterplan written : 10 categories, ~49 topics inventoried (`docs/masterplan/frontend-masterplan-raw.md`)
- SOURCES.md populated with ~25 approved web-standards URLs (MDN, W3C, WAI-ARIA, web.dev, WHATWG, Baseline, caniuse, Open UI)
- Ready for Phase 2 deep research
