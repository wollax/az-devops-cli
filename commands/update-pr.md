---
allowed-tools:
  - Bash(az repos:*)
  - Bash(git:*)
---

# Update Azure DevOps Pull Request

Update an existing pull request for the current branch.

## Gather Context

1. `git branch --show-current` — get the current branch name
2. `az repos pr list --source-branch <current-branch> --status active -o json` — find the active PR for this branch
3. If a PR is found, extract its ID and run `az repos pr show --id <id> -o json` to get current details

## Behavior

- **Auto-detect PR ID** from the current branch's active PR. If no active PR is found, inform the user and stop.
- If the user passes a PR ID directly as an argument, use that instead of auto-detection.
- Show the user the current PR state (title, description, status, reviewers) before making changes.
- Apply only the updates the user specifies.

## Supported Updates

The user specifies what to change via command arguments:

- `--title "<new title>"` — update the PR title
- `--description "<new description>"` — update the PR description
- `--draft true|false` — toggle draft status
- `--reviewers <email1,email2>` — set required reviewers (replaces existing)
- `--add-reviewers <email1,email2>` — add reviewers without removing existing ones
- `--work-items <id1,id2>` — link additional work items
- `--target <branch>` — change target branch
- `--auto-complete true|false` — toggle auto-complete
- `<pr-id>` — explicit PR ID (positional argument)

## Update the PR

```
az repos pr update \
  --id <pr-id> \
  [--title "<title>"] \
  [--description "<description>"] \
  [--draft true|false] \
  [--auto-complete true|false] \
  [--target-branch <branch>] \
  -o table
```

For reviewer updates, use:

```
az repos pr reviewer add --id <pr-id> --reviewers <emails>
```

After updating, display the updated PR details.
