# Dogfooding Oh SkillOps

Oh SkillOps is developed by applying SkillOps to itself.

This repository is not only a description of a governance model. It is the first SkillOps-managed skill ecosystem. Every project concept should eventually appear as a repository-native artifact: a schema, manifest, eval, rubric, checklist, roadmap gate, issue, or reviewable commit.

## Why Dogfooding Matters

Dogfooding keeps the project honest.

If Oh SkillOps claims that skills should be managed as code, this repository should manage its own examples as code. If it claims that skills need standards, those standards should be machine-readable. If it claims that skills should improve safely, its own evolution workflow should be proposed, reviewed, tested, and versioned.

The goal is to avoid becoming a methodology-only repository. A standard matters when it can be run.

## Current Dogfood Artifacts

### Process Dogfood

The first dogfooded project-development skill is:

```text
skills/project-incubation/thesis-capture/
  SKILL.md
  skillops.yaml
  evals/
    trigger.yaml
    output.yaml
  memory/
    accepted-lessons.md
    rejected-edits.md
```

This skill captures the process we used to turn the original idea into a repository thesis: Skill-as-Code, Self-improving Skills, and Skill Standards. It makes the project creation process itself a managed skill package.

### Skill-as-Code

The first managed example skill package is:

```text
examples/good/repo-change-review/
  SKILL.md
  skillops.yaml
  evals/
    trigger.yaml
    output.yaml
```

These packages demonstrate the minimum SkillOps unit:

- `SKILL.md`: runtime skill instructions.
- `skillops.yaml`: operational sidecar manifest.
- `evals/trigger.yaml`: should-trigger and should-not-trigger cases.
- `evals/output.yaml`: expected output shape and forbidden behavior.
- `memory/`: accepted lessons and rejected edits when a process skill starts evolving.

### Skill Standards

The first machine-readable standard is:

```text
schemas/skillops.schema.json
```

It defines the v0 sidecar manifest contract:

- `name`
- `owner`
- `version`
- `runtime`
- `scope`
- `risk`
- `status`
- `skill_file`

It also locks known lifecycle and risk values while keeping runtime identifiers open to avoid client lock-in.

### Security and Permission Review

The first audit specification is:

```text
docs/skill-security.md
```

It defines manual review layers that can later become `skillops audit` rules:

1. Static checks.
2. LLM review.
3. Jury review for high-risk skills.

### Quality Scoring

The first scoring rubric is:

```text
rubrics/skill-quality.yaml
```

It provides the initial categories for `skillops score`:

- trigger precision
- procedural clarity
- tool and permission safety
- testability
- maintainability

### Research and Architecture

The project direction is grounded in:

```text
docs/research-notes.md
docs/implementation-architecture.md
```

Research notes separate source facts, interpretation, project implications, and source-confidence limitations. The implementation architecture turns those findings into a local-first toolchain plan.

## Dogfood Rules

The project should follow these rules for its own changes.

### 1. Managed Skills Need Manifests

Any new managed skill under `skills/` or good example under `examples/good/` should include a `skillops.yaml` manifest.

Minimum expectation:

- owner
- version
- runtime
- scope
- risk
- status
- skill file path

### 2. Managed Skills Need Evals

Any skill that claims to be a good example should include at least one trigger eval and one output eval.

Minimum expectation:

- one should-trigger case
- one should-not-trigger case
- required output sections or assertions
- forbidden behavior when relevant

### 3. Risk Changes Need Review

Any change that increases risk, expands tools, adds scripts, adds network assumptions, or broadens a trigger should be treated as a governance-significant change.

Examples:

- `risk: low` to `risk: medium`
- adding shell or network access
- moving from project scope to organization scope
- changing status to `approved`
- broadening a description so the skill triggers more often

### 4. Self-Improvement Is Propose-Only

Runtime feedback may produce a candidate patch. It must not directly mutate a production skill.

The intended flow is:

```text
feedback or trace
  -> candidate patch
  -> lint
  -> score
  -> audit
  -> eval
  -> human review
  -> merge or reject
```

### 5. The CLI Must Run Here First

The first `skillops` CLI implementation should be validated against this repository before it is positioned as a general tool.

Initial dogfood commands:

```bash
skillops lint skills/project-incubation/thesis-capture
skillops score skills/project-incubation/thesis-capture
skillops audit skills/project-incubation/thesis-capture
skillops lint examples/good/repo-change-review
skillops score examples/good/repo-change-review
skillops audit examples/good/repo-change-review
```

Later:

```bash
skillops eval skills/project-incubation/thesis-capture
skillops eval examples/good/repo-change-review
skillops evolve --from traces/session.json --propose-only
```

## Current Gaps

The repository has the first standards artifacts, but not the executable CLI yet.

Open gaps:

- no `skillops lint` implementation yet
- no `skillops score` implementation yet
- no `skillops audit` implementation yet
- no GitHub Actions workflow yet
- no trace format yet
- no proposed-patch format yet

These gaps are intentional. The project is moving from standards to executable checks in Milestone 1.

## Narrative

The strongest version of the project story is:

> Oh SkillOps is dogfooded on itself: every concept in the project is first expressed as repository-native files, schemas, evals, and reviewable changes.

This keeps the project practical. Oh SkillOps should not merely talk about better skill governance. It should show what better skill governance looks like in Git.
