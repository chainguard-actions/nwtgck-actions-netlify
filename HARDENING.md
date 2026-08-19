<!-- markdownlint-disable -->

# Hardening Report: nwtgck--actions-netlify/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nwtgck--actions-netlify/v2.1.0** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings:
1. unpinned-uses: Pinned all action references to full SHAs — actions/checkout@v3 → a37ce9120846195fa4ece8f58b268e6043cb2f26, actions/setup-node@v3 → 3235b876344d2a9aa001b8d1453c930bba69e610, nwtgck/actions-comment-run@v2.0 → 9fc2ff922e2677245e44e7aec38d1d5a58e0f91e — in both .github/workflows/comment-run.yml and .github/workflows/test.yml.
2. missing-permissions: Added top-level permissions block to comment-run.yml with minimal scopes (contents: read, issues: write) needed for the checkout and comment-run actions.
3. script-injection: In test.yml, moved ${{ steps.deploy-to-netlify.outputs.deploy-url }} out of the run: shell string into an env: block as DEPLOY_URL, then referenced it as $DEPLOY_URL in the shell command.

