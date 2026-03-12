---
allowed-tools: Bash(az pipelines:*), Bash(git:*)
description: Check Azure DevOps CI pipeline runs
argument-hint: "[--list] [--logs] [--full] [--pipeline <name-or-id>] [--branch <branch>]"
---

## Context

- Current branch: !`git branch --show-current`
- Repository name: !`basename $(git rev-parse --show-toplevel)`

## Your task

Check Azure DevOps CI pipeline run status. By default, show the most recent run for the current branch.

### Step 1: Determine parameters

Resolve these from user arguments or defaults:

- **Branch:** user-provided `--branch` value, or current branch (from context above)
- **Pipeline:** user-provided `--pipeline <name-or-id>`, or auto-detect (see step 2)
- **Mode:** one of:
  - **summary** (default) — compact status output
  - **logs** (`--logs`) — summary + failed stage/job names and log output for failed steps
  - **full** (`--full`) — everything: all stages/jobs timeline, duration, triggered-by, artifacts
- **List:** if `--list` is passed, show the last 5 runs instead of just the most recent

### Step 2: Resolve pipeline (if not provided)

If no `--pipeline` was given, list pipelines for this repository:

```
az pipelines list --repository <repo-name> --repository-type tfsgit -o json
```

- If exactly one pipeline is found, use it
- If multiple pipelines are found, list them (name + ID) and ask the user which one to check
- If none found, inform the user and stop

### Step 3: Get pipeline run(s)

**If `--list` was passed**, get the last 5 runs:

```
az pipelines runs list \
  --pipeline-ids <pipeline-id> \
  --branch <branch> \
  --top 5 \
  --query-order FinishTimeDesc \
  -o json
```

Present as a table: Run ID | Status | Result | Start Time | Finish Time | Reason

Then stop (unless the user asks to inspect a specific run).

**Otherwise**, get the most recent run:

```
az pipelines runs list \
  --pipeline-ids <pipeline-id> \
  --branch <branch> \
  --top 1 \
  --query-order FinishTimeDesc \
  -o json
```

### Step 4: Display results

#### Summary mode (default)

Present a compact summary:
- Pipeline name
- Run ID
- Status (notStarted / inProgress / completed)
- Result (succeeded / failed / canceled / partiallySucceeded) — only if completed
- Source branch and commit
- Start time and duration (or "in progress")
- Reason (manual / individualCI / pullRequest / etc.)

#### Logs mode (`--logs`)

Show everything from summary, plus:

1. Get the run timeline to find failed stages/jobs:
   ```
   az pipelines runs show --id <run-id> -o json
   ```
2. If the run failed or partially succeeded, identify which stages/jobs failed from the timeline
3. Fetch and display logs for the failed steps using:
   ```
   az pipelines runs artifact list --run-id <run-id> -o json
   ```
   Or use the build log endpoint:
   ```
   az devops invoke --area build --resource builds --route-parameters buildId=<run-id> --api-version 7.1 -o json
   ```
   Then fetch specific log content as needed

#### Full mode (`--full`)

Show everything from logs mode, plus:
- Complete timeline of all stages and jobs (with status indicators)
- Who triggered the run
- Artifacts produced (if any)
- Tags applied to the run
- Queue and pool information

### Output formatting

- Use clear status indicators: succeeded, failed, running, canceled
- For in-progress runs, note that the run is still active
- Always include the web URL for the run so the user can open it in a browser (construct from org/project URL + `_build/results?buildId=<run-id>`)
