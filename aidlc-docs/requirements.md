# Requirements — PM Agent (Andromeda: PRD Authoring Workspace)

## Document Metadata
- **Feature:** PM Agent (Andromeda)
- **Version:** 1.1
- **Date:** 2026-07-03
- **Author:** pkovur (with AI-DLC Inception)
- **Source PRD:** PRD-Andromeda-PRD-Authoring.docx
- **Status:** Draft — Pending Team Review

---

## 1. Overview

Andromeda (working name — internal only) is a Kiro agent integrated into the AI-DLC workflow that provides a guided PRD authoring workspace for Product Managers. The agent interviews the PM, infers aggressively, drafts a PRD conforming to a versioned standard, checks readiness, and — on explicit PM confirmation — creates a Jira epic with the PRD as its description. This feeds directly into AI-DLC inception.

The agent runs on Claude underneath Kiro. It uses the PM's existing IBM/Apptio SSO identity and their Jira MCP token for epic creation.

---

## 2. Context Discovery Phase (Upfront Knowledge Building)

Before any PRD authoring begins, the PM Agent must conduct a structured context discovery to build maximum knowledge about the product landscape.

### 2.1 Greenfield vs. Brownfield Determination

**REQ-CD-01:** The agent MUST ask the PM at the start whether this is a greenfield (net new) or brownfield (extending existing product) feature.

**REQ-CD-02:** If brownfield, the agent MUST ask for:
- Existing Git repository URL(s)
- Relevant branch or tag references
- Path to help files / documentation within the repo
- Confluence space or page URLs for existing product docs
- Related Jira epics / tickets from prior work
- Existing architecture diagrams or design docs

**REQ-CD-03:** For brownfield projects, the agent MUST build a "Knowledge Graph" from the provided repo(s) — performing a full analysis of code structure, existing architecture, API contracts, data models, and component relationships — before proceeding to PRD authoring. The Knowledge Graph MUST be persisted back to Git as a structured artifact.

**REQ-CD-04:** Even for greenfield projects, the agent MUST ask about:
- The broader product ecosystem this feature lives within
- Existing architecture patterns and conventions in use at the team/org
- Existing Confluence documentation or architectural decision records
- Related Jira epics that provide context
- Technology stack preferences or constraints
- Team composition and expertise

**REQ-CD-05:** The agent MUST persist all gathered context as a structured knowledge artifact (markdown file) in Git so it can be referenced during authoring and by downstream inception. The Knowledge Graph is persisted on the PM's feature branch in the AIDLC workflow repo.

### 2.2 Project Setup Questions

**REQ-CD-06:** The agent MUST ask the PM which Jira project the epic should be created in (from all available projects the PM has access to).

**REQ-CD-07:** The agent MUST ask for participating product repos if known at this stage.

---

## 3. Guided Authoring Experience

### 3.1 Input Acceptance

**REQ-GA-01:** The agent MUST accept starting input in any form: a one-line idea, a Jira ticket reference, a discovery doc, a Confluence page link, pasted text, or an attached file.

**REQ-GA-02 (MVP2):** The agent SHOULD accept multiple sources simultaneously — Jira tickets, Confluence pages, Slack threads, or existing docs — to bootstrap the PRD from existing artifacts.

### 3.2 Problem Reframing and Challenge

**REQ-GA-03:** The agent MUST restate the job back to the PM in one sentence so they can correct it early.

**REQ-GA-04:** The agent MUST actively challenge the PM's framing — pushing back on assumptions, questioning premises, and surfacing what the PM might actually be describing vs. what they said. (Inspired by gstack's "forcing questions" pattern.)

**REQ-GA-05:** The agent MUST exercise judgment on sizing and flag when a request is really two (or more) jobs, proposing a split with clear rationale.

### 3.3 Interview Style

**REQ-GA-06:** The agent MUST interview in clusters (3-5 questions at a time) and infer aggressively rather than asking long sequential forms.

**REQ-GA-07:** The agent MUST present questions with lettered options (A/B/C/D) where applicable, allowing PMs to respond in shorthand (e.g., "1A, 2C, 3B") for fast iteration.

**REQ-GA-08:** The agent MUST ask only load-bearing questions — if it can infer the answer from context, it should state its inference and ask for confirmation rather than asking the question cold.

### 3.4 Intent-Level Guardrails

