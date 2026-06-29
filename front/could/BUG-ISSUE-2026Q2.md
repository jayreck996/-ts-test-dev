ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:bug 2026-06-25 13:25 → formatPrice in src/utils.js returns bare `$` — ReferenceError at runtime
## ISSUE:bug 2026-06-29 12:21 → validateItem returns mixed falsy types — callers cannot distinguish null input, empty name, or invalid price

**Finding — `src/index.js`**
`validateItem(item)` uses a short-circuit `&&` chain: `item && item.name && item.price > 0`. This returns three distinct falsy values depending on which guard fails: `null` when `item` is null, `''` when `item.name` is falsy, and `false` when `item.price <= 0`. Callers using strict `=== false` checks will miss the first two cases entirely. The function should coerce its return with `!!` or an explicit conditional to guarantee a consistent boolean — currently the return type is `null | string | number | boolean`, not `boolean`.
## ISSUE:bug 2026-06-29 09:00 → formatPrice ignores its `amount` parameter — return value is entirely input-independent

**Finding — `src/utils.js`**
`formatPrice(amount)` accepts an `amount` argument but the body is `return $` — the parameter is never referenced. Even if `$` were defined, every call would return the same constant regardless of input. The function is both broken (ReferenceError) and incorrectly scoped: callers have no way to produce a formatted price string for any given amount.
## ISSUE:bug 2026-06-26 13:23 → slugify and processOrder lack null/type guards — both throw TypeError on non-string or null input

`src/utils.js` `slugify(str)` calls `str.toLowerCase()` without confirming `str` is a string — passing `null`, `undefined`, or a number throws `TypeError: str.toLowerCase is not a function`. Similarly, `processOrder` in `src/index.js` accesses `order.id` before confirming `order` is not `null` — `processOrder(null)` throws `TypeError: Cannot read properties of null (reading 'id')` before the explicit id guard is reached. Both failure modes are silent in callers that do not inspect return values.
## ISSUE:bug 2026-06-25 14:41 → formatPrice in src/utils.js returns bare `$` — ReferenceError at runtime

`src/utils.js` line 3: `return $` is an unresolved stub. `$` is not declared anywhere in the module, so every call to `formatPrice()` throws `ReferenceError: $ is not defined`. The function is completely non-functional. Additionally, `validateItem` in `src/index.js` does not guard against `NaN` prices — `NaN > 0` evaluates to `false`, silently rejecting items with corrupt price data rather than surfacing an error.

`src/utils.js` line 3: `return $` is a broken stub. `$` is not defined in this module, so any call to `formatPrice()` throws `ReferenceError: $ is not defined` at runtime. The function never returns a formatted price string.

Secondary: `validateItem` in `src/index.js` checks `item.price > 0` but does not guard against `NaN` — `NaN > 0` is `false` so `NaN`-priced items are silently rejected without error, masking bad data.
