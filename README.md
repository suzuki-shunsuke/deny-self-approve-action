# deny-self-approve-action

[![License](http://img.shields.io/badge/license-mit-blue.svg?style=flat-square)](https://raw.githubusercontent.com/suzuki-shunsuke/deny-self-approve-action/main/LICENSE) | [action.yaml](action.yaml)

GitHub Action to deny self approval using [deny-self-approve](https://github.com/suzuki-shunsuke/deny-self-approve).

This action prevents pull requests' self-approvals by dimissing approvals from people who pushed commits to pull requests.
This action fails if no one who didn't push commits to the pull request approves the pull request.
This action uses [deny-self-approve](https://github.com/suzuki-shunsuke/deny-self-approve).

## How To Use

1. Run this action via pull_request_review events
2. Add the job to required checks

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
      pull-requests: write
    steps:
      - uses: suzuki-shunsuke/deny-self-approve-action@main
```

### How does this action prevent self-approval?

A pull request can't be merged until someone approves the pull request because the job is required.
When someone who pushed commits to the pull request approves the pull request, this workflow is triggered and self-approval is dismissed and the job fails.
The pull request can't be merged because the required job fails and the self-approval is dismissed.
If someone who doesn't push any commit to the pull request approves the pull request, the workflow is triggered and the job passes. Then the pull request can be merged.
That's how this action prevents self-approval.
