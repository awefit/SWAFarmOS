# SWAFarmOS Product Charter

## 1. Purpose

SWAFarmOS is designed as an integrated Farm & Agribusiness Operating System. Its purpose is to turn operational activity into reliable business data and turn that data into better capital-allocation and production decisions.

## 2. Primary Design Goal

The system must answer four questions continuously:

1. What is happening in the operation?
2. What does it cost?
3. What is it producing and earning?
4. Where should the next unit of capital be allocated?

## 3. Core Domains

- Farm
- Land and plots
- Crops
- Aquaculture
- Poultry/livestock
- Production batches
- Inputs and inventory
- Activities and field logs
- Harvest
- Sales and customers
- Suppliers and purchasing
- Finance
- Analytics and KPI
- Experiments and decisions

## 4. Economic Principle

Every production batch should be traceable from initial capital/input through production, harvest, sale, revenue, direct cost, contribution margin, and return on capital.

Example lifecycle:

`Capital -> Input -> Activity -> Production -> Harvest -> Sale -> Revenue -> Cost -> Margin -> ROI -> Decision`

## 5. Reference Deployment

The first real-world deployment is a 19-month agribusiness program starting 1 September 2026. The initial target is Rp500 million of measurable accumulated business value/cash-equivalent by the end of the program.

The reference deployment is not the product definition. It is the environment used to validate workflows, metrics, economics, and decision support.

## 6. Product Principles

### 6.1 Business requirements drive software

Features must solve an operational or economic problem. Avoid feature accumulation without measurable value.

### 6.2 Data before dashboards

A dashboard is only as useful as the underlying data model and data quality.

### 6.3 Measure before scaling

Production units should be evaluated using repeatable metrics before additional capital is committed.

### 6.4 Modular by domain

Farm, aquaculture, poultry, crop, finance, sales, and intelligence should be separable modules with clear interfaces.

### 6.5 Open-core by design

The open-source core should remain useful on its own. Commercial capabilities should be separated through explicit technical and licensing boundaries.

### 6.6 Compliance is part of architecture

Third-party dependencies and source-derived components must retain all legally required notices, attribution, and license obligations.

## 7. Non-Goals for V0.1

- Full ERP replacement
- Complex accounting compliance for every jurisdiction
- IoT hardware integration before the core domain model is stable
- Advanced AI before sufficient operational data exists
- Building every mobile and web feature simultaneously

## 8. Success Criteria

V0.1 is successful when the architecture can represent a real farm operation and reliably calculate production and economic outcomes for a batch without relying on hidden manual calculations.
