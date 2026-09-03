<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.22.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.22.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses a mutable branch ref `@main` instead of a pinned 40-character commit SHA for `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main`. This means the referenced workflow can be changed at any time without notice, creating a supply-chain attack vector.

Locations:

- `.github/workflows/propose-release.yml:17`

### github-env-injection (severity: high)

In release.yml, the step 'Optionally tag a vMAJOR tag' writes `shorttag` to `$GITHUB_OUTPUT` without sanitization. `shorttag` is derived from the `$VERSION` env var, which is set from `${{ github.ref_name }}` (an untrusted input). The write `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"` is missing the required sanitization step (`printf '%s' "$shorttag" | tr -d '\n\r'`) before the write. A crafted ref name containing newline characters could inject additional key=value pairs into GITHUB_OUTPUT.

Locations:

- `.github/workflows/release.yml:26`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to the resolved commit SHA `c294441424764ab5486d56a8364fab91eebfaf53` with a `# main` comment for readability. 2. release.yml: Added sanitization of the `shorttag` variable (derived from the untrusted `github.ref_name`) before writing to `$GITHUB_OUTPUT`. The value is now passed through `printf '%s' "$shorttag" | tr -d '\n\r'` to strip any embedded newline or carriage-return characters that could inject additional key=value pairs into GITHUB_OUTPUT.

