# Sources : Frontend Design Skill Package

## Approved Sources

All skill content MUST be verified against these approved sources. No unverified blog posts, no AI-generated content without WebFetch verification, no random StackOverflow answers.

### Primary Sources : Authoritative Web Standards

| Source | URL | Type | Last Verified |
|--------|-----|------|---------------|
| MDN Web Docs : HTML | https://developer.mozilla.org/en-US/docs/Web/HTML | Reference | 2026-05-19 |
| MDN Web Docs : CSS | https://developer.mozilla.org/en-US/docs/Web/CSS | Reference | 2026-05-19 |
| MDN Web Docs : JavaScript | https://developer.mozilla.org/en-US/docs/Web/JavaScript | Reference | 2026-05-19 |
| MDN Web Docs : Web APIs | https://developer.mozilla.org/en-US/docs/Web/API | Reference | 2026-05-19 |
| MDN Web Docs : Accessibility | https://developer.mozilla.org/en-US/docs/Web/Accessibility | Reference | Not yet |
| WHATWG HTML Living Standard | https://html.spec.whatwg.org/multipage/ | Specification | 2026-05-19 |
| WHATWG DOM Living Standard | https://dom.spec.whatwg.org/ | Specification | Not yet |
| W3C CSS Working Group | https://www.w3.org/Style/CSS/ | Specification index | Not yet |
| W3C Technical Reports (TR) | https://www.w3.org/TR/ | Specification index | Not yet |
| W3C WAI : WCAG 2.2 | https://www.w3.org/TR/WCAG22/ | Specification | 2026-05-19 |
| W3C WAI : ARIA 1.2 | https://www.w3.org/TR/wai-aria-1.2/ | Specification | Not yet |
| W3C WAI : ARIA Authoring Practices Guide | https://www.w3.org/WAI/ARIA/apg/ | Patterns | 2026-05-19 |
| W3C WAI APG : Dialog Modal | https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/ | Pattern | 2026-05-19 |
| W3C WAI APG : Combobox | https://www.w3.org/WAI/ARIA/apg/patterns/combobox/ | Pattern | 2026-05-19 |
| W3C WAI APG : Tabs | https://www.w3.org/WAI/ARIA/apg/patterns/tabs/ | Pattern | 2026-05-19 |
| W3C WAI WCAG22 Understanding : Target Size Minimum | https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html | Understanding doc | 2026-05-19 |
| W3C Design Tokens Community Group | https://www.w3.org/community/design-tokens/ | Spec draft | Not yet |
| W3C Design Tokens draft | https://www.designtokens.org/tr/drafts/format/ | Spec draft | 2026-05-19 |
| web.dev : Patterns | https://web.dev/patterns/ | Tutorial | Not yet |
| web.dev : Performance | https://web.dev/explore/performance | Tutorial | Not yet |
| web.dev : Accessibility | https://web.dev/explore/accessibility | Tutorial | Not yet |
| web.dev : CSS | https://web.dev/explore/css | Tutorial | Not yet |
| web.dev : Vitals (Core Web Vitals) | https://web.dev/articles/vitals | Tutorial | 2026-05-19 |
| web.dev : Optimize INP | https://web.dev/articles/optimize-inp | Tutorial | 2026-05-19 |
| web.dev : Baseline | https://web.dev/baseline | Reference | 2026-05-19 |
| Baseline : Web Platform Status | https://web-platform-dx.github.io/web-features/ | Compatibility | Not yet |
| Can I Use | https://caniuse.com/ | Compatibility | Not yet |
| developer.chrome.com | https://developer.chrome.com/docs/web-platform/ | Tutorial | Not yet |
| Open UI Community Group | https://open-ui.org/ | Spec drafts | Not yet |

### Secondary Sources : Use Only When Primary Is Insufficient

| Source | URL | Type | Last Verified |
|--------|-----|------|---------------|
| TC39 Proposals | https://github.com/tc39/proposals | Spec drafts | Not yet |
| CSS Working Group GitHub | https://github.com/w3c/csswg-drafts | Spec drafts | Not yet |
| WICG : Web Incubator Community Group | https://wicg.io/ | Proposals | Not yet |

### Banned Sources

- Random blog posts without spec citations
- AI-generated content without WebFetch verification
- StackOverflow answers older than 24 months without re-verification
- Tutorial sites lacking maintenance dates
- Framework-specific blogs (this is a framework-agnostic package)

## Verification Rules

1. **Primary sources ONLY** : Official specs (W3C/WHATWG/WAI) > MDN > web.dev > developer.chrome.com.
2. **WebFetch verification REQUIRED** for every code snippet, API signature, and behavior claim (per D-006).
3. **Version-check** : ensure source matches `evergreen-2026` baseline. Cite "Baseline 2024/2025" status when applicable.
4. **Date-check** : log Last-Verified date per source in this table on first verification.
5. **Cross-reference** : if MDN and spec disagree, spec wins, document discrepancy in LESSONS.md.
6. **Citation format in research docs** : `[MDN: <topic>](<url>) (verified 2026-05-19)`.
7. **No fallback sources** : if approved sources insufficient for a claim, do not include the claim; add gap to LESSONS.md instead.

## Source Addition Protocol

When discovering a new source during research :

1. Verify it is official (W3C/WHATWG/WAI/standards-body) or maintained by a browser-vendor team (Chrome/Firefox/WebKit).
2. Add to appropriate table above with type and "Not yet" Last-Verified.
3. WebFetch and update Last-Verified to current date on first use.
4. Record in LESSONS.md if the source revealed insights not in primary sources.

## Last-Verified Update Protocol

When a research agent verifies a source via WebFetch :

1. Update Last-Verified column to current date (YYYY-MM-DD).
2. If source content changed materially since last verify, note in LESSONS.md.
3. If source is gone / moved : flag in LESSONS.md, find replacement, update table.

## Browser Compatibility Source Hierarchy

For "does X work in browser Y" questions :

1. Baseline (web-platform-dx.github.io) : authoritative for evergreen status
2. Can I Use (caniuse.com) : detailed per-version, including partial support
3. MDN compatibility tables : per-API/per-property, sourced from BCD
4. Browser vendor release notes : when above are unclear or stale

Never assert browser compatibility from training data : always verify via Baseline or Can I Use.
