# Contribution Workflow Automation — State Machine & Orchestration

**Sprint Q42 · Technical Lead (00)**  
**Date:** 2026-02-18  
**Status:** Complete

---

## Overview

While Q40 defined the *schema* (the five stages) and Q41 defined the *input* (NL parser), **Q42 defines the engine** that moves contributions between stages.

The workflow engine (`contribution-lifecycle-workflow.ts`) enforces valid state transitions and business rules. The orchestrator (`contribution-orchestrator.ts`) provides high-level convenience functions for coordinators, enabling "fast-track" approvals and batch operations.

---

## The Engine: `contribution-lifecycle-workflow.ts`

This low-level module handles atomic state transitions. Each function validates the transition against the chain state before appending a new entry.

### 1. Validation (`validateContribution`)
- **Input:** Contribution ID, validator ID, status (authentic/needs_clarification/out_of_scope)
- **Checks:** Is current state `submitted`?
- **Auto-validation:** Can automatically validate if `autoValidate: true` AND:
  - Source URL is from a trusted domain (GitHub, GitLab, Notion, Docs)
  - OR Confidence score from NL parser was high (inherited from submission)

### 2. Valuation (`valueContribution`)
- **Input:** Contribution ID, valuer ID, method (fixed_usd/formula_weight/etc.), amount/weight
- **Checks:** Is current state `validated`?
- **Auto-valuation:** Can automatically assign value if `autoValue: true` AND:
  - Matches a known pattern (e.g., "Bug fix" category + "Low" effort = $500)
  - Matches a standard documentation rate
  - *Configurable rules per convergence*

### 3. Approval (`approveContribution`)
- **Input:** Contribution ID, approver ID, credit amount
- **Checks:** Is current state `valued`?
- **Side Effect:** Automatically creates a **double-entry transaction** (`treasury.transaction.posted`) alongside the approval entry.
  - Debit: `contribution-expense`
  - Credit: `member-capital-{id}`

### 4. Rejection (`rejectContribution`)
- **Input:** Contribution ID, rejector ID, reason
- **Checks:** Can transition from any non-terminal state.

### 5. Void (`voidContribution`)
- **Input:** Contribution ID, voider ID, reason
- **Checks:** Can transition from *any* state.
- **Side Effect:** If the contribution was already Approved, automatically creates a **compensating transaction** to reverse the capital account credit.

---

## The Orchestrator: `contribution-orchestrator.ts`

High-level functions that string together engine calls for common coordinator tasks.

### Fast-Track Approval
`fastTrackApprove({...})`
- **Use case:** Trusted coordinator reviewing a clear contribution (e.g., "I fixed the typo in the README").
- **Action:** Performs Validation → Valuation → Approval in a single awaitable call.
- **Result:** Contribution goes from Submitted to Approved instantly, with full chain history preserved (3 entries added).

### Batch Processing
`batchValidateSubmitted({...})` & `batchValueValidated({...})`
- **Use case:** Coordinator clearing the inbox at the end of the week.
- **Action:** Iterates through all contributions in a specific state and attempts to auto-process them based on rules.
- **Result:** Returns a report of what was auto-processed vs. what needs manual review.

### Review Queue
`getReviewQueue({...})`
- **Use case:** Dashboard UI showing "Tasks for Coordinator".
- **Action:** Aggregates all contributions blocked at `submitted` (needs validation) or `validated` (needs valuation).

---

## Usage Examples

### Coordinator Dashboard

```typescript
import { getReviewQueue, fastTrackApprove } from '@/lib/contribution-orchestrator'

// Load tasks
const queue = await getReviewQueue({ convergenceId: 'techne-001' })
console.log(`Pending validation: ${queue.submitted.length}`)

// Process a clear task
const result = await fastTrackApprove({
  convergenceId: 'techne-001',
  contributionId: 'contrib-123',
  coordinatorId: 'coord-001',
  creditAmount: 500,
  justification: 'Standard bug fix rate'
})
```

### Automated Bot

```typescript
import { submitContributionFromNL } from '@/lib/contribution-workflow'
import { validateContribution } from '@/lib/contribution-lifecycle-workflow'

// 1. Ingest
const submission = await submitContributionFromNL({...})

// 2. Try to auto-validate immediately if it has a GitHub link
if (submission.currentState === 'submitted') {
  await validateContribution({
    convergenceId: 'techne-001',
    contributionId: submission.contributionId,
    validatorId: 'bot-validator',
    validationStatus: 'authentic',
    autoValidate: true // Only proceeds if URL is trusted
  })
}
```

---

## Audit Compliance

The engine ensures that **no step is skipped** in the chain history, even during fast-tracking.
- A "Fast-Track" approval still writes `validated`, `valued`, and `approved` entries.
- An auditor replaying the chain sees the full lifecycle, with timestamps showing they happened sequentially in the same millisecond.
- 704b compliance is maintained: every credit has a validation record and a valuation record backing it.

---

*TIO · Contribution Workflow Documentation · February 2026*
