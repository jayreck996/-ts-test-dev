ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:test 2026-06-25 12:03 → integration tests run against a real PostgreSQL database, not mocks
## ASSET:TEST 2026-06-29 12:24 → processOrder's explicit throw message gives tests a stable string assertion target

`src/index.js` `processOrder` throws `new Error('Order missing id')` when `!order.id`. The explicit string message provides a concrete, matchable assertion: `expect(() => processOrder({})).toThrow('Order missing id')`. Jest, Mocha, and Vitest all support substring matching on thrown error messages, so the assertion targets semantics rather than the error class. As the function grows with additional validation branches, each path can have its own discriminating message and tests need not couple to error types or instance checks. This throw-first pattern should be replicated for any new validation added to `processOrder`.
## ASSET:TEST 2026-06-29 08:57 → slugify is referentially transparent — same input always yields same output, ideal for property-based testing

`src/utils.js` `slugify` is a pure string transform with no I/O, no state, and no side-effects. This referential transparency makes it a strong candidate for property-based tests (e.g. fast-check or jest-quick-check): generate random strings and assert that the output never contains uppercase characters, never contains spaces, and is always a valid URL path segment. Property-based tests catch edge cases — unicode codepoints, zero-width spaces, emoji — that hand-written examples miss. Because `slugify` has no dependencies and runs in microseconds, the property suite adds negligible CI overhead.
## ASSET:TEST 2026-06-26 13:25 → CommonJS module.exports lets tests require files directly with no ESM config

`src/index.js` and `src/utils.js` both use `module.exports` (CommonJS). Any Jest, Mocha, or Vitest setup can `require()` them synchronously without ESM transforms, `--experimental-vm-modules` flags, or a build step. This removes the most common friction point when bootstrapping a test suite — no `"type": "module"` conflict and no dynamic `import()` needed. A minimal `jest.config.js` and a single `__tests__/` directory are all that stand between the current state and a running test suite.
## ASSET:TEST 2026-06-25 14:39 → all four exported functions have simple, pure interfaces that are straightforward to unit-test

All exported functions (`processOrder`, `validateItem`, `formatPrice`, `slugify`) are pure or near-pure with no I/O dependencies, making them ideal candidates for fast unit tests with no mocking required. `processOrder` throws predictably on bad input, giving a clear assertion target. `validateItem` returns a boolean from a deterministic chain. `slugify` is a string-in/string-out transform. Once a test runner is added, achieving full branch coverage across all four functions should require fewer than 20 small test cases.

The `vitest.config.ts` points `DATABASE_URL` at a local `toifood_test` PostgreSQL instance and the `db.ts` setup file runs real Prisma queries. This means FK constraints, cascade-delete behaviour, unique-index violations, and Prisma migration compatibility are all exercised on every test run rather than being hidden behind an in-memory mock. The `createTestUser` helper in `src/__tests__/helpers/auth.ts` also generates a unique email per call (via `randomUUID` slice), so parallel test registration scenarios do not collide even within a single suite run.
