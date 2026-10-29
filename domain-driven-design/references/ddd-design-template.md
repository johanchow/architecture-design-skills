# DDD Design Deliverable Template

Use this template when producing a new design or substantially restructuring an
existing DDD design. Omit sections that are genuinely inapplicable and state why;
do not invent aggregates or CQRS solely to fill a template.

## 1. Domain Inventory and Domain Map

| # | Domain | Type | Owns | Upstream / downstream | Integration |
|---|---|---|---|---|---|
| 1 | `<name>` | Core / Supporting / Generic | `<semantics, model, and rules>` | `<domains>` | `<pattern + contract>` |

```text
<Upstream Domain> -- <Published Event / ACL / API> --> <Downstream Domain>
```

A Domain is the business boundary. Do not add a Subdomain or Bounded Context
column. Define the ubiquitous language that differs across domains. State
explicitly which domain owns each mutable business concept.

### Global visual evidence

- Domain Map source and path:
- Layered Architecture Map source and path, or reason it is unnecessary:

The Domain Map documents business ownership and integration between domains. The Layered
Architecture Map documents Interface → Application → Domain ports and
Infrastructure adapter dependency direction; do not combine the two into an
unreadable all-in-one diagram. It is required when persistence, asynchronous
processing, external adapters, or runtime/layer dependencies are material.

## 2. Domain: `<name>`

### Boundary

- Responsibility:
- Non-goals:
- Upstream contracts consumed:
- Downstream contracts published:
- Ubiquitous language:

### Domain model

#### Aggregate Map

- Diagram source and path, or reason it is unnecessary:
- Write aggregates and their aggregate roots:
- ID/event references between aggregates:
- Strong-consistency boundaries and eventual-consistency paths:

#### Aggregate: `<AggregateRoot>`

- Identity:
- Entities owned by the aggregate:
- Value objects:
- Invariants enforced atomically:
- Public domain behaviors:
- Domain events raised:
- References to other aggregates (IDs only):
- Repository port:

#### Entity Inventory (required for a non-trivial aggregate)

| Object | Kind | Identity | Key domain attributes | Public domain behaviors | Invariant responsibility |
|---|---|---|---|---|---|
| `<AggregateRoot>` | Aggregate root | `<id>` | `<only decision-relevant state>` | `<verbs, not CRUD setters>` | `<what it enforces atomically>` |
| `<ChildEntity>` | Entity | `<local identity / composite identity>` | `<key state>` | `<verbs>` | `<its local rule, or “owned by root”>` |
| `<ValueObject>` | Value object | None | `<immutable fields>` | `<validation / calculation>` | `<valid construction rule>` |

Do not turn this into an ORM schema. Include only state and methods that affect a
domain decision, lifecycle, or invariant. Omit the table only for a simple root
whose composition is fully clear from its Aggregate Card, and state why.

#### Aggregate Internal UML

- Diagram source and path, or reason it is unnecessary:
- Match the Entity Inventory: root, child entity identities/key attributes,
  value-object fields, multiplicity, mutating behaviors, and invariant owner.
  Exclude DTOs, ORM annotations, repositories, and foreign aggregate objects.

#### Lifecycle State Diagram

- Diagram source and path, or reason the lifecycle is trivial:
- States, transition command/event, guards, and terminal/recoverable states:

#### Domain service: `<name>`

- Required only when this business rule belongs to no single aggregate.
- Inputs / outputs:
- Rule implemented:
- Does not own transactions, authorization, or transport.

### Application commands

#### State-change trigger matrix

Complete one row for every material state-changing Command. A cross-domain
event is received by an Infrastructure consumer adapter, then interpreted by the
receiving domain's Command; it never calls the producer's Aggregate directly.

| Trigger and source | Interface / entrypoint | Receiving Command or Process Manager | Aggregate / domain method | Local domain event | Outbox, query, or next local action | Consistency, idempotency, failure/reconciliation |
|---|---|---|---|---|---|---|
| `<actor intent / scheduler / callback / EventName.v1>` | `<interface / worker / consumer adapter>` | `<Command / ProcessManager>` | `<Root.method() / DomainService>` | `<LocalPastTenseEvent>` | `<outbox / view / none>` | `<local transaction / key / retry or compensation>` |

- State whether the trigger is synchronous, locally deferred, or cross-domain.
- For a strong local invariant, show the synchronous Application-to-Domain call;
  do not introduce an event solely as a stylistic relay.
- For an asynchronous or cross-domain write path, provide a Trigger →
  Interface → Command / Process Manager → Domain → Event/Query sequence
  diagram and participant inventory.

#### `<CommandName>`

```python
class <CommandName>Input(BaseModel):
    actor_id: UUID
    aggregate_id: UUID
    idempotency_key: str
    # intent fields
```

