# .github

Organisation-wide default community health files, reusable GitHub Actions workflows,
and shared config templates.

## Community health files

| File                     | Purpose                                          |
|--------------------------|--------------------------------------------------|
| `.github/CODEOWNERS`     | Default code owners (`@yxtay`)                   |
| `.github/dependabot.yml` | Dependabot config (docker, compose, actions, uv) |
| `.github/labeler.yml`    | PR labeling rules based on branch naming         |
| `.github/release.yml`    | GitHub Release changelog categories              |

> **Note:** GitHub does not inherit `CODEOWNERS` or `labeler.yml`
> from the org `.github` repo.
> These are stored here as canonical source of truth.
> Each repo keeps its own copy.

## Shared config templates

Root-level config files to copy into new repos.
These are the canonical superset — repos may trim
entries that don't apply (e.g., remove `uv` from dependabot
if not a Python project).

| File                      | Purpose                                    |
|---------------------------|--------------------------------------------|
| `.editorconfig`           | Editor formatting (indent, EOL, charset)   |
| `.markdownlint.json`      | Markdown lint rules (120 char line width)  |
| `.mega-linter.yml`        | MegaLinter config (disabled linters, args) |
| `.pre-commit-config.yaml` | Pre-commit hooks (superset of all repos)   |
| `.yamlfmt`                | YAML formatter config                      |
| `renovate.json`           | Renovate bot config (automerge, security)  |

## Reusable workflows

Located in `.github/workflows/`.
All workflows run in this repo directly
(push, PR, merge_group, workflow_dispatch)
and are also callable from other repos via `workflow_call`.

| Workflow        | Description                                        |
|-----------------|----------------------------------------------------|
| `automerge.yml` | Auto-merge dependabot and pre-commit-ci PRs        |
| `pr.yml`        | Lint PR title (conventional commits), apply labels |
| `ossf.yml`      | OpenSSF Scorecard security scan                    |
| `scans.yml`     | MegaLinter scans (configurable flavors via input)  |

### Calling from other repos

Each repo needs thin caller files:

```yaml
# in <repo>/.github/workflows/automerge.yml
name: automerge
on:
  pull_request:
    branches: [main]
jobs:
  automerge:
    uses: yxtay/.github/.github/workflows/automerge.yml@main

# in <repo>/.github/workflows/pr.yml
name: pr
on:
  pull_request:
    types: [opened, synchronize, reopened, edited]
    branches: [main]
jobs:
  pr:
    uses: yxtay/.github/.github/workflows/pr.yml@main

# in <repo>/.github/workflows/ossf.yml
name: ossf
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  merge_group:
    branches: [main]
jobs:
  ossf:
    uses: yxtay/.github/.github/workflows/ossf.yml@main

# in <repo>/.github/workflows/scans.yml
name: scans
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  merge_group:
    branches: [main]
jobs:
  scans:
    uses: yxtay/.github/.github/workflows/scans.yml@main
```

### MegaLinter flavors

The reusable `scans.yml` runs megalinter via a matrix job.
Default flavors: `security`, `formatters`.
Callers can override via input:

```yaml
jobs:
  scans:
    uses: yxtay/.github/.github/workflows/scans.yml@main
    with:
      flavors: '["security", "python", "go"]'
```

Supported flavors: `all`, `c_cpp`, `ci_light`, `cupcake`,
`documentation`, `dotnet`, `dotnetweb`, `formatters`, `go`,
`java`, `javascript`, `php`, `python`, `ruby`, `rust`,
`salesforce`, `security`, `swift`, `terraform`.
