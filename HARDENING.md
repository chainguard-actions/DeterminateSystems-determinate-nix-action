<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.22.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.22.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main`, which is pinned to a mutable branch name (`main`) rather than a full 40-character commit SHA. This means the referenced workflow can change at any time without notice, enabling a supply-chain attack if the upstream repository is compromised or the branch is force-pushed.

Locations:

- `.github/workflows/propose-release.yml:16`

### github-env-injection (severity: high)

In the `check_tag` step of release.yml, the value of `shorttag` is derived from `$VERSION` (which is sourced from `github.ref_name` via an `env:` mapping) and written directly to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). Although the value passes a regex check, the newline-stripping sanitization is still required before any write to a special environment file when the source is an untrusted context like `github.ref_name`. The offending line is: `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"`.

Locations:

- `.github/workflows/release.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to full SHA `c294441424764ab5486d56a8364fab91eebfaf53` (with `# main` comment for readability). 2. release.yml: Added newline sanitization for the `shorttag` value before writing to `$GITHUB_OUTPUT` — introduced `safe_shorttag=$(printf '%s' "$shorttag" | tr -d '\n\r')` and used `safe_shorttag` in the echo statement.

