---
template_name: "Apptio PRD Standard"
template_version: "1.0.0"
description: "Canonical PRD template for AI-DLC. Built from best practices across ChatPRD, gstack, Spec Kit, ralph/snarktank, and traditional PM frameworks."
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
  - id: goals_and_success_metrics
    required: true
    weight: 1.0
  - id: scope
    required: true
    weight: 1.5
  - id: users_and_stakeholders
    required: true
    weight: 1.0
  - id: what_it_needs_to_do
    required: true
    weight: 1.2
  - id: example_scenarios
    required: true
    weight: 1.0
  - id: non_functional_requirements
    required: true
    weight: 1.0
  - id: failure_modes_and_edge_cases
    required: true
    weight: 1.2
  - id: dependencies_and_constraints
    required: true
    weight: 0.8
  - id: assumptions
    required: true
    weight: 0.8
  - id: open_questions
    required: false
    weight: 0.5
  - id: references
    required: false
    weight: 0.3
---

# PRD Template v1.0.0

> This is the canonical PRD template for the PM Agent (Andromeda). The agent uses this template to guide PMs through authoring and to score readiness. Each section includes guidance on what belongs there, what "good" looks like, and what "thin" looks like.

---

## 1. Summary

**What belongs here:** A 2-4 sentence overview of what is being proposed. State what it is, why it matters now, and whether it is new or a change to something existing.

**What good looks like:**
> "This proposes building a guided PRD authoring workspace for Product Managers, integrated into the AI-DLC workflow as a Kiro agent. It matters now because PRD quality is the entry point to inception, and inconsistency across authors creates rework downstream. This is a net new application (working name: Andromeda), not a feature added to an existing product."

**What thin looks like:**
> "We want to build a PRD tool." (No context, no "why now", no framing.)

**Scoring rubric:**
- 9-10: Clear what, why-now, new-vs-change in ≤4 sentences
- 7-8: Covers the basics but missing one dimension (e.g., no "why now")
- 4-6: Vague or too long; reader still unsure what this is
- 1-3: Missing or single sentence with no context

---

## 2. Problem and Context

**What belongs here:** The pain being solved. Who feels it, why it exists today, and why existing solutions are insufficient. Include what has changed that makes this the right time.

**What good looks like:**
> Describes the specific pain (e.g., "PRD quality varies by author"), explains why it exists structurally (e.g., "expertise lives in a process, not in every PM's head"), names who feels it most, and explains the timing (e.g., "AI-DLC is already in flight and PRDs are its entry point").

**What thin looks like:**
> "PMs need better tools." (No specifics about the pain, no structural explanation, no timing.)

**Coaching — why this matters:**
> This section tells inception what problem is being solved. If this is thin, inception will spend its first rounds discovering the problem rather than elaborating the solution. Strong problem statements prevent 2-3 rounds of clarification.

**Scoring rubric:**
- 9-10: Specific pain, structural root cause, timing justification, named affected users
- 7-8: Good pain description but missing root cause or timing
- 4-6: Generic problem statement without specifics
- 1-3: Missing or one-liner

---

## 3. Job to Be Done

**What belongs here:** A single sentence in the format: "As a [user], I want [outcome] so that [benefit]." This is the atomic job — one job, clearly stated.

**What good looks like:**
> "As a product manager, I want to author a clear, right-sized PRD with AI guidance and attach it to a Jira epic, so that AI-DLC inception starts from strong, consistent intent."

**What thin looks like:**
> "Help PMs write PRDs." (Not in JTBD format, no user, no outcome, no benefit.)

**Coaching — why this matters:**
> The JTBD is the single sentence the agent will restate back to the PM for confirmation. If this is unclear, the entire PRD may be solving the wrong problem. One PRD = one job. If you have two jobs, you need two PRDs.

**Scoring rubric:**
- 9-10: Single sentence, correct format, one job, clear benefit
- 7-8: Correct format but slightly compound or benefit is vague
- 4-6: Multiple jobs in one sentence, or missing format
- 1-3: Missing or not a JTBD

---

## 4. Goals and Success Metrics

**What belongs here:** 3-5 measurable goals. For each goal, state how you would know it is achieved. It's acceptable to set targets later once you have a baseline — but the measurement approach must be defined.

**What good looks like:**
> | Goal | How We'd Know |
> |------|---------------|
> | PMs produce ready PRDs faster | Median time from start to attach, tracked over time |
> | PRD quality goes up | Share of PRDs passing readiness check; fewer inception clarification rounds |
> | New PMs become self-sufficient | A new PM reaches a passing PRD without a human expert |

**What thin looks like:**
> "Make things better." (No measurement, no specificity.)

**Coaching — why this matters:**
> Goals tell inception what success looks like. Without them, there's no way to validate the design. Inception uses these to derive acceptance criteria for the feature.

