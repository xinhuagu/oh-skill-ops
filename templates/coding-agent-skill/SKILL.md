---
name: coding-agent-skill
description: Use when the user asks for a bounded software engineering workflow in a repository, such as reviewing a diff, adding focused tests, or applying a small bug fix.
---

# Coding Agent Skill

## Scope

Use this skill for bounded repository work. Do not use it for broad rewrites, production deployment, or unrelated product planning.

## Procedure

1. Inspect the repository conventions before editing.
2. Identify the smallest safe change.
3. Update only files required by the task.
4. Run focused validation when available.
5. Summarize the change, validation, and remaining risk.

## Constraints

- Do not overwrite user changes.
- Do not run destructive commands.
- Do not introduce new dependencies unless the task requires them.
- Do not claim validation passed unless it actually ran.

## Output

Return:

- Files changed:
- Behavior changed:
- Validation:
- Residual risk:
