# Skill Security and Permission Review

Agent skills are operational instructions. They are not passive documentation. A skill can influence tool use, file edits, network calls, data exposure, and production behavior. Oh SkillOps should therefore review skills with the same seriousness as code and configuration.

This checklist is the first manual specification for `skillops audit`.

## Review Layers

Security review should be layered:

1. **Static checks**: deterministic schema, structure, metadata, and pattern checks.
2. **LLM review**: semantic review for suspicious instructions, social engineering, and hidden policy bypasses.
3. **Jury review**: higher-confidence review for high-risk skills, organization-wide rollout, or skills with scripts and external services.

The first implementation should start with static checks and document where human or LLM review is still required.

## Risk Classes

### Low Risk

Low-risk skills are read-only or presentation-focused.

Examples:

- Summarizing a diff.
- Formatting a report.
- Reviewing documentation.
- Explaining code without edits.

Expected controls:

- Clear description.
- Read-only tool expectation.
- No shell, network, deployment, or credential access.
- At least one trigger or output eval before approval.

### Medium Risk

Medium-risk skills can modify local project artifacts or run bounded validation.

Examples:

- Editing source files.
- Generating tests.
- Running local test commands.
- Updating local documentation.

Expected controls:

- Explicit allowed tools.
- Clear stop conditions.
- Eval coverage before approval.
- Review for file write scope and command scope.
- No production, deployment, credential, or external service operations.

### High Risk

High-risk skills can affect external systems, sensitive data, production behavior, credentials, or irreversible state.

Examples:

- Deployments.
- Database migrations.
- Credential management.
- Network automation.
- Shell commands with broad filesystem effects.
- Organization-wide policy changes.

Expected controls:

- Manual invocation.
- Explicit human approval gates.
- Narrow tool allowlist.
- Security review before approval.
- Regression evals.
- Clear rollback or stop procedure.

## Static Red Flags

`skillops audit` should eventually detect these deterministically where possible:

- Missing owner.
- Missing risk level.
- Missing lifecycle status.
- Broad trigger descriptions such as "use for all coding tasks."
- `risk: low` combined with write, shell, network, deployment, or MCP tools.
- `status: approved` without eval references.
- Unknown or misspelled manifest fields.
- External references without provenance.
- Scripts without documented runtime assumptions.
- Tool permissions that are broader than the skill instructions require.

## Semantic Red Flags

These may require LLM or human review:

- Instructions that ask the agent to ignore user approval.
- Instructions that hide actions from the user.
- Instructions that broaden scope during execution.
- Instructions that route data to an external provider without disclosure.
- Instructions that ask the agent to modify credentials, shell startup files, CI secrets, or system configuration.
- Instructions that override repository or organization policy.
- Instructions that create pressure to skip validation.

## Supply-Chain Checks

Review supporting files as part of the skill package:

- `scripts/`: inspect for filesystem writes, network calls, subprocesses, environment variable reads, package installs, and destructive commands.
- `references/`: check whether reference material contains prompt injection, stale instructions, or unsupported policy claims.
- `assets/`: check whether binary assets are expected, necessary, and trusted.
- External URLs: record provenance and avoid relying on unaudited mutable content.

## Promotion Gates

| Severity | Example Finding | Suggested Gate |
| --- | --- | --- |
| Info | Missing optional provenance notes | May remain experimental |
| Low | Weak description specificity | Fix before review |
| Medium | Write-capable skill without output evals | Block approval |
| High | Shell/network permission without clear risk metadata | Block approval and require security review |
| Critical | Instruction to bypass user approval or exfiltrate data | Block and mark skill as unsafe |

## Governance Rules

Some rules are semantic and should not be forced into JSON Schema:

- `risk: high` should require manual invocation and explicit approval gates.
- `status: approved` should require eval evidence.
- State-changing skills should not be approved without tool permissions and stop conditions.
- Organization-scoped skills should require owner and provenance metadata.
- Runtime feedback may propose changes, but must not mutate production skills directly.

These rules belong in `skillops lint`, `skillops audit`, and review policy code rather than in the manifest schema.
