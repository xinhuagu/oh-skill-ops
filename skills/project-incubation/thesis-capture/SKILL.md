---
name: project-thesis-capture
description: Capture a raw project idea and turn it into a clear project thesis, commitments, non-goals, differentiators, and first executable artifacts. Use when a user is starting or reshaping a project and needs the idea converted into repository-ready direction.
---

# Project Thesis Capture

## Purpose

Use this skill to turn an early project idea into a practical project direction that can be committed to a repository.

This skill is for project incubation, not general brainstorming. It should preserve the user's actual intent while making the idea legible, scoped, and actionable.

## Inputs

Required:

- The user's raw project idea, goal, or thesis.

Optional:

- Existing README, roadmap, or docs.
- Source material or competitor references.
- Constraints such as target audience, compliance needs, runtime, ecosystem, or preferred implementation stack.

## Procedure

1. Capture the user's idea in one direct sentence.
2. Extract the core thesis.
3. Identify two to five commitments that the project must uphold.
4. Identify non-goals so the project does not sprawl.
5. Identify differentiators against nearby projects or obvious alternatives.
6. Translate the thesis into repository-native artifacts: README sections, roadmap items, schemas, examples, evals, or issues.
7. Mark unsupported claims as assumptions if they are not backed by sources or repository evidence.
8. Recommend the smallest next commit that makes the project more concrete.

## Output

Return:

- Project thesis:
- Core commitments:
- Non-goals:
- Differentiators:
- Repository artifacts to create or update:
- Open questions:
- Smallest next commit:

## Constraints

- Do not invent source-backed claims.
- Do not broaden the project beyond the user's intent.
- Do not turn one project into a marketplace, platform, or SaaS unless the user asks for that.
- Do not make irreversible repository or external-service changes.
- If research is needed, separate facts from interpretation and project decisions.

## Dogfood Use

Oh SkillOps uses this skill to convert the user's original idea into its own thesis:

- Skill-as-Code
- Self-improving Skills
- Skill Standards

This skill should be evaluated against whether it preserves those commitments and turns them into concrete repository artifacts.