- Actor and authorization rule:
- Trigger and adapter/entrypoint:
- For an inbound event: schema/version validation, idempotency key, and ACL mapping:
- Preconditions:
- Transaction boundary:
- Aggregate loaded and method invoked:
- Domain service used, if any:
- Persisted domain events / published integration events:
- Idempotency behavior:
- Expected failures and user-safe result:
- Command–Event–Query Flow source and path when query is asynchronous:

#### Sequence Diagram Participant Inventory

| Participant | Canonical type | Owned responsibility |
|---|---|---|
| `<name>` | `<Actor / Interface / Command / Query / Process Manager / Aggregate Root / Entity / Value Object / Domain Service / Domain Event / Integration Event / Infrastructure>` | `<responsibility>` |

### Query side / CQRS

#### `<ViewName>`

- Purpose and consumers:
- Source events / source tables:
- Fields and redaction policy:
- Freshness: strongly consistent / eventually consistent, with expected lag:

```python
class <QueryName>(BaseModel):
    actor_id: UUID
    # filters and pagination

class <ViewName>(BaseModel):
    # response-only fields
```

### Interface contracts

```text
POST /<resource>                 → <CommandName>
GET  /<resource>/{id}            → <QueryName> / <ViewName>
<Domain>.<EventName>.v1         → published integration event
```

- Request/response schemas:
- Authorization:
- Validation and domain error mapping:
- Versioning and compatibility:

### Infrastructure mapping

Complete every subsection that applies. If one is not applicable, state why rather
than leaving it blank. This section maps the design to adapters and operations;
it must not redefine domain invariants.

#### Ports and adapters

| Port owned by Domain/Application | Concrete adapter | Protocol / dependency | Configuration / secret boundary | Timeout, retry, rate limit / circuit breaker | Error mapping / fallback |
|---|---|---|---|---|---|
| `<RepositoryPort or GatewayPort>` | `<adapter>` | `<database / HTTP / SDK / queue>` | `<configuration owner>` | `<policy or N/A with reason>` | `<domain-safe behavior>` |

Application and Domain code depend only on the port. The concrete adapter is
assembled at the composition root. State an Anti-Corruption Layer translation
when an upstream/external model differs from the local language.

#### Entity-Relationship Diagram

Required for every domain that owns persisted tables. A domain that owns no
tables states that here and does not invent an ER diagram.

- Diagram source and path:
- Tables owned by this domain, and whether each backs an aggregate or a query:
- Primary keys, foreign keys, and cardinality:
- ID-only references to tables owned by other domains:

The diagram shows persisted structure. It does not restate invariants or replace
the Entity Inventory.

#### Persistence and transaction

| Aggregate / query | Repository / store | Tables, keys, indexes, and FK policy | Transaction owner and atomic writes | Schema migration / compatibility / rollback |
|---|---|---|---|---|
| `<AggregateRoot>` | `<RepositoryPort / adapter>` | `<physical mapping>` | `<Command>` | `<strategy>` |

- One repository port per aggregate root; queries use query stores,
  never aggregate repositories for writes.
- State the idempotency constraint and where it is enforced.
- For a schema change, state migration order, compatibility window, backfill/rebuild
  and rollback or why those are unnecessary.

#### Async delivery and queries

| Event / job | Outbox / producer owner | Consumer adapter → receiving Command / query | Idempotency key | Ordering and freshness | Retry, dead-letter, reconciliation, replay |
|---|---|---|---|---|---|
| `<EventName.v1>` | `<local transaction owner>` | `<consumer>` | `<key>` | `<assumption / lag>` | `<operational behavior>` |

If no asynchronous delivery exists, state that the command remains synchronous
and why. An audit log, trace, or ordinary retry record is not automatically a
domain or integration event.

#### Security, data governance, and observability

| Concern | Boundary / mechanism | Redaction, retention, or deletion | Evidence / metric / alert |
|---|---|---|---|
| Authorization | `<principal and scope check>` | `<sensitive data rule>` | `<audit / metric>` |
| Secrets / external access | `<owner and injection mechanism>` | `<what must not be logged>` | `<failure signal>` |
| Trace and operations | `<correlation / trace propagation>` | `<audit retention>` | `<logs, metrics, SLO/alert>` |

### Acceptance scenarios

```gherkin
Given <domain state>
When <actor> invokes <command>
Then <aggregate invariant/result>
And <domain event or query outcome>
```

## 3. Cross-domain workflow

Use this only when a business outcome spans domains.

| Step | Owning domain | Trigger / local Command / published event | Receiving Command | Consistency | Failure / compensation |
|---|---|---|---|---|---|
| 1 | `<domain>` | `<command/event>` | `<Command or N/A>` | Local transaction | `<behavior>` |

Document the Process Manager / Saga only when it owns real multi-step workflow
state. Do not use it as a generic event relay.

- Event Choreography / Saga Flow source and path when this workflow crosses
  domains:
- Request Sequence Diagram source and path when API, SSE, or resume semantics
  are material to the workflow:
