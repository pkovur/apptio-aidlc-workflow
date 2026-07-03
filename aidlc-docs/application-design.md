# Application Design — PM Agent (Andromeda)

## Document Metadata
- **Feature:** PM Agent (Andromeda)
- **Version:** 1.0
- **Date:** 2026-07-03
- **Status:** Draft — Pending Team Review

---

## 1. Architecture Overview

The PM Agent is a **Kiro agent** — not a standalone service. It lives entirely within the `.kiro/` directory structure of the AIDLC workflow repo and executes via Kiro's built-in Claude model. Its "architecture" is a set of steering files, skills, and templates that orchestrate a multi-turn conversation with the PM.

### 1.1 High-Level Component Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    KIRO IDE                               │
│                                                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │              PM Agent (Steering Files)              │  │
│  │                                                    │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │  │
│  │  │ Context  │  │ Authoring│  │ Readiness &      │ │  │
│  │  │ Discovery│→ │ Engine   │→ │ Attach           │ │  │
│  │  └──────────┘  └──────────┘  └──────────────────┘ │  │
│  │       │              │               │             │  │
│  │       ▼              ▼               ▼             │  │
│  │  ┌──────────────────────────────────────────────┐  │  │
│  │  │           Shared Components                   │  │  │
│  │  │  • PRD Template (versioned)                   │  │  │
│  │  │  • Knowledge Graph Builder                    │  │  │
│  │  │  • Readiness Scorer                           │  │  │
│  │  │  • Audit Trail Writer                         │  │  │
│  │  └──────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────┘  │
│                          │                                │
│         ┌────────────────┼────────────────┐              │
│         ▼                ▼                ▼              │
│  ┌─────────────┐  ┌───────────┐  ┌──────────────┐      │
│  │  Jira MCP   │  │  Git      │  │ Confluence   │      │
│  │  Server     │  │  (local)  │  │ MCP Server   │      │
│  └─────────────┘  └───────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────┘
```

### 1.2 Design Principles

1. **Steering-first** — All agent behavior is defined in markdown steering files. No compiled code.
2. **Stateless between sessions** — State is persisted to Git (.md files). Any session can resume from artifacts.
3. **Human-in-the-loop** — Every state transition requires PM confirmation. The agent proposes; the PM approves.
4. **Progressive disclosure** — Don't overwhelm the PM. Reveal complexity only when needed.
5. **Fail-safe** — If anything goes wrong (Jira down, session timeout), the PM's work is never lost.

---

## 2. Component Design

### 2.1 Agent Workflow (State Machine)

The PM Agent follows a linear state machine with defined transitions:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   START     │────▶│  CONTEXT    │────▶│  REFRAME    │
│             │     │  DISCOVERY  │     │  & CHALLENGE│
└─────────────┘     └─────────────┘     └─────────────┘
                                               │
                                               ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  ATTACH     │◀────│  READINESS  │◀────│  INTERVIEW  │
│  (Jira)     │     │  CHECK      │     │  & DRAFT    │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │
       ▼                   ▼ (score < 7)
┌─────────────┐     ┌─────────────┐
│   DONE      │     │  REFINE     │──── loops back to
└─────────────┘     └─────────────┘     INTERVIEW & DRAFT
```

**State Descriptions:**

| State | What Happens | Exit Condition |
|-------|-------------|----------------|
| START | PM triggers agent ("Using AIDLC" or direct invocation) | Agent loads steering files |
| CONTEXT DISCOVERY | Ask greenfield/brownfield, gather repos, Jira project, build Knowledge Graph | PM confirms context is complete |
| REFRAME & CHALLENGE | Restate the job in one sentence, challenge assumptions, check sizing | PM confirms the framing is correct |
| INTERVIEW & DRAFT | Clustered Q&A (lettered options), aggressive inference, draft PRD sections iteratively | All template sections drafted (or turn limit reached) |
| READINESS CHECK | Score each section, produce overall score, flag weak areas | Score ≥ 7/10 |
| REFINE | Guide PM to strengthen weak sections, re-score | Score reaches ≥ 7/10 |
| ATTACH | Show full draft for review, ask for explicit confirmation, create Jira epic | PM confirms OR saves as draft |
| DONE | PRD attached to Jira, all artifacts committed to Git | Terminal state |

### 2.2 Context Discovery Component

**Purpose:** Build maximum knowledge before authoring begins.

**Inputs:**
- PM's answer to greenfield/brownfield question
- Git repo URLs (brownfield)
- Confluence page links
- Jira epic references
- Help file paths

**Process:**
1. Ask greenfield vs. brownfield
2. Branch:
   - **Brownfield:** Clone/analyze repo(s) → extract architecture, API contracts, data models, component map → persist as Knowledge Graph
   - **Greenfield:** Ask about ecosystem, conventions, stack preferences, related work
3. Ask for Jira project (for eventual epic creation)
4. Persist context artifact to Git

**Output:** `aidlc-docs/prd-drafts/<feature>/knowledge-graph.md`

### 2.3 Authoring Engine Component

**Purpose:** Guide the PM through PRD creation via structured conversation.

