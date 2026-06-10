# Research Notes: SkillOps Direction

Date: 2026-06-10

This note investigates three questions behind Oh SkillOps:

1. How should good agent skills be written?
2. How should skills be managed as code, with special attention to Alibaba/Qwen and Tencent/CodeBuddy public material?
3. How can skills improve themselves safely over time?

The document separates facts from interpretation. Where public source material is weak, the limitation is stated explicitly.

## Executive Synthesis

Agent skills are emerging as a portable capability layer for AI agents. The strongest public standard today treats a skill as a directory centered on `SKILL.md`, with metadata, instructions, optional scripts, references, assets, and progressive disclosure. The authoring lesson is clear: a skill should be a scoped, testable procedure, not a long prompt dump.

The management layer is less mature. Anthropic and Agent Skills specify package structure and runtime behavior; Qwen Code demonstrates a richer open-source engineering surface for skills, subagents, MCP, path gating, project/user/extension scopes, and permission modes. Tencent CodeBuddy appears, from public product material and secondary reports, to focus on an end-to-end product workflow across IDE/plugin/CLI and multi-agent development stages, but I did not find enough public technical documentation to claim a concrete Skill-as-Code registry or governance mechanism.

The research frontier is moving toward skill banks, lifecycle management, evaluation, and controlled refinement. Recent papers converge on the same point: skills should be long-lived, versioned, evaluated assets. They can be generated or improved from traces and external knowledge, but direct runtime mutation is unsafe. Oh SkillOps should therefore position itself as the operations layer: authoring standards, manifest metadata, evals, security checks, registries, and human-reviewed evolution loops.

## 1. Writing Good Skills

### Source Facts

The Agent Skills open standard defines a skill as a folder containing `SKILL.md` plus optional directories such as `scripts/`, `references/`, and `assets/`. The standard describes skills as lightweight, portable packages for specialized knowledge and workflows. It also describes progressive disclosure: agents first load only names and descriptions, then load full instructions when a task matches, and then optionally load supporting files or execute bundled code during execution.

Sources:

- Agent Skills overview: https://agentskills.io/
- Agent Skills specification: https://agentskills.io/specification

The Agent Skills specification requires `name` and `description` frontmatter fields. It constrains `name`, limits `description`, provides optional `license`, `compatibility`, `metadata`, and experimental `allowed-tools`, and recommends step-by-step instructions, input/output examples, and edge cases in the Markdown body.

Source:

- Agent Skills specification: https://agentskills.io/specification

Claude Code documentation gives an implementation-oriented view. Skills extend Claude Code with instructions in `SKILL.md`; the skill body loads only when used, unlike always-loaded project context. Claude Code supports personal, project, enterprise, and plugin skills; supporting files; invocation controls such as `disable-model-invocation` and `user-invocable`; tool pre-approval through `allowed-tools`; settings-level visibility overrides; and sharing through project skills, plugins, and managed settings.

Source:

- Claude Code skills documentation: https://code.claude.com/docs/en/skills

Agent Skills best-practice guidance emphasizes that good skills should start from real expertise rather than generic model-generated advice. Source material can include internal documentation, runbooks, style guides, API specs, code review comments, issue trackers, version-control history, and real failure resolutions. It also recommends refining skills through real execution, studying agent traces, spending context carefully, designing coherent units, using moderate detail, using progressive disclosure for large skills, and structuring instructions with gotchas, templates, checklists, validation loops, plan-validate-execute flows, and reusable scripts.

Source:

- Agent Skills best practices: https://agentskills.io/skill-creation/best-practices

Agent Skills description guidance treats the `description` field as the primary trigger mechanism. It recommends testing descriptions with labeled should-trigger and should-not-trigger queries, including near-miss negatives, running multiple trials because model behavior is nondeterministic, using train/validation splits to avoid overfitting, and iterating until the description improves trigger accuracy.

Source:

