# Contribution Lifecycle — Five-Stage Chain-Based Workflow

**Sprint Q40 · Technical Lead (00)**  
**Date:** 2026-02-18  
**Status:** Foundation complete

---

## Overview

Contributions in Techne are recorded as append-only chain events that transition through five stages from creation to approval/rejection. Each stage is immutable, auditable, and cryptographically linked.

Unlike traditional databases where contribution state is mutated in place, **state is derived by replaying chain entries**. This enables:
- Complete audit trail (who valued what, when, and why)
- Temporal queries ("what was this contribution's state on Feb 10?")
- Compliance verification (704b double-entry reconciliation)
- Rollback via compensating entries (voided contributions)

---

## The Five Stages

```
┌─────────┐    ┌───────────┐    ┌───────────┐    ┌────────┐    ┌──────────┐
│ Created │ -> │ Submitted │ -> │ Validated │ -> │ Valued │ -> │ Approved │
└─────────┘    └───────────┘    └───────────┘    └────────┘    └──────────┘
                                                                       │
                                                                       │
                                                      ┌──────────┐     │
                                                      │ Rejected │ <───┘
                                                      └──────────┘
                                                      
                     (any stage) ──────> Voided (rollback/correction)
```

### 1. Created
**Event:** `people.contribution.created`  
**Actor:** Contributor (member or coordinator entering on behalf)  
**Purpose:** Capture contribution intent

**Payload:**
- `title` — Short name (e.g., "Patronage formula research")
- `description` — Full description or summary
- `contributorId` — Member who made the contribution
- `createdAt` — ISO 8601 timestamp
- `nlSource` (optional) — Original natural language input (if from chat/form)
- `sourceUrl` (optional) — Link to external artifact (GitHub PR, Notion doc, etc.)
- `tags` (optional) — Category tags

**Transition to:** `Submitted` or `Voided`

---

### 2. Submitted
**Event:** `people.contribution.submitted`  
**Actor:** Contributor or LLM parser  
**Purpose:** Add structured metadata for review

**Payload:**
- `contributionId` — Reference to the contribution aggregate
- `submittedBy` — Who submitted (may differ from contributor if coordinator-assisted)
- `submittedAt` — Timestamp
- `extractedData` (optional):
  - `category` — 'code', 'research', 'coordination', 'design', etc.
  - `effort` — 'low', 'medium', 'high', 'exceptional'
  - `impact` — 'local', 'convergence', 'ecosystem'
  - `relatedArtifactIds` — Links to other contributions/artifacts
  - `relatedMemberIds` — Collaborators
- `submissionNotes` (optional) — Additional context

**Why separate from Created?**  
Natural language contributions may need LLM extraction or manual structuring before review. "Submitted" signals "ready for coordinator eyes."

**Transition to:** `Validated`, `Rejected`, or `Voided`

---

### 3. Validated
**Event:** `people.contribution.validated`  
**Actor:** Coordinator, steward, or governance process  
**Purpose:** Verify authenticity and scope

**Payload:**
- `contributionId`
- `validatedBy` — Validator ID
- `validatedAt` — Timestamp
- `validationStatus`:
  - `authentic` — Verified as real, in-scope, ready for valuation
  - `needs_clarification` — Questions or missing info
  - `out_of_scope` — Doesn't fit convergence criteria
- `validationNotes` (optional)
- `requestedChanges` (optional) — If clarification needed

**Checks performed:**
- Did this person actually do this?
- Is it within the convergence's scope?
- Is it a duplicate of an existing contribution?
- Does it meet minimum quality standards?

**Transition to:** `Valued`, `Rejected`, or `Voided`

---

### 4. Valued
**Event:** `people.contribution.valued`  
**Actor:** Coordinator, valuation committee, or automated formula  
**Purpose:** Assign economic value

**Payload:**
- `contributionId`
- `valuedBy` — Valuer ID (or "formula_engine" if automated)
- `valuedAt` — Timestamp
- `valueUsd` (optional) — Direct USD value
- `valueWeight` (optional) — Formula weight (0.0–1.0)
- `valueMethod`:
  - `fixed_usd` — Direct dollar amount
  - `formula_weight` — Contribution weight in patronage formula
  - `peer_assessment` — Member peer review scores
  - `governance_vote` — Voted allocation
