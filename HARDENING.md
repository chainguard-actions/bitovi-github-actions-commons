<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v0.0.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v0.0.13** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yaml references two GitHub Actions using mutable version tags instead of pinned 40-character SHA commit hashes. This exposes the action to supply-chain attacks if the upstream action tags are moved or compromised. Failing references: `uses: actions/checkout@v4` and `uses: actions/upload-artifact@v3`.

Locations:

- `action.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned two unpinned action references in hardened/action/action.yaml:
1. `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
2. `actions/upload-artifact@v3` → `actions/upload-artifact@ff15f0306b3f739f7b6fd43fb5d26cd321bd4de5 # v3`

Both SHAs were resolved via lookup_action_sha and the original version tags are preserved as inline comments for readability.

### Iteration 1

**Fixes applied:** script-injection, github-env-injection

**Notes:**

1. script-injection (action.yaml): Quoted all three unquoted $GITHUB_ACTION_PATH usages in run: blocks — two in the 'Deploy with BitOps' step (deploy.sh and export_vars.sh invocations) and one in the 'Generate Summary Output' step (summary.sh invocation). Changed from `$GITHUB_ACTION_PATH/...` to `"$GITHUB_ACTION_PATH/..."`.
2. github-env-injection (export_vars.sh): Replaced the unsafe `cat $BO_OUT >> $GITHUB_OUTPUT` with a while-read loop that splits each line into key and value, sanitizes the value using `printf '%s' "$value" | tr -d '\n\r'`, and writes the sanitized pair to $GITHUB_OUTPUT. This prevents injection of additional output variables via embedded newlines in values derived from user-controlled inputs.

