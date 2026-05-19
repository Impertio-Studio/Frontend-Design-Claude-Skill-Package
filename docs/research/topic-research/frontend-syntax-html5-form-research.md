# Topic Research : frontend-syntax-html5-form

## Status

Drill-down research for the `frontend-syntax-html5-form` skill. Produced for Phase 4 of the 7-phase methodology. Source-verified against MDN, WHATWG HTML Living Standard, Open UI Community Group, and W3C WAI ARIA. All citations dated `verified 2026-05-19`. Closes the §14 verification gap items 7 (Open UI status of select/button customization) and adds coverage of form-associated custom elements via ElementInternals (deferred from `frontend-impl-web-components` but cross-referenced from the form skill).

## 1. Input type matrix

The HTML `<input>` element supports a single attribute (`type`) that switches the element between 22 distinct rendering and behavior modes. Per [MDN : `<input>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input) (verified 2026-05-19), all 22 types are Baseline Widely Available in evergreen-2026, but their visual UI and constraint behavior differ significantly per type and per OS. The complete matrix follows.

| Type | Purpose | Baseline | Validation flags activated | Notes |
|------|---------|----------|----------------------------|-------|
| `text` | Single-line text (default) | Widely | `valueMissing`, `tooShort`, `tooLong`, `patternMismatch` | Default when `type` is omitted or invalid |
| `email` | Email address | Widely | `valueMissing`, `typeMismatch`, `tooShort`, `tooLong`, `patternMismatch` | `multiple` attribute allows comma-separated list |
| `url` | Absolute URL | Widely | `valueMissing`, `typeMismatch`, `tooShort`, `tooLong`, `patternMismatch` | Must be absolute (scheme + host) |
| `tel` | Telephone | Widely | `valueMissing`, `tooShort`, `tooLong`, `patternMismatch` | NO format validation by spec, only structural attrs |
| `number` | Numeric (with spinner) | Widely | `valueMissing`, `rangeUnderflow`, `rangeOverflow`, `stepMismatch`, `badInput` | `min`, `max`, `step`; mobile shows numeric keypad |
| `range` | Slider (no text input) | Widely | `rangeUnderflow`, `rangeOverflow`, `stepMismatch` | Defaults : `min=0`, `max=100`, `step=1` |
| `date` | Calendar (Y-M-D) | Widely | `valueMissing`, `rangeUnderflow`, `rangeOverflow`, `stepMismatch`, `badInput` | Locale-dependent UI; value is always ISO 8601 |
| `time` | Hour-minute (optionally seconds) | Widely | `valueMissing`, `rangeUnderflow`, `rangeOverflow`, `stepMismatch`, `badInput` | 24-hour ISO format on the wire |
| `datetime-local` | Date + time | Widely | `valueMissing`, `rangeUnderflow`, `rangeOverflow`, `stepMismatch`, `badInput` | NO timezone; treat as local |
| `month` | Year + month | Widely | `valueMissing`, `rangeUnderflow`, `rangeOverflow`, `stepMismatch`, `badInput` | Less browser support for picker UI |
| `week` | Year + ISO week number | Widely | `valueMissing`, `rangeUnderflow`, `rangeOverflow`, `stepMismatch`, `badInput` | Format `YYYY-Www` |
| `color` | Color picker | Widely | NONE | Returns `#rrggbb` hex; no alpha |
| `search` | Search box | Widely | `valueMissing`, `tooShort`, `tooLong`, `patternMismatch` | UA may add clear button + history dropdown |
| `password` | Obscured text | Widely | `valueMissing`, `tooShort`, `tooLong`, `patternMismatch` | NEVER persist via `value` attribute; respect password managers |
| `file` | File upload | Widely | `valueMissing` | `accept`, `multiple`, `capture` attrs |
| `hidden` | Server-side state | Widely | NONE | Submitted, never validated, never focused |
| `image` | Graphical submit | Widely | NONE | Submits `name.x` and `name.y` click coords. Legacy. |
| `checkbox` | Toggle | Widely | `valueMissing` (when `required`) | `checked` is initial; `:checked` reflects current |
| `radio` | Single-of-group | Widely | `valueMissing` per `name` group | Shares state via `name` attribute |
| `submit` | Form submission | Widely | NONE | Triggers form `submit` event + validation |
| `reset` | Form reset | Widely | NONE | Fires `reset` event; restores default attrs |
| `button` | Generic button (no default action) | Widely | NONE | Prefer `<button>` element over `<input type="button">` |

Per [MDN : Constraint validation](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Constraint_validation) (verified 2026-05-19), built-in `type="email"` and `type="url"` perform intrinsic format validation with `typeMismatch`. The `<input type="tel">` element does NOT perform intrinsic format validation because international phone formats are too varied. Authors MUST pair `type="tel"` with a `pattern` attribute when a specific format is required. The `inputmode` attribute (next section) is the correct lever for mobile keyboard, NOT `type`.

## 2. inputmode + autocomplete

The `inputmode` attribute and the `autocomplete` attribute are orthogonal to `type` and to each other. Per [MDN : `<input>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input) (verified 2026-05-19), `inputmode` accepts eight tokens : `none`, `text`, `decimal`, `numeric`, `tel`, `search`, `email`, `url`. The `inputmode` attribute is a hint to the user-agent only; it tunes the on-screen virtual keyboard on mobile devices and has NO effect on validation, NO effect on desktop, and NO effect on the submitted value. It is the canonical mechanism for the common pattern "I want a numeric keypad but I do not want a spinner and I do not want the field to be a `type="number"` (which strips leading zeros)" : use `type="text" inputmode="numeric" pattern="[0-9]*"`.

Per [MDN : autocomplete attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/autocomplete) (verified 2026-05-19), the `autocomplete` attribute accepts a space-separated token list with the following structure : `[section-* ][shipping|billing ][home|work|mobile|fax|pager ]<detail-token>[ webauthn]`. The `section-*` prefix is OPTIONAL and groups fields that belong to the same logical record (e.g., a form with two addresses). The `shipping` / `billing` prefix is OPTIONAL and indicates address purpose. The detail token is REQUIRED and MUST be one of the WHATWG-defined values. The complete detail-token inventory groups as :

- **Name parts** : `name`, `given-name`, `additional-name`, `family-name`, `honorific-prefix`, `honorific-suffix`, `nickname`
- **Account** : `username`, `current-password`, `new-password`, `one-time-code`
- **Contact** : `email`, `impp`, `tel`, `tel-country-code`, `tel-national`, `tel-area-code`, `tel-local`, `tel-local-prefix`, `tel-local-suffix`, `tel-extension`
- **Personal** : `organization`, `organization-title`, `bday`, `bday-day`, `bday-month`, `bday-year`, `sex`, `language`, `url`, `photo`
- **Address** : `street-address`, `address-line1`, `address-line2`, `address-line3`, `address-level1`, `address-level2`, `address-level3`, `address-level4`, `country`, `country-name`, `postal-code`
- **Credit card** : `cc-name`, `cc-given-name`, `cc-additional-name`, `cc-family-name`, `cc-number`, `cc-exp`, `cc-exp-month`, `cc-exp-year`, `cc-csc`, `cc-type`, `transaction-currency`, `transaction-amount`

The `webauthn` token is OPTIONAL and MUST be LAST; it signals a conditional WebAuthn passkey assertion via `navigator.credentials.get({mediation: "conditional"})`. The literal values `on` and `off` are accepted only when there is no detail-token; they cannot mix with detail tokens. Setting `autocomplete="off"` on password fields is an anti-pattern because it breaks password managers; the correct token is `current-password` (login) or `new-password` (registration / change-password). Per [W3C : WCAG 2.2](https://www.w3.org/TR/WCAG22/) (verified 2026-05-19), Success Criterion 1.3.5 ("Identify Input Purpose") makes correct `autocomplete` tokens an accessibility requirement at level AA when the field collects personal data on the WCAG list.

## 3. Constraint Validation API

The Constraint Validation API is the canonical native validation model. Per [MDN : Constraint validation](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Constraint_validation) (verified 2026-05-19), every form-associated element (`<input>`, `<select>`, `<textarea>`, `<button>`, `<output>`, `<fieldset>`) exposes a `validity` property that returns a `ValidityState` object with eleven boolean flags :

| Flag | Triggered when |
|------|---------------|
| `valueMissing` | `required` attribute set AND value is empty (or, for radios, no member of the group is checked; for checkboxes, the checkbox is unchecked; for `<select>`, no option is selected). |
| `typeMismatch` | `type="email"` AND value is not a valid email; or `type="url"` AND value is not an absolute URL. |
| `patternMismatch` | `pattern` attribute set AND value does not match the regex (anchored implicitly at start and end). |
| `rangeUnderflow` | `min` attribute set AND value is below it. |
| `rangeOverflow` | `max` attribute set AND value is above it. |
| `stepMismatch` | `step` attribute set AND value is not an integer multiple of step away from `min` (or 0). |
| `tooShort` | `minlength` set AND user-supplied value is shorter. NEVER triggered by programmatic `.value` assignment. |
| `tooLong` | `maxlength` set AND user-supplied value is longer. Same caveat. |
| `badInput` | User-entered text the UA cannot parse to the type (e.g., `type="number"` with letters). |
| `customError` | `setCustomValidity(msg)` was called with a non-empty string. |
| `valid` | True if-and-only-if all above flags are false. |

The element-level methods are `checkValidity()` (silent : returns boolean, also fires the `invalid` event if invalid, but does NOT show the browser bubble) and `reportValidity()` (interactive : returns boolean, fires the `invalid` event if invalid, AND surfaces the native browser bubble at the first invalid field). The form-level equivalents `form.checkValidity()` and `form.reportValidity()` walk the form's controls and aggregate. The `setCustomValidity(message: string)` method is the only legitimate way to inject a domain-specific error : passing a non-empty string marks the field invalid and sets `validationMessage` to that string; passing the empty string clears the custom error. Per [MDN : Constraint validation](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Constraint_validation) (verified 2026-05-19), `customError` overrides the default `validationMessage` produced by the other flags.

Per [WHATWG HTML : form submission](https://html.spec.whatwg.org/multipage/forms.html) (verified 2026-05-19), `HTMLFormElement.submit()` BYPASSES interactive constraint validation entirely and does NOT fire the `submit` event. `HTMLFormElement.requestSubmit(submitter?)` performs validation, fires `submit`, and accepts an optional submitter button (whose `formaction` / `formenctype` / `formmethod` / `formnovalidate` / `formtarget` attributes can override the form's). Clicking an actual `<button type="submit">` is equivalent to `requestSubmit(button)`. The `formnovalidate` attribute on a submit button (and the `novalidate` attribute on the form) skip validation while still firing `submit`. The pseudo-classes `:valid`, `:invalid`, `:required`, `:optional`, `:in-range`, `:out-of-range`, `:user-valid`, `:user-invalid` reflect the current validity state in CSS. Per [MDN : :user-invalid](https://developer.mozilla.org/en-US/docs/Web/CSS/:user-invalid) (verified 2026-05-19), `:user-invalid` is Baseline Widely Available since November 2023; it matches only AFTER the user has interacted with the field (typed, blurred, or attempted submit). `:invalid` matches immediately on page load and is the wrong default for visible error styling.

## 4. Form events + FormData

Three form-related events drive the submission lifecycle. The `submit` event fires on the `<form>` when submission is requested via button click, Enter key in a single-line input, or `form.requestSubmit()`. It is cancelable; `event.preventDefault()` halts submission. The `SubmitEvent.submitter` property identifies the button that initiated the submit (per [WHATWG HTML](https://html.spec.whatwg.org/multipage/forms.html) verified 2026-05-19), which lets authors read which submit button was used and read its `formaction` / `formenctype` overrides. The `reset` event fires when the form is reset via `<input type="reset">` or `form.reset()`. The `invalid` event fires on each invalid element during `checkValidity()` / `reportValidity()`; it is cancelable to suppress the default browser bubble while still keeping the validity state.

The `formdata` event is the modern hook for serializing form data. Per [MDN : FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) (verified 2026-05-19), `formdata` fires after `submit` (and only when the form is actually submitted, not just validated). The event's `formData` property holds a mutable `FormData` instance; authors can `append()`, `set()`, or `delete()` entries before the network request is built. This is the canonical place to add CSRF tokens, computed fields, or to drop sensitive entries from the wire.

The `FormData` constructor accepts a form element and an optional submitter button : `new FormData(form, submitter?)`. The submitter argument ensures the clicked submit button's `name=value` is included exactly as the browser would have included it. The instance methods are : `append(name, value, filename?)`, `set(name, value, filename?)`, `get(name)` (first match), `getAll(name)` (all matches), `has(name)`, `delete(name)`, `keys()`, `values()`, `entries()`, `forEach((value, key) => ...)`. The encoding when sent via `fetch(url, {method: "POST", body: formData})` is `multipart/form-data`, which is the only choice when the form includes a `<input type="file">`. For URL-encoded encoding, construct `new URLSearchParams(formData)` (this only works when no `File` entries are present). To serialize to JSON, use `Object.fromEntries(formData)` for the flat case, or a manual reduction that groups duplicate keys into arrays when the form has multi-value fields (radio groups, checkbox sets, `multiple` selects).

## 5. Open UI customizable controls

This section closes the §14 gap. The Open UI Community Group has been working on customizable form controls for several years. The state as of 2026-05-19 follows.

**Customizable `<select>` element (`appearance: base-select`)** is the headline shipment. Per [Open UI : Customizable Select](https://open-ui.org/components/customizableselect/) (verified 2026-05-19), the proposal has reached "Graduated Proposal" status and shipped in Chromium-based browsers. The opt-in is a CSS one-liner :

```css
select, ::picker(select) {
  appearance: base-select;
}
```

Once opted-in, the `<select>` accepts a `<button>` child (the trigger), a `<selectedcontent>` child (the live-cloned preview of the selected option's content), and an arbitrary mix of `<option>`, `<optgroup>`, `<hr>`, and other phrasing content inside the picker. The picker is implemented as a popover (using the same top-layer plumbing as the Popover API), positioned with CSS anchor positioning, and styleable via the `::picker(select)` pseudo-element. Per [MDN : appearance](https://developer.mozilla.org/en-US/docs/Web/CSS/appearance) (verified 2026-05-19), `appearance: base-select` is recognized in evergreen-2026 Chromium-family browsers; the broader Baseline status is "Limited Availability" (Chromium-only) and authors MUST treat the upgrade as progressive enhancement. Firefox and WebKit implementation tracking shows positive signals but no shipped builds at 2026-05-19. The `appearance: base` keyword (the generalized form that would apply to ALL controls) is specified but NOT YET implemented in any shipped browser.

The `<selectedcontent>` element (formerly called `<selectedoption>` in earlier drafts) is part of the customizable-select package. It is a passive slot : the browser automatically clones the DOM of the currently selected `<option>` into the `<selectedcontent>`, including images and rich markup. The element has no scripting API of its own and lives or dies with `appearance: base-select`. The earlier `<selectedoption>` name has been deprecated in favor of `<selectedcontent>` to reflect "what is rendered" rather than "which option object". When referring to the element in skill docs, ALWAYS write `<selectedcontent>` and treat `<selectedoption>` as historical.

**Customizable `<button>`** has been delivered through complementary specifications. The `popovertarget` and `popovertargetaction` attributes on `<button>` ship in Baseline 2025 per the Popover API and let a button declaratively open / close / toggle a popover without JavaScript. The `commandfor` and `command` attributes (the generalization) are still draft as of 2026-05-19; they would let buttons declaratively trigger any registered command on a target element. Authors MUST treat `commandfor`/`command` as not-yet-Baseline and write JS fallbacks; `popovertarget` is safe to use without fallback.

**Form-associated custom elements via `ElementInternals`** are Baseline Widely Available since March 2023, per [MDN : ElementInternals](https://developer.mozilla.org/en-US/docs/Web/API/ElementInternals) (verified 2026-05-19), and are documented in detail in section 6. This is the production-ready path for "I need a fully custom form widget that participates in the form lifecycle and constraint validation".

Practical guidance for the form skill : recommend `appearance: base-select` only with the disclaimer "Chromium-shipped; treat as progressive enhancement; provide standard `<select>` fallback by writing the markup so it works without the opt-in". Treat `<selectedcontent>` as the official name. Avoid mentioning `commandfor` outside of "watch this space" notes. For framework-grade custom controls, send authors to `frontend-impl-web-components` and ElementInternals.

## 6. Form-Associated Custom Elements

A form-associated custom element is a custom element that declares the static field `static formAssociated = true` and calls `this.attachInternals()` in its constructor. Per [MDN : ElementInternals](https://developer.mozilla.org/en-US/docs/Web/API/ElementInternals) (verified 2026-05-19), this gives the element full participation in the form lifecycle and constraint validation, identical to a native `<input>`.

The `ElementInternals` interface exposes :

- **`form`** : the owning `<form>` element (or null).
- **`labels`** : a `NodeList` of `<label>` elements that reference the host via `for=`.
- **`willValidate`** : true if the element is subject to constraint validation.
- **`validity`** : a `ValidityState` mirror.
- **`validationMessage`** : the current invalid message string.
- **`checkValidity()` / `reportValidity()`** : same semantics as native.
- **`setFormValue(value, state?)`** : sets the value submitted with the form (and optionally a state for restoration). `value` can be a string, `File`, or `FormData` (the multi-entry case).
- **`setValidity(flags, message?, anchor?)`** : the moral equivalent of `setCustomValidity` with full ValidityState control. Pass `{}` and no message to clear. `flags` is an object with the same keys as `ValidityState`; `anchor` is the focusable element to scroll/focus when `reportValidity` runs.

The lifecycle callbacks on the custom-element class fire when the form interacts with the element :

- **`formAssociatedCallback(form)`** : fired when the element is associated with (or disassociated from) a form. Argument is the form or null.
- **`formDisabledCallback(disabled)`** : fired when the form (or a containing `<fieldset>`) is disabled or enabled.
- **`formResetCallback()`** : fired on `form.reset()`. The element MUST restore its initial state here.
- **`formStateRestoreCallback(state, reason)`** : fired when the browser restores state after navigation (`reason === "restore"`) or autofill (`reason === "autocomplete"`).

A minimal sketch :

```javascript
class StarRating extends HTMLElement {
  static formAssociated = true;
  static observedAttributes = ["name", "value", "required"];

  #internals = this.attachInternals();
  #value = "";

  get value() { return this.#value; }
  set value(v) {
    this.#value = String(v ?? "");
    this.#internals.setFormValue(this.#value);
    this.#syncValidity();
  }

  get form()             { return this.#internals.form; }
  get labels()           { return this.#internals.labels; }
  get validity()         { return this.#internals.validity; }
  get validationMessage(){ return this.#internals.validationMessage; }
  get willValidate()     { return this.#internals.willValidate; }
  checkValidity()        { return this.#internals.checkValidity(); }
  reportValidity()       { return this.#internals.reportValidity(); }

  formResetCallback()    { this.value = this.getAttribute("value") ?? ""; }
  formStateRestoreCallback(state) { this.value = state; }

  #syncValidity() {
    if (this.hasAttribute("required") && !this.#value) {
      this.#internals.setValidity({ valueMissing: true }, "Pick a rating");
    } else {
      this.#internals.setValidity({});
    }
  }
}
customElements.define("star-rating", StarRating);
```

The form skill cross-references this section but the full pattern lives in `frontend-impl-web-components`. The reason this section sits in the form-research file is that ANY accurate guidance on "how to make a custom widget participate in a form" must cite `setFormValue` + `setValidity` from `ElementInternals` and cannot be deferred without leaving a hole.

## 7. Accessibility for forms

Form accessibility is a non-negotiable layer; failing it makes the form unusable for assistive-tech users and fails WCAG 2.2 success criteria 1.3.1, 1.3.5, 3.3.1, 3.3.2, 3.3.3, 3.3.4, 3.3.7, 3.3.8. Per [MDN : `<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/label) (verified 2026-05-19), every interactive form control MUST have an accessible name and the canonical mechanism is the `<label>` element. Two association modes :

- **Explicit** : `<label for="email">Email</label><input id="email">`. Maximum compatibility with screen readers; allows layout where the label and the input are siblings or distant cousins.
- **Implicit** : `<label>Email <input></label>`. Wrap the input. Compatibility is good but not universal; the explicit form is preferred when both are equally easy.

Labelable elements are : `<button>`, `<input>` (all types except `hidden`), `<meter>`, `<output>`, `<progress>`, `<select>`, `<textarea>`. A control MAY have multiple labels (e.g., a "Forgot password?" link rendered as a second `<label for="password">`). A control MUST NOT have nested interactive content in its label (no link, button, heading inside `<label>`); per MDN, this conflicts with the implicit click-forwarding semantics and confuses screen readers.

Per [MDN : `<fieldset>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/fieldset) (verified 2026-05-19), groups of related controls (radio groups, checkbox sets, address blocks) MUST be wrapped in `<fieldset>` with a `<legend>` as the FIRST child. The `<legend>` is the accessible name for the group; screen readers announce it on focus into the first control. The `<fieldset disabled>` attribute disables all descendants EXCEPT controls inside the `<legend>`, which remain enabled. The `<fieldset>` is form-associated via implicit owner-form or via the `form` attribute (referencing an outside form's id). Per MDN, modern Chromium and Firefox correctly render `<fieldset>` as a flex or grid container when `display: flex`/`grid` is set on it, removing a long-standing CSS limitation.

Error messaging requires three coordinated mechanisms. Per [MDN : aria-errormessage](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Reference/Attributes/aria-errormessage) (verified 2026-05-19), the field MUST set `aria-invalid="true"` while invalid and MUST reference the error message via `aria-errormessage="<id>"`. The error message element MUST be present in the DOM and visible (CSS `visibility: hidden` while clean, `visibility: visible` when `aria-invalid="true"`). `aria-invalid` accepts `true`, `false`, `grammar`, `spelling`; the latter two are dictation hints. The relationship to `aria-describedby` is that `aria-describedby` is a generic description channel (hints, format help) while `aria-errormessage` is specifically for the error state and only active when `aria-invalid="true"`. Authors SHOULD also fire a polite `aria-live` region (or use `role="alert"` for assertive) so screen readers announce the error when it first appears.

Focus styling for form controls MUST use `:focus-visible` (not `:focus`) to avoid showing a focus ring on mouse-click while preserving it on keyboard navigation. The focus indicator MUST meet WCAG 2.2 SC 2.4.11 ("Focus Not Obscured") and SC 2.4.13 ("Focus Appearance") with at least 3:1 contrast against the background and a 2-pixel-or-thicker outline. NEVER remove the focus outline without replacing it with a visible alternative.

## 8. Decision matrix : which control for which need

| Need | Type | inputmode | autocomplete | Notes |
|------|------|-----------|--------------|-------|
| Email address | `email` | `email` (default) | `email` | Allows `multiple` for csv list |
| US phone (free format) | `tel` | `tel` | `tel` | Add `pattern` for strict format |
| Numeric ID (preserve leading zeros) | `text` | `numeric` | (custom or off) | `pattern="[0-9]*"` |
| Currency amount | `text` | `decimal` | (none) | Manual parse; `type="number"` mangles `1,234.56` |
| Login password | `password` | (auto) | `current-password` | NEVER `autocomplete="off"` |
| New password | `password` | (auto) | `new-password` | Triggers strong-password generator |
| One-time code | `text` or `number` | `numeric` | `one-time-code` | Triggers SMS-autofill on iOS / Android |
| Calendar date | `date` | (n/a) | `bday` for birth | ISO 8601 wire format |
| Color swatch | `color` | (n/a) | (none) | Returns `#rrggbb` only |
| File upload (image) | `file` | (n/a) | (none) | `accept="image/*"` and `capture="environment"` |
| Boolean toggle | `checkbox` | (n/a) | (none) | `required` for "must accept" |
| Single-of-N | `radio` | (n/a) | (none) | Wrap in `<fieldset>` + `<legend>` |
| Dropdown (native) | `<select>` | (n/a) | per-purpose | Use this until customizable-select is Baseline |
| Dropdown (styled, Chromium-only) | `<select>` + `appearance: base-select` | (n/a) | per-purpose | Progressive enhancement |
| Multi-line text | `<textarea>` | (auto) | (per purpose) | `maxlength` for character cap |
| Custom widget (star-rating, etc.) | custom element + `ElementInternals` | (host-defined) | (host-defined) | Use `setFormValue` + `setValidity` |

## 9. Anti-patterns

1. **`:invalid` for visible error styling** : red border appears on first paint of an empty `required` field. The user has not yet had a chance to fill it. Use `:user-invalid` instead, which only matches after blur, after typing invalid input, or after a submit attempt.
2. **`autocomplete="off"` on password fields** : breaks password managers, encourages weak passwords, fails WCAG 2.2 SC 1.3.5. Use `autocomplete="current-password"` (login) or `"new-password"` (registration / change).
3. **No `<label>`, relying on `placeholder` as label** : placeholder disappears on focus, is not announced by all screen readers, fails WCAG 2.2 SC 3.3.2 ("Labels or Instructions"). ALWAYS pair every input with a `<label for>`.
4. **`HTMLFormElement.submit()` to programmatically submit** : silently bypasses constraint validation AND does not fire the `submit` event, so any `formdata` listener (CSRF token injection, computed fields) is skipped. Use `form.requestSubmit()` or `submitButton.click()`.
5. **Building a custom dropdown with `<div>` + `<ul>` + JavaScript** : no constraint validation, no autofill, no implicit form participation, no built-in accessibility tree. Use `<select>` (native), `<select>` + `appearance: base-select` (Chromium-styled), or a form-associated custom element via `ElementInternals` (production-grade).
6. **Custom error message rendered via JS overlay WITHOUT `setCustomValidity()`** : the field's `validity.valid` remains `true`, the form will submit, and the screen reader will not announce the error. Always reflect the error into Constraint Validation via `setCustomValidity()` (or `ElementInternals.setValidity()`).
7. **`type="number"` for credit-card / ZIP / IDs** : strips leading zeros, allows `e` (exponent notation), permits `+`/`-`, the spinner is bad on mobile. Use `type="text" inputmode="numeric" pattern="[0-9]*"`.
8. **`autofocus` on the primary input** : per MDN, this disorients screen-reader users and scroll-jacks on mobile keyboards. AVOID `autofocus`; if focus must be moved on load, move it after the user's first interaction or on route enter with explicit `focus()`.

## 10. Sources Used

| # | URL | Verified | Extracted |
|---|-----|----------|-----------|
| 1 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/input | 2026-05-19 | 22 input types, validation flags, attribute matrix |
| 2 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/autocomplete | 2026-05-19 | autocomplete token grammar, full detail-token inventory, off vs unset, webauthn modifier |
| 3 | https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Constraint_validation | 2026-05-19 | ValidityState flags, setCustomValidity, checkValidity vs reportValidity, submit() bypass |
| 4 | https://developer.mozilla.org/en-US/docs/Web/CSS/:user-invalid | 2026-05-19 | Baseline since Nov 2023, when it matches, difference from :invalid, :user-valid |
| 5 | https://developer.mozilla.org/en-US/docs/Web/API/FormData | 2026-05-19 | constructor with submitter, append/set/get/getAll/has/delete/keys/values/entries, formdata event |
| 6 | https://open-ui.org/components/customizableselect/ | 2026-05-19 | appearance: base-select opt-in, `<selectedcontent>`, ::picker(select), shipped in Chromium |
| 7 | https://developer.mozilla.org/en-US/docs/Web/CSS/appearance | 2026-05-19 | appearance keyword values, base vs base-select, Baseline status nuance |
| 8 | https://developer.mozilla.org/en-US/docs/Web/API/ElementInternals | 2026-05-19 | formAssociated, attachInternals, setFormValue, setValidity, lifecycle callbacks, Baseline since March 2023 |
| 9 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/label | 2026-05-19 | explicit vs implicit association, labelable elements, multiple labels, anti-patterns |
| 10 | https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/fieldset | 2026-05-19 | disabled-cascade, legend semantics, flex/grid container, ARIA group role |
| 11 | https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Reference/Attributes/aria-errormessage | 2026-05-19 | aria-invalid + aria-errormessage pairing, relationship to aria-describedby, valid values |
| 12 | https://html.spec.whatwg.org/multipage/forms.html | 2026-05-19 | requestSubmit(submitter), formaction/formmethod/formenctype/formtarget/formnovalidate overrides, novalidate |
| 13 | https://www.w3.org/TR/WCAG22/ | 2026-05-19 | SC 1.3.5 Identify Input Purpose, SC 3.3.x error identification and prevention |
