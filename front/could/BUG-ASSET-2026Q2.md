ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-06-25 13:25 → processOrder guards order.id before mutation; slugify is a pure safe transform
## ASSET:bug 2026-06-29 12:21 → validateItem is side-effect-free and stateless — safely replaceable without risk to surrounding module state

**Finding — `src/index.js`**
Despite its inconsistent return type (see BUG-ISSUE), `validateItem` has no side effects, no external calls, and no shared mutable state. It can be replaced or wrapped in isolation — changing the body to `return !!(item && item.name && item.price > 0)` fixes the type inconsistency without touching any other function in the module. The narrow surface area and zero dependencies make this the lowest-risk fix in the codebase.
## ASSET:bug 2026-06-29 09:00 → processOrder uses explicit throw rather than silent failure — fail-fast guard surfaces caller bugs early

**Finding — `src/index.js`**
`processOrder` throws `new Error('Order missing id')` when `order.id` is falsy, rather than returning a default or silently continuing. This fail-fast pattern means missing-id bugs surface immediately at the call site with a readable message, rather than propagating as undefined behaviour downstream. It is the correct approach for a mandatory field guard.
## ASSET:bug 2026-06-26 13:23 → processOrder's fail-fast id guard and slugify's pure regex transform are the safest exports in the codebase

`processOrder` in `src/index.js` throws a descriptive `Error('Order missing id')` before spreading the order object, preventing partial mutation from propagating on missing-id input. `slugify` in `src/utils.js` is a single-expression pure function with no side effects, no external dependencies, and deterministic output for valid string inputs — it is the lowest-risk export in the codebase for refactoring or reuse.
## ASSET:bug 2026-06-25 14:41 → processOrder uses fail-fast id guard; slugify is a pure deterministic transform

`processOrder` in `src/index.js` validates `order.id` before any mutation and throws a descriptive error immediately, preventing partial state from propagating downstream. `slugify` in `src/utils.js` is a pure function: no side effects, no external dependencies, fully deterministic — it is low risk under refactor and easy to reason about in isolation.

`processOrder` in `src/index.js` throws early with a descriptive error (`'Order missing id'`) before touching the order object, preventing partial mutations from propagating. `slugify` in `src/utils.js` is a pure function with no side effects, no external dependencies, and deterministic output — low risk of introducing bugs under change.
