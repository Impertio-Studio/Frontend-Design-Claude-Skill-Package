# Topic Research : frontend-perf-core-web-vitals-inp

## Status

Drilled to close §14 verification gaps in `vooronderzoek-frontend.md` for the `frontend-perf-core-web-vitals-inp` skill (batch 6). Specifically resolved gap 5 (`scheduler.yield()` signature, Baseline status, yield-ladder ordering) and gap 10 (Speculation Rules eagerness values `immediate` / `eager` / `moderate` / `conservative`). All Core Web Vitals thresholds for 2026 are re-confirmed (LCP 2.5s / 4.0s, INP 200ms / 500ms, CLS 0.1 / 0.25). Long Animation Frame API (Baseline 2024 in Chromium-equivalent, Limited outside) is added as the primary INP-diagnosis surface. Speculation Rules API is confirmed Limited (Chromium-only), folded into this skill per masterplan D-R10. WebFetch round count : 12.

All claims sourced inline via `(verified 2026-05-19)` citations against MDN, web.dev, and developer.chrome.com (all already on SOURCES.md or being added in §9 below). The skill targets evergreen-2026 ; non-Baseline surfaces (`scheduler.yield`, Speculation Rules, Long Animation Frame) MUST be feature-detected in produced code samples.

## 1. LCP / CLS / INP definitions and thresholds 2026

The three Core Web Vitals are LCP, INP, and CLS. INP replaced FID as the official interactivity metric when it became a stable Core Web Vital in 2024 (web.dev calls out the 2023 promotion to "pending" with the explicit intent to retire FID, finalized through 2024).

