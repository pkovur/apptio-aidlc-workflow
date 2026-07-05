---
template_name: "Apptio PRD Standard"
template_version: "1.0.0"
description: "Canonical PRD template for AI-DLC. Tool-agnostic — works in Kiro, Bob, or any AIDLC-compatible IDE."
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

> Canonical PRD template for the PM Agent (Andromeda). Tool-agnostic.

## Sections and Scoring Rubrics

### 1. Summary
- 9-10: Clear what, why-now, new-vs-change in ≤4 sentences
- 7-8: Covers basics but missing one dimension
- 4-6: Vague or too long
- 1-3: Missing or single sentence

### 2. Problem and Context
- 9-10: Specific pain, structural root cause, timing, named users
- 7-8: Good pain but missing root cause or timing
- 4-6: Generic problem statement
- 1-3: Missing or one-liner

### 3. Job to Be Done
- 9-10: Single sentence, correct format, one job, clear benefit
- 7-8: Correct format but slightly compound
- 4-6: Multiple jobs or missing format
- 1-3: Missing or not a JTBD

### 4. Goals and Success Metrics
- 9-10: 3-5 goals with measurement approach
- 7-8: Goals present but some lack measurement
- 4-6: Vague goals
- 1-3: Missing or aspirational

### 5. Scope (In/Out)
- 9-10: Clear in-scope + 3+ out-of-scope with rationale and tracking
- 7-8: In-scope clear but out-of-scope thin
- 4-6: Only in-scope listed
- 1-3: Missing or vague

### 6. Users and Stakeholders
- 9-10: 3+ roles with relationship type and concerns
- 7-8: Roles listed but concerns generic
- 4-6: Only primary user
- 1-3: Missing

### 7. What It Needs to Do
- 9-10: 5+ intent-level capability bullets
- 7-8: Good but some creep into "how"
- 4-6: Too few or too vague
- 1-3: Missing

### 8. Example Scenarios
- 9-10: 3+ with situation/action/outcome + edge cases
- 7-8: 2 scenarios, happy paths only
- 4-6: 1 scenario or generic
- 1-3: Missing

### 9. Non-Functional Requirements
- 9-10: 4+ categories with specific expectations
- 7-8: Categories mentioned but vague
- 4-6: 1-2 categories
- 1-3: Missing or generic

### 10. Failure Modes and Edge Cases
- 9-10: 4+ failure modes with behaviors
- 7-8: Some modes but vague behaviors
- 4-6: 1-2 modes
- 1-3: Missing

### 11. Dependencies and Constraints
- 9-10: Clearly separated, 3+ each, specific
- 7-8: Dependencies listed but constraints missing
- 4-6: Partial list
- 1-3: Missing

### 12. Assumptions
- 9-10: 4+ specific, falsifiable
- 7-8: 2-3 but some unfalsifiable
- 4-6: 1 or generic
- 1-3: Missing

### 13. Open Questions
- 9-10: Genuine unknowns with owners
- 7-8: Present but no owners
- 4-6: Empty (may be valid)
- 1-3: Section missing

### 14. References
- 9-10: 3+ with descriptions
- 7-8: Present but minimal
- 4-6: 1-2
- 1-3: Missing
