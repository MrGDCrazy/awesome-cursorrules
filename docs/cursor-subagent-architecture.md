# Cursor Subagent Architecture

## Repository Intelligence Report

### Architecture

This repository is a curated awesome list for Cursor Project Rules. The primary product is `README.md`, supported by reusable rule templates in `rules/*.mdc` and deterministic Node validators in `scripts/*.mjs`. GitHub Actions in `.github/workflows/main.yml` run the same checks used locally.

### Languages, frameworks, and package managers

- Repository tooling uses JavaScript ESM on Node.
- Content uses Markdown plus YAML frontmatter.
- Package management uses `pnpm`.
- There is no application framework, frontend, backend, database, or runtime service.

### Infrastructure and deployment targets

- Deployment is GitHub-hosted repository content.
- There is no Docker, Kubernetes, Terraform, database, API server, or browser app.
- Running the product means running validators and tests.

### Testing strategy

- `pnpm test` runs Node's built-in test runner over `scripts/*.test.mjs`.
- Local CI-equivalent checks are `pnpm check:awesome-list`, `pnpm check:repo-hygiene`, `pnpm check:readme-hygiene`, `pnpm check:rule-hygiene`, `pnpm check:issue-template-policy`, and `pnpm check:repo-security`.
- `pnpm check:awesome-list:upstream` uses network access to run `awesome-lint@2.3.0`.

### CI/CD

- `.github/workflows/main.yml` defines pull request, pull request target, and push checks.
- Fork-sensitive checks use trusted base checkout and read-only permissions.
- CI pins Node 20 and activates pnpm 10.20.0.

### Documentation and standards

- `README.md` is the public catalog.
- `AGENTS.md` documents Cursor Cloud workflow.
- `contributing.md` defines issue scope, author gates, and rule format.
- `.github/pull_request_template.md` documents contribution quality expectations.

### Dependency graph

```text
README.md <-> rules/*.mdc
package.json -> scripts/*.mjs -> scripts/*.test.mjs
scripts/check-*.mjs -> scripts/check-repo-hygiene.mjs
.github/workflows/main.yml -> package scripts and trusted-base validators
```

### AI/LLM integrations

- The repository does not call LLM APIs.
- The content catalog includes rules about AI, MCP, Vercel AI, LLM workflows, and agent systems for downstream projects.

### Browser automation

- No browser automation is part of this repository.
- Playwright and Cypress appear only as rule topics in `rules/*.mdc`.

### Security architecture

- Prompt-like content is treated as reviewable input.
- `check-repo-security.mjs` and `check-repo-hygiene.mjs` detect unsafe prompt patterns.
- GitHub Actions use read-only permissions and trusted-base scripts for fork PRs.
- PR author age policy is enforced by `scripts/check-pr-author.mjs`.

### Developer workflow

1. Add or update `rules/<name>.mdc`.
2. Keep frontmatter fields `description`, `globs`, and `alwaysApply`.
3. Add or update a canonical GitHub `blob/main/rules/...` README entry when appropriate.
4. Run tests and local checks.
5. Use CI failure output to fix content, validators, or docs.

### Technical debt and pain points

- Rule files outnumber README entries, so catalog drift is likely.
- Some filenames appear legacy-truncated.
- Several rules are near-duplicates across popular stacks.
- `contributing.md` references missing `create-list.md`.
- There is no single `check:all` script.
- Upstream awesome-lint needs network access.

## Subagent Architecture Diagram

```text
repository-architecture-steward
  -> rule-catalog-curator
  -> validator-ci-engineer
  -> documentation-maintainer

rule-catalog-curator
  -> prompt-security-auditor
  -> repository-test-runner

validator-ci-engineer
  -> prompt-security-auditor
  -> repository-test-runner

documentation-maintainer
  -> repository-test-runner

prompt-security-auditor
  -> repository-test-runner

repository-test-runner
  -> final-quality-reviewer

final-quality-reviewer
  -> none
```

## Ownership Matrix

