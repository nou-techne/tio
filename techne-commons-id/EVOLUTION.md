# Techne commons.id — Evolutionary Roadmap

**Method:** Ebb-flow sprint cycles, self-evolving. Sub-agent executes sprints autonomously. Blockers are skipped and revisited. Progress logged to SPRINT_QUEUE.md.

## Cycle Structure

Each cycle = 8 sprints. Ebb = foundation/infrastructure. Flow = features/integration.

### Cycle 1: Chain Foundation (Q32–Q39)

**Ebb (Q32–Q35):** Schema, engine, convergence scaffolding
- ✅ Q32: Supabase migration — `chain_entries` table + `convergence_type` column
- Q33: `types/chain.ts` — ChainEntry, ChainEventType, typed payloads
- Q34: `lib/chain-engine.ts` — computeHash, appendEntry, verifyChain, queryChain
- ✅ Q35: Genesis script — seed Techne convergence + 8 founding members

**Flow (Q36–Q39):** App integration, convergence routing, Techne visibility
- ✅ Q36: ConvergenceProvider context + convergence picker at `/app/`
- Q37: Replace hardcoded convergence ID — all pages read from context
- ✅ Q38: Techne theme in convergence config (copper/alpine/gold)
- Q39: Chain explorer page — browse chain entries for any convergence

### Cycle 2: Contribution Chain (Q40–Q47) ✅ COMPLETE

**Ebb (Q40–Q43):** Contribution types, NL parser, lifecycle state machine
- ✅ Q40: Contribution chain entry types + five-stage lifecycle schema
- ✅ Q41: NL contribution parser (rule-based + LLM extraction → typed payloads)
- ✅ Q42: Contribution lifecycle workflow (state machine with validated transitions)
- ✅ Q43: Double-entry transaction engine on contribution approval

**Flow (Q44–Q47):** Input surfaces, member views, cross-references
- ✅ Q44: ContributionSubmitForm with real-time NL parser preview
- ✅ Q45: MemberContributionHistory component (filterable by category)
- ✅ Q46: Cross-convergence participant linking (auth_user_id + name match)
- ✅ Q47: Chain integrity verification script (cron-ready, all convergences)

**Cycle 2 Notes:**
- Q32 (chain_entries table) still BLOCKED — code is ready but needs admin migration
- NL parser is heuristic/rule-based (no external API dependency), with LLM extraction optional
- Double-entry engine uses standard account ID format: `capital:{memberId}`, `expense:patronage:{convergenceId}`
- Participant linking works across convergences via auth_user_id (strong) or name match (fuzzy)

### Cycle 3: Patronage Engine (Q48–Q55) ✅ COMPLETE

**Ebb (Q48–Q51):** Formula, period mechanics, compliance
- ✅ Q48: Port PatronageFormulaEngine from habitat (variable weights)
- ✅ Q49: Period open/close chain entries + period lifecycle
- ✅ Q50: Allocation calculation → chain entries (formula inputs + outputs recorded)
- ✅ Q51: Compliance check entries (704b validator, double-entry checker)

**Flow (Q52–Q55):** Dashboards, statements, governance
- ✅ Q52: MemberCapitalDashboard component (balance, YTD summary, transactions, pending)
- ✅ Q53: K-1 export (Schedule K-1 Form 1065) + allocation statements
- ✅ Q54: Period close governance (democratic voting, quorum, 7-day window)
- ✅ Q55: Chain anchor to Base L2 (mock impl + Solidity contract reference)

**Cycle 3 Notes:**
- Patronage formula supports variable category weights (configurable per period)
- Period lifecycle: open → contributions → close (governance) → allocations finalized
- K-1 generation ready for CPA review (CSV export, proper IRS Schedule K-1 structure)
- Governance: 1 member = 1 vote (democratic, not capital-weighted), 50% quorum, simple majority
- Base anchoring: placeholder implementation (needs RPC + wallet for production deployment)
- ChainAnchorRegistry.sol included for Base deployment (~$0.001/anchor gas cost)

### Cycle 4: Venture Royalties Engine (Q56–Q63) ✅ COMPLETE

Techne is a venture studio. Members co-create tools and technologies that generate revenue.
The royalties system runs *in parallel* with patronage — patronage tracks cooperative contributions
(labor, coordination, stewardship), while royalties track future revenue share from co-created
venture IP. Both feed into the same capital account infrastructure but with distinct accounting streams.