- Optimizing skill descriptions: https://agentskills.io/skill-creation/optimizing-descriptions

Agent Skills evaluation guidance recommends concrete assertions, evidence-backed grading, mechanical validators where possible, comparing with-skill versus without-skill performance, and aggregating pass rate, time, and token cost to determine whether a skill adds enough value to justify its cost.

Source:

- Evaluating skill output quality: https://agentskills.io/skill-creation/evaluating-skills

Anthropic's product announcement frames skills as composable, portable, efficient, and capable of including executable code. It also says Claude Code users can install skills via plugins, share them through version control, and manually install them under `~/.claude/skills`. The same announcement notes programmatic custom skill versioning and management through the API and warns that skills can execute code, so trusted sources matter.

Source:

- Introducing Agent Skills: https://claude.com/blog/skills

### Interpretation

For Oh SkillOps, skill writing should be treated as engineering, not copywriting. The core design object is a small executable procedure with a trigger contract.

Practical authoring principles:

- The `description` is a routing contract, not a tagline.
- The `SKILL.md` body should be short, procedural, and scoped to what the agent needs every time.
- Large reference material should move into supporting files and be loaded by explicit conditions.
- Skills should include examples, output contracts, validation steps, and stop conditions.
- Skill writing should include trigger evals and output evals from the beginning.
- A skill should be split when its trigger becomes broad, its permissions become mixed-risk, or it combines unrelated workflows.

## 2. Managing Skills as Code

### 2.1 Anthropic and Agent Skills Management Surface

#### Source Facts

Agent Skills gives the portable package format. The open standard includes structured frontmatter, optional metadata, optional compatibility information, optional `allowed-tools`, and optional directories for scripts, references, and assets.

Sources:

- Agent Skills overview: https://agentskills.io/
- Agent Skills specification: https://agentskills.io/specification

Claude Code adds operational scope. Skills can live at enterprise, personal, project, or plugin level. Project skills are committed into `.claude/skills/`; personal skills live under `~/.claude/skills/`; plugin skills are namespaced; managed settings can deploy organization-wide skills. Claude Code also supports skill visibility overrides, automatic discovery, live change detection for skill text, supporting files, invocation controls, and tool pre-approval.

Source:

- Claude Code skills documentation: https://code.claude.com/docs/en/skills

#### Interpretation

Anthropic's public material defines the skill package and several runtime controls, but it does not by itself define a complete enterprise SkillOps lifecycle: ownership, semantic versioning, rollout states, approval gates, risk scoring, eval requirements, audit logs, or deprecation workflows. Oh SkillOps can fill that gap without competing with the standard.

### 2.2 Alibaba / Qwen Code

#### Source Facts

Qwen Code is an open-source terminal AI agent optimized for Qwen models. Its README describes it as terminal-first and IDE-friendly, with multi-provider support, open-source co-evolution, and built-in tools including Skills and SubAgents for agentic workflows.

Source:

- Qwen Code README: https://github.com/QwenLM/qwen-code

Qwen Code's user feature index lists Skills, SubAgents, MCP, approval mode, auto mode, worktrees, sandboxing, hooks, scheduled tasks, memory, and other operational features.

Source:

- Qwen Code feature index: https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/_meta.ts

Qwen Code's skills documentation says skills are model-invoked modular capabilities organized as folders containing `SKILL.md` and optional supporting files. It supports personal skills in `~/.qwen/skills/`, project skills in `.qwen/skills/`, and extension skills provided by installed extensions. Project skills can be checked into git and shared with teammates. Qwen Code also supports explicit skill invocation through `/skills <skill-name>`, `priority`, `paths:` gating for file-path-specific activation, debugging through `qwen --debug`, and team sharing by committing `.qwen/skills/`.

Source:

- Qwen Code skills documentation: https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/skills.md

