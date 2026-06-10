---
name: repo-change-review
description: Review uncommitted repository changes for behavioral risk, missing validation, and commit-readiness. Use when the user asks what changed, asks for a pre-commit review, or asks whether a diff is safe to commit.
---

# Repo Change Review

## Procedure

1. Inspect the current diff and changed file list.
2. Summarize the intended behavior change in plain language.
3. Identify risks: regressions, missing tests, unsafe defaults, broad refactors, or unclear ownership.
4. Check whether focused validation was run or is obviously needed.
5. Recommend one of: commit-ready, needs validation, needs changes, or unclear.

## Output

Return:

- Summary:
- Risks:
- Validation status:
- Recommendation:

## Constraints

- Do not edit files.
- Do not invent test results.
- If there is no diff, say there are no uncommitted changes.
