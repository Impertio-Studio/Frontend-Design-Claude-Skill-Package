# Topic Research : frontend-syntax-js-es2024-ts-dom

## Status

Drilled to close §14 verification gaps in `vooronderzoek-frontend.md` for the combined ES2024 + TypeScript-DOM surface that backs `frontend-syntax-js-es2024-ts-dom` (batch 4). Specifically resolved gap 5 (`scheduler.yield`) and gap 6 (Iterator helpers) and verified every supporting API used by the masterplan scope. All claims here are sourced by `[MDN : ...]` or `[TC39 : ...]` citations with verification dates. WebFetch round count : 12.

The skill targets evergreen-2026, so Baseline status is recorded per feature; some surfaces (Iterator helpers, `scheduler.yield`, import attributes) are NOT uniformly Baseline yet and MUST be feature-detected in produced code samples.

## 1. ES2024 DOM-relevant features

The 2024 edition of ECMAScript graduated several features that materially simplify DOM-side authoring. The masterplan binds the skill to four primaries plus four supporting features ; this section verifies all eight.

**`Object.groupBy(items, callbackFn)`** : returns a `null`-prototype object whose keys are the values returned by `callbackFn` (coerced to property keys), and whose values are arrays of the matching items. Signature : `Object.groupBy(items, (element, index) => key)`. The `index` parameter is the iteration index, not an array index, so this works on any iterable. Baseline : Newly available since March 2024 ([MDN : Object.groupBy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/groupBy) verified 2026-05-19). Use over `Map.groupBy` whenever the group key is a string or symbol ; switch to `Map.groupBy` only when the key is an arbitrary object reference.

**`Promise.withResolvers()`** : returns `{ promise, resolve, reject }` so the resolver functions live in the same scope as the promise itself. Baseline : Newly available since March 2024 ([MDN : Promise.withResolvers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/withResolvers) verified 2026-05-19). Use for event-driven flows where you cannot resolve inside the `new Promise(...)` executor body : streams, queues, request-response patterns, dialog-close awaits. The classic "deferred" workaround that hoisted `resolve`/`reject` out of an executor closure is now an anti-pattern.

**`structuredClone(value, options?)`** : deep-clones a value using the structured-clone algorithm. Supports `Date`, `Map`, `Set`, `ArrayBuffer`, typed arrays, `Blob`, `File`, `RegExp`, `Error`, cycles. The `options.transfer` array moves transferable objects (`ArrayBuffer`, `ImageBitmap`, `MessagePort`, `OffscreenCanvas`, `ReadableStream`) to the clone, detaching them in the source. Functions, DOM nodes (in most engines), getters and setters, and class-instance prototypes do NOT clone : `DataCloneError` is thrown. Baseline : Widely available since March 2022 ([MDN : structuredClone](https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone) verified 2026-05-19). Replaces every `JSON.parse(JSON.stringify(x))` use except for the narrow case of "I want to drop functions and Dates intentionally".

**Top-level `await` in modules** : permitted at the top level of any ES module (`<script type="module">`, ESM `.mjs`, native `import`-resolvable file). Forbidden in classic scripts and CommonJS. Baseline : Widely available since March 2022 (per the await-operator MDN page verified 2026-05-19). Sibling modules continue to load in parallel ; only the importing module pauses. Practical implication for DOM authoring : top-level `await fetch(...)` for config or feature flags before the first paint is now portable.

**RegExp `v` flag (unicodeSets)** : ES2024 upgrade to the `u` flag. Adds set-intersection (`&&`), set-subtraction (`--`), and string-property `\p{...}` escapes such as `\p{RGI_Emoji}` that match multi-code-point sequences. Mixing `u` and `v` throws a `SyntaxError` at compile time. Baseline : Widely available since September 2023 ([MDN : RegExp v flag](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicodeSets) verified 2026-05-19). Use whenever you parse emoji or script-mixed input that the older `u` flag mishandled.