**Largest Contentful Paint (LCP)** : measures loading performance. Per [web.dev : LCP](https://web.dev/articles/lcp) (verified 2026-05-19), LCP reports the render time of the largest image, text block, or video visible in the viewport.

| Bucket | Threshold |
|--------|-----------|
| Good | <= 2.5 s |
| Needs improvement | 2.5 s to 4.0 s |
| Poor | > 4.0 s |

Qualifying elements : `<img>` (first frame for animated content), `<image>` inside `<svg>`, `<video>` poster (or first frame, whichever is earlier), block-level elements containing text, and elements with a CSS `background-image` set via `url()`. The element list is intentionally restricted by the spec to keep the metric stable. LCP includes any unload time from a previous page, connection setup, redirect time, and TTFB. The reported `largest-contentful-paint` PerformanceObserver entry exposes `renderTime`, `loadTime`, `size`, `id`, `url`, `element`, `paintTime`, and `presentationTime` ([MDN : LargestContentfulPaint](https://developer.mozilla.org/en-US/docs/Web/API/LargestContentfulPaint) verified 2026-05-19, Newly available 2025).

**Interaction to Next Paint (INP)** : measures responsiveness. Per [web.dev : INP](https://web.dev/articles/inp) (verified 2026-05-19), INP observes the latency of all click, tap, and keyboard interactions across the lifespan of a page visit and reports a single value representing overall responsiveness. Scroll, hover, and zoom are explicitly excluded.

| Bucket | Threshold |
|--------|-----------|
| Good | <= 200 ms |
| Needs improvement | 200 ms to 500 ms |
| Poor | > 500 ms |

INP applies a one-outlier-per-50-interactions filter, then reports the 75th percentile across page views per ([web.dev : INP](https://web.dev/articles/inp) verified 2026-05-19). INP replaced FID as a Core Web Vital in March 2024 (per [web.dev : Vitals](https://web.dev/articles/vitals) verified 2026-05-19 and vooronderzoek §7).

**Cumulative Layout Shift (CLS)** : measures visual stability. Per [web.dev : CLS](https://web.dev/articles/cls) (verified 2026-05-19), CLS is `impact fraction * distance fraction` summed within a session window (max 5 s window, max 1 s gap between shifts). The reported score is the **largest** burst, not the sum of all bursts.

| Bucket | Threshold |
|--------|-----------|
| Good | <= 0.1 |
| Needs improvement | 0.1 to 0.25 |
| Poor | > 0.25 |

User-initiated shifts within 500 ms of a click, tap, or keypress are excluded via `hadRecentInput` on the `layout-shift` entry ([MDN : LayoutShift](https://developer.mozilla.org/en-US/docs/Web/API/LayoutShift) verified 2026-05-19, Limited availability per MDN).

**Assessment rule** : all three metrics are evaluated at the **75th percentile** of page loads, segmented across mobile and desktop, per [web.dev : Vitals](https://web.dev/articles/vitals) (verified 2026-05-19). A page only "passes Core Web Vitals" when all three reach the Good bucket at the 75th percentile.

**Supporting metrics** : TTFB and FCP diagnose load issues but have no fixed Core Web Vital threshold ; TBT serves as a lab proxy for INP because INP cannot be measured deterministically in synthetic environments per [web.dev : Vitals](https://web.dev/articles/vitals) (verified 2026-05-19).

## 2. INP measurement and optimization

INP decomposes into three measurable phases per [web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19) :

1. **Input delay** : time between the user gesture and the start of the first event callback. Mostly caused by main-thread contention (script evaluation during load, long tasks from third-party libraries, large hydration steps).
2. **Processing duration** : time the event handler callbacks themselves run. Caused by synchronous work in the handler : layout thrash, large DOM updates, JSON parsing, framework reconciliation.
3. **Presentation delay** : time between the last callback finishing and the next paint. Caused by oversized DOM, expensive `requestAnimationFrame` callbacks, layout/style invalidation in the same frame, and client-side HTML rendering.

**Field vs lab** : INP can only be measured accurately in the field via Real User Monitoring (RUM) ([web.dev : INP](https://web.dev/articles/inp) verified 2026-05-19) using the web-vitals JavaScript library or PerformanceObserver. In the lab, Lighthouse exposes **Total Blocking Time (TBT)** as a proxy because synthetic interactions cannot reproduce real-world user gestures with timing fidelity.

**PerformanceObserver entry types for INP attribution** ([MDN : PerformanceObserver](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver) verified 2026-05-19, Baseline Widely available since January 2020) :

| Entry type | Yields |
|------------|--------|
| `event` | per-interaction latency, target, duration |
| `long-animation-frame` | LoAF entries with script attribution |
| `largest-contentful-paint` | LCP candidates with element reference |
| `layout-shift` | CLS deltas with `sources` attribution |
| `longtask` | legacy 50ms+ task entries (use LoAF instead) |
| `first-input` | legacy FID measurement (kept for compatibility) |

**Long Animation Frame (LoAF) API** is the canonical 2026 surface for INP attribution. Per [MDN : LongAnimationFrameTiming](https://developer.mozilla.org/en-US/docs/Web/API/LongAnimationFrameTiming) (verified 2026-05-19, Baseline 2024 in Chromium-equivalent ; Firefox and Safari lack implementation per [developer.chrome.com : Long Animation Frames](https://developer.chrome.com/docs/web-platform/long-animation-frames) verified 2026-05-19), a Long Animation Frame is any rendering update delayed beyond 50 ms. LoAF entry properties : `duration`, `startTime`, `renderStart`, `styleAndLayoutStart`, `firstUIEventTimestamp`, `blockingDuration`, `paintTime`, `presentationTime`, and `scripts[]` (an array of `PerformanceScriptTiming` entries). Each `PerformanceScriptTiming` exposes `invoker` (e.g. `"DOMWindow.onclick"`), `invokerType`, `sourceURL`, `sourceFunctionName`, `sourceCharPosition`, `duration`, `executionStart`, `forcedStyleAndLayoutDuration`, `pauseDuration`, and `windowAttribution`. Script attribution is same-origin only ; cross-origin iframes, web workers, and service workers report null attribution.

**Why LoAF beats Long Tasks** : the legacy Long Tasks API misses cumulative blocking from many sub-50ms tasks that together delay a frame, and offers no script attribution. LoAF measures full frame cycles (input + script + rendering + paint), so a developer can identify exactly which handler delayed the next paint.

**Observation pattern** (verified pattern from [developer.chrome.com : Long Animation Frames](https://developer.chrome.com/docs/web-platform/long-animation-frames) verified 2026-05-19) :

```js
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    if (entry.blockingDuration > 100) {
      reportLoaf(entry);
    }
  }
});
observer.observe({ type: "long-animation-frame", buffered: true });
```

**Optimization tactics** : break long tasks via yielding (see §3), apply visual updates FIRST in event handlers and defer background work to after the next frame, debounce expensive handlers (typeahead, search), prefer compositor-only animations (`transform`, `opacity`, `filter`), and use `content-visibility: auto` for off-screen sections so style and layout work is skipped until the section approaches the viewport ([web.dev : Optimize INP](https://web.dev/articles/optimize-inp) verified 2026-05-19). Avoid client-side HTML rendering on critical interaction paths : the browser cannot yield while parsing the rendered HTML, which inflates presentation delay.

## 3. scheduler.yield + yield ladder

`scheduler.yield()` is the canonical mechanism to break a long task and let the browser process input, paint, and other high-priority work before continuing. Per [MDN : Scheduler.yield](https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield) (verified 2026-05-19) the signature is parameter-less and returns a `Promise<void>` (rejected with `AbortSignal.reason` if cancelled).

**Critical Baseline note** : `scheduler.yield()` is **Limited availability** as of 2026-05-19. It does NOT pass Baseline because Firefox and Safari do not implement it (the underlying Prioritized Task Scheduling API at [WICG scheduling-apis](https://wicg.github.io/scheduling-apis/) is still incubating). Code targeting evergreen-2026 MUST feature-detect and provide a fallback. This closes vooronderzoek §14 gap #5.

**Priority inheritance** : when called inside a `scheduler.postTask(fn, { priority: "background" })` body, `await scheduler.yield()` inherits `"background"` priority. Outside any `postTask` context, the default is `"user-visible"`. Yielded continuations enter a **boosted task queue** that runs before same-priority `postTask` tasks but after higher-priority work, ensuring fairness ([MDN : Scheduler.yield](https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield) verified 2026-05-19).

**The yield ladder** (preferred to worst, for INP impact) :

| Rank | API | Behavior | INP suitability |
|------|-----|----------|------------------|
| 1 | `await scheduler.yield()` | yields to high-priority work, resumes via boosted queue | best, where supported |
| 2 | `await scheduler.postTask(fn, { priority })` | new task with explicit priority | second choice, more verbose |
| 3 | `await new Promise(r => setTimeout(r, 0))` | clamped to >=4ms after 5 nested calls, no priority awareness | fallback ; degrades after deep nesting |
| 4 | `requestIdleCallback(fn)` | only fires when main thread is idle ; may starve | unsuitable for processing-phase work |
| 5 | `requestAnimationFrame(fn)` | fires before next paint, BEFORE yielding to input | worst for INP : keeps the frame busy through paint |

Per [web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19) : applying visual updates FIRST in a handler then `setTimeout(fn, 0)` inside a `requestAnimationFrame` callback is the safe deferral pattern. The order matters : `rAF` schedules for "right before the next paint", and `setTimeout` inside that callback pushes the deferred work to AFTER paint, so the user sees the visual response on the next frame.

**Production yield pattern** (composes feature-detection + priority fallback) :

```js
async function yieldToMain() {
  if (globalThis.scheduler?.yield) {
    return scheduler.yield();
  }
  if (globalThis.scheduler?.postTask) {
    return scheduler.postTask(() => {}, { priority: "user-visible" });
  }
  return new Promise((resolve) => setTimeout(resolve, 0));
}
```

**`isInputPending()`** ([Web Platform Incubator API, Limited]) : returns `true` if there is a pending user input event. Where supported, use it to yield only when needed : `if (navigator.scheduling?.isInputPending?.()) await yieldToMain();`. This avoids paying the yield cost on every loop iteration. It is also Limited availability (Chromium-only) and MUST be feature-detected.

**Anti-pattern** : yielding without splitting work. Calling `await yieldToMain()` once at the start of a long handler still leaves a single long task afterwards. Yield BETWEEN sub-tasks ; never put all work after a single yield.

## 4. Speculation Rules API + eagerness values

Speculation Rules API enables prefetching or prerendering future navigation targets. Per [MDN : Speculation Rules API](https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API) (verified 2026-05-19), rules are declared in `<script type="speculationrules">` with JSON containing `"prefetch"` and/or `"prerender"` arrays. **Status : Limited availability** (Chromium-based browsers only ; Firefox and Safari do not implement). Feature-detect via `HTMLScriptElement.supports?.("speculationrules")`.

**Prefetch vs prerender** :

| Aspect | prefetch | prerender |
|--------|----------|-----------|
| What loads | document response body only | full page including subresources, JS, data fetches |
| Cost | low (single GET) | ~ equivalent to a hidden `<iframe>` |
| Activation | render on navigation | near-instant tab swap |
| Cross-site | limited, no cookies for privacy | same-origin or same-site with opt-in header |

**Eagerness values** (closes vooronderzoek §14 gap #10) per [developer.chrome.com : prerender-pages](https://developer.chrome.com/docs/web-platform/prerender-pages) and [developer.chrome.com : speculation-rules-improvements](https://developer.chrome.com/blog/speculation-rules-improvements) (both verified 2026-05-19) :

| Eagerness | Trigger | Use case |
|-----------|---------|----------|
| `immediate` | as soon as the rules are observed (page load) | static sites, predictable navigation, small payloads |
| `eager` | currently behaves like `immediate` ; reserved for future positioning between immediate and moderate | future-proofing |
| `moderate` | hover for 200 ms OR `pointerdown` (whichever comes first ; on mobile where no hover, on scroll-stop after 500 ms) | balanced default ; recommended starting point |
| `conservative` | `pointerdown` or `touchstart` only | maximum user-intent confirmation, complex apps |

**Defaults** : `urls` list rules default to `immediate` ; `where` document rules default to `conservative`. This is the opposite of what most authors expect, so the masterplan example MUST set eagerness explicitly.

**Budget limits** (per developer.chrome.com verified 2026-05-19) : Chrome enforces caps of 50 prefetch URLs and 10 prerender URLs at `immediate` eagerness. User-interaction-triggered eagerness (`moderate`, `conservative`) is limited to 2 FIFO slots. Exceeding the cap drops the oldest entry.

**Privacy and destructive-URL prevention** : per [MDN : Speculation Rules API](https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API) (verified 2026-05-19), prefetch and prerender MUST NOT include URLs that cause side effects on GET : sign-out, language switching, add-to-cart, OTP/SMS sign-in, ad-conversion tracking, usage-allowance increments. Mitigation strategies : (a) server-side, watch for the `Sec-Purpose: prefetch` request header and defer side effects ; (b) client-side, gate work on `Document.prerendering` and the `prerenderingchange` event ; (c) use `Clear-Site-Data: "prefetchCache" "prerenderCache"` to invalidate speculated copies after auth state changes.

**Recommended pattern** (closes the masterplan decision-tree requirement) :

```html
<script type="speculationrules">
{
  "prerender": [{
    "source": "document",
    "where": { "and": [
      { "href_matches": "/*" },
      { "not": { "href_matches": "/logout" } },
      { "not": { "href_matches": "/*\\?*(^|&)add-to-cart=*" } },
      { "not": { "selector_matches": "[rel~=nofollow]" } },
      { "not": { "selector_matches": ".no-prerender" } }
    ]},
    "eagerness": "moderate"
  }]
}
</script>
```

Cross-site prerendering requires the destination server to send `Supports-Loading-Mode: credentialed-prerender`.

## 5. LCP optimization tactics

LCP optimization reduces the four sub-parts (TTFB, resource load delay, resource load time, element render delay) per [web.dev : LCP](https://web.dev/articles/lcp) (verified 2026-05-19) and [web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19).

**fetchpriority** : the `fetchpriority` attribute hints the browser to upgrade or downgrade resource fetch priority. Per [MDN : fetchpriority](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/fetchpriority) (verified 2026-05-19, Baseline 2024 since October 2024), values are `high` / `low` / `auto`. Applies to `<img>`, `<link>`, and `<script>`. Production rule : ALWAYS set `fetchpriority="high"` on the LCP image, and ALWAYS set `fetchpriority="low"` on below-the-fold non-critical images.

**Preload** : `<link rel="preload" as="image" href="hero.avif" fetchpriority="high">` triggers an early fetch during HTML parse, useful when the LCP image is discovered late (e.g. set via CSS `background-image` or rendered by client-side JS). Pair with a matching `imagesrcset` and `imagesizes` to preload the responsive variant the layout will actually use. Preload the LCP webfont via `<link rel="preload" as="font" type="font/woff2" crossorigin>` (crossorigin is required even for same-origin).

**Responsive image discovery** : `srcset` + `sizes` allows the preloader to pick the right resolution without waiting for layout, reducing wasted bytes on smaller screens. Use modern formats (AVIF first, WebP fallback, original JPEG/PNG last) via `<picture>` with `type="image/avif"` sources.

**font-display strategy** : per vooronderzoek §7 and [MDN : font-display](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display) (Baseline Widely Available since January 2020), use `font-display: swap` for body text (avoid invisible-text-while-loading) paired with `size-adjust`, `ascent-override`, and `descent-override` on a matched fallback font to minimize CLS at swap. Use `font-display: optional` for nice-to-have display fonts.

**Critical CSS** : inline above-the-fold CSS in `<head>` and defer the rest via `<link rel="preload" as="style" onload="this.rel='stylesheet'">`. This eliminates render-blocking for the initial paint.

**Server-side optimization** : reduce TTFB via CDN edge caching, HTTP/2 server push (or 103 Early Hints with `Link: <hero.avif>; rel=preload`), and HTML streaming so the LCP element flushes early.

## 6. CLS prevention tactics

CLS prevention is a contract : every late-loading element MUST reserve its eventual space at first paint. Per [web.dev : CLS](https://web.dev/articles/cls) (verified 2026-05-19) and [MDN : LayoutShift](https://developer.mozilla.org/en-US/docs/Web/API/LayoutShift) (verified 2026-05-19) :

**Images and video** : ALWAYS set `width` and `height` attributes (or `aspect-ratio` CSS) on `<img>`, `<video>`, and `<iframe>`. The browser uses these to reserve a correctly-sized box before the resource loads. If only intrinsic ratio is known, use `style="aspect-ratio: 16 / 9"` or the CSS `aspect-ratio` property.

**Ads, embeds, dynamic content** : pre-allocate the maximum-likely size as a min-height. For ads with variable slot sizes, prefer a fixed reservation matching the most common slot to minimize average shift, and reserve a placeholder background. Inject new content BELOW existing content where possible, never ABOVE the fold post-load.

**Web fonts** : use `font-display: swap` with `size-adjust` / `ascent-override` / `descent-override` to match fallback metrics to the loaded webfont, so swap shifts content by less than the CLS threshold. Without metric overrides, even `swap` produces a visible reflow ; with overrides, the visual difference is sub-pixel.

**User-initiated shifts** : a layout shift within 500 ms of a user gesture is excluded automatically (per `hadRecentInput` on the `layout-shift` entry per [MDN : LayoutShift](https://developer.mozilla.org/en-US/docs/Web/API/LayoutShift) verified 2026-05-19). Take advantage by triggering necessary reflows from inside click handlers rather than from timers, network responses, or animations.

**Session window** : per [web.dev : CLS](https://web.dev/articles/cls) (verified 2026-05-19), shifts cluster into sessions of max 5 s total duration with at most 1 s between shifts. The reported CLS is the largest such session, not the cumulative total across the entire visit. This means a single very-bad burst dominates the score.

**Attribution** : `LayoutShift.sources[]` ([MDN : LayoutShift](https://developer.mozilla.org/en-US/docs/Web/API/LayoutShift) verified 2026-05-19) yields an array of `LayoutShiftAttribution` objects with `node`, `currentRect`, and `previousRect`. Use this in RUM to identify which elements shifted in production.

## 7. Decision matrix : metric regression to tactic

| Regression symptom | Primary metric | First tactic | Second tactic |
|--------------------|----------------|--------------|---------------|
| Hero image renders late | LCP | `fetchpriority="high"` on hero `<img>` + explicit width/height | `<link rel="preload" as="image" imagesrcset="..." imagesizes="..." fetchpriority="high">` |
| Webfont in LCP element flashes | LCP + CLS | `<link rel="preload" as="font" type="font/woff2" crossorigin>` + `font-display: swap` | matched fallback via `size-adjust` / `ascent-override` |
| Render-blocking JS pushes LCP | LCP | `defer` or `async` on non-critical scripts | code-split, ship only above-fold JS in entry |
| Click handler runs > 100 ms | INP processing | yield between sub-tasks via `await scheduler.yield()` (fallback to `setTimeout(_, 0)`) | move CPU-heavy work to Web Worker |
| Handler updates DOM then computes | INP presentation | apply visual update FIRST, defer compute via `requestAnimationFrame` + `setTimeout(_, 0)` | precompute on idle / hover, cache result |
| Typeahead lag during input | INP input delay | debounce 150-300 ms via `AbortController` or `setTimeout` | use `requestIdleCallback` for non-critical reindex |
| Off-screen lists block interaction | INP processing | `content-visibility: auto` + `contain-intrinsic-size: auto Npx` | virtualise lists with IntersectionObserver |
| Image without dimensions reflows | CLS | add `width` and `height` attrs or `aspect-ratio` CSS | wrap in `<div style="aspect-ratio: w/h">` |
| Ad slot reflows post-load | CLS | reserve fixed `min-height` matching most common slot | use `aspect-ratio` placeholder div |
| Banner injected on scroll | CLS | inject BELOW fold, never push existing content | wait for user gesture (auto-excluded for 500 ms) |
| Cross-origin third-party script blocks main thread | INP input delay | defer or replace ; isolate via iframe with `loading="lazy"` | self-host critical third-party or remove |
| Navigation to next page slow | LCP on next page | Speculation Rules prerender with `eagerness: "moderate"` | prefetch only if Chromium-acceptance insufficient |

## 8. Anti-patterns

1. **`<img>` without `width`/`height`** : reserves no layout box. As the image loads, content reflows downward, scoring CLS proportional to viewport area shifted. Fix : ALWAYS set `width` and `height` attributes (matched to intrinsic ratio) or `aspect-ratio` CSS. Per [web.dev : CLS](https://web.dev/articles/cls) (verified 2026-05-19).

2. **`loading="lazy"` on the LCP image** : defers fetching past first paint, causing the LCP timer to wait for a viewport-intersect callback. Fix : only use `loading="lazy"` for below-fold images ; LCP image gets `fetchpriority="high"` and (often) `loading="eager"` (the default). Per [MDN : fetchpriority](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/fetchpriority) (verified 2026-05-19).

3. **Synchronous long task in click handler** : a 350 ms loop on the main thread inside `onclick` blocks the next frame, producing >350 ms input delay PLUS processing duration. Fix : yield between sub-tasks via `await scheduler.yield()` with a `setTimeout(_, 0)` fallback ; never run a single long task synchronously. Per [web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19).

4. **`font-display: block`** : produces a 3-second invisible-text period before swap, making the LCP element render late. Fix : use `font-display: swap` with `size-adjust` / `ascent-override` matched-fallback metrics so the swap delta falls below the CLS threshold. Per vooronderzoek §7 and [MDN : @font-face/font-display](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display) (verified prior round, included in SOURCES.md).

5. **Speculation Rules prerendering `/logout`** : `prerender` fully executes the destination's JavaScript, so a hover-triggered `/logout` link silently signs the user out. Fix : exclude destructive URLs via `where: { not: { href_matches: "/logout" } }` and similar patterns for `?add-to-cart=`, `[rel~=nofollow]`, sign-in flows. Per [MDN : Speculation Rules API](https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API) (verified 2026-05-19).

6. **`content-visibility: auto` without `contain-intrinsic-size`** : off-screen content collapses to 0 height, then re-expands on viewport-approach, causing scrollbar jitter and CLS spikes. Fix : ALWAYS pair with `contain-intrinsic-size: auto Npx` matching the expected rendered size. Per vooronderzoek §7.

7. **`requestAnimationFrame` to defer post-handler work** : `rAF` runs BEFORE the next paint, so heavy compute inside it inflates presentation delay and worsens INP. Fix : if work must run after paint, use `requestAnimationFrame(() => setTimeout(work, 0))` so the work is pushed past the paint commit. Per [web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19).

8. **Single `await yieldToMain()` at top of long task** : yielding once does not split work ; the remaining synchronous code is still one long task. Fix : yield BETWEEN sub-tasks in a loop, ideally guarded by `isInputPending()` where supported. Per [MDN : Scheduler.yield](https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield) (verified 2026-05-19).

9. **Assuming `scheduler.yield()` is Baseline** : Firefox and Safari do not implement it ; shipping unguarded calls throws `TypeError: scheduler.yield is not a function`. Fix : feature-detect `globalThis.scheduler?.yield` and fall back to `scheduler.postTask` then `setTimeout(_, 0)`. Per [MDN : Scheduler.yield](https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield) (verified 2026-05-19).

10. **Speculation Rules with default eagerness on `where` rules** : the default is `conservative` (pointerdown only), so authors expecting hover-based prerender see no benefit. Fix : set `"eagerness": "moderate"` explicitly on `where` rules unless user-intent confirmation is critical. Per [developer.chrome.com : speculation-rules-improvements](https://developer.chrome.com/blog/speculation-rules-improvements) (verified 2026-05-19).

11. **Observing only `longtask` for INP debugging** : Long Tasks API misses cumulative sub-50 ms blocking and provides no script attribution. Fix : observe `long-animation-frame` and use `scripts[]` attribution to identify the source handler. Per [developer.chrome.com : long-animation-frames](https://developer.chrome.com/docs/web-platform/long-animation-frames) (verified 2026-05-19).

12. **Client-side HTML rendering on critical interaction path** : the browser does not yield while parsing rendered HTML, so a click-triggered `innerHTML = bigHtml` blocks paint until parse completes. Fix : stream HTML server-side, use `DocumentFragment` with incremental insertion, or move parse off-thread via a Worker. Per [web.dev : Optimize INP](https://web.dev/articles/optimize-inp) (verified 2026-05-19).

## 9. Sources Used

| # | Source URL | Verified | Extracted |
|---|------------|----------|-----------|
| 1 | https://web.dev/articles/vitals | 2026-05-19 | LCP/INP/CLS thresholds (2.5s/200ms/0.1), 75th-percentile rule, INP replacing FID 2024, TTFB/FCP/TBT support roles |
| 2 | https://web.dev/articles/inp | 2026-05-19 | INP definition, 3 phases, click/tap/keyboard scope, 200/500 thresholds, 75th percentile with one-outlier filter |
| 3 | https://web.dev/articles/optimize-inp | 2026-05-19 | input-delay/processing/presentation tactics, rAF + setTimeout deferral pattern, content-visibility for off-screen |
| 4 | https://web.dev/articles/lcp | 2026-05-19 | LCP definition, qualifying elements (img/image/video/text/bg-image), 2.5s/4.0s thresholds |
| 5 | https://web.dev/articles/cls | 2026-05-19 | CLS formula (impact*distance), 0.1/0.25 thresholds, session window (5s/1s), excluded-input 500ms |
| 6 | https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield | 2026-05-19 | yield() signature, Limited availability (Chromium-only), priority inheritance, boosted queue ordering |
| 7 | https://developer.mozilla.org/en-US/docs/Web/API/Speculation_Rules_API | 2026-05-19 | script type=speculationrules JSON shape, prefetch vs prerender, urls vs where patterns, destructive URL list, Limited availability |
| 8 | https://developer.mozilla.org/en-US/docs/Web/API/LongAnimationFrameTiming | 2026-05-19 | LoAF entry properties (duration, renderStart, blockingDuration, scripts[]), PerformanceScriptTiming, INP attribution |
| 9 | https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver | 2026-05-19 | constructor, observe/disconnect/takeRecords, entryTypes array, buffered:true, supportedEntryTypes |
| 10 | https://developer.chrome.com/docs/web-platform/prerender-pages | 2026-05-19 | four eagerness values, defaults (immediate for urls, conservative for where), Chrome budget caps |
| 11 | https://developer.chrome.com/blog/speculation-rules-improvements | 2026-05-19 | eagerness trigger details (200ms hover for moderate, pointerdown for conservative), recommended JSON |
| 12 | https://developer.chrome.com/docs/web-platform/long-animation-frames | 2026-05-19 | LoAF replaces longtask, blockingDuration math, Chrome 123+, Firefox/Safari gap |
| 13 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/fetchpriority | 2026-05-19 | high/low/auto values, applies to img/link/script, Baseline 2024 (October), use-sparingly warning |
| 14 | https://developer.mozilla.org/en-US/docs/Web/API/LargestContentfulPaint | 2026-05-19 | renderTime/loadTime/size/id/url/element/paintTime/presentationTime, qualifying elements, Newly available 2025 |
| 15 | https://developer.mozilla.org/en-US/docs/Web/API/LayoutShift | 2026-05-19 | value/hadRecentInput/sources, LayoutShiftAttribution (node, currentRect, previousRect), 500ms input-exclusion |

### New sources to add to SOURCES.md

| Source | URL | Type | Last Verified |
|--------|-----|------|---------------|
| web.dev : LCP | https://web.dev/articles/lcp | Tutorial | 2026-05-19 |
| web.dev : CLS | https://web.dev/articles/cls | Tutorial | 2026-05-19 |
| web.dev : INP | https://web.dev/articles/inp | Tutorial | 2026-05-19 |
| MDN : LongAnimationFrameTiming | https://developer.mozilla.org/en-US/docs/Web/API/LongAnimationFrameTiming | Reference | 2026-05-19 |
| MDN : PerformanceObserver | https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver | Reference | 2026-05-19 |
| MDN : LargestContentfulPaint | https://developer.mozilla.org/en-US/docs/Web/API/LargestContentfulPaint | Reference | 2026-05-19 |
| MDN : LayoutShift | https://developer.mozilla.org/en-US/docs/Web/API/LayoutShift | Reference | 2026-05-19 |
| MDN : fetchpriority | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/fetchpriority | Reference | 2026-05-19 |
| developer.chrome.com : prerender-pages | https://developer.chrome.com/docs/web-platform/prerender-pages | Tutorial | 2026-05-19 |
| developer.chrome.com : speculation-rules-improvements | https://developer.chrome.com/blog/speculation-rules-improvements | Tutorial | 2026-05-19 |
| developer.chrome.com : long-animation-frames | https://developer.chrome.com/docs/web-platform/long-animation-frames | Tutorial | 2026-05-19 |
