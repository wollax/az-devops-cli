---
allowed-tools: Bash(az repos:*), Bash(git:*)
description: Update an Azure DevOps pull request
---

## Context

- Current branch: !`git branch --show-current`
- Active PR for branch: !`az repos pr list --source-branch $(git branch --show-current) --status active -o json 2>/dev/null || echo "[]"`

## Your task

Update an existing Azure DevOps pull request. Follow these rules:

1. **Auto-detect PR ID** from the active PR for the current branch (from context above). If the user provided a PR ID as an argument, use that instead.
2. If no active PR is found and no ID was given, inform the user and stop.
3. Fetch current PR details: `az repos pr show --id <id> -o json`
4. Show the user the current PR state (title, description, status, reviewers) before making changes.
5. Apply only the updates the user specifies.

Supported updates from user arguments:
- `--title "<new title>"` — update the PR title
- `--description "<new description>"` — update the PR description
- `--draft true|false` — toggle draft status
- `--reviewers <email1,email2>` — set required reviewers (use `az repos pr reviewer add`)
- `--work-items <id1,id2>` — link additional work items
- `--target <branch>` — change target branch
- `--auto-complete true|false` — toggle auto-complete
- `<pr-id>` — explicit PR ID (positional argument)

Update the PR via `az repos pr update --id <pr-id> [options] -o table`.
For reviewer changes, use `az repos pr reviewer add --id <pr-id> --reviewers <emails>`.

After updating, display the updated PR details.
