<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v2.0.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v2.0.8** was hardened automatically. 1 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yaml use mutable version tags (@v4) instead of pinned 40-character SHA digests. This exposes the action to supply-chain attacks if the upstream action is compromised or the tag is moved. Affected references: `actions/checkout@v4` and `actions/upload-artifact@v4`.

Locations:

- `action.yaml:1413`
- `action.yaml:1697`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned two mutable `@v4` action references in hardened/action/action.yaml to their full 40-character commit SHAs: `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4` (line 1413), and `actions/upload-artifact@v4` → `actions/upload-artifact@ea165f8d65b6e75b540449e92b4886f43607fa02 # v4` (line 1697). The original tag is preserved as a comment for readability.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection vulnerability in operations/_scripts/deploy/deploy.sh by converting BITOPS_EXTRA_ENV_VARS_FILE and BITOPS_EXTRA_ENV_VARS from unquoted string variables to bash arrays. BITOPS_EXTRA_ENV_VARS_FILE_ARGS=() is now populated with (--env-file "$path") when the env file exists, and BITOPS_EXTRA_ENV_VARS_ARGS=() is built by appending (-e "${i}") for each BITOPS_ env var. The docker run command now uses "${BITOPS_EXTRA_ENV_VARS_FILE_ARGS[@]}" and "${BITOPS_EXTRA_ENV_VARS_ARGS[@]}" which safely expand into multiple arguments without allowing shell metacharacters to break out of the intended argument structure.

### Iteration 1

**Fixes applied:** github-env-injection, script-injection

**Notes:**

Fixed two high-severity findings: (1) In export_vars.sh, replaced direct `cat $BO_OUT >> $GITHUB_OUTPUT` with a sanitizing while-read loop that strips newlines/carriage returns from each line before writing to GITHUB_OUTPUT, preventing newline injection. (2) In deploy.sh, quoted all unquoted variable expansions: $GITHUB_REPOSITORY in command substitution, $ANSIBLE_SKIP and $TF_STACK_DESTROY passed to alpha_only(), all $GITHUB_ACTION_PATH path references in /bin/bash invocations, $TF_STATE_BUCKET as a positional argument to check_bucket_name.sh, and the cat/docker -v mount references.

