# Code Review

**Version:** 1.0.0
**Name:** Code Review

## Overview

AI-powered agent that reviews pull request code changes, generates structured feedback, and posts review comments on the PR.

---

## Key Capabilities

- **Diff-aware review**: Clones the pull request with deep history (`depth: 1000`) for additional review context
- **AI feedback generation**: Uses an LLM coding agent to produce actionable review comments
- **Automated PR comments**: Posts findings back to the pull request via the comment plugin

---

## How It Works

1. **Clone PR**: Fetches the pull request ref with `depth: 1000` for additional commit context around the change
2. **Generate review prompt**: Builds a task file using the target `repo` and `pullReq`, plus a review output path for the coding agent
3. **Run coding agent**: Executes the AI review against the cloned workspace
4. **Post comments**: Publishes structured review comments to the target pull request

## Required Inputs

| Input | Type | Description | Default |
|-------|------|-------------|---------|
| `repo` | string | Repository identifier (org/repo) | -- |
| `pullReq` | string | Pull request number to review | -- |
| `llmConnector` | connector | Anthropic-compatible LLM connector (token mapped to `ANTHROPIC_API_KEY`) | -- |
| `gitConnector` | connector | Git repository connector | -- |
| `harnessKey` | secret | API key used to post PR review comments | `account.harness_api_key` |
| `arch` | string | Pipeline platform architecture (`amd64` or `arm64`) | `amd64` |
| `reviewImage` | string | Review prompt generation image (replace with your registry) | `myregistry/ai-review:1.0.0` |
| `codingAgentImage` | string | Coding agent image (replace with your registry) | `myregistry/coding-agent:1.0.0` |
| `commentImage` | string | PR comment plugin image (replace with your registry) | `myregistry/comment-plugin:1.0.0` |

## Usage Example

```yaml
inputs:
  repo: "my-org/my-repo"
  pullReq: "42"
  llmConnector: account.anthropic_connector
  gitConnector: account.github_connector
  harnessKey: account.harness_api_key
  reviewImage: "ghcr.io/my-org/ai-review:1.0.0"
  codingAgentImage: "ghcr.io/my-org/coding-agent:1.0.0"
  commentImage: "ghcr.io/my-org/comment-plugin:1.0.0"
```

## Security Notes

PR diffs, commit messages, and file contents are untrusted input. The `reviewImage` implementation must emit a **structured** task file (`task.json`) with separate `instructions`, `diff`, and `context` fields — never a freeform prompt that concatenates raw PR text into agent commands. The coding agent must treat the `diff`/`context` sections as data only.

## Troubleshooting

### Review comments are not posted
Confirm `harnessKey` has permission to comment on the PR and that `pullReq` matches an open pull request in `repo`.

### Coding agent fails authentication
Verify `llmConnector` is configured and that `<+inputs.llmConnector.token>` resolves to a valid Anthropic-compatible API token.

### Incomplete or empty review
Ensure the PR clone succeeds (`depth: 1000`) and that the review prompt generation step writes `/harness/review/task.json` before the coding agent runs.

### Image pull failures
Replace the default `myregistry/...:1.0.0` image inputs with pinned images from a registry your build infrastructure can pull.

### Connector not found
Create the LLM and Git connectors in Harness before running this agent, then pass their identifiers as inputs.
