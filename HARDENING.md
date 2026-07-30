<!-- markdownlint-disable -->

# Hardening Report: DeterminateSystems--determinate-nix-action/v3.21.9

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **DeterminateSystems--determinate-nix-action/v3.21.9** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses an unpinned `uses:` reference with a mutable branch ref (`@main`) instead of a full 40-character commit SHA. This means the referenced workflow can change at any time without notice, enabling supply-chain attacks. Offending line: `uses: DeterminateSystems/propose-release/.github/workflows/workflow.yml@main`

Locations:

- `.github/workflows/propose-release.yml:17`

### github-env-injection (severity: high)

In the `Optionally tag a vMAJOR tag` step, the variable `shorttag` is derived from `$VERSION` (which is set from `github.ref_name` via the `env:` block — an untrusted input) and written directly to `$GITHUB_OUTPUT` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). Although `cut -d'.' -f1` limits the value, it does not strip embedded newline or carriage-return characters, so a crafted ref name could inject additional key=value pairs into `$GITHUB_OUTPUT`. The offending line is: `echo "shorttag=$shorttag" >> "$GITHUB_OUTPUT"`

Locations:

- `.github/workflows/release.yml:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, github-env-injection

**Notes:**

1. propose-release.yml: Pinned `DeterminateSystems/propose-release/.github/workflows/workflow.yml@main` to full SHA `c294441424764ab5486d56a8364fab91eebfaf53` with `# main` comment for readability.
2. release.yml: Added sanitization of the `shorttag` variable before writing to `$GITHUB_OUTPUT`. The value is now passed through `printf '%s' "$shorttag" | tr -d '\n\r'` to strip any embedded newline or carriage-return characters that could be used to inject additional key=value pairs into the output file.

