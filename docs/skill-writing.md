# Writing Good Skills

Good skills are small, bounded, testable procedures. They should make an agent more reliable at a specific workflow without hiding risk or bloating context.

## Core structure

An agent skill should usually be a folder:

```text
my-skill/
  SKILL.md
  examples/
  references/
  scripts/
  assets/
```

`SKILL.md` is the entry point. It should contain metadata and the shortest useful procedure. Long references, templates, examples, and scripts should live in supporting files.

## Description is a trigger contract

The `description` is not marketing copy. It is the agent's first signal for whether the skill should load.

Good descriptions say:

- What the skill does.
- When to use it.
- When not to use it if the boundary is easy to confuse.
- What inputs the user or repository must provide.

Avoid broad descriptions such as "Use for all coding tasks." Broad triggers cause skills to load too often and pollute context.

## Keep the body procedural

The body should tell the agent what to do:

1. Gather the required context.
2. Check preconditions.
3. Execute the workflow.
4. Validate the result.
5. Report output in a known shape.

Avoid explaining the philosophy of the skill inside the runtime instructions. Put background material in `references/`.

## Declare boundaries

Every non-trivial skill should define:

- Inputs: what the agent needs before starting.
- Outputs: what the agent should produce.
- Constraints: what the agent must not do.
- Failure behavior: when to stop, ask, or report uncertainty.
- Tool policy: which tools are expected, allowed, or forbidden.

## Prefer examples over vague style rules

Examples reduce ambiguity. A good skill often includes:

- One minimal happy path.
- One edge case.
- One bad output that the agent should avoid.

## Writing checklist

- The description is specific enough to trigger only for the intended task.
- The skill has a clear input and output contract.
- The procedure is short enough to stay useful in context.
- Supporting files are referenced by name and purpose.
- State-changing operations require explicit review or permission.
- The skill can be evaluated with at least one replay task.
