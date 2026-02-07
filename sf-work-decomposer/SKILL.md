---
name: sf-work-decomposer
description: "Break Salesforce technical designs into atomic, sequenced work packages for sub-agent execution. Use when a Salesforce implementation design needs to be decomposed into independent tasks with dependency ordering, checkpoint files, and acceptance criteria. Supports the stateless sub-agent chaining pattern for reliable multi-step org configuration."
---

# SF Work Decomposer

Break a technical design into atomic tasks that sub-agents can execute independently.

## Workflow

1. Read the technical design document
2. Identify all components (objects, fields, flows, agents, permissions, etc.)
3. Build dependency graph
4. Order tasks by dependency (see dependency rules below)
5. Generate task files with full context for each step

## Dependency Ordering Rules

Execute in this order — each category depends on the previous:

```
1. Custom Objects (create the containers)
2. Custom Fields (populate the containers)
3. Page Layouts (arrange the fields)
4. Record Types (if needed)
5. Validation Rules
6. Flows (reference objects + fields)
7. Apex Classes (reference objects + fields)
8. Knowledge Articles (content for agents)
9. Agent Topics + Actions (reference flows/apex)
10. Agent Configuration + Activation
11. Permission Sets (grant access to everything above)
12. Permission Set Groups
13. Sharing Rules
14. Testing + Validation
```

Within each category, order by object dependency (parent before child, lookup target before source).

## Task File Format

Generate one file per task at `tasks/task-{nn}-{description}.md`:

```markdown
# Task {nn}: {Title}

## Org
- **URL:** {org login URL}
- **Username:** {username}
- **Password:** {password}

## Objective
{One clear sentence: what to create/configure}

## Prerequisites
- Task {nn-1} must be complete
- {Any specific element that must exist}

## Steps
1. {Precise step with exact values}
2. {Next step}
...

## Acceptance Criteria
- [ ] {Verifiable outcome — what to check in the org}
- [ ] {Field exists with correct type}
- [ ] {Flow activates without errors}

## Checkpoint
Write completion status to `tasks/checkpoint.json`:
```json
{
  "task": {nn},
  "status": "complete",
  "timestamp": "{ISO datetime}",
  "notes": "{any deviations or issues}"
}
```
```

## Task Granularity Rules

Each task should be:
- **Atomic**: One logical unit of work (one object, one flow, one permission set)
- **Self-contained**: All information needed is in the task file
- **Idempotent**: Can be re-run safely (check if exists before creating)
- **Verifiable**: Clear acceptance criteria that can be checked via UI or API

Split when:
- A flow has >10 elements (split into: create flow + configure elements + activate)
- An object has >15 fields (split into: create object + batch 1 fields + batch 2 fields)
- Agent setup (split into: create topic + add actions + configure instructions + activate)

Don't split:
- A single field creation
- A simple 3-5 element flow
- A permission set with <10 permissions

## Checkpoint File Schema

`tasks/checkpoint.json` tracks overall progress:

```json
{
  "project": "{project name}",
  "totalTasks": {n},
  "completed": [{task numbers}],
  "current": {current task number or null},
  "failed": [{task numbers with errors}],
  "notes": {}
}
```

Sub-agents read this file first to know where to resume.

## Sub-Agent Spawning Pattern

When generating tasks, also generate a runner script outline:

```
For each task in tasks/:
  1. Read checkpoint.json — skip if task already complete
  2. Spawn sub-agent with task file content as context
  3. Sub-agent executes steps in the org
  4. Sub-agent updates checkpoint.json
  5. If failure: log error, continue to next independent task or stop if blocking
```

## References

- See `references/common-splits.md` for examples of how to split complex components
