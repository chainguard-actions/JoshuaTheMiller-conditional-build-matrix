<!-- markdownlint-disable -->

# Hardening Report: JoshuaTheMiller--conditional-build-matrix/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JoshuaTheMiller--conditional-build-matrix/v1.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses actions/checkout@v3.1.0, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. This allows the referenced action to be silently changed by the upstream maintainer or an attacker who compromises the repository, enabling a supply-chain attack. Fix: pin to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3.1.0`.

Locations:

- `.github/workflows/sample.yml:12`

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command. On line 26, `run: echo "${{ matrix.runOn }}"` embeds the matrix context value directly into the shell command string before the shell ever sees it. An attacker who controls the matrix value (e.g. via a workflow_dispatch input or a crafted matrix entry) can inject arbitrary shell commands. Fix: move the value into an env: variable and reference it as a quoted shell variable, e.g. `env: { RUN_ON: "${{ matrix.runOn }}" }` then `run: echo "$RUN_ON"`.

Locations:

- `.github/workflows/sample.yml:26`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key, and neither of its jobs (matrix_prep, build) defines a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, packages, etc.). Fix: add a top-level `permissions: {}` block with only the minimal scopes required.

Locations:

- `.github/workflows/sample.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings in .github/workflows/sample.yml: (1) Pinned actions/checkout@v3.1.0 to its full commit SHA 93ea575cb5d8a053eaa0ac8fa3b40d7e05a33cc8 with the tag preserved as a comment. (2) Moved the ${{ matrix.runOn }} expression out of the run: shell string and into an env: block as RUN_ON, referencing it as the safe shell variable $RUN_ON to prevent script injection. (3) Added a top-level `permissions: {}` block to enforce least-privilege — no permissions granted by default.

