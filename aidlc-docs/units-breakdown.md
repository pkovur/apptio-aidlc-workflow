# Units Breakdown — PM Agent (Andromeda)

## Document Metadata
- **Feature:** PM Agent (Andromeda)
- **Version:** 1.0
- **Date:** 2026-07-03
- **Developers:** 1 (sequential)
- **Target Repo:** pkovur/apptio-aidlc-workflow (feature/pm-agent)
- **Estimated Total:** 5 units (~5 sprints)

---

## Unit Sequence

```
Unit 1: PRD Template & Foundation
    ↓
Unit 2: Context Discovery Steering
    ↓
Unit 3: Authoring Engine Steering
    ↓
Unit 4: Readiness Scorer & Coaching
    ↓
Unit 5: Jira Integration & End-to-End Flow
```

---

## Unit 1: PRD Template & Agent Foundation

**Scope:** Build the versioned PRD template and the main orchestrator steering file that defines the agent's persona, state machine, and routing logic.

**Deliverables:**
- `.kiro/steering/prd-template-v1.0.0.md` — The canonical PRD template with all 14 sections, scoring weights, "what good looks like" examples, and coaching text
- `.kiro/steering/pm-agent-main.md` — Main orchestrator steering file defining:
  - Agent persona and behavior constraints
  - State machine (states, transitions, exit conditions)
  - Turn counter logic (max 15)
  - Save/resume instructions (read from/write to Git artifacts)
  - Routing to sub-steering files based on state
  - Error handling behaviors
- `aidlc-docs/prd-drafts/.gitkeep` — Directory structure for artifact output

**Acceptance Criteria:**
- [ ] PRD template contains all 14 sections with descriptions, examples, and scoring rubrics
- [ ] Template has YAML front matter with version metadata (semver: 1.0.0)
- [ ] Main steering file defines the complete state machine (START → CONTEXT DISCOVERY → REFRAME → INTERVIEW → READINESS → ATTACH → DONE)
- [ ] Behavior constraints from requirements are encoded (no fabrication, no technical design, intent-level enforcement)
- [ ] Turn counting logic documented (warn at 12, cap at 15)
- [ ] Save/resume behavior defined (artifacts persist to `aidlc-docs/prd-drafts/<feature>/`)
- [ ] Agent can be triggered in Kiro and responds with correct persona

**Dependencies:** None (first unit)

---

## Unit 2: Context Discovery Steering

**Scope:** Build the steering file that guides the initial context-gathering phase — greenfield/brownfield detection, Knowledge Graph generation, and project setup.

**Deliverables:**
- `.kiro/steering/pm-agent-context-discovery.md` — Context discovery steering file defining:
  - Greenfield vs. brownfield question flow
  - Brownfield: repo analysis prompts, Knowledge Graph generation instructions
  - Greenfield: ecosystem/architecture/conventions questions
  - Jira project selection flow
  - Output format specification for knowledge-graph.md
- Sample output: `aidlc-docs/prd-drafts/sample/knowledge-graph.md` — Example Knowledge Graph artifact

**Acceptance Criteria:**
- [ ] Agent asks greenfield/brownfield as first question
- [ ] Brownfield flow asks for: Git repos, branches, help files, Confluence pages, Jira epics, architecture docs
- [ ] Brownfield flow includes instructions for full repo analysis (code structure, APIs, data models, components)
- [ ] Greenfield flow asks for: product ecosystem, existing architecture patterns, stack preferences, team composition, related Jira work
- [ ] Agent asks which Jira project the epic should be created in
- [ ] Knowledge Graph output format is defined and includes structured sections (architecture, API contracts, data models, component relationships)
- [ ] Knowledge Graph is persisted to `aidlc-docs/prd-drafts/<feature>/knowledge-graph.md`
- [ ] Context discovery transitions cleanly to REFRAME & CHALLENGE state

**Dependencies:** Unit 1 (main orchestrator must route to this file)

---

## Unit 3: Authoring Engine Steering

**Scope:** Build the core authoring experience — problem reframing, clustered Q&A, lettered options, aggressive inference, and PRD draft generation.

**Deliverables:**
- `.kiro/steering/pm-agent-authoring.md` — Authoring engine steering file defining:
  - Problem reframing ("restate the job in one sentence") instructions
  - Challenge/pushback patterns (assumptions, premises, sizing)
  - Sizing judgment (detect multi-job requests, propose splits)
  - Interview clustering strategy (3-5 questions per turn)
  - Lettered option formatting rules (A/B/C/D)
  - Aggressive inference patterns ("I believe X based on Y — confirm?")
  - Section-by-section authoring guidance (which sections first, logical ordering)
  - Intent-level guardrails (redirect patterns for technical design creep)
  - Draft generation format (references PRD template)
- `.kiro/steering/pm-agent-audit-writer.md` — Audit trail format and capture instructions

