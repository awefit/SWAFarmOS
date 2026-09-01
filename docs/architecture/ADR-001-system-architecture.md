# ADR-001 — SWAFarmOS Core Architecture

- Status: Proposed
- Date: 2026-09-01
- Decision scope: SWAFarmOS Core

## Context

SWAFarmOS must support a real integrated agribusiness operation while remaining suitable for future multi-farm SaaS deployment. The system must cover agriculture, aquaculture, poultry/livestock, inventory, sales, finance, analytics, and future intelligence without coupling the core to AI or a single farm.

The architecture must therefore prioritize domain integrity, traceability, economic accounting at production-batch level, modularity, API access, auditability, and a clean open-core boundary.

## Decision

SWAFarmOS will use a **modular monolith with explicit domain boundaries and an API-first application boundary** for the initial product. The architecture will be designed so domains can later be extracted into services if scale requires it, but distributed microservices are explicitly deferred.

### 1. Architectural layers

```text
Presentation
  Web / Mobile / Admin
        |
Application API
  Authentication / Authorization / DTOs / Commands / Queries
        |
Domain Core
  Farm | Land | Crop | Aquaculture | Livestock | Inventory
  Production | Harvest | Sales | Finance | Analytics
        |
Infrastructure
  PostgreSQL | Files | Jobs | Notifications | External APIs
```

AI and advanced intelligence are consumers of domain APIs/data, not dependencies of the core domain.

### 2. Tenant boundary

The top-level business boundary is `Organization`. An organization may own or operate one or more `Farm` entities. This allows the first deployment to represent one operation while preserving a path to multi-farm and SaaS use cases.

```text
Organization
  └── Farm*
       ├── Production Units*
       ├── Locations*
       └── Users / Roles
```

### 3. Operational hierarchy

SWAFarmOS distinguishes physical resources from production cycles.

```text
Farm
 ├── Land / Plot
 ├── Pond
 ├── Coop / Housing Unit
 └── Other Production Unit
          |
          └── Production Batch*
```

A `ProductionUnit` represents where production happens. A `ProductionBatch` represents a specific economic/biological production cycle.

### 4. Production batch as the economic anchor

Every production batch must be traceable through:

`Plan -> Inputs -> Activities -> Observations -> Losses -> Production -> Harvest -> Sale -> Revenue -> Direct Costs -> Margin -> ROI`

This applies to crop, aquaculture, poultry/livestock, and future production domains.

### 5. Event and audit principle

Operational records should be append-oriented where practical. Material changes to production, inventory, and finance must retain an audit trail containing who changed what and when. Historical facts must not be silently overwritten when doing so would destroy economic or operational traceability.

### 6. Finance principle

Operational transactions and financial transactions are related but not identical. Domain modules may emit economic events, while the finance layer is responsible for normalized cost/revenue records and financial aggregation.

The system must support at minimum:

- direct cost
- indirect/allocated cost
- revenue
- COGS
- contribution margin
- net result
- cash movement
- capital expenditure
- return on capital / ROI

Allocation rules must be explicit and versionable.

### 7. Inventory principle

Inventory is quantity- and value-aware. Inputs such as seed, feed, fertilizer, medicine, packaging, and other consumables should be traceable from acquisition to consumption and, where appropriate, to a production batch.

### 8. Sales principle

A harvest may be sold through one or multiple sales transactions. Sales must link back to the relevant harvest/production output where traceability is available.

### 9. API-first boundary

Web, mobile, automation, integrations, and intelligence should consume stable application interfaces rather than accessing domain persistence directly.

The initial API should support resource and command/query patterns without prematurely committing the project to a microservice topology.

### 10. Intelligence boundary

Analytics and AI must operate above the stable domain model.

```text
Core data -> Metrics -> Analytics -> Forecasting / Optimization -> AI decision support
```

The core must remain fully functional if the intelligence layer is disabled.

### 11. Open-core boundary

The core domain model and core operational capabilities are intended to form the open-source foundation. Commercial capabilities will be isolated through explicit modules, interfaces, or services rather than hidden proprietary modifications inside the open-source core.

Third-party components must be tracked with their licenses and required notices. SWAFarmOS branding remains independent.

### 12. Deployment

Initial deployment should support a single-node/self-hosted installation suitable for a real farm. Production architecture must remain compatible with containerized deployment and managed cloud infrastructure.

### 13. Technology direction

The final framework choices will be made after the domain model and data contracts are defined. PostgreSQL is the preferred primary relational database because the domain is strongly relational, transactional, and analytical. Framework selection remains an explicit subsequent architecture decision.

## Consequences

### Positive

- Strong domain integrity before UI complexity
- Simple initial deployment
- Clear path to multi-farm SaaS
- Easier testing than distributed microservices
- AI can evolve independently
- Production economics are first-class rather than an afterthought
- Clearer open-source/commercial separation

### Negative

- Requires disciplined module boundaries inside one codebase
- Some future scaling work may require service extraction
- More upfront modeling is required before feature development

## Deferred Decisions

- Exact backend framework
- Exact frontend/mobile framework
- Authentication provider
- Job/queue implementation
- Object/file storage provider
- Analytics warehouse, if needed
- IoT protocol and hardware integrations
- Final open-source license text and commercial licensing terms

## Acceptance Criteria for the Next ADR

Before implementation begins, the next architecture artifact must define the core domain entities, identifiers, relationships, lifecycle states, and ownership rules sufficiently to produce an initial PostgreSQL schema without inventing relationships during coding.
