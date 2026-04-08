---
name: add-analytics-instrumentation
description: >
  End-to-end analytics instrumentation workflow for a PR, branch, file,
  directory, or feature. Reads the code, discovers what events should be
  tracked, and produces a concrete instrumentation plan. Use this skill whenever
  a user wants to add analytics to a PR or feature.
---

# add-analytics-instrumentation

Orchestrator for the analytics instrumentation pipeline. Your job is to figure out what the user wants to instrument, gather the relevant code, and run the pipeline to produce a tracking plan.

## Pipeline

### Step 0: Capture intent

Determine **what** the user wants to instrument:

| Input type           | How to recognize it                                                            |
| -------------------- | ------------------------------------------------------------------------------ |
| **PR**               | A PR URL, PR number, or phrases like "this PR", "my PR"                        |
| **Branch**           | A branch name or "this branch", "my branch"                  |
| **File / Directory** | A file path, directory path, or glob pattern                                   |
| **Feature**          | A natural-language description of functionality |

**If ambiguous**, ask the user what they want to instrument.

### Step 1: diff-intake (PR or Branch)

Invoke the `diff-intake` skill with the PR or branch reference.

It produces a `change_brief` YAML block.

### Step 1a: Direct file read (File / Directory)

Build the `change_brief` YAML by reading files directly:

1. Resolve the input (directory → all source files, file → that file)
2. Read each file and summarize what it does
3. Scan for existing instrumentation patterns
4. Build `change_brief` YAML

### Step 1b: Feature search (Feature)

The user described a feature. Find the relevant code:

1. Search git commit history for related commits
2. Search codebase for files related to feature
3. Build `change_brief` YAML

### Step 2: discover-event-surfaces

Invoke `discover-event-surfaces` with the `change_brief` YAML.

It produces an `event_candidates` YAML block.

If empty, stop and tell user nothing to instrument.

### Step 3: instrument-events

Invoke `instrument-events` with the `event_candidates` YAML.

It produces a `trackingPlan` JSON with:
- Exact file locations
- Tracking code
- Property definitions

## Presenting the result

Walk through each event:
- What it tracks and why
- Where tracking call goes
- What properties it sends

Then ask if they want to adjust or proceed.

## Error handling

If any step fails, surface the error and stop. Don't continue with incomplete data.
