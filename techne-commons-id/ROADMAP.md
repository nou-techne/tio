# Techne × commons.id — Perpetual Convergence Chain

**TIO Sprint Plan: Cooperative Patronage as a Living Knowledge Graph**
**Date:** February 18, 2026
**Status:** Planning

---

## Framing

ETHBoulder was a **bounded convergence** — a 3-day event with a beginning and end, where sessions, speakers, and topics formed the artifact graph. The knowledge graph captured what happened and who was involved.

Techne's commons.id is a **perpetual convergence** — an ongoing, append-only merkle chain where cooperative economic events accumulate over time. There is no end date. Every contribution, allocation, period close, and governance decision becomes an immutable entry in the chain, forming a living record of the cooperative's economic life.

This is not a fork of the ETHBoulder instance. It is a **new convergence type** within commons.id: one that runs indefinitely and whose artifacts are economic events rather than conference sessions.

---

## What Habitat Already Built (Priority Assessment)

The habitat repo contains three layers of implementation. Assessed by value-creation priority for commons.id integration:

### Tier 1 — Highest Priority (Port First)

These are the patterns that create immediate, unique value as commons.id chain entries:

**1. Event Schema (Layer 4)**
`habitat/packages/worker/src/events/schema.ts`

The REA-based event type registry is the direct mapping to convergence chain entries:

| Habitat Event | Commons.id Chain Entry | Pattern Layer |
|---|---|---|
| `people.contribution.submitted` | Contribution artifact created | Event (4) |
| `people.contribution.approved` | Contribution status → approved | Event (4) + State (2) |
| `treasury.transaction.posted` | Double-entry transaction recorded | Event (4) + Flow (5) |
| `treasury.period.opened` | New accounting period begins | Event (4) |
| `treasury.period.closed` | Period locked, balances frozen | Event (4) + Constraint (6) |
| `agreements.allocation.proposed` | Patronage allocation calculated | Flow (5) |
| `agreements.allocation.approved` | Governance approved allocation | Event (4) + Constraint (6) |
| `agreements.distribution.completed` | Cash distributed to member | Flow (5) |
| `agreements.capitalAccount.updated` | Capital account balance changed | State (2) |

Each event becomes a **chain entry** with a content hash linking to its predecessor — the merkle chain. The chain is the audit trail. It is append-only. Period close locks a range of entries. Capital account balances are computed views over the chain, not stored state.

**2. Patronage Formula Engine (Layer 5 — Flow)**
`habitat/packages/shared/src/engine/patronage-formula.ts`

This is the core value-creation mechanism. It takes raw contributions, applies configurable weights, and produces member allocations. In commons.id, the formula execution itself becomes a chain entry — you can see *exactly* how the allocation was computed, with what weights, from what inputs.

The `PatronageFormulaEngine` and `MultiPeriodPatronageAccumulator` port directly. The `verifyAllocations()` function provides the invariant checks that become Constraint (Layer 6) entries in the chain.

**3. Contribution Lifecycle (Layer 4 — Event)**
`habitat/packages/worker/src/workflows/contribution-claim.ts`
`projects/patronage-system/concepts/contribution-lifecycle.md`

The five-stage workflow (Submit → Validate → Value → Approve → Credit) is the primary input mechanism. Each stage transition is a chain entry. The lifecycle transforms natural language descriptions into valued economic events:

```
"3 hours on treasury API" 
  → contribution.created (chain entry #N)
  → contribution.submitted (chain entry #N+1)  
  → contribution.valued { hours: 3, rate: $75, value: $225 } (chain entry #N+2)
  → contribution.approved { approvedBy: steward } (chain entry #N+3)
  → transaction.posted { debit: labor_expense, credit: member_capital } (chain entry #N+4)
```

Five chain entries from one sentence. Each is immutable, hashlinked, auditable.

### Tier 2 — High Priority (Port Second)

**4. Period Close Workflow (Layer 5 — Flow + Layer 6 — Constraint)**
`habitat/packages/worker/src/workflows/period-close.ts`

The multi-step orchestration: aggregate → weight → allocate → propose → approve → lock. This is where the chain becomes economically meaningful — a period close entry seals a range of the chain and produces allocation entries that change capital account balances.

