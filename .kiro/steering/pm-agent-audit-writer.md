---
inclusion: manual
name: PM Agent - Audit Trail Writer
description: "Captures decisions, inferences, challenges, and outcomes throughout the authoring session."
---

# PM Agent — Audit Trail Writer

This component runs throughout the entire PM Agent session, capturing every significant interaction.

---

## What to Capture

Record each of the following event types as they occur:

### 1. Questions Asked
```markdown
### [Timestamp] Question Asked
- **State:** [current state]
- **Section:** [which PRD section this relates to]
- **Question:** [the question posed]
- **Options offered:** [A/B/C/D if lettered]
```

### 2. PM Answers
```markdown
### [Timestamp] PM Response
- **Answer:** [what the PM said]
- **Interpreted as:** [how the agent used this answer]
```

### 3. Agent Inferences
```markdown
### [Timestamp] Inference Made
- **Inferred:** [what the agent inferred]
- **Based on:** [source — earlier answer, Knowledge Graph, context]
- **PM response:** Confirmed / Adjusted to: [adjustment] / Rejected
```

### 4. Challenges and Pushback
```markdown
### [Timestamp] Challenge
- **Agent challenged:** [what was challenged]
- **Rationale:** [why the agent pushed back]
- **PM outcome:** Accepted / Rejected / Modified to: [modification]
```

### 5. Sizing Decisions
```markdown
### [Timestamp] Sizing Decision
- **Detected:** [potential multi-job request]
- **Proposed split:** [Job A] vs [Job B]
- **PM decision:** Split (chose Job [X]) / Keep together (reason: [reason])
```

### 6. Readiness Scores
```markdown
### [Timestamp] Readiness Score
- **Overall:** [X.X/10]
- **Status:** Pass / Needs Work
- **Weakest sections:** [list]
- **Action taken:** [proceed to attach / enter refine loop]
```

### 7. State Transitions
```markdown
### [Timestamp] State Transition
- **From:** [previous state]
- **To:** [new state]
- **Reason:** [exit condition met]
```

---

## Output Format

Persist to `aidlc-docs/prd-drafts/<feature-name>/audit-trail.md`:

```markdown
---
feature: "<feature-name>"
session_start: "YYYY-MM-DDTHH:MM:SS"
session_end: "YYYY-MM-DDTHH:MM:SS"
total_turns: X
final_readiness_score: X.X
status: "attached" | "draft_saved" | "in_progress"
---

# Audit Trail — <Feature Name>

## Session Summary
- **PM:** [name]
- **Date:** [date]
- **Turns used:** [X] / 15
- **Final score:** [X.X] / 10
- **Outcome:** [Epic created: PROJ-123 / Saved as draft / In progress]

## Decision Log

[All entries in chronological order, using the formats above]

## Key Decisions
| # | Decision | Rationale | PM Choice |
|---|----------|-----------|----------|
| 1 | [topic] | [why] | [what PM decided] |
| 2 | ... | ... | ... |

## Inferences Made
| # | Inference | Source | Confirmed? |
|---|-----------|--------|------------|
| 1 | [what was inferred] | [from where] | Yes/No/Adjusted |
| 2 | ... | ... | ... |

## Challenges Issued
| # | Challenge | PM Response |
|---|-----------|-------------|
| 1 | [what was challenged] | [accepted/rejected/modified] |
| 2 | ... | ... |
```

---

## When to Write

- **After each turn:** Append new entries to the in-memory audit trail
- **On save/resume:** Write full audit trail to Git
- **On session end:** Write final version with summary section
- **On attach:** Write final version marking outcome as "attached"

---

## Privacy Note

The audit trail contains the PM's answers and decisions. It is persisted on their feature branch with the same access controls as the PRD itself. It is intended for team review and traceability.
