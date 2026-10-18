---
title: Acompany AI ("Aria") — Sample Use Case (AI-DLC Inception) — Generic Template
status: sample-template
version: 1.0
date: 2026-06-13
owner: OCI × Acompany (AI-DLC workshop)
product: Acompany AI ("Aria") — Conversational AI Inventory agent
region: <oci-region>
---

# Acompany AI Agent ("Aria") — Sample Use Case (Generic Template)

> **Purpose.** A reusable, customer-agnostic use-case definition to drive the **AI-DLC Inception**
> phase (decompose into Units → user stories → acceptance criteria). Kiro/AI-agent-friendly: stable
> IDs, tables, and explicit scope flags. Replace every `<placeholder>` and resolve each `[CONFIRM]`
> with the customer (see §12).
>
> **Convention.** `[CONFIRM]` = needs customer (Acompany) confirmation. IDs are stable references for
> Inception (CAP-*, FR-*, NFR-*, OUT-*, UNIT-*).

## 1. Summary

| Field | Value |
|---|---|
| Product | Acompany AI, AI sales agent persona **"Aria"** |
| One-liner | An AI agent that chats that **manages and monitor inventory** for 24/7 across the customer's sales channels, in one inbox |
| Build type | **Greenfield or Brownfield** — `[CONFIRM]` whether to extend an existing platform or build new |
| First scope | **<primary channel> first**, **brand/tenant-scoped**, region **<oci-region>**, **OCI Generative AI-first** |
| Workshop | AI-DLC — reach a working, deployable vertical slice in 3 days |

## 2. Problem Statement

Distributors and trade sellers lose orders because reordering is manual and error-prone. Retailers send handwritten lists, photos of empty shelves, or messages after hours, and staff must re-key them into the ERP, check stock, apply tiered or contract pricing, and confirm credit terms before anything ships. Stockouts, wrong substitutions, and pricing mistakes cut both revenue and trust, while excess and expiring inventory ties up working capital. Aria digitizes orders from a photo or chat, validates them against live ERP stock and customer-specific pricing, suggests in-stock substitutes, checks credit limits, and pushes a clean order straight into the ERP — closing trade orders 24/7 with fewer errors.


## 3. Goals & Non-Goals

### Goals

- Maintain a single, real-time view of stock across storefront, warehouse, and marketplace listings to eliminate overselling and stale counts.
- Improve stock-count accuracy via image/vision-based shelf and bin audits instead of manual cycle counts.
- Forecast demand per SKU/location using historical sales, seasonality, and promotions to cut both stockouts and overstock.
- Recommend optimal reorder points and reorder quantities automatically, replacing gut-feel reordering

### Non-Goals (this engagement)

- Phase 2 **Voice AI** (outbound closing calls) — out of scope now `[CONFIRM]`.
- Full omnichannel + all marketplace/messaging integrations — **roadmap unless confirmed** (§12).

## 4. Personas

| ID | Persona | Need |
|---|---|---|
| P1 | Buyers (<primary channel>-first) | Fast answers, styling/try-on, easy pay, order tracking |
| P2 | Inventory admin / CS agent | One inbox; AI handles volume; clean handoff with context |
| P3 | Merchant owner / brand | More conversion, fewer admins, brand-safe AI, analytics |


## 5. Capabilities (single source of truth)

> Each capability has one ID. Functional requirements (§6) reference these — they are **not**
> re-described elsewhere, to avoid duplication.

| ID | Capability | Notes / scope |
|---|---|---|
| CAP-1 | **Real-time stock visibility** | Single source of truth across storefront, warehouses, stores, and marketplace listings; live on-hand / available-to-promise; prevents overselling |
| CAP-2 | **Demand forecasting** | Per-SKU/location forecasts using history, seasonality, promotions, and trends (e.g., AutoGluon on OCI Data Science); confidence intervals |
| CAP-3 | **Replenishment & reorder optimization** | AI-recommended reorder points, safety stock, and reorder quantities; auto-draft purchase orders within policy thresholds |


## 6. Functional Requirements

> EARS-style, referencing capabilities (§5). To be decomposed into user stories + acceptance
> criteria during Inception.

| ID | Requirement | Caps |
|---|---|---|
| FR-1 | The system shall aggregate stock from all sources (storefront, warehouses, stores, marketplaces) into a single real-time view with on-hand and available-to-promise quantities. | CAP-1 |
| FR-2 | The system shall generate per-SKU/location demand forecasts using historical sales, seasonality, and promotions, and expose confidence intervals. | CAP-2 |


## 7. Non-Functional Requirements

