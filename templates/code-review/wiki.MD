# Code Review

**Version:** 1.0.0
**Name:** Code Review

## Overview

AI-powered agent that reviews pull request code changes, generates structured feedback, and posts review comments on the PR.

---

## Key Capabilities

- **Diff-aware review**: Clones the pull request with full history and analyzes the change set
- **AI feedback generation**: Uses an LLM coding agent to produce actionable review comments
- **Automated PR comments**: Posts findings back to the pull request via the comment plugin

---

## How It Works

1. **Clone PR**: Fetches the pull request ref with depth 1000 so the full diff is available
2. **Generate review prompt**: Builds a task file and review output path for the coding agent
3. **Run coding agent**: Executes the AI review against the cloned workspace
4. **Post comments**: Publishes structured review comments to the target pull request

## Required Inputs

| Input | Type | Description | Default |
|-------|------|-------------|---------|
| `repo` | string | Repository identifier (org/repo) | -- |
| `pullReq` | string | Pull request number to review | -- |
| `llmConnector` | connector | LLM provider connector for AI review | -- |
| `gitConnector` | connector | Git repository connector | -- |
| `harnessKey` | secret | API key used to post PR review comments | `account.harness_api_key` |

## Usage Example

```yaml
inputs:
  repo: "my-org/my-repo"
  pullReq: "42"
  llmConnector: account.anthropic_connector
  gitConnector: account.github_connector
  harnessKey: account.harness_api_key
```

## Troubleshooting

### Review comments are not posted
Confirm `harnessKey` has permission to comment on the PR and that `pullReq` matches an open pull request in `repo`.

### Coding agent fails authentication
Verify `llmConnector` is configured and that `<+inputs.llmConnector.token>` resolves to a valid API token.

### Incomplete or empty review
Ensure the PR clone succeeds (`depth: 1000`) and that the review prompt generation step writes `/harness/review/task.txt` before the coding agent runs.

### Connector not found
Create the LLM and Git connectors in Harness before running this agent, then pass their identifiers as inputs.