**Ebb (Q56–Q59):** Venture registry, royalty agreements, revenue events
- ✅ Q56: Venture registry chain entries — `venture.created`, `venture.updated`, `venture.archived`. Each venture has: name, description, members involved, revenue model, status (ideation → active → generating → sunset). Links to convergence as parent entity.
- ✅ Q57: Royalty agreement types + chain entries — `agreements.royalty.created`, `.activated`, `.modified`, `.terminated`. Defines: venture ID, member shares (%), vesting schedule, triggering conditions (revenue threshold, time-based), dilution rules for new contributors. Agreements are immutable chain entries; modifications create new versions with prev-agreement linkage.
- ✅ Q58: Revenue event chain entries — `treasury.revenue.received`, `.allocated`, `.distributed`. Revenue flows: external payment → venture revenue account → royalty allocation (per agreement shares) → member royalty accounts. Double-entry: debit revenue receivable, credit venture revenue; then debit venture revenue, credit member royalty accounts per share.
- ✅ Q59: Royalty vesting engine — time-based and milestone-based vesting. Chain entries for `agreements.royalty.vested`, `agreements.royalty.cliff_reached`. Computes vested vs. unvested portions. Cliff periods, linear vesting, milestone triggers (e.g., "vests when venture reaches $X revenue").

**Flow (Q60–Q63):** Venture dashboards, member views, projections
- ✅ Q60: Venture portfolio page — list all ventures, status, team, revenue to date, active royalty agreements. Filterable by status and member involvement.
- ✅ Q61: Member royalties dashboard — shows each venture involvement, vested/unvested shares, accumulated royalties, projected future revenue (based on current trajectory). Integrated with existing capital account view from Q52.
- ✅ Q62: Royalty agreement builder — UI for creating/proposing royalty agreements. Supports: equal split, weighted by contribution, custom percentages. Preview of vesting timeline. Requires governance approval (reuses Q54 approval workflow).
- ✅ Q63: Venture revenue reconciliation

**Cycle 4 Notes:**
- Venture types (venture.ts): 6 statuses, 7 revenue models, 5 vesting types, 4 dilution rules
- Venture engine (venture-engine.ts): CRUD + revenue allocation + distribution + vesting processor
- VentureView projection from chain entries (like ContributionView)
- Revenue → allocation respects vesting: only vested portions are distributable
- Double-entry integration: revenue debit → member royalty account credit
- Agreement shares validate ≤ 100%, support unallocated pool for future contributors
- Reconciliation engine: import with dedup, auto-allocate, discrepancy reports, CSV export — import revenue events (manual or API), match to ventures, trigger automatic royalty allocation. Reconciliation report shows: expected vs. actual allocations, any discrepancies, audit trail.

### Cycle 5: Member Education & Accessibility (Q64–Q71) ✅ COMPLETE

Cooperative economics is unfamiliar territory for most people. The system must teach as it operates.
Every interface should make the *why* as clear as the *what*. This cycle adds contextual education,
progressive disclosure, and training pathways that make patronage, royalties, and cooperative
governance genuinely accessible — not just technically available.

**Ebb (Q64–Q67):** Content system, contextual help, learning paths
- ✅ Q64: Education content schema — glossary, explainers, paths, help contexts map
- ✅ Q65: Contextual help system — tooltip/popover with "Learn more" progressive disclosure
- ✅ Q66: Learning path engine — structured sequences, progress tracking, content suggestions
- ✅ Q67: Glossary chain entries — versioned terms, CRUD, core seed terms (capital account, patronage)

**Flow (Q68–Q71):** Training UI, onboarding flow, community writer tooling
- ✅ Q68: Onboarding wizard — interactive "This is your capital account" walkthrough
- ✅ Q69: Education hub — `/app/learn` with search, glossary browser, path directory
- ✅ Q70: Community writer toolkit — editor for TIO-08 with style enforcement (plain language)
- ✅ Q71: Training analytics — dashboard for content engagement and path completion

**Cycle 5 Notes:**
- Education is first-class infrastructure, not just documentation
- Content is stored on-chain (glossary) or derived from it
- ContextualHelp component allows dropping explanations into any UI element
- Style guide enforcement helps keep technical concepts accessible

### Cycle 6: Integration & Polish (Q72–Q79) ✅ COMPLETE

**Ebb (Q72–Q75):** Cross-system integration, performance, security
- ✅ Q72: Unified member profile — single view combining: patronage contributions, venture royalties, learning progress, governance participation. The "whole member" view.
- ✅ Q73: Notification system — chain-event-driven notifications. "Your contribution was approved." "Royalty payment received." "New learning content about a feature you used." "Period close vote starting."
- ✅ Q74: Audit trail viewer — comprehensive chain history for any entity (member, venture, contribution, agreement). Timeline view with event descriptions. Export for compliance review.
- ✅ Q75: Performance optimization — pagination, caching, lazy loading for chain queries. Denormalized views for dashboard performance. Background chain verification.

**Flow (Q76–Q79):** Public views, ecosystem integration, launch readiness
- ✅ Q76: Public venture portfolio — non-authenticated view of Techne ventures (opt-in per venture). Shows: what Techne builds, who's involved (with consent), how the cooperative model works. Recruitment surface.
- ✅ Q77: Ecosystem interop — Bonfires.ai activity → contribution suggestions. ETHBoulder participation → member activity feed. External revenue API webhooks for venture income.
- ✅ Q78: Mobile responsiveness audit — ensure all flows work on mobile. Contribution submission, dashboard viewing, education content, governance voting.
- ✅ Q79: Launch checklist — blocker review (Q32 et al.), data migration plan, member communication, onboarding materials, go-live verification.

