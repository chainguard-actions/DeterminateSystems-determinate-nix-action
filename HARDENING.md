<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.21.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.21.8** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses a mutable branch ref (`@main`) instead of a pinned 40-character commit SHA for `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main`. This means the referenced workflow can change at any time without notice, enabling supply-chain attacks.

Locations:

- `.github/workflows/propose-release.yml:14`

### github-env-injection (severity: high)

In the `check_tag` step of release.yml, the value `shorttag` is derived from `$VERSION` (which is set from `github.ref_name`, an untrusted/attacker-influenced input) and written directly to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' "$shorttag" | tr -d '\n\r'`). A crafted tag name containing newlines could inject arbitrary key-value pairs into the GitHub output context. The offending line is: `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"`

Locations:

- `.github/workflows/release.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml line 14: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to the resolved commit SHA `c294441424764ab5486d56a8364fab91eebfaf53` (with `# main` comment for readability). 2. release.yml line 22: Added sanitization of the `shorttag` value (derived from `$VERSION`/`github.ref_name`) using `printf '%s' "$shorttag" | tr -d '\n\r'` before writing to `$GITHUB_OUTPUT`, preventing newline injection attacks.

