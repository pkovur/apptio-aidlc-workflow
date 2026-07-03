# Decision Log — PM Agent (Andromeda)

## Document Metadata
- **Feature:** PM Agent (Andromeda)
- **Started:** 2026-07-03
- **Last Updated:** 2026-07-03

---

## Decision Record

### DEC-001: Application Type
- **Date:** 2026-07-03
- **Question:** Where does Andromeda live? Standalone web app, Slack bot, or IDE extension?
- **Decision:** Kiro agent integrated into AI-DLC workflow
- **Rationale:** Let PMs open Kiro, run AI-DLC, and the workflow triggers the PM agent. Minimizes new infrastructure. Other interface options can be explored later.
- **Decided by:** PM (pkovur)

### DEC-002: AI Platform
- **Date:** 2026-07-03
- **Question:** What AI platform runs the agent?
- **Decision:** Claude via Kiro's built-in model — no separate AI platform needed
- **Rationale:** If it runs under Kiro, no separate platform is required. Uses Claude underneath.
- **Decided by:** PM (pkovur)

### DEC-003: Authentication
- **Date:** 2026-07-03
- **Question:** How do PMs authenticate?
- **Decision:** Reuse existing IBM/Apptio SSO
- **Rationale:** No new auth flow needed. Existing identity infrastructure is sufficient.
- **Decided by:** PM (pkovur)

### DEC-004: PRD-Writer Skill
- **Date:** 2026-07-03
- **Question:** Is there an existing prd-writer skill, or is it net new?
- **Decision:** Net new — will be built as part of this epic. Lives as a Kiro agent integrated with AI-DLC workflow.
- **Rationale:** No existing skill exists today. This IS the PM Agent being built.
- **Decided by:** PM (pkovur)

### DEC-005: Draft Persistence
- **Date:** 2026-07-03
- **Question:** Where do in-progress PRD drafts live?
- **Decision:** Persist in Git as .md artifacts on the PM's feature branch in the AIDLC workflow repo.
- **Rationale:** Aligns with AI-DLC's artifact-in-Git philosophy. Version controlled and accessible. Each PM's work lives on their own branch.
- **Decided by:** PM (pkovur)

### DEC-006: Jira Project Scope
- **Date:** 2026-07-03
- **Question:** Which Jira projects can epics be created in?
- **Decision:** All Jira projects the PM has access to
- **Rationale:** No artificial restriction for v1. Access controls handled by Jira permissions.
- **Decided by:** PM (pkovur)

### DEC-007: Jira Project Selection
- **Date:** 2026-07-03
- **Question:** How does the agent know which project to create the epic in?
- **Decision:** Ask the PM at the beginning of the agent workflow
- **Rationale:** Simple and explicit. PM chooses; agent doesn't guess.
- **Decided by:** PM (pkovur)

### DEC-008: Jira Permissions Model
- **Date:** 2026-07-03
- **Question:** Does the agent use the PM's token or a service account?
- **Decision:** Uses the PM's Jira token already provided in the MCP server
- **Rationale:** Agent acts as the PM. No service account complexity. Respects existing permissions.
- **Decided by:** PM (pkovur)

### DEC-009: Session Turn Limit
- **Date:** 2026-07-03
- **Question:** Should sessions be time-bounded or turn-bounded?
- **Decision:** Max 15 turns
- **Rationale:** Keeps sessions focused and cost-reasonable. After 15 turns, summarize and prompt for action.
- **Decided by:** PM (pkovur)

### DEC-010: Export Format
- **Date:** 2026-07-03
- **Question:** What format for PRD export when Jira is unavailable?
- **Decision:** Markdown (.md) file
- **Rationale:** Consistent with Git persistence. Renders well in Jira, Confluence, and IDEs.
- **Decided by:** PM (pkovur)

### DEC-011: Additional Requirements from Research
- **Date:** 2026-07-03
- **Question:** Which additional capabilities (A-L) identified from market research should be included?
- **Decision:** ALL (A through L) included, phased into MVP 1 and MVP 2
- **Phasing:**
  - MVP 1: B (lettered Q&A), C (reframe challenge), E (readiness scoring), F (bridge metadata), H (coaching), J (NFRs), K (failure modes), L (audit trail), A (template — built from open sources)
  - MVP 2: D (competitive context intake), G (multi-source input), I (PRD diff/comparison)
- **Rationale:** All requirements are valuable. Phasing allows focused first delivery while planning for the full vision.
- **Decided by:** PM (pkovur)

