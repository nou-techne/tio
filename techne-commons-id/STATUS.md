# commons.id — System Status

**Last updated:** 2026-02-18T06:00Z
**Sprint runner status:** IDLE — awaiting operational inputs

## What's Built (Q32–Q95, 64 sprints)

### Infrastructure
- **Append-only merkle chain** — live in production, 11 entries, integrity verified
- **Genesis:** Techne convergence + 8 founding members on chain
- **Sample contribution** seeded and approved ($5,600 credit)

### Engines (lib/)
| Module | Purpose | Tests |
|--------|---------|-------|
| `chain-engine.ts` | Hash, append, verify, query | 7 |
| `contribution-parser.ts` | NL → structured contribution | 19 |
| `contribution-workflow.ts` | Submit → validate → approve pipeline | — |
| `double-entry.ts` | Debit/credit from chain events | — |
| `patronage-engine.ts` | Weighted formula + IRC 1385 allocation | 8 |
| `period-lifecycle.ts` | Period open/close management | — |
| `compliance-engine.ts` | Subchapter K compliance checks | — |
| `venture-engine.ts` | Venture CRUD + royalty management | — |
| `revenue-reconciliation.ts` | Revenue import + auto-allocate | — |
| `education-engine.ts` | Learning paths + glossary | — |
| `notification-engine.ts` | Chain-event → member notifications | — |
| `chain-cache.ts` | LRU cache + pagination + prefetch | — |
| `ecosystem-interop.ts` | Bonfires.ai + ETHBoulder + webhooks | — |
| `k1-export.ts` | IRS K-1 schedule generation | — |
| `chain-anchor.ts` | Base L2 anchoring reference | — |
| `rest-api.ts` | Typed API layer for external tools | — |
| `app-config.ts` | Environment detection + feature flags | — |
| `responsive.ts` | Mobile breakpoints + touch targets | — |
| `theme-engine.ts` | Convergence-driven CSS theming | — |

### Pages (14 new)
MemberProfile, AuditTrail, CoordinatorQueue, EducationHub, OnboardingWizard,
VenturePortfolio, MemberRoyaltiesDashboard, MemberCapitalDashboard,
MemberContributionHistory, PublicPortfolio, ContributionSubmitForm,
RoyaltyAgreementBuilder, TrainingAnalytics, EducationEditor

### Build
- TypeScript: clean (`tsc --noEmit` passes)
- Vite: builds in 4.5s, all pages code-split
- Tests: 64 passing, 13 skipped, 1 pre-existing RLS failure

## What's Needed Next (Not Sprint Runner Work)

These require human/committee decisions or operational access:

1. **Financial Engineering Committee** (~March 10) — set patronage weights
   - Currently no hardcoded defaults; engine accepts any weight configuration
   
2. **Deploy to production** — `vite build` output needs hosting update
   - Todd decision: subdomain (techne.commons.id) vs path (/techne)

3. **Real member onboarding** — founding members need accounts + walkthrough
   - OnboardingWizard is built; needs actual member sessions

4. **Content seeding** — education glossary has 4 terms; needs 20+ from committee

5. **Patronage weight parameterization** — blocked on committee formation

## Sprint Runner Reactivation Triggers

Resume sprinting when any of these arrive:
- Financial Engineering Committee decisions → implement weight config
- Deploy feedback → fix bugs, adjust UI
- New feature request from Todd/committee
- Bonfires.ai API key → enable two-way sync
- Real contribution data → tune NL parser
