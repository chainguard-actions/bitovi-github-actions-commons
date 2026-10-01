<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v2.0.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v2.0.9** was hardened automatically. 2 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml use mutable tag refs (@v4) instead of pinned 40-character SHA commit digests. An attacker who compromises the upstream action repository could push a malicious commit under the same tag and have it execute in all workflows using this action.

Failing references:
- `uses: actions/checkout@v4` (line ~1467)
- `uses: actions/upload-artifact@v4` (line ~1490)

These should be pinned to their full SHA digests, e.g.:
  `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`
  `uses: actions/upload-artifact@65462800fd760344b1a7b4382951275a0abb4808 # v4`

Locations:

- `action.yaml:1467`
- `action.yaml:1490`

### script-injection (severity: high)

Sub-rule (b): The env var `GITHUB_ACTION_PATH` is sourced from `${{ github.action_path }}` (a `github.*` context value) in the `env:` block of two composite action steps, then used **unquoted** in the `run:` shell commands. An unquoted shell expansion allows the shell to parse metacharacters out of the value.

Affected steps and offending lines:

1. "Deploy with BitOps" step — run block:
   ```
   $GITHUB_ACTION_PATH/operations/_scripts/deploy/deploy.sh
   $GITHUB_ACTION_PATH/operations/_scripts/deploy/export_vars.sh
   ```

2. "Generate Summary Output" step — run block:
   ```
   $GITHUB_ACTION_PATH/operations/_scripts/deploy/summary.sh
   ```

Fix: quote the variable in all uses, e.g. `"$GITHUB_ACTION_PATH"/operations/_scripts/deploy/deploy.sh`

Locations:

- `action.yaml:1471`
- `action.yaml:1487`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed two unpinned action references: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 and actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02. Fixed three unquoted $GITHUB_ACTION_PATH usages in run blocks (deploy.sh, export_vars.sh, summary.sh) by quoting them as "$GITHUB_ACTION_PATH"/... to prevent shell metacharacter interpretation.

### Iteration 2

**Fixes applied:** invalid-yaml

**Notes:**

Fixed YAML parse error at line 1759 in hardened/action/action.yaml. The line `run: "$GITHUB_ACTION_PATH"/operations/_scripts/deploy/summary.sh` was causing a YAML parsing failure because YAML interpreted the double-quoted `"$GITHUB_ACTION_PATH"` as a complete scalar and rejected the trailing path. Converted to a block scalar (`run: |`) with the command indented on the next line, which makes the entire command a literal string and resolves the parse error.

### Iteration 3

**Fixes applied:** github-env-injection, script-injection

**Notes:**

Fixed two security findings:

1. export_vars.sh (github-env-injection): Replaced `cat $BO_OUT >> $GITHUB_OUTPUT` with a sanitizing while loop that reads each line, strips newlines/carriage returns via `tr -d '\n\r'`, then writes the safe value to GITHUB_OUTPUT. This prevents terraform output values (which can be influenced by attacker-controlled inputs) from injecting arbitrary key=value pairs into GITHUB_OUTPUT via embedded newlines.

2. deploy.sh (script-injection, 3 locations):
   - BITOPS_EXTRA_ENV_VARS_FILE: Converted from unquoted string expansion to a bash array `BITOPS_EXTRA_ENV_VARS_FILE_ARGS=(--env-file "$path")`, safely expanded as `"${BITOPS_EXTRA_ENV_VARS_FILE_ARGS[@]}"` in docker run.
   - BITOPS_EXTRA_ENV_VARS: Converted from unquoted string expansion to a bash array `BITOPS_EXTRA_ENV_VARS_ARGS` built with `+=(-e "$i")` in a while loop, safely expanded as `"${BITOPS_EXTRA_ENV_VARS_ARGS[@]}"` in docker run.
   - GITHUB_ACTION_PATH: Removed the unnecessary `$(echo $GITHUB_ACTION_PATH)` command substitution and replaced with properly quoted `"${GITHUB_ACTION_PATH}/operations:/opt/bitops_deployment"`.

