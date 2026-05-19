# Topic Research : frontend-impl-popover-dialog-anchor

## Status

This document drills the four sub-topics that the skill `frontend-impl-popover-dialog-anchor` must cover : the `<dialog>` element, the Popover API, CSS Anchor Positioning, and the `@starting-style` + `transition-behavior: allow-discrete` + `overlay` recipe required for enter/exit animations on top-layer elements. The drill is mandated by `vooronderzoek-frontend.md` §14 verification gap #4 (the `@starting-style` + `transition-behavior` syntax was not WebFetched in the vooronderzoek round) and §13 SPLIT+MERGE decision D-R09 that collapsed all four areas into one cohesive skill because they share the same mental model (top layer, position-anchor, discrete transitions) and authors always need them together.

All claims below are verified against approved primary sources (MDN, WHATWG HTML Living Standard) via WebFetch on 2026-05-19. Inline citations use the format `(verified 2026-05-19)`. The §9 source table at the end lists every URL consulted.

---

## 1. Dialog element + showModal vs show

The HTML `<dialog>` element ships three primary methods : `showModal()`, `show()`, and `close(returnValue?)` ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19)).

`showModal()` opens the dialog as **modal**. It promotes the dialog into the browser's top layer, automatically makes the rest of the document inert (the user agent applies the `inert` algorithm to siblings), enables Escape-to-close, and causes the `::backdrop` pseudo-element to render behind the dialog. Initial focus is moved into the dialog's first focusable descendant unless `autofocus` is set explicitly on a specific child. The `close` event fires when the dialog dismisses for any reason (script `close(value)`, form submission with `method="dialog"`, Escape key, or light dismiss when `closedby="any"` is set).

`show()` opens the dialog as **non-modal**. The dialog renders in normal flow (not the top layer), the rest of the page remains interactive, no `::backdrop` is shown, and Escape does NOT close by default. The page does not become inert. Authors who need a non-modal sheet should generally prefer the Popover API instead, because popovers ship light-dismiss, top-layer placement, and anchor associations that `dialog.show()` does not.

`close(returnValue?)` closes the dialog regardless of how it was opened. The optional `returnValue` argument is stored on `dialog.returnValue` and is the canonical way to pass a result from the dialog back to its caller (e.g. "OK" vs "cancel"). A `<form method="dialog">` inside the dialog will submit with the submit-button's `value` attribute as the `returnValue`, which is the declarative equivalent.

The `open` attribute is a **boolean reflection** of the dialog's open state but with one caveat : dialogs opened via `open` (HTML attribute) are non-modal and behave like `show()`. The attribute is NOT recommended for dynamic toggling; use `show()`/`showModal()`/`close()` from script instead ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19)).

`<dialog>` is **Baseline Widely Available** since March 2022 ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19)). The `closedby` attribute (drilled in §5 below) is more recent and gates differently per browser.

Anti-pattern flagged inline by MDN : NEVER set `tabindex` on the `<dialog>` element itself; the dialog is a container, focus belongs on its interactive descendants. Authors who add `tabindex="0"` or `tabindex="-1"` to `<dialog>` confuse the focus model and break assistive technology traversal.

---

## 2. Popover API + popovertarget

The Popover API is **Baseline 2025**, Newly Available since January 2025 ([MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19)). It exposes a global HTML attribute `popover` and a pair of trigger attributes `popovertarget` / `popovertargetaction`, plus three element methods.

