<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v2.0.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v2.0.8** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags instead of immutable 40-character SHA digests, making the action vulnerable to supply-chain attacks if the referenced tag is moved or overwritten. Failing references in action.yaml: `actions/checkout@v4`, `actions/upload-artifact@v4`. Failing references in .github/workflows/ci.yml: `actions/checkout@v5`, `hashicorp/setup-terraform@v3`, `actions/github-script@v7`. Failing references in .github/workflows/unit_tests.yaml: `mig4/setup-bats@v1`, `actions/checkout@v2`.

Locations:

- `action.yaml:1553`
- `action.yaml:1637`
- `.github/workflows/ci.yml:14`
- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:113`
- `.github/workflows/unit_tests.yaml:14`
- `.github/workflows/unit_tests.yaml:17`

### missing-permissions (severity: medium)

The workflow file .github/workflows/ci.yml has no top-level `permissions:` key and no job-level `permissions:` key on its `terraform-validation` job. Without explicit permissions, the job inherits the default repository token permissions, which may be overly broad (write access to contents and other scopes depending on repository settings). A minimal `permissions:` block should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by replacing mutable tags with full 40-character SHA digests: (1) action.yaml: actions/checkout@v4 → @11d5960a..., actions/upload-artifact@v4 → @ea165f8d...; (2) .github/workflows/ci.yml: actions/checkout@v5 → @fbc6f399..., hashicorp/setup-terraform@v3 → @b9cd54a3..., actions/github-script@v7 → @f28e40c7...; (3) .github/workflows/unit_tests.yaml: mig4/setup-bats@v1 → @af9a00de..., actions/checkout@v2 → @0717577d.... Added top-level `permissions: contents: read` to ci.yml and a job-level permissions block (`contents: read`, `issues: write`, `pull-requests: write`) to the terraform-validation job — the github-script step creates PR comments so it needs issues/pull-requests write access.

### Iteration 2

**Fixes applied:** script-injection, github-env-injection

**Notes:**

1. deploy.sh (script-injection): Converted BITOPS_EXTRA_ENV_VARS_FILE and BITOPS_EXTRA_ENV_VARS from unquoted string variables to bash arrays (BITOPS_EXTRA_ENV_VARS_FILE_ARGS and BITOPS_EXTRA_ENV_VARS_ARGS). The --env-file path is stored as properly quoted array elements, and the BITOPS_ env-var loop appends each -e KEY=VAL as separate quoted array elements. The docker run command now uses "${BITOPS_EXTRA_ENV_VARS_FILE_ARGS[@]}" and "${BITOPS_EXTRA_ENV_VARS_ARGS[@]}" to prevent shell metacharacter injection while preserving argument boundaries.

2. export_vars.sh (github-env-injection): Replaced the raw `cat $BO_OUT >> $GITHUB_OUTPUT` with a while-read loop that reads each line, sanitizes it with `printf '%s' "$line" | tr -d '\n\r'`, and writes the safe value to GITHUB_OUTPUT. This prevents embedded newlines in Terraform output values from injecting additional entries into GITHUB_OUTPUT.

