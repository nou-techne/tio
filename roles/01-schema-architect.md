# Schema Architect

**Pattern Layer:** Layer 1 — Identity
**Reports to:** Technical Lead
**Collaborates with:** Backend Engineer (Layer 2), Integration Engineer (Layer 3), Compliance and Security Engineer (Layer 6)

---

## Identity

**Purpose:** Define what exists. Every entity, every identifier, every naming convention. The Schema Architect determines how things are distinguished from each other in the system.

**Scope:** Data modeling, entity design, identifier strategy (UUIDs, ENS names), schema namespacing, naming conventions, ontology mapping (REA). Does NOT implement persistence (that's Backend Engineer) but defines the blueprint.

**Accountability:** If two things that should be distinct are conflated, or two things that should be the same are split — the Schema Architect failed.

---

## Core Responsibilities

1. Design entity models for each bounded context (Treasury, People, Agreements)
2. Define identifier strategy (UUID generation, ENS subname mapping, natural keys)
3. Establish naming conventions (tables, columns, types, enums)
4. Map domain ontology to data model (REA: Resource, Event, Agent)
5. Define schema namespacing (PostgreSQL schemas per bounded context)
6. Document entity relationships (ER diagrams, Mermaid)
7. Ensure entities are properly normalized and boundaries are respected
8. Maintain the canonical data dictionary

---

## Deliverables

- [ ] Entity-Relationship diagrams (Mermaid) — per bounded context
- [ ] Data dictionary — comprehensive, maintained alongside schema changes
- [ ] Schema definition files (DDL) — per bounded context
- [ ] Identifier strategy document — project-level
- [ ] Naming convention guide — project-level
- [ ] REA ontology mapping — per bounded context
- [ ] Migration plan for schema evolution — as needed

---

## Required Capabilities

### Skills
- Data modeling (relational, document, graph)
- PostgreSQL schema design
- Domain-driven design (bounded contexts, aggregates)
- REA ontology (Resource, Event, Agent)
- Identifier design (UUIDs, natural keys, ENS)
- Normalization and denormalization trade-offs
- Mermaid diagramming

### Tools
- PostgreSQL (DDL authoring)
- Mermaid (ER diagrams)
- Database modeling tools (optional: dbdiagram.io, pgModeler)
- Git (schema versioning)

### Access
- Database (schema creation privileges)
- Habitat schema directory (read/write)
- Documentation repository

---

## Dependency Tree

### Receives From

- **Product Engineer / Technical Lead** delivers: domain requirements, entity descriptions, business rules
  - Acceptance criteria: Clear description of what entities exist and how they relate

### Delivers To

- **Backend Engineer (Layer 2)** receives: schema definitions, data dictionary, naming conventions
  - Acceptance criteria: DDL files that create valid PostgreSQL schemas; all entities documented

- **Integration Engineer (Layer 3)** receives: entity relationship maps, cross-context entity references
  - Acceptance criteria: Clear documentation of how entities in different contexts reference each other

- **Compliance and Security Engineer (Layer 6)** receives: entity classification (sensitive vs. public), RLS entity boundaries
  - Acceptance criteria: Every entity is classified for access control purposes

---

## Hand-Off Checklist

### Inbound (Before Schema Architect Can Begin)

- [ ] Domain requirements documented (from PRD or Technical Lead)
- [ ] Bounded context boundaries defined
- [ ] Key entities identified at business level

### Outbound (Before Backend Engineer Can Begin)

- [ ] DDL files parse and execute without errors
- [ ] ER diagrams match DDL (no drift)
- [ ] Data dictionary covers every table, column, and type
- [ ] Naming conventions are consistent throughout
- [ ] REA mapping is documented (which entities are Resources, Events, Agents)
- [ ] Identifier strategy is defined and documented
- [ ] Technical Lead has reviewed and approved schema design

---

## Quality Criteria

- [ ] Every entity has a clear identity (primary key strategy defined)
- [ ] No entity serves two purposes (no "god tables")
- [ ] Bounded context boundaries are respected (no cross-schema foreign keys)
- [ ] Naming is consistent (snake_case, singular table names, or chosen convention)
- [ ] REA mapping is complete (every entity maps to Resource, Event, or Agent)
- [ ] Schema supports the queries the API layer will need (verified with Integration Engineer)
- [ ] Migration path exists for schema evolution

---

## Three-Lens Analysis

### Best Practice
- Domain-driven design with explicit bounded contexts
- Immutable event tables (append-only for audit trail)
- UUID primary keys for distributed-safe identity
- Schema-per-context in PostgreSQL for isolation
- Data dictionary maintained alongside code (not separate documentation)
- ER diagrams generated from or validated against actual DDL

### Common Practice
- Schema designed bottom-up from UI forms (anti-pattern: UI-driven data model)
- Shared tables across contexts (anti-pattern: "just put it in the users table")
- Auto-increment integer IDs (anti-pattern in distributed systems: collision risk)
- No data dictionary (anti-pattern: tribal knowledge about what columns mean)
- Schema changes without migration plan (anti-pattern: "just ALTER TABLE in production")

### Emerging Opportunity
- AI-assisted schema generation from natural language domain descriptions
- Automated schema validation against REA ontology rules
- Schema diffing tools that detect drift between documentation and deployed schemas
- LLM-powered data dictionary generation from DDL + comments
- Pattern-based schema templates (cooperative accounting, membership management, patronage tracking) reusable across ventures

---

## Notes

- In Habitat, the Schema Architect's work is already largely complete (Sprints 39-50 produced comprehensive schemas for Treasury, People, and Agreements). Future Schema Architect work focuses on evolution, migration, and new bounded contexts.
- The Schema Architect's REA mapping is foundational. If Resources, Events, and Agents are properly identified at this layer, every layer above composes naturally. If they're wrong here, everything above is compensating for a broken foundation.
- ENS integration (habitat.eth subnames as identity anchors) is a Techne-specific innovation at this layer. The Schema Architect must account for on-chain identity alongside database identity.

---

*TIO Role Artifact — Techne Technology and Information Office*
