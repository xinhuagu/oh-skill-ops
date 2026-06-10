# Oh SkillOps

Skill-as-Code operations for AI agents: author, test, govern, and evolve reusable agent skills.

## Thesis

Oh SkillOps is built on three commitments:

- **Skill-as-Code**: skills should be versioned, reviewed, tested, released, and deprecated like production code.
- **Self-improving Skills**: skills should improve from traces, failures, evals, and feedback through proposed, reviewed, and versioned patches.
- **Skill Standards**: skills need clear structure, metadata, scope, permissions, evals, lifecycle states, and safety rules.

## Dogfooding

Oh SkillOps is dogfooded on itself. The repository is not only a description of SkillOps; it is the first SkillOps-managed skill ecosystem. Every core concept is expressed as repository-native files: schemas, manifests, evals, rubrics, security checklists, roadmap gates, and reviewable commits.

See [Dogfooding](docs/dogfooding.md) for how the project uses its own standards.

Oh SkillOps is an open project for treating agent skills as production-grade software assets. A skill is not just a prompt. It is a versioned package of instructions, metadata, examples, scripts, permissions, and evaluation evidence that changes how an AI agent behaves.

## Why this exists

Agent Skills are becoming a portable capability layer for coding agents and workplace agents. The emerging standard defines a skill as a folder centered on `SKILL.md`, with optional scripts, references, assets, and templates. That solves the packaging problem, but teams still need an operations layer:

- How do we write skills that trigger correctly and stay bounded?
- How do we review, version, test, and govern skills like code?
- How do we safely improve skills from real execution traces and feedback?

Oh SkillOps focuses on that missing layer.

## Project pillars

### 1. Write better skills

Guides, templates, examples, and rubrics for writing concise, testable, portable skills.

Key ideas:

- Put the trigger contract in `description`.
- Keep `SKILL.md` short and procedural.
- Move long references into supporting files.
- Declare inputs, outputs, constraints, failure behavior, and permissions.
- Prefer examples and validators over vague instructions.

### 2. Manage skills as code

A governance model for skill ownership, versioning, risk classification, permissions, test status, and rollout state.

Key ideas:

- Every skill should have an owner.
- State-changing skills need explicit risk and permission review.
- Skill changes should go through review, tests, and changelogs.
- Skills should be scoped: personal, project, team, organization, or plugin.

### 3. Evolve skills safely

A lifecycle for turning agent execution traces, failures, and user feedback into reviewed skill improvements.

Key ideas:

- Never let runtime feedback mutate production skills directly.
- Convert traces into candidate patches.
- Replay tasks before accepting changes.
- Keep rejected edits and regression evidence.
- Promote only improvements that pass quality, safety, and usefulness gates.

## Repository map

```text
docs/       Core concepts and operating model
examples/   Good and bad skill examples with manifests and evals
templates/  Starter skill packages
rubrics/    Quality scoring and review criteria
schemas/    Machine-readable SkillOps manifest schemas
```

## Initial references

- Agent Skills open standard: https://agentskills.io/
- Claude Code skills documentation: https://code.claude.com/docs/en/skills
- Qwen Code: https://github.com/QwenLM/qwen-code
- MUSE-Autoskill: https://arxiv.org/abs/2605.27366
- CODESKILL: https://arxiv.org/abs/2605.25430
- Skilldex: https://arxiv.org/abs/2604.16911

## Status

This repository is at the project-direction stage. The first milestone is a usable SkillOps playbook: writing guide, governance model, evolution loop, templates, examples, and quality rubric.