**Scoring rubric:**
- 9-10: 3-5 goals, each with a measurement approach, specific and achievable
- 7-8: Goals present but some lack measurement clarity
- 4-6: Vague goals or missing measurements
- 1-3: Missing or aspirational without substance

---

## 5. Scope

**What belongs here:** Two subsections: **In Scope** (what this PRD covers) and **Out of Scope** (what it explicitly does NOT cover, with rationale for each exclusion). Out-of-scope items should note where they are tracked if deferred.

**What good looks like:**
> **In scope:** Guided authoring, sizing guidance, readiness check, Jira epic creation, save/resume.
>
> **Out of scope:**
> | Item | Why Not Now | Where Tracked |
> |------|------------|---------------|
> | Visual SDLC lifecycle tracking | Distinct job, different users | Follow-on epic |
> | Generating user stories | Inception owns elaboration | AI-DLC |
> | Non-PM authors | Changes the experience | Not planned for v1 |

**What thin looks like:**
> "Everything related to PRDs." (No boundaries, no out-of-scope.)

**Coaching — why this matters:**
> The Out-of-Scope section is where most inception rework is prevented. If this is empty, inception will discover scope boundaries mid-design — which is expensive. The agent will specifically flag an empty Out-of-Scope as a blocker.

**Scoring rubric:**
- 9-10: Clear in-scope list + 3+ out-of-scope items with rationale and tracking
- 7-8: In-scope clear but out-of-scope is thin (1-2 items, no rationale)
- 4-6: Only in-scope listed, no out-of-scope
- 1-3: Missing or vague ("everything we need")

---

## 6. Users and Stakeholders

**What belongs here:** A table of who uses this, who is affected by it, and what each person/role cares about. Distinguish primary users from downstream consumers and decision-makers.

**What good looks like:**
> | Who | Relationship | What They Care About |
> |-----|-------------|---------------------|
> | PMs across the suite | Primary users | Getting to a strong PRD fast |
> | Engineers in inception | Downstream consumers | Clear intent and boundaries |
> | Engineering leadership | Standard setters | Consistency across epics |

**What thin looks like:**
> "Product managers." (No table, no roles, no concerns.)

**Scoring rubric:**
- 9-10: 3+ distinct roles with relationship type and specific concerns
- 7-8: Roles listed but concerns are generic
- 4-6: Only primary user mentioned
- 1-3: Missing or single word

---

## 7. What It Needs to Do

**What belongs here:** A bulleted list of capabilities at intent level. Each bullet describes what the system must enable or provide, without specifying how (no technical design, no acceptance criteria). Think "job stories" not "user stories."

**What good looks like:**
> - Let a PM start from whatever they have: an idea, a ticket, or a doc
> - Restate the job back to the PM so they can correct early
> - Exercise judgment on sizing and flag when a request is really two jobs
> - Show plainly whether the PRD is ready or what is still thin
> - Create the epic in Jira only after PM confirms

**What thin looks like:**
> "Make a PRD generator." (One bullet, no specifics.)

**Coaching — why this matters:**
> This section becomes the functional requirement source for inception. Each bullet here may become one or more user stories. More bullets = more precision for the engineering team.

**Scoring rubric:**
- 9-10: 5+ specific capability bullets at intent level, no technical design
- 7-8: Good bullets but some creep into "how" rather than "what"
- 4-6: Too few bullets (1-3) or too vague
- 1-3: Missing or single statement

---

## 8. Example Scenarios

**What belongs here:** 2-4 concrete scenarios showing how a user would interact with the feature. Each scenario has: Situation, User Action, Expected Outcome. These are NOT acceptance criteria — they illustrate the intent.

**What good looks like:**
> **Scenario 1: Idea to attached epic**
> Situation: A PM has a one-line idea and opens the workspace.
> User should be able to: answer clustered questions, watch a draft take shape, refine it, confirm.
> Should see: readiness check passes, Jira epic created, PRD attached.

**What thin looks like:**
> "PM writes a PRD and attaches it." (No situation, no detail.)

**Scoring rubric:**
- 9-10: 3+ scenarios with situation/action/outcome, covering happy path + edge cases
- 7-8: 2 scenarios but only happy paths
- 4-6: 1 scenario or very generic
- 1-3: Missing

---

## 9. Non-Functional Requirements

**What belongs here:** Expectations around performance, security, accessibility, privacy, cost, and reliability that apply system-wide (not to a specific feature). If a category doesn't apply, state that explicitly.

**What good looks like:**
> - **Speed:** Fast enough to feel conversational (sub-5s response per turn)
> - **Reliability:** If Jira is down, PM still walks away with a finished PRD
> - **Privacy:** PRDs may contain unreleased plans; respect existing access controls
> - **Cost:** Keep per-session cost reasonable (max 15 turns)
> - **Accessibility:** N/A for v1 (agent is text-based in IDE)

