<!-- markdownlint-disable -->

# Hardening Report: bitovi--github-actions-commons/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **bitovi--github-actions-commons/v1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yaml references two GitHub Actions by mutable version tags instead of full 40-character SHA digests, making the action vulnerable to supply-chain attacks if those tags are moved. Failing references: `uses: actions/checkout@v4` and `uses: actions/upload-artifact@v4`.

Locations:

- `action.yaml:1`
- `action.yaml:1`

### unpinned-uses (severity: high)

`.github/workflows/unit_tests.yaml` references two GitHub Actions by mutable version tags instead of full 40-character SHA digests. Failing references: `uses: mig4/setup-bats@v1` and `uses: actions/checkout@v2`.

Locations:

- `.github/workflows/unit_tests.yaml:9`
- `.github/workflows/unit_tests.yaml:12`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned all four unpinned action references to full 40-character SHA digests: (1) actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 in action.yaml, (2) actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02 in action.yaml, (3) mig4/setup-bats@v1 → @af9a00deb21b5d795cabfeaa8d9060410377686d in .github/workflows/unit_tests.yaml, (4) actions/checkout@v2 → @0717577d45739eb3c851188b29f50ed6c0b2194e in .github/workflows/unit_tests.yaml. Original tag names preserved as inline comments.

