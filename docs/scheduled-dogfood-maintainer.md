# Scheduled Dogfood Maintainer

This document defines how Oh SkillOps can use scheduled automation to improve itself without bypassing SkillOps governance.

The goal is not "an agent that merges its own changes." The goal is a recurring maintainer loop that uses the repository's own managed skills to find the next useful improvement, create a reviewable proposal, and wait for a human gate before code changes happen.

## Position

Oh SkillOps should dogfood scheduled self-improvement in two stages:

1. **Scout**: scheduled, read-mostly, issue-producing.
2. **Builder**: approval-gated, branch-and-PR-producing.

The scout stage is implemented first because it exercises the self-improvement loop while preserving a clear human review boundary.

## Why Scout First

A scheduled workflow can create constant pressure toward improvement, but it also creates automation risk. A workflow that can wake up, edit files, push branches, and merge itself is not a SkillOps loop. It is an uncontrolled mutation loop.

The scout stage keeps the useful part:

- recurring inspection
- skill-guided project analysis
- repository-native output
- durable issue history

It excludes the dangerous part:

- no direct commits
- no direct pushes
- no self-merge
- no secret or permission changes
- no production skill mutation

This matches the project rule: self-improvement is propose-only until lint, score, audit, and eval gates exist.

## Current Implementation

The first scheduled loop is:

```text
.github/workflows/dogfood-scout.yml
```

It runs weekly and can also be triggered manually. It invokes Claude Code Action with a prompt that tells the agent to:

- read the dogfood loop and project-incubation skill
- inspect the repository for dogfood gaps
- create or update a GitHub issue for human review
- avoid file edits, branches, commits, PRs, releases, workflow permission changes, and secrets

The workflow grants only:

```yaml
permissions:
  contents: read
  issues: write
```

That is intentional. The scout can read the repository and create an issue, but it cannot write repository contents.

## Activation Requirements

The workflow is safe to commit before credentials are configured. If `ANTHROPIC_API_KEY` is not present, scheduled runs skip the Claude step. Manual `workflow_dispatch` runs still dogfood the repository through a deterministic no-key fallback that creates a reviewable GitHub issue using only `GITHUB_TOKEN`.

To enable it:

1. Install and configure Claude Code Action for the repository.
2. Add the repository secret `ANTHROPIC_API_KEY`, or adapt the workflow to use Claude Code OAuth or workload identity.
3. Optionally create labels:
   - `dogfood`
   - `automation`
   - `needs-human-review`
4. Keep branch protection enabled before adding any builder workflow.

If workload identity is used instead of a static Anthropic API key, the workflow may need `id-token: write`. Do not add that permission unless the authentication path requires it.

## No-Key Manual Fallback

Manual dogfooding must not require an external model credential. When the workflow is triggered manually without `ANTHROPIC_API_KEY`, it runs a deterministic fallback that:

- creates a GitHub issue with the normal scout contract
- records that the no-key path was used
- points at a concrete current repository gap
- avoids file edits, branches, commits, pull requests, releases, secrets, and settings

The fallback is intentionally narrow. It is not a replacement for the Claude scout, and it should not pretend to perform model-based repository analysis. Its purpose is to keep the SkillOps loop executable from day one: even a new clone with only GitHub Actions and `GITHUB_TOKEN` can produce reviewable dogfood evidence.

The current fallback points at the first executable standard gap: implementing `skillops lint`.

## Scout Contract

Each scheduled scout should produce one issue with this shape:

```text
Dogfood observation:
Evidence:
Relevant managed skill:
Proposed repository artifact:
Risk and scope:
Suggested gates:
Smallest next commit:
```

The issue should be specific enough that a maintainer can say yes, no, or not yet.

Good scout outputs:

- "The thesis-capture skill has memory but no regression eval for optimizer-first drift."
- "The good example skill has output evals, but the future lint rules are not mapped to schema fields."
- "The README claims scheduled dogfooding, but only the manual loop is documented."

Bad scout outputs:

- "Improve the project."
- "Rewrite the roadmap."
- "Create a hosted platform."
- "Let the bot implement and merge the fix."

## Builder Stage

The builder stage should not be added until the scout stage has produced useful issues consistently.

When added, it should be triggered by explicit human approval, for example:

```text
issue label: dogfood-approved
or
workflow_dispatch with an issue number
```

Builder constraints:

- create a branch, never commit to `main`
- open a pull request, never self-merge
- modify only allowlisted paths
- run `skillops lint`, `skillops score`, `skillops audit`, and relevant evals when those commands exist
- include the source issue and dogfood skill in the PR body

Initial allowlisted paths should be narrow:

```text
docs/**
skills/**
examples/**
schemas/**
rubrics/**
```

Workflow files, secrets, release settings, package publishing, and repository permissions should remain human-only until the project has explicit audit rules for them.

## Failure Handling

If the scout produces a bad issue, close it and update one of:

```text
skills/project-incubation/thesis-capture/evals/output.yaml
skills/project-incubation/thesis-capture/evals/trigger.yaml
skills/project-incubation/thesis-capture/memory/rejected-edits.md
```

If it finds a durable lesson, update:

```text
skills/project-incubation/thesis-capture/memory/accepted-lessons.md
```

This turns automation failures back into SkillOps evidence instead of treating them as one-off prompt mistakes.

## References

- GitHub scheduled workflows: https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#schedule
- GitHub token permissions: https://docs.github.com/en/actions/tutorials/authenticate-with-github_token
- Claude Code Action usage: https://github.com/anthropics/claude-code-action/blob/main/docs/usage.md
- Claude Code Action scheduled maintenance example: https://github.com/anthropics/claude-code-action/blob/main/docs/solutions.md#scheduled-repository-maintenance
- Claude Code Action configuration: https://github.com/anthropics/claude-code-action/blob/main/docs/configuration.md
