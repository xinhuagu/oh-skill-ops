# Implementation Architecture

This document turns the project thesis into an implementation direction.

Oh SkillOps should begin as a local-first SkillOps toolchain, not as a marketplace or hosted platform. The first goal is to make skills inspectable, reviewable, testable, and safe to improve inside a normal Git repository.

## Product Thesis

Oh SkillOps is a CI/CD and governance layer for agent skills.

It exists to support three commitments:

1. **Skill-as-Code**: skills should be versioned, reviewed, tested, released, and deprecated like production code.
2. **Self-improving Skills**: skills should improve from traces, failures, evals, and feedback through proposed, reviewed, and versioned patches.
3. **Skill Standards**: skills need clear structure, metadata, scope, permissions, evals, lifecycle states, and safety rules.

## Why Local-First

The first implementation should run locally and in CI because skill governance needs to happen where skill changes happen: in pull requests, repository history, and developer workflows.

Starting local-first avoids premature platform work and keeps the project useful before there is a registry, UI, or hosted service.

Local-first also matches the nature of agent skills:

- Skills are files and folders.
- Project skills are usually checked into Git.
- Review happens through diffs.
- Evals can run against local fixtures.
- Security checks should run before installation or rollout.

A marketplace can come later. The first milestone is the toolchain that makes a marketplace safe.

## Core Architecture

```text
Skill Package
  -> SkillOps Manifest
  -> SkillOps CLI
  -> Registry Metadata
  -> Evals and Audit Evidence
  -> Proposed Evolution Patches
```

### 1. Skill Package

A skill package is the unit of work.

Draft layout:

```text
skills/repo-change-review/
  SKILL.md
  skillops.yaml
  evals/
    trigger.yaml
    output.yaml
  memory/
    accepted-lessons.md
    rejected-edits.md
  references/
  scripts/
```

`SKILL.md` remains the portable agent skill entry point. Oh SkillOps should not replace the Agent Skills standard. It should add operational metadata, checks, evals, and lifecycle management around it.

### 2. SkillOps Manifest

`skillops.yaml` is the project-owned operational manifest.

Draft example:

```yaml
name: repo-change-review
owner: platform-team
version: 0.1.0
runtime:
  - claude-code
  - qwen-code
scope: project
risk: low
status: experimental
skill_file: SKILL.md
allowed_tools:
  - read
  - grep
evals:
  - evals/trigger.yaml
  - evals/output.yaml
provenance:
  source: manual
  created_from:
    - docs/research-notes.md
```

The manifest should start small. It should include only fields needed for review, automation, and lifecycle decisions.

Required v0 fields:

- `name`
- `owner`
- `version`
- `runtime`
- `scope`
- `risk`
- `status`
- `skill_file`

Optional v0 fields:

- `allowed_tools`
- `disallowed_tools`
- `mcp_servers`
- `evals`
- `dependencies`
- `provenance`
- `references`
- `compatibility`

### 3. SkillOps CLI

The CLI is the first executable product surface.

Planned commands:

```bash
skillops init
skillops lint skills/repo-change-review
skillops score skills/repo-change-review
skillops audit skills/repo-change-review
skillops eval skills/repo-change-review
skillops evolve --from traces/session.json --propose-only
```

The first implementation should focus on deterministic checks before model-assisted workflows.

#### `skillops lint`

Purpose: validate structure and manifest correctness.

Checks:

- package contains `SKILL.md`
- manifest exists and parses
- manifest required fields exist
- skill name matches package name where applicable
- frontmatter parses
- description exists and is not empty
- referenced eval files exist
- lifecycle state is valid
- risk value is valid

#### `skillops score`

Purpose: apply the quality rubric.

Inputs:

- `SKILL.md`
- `skillops.yaml`
- optional eval metadata
- `rubrics/skill-quality.yaml`

Outputs:

- total score
- category scores
- diagnostics
- suggested review status

Scoring should begin rule-based and transparent. LLM-assisted scoring can come later, but early adoption depends on deterministic diagnostics.

#### `skillops audit`

Purpose: identify safety, permission, and supply-chain risks.

Checks:

- broad or vague trigger descriptions
- state-changing behavior without explicit risk metadata
- dangerous tool permissions
- shell/network/deployment assumptions
- scripts with unclear dependencies
- external references without provenance
- instructions that attempt to bypass user approval

#### `skillops eval`

Purpose: run trigger and output checks.

