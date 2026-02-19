---
allowed-tools: Bash(az repos:*), Bash(git:*)
description: Create an Azure DevOps pull request
---

## Context

- Current branch: !`git branch --show-current`
- Remote tracking: !`git rev-parse --abbrev-ref HEAD@{upstream} 2>/dev/null || echo "no upstream"`
- Default branch: !`git remote show origin 2>/dev/null | grep 'HEAD branch' | awk '{print $NF}'`
- Commits on branch: !`git log --oneline $(git remote show origin 2>/dev/null | grep 'HEAD branch' | awk '{print $NF}')..HEAD 2>/dev/null`
- Changed files: !`git diff --stat $(git remote show origin 2>/dev/null | grep 'HEAD branch' | awk '{print $NF}')...HEAD 2>/dev/null`
- Existing PRs for branch: !`az repos pr list --source-branch $(git branch --show-current) --status active -o table 2>/dev/null || echo "none"`

## Your task

Create an Azure DevOps pull request for the current branch. Follow these rules:

1. If the branch has no upstream remote, push it first: `git push -u origin <branch>`
2. If an active PR already exists for this branch, inform the user and stop. Suggest `/az-devops-cli:update-pr` instead.
3. **Source branch:** current branch
4. **Target branch:** the repo's default branch (from context above). Fall back to `main` if detection failed.
5. **Title:** derive from branch name (strip prefixes like `feature/`, `fix/`, `ndi-123/`, convert kebab-case/snake_case to sentence case) or summarize from commit log
6. **Description:** generate from commit log — list commits as bullet points with a brief summary at the top
7. Pass `--delete-source-branch true` by default

If the user passed arguments, apply them:
- `--reviewers <email1,email2>` — add required reviewers
- `--work-items <id1,id2>` — link work items
- `--draft` — create as draft PR
- `--labels <label1,label2>` — add labels
- `--target <branch>` — override target branch

Create the PR:

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
