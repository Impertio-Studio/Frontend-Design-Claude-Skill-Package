# Lessons Learned

Observations and findings captured during skill package development.

---

## L-001 : Bootstrap must pre-create {prefix}-{cat} dirs, not bare cat dirs

- **Date** : 2026-05-19
- **Discovered during** : Phase 5 batch-1 dispatch
- **Symptom** : `validate-structure.js` failed with "Does not match expected path pattern skills/source/{prefix}-{category}/{prefix}-{category}-{topic}/SKILL.md" on every batch-1 skill. Both fd-worker-2 and fd-worker-3 committed to `skills/source/core/...`, fd-worker-1 blocked at start when self-validation flagged the mismatch.
- **Root cause** : `scripts/bootstrap-new-package.sh` created bare `skills/source/{core,syntax,impl,errors,agents}/` dirs without the `{prefix}-` prefix. Masterplan template inherited the same pattern. Validator (shared across all OpenAEC packages) requires `skills/source/{prefix}-{cat}/{prefix}-{cat}-{topic}/`.
- **Fix applied (commit a259f8c)** : `git mv` rename all 5 bare dirs to `frontend-*`; mkdir 5 additional cats with prefix; sed-update all 55 path references in masterplan; re-instruct blocked worker with new file-scope.
- **Forward action** : update `Skill-Package-Workflow-Template/scripts/bootstrap-new-package.sh` to emit `skills/source/{prefix}-{cat}/` directly. Update `templates/masterplan.md.template` to use prefixed paths in all examples.
- **Impact prevention** : without fix, every batch in this pkg would have hit the same blocker, multiplying re-instruct cost by 12 batches. One-shot rename closed it for the rest of the pkg.

---

## L-002 : Phase-3 refinement agent should generate per-skill prompts that already match the validator-expected path

- **Date** : 2026-05-19
- **Discovered during** : Phase 3 agent output review (only surfaced when Phase 5 ran)
- **Symptom** : Per-skill agent prompts inside `docs/masterplan/frontend-masterplan.md` specified output paths like `skills/source/core/frontend-core-architecture/` which are not what the validator accepts.
- **Root cause** : Phase 3 agent inherited path convention from raw masterplan template + bootstrap dir layout; did not cross-reference `Skill-Package-Workflow-Template/scripts/validate-structure.js` regex.
- **Fix applied** : sed across masterplan during L-001 rename. All future Phase 3 dispatches should explicitly require the agent to read `validate-structure.js` and emit prompts matching the regex.
- **Forward action** : add validator-path-cross-check to the Phase 3 agent prompt template in `BOOTSTRAP-RUNBOOK.md §5`.