Qwen Code's subagents documentation defines subagents as specialized assistants with separate context, task-specific prompts, controlled tools, autonomous execution, and progress visibility. Subagents are configured as Markdown files with YAML frontmatter and can be stored at project, user, or extension level. Their frontmatter supports model selection, `approvalMode`, `tools`, and `disallowedTools`. MCP tools follow the same allow/block rules.

Source:

- Qwen Code subagents documentation: https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/sub-agents.md

Qwen Code's MCP documentation defines project and user scopes for MCP configuration, supports CLI and settings-based management, includes server trust flags, OAuth configuration, include/exclude tool filters, and global MCP allow/deny lists.

Source:

- Qwen Code MCP documentation: https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/mcp.md

Qwen Code's approval mode documentation defines permission modes that map directly to risk: Plan, Ask Permissions, Auto-Edit, Auto, and YOLO. It describes which modes allow file edits, shell commands, manual approvals, classifier-based approvals, and full automation.

Source:

- Qwen Code approval mode documentation: https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/approval-mode.md

Qwen Code itself includes a `.qwen/skills/qwen-code-claw/SKILL.md` example that instructs another agent how to install, authenticate, and use Qwen Code through ACPX for coding tasks. This is a concrete example of a skill acting as an agent-integration package rather than just a local workflow note.

Source:

- Qwen Code Claw skill: https://github.com/QwenLM/qwen-code/blob/main/.qwen/skills/qwen-code-claw/SKILL.md

#### Interpretation

Qwen Code is the strongest public Alibaba-adjacent reference for Skill-as-Code management. It shows several management mechanisms that Oh SkillOps should study:

- Filesystem-native skill packages.
- Git-based project sharing.
- Personal/project/extension scopes.
- Path-gated skill activation.
- Priority metadata for listing order.
- Debuggable skill loading.
- Subagent configs with tool allowlists and approval modes.
- MCP server scopes, trust flags, include/exclude filters, and global allow/deny.
- Permission modes that can be mapped into skill risk classes.

This suggests that a SkillOps registry should not only store skill text. It should also store scope, activation boundaries, tool permissions, runtime compatibility, subagent relationships, MCP dependencies, and approval-mode expectations.

### 2.3 Tencent / CodeBuddy

#### Source Facts

The official CodeBuddy site is available at https://www.codebuddy.ai/, but the public page returned little extractable technical documentation through the available browsing tools.

Source:

- CodeBuddy official site: https://www.codebuddy.ai/

Search results and secondary summaries describe CodeBuddy as Tencent Cloud's AI coding assistant with IDE/plugin/CLI product surfaces and an end-to-end development workflow. The same secondary material describes multi-agent roles such as PlanAgent, DesignAgent, CodingAgent, and DeployAgent, plus MCP support. However, I did not find official, detailed, source-accessible documentation for CodeBuddy's skill package format, skill registry, skill versioning, or skill governance model.

Secondary source, use cautiously:

- CodeBuddy summary page: https://zh.wikipedia.org/wiki/Codebuddy

#### Interpretation

Tencent/CodeBuddy is useful as a product-shape reference, not yet as a technical Skill-as-Code reference. The likely lesson is workflow orchestration: skills or agent capabilities should map to a full delivery chain, not just isolated coding steps. But until official technical documentation is available, Oh SkillOps should avoid claiming that CodeBuddy has a public skill registry, skill package format, or skill governance mechanism.

For Oh SkillOps, the Tencent-inspired direction should be framed as a hypothesis:

- Product-level agent systems need skill governance across planning, design, coding, review, deployment, and cloud operations.
- A SkillOps layer should make each capability auditable and scoped, especially when workflows cross from local code edits into deployment or cloud resources.
- The project should keep a `source-confidence` field in research notes so weakly sourced product claims do not become technical assumptions.

## 3. Self-Evolving and Improving Skills

### Source Facts

MUSE-Autoskill proposes a skill-centric lifecycle covering creation, memory, management, evaluation, and refinement. It argues that static skills limit reusability and long-term improvement, and introduces skill-level memory, unit tests, and runtime feedback for continuous refinement.