**5. Capital Account Computation (Layer 2 — State, derived)**
`habitat/packages/shared/src/engine/balance-computation.ts`

Capital accounts are *computed from the chain*, not stored directly. Beginning balance + contributions + allocations - distributions = ending balance. This is the key architectural insight: **state is a view over events**, not a separate data store. The chain IS the accounting system.

**6. Compliance Layer (Layer 6 — Constraint)**
`habitat/packages/shared/src/compliance/`
- `704b-validator.ts` — Substantial economic effect test
- `allocation-verifier.ts` — Invariant checks on allocations
- `double-entry-checker.ts` — Debits must equal credits
- `k1-assembly.ts` — Tax data extraction

Each constraint violation is itself a chain entry — the system records not just what happened, but what it checked and whether it passed.

### Tier 3 — Important (Port Third)

**7. Three Composable Tools (Treasury, People, Agreements)**
`habitat/packages/api/src/data/treasury.ts`
`habitat/packages/api/src/data/people.ts`
`habitat/packages/api/src/graphql/resolvers/agreements.ts`

These are the read/write interfaces. In commons.id, they become the API layer that reads chain state and appends new entries. The GraphQL schema ports as the query interface for the graph visualization.

**8. Seven-Layer Pattern Library (Layer 7 — View)**
`habitat/docs/patterns/`

The pattern library is both documentation and a design constraint. Each chain entry is tagged with its pattern layer, making the graph filterable by abstraction level. The patterns page from the-habitat.org becomes the educational "how to read this graph" view.

---

## Perpetual Convergence Architecture

### Chain Structure

```
Entry #0: convergence.created { name: "Techne", type: "perpetual", startDate: "2026-02-06" }
  │
Entry #1: people.member.created { name: "Todd", tier: "cooperative", memberNumber: "001" }
  │
Entry #2: people.member.created { name: "Aaron", tier: "cooperative", memberNumber: "002" }
  │
  ... (founding members)
  │
Entry #9: treasury.period.opened { period: "2026-Q1", fiscalYear: 2026 }
  │
Entry #10: people.contribution.submitted { member: "Todd", type: "labor", desc: "LCA formation" }
  │
  ... (daily contributions accumulate)
  │
Entry #N: treasury.period.closed { period: "2026-Q1" }
  │
Entry #N+1: agreements.allocation.proposed { member: "Todd", amount: "$X", formula: {...} }
  │
  ... (allocations for each member)
  │
Entry #N+M: agreements.allocation.approved { period: "2026-Q1", approvedBy: "governance" }
  │
  ... (chain continues indefinitely)
```

### Merkle Chain Properties

Each entry contains:
```typescript
interface ChainEntry {
  index: number                    // Sequential position
  timestamp: string                // ISO 8601
  eventType: string                // From habitat event registry
  aggregateId: string              // Entity this affects
  aggregateType: string            // member | contribution | account | period
  payload: object                  // Event-specific data
  patternLayer: 1|2|3|4|5|6|7     // Seven-layer classification
  contentHash: string              // SHA-256 of (prevHash + payload)
  prevHash: string                 // Hash of previous entry
  metadata: {
    memberId?: string              // Who triggered this
    correlationId?: string         // Groups related entries
    naturalLanguageSource?: string // Original NL input (if applicable)
  }
}
```

The chain is:
- **Append-only** — no edits, no deletes (voids create new entries referencing the voided entry)
- **Hash-linked** — each entry's hash includes the previous entry's hash
- **Auditable** — any entry can be verified by recomputing hashes from genesis
- **Period-lockable** — period close marks a range as frozen; no new entries can reference that period

### Convergence vs ETHBoulder: Structural Differences

| Dimension | ETHBoulder (bounded) | Techne (perpetual) |
|---|---|---|
| **Duration** | 3 days | Indefinite |
| **Artifacts** | Sessions, speakers, topics | Members, contributions, capital accounts |
| **Relationships** | Speaker↔session, topic↔track | Member↔contribution, contribution↔venture |
| **Events** | Session occurred | Contribution logged, period closed, allocation calculated |
| **Flow** | Knowledge between sessions | Value: contribution → patronage → capital account |
| **Constraints** | None enforced | 704(b), double-entry, formula invariants |
| **Views** | Graph + session pages | Graph + member dashboards + K-1 exports |
| **Chain growth** | ~500 entries, stops | Grows continuously (~100-500 entries/quarter) |
| **Cross-convergence** | Links to future events | Links to bounded convergences (ETHBoulder participants who are members) |

