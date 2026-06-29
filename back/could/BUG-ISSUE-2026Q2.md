ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:bug 2026-06-25 12:03 → register endpoint imports sendVerificationEmail but never calls it
## ISSUE:BUG 2026-06-29 12:24 → processOrder throws TypeError when order is null — null guard missing before id check

`src/index.js` `processOrder(order)` begins with `if (!order.id) throw new Error('Order missing id')`. If `order` itself is `null` or `undefined`, evaluating `order.id` throws `TypeError: Cannot read properties of null (reading 'id')` before the guard can run. Callers receive a `TypeError` rather than the intended named error, making it impossible to write a reliable `catch` block that distinguishes a missing id from an unhandled null argument. The fix is a leading null check — `if (order == null) throw new Error('Order missing id')` — before any property access.
## ISSUE:BUG 2026-06-29 08:57 → slugify produces leading/trailing dashes when input has surrounding whitespace

`src/utils.js` `slugify(str)` calls `str.toLowerCase().replace(/\s+/g, '-')` without first trimming the string. Any input with leading or trailing whitespace — e.g. `" hello world "` — produces a slug with surrounding dashes: `-hello-world-`. URLs and keys containing such slugs are malformed and may cause 404s or failed lookups depending on how the slug is consumed downstream. The fix is a single `.trim()` before the `.replace()` call: `return str.toLowerCase().trim().replace(/\s+/g, '-')`.
## ISSUE:BUG 2026-06-26 13:25 → processOrder throws on id=0 — falsy guard rejects valid zero-value ids

`src/index.js` `processOrder` uses `if (!order.id)` to detect a missing id. This check treats `0`, `false`, and `""` as absent ids and throws. In systems where IDs are zero-indexed or sourced from an external API that uses `0` as a valid sentinel, legitimate orders are silently rejected. The fix is a null-coalescing guard (`if (order.id == null)`) or explicit `if (order.id === undefined || order.id === null)`, which rejects only truly absent ids without discarding valid falsy values.
## ISSUE:BUG 2026-06-25 14:39 → formatPrice in src/utils.js returns bare `$` — invalid JS, throws at runtime

`src/utils.js` `formatPrice(amount)` has a broken body: `return $`. The bare `$` is not a valid identifier in this context (no jQuery or similar in scope), so any call to `formatPrice` will throw a `ReferenceError` at runtime. The function is exported and presumably called anywhere prices need display formatting, making this a silent production-breaking bug. `slugify` in the same file is unaffected. `processOrder` and `validateItem` in `src/index.js` are structurally sound.

**Fix:** replace the body with a real template-literal or `toFixed` call, e.g. `` return `$${Number(amount).toFixed(2)}` ``.

In `src/routes/auth.ts`, `sendVerificationEmail` is imported at the top of the file alongside `sendPasswordResetEmail`, but the `POST /register` handler creates the user and immediately returns a signed JWT without calling it. Users are created with `emailVerified: false` (the Prisma default) and that field never flips to `true` unless they independently discover and call `POST /auth/resend-verification`. The verification email send path only exists in that resend endpoint. This means the email-verification flow is completely bypassed at the point of registration.