### Cycle 7: Hardening & Operational Readiness (Q80–Q87)

Now that the chain is live in production with 9 entries and all blockers resolved,
this cycle focuses on making the system production-grade.

**Ebb (Q80–Q83):** Testing, error handling, schema fixes
- Q80: Fix pre-existing test failure (missing `slug` column on convergences) + add `slug` migration
- Q81: Unit tests for chain-engine (computeHash, appendEntry, verifyChain, queryChain)
- Q82: Unit tests for patronage-engine (formula correctness, edge cases, zero-weight)
- Q83: Error boundary components + graceful degradation for chain read failures

**Flow (Q84–Q87):** Coordinator tools, REST API, documentation
- Q84: Coordinator review queue UI (pending contributions, validations, approvals)
- Q85: REST API endpoints for external integrations (contribution submit, chain read, member lookup)
- Q86: Comprehensive README + onboarding guide for new convergences
- Q87: Integration test — full contribution lifecycle (NL input → chain entry → capital account credit)

### Cycle 8: Wiring & Build Verification (Q88–Q95)

All modules exist but aren't connected. This cycle wires routes, verifies the build compiles,
and ensures the app is deployable.

**Ebb (Q88–Q91):** Routing, navigation, build
- Q88: Wire all new pages into App.tsx router (MemberProfile, AuditTrail, CoordinatorQueue, EducationHub, VenturePortfolio, PublicPortfolio, OnboardingWizard)
- Q89: App navigation update — add Techne-specific nav items (patronage, ventures, learn, audit)
- Q90: Build verification — fix all TypeScript/import errors, ensure `vite build` succeeds
- Q91: Supabase RPC audit — ensure all referenced RPCs exist or have graceful fallbacks

**Flow (Q92–Q95):** Data seeding, deployment prep
- Q92: Seed sample contribution data on chain (test the full append → query pipeline in production)
- Q93: App config — environment detection (ETHBoulder vs Techne convergence routing)
- Q94: Deployment checklist script (automated pre-deploy validation)
- Q95: Production build + deploy verification

### Cycle 9: Make It Real (Q96–Q103)

The app exists but isn't usable. Fix that.

**Ebb (Q96–Q99):** SPA routing, convergence switching, visibility
- Q96: Fix GitHub Pages SPA routing (404.html redirect for client-side routes)
- Q97: Convergence switcher — UI to toggle between ETHBoulder and Techne
- Q98: Chain status widget on main page — show live chain stats for current convergence
- Q99: Sprint progress dashboard — visualize Q32-Q95 completion in the app itself

**Flow (Q100–Q103):** Techne landing, member directory, self-service
- Q100: Techne landing page at /techne — cooperative overview, chain stats, member count
- Q101: Member directory — public view of founding members (from chain genesis entries)
- Q102: Contribution submission flow — end-to-end working form that appends to chain
- Q103: Self-evolving roadmap — EVOLUTION.md rendered in-app, auto-updated by sprint runner

---

### Cycle 4 (Original — Superseded by Cycles 4-6 above): Integration & Polish (Q56–Q63)

**Ebb (Q56–Q59):** Testing, error handling, API hardening
- Q56: Unit tests for patronage-engine (formula correctness, edge cases)
- Q57: Unit tests for compliance-engine (each check type, pass/fail scenarios)
- Q58: Integration test: full contribution lifecycle (NL input → capital account credit)
- Q59: Error boundary components + graceful degradation for chain read failures

**Flow (Q60–Q63):** User-facing features, polish, documentation
- Q60: Coordinator review queue UI (pending validations, valuations, approvals)
- Q61: Convergence-scoped API endpoints (REST) for external integrations
- Q62: Comprehensive README + onboarding guide for new convergences
- Q63: Performance optimization — chain query caching + pagination

## Blocker Rules

1. **Never idle.** If a sprint is blocked, mark it `BLOCKED: {reason}`, skip to next unblocked sprint.
2. **Revisit blockers** at the start of each new cycle — check if any BLOCKED sprints are now unblocked.
3. **Adapt scope.** If a sprint reveals the next sprint needs different inputs, rewrite the next sprint scope before executing. Log the change.
4. **No Todd intervention needed** for: schema decisions within the roadmap, code architecture, file structure, build issues, test failures, dependency updates.
5. **Flag for Todd** only: Supabase admin operations requiring dashboard access, DNS/domain changes, decisions explicitly marked "open question" in ROADMAP.md, anything touching production data destructively.

## Evolution Protocol

After each sprint:
1. Log completion in SPRINT_QUEUE.md
2. If the sprint revealed something that changes subsequent sprints, update this file
3. If a blocker was encountered, document it and what was done instead
4. Commit and push all changes

After each cycle (8 sprints):
1. Review what was built vs. what was planned
2. Rewrite the next cycle based on what was learned
3. Update ROADMAP.md if the architecture has evolved
4. Log cycle summary in memory/YYYY-MM-DD.md
