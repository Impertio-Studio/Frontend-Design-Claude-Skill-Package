# Topic Research : frontend-a11y-focus-keyboard-inert

## Status

Phase 4 topic-research drilling §14 verification gap #3 from `docs/research/vooronderzoek-frontend.md`. This document closes the `inert` attribute knowledge gap and verifies the supporting focus and keyboard primitives (`:focus-visible`, `:focus-within`, `tabindex`, roving tabindex, arrow-key APG patterns, Escape and focus-restoration semantics) against approved primary sources. All claims below are WebFetched on 2026-05-19. The skill `frontend-a11y-focus-keyboard-inert` (Batch 5, accessibility category) depends on the contents of this document.

Sources WebFetched in this round :

1. [MDN : inert global attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/inert) (verified 2026-05-19)
2. [MDN : :focus-visible](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible) (verified 2026-05-19)
3. [MDN : :focus-within](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-within) (verified 2026-05-19)
4. [MDN : tabindex global attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex) (verified 2026-05-19)
5. [W3C WAI APG : Keyboard Interface](https://www.w3.org/WAI/ARIA/apg/practices/keyboard-interface/) (verified 2026-05-19)
6. [MDN : `<dialog>` element](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog) (verified 2026-05-19)
7. [MDN : HTMLElement.focus()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) (verified 2026-05-19)
8. [W3C WAI APG : Dialog (Modal) pattern](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/) (verified 2026-05-19)
9. [MDN : Popover API : Using](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API/Using) (verified 2026-05-19)
10. [W3C WAI APG : Tabs pattern](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) (verified 2026-05-19)
11. [W3C WAI APG : Grid pattern](https://www.w3.org/WAI/ARIA/apg/patterns/grid/) (verified 2026-05-19)

---

## 1. inert attribute

The `inert` HTML global attribute is "a Boolean attribute indicating that the element and all of its flat tree descendants become inert" ([MDN : inert](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/inert) (verified 2026-05-19)). It applies to any HTML element and disables interactivity for the element and the entire subtree it roots, including form controls, links, and buttons.

### What inert does (six effects)

Per MDN, when an element is inert, its descendants :

1. "Do not have click events fired when clicked on."
2. "Cannot be focused and focus events cannot be fired on them."
3. "Are not searchable via browser find-in-page features (none of their content is found/matched)."
4. "Disallow users from selecting text contained within their content, akin to using the CSS property user-select to disable text selection."
5. "Cannot have otherwise-editable content edited. This includes, for example, the contents of textual `<input>` fields, and text elements with contenteditable set on them."
6. "Are hidden from assistive technologies as they are excluded from the accessibility tree."

This is the single most important attribute for modal patterns : it removes the entire background subtree from focus, AT exposure, find-in-page, text selection, click events, and editing in one declarative attribute, replacing the brittle hand-rolled focus-trap + `aria-hidden` + `tabindex="-1"`-on-all-things pattern that dominated 2015-2022 code.

### Baseline status

"Widely available. This feature is well established and works across many devices and browser versions. It's been available across browsers since April 2023" ([MDN : inert](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/inert) (verified 2026-05-19)). The Baseline progression : `inert` reached Baseline Newly Available on 2023-04-03 (Safari 15.5 was the last holdout), and graduated to Baseline Widely Available 30 months later in late 2025. For an `evergreen-2026` package this is safe to use without a feature query.

### inert vs pointer-events: none vs aria-hidden vs disabled

`inert` is comprehensive. The common substitutes are NOT equivalent :

- `pointer-events: none` (CSS) only blocks mouse/touch/pen events. Keyboard focus still lands on the element, the element remains in the tab order, screen readers still announce it, find-in-page still matches it, and text selection still works inside it. It is a paint-tree filter, not an interaction model.
- `aria-hidden="true"` hides from AT but does NOT remove the subtree from sequential focus. A keyboard user will still Tab into an `aria-hidden` subtree, focus an element that screen readers cannot describe, and become disoriented. WAI-ARIA explicitly warns against `aria-hidden` on a focusable element.
- `disabled` (on form controls only) removes them from the tab sequence and grays them out, but does NOT work on `<div>`, `<a>`, or any non-form-control element. Per MDN tabindex page, "Browsers remove HTML input elements with the disabled attribute from the tab sequence" but this attribute is unsupported on most elements.

For an individual control, prefer `disabled` over `inert`. Per MDN : "To make individual controls inert, consider using the disabled attribute, along with CSS `:disabled` styles, instead." Use `inert` on a container (background pane, off-screen carousel slide, hidden tab panel, etc.).

### Interaction with `<dialog>` and `showModal()`

Modal dialogs created via `dialog.showModal()` "escape inertness, meaning that they don't inherit inertness from their ancestors, but can be made inert by having the `inert` attribute explicitly set on themselves. No other element can escape inertness" ([MDN : inert](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/inert) (verified 2026-05-19)).

The `<dialog>` page reinforces this : "Modal dialog boxes block interaction with other UI elements, making the rest of the page inert, while non-modal dialog boxes allow interaction with the rest of the page" ([MDN : dialog](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog) (verified 2026-05-19)). The browser handles this automatically when `showModal()` is called : the dialog is moved to the top layer, and the rest of the document tree is treated as if it had `inert` set, without the author setting any attribute.

For `dialog.show()` (non-modal) this is NOT automatic : the background remains interactive. Authors who need a "drawer" or "side panel" pattern with `show()` must set `inert` on `<main>` themselves while the drawer is open.

### Interaction with Popover API

Popovers are different. "Popovers created using the Popover API are always non-modal. If you want to create a modal popover, a `<dialog>` element is the right way to go" ([MDN : Popover API : Using](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API/Using) (verified 2026-05-19)). The Popover API places the popover in the top layer, but the rest of the page is NOT inert. Light-dismiss works (click outside, Esc), and focus returns to the invoker on Esc-close.

### Accessibility responsibility

Per MDN : "Use careful consideration for accessibility when applying the inert attribute. By default, there is no visual way to tell whether or not an element or its subtree is inert. As a web developer, it is your responsibility to clearly indicate the content parts that are active and those that are inert." A visual cue (overlay scrim, opacity reduction, blur) is mandatory.

---

## 2. :focus-visible vs :focus-within

`:focus-visible` is the keystone of modern focus styling. Per MDN, it "applies while an element matches the `:focus` pseudo-class and the UA determines via heuristics that the focus should be made evident on the element. (Many browsers show a focus ring by default in this case.)" ([MDN : :focus-visible](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible) (verified 2026-05-19)). It is Baseline Widely Available since March 2022.

### UA heuristic : keyboard vs pointer

Focus indicators are shown when "users are navigating the page with the keyboard or when focus is managed via scripts." Focus indicators are NOT shown when "the user knows where they are putting focus, such as when they use a pointing device such as a mouse or finger to physically set focus on an element, unless that element continues to need user attention." Concretely : "when a button is clicked using a pointing device, the focus is generally not visually indicated, but when a text box needing user input has focus, focus is indicated" ([MDN : :focus-visible](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible) (verified 2026-05-19)).

This solves the long-standing tension between developers ("focus rings look ugly on mouse-clicked buttons") and accessibility ("keyboard users need focus rings on every focusable element"). With `:focus-visible`, the focus ring appears for the keyboard user (where it is needed) and is suppressed for the mouse user (where it was visual noise). The MDN authoring guidance : "using the `:focus-visible` (instead of the `:focus` pseudo-class) allows authors to change the appearance of the focus indicator without changing when the focus indicator appears."

`:focus-visible` is the default focus selector. Authors should use `:focus-visible` exclusively for custom outlines, NOT `:focus`. The replacement style MUST meet WCAG 2.1 SC 1.4.11 Non-Text Contrast at 3:1 against the adjacent background. Per MDN's accessibility note : "WCAG 2.1 SC 1.4.11 Non-Text Contrast requires that the visual focus indicator be at least 3 to 1."

### :focus-within

`:focus-within` "matches an element if the element or any of its descendants are focused. In other words, it represents an element that is itself matched by the `:focus` pseudo-class or has a descendant that is matched by `:focus`. (This includes descendants in shadow trees.)" ([MDN : :focus-within](https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-within) (verified 2026-05-19)). It is Baseline Widely Available since January 2020.

The canonical use case : highlight a `<form>` when any of its `<input>` children has focus. Modern uses extend this to card components (lift the card when any inner control is focused), navigation rails (expand the rail when any link is focused), and combobox containers (style the wrapper while the input or popup has focus).

`:focus-within` combines naturally with `:has()` (Baseline 2024) for cross-tree styling : `.row:has(:focus-within) { background: var(--hover); }` highlights the row containing the focused descendant, even when that descendant is multiple levels deep.

---

## 3. tabindex semantics + anti-patterns

Per MDN, the `tabindex` global attribute "allows developers to make HTML elements focusable, allow or prevent them from being sequentially focusable (usually with the Tab key, hence the name) and determine their relative ordering for sequential focus navigation" ([MDN : tabindex](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex) (verified 2026-05-19)).

### Focusable vs tabbable

These are different :

- **Focusable** : the element can receive keyboard focus by `element.focus()` or mouse click.
- **Tabbable** : the element is reachable via sequential Tab navigation.

An element with `tabindex="-1"` is focusable but NOT tabbable. An element with `tabindex="0"` (or default-focusable elements with no `tabindex`) is both focusable and tabbable.

### Values

| Value | Effect |
|-------|--------|
| (omitted) | Default. Form controls and links are focusable+tabbable; non-interactive elements are neither. |
| `0` | Focusable AND included in tab order. Position determined by DOM order. |
| `-1` | Focusable programmatically and via mouse click, but NOT in tab order. |
| Positive integers (`1`, `2`, ...) | Focusable AND in tab order, with positive-integer items tabbed first in numeric order, then default elements in DOM order. ANTI-PATTERN. |

### Why positive tabindex is an anti-pattern

Per MDN : "Avoid using tabindex values greater than 0 and CSS properties that can change the order of focusable HTML elements. Doing so makes it difficult for people who rely on using keyboard for navigation or assistive technology to navigate and operate page content. Instead, write the document with the elements in a logical sequence." MDN's recommendation is explicit : "You are recommended to only use 0 and -1 as tabindex values."

Two reasons positive `tabindex` is destructive :

1. It decouples tab order from DOM order, which means visual / reading / focus order can diverge. WCAG 2.4.3 Focus Order requires that focus follow a meaningful sequence; positive tabindex makes this nearly impossible to maintain.
2. It infects the rest of the page : once ANY element has `tabindex="2"`, every other interactive element on the page is now in a relative-position dispute with it. The fix is global, not local.

### Default-focusable elements (do NOT add tabindex)

Per MDN, the following elements have implicit `tabindex="0"` : `<a>` or `<area>` with `href`, `<button>`, `<frame>`, `<iframe>`, `<input>`, `<object>`, `<select>`, `<textarea>`, SVG `<a>`, and `<summary>` inside `<details>`. "Developers shouldn't add the tabindex attribute to these elements unless it changes the default behavior (for example, including a negative value will remove the element from the focus navigation order)."

### tabindex on `<dialog>`

Per MDN dialog page : "Do not add the tabindex property to the `<dialog>` element as it is not interactive and does not receive focus. The dialog's contents, including the close button contained in the dialog, can receive focus and be interactive." This contradicts pre-2020 advice and reflects the modern `<dialog>` semantics.

### Non-interactive elements and accessibility

MDN warns : "Interactive components authored using non-interactive elements are not listed in the accessibility tree. This prevents assistive technology from being able to navigate to and manipulate those components. The content should be semantically described using interactive elements (`<a>`, `<button>`, `<details>`, `<input>`, `<select>`, `<textarea>`, etc.) instead." Adding `tabindex="0"` to a `<div>` does NOT make it a button; it only makes it focusable. The element still has no implicit role, no Space/Enter activation, no `disabled` state. Use the right element.

---

## 4. Roving tabindex vs aria-activedescendant

Composite widgets (tabs, listboxes, menus, grids, trees, toolbars) contain multiple focusable items but should expose ONE Tab-stop. Per APG, "the tab and shift + tab keys move focus from one UI component to another while other keys, primarily the arrow keys, move focus inside of components that include multiple focusable elements" ([W3C WAI APG : Keyboard Interface](https://www.w3.org/WAI/ARIA/apg/practices/keyboard-interface/) (verified 2026-05-19)). Tab enters the widget once; arrow keys then navigate inside; Tab again leaves the widget.

Two strategies implement this single-Tab-stop behavior :

### Roving tabindex

The DOM focus moves between items as the user presses arrow keys. Per APG :

- "set `tabindex='0'` on the element that will initially be included in the tab sequence and set `tabindex='-1'` on all other focusable elements"
- When a navigation key fires, "set `tabindex='-1'` on the element that has `tabindex='0'`" and move the `tabindex` attribute to the newly focused element, then call `element.focus()`.

Benefit per APG : "One benefit of using roving tabindex rather than aria-activedescendant to manage focus is that the user agent will scroll the newly focused element into view." Other benefits : `:focus` selectors in CSS work naturally; assistive technology virtual-cursor mode mirrors visual focus; the active element is observable via `document.activeElement`.

### aria-activedescendant

DOM focus stays on the container. The container has `aria-activedescendant="id-of-active-item"` pointing to a non-focused descendant. Per APG : "only the container element needs to be included in the tab sequence. When the container has DOM focus, the value of aria-activedescendant on the container tells assistive technologies which element is active within the widget" ([W3C WAI APG : Keyboard Interface](https://www.w3.org/WAI/ARIA/apg/practices/keyboard-interface/) (verified 2026-05-19)).

DOM requirements : the referenced element must be a DOM descendant, owned via `aria-owns`, or controlled via `aria-controls` (for combobox / textbox / searchbox roles).

### When to use which

| Widget | Strategy | Why |
|--------|----------|-----|
| Tabs (`role="tablist"`) | Roving tabindex | DOM focus on the active tab fires `:focus-visible` on the tab; ScrollIntoView works automatically. |
| Listbox (single-Tab-stop, but content focus moves) | Roving tabindex (typical) | Same as tabs. Visual focus on the option. |
| Combobox input + listbox popup | `aria-activedescendant` | DOM focus must stay on the `<input>` so the user can keep typing; highlight moves via `aria-activedescendant`. |
| Toolbar / Menubar | Roving tabindex | Natural arrow-key navigation, DOM focus on the active item. |
| Tree | Roving tabindex (default) | Subtree expansion is per-DOM-node; focus must be observable. |
| Grid / Treegrid | Roving tabindex (typical) | Cell focus must move with arrow keys for screen-reader exposure. |
| Editable controls with suggestion popup | `aria-activedescendant` | Editing the input is the primary action; suggestions are secondary. |

The general rule : roving tabindex when DOM focus belongs on the active item; `aria-activedescendant` when DOM focus belongs on an editable input and a separate "highlighted" item is presented.

---

## 5. Arrow-key nav patterns (1D + 2D)

The APG specifies that arrow-key bindings should follow "the same key bindings as similar components in common GUI operating systems" ([W3C WAI APG : Keyboard Interface](https://www.w3.org/WAI/ARIA/apg/practices/keyboard-interface/) (verified 2026-05-19)). The matrix below is verified per-pattern.

### 1D horizontal (tabs default, toolbar)

Per [W3C WAI APG : Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) (verified 2026-05-19), horizontal tabs use :

- **Left Arrow** : "moves focus to the previous tab. If focus is on the first tab, moves focus to the last tab." (Wrapping is conventional, but not mandatory.)
- **Right Arrow** : "Moves focus to the next tab. If focus is on the last tab element, moves focus to the first tab."
- **Home** : moves focus to the first tab; may optionally activate it.
- **End** : moves focus to the last tab; may optionally activate it.
- **Space or Enter** : "Activates the tab if it was not activated automatically on focus."

### 1D vertical (listbox, menu, vertical tabs)

When `aria-orientation="vertical"` : "Down Arrow functions like Right Arrow, and Up Arrow functions like Left Arrow" ([W3C WAI APG : Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) (verified 2026-05-19)). The 90-degree rotation rule applies across listbox, vertical menu, vertical toolbar, tree (which is inherently vertical), and radio group.

### Automatic vs manual activation

Per APG : two activation styles for tabs and similar widgets :

- **Automatic** : focus = activation (Left/Right immediately switch panels). Acceptable when activation is cheap (no network request, no expensive render).
- **Manual** : focus moves silently; Space or Enter activates. REQUIRED when activation has side effects.

### 2D grid

Per [W3C WAI APG : Grid](https://www.w3.org/WAI/ARIA/apg/patterns/grid/) (verified 2026-05-19) :

- **Right Arrow** : "Moves focus one cell to the right. If focus is on the right-most cell in the row, focus does not move." (No row-wrap.)
- **Left Arrow** : "Moves focus one cell to the left. If focus is on the left-most cell in the row, focus does not move."
- **Down Arrow** : "Moves focus one cell down. If focus is on the bottom cell in the column, focus does not move."
- **Up Arrow** : "Moves focus one cell up. If focus is on the top cell in the column, focus does not move."
- **Home** : "moves focus to the first cell in the row that contains focus."
- **End** : "moves focus to the last cell in the row that contains focus."
- **Control + Home** : "moves focus to the first cell in the first row."
- **Control + End** : "moves focus to the last cell in the last row."
- **Page Down** : "Moves focus down an author-determined number of rows, typically scrolling so the bottom row in the currently visible set of rows becomes one of the first visible rows."
- **Page Up** : "Moves focus up an author-determined number of rows, typically scrolling so the top row in the currently visible set of rows becomes one of the last visible rows."

The grid is the canonical 2D pattern. Treegrid extends this with expand/collapse via Right/Left at parent nodes.

### Landing position when the widget is re-entered

Per APG, the conventional landing element depends on the widget :

- Grid / treegrid : last focused element, or first if never focused before.
- Radio group, tabs, listbox, tree : the selected element (or first if none selected).
- Menubar / toolbar : the first element.

---

## 6. Escape + close-button + focus restoration

### Escape key on `<dialog>`

For modal dialogs : "By default, a dialog invoked by the showModal() method can be dismissed by pressing the Esc key" ([MDN : dialog](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog) (verified 2026-05-19)). Per APG dialog-modal : "Escape : Closes the dialog." For non-modal dialogs (`dialog.show()`) "A non-modal dialog does not dismiss via the Esc key by default."

### The `closedby` attribute (modern dialogs)

Per MDN, the new `closedby` attribute on `<dialog>` controls dismissal mechanisms :

| Value | Behavior |
|-------|----------|
| `any` | Dismissible by light dismiss (click outside), Esc, AND a developer mechanism (button/form). |
| `closerequest` | Dismissible by Esc or a developer mechanism. (Default when `showModal()` is called.) |
| `none` | Dismissible ONLY by a developer mechanism. (Default for `show()` and the `open` attribute.) |

If unspecified : "if it was opened using showModal(), it behaves as if the value was 'closerequest'; otherwise, it behaves as if the value was 'none'." Use `closedby="any"` to opt into light-dismiss for modal patterns; use `closedby="none"` to force the user to make an explicit choice (confirmation dialogs).

### Escape on custom widgets

Custom popovers and menus that are not `<dialog>` MUST handle Escape explicitly. The browser does NOT fire Esc-close on a `<div role="menu">`. Implementation : `document.addEventListener('keydown', e => { if (e.key === 'Escape' && menuOpen) close(); })`.

The Popover API provides Esc handling for elements with `popover` attribute : "The popover can also be closed, using browser-specific mechanisms such as pressing the Esc key" ([MDN : Popover API : Using](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API/Using) (verified 2026-05-19)).

### Focus restoration to trigger

Per APG dialog-modal : "focus returns to the element that invoked the dialog unless" the invoking element no longer exists or workflow design suggests otherwise (e.g., focusing the first new row after adding rows) ([W3C WAI APG : Dialog Modal](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/) (verified 2026-05-19)).

For `<dialog>` this is NOT automatic. The author MUST capture the trigger before `showModal()` and restore focus on close :

```js
let triggerElement = null;
showButton.addEventListener('click', () => {
  triggerElement = document.activeElement;
  dialog.showModal();
});
dialog.addEventListener('close', () => {
  triggerElement?.focus();
  triggerElement = null;
});
```

For the Popover API, focus restoration IS automatic on Esc-close : "when closing the popover via the keyboard (usually via the Esc key), focus is shifted back to the invoker" ([MDN : Popover API : Using](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API/Using) (verified 2026-05-19)). This works because the popover and its invoker are linked by `popovertarget`.

### Initial focus inside a dialog

Per MDN : "When using HTMLDialogElement.showModal() to open a `<dialog>`, focus is set on the first nested focusable element. Explicitly indicating the initial focus placement by using the autofocus attribute will help ensure initial focus is set on the element deemed the best initial focus placement for any particular dialog" ([MDN : dialog](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog) (verified 2026-05-19)). Add `autofocus` to the primary action button or the close button, NOT to a destructive action.

Per APG dialog-modal pattern, large-content dialogs should give a static element at the start `tabindex="-1"` and focus that element initially, so the screen-reader user can navigate the semantic structure from the top rather than skipping over headings to the first interactive control.

### focus() options

Per [MDN : HTMLElement.focus()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus) (verified 2026-05-19) :

- `preventScroll : true` prevents the browser from scrolling the focused element into view. Useful when programmatically restoring focus to an element that may be off-screen but should not pull the viewport.
- `focusVisible : true` forces a visible focus ring (overriding the heuristic); `false` suppresses it. Use sparingly; the heuristic is usually right.

`focus()` on a non-focusable element is a silent no-op. To make a `<div>` programmatically focusable, give it `tabindex="-1"` first.

---

## 7. Decision matrix : focus management technique by widget

| Widget / scenario | Tabindex strategy | Focus indicator | Background isolation | Escape handling | Focus restoration |
|-------------------|-------------------|-----------------|----------------------|-----------------|-------------------|
| Modal dialog (`<dialog>.showModal()`) | `autofocus` on primary action; container does not take focus | `:focus-visible` on interactive children, 3:1 contrast | Automatic via top-layer + implicit inert on rest | Automatic (Esc closes); customize via `closedby` | Manual : capture trigger, restore on `close` event |
| Non-modal dialog (`<dialog>.show()`) | `autofocus` if appropriate | `:focus-visible` | NONE by default; set `inert` on `<main>` if needed | Author must add keydown handler | Manual : capture and restore |
| Popover (`popover="auto"`) | DOM focus moves to popover content | `:focus-visible` | None (rest of page interactive) | Automatic (Esc closes, click-outside dismisses) | Automatic to invoker on Esc |
| Tabs (`role="tablist"`) | Roving tabindex : active tab `tabindex="0"`, others `-1` | `:focus-visible` on the active tab | None | Not applicable | Not applicable |
| Listbox (`role="listbox"`) | Roving tabindex | `:focus-visible` on highlighted option | None | If used in dropdown, close on Esc | Restore to trigger if combobox |
| Combobox + listbox popup | DOM focus stays on `<input>`; `aria-activedescendant` points to highlighted option | `:focus-visible` on input; visual highlight on aria-activedescendant via attribute selector | None | Esc closes popup, second Esc clears input | Focus stays on input |
| Menubar | Roving tabindex (first item `tabindex="0"`) | `:focus-visible` | None | Esc closes submenu, restores to parent | Restore to button trigger when menu closes |
| Toolbar | Roving tabindex (first item `tabindex="0"`) | `:focus-visible` | None | Not applicable | Not applicable |
| Tree | Roving tabindex | `:focus-visible`; Right/Left expand/collapse | None | Not applicable | Not applicable |
| Grid (`role="grid"`) | Roving tabindex on cells (or rows) | `:focus-visible` on cell | None | Not applicable | Not applicable |
| Treegrid | Roving tabindex; Right/Left expand/collapse rows | `:focus-visible` | None | Not applicable | Not applicable |
| Off-screen carousel slide | None; subtree has `inert` while off-screen | None (subtree is inert) | `inert` attribute | Not applicable | Restore to controls when slide changes |
| Hidden tab panel | None; subtree has `inert` and `hidden` while inactive | None | `inert` + `hidden` | Not applicable | Not applicable |
| Side drawer / off-canvas nav | Drawer focusable; `<main>` gets `inert` while open | `:focus-visible` inside drawer | `inert` on `<main>` and other peers | Esc closes drawer | Restore to drawer-open button |
| Toast notification | Not focusable by default; if action, has `tabindex="0"` | `:focus-visible` on action button | None | Esc dismisses if focusable | Restore to invoking action |
| Skip link | First in DOM, `tabindex` default | `:focus` (visible always when focused) OR `:focus-visible` if pure-keyboard | None | Not applicable | Not applicable |

---

## 8. Anti-patterns

1. **`:focus { outline: none }` without replacement** : per MDN's `:focus-visible` accessibility note and WCAG 2.4.7 Focus Visible, removing the focus indicator without a 3:1-contrast replacement violates WCAG. NEVER ship `outline: none` on a `:focus` selector unless `:focus-visible` provides a compliant replacement.

2. **Positive `tabindex` values** : per MDN tabindex : "Avoid using tabindex values greater than 0... Doing so makes it difficult for people who rely on using keyboard for navigation or assistive technology to navigate and operate page content. Instead, write the document with the elements in a logical sequence." Use DOM order plus `tabindex="0"` (rarely) and `tabindex="-1"` (for programmatic focus and roving tabindex).

3. **`aria-hidden="true"` on the background instead of `inert`** : `aria-hidden` hides from AT but does NOT prevent keyboard focus, click events, find-in-page, or text selection on the subtree. A keyboard user will Tab into an `aria-hidden` subtree and become disoriented. Use `inert` (or `<dialog>.showModal()` which sets inert automatically on the rest).

4. **`pointer-events: none` as a substitute for `inert`** : `pointer-events: none` blocks mouse only. Keyboard focus still lands; AT still announces; find-in-page still matches. It is a paint-level filter, not an interaction model. Use `inert` (or `disabled` for form controls).

5. **Missing `tabindex="-1"` on programmatically-focused container** : `element.focus()` is a silent no-op on non-focusable elements. A `<div role="dialog">` will refuse focus unless given `tabindex="-1"`. Symptom : `dialog.focus()` runs without error, but `document.activeElement` remains the trigger button. (For `<dialog>` element, do NOT add tabindex; the dialog's contents receive focus, per MDN.)

6. **Not restoring focus to trigger on dialog/popover close** : per APG dialog-modal, "focus returns to the element that invoked the dialog." Screen-reader users disoriented when focus jumps to `<body>`. Capture `document.activeElement` before open; call `triggerElement.focus()` on the `close` event. (Automatic for Popover API on Esc-close; manual for `<dialog>`.)

7. **Tabindex chaos via CSS `order` or `flex-direction: row-reverse`** : per MDN tabindex : "Avoid... CSS properties that can change the order of focusable HTML elements." Visual reading order can diverge from DOM order, but focus order ALWAYS follows DOM. Don't reorder visually while DOM stays the same; rewrite DOM if reading order must change.

8. **Focus trap that prevents Escape** : a custom focus-trap implementation that intercepts Esc and prevents the dialog from closing. Per APG : "Escape : Closes the dialog." NEVER swallow Esc. The trap is for Tab and Shift+Tab cycling only.

9. **Roving tabindex without calling `element.focus()`** : moving the `tabindex="0"` attribute between elements does NOT move DOM focus. Authors must ALSO call `element.focus()` after updating the `tabindex` attributes. Symptom : arrow keys silently update markup but the focus ring does not move.

10. **`<div role="button" tabindex="0">` instead of `<button>`** : per MDN tabindex : "The content should be semantically described using interactive elements (`<a>`, `<button>`, `<details>`, `<input>`, `<select>`, `<textarea>`, etc.) instead." Adding `tabindex="0"` to a `<div>` does NOT add Space/Enter activation, disabled state, default form-submit behavior, or implicit button role.

11. **Setting `inert` on the dialog itself while it is open** : per MDN : "Modal `<dialog>`s generated with showModal() escape inertness... but can be made inert by having the inert attribute explicitly set on themselves." Authors who toggle `inert` on the dialog while it is open will make it unfocusable and AT-invisible. NEVER set `inert` on the open dialog.

12. **Light-dismiss without restoring focus** : `closedby="any"` enables click-outside dismissal, but the click-outside path does NOT auto-restore focus. Listen for the `close` event (fired for all dismissal paths) and restore focus there.

---

## 9. Sources Used

| # | URL | Type | Verified |
|---|-----|------|----------|
| 1 | https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/inert | Reference | 2026-05-19 |
| 2 | https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-visible | Reference | 2026-05-19 |
| 3 | https://developer.mozilla.org/en-US/docs/Web/CSS/:focus-within | Reference | 2026-05-19 |
| 4 | https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex | Reference | 2026-05-19 |
| 5 | https://www.w3.org/WAI/ARIA/apg/practices/keyboard-interface/ | Pattern guide | 2026-05-19 |
| 6 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog | Reference | 2026-05-19 |
| 7 | https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/focus | Reference | 2026-05-19 |
| 8 | https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/ | Pattern | 2026-05-19 |
| 9 | https://developer.mozilla.org/en-US/docs/Web/API/Popover_API/Using | Reference | 2026-05-19 |
| 10 | https://www.w3.org/WAI/ARIA/apg/patterns/tabs/ | Pattern | 2026-05-19 |
| 11 | https://www.w3.org/WAI/ARIA/apg/patterns/grid/ | Pattern | 2026-05-19 |

All sources are in SOURCES.md "Primary Sources" table (MDN Web Docs and W3C WAI APG). No new sources discovered this round.

Cross-cutting decisions surfaced :

- `inert` is the modern foundation. The skill `frontend-a11y-focus-keyboard-inert` MUST teach `inert` first, then layer `:focus-visible`, tabindex, roving tabindex, and the APG keyboard tables.
- `<dialog>.showModal()` provides automatic inert and Tab-cycling (focus-trap is browser-provided). Custom focus-trap implementations are a legacy pattern; teach `<dialog>` first.
- `closedby` attribute (new) lets authors opt into light-dismiss for modal dialogs. Pair with manual focus restoration via the `close` event listener.
- Popover API auto-restores focus to invoker on Esc-close; `<dialog>` does NOT. The skill must call this out explicitly.

Open items for the SKILL.md author :

- Decision tree in Quick Reference per masterplan instructions (focus style branch, inert / `<dialog>` branch, composite-widget branch).
- Code example : focus-trapped modal via `<dialog>` + `showModal()` showing automatic `inert` on background, `closedby="closerequest"` Esc handling, and manual focus restoration on close.
- Code example : roving tabindex implementation for a 5-tab `role="tablist"`, with `:focus-visible` styles meeting 3:1 contrast.
