# .github

Organisation-wide default community health files and reusable GitHub Actions workflows.

## Community health files

| File | Purpose |
|------|---------|
| `.github/CODEOWNERS` | Default code owners (`@yxtay`) |
| `.github/dependabot.yml` | Dependabot config for GitHub Actions updates |
| `.github/labeler.yml` | PR labeling rules based on branch naming |
| `.github/release.yml` | GitHub Release changelog categories |

> **Note:** GitHub does not inherit `CODEOWNERS` or `labeler.yml` from the org `.github` repo.
> These are stored here as canonical source of truth. Each repo keeps its own copy.

## Reusable workflows

Located in `.github/workflows/`. All workflows run in this repo directly (push, PR, merge_group, workflow_dispatch) and are also callable from other repos via `workflow_call`.

| Workflow | Description |
|----------|-------------|
| `automerge.yml` | Auto-merge dependabot and pre-commit-ci PRs |
| `pr.yml` | Lint PR title (conventional commits), apply labels, size label |
| `ossf.yml` | OpenSSF Scorecard security scan |
| `scans.yml` | MegaLinter security and formatters scans |

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

The reusable `scans.yml` uses `oxsecurity/megalinter/flavors/security` and `oxsecurity/megalinter/flavors/formatters`.
Repos needing a different flavor should keep their own `scans.yml` or configure via `.mega-linter.yml`.