---

## Revised Sprint Plan

### Phase 0: Chain Foundation (Sprints 1–2)
**Goal:** Perpetual convergence chain with member genesis

**Sprint 1 — Chain Engine**
- [ ] `ChainEntry` type and merkle hash computation
- [ ] Append-only chain store (Supabase table with hash verification)
- [ ] Genesis entry: `convergence.created { type: "perpetual" }`
- [ ] Chain verification function (walk from genesis, verify all hashes)
- [ ] Convergence type: `perpetual` alongside existing `bounded`
- **TIO:** Schema Architect + Backend Engineer
- **Habitat source:** `events/schema.ts` → `ChainEntry` adapter

**Sprint 2 — Member Genesis**
- [ ] Port `MemberCreatedPayload` as chain entries
- [ ] Seed 8 founding members from ETHBoulder organizer list
- [ ] Member identity: name, tier (cooperative/community), memberNumber
- [ ] Optional: ENS/wallet association
- [ ] Capital account initialized at $0 (computed view, not stored)
- **TIO:** Schema Architect + Backend Engineer
- **Habitat source:** `types/people.ts`, `data/people.ts`

### Phase 1: Contribution Chain (Sprints 3–5)
**Goal:** Natural language → chain entries with the full contribution lifecycle

**Sprint 3 — NL Contribution Parser**
- [ ] LLM extraction: natural language → typed contribution event
- [ ] Pattern categories: labor, revenue, cash, community (mapped to formula weights)
- [ ] `naturalLanguageSource` preserved in chain entry metadata
- [ ] API endpoint: `POST /chain/contribute` (accepts text, returns chain entry)
- [ ] Telegram integration: message → contribution chain entry
- **TIO:** Integration Engineer + Backend Engineer

**Sprint 4 — Contribution Lifecycle as Chain Entries**
- [ ] Five-stage workflow: each transition = new chain entry
- [ ] Port `contribution-claim.ts` workflow logic
- [ ] Approval chain entries with `approvedBy` and correlation to submission
- [ ] Valuation chain entries (hours × rate = monetary value)
- [ ] Double-entry transaction entries on approval (debit expense, credit capital)
- **TIO:** Event Systems Engineer + Workflow Engineer
- **Habitat source:** `workflows/contribution-claim.ts`, `concepts/contribution-lifecycle.md`

**Sprint 5 — Chain Graph Visualization**
- [ ] Adapt commons.id/app graph to render perpetual chain
- [ ] Node types: member (identity), contribution (event), account (state)
- [ ] Edge types: contributed_by, approved_by, credited_to
- [ ] Time-based filtering (show chain entries for a period)
- [ ] Pattern layer filtering (show only Flow entries, only Event entries, etc.)
- **TIO:** Frontend/DevOps
- **commons.id source:** Existing graph component, adapted for chain entry types

### Phase 2: Patronage Engine (Sprints 6–8)
**Goal:** Period close produces allocation chain entries

**Sprint 6 — Patronage Formula on Chain**
- [ ] Port `PatronageFormulaEngine` from habitat
- [ ] Formula execution produces chain entry: inputs, weights, outputs
- [ ] Configurable weights stored as chain entry (agreement type)
- [ ] `verifyAllocations()` produces constraint chain entries
- **TIO:** Backend Engineer + Event Systems Engineer
- **Habitat source:** `engine/patronage-formula.ts`

**Sprint 7 — Period Close Workflow**
- [ ] Port `executePeriodClose()` from habitat
- [ ] Each step (aggregate → weight → allocate → propose) = chain entry
- [ ] Period lock: marks a range of entries as frozen
- [ ] Governance approval entry seals the allocations
- [ ] Capital account balance recomputed from chain
- **TIO:** Event Systems Engineer + Workflow Engineer
- **Habitat source:** `workflows/period-close.ts`

**Sprint 8 — Compliance Chain Entries**
- [ ] Port `704b-validator.ts` — each check = chain entry
- [ ] Port `double-entry-checker.ts` — verification entries
- [ ] Port `allocation-verifier.ts` — invariant check entries
- [ ] The chain records not just what happened, but what was verified
- **TIO:** Compliance/Security
- **Habitat source:** `compliance/*.ts`

