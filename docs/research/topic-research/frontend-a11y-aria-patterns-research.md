# Topic Research : frontend-a11y-aria-patterns

## Status

Author : Phase 4 topic-research agent
Date : 2026-05-19
Skill target : `frontend-a11y-aria-patterns`
Batch : 5 (accessibility)
Closes verification gap : §14 #8 (drill remaining APG patterns)
Input docs read : `docs/research/vooronderzoek-frontend.md` §6 + §14, `SOURCES.md`, `docs/masterplan/frontend-masterplan.md` (`### Skill : frontend-a11y-aria-patterns` block)
APG patterns drilled this round : Carousel, Disclosure, Listbox, Menu / Menubar, Radio Group, Tree, Treegrid
APG patterns expanded from vooronderzoek : Combobox, Dialog (Modal), Tabs
Supporting normative sources drilled : WAI-ARIA 1.2 (W3C TR), ARIA in HTML (W3C TR), APG Alert pattern
WebFetch citations in this doc : 11 unique source URLs

## 1. First rule of ARIA + when native suffices

The single most important sentence in accessibility engineering is the first rule of ARIA. The [WAI-ARIA 1.2 specification](https://www.w3.org/TR/wai-aria-1.2/) (verified 2026-05-19) states normatively : "WAI-ARIA is intended to be used as a supplement for native language semantics, not a replacement. When the host language provides a feature that provides equivalent accessibility to the WAI-ARIA feature, use the host language feature. WAI-ARIA should only be used in cases where the host language lacks the needed role, state, and property indicators." In practice this means : reach for ARIA only after confirming that no HTML element already does the job.

The [ARIA in HTML W3C TR](https://www.w3.org/TR/html-aria/) (verified 2026-05-19) puts a complementary constraint on authors : "Authors MUST NOT use the ARIA `role` and `aria-*` attributes in a manner that conflicts with the semantics" of the underlying HTML element. Two failure modes follow from violating this rule. First, redundancy : `<nav role="navigation">`, `<button role="button">`, `<main role="main">`, `<h1 role="heading">`, `<form role="form">` add cost without value because the implicit role already exists. Second, conflict : `<button role="heading">` or `<a role="button" href="...">` strip the native interaction semantics that screen readers, keyboard users, and the form-submission pipeline depend on, and produce widgets that announce wrong, focus wrong, or fail to participate in default behaviors.

Implicit ARIA semantics MUST be the default mental model. The mapping table in ARIA in HTML assigns implicit roles to elements such as : `h1`-`h6` -> `heading` (with `aria-level` derived from the level), `button` -> `button`, `a[href]` -> `link`, `nav` -> `navigation`, `main` -> `main`, `header` (top-level) -> `banner`, `footer` (top-level) -> `contentinfo`, `aside` -> `complementary`, `article` -> `article`, `section` (with accessible name) -> `region`, `form` (with accessible name) -> `form`, `input[type=text]` -> `textbox`, `textarea` -> `textbox`, `select` -> `combobox` or `listbox`, `dialog` -> `dialog`, `details` -> `group`. Use these elements first; reach for ARIA only when the widget has no native HTML equivalent (combobox with custom popup, treegrid, listbox with multi-select, carousel, treeview, menu with submenus, custom dialog when `<dialog>` cannot be used due to layout constraints).

The decision flow is binary : (1) Does a single HTML element semantically and behaviorally satisfy the requirement? If yes, ship it with zero ARIA. (2) Does a composition of HTML elements satisfy it (e.g. `<button>` + `<dialog>` + `popovertarget`)? If yes, ship that. (3) Only when both fail, layer ARIA on top, and only the minimum set of attributes required by the relevant APG pattern.

## 2. Combobox + Dialog + Tabs (expansion of vooronderzoek)

This section expands the three APG patterns already covered in `vooronderzoek-frontend.md` §6 with details required by the skill.

**Dialog (Modal)** per [APG : Dialog Modal](https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/) (verified 2026-05-19, vooronderzoek §6). Required attributes : `role="dialog"` (or `role="alertdialog"` for confirmation prompts), `aria-modal="true"`, and an accessible name via either `aria-labelledby` (pointing to a visible heading inside the dialog) or `aria-label`. Required behaviors : (a) focus MUST move into the dialog on open, typically to the first useful interactive element OR to the heading made focusable with `tabindex="-1"` to give screen-reader users context; (b) Tab and Shift+Tab MUST cycle WITHIN the dialog (focus trap); (c) Escape MUST close the dialog; (d) on close, focus MUST return to the trigger element unless the workflow logically dictates otherwise. In modern HTML, `<dialog>` + `showModal()` provides 3 of these 4 behaviors natively (focus trap, Escape close, and inert-on-rest are automatic). The fourth (focus restore on close) MUST still be implemented by the author. Using `<dialog>` natively makes `aria-modal="true"` and `role="dialog"` redundant since they are implicit; the only required ARIA is `aria-labelledby` or `aria-label`.

**Combobox** per [APG : Combobox](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/) (verified 2026-05-19, vooronderzoek §6). The combobox element (an input or button) carries `role="combobox"` (only if not a native `<input>` whose `list` attribute already does the work; native `<input list="...">` + `<datalist>` is the zero-ARIA path), with `aria-controls` referencing the popup, `aria-expanded` reflecting popup state, `aria-autocomplete` (`none` | `list` | `both`) describing autosuggest behavior, and `aria-haspopup` (`listbox` is implicit and may be omitted; `grid`, `tree`, or `dialog` MUST be set explicitly). Focus management depends on popup type. For `listbox`, `grid`, and `tree` popups, DOM focus remains on the combobox input and `aria-activedescendant` references the highlighted option's ID; this is REQUIRED, not optional, because moving DOM focus into a listbox while the user is typing breaks typing flow. For `dialog` popups (e.g. date picker), DOM focus MUST move into the dialog. Keyboard model : Down opens popup AND moves to first option; Up moves to last; Enter accepts; Escape closes (and per APG MAY also clear the input). Alt+Down opens popup without moving selection; Alt+Up commits and closes.

**Tabs** per [APG : Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) (verified 2026-05-19, vooronderzoek §6). Roles : container `role="tablist"`, each tab `role="tab"`, each panel `role="tabpanel"`. State : `aria-selected="true"` on the active tab, `aria-selected="false"` on the rest; `aria-controls` on each tab references its panel's ID; each panel's `aria-labelledby` references its tab's ID. Focus management : roving tabindex — exactly one tab has `tabindex="0"` (the selected one), all others have `tabindex="-1"`. Keyboard : Left/Right (horizontal) or Up/Down (vertical) move between tabs; Home/End jump to first/last; Tab from tab moves out of tablist into the panel. Activation : automatic (focus implies activation) is the default and the better UX when activation has no cost; manual (Space/Enter activates) is REQUIRED when activation has side effects such as network requests, expensive renders, or analytics events.

## 3. Carousel pattern

Per [APG : Carousel](https://www.w3.org/WAI/ARIA/apg/patterns/carousel/) (verified 2026-05-19). The carousel container MUST have `role="region"` or `role="group"`, with `aria-roledescription="carousel"` to give screen-reader users a domain-specific landmark name. Labelling follows the standard precedence : `aria-labelledby` pointing to a visible heading OR `aria-label` if no visible label exists; note the spec rule that "since the `aria-roledescription` is set to 'carousel', the label does not contain the word 'carousel'" (avoid stuttered announcements like "Featured Articles carousel, carousel").

Three structural variants exist : (1) Basic carousel with Prev/Next buttons only, slides as `role="group"` + `aria-roledescription="slide"`; (2) Carousel with slide-picker buttons (one button per slide, current slide's button has `aria-disabled="true"`, which is preferred over the HTML `disabled` attribute because disabled-via-HTML removes the button from the Tab sequence and disorients screen-reader users); (3) Tabbed carousel where slides are `role="tabpanel"` and the picker is a full Tabs pattern (`role="tablist"` with `aria-label` such as "Choose slide to display").

Autoplay rules are the most-failed part of this pattern. When the carousel auto-rotates : (a) the rotation control button MUST have a dynamic accessible name that changes between "Stop slide rotation" and "Start slide rotation" — `aria-pressed` is explicitly NOT used here because the spec states "[the button] does not have any states, e.g., `aria-pressed`, specified"; (b) auto-rotation MUST stop when keyboard focus enters the carousel and MUST stop when the pointer hovers over the carousel; (c) the slide wrapper MAY carry `aria-live="off"` while auto-rotating and switch to `aria-live="polite"` when paused (with `aria-atomic="false"`), so that the slide change is only announced when the user has actively paused; (d) auto-rotation that stops on focus MUST only resume by explicit user action, never automatically.

Slide accessible names follow a graceful-degradation rule : if each slide has a unique, meaningful name, use it; if not, "a number and set size can serve as a meaningful alternative, e.g., '3 of 10'." This is acceptable because it gives the user position context even when slide content lacks a natural title.

Keyboard model for non-tabbed carousels : Tab navigates the standard page order; Prev/Next buttons are activated by Enter or Space; no arrow-key navigation is required on the carousel container itself (arrow keys remain available to scroll the page). For tabbed carousels, the picker IS a tablist and inherits the full Tabs pattern keyboard model (Left/Right between tabs, Home/End, automatic-vs-manual activation).

The most common production violations are : an auto-rotating carousel that does not pause on focus (WCAG 2.2.2 violation, "Pause, Stop, Hide"), a carousel container without `aria-roledescription="carousel"` (announces as a generic region), and slide-picker buttons that use the HTML `disabled` attribute instead of `aria-disabled="true"` (removes from Tab sequence).

## 4. Disclosure pattern

Per [APG : Disclosure](https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/) (verified 2026-05-19). The disclosure pattern is the simplest interactive ARIA pattern : a single button toggles the visibility of a content region. The minimal contract is : (a) the trigger MUST behave as a button (use `<button>` element; if that is impossible for layout reasons, `role="button"` + `tabindex="0"` + keyboard handlers for Enter and Space); (b) the trigger MUST carry `aria-expanded="true"` when the region is visible and `aria-expanded="false"` when hidden; (c) the trigger MAY carry `aria-controls` referencing the disclosed region's ID, although browser support for `aria-controls` is inconsistent and the attribute is optional per APG.

Disclosure differs from three adjacent patterns. Versus **Accordion** : a disclosure is a single trigger / single region; an accordion is a coordinated set of disclosures with optional single-open-at-a-time behavior and conventional Up/Down arrow navigation between headers. Versus **HTML `<details>` / `<summary>`** : the native element does everything the disclosure pattern does without any ARIA at all; ship `<details>` whenever the toggled region is static body content and the trigger label can be expressed as inline `<summary>` content. Versus **Dialog** : a dialog is modal and requires focus trap + Escape + inert background; a disclosure is non-modal, does not trap focus, and does not block the rest of the page.

Keyboard model : Enter and Space MUST activate the trigger (free with `<button>`; manual if `role="button"` on non-button). No other keys are required. The disclosed region itself takes no special role or live-region attributes.

Anti-pattern alert : do NOT add `role="region"` to the disclosed region unless it is a true page landmark. Adding extraneous landmark roles pollutes the screen-reader landmark navigation map.

## 5. Listbox pattern (single + multi)

Per [APG : Listbox](https://www.w3.org/WAI/ARIA/apg/patterns/listbox/) (verified 2026-05-19). The listbox is a presentation of options where the user selects one (single-select) or more (multi-select). The container has `role="listbox"`, each option has `role="option"`, options MAY be grouped with `role="group"` containers carrying their own `aria-label` or `aria-labelledby`.

Single vs multi-select : `aria-multiselectable="true"` on the listbox container declares the listbox as multi-select; absent the attribute or set to `false`, the listbox is single-select. Selection state is exposed via `aria-selected` (or `aria-checked` in patterns that combine listbox with checkbox semantics, but never both attributes in the same listbox). Convention : `aria-selected="true"` on selected options and `aria-selected="false"` on selectable-but-unselected options. Unselectable options (read-only items in the same list) omit the attribute entirely so that screen readers do not announce them as toggleable.

Focus management : two valid patterns. **Roving tabindex** moves DOM focus among options (only the currently-focused option has `tabindex="0"`, others `tabindex="-1"`). **`aria-activedescendant`** keeps DOM focus on the listbox container itself and uses an ID reference to the visually-highlighted option. Both are valid; the choice depends on context. Listboxes embedded inside comboboxes MUST use `aria-activedescendant` (per the Combobox pattern, DOM focus stays on the input). Standalone listboxes can use either.

Keyboard model (standalone listbox) : Up / Down arrows move focus; Home / End jump to first / last (recommended when the list exceeds 5 items); type-ahead — typing a printable character moves focus to the next option whose accessible name starts with that character. Multi-select additions : Space toggles selection of the focused option; Shift+Up/Down extends selection; Ctrl+A toggles select-all (optional but recommended). For horizontal listboxes set `aria-orientation="horizontal"` (default is vertical) and remap arrow keys to Left/Right.

Labelling rules : standalone listboxes MUST have an accessible name via `aria-label` or `aria-labelledby`. Listboxes embedded in a combobox inherit labelling from the combobox input's label and do not need a separate name. For required listboxes that participate in form validation, `aria-required="true"` on the listbox container exposes the requirement; for invalid state, `aria-invalid="true"` plus `aria-errormessage` (linked to a visible error message with id) form the modern error-binding pattern (recall : `aria-errormessage` requires `aria-invalid="true"` to take effect).

## 6. Menu pattern (menu + menubar + menuitem variants)

Per [APG : Menu and Menubar](https://www.w3.org/WAI/ARIA/apg/patterns/menu/) (verified 2026-05-19). The menu pattern represents a list of commands or choices, typically opened from a button or persistently visible as a menubar. Roles : container is `role="menu"` (popup menu, hidden by default) or `role="menubar"` (visually persistent, typically horizontal at the top of an application region). Child roles : `role="menuitem"` for a plain command, `role="menuitemcheckbox"` for a toggleable command, `role="menuitemradio"` for one-of-N within a group, `role="separator"` to visually and semantically divide groups, `role="group"` to wrap a set of radio items.

Trigger relationship : the button that opens a menu carries `aria-haspopup="menu"` (or the legacy value `"true"`, which is equivalent), with `aria-expanded="false"` while the menu is closed and `aria-expanded="true"` while open. For triggers that open submenus from within an existing menu, the parent `menuitem` MUST carry the same `aria-haspopup` and `aria-expanded` pattern.

State : `aria-checked="true"` on a checked `menuitemcheckbox` and on the single chosen `menuitemradio` within a group; `aria-checked="false"` on the unchecked items of either kind. Disabled items carry `aria-disabled="true"` (preferred over the HTML `disabled` attribute, which removes the element from the Tab sequence and confuses orientation). Orientation : `aria-orientation="vertical"` on a menubar that runs vertically; default for menubar is horizontal, default for menu is vertical.

Focus model : roving tabindex. The spec is explicit : "Each item in the menu has `tabindex` set to `-1`, except in a menubar, where the first item has `tabindex` set to `0`." DOM focus moves between items; `aria-activedescendant` is NOT used in the menu pattern (this is the most common confusion with listbox).

Keyboard model : Enter activates a menuitem (or opens a submenu if it has one); Space behaves the same. Down / Up move focus within a menu (vertical) and from a menubar item into the menu (Down). Right / Left move across menubar items, OR open submenus (Right) and close them (Left) inside a menu. Escape closes the menu and returns focus to the menu's opener (the menu button or the parent menubar item). Home / End move to first / last visible item. Type-ahead : typing a printable character moves focus to the next item whose label begins with that character; if the menu is open more than 500 ms, the buffer resets.

Common production failure : building "menus" as `role="menu"` for ordinary page navigation. The `role="menu"` is intended for command lists (like an application menu : File, Edit, View). Plain page navigation belongs in a `<nav>` element with a `<ul>` of `<a>` links, with NO `role="menu"` and NO arrow-key navigation. Misapplying the menu role to navigation links creates a non-standard keyboard model (arrow keys instead of Tab) that confuses both keyboard and screen-reader users.

## 7. Radio Group pattern

Per [APG : Radio Group](https://www.w3.org/WAI/ARIA/apg/patterns/radio/) (verified 2026-05-19). The radio group is a fundamental form pattern, but the first-rule-of-ARIA reflex applies hard : native `<fieldset><legend>Group</legend> <input type="radio">` is the correct path for almost all production form usage, and it requires zero ARIA. The ARIA pattern is only needed when the radios are not part of an HTML form (e.g. an in-app preference selector) or when the visual presentation requires structures that `<input>` cannot accommodate.

ARIA-based structure : container `role="radiogroup"` with child elements `role="radio"`. Selection state : `aria-checked="true"` on the selected radio, `aria-checked="false"` on the rest, mutually exclusive within the group. Labelling : `aria-labelledby` pointing to a visible group label, or `aria-label` if no visible label exists. For required groups : `aria-required="true"` on the radiogroup container.

Single-tabstop focus model : the selected radio has `tabindex="0"`; if no radio is selected, the FIRST radio has `tabindex="0"`; all other radios have `tabindex="-1"`. This means Tab moves into the group exactly once, then arrow keys take over. Tab again moves out of the group entirely.

Keyboard model (standalone, not in toolbar) : arrow keys (Up / Down / Left / Right — all four work regardless of orientation) move focus to the next or previous radio AND select that radio immediately (the "focus = selection" model). Space selects the focused radio if it is not already checked. When a radio group is nested inside a toolbar context, the behavior differs : arrow keys still move focus but do NOT change selection, and Space or Enter then commits the selection — this avoids accidental selection while navigating a toolbar.

Most-failed detail : the focus-equals-selection model. Engineers often build radio groups where arrows move focus but Space is required to select. This deviates from APG and from native `<input type="radio">` behavior and breaks user expectation.

## 8. Tree + Treegrid patterns

**Tree** per [APG : Tree View](https://www.w3.org/WAI/ARIA/apg/patterns/treeview/) (verified 2026-05-19). A tree presents hierarchical data, typically used for file browsers, navigation outlines, and IFC spatial structures. Container : `role="tree"` with `aria-label` or `aria-labelledby`. Children : `role="treeitem"`. Child groups : `role="group"` element containing nested `treeitem` children of an expandable parent.

State on each treeitem : `aria-expanded="false"` (collapsed) or `aria-expanded="true"` (expanded) on parent nodes only — leaf nodes MUST NOT carry `aria-expanded`. Selection (when supported) : `aria-selected="true"` on selected nodes, `aria-selected="false"` on selectable-but-unselected, omitted on non-selectable nodes. For multi-select trees, `aria-multiselectable="true"` on the root tree container. Hierarchy metadata is REQUIRED only when nodes are not all present in the DOM (lazy loading) : `aria-level` (1-based integer depth), `aria-posinset` (1-based position within parent), `aria-setsize` (count of siblings).

Focus model : roving tabindex (one node has `tabindex="0"`, others `tabindex="-1"`). NOT `aria-activedescendant` (this is the same convention as menus and tabs, opposite of listbox-in-combobox).

Keyboard model : Up / Down move focus to the previous / next visible node (skipping collapsed subtrees); Right Arrow on a collapsed parent expands it, on an expanded parent moves to the first child, on a leaf does nothing; Left Arrow on an expanded parent collapses it, on a leaf or collapsed parent moves to the parent; Home / End jump to first / last visible node; Enter activates the node (open the file, run the command); Space toggles selection in multi-select trees; type-ahead matches printable characters against node labels; the asterisk key `*` MAY optionally expand all siblings of the focused node (the only optional key in the standard model).

**Treegrid** per [APG : Treegrid](https://www.w3.org/WAI/ARIA/apg/patterns/treegrid/) (verified 2026-05-19). A treegrid is the union of grid and tree : tabular data with hierarchical rows that can be expanded and collapsed. The container is `role="treegrid"`. Rows are `role="row"`, optionally wrapped in `role="rowgroup"`. Cells are `role="gridcell"`, `role="rowheader"`, or `role="columnheader"`. Expandable rows carry `aria-expanded` on either the row or one of its cells; per spec, "aria-expanded state is set to `false` when the child rows are not displayed and set to `true` when the child rows are displayed."

Treegrid uniquely supports two focus modes that are author choices : cell-focus mode (DOM focus moves between cells) or row-focus mode (DOM focus moves between rows). Both use single tabstop : exactly one focusable element within the treegrid has `tabindex="0"`, all others `-1`. The selection state is independent of focus state in multi-select treegrids — focus indicates the current navigation point, selection (via `aria-selected="true"`) is a separate user action.

Keyboard model (2D plus expand/collapse) : arrow keys move between cells (Left/Right) and rows (Up/Down); Right Arrow on a collapsed parent row expands it, Left Arrow on an expanded parent row collapses it (this is the key difference from a plain grid, where Left/Right only navigate cells); Home / End move within the current row; Ctrl+Home / Ctrl+End jump to the first / last cell of the treegrid; Page Up / Page Down move by a page; Enter activates the focused cell or row (semantics defined by the author); F2 enters edit mode for editable cells; Escape exits edit mode. For multi-select treegrids, Shift+arrow extends row or cell selection and Ctrl+Space toggles selection of the focused row.

`aria-multiselectable="true"` on the root enables multi-row or multi-cell selection. Hierarchical metadata (`aria-level`, `aria-posinset`, `aria-setsize`) applies to rows the same way it applies to treeitems in a plain tree, REQUIRED when rows are not all present in the DOM.

## 9. Live regions + labelling

**Live regions** per [WAI-ARIA 1.2](https://www.w3.org/TR/wai-aria-1.2/) (verified 2026-05-19). A live region is a DOM area whose updates the screen reader announces without the user having navigated focus into the region. Four attributes coordinate this announcement.

`aria-live` declares politeness : `"off"` (no announcement, the default), `"polite"` (announce when the user is idle, do not interrupt the current utterance), `"assertive"` (interrupt immediately). Use `"polite"` for status messages ("Saved", "3 results found"), `"assertive"` ONLY for genuinely time-critical alerts (session expiring in 60 seconds, payment failed); over-using assertive is hostile to screen-reader users.

`aria-atomic` controls scope : `"false"` (default; only the changed nodes are read) or `"true"` (the entire live region is re-read on every change). Set `aria-atomic="true"` when the region is short and the surrounding text gives context (e.g. "Score: 5" -> "Score: 6"); leave it false when the region is long and only deltas matter.

`aria-relevant` filters which change types announce : `"additions"`, `"removals"`, `"text"`, `"all"`. Default is `"additions text"`. Rarely needs to be overridden.

`aria-busy="true"` on a live region tells the screen reader "stop announcing changes until I clear this." Useful when batching multiple DOM updates ; set busy, mutate the DOM, set busy false, and the screen reader reads the final coherent state.

The two pre-baked live-region roles : `role="status"` is implicit `aria-live="polite"` with `aria-atomic="true"`; `role="alert"` is implicit `aria-live="assertive"` with `aria-atomic="true"`. Per [APG : Alert](https://www.w3.org/WAI/ARIA/apg/patterns/alert/) (verified 2026-05-19), alerts MUST NOT auto-dismiss (WCAG 2.2.3 violation) and MUST be used sparingly to comply with WCAG 2.2.4 (frequent interruptions degrade usability).

Critical implementation detail : the live region element MUST exist in the DOM BEFORE the content is inserted, otherwise many screen readers do not detect the change and the announcement never fires. Render an empty `<div role="status" aria-live="polite" aria-atomic="true"></div>` at page load; update its `textContent` when you need to announce.

**Labelling** per WAI-ARIA 1.2 accessible-name computation precedence : `aria-labelledby` (highest) > `aria-label` > native labelling (HTML `<label>`, `alt`, `title`) > element content text > tooltip attributes. Practical rules : (a) if a visible label exists, use `aria-labelledby` pointing to it (NOT `aria-label`, because `aria-label` overrides and creates a divergence between the visible text and the announced text); (b) `aria-describedby` adds supplementary description AFTER the name (announced as a follow-up, often suppressible by SR users); (c) `aria-errormessage` references a visible error message and REQUIRES `aria-invalid="true"` on the same element to take effect — without `aria-invalid`, the errormessage is silently ignored.

## 10. Decision matrix : which APG pattern for which UI

| UI concept | Native HTML first? | APG pattern if no native fit | Notes |
|------------|--------------------|------------------------------|-------|
| Show/hide a section under a button | `<details><summary>` | Disclosure | Use native unless the trigger needs to live outside the disclosed region |
| Modal blocking dialog | `<dialog>` + `showModal()` | Dialog (Modal) | Native gives focus trap, Escape, inert-on-rest for free |
| Tabbed content panels | none | Tabs | Roving tabindex, manual activation when activation has cost |
| Dropdown select | `<select>` | Combobox + Listbox | Native unless custom rendering of options required |
| Autocomplete / search-as-you-type | `<input list>` + `<datalist>` | Combobox with listbox popup, `aria-activedescendant` | Native fails for rich option rendering |
| Single-choice within group | `<input type=radio>` + `<fieldset>` | Radio Group | Native unless non-form context |
| Multi-choice list | `<select multiple>` or checkboxes | Listbox with `aria-multiselectable=true` | Listbox needed for custom option content |
| Application command menu | none (no native menu element) | Menu / Menubar | Roving tabindex, NOT for navigation links |
| Site or section navigation | `<nav><ul><li><a>` | none (do NOT use Menu) | Navigation is plain links, not commands |
| Notification banner | none | Live region `role="status"` or `role="alert"` | Empty region MUST exist before update |
| Image carousel / hero slider | none | Carousel | Pause on focus, pause on hover, dynamic Stop/Start label |
| Hierarchical file/folder tree | none | Tree | Roving tabindex, NOT `aria-activedescendant` |
| Hierarchical data table | `<table>` for flat data | Treegrid | Only when hierarchy AND tabular both required |
| Toggle (on/off) | `<input type=checkbox>` | Switch (`role="switch"`) | Switch is checkbox-with-different-presentation |
| Progress feedback | `<progress>` or `<output>` | `role="progressbar"` | Native first |

## 11. Anti-patterns (ARIA misuse)

1. **`<div role="button" onclick="...">` instead of `<button>`** : symptom — keyboard users cannot activate, no focus by default, no `:focus-visible`, no implicit form-submit handling : root cause — reflex to style div before remembering button can be styled identically : fix — use `<button type="button">` and style with CSS.

2. **`<nav role="navigation">`** : symptom — none functionally, but cost in author time and a noise signal that the author does not understand implicit roles : root cause — copy-paste from outdated tutorials : fix — remove the redundant role; `<nav>` is already `role="navigation"`.

3. **Tabs with every tab `tabindex="0"`** : symptom — Tab key walks through every tab one by one before reaching the panel; screen-reader users perceive a long flat list of tabs instead of a tablist : root cause — failing to implement roving tabindex : fix — only the selected tab gets `tabindex="0"`, all others `tabindex="-1"`; arrow keys move between tabs.

4. **Combobox listbox popup using DOM focus on options** : symptom — typing in the input loses focus when an option highlights; screen reader announces "out of input" : root cause — moving DOM focus into the listbox instead of using `aria-activedescendant` : fix — DOM focus stays on the combobox input; `aria-activedescendant` references the highlighted option's id.

5. **`aria-label` on an element that already has visible text** : symptom — screen reader announces text different from what is visible; if the `aria-label` is stale, sighted and screen-reader users see different things : root cause — `aria-label` overrides native label per name-computation precedence : fix — use `aria-labelledby` pointing to the visible text element, or remove the `aria-label` entirely.

6. **`role="alert"` polled via `setInterval`** : symptom — screen reader interrupts the user every interval, even when nothing changed : root cause — using assertive live region for non-urgent status : fix — use `role="status"` (polite) for periodic updates; reserve `role="alert"` for genuine time-critical events.

7. **Missing focus restore on dialog close** : symptom — focus jumps to `<body>` on close, screen-reader users lose their place : root cause — `<dialog>` + `showModal()` does not restore focus to the trigger automatically : fix — capture `document.activeElement` before `showModal()`, call `.focus()` on it after `close()`.

8. **Live region inserted into DOM at the moment of update** : symptom — announcement does not fire on most screen readers : root cause — the region must exist BEFORE the change for the SR to observe the mutation : fix — render an empty `<div role="status" aria-live="polite" aria-atomic="true"></div>` at page load, mutate its `textContent` when you announce.

9. **`role="menu"` on a navigation list of links** : symptom — Tab does not work between items, arrow keys are required, screen reader announces "menu" for a list of `<a>` : root cause — confusing application menus (commands) with navigation menus (links) : fix — `<nav>` + `<ul>` + `<a>`; no `role="menu"`, no arrow keys.

10. **`aria-hidden="true"` on the background while a custom modal is open** : symptom — focusable elements in the background are still reachable by Tab, even though screen readers cannot see them : root cause — `aria-hidden` hides from the AT but does NOT remove from sequential focus : fix — use the `inert` attribute on the background, which removes BOTH focusability and AT-visibility (see `frontend-a11y-focus-keyboard-inert`).

## 12. Sources Used

| # | Source URL | Verified | Extracted |
|---|------------|----------|-----------|
| 1 | https://www.w3.org/TR/wai-aria-1.2/ | 2026-05-19 | first rule of ARIA, role categories, live-region attributes, accessible-name precedence, role-overrides-implicit |
| 2 | https://www.w3.org/TR/html-aria/ | 2026-05-19 | implicit ARIA semantics for HTML elements, author conformance, redundant-role examples |
| 3 | https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/ | 2026-05-19 | role+aria-modal, focus trap, Escape, initial/restore focus |
| 4 | https://www.w3.org/WAI/ARIA/apg/patterns/combobox/ | 2026-05-19 | role=combobox, aria-activedescendant vs DOM focus, keyboard model |
| 5 | https://www.w3.org/WAI/ARIA/apg/patterns/tabs/ | 2026-05-19 | tablist/tab/tabpanel, roving tabindex, auto-vs-manual activation |
| 6 | https://www.w3.org/WAI/ARIA/apg/patterns/carousel/ | 2026-05-19 | aria-roledescription=carousel, dynamic Stop/Start label (no aria-pressed), pause on focus/hover, slide group/tabpanel variants |
| 7 | https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/ | 2026-05-19 | role=button + aria-expanded, optional aria-controls, Enter/Space activation |
| 8 | https://www.w3.org/WAI/ARIA/apg/patterns/listbox/ | 2026-05-19 | role=listbox/option, aria-multiselectable, aria-activedescendant vs roving tabindex, keyboard model |
| 9 | https://www.w3.org/WAI/ARIA/apg/patterns/menu/ | 2026-05-19 | menu vs menubar, menuitem variants, aria-haspopup, aria-expanded, aria-checked, roving tabindex |
| 10 | https://www.w3.org/WAI/ARIA/apg/patterns/radio/ | 2026-05-19 | role=radiogroup/radio, aria-checked, single-tabstop, arrow-key focus-equals-selection |
| 11 | https://www.w3.org/WAI/ARIA/apg/patterns/treeview/ | 2026-05-19 | role=tree/treeitem/group, aria-expanded, aria-level/posinset/setsize, roving tabindex, asterisk expand-siblings |
| 12 | https://www.w3.org/WAI/ARIA/apg/patterns/treegrid/ | 2026-05-19 | role=treegrid, cell vs row focus mode, 2D nav with Left/Right collapse/expand, Ctrl+Home/Ctrl+End, F2 edit mode |
| 13 | https://www.w3.org/WAI/ARIA/apg/patterns/alert/ | 2026-05-19 | implicit aria-live=assertive + aria-atomic=true, no auto-dismiss (WCAG 2.2.3), avoid overuse (WCAG 2.2.4) |
| 14 | https://www.w3.org/WAI/ARIA/apg/patterns/ | 2026-05-19 (vooronderzoek) | full APG pattern index, scope of patterns |

**Sources newly added to SOURCES.md by this research** :
- https://www.w3.org/TR/wai-aria-1.2/ (was Not yet, NOW verified 2026-05-19)
- https://www.w3.org/TR/html-aria/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/carousel/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/listbox/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/menu/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/radio/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/treeview/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/treegrid/ (NEW)
- https://www.w3.org/WAI/ARIA/apg/patterns/alert/ (NEW)
