# GitHub Workflows

Reusable workflows for release management and branch syncing.

## Workflows

### 1. Create Release

Creates a GitHub release from `release/v*` or `hotfix/v*` branches after they are merged into `main`.

**Caller example:**

```yaml
name: Create Release on Merge

on:
  pull_request_target:
    types: [closed]
    branches:
      - main

jobs:
  create-release:
    if: github.event.pull_request.merged == true && (startsWith(github.event.pull_request.head.ref, 'release/') || startsWith(github.event.pull_request.head.ref, 'hotfix/'))
    uses: poudelmadhav/github-workflows/.github/workflows/create-release.yml@v2
    with:
      branch: ${{ github.event.pull_request.head.ref }}
    permissions:
      contents: write
```

**Inputs:**

| Input    | Required | Description                                        |
| -------- | -------- | -------------------------------------------------- |
| `branch` | yes      | The merged branch name (e.g. `release/v1.1.0`)    |

### 2. Sync Main to Develop

Creates a PR from a source branch (default: `main`) into a target branch (default: `develop`) and attempts auto-merge.

**Caller example:**

```yaml
name: Sync Main to Develop

on:
  push:
    branches:
      - main

jobs:
  sync:
    uses: poudelmadhav/github-workflows/.github/workflows/sync-main-to-develop.yml@v2
    with:
      source_branch: main
      target_branch: develop
    permissions:
      contents: write
      pull-requests: write
```

**Inputs:**

| Input           | Required | Default   | Description            |
| --------------- | -------- | --------- | ---------------------- |
| `source_branch` | no       | `main`    | Source branch name     |
| `target_branch` | no       | `develop` | Target branch name     |
