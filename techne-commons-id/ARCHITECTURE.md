# Sibling Architecture: ETHBoulder + Techne on commons.id

**Question:** How do we add Techne's chain engine without breaking ETHBoulder's existing knowledge graph — and ensure both can grow, share participants, and cross-reference?

---

## What Exists Now

The commons.id/app is effectively **single-tenant ETHBoulder**:

- **One convergence** in the `convergences` table: `00000000-0000-0000-0000-000000000100` (ETHBoulder 2026)
- **All data scoped by `convergence_id`**: artifacts, events, sessions, participants, channels — every table has a `convergence_id` FK
- **Scoping utilities exist** (`convergence-scope.ts`): `applyScope()`, `scopeMultiple()`, `mergeScopes()`, `groupByConvergence()` — all already written but used only for ETHBoulder
- **Active convergence loaded via RPC**: `get_active_convergence` returns config (theme, dimensions, name)
- **Hardcoded in one place**: `Contribute.tsx` has `const CONVERGENCE_ID = '00000000-...-000000000100'`
- **App mounts at `/app`**: `<BrowserRouter basename="/app">`

The scoping infrastructure is already built. It just hasn't been exercised with more than one convergence.

---

## The Sibling Model

ETHBoulder and Techne are **siblings** — two convergences in the same Supabase project, sharing infrastructure but with distinct data, types, and lifecycles.

### What they share

| Layer | Shared? | Notes |
|---|---|---|
| **Supabase project** | Yes | Same database, same auth, same RLS policies |
| **Participants** | Yes | A person who spoke at ETHBoulder and is a Techne member is ONE participant with TWO convergence associations |
| **Auth / identity** | Yes | One login, multiple convergence memberships |
| **App shell** | Yes | Same React app, same nav, same graph component |
| **Cross-references** | Yes | `cross_convergence_refs` table already typed (`cross-convergence.ts`) |
| **API server** | Yes | Same `api.commons.id` with convergence-scoped endpoints |

### What they own separately

| Layer | Separate? | Notes |
|---|---|---|
| **Convergence record** | Yes | Each gets a row in `convergences` with its own theme, dimensions, config |
| **Artifacts / events** | Yes | Scoped by `convergence_id` — ETHBoulder artifacts and Techne chain entries never mix in queries |
| **chain_entries** | Techne only | New table (Sprint 1). ETHBoulder's `events` table continues as-is. Techne writes to `chain_entries` |
| **Theme / branding** | Yes | ETHBoulder: lime/dark. Techne: copper/alpine/gold |
| **Dimensions** | Yes | ETHBoulder: e/H-LAM/T/S. Techne: seven-layer pattern stack |
| **Landing page** | Yes | `/ethboulder/` and `/techne/` are static HTML siblings |

---

## Routing: How the App Knows Which Sibling

### Option A: URL path prefix (recommended)

```
commons.id/app/                     → convergence picker (new, simple)
commons.id/app/c/ethboulder-2026/   → ETHBoulder scoped app
commons.id/app/c/techne/            → Techne scoped app
```

This is already anticipated in the commons.id lander:
> `commons.id/app/c/ethboulder-2026/` — The platform filtered to a single event

**Implementation:**
- Add a route: `<Route path="/c/:convergenceSlug/*" element={<ConvergenceScopedApp />} />`
- `ConvergenceScopedApp` loads the convergence config by slug, sets it in context
- All child pages read convergence from context instead of `DEFAULT_CONFIG` or hardcoded ID
- Root `/app/` becomes a simple picker: "ETHBoulder 2026 · Techne" (two cards)

**ETHBoulder keeps working:** The current `/app/` routes can remain as-is during transition, defaulting to ETHBoulder. Once the convergence picker exists, `/app/` redirects to the picker and `/app/c/ethboulder-2026/` serves the scoped view.

### Option B: Subdomain routing

```
ethboulder.commons.id/app/    → ETHBoulder
techne.commons.id/app/        → Techne
```

Already described in the commons.id lander as a partner feature. More complex (DNS, SSL, app config per subdomain). Not needed for siblings under the same org. Save for external partners.

**Recommendation: Option A.** Path-based. Simple. Already anticipated in the URL namespace design.

---

## Database Changes

### New table: `chain_entries` (Sprint 1)

Already scoped in the sprint plan. Key point: `chain_entries` has `convergence_id` — it belongs to Techne's convergence. ETHBoulder doesn't use this table. Techne doesn't use ETHBoulder's `events` table for its chain (it has its own event semantics).

### New column: `convergence_type` on `convergences`

```sql
alter table public.convergences
  add column convergence_type text not null default 'bounded'
  check (convergence_type in ('bounded', 'ongoing'));
```

- ETHBoulder 2026: `bounded` (has start/end dates, reaches `post` state)
- Techne: `ongoing` (has start date, no end date, never reaches `post`)
- ETHBoulder 2027: `bounded` (new convergence row, linked to 2026 via cross-references)

### Participant linking (already supported)

The `participants` table is convergence-agnostic — participants exist globally. The association is through `participant_convergences` (or via `convergence_id` on contributions/artifacts). A person who is both an ETHBoulder speaker and a Techne member has:

