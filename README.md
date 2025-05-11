# deny-self-approve-action

[![License](http://img.shields.io/badge/license-mit-blue.svg?style=flat-square)](https://raw.githubusercontent.com/suzuki-shunsuke/deny-self-approve-action/main/LICENSE) | [action.yaml](action.yaml)

GitHub Action to deny self approval using [deny-self-approve](https://github.com/suzuki-shunsuke/deny-self-approve).
For details, please see [deny-self-approve](https://github.com/suzuki-shunsuke/deny-self-approve).

## :warning: Deprecated. Please use [validate-pr-review-action](https://github.com/suzuki-shunsuke/validate-pr-review-action) :warning:

validate-pr-review-action is a successor of deny-self-approve.

https://github.com/suzuki-shunsuke/validate-pr-review-action

---

## How To Use

There are two options:

1. Run this action via **pull_request_review** events
1. Run this action via **merge_group** events

### 1. Run this action via **pull_request_review** events

Please add the job `check-approval` to required checks.

```yaml
name: Check approval
on:
  pull_request_review:
    types:
      - submitted
jobs:
  check-approval:
    timeout-minutes: 10
    runs-on: ubuntu-24.04
    permissions:
      contents: read # To list commits in pull requests
    steps:
      - uses: suzuki-shunsuke/deny-self-approve-action@9ee0a3ee1ed44415a065b05291560487c7847db3 # v0.2.0
```

A pull request can't be merged until someone approves the pull request because the job is required.
When someone who pushed commits to the pull request approves the pull request, this workflow is triggered and the job fails.
The pull request can't be merged because the required job fails.
If someone who doesn't push any commit to the pull request approves the pull request, the workflow is triggered and the job passes.
Then the pull request can be merged.
That's how this action prevents self-approval.

### 2. Run this action via **merge_group** events

Please add the job `check-approval` to required checks.

```yaml
name: Check approval
on: merge_group
jobs:
  check-approval:
    timeout-minutes: 10
    runs-on: ubuntu-24.04
    permissions:
      pull-requests: write
      contents: read
    steps:
      - uses: suzuki-shunsuke/deny-self-approve-action@9ee0a3ee1ed44415a065b05291560487c7847db3 # v0.2.0
```

You need to run the same name job via `pull_request` event to add pull requests to Merge Queue.

```yaml
name: Test
on: pull_request
jobs:
  check-approval:
    timeout-minutes: 10
    runs-on: ubuntu-24.04
    permissions: {}
    if: "false" # The job is always skipped.
    steps:
      - run: ":"
```