- `justification` (optional) — Why this value?
- `assessmentData` (optional) — Method-specific details (peer scores, vote tallies, etc.)

**Why separate from Approval?**  
Valuation may require committee review, formula runs, or peer assessment. The value can be proposed, debated, and finalized before capital account credit is posted.

**Transition to:** `Approved`, `Rejected`, or `Voided`

---

### 5a. Approved
**Event:** `people.contribution.approved`  
**Actor:** Coordinator or governance approval process  
**Purpose:** Post capital account credit

**Payload:**
- `contributionId`
- `approvedBy` — Approver ID
- `approvedAt` — Timestamp
- `creditAmount` — Final capital account credit (in USD or convergence unit)
- `transactionId` — Reference to double-entry transaction entry (Sprint Q43+)
- `periodId` (optional) — Patronage period if applicable
- `approvalNotes` (optional)

**Effects:**
- Capital account balance increases by `creditAmount`
- Member's share of future patronage distributions increases
- Contribution is now factored into K-1 reporting (Sprint Q53+)

**Terminal state.** Can only transition to `Voided` (requires compensating transaction).

---

### 5b. Rejected
**Event:** `people.contribution.rejected`  
**Actor:** Coordinator or governance process  
**Purpose:** Decline contribution with explanation

**Payload:**
- `contributionId`
- `rejectedBy` — Rejector ID
- `rejectedAt` — Timestamp
- `rejectionReason`:
  - `duplicate` — Already credited elsewhere
  - `out_of_scope` — Doesn't fit convergence mission
  - `insufficient_evidence` — Can't verify authenticity
  - `policy_violation` — Violates agreement terms
  - `other` — See notes
- `rejectionNotes` (optional)
- `appealEligible` — Can the contributor request reconsideration?

**Terminal state.** Cannot transition further (except to `Voided` if rejection was in error).

---

### Voided (Rollback/Correction)
**Event:** `people.contribution.voided`  
**Actor:** Coordinator or compliance officer  
**Purpose:** Correct errors, handle disputes, or reverse fraudulent entries

**Payload:**
- `contributionId`
- `voidedBy` — Who initiated the void
- `voidedAt` — Timestamp
- `voidReason` — Explanation
- `compensatingTransactionId` (optional) — If a credit was already posted, this reverses it

**Can happen at any stage.** If a contribution was approved and then voided, a compensating transaction debits the member's capital account.

**Terminal state.**

---

## State Transitions (Valid Paths)

| From       | To                                  |
|------------|-------------------------------------|
| Created    | Submitted, Voided                   |
| Submitted  | Validated, Rejected, Voided         |
| Validated  | Valued, Rejected, Voided            |
| Valued     | Approved, Rejected, Voided          |
| Approved   | Voided (only)                       |
| Rejected   | (terminal)                          |
| Voided     | (terminal)                          |

**Invalid transition example:** Cannot go from `Created` directly to `Approved` without passing through `Submitted`, `Validated`, and `Valued`.

**Why enforce these paths?**  
Audit compliance. Every contribution that results in a capital account credit must have evidence of validation and valuation. Skipping stages would break the audit trail.

---

## Deriving Current State

Since state is append-only events, the **current state** is the most recent lifecycle event in the chain for a given contribution.

```typescript
function deriveContributionState(entries: ChainEntry[]): ContributionLifecycleState {
  const sorted = entries.sort((a, b) => b.chain_index - a.chain_index)
  
  for (const entry of sorted) {
    if (entry.event_type === 'people.contribution.approved') return 'approved'
    if (entry.event_type === 'people.contribution.rejected') return 'rejected'
    if (entry.event_type === 'people.contribution.voided') return 'voided'
    if (entry.event_type === 'people.contribution.valued') return 'valued'
    if (entry.event_type === 'people.contribution.validated') return 'validated'
    if (entry.event_type === 'people.contribution.submitted') return 'submitted'
    if (entry.event_type === 'people.contribution.created') return 'created'
  }
  
  return null  // no lifecycle events found
}
```

---

