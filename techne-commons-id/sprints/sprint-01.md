# Sprint 1 — Chain Engine

**Goal:** Add a perpetual convergence chain to the existing commons.id Supabase project. Genesis entry for Techne. Member entries for founding organizers. Verify the chain is append-only and hash-linked.

**Prerequisite decisions:** All resolved (Feb 18).
**Estimated effort:** 1 sprint (~1 week)

---

## 1. Schema: `chain_entries` table

New table in the existing Supabase project alongside `events`, `artifacts`, `convergences`, etc.

```sql
-- Supabase migration: create chain_entries table
create table public.chain_entries (
  id uuid primary key default gen_random_uuid(),
  chain_index bigint not null,
  convergence_id uuid not null references public.convergences(id),
  
  -- Event identity
  event_type text not null,           -- e.g. 'convergence.created', 'people.member.created'
  aggregate_id uuid not null,         -- entity this affects
  aggregate_type text not null,       -- 'convergence' | 'member' | 'contribution' | 'period' | 'allocation'
  
  -- Payload
  payload jsonb not null default '{}',
  
  -- Pattern layer (1-7)
  pattern_layer smallint not null check (pattern_layer between 1 and 7),
  
  -- Merkle chain
  content_hash text not null,         -- SHA-256 of (prev_hash || event_type || aggregate_id || payload_canonical)
  prev_hash text not null,            -- hash of previous entry ('genesis' for entry #0)
  
  -- Metadata
  actor_id uuid,                      -- who triggered this (references participants)
  correlation_id uuid,                -- groups related entries
  causation_id uuid,                  -- entry that caused this entry
  nl_source text,                     -- original natural language input (Sprint 3)
  schema_version text not null default '1.0',
  
  created_at timestamptz not null default now(),
  
  -- Constraints
  unique (convergence_id, chain_index),
  unique (content_hash)
);

-- Sequential index integrity: no gaps
create index idx_chain_entries_convergence_index on public.chain_entries (convergence_id, chain_index);

-- Event type filtering
create index idx_chain_entries_event_type on public.chain_entries (event_type);

-- Aggregate lookups (e.g. "all entries for member X")
create index idx_chain_entries_aggregate on public.chain_entries (aggregate_type, aggregate_id);

-- Correlation grouping
create index idx_chain_entries_correlation on public.chain_entries (correlation_id) where correlation_id is not null;

-- RLS: members of the convergence can read; service role writes
alter table public.chain_entries enable row level security;

-- Read policy: authenticated users who are participants of this convergence
create policy "chain_entries_select" on public.chain_entries
  for select using (true);  -- TODO Sprint privacy: restrict to convergence members

-- Insert policy: service role only (entries come through the API, not direct inserts)
create policy "chain_entries_insert" on public.chain_entries
  for insert with check (auth.role() = 'service_role');

-- No update or delete policies. The chain is append-only.
```

### Why this shape

- **`chain_index`** is a sequential bigint per convergence, not a global counter. Each convergence has its own chain. ETHBoulder's event entries and Techne's economic entries don't share sequence numbers.
- **`content_hash` / `prev_hash`** form the merkle chain. Content hash is computed client-side before insert. The unique constraint on `content_hash` prevents duplicate entries.
- **`pattern_layer`** tags each entry with its position in the seven-layer stack (Identity=1, State=2, Relationship=3, Event=4, Flow=5, Constraint=6, View=7). Enables graph filtering by abstraction level.
- **`payload`** is JSONB — same approach as the existing `events.data` column. Event-type-specific structure validated in application code, not database constraints.
- **No UPDATE or DELETE** — enforced at the RLS level. The chain is append-only. Corrections create new entries (e.g. `contribution.voided` references the original via `causation_id`).

---

## 2. Convergence type: `perpetual`

Extend the `convergence_state` enum or add a `convergence_type` column:

```sql
-- Option A: Add convergence_type column (preferred — state and type are orthogonal)
alter table public.convergences
  add column convergence_type text not null default 'bounded'
  check (convergence_type in ('bounded', 'perpetual'));

-- Perpetual convergences have no date_end requirement
-- Bounded convergences (ETHBoulder) keep existing behavior
```

This is cleaner than overloading `convergence_state`. A perpetual convergence can still have states (pre, live, archived) — it's just never expected to reach `post` under normal operation.

---

## 3. TypeScript types

New file: `app-src/src/types/chain.ts`

