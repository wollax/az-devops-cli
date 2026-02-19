---
allowed-tools: Bash(az repos:*), Bash(git:*)
description: Create an Azure DevOps pull request
---

## Context

- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -20`

## Your task

Create an Azure DevOps pull request for the current branch.

### Step 1: Gather additional context

Run these commands to understand the current state:

1. `git rev-parse --abbrev-ref HEAD@{upstream} 2>/dev/null` — check if branch tracks a remote (empty = no upstream)
2. `git remote show origin 2>/dev/null | grep 'HEAD branch' | awk '{print $NF}'` — detect default branch
3. Using the default branch detected above, run `git log --oneline <default-branch>..HEAD` and `git diff --stat <default-branch>...HEAD` to see branch-specific commits and changed files
4. `az repos pr list --source-branch <current-branch> --status active -o table` — check for existing PRs

### Step 2: Validate

- If an active PR already exists for this branch, inform the user and stop. Suggest `/az-devops-cli:update-pr` instead.
- If the branch has no upstream remote, push it first: `git push -u origin <branch>`

### Step 3: Create the PR

- **Source branch:** current branch
- **Target branch:** the repo's default branch detected above. Fall back to `main` if detection failed.
- **Title:** derive from branch name (strip prefixes like `feature/`, `fix/`, `ndi-123/`, convert kebab-case/snake_case to sentence case) or summarize from commit log
- **Description:** generate from commit log — list commits as bullet points with a brief summary at the top
- Pass `--delete-source-branch true` by default

If the user passed arguments, apply them:
- `--reviewers <email1,email2>` — add required reviewers
- `--work-items <id1,id2>` — link work items
- `--draft` — create as draft PR
- `--labels <label1,label2>` — add labels
- `--target <branch>` — override target branch

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
