---
allowed-tools:
  - Bash(az repos:*)
  - Bash(git:*)
---

# Complete Azure DevOps Pull Request

Complete (merge) a pull request for the current branch.

## Gather Context

1. `git branch --show-current` — get the current branch name
2. `az repos pr list --source-branch <current-branch> --status active -o json` — find the active PR for this branch
3. If a PR is found, extract its ID and run `az repos pr show --id <id> -o json` to get current state and policy status

## Behavior

- **Auto-detect PR ID** from the current branch's active PR. If no active PR is found, inform the user and stop.
- If the user passes a PR ID directly as an argument, use that instead of auto-detection.
- Show the user the current PR state before completing.

## Completion Modes

### Default: Auto-Complete with Squash (no extra args)

Set auto-complete so the PR merges automatically once all policies pass:

```
az repos pr update \
  --id <pr-id> \
  --auto-complete true \
  --squash true \
  --delete-source-branch true \
  --transition-work-items true \
  --merge-commit-message "<PR title>"
```

### `--now`: Force Complete Immediately

Complete the PR right now (bypasses waiting for policies — use when all checks already pass):

```
az repos pr update \
  --id <pr-id> \
  --status completed \
  --squash true \
  --delete-source-branch true \
  --transition-work-items true \
  --merge-commit-message "<PR title>"
```

### `--merge`: Use Merge Instead of Squash

Applies to both default and `--now` modes. Omit `--squash` or set `--squash false`:

```
az repos pr update \
  --id <pr-id> \
  --auto-complete true \
  --squash false \
  --delete-source-branch true \
  --transition-work-items true
```

## Optional Arguments

- `<pr-id>` — explicit PR ID (positional argument)
- `--now` — force complete immediately instead of setting auto-complete
- `--merge` — use merge commit instead of squash
- `--no-delete-branch` — keep the source branch after merge
- `--no-transition` — don't transition linked work items

## After Completion

Display the final PR status via `az repos pr show --id <pr-id> -o table`.
