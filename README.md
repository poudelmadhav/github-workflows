# GitHub Workflows

Reusable workflows for release management and branch syncing.

> **Private repos:** Go to this repo's Settings → Actions → General → Access and grant access to your org/repos, or use `secrets: inherit` with a PAT.

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
    uses: poudelmadhav/github-workflows/.github/workflows/create-release.yml@v3
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

Creates a PR from a source branch into a target branch and attempts auto-merge. Optionally auto-approves the PR using a PAT (useful when branch protection requires approvals).

**Caller example:**

```yaml
name: Sync Main to Develop

on:
  push:
    branches:
      - main

jobs:
  sync:
    uses: poudelmadhav/github-workflows/.github/workflows/sync-main-to-develop.yml@v3
    with:
      source_branch: main
      target_branch: develop
    secrets:
      approval_token: ${{ secrets.PAT_TOKEN }}
    permissions:
      contents: write
      pull-requests: write
```

**Inputs:**

| Input           | Required | Default   | Description            |
| --------------- | -------- | --------- | ---------------------- |
| `source_branch` | no       | `main`    | Source branch name     |
| `target_branch` | no       | `develop` | Target branch name     |

**Secrets:**

| Secret           | Required | Description                                                                 |
| ---------------- | -------- | --------------------------------------------------------------------------- |
| `approval_token` | no       | [PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) to auto-approve the PR. Falls back to `GITHUB_TOKEN` for auto-merge. |
