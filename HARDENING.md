<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.21.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.21.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

propose-release.yml references a reusable workflow using a mutable branch ref (@main) instead of a pinned 40-character commit SHA. This means the workflow could silently change if the upstream branch is updated or compromised. Failing reference: `uses: DeterminateSystems/propose-release/.github/workflows/workflow.yml@main`

Locations:

- `.github/workflows/propose-release.yml:17`

### github-env-injection (severity: high)

In release.yml, the variable `shorttag` is derived from `$VERSION` (which is set from `github.ref_name` via the `env:` block) and written directly to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' "$shorttag" | tr -d '\n\r'`). A crafted tag name containing newlines could inject additional key=value pairs into GITHUB_OUTPUT, potentially poisoning downstream step outputs. The offending line is: `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"`

Locations:

- `.github/workflows/release.yml:26`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml: Pinned the reusable workflow `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to the full commit SHA `c294441424764ab5486d56a8364fab91eebfaf53` (with `# main` comment for readability). 2. release.yml: Added sanitization of the `shorttag` variable before writing to `$GITHUB_OUTPUT` — now uses `printf '%s' "$shorttag" | tr -d '\n\r'` to strip any embedded newlines that could inject additional key=value pairs into GITHUB_OUTPUT.

