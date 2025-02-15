# deny-self-approve-action

[![License](http://img.shields.io/badge/license-mit-blue.svg?style=flat-square)](https://raw.githubusercontent.com/suzuki-shunsuke/deny-self-approve-action/main/LICENSE) | [action.yaml](action.yaml)

GitHub Action to deny self approval using [deny-self-approve](https://github.com/suzuki-shunsuke/deny-self-approve).

This action prevents pull requests' self-approvals by dimissing approvals from people who pushed commits to pull requests.
This action fails if no one who didn't push commits to the pull request approves the pull request.
This action uses [deny-self-approve](https://github.com/suzuki-shunsuke/deny-self-approve).

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
      pull-requests: write # To dismiss self-approvals
      contents: read # To list commits in pull requests
    steps:
      - uses: suzuki-shunsuke/deny-self-approve-action@85a0ec0a189b083a84a95d37766f6f6df5aea1ba # v0.1.0
```

A pull request can't be merged until someone approves the pull request because the job is required.
When someone who pushed commits to the pull request approves the pull request, this workflow is triggered and self-approval is dismissed and the job fails.
The pull request can't be merged because the required job fails and the self-approval is dismissed.
If someone who doesn't push any commit to the pull request approves the pull request, the workflow is triggered and the job passes. Then the pull request can be merged.
That's how this action prevents self-approval.

### 2. Run this action via **merge_group** events

Please add the job `check-approval` to required checks.

> [!WARNING]
> Unfortunately, seems like we need to add the job `check-approval` to required checks.
> We thought pull requests weren't merged by dismissing self-approvals.
> But actually pull requests were merged even if self-approvals were dismissed.
> Maybe this is a bug of GitHub.

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
      - uses: suzuki-shunsuke/deny-self-approve-action@85a0ec0a189b083a84a95d37766f6f6df5aea1ba # v0.1.0
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
