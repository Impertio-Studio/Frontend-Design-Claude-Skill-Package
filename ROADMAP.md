# ROADMAP : Frontend Design Skill Package

## Current Status

| Phase | Description | Status | Progress |
|-------|------------|--------|----------|
| Phase 1 | Raw Masterplan | DONE | 100% |
| Phase 2 | Deep Research (Vooronderzoek) | DONE | 100% |
| Phase 3 | Masterplan Refinement | DONE | 100% |
| Phase 4 | Topic-Specific Research | NEXT (interleaved with Phase 5) | 0% |
| Phase 5 | Skill Creation | PENDING | 0% |
| Phase 6 | Validation | PENDING | 0% |
| Phase 7 | Publication | PENDING | 0% |

**Overall Progress** : 42% (infrastructure + raw masterplan + vooronderzoek + Phase 3 refined masterplan complete; awaiting user-checkpoint before Phase 4+5)

## Next Steps

1. User-checkpoint : present refined masterplan (19 decisions, 36 skills, 12 batches, full per-skill prompts) for approval
2. Phase 4 + 5 : tmux-orchestration with 3 skill-builder workers
   - Per batch : in-process opus agents for Phase 4 topic-research (11 skills require it, 25 skip per skip-criteria) -> tmux workers receive bundle-injected batch prompts
   - Quality-gate every worker reply (validate-frontmatter + line-count + structure + language + emdash all exit 0)
3. Phase 6 : full-pkg validation (frontend-agents-design-system-validator + frontend-agents-a11y-perf-consistency-auditor self-applied)
4. Phase 7 : INDEX.md + README.md + social preview banner + GitHub release v1.0.0

## Skill Summary (Phase 3 refined, locked)

| Category | Planned | Created | Validated |
|----------|---------|---------|-----------|
| core | 3 | 0 | 0 |
| syntax | 9 | 0 | 0 |
| impl | 6 | 0 | 0 |
| errors | 4 | 0 | 0 |
| theming | 2 | 0 | 0 |
| visual-effects | 3 | 0 | 0 |
| accessibility | 3 | 0 | 0 |
| performance | 2 | 0 | 0 |
| component-patterns | 2 | 0 | 0 |
| agents | 2 | 0 | 0 |
| **Total** | **36** | **0** | **0** |

Final count locked at 36 after Phase 3 refinement (19 decisions applied : 8 MERGE + 4 ADD + 3 DROP + 1 SPLIT + 1 MOVE; net delta from raw 49 topics).

## Changelog

### Phase 3 : Masterplan Refinement (2026-05-19)

- Refined masterplan written : `docs/masterplan/frontend-masterplan.md` (3000+ lines)
- 19 Refinement Decisions table (D-R01..D-R19) with MERGE / DROP / SPLIT / ADD / MOVE actions
- Final Category Architecture : 10 categories, 36 skills, dependency chain core -> syntax -> impl / a11y / perf -> theming / visual / errors -> component-patterns -> agents
- Execution Plan : 12 batches of 3 workers each, file-scope-disjoint per worker
- Per-Skill Agent Prompts : 36 complete tmux-worker-ready prompts (scope bullets, out-of-scope, keywords, approved sources, decision trees, anti-patterns, quality rules, validators)
- Phase 4 Topic-Research Strategy : 11 / 36 skills require Phase 4 research; 25 skip per skip-criteria
- Quality Gates : 5 validators per skill (frontmatter, line-count, structure, language, emdash)
- Risk Register : 13 risks tracked, top 3 (RISK-08 APG patterns, RISK-04 @starting-style, RISK-11 DTCG draft)
- DECISIONS.md D-008 added documenting Phase-3 categorization decisions
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