```typescript
/**
 * Chain Entry — the atomic unit of the perpetual convergence chain
 * 
 * Each entry is an immutable, hash-linked record of a cooperative economic event.
 * Capital accounts, balances, and member states are computed views over the chain,
 * not stored independently.
 */

export interface ChainEntry {
  id: string
  chain_index: number
  convergence_id: string
  
  // Event identity
  event_type: ChainEventType
  aggregate_id: string
  aggregate_type: AggregateType
  
  // Payload (event-specific, validated per event_type)
  payload: Record<string, unknown>
  
  // Seven-layer classification
  pattern_layer: PatternLayer
  
  // Merkle chain
  content_hash: string
  prev_hash: string     // 'genesis' for entry #0
  
  // Metadata
  actor_id?: string
  correlation_id?: string
  causation_id?: string
  nl_source?: string
  schema_version: string
  
  created_at: string
}

export type PatternLayer = 1 | 2 | 3 | 4 | 5 | 6 | 7

export const PATTERN_LAYER_NAMES: Record<PatternLayer, string> = {
  1: 'Identity',
  2: 'State',
  3: 'Relationship',
  4: 'Event',
  5: 'Flow',
  6: 'Constraint',
  7: 'View',
}

export type AggregateType = 
  | 'convergence'
  | 'member'
  | 'contribution'
  | 'period'
  | 'allocation'
  | 'distribution'
  | 'account'
  | 'transaction'

/**
 * Chain event types — REA-based, ported from habitat event registry
 * Format: domain.entity.action
 */
export type ChainEventType =
  // Convergence lifecycle
  | 'convergence.created'
  | 'convergence.updated'
  // People
  | 'people.member.created'
  | 'people.member.updated'
  // Contributions
  | 'people.contribution.created'
  | 'people.contribution.submitted'
  | 'people.contribution.valued'
  | 'people.contribution.approved'
  | 'people.contribution.rejected'
  | 'people.contribution.voided'
  // Treasury
  | 'treasury.period.opened'
  | 'treasury.period.closed'
  | 'treasury.transaction.posted'
  | 'treasury.transaction.voided'
  | 'treasury.account.created'
  // Agreements
  | 'agreements.allocation.created'
  | 'agreements.allocation.proposed'
  | 'agreements.allocation.approved'
  | 'agreements.distribution.scheduled'
  | 'agreements.distribution.completed'
  | 'agreements.capitalAccount.updated'
  // Compliance
  | 'compliance.check.passed'
  | 'compliance.check.failed'

/**
 * Typed payloads for Sprint 1 event types
 */
export interface ConvergenceCreatedPayload {
  name: string
  type: 'perpetual' | 'bounded'
  description?: string
  location?: string
  startDate: string  // ISO 8601
}

export interface MemberCreatedPayload {
  memberNumber: string
  displayName: string
  tier: 'cooperative' | 'community'
  ensName?: string
  walletAddress?: string
}
```

---

## 4. Chain engine: hash computation and append

New file: `app-src/src/lib/chain-engine.ts`

