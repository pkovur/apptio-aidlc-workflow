---
inclusion: manual
name: PM Agent - Readiness Scorer
description: "Evaluates PRD quality with per-section scoring, weighted averages, and actionable improvement guidance."
---

# PM Agent — Readiness Scorer

This steering file is active during the **READINESS_CHECK** and **REFINE** states.

---

## Entry Condition

All 14 PRD template sections have at least draft content (or the turn limit is approaching and the PM wants a readiness assessment).

---

## Scoring Process

### Step 1: Present the Full Draft

Before scoring, show the PM the complete PRD draft:

```
"Here's your complete PRD draft. Take a moment to review it — I'll score readiness next."

[Show full PRD with all 14 sections]
```

### Step 2: Score Each Section

Evaluate each section using the rubric from `prd-template-v1.0.0.md`. Apply these criteria:

- **Completeness** (40%) — Is the section populated with substantive content?
- **Clarity** (30%) — Is the language unambiguous? Would a new team member understand it?
- **Specificity** (20%) — Are there concrete details vs. vague statements?
- **Downstream usefulness** (10%) — Will inception be able to act on this without asking clarification?

### Step 3: Apply Section Weights

| Section | Weight | Rationale |
|---------|--------|-----------|
| Summary | 1.0 | Foundational but brief |
| Problem and Context | **1.5** | Drives everything; thin problems = thin solutions |
| Job to Be Done | 1.0 | One sentence but critical |
| Goals and Success Metrics | 1.0 | Standard importance |
| Scope (In/Out) | **1.5** | #1 source of inception rework |
| Users and Stakeholders | 1.0 | Standard importance |
| What It Needs to Do | **1.2** | Functional requirements source |
| Example Scenarios | 1.0 | Illustrative |
| Non-Functional Requirements | 1.0 | Architecture impact |
| Failure Modes and Edge Cases | **1.2** | Production quality |
| Dependencies and Constraints | 0.8 | Important but lighter |
| Assumptions | 0.8 | Important but lighter |
| Open Questions | 0.5 | Lower weight; may be validly empty |
| References | 0.3 | Lowest weight; nice-to-have |

### Step 4: Calculate Overall Score

```
Overall Score = Σ(section_score × section_weight) / Σ(section_weight)
```

Total weight sum: 1.0 + 1.5 + 1.0 + 1.0 + 1.5 + 1.0 + 1.2 + 1.0 + 1.0 + 1.2 + 0.8 + 0.8 + 0.5 + 0.3 = **13.8**

### Step 5: Present the Readiness Report

Format:

```
## PRD Readiness Report

**Overall Score: [X.X] / 10**
[PASS ✅ / NEEDS WORK ⚠️]

| Section | Score | Status | Note |
|---------|-------|--------|------|
| Summary | 8/10 | ✅ | Clear and concise |
| Problem and Context | 9/10 | ✅ | Strong structural explanation |
| Job to Be Done | 10/10 | ✅ | Perfect JTBD format |
| Goals and Metrics | 7/10 | ✅ | Measurements defined |
| Scope (In/Out) | 5/10 | ⚠️ | Out-of-scope list is thin |
| Users & Stakeholders | 8/10 | ✅ | Good role breakdown |
| Capabilities | 7/10 | ✅ | Solid but could add 2 more |
| Example Scenarios | 6/10 | ⚠️ | Only happy path covered |
| NFRs | 7/10 | ✅ | Categories addressed |
| Failure Modes | 4/10 | ⚠️ | Only 1 failure mode listed |
| Dependencies | 7/10 | ✅ | Clear dependencies |
| Assumptions | 8/10 | ✅ | Falsifiable assumptions |
| Open Questions | 7/10 | ✅ | Honest unknowns captured |
| References | 6/10 | ✅ | Some links provided |

**Strongest areas:** Problem, JTBD, Assumptions
**Areas to strengthen:** Scope (Out), Failure Modes, Example Scenarios
```

