---
allowed-tools:
  - Bash(az repos:*)
  - Bash(git:*)
---

# Create Azure DevOps Pull Request

Create a pull request for the current branch in Azure DevOps.

## Gather Context

Run these commands to understand the current state:

1. `git branch --show-current` — get the source branch name
2. `git rev-parse --abbrev-ref HEAD@{upstream} 2>/dev/null` — check if branch tracks a remote
3. `git log --oneline main..HEAD` — commits on this branch (fall back to `develop` or the repo's default branch if `main` doesn't exist)
4. `git diff main...HEAD --stat` — changed files summary
5. `az repos pr list --source-branch <current-branch> --status active -o table` — check for existing PRs on this branch

## Behavior

- If the branch has no upstream remote, push it first: `git push -u origin <branch>`
- If an active PR already exists for this branch, inform the user and stop (suggest `/az-devops-cli:update-pr` instead).
- **Source branch:** current branch
- **Target branch:** default branch (typically `main` or `develop`; detect from repo config or use `main` as fallback)
- **Title:** derive from branch name (strip prefixes like `feature/`, `fix/`, `ndi-123/`, convert kebab-case/snake_case to sentence case) or summarize from commit log
- **Description:** generate from commit log — list commits as bullet points, add a brief summary at the top
- Pass `--delete-source-branch true` by default

## Optional Arguments

The user may pass these as command arguments:

- `--reviewers <email1,email2>` — add required reviewers
- `--work-items <id1,id2>` — link work items
- `--draft` — create as draft PR
- `--labels <label1,label2>` — add labels
- `--target <branch>` — override target branch (default: `main`)

## Create the PR

```
az repos pr create \
  --source-branch <source> \
  --target-branch <target> \
  --title "<title>" \
  --description "<description>" \
  --delete-source-branch true \
  [--reviewers <emails>] \
  [--work-items <ids>] \
  [--draft] \
  [--labels <labels>] \
  -o table
```

After creation, display the PR URL and ID.