### Phase 3: Views & Export (Sprints 9–11)
**Goal:** Member-facing dashboards and tax compliance

**Sprint 9 — Member Dashboard**
- [ ] Capital account balance (computed from chain)
- [ ] Contribution history (chain entries filtered by member)
- [ ] Patronage percentage (derived from latest allocation entries)
- [ ] Chain explorer: browse the raw merkle chain
- **TIO:** Frontend/DevOps

**Sprint 10 — Allocation Statements & K-1**
- [ ] Port `capital-account-statement.ts` and `k1-export.ts`
- [ ] PDF/CSV export of member statements
- [ ] Period-over-period comparison views
- [ ] Accountant handoff format
- **TIO:** Frontend/DevOps + Compliance/Security
- **Habitat source:** `exports/*.ts`

**Sprint 11 — Cross-Convergence Links**
- [ ] Link perpetual chain members to bounded convergences (ETHBoulder)
- [ ] Member who spoke at ETHBoulder → their contributions in Techne chain
- [ ] Cross-convergence graph: see a person's full participation across convergences
- [ ] Artifact lineage: idea at ETHBoulder → venture in Techne → contributions in chain
- **TIO:** Integration Engineer
- **commons.id source:** `types/cross-convergence.ts`

### Phase 4: White-Label (Sprint 12)
**Goal:** Any cooperative can launch a perpetual convergence chain

**Sprint 12 — Configurable Chain**
- [ ] Formula weights as parameters (not hardcoded)
- [ ] Contribution categories as configurable
- [ ] Period cadence as configurable (monthly, quarterly, solar audit)
- [ ] Branding/theming per instance
- [ ] First candidate: Circle Trust / Roots Trust LCA
- **TIO:** Product Engineer + Technical Lead

---

## What This Enables

### For Techne
A living, auditable, hashlinked record of every economic event in the cooperative's life. Capital accounts computed from the chain, not stored in a database. The graph visualization makes the cooperative's economic flows *visible* — who contributes what, how value flows, where surplus goes.

### For the Patronage System
The commons.id chain IS the accounting system. Events go in, balances come out. The chain replaces the traditional ledger with something richer: it preserves the natural language source, the approval chain, the formula execution, and the compliance checks. An auditor can walk the chain from genesis and verify every balance.

### For commons.id
A new convergence type — perpetual — that demonstrates the platform's generality. If it can track a 3-day hackathon AND an ongoing cooperative's entire economic life, it can track anything that accumulates structured events over time.

### For Other Cooperatives
Sprint 12 makes this a product. Any cooperative running under Subchapter K can launch a perpetual convergence chain with their own patronage formula. The chain is the compliance record. The graph is the dashboard. The NL parser is the input mechanism. Circle Trust's commitment pooling model could run as a parallel chain type.

---

## Decisions (Resolved Feb 18, 2026)

1. **Chain storage:** Shared Supabase project (existing RegenHub/Techne instance at `hvbdpgkdcdskhpbdeeim.supabase.co`). New tables in the same project.
2. **On-chain anchoring:** Off-chain primary storage with on-chain accountability and integrity. Periodically anchor the chain head hash on-chain (Base) for tamper-proof timestamping. The chain lives in Supabase; the hash anchors prove it hasn't been altered.
3. **Solar audit sub-periods:** Backwards compatible with quarterly. Quarterly periods are the primary accounting unit. Solar audit markers (sunrise/sunset) are optional sub-period annotations within quarterly periods — they enrich the data without breaking the standard quarterly close workflow.
4. **Formula weights:** All weights are variable by design. No hardcoded defaults. First concrete weight set to be determined by Financial Engineering Committee (Aaron G + Todd) within 3 weeks (~March 10, 2026). This must happen before Sprint 6.
5. **Interoperability:** No Circle Trust-specific design at this stage. Architecture should remain interoperable (standard chain entry types, configurable formula) but not optimize for any external deployment. Techne-first.

## Open Questions

- **Privacy model:** Chain entries contain member names and contribution amounts. Who can read the chain? Members only? Public? Configurable per entry type?

---

*TIO · Technology and Information Office · Techne Institute · February 2026*
