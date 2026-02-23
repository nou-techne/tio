# Technology and Information Office (TIO)

*Moving technology out of the cost center and into the value creative role.*

---

## Purpose

The Technology and Information Office is Techne's engineering methodology — a composable collection of role artifacts that decompose information patterns for the build, testing, deployment, and maintenance of any tool or technology we create.

These roles serve projects, ventures, and client bodies equally. The same pattern stack that builds Habitat builds everything else. The TIO is not a department — it is a discipline, a set of practiced capacities that any Techne engagement can draw from.

## Philosophy

**Technology as value creation, not cost center.** The traditional IT office exists to "keep the lights on." The TIO exists to build the lights, improve the lights, and help others see by them. Every role in this office creates durable artifacts that compound. Every hand-off between roles produces legible evidence of work. Every engagement strengthens the pattern library.

**The seven-layer pattern stack as team structure.** Each role maps to a layer of the Seven Progressive Design Patterns. The build order is the pattern order. Layer N requires Layer N-1 to be solid. This isn't just architectural theory — it's how we staff, sequence, and hold each other accountable.

**Three lenses on every role:**
- **Best Practice** — what the industry has established as reliable
- **Common Practice** — what most teams actually do (including anti-patterns to avoid)
- **Emerging Opportunity** — what's becoming possible that changes how this role works

---

## The Seven Progressive Design Patterns

1. **Identity** — distinguishing one thing from another
2. **State** — recording attributes
3. **Relationship** — connecting identifiable things
4. **Event** — recording that something happened
5. **Flow** — value or information moving between agents
6. **Constraint** — rules governing valid states and transitions
7. **View** — presenting information for a purpose

Each layer presupposes the layers beneath it. You cannot build Views of systems that don't exist, or define Constraints for Flows that haven't been implemented.

---

## Roles

### Leadership (Cross-Cutting)

**Product Engineer** — Advocate for the technology team within business and executive leadership. Translates business needs into technical requirements and technical capabilities into business value. Owns the product roadmap, prioritization, and stakeholder communication. Ensures technology serves the cooperative's mission and economic model.

**Technical Lead** — Bridge between the Product Engineer and the engineering team. Holds the entire pattern stack as a single system. Ensures layer dependencies are respected, architecture decisions are sound, and the team's work composes into a coherent whole. Owns the dependency tree and build sequence.

*The Product Engineer and Technical Lead operate in parallel — one faces outward (business, members, ventures), one faces inward (architecture, team, quality). Together they ensure technology is both valuable and sound.*

### Pattern Stack Roles

| Layer | Role | Primary Responsibility |
|-------|------|----------------------|
| 1. Identity | Schema Architect | Data model, entity design, naming |
| 2. State | Backend Engineer | Data persistence, queries, migrations |
| 3. Relationship | Integration Engineer | Cross-context connections, API design |
| 4. Event | Event Systems Engineer | Event sourcing, messaging, workflows |
| 5. Flow | Workflow Engineer | End-to-end processes, orchestration |
| 6. Constraint | Compliance and Security Engineer | Rules enforcement, audit, security |
| 7. View | Frontend and DevOps Engineer | UI, deployment, monitoring, observability |

### Cross-Cutting

**QA and Test Engineer** — Spans all layers. Validates that each layer's contract with the layers below is honored. Unit tests per layer, integration tests across layers, compliance verification, load testing.

---

## Role Artifacts

Each role is documented using a standard template (`ROLE_TEMPLATE.md`) producing a unique artifact in `roles/`. Every role artifact contains:

- Role identity and pattern layer mapping
- Core responsibilities and deliverables
- Required capabilities (skills, tools, access)
- Dependency tree (what this role needs from others)
- Hand-off checklist (what this role delivers to others)
- Quality criteria and acceptance standards
- Three-lens analysis (best practice, common practice, emerging opportunity)

---

## Dependency Tree

The dependency tree flows from Layer 1 upward. Each role depends on the roles in lower layers and delivers to the roles in higher layers.

```
Product Engineer ←→ Technical Lead
                        ↕
                [Pattern Stack]
                        
Layer 1: Schema Architect
    ↓ delivers: entity definitions, data model, naming conventions
Layer 2: Backend Engineer
    ↓ delivers: database operations, API endpoints, data access layer
Layer 3: Integration Engineer
    ↓ delivers: cross-context queries, GraphQL schema, REA implementation
Layer 4: Event Systems Engineer
    ↓ delivers: event bus, message handlers, event schema
Layer 5: Workflow Engineer
    ↓ delivers: end-to-end workflows, orchestration, integration tests
Layer 6: Compliance and Security Engineer
    ↓ delivers: validated rules, security policies, audit capabilities
Layer 7: Frontend and DevOps Engineer
    ↓ delivers: user interfaces, deployment, monitoring

QA and Test Engineer ←→ All Layers
```

---

## How TIO Serves Techne

**For projects:** TIO roles decompose the build. Any project can be scoped by identifying which layers it touches and which roles are needed.

**For ventures:** TIO roles staff the engineering. A venture like LearnVibe.Build might need Layers 1-3 and 7 initially, adding Layers 4-6 as it matures.

**For clients:** TIO roles define the engagement. External work is scoped, staffed, and delivered using the same pattern stack and role artifacts.

**The cycle completes:** PRD defines what to build → TIO roles define who builds it and how → Contributions to the build are tracked as patronage → Patronage flows through Habitat → Recognition returns to the builders.

---

## Directory Structure

```
tio/
├── README.md                           ← this file
├── ROLE_TEMPLATE.md                    ← standard template for all roles
└── roles/
    ├── 00-product-engineer.md          ← Leadership: business-facing
    ├── 00-technical-lead.md            ← Leadership: architecture-facing
    ├── 01-schema-architect.md          ← Layer 1: Identity
    ├── 02-backend-engineer.md          ← Layer 2: State
    ├── 03-integration-engineer.md      ← Layer 3: Relationship
    ├── 04-event-systems-engineer.md    ← Layer 4: Event
    ├── 05-workflow-engineer.md         ← Layer 5: Flow
    ├── 06-compliance-security.md       ← Layer 6: Constraint
    ├── 07-frontend-devops.md           ← Layer 7: View
    └── qa-test-engineer.md             ← Cross-cutting: all layers
```

---

*The TIO is a commons. The roles, templates, and artifacts improve with every engagement. What we learn building Habitat makes us better at building the next thing. The pattern library compounds.*
