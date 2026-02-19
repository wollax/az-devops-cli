---
allowed-tools: Bash(az repos:*), Bash(git:*)
description: Complete an Azure DevOps pull request
---

## Context

- Current branch: !`git branch --show-current`
- Active PR for branch: !`az repos pr list --source-branch $(git branch --show-current) --status active -o json 2>/dev/null || echo "[]"`

## Your task

Complete (merge) an Azure DevOps pull request. Follow these rules:

1. **Auto-detect PR ID** from the active PR for the current branch (from context above). If the user provided a PR ID as an argument, use that instead.
2. If no active PR is found and no ID was given, inform the user and stop.
3. Fetch current PR details: `az repos pr show --id <id> -o json`
4. Show the user the current PR state before completing.

## Completion modes

### Default (no extra args): Auto-complete with squash

Set auto-complete so the PR merges automatically once all policies pass:

```
az repos pr update --id <pr-id> \
  --auto-complete true \
  --squash true \
  --delete-source-branch true \
  --transition-work-items true \
  --merge-commit-message "<PR title>"
```

### `--now` argument: Force complete immediately

Complete the PR right now (use when all checks already pass):

```
az repos pr update --id <pr-id> \
  --status completed \
  --squash true \
  --delete-source-branch true \
  --transition-work-items true \
  --merge-commit-message "<PR title>"
```

### `--merge` argument: Use merge instead of squash

Applies to both default and `--now` modes. Set `--squash false` instead of `--squash true`.

## Optional arguments

- `<pr-id>` — explicit PR ID (positional argument)
- `--now` — force complete immediately instead of setting auto-complete
- `--merge` — use merge commit instead of squash
- `--no-delete-branch` — keep the source branch after merge (omit `--delete-source-branch` or set to `false`)
- `--no-transition` — don't transition linked work items (omit `--transition-work-items` or set to `false`)

After completion, display the final PR status via `az repos pr show --id <pr-id> -o table`.
