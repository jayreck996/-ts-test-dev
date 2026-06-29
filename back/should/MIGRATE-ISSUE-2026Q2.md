SHOULD ISSUE LOG
prompt: review and update migration plans, version upgrades, breaking changes, deprecation paths, schema changes
path: should/MIGRATE-ISSUE-2026Q2.md
target: ts-test-back

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:MIGRATE {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ISSUE:MIGRATE 2026-06-28 18:33 ▸ formatPrice in src/utils.js returns bare '$' — functional regression with no migration or fix path defined

`src/utils.js` line 3: `formatPrice` returns only the string `$` instead of a formatted currency amount. This is a broken implementation. There is no `package.json` so no dependency versions are pinned and no upgrade path is tracked. No ORM, no schema migration tooling (no Prisma, no Knex), and no ESM migration underway — the codebase is entirely CommonJS. If a JS→TS migration is planned, the broken `formatPrice` function would surface as a type error immediately.