**REQ-GA-09:** The agent MUST keep the PRD at intent level and gently redirect when the PM starts writing technical design or acceptance criteria, explaining that inception owns elaboration.

**REQ-GA-10:** The agent MUST NOT generate user stories, acceptance criteria, or technical design — those belong to AI-DLC inception.

### 3.5 Competitive and Market Context

**REQ-GA-11 (MVP2):** The agent SHOULD allow the PM to feed in competitor info, market research, or customer discovery docs to ground the PRD in market context.

### 3.6 Session Constraints

**REQ-GA-12:** The authoring conversation MUST cap at 15 turns. After that, the agent summarizes progress and prompts for attach/save/continue-later.

**REQ-GA-13:** The agent MUST allow the PM to save a draft and come back to it (drafts persisted as .md files on the PM's feature branch in the AIDLC workflow repo).

---

## 4. PRD Template and Standard

### 4.1 Versioned Template

**REQ-PT-01:** The PM Agent MUST reference a versioned PRD template file as the canonical standard for authoring. PRDs embed which template version they were authored against.

**REQ-PT-02:** The template MUST be built from best practices across open-source PRD frameworks and industry standards (ChatPRD, gstack, Spec Kit, traditional PM frameworks).

**REQ-PT-03:** The template MUST include (at minimum) these sections:
1. Summary / Overview
2. Problem and Context
3. Job to Be Done
4. Goals and Success Metrics
5. Scope (In-scope / Out-of-scope with rationale)
6. Users and Stakeholders
7. What It Needs to Do (functional intent)
8. Example Scenarios
9. Non-Functional Requirements (performance, security, accessibility, privacy)
10. Failure Modes and Edge Cases
11. Dependencies and Constraints
12. Assumptions
13. Open Questions
14. References

**REQ-PT-04:** The template MUST explicitly prompt for a Non-Functional Requirements section — including performance expectations, security considerations, accessibility needs, and data privacy constraints.

**REQ-PT-05:** The template MUST explicitly prompt for a Failure Modes / Edge Cases section — asking "what happens when things go wrong?" to force the PM to consider unhappy paths.

### 4.2 Template Evolution

**REQ-PT-06:** The template MUST be versioned (semver) so old PRDs can be compared against the standard they were written to.

**REQ-PT-07 (MVP2):** The agent SHOULD be able to identify when a PRD was written against an older template version and highlight gaps vs. the current standard.

---

## 5. Readiness Check

**REQ-RC-01:** The agent MUST run a readiness check before allowing attach, evaluating each section of the PRD for completeness and clarity.

**REQ-RC-02:** The readiness check MUST produce a numeric score (e.g., 7/10) with a per-section breakdown showing which areas are strong vs. thin.

**REQ-RC-03:** The agent MUST say plainly whether the PRD is ready to attach or what is still thin, with specific guidance on how to strengthen weak sections.

**REQ-RC-04:** The agent MUST specifically flag if the Out-of-Scope section is empty or weak — this is where most downstream rework is prevented.

**REQ-RC-05:** A minimum readiness score of **7/10** MUST be met before attach is offered.

---

## 6. Coaching and Education

**REQ-CE-01:** The agent MUST teach the PM *why* each section matters and what "good" looks like, not just produce the document.

**REQ-CE-02:** When the PM asks "why do I need this?" for any section, the agent MUST explain the downstream impact on inception and development.

**REQ-CE-03:** The agent MUST be usable by a PM unfamiliar with AI-DLC — they should reach a passing PRD through the guided flow without a human expert.

**REQ-CE-04 (MVP2):** The agent SHOULD surface examples of well-written sections from previous PRDs (anonymized) when coaching.

---

## 7. Review, Edit, and Diff

**REQ-RE-01:** The agent MUST let the PM review and edit the draft before anything leaves the workspace.

**REQ-RE-02:** The agent MUST present the full draft for review before offering to attach.

**REQ-RE-03 (MVP2):** The agent SHOULD support PRD comparison/diff — showing what changed between draft versions or between the current draft and a previous iteration.

---

## 8. PRD-to-Inception Bridge (Machine-Readable Metadata)

**REQ-BR-01:** The finished PRD MUST include YAML front matter with structured metadata so AI-DLC inception can parse it programmatically. Minimum fields:
```yaml
---
prd_version: "1.0"
template_version: "1.2.0"
authored_by: "<PM name>"
authored_date: "2026-07-03"
jira_project: "PROJ"
jira_epic: "PROJ-123"
status: "attached"
greenfield: true
participating_repos: []
---
```

**REQ-BR-02:** The metadata MUST include enough information for inception to begin without re-asking the PM basic context questions.

---

## 9. Jira Integration

**REQ-JI-01:** The agent MUST create a Jira epic and attach the finished PRD as its description, only after explicit PM confirmation.

**REQ-JI-02:** The agent MUST act as the PM (using their Jira MCP token), not as a service account.

**REQ-JI-03:** The agent MUST work with all Jira projects the PM has access to.

**REQ-JI-04:** The agent MUST NEVER create or modify the epic without explicit PM confirmation.

**REQ-JI-05:** If Jira is unavailable, the agent MUST NOT lose the PM's work. The PRD is saved as a .md file and the PM can retry attach later.

**REQ-JI-06:** The PRD content MUST render cleanly as a Jira epic description (valid Jira markdown/wiki markup).

---

## 10. Persistence and Artifacts

**REQ-PA-01:** In-progress drafts MUST be persisted as .md files in Git on the PM's feature branch in the AIDLC workflow repo.

**REQ-PA-02:** The finished PRD MUST be saved as a .md file in Git regardless of whether Jira attach succeeds.

**REQ-PA-03:** If Jira is unavailable, the PM MUST be able to export/copy the finished PRD as a markdown document.

**REQ-PA-04:** The context discovery artifact (Knowledge Graph) MUST be persisted in Git on the PM's feature branch alongside the PRD. Full repo analysis is performed and stored.

---

## 11. Session Audit Trail

**REQ-AT-01:** At the end of authoring, the agent MUST produce a decision log showing:
- What was asked
- What the PM answered
- What was inferred by the agent
- What was challenged and the outcome
- Sizing decisions (split or keep)
- Readiness scores over time

**REQ-AT-02:** The audit trail MUST be saved as a separate .md file alongside the PRD for stakeholder review.

**REQ-AT-03:** The audit trail provides traceability for stakeholders who were not in the authoring session.

---

## 12. Agent Behavior Constraints

**REQ-AB-01:** The agent drafts and proposes; the PM edits and approves. Authoring is collaborative and reversible.

**REQ-AB-02:** The agent MUST NOT invent details the PM did not provide. Genuine unknowns are captured as open questions, not fabricated.

**REQ-AB-03:** The agent MUST NOT pre-write technical design or acceptance criteria.

**REQ-AB-04:** If the PM requests something outside this agent's job (e.g., "write the code", "generate stories"), the agent declines politely and points to where that happens (inception).

**REQ-AB-05:** The agent MUST ask up to a few load-bearing questions when information is insufficient, rather than guessing.

---

## 13. Platform and Architecture

**REQ-PL-01:** The PM Agent is a Kiro agent — implemented as steering files and/or skills within the `.kiro/` directory structure.

**REQ-PL-02:** The agent runs on Claude via Kiro's built-in model. No separate AI platform infrastructure required.

**REQ-PL-03:** Authentication uses existing IBM/Apptio SSO (no new auth flow).

**REQ-PL-04:** Jira integration uses the existing Jira MCP server connection configured in the user's Kiro workspace.

**REQ-PL-05:** The agent is integrated into the AI-DLC workflow as the entry point — when a PM triggers AI-DLC, the PM Agent phase runs first to produce the PRD before inception proceeds.

---

## 14. Quality Expectations

**REQ-QE-01:** Speed — fast enough to feel like a conversation (interactive, iterative).

**REQ-QE-02:** Reliability — if Jira is down, the PM still walks away with a finished PRD.

**REQ-QE-03:** Privacy — PRDs can contain unreleased product plans. The workspace must respect existing access controls and keep drafts within approved boundaries.

**REQ-QE-04:** Cost — sessions are interactive and multi-turn; keep per-session cost reasonable (max 15 turns as a natural limiter).

---

## 15. MVP Phasing

### MVP 1 (First Release)

| ID | Requirement | Summary |
|----|-------------|---------|
| REQ-CD-01 to CD-07 | Context Discovery | Greenfield/brownfield detection, full knowledge building, Knowledge Graph persisted to Git, Jira project selection |
| REQ-GA-01 | Single-source input | Accept one-line idea, ticket, doc, or file |
| REQ-GA-03 to GA-06 | Reframing & challenge | Restate job, challenge framing, sizing judgment, clustered interview |
| REQ-GA-07 to GA-10 | Interview style & guardrails | Lettered Q&A, aggressive inference, intent-level enforcement |
| REQ-GA-12 to GA-13 | Session constraints | 15-turn cap, save/resume drafts |
| REQ-PT-01 to PT-06 | Versioned PRD template | Build and reference template, all core sections including NFRs and failure modes |
| REQ-RC-01 to RC-05 | Readiness check | Numeric scoring (min 7/10), per-section breakdown |
| REQ-CE-01 to CE-03 | Coaching | Explain why, teach the bar, support new PMs |
| REQ-RE-01 to RE-02 | Review & edit | Full draft review before attach |
| REQ-BR-01 to BR-02 | Bridge metadata | YAML front matter for inception handoff |
| REQ-JI-01 to JI-06 | Jira integration | Epic creation, PM token, all projects, confirmation gate, fallback |
| REQ-PA-01 to PA-04 | Persistence | Git-based .md artifacts on PM's feature branch, Knowledge Graph persisted |
| REQ-AT-01 to AT-03 | Audit trail | Decision log, saved alongside PRD |
| REQ-AB-01 to AB-05 | Behavior constraints | No fabrication, no technical design, graceful decline |
| REQ-PL-01 to PL-05 | Platform | Kiro agent, Claude, SSO, Jira MCP, AI-DLC entry point |
| REQ-QE-01 to QE-04 | Quality | Speed, reliability, privacy, cost |

### MVP 2 (Follow-on Release)

| ID | Requirement | Summary |
|----|-------------|---------|
| REQ-GA-02 | Multi-source input | Accept Jira tickets, Confluence pages, Slack threads simultaneously |
| REQ-GA-11 | Competitive context | Feed in competitor info and market research |
| REQ-PT-07 | Template gap detection | Identify PRDs written against older template versions |
| REQ-CE-04 | Example surfacing | Show anonymized examples of well-written sections |
| REQ-RE-03 | PRD diff/comparison | Show changes between draft versions |
| REQ-MVP2-01 | Post-inception edit path | Support change requests after PRD has entered inception (round-trip editing) |

---

## 16. Resolved Questions

| # | Question | Answer | Decided By | Date |
|---|----------|--------|------------|------|
| 1 | Does v1 adopt the "Andromeda" name publicly or is it an internal working name? | Working name only (internal) | PM (pkovur) | 2026-07-03 |
| 2 | How does the originating engineer (Sauravmoy Sarkar) stay involved? | No specific involvement requirement for the build phase | PM (pkovur) | 2026-07-03 |
| 3 | Where do in-progress drafts live in Git? | On the PM's feature branch in the AIDLC workflow repo | PM (pkovur) | 2026-07-03 |
| 4 | Does v1 need an edit path after PRD enters inception? | MVP1: one-way handoff. MVP2: extend for change requests | PM (pkovur) | 2026-07-03 |
| 5 | Knowledge Graph depth for large brownfield repos? | Full analysis. Knowledge Graph persisted back to Git | PM (pkovur) | 2026-07-03 |
| 6 | Minimum viable readiness score for attach? | 7/10 | PM (pkovur) | 2026-07-03 |

---

## 17. References

- PRD-Andromeda-PRD-Authoring.docx (source PRD)
- [gstack by Garry Tan](https://github.com/garrytan/gstack) — `/office-hours` forcing questions pattern
- [ChatPRD](https://chatprd.ai/) — PRD coaching and documentation leader
- [Product Manager Skills](https://github.com/deanpeters/Product-Manager-Skills) — OSS PM skills for Claude Code
- [ralph/snarktank PRD skill](https://github.com/snarktank/ralph/blob/main/skills/prd/SKILL.md) — Lettered Q&A workflow
- [Spec Kit](https://www.codewithseb.com/blog/spec-driven-development-spec-kit-claude-code-guide) — Spec-driven development
- [PM Agents Compared (2026)](https://rywalker.com/research/product-management-agents) — Market analysis
- [Kiro spec-driven development](https://kiro.dev/blog/from-chat-to-specs-deep-dive/) — Kiro SDD methodology
- AI-DLC Onboarding Guide (AIDLC methodology)
