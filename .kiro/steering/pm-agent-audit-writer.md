---
inclusion: manual
name: PM Agent - Audit Trail Writer
description: "Captures decisions, inferences, challenges, forcing question outcomes, premises, and scores throughout the authoring session."
---

# PM Agent — Audit Trail Writer

This component runs throughout the entire PM Agent session, capturing every significant interaction.

> **Tool-agnostic:** This file works in any AIDLC-compatible IDE (Kiro, Bob, or any tool that supports steering files).

---

## What to Capture

1. **Questions Asked** — State, section, question, options offered
2. **PM Answers** — Answer and how it was interpreted
3. **Agent Inferences** — What was inferred, source, PM confirmation
4. **Forcing Questions (MVP 2)** — FQ ID, question, answer, push-back applied, answer quality, signal observed
5. **Challenges and Pushback** — What was challenged, rationale, PM outcome
6. **Premises (MVP 2)** — Premises presented, PM responses, revisions
7. **Alternatives Considered (MVP 2)** — Approaches, recommendation, PM choice, rationale
8. **Sizing Decisions** — Multi-job detection, proposed split, PM decision
9. **Readiness Scores** — Overall score, status, weakest sections, adversarial issues, action taken
10. **State Transitions** — From, to, reason

---

## Output Format

Persist to `aidlc-docs/prd-drafts/<feature-name>/audit-trail.md` with YAML front matter including: feature, session timestamps, total turns, final score, status, mode detected, forcing questions asked, premises count, alternatives presented.

Sections: Session Summary, Premises Agreed, Approach Chosen, Decision Log, Key Decisions table, Inferences table, Challenges table, Forcing Question Outcomes table.

---

## Learnings That Compound (MVP 2 — from gstack)

At session end, note patterns in a "## Learnings" section:
- PM tendencies (scopes broadly, prefers bullets, etc.)
- Product area patterns (tight coupling, always ask about X)
- Pacing preferences

These are local observations for repeat users of the same product area.

---

## When to Write

- After each turn: append entries in-memory
- On save/resume: write full trail to Git
- On session end: write final version with summary
- On attach: mark outcome as "attached"

---

## Privacy Note

The audit trail is persisted on the PM's feature branch with the same access controls as the PRD itself.
