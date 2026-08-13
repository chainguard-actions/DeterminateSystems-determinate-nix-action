<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.19.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.19.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved or compromised.

- ci.yml: `uses: actions/checkout@v4` (tag `v4`)
- propose-release.yml: `uses: DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` (branch `main`)
- release.yml: `uses: actions/checkout@v4` (tag `v4`)

Locations:

- `.github/workflows/ci.yml:10`
- `.github/workflows/propose-release.yml:17`
- `.github/workflows/release.yml:22`

### github-env-injection (severity: high)

In release.yml, the step 'Optionally tag a vMAJOR tag' sets the env var VERSION from `${{ github.ref_name }}` (an untrusted GitHub context value), derives `shorttag` from it via `shorttag=$(echo "$VERSION" | cut -d'.' -f1)`, and then writes it to $GITHUB_OUTPUT with `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"` — without the required sanitization step (`printf '%s' "$shorttag" | tr -d '\n\r'`) applied before the write. An attacker who can influence the release tag name (e.g. by embedding a newline) could inject arbitrary key-value pairs into GITHUB_OUTPUT.

Locations:

- `.github/workflows/release.yml:21`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

Fixed all three unpinned action references: actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262 in ci.yml and release.yml; DeterminateSystems/propose-release workflow @main → @c294441424764ab5486d56a8364fab91eebfaf53 in propose-release.yml. Fixed github-env-injection in release.yml by sanitizing the shorttag value with `printf '%s' "$shorttag" | tr -d '\n\r'` before writing to $GITHUB_OUTPUT.

