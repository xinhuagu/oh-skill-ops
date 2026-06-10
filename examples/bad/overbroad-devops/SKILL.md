---
name: overbroad-devops
description: Use for all devops, coding, deployment, cloud, testing, bug fixing, CI, database, and production tasks.
---

# Overbroad DevOps Skill

This is intentionally bad.

## Problems

- The description is too broad and will trigger too often.
- It mixes coding, deployment, CI, databases, and production operations.
- It does not define inputs, outputs, constraints, or stop conditions.
- It does not separate read-only guidance from state-changing behavior.
- It does not explain permissions or risk.
- It cannot be evaluated with a stable replay task.

## Better approach

Split this into smaller skills:

- `ci-failure-triage`
- `focused-test-runner`
- `staging-deploy-checklist`
- `database-migration-review`
