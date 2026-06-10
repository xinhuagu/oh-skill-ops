# Roadmap

## Milestone 0: Project frame

- Define the project direction and vocabulary.
- Publish the initial writing guide, governance guide, evolution guide, and implementation architecture.
- Add starter templates and quality rubric.
- Add good and bad examples that reviewers can discuss.
- Capture source-backed research notes and competitive positioning.

## Milestone 1: Governance-first executable standard

The first usable version should make Skill-as-Code machine-checkable. This merges the original playbook and CLI prototype milestones so the standard is executable from the start.

Deliverables:

- Define the `skillops.yaml` sidecar manifest schema.
- Add example manifests for existing good examples.
- Add minimal trigger and output eval fixtures.
- Add a security and permission review checklist.
- Implement `skillops lint` for package structure, schema validation, lifecycle fields, and obvious semantic gates.
- Implement `skillops score` against `rubrics/skill-quality.yaml`.
- Implement `skillops audit` for risk, permission, and supply-chain red flags.
- Add a GitHub Actions example for running lint/score/audit in pull requests.

Planned early commands:

```bash
skillops init
skillops lint skills/repo-review
skillops score skills/repo-review
skillops audit skills/repo-review
```

## Milestone 2: Skill evals

- Define trigger evals with should-trigger and should-not-trigger cases.
- Define output evals with required sections, assertions, and forbidden behavior.
- Define regression evals for previously fixed failures.
- Implement `skillops eval`.
- Report eval status in a format usable by CI and registry metadata.

## Milestone 3: Safe evolution loop

The first evolution loop should be propose-only and human-reviewed. It should not attempt direct runtime mutation.

Deliverables:

- Define a minimal trace format for agent runs.
- Store accepted lessons and rejected edits under `memory/`.
- Generate candidate patches from traces, failures, feedback, and eval results.
- Run lint, score, audit, and eval before promotion.
- Treat optimizers such as SkillOpt as possible backends, not as the project identity.

Planned command:

```bash
skillops evolve --from traces/session.json --propose-only
```

## Milestone 4: Registry and governance

- Add a local metadata-only `registry.yaml` format.
- Support skill lifecycle states: experimental, review, approved, deprecated, blocked.
- Track owner, version, runtime, scope, risk, eval status, and provenance.
- Track clone/similarity risk and stale dependencies.
- Add organization-level policy examples.
- Explore optional hosted registry or MCP server only after local-first workflows are useful.

## Non-goal reminder

Oh SkillOps should not start as a marketplace or skill optimizer. The first durable contribution is the governance and CI/CD layer for skill fleets.