| Concern | Owner | Backup |
| --- | --- | --- |
| Repository architecture and agent boundaries | `repository-architecture-steward` | None |
| README catalog taxonomy and canonical rule links | `rule-catalog-curator` | None |
| `rules/*.mdc` contribution quality | `rule-catalog-curator` | `prompt-security-auditor` for security-only concerns |
| Prompt safety and unsafe instruction patterns | `prompt-security-auditor` | None |
| Node validators and tests | `validator-ci-engineer` | None |
| GitHub Actions and PR policy checks | `validator-ci-engineer` | `prompt-security-auditor` for workflow security |
| Repository documentation outside catalog entries | `documentation-maintainer` | None |
| Test and validation command execution | `repository-test-runner` | None |
| Final readiness review | `final-quality-reviewer` | None |
| Browser automation | No owner; not applicable to this repo | None |
| Database, API, infrastructure, deployment services | No owner; not applicable to this repo | None |

## Delegation Matrix

| Starting work | Delegate to | Do not delegate to |
| --- | --- | --- |
| New or changed rule file | `rule-catalog-curator` | `validator-ci-engineer` unless validator behavior changes |
| README category, link, or description cleanup | `rule-catalog-curator` | `documentation-maintainer` unless non-catalog docs change |
| Security warning in rule or workflow content | `prompt-security-auditor` | `rule-catalog-curator` for final security judgment |
| Validator script, test, CI, or author gate change | `validator-ci-engineer` | `rule-catalog-curator` |
| AGENTS, contributing, issue templates, PR template text | `documentation-maintainer` | `rule-catalog-curator` unless catalog taxonomy changes |
| Running repo checks or interpreting failures | `repository-test-runner` | Content owners for command execution |
| Pre-merge confidence review | `final-quality-reviewer` | Any implementation agent |

## Collaboration Graph

```text
Architecture sets scope.
Content and tooling owners implement scoped changes in parallel when independent.
Security audits prompt-like and workflow-sensitive content.
Test runner validates the changed surfaces.
Documentation updates contributor-facing guidance.
Final review checks ownership, duplication, validation evidence, and release readiness.
```

## Dependency Graph

- `rule-catalog-curator` depends on repository taxonomy and validator policies.
- `prompt-security-auditor` depends on `scripts/check-repo-security.mjs` and workflow security tests.
- `validator-ci-engineer` depends on package scripts, Node built-ins, and CI workflow behavior.
- `repository-test-runner` depends on package scripts and CI-equivalent commands.
- `final-quality-reviewer` depends on output from all relevant specialists.

## Escalation Paths

- New product surface not represented in this document: escalate to `repository-architecture-steward`.
- Unsafe or ambiguous prompt content: escalate to `prompt-security-auditor`.
- Validator failure with unclear root cause: escalate to `validator-ci-engineer`.
- Missing evidence that checks ran: escalate to `repository-test-runner`.
- Conflicting agent ownership: escalate to `repository-architecture-steward`.

## Parallel Execution Opportunities

- Catalog cleanup and validator script changes can run in parallel when they touch separate files.
- Documentation cleanup can run in parallel with tests when docs are not part of validator logic.
- Security review can run in parallel with README taxonomy review.

## Background Execution Opportunities

- `repository-test-runner` is suitable for background execution because full validation can take longer than review tasks.
- Future read-only catalog drift scans could run in the background if split from the writable `rule-catalog-curator`.
- Writable agents should stay foreground because they make edits or judgment calls that shape implementation.

## Recommended Invocation Patterns

- Use `repository-architecture-steward` before broad changes that alter repo ownership, workflow, or agent boundaries.
- Use `rule-catalog-curator` for every new rule, rule update, README listing, duplicate review, or category decision.
- Use `prompt-security-auditor` for prompt safety, untrusted content, workflow security, and CI warnings involving unsafe instructions.
- Use `validator-ci-engineer` for scripts, tests, package scripts, and GitHub Actions.
- Use `repository-test-runner` before claiming repository changes are complete.
- Use `final-quality-reviewer` before opening or updating a PR.

## Automatic Delegation Recommendations

- Keep descriptions specific to this repository's artifact names: `README.md`, `rules/*.mdc`, `scripts/*.mjs`, and `.github/workflows/main.yml`.
- Avoid generic implementation, backend, frontend, database, browser, infrastructure, and API agents.
- Route by changed path first, then by failure type.
- Prefer read-only specialists for review, testing, and security judgment.

## Future Expansion Opportunities

- Add a catalog analytics agent only if maintainers add metrics for duplicate detection or category coverage.
- Add a release-notes agent only if the repository begins publishing tagged releases.
- Add a GitHub triage agent only if issue volume grows enough to justify automation.
- Add a dependency management agent only if third-party packages are introduced.
