<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v2.0.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v2.0.9** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags instead of full 40-character commit SHAs, making the action vulnerable to supply-chain attacks if the tag is moved.

**action.yaml:**
- `actions/checkout@v4` (line ~57397)
- `actions/upload-artifact@v4` (line ~89727)

**ci.yml:**
- `actions/checkout@v5`
- `hashicorp/setup-terraform@v3`
- `actions/github-script@v7`

**unit_tests.yaml:**
- `mig4/setup-bats@v1`
- `actions/checkout@v2`

All of these should be pinned to a full SHA digest, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yaml:1`
- `.github/workflows/ci.yml:10`
- `.github/workflows/unit_tests.yaml:13`

### missing-permissions (severity: medium)

The workflow file `ci.yml` has no top-level `permissions:` key and its only job (`terraform-validation`) also has no job-level `permissions:` key. Without explicit permissions, the job inherits the default repository token permissions, which may be overly broad (write access to contents, pull-requests, etc.). A minimal permissions block such as `permissions: contents: read` should be added.

Locations:

- `.github/workflows/ci.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all unpinned `uses:` references to full commit SHAs: actions/checkout@v4 → 11d5960a..., actions/upload-artifact@v4 → ea165f8d... (action.yaml); actions/checkout@v5 → fbc6f399..., hashicorp/setup-terraform@v3 → b9cd54a3..., actions/github-script@v7 → f28e40c7... (ci.yml); mig4/setup-bats@v1 → af9a00de..., actions/checkout@v2 → 0717577d... (unit_tests.yaml). Added top-level `permissions: contents: read` to ci.yml to fix the missing-permissions finding.

