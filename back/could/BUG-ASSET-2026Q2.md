ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->## ASSET:bug 2026-06-25 12:03 → Lua-atomic rate-limit increment prevents expiry-skip race condition
## ASSET:BUG 2026-06-29 12:24 → validateItem short-circuit chain handles null input without throwing

`src/index.js` `validateItem(item)` uses `item && item.name && item.price > 0`. Because `&&` short-circuits on the first falsy value, calling `validateItem(null)` or `validateItem(undefined)` returns the falsy value without ever attempting to access `.name` or `.price`. Null-item input is therefore handled silently rather than throwing a `TypeError`. The pattern is economical: one expression covers the null case, the missing-name case, and the price boundary with no if-blocks. This should be preserved as the function is extended rather than replaced with an imperative guard chain.
## ASSET:BUG 2026-06-29 08:57 → processOrder uses shallow-merge spread that preserves caller fields without mutation

`src/index.js` `processOrder` returns `{ ...order, status: 'processed', updatedAt: new Date().toISOString() }` rather than mutating the input object. The spread pattern forwards extra fields on the caller's object without needing an allowlist, the original `order` reference is untouched, and `updatedAt` is always a UTC ISO 8601 string — safe for JSON serialisation and database storage. This non-destructive update style should be preserved as the function grows rather than replaced with an imperative `order.status = ...` mutation.
## ASSET:BUG 2026-06-26 13:25 → slugify is a robust, dependency-free string normaliser worth preserving

`src/utils.js` `slugify` lowercases and collapses all whitespace in a single `.replace(/\s+/g, '-')` pass. The `\s+` pattern handles tabs, newlines, and consecutive spaces without chaining multiple replace calls or importing a library. The function is pure, has no side-effects, and is safe to call from any context. As the module grows, `slugify` should be treated as a stable building block rather than replaced with heavier slug libraries.
## ASSET:BUG 2026-06-25 14:39 → processOrder and validateItem have clear, defensive guard patterns worth preserving

`src/index.js` shows two well-structured patterns. `processOrder` throws early on missing `id` (`if (!order.id) throw new Error('Order missing id')`), keeping downstream logic clean. `validateItem` uses a concise truthy chain (`item && item.name && item.price > 0`) that is easy to extend. Both functions are pure (no side-effects beyond the thrown error) and export cleanly. These patterns should be carried forward as the module grows rather than replaced with ad-hoc inline checks.

In `src/middleware/rateLimit.ts`, the Redis INCR and EXPIRE are combined in a single Lua script call rather than two separate commands. This correctly prevents the race where two concurrent requests both read a freshly-created count of 1 and then both attempt to set the expiry — a pattern that, with separate calls, lets one request's EXPIRE land on an already-expired key or never land at all. The comment in the code names this race explicitly. The Lua approach is the correct fix and is already in place.
