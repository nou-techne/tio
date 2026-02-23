# Technical Lead

**Pattern Layer:** Leadership (Cross-Cutting)
**Reports to:** Product Engineer (priority alignment), cooperative governance (accountability)
**Collaborates with:** Product Engineer, all pattern stack roles, QA

---

## Identity

**Purpose:** Bridge between the Product Engineer and the engineering team — holds the entire seven-layer pattern stack as a single coherent system and ensures the team's work composes correctly.

**Scope:** Architecture decisions, build sequencing, dependency management, code quality, technical mentorship, tool selection. Does NOT own the product roadmap or prioritization (that's the Product Engineer) but advises on feasibility and technical trade-offs.

**Accountability:** If the system doesn't compose — if layers conflict, if hand-offs break, if the architecture can't support the product requirements — the Technical Lead failed.

---

## Core Responsibilities

1. Maintain the dependency tree across all seven layers
2. Ensure layer N-1 is solid before layer N begins
3. Make architecture decisions (technology selection, patterns, trade-offs)
4. Review and approve hand-offs between roles
5. Resolve technical conflicts between layers and roles
6. Mentor pattern stack engineers on design principles
7. Provide feasibility assessments and effort estimates to the Product Engineer
8. Own the system integration — the whole is more than the sum of layers
9. Maintain technical documentation and architecture decision records

---

## Deliverables

- [ ] Architecture decision records (ADRs) — per significant decision
- [ ] Dependency tree (current state) — updated per sprint
- [ ] Build sequence plan — per engagement
- [ ] Technical feasibility assessments — per PRD received
- [ ] Code review and hand-off approvals — continuous
- [ ] System integration verification — per milestone
- [ ] Technical debt inventory — quarterly assessment
- [ ] Technology radar (tools, frameworks, emerging tech) — quarterly

---

## Required Capabilities

### Skills
- Full-stack systems architecture
- Seven Progressive Design Patterns (fluent application)
- REA ontology and event sourcing
- Database design (PostgreSQL, schema design, RLS)
- API design (GraphQL, REST)
- Event-driven architecture (RabbitMQ, pub/sub)
- Security architecture (authentication, authorization, audit)
- DevOps and deployment (Docker, CI/CD, monitoring)
- Technical communication (architecture diagrams, ADRs)
- Mentorship and technical leadership

### Tools
- Version control (Git, GitHub)
- Diagramming (Mermaid, architecture diagrams)
- Database tools (psql, pgAdmin, schema management)
- Monitoring (Prometheus, Grafana)
- CI/CD (GitHub Actions)
- IDE and development environment

### Access
- Full read/write across all Habitat contexts
- Infrastructure access (servers, databases, monitoring)
- GitHub repository admin
- All TIO role artifacts (read/write)

---

## Dependency Tree

### Receives From

- **Product Engineer** delivers: prioritized requirements, PRDs, acceptance criteria
  - Acceptance criteria: Requirements are clear, feasible, and sequenced

- **All Pattern Stack Roles** deliver: completed layer work, hand-off artifacts
  - Acceptance criteria: Each layer's deliverables meet quality criteria and compose with adjacent layers

- **QA and Test Engineer** delivers: test results, quality reports, regression findings
  - Acceptance criteria: Issues are classified by severity and layer

### Delivers To

- **Product Engineer** receives: feasibility assessments, effort estimates, technical constraints, progress reports
  - Acceptance criteria: Honest and timely, with options when constraints arise

- **All Pattern Stack Roles** receive: architecture decisions, build sequence, integration guidance, hand-off approvals
  - Acceptance criteria: Clear direction that respects layer boundaries

- **QA and Test Engineer** receives: test strategy, integration points, acceptance criteria
  - Acceptance criteria: Testable system with clear boundaries between layers

---

## Hand-Off Checklist

### Inbound (Before Technical Lead Can Begin)

- [ ] Product requirements exist (PRD from Product Engineer)
- [ ] Scope is defined and prioritized
- [ ] Resource constraints are known (people, time, budget)

### Outbound (Before Each Layer Can Begin)

- [ ] Architecture decisions are documented (ADRs)
- [ ] Dependency tree is current
- [ ] Build sequence is defined
- [ ] Layer N-1 has been verified as solid
- [ ] Integration points between layers are specified

---

## Quality Criteria

- [ ] No layer conflicts — work at each layer composes cleanly with adjacent layers
- [ ] Dependency tree is always current and accurate
- [ ] Architecture decisions are documented and justified
- [ ] Hand-offs between roles produce no surprises
- [ ] Technical debt is tracked, not hidden
- [ ] System integration works end-to-end at each milestone
- [ ] Every engineer understands how their layer fits into the whole

---

## Three-Lens Analysis

### Best Practice
- Architecture decision records for all significant choices
- Regular architecture review (weekly during active build)
- Dependency management with clear build ordering
- Integration testing at every layer boundary
- Technical debt tracked as first-class work items
- Mentorship embedded in code review, not separate from delivery

### Common Practice
- Technical lead also writes most of the code (anti-pattern: bottleneck on one person)
- Architecture decisions made verbally and forgotten (anti-pattern: tribal knowledge)
- Layers built in parallel without integration verification (anti-pattern: integration hell)
- Technical debt ignored until crisis (anti-pattern: debt spiral)
- Technical lead reports to business without product engineer mediation (anti-pattern: lost in translation)

### Emerging Opportunity
- AI-assisted architecture review (LLM agents reviewing schemas, APIs, event flows for consistency)
- Automated dependency tree validation (CI checks that layer N-1 tests pass before layer N builds)
- AI pair programming with pattern-aware context (agents that understand the seven-layer stack)
- Living architecture documentation generated from code and configuration
- Agent-based technical lead assistants that monitor integration health across bounded contexts

---

## Notes

- The Technical Lead and Product Engineer form a dyad. Neither works without the other. The Product Engineer prevents building the wrong thing. The Technical Lead prevents building it wrong.
- In Techne's context, the Technical Lead also holds the pattern library as a commons. Architecture decisions made for one venture should inform the next.
- The seven-layer pattern stack is the Technical Lead's primary tool. It provides the vocabulary for decomposition, the sequence for construction, and the framework for accountability.

---

*TIO Role Artifact — Techne Technology and Information Office*