Source:

- MUSE-Autoskill: https://arxiv.org/abs/2605.27366

CODESKILL treats coding-agent trajectories as source material for reusable procedural skills. It extracts multi-granularity skills, evolves them with new experience, and maintains a compact skill bank. It trains a management policy using rubric-based skill-quality feedback and verifiable execution feedback.

Source:

- CODESKILL: https://arxiv.org/abs/2605.25430

Anything2Skill argues that RAG retrieves fragmented declarative evidence and leaves agents to repeatedly infer procedures. It compiles external knowledge into structured skill contracts with invocation conditions, contraindications, action moves, workflow steps, constraints, output specifications, supporting evidence, and confidence scores. It then manages extracted skills in a persistent SkillBank with taxonomy-aware compilation, registry-level reconciliation, lifecycle tracking, versioned updates, and visible skill-tree projection.

Source:

- Anything2Skill: https://arxiv.org/abs/2606.09316

CoEvoSkills focuses on autonomous construction of complex multi-file skill packages. It couples a skill generator with a surrogate verifier that co-evolves to provide feedback when ground-truth test content is unavailable.

Source:

- CoEvoSkills: https://arxiv.org/abs/2604.01687

Skilldex proposes a package manager and registry for agent skill packages. It includes compiler-style format conformance scoring, line-level diagnostics for description specificity and frontmatter validity, a skillset abstraction for coherent bundled skills, hierarchical scope distribution, a human-in-the-loop suggestion loop, a metadata-only community registry, and an MCP server.

Source:

- Skilldex: https://arxiv.org/abs/2604.16911

A data-driven analysis of 40,285 public skills finds that skill content is concentrated in software engineering workflows, shows strong ecosystem homogeneity and intent-level redundancy, and identifies non-trivial safety risks including state-changing or system-level actions.

Source:

- Agent Skills data-driven analysis: https://arxiv.org/abs/2602.08004

Security research warns that `SKILL.md` is operational text, not passive documentation. One paper studies semantic supply-chain attacks in discovery, selection, and governance stages, showing that description-only framing can influence skill visibility, selection, and governance decisions. SkillSieve proposes layered detection for malicious skills using metadata/static checks, LLM review, and jury-style review for high-risk cases.

Sources:

- Under the Hood of SKILL.md: https://arxiv.org/abs/2605.11418
- SkillSieve: https://arxiv.org/abs/2604.06550

### Interpretation

The self-evolution literature points toward a controlled SkillOps loop:

```text
execution trace
  -> failure, correction, or feedback
  -> candidate skill patch
  -> trigger evals and task replays
  -> security and permission audit
  -> human review
  -> versioned promotion or rejection
```

The strongest shared ideas are:

- Skills should have lifecycle states, not just files.
- Skill evolution should be driven by traces, failures, feedback, and external knowledge.
- A skill bank should stay compact; redundant skills need merge or deprecation mechanisms.
- Skill-level memory is useful, but it should not automatically mutate production instructions.
- Evaluation should compare with-skill and without-skill behavior, not just check whether the skill can run.
- Security review must inspect natural-language instructions and metadata, not just scripts.

This supports an Oh SkillOps rule: runtime systems may propose skill edits, but only reviewed, tested, and versioned changes should enter the production skill registry.

## Project Direction Implications


### Competitive Positioning Update

Recent research makes the self-improving-skill area more crowded than the initial project frame suggested. SkillOpt treats a skill document as trainable external state for a frozen agent. It uses a separate optimizer model to propose bounded add/delete/replace edits and accepts an edit only when it improves held-out validation. That is close to the most ambitious version of `skillops evolve`, but focused on optimizing an individual skill artifact against eval feedback.

Source:

- SkillOpt: https://arxiv.org/abs/2605.23904