| ID | NFR | Target |
|---|---|---|
| NFR-1 | Stock sync latency | Inventory updates reflected across channels in near real time (target < 5s) to prevent overselling |
| NFR-2 | Forecast accuracy | Meet/beat an agreed baseline (e.g., MAPE/WAPE target) per SKU class **[CONFIRM]** |
| NFR-3 | Availability | 24/7 for stock reads and order intake; resilient under seasonal/flash-sale surge **[CONFIRM]** SLA |
| NFR-4 | Scalability | Elastic to catalog size, locations, and peak transaction volume; multi-tenant **[CONFIRM]** |
| NFR-5 | Security/Privacy | Privacy-compliant; encryption in transit & at rest; per-tenant/site isolation; audit; export/delete |


## 8. Expected Outcomes

> Business outcomes (OUT-B*). Metrics are **illustrative/simulated** targets — validate with pilot
> data.

### Business outcomes

| ID | Outcome |
|---|---|
| OUT-B1 | Fewer stockouts and lost sales via accurate, real-time stock and forecasting (target −X% stockouts) |
| OUT-B2 | Lower excess/dead stock and write-offs through early near-expiry/slow-mover detection (target −Y%) |
| OUT-B3 | Improved inventory turns and reduced days-of-inventory / working capital tied up |


## 9. Target (To-Be) Architecture

**Style:** OCI-hosted hybrid monorepo. `apps/api` (NestJS BFF/orchestrator), `services/ai`
(Python FastAPI; forecasting + agent), front-office `apps/web`/`apps/admin` (Next.js).
`[CONFIRM]` greenfield vs extending an existing platform.

```text
OCI DNS + TLS for *.<acompany-domain>
ERP / WMS / POS / Marketplace ──┐
Admin / ops users ──────────────┴─→ OCI Load Balancer ─┬─ apps/api (NestJS BFF + Socket.io)
                                                       ├─ apps/web · apps/admin (Next.js)
                                                       └─ services/ai (FastAPI)
                                                             ├─ OCI Generative AI (chat + embeddings; model by region)
                                                             ├─ OCI Data Science (demand forecasting, e.g. AutoGluon; model deployments)
                                                             ├─ OCI Vision (shelf/bin image analysis)
                                                             └─ OCI Document Understanding (order/invoice OCR)
Eventing/orchestration: OCI Events + Functions; OCI Queue or Streaming for asynchronous work;
                        [CONFIRM] approach for long-running workflows
Data/platform: OCI Database with PostgreSQL 16 + pgvector · OCI Cache with Redis (BullMQ) ·
               OCI Object Storage (+ selected analytics service) · OCI Vault · OCI Logging/Monitoring ·
               OCI Container Registry
```

> 📐 Provide an OCI-icon diagram and an ERP/WMS integration detail doc for the customer's build.

**Design principles:** OCI-native, OCI Generative AI-first (select a model available in the target region;
keep pluggable LLM adapters); tenant/site-scoped (`tenantId`); adapter pattern for `ErpConnector`,
`StockSource`, `WmsConnector`, and a forecasting model registry. Event-driven sync and reconciliation
so external systems remain the system of record while AI adds the intelligence layer. If brownfield:
"port, don't fork."

**Open architecture decisions** (Phase 0): Compute **OCI Container Instances vs OKE**; IaC workflow
(**Terraform CLI vs OCI Resource Manager**); forecasting approach (**AutoGluon on OCI Data Science vs
custom**); OCI Generative AI model + region (Spike P0); confirm PostgreSQL version, `pgvector` enablement,
and region availability; sync pattern (event-driven vs batch) per ERP/WMS connector. See §12.


## 10. Tech Stack

**Mandatory:** Oracle Cloud Infrastructure (OCI) · OCI Generative AI (chat/embeddings; model selected
for the target region) · OCI Object Storage · `*.<acompany-domain>` (OCI DNS + TLS) · PostgreSQL 16 +
pgvector · `[CONFIRM]` existing platform base if brownfield.

**Recommended baseline:** Next.js · NestJS + Fastify + Socket.io · Python FastAPI (`services/ai`) ·
Prisma · OCI Database with PostgreSQL 16 + pgvector + pg_trgm · OCI Cache with Redis + BullMQ · OCI
Container Registry · OCI Vault · OCI Logging + Monitoring. **Compute = OCI Container Instances**
(recommended default for the initial slice; use OKE if Kubernetes is needed); **IaC Terraform CLI vs
OCI Resource Manager** [CONFIRM].

**AI/GenAI:** OCI Generative AI API (chat + multilingual embeddings; choose a supported model) · OCI
Vision (image analysis for shelf/bin audits) · OCI Data Science (forecasting with AutoGluon or a custom
model; GPU deployments for selected image-generation/virtual try-on models, subject to model and region
availability [CONFIRM]) · OCI Document Understanding (document/slip OCR).
