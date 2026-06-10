# Roadmap

## Milestone 0: Project frame

- Define the project direction and vocabulary.
- Publish the initial writing guide, governance guide, and evolution guide.
- Add starter templates and quality rubric.
- Add good and bad examples that reviewers can discuss.

## Milestone 1: SkillOps playbook

- Expand the skill quality rubric into a review checklist.
- Add a skill manifest convention for owner, version, runtime, scope, risk, permissions, and tests.
- Add examples for coding agents, research agents, and document agents.
- Define minimal eval cases for trigger precision, task success, and safety regressions.

## Milestone 2: CLI prototype

Planned commands:

```bash
skillops init
skillops lint skills/repo-review
skillops score skills/repo-review
skillops audit skills/repo-review
skillops eval skills/repo-review
skillops evolve --from traces/session.json
```

The first implementation should stay local-first and avoid external services.

## Milestone 3: Evolution loop

- Define a trace format for agent runs.
- Generate candidate skill patches from repeated failures and feedback.
- Replay tasks before accepting changes.
- Keep a rejected-edit buffer and regression evidence.
- Require human review before promotion.

## Milestone 4: Registry and governance

- Add a metadata-only skill registry format.
- Support skill lifecycle states: experimental, review, approved, deprecated, blocked.
- Track clone/similarity risk and stale dependencies.
- Add organization-level policy examples.
