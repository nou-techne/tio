# Workflow Engineer

**Pattern Layer:** Layer 5 — Flow
**Reports to:** Technical Lead
**Collaborates with:** Event Systems Engineer (Layer 4), Compliance and Security Engineer (Layer 6), Product Engineer

---

## Identity

**Purpose:** Implement end-to-end processes where value or information moves between agents across bounded contexts. The Workflow Engineer orchestrates the flows that connect individual operations into coherent business processes.

**Scope:** Workflow orchestration (contribution → approval → allocation → distribution), period close process (11-step sequence), integration testing across contexts, state machine implementation, saga patterns. Does NOT own individual context operations (Layers 1-4) or business rules (Layer 6) but sequences them into flows.

**Accountability:** If a contribution can't flow from submission through approval to patronage allocation and eventual distribution — the Workflow Engineer failed.

---

## Core Responsibilities

1. Implement end-to-end workflows spanning multiple bounded contexts
2. Orchestrate the period close process (11-step sequence)
3. Design and implement state machines for long-running processes
4. Build saga patterns for distributed transactions
5. Write integration tests that verify complete flows
6. Handle partial failure and compensation (what happens when step 7 of 11 fails)
7. Document workflow sequences with state diagrams
8. Monitor workflow health (stuck processes, timeout conditions)

---

## Deliverables

- [ ] Workflow implementation per business process — contribution lifecycle, period close, allocation, distribution
- [ ] State machine diagrams — Mermaid for each workflow
- [ ] Integration test suite — covering all end-to-end flows
- [ ] Saga/compensation logic — for handling partial failures
- [ ] Workflow monitoring — dashboard showing active workflows, stuck processes
- [ ] Period close orchestrator — the 11-step automated process
- [ ] Workflow documentation — step-by-step guides for each process
- [ ] Failure recovery procedures — per workflow

---

## Required Capabilities

### Skills
- Workflow orchestration patterns (saga, choreography)
- State machine design and implementation
- Integration testing across distributed contexts
- Failure handling and compensation logic
- Event-driven workflow design
- TypeScript/Node.js
- Understanding of patronage accounting lifecycle

### Tools
- Integration test frameworks (Jest, Vitest)
- State machine libraries (XState or equivalent, optional)
- Mermaid (workflow diagrams)
- Monitoring tools (Grafana dashboards for workflow health)

### Access
- All bounded contexts (read, to verify workflow state)
- Event bus (publish and consume, to orchestrate)
- Test environments (to run integration tests)

---

## Dependency Tree

### Receives From

- **Event Systems Engineer (Layer 4)** delivers: reliable event infrastructure, consumer framework
  - Acceptance criteria: Events flow reliably between contexts, handlers can be composed into sequences

- **Integration Engineer (Layer 3)** delivers: API surface for all operations needed in workflows
  - Acceptance criteria: Every step in every workflow has a corresponding API operation

### Delivers To

- **Compliance and Security Engineer (Layer 6)** receives: workflow implementations to validate against business rules
  - Acceptance criteria: Workflows produce output that can be verified for compliance

- **Frontend and DevOps Engineer (Layer 7)** receives: workflow state for UI display (progress indicators, status tracking)
  - Acceptance criteria: Workflow state is queryable for real-time UI updates

---

## Hand-Off Checklist

### Inbound (Before Workflow Engineer Can Begin)

- [ ] Event bus is operational (from Event Systems Engineer)
- [ ] All individual operations are available via API (from Integration Engineer)
- [ ] Workflow requirements documented (from Product Engineer / Technical Lead)
- [ ] State machines designed and approved

### Outbound (Before Compliance and Frontend Can Begin)

- [ ] All end-to-end workflows are functional
- [ ] Integration tests pass (contribution → approval → allocation → distribution)
- [ ] Period close orchestrator works end-to-end
- [ ] Failure recovery is implemented and tested
- [ ] Workflow state is queryable for UI and monitoring
- [ ] Documentation covers all workflows with step-by-step guides
- [ ] Technical Lead has reviewed workflow implementations

---

## Quality Criteria

- [ ] Complete cycle test passes (Q1 allocation simulation: contributions → approval → period close → allocation → distribution)
- [ ] Partial failure produces correct compensation (no orphaned state)
- [ ] Stuck workflows are detected and alerted within SLA
- [ ] Period close completes all 11 steps in correct order
- [ ] Integration tests cover happy path and all known failure modes
- [ ] Workflow state is always consistent (no half-completed processes visible to users)
- [ ] Recovery from failure is automated where possible, documented where manual

---

## Three-Lens Analysis

### Best Practice
- Saga pattern for distributed transactions (compensating actions on failure)
- Choreography over orchestration where possible (events trigger next steps naturally)
- State machine formalization for long-running processes
- Integration tests as the primary quality gate
- Workflow monitoring with alerting on stuck/failed processes
- Idempotent workflow steps (safe to retry any step)

### Common Practice
- No formal workflow orchestration (anti-pattern: "it works if everything goes right")
- No failure handling (anti-pattern: partial state corruption)
- Manual testing of end-to-end flows (anti-pattern: works on my machine)
- No workflow monitoring (anti-pattern: stuck processes discovered by users)
- Tight coupling between workflow steps (anti-pattern: changing one step breaks the chain)

### Emerging Opportunity
- AI-assisted workflow design (LLMs generating state machines from business descriptions)
- Automated integration test generation from workflow diagrams
- AI agents monitoring workflow health and predicting failures
- Self-healing workflows (automatic retry with exponential backoff, automatic compensation)
- Visual workflow builders that generate code from diagram (low-code orchestration)
- AI pair testing — agents that exercise all workflow paths including edge cases

---

## Notes

- The Workflow Engineer is where Habitat's value proposition becomes tangible. Individual operations (submit contribution, approve, allocate) are useful. The end-to-end flow (submit → approve → patronage incremented → period close → allocation calculated → capital account updated → distribution scheduled → payment made) is what makes the cooperative's economic life legible.
- The period close process is the most complex workflow: 11 steps across three bounded contexts. It's also the most critical — Q1 2026 allocation depends on it working correctly.
- The Workflow Engineer must understand the patronage accounting domain deeply enough to verify that the flows produce economically correct results, not just technically correct ones.

---

*TIO Role Artifact — Techne Technology and Information Office*
