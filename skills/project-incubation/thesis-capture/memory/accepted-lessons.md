# Accepted Lessons

- Keep Oh SkillOps governance-first and local-first before marketplace or hosted platform work.
- Treat self-improvement as propose-only until lint, score, audit, and eval gates exist.
- Dogfood every major concept as repository-native artifacts before claiming it as a standard.
- Scheduled self-improvement should start as a scout that creates reviewable issues before any approval-gated builder writes branches or pull requests.
- Manual dogfood should work without external model credentials through a deterministic no-key fallback that creates reviewable evidence.
- Approval-gated builders may create branches and PRs, but the approval label and final merge must stay human-controlled.
- The repository's dogfood automation should use Claude Code OAuth via `CLAUDE_CODE_OAUTH_TOKEN`, not direct Anthropic API keys, when the maintainer wants subscription-based usage.