Another paper, also named SkillOps, frames skill-library maintenance as a method-agnostic plug-in layer. It introduces typed skill contracts, a hierarchical ecosystem graph, and maintenance diagnostics across utility, compatibility, risk, and validation dimensions. This overlaps with the Oh SkillOps governance direction and creates a naming/positioning collision that the project should acknowledge.

Source:

- SkillOps: Managing LLM Agent Skill Libraries as Self-Maintaining Software Ecosystems: https://arxiv.org/abs/2605.13716

The practical implication is that Oh SkillOps should avoid competing head-on as a skill optimizer. The strongest position is governance-first:

- define standards and manifests;
- run lint, score, audit, and eval gates;
- manage owner, scope, risk, lifecycle, and provenance;
- treat optimizers such as SkillOpt as possible backends for `skillops evolve --propose-only`;
- require review, audit, and eval gates before accepting optimizer-generated patches.

This keeps Oh SkillOps focused on the operational control plane for skill fleets rather than on a single optimization algorithm.

### Recommended Positioning

Oh SkillOps should not start as a marketplace. Marketplaces amplify the hardest unsolved problems: redundant packages, weak trust signals, hidden permissions, prompt-injection risk, stale skills, and poor eval evidence.

The stronger initial positioning is:

> Oh SkillOps is the CI/CD and governance layer for Agent Skills.

Expanded:

> Oh SkillOps helps teams treat agent skills like production code: authored with standards, reviewed with tests, governed with metadata, and improved from real execution feedback.

### Initial Product Surface

The first useful deliverable should be a local-first playbook plus machine-readable conventions:

1. Authoring guide
   - `description` trigger contract.
   - `SKILL.md` structure.
   - supporting file guidance.
   - examples and anti-examples.

2. Manifest and registry model
   - owner, version, scope, runtime, lifecycle state, risk level.
   - tools and MCP dependencies.
   - test/eval references.
   - source confidence and provenance.

3. Evaluation format
   - should-trigger and should-not-trigger evals.
   - task replay cases.
   - objective assertions.
   - with-skill versus without-skill comparison.

4. Security checklist
   - prompt injection and social engineering in natural language.
   - permissions and tool expansion.
   - scripts and external dependencies.
   - network, credential, and filesystem assumptions.

5. Evolution workflow
   - trace capture.
   - candidate patch generation.
   - rejected-edit memory.
   - replay and audit gates.
   - human-reviewed promotion.

### Draft CLI Direction

A future `skillops` CLI should start with local static and semi-automated checks:

```bash
skillops lint skills/repo-review
skillops score skills/repo-review
skillops audit skills/repo-review
skillops eval skills/repo-review
skillops evolve --from traces/session.json --propose-only
```

The `--propose-only` posture matters: early versions should generate patches for review, not self-apply changes.

## Open Questions

1. Should Oh SkillOps define its own `skillops.yaml`, or should it store all extra metadata under the Agent Skills `metadata` field?
2. Should evals be scoped to one skill, a skillset, or a registry?
3. How should the project model cross-agent compatibility across Claude Code, Codex, Qwen Code, and other clients?
4. What is the minimum useful trace format for skill evolution?
5. Should security checks be rule-first, LLM-first, or layered like SkillSieve?
6. How should redundant skills be detected and merged without erasing useful domain-specific nuance?
7. What claims about CodeBuddy can be substantiated from official technical documentation, and what should remain out of scope until better sources are available?

## Source Confidence Notes

- Strong: Agent Skills standard, Claude Code docs, Qwen Code repo documentation, arXiv papers.
- Medium: Anthropic product announcement for product-level roadmap and API positioning.
- Weak: CodeBuddy technical mechanism claims, because official source-accessible technical docs were not found during this pass. Treat Tencent/CodeBuddy as product inspiration, not as a documented Skill-as-Code governance model.
- Naming risk: a 2026 arXiv paper is also titled "SkillOps"; keep the repository name `oh-skill-ops` distinct and emphasize the project as an open, local-first implementation and governance toolkit.
