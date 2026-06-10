# Skill Governance

Skills should be managed like code because they can change agent behavior, tool access, data handling, and production workflows.

## Skill manifest

A managed skill should have explicit metadata. This project uses the following draft convention:

```yaml
name: repo-review
owner: platform-team
version: 0.1.0
runtime:
  - claude-code
  - codex
scope: project
risk: medium
status: experimental
allowed_tools:
  - read
  - grep
  - bash:test-only
tests:
  - evals/repo-review/basic.yaml
```

The manifest can live in `skillops.yaml` or be embedded in a broader registry file.

## Lifecycle states

- `experimental`: useful but not trusted for broad reuse.
- `review`: ready for human and automated checks.
- `approved`: stable enough for normal use.
- `deprecated`: replaced by a better skill or no longer needed.
- `blocked`: unsafe, stale, misleading, or policy-violating.

## Review gates

At minimum, review should check:

- Trigger precision.
- Tool and permission scope.
- Data exposure risk.
- State-changing behavior.
- Test coverage.
- Ownership and maintenance path.
- Compatibility with target agents.

## Risk levels

- `low`: read-only guidance, formatting, summarization, or style conventions.
- `medium`: writes files, edits code, runs local tests, or changes project artifacts.
- `high`: deploys, deletes, manages credentials, touches production data, or automates external systems.

High-risk skills should be manually invoked and should require explicit approval for dangerous actions.

## Distribution scopes

- Personal: useful to one user across projects.
- Project: checked into a repository and tied to local conventions.
- Team: shared by a team with common workflow assumptions.
- Organization: centrally managed and policy-controlled.
- Plugin: bundled with tools, agents, hooks, or MCP servers.

## Governance principle

A skill should be easy to install, but hard to silently expand. Changes to permissions, scope, trigger breadth, or state-changing behavior deserve review.