## Temporal Queries

**Question:** "What was the state of contribution XYZ on February 10, 2026?"

**Answer:**
1. Query all chain entries for contribution XYZ where `created_at <= 2026-02-10T23:59:59Z`
2. Derive state from those entries
3. You now have the state as it existed on that date

This is critical for compliance audits ("show me what was approved during Q1 2026").

---

## Integration with Double-Entry Accounting (Sprint Q43+)

When a contribution is **approved**, two chain entries are written:

1. **Contribution approval entry** (`people.contribution.approved`)
2. **Transaction entry** (`treasury.transaction.posted`)

The transaction entry records:
- **Debit:** Contribution expense account (increases equity)
- **Credit:** Member capital account (increases member's equity share)
- **Amount:** Same as `creditAmount` from the approval payload
- **Reference:** Links back to the contribution ID

This ensures that every capital account credit has a corresponding double-entry transaction. Compliance checks (Sprint Q51) verify that these match.

---

## Example Contribution Flow

**Kevin Owocki submits patronage formula research**

1. **Created** (Feb 10, 10:00 AM)
   - Kevin pastes a Notion doc link into the contribution form
   - Chain entry: `people.contribution.created`
   - Payload: title="Patronage at the Speed of the Sun research", contributorId=kevin-004, sourceUrl="https://notion.so/...", tags=["research", "patronage"]

2. **Submitted** (Feb 10, 10:05 AM)
   - LLM extracts structured metadata
   - Chain entry: `people.contribution.submitted`
   - Payload: category="research", effort="high", impact="convergence"

3. **Validated** (Feb 10, 3:00 PM)
   - Coordinator verifies Kevin authored the doc and it's in scope
   - Chain entry: `people.contribution.validated`
   - Payload: validatedBy=coordinator-001, validationStatus="authentic"

4. **Valued** (Feb 11, 9:00 AM)
   - Valuation committee assigns a weight of 0.85 (high impact)
   - Chain entry: `people.contribution.valued`
   - Payload: valueWeight=0.85, valueMethod="peer_assessment", justification="Foundation for patronage engine, high ecosystem impact"

5. **Approved** (Feb 11, 10:00 AM)
   - Coordinator approves, posts $12,000 credit
   - Chain entries:
     - `people.contribution.approved` (creditAmount=12000, transactionId=tx-789)
     - `treasury.transaction.posted` (debit: contribution_expense, credit: kevin_capital_account, amount=12000)

Kevin's capital account balance increases by $12,000. His share of future patronage distributions increases proportionally.

---

## Future: Automated Transitions (Sprint Q41+)

**Sprint Q41** introduces an NL contribution parser (LLM-powered) that can:
- Extract structured data from free-form input
- Auto-transition from `Created` to `Submitted` if confidence is high
- Flag for human review if ambiguous

**Sprint Q42** adds workflow automation:
- Auto-validate if source URL is verified (GitHub commit, published article)
- Auto-value if contribution matches a known pattern (e.g., "bug fix" = $500)
- Require human approval for large amounts or novel categories

---

## Compliance & Auditing

**704b Requirements:**
- Capital account credits must trace to specific contributions
- Contributions must have validation evidence
- Values must be justified and approved

**How this lifecycle satisfies 704b:**
- Every approved contribution has a chain of evidence (created → submitted → validated → valued → approved)
- Each stage has an actor, timestamp, and justification
- Auditors can replay the chain to verify compliance at any point in time
- Voided contributions leave an audit trail (no deletion, only compensating entries)

---

## Implementation Notes (Sprint Q40)

**What was built:**
- TypeScript types for all five stages (see `types/chain.ts`)
- Payload interfaces for each lifecycle event
- State transition validation helpers (`isValidLifecycleTransition`)
- State derivation function (`deriveContributionState`)
- ContributionView aggregate type (projection of all events for a contribution)

**What's next (Sprint Q41–Q43):**
- NL parser (Q41) — LLM extraction of structured data from free-form input
- Workflow engine (Q42) — Automate transitions based on rules
- Double-entry integration (Q43) — Post transaction entries on approval

---

*TIO · Contribution Lifecycle Specification · February 2026*
