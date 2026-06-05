# AI Agent Instructions

This repo contains org-wide GitHub Actions reusable workflows
and community health files. See `README.md` for project overview.

## Before starting work

- Read `README.md` and this file (`AGENTS.md`) in full.
- This file covers conventions and rules.
  `README.md` covers project context and usage docs.
- Do not duplicate information between the two files.
- When changes affect project docs, update `README.md`.
  When changes affect agent workflow rules, update `AGENTS.md`.
- Keep both files accurate and current.

## Repo structure

```
.github/
├── CODEOWNERS
├── dependabot.yml
├── labeler.yml
├── release.yml
└── workflows/
    ├── automerge.yml
    ├── ossf.yml
    ├── pr.yml
    └── scans.yml
```

## Workflow conventions

### Action pinning

Pin all actions to full SHA with version comment:

```yaml
- uses: actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd # v6
```

Never use tag-only refs (`@v6`). Dependabot updates SHAs automatically.

### Permissions

- Top-level `permissions: {}` (deny all).
- Grant minimum required permissions per job.

### Triggers

All workflows include `workflow_call` (reusable) and `workflow_dispatch` (manual).
Event triggers vary by workflow purpose:

- CI/scan workflows: push, pull_request, merge_group
- PR-specific workflows (pr, automerge): pull_request only

`workflow_call` makes them reusable by other repos.

### Security

Never interpolate untrusted input in `run:` blocks.
Use environment variables instead:

```yaml
# UNSAFE
run: echo "${{ github.event.pull_request.title }}"

# SAFE
run: echo "${TITLE}"
env:
  TITLE: ${{ github.event.pull_request.title }}
```

Untrusted inputs include: PR title/body, issue title/body,
commit messages, branch names, comment bodies.

### Concurrency

All workflows use:

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.ref_name != github.event.repository.default_branch }}
```

## Editing rules

- Do not break `workflow_call` interface — other repos depend on it.
- Do not remove SHA pins or replace with tag refs.
- Do not add application code to this repo.
- Keep YAML formatting consistent: 2-space indent, no trailing whitespace.
- Test changes via `workflow_dispatch` after pushing.

## Git conventions

- Branch naming: `<type>/<short-description>` (e.g., `ci/update-megalinter`)
- Conventional Commits: `<type>(<scope>): <description>`
- Types: feat, fix, ci, docs, chore, refactor
- Subject ≤ 50 chars, imperative mood, no trailing period.