```typescript
/**
 * Chain Engine — append-only merkle chain operations
 * 
 * Core functions:
 * - computeHash: SHA-256 of (prevHash + eventType + aggregateId + canonicalPayload)
 * - appendEntry: compute hash, insert into Supabase, return entry
 * - verifyChain: walk from genesis, recompute all hashes, report violations
 * - getChainHead: return the latest entry for a convergence
 */

import { supabase } from './supabase'
import type { ChainEntry, ChainEventType, AggregateType, PatternLayer } from '../types/chain'

/**
 * Compute content hash for a chain entry
 * SHA-256 of: prevHash || eventType || aggregateId || JSON.stringify(payload, sorted keys)
 */
export async function computeHash(
  prevHash: string,
  eventType: string,
  aggregateId: string,
  payload: Record<string, unknown>
): Promise<string> {
  const canonical = JSON.stringify(payload, Object.keys(payload).sort())
  const input = `${prevHash}|${eventType}|${aggregateId}|${canonical}`
  const encoder = new TextEncoder()
  const data = encoder.encode(input)
  const hashBuffer = await crypto.subtle.digest('SHA-256', data)
  const hashArray = Array.from(new Uint8Array(hashBuffer))
  return hashArray.map(b => b.toString(16).padStart(2, '0')).join('')
}

/**
 * Append an entry to the chain
 * Fetches the current head, computes the next hash, inserts atomically
 */
export async function appendEntry(params: {
  convergenceId: string
  eventType: ChainEventType
  aggregateId: string
  aggregateType: AggregateType
  payload: Record<string, unknown>
  patternLayer: PatternLayer
  actorId?: string
  correlationId?: string
  causationId?: string
  nlSource?: string
}): Promise<ChainEntry> {
  // Get current chain head
  const head = await getChainHead(params.convergenceId)
  const prevHash = head ? head.content_hash : 'genesis'
  const nextIndex = head ? head.chain_index + 1 : 0
  
  // Compute content hash
  const contentHash = await computeHash(
    prevHash,
    params.eventType,
    params.aggregateId,
    params.payload
  )
  
  const entry = {
    chain_index: nextIndex,
    convergence_id: params.convergenceId,
    event_type: params.eventType,
    aggregate_id: params.aggregateId,
    aggregate_type: params.aggregateType,
    payload: params.payload,
    pattern_layer: params.patternLayer,
    content_hash: contentHash,
    prev_hash: prevHash,
    actor_id: params.actorId,
    correlation_id: params.correlationId,
    causation_id: params.causationId,
    nl_source: params.nlSource,
    schema_version: '1.0',
  }
  
  const { data, error } = await supabase
    .from('chain_entries')
    .insert(entry)
    .select()
    .single()
  
  if (error) throw new Error(`Chain append failed: ${error.message}`)
  return data as ChainEntry
}

/**
 * Get the latest chain entry for a convergence
 */
export async function getChainHead(
  convergenceId: string
): Promise<ChainEntry | null> {
  const { data, error } = await supabase
    .from('chain_entries')
    .select('*')
    .eq('convergence_id', convergenceId)
    .order('chain_index', { ascending: false })
    .limit(1)
    .maybeSingle()
  
  if (error) throw new Error(`Chain head fetch failed: ${error.message}`)
  return data as ChainEntry | null
}

/**
 * Verify chain integrity from genesis
 * Returns list of violations (empty = valid)
 */
export async function verifyChain(
  convergenceId: string
): Promise<{ valid: boolean; violations: string[]; entriesChecked: number }> {
  const violations: string[] = []
  
  const { data: entries, error } = await supabase
    .from('chain_entries')
    .select('*')
    .eq('convergence_id', convergenceId)
    .order('chain_index', { ascending: true })
  
  if (error) throw new Error(`Chain fetch failed: ${error.message}`)
  if (!entries || entries.length === 0) {
    return { valid: true, violations: [], entriesChecked: 0 }
  }
  
  // Entry #0 must have prev_hash = 'genesis'
  if (entries[0].prev_hash !== 'genesis') {
    violations.push(`Entry #0: prev_hash should be 'genesis', got '${entries[0].prev_hash}'`)
  }
  
  for (let i = 0; i < entries.length; i++) {
    const entry = entries[i]
    
    // Verify chain_index is sequential
    if (entry.chain_index !== i) {
      violations.push(`Entry #${i}: expected chain_index ${i}, got ${entry.chain_index}`)
    }
    
    // Verify content hash
    const expectedPrev = i === 0 ? 'genesis' : entries[i - 1].content_hash
    if (entry.prev_hash !== expectedPrev) {
      violations.push(`Entry #${i}: prev_hash mismatch`)
    }
    
    const recomputed = await computeHash(
      entry.prev_hash,
      entry.event_type,
      entry.aggregate_id,
      entry.payload as Record<string, unknown>
    )
    if (entry.content_hash !== recomputed) {
      violations.push(`Entry #${i}: content_hash mismatch (stored: ${entry.content_hash.slice(0,12)}..., computed: ${recomputed.slice(0,12)}...)`)
    }
  }
  
  return {
    valid: violations.length === 0,
    violations,
    entriesChecked: entries.length,
  }
}

/**
 * Get chain entries filtered by type, aggregate, or time range
 */
export async function queryChain(params: {
  convergenceId: string
  eventType?: ChainEventType
  aggregateType?: AggregateType
  aggregateId?: string
  patternLayer?: PatternLayer
  fromIndex?: number
  toIndex?: number
  limit?: number
}): Promise<ChainEntry[]> {
  let query = supabase
    .from('chain_entries')
    .select('*')
    .eq('convergence_id', params.convergenceId)
    .order('chain_index', { ascending: true })
  
  if (params.eventType) query = query.eq('event_type', params.eventType)
  if (params.aggregateType) query = query.eq('aggregate_type', params.aggregateType)
  if (params.aggregateId) query = query.eq('aggregate_id', params.aggregateId)
  if (params.patternLayer) query = query.eq('pattern_layer', params.patternLayer)
  if (params.fromIndex !== undefined) query = query.gte('chain_index', params.fromIndex)
  if (params.toIndex !== undefined) query = query.lte('chain_index', params.toIndex)
  if (params.limit) query = query.limit(params.limit)
  
  const { data, error } = await query
  if (error) throw new Error(`Chain query failed: ${error.message}`)
  return (data || []) as ChainEntry[]
}
```

---

## 5. Genesis script

Executable script to seed the Techne convergence and founding members:

```typescript
// scripts/genesis-techne.ts
// Run once to create the Techne perpetual convergence and seed founding members

