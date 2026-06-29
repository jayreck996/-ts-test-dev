ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-06-25 12:03 → beforeEach truncation in db.ts omits RecipeReview and Flow models
## ISSUE:TEST 2026-06-29 12:24 → processOrder(null) throws TypeError instead of the named error — error contract is untested

`src/index.js` `processOrder` has no null-guard on the `order` argument itself. Calling `processOrder(null)` raises `TypeError: Cannot read properties of null (reading 'id')` before the `throw new Error('Order missing id')` line is reached. No test exists to document or pin which error callers receive in this path. Code that catches `Error` and matches on `'Order missing id'` will silently fail to catch the `TypeError`. A test asserting `expect(() => processOrder(null)).toThrow()` would immediately surface the mismatch between the intended and actual error type.
## ISSUE:TEST 2026-06-29 08:57 → validateItem price boundary at 0 is untested — falsy guard silently rejects free items

`src/index.js` `validateItem` checks `item.price > 0`, which evaluates to `false` for `price === 0`. Free or promotional items with a zero price are therefore treated as invalid without any test exercising this boundary. The absence of a test suite (flagged separately) means this semantic decision — whether zero is a valid price — has never been explicitly verified. A test case of `validateItem({ name: 'promo', price: 0 })` would immediately surface whether the current `> 0` guard is intentional or an off-by-one in the comparison operator.
## ISSUE:TEST 2026-06-26 13:25 → slugify has no guard against non-string input — null or number argument throws TypeError at runtime

`src/utils.js` `slugify(str)` calls `str.toLowerCase()` with no type check or null guard. Passing `null`, `undefined`, a number, or an array throws `TypeError: str.toLowerCase is not a function` (or `Cannot read properties of null`). The function is exported and used at any URL or key-generation callsite, so a malformed caller input produces an unhandled runtime error rather than a safe empty string. A guard of `if (typeof str !== 'string') return ''` at the function entry is sufficient. Without a test suite this class of input is never exercised before deploy.
## ISSUE:TEST 2026-06-25 14:39 → zero test files in repo; broken formatPrice and edge cases in validateItem are entirely uncovered

The file tree contains only `src/index.js` and `src/utils.js` — no test or spec files exist. As a result:
- `formatPrice` runtime crash (bare `$`) would never be caught by a test suite before deploy.
- `validateItem` edge cases (null item, zero price, missing name) are unverified.
- `processOrder` spread behaviour when `order` has extra fields is untested.
- `slugify` is untested for unicode, consecutive spaces, or leading/trailing whitespace.

No test runner config (`jest`, `mocha`, `vitest`) is present in the tree, so the test infrastructure itself is missing.

The `beforeEach` block in `src/__tests__/helpers/db.ts` manually deletes rows from 11 tables in FK-safe order, but `RecipeReview` and `Flow` are absent from the list. Both models exist in `prisma/schema.prisma`: `RecipeReview` has a unique constraint on `[userId, recipeId]` and `Flow` drives the `UserFlowView` rows that ARE deleted. Any test that creates a `RecipeReview` or `Flow` record will leave that data in the test database, potentially causing unique-constraint collisions or phantom rows in subsequent tests. As new models are added to the schema the omission pattern is likely to repeat.
