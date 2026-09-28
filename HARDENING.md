<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v1** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml are pinned to mutable version tags (@v4) rather than immutable 40-character commit SHA digests. This exposes the action to supply-chain attacks if the upstream tag is moved or compromised. Affected references: `actions/checkout@v4` and `actions/upload-artifact@v4`.

Locations:

- `action.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned two mutable action references in hardened/action/action.yaml:
- `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- `actions/upload-artifact@v4` → `actions/upload-artifact@ea165f8d65b6e75b540449e92b4886f43607fa02 # v4`
SHAs were resolved via lookup_action_sha and verified by reading back the file.

### Iteration 1

**Fixes applied:** github-env-injection

**Notes:**

Fixed export_vars.sh: replaced `cat $BO_OUT >> $GITHUB_OUTPUT` with a while-read loop that splits each line into key and value, sanitizes the value using `printf '%s' "$value" | tr -d '\n\r'` to strip embedded newlines/carriage returns, and writes the sanitized pair to $GITHUB_OUTPUT. This prevents injection of additional key=value pairs via newlines embedded in Terraform output values.

