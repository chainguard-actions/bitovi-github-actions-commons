<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml are pinned to mutable version tags instead of immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or compromised. Failing references: `actions/checkout@v4` and `actions/upload-artifact@v4`.

Locations:

- `action.yaml:1`

### github-env-injection (severity: high)

In `operations/_scripts/deploy/export_vars.sh` (line 10), the entire content of the `bo-out.env` file is written directly to `$GITHUB_OUTPUT` via `cat $BO_OUT >> $GITHUB_OUTPUT` without any newline sanitization (`tr -d '\n\r'`). The `bo-out.env` file is generated inside the BitOps Docker container by `generate-outputs.sh` using `terraform output`, which processes Terraform resource outputs derived from action inputs (e.g., `aws_resource_identifier`, `aws_additional_tags`, resource names, etc.) that are caller-controlled. A malicious value containing a newline followed by a key=value pair could inject arbitrary entries into `$GITHUB_OUTPUT`, potentially overwriting outputs consumed by downstream steps.

Locations:

- `operations/_scripts/deploy/export_vars.sh:10`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. Pinned `actions/checkout@v4` to immutable SHA `11d5960a326750d5838078e36cf38b85af677262 # v4` in action.yaml.
2. Pinned `actions/upload-artifact@v4` to immutable SHA `ea165f8d65b6e75b540449e92b4886f43607fa02 # v4` in action.yaml.
3. Fixed `operations/_scripts/deploy/export_vars.sh`: replaced the unsafe `cat $BO_OUT >> $GITHUB_OUTPUT` with a line-by-line loop that sanitizes each line using `printf '%s' "$line" | tr -d '\n\r'` before writing to `$GITHUB_OUTPUT`, preventing newline injection of arbitrary key=value pairs into GitHub Actions outputs.