import { appendEntry } from '../src/lib/chain-engine'

const TECHNE_CONVERGENCE_ID = 'techne-regenhub-2026' // or UUID from convergences table

const FOUNDING_MEMBERS = [
  { number: '001', name: 'Aaron G Neyer', tier: 'cooperative' },
  { number: '002', name: 'Benjamin Ross', tier: 'cooperative' },
  { number: '003', name: 'Jonathan Borichevskiy', tier: 'cooperative' },
  { number: '004', name: 'Kevin Owocki', tier: 'cooperative' },
  { number: '005', name: 'Lucian Hymer', tier: 'cooperative' },
  { number: '006', name: 'Neil Mackay Yarnal', tier: 'cooperative' },
  { number: '007', name: 'Savannah Kruger', tier: 'cooperative' },
  { number: '008', name: 'Todd Youngblood', tier: 'cooperative' },
] as const

async function genesis() {
  console.log('=== Techne Genesis ===')
  
  // Entry #0: convergence.created
  const genesis = await appendEntry({
    convergenceId: TECHNE_CONVERGENCE_ID,
    eventType: 'convergence.created',
    aggregateId: TECHNE_CONVERGENCE_ID,
    aggregateType: 'convergence',
    payload: {
      name: 'Techne',
      type: 'perpetual',
      description: 'RegenHub LCA — cooperative patronage chain',
      location: 'Boulder, Colorado',
      startDate: '2026-02-06T00:00:00Z', // LCA filing date
    },
    patternLayer: 1, // Identity
  })
  console.log(`Entry #0: ${genesis.event_type} [${genesis.content_hash.slice(0,12)}...]`)
  
  // Entries #1-8: founding members
  for (const member of FOUNDING_MEMBERS) {
    const memberId = crypto.randomUUID()
    const entry = await appendEntry({
      convergenceId: TECHNE_CONVERGENCE_ID,
      eventType: 'people.member.created',
      aggregateId: memberId,
      aggregateType: 'member',
      payload: {
        memberNumber: member.number,
        displayName: member.name,
        tier: member.tier,
      },
      patternLayer: 1, // Identity
      correlationId: genesis.id, // All genesis-related
    })
    console.log(`Entry #${entry.chain_index}: ${member.name} [${entry.content_hash.slice(0,12)}...]`)
  }
  
  console.log('\n=== Genesis complete: 9 entries ===')
}

genesis().catch(console.error)
```

---

## 6. Deliverables checklist

- [ ] Supabase migration: `chain_entries` table with indexes and RLS
- [ ] Supabase migration: `convergence_type` column on `convergences`
- [ ] `src/types/chain.ts` — ChainEntry, ChainEventType, payloads
- [ ] `src/lib/chain-engine.ts` — computeHash, appendEntry, verifyChain, queryChain, getChainHead
- [ ] `scripts/genesis-techne.ts` — seed convergence + 8 founding members
- [ ] Run genesis script → 9 chain entries in production Supabase
- [ ] Run `verifyChain()` → all 9 entries pass hash verification
- [ ] Manual test: attempt UPDATE on chain_entries → blocked by RLS
- [ ] Manual test: attempt DELETE on chain_entries → blocked by RLS

## 7. What this does NOT include (deferred)

- Contribution lifecycle (Sprint 4)
- Natural language parsing (Sprint 3)
- Patronage formula (Sprint 6)
- Graph visualization of chain (Sprint 5)
- Period open/close (Sprint 7)
- On-chain hash anchoring (future, after chain has entries worth anchoring)
- Privacy model / RLS refinement (open question, before public launch)

---

## 8. Concurrency note

The `appendEntry` function reads the current head then inserts. Two concurrent appends could race. For Sprint 1 this is fine — there's one writer (the genesis script, then manual API calls). Sprint 4 will need a Supabase RPC function or advisory lock to serialize appends when contribution ingestion becomes concurrent.

---

*TIO Sprint 1 · Chain Foundation · February 2026*