- One `participants` row (their identity)
- Artifacts/contributions in both convergences (scoped by `convergence_id`)
- Cross-references linking their ETHBoulder talk to their Techne contributions

### ETHBoulder 2027

When ETHBoulder 2027 happens:
1. Create new convergence: `ethboulder-2027` (bounded, new dates)
2. Cross-reference to `ethboulder-2026` (continuation relationship)
3. Participants who return are the same `participants` rows — their history spans both years
4. The 2026 data is read-only (archived state). 2027 accumulates new artifacts.
5. Graph can show cross-year connections: "This idea from 2026 evolved into this proposal in 2027"

---

## App Changes (Minimal)

### 1. Convergence context provider

```typescript
// New: ConvergenceProvider wraps the app
const ConvergenceContext = createContext<ConvergenceConfig>(DEFAULT_CONFIG)

function ConvergenceScopedApp() {
  const { convergenceSlug } = useParams()
  const [config, setConfig] = useState<ConvergenceConfig | null>(null)
  
  useEffect(() => {
    // Load convergence by slug from Supabase
    loadConvergenceBySlug(convergenceSlug).then(setConfig)
  }, [convergenceSlug])
  
  if (!config) return <Loading />
  
  return (
    <ConvergenceContext.Provider value={config}>
      <ThemedApp config={config}>
        <Routes>
          {/* Same routes as today, but all queries use context convergence_id */}
        </Routes>
      </ThemedApp>
    </ConvergenceContext.Provider>
  )
}
```

### 2. Replace hardcoded convergence ID

One change in `Contribute.tsx`:
```diff
- const CONVERGENCE_ID = '00000000-0000-0000-0000-000000000100'
+ const { id: CONVERGENCE_ID } = useConvergence()
```

And similar for any other hardcoded references.

### 3. Theme from convergence config

The `convergences` table already has `theme_primary`, `theme_bg`, `theme_surface`, `theme_border`. When Techne loads, CSS variables update to copper/alpine/gold. When ETHBoulder loads, they update to lime/dark. Same components, different paint.

### 4. Convergence picker at `/app/`

Simple page: two cards. ETHBoulder (lime accent, "Feb 13–16, 2026", "115 sessions"). Techne (copper accent, "Founded Feb 6, 2026", "ongoing"). Click → route to `/app/c/{slug}/`.

---

## Cross-Referencing Between Siblings

The `cross-convergence.ts` types are already built:

```typescript
interface CrossReference {
  sourceConvergenceId: string      // e.g. ethboulder-2026
  targetConvergenceId: string      // e.g. techne
  sourceEntityType: EntityType     // 'participant' | 'artifact' | ...
  sourceEntityId: string
  targetEntityType: EntityType
  targetEntityId: string
  relationship: CrossReferenceRelationship  // 'same_author' | 'builds_on' | ...
}
```

**Example cross-references:**

| Source (ETHBoulder) | Relationship | Target (Techne) |
|---|---|---|
| Kevin Owocki (speaker) | `same_author` | Kevin Owocki (member #004) |
| "Patronage at the Speed of the Sun" talk | `builds_on` | Patronage formula chain entries |
| Civic Finance Forum artifacts | `related_to` | Techne agreements tool |

These links mean the graph can show: "Kevin spoke about X at ETHBoulder → he's now contributing Y to Techne → his capital account reflects Z." A person's full participation across all convergences, unified.

---

## Migration Path (Zero Downtime)

1. **Sprint 1 pre-work:** Add `convergence_type` column, insert Techne convergence row, create `chain_entries` table. ETHBoulder data untouched.
2. **Sprint 1:** Run genesis script — seeds Techne chain. ETHBoulder app still works exactly as before (still reads `DEFAULT_CONFIG`).
3. **Sprint 2–3:** Add `ConvergenceProvider`, convergence picker, replace hardcoded ID. ETHBoulder now loads via `/app/c/ethboulder-2026/` and Techne via `/app/c/techne/`. Old `/app/` routes redirect to picker.
4. **Sprint 4+:** Techne-specific pages (contribution submission, chain explorer, member dashboard) render only when convergence type is `ongoing`.

At no point does ETHBoulder break. The migration is additive — new table, new convergence row, new routes. Existing routes and data are untouched until the redirect is ready.

---

## Summary

```
commons.id/
├── ethboulder/          ← static lander (exists)
├── techne/              ← static lander (exists)
├── app/                 ← convergence picker (new)
│   ├── c/ethboulder-2026/  ← ETHBoulder scoped app
│   ├── c/techne/           ← Techne scoped app
│   └── c/ethboulder-2027/  ← future (same structure)
└── api/                 ← convergence-scoped API (exists)
```

The answer to "how do we save ETHBoulder" is: **we don't have to do anything special**. The scoping infrastructure already exists. ETHBoulder's data lives in the same tables, scoped by its convergence ID. Techne's chain entries live in a new table, scoped by Techne's convergence ID. They share participants and cross-references. The app loads whichever convergence the URL specifies.

They're siblings. Same house, different rooms, shared hallways.

---

*TIO · Architecture Note · February 2026*
