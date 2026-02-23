# TIO RACI Matrix: Cross-Team Sprint Coordination

Enhances dependency-driven planning by clarifying roles across sprints. **RACI**: *Responsible* (does work), *Accountable* (owns outcome), *Consulted* (input required), *Informed* (kept updated).

## Roles (from roles/)
- **PE**: Product Engineer (00-product-engineer)
- **TL**: Technical Lead (00-technical-lead)
- **SA**: Schema Architect (01-schema-architect)
- **BE**: Backend Engineer (02-backend-engineer)
- **IE**: Integration Engineer (03-integration-engineer)
- **ESE**: Event Systems Engineer (04-event-systems-engineer)
- **WE**: Workflow Engineer (05-workflow-engineer)
- **CS**: Compliance/Security (06-compliance-security)
- **FD**: Frontend/DevOps (07-frontend-devops)
- **TCW**: Technical Community Writer (08-technical-community-writer)
- **QATE**: QA/Test Engineer (qa-test-engineer)

## Key Processes

| Process | PE | TL | SA | BE | IE | ESE | WE | CS | FD | TCW | QATE |
|---------|----|----|----|----|----|----|----|----|----|-----|------|
| **Sprint Planning**<br>Define backlog, deps | R | A | C | C | C | C | C | I | I | I | I |
| **Sprint Kickoff**<br>Assign tasks, align | C | A | R | R | R | R | R | C | C | I | I |
| **Daily Standup**<br>Blockers, progress | I | I | C | R | R | R | R | I | I | I | C |
| **Cross-Sprint Handoff**<br>Artifact review, deps | C | A | R | C | C | C | C | C | I | R | C |
| **PR Review**<br>Code quality gate | I | C | C | R | R | R | R | C | C | I | R |
| **Deployment**<br>Release to staging/prod | I | A | I | C | C | C | C | R | R | I | R |
| **Retro/Feedback**<br>Lessons, improve | R | A | C | C | C | C | C | I | I | R | C |
| **Docs Update**<br>Post-sprint artifacts | I | I | C | I | I | I | I | I | I | R | I |

## Guidelines for Communication
- **Consulted (C)**: Mandatory sync before decision (e.g., Slack thread, 15min standup).
- **Informed (I)**: Async update via #tio-sprint channel or ROADMAP.md.
- **Cross-Team Escalation**: TL accountable; unblock via TL standup if deps stall >1 day.
- **Assignments**: Update per sprint in techne-commons-id/SPRINT_BLOCKERS.md + roles/.

*Enables parallel sprints with clear handoffs; review quarterly.*

