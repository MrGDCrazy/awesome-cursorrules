# Documentation Generator

**Version:** 1.0.0
**Name:** Documentation Generator

## Overview

AI-powered agent that analyzes a codebase, generates documentation artifacts, and opens a pull request with the results.

---

## Key Capabilities

- **Codebase analysis**: Reviews the repository to produce README, API docs, usage examples, and architecture diagrams
- **Change preview**: Stages generated files and shows the diff before PR creation
- **Multi-SCM PR creation**: Detects GitHub, GitLab, Bitbucket, or Harness Code and opens a documentation PR

---

## How It Works

1. **Clone branch**: Fetches the target branch with depth 1
2. **Generate documentation**: Runs the coding agent with a documentation prompt
3. **Show changes**: Stages generated files and prints the cached diff
4. **Detect SCM provider**: Resolves provider-specific auth and base URL outputs
5. **Create PR**: Pushes an `ai-docs` branch and opens a pull request

## Required Inputs

| Input | Type | Description | Default |
|-------|------|-------------|---------|
| `repo` | string | Repository identifier (org/repo) | -- |
| `branch` | string | Branch to analyze and document | `main` |
| `llmConnector` | connector | LLM connector for AI documentation generation | -- |
| `gitConnector` | connector | Git connector for repository access | -- |
| `harnessKey` | secret | API key used for Harness Code SCM operations | `account.harness_api_key` |

## Usage Example

```yaml
inputs:
  repo: "my-org/my-repo"
  branch: "main"
  llmConnector: account.anthropic_connector
  gitConnector: account.github_connector
  harnessKey: account.harness_api_key
```

## Troubleshooting

### PR creation fails
Confirm git changes exist (`git diff --cached` is non-empty) and that the SCM token has write access to create branches and PRs.

### SCM provider detection failing
Verify `DRONE_REPO_SCM` is available and that connector tokens have the correct permissions for your provider.

### Coding agent fails authentication
Verify `llmConnector` is configured and that `<+inputs.llmConnector.token>` resolves to a valid API token.

### Connector not found
Create the LLM and Git connectors in Harness before running this agent, then pass their identifiers as inputs.
