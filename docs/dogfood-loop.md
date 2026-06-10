# Dogfood Loop

This document defines how Oh SkillOps uses its own skills to develop the project.

The loop is manual-first, propose-only, and evidence-gated. Until the CLI exists, the maintainer or agent follows this process by reading the relevant skill package, applying its procedure, and producing reviewable repository changes.

## Loop Summary

```text
project-development task
  -> choose managed skill
  -> load skill package context
  -> produce proposed output
  -> check output contract
  -> audit risk and scope
  -> human review
  -> commit
  -> update memory or evals
```

The loop should not silently mutate production skills. Runtime feedback may suggest improvements, but accepted changes must be reviewable commits.

## First Managed Process Skill

The first process skill is:

```text
skills/project-incubation/thesis-capture/
```

Use it when the task is about turning a raw project idea, direction change, or strategic insight into repository-ready artifacts.

Examples:

- capturing a project thesis
- turning user intent into commitments and non-goals
- deciding the smallest next commit
- converting a repeated project-development pattern into a managed skill
- keeping Oh SkillOps governance-first rather than optimizer-first

Do not use it for implementation bugs, CI failures, security audits, or generic commit messages.

## Context to Load

Before using a process skill, load these files:

```text
skills/project-incubation/thesis-capture/SKILL.md
skills/project-incubation/thesis-capture/skillops.yaml
skills/project-incubation/thesis-capture/evals/trigger.yaml
skills/project-incubation/thesis-capture/evals/output.yaml
skills/project-incubation/thesis-capture/memory/accepted-lessons.md
skills/project-incubation/thesis-capture/memory/rejected-edits.md
```

When relevant, also load:

```text
docs/dogfooding.md
docs/implementation-architecture.md
docs/research-notes.md
ROADMAP.md
README.md
```

## Required Output Shape

When using `project-thesis-capture`, the work should produce or internally check this shape:

```text
Project thesis:
Core commitments:
Non-goals:
Differentiators:
Repository artifacts to create or update:
Open questions:
Smallest next commit:
```

A final user-facing response may be shorter, but the change should still satisfy this contract.

## Gates

### 1. Trigger Gate

Confirm the task actually matches the skill.

Use the trigger eval intent:

- should trigger for project thesis, project direction, repo incubation, or converting a development process into a skill
- should not trigger for code bug fixes, security audits, or commit-message-only work

### 2. Output Gate

Check that the proposed work satisfies the output eval contract:

- preserves the user's stated intent
- includes concrete repository artifacts
- includes non-goals where scope could sprawl
- separates facts from assumptions when external claims are involved
- does not default to SaaS, marketplace, or optimizer-first direction

### 3. Risk Gate

Use `skillops.yaml` as the risk frame.

The current skill is `risk: medium` because it can reshape project direction and write docs. It must not:

- deploy anything
- touch secrets
- mutate external services
- use production data
- run destructive shell commands

### 4. Review Gate

Every accepted loop output should become a reviewable commit or issue.

For now, review is human approval in the conversation. Later, the same gate should move into pull requests, CI checks, and `skillops` CLI output.

## Memory Updates

Update memory only when the loop produces a durable lesson.

### Accepted Lessons

Append to:

```text
skills/project-incubation/thesis-capture/memory/accepted-lessons.md
```

Use this when a lesson should influence future project-development work.

Examples:

- governance-first beats optimizer-first for project positioning
- sidecar manifests preserve runtime portability better than custom frontmatter for operational metadata
- CLI checks should come early so the standard is executable

### Rejected Edits

Append to:

```text
skills/project-incubation/thesis-capture/memory/rejected-edits.md
```

Use this when a tempting direction should not be repeated.

Examples:

- do not collapse the full project creation process into one broad always-on skill
- do not make runtime feedback directly mutate production skills
- do not position the project primarily as a marketplace before governance exists

## Eval Updates

Add or update evals when a failure becomes repeatable.

### Trigger Evals

Update `evals/trigger.yaml` when the skill was invoked too broadly or missed a project-incubation task.

Examples:

- add a should-not-trigger case for generic code review
- add a should-trigger case for turning a repeated development process into a managed skill

### Output Evals

Update `evals/output.yaml` when an output failure should never repeat.

Examples:

- the response omitted non-goals
- the response recommended a marketplace too early
- the response failed to identify a concrete repository artifact

### Regression Evals

A future `evals/regression.yaml` should collect failures that were fixed and must stay fixed.

## Commit Discipline

A dogfood-loop commit should be small and explain which part of the loop it advanced.

Good examples:

```text
Add project thesis capture dogfood skill
Document manual dogfood loop
Add regression eval for optimizer-first drift
```

Avoid broad commits that mix strategy, schema, examples, and implementation unless they are part of one explicit milestone.

## When to Create the Next Process Skill

Create a new process skill when a repeated project-development workflow becomes distinct enough to govern separately.

Likely next skills:

```text
skills/project-incubation/research-grounding/
skills/project-incubation/architecture-design/
skills/project-incubation/governance-artifact-author/
skills/project-incubation/dogfood-auditor/
```

Do not add a new skill just because a document exists. Add one when the workflow has a trigger, procedure, output contract, and evals.

## Future CLI Mapping

This manual loop maps directly to future commands:

```bash
skillops lint skills/project-incubation/thesis-capture
skillops score skills/project-incubation/thesis-capture
skillops audit skills/project-incubation/thesis-capture
skillops eval skills/project-incubation/thesis-capture
skillops evolve --from traces/session.json --propose-only
```

The CLI should automate checks, not replace review.
