ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-06-25 13:25 → zero test files and no test runner configured — all exports are untested
## ISSUE:test 2026-06-29 12:21 → processOrder's inline new Date() call is non-deterministic — full output shape is untestable without a date stub

**Finding — `src/index.js`**
`processOrder` returns `{ ...order, status: 'processed', updatedAt: new Date().toISOString() }`. The `updatedAt` field changes on every call, so a `deepEqual` assertion against an expected object will always fail unless the test stubs `Date` or relaxes the assertion to a regex match on `updatedAt`. Neither approach is documented or configured. Without injectable or mockable time, full-output testing of `processOrder` requires tooling (e.g. `jest.useFakeTimers()`) that is not available — no `package.json` or runner is set up.
## ISSUE:test 2026-06-29 09:00 → zero test coverage — no test files or test runner configured across entire src/ surface

**Finding — `src/index.js`, `src/utils.js`**
Neither a `package.json` with a `test` script nor any `*.test.js` / `*.spec.js` files exist in the repository. All four exported functions (`processOrder`, `validateItem`, `formatPrice`, `slugify`) have no automated tests. The broken `formatPrice` stub (ReferenceError on every call) and the null-input crash in `processOrder(null)` could both be caught by a single basic unit test run.
## ISSUE:test 2026-06-26 13:23 → no test files, no package.json, and no runner — three distinct crash-path bugs are undetected without coverage

The repo contains no spec or test files and no `package.json` to register a test runner. Three runtime crashes go undetected: (1) `formatPrice()` throws `ReferenceError: $ is not defined`; (2) `slugify(null)` throws `TypeError: str.toLowerCase is not a function`; (3) `processOrder(null)` throws `TypeError: Cannot read properties of null` before the explicit guard fires. Each would be caught by a single unit test but is currently invisible without a test suite.
## ISSUE:test 2026-06-25 14:41 → no test files and no test runner — all four exported functions are untested

The repo contains no spec or test files and no `package.json` to configure a test runner. All four exported functions (`processOrder`, `validateItem`, `formatPrice`, `slugify`) have zero test coverage. The `formatPrice` ReferenceError in `src/utils.js` is a straightforward crash-on-call bug that a single unit test would have caught immediately.

No spec or test files exist anywhere in the repo. There is no `package.json`, so no test runner (Jest, Mocha, etc.) is configured. All four exported functions — `processOrder`, `validateItem`, `formatPrice`, `slugify` — are completely untested. The `formatPrice` ReferenceError bug (see BUG-ISSUE) would be caught by a single test case but has gone undetected without coverage.
