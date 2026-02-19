---
allowed-tools: Bash(az repos:*), Bash(git:*)
description: Update an Azure DevOps pull request
---

## Context

- Current branch: !`git branch --show-current`

## Your task

Update an existing Azure DevOps pull request.

### Step 1: Find the PR

If the user provided a PR ID as an argument, use that. Otherwise, auto-detect by running:

```
az repos pr list --source-branch <current-branch> --status active -o json
```

If no active PR is found and no ID was given, inform the user and stop.

### Step 2: Show current state

Fetch and display current PR details:

```
az repos pr show --id <id> -o json
```

Show the user the title, description, status, and reviewers before making changes.

### Step 3: Apply updates

Apply only the updates the user specifies via their arguments:

- `--title "<new title>"` — update the PR title
- `--description "<new description>"` — update the PR description
- `--draft true|false` — toggle draft status
- `--reviewers <email1,email2>` — set required reviewers (use `az repos pr reviewer add`)
- `--work-items <id1,id2>` — link additional work items
- `--target <branch>` — change target branch
- `--auto-complete true|false` — toggle auto-complete
- `<pr-id>` — explicit PR ID (positional argument)

Update via `az repos pr update --id <pr-id> [options] -o table`.
For reviewer changes, use `az repos pr reviewer add --id <pr-id> --reviewers <emails>`.

After updating, display the updated PR details.
