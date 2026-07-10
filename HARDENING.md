<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.21.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **DeterminateSystems--determinate-nix-action/v3.21.5** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` which is pinned to a mutable branch name (`@main`) rather than a full 40-character commit SHA. This means the referenced workflow can change at any time without notice, creating a supply-chain risk.

Locations:

- `.github/workflows/propose-release.yml:17`

### github-env-injection (severity: high)

In the 'Optionally tag a vMAJOR tag' step, the env var VERSION is set from `${{ github.ref_name }}` (an untrusted GitHub context value). The script derives `shorttag` from `$VERSION` and writes it to $GITHUB_OUTPUT via `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"` without first sanitizing with `printf '%s' ... | tr -d '\n\r'`. An attacker who can control the ref name (e.g. a tag containing a newline) could inject arbitrary key-value pairs into GITHUB_OUTPUT.

Locations:

- `.github/workflows/release.yml:26`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to full commit SHA `c294441424764ab5486d56a8364fab91eebfaf53` with `# main` comment for readability. 2. release.yml: Added sanitization of the `shorttag` value (derived from `github.ref_name`) before writing to `$GITHUB_OUTPUT` using `printf '%s' "$shorttag" | tr -d '\n\r'` to strip any embedded newlines that could inject arbitrary key-value pairs.

