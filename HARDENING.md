<!-- markdownlint-disable -->

# Hardening Report: JoshuaTheMiller--conditional-build-matrix/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JoshuaTheMiller--conditional-build-matrix/v2.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a `run:` shell command string. On line 27, `run: echo "${{ matrix.runOn }}"` embeds `${{ matrix.runOn }}` directly in the shell command. Even though `matrix.runOn` comes from the matrix strategy (which is itself derived from the action's filtered output), any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, allowing metacharacters to break out of the intended command.

Locations:

- `.github/workflows/sample.yml:27`

### unpinned-uses (severity: high)

The workflow uses `actions/checkout@v3.1.0` which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks. It should be replaced with a full SHA pin, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3.1.0`.

Locations:

- `.github/workflows/sample.yml:12`

### missing-permissions (severity: medium)

The workflow file `sample.yml` has no top-level `permissions:` key, and neither the `matrix_prep` job nor the `build` job defines its own `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be `write-all`), granting broader access than necessary. A minimal `permissions:` block (e.g. `contents: read`) should be added at the top level or per job.

Locations:

- `.github/workflows/sample.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings in .github/workflows/sample.yml: (1) Pinned actions/checkout@v3.1.0 to its full commit SHA 93ea575cb5d8a053eaa0ac8fa3b40d7e05a33cc8 with a comment preserving the tag name. (2) Added a top-level `permissions: contents: read` block to restrict the default token permissions. (3) Moved `${{ matrix.runOn }}` out of the `run:` shell string into a step-level `env:` block as `RUN_ON`, then referenced it as `$RUN_ON` in the shell command to prevent script injection.

