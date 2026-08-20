<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.22.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.22.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses a mutable branch ref (`@main`) instead of a pinned full-length commit SHA. This means the referenced workflow can change at any time without notice, enabling supply-chain attacks. The offending reference is: `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main`

Locations:

- `.github/workflows/propose-release.yml:14`

### github-env-injection (severity: high)

In the `check_tag` step, the env var `VERSION` is set from the untrusted GitHub context value `${{ github.ref_name }}`. A shell-derived value `shorttag` is computed from `$VERSION` and then written to `$GITHUB_OUTPUT` via `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"` without the required sanitization step (`printf '%s' "$shorttag" | tr -d '\n\r'`). A maliciously crafted release tag name containing newlines could inject arbitrary key-value pairs into `$GITHUB_OUTPUT`, potentially overwriting subsequent step outputs.

Locations:

- `.github/workflows/release.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to full commit SHA `c294441424764ab5486d56a8364fab91eebfaf53 # main`. 2. release.yml: Added sanitization of the `shorttag` value before writing to `$GITHUB_OUTPUT` — introduced `safe_shorttag=$(printf '%s' "$shorttag" | tr -d '\n\r')` and used `safe_shorttag` in the echo statement to prevent newline injection from a crafted `github.ref_name`.

