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
| `llmConnector` | connector | Anthropic-compatible LLM connector (token mapped to `ANTHROPIC_API_KEY`) | -- |
| `gitConnector` | connector | Git repository connector | -- |
| `harnessKey` | secret | API key used to post PR findings comments | `account.harness_api_key` |
| `arch` | string | Pipeline platform architecture (`amd64` or `arm64`) | `amd64` |
| `scannerImage` | string | Vulnerability scanner image (replace with your registry) | `myregistry/security-scanner:1.0.0` |
| `reportImage` | string | Report generator image (replace with your registry) | `myregistry/report-generator:1.0.0` |
| `commentImage` | string | PR comment plugin image (replace with your registry) | `myregistry/pr-comment:1.0.0` |

## Usage Example

```yaml
inputs:
  repo: "my-org/my-repo"
  branch: "main"
  pullReq: "42"
  llmConnector: account.anthropic_connector
  gitConnector: account.github_connector
  harnessKey: account.harness_api_key
  scannerImage: "ghcr.io/my-org/security-scanner:1.0.0"
  reportImage: "ghcr.io/my-org/report-generator:1.0.0"
  commentImage: "ghcr.io/my-org/pr-comment:1.0.0"
```

## Troubleshooting

### No findings report produced
Confirm the vulnerability scanner wrote `/harness/security/findings.json` and that the report generator can read that path.

### PR comment step skipped
The `post_findings` step only runs when `pullReq` is present and non-empty. Pass a valid PR number to enable commenting.

### Scanner authentication fails
Verify `llmConnector` is configured and that `<+inputs.llmConnector.token>` resolves to a valid Anthropic-compatible API token.

### Image pull failures
Replace the default `myregistry/...:1.0.0` image inputs with pinned images from a registry your build infrastructure can pull.

### Connector not found
Create the LLM and Git connectors in Harness before running this agent, then pass their identifiers as inputs.