**Array `findLast` / `findLastIndex`** : reverse-search without reversing the array. Baseline : Widely available since August 2022 ([MDN : Array.prototype.findLast](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findLast) verified 2026-05-19). Preferred over `[...arr].reverse().find(fn)` because it avoids the intermediate array allocation.

**Well-formed JSON** (`String.prototype.isWellFormed`, `String.prototype.toWellFormed`) : detects and replaces lone UTF-16 surrogates. Baseline : Widely available since October 2023 ([MDN : String.prototype.isWellFormed](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/isWellFormed) verified 2026-05-19). Prevents `URIError: URI malformed` when calling `encodeURI()` on user input that may contain unpaired surrogates. The TC39 "Well-formed JSON.stringify" proposal landed in 2019 and provides escape behavior for lone surrogates ([TC39 : finished-proposals](https://github.com/tc39/proposals/blob/main/finished-proposals.md) verified 2026-05-19).

**Map.groupBy** : the `Map`-keyed sibling of `Object.groupBy`. Same signature, same Baseline (March 2024). The `Object` variant coerces the callback return to a string ; the `Map` variant keeps object identity so two distinct objects with identical shape produce two distinct groups. The TC39 "Array Grouping" proposal that wraps both reached Stage 4 in 2024 (verified via finished-proposals).

## 2. Iterator helpers (Baseline status)

Iterator helpers extend `Iterator.prototype` with chainable, lazy operations on any iterable. The proposal "Sync Iterator helpers" reached TC39 Stage 4 in 2025 ([TC39 : finished-proposals](https://github.com/tc39/proposals/blob/main/finished-proposals.md) verified 2026-05-19).

**Available methods** ([MDN : Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Iterator) verified 2026-05-19) :

| Method | Signature | Returns |
|--------|-----------|---------|
| `.map(fn)` | `(value, index) => any` | Iterator helper (lazy) |
| `.filter(fn)` | `(value, index) => boolean` | Iterator helper (lazy) |
| `.flatMap(fn)` | `(value, index) => iterable` | Iterator helper (lazy) |
| `.take(count)` | `count : number` | Iterator helper (lazy) |
| `.drop(count)` | `count : number` | Iterator helper (lazy) |
| `.reduce(fn, init?)` | `(acc, value, index) => any` | Single value |
| `.toArray()` | none | Array |
| `.forEach(fn)` | `(value, index) => void` | `undefined` |
| `.find(fn)` | `(value, index) => boolean` | Element or `undefined` |
| `.some(fn)` | `(value, index) => boolean` | boolean |
| `.every(fn)` | `(value, index) => boolean` | boolean |

Plus static helpers `Iterator.from(x)` (wrap any iterable in a real Iterator), and per TC39 finished-proposals : `Iterator.concat`, `Iterator.zip`, `Iterator.zipKeyed`.

**Baseline status** : MDN currently labels the Iterator interface page as "Baseline Widely available *" with an asterisked note that "Some parts of this feature may have varying levels of support". That asterisk applies precisely to the helper methods. Per TC39 the proposal finalized in 2025 ; browser-vendor implementations rolled out across Chrome, Safari, and Firefox during 2024-2025. As of 2026-05-19 the helper methods are practically available on evergreen-2026 baselines but the MDN page does not yet declare a single "Widely available" date for the helpers as a group. Treat them as **partial Baseline 2025** : safe in evergreen-only authoring, but feature-detect when targeting older mobile WebViews.

**Feature detection** :

```js
const hasIteratorHelpers =
  typeof Iterator !== "undefined" &&
  typeof Iterator.prototype.map === "function";
```

**Laziness rule** : `.map`, `.filter`, `.flatMap`, `.take`, `.drop` return new iterator helpers ; nothing iterates until a terminal method (`.toArray`, `.reduce`, `.forEach`, `.find`, `.some`, `.every`) or an explicit `for...of` runs. This is the central performance advantage : `iter.filter(...).map(...).take(10).toArray()` only iterates as many source elements as `.take(10)` needs.

**Shared-source caveat** : iterator helpers do NOT fork the underlying source. Calling `.drop(0)` does not copy the iterator ; `it.next()` and the helper's `next()` share state. Authors used to RxJS-style "two independent subscriptions" will get the wrong mental model.

**Polyfill** : `core-js` provides the helpers, and `es-iterator-helpers` is the es-shims package. Bundle only when supporting non-evergreen targets.

## 3. scheduler.yield + alternatives

`scheduler.yield()` ([MDN : Scheduler.yield](https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield) verified 2026-05-19) returns a `Promise<undefined>` that resolves after the browser yields to the event loop. Signature : `scheduler.yield()` takes no parameters in the standard form ; a future `{ signal, priority }` options bag is on the WICG roadmap but is not yet stable per MDN.

**Baseline status** : NOT Baseline. The MDN page explicitly states "This feature is not Baseline because it does not work in some of the most widely-used browsers". As of 2026-05-19, Chromium shipped `scheduler.yield()` in stable channels ; Safari and Firefox have not yet shipped. Treat as **Limited availability** and ALWAYS feature-detect before use.

**Priority inheritance** : when called inside a `scheduler.postTask(fn, { priority })` task, the resumed continuation inherits that task's priority. Outside `postTask`, the default priority is `"user-visible"`. The resumed task is enqueued in a *boosted* queue ahead of equally-prioritized `postTask` calls, so `scheduler.yield()` is the right tool for "I want to stay responsive but resume ASAP", whereas `setTimeout(fn, 0)` enqueues at the end of the macrotask queue with no priority boost.

**Comparison table** :

| API | Resumes via | Priority | Best for |
|-----|-------------|----------|----------|
| `await scheduler.yield()` | Boosted task queue at current/`user-visible` priority | Inherits or `user-visible` | Splitting long tasks ; staying responsive to input without giving up scheduling order |
| `setTimeout(fn, 0)` | Macrotask queue tail | None (low) | Fallback when `scheduler` is missing |
| `await new Promise(r => setTimeout(r, 0))` | Macrotask queue tail | None | Same fallback in async/await form |
| `requestAnimationFrame(fn)` | Before next paint | Tied to frame | Visual updates only ; NOT a generic yield |
| `requestIdleCallback(fn)` | When browser is idle | Lowest | Truly deferrable work that may never run |
| `navigator.scheduling.isInputPending()` | Synchronous check | n/a | Decide *whether* to yield mid-loop |

**Recommended pattern** (from [developer.chrome.com : scheduler.yield origin trial](https://developer.chrome.com/blog/introducing-scheduler-yield-origin-trial) verified 2026-05-19) :

```js
async function yieldToMain() {
  if ("scheduler" in globalThis && "yield" in globalThis.scheduler) {
    return globalThis.scheduler.yield();
  }
  return new Promise((r) => setTimeout(r, 0));
}

async function processChunks(items) {
  for (const item of items) {
    work(item);
    if (navigator.scheduling?.isInputPending?.()) {
      await yieldToMain();
    }
  }
}
```

**Worker availability** : MDN confirms `scheduler.yield()` is available in Web Workers in browsers that ship it on the Window context.

**Anti-pattern** : `await new Promise(requestAnimationFrame)` as a generic yield. It only resumes at the next frame boundary (~16 ms) which is far too coarse for breaking up tasks aimed at the INP budget of 200 ms. Use `scheduler.yield()` or `setTimeout(_, 0)` for INP work ; reserve `requestAnimationFrame` for visual updates.

## 4. TypeScript DOM strict patterns

TypeScript's `lib.dom.d.ts` ships with the compiler and reflects the WebIDL surface ; strict-mode behavior depends on tsconfig flags. The masterplan binds four flags as required ; this section verifies each ([TypeScript : tsconfig reference](https://www.typescriptlang.org/tsconfig/) verified 2026-05-19).

**`strict: true`** enables a bundle that includes `strictNullChecks`, `noImplicitAny`, `strictFunctionTypes`, `strictBindCallApply`, `strictPropertyInitialization`, `useUnknownInCatchVariables`, and `alwaysStrict`. For DOM code the dominant effect is `strictNullChecks` : every nullable return (`getElementById`, `querySelector`, `closest`, `parentElement`) MUST be narrowed before use.

**`noUncheckedIndexedAccess: true`** adds `| undefined` to every indexed access. Critical for DOM-adjacent collections : `form.elements[0]` becomes `Element | undefined` ; `dataset.foo` becomes `string | undefined`. Without this flag, `arr[i]` falsely typed as `T` is the single biggest source of runtime "cannot read property of undefined" bugs.

**`noImplicitOverride: true`** requires the `override` keyword whenever a subclass method shadows a parent method. For Web Components extending `HTMLElement`, this catches typos like `disconnectCallback` (silent dead method) versus the spec-defined `disconnectedCallback`.

**`exactOptionalPropertyTypes: true`** distinguishes "property missing" from "property set to `undefined`". For `interface Opts { foo?: string }` the property cannot be set to `undefined` explicitly under this flag ; only omitted or set to a real `string`. Prevents the common mistake of clearing a value with `obj.foo = undefined` when the intent was deletion.

**Narrowing patterns** ([TypeScript : Narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html) verified 2026-05-19) :

1. **Null narrow** :
   ```ts
   const el = document.getElementById("submit");
   if (!el) return;
   // el is HTMLElement here, not HTMLElement | null
   ```

2. **`instanceof` narrow on `event.target`** :
   ```ts
   form.addEventListener("input", (e) => {
     if (!(e.target instanceof HTMLInputElement)) return;
     console.log(e.target.value); // typed as string
   });
   ```

3. **Tagged-type narrow with the `currentTarget` generic** :
   ```ts
   const input = document.querySelector<HTMLInputElement>("input[name=q]");
   if (!input) return;
   input.addEventListener("input", function (e) {
     // `this` and `e.currentTarget` both typed as HTMLInputElement
     console.log(this.value);
   });
   ```

4. **User-defined type guard for repeated checks** :
   ```ts
   function isCheckbox(el: EventTarget | null): el is HTMLInputElement {
     return el instanceof HTMLInputElement && el.type === "checkbox";
   }
   ```

**Why `as HTMLInputElement` is a code smell** : `as` casts are unchecked. If a different element ever dispatches the event (delegated handlers, event-retargeting through shadow DOM, programmatic dispatch from another handler), the cast silently lies and a runtime `TypeError: cannot read .value of HTMLDivElement` follows. `instanceof` checks at runtime, narrows at compile time, and survives delegation. The single legitimate use of `as` is when you have a stronger out-of-band guarantee (e.g. you just created the element with `document.createElement('input')`) and even there `document.createElement<'input'>("input")` produces `HTMLInputElement` without any cast.

**`EventTarget` typing pitfall** : `Event.target` and `Event.currentTarget` are typed as `EventTarget | null` in `lib.dom.d.ts`. `EventTarget` has only `addEventListener` / `removeEventListener` / `dispatchEvent`. To access `.value`, `.checked`, `.dataset`, you MUST narrow. Module augmentation lets you tighten event types :

```ts
declare global {
  interface HTMLElementEventMap {
    "app:open-modal": CustomEvent<{ id: string }>;
  }
}
```

After augmentation, `el.addEventListener("app:open-modal", (e) => e.detail.id)` is typed correctly without casts.

## 5. lib.dom.d.ts quirks

The `lib.dom.d.ts` definitions are generated from WebIDL plus hand-curated TypeScript overloads. Several quirks bite frontend authors regularly.

**`Document.querySelector` overload set** ([MDN : Document.querySelector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector) verified 2026-05-19, plus lib.dom.d.ts inspection) :

```ts
querySelector<K extends keyof HTMLElementTagNameMap>(selectors: K): HTMLElementTagNameMap[K] | null;
querySelector<K extends keyof SVGElementTagNameMap>(selectors: K): SVGElementTagNameMap[K] | null;
querySelector<E extends Element = Element>(selectors: string): E | null;
```

This means `querySelector("button")` returns `HTMLButtonElement | null` (literal-tag overload), but `querySelector(".submit")` falls back to `Element | null`. The escape hatch is the generic : `querySelector<HTMLButtonElement>(".submit")` returns the desired type. Authors who write `querySelector("#x")` and expect `HTMLInputElement` get bitten because `#x` is not a recognized tag-name literal.

**`getElementById` returns `HTMLElement | null`**, not `Element | null` ; the spec stays on `Element` but the TypeScript override narrows to `HTMLElement` since SVG-by-id is unusual. To get a specific subtype, narrow via `instanceof` or generic-typed `querySelector` instead.

**`Element.shadowRoot` typing** : `ShadowRoot | null` ([MDN : Element.shadowRoot](https://developer.mozilla.org/en-US/docs/Web/API/Element/shadowRoot) verified 2026-05-19). Returns `null` when no shadow root is attached, OR when the shadow root was attached with `mode: "closed"` from outside the component, OR for built-in elements with user-agent shadow roots (`<input>`, `<img>`, `<video>`). Closed-mode shadow roots remain reachable from inside the component that created them via a captured reference ; the property only hides the root from external observers.

**`HTMLButtonElement.popoverTargetElement`** ([MDN : button element](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/button) verified 2026-05-19) : IDL property that reflects the `popovertarget` attribute as a resolved `Element | null`. Typed as `Element | null` in `lib.dom.d.ts`. Pair with `popoverTargetAction: "show" | "hide" | "toggle"`. Baseline : Widely available since 2024 alongside the Popover API.

**`HTMLElement.dataset`** : `DOMStringMap`, indexed access returns `string | undefined` only under `noUncheckedIndexedAccess`. Without that flag, `el.dataset.foo` is typed as `string` even though it's `undefined` at runtime when the attribute is missing. This is the canonical reason to enable the flag.

**`addEventListener` generic vs string signature** : the typed overload `addEventListener<K extends keyof HTMLElementEventMap>(type: K, listener: (this: HTMLElement, ev: HTMLElementEventMap[K]) => void)` only kicks in for known event names. For custom events you augment `HTMLElementEventMap` (see §4) or fall back to `addEventListener(type: string, listener: EventListenerOrEventListenerObject)` which forces a manual narrow inside the handler.

**`EventTarget` has no `addEventListener` overload that ties listener-`this` to the receiver** : when you assign a method as a listener (`el.addEventListener("click", this.handleClick)`), the listener's `this` is the element, not the original object. The TypeScript compiler does not warn unless you use the `EventListener` interface explicitly. Prefer `addEventListener("click", (e) => this.handleClick(e))` or `this.handleClick = this.handleClick.bind(this)` in the constructor.

## 6. Module specifiers + import attributes

ECMAScript "Import Attributes" reached TC39 Stage 4 in 2025 ([TC39 : finished-proposals](https://github.com/tc39/proposals/blob/main/finished-proposals.md) verified 2026-05-19), replacing the earlier "Import Assertions" proposal that used the `assert` keyword. The `assert` syntax shipped in some browsers but is being phased out ; new code MUST use `with`.

**Static form** ([MDN : import statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) verified 2026-05-19) :

```js
import config from "./config.json" with { type: "json" };
import styles from "./styles.css" with { type: "css" };
```

**Dynamic form** ([MDN : import() expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import) verified 2026-05-19) :

```js
const { default: config } = await import("./config.json", {
  with: { type: "json" },
});
```

**Why required** : a server-side or in-flight MIME-type mismatch can otherwise cause a `.json` URL to be interpreted as JavaScript and executed. The `type: "json"` attribute forces the host to refuse the module if the response is anything other than JSON. The same logic applies to `type: "css"` for CSS Module Scripts.

**Baseline status** : per MDN the `with` form is "available across browsers since May 2018 *" with the asterisked note about parts having varying support. The `assert` form is deprecated. Chromium and Safari ship `with { type: "json" }` ; Firefox shipped during 2024-2025. As of 2026-05-19, treat as **Baseline 2025** but verify against `web-features` before assuming production-safe in older mobile WebViews.

**Top-level `await` interaction** : modules that use `await import(..., { with: ... })` at the top level work in any ES module context. Forbidden in classic `<script>` (no `type="module"`) and in CommonJS. The "Top-Level Await" proposal landed in TC39 in 2020 and is Baseline Widely Available since March 2022 ([MDN : await operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) verified 2026-05-19).

**Practical guidance** : co-locate small JSON config (feature flags, design tokens) next to the module that consumes it, and load via static `import ... with { type: "json" }`. For dynamic / per-route config, use the `import()` form with the same attribute. Never `fetch(...).then(r => r.json())` for static-known config when import attributes work : the import path is statically analyzable by bundlers and the browser, the fetch path is not.

## 7. Decision matrix : which API for which task

| Task | API | Rationale |
|------|-----|-----------|
| Group a list of items by string key | `Object.groupBy(items, fn)` | Baseline 2024 ; null-prototype object ; cleanest signature |
| Group by arbitrary object key | `Map.groupBy(items, fn)` | Preserves key identity ; only `Map` supports non-string keys |
| Deep clone DOM-cloneable data (`Date`, `Map`, `Blob`, typed arrays) | `structuredClone(value)` | Handles cycles ; preserves types |
| Transfer ownership of `ArrayBuffer` / `OffscreenCanvas` | `structuredClone(value, { transfer: [buf] })` | Avoids copy ; detaches source |
| Resolve a promise from outside its executor | `Promise.withResolvers()` | Replaces hand-rolled "deferred" pattern |
| Find last matching element in array | `array.findLast(fn)` | No intermediate reversed array |
| Detect lone surrogates before `encodeURI` | `str.isWellFormed()` | Avoids `URIError: URI malformed` |
| Process emoji / multi-codepoint Unicode in regex | `/.../v` (unicodeSets) | Set notation ; `\p{RGI_Emoji}` strings |
| Chain transformations on iterable without intermediate arrays | `Iterator.from(src).filter(...).map(...).take(n).toArray()` | Lazy ; bounded memory |
| Break up a long task to keep INP responsive | `await scheduler.yield()` with `setTimeout(_, 0)` fallback | Inherits priority ; boosted resume |
| Narrow `event.target` to concrete element type | `if (e.target instanceof HTMLInputElement) ...` | Runtime-safe under delegation |
| Type custom events without casts | Augment `HTMLElementEventMap` | First-class typed `addEventListener` |
| Load JSON config at parse time | `import x from "./x.json" with { type: "json" }` | Static-analyzable ; MIME-type safe |

## 8. Anti-patterns

1. **`JSON.parse(JSON.stringify(x))` for deep clone** : drops `Date` (becomes ISO string), `Map`, `Set`, `undefined`, functions, `Symbol`-keyed props ; crashes on cycles. Use `structuredClone(x)`.

2. **Hand-rolled "deferred" pattern** :
   ```js
   let resolve;
   const p = new Promise((r) => { resolve = r; });
   ```
   Leaks the resolver out of the executor closure. Use `const { promise, resolve, reject } = Promise.withResolvers();`.

3. **`reduce` chains for grouping** :
   ```js
   items.reduce((acc, item) => {
     (acc[item.type] ||= []).push(item);
     return acc;
   }, {});
   ```
   Five lines of accumulator boilerplate for what `Object.groupBy(items, (x) => x.type)` does in one.

4. **Blind `as` cast on `event.target`** :
   ```ts
   const value = (e.target as HTMLInputElement).value;
   ```
   The cast lies if delegation or programmatic dispatch ever changes the target type. Use `instanceof` narrowing.

5. **Non-null assertion on `getElementById`** :
   ```ts
   document.getElementById("submit")!.click();
   ```
   The `!` operator suppresses the compiler warning ; the runtime throws `TypeError: Cannot read properties of null` when the ID is missing. Always narrow with `if (!el) return;` first.

6. **Iterator helpers without feature detection** : as of 2026-05-19 the methods are partial-Baseline-2025. Code that ships to older WebViews must either polyfill (`core-js` / `es-iterator-helpers`) or feature-detect (`typeof Iterator?.prototype?.map === "function"`).

7. **`requestAnimationFrame` as a generic yield** : `await new Promise(requestAnimationFrame)` only resumes at frame boundaries (~16 ms). Use `scheduler.yield()` (with `setTimeout(_, 0)` fallback) for INP-sensitive task chunking.

8. **`querySelector(".x")` typed as `HTMLInputElement`** : the literal-tag overload only matches tag-name selectors. Use the generic : `querySelector<HTMLInputElement>(".x")`.

9. **`for (const x of items.values())` on a plain array** when `for (const x of items)` works : not wrong, but the `.values()` form only pays off when you then chain Iterator helpers. Bare iteration over arrays should use the array directly.

10. **`assert { type: "json" }` (Import Assertions)** : the older keyword is deprecated. Use `with { type: "json" }` (Import Attributes).

## 9. Sources Used

| # | URL | Verified | Extracted |
|---|-----|----------|-----------|
| 1 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Iterator | 2026-05-19 | Iterator helper methods, Baseline asterisk, laziness, polyfills |
| 2 | https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield | 2026-05-19 | signature, Promise<undefined> return, NOT Baseline, priority inheritance, Worker availability |
| 3 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/groupBy | 2026-05-19 | signature, null-prototype object, Baseline March 2024, Map.groupBy contrast |
| 4 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/withResolvers | 2026-05-19 | signature, return shape, Baseline March 2024, stream example |
| 5 | https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone | 2026-05-19 | signature, transferables list, Baseline March 2022, DataCloneError |
| 6 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicodeSets | 2026-05-19 | v flag, set notation, properties of strings, Baseline Sept 2023 |
| 7 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findLast | 2026-05-19 | findLast / findLastIndex signatures, Baseline August 2022 |
| 8 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import | 2026-05-19 | dynamic import attributes, with-vs-assert, json modules |
| 9 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import | 2026-05-19 | static import attributes, top-level await context |
| 10 | https://github.com/tc39/proposals/blob/main/finished-proposals.md | 2026-05-19 | Stage 4 status of Iterator helpers (2025), Import Attributes (2025), Promise.withResolvers (2024), Array Grouping (2024), v flag (2024) |
| 11 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/isWellFormed | 2026-05-19 | signature, lone-surrogate use case, Baseline October 2023 |
| 12 | https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector | 2026-05-19 | return type Element \| null, lib.dom.d.ts overload set |
| 13 | https://www.typescriptlang.org/docs/handbook/2/narrowing.html | 2026-05-19 | instanceof / typeof / in / equality / type-guards / discriminated unions |
| 14 | https://www.typescriptlang.org/tsconfig/ | 2026-05-19 | strict / noUncheckedIndexedAccess / noImplicitOverride / exactOptionalPropertyTypes definitions |
| 15 | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await | 2026-05-19 | top-level await Baseline Widely Available, classic-script / CommonJS forbidden |
| 16 | https://developer.chrome.com/blog/introducing-scheduler-yield-origin-trial | 2026-05-19 | yieldToMain feature-detect pattern, INP-targeted use case |
| 17 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/button | 2026-05-19 | popovertarget / popovertargetaction attributes, popoverTargetElement IDL |
| 18 | https://developer.mozilla.org/en-US/docs/Web/API/Element/shadowRoot | 2026-05-19 | ShadowRoot \| null, closed-mode hides external access, Baseline January 2020 |