**Behavior:**
- References the versioned PRD template as a checklist
- Works through sections in a logical order (not necessarily template order)
- Clusters 3-5 questions per turn with lettered options
- Infers answers from context when possible (states inference, asks for confirmation)
- Redirects technical design/acceptance criteria back to inception
- Coaches the PM on why each section matters
- Tracks turn count (max 15)

**Interview Strategy:**
1. Start with the "what" — Summary, Problem, Job to Be Done
2. Move to "who" — Users, Stakeholders
3. Then "boundaries" — Scope, Out-of-scope, Non-goals
4. Then "how we'd know" — Goals, Success Metrics
5. Then "what could go wrong" — Failure Modes, Dependencies, Assumptions
6. Finally "what's left" — Open Questions, References

### 2.4 PRD Template Component

**Purpose:** The canonical standard for PRD structure and quality.

**Location:** `.kiro/steering/prd-template-v1.0.0.md`

**Structure:**
```yaml
---
template_name: "Apptio PRD Standard"
template_version: "1.0.0"
sections:
  - id: summary
    required: true
    weight: 1.0
  - id: problem_and_context
    required: true
    weight: 1.5
  - id: job_to_be_done
    required: true
    weight: 1.0
  # ... (all 14 sections)
---
```

Each section includes:
- Description of what belongs there
- What "good" looks like (coaching text)
- What "thin" looks like (for readiness scoring)
- Example snippets

### 2.5 Readiness Scorer Component

**Purpose:** Evaluate PRD quality before attach.

**Scoring Algorithm:**
- Each of the 14 template sections is scored 0-10 based on:
  - **Completeness** — Is the section populated with substantive content?
  - **Clarity** — Is the language unambiguous?
  - **Specificity** — Are there concrete details vs. vague statements?
  - **Downstream usefulness** — Will inception be able to act on this?
- Sections have weights (Out-of-Scope and Problem weighted higher)
- Overall score = weighted average across all sections
- Minimum threshold: 7/10

**Output:** Per-section scores with specific guidance on improvement.

### 2.6 Audit Trail Writer Component

**Purpose:** Maintain a decision log throughout the session.

**Captures:**
- Every question asked and the PM's answer
- Every inference made by the agent
- Every challenge/pushback and outcome
- Sizing decisions (split or keep)
- Readiness scores (each time scored)
- Timestamps

**Output:** `aidlc-docs/prd-drafts/<feature>/audit-trail.md`

### 2.7 Jira Integration Component

**Purpose:** Create epic and attach PRD as description.

**Flow:**
1. Agent presents final PRD for review
2. PM explicitly confirms ("yes, create the epic")
3. Agent calls Jira MCP: create epic in selected project
4. Agent sets epic description to PRD content (formatted for Jira)
5. Agent updates PRD metadata (front matter) with epic key
6. If Jira unavailable: save PRD locally, inform PM, offer retry later

**Jira Formatting:**
- Convert markdown to Jira-compatible markup
- Preserve headers, lists, tables, code blocks
- Strip YAML front matter from visible description

---

## 3. File Structure

### 3.1 Agent Implementation (in AIDLC workflow repo)

```
.kiro/
├── steering/
│   ├── pm-agent-main.md              ← Primary steering file (orchestrator)
│   ├── pm-agent-context-discovery.md  ← Context discovery phase instructions
│   ├── pm-agent-authoring.md          ← Authoring engine behavior
│   ├── pm-agent-readiness.md          ← Readiness scoring logic
│   ├── pm-agent-coaching.md           ← Coaching and education prompts
│   └── prd-template-v1.0.0.md        ← Versioned PRD template
├── skills/
│   └── pm-agent/                      ← Optional skill wrapper
│       └── skill.md
└── settings/
    └── mcp.json                       ← MCP server configs (Jira, Confluence)
```

### 3.2 Generated Artifacts (per PM session)

```
aidlc-docs/
└── prd-drafts/
    └── <feature-name>/
        ├── knowledge-graph.md         ← Context discovery output
        ├── prd-draft.md               ← The PRD itself (with YAML front matter)
        ├── audit-trail.md             ← Decision log for this session
        └── readiness-report.md        ← Final readiness scores
```

### 3.3 PRD Output Format

```markdown
---
prd_version: "1.0"
template_version: "1.0.0"
authored_by: "Jane Doe"
authored_date: "2026-07-15"
jira_project: "PROJ"
jira_epic: "PROJ-456"
status: "attached"
greenfield: false
participating_repos:
  - "github.com/Apptio-PNE/studio-ui"
  - "github.com/Apptio-PNE/biit"
readiness_score: 8.2
---

# [Feature Name]

## 1. Summary
...

## 2. Problem and Context
...

[... all 14 sections ...]
```

---

## 4. Steering File Design

### 4.1 Main Orchestrator (`pm-agent-main.md`)

The primary steering file that Kiro loads when the PM Agent is triggered. It:
- Defines the agent's persona and behavior constraints
- Routes to sub-steering files based on current state
- Manages turn counting
- Handles save/resume logic
- Enforces the state machine transitions

**Inclusion:** Always (when PM Agent is active)