The `popover` attribute accepts three enumerated states ([WHATWG HTML : popover](https://html.spec.whatwg.org/multipage/popover.html) (verified 2026-05-19)) :

- `auto` : "Closes other popovers when opened; has light dismiss and responds to close requests" (WHATWG). Light dismiss closes the popover when the user clicks outside it OR presses Escape OR opens another auto-popover that is not an ancestor of the current one. Only one chain of auto-popovers can be open at a time; opening a new auto-popover closes any open auto-popovers down to the common ancestor.
- `manual` : "Does not close other popovers; does not light dismiss or respond to close requests" (WHATWG). The author MUST close `manual` popovers by calling `.hidePopover()`, `.togglePopover()`, or by clicking a control with `popovertargetaction="hide"`.
- `hint` : "Closes other hint popovers when opened, but not other auto popovers; has light dismiss and responds to close requests" (WHATWG). `hint` maintains a separate showing list from `auto`; the spec dictates that when an auto popover is shown, hint popovers close first, then auto popovers down to the ancestor. `hint` is intended for tooltips and ephemeral hint surfaces.

Trigger buttons declare the relationship with `popovertarget="<id>"` (the id of the popover element) and the optional `popovertargetaction` attribute, which accepts `show`, `hide`, or `toggle` (default `toggle`) ([MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19)) :

```html
<button popovertarget="settings" popovertargetaction="toggle">Settings</button>
<div id="settings" popover="auto">
  <h2>Settings</h2>
  <button popovertarget="settings" popovertargetaction="hide">Close</button>
</div>
```

The script API mirrors this with three methods on every `HTMLElement` : `showPopover()`, `hidePopover()`, `togglePopover(force?)`. Both the declarative and script paths fire a `ToggleEvent` named `beforetoggle` (cancelable on show) and `toggle` (not cancelable) with `oldState` and `newState` set to `"closed"` or `"open"` ([MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19)).

Popovers are **ALWAYS non-modal** by definition. The rest of the page remains interactive while a popover is open. For modal behavior, use `<dialog>.showModal()`. The Popover API and the `<dialog>` element are complementary, not interchangeable.

The CSS pseudo-class `:popover-open` matches a popover element in the showing state ([MDN : `:popover-open`](https://developer.mozilla.org/en-US/docs/Web/CSS/:popover-open) (verified 2026-05-19), Baseline 2024 since April 2024). It is the hook that authors use to style or transition open-state appearance.

---

## 3. Anchor positioning + position-try-fallbacks

CSS Anchor Positioning lets authors tether a positioned element to a separate "anchor" element with pure CSS, no JavaScript layout math, no `getBoundingClientRect`, no scroll/resize listeners ([MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning) (verified 2026-05-19)).

The author declares an anchor with `anchor-name: --some-name;` on the source element, then tethers a positioned element with `position-anchor: --some-name;` plus either the `anchor()` function inside `top`/`bottom`/`left`/`right`/`inset-*` properties, or the higher-level `position-area` shorthand for grid-based placement.

Three core properties make up the system :

- `anchor-name` : a `<dashed-ident>` (e.g. `--menu-btn`) or `none`. Marks the source element as an anchor that can be referenced ([MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning) (verified 2026-05-19)).
- `position-anchor` : a `<dashed-ident>` matching an `anchor-name` declared elsewhere. Establishes the default anchor for the positioned element.
- `position-area` : a grid-based placement keyword pair on a 3x3 grid where the anchor is the center cell ([MDN : position-area](https://developer.mozilla.org/en-US/docs/Web/CSS/position-area) (verified 2026-05-19)). Keywords include physical (`top`, `bottom`, `left`, `right`), logical (`block-start`, `block-end`, `inline-start`, `inline-end`), coordinate (`y-start`, `y-end`, `x-start`, `x-end`), and `span-*` variants (`span-left`, `span-all`, etc.). `position-area: top center` places the element above and horizontally-centered on the anchor.

The `anchor()` function accepts an anchor-side (`top`, `bottom`, `start`, `end`, `left`, `right`, `center`, `inside`, `outside`, `self-start`, `self-end`, or a `<percentage>`) and is usable in `top`/`bottom`/`left`/`right`/`inset-*` properties. Example : `top: anchor(bottom); left: anchor(center);` places the element immediately below the anchor, horizontally centered on the anchor's center line ([MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning) (verified 2026-05-19)).

Anchor positioning also supplies `anchor-size()` for sizing-relative-to-anchor and `anchor-center` (a value of `justify-self` / `align-self`) for axis-centering without `translate(-50%)` math ([MDN : Anchor Positioning : Using](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using) (verified 2026-05-19)).

**Collision handling with `position-try-fallbacks`** : when the primary placement would overflow the viewport / containing block, the browser tries the listed fallback options in order until one fits or all fail ([MDN : position-try-fallbacks](https://developer.mozilla.org/en-US/docs/Web/CSS/position-try-fallbacks) (verified 2026-05-19)). Built-in try-tactic keywords :

- `flip-block` : mirrors the placement across the inline axis through the anchor center (e.g. `top` becomes `bottom`).
- `flip-inline` : mirrors across the block axis (e.g. `left` becomes `right`).
- `flip-start` : mirrors diagonally across the anchor center, swapping start/end properties.

Tactics can be combined inside one fallback option (space-separated, composed as a single transformation : `flip-block flip-inline`) or listed as separate options (comma-separated, tried in order : `flip-block, flip-inline, flip-block flip-inline`). Authors can also register named custom options with `@position-try --name { ... }` and reference them from the property : `position-try-fallbacks: --my-custom-option, flip-block;` ([MDN : position-try-fallbacks](https://developer.mozilla.org/en-US/docs/Web/CSS/position-try-fallbacks) (verified 2026-05-19)).

If all fallbacks overflow, the browser reverts to the original placement (which may overflow). `position-visibility: auto` lets authors hide the element entirely when no placement fits.

**Baseline verdict** : `position-try-fallbacks` is **Baseline 2026 Newly Available since January 2026** ([MDN : position-try-fallbacks](https://developer.mozilla.org/en-US/docs/Web/CSS/position-try-fallbacks) (verified 2026-05-19)). The same Baseline 2026 verdict applies to `position-area` ([MDN : position-area](https://developer.mozilla.org/en-US/docs/Web/CSS/position-area) (verified 2026-05-19)). The broader Anchor Positioning module (`anchor-name`, `position-anchor`, `anchor()`) shipped earlier in Chromium 2024 and is rolling out across the other two engines through 2025/2026; the MDN reference page warns "Limited/Experimental" with full cross-engine support pending ([MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning) (verified 2026-05-19)). The skill MUST gate the entire pattern behind `@supports (anchor-name: --x) { ... }` and provide a JS-`getBoundingClientRect` fallback for non-supporting browsers.

Anchor positioning composes naturally with the Popover API. When a popover is triggered by a button with `popovertarget`, the popover acquires an **implicit anchor** to that button, so authors can write `position-area: bottom span-inline-end;` directly inside the popover rule without declaring `anchor-name` / `position-anchor` ([MDN : Anchor Positioning : Using](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using) (verified 2026-05-19)).

---

## 4. @starting-style + transition-behavior allow-discrete + overlay

This is the §14 gap #4 drill. The three together solve the problem that CSS transitions historically could not animate **discrete properties** (most importantly `display`) and could not animate elements as they entered the top layer (`overlay`). The combination is REQUIRED for any usable popover / dialog enter/exit animation; without it the open/close visual snaps instead of fading.

**Problem statement**. By default, CSS transitions do not start in two situations : (1) an element's very first style update (because there is no prior state to transition from), and (2) when a property is "discrete" (animates by flipping between values at 50% duration with no intermediate states; `display`, `content-visibility`, `overlay`, and `visibility` are the relevant discretes). Together this means a popover with `display: none` cannot fade in : the property switch from `none` to `block` happens instantly, before the transition can start, so the element appears full-opacity with no intermediate frames.

**Solution part A : `transition-behavior: allow-discrete`** ([MDN : transition-behavior](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior) (verified 2026-05-19)). The property has two values, `normal` (default; do not start transitions for discrete properties) and `allow-discrete` (do start them, with special timing). Baseline 2024 since August 2024.

For `display` specifically there is special handling : when transitioning TO `display: none`, the property flips at 100% of duration (so the element stays visible throughout the exit animation). When transitioning FROM `display: none`, it flips at 0% (so the element appears immediately at the start of the entry animation). This is what makes the trick work : the element is visible for the full duration of both enter and exit animations, even though the underlying property is "discrete". The same special handling applies to `content-visibility`.

For `overlay` (the read-only property that the user agent sets when an element is in the top layer), `allow-discrete` defers the removal from the top layer until the animation completes, so exit animations are not cut short by immediate top-layer removal ([MDN : overlay](https://developer.mozilla.org/en-US/docs/Web/CSS/overlay) (verified 2026-05-19)). Without `overlay 0.7s allow-discrete` in the transition list, a popover/dialog will visually pop out the moment `.hidePopover()` or `.close()` is called, because the element is dropped from the top layer and renders below its z-stacking neighbors.

**Solution part B : `@starting-style`** ([MDN : `@starting-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style) (verified 2026-05-19), Baseline 2024 since August 2024). This at-rule declares the "from" state for the FIRST style update. It has two syntactic forms :

```css
/* Standalone, nested ruleset with selectors */
@starting-style {
  [popover]:popover-open {
    opacity: 0;
    transform: scale(0.95);
  }
}

/* Nested inside a ruleset, declarations only */
[popover]:popover-open {
  opacity: 1;
  transform: scale(1);

  @starting-style {
    opacity: 0;
    transform: scale(0.95);
  }
}
```

`@starting-style` has the SAME specificity as the original rule, so it MUST be written AFTER the main rule (or nested inside it) to override correctly. Authors who put it first will find that the main rule's values win and no entry animation runs.

**The combined recipe** for a fade-and-scale popover entry/exit animation ([MDN : transition-behavior](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior) (verified 2026-05-19), [MDN : `@starting-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style) (verified 2026-05-19), [MDN : overlay](https://developer.mozilla.org/en-US/docs/Web/CSS/overlay) (verified 2026-05-19)) :

```css
/* Closed state : final state of exit animation, initial state of entry */
[popover] {
  opacity: 0;
  transform: scale(0.95);
  transition:
    opacity 0.3s ease,
    transform 0.3s ease,
    display 0.3s allow-discrete,
    overlay 0.3s allow-discrete;
}

/* Open state : final state of entry animation */
[popover]:popover-open {
  opacity: 1;
  transform: scale(1);
}

/* Starting state of entry animation (BEFORE the first paint) */
@starting-style {
  [popover]:popover-open {
    opacity: 0;
    transform: scale(0.95);
  }
}

/* Animate the backdrop too if you use it */
[popover]::backdrop {
  background-color: rgb(0 0 0 / 0%);
  transition:
    background-color 0.3s,
    display 0.3s allow-discrete,
    overlay 0.3s allow-discrete;
}
[popover]:popover-open::backdrop {
  background-color: rgb(0 0 0 / 25%);
}
@starting-style {
  [popover]:popover-open::backdrop {
    background-color: rgb(0 0 0 / 0%);
  }
}
```

For `<dialog>` opened with `showModal()`, the same pattern applies but with the `[open]` attribute selector (or `dialog:open`) instead of `:popover-open`. The four lines that MUST be in the transition list : `opacity` (or whatever visual property animates), `display allow-discrete`, `overlay allow-discrete`, and any other transformed property. Omitting any of these breaks the animation in a specific way (see anti-patterns §10).

Cross-browser-safe shorthand pattern : declare `transition: all 0.3s;` first and then `transition: all 0.3s allow-discrete;` on the next line so non-supporting browsers still get the regular opacity/transform animation while supporting browsers get the full discrete-property animation ([MDN : transition-behavior](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior) (verified 2026-05-19)).

`overlay` is read-only; only the user agent sets it. Authors NEVER write `overlay: auto` in their stylesheet, they only list it in the `transition-property` (or shorthand) ([MDN : overlay](https://developer.mozilla.org/en-US/docs/Web/CSS/overlay) (verified 2026-05-19)). The current Baseline status of `overlay` is Limited / experimental (Chromium-led), so the skill MUST cite this Baseline gap explicitly.

---

## 5. closedby attribute + focus restoration

The `closedby` attribute on `<dialog>` is the declarative control for light-dismiss / Escape behavior ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19)). Three values :

- `any` : the dialog dismisses on outside-click (light dismiss), Escape, AND any developer-driven `close()` call.
- `closerequest` : Escape and `close()` only. No light dismiss. This is the default for `showModal()`.
- `none` : only `close()` works. Escape will not close. This is the default for `show()` and for the boolean `open` attribute.

The defaults are surprising and worth memorizing : `showModal()` defaults to `closerequest` (Escape works), but a dialog opened via `show()` or `<dialog open>` defaults to `none` (Escape does NOT work). Authors who want non-modal dialogs with Escape support MUST add `closedby="closerequest"` explicitly.

Focus restoration differs between `<dialog>` and Popover API in an important way. For `<dialog>`, focus restoration on close is the author's responsibility : the spec moves focus into the dialog on open, but does NOT guarantee restoration to the trigger element on close. The pattern is to capture the previously-focused element before calling `showModal()` and restore it after `close` event :

```javascript
const trigger = document.querySelector('#open-dialog');
const dialog = document.querySelector('#my-dialog');
let lastActive = null;

trigger.addEventListener('click', () => {
  lastActive = document.activeElement;
  dialog.showModal();
});
dialog.addEventListener('close', () => {
  lastActive?.focus();
});
```

For the Popover API, the WHATWG spec mandates focus restoration on hide : "if focusPreviousElement is true and document's focused area is a shadow-including inclusive descendant of element, then run the focusing steps for previouslyFocusedElement" ([WHATWG HTML : popover](https://html.spec.whatwg.org/multipage/popover.html) (verified 2026-05-19)). In practice this means : when an auto/hint popover light-dismisses or is hidden by a `popovertargetaction="hide"` button, the browser restores focus to the previously-focused element automatically. Authors do NOT need the manual capture-and-restore pattern that `<dialog>` requires.

Initial focus on dialog open : if `autofocus` is set on a descendant, that element receives focus. If not, focus moves to the first focusable element. `tabindex` on the dialog itself is forbidden by MDN; the dialog is a container, not a focus target.

---

## 6. Stacking and top-layer interaction

Top-layer elements are painted above every other element on the page regardless of `z-index` ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19), [MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19)). Three categories of elements live in the top layer : fullscreen elements (`Element.requestFullscreen()`), modal dialogs (`HTMLDialogElement.showModal()`), and showing popovers (`HTMLElement.showPopover()` or declarative `popovertarget=`).

Within the top layer, elements stack in a **last-in / first-out** (LIFO) order : the most recently promoted element renders on top. Each top-layer element gets its own `::backdrop` pseudo-element rendered immediately below it ([MDN : `::backdrop`](https://developer.mozilla.org/en-US/docs/Web/CSS/::backdrop) (verified 2026-05-19)). For dialogs opened with `showModal()`, the `::backdrop` is visible and intercepts pointer events. For popovers, `::backdrop` is generated but transparent by default and does NOT block pointer events (the rest of the page stays interactive).

Popover-over-popover stacking is governed by the **popover stack** ([WHATWG HTML : popover](https://html.spec.whatwg.org/multipage/popover.html) (verified 2026-05-19)). When an `auto` popover is shown, the spec runs the "hide popover stack until" algorithm : it walks down the stack closing any open popovers that are NOT ancestors of the new popover, then promotes the new popover to the top. A click outside ALL open popovers closes the entire stack. `manual` popovers do not participate in the stack and do not light-dismiss each other; the author MUST close them explicitly.

Dialog-over-popover stacking is NOT explicitly defined by the WHATWG spec ([WHATWG HTML : popover](https://html.spec.whatwg.org/multipage/popover.html) (verified 2026-05-19)). In practice, calling `dialog.showModal()` while a popover is open promotes the dialog above the popover in the top-layer LIFO stack, and the modal-backdrop covers the popover. The reverse case (`showPopover()` while a modal dialog is open) is allowed but the popover will appear above the modal's backdrop, which usually breaks the modal contract. Authors SHOULD NOT mix modal dialogs and popovers in the same interaction; pick one model per surface.

`<dialog popover>` is syntactically valid and combines both APIs on one element ([MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19)). It is rarely useful : pick `<dialog>` for modal, popover for non-modal. The skill should mention the combination only to warn against blindly applying both attributes.

---

## 7. Decision matrix : pattern to API combo

| UI Pattern | Dialog or Popover? | Anchor? | closedby / popover state | Animation? |
|------------|--------------------|---------|--------------------------|------------|
| Confirmation modal (blocking) | `<dialog>` + `showModal()` | No | default `closerequest` (Escape works) | optional fade + `display allow-discrete` |
| Cookie-consent dialog (must dismiss) | `<dialog>` + `showModal()` | No | `closedby="none"` (block dismiss) | optional fade |
| Side-sheet (non-blocking) | `popover="auto"` | No (full-screen edge) | implicit light-dismiss | slide + `display allow-discrete` + `overlay allow-discrete` |
| Dropdown menu (button trigger) | `popover="auto"` | Yes (implicit from `popovertarget`) | light-dismiss + Escape | fade-scale + full discrete recipe |
| Tooltip / hint on hover | `popover="hint"` | Yes | light-dismiss; closes other hints only | fade |
| Settings panel (stays open while editing) | `popover="manual"` | Optional | script close only | optional |
| Combobox listbox | `popover="auto"` | Yes (anchor to input) | light-dismiss | optional fade |
| Date picker | `popover="auto"` | Yes (anchor to input) | light-dismiss | optional |
| Toast (transient banner) | popover NOT recommended; use ARIA live region | No | n/a | translate-in |
| Drawer / Off-canvas nav | `<dialog>` (modal) OR `popover="auto"` (non-modal) | No | depends on UX | translate-in |

The single most useful default to internalize : **modal interrupts user workflow -> `<dialog>` + `showModal()`. Transient surface anchored to a trigger -> `popover="auto"` with implicit anchor.**

---

## 8. Anti-patterns

1. **`tabindex` on `<dialog>`**. MDN explicitly forbids this ([MDN : `<dialog>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog) (verified 2026-05-19)). The dialog is a container, not a focus target. Rely on `autofocus` for initial focus and on the natural tab order for traversal.

2. **Combining `popover` and `showModal()` on the same element**. Popovers are ALWAYS non-modal by spec ([MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19)). Adding `popover="auto"` to a `<dialog>` then calling `showModal()` results in undefined cross-API state. Pick one model.

3. **Custom click-outside JS to dismiss a popover**. The Popover API ships built-in light-dismiss for `auto` and `hint`; rolling your own is 4 KB of redundant code and inevitably gets the focus restoration wrong ([MDN : Popover API](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) (verified 2026-05-19), [WHATWG HTML : popover](https://html.spec.whatwg.org/multipage/popover.html) (verified 2026-05-19)).

4. **Anchor positioning without `@supports` gating**. `position-try-fallbacks` and `position-area` are Baseline 2026 ([MDN : position-try-fallbacks](https://developer.mozilla.org/en-US/docs/Web/CSS/position-try-fallbacks) (verified 2026-05-19), [MDN : position-area](https://developer.mozilla.org/en-US/docs/Web/CSS/position-area) (verified 2026-05-19)); browsers older than 2025 render misaligned. Always gate behind `@supports (anchor-name: --x) { ... }` and supply a JS fallback (or accept the degraded layout).

5. **Missing `@starting-style` for popover/dialog open animation**. Without it, the property switch happens before the transition can start, so opacity stays at the open value from frame one. The element appears instantly instead of fading ([MDN : `@starting-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style) (verified 2026-05-19)).

6. **Forgetting `display 0.3s allow-discrete` in the transition shorthand**. The animation runs but the element jumps to `display: none` immediately at the end, cutting off the exit animation. Always add `display allow-discrete` (and `overlay allow-discrete` for top-layer elements) to the transition list ([MDN : transition-behavior](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior) (verified 2026-05-19)).

7. **Forgetting `overlay 0.3s allow-discrete` for top-layer exits**. The element fades correctly but pops out of the top layer immediately on hide, falling behind other content for the last frames. Add `overlay` to the transition list ([MDN : overlay](https://developer.mozilla.org/en-US/docs/Web/CSS/overlay) (verified 2026-05-19)).

8. **`position: fixed` tooltip with manual `getBoundingClientRect` math**. Reinvents what anchor positioning ships natively. Use `anchor-name` + `position-anchor` + `position-area` with `position-try-fallbacks` for viewport-aware positioning ([MDN : CSS Anchor Positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning) (verified 2026-05-19)).

9. **Placing `@starting-style` BEFORE the main rule**. The two have equal specificity, so source order decides : `@starting-style` declared first will be overridden by the main rule and the entry animation never runs. ALWAYS declare `@starting-style` after the main rule, or nest it inside ([MDN : `@starting-style`](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style) (verified 2026-05-19)).

10. **Manual focus-restoration for popovers**. The spec restores focus automatically on light-dismiss and explicit hide ([WHATWG HTML : popover](https://html.spec.whatwg.org/multipage/popover.html) (verified 2026-05-19)); duplicating that in script causes focus to bounce twice. Only `<dialog>` needs manual capture-and-restore.

---

## 9. Sources Used

| # | Source URL | Verified | Extracted |
|---|------------|----------|-----------|
| 1 | https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dialog | 2026-05-19 | showModal/show/close, ::backdrop, closedby, autofocus, tabindex warning, open attribute, Baseline since March 2022 |
| 2 | https://developer.mozilla.org/en-US/docs/Web/API/Popover_API | 2026-05-19 | popover auto/manual/hint, popovertarget(action), light-dismiss, ToggleEvent, show/hide/togglePopover, :popover-open, Baseline 2025 |
| 3 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning | 2026-05-19 | anchor-name, position-anchor, anchor() function, position-area, position-try-fallbacks, anchor-size(), @position-try at-rule |
| 4 | https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_anchor_positioning/Using | 2026-05-19 | implicit popover-anchor relationship, 3x3 grid model, anchor-center, anchor-scope, practical tooltip example |
| 5 | https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style | 2026-05-19 | standalone vs nested syntax, specificity equal to main rule, three states (starting/transitioned/default), recipe with allow-discrete |
| 6 | https://developer.mozilla.org/en-US/docs/Web/CSS/transition-behavior | 2026-05-19 | normal vs allow-discrete, discrete properties (display, content-visibility, overlay), 0%/100% flip handling for display, Baseline 2024 |
| 7 | https://developer.mozilla.org/en-US/docs/Web/CSS/overlay | 2026-05-19 | auto/none values, UA read-only, required for top-layer exit transitions, must pair with allow-discrete, Limited availability status |
| 8 | https://developer.mozilla.org/en-US/docs/Web/CSS/position-try-fallbacks | 2026-05-19 | flip-block/flip-inline/flip-start, comma-separated options, space-separated composition, @position-try reference, Baseline 2026 |
| 9 | https://developer.mozilla.org/en-US/docs/Web/CSS/position-area | 2026-05-19 | 3x3 grid keywords, span-* variants, logical/physical/coordinate axes, popover workaround, Baseline 2026 |
| 10 | https://developer.mozilla.org/en-US/docs/Web/CSS/:popover-open | 2026-05-19 | matches popover in showing state, UA stylesheet defaults, transition pairing, Baseline 2024 |
| 11 | https://developer.mozilla.org/en-US/docs/Web/CSS/::backdrop | 2026-05-19 | fullscreen + dialog showModal + popover top-layer applicability, LIFO stacking, viewport-sized box, no inheritance, Baseline since March 2022 |
| 12 | https://html.spec.whatwg.org/multipage/popover.html | 2026-05-19 | popover attribute states normative definition, popover stack hide-until algorithm, beforetoggle cancelability, focus restoration on hide |
