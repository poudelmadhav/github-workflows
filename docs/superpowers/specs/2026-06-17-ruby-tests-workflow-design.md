# Ruby Tests Reusable Workflow

## Overview

A reusable GitHub Actions workflow for running RuboCop and RSpec on Ruby projects. Callers can enable/disable each tool via boolean inputs.

## File

`.github/workflows/ruby-tests.yml`

## Trigger

`workflow_call`

## Inputs

| Input          | Required | Default | Description                                                    |
| -------------- | -------- | ------- | -------------------------------------------------------------- |
| `ruby_version` | no       | —       | Ruby version (e.g. `3.2.6`, `3.4`). Falls back to `.ruby-version` file contents, then to `3.4`. |
| `rubocop`      | no       | `true`  | Run RuboCop (`bundle exec rubocop`)                            |
| `rspec`        | no       | `true`  | Run RSpec (`bundle exec rspec`)                                |

## Jobs

### `test`

Runs on `ubuntu-latest`.

**Steps:**

1. **Checkout** — `actions/checkout@v6`
2. **Resolve Ruby version** — If `ruby_version` input is empty, check for `.ruby-version` file. Read it if found. Otherwise default to `3.4`.
3. **Setup Ruby** — `ruby/setup-ruby@v1` with `ruby-version: <resolved>` and `bundler-cache: true`
4. **Run RuboCop** — `bundle exec rubocop` (skipped if `rubocop: false`)
5. **Run RSpec** — `bundle exec rspec` (skipped if `rspec: false`)

## Caller Example

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    uses: poudelmadhav/github-workflows/.github/workflows/ruby-tests.yml@v3
    with:
      ruby_version: 3.3
      rubocop: false
```

Or with defaults (latest Ruby, both tools enabled):

```yaml
jobs:
  test:
    uses: poudelmadhav/github-workflows/.github/workflows/ruby-tests.yml@v3
```

Or with `.ruby-version` auto-detection and RuboCop disabled:

```yaml
jobs:
  test:
    uses: poudelmadhav/github-workflows/.github/workflows/ruby-tests.yml@v3
    with:
      rubocop: false
```
