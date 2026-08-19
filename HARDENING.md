<!-- markdownlint-disable -->

# Hardening Report: nwtgck--actions-netlify/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nwtgck--actions-netlify/v2.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tag or version refs instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced tag is moved or overwritten.

In `.github/workflows/comment-run.yml`:
- `actions/checkout@v2` (line 10)
- `actions/setup-node@v3` (line 14)
- `nwtgck/actions-comment-run@v1.1` (line 16)

In `.github/workflows/test.yml`:
- `actions/setup-node@v3` (line 15)
- `actions/checkout@v3` (line 17)
- `actions/checkout@v3` (line 23)

Locations:

- `.github/workflows/comment-run.yml:10`
- `.github/workflows/comment-run.yml:14`
- `.github/workflows/comment-run.yml:16`
- `.github/workflows/test.yml:15`
- `.github/workflows/test.yml:17`
- `.github/workflows/test.yml:23`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is directly interpolated inside a `run:` shell command string. In `.github/workflows/test.yml` line 36, the step output `${{ steps.deploy-to-netlify.outputs.deploy-url }}` is embedded directly in the shell command: `run: "echo 'outputs.deploy-url: ${{ steps.deploy-to-netlify.outputs.deploy-url }}'"`. This value flows through YAML template substitution before the shell sees it, allowing any newlines or shell metacharacters in the deploy URL to be interpreted by the shell. The value should be passed via an `env:` variable and referenced as a quoted `"$ENV_VAR"` instead.

Locations:

- `.github/workflows/test.yml:36`

### missing-permissions (severity: medium)

`.github/workflows/comment-run.yml` has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be `write-all` depending on repository settings), granting broader access than necessary. A minimal `permissions:` block should be added.

Locations:

- `.github/workflows/comment-run.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings:

1. **unpinned-uses** (comment-run.yml + test.yml): Pinned all 6 action references to full 40-char SHAs:
   - actions/checkout@v2 → @0717577d45739eb3c851188b29f50ed6c0b2194e
   - actions/setup-node@v3 → @3235b876344d2a9aa001b8d1453c930bba69e610
   - nwtgck/actions-comment-run@v1.1 → @ce01ade2c0d5e78e665bc9f5c13ad040d8491ebc
   - actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26 (used twice in test.yml)
   Original tags preserved as inline comments.

2. **script-injection** (test.yml line 36): Moved `${{ steps.deploy-to-netlify.outputs.deploy-url }}` out of the `run:` shell string into an `env:` block as `DEPLOY_URL`, then referenced it as `$DEPLOY_URL` in the shell command.

3. **missing-permissions** (comment-run.yml): Added a top-level `permissions:` block with `contents: read` and `issues: write` — the minimum needed for the comment-run action to read the repo and interact with issues/comments.

