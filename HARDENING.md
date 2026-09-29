<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v1.0.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v1.0.6** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml are pinned to mutable version tags instead of full 40-character SHA commit digests. This exposes the action to supply-chain attacks if the upstream tag is moved or overwritten.

- `uses: actions/checkout@v4` (tag `v4` is mutable)
- `uses: actions/upload-artifact@v4` (tag `v4` is mutable)

These should be replaced with their corresponding full SHA digests, e.g.:
  `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`
  `uses: actions/upload-artifact@ea165f8d65b6e75b540449e92b4886f43607fa02 # v4`

Locations:

- `action.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned two mutable `uses:` references in hardened/action/action.yaml:
- `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`
- `actions/upload-artifact@v4` → `actions/upload-artifact@ea165f8d65b6e75b540449e92b4886f43607fa02 # v4`

SHAs were resolved via lookup_action_sha and are the actual commit digests for the v4 tags.

### Iteration 1

**Fixes applied:** github-env-injection, script-injection

**Notes:**

Fixed github-env-injection in export_vars.sh by replacing direct `cat >> $GITHUB_OUTPUT` with a sanitizing while-read loop that strips newlines/carriage returns from each line before writing. Fixed script-injection in deploy.sh (quoted $GITHUB_REPOSITORY in echo|sed, quoted $TF_STATE_BUCKET and $AWS_ELB_ACCESS_LOG_BUCKET_NAME as positional args to check_bucket_name.sh), generate_identifier.sh (quoted $GITHUB_REPOSITORY in echo|sed pipelines, quoted ${GITHUB_IDENTIFIER} and $1 in shorten_identifier calls, quoted $GITHUB_IDENTIFIER in final echo|tr pipeline), and generate_vars_terraform.sh (quoted $GITHUB_REPOSITORY in both echo|sed pipelines).

