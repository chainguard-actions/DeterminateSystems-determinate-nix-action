<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.21.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.21.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses a mutable branch ref (`@main`) instead of a pinned full-length commit SHA. This means the referenced workflow can be silently updated to run arbitrary code in future runs. Failing reference: `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main`

Locations:

- `.github/workflows/propose-release.yml:16`

### github-env-injection (severity: high)

In the `Optionally tag a vMAJOR tag` step, `github.ref_name` is placed into the env var `VERSION`, then `shorttag` is derived from it via `$(echo "$VERSION" | cut -d'.' -f1)` and written to `$GITHUB_OUTPUT` with `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"`. No sanitization (`printf '%s' ... | tr -d '\n\r'`) is applied before the write. A tag name containing a newline could inject arbitrary key-value pairs into `$GITHUB_OUTPUT`, potentially poisoning subsequent steps.

Locations:

- `.github/workflows/release.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml line 16: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to full commit SHA `c294441424764ab5486d56a8364fab91eebfaf53` (with `# main` comment for readability). 2. release.yml line 22: Added newline sanitization for `shorttag` before writing to `$GITHUB_OUTPUT` — now uses `safe_shorttag=$(printf '%s' "$shorttag" | tr -d '\n\r')` and writes `safe_shorttag` instead, preventing newline injection into the output file.

