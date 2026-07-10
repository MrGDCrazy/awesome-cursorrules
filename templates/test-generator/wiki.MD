# Test Generator

**Version:** 1.0.0
**Name:** Test Generator

## Overview

AI-powered agent that analyzes a codebase, generates unit tests to improve coverage, and opens a pull request with the new tests.

---

## Key Capabilities

- **Coverage-focused generation**: Targets untested or under-tested modules and public APIs
- **Convention-aware tests**: Follows existing project test frameworks and patterns
- **Multi-SCM PR creation**: Detects GitHub, GitLab, Bitbucket, or Harness Code and opens a tests PR

---

## How It Works

1. **Clone branch**: Fetches the target branch with depth 1
2. **Generate tests**: Runs the coding agent with a unit-test generation prompt
3. **Show changes**: Stages generated files and prints the cached diff
4. **Detect SCM provider**: Resolves provider-specific auth and base URL outputs
5. **Create PR**: Pushes an `ai-tests` branch and opens a pull request

## Required Inputs

| Input | Type | Description | Default |
|-------|------|-------------|---------|
| `repo` | string | Repository identifier (org/repo) | -- |
| `branch` | string | Branch to analyze for missing tests | `main` |
| `llmConnector` | connector | LLM connector for AI test generation | -- |
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

### No tests generated
Ensure the coding agent can identify existing test conventions and that the repository contains source modules worth covering.

### SCM provider detection failing
Verify `DRONE_REPO_SCM` is available and that connector tokens have the correct permissions for your provider.

### Coding agent fails authentication
Verify `llmConnector` is configured and that `<+inputs.llmConnector.token>` resolves to a valid API token.