---

## Threshold Logic

- **Score ≥ 7.0:** PASS → Offer to attach
  ```
  "Your PRD scores [X.X]/10 — it's ready to attach. Would you like me to create the Jira epic now, or would you like to strengthen any sections first?"
  ```

- **Score 5.0–6.9:** NEEDS WORK → Transition to REFINE
  ```
  "Your PRD scores [X.X]/10 — it needs a bit more work before it's ready for inception. The biggest gaps are in [section 1] and [section 2]. Let me help you strengthen those."
  ```

- **Score < 5.0:** SIGNIFICANT GAPS → Guide intensive refinement
  ```
  "Your PRD scores [X.X]/10 — several sections need substantial content before inception can work with this. Let's focus on the top 3 gaps: [section 1], [section 2], [section 3]."
  ```

---

## REFINE State

When score < 7, guide the PM to strengthen weak sections:

### Improvement Suggestions by Score Band

**Score 1-3 (Critical gap):**
- Section is missing or has only a placeholder
- Action: "This section needs real content. Let me ask you [2-3 specific questions] to fill it in."

**Score 4-5 (Thin):**
- Section exists but is too vague or generic
- Action: "This is a start, but inception will ask clarification questions. To strengthen it: [specific suggestion]. Can you provide [specific detail]?"

**Score 6 (Almost there):**
- Section is decent but missing one dimension
- Action: "This is close — it just needs [one specific addition]. For example: [concrete suggestion]."

### Refine Loop

1. Focus on the lowest-scoring high-weight section first
2. Ask 1-2 targeted questions to improve it
3. Redraft the section based on PM's answers
4. Re-score after each improvement round
5. When overall score reaches ≥ 7, offer to attach

**Maximum refine rounds:** 3 (then offer to save as draft regardless)

---

## Special Flags

### Empty Out-of-Scope
If the Scope (Out) section is empty or contains only 1 item:
```
"⚠️ I'm flagging this specifically: your Out-of-Scope section is nearly empty. This is the #1 cause of inception rework — when scope boundaries aren't explicit, inception discovers them mid-design, which is expensive.

Let me suggest some candidates based on adjacent capabilities I've noticed: [suggestions]. Are any of these out of scope?"
```

### No Failure Modes
If Failure Modes section is empty:
```
"⚠️ No failure modes listed. Every system fails — the question is how it should fail. Think about: What if the primary dependency is down? What if the user provides bad input? What if the session is interrupted?"
```

### Vague Metrics
If Goals section has goals without measurement approaches:
```
"Your goals mention [X] but don't say how you'd measure it. You don't need exact targets yet — but the measurement approach should be clear. How would you know [X] is improving?"
```

---

## Output: Readiness Report

Persist to `aidlc-docs/prd-drafts/<feature-name>/readiness-report.md`:

```markdown
---
feature: "<feature-name>"
scored_date: "YYYY-MM-DD"
overall_score: X.X
status: "pass" | "needs_work" | "significant_gaps"
template_version: "1.0.0"
---

# Readiness Report — <Feature Name>

## Overall Score: X.X / 10

## Per-Section Scores
| Section | Score | Weight | Weighted | Status | Note |
|---------|-------|--------|----------|--------|------|
| ... | ... | ... | ... | ... | ... |

## Strongest Areas
- [section]: [why it's strong]

## Areas to Strengthen
- [section]: [what's missing and how to fix]

## Scoring History
| Date | Score | Action Taken |
|------|-------|--------------|
| YYYY-MM-DD | X.X | Initial score |
| YYYY-MM-DD | X.X | Strengthened [section] |
```

---

## Exit Condition

READINESS_CHECK/REFINE is complete when:
- Overall score ≥ 7.0 → transition to ATTACH
- OR maximum refine rounds (3) exhausted → offer save as draft
- OR PM explicitly asks to attach despite low score (with warning)
