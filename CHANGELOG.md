# Changelog

All notable changes to the Frontend Design Skill Package.

Format follows [Keep a Changelog](https://keepachangelog.com/).

## [1.0.0] : 2026-05-19

### Added

- 36 deterministic Claude skills across 10 categories for framework-agnostic modern frontend design
- 7-phase research-first methodology applied : raw masterplan, deep research, refined masterplan, topic research, skill creation, validation, publication
- Vooronderzoek `docs/research/vooronderzoek-frontend.md` (7545 words, 41 WebFetch verifications)
- 11 Phase-4 topic-research files at `docs/research/topic-research/` (~40,000 combined words, 200+ verifications)
- All skills WebFetch-verified against approved sources : MDN, W3C, WAI-APG, web.dev, WHATWG, designtokens.org, Open UI
- INDEX.md generated catalog with category dependency chain
- agentskills.org standard discovery : `package.json` `agents.skills[]` + `agents/openai.yaml`
- Social preview banner `docs/social-preview.png` (1280x640)
- 5 validators green : frontmatter / line-count / structure / language / em-dash
- Compliance audit : 100% (4/4 checks passed)

### Skills per category

| Category | Count |
|----------|:-----:|
| frontend-core | 3 |
| frontend-syntax | 9 |
| frontend-impl | 6 |
| frontend-errors | 4 |
| frontend-theming | 2 |
| frontend-visual | 3 |
| frontend-a11y | 3 |
| frontend-perf | 2 |
| frontend-component | 2 |
| frontend-agents | 2 |
| **Total** | **36** |

### Methodology lessons

- L-001 : `bootstrap-new-package.sh` MUST emit `skills/source/{prefix}-{cat}/` directly, not bare `{cat}/`. Validator `validate-structure.js` enforces `{prefix}-{cat}` parent-dir pattern.
- L-002 : Phase-3 refinement agent MUST cross-check `validate-structure.js` regex when emitting per-skill output paths.

## [Unreleased]

(no pending changes)