**Acceptance Criteria:**
- [ ] Agent restates the PM's job in one sentence before proceeding
- [ ] Agent actively challenges framing (at least 2-3 pushback patterns defined)
- [ ] Agent detects multi-job requests and proposes splits with rationale
- [ ] Questions are presented in clusters of 3-5 with lettered options (A/B/C/D)
- [ ] Agent infers answers from context and asks for confirmation rather than asking cold
- [ ] Agent redirects technical design/acceptance criteria back to inception
- [ ] PRD sections are drafted iteratively (not all at once at the end)
- [ ] Audit trail captures: questions asked, PM answers, inferences, challenges, outcomes
- [ ] Audit trail persisted to `aidlc-docs/prd-drafts/<feature>/audit-trail.md`

**Dependencies:** Unit 1 (template for section structure), Unit 2 (context feeds into authoring)

---

## Unit 4: Readiness Scorer & Coaching

**Scope:** Build the readiness evaluation and coaching components — scoring rubric, per-section evaluation, improvement guidance, and educational coaching mode.

**Deliverables:**
- `.kiro/steering/pm-agent-readiness.md` — Readiness scorer steering file defining:
  - Scoring rubric per section (0-10 scale)
  - Section weights (Out-of-Scope, Problem weighted higher)
  - Overall score calculation (weighted average)
  - Threshold logic (≥ 7 = pass, < 7 = refine)
  - Per-section improvement suggestions by score band
  - Output format for readiness-report.md
- `.kiro/steering/pm-agent-coaching.md` — Coaching steering file defining:
  - "Why does this matter?" explanations per PRD section
  - "What good looks like" examples per section
  - AI-DLC context education for new PMs
  - Gentle redirect patterns for common anti-patterns
  - Progressive disclosure (don't overwhelm; teach when asked)

**Acceptance Criteria:**
- [ ] Each of 14 PRD sections has a defined scoring rubric (0-10)
- [ ] Sections have differentiated weights (Out-of-Scope and Problem are higher)
- [ ] Overall score is weighted average across sections
- [ ] Score ≥ 7/10 triggers transition to ATTACH state
- [ ] Score < 7/10 triggers REFINE loop with specific guidance on weak sections
- [ ] Agent provides improvement suggestions per section ("To raise this from 5 to 7, consider...")
- [ ] Coaching explains downstream impact on inception for each section
- [ ] Agent can answer "why do I need this?" for any section at any point
- [ ] Readiness report persisted to `aidlc-docs/prd-drafts/<feature>/readiness-report.md`
- [ ] New PMs can complete a passing PRD without external help

**Dependencies:** Unit 1 (template with section weights), Unit 3 (draft to score)

---

## Unit 5: Jira Integration & End-to-End Flow

**Scope:** Wire up Jira epic creation, final review flow, YAML front matter generation, and validate the complete end-to-end experience from trigger to attached epic.

**Deliverables:**
- Updates to `.kiro/steering/pm-agent-main.md` — ATTACH state implementation:
  - Full draft presentation for review
  - Explicit confirmation gate ("yes, create the epic")
  - Jira MCP call to create epic
  - PRD formatting for Jira description (markdown → Jira markup)
  - YAML front matter generation with epic key
  - Error handling (Jira unavailable → save and retry later)
- Integration testing script or validation checklist:
  - End-to-end flow walkthrough
  - Save/resume verification
  - Jira error handling
  - Turn limit behavior

**Acceptance Criteria:**
- [ ] Agent presents full PRD for review before offering attach
- [ ] Agent asks for explicit PM confirmation before creating epic
- [ ] Agent creates Jira epic in the selected project via MCP
- [ ] Epic description contains the PRD content formatted for Jira
- [ ] YAML front matter updated with epic key, status "attached", readiness score
- [ ] If Jira unavailable: PRD saved to Git, PM informed, retry offered
- [ ] Agent never creates/modifies epic without explicit confirmation
- [ ] Complete end-to-end flow works: trigger → context → reframe → interview → readiness → attach → done
- [ ] Save/resume works: PM can quit and return, agent picks up from artifacts
- [ ] Turn limit respected: at 15 turns, summarize and offer save/attach/continue-later
- [ ] All artifacts committed to Git at end of session

**Dependencies:** Units 1-4 (all prior components must be in place)

---

## Summary

| Unit | Name | Sprint | Key Output |
|------|------|--------|------------|
| 1 | PRD Template & Foundation | Sprint 1 | `prd-template-v1.0.0.md`, `pm-agent-main.md` |
| 2 | Context Discovery | Sprint 2 | `pm-agent-context-discovery.md` |
| 3 | Authoring Engine | Sprint 3 | `pm-agent-authoring.md`, `pm-agent-audit-writer.md` |
| 4 | Readiness & Coaching | Sprint 4 | `pm-agent-readiness.md`, `pm-agent-coaching.md` |
| 5 | Jira & End-to-End | Sprint 5 | Jira integration, full flow validation |

---

## Notes

- All units deliver to the same repo: `pkovur/apptio-aidlc-workflow` on branch `feature/pm-agent`
- Each unit is independently testable in Kiro (invoke the agent and exercise that phase)
- Unit 5 is the integration unit — validates everything works together
- After Unit 5, the agent is ready for team pilot testing
