<!-- markdownlint-disable -->

# Hardening Report: nwtgck--actions-netlify/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nwtgck--actions-netlify/v4.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tag-based refs instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced tag is moved or overwritten.

In `.github/workflows/comment-run.yml`:
- `uses: actions/checkout@v4`
- `uses: actions/setup-node@v4`
- `uses: nwtgck/actions-comment-run@v3.0`

In `.github/workflows/test.yml`:
- `uses: actions/setup-node@v4`
- `uses: actions/checkout@v4` (appears twice)

All should be pinned to their full 40-character commit SHA (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/comment-run.yml:10`
- `.github/workflows/comment-run.yml:13`
- `.github/workflows/comment-run.yml:15`
- `.github/workflows/test.yml:14`
- `.github/workflows/test.yml:16`
- `.github/workflows/test.yml:23`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/comment-run.yml` has no top-level `permissions:` key and its only job (`comment-run`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. `write` access to all scopes). A minimal explicit `permissions:` block should be added.

Locations:

- `.github/workflows/comment-run.yml:1`

### script-injection (severity: high)

Sub-rule (a): A `run:` block in `.github/workflows/test.yml` directly interpolates a GitHub Actions expression inside a shell command string. The expression `${{ steps.deploy-to-netlify.outputs.deploy-url }}` is substituted into the shell command before the shell parses it, allowing an attacker who can influence the deploy URL output to inject arbitrary shell commands.

Offending line:
```yaml
- run: "echo 'outputs.deploy-url: ${{ steps.deploy-to-netlify.outputs.deploy-url }}'"
```

Fix: Move the value into an `env:` variable and reference it as a quoted shell variable:
```yaml
env:
  DEPLOY_URL: ${{ steps.deploy-to-netlify.outputs.deploy-url }}
run: echo "outputs.deploy-url: $DEPLOY_URL"
```

Locations:

- `.github/workflows/test.yml:36`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings:
1. unpinned-uses: Pinned all action references to full SHAs:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 # v4 (in both files, 3 occurrences)
   - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020 # v4 (in both files, 2 occurrences)
   - nwtgck/actions-comment-run@v3.0 → @eef5a9123b8fdc13af1cbd4cb8292316c0cbe2c5 # v3.0
2. missing-permissions: Added top-level `permissions:` block to comment-run.yml with `contents: read` and `issues: write` (minimal permissions needed for checkout and comment-run action).
3. script-injection: In test.yml, moved `${{ steps.deploy-to-netlify.outputs.deploy-url }}` from the run: shell string into an `env:` block as `DEPLOY_URL`, then referenced it as `$DEPLOY_URL` in the shell command.

