# AGENTS.md

## Cursor Cloud specific instructions

This repository is a curated "awesome list" of Cursor rules, not a runnable service. The
"product" is the `README.md` awesome list plus the `rules/*.mdc` Cursor Project Rule files.
The `scripts/*.mjs` validators (run via Node) are the tooling that enforces content quality.
There is no dev server, database, or GUI — "running the application" means running the
validation scripts and the test suite.

### Services / commands

All commands and their scripts are defined in `package.json`. Standard workflows:

- Tests: `pnpm test` (Node's built-in test runner over `scripts/*.test.mjs`).
- Validation checks: `pnpm check:awesome-list`, `check:repo-hygiene`, `check:readme-hygiene`,
  `check:rule-hygiene`, `check:issue-template-policy`, `check:repo-security`. These mirror the
  `.github/workflows/main.yml` CI gates.

### Non-obvious notes

- No third-party dependencies: every script imports only `node:*` builtins, and
  `pnpm-lock.yaml` has no packages. `pnpm install` is effectively a no-op but is safe/idempotent.
- `pnpm check:awesome-list:upstream` runs `pnpm dlx awesome-lint@2.3.0`, which needs network
  access to download the package. It will fail in an offline sandbox — this is expected and is
  separate from the local `check:awesome-list` script.
- CI pins Node 20; newer Node (e.g. 22) also runs the scripts/tests fine locally.
- Contributor workflow to validate end to end: add a `rules/<name>.mdc` file (with `description`,
  `globs`, `alwaysApply` frontmatter) and a canonical GitHub `blob` URL entry in the matching
  `README.md` category, then run the `check:*` scripts. README rule links must use canonical
  `https://github.com/.../blob/main/rules/...` URLs (not `./rules/...`).