### 4.2 Context Discovery (`pm-agent-context-discovery.md`)

**Inclusion:** Conditional — active during CONTEXT DISCOVERY state

Contains:
- The greenfield/brownfield question tree
- Instructions for Knowledge Graph generation
- Prompts for gathering repo/Confluence/Jira context
- Output format for knowledge-graph.md

### 4.3 Authoring Engine (`pm-agent-authoring.md`)

**Inclusion:** Conditional — active during INTERVIEW & DRAFT and REFINE states

Contains:
- Interview clustering strategy
- Lettered option formatting rules
- Inference patterns ("I believe X based on Y — is that correct?")
- Redirect patterns for technical design creep
- Section-by-section authoring prompts
- Reference to the PRD template file

### 4.4 Readiness Scorer (`pm-agent-readiness.md`)

**Inclusion:** Conditional — active during READINESS CHECK state

Contains:
- Scoring rubric per section
- Section weights
- Output formatting (score table + guidance)
- Threshold logic (≥ 7 = pass)
- Improvement suggestions per score band

### 4.5 Coaching (`pm-agent-coaching.md`)

**Inclusion:** Always (available at any point in the flow)

Contains:
- "Why does this matter?" explanations per section
- "What good looks like" examples
- AI-DLC context education (for new PMs)
- Gentle redirects for common anti-patterns

---

## 5. Integration Points

### 5.1 Jira MCP

| Operation | MCP Tool | When |
|-----------|----------|------|
| List projects | `jira_get_all_projects` | Context Discovery (project selection) |
| Get issue details | `jira_get_issue` | Context Discovery (brownfield — reading related epics) |
| Create epic | `jira_create_issue` | Attach phase |
| Search issues | `jira_search` | Context Discovery (finding related work) |

### 5.2 Confluence MCP

| Operation | MCP Tool | When |
|-----------|----------|------|
| Search pages | `confluence_search` | Context Discovery (finding related docs) |
| Get page content | `confluence_get_page` | Context Discovery (reading architecture docs) |

### 5.3 Git (Local)

| Operation | How | When |
|-----------|-----|------|
| Persist draft | Write .md file to `aidlc-docs/prd-drafts/` | After each significant state change |
| Persist Knowledge Graph | Write .md file | After Context Discovery |
| Persist audit trail | Append to audit-trail.md | After each turn |
| Resume session | Read existing artifacts from Git | On session resume |

### 5.4 AI-DLC Workflow Integration

The PM Agent integrates into AI-DLC as the **entry point** for the Inception phase:

```
PM triggers "Using AIDLC" 
    → AI-DLC steering loads
    → Detects: no PRD attached yet
    → Triggers PM Agent steering files
    → PM Agent runs (Context Discovery → Authoring → Attach)
    → PRD attached to Jira epic
    → AI-DLC proceeds with normal Inception (Workspace Detection → Requirements → Design → Units)
```

---

## 6. Session Management

### 6.1 Turn Tracking

- Counter incremented on each PM response (not agent output)
- At turn 12: agent warns "3 turns remaining"
- At turn 15: agent summarizes, offers save/attach/continue-later
- "Continue later" saves current state and all drafts to Git

### 6.2 Save and Resume

**Save:** All current state written to Git artifacts:
- `prd-draft.md` — current draft (even if incomplete)
- `audit-trail.md` — conversation history
- `knowledge-graph.md` — context gathered so far

**Resume:** Agent reads all artifacts from Git, reconstructs state, and continues from where the PM left off. The PM triggers resume with: "Using AIDLC, resume the workflow."

### 6.3 Error Handling

| Scenario | Behavior |
|----------|----------|
| Jira unavailable at attach | Save PRD to Git, inform PM, offer retry later |
| Session interrupted (IDE crash) | All state already persisted to Git; resume picks up |
| PM provides insufficient context | Ask load-bearing questions; capture unknowns as Open Questions in PRD |
| PM writes technical design | Gently redirect; explain inception owns elaboration |
| Turn limit reached, PRD incomplete | Save draft, summarize gaps, offer to continue in new session |

---

## 7. Security and Privacy

- PRDs may contain unreleased product plans — drafts stay within Git (same access controls as code)
- No data leaves the Kiro workspace boundary except via Jira MCP (PM's own token)
- No telemetry or analytics on PRD content
- Knowledge Graph analysis happens locally (within Kiro session) and persists only to the PM's branch

---

## 8. MVP1 vs MVP2 Boundaries

### MVP1 Builds:
- All steering files (main, context-discovery, authoring, readiness, coaching)
- PRD template v1.0.0
- Knowledge Graph builder (full repo analysis for brownfield)
- Readiness scorer with 7/10 threshold
- Jira epic creation
- Git persistence (drafts, Knowledge Graph, audit trail)
- Save/resume capability
- 15-turn session management

### MVP2 Extends:
- Multi-source input (Jira + Confluence + Slack simultaneously)
- Competitive context ingestion
- PRD diff between versions
- Post-inception change request flow (round-trip editing)
- Template gap detection for older PRDs
- Example surfacing from anonymized prior PRDs