### DEC-012: Context Discovery — Knowledge Graph
- **Date:** 2026-07-03
- **Question:** Should the agent gather context before authoring begins?
- **Decision:** Yes. Agent must conduct structured context discovery for BOTH greenfield and brownfield projects.
- **Details:**
  - Brownfield: ask for Git repo(s), Confluence pages, earlier Jira epics, help file paths → build Knowledge Graph from existing code
  - Greenfield: still ask about product ecosystem, existing architecture, team conventions, related Jira work
  - All context persisted as a structured artifact
- **Rationale:** Maximum knowledge before kickoff produces better PRDs. The quality of inception depends on the quality of context, not just the PRD text.
- **Decided by:** PM (pkovur)

### DEC-013: PRD Template Source
- **Date:** 2026-07-03
- **Question:** Where does the PRD template come from?
- **Decision:** Build from open-source best practices on the internet. Referenced as a guiding factor by the PM Agent.
- **Sources to draw from:** ChatPRD, gstack, ralph/snarktank, Spec Kit, traditional PM frameworks, OpenAI PRD template, Leanware patterns
- **Rationale:** No need to reinvent. Curate the best from existing proven patterns and make it the Apptio standard.
- **Decided by:** PM (pkovur)

### DEC-014: Public Name
- **Date:** 2026-07-03
- **Question:** Does v1 adopt the "Andromeda" name publicly or is it an internal working name?
- **Decision:** Working name only — internal use. Not a public-facing brand for v1.
- **Rationale:** Keep naming simple during build phase. Public branding can be decided later.
- **Decided by:** PM (pkovur)

### DEC-015: Originating Engineer Involvement
- **Date:** 2026-07-03
- **Question:** How does Sauravmoy Sarkar stay involved as this moves to formal build?
- **Decision:** No specific involvement requirement for the build phase.
- **Rationale:** The PRD formalizes and carries forward the ideation. No ongoing gating or approval role required from the originator during construction.
- **Decided by:** PM (pkovur)

### DEC-016: Knowledge Graph — Depth and Persistence
- **Date:** 2026-07-03
- **Question:** How does the Knowledge Graph handle large brownfield repos? Full analysis or targeted?
- **Decision:** Full analysis. The Knowledge Graph is persisted back to Git on the PM's feature branch.
- **Rationale:** Full analysis gives the richest context for PRD authoring and downstream inception. Persisting to Git ensures it's available for the team and survives session boundaries.
- **Decided by:** PM (pkovur)

### DEC-017: Minimum Readiness Score
- **Date:** 2026-07-03
- **Question:** What is the minimum readiness score required before attach?
- **Decision:** 7/10
- **Rationale:** Balances quality gate with pragmatism. 7/10 means all critical sections are covered, even if some could be richer. Below 7, the PRD is too thin for inception to start cleanly.
- **Decided by:** PM (pkovur)

### DEC-018: Post-Inception Edit Path
- **Date:** 2026-07-03
- **Question:** Does v1 need an edit path after a PRD has entered inception?
- **Decision:** MVP1: one-way handoff (no edit path). MVP2: extend to support change requests after inception has started.
- **Rationale:** One-way handoff is simpler and acceptable for v1. Real-world usage will reveal how often PMs need to update a PRD mid-inception, informing the MVP2 design.
- **Decided by:** PM (pkovur)

---

## Process Decisions

### PROC-001: Development Methodology
- **Date:** 2026-07-03
- **Methodology:** AI-DLC (AI Development Lifecycle)
- **Repo:** pkovur/apptio-aidlc-workflow (fork of Apptio-PNE/apptio-aidlc-workflow)
- **Branch:** feature/pm-agent
- **Phase:** Inception (mob session)

### PROC-002: AIDLC Steps Completed
- **Date:** 2026-07-03
- **Step 1:** Fork created — pkovur/apptio-aidlc-workflow ✅
- **Step 2:** Feature branch created — feature/pm-agent ✅
- **Step 3:** Workspace initialized — aidlc-docs/ directory ready ✅
- **Step 4:** PRD ingested and analyzed ✅
- **Step 5:** Requirements analysis — clarifying questions asked and answered ✅
- **Step 5 (continued):** Market research conducted, additional requirements identified and approved ✅
- **Step 5 (continued):** All open questions resolved ✅

---

## All Questions Resolved

No pending decisions remain. Requirements document is ready for team approval.
