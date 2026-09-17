# domain-driven-design-skill

A Cursor Agent Skill for **designing and reviewing** complex business software with Domain-Driven Design (DDD).

## Purpose

- Align business language, bounded contexts, and invariants before coding
- Enforce layer boundaries (Domain / Application / Query·CQRS / Interface / Infrastructure)
- Classify participants into canonical concept types (Aggregate, Use Case, Process Manager, Domain Event, etc.)
- Produce actionable DDD design docs (see `references/ddd-design-template.md`)

**Use when:** the subdomain has real business invariants, aggregates, CQRS, or context integration.  
**Skip when:** the module is simple CRUD with no meaningful business rules.

## Strategy

1. **Problem space first** — build a Domain Inventory (subdomain type, bounded context, upstream/downstream, integration style) before aggregates or deployment boundaries
2. **Tactical DDD only where needed** — apply aggregates and domain events where complexity warrants them; keep simple modules simple
3. **Keep layers distinct** — Domain owns no HTTP/ORM; Application owns use cases and transactions; write-side invariants do not live in projections
4. **Aggregate = consistency boundary** — keep aggregates small, reference others by ID, use events across aggregates; use cases are not part of an aggregate
5. **Fixed design order** — boundary & language → domain model → use cases → CQRS → interface contracts → infrastructure mapping
6. **Validate with an event chain** — `Trigger → Adapter → Use Case/PM → Aggregate → Domain Event → Outbox/Projection`

## Contents

| Path | Description |
|------|-------------|
| `SKILL.md` | Skill body (Agent entry point) |
| `references/ddd-design-template.md` | Template for DDD technical design documents |
