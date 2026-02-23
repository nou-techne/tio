# TIO Procedure: Dependency-Driven Planning for Agent-Driven Development

**Document ID:** TIO-PROC-001  
**Effective Date:** February 23, 2026  
**Applies To:** All TIO product development work  
**Review Cadence:** Quarterly

---

## Purpose

This procedure encodes the principle that **agent-driven development operates on functional dependencies, not time estimates**. Traditional product management uses time-based planning because human labor has predictable constraints (hours per week, cognitive load, coordination overhead). Agent-driven development has different constraints: computational resources, API rate limits, data availability, and dependency resolution.

**Why This Matters:** Time estimates in agent-driven contexts create false precision and obscure the actual constraints. A task that requires 100 API calls at 10/minute takes 10 minutes regardless of "story points." A schema migration either succeeds or fails based on data state, not "effort."

---

## Scope

This procedure applies to:
- All PRDs produced under TIO methodology
- All roadmaps and sprint plans for agent-executed work
- All product documentation where time estimates would traditionally appear

---

## Principle: Dependency-Driven Planning

### Traditional (Human) Planning
```
Story Points → Velocity → Timeline → Release Date
```

**Assumptions:**
- Humans have limited capacity (40 hours/week)
- Coordination overhead scales with team size
- Context switching has cognitive cost
- Estimates improve with experience

### Agent-Driven Planning
```
Functional Dependencies → Resource Requirements → Execution Sequence → Completion
```

**Assumptions:**
- Agents have near-unlimited parallel capacity
- Coordination overhead is near-zero (same agent, shared context)
- Context switching is free (state is persistent)
- Constraints are: API limits, compute resources, data availability, dependency order

---

## Procedure

### 1. PRD Structure (No Time Estimates)

**Remove:**
- ❌ "Phase 1: Months 1-3"
- ❌ "Sprint 1-2: Foundation"
- ❌ "Release 1.0 (Month 1)"
- ❌ Any calendar-based milestones

**Replace With:**
- ✅ "Phase 1: Foundation" (no duration)
- ✅ "Sprint 1: Schema Design" (ordered by dependency, not date)
- ✅ "Release 1.0: MVP" (triggered by feature completion, not date)
- ✅ Explicit dependency declarations

**Example:**

```markdown
### Phase 1: Foundation

**Objective:** Deploy core coordination infrastructure for single-node deployment.

**Dependencies:** None (greenfield)

**Epics (in dependency order):**
1. EPIC-001: Professional Profile System (foundation for all other work)
2. EPIC-003: Capacity Management (independent, but needed for engagements)
3. EPIC-002: Engagement Classification (requires: Profiles, Capacity)
4. EPIC-004: Contribution Ledger (requires: Engagements)
5. EPIC-005: Recognition Levels (requires: Contributions)
```

### 2. Roadmap Structure (Feature-Triggered Releases)

**Remove:**
- ❌ "Month 6: Practice Communities"
- ❌ "Q3 2026: Federation Launch"

**Replace With:**
- ✅ "Release 2.0: Federation" (triggered by: Phase 1 complete + 2nd node onboarded)
- ✅ "Release 3.0: Economic Infrastructure" (triggered by: 5+ nodes + legal entity formed)

**Example:**

```markdown
### Release Triggers

**Release 1.0 (MVP):**
- Trigger: All Phase 1 epics complete + 100% organizer onboarding
- Features: PROF-001-008, ENG-001-006, CAP-001-007, CONT-001-007, REC-001-006

**Release 2.0 (Federation):**
- Trigger: Release 1.0 stable + 2nd node operational + bridge covenant signed
- Features: PC-001-006, BRIDGE-001-006, CNC-001-005, EXP-001-005

**Release 3.0 (Economic Infrastructure):**
- Trigger: Release 2.0 stable + 5+ nodes + legal entity (DIT) incorporated
- Features: CREDIT-001-006, PAT-001-006, TRUST-001-006, FED-001-006
```

### 3. Sprint Queue (Ordered by Dependency)

**Remove:**
- ❌ "Sprint 1: February 24 - March 9"
- ❌ "Sprint 2: March 10 - March 23"

**Replace With:**
- ✅ "Sprint 1: Schema Design" (starts: when PRD approved)
- ✅ "Sprint 2: API Implementation" (starts: when Sprint 1 complete)
- ✅ Explicit prerequisite declarations

**Example:**

```markdown
## Sprint Queue

### Sprint 1: Schema Design
**Prerequisites:** PRD approved, database access configured  
**TIO Role:** Schema Architect  
**Deliverables:**
- DDL migrations for all Phase 1 tables
- ER diagram (visual + Mermaid)
- Data dictionary (field definitions, constraints)

**Completion Criteria:**
- All tables created in staging database
- Foreign key constraints validated
- Schema reviewed by Technical Lead

---

### Sprint 2: API Implementation
**Prerequisites:** Sprint 1 complete, API framework configured  
**TIO Role:** Backend Engineer  
**Deliverables:**
- REST endpoints for all Phase 1 operations
- Unit tests (80%+ coverage)
- API documentation (OpenAPI/Swagger)

**Completion Criteria:**
- All endpoints functional in staging
- Integration tests passing
- API reviewed by Technical Lead
```

### 4. Success Metrics (Outcome-Based, Not Time-Based)

**Remove:**
- ❌ "100 users by Month 3"
- ❌ "50% reduction in burnout by Q2"

**Replace With:**
- ✅ "100 users with complete profiles" (trigger: onboarding complete)
- ✅ "50% reduction in capacity over-allocation" (trigger: 4 weeks of data)

**Example:**

```markdown
### Phase 1 Success Criteria

**Adoption:**
- 100% of Techne organizers have professional profiles (trigger: onboarding complete)
- 50+ engagements created (trigger: 4 weeks of active usage)

**Quality:**
- 99.9% uptime (measured: rolling 30-day window)
- <500ms API response time p95 (measured: continuous)

**Impact:**
- 50% reduction in capacity over-allocation (measured: compare Week 1 vs Week 4)
```

---

## Encoding in Documentation

### PRD Template Updates

Add this section to all PRDs:

```markdown
## Planning Model

**Development Mode:** Agent-Driven (Dependency-Based)

**Time Estimates:** Not applicable. All planning is based on functional dependencies and resource requirements.

**Completion Triggers:**
- Sprint N starts when Sprint N-1 is complete
- Release X deploys when all features are complete and tested
- Phase transitions occur when success criteria are met
```

### Roadmap Template Updates

Add this section to all roadmaps:

```markdown
## Planning Model

**Approach:** Dependency-driven planning (not time-based)

**Why:** Agent-driven development has different constraints than human labor:
- Near-unlimited parallel capacity
- Zero context-switching cost
- Constraints are: API limits, compute, data availability, dependency order

**Release Triggers:** Features + readiness criteria (not calendar dates)
```

---

## Exceptions

Time-based planning MAY be used when:
1. **External deadlines exist** (e.g., conference demo, grant reporting)
2. **Coordination with humans required** (e.g., stakeholder review, user testing)
3. **Regulatory/compliance deadlines** (e.g., tax year, audit cycle)

In these cases:
- Mark time-bound items explicitly: "**TIME-BOUND:** Conference demo (March 15)"
- Keep dependency-driven planning for all other work
- Note the constraint in the sprint/roadmap

---

## Rationale

### Why This Matters for TIO Methodology

1. **Honesty about constraints:** Time estimates imply human labor constraints that don't apply to agents. Dependency declarations reveal actual constraints.

2. **Composability:** Dependency graphs compose across projects. Time estimates don't (your "Month 2" isn't my "Month 2").

3. **Parallelization:** Agents can execute independent sprints in parallel. Time-based planning obscures this opportunity.

4. **Legibility:** Dependencies are verifiable (either the schema exists or it doesn't). Time estimates are guesses.

5. **Pattern library:** Dependency patterns recur (schema → API → UI). Time estimates are context-specific noise.

### Alignment with TIO Philosophy

> "Every role produces durable artifacts. Every hand-off produces legible evidence."

Dependency-driven planning produces **legible hand-offs**:
- "Schema complete" → verifiable (tables exist)
- "API complete" → verifiable (endpoints respond)
- "UI complete" → verifiable (components render)

Time-based planning produces **illegible hand-offs**:
- "Sprint 1 complete" → what does that mean?
- "On track for Month 3" → based on what?

---

## Implementation Checklist

When creating new TIO product documentation:

- [ ] Remove all calendar-based estimates (months, weeks, dates)
- [ ] Replace with phase/sprint names (Foundation, Federation, Economic Infrastructure)
- [ ] Declare explicit dependencies between epics/sprints
- [ ] Define completion triggers (feature-based, not date-based)
- [ ] Add "Planning Model" section explaining dependency-driven approach
- [ ] Verify success metrics are outcome-based (not time-based)

---

## Related Documents

- [TIO README](../../tio/README.md)
- [Workcraft PRD](../product/PRD_v1.md)
- [Workcraft Roadmap](../product/ROADMAP.md)
- [TIO Role Template](../../tio/ROLE_TEMPLATE.md)

---

## Document History

| Date | Version | Change | Author |
|------|---------|--------|--------|
| 2026-02-23 | 1.0 | Initial procedure | Product Engineer |

---

*TIO Procedure 001 — Dependency-Driven Planning*  
*Technology and Information Office (TIO) — Techne Studio*  
*The pattern library compounds.*
