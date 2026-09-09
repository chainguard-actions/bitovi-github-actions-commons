<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v0.0.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v0.0.13** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yaml references two GitHub Actions using mutable version tags instead of pinned SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved. Failing references: `uses: actions/checkout@v4` and `uses: actions/upload-artifact@v3`.

Locations:

- `action.yaml:1`
- `action.yaml:1`

### unpinned-uses (severity: high)

The workflow file unit_tests.yaml references two GitHub Actions using mutable version tags instead of pinned SHA digests. Failing references: `uses: mig4/setup-bats@v1` and `uses: actions/checkout@v2`.

Locations:

- `.github/workflows/unit_tests.yaml:10`
- `.github/workflows/unit_tests.yaml:13`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all four unpinned action references to full commit SHAs:
- hardened/action/action.yaml: `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- hardened/action/action.yaml: `actions/upload-artifact@v3` → `actions/upload-artifact@ff15f0306b3f739f7b6fd43fb5d26cd321bd4de5 # v3`
- hardened/action/.github/workflows/unit_tests.yaml: `mig4/setup-bats@v1` → `mig4/setup-bats@af9a00deb21b5d795cabfeaa8d9060410377686d # v1`
- hardened/action/.github/workflows/unit_tests.yaml: `actions/checkout@v2` → `actions/checkout@0717577d45739eb3c851188b29f50ed6c0b2194e # v2`

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed three unquoted variable expansions in operations/_scripts/deploy/deploy.sh:
1. Converted BITOPS_EXTRA_ENV_VARS_FILE from an unquoted string to a bash array (BITOPS_EXTRA_ENV_VARS_FILE_ARGS) that is safely expanded with "${BITOPS_EXTRA_ENV_VARS_FILE_ARGS[@]}" in the docker run command.
2. Converted BITOPS_EXTRA_ENV_VARS from an unquoted string to a bash array (BITOPS_EXTRA_ENV_VARS_ARGS) built with properly quoted elements, expanded with "${BITOPS_EXTRA_ENV_VARS_ARGS[@]}" in the docker run command.
3. Replaced the unquoted $(echo $GITHUB_ACTION_PATH) with a properly quoted "${GITHUB_ACTION_PATH}" in the -v mount argument. The script uses #!/bin/bash so bash arrays are appropriate.

