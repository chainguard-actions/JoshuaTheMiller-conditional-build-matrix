<!-- markdownlint-disable -->

# Hardening Report: JoshuaTheMiller--conditional-build-matrix/v1.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JoshuaTheMiller--conditional-build-matrix/v1.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses actions/checkout@v3.1.0, which is pinned to a mutable tag rather than a full 40-character commit SHA. This means the action could be silently updated or replaced, enabling supply-chain attacks.

Locations:

- `.github/workflows/sample.yml:12`

### permissions (severity: medium)

missing-permissions: The workflow file has no top-level `permissions:` key, and neither job (`matrix_prep` nor `build`) defines its own `permissions:` block. This means the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/sample.yml:1`

### script-injection (severity: high)

Rule (a) violation: A GitHub Actions expression is interpolated directly inside a `run:` shell command string. On line 25, `run: echo "${{ matrix.runOn }}"` injects the `matrix.runOn` value — which is workflow-controllable — directly into the shell command before the shell ever sees it. An attacker who controls the matrix value could inject arbitrary shell commands.

Locations:

- `.github/workflows/sample.yml:25`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, script-injection

**Notes:**

Fixed all three findings in .github/workflows/sample.yml: (1) Pinned actions/checkout@v3.1.0 to its full commit SHA 93ea575cb5d8a053eaa0ac8fa3b40d7e05a33cc8 with the tag preserved as a comment. (2) Added top-level `permissions: {}` block to prevent the workflow from running with broad default GITHUB_TOKEN permissions. (3) Moved `${{ matrix.runOn }}` from the `run:` shell string into an `env:` block as `RUN_ON`, referencing it as `$RUN_ON` in the shell command to prevent script injection.

