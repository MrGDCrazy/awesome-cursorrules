# Security Scanner

**Version:** 1.0.0
**Name:** Security Scanner

## Overview

AI-powered agent that scans a repository for security vulnerabilities, generates a markdown findings report, and optionally posts results as a pull request comment.

---

## Key Capabilities

- **Full-repo vulnerability scan**: Analyzes the cloned branch for security issues above a configurable severity threshold
- **Markdown security report**: Converts findings into `SECURITY_REPORT.md` for review
- **Optional PR comments**: Posts the report to a pull request when `pullReq` is provided

---

## How It Works

1. **Clone branch**: Fetches the target branch with depth 1
2. **Scan for vulnerabilities**: Runs the security scanner and writes findings to JSON
3. **Generate report**: Converts findings into a markdown security report
4. **Post findings** (optional): Comments the report on the pull request when `pullReq` is set

## Required Inputs

| Input | Type | Description | Default |
|-------|------|-------------|---------|
| `repo` | string | Repository identifier (org/repo) | -- |
| `branch` | string | Branch to scan | `main` |
| `pullReq` | string | Pull request number (optional, for PR comments) | `""` |
| `llmConnector` | connector | LLM provider connector for AI security analysis | -- |
| `gitConnector` | connector | Git repository connector | -- |
| `harnessKey` | secret | API key used to post PR findings comments | `account.harness_api_key` |

## Usage Example

```yaml
inputs:
  repo: "my-org/my-repo"
  branch: "main"
  pullReq: "42"
  llmConnector: account.anthropic_connector
  gitConnector: account.github_connector
  harnessKey: account.harness_api_key
```

## Troubleshooting

### No findings report produced
Confirm the vulnerability scanner wrote `/harness/security/findings.json` and that the report generator can read that path.

### PR comment step skipped
The `post_findings` step only runs when `pullReq` is non-empty. Pass a valid PR number to enable commenting.

### Scanner authentication fails
Verify `llmConnector` is configured and that `<+inputs.llmConnector.token>` resolves to a valid API token.

### Connector not found
Create the LLM and Git connectors in Harness before running this agent, then pass their identifiers as inputs.