**What thin looks like:**
> "Should be fast and secure." (No specifics, no categories.)

**Coaching — why this matters:**
> NFRs prevent inception from discovering constraints mid-build. Performance, security, and privacy requirements especially affect architecture choices. Stating them upfront saves redesign.

**Scoring rubric:**
- 9-10: 4+ categories addressed with specific expectations
- 7-8: Categories mentioned but expectations vague
- 4-6: 1-2 categories only
- 1-3: Missing or generic

---

## 10. Failure Modes and Edge Cases

**What belongs here:** What happens when things go wrong? Cover: system failures, user errors, boundary conditions, and adversarial inputs. For each, state the expected behavior.

**What good looks like:**
> - **Jira unavailable:** Never lose PM's work; save PRD locally, offer retry
> - **Insufficient info from PM:** Ask load-bearing questions; capture unknowns as Open Questions
> - **PM writes technical design:** Redirect gently; explain inception owns elaboration
> - **Request outside scope:** Decline politely, point to where that happens
> - **Session timeout/crash:** All state persisted to Git; resume picks up

**What thin looks like:**
> "Handle errors gracefully." (No specifics, no scenarios.)

**Coaching — why this matters:**
> Failure modes are where production quality lives. If you don't define what happens when Jira is down, the engineer will make their own choice — and it may not match your intent.

**Scoring rubric:**
- 9-10: 4+ failure modes with specific expected behaviors
- 7-8: Some failure modes but behaviors are vague
- 4-6: 1-2 failure modes only
- 1-3: Missing or "handle errors"

---

## 11. Dependencies and Constraints

**What belongs here:** What this feature depends on (other systems, teams, tools, data) and what constraints bound the solution (technical, organizational, timing).

**What good looks like:**
> **Depends on:** Jira (epic creation + permissions), Kiro IDE (Claude model access), AI-DLC workflow (downstream consumer), existing SSO infrastructure.
>
> **Constraints:** PRD must stay at intent level (elaboration belongs to inception); output must render as valid Jira description; authoring standard sourced from template file (not hardcoded).

**What thin looks like:**
> "Needs Jira." (No constraints, no detail.)

**Scoring rubric:**
- 9-10: Dependencies and constraints clearly separated, 3+ of each, specific
- 7-8: Dependencies listed but constraints missing or vague
- 4-6: Partial list
- 1-3: Missing

---

## 12. Assumptions

**What belongs here:** Things you believe to be true that, if wrong, would change the design. State them explicitly so inception can challenge them.

**What good looks like:**
> - PMs have Jira access and permission to create epics
> - AI-DLC inception consumes the attached epic description as its starting artifact
> - v1 targets standard epic type across all Jira projects
> - Attach is a one-way handoff for v1

**What thin looks like:**
> "We assume everything works." (No specifics.)

**Scoring rubric:**
- 9-10: 4+ specific, falsifiable assumptions
- 7-8: 2-3 assumptions but some are obvious/unfalsifiable
- 4-6: 1 assumption or very generic
- 1-3: Missing

---

## 13. Open Questions

**What belongs here:** Genuine unknowns that haven't been resolved. These are things the team still needs to decide — NOT a placeholder for "I didn't think about it." Each should note who owns answering it.

**What good looks like:**
> | Question | Owner | Status |
> |----------|-------|--------|
> | Which Jira projects are in the first release? | PM | Open |
> | Where do drafts live before attach? | Engineering | Resolved: Git |

**What thin looks like:**
> Empty section. (Either everything is truly resolved — fine — or questions are hiding in other sections.)

**Coaching — why this matters:**
> Open questions are honest. They prevent fabrication. It's better to say "I don't know yet" here than to make up an answer elsewhere. Inception will help resolve these.

**Scoring rubric:**
- 9-10: Genuine unknowns captured with owners; resolved items shown as resolved
- 7-8: Questions present but no owners
- 4-6: Section present but empty (may be valid if all resolved)
- 1-3: Section missing entirely

---

## 14. References

**What belongs here:** Links to related documents, prior art, existing systems, Jira tickets, Confluence pages, or any material that provides additional context.

**What good looks like:**
> - prd-writer skill (authoring engine)
> - Andromeda ideation by Sauravmoy Sarkar
> - Existing AI-DLC effort (inception process)
> - [Confluence: Architecture Decision Records](https://...)

**What thin looks like:**
> Empty. (There is almost always prior art or context worth linking.)

**Scoring rubric:**
- 9-10: 3+ relevant references with descriptions
- 7-8: References present but minimal descriptions
- 4-6: 1-2 references
- 1-3: Missing (acceptable only if truly no prior art exists)
