<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.21.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.21.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks:
- ci.yml: `uses: actions/checkout@v4` (tag)
- propose-release.yml: `uses: DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` (branch)
- release.yml: `uses: actions/checkout@v4` (tag)

Locations:

- `.github/workflows/ci.yml:11`
- `.github/workflows/propose-release.yml:14`
- `.github/workflows/release.yml:20`

### github-env-injection (severity: high)

In release.yml, the `check_tag` step writes a value derived from `github.ref_name` (an untrusted GitHub context) to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). The value flows: `github.ref_name` → env var `VERSION` → shell variable `shorttag` (via `cut`) → `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"`. A crafted ref name containing newlines could inject additional key=value pairs into GITHUB_OUTPUT.

Locations:

- `.github/workflows/release.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. ci.yml: Pinned `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`.
2. propose-release.yml: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` → `...@c294441424764ab5486d56a8364fab91eebfaf53 # main`.
3. release.yml: Pinned `actions/checkout@v4` → `actions/checkout@11d5960a326750d5838078e36cf38b85af677262 # v4`. Also fixed github-env-injection in the `check_tag` step: the `shorttag` derivation now uses `printf '%s' "$VERSION" | cut -d'.' -f1 | tr -d '\n\r'` to strip any embedded newlines from the untrusted `github.ref_name` value before writing to `$GITHUB_OUTPUT`.

