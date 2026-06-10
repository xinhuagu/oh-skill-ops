# Skill Evolution

Skill evolution is the process of turning repeated agent experience into better reusable procedures.

The goal is not to let an agent rewrite production skills freely. The goal is to create a controlled improvement loop.

## Evolution loop

```text
execution trace
  -> failure or feedback
  -> candidate skill patch
  -> replay evaluation
  -> risk audit
  -> human review
  -> promotion or rejection
```

## Inputs

Useful evolution signals include:

- Failed tasks.
- Repeated user corrections.
- Review comments.
- Test failures.
- Tool permission denials.
- Long reasoning loops.
- Repeated manual context gathering.

## Candidate patches

A candidate patch may:

- Narrow or expand trigger conditions.
- Add a missing precondition.
- Add a validation step.
- Move long instructions into references.
- Add an example.
- Remove unsafe or ineffective behavior.

Candidate patches should be small and reviewable.

## Evaluation

Before accepting a skill change, replay tasks should check:

- The new skill solves the original failure.
- Existing successful cases still pass.
- The skill does not trigger in unrelated tasks.
- Tool permissions remain justified.
- Output shape remains stable.

## Skill-level memory

Each skill can keep a small memory file for lessons learned:

```text
memory/
  accepted-lessons.md
  rejected-edits.md
  eval-results.jsonl
```

Accepted lessons can later become instructions or examples. Rejected edits prevent the system from retrying known-bad changes.

## Safety rule

Runtime feedback may propose edits, but only reviewed patches should change the production skill.
