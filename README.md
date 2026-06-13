# Uber Capacity Planning & System Design

Production-grade capacity planning and system design artifacts for **Uber at global scale**, built using the [Universal Capacity Planning Prompt](Universal_Capacity_Planning_Prompt.md) template (OrionVexa Engineering Templates v1.0).

All figures are **educational estimates** for back-of-envelope planning — not official Uber metrics.

---

## Overview

This repository contains two primary deliverables plus supporting reference material:

| Deliverable | File | Description |
|---|---|---|
| **Interactive dashboard** | [`uber_capacity_dashboard.html`](uber_capacity_dashboard.html) | Single-file HTML/CSS/JS dashboard with live recalculation from sliders |
| **Excel workbook** | [`uber_capacity_plan.xlsx`](uber_capacity_plan.xlsx) | Formula-driven capacity plan with color-coded inputs and growth scenarios |
| **Architecture diagram** | [`uber_final_architecture.svg`](uber_final_architecture.svg) | 7-tier corrected system architecture (client → ML/geospatial) |
| **Prompt template** | [`Universal_Capacity_Planning_Prompt.md`](Universal_Capacity_Planning_Prompt.md) | Reusable LLM prompt for any system type |

An additional export is available at [`Created/Uber_CapacityPlan_100MDAU_2026-06-13.xlsx`](Created/Uber_CapacityPlan_100MDAU_2026-06-13.xlsx).

---

## Scale Assumptions (Baseline)

| Parameter | Value |
|---|---|
| Daily Active Users (DAU) | 100 million |
| Workload type | Mixed (bookings, GPS telemetry, map/ETA reads, surge pricing) |
| Ride booking actions / user / day | 3 |
| Read actions / user / day (map, ETA, status) | 30 |
| Peak traffic multiplier | 3× average |
| Active drivers (% of DAU) | 10% |
| GPS events / active driver / sec | 1 (64-byte events) |
| Avg trip record size | 5 KB |
| Replication factor | 3× |
| Cache hit rate | 85% |
| Data retention | 3 years |
| Geographic distribution | Global |

---

## Quick Start

### Interactive Dashboard

1. Open `uber_capacity_dashboard.html` in any modern browser (Chrome, Firefox, Edge, Safari).
2. Use the **Assumptions** tab sliders to adjust DAU, traffic, storage, and driver parameters.
3. All metrics across tabs update live — no server or build step required.
4. Click **Export to Excel** in the header to download the current scenario as a workbook.

**Dashboard tabs:**

| Tab | Contents |
|---|---|
| Assumptions | Tunable inputs and derived constants (MAU, active drivers) |
| Traffic & RPS | Booking writes, GPS ingest, read RPS, surge pricing, peak totals |
| Storage | Trip records, GPS telemetry, replication, metadata, 5-year growth |
| Bandwidth | Egress, CDN offload, origin load, daily transfer |
| Infrastructure | DB sharding, Redis, Kafka partitions, server counts per tier |
| Cost | Monthly/annual cloud cost breakdown with bar chart |
| Growth | 1×, 3×, 5×, 10× DAU scaling table |
| Architecture | Layer-by-layer tech stack and justification |
| Pipeline | End-to-end ride request → payment settlement walkthrough |

### Excel Workbook

1. Open `uber_capacity_plan.xlsx` in Excel, LibreOffice Calc, or Google Sheets.
2. Edit **yellow cells** (Section A inputs) — all calculated cells update via formulas.
3. Review growth scenarios (Section C) and architecture reference (Section D).

**Color coding:**

| Color | Meaning |
|---|---|
| Yellow + blue bold | Editable input cells |
| White + black | Formula / calculated cells |
| Green + white bold | Key headline metrics |
| Light grey | Section headers |

---

## Architecture Summary

The corrected 7-tier design covers Uber's ride-hailing, food delivery, and freight surfaces at global scale:

1. **Client layer** — Rider app, driver app (GPS ping every 4s), web app, Uber Eats
2. **CDN / Edge** — CloudFront / Akamai for static assets and map tiles
3. **API Gateway** — Kong / AWS API Gateway with rate limiting and auth
4. **Microservices** — Trip, dispatch, geo (H3 hex grid), pricing, payment, notification
5. **Streaming** — Apache Kafka (MSK) + Flink for GPS telemetry and trip events
6. **Polyglot data** — PostgreSQL (trips/users), Cassandra (GPS/time-series), Redis (live driver locations), Elasticsearch (search)
7. **ML / Geospatial** — SageMaker / Vertex AI for ETA, surge pricing, fraud detection; Feast feature store

See [`uber_final_architecture.svg`](uber_final_architecture.svg) for the full visual diagram.

---

## Key Formulas

| Metric | Formula |
|---|---|
| Write RPS | `(DAU × writes_per_user_per_day) / 86,400` |
| Read RPS | `(DAU × reads_per_user_per_day) / 86,400` |
| GPS Ingest RPS | `active_drivers × gps_events_per_sec` |
| Peak RPS | `Avg RPS × peak_multiplier` |
| Daily storage (raw) | `writes_per_day × avg_object_size_bytes` |
| Storage with replication | `Compressed × replication_factor` |
| Egress bandwidth | `Peak read RPS × avg_response_size × 8 / 1e12` (Tbps) |
| CDN traffic offloaded | `Egress × cache_hit_rate` |
| Origin bandwidth | `Egress × (1 − cache_hit_rate)` |

Full formula reference: [`Universal_Capacity_Planning_Prompt.md`](Universal_Capacity_Planning_Prompt.md#formulas-reference-sheet).

---

## Repository Structure

```
Week3-Task6/
├── README.md                              ← This file
├── Universal_Capacity_Planning_Prompt.md  ← Reusable prompt template
├── uber_capacity_dashboard.html           ← Interactive HTML dashboard
├── uber_capacity_plan.xlsx                ← Excel capacity workbook
├── uber_final_architecture.svg            ← System architecture diagram
└── Created/
    └── Uber_CapacityPlan_100MDAU_2026-06-13.xlsx
```

---

## Reusing the Template

To generate capacity plans for other systems (messaging, video, AI/LLM, e-commerce, etc.):

1. Open [`Universal_Capacity_Planning_Prompt.md`](Universal_Capacity_Planning_Prompt.md).
2. Fill in the `[BRACKETED]` fields with your system's details.
3. Paste the prompt into an LLM to receive an HTML dashboard and Excel generator script.

The template includes customization guidance for messaging apps, video platforms, AI services, social feeds, ride-sharing, gaming backends, and IoT telemetry.

---

## Author

**Anu Sasidharan** · [OrionVexa](https://orionvexa.ca) · [github.com/anulsasidharan](https://github.com/anulsasidharan)

*Template version 1.0 · June 2026*
