# Event Systems Engineer

**Pattern Layer:** Layer 4 — Event
**Reports to:** Technical Lead
**Collaborates with:** Integration Engineer (Layer 3), Workflow Engineer (Layer 5), Backend Engineer (Layer 2)

---

## Identity

**Purpose:** Record that something happened — and ensure the right processes respond. The Event Systems Engineer implements the event bus, event sourcing infrastructure, and message handling that enables cross-context coordination.

**Scope:** RabbitMQ topology, event publishing, event consumption, event schema design, idempotency, dead-letter handling, event replay. Does NOT define business workflows (Layer 5) but provides the messaging infrastructure they run on.

**Accountability:** If an event is lost, processed twice with different results, or fails silently — the Event Systems Engineer failed.

---

## Core Responsibilities

1. Design and implement RabbitMQ topology (exchanges, queues, bindings)
2. Define event schema and envelope format
3. Implement event publishing from mutation resolvers
4. Build event consumer framework (handler registration, ack/nack)
5. Implement idempotency tracking (prevent duplicate processing)
6. Configure dead-letter handling (DLX, DLQ, retry policies)
7. Build event replay capability (reprocess historical events)
8. Monitor event flow (processing rates, queue depths, failures)
9. Manage event schema versioning

---

## Deliverables

- [ ] RabbitMQ topology configuration — exchanges, queues, bindings per context
- [ ] Event schema definitions — JSON schema per event type
- [ ] Event publishing utility — used by all mutation resolvers
- [ ] Consumer framework — handler registration, ack/nack, error handling
- [ ] Idempotency tracking implementation — processed_events table and checks
- [ ] Dead-letter queue configuration — retry policies, DLQ monitoring
- [ ] Event replay tooling — CLI or admin interface for reprocessing events
- [ ] Event catalog — comprehensive list of all event types, publishers, consumers
- [ ] Monitoring integration — Prometheus metrics for event processing

---

## Required Capabilities

### Skills
- Message broker architecture (RabbitMQ, AMQP protocol)
- Event sourcing patterns
- Distributed systems reliability (at-least-once delivery, idempotency)
- Dead-letter handling and retry strategies
- Event schema design and versioning
- TypeScript/Node.js (amqplib)
- Monitoring and observability

### Tools
- RabbitMQ (management UI, CLI)
- amqplib (Node.js AMQP client)
- Prometheus (metrics)
- Git

### Access
- RabbitMQ (admin)
- Database (processed_events table read/write)
- Monitoring dashboards

---

## Dependency Tree

### Receives From

- **Integration Engineer (Layer 3)** delivers: mutation structure defining what events to publish
  - Acceptance criteria: Every mutation has a defined event type and payload schema

- **Backend Engineer (Layer 2)** delivers: database support for event log and idempotency tables
  - Acceptance criteria: Tables exist, queries work, transactions wrap event storage

### Delivers To

- **Workflow Engineer (Layer 5)** receives: reliable event delivery infrastructure, consumer framework
  - Acceptance criteria: Events are delivered at-least-once, handlers can be registered per queue, idempotency is enforced

- **Compliance and Security Engineer (Layer 6)** receives: audit event stream, event log for compliance verification
  - Acceptance criteria: All system events are logged immutably, events can be queried for audit purposes

---

## Hand-Off Checklist

### Inbound (Before Event Systems Engineer Can Begin)

- [ ] GraphQL mutations defined (from Integration Engineer)
- [ ] Event types identified per mutation
- [ ] Database tables for event log and idempotency exist (from Backend Engineer)
- [ ] Technical Lead has approved event architecture

### Outbound (Before Workflow Engineer Can Begin)

- [ ] RabbitMQ topology deployed and tested
- [ ] All event types are published correctly from mutations
- [ ] Consumer framework is operational (handlers receive events)
- [ ] Idempotency tracking prevents duplicate processing (verified)
- [ ] Dead-letter queues capture failed events
- [ ] Event catalog is complete and current
- [ ] Monitoring metrics are flowing
- [ ] Technical Lead has reviewed event bus implementation

---

## Quality Criteria

- [ ] No events are silently lost (every event either processed or in DLQ)
- [ ] Idempotency is enforced (duplicate delivery produces identical results)
- [ ] Event schema is documented and versioned
- [ ] Dead-letter queue depth is monitored and alerted
- [ ] Event replay produces correct state (verified against baseline)
- [ ] Event processing latency meets SLA (contribution approved → patronage updated within seconds)
- [ ] Queue depths stay within healthy bounds under normal load
- [ ] Consumer framework handles crashes gracefully (reconnect, resume)

---

## Three-Lens Analysis

### Best Practice
- At-least-once delivery with idempotent consumers
- Dead-letter exchanges with configurable retry limits
- Event schema versioning (backward-compatible evolution)
- Immutable event log alongside message broker
- Idempotency keys derived from event ID + handler name
- Monitoring on queue depth, processing rate, and error rate
- Event replay capability for disaster recovery and debugging

### Common Practice
- At-most-once delivery with no retry (anti-pattern: lost events)
- No dead-letter handling (anti-pattern: failed events vanish)
- No event schema (anti-pattern: unstructured payloads break consumers)
- Idempotency assumed but not enforced (anti-pattern: duplicate side effects)
- Event log only in the broker (anti-pattern: no audit trail after message consumed)
- No monitoring (anti-pattern: queue fills silently until system fails)

### Emerging Opportunity
- AI-assisted event flow analysis (detecting missing handlers, orphaned events)
- Automated event schema validation in CI/CD pipeline
- Event sourcing as the primary state mechanism (current state derived from event log)
- CQRS (Command Query Responsibility Segregation) enabled by event infrastructure
- AI agents monitoring event patterns and predicting failures before they occur
- Cross-cooperative event federation (events flowing between Habitat instances)

---

## Notes

- In Habitat, the event bus is the nervous system. Treasury, People, and Agreements are independent bounded contexts that coordinate entirely through events. If the event bus fails, the system fragments into three isolated databases.
- The event catalog (Sprint 50, event-bus-specification) defines 25+ event types. The Event Systems Engineer implements all of them.
- Event replay is critical for compliance. If an auditor questions an allocation, we need to replay the events that produced it and verify the result matches.

---

*TIO Role Artifact — Techne Technology and Information Office*
