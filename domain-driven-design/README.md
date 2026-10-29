# domain-driven-design-skill

A Cursor Agent Skill for **designing and reviewing** complex business software with Domain-Driven Design (DDD).

## Purpose

- Align business language, domains, and invariants before coding
- Enforce layer boundaries (Domain / Application / Query·CQRS / Interface / Infrastructure)
- Classify participants into canonical concept types (Aggregate, Command, Process Manager, Domain Event, etc.)
- Produce actionable DDD design docs (see `references/ddd-design-template.md`)

**Use when:** the domain has real business invariants, aggregates, CQRS, or integration with other domains.  
**Skip when:** the module is simple CRUD with no meaningful business rules.

## Strategy

1. **Problem space first** — a Domain is the business boundary that owns semantics, model, and rules. Do not split it into a Subdomain and a Bounded Context. Build a Domain Inventory (strategic type, upstream/downstream domains, and integration style) before aggregates or deployment boundaries
2. **Tactical DDD only where needed** — apply aggregates and domain events where complexity warrants them; keep simple modules simple
3. **Keep layers distinct** — Domain owns no HTTP/ORM; Application owns commands and transactions; write-side invariants do not live in queries
4. **Aggregate = consistency boundary** — keep aggregates small, reference others by ID, use events across aggregates; commands are not part of an aggregate
5. **Fixed design order** — boundary & language → domain model → commands → CQRS → interface contracts → infrastructure mapping
6. **Validate with an event chain** — `Trigger → Adapter → Command/PM → Aggregate → Domain Event → Outbox/Query`

## Contents

| Path | Description |
|------|-------------|
| `SKILL.md` | Skill body (Agent entry point) |
| `references/ddd-design-template.md` | Template for DDD technical design documents |
