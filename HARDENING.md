<!-- markdownlint-disable -->

# Hardening Report: JoshuaTheMiller--conditional-build-matrix/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JoshuaTheMiller--conditional-build-matrix/v2.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `actions/checkout@v3.1.0`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved. It should be replaced with a full SHA pin, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3.1.0`.

Locations:

- `.github/workflows/sample.yml:12`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is directly interpolated inside a `run:` shell command. The step `run: echo "${{ matrix.runOn }}"` embeds the `matrix.runOn` value directly into the shell command string before the shell ever sees it. An attacker who controls the matrix value (e.g. via a workflow_dispatch input or a forked PR that modifies the matrix JSON) could inject arbitrary shell commands. The fix is to pass the value via an `env:` variable and reference it as a quoted shell variable: `env: { RUN_ON: "${{ matrix.runOn }}" }` then `run: echo "$RUN_ON"`.

Locations:

- `.github/workflows/sample.yml:27`

### missing-permissions (severity: medium)

The workflow file `sample.yml` has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs (`matrix_prep`, `build`). Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write access to contents, pull-requests, etc.). A minimal `permissions: {}` or specific scopes (e.g. `contents: read`) should be declared at the top level or on each job.

Locations:

- `.github/workflows/sample.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings in hardened/action/.github/workflows/sample.yml: (1) Pinned actions/checkout@v3.1.0 to full SHA 93ea575cb5d8a053eaa0ac8fa3b40d7e05a33cc8 with tag preserved as comment. (2) Added top-level `permissions: {}` to deny all permissions by default. (3) Moved `${{ matrix.runOn }}` from the `run:` shell string into an `env:` block as RUN_ON, referencing it as `"$RUN_ON"` in the shell command to prevent script injection.

