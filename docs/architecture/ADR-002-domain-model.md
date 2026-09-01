# ADR-002 — SWAFarmOS Initial Domain Model

- Status: Proposed
- Date: 2026-09-01
- Depends on: ADR-001

## Decision

SWAFarmOS will model the business around explicit ownership, physical production resources, production cycles, operational events, inventory movements, and economic transactions.

## Core entities

### Organization

The legal/business boundary that owns or operates farms and users.

Key concepts: `id`, name, status, settings.

### Farm

A managed agricultural operation belonging to an organization.

Key concepts: `id`, `organization_id`, name, location, status.

### Production Unit

A physical or logical place where production occurs. Examples: plot, pond, coop, greenhouse.

Key concepts: `id`, `farm_id`, type, name, area/capacity, status.

### Production Batch

A bounded production cycle associated with a production unit and production type.

Key concepts: `id`, `production_unit_id`, batch number, production type, planned dates, actual dates, status, quantity started.

### Activity

An operational action performed against a farm, production unit, or batch. Examples: feeding, planting, fertilizing, vaccination, water exchange, treatment, inspection.

Key concepts: `id`, target reference, activity type, performed_at, actor, quantities, notes.

### Observation

A measurement or field observation. Examples: fish sampling weight, poultry weight, plant height, water quality, mortality count, disease observation.

Key concepts: `id`, target reference, observed_at, metric, value, unit, source.

### Input Item

A material/resource consumed by operations, such as seed, feed, fertilizer, medicine, fuel, packaging, or other consumables.

### Inventory Item

The catalog definition of a stockable item.

### Inventory Movement

A quantity/value movement into, out of, or between inventory locations, optionally attributable to a production batch.

### Harvest

A recorded output from a production batch.

Key concepts: `id`, `batch_id`, harvested_at, quantity, unit, quality/grade, location.

### Product

A saleable commercial item or service. A product can originate from harvested production or be an externally sourced/resold item.

### Customer

A buyer/customer of the organization.

### Sale

A commercial transaction representing one or more products sold to a customer.

### Sale Line

A product-level line belonging to a sale, including quantity, price, discount, and linkage to harvest/output where traceability exists.

### Supplier

A source of purchased goods or services.

### Purchase

A procurement transaction from a supplier.

### Cost Record

A normalized economic record representing a cost attributable to an operational scope, batch, asset, or organization.

### Revenue Record

A normalized economic record representing recognized revenue and its source transaction.

### Cash Transaction

A movement of cash/bank funds. Cash is intentionally modeled separately from operational revenue/cost recognition.

### Asset

A durable resource such as pond infrastructure, pump, vehicle, building, machinery, or equipment.

### Experiment

A controlled business or operational test with a hypothesis, intervention, measurement, result, and decision.

### Decision

A recorded management decision, its context, evidence, action, and expected outcome.

## Relationships

```text
Organization
 ├── Farms*
 ├── Users*
 ├── Customers*
 ├── Suppliers*
 ├── Inventory Items*
 ├── Products*
 └── Assets*

Farm
 ├── Production Units*
 ├── Batches*
 ├── Activities*
 └── Observations*

Production Unit
 └── Production Batches*

Production Batch
 ├── Activities*
 ├── Observations*
 ├── Inventory Movements*
 ├── Cost Records*
 └── Harvests*

Harvest
 └── Sale Lines*

Sale
 ├── Sale Lines*
 └── Revenue Record*

Purchase
 ├── Inventory Movements*
 └── Cost Record*
```

## Production type strategy

Crop, aquaculture, poultry, and other livestock will share the `Production Batch` abstraction. Domain-specific measurements and workflows will be modeled through typed attributes, domain tables, or extension modules rather than duplicating the entire batch lifecycle.

Examples:

- Aquaculture: stocking quantity, species, feed consumption, biomass sampling, water quality, mortality, harvest biomass.
- Poultry: placement quantity, breed/strain, feed consumption, vaccination, mortality, weight sampling, sale quantity.
- Crop: planted area, cultivar, planting quantity, input applications, observations, yield, grade, harvest.

## Identity strategy

All persistent domain entities receive opaque stable identifiers. Human-readable batch numbers, farm codes, and inventory SKUs are business identifiers and must not be used as database primary keys.

## State strategy

Lifecycle states must be explicit and validated. Examples:

- Batch: planned -> active -> completed | cancelled
- Sale: draft -> confirmed -> fulfilled -> cancelled
- Purchase: draft -> ordered -> received -> closed | cancelled
- Inventory movement: pending -> posted | voided

State transitions should be auditable.

## Economic traceability

A cost or revenue record should be able to reference its operational origin. The system should support a trace such as:

`Purchase -> Inventory Movement -> Batch Activity -> Production Cost -> Harvest -> Sale -> Revenue`

Not every transaction will have every link, but the model must not prevent detailed attribution when data exists.

## Deliberately excluded from the first schema

- General ledger implementation
- Tax engines
- IoT telemetry storage at high frequency
- Marketplace settlement complexity
- Advanced forecasting models
- AI agent state

These are separate decisions after the core model is validated.
