# az-devops-cli

Claude Code plugin for managing Azure DevOps pull requests and CI pipelines via the `az` CLI.

## Prerequisites

- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) with the `azure-devops` extension
- `AZURE_DEVOPS_EXT_PAT` environment variable set for authentication
- Repos cloned under `~/Git/NDIT/`

## Installation

```bash
claude plugin add ~/Git/personal/az-devops-cli
```

## Commands

| Command | Description |
|---|---|
| `/az-devops-cli:create-pr` | Create a pull request for the current branch |
| `/az-devops-cli:update-pr` | Update an existing PR (title, description, reviewers, etc.) |
| `/az-devops-cli:complete-pr` | Complete/merge a PR (squash + auto-complete by default) |
| `/az-devops-cli:check-ci` | Check CI pipeline run status for the current branch |

### `/az-devops-cli:create-pr`

Creates a PR from the current branch to the default target branch (`main`). Pushes the branch if needed, generates title and description from commits, and sets `--delete-source-branch` by default.

Optional args: `--reviewers`, `--work-items`, `--draft`, `--labels`, `--target`

### `/az-devops-cli:update-pr`

Updates an existing PR auto-detected from the current branch. Supports changing title, description, draft status, reviewers, work items, and target branch.

### `/az-devops-cli:complete-pr`

Sets auto-complete with squash merge by default. Use `--now` to complete immediately, `--merge` for merge commit instead of squash.

### `/az-devops-cli:check-ci`

Checks CI pipeline run status for the current branch. Auto-detects the pipeline from the repository, or accepts `--pipeline <name-or-id>`.

Optional args:
- `--list` — show last 5 runs instead of just the most recent
- `--logs` — include failed stage/job names and log output
- `--full` — complete timeline, artifacts, triggered-by info
- `--pipeline <name-or-id>` — specify pipeline (auto-detected if omitted)
- `--branch <branch>` — override branch (defaults to current branch)
