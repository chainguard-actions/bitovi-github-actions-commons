<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v2.0.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v2.0.9** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yaml references two GitHub Actions using mutable tag refs instead of full 40-character SHA digests. `actions/checkout@v4` and `actions/upload-artifact@v4` are both pinned to version tags, which can be silently redirected to different (potentially malicious) commits if the upstream repository is compromised. They must be pinned to their full commit SHA (e.g., `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `action.yaml:1`
- `action.yaml:1`

### github-env-injection (severity: high)

In `operations/_scripts/deploy/export_vars.sh` (invoked from the `run:` block of the 'Deploy with BitOps' step in action.yaml), the entire contents of `bo-out.env` are written directly to `$GITHUB_OUTPUT` via `cat $BO_OUT >> $GITHUB_OUTPUT` without newline sanitization. The `bo-out.env` file is generated from `terraform output` and can contain values derived from user-controlled inputs (e.g., branch names, resource identifiers, tag values). A value containing embedded newlines could inject additional `key=value` pairs into `$GITHUB_OUTPUT`, allowing an attacker to overwrite outputs consumed by downstream steps. The fix requires sanitizing each value with `printf '%s' "$value" | tr -d '\n\r'` before writing to the special environment file.

Locations:

- `operations/_scripts/deploy/export_vars.sh:10`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. Pinned `actions/checkout@v4` to `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4` in action.yaml. 2. Pinned `actions/upload-artifact@v4` to `actions/upload-artifact@ea165f8d65b6e75b540449e92b4886f43607fa02 # v4` in action.yaml. 3. Fixed `operations/_scripts/deploy/export_vars.sh`: replaced the unsafe `cat $BO_OUT >> $GITHUB_OUTPUT` with a loop that reads each line, splits key from value on the first '=', sanitizes the value with `printf '%s' "$value" | tr -d '\n\r'`, and writes the sanitized pair to $GITHUB_OUTPUT — preventing newline injection from terraform output values.

