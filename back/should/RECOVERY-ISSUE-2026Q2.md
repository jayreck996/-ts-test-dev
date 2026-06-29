SHOULD ISSUE LOG
prompt: review and update disaster recovery procedures, rollback plans, backup strategies, incident response runbooks
path: should/RECOVERY-ISSUE-2026Q2.md
target: ts-test-back

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:RECOVERY {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ISSUE:RECOVERY 2026-06-28 18:33 ▸ No rollback plan, CI/CD config, or incident runbook present in the repository

No CI/CD configuration files (no `.github/workflows/`, no `Dockerfile`, no deploy scripts) were found. No rollback procedure or backup strategy is documented. The broken `src/utils.js:formatPrice` function has no associated fix or revert commit visible. If this stub were promoted to a real service, there would be no automated recovery path. No health-check endpoints or observability hooks are present in `src/index.js`.
