# .github

Organisation-wide default community health files and reusable GitHub Actions workflows.

## Community health files

| File | Purpose |
|------|---------|
| `CODEOWNERS` | Default code owners (`@yxtay`) |
| `labeler.yml` | PR labeling rules based on branch naming |
| `release.yml` | GitHub Release changelog categories |

> **Note:** GitHub does not inherit `CODEOWNERS`, `labeler.yml`, or `release.yml` from the org `.github` repo.
> These are stored here as canonical source of truth. Each repo keeps its own copy.

## Reusable workflows

Located in `.github/workflows/`. Repos call them like:

```yaml
# in <repo>/.github/workflows/automerge.yml
name: automerge
on:
  pull_request:
    branches: [main]
jobs:
  automerge:
    uses: yxtay/.github/.github/workflows/automerge.yml@main
```

| Workflow | Description |
|----------|-------------|
| `automerge.yml` | Auto-merge dependabot and pre-commit-ci PRs |
| `pr.yml` | Lint PR title (conventional commits), apply labels |
| `ossf.yml` | OpenSSF Scorecard security scan |
| `scans.yml` | MegaLinter (generic flavor — repos configure via `.mega-linter.yml`) |

### Caller workflow examples

Each repo needs thin caller files that trigger on the appropriate events:

```yaml
# automerge.yml
name: automerge
on:
  pull_request:
    branches: [main]
jobs:
  automerge:
    uses: yxtay/.github/.github/workflows/automerge.yml@main

# pr.yml
name: pr
on:
  pull_request:
    types: [opened, synchronize, reopened, edited]
    branches: [main]
jobs:
  pr:
    uses: yxtay/.github/.github/workflows/pr.yml@main

# ossf.yml
name: ossf
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  merge_group:
    branches: [main]
  workflow_call:
  workflow_dispatch:
jobs:
  ossf:
    uses: yxtay/.github/.github/workflows/ossf.yml@main

# scans.yml
name: scans
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  merge_group:
    branches: [main]
  workflow_call:
  workflow_dispatch:
jobs:
  scans:
    uses: yxtay/.github/.github/workflows/scans.yml@main
```

### MegaLinter flavor

The reusable `scans.yml` uses generic `oxsecurity/megalinter`. Repos needing a specific flavor
(python, terraform, documentation) should keep their own `scans.yml` or configure via `.mega-linter.yml`.