This should come after `lint`, `score`, and `audit` because evals require stable package and metadata conventions.

V0 eval types:

- trigger evals: should-trigger and should-not-trigger queries
- output evals: expected output shape and objective assertions
- regression evals: previous failures that should stay fixed

#### `skillops evolve --propose-only`

Purpose: generate candidate skill patches from traces, failures, feedback, and eval results.

This must be propose-only at first. It should never mutate production skills directly.

Workflow:

```text
trace or feedback
  -> candidate patch
  -> lint
  -> score
  -> audit
  -> eval
  -> human review
  -> merge or reject
```

## Registry Model

A registry is metadata about known skills. It should not require hosting in v0.

Draft local registry:

```yaml
skills:
  - path: skills/repo-change-review
    name: repo-change-review
    owner: platform-team
    version: 0.1.0
    status: experimental
    risk: low
    runtimes:
      - claude-code
      - qwen-code
```

Registry responsibilities:

- list skill packages
- track owners
- track lifecycle status
- track risk level
- track supported runtimes
- record eval status
- support deprecation and replacement metadata

A hosted registry can later index these files, but the source of truth should remain Git-friendly.

## Implementation Stack

Recommended initial stack: TypeScript + Node.js.

Reasons:

- Simple npm distribution.
- Strong CLI ecosystem.
- Good YAML, Markdown, frontmatter, and JSON Schema support.
- Familiar to agent-tooling contributors.
- Easy GitHub Actions integration.

Draft source layout:

```text
src/
  cli.ts
  commands/
    init.ts
    lint.ts
    score.ts
    audit.ts
    eval.ts
    evolve.ts
  core/
    diagnostics.ts
    manifest.ts
    skill-package.ts
    frontmatter.ts
    registry.ts
  rules/
    structure.ts
    description.ts
    permissions.ts
    lifecycle.ts
    references.ts
schemas/
  skillops.schema.json
fixtures/
  valid-basic-skill/
  invalid-overbroad-skill/
```

## Implementation Order

### Phase 1: Standards and Static Checks

Goal: make skill packages inspectable and reviewable.

Deliverables:

1. `skillops.yaml` schema.
2. Example valid and invalid skill packages.
3. `skillops lint`.
4. `skillops score` using the existing rubric.
5. `skillops audit` with deterministic red flags.
6. GitHub Action example.

This phase directly serves Skill-as-Code and Skill Standards.

### Phase 2: Evals

Goal: make skill usefulness measurable.

Deliverables:

1. Trigger eval format.
2. Output eval format.
3. Regression eval format.
4. `skillops eval` runner.
5. Comparison reports for with-skill and without-skill behavior.

This phase creates the evidence needed for safe self-improvement.

### Phase 3: Proposed Evolution

Goal: turn traces and feedback into reviewed patches.

Deliverables:

1. Minimal trace format.
2. `memory/accepted-lessons.md` and `memory/rejected-edits.md` conventions.
3. `skillops evolve --propose-only`.
4. Patch reports with rationale and risk notes.
5. Required lint/score/audit/eval gates before promotion.

This phase serves Self-improving Skills without allowing uncontrolled runtime mutation.

### Phase 4: Registry and Distribution

Goal: manage skill collections across teams.

Deliverables:

1. Local `registry.yaml`.
2. Skill lifecycle transitions.
3. Deprecation and replacement metadata.
4. Compatibility matrix across Claude Code, Qwen Code, Codex, and other clients.
5. Optional MCP server or hosted index.

A public marketplace should only be considered after this phase.

## Non-Goals for the First Version

- No hosted SaaS.
- No public marketplace.
- No automatic production skill mutation.
- No opaque LLM-only scoring.
- No attempt to replace Agent Skills, Claude Code skills, or Qwen Code skills.
- No runtime-specific lock-in.

## Design Principles

1. **Compatible, not competing**: build around existing skill formats.
2. **Deterministic first**: start with checks users can understand.
3. **Evidence over vibes**: score and evolve skills from evals, traces, and reviewable diagnostics.
4. **Propose, do not mutate**: self-improvement should create candidate patches, not silently rewrite production skills.
5. **Git-native governance**: PRs, diffs, reviews, and CI are the first control plane.
6. **Risk-aware by default**: permission expansion and state-changing behavior should be visible and reviewable.

## Recommended Next Step

Start with issue #7, the skill manifest schema, then implement `skillops lint`.

That is the smallest executable step that turns the thesis into a working standard.
