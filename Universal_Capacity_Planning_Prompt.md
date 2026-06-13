# Universal Capacity Planning & System Design Prompt
> **OrionVexa Engineering Templates** | Version 1.0 | June 2026  
> Use this prompt to generate a complete capacity planning document for **any system** — messaging, video, AI/LLM, e-commerce, social, ride-sharing, and more.

---

## How to Use This Prompt

1. Copy the full prompt block below
2. Fill in all `[BRACKETED]` fields with your system's details
3. Paste into Claude (or any capable LLM)
4. You will receive two deliverables: an **interactive HTML dashboard** + a **downloadable Excel workbook**

---

## Quick Customization Reference

| System Type | Key Parameters to Adjust | Extra Sections to Add |
|---|---|---|
| **Messaging app** | Messages/user/day, media ratio, group size | Encryption overhead, delivery receipts |
| **Video platform** | Upload count, avg duration, bitrate tiers | Transcoding pipeline, codec comparison |
| **AI / LLM service** | Prompts/user/day, avg tokens, GPU VRAM | Model comparison, inference cost/token |
| **E-commerce** | Orders/day, catalog size, peak seasons | Inventory DB, payment gateway throughput |
| **Social feed** | Posts/day, feed refresh rate, fanout | Fanout-on-write vs read tradeoffs |
| **Ride-sharing** | Concurrent rides, GPS update rate | Real-time matching latency, surge calc |
| **Gaming backend** | Concurrent sessions, state update rate | Game state sync, matchmaking latency |
| **IoT / telemetry** | Devices, events/device/sec, retention | Time-series DB sizing, aggregation |

---

## ✦ THE PROMPT ✦

---

**System:** You are an expert systems architect and capacity planning engineer with deep knowledge of distributed systems, cloud infrastructure, and back-of-envelope estimation techniques.

**Task:** Create a complete, production-grade capacity planning and system design document for **Uber** 

---

### Scale Assumptions *(customize these before running)*

- **DAU (Daily Active Users):** 100 million
- **Primary workload type:**  mixed
- **Core user actions per day:**  Users will book rides in uber platform
- **Peak traffic multiplier:**  3× average
- **Data retention:**3 years
- **Geographic distribution:** global

---

### Deliverable 1 — Interactive HTML Dashboard Widget

Build a fully self-contained, single-file HTML/CSS/JavaScript interactive capacity planning dashboard. All metrics must recalculate live from slider/input changes. Include the following tabs:

#### Tab 1 — Scale Assumptions & Inputs
Sliders or input fields for: DAU, actions per user per day, avg payload/data size, peak multiplier, replication factor, cache hit rate, and any system-specific parameters (e.g., avg video duration, avg message size, avg tokens per request). Display derived constants like Monthly Active Users and seconds per day as read-only reference values.

#### Tab 2 — Traffic & RPS Analysis
Calculate and display:
- Write RPS = (DAU × writes per user) / 86,400
- Read RPS = (DAU × reads per user) / 86,400
- Peak Write RPS = Write RPS × peak multiplier
- Peak Read RPS = Read RPS × peak multiplier
- Total RPS and Peak Total RPS
- For ML/AI systems: Inference RPS, tokens per second, concurrency

#### Tab 3 — Storage Estimation
Calculate and display:
- Daily new storage (raw) = writes per day × avg object size
- With encoding/compression savings (state the codec or method)
- With replication overhead
- With metadata overhead (typically 5–10%)
- Annual storage growth
- 5-year cumulative storage
- Break down by data type where relevant (e.g., media vs. metadata vs. index)
- For AI systems: model weights VRAM, KV cache, embedding store size

#### Tab 4 — Bandwidth & CDN
Calculate and display:
- Egress bandwidth = peak read RPS × avg response size
- CDN offload = egress × cache hit rate
- Origin bandwidth = egress × (1 − cache hit rate)
- Total daily data transferred
- CDN PoP recommendation (regional breakdown if global)

#### Tab 5 — Database & Infrastructure Sizing
Based on the system type, calculate:
- DB type recommendation (SQL vs. NoSQL vs. time-series vs. vector DB) with justification
- Sharding strategy (key/hash/range) and estimated shard count
- Read replica count
- Cache layer sizing (Redis/Memcached): working set size
- Message queue throughput (if applicable — Kafka partition count)
- Server count per tier: API servers, DB nodes, cache nodes, worker nodes
- For AI/ML systems: GPU count = ceil(model VRAM / GPU VRAM) × replication factor

#### Tab 6 — Cost Estimation
Monthly cost breakdown using reasonable cloud pricing (AWS/GCP/Azure):
- Compute (vCPU/memory)
- Storage (object + block + DB)
- Bandwidth / egress
- CDN
- Managed services (Kafka, Redis, etc.)
- GPU inference cost (if applicable)
- Total monthly and annual cost
- Display as a bar chart

#### Tab 7 — Growth Scenarios
A scaling table at **1× (baseline), 3×, 5×, and 10× DAU** for all key metrics: RPS, storage, bandwidth, server count, monthly cost.

#### Tab 8 — Architecture Overview
A text + visual summary of all system architecture layers:
- Client layer
- CDN / Edge
- Load balancer / API Gateway
- Application / Service layer (list key microservices)
- Data pipeline (if applicable — ingest → process → store → serve)
- Database tier (primary + cache + search)
- Async processing / queue layer
- Monitoring / observability

Describe the tech stack for each layer with a one-line justification.

#### Tab 9 — Pipeline Walkthrough *(include if the system has multi-stage processing)*
A step-by-step table with columns: **Stage | Component | Tech Stack | Latency SLO | Key Metric | Handoff**. Cover the full data flow from user action to final delivery.

*Example for video:* Upload → Kafka Ingest → Transcoding → Moderation → Feature Extraction → Recommendation → CDN Delivery

#### Tab 10 — AI Q&A *(optional but recommended)*
Integrate the Anthropic Claude API (model: `claude-sonnet-4-6`) with a text input where users can ask any system design or capacity planning question. Display AI responses inline.

Use this system prompt for the API call:
> *"You are a senior systems architect expert in [SYSTEM NAME] capacity planning. Answer questions clearly and cite specific numbers from the capacity plan where relevant."*

---

### Deliverable 2 — Excel Capacity Planning Workbook (.xlsx)

Build a Python script using `openpyxl` that generates a multi-sheet Excel file. After generating, validate with a recalculation script and confirm **zero formula errors** before delivering.

#### Mandatory Color Coding

| Color | Meaning |
|---|---|
| Yellow fill + blue bold text | Editable input cells (Section A only) |
| White fill + black text | Formula / calculated cells |
| Green fill + white bold text | Key headline metric cells |
| Light grey fill | Section headers |

#### Sheet 1 — README
Explain the workbook purpose, color coding convention, how to use it, and list all sheets.

#### Sheet 2 — Capacity Plan

**Section A — Inputs** *(all yellow, user-editable)*  
List all tunable parameters: DAU, actions per user, data sizes, peak multiplier, replication factor, cache hit rate, retention years, and any system-specific parameters. Each cell must have a descriptive label and explicit units (e.g., "DAU [millions]", "Avg message size [KB]").

**Section B — Calculated Metrics** *(all formula-driven, referencing Section A)*  
Group metrics by category with sub-headers:
- **Traffic:** Write RPS, Read RPS, Peak RPS variants, Total RPS
- **Storage:** Daily raw, daily compressed, with replication, with metadata, annual, 5-year
- **Bandwidth:** Egress, CDN offload, origin load, daily transfer
- **Infrastructure:** DB shard count, replica count, cache size, server counts per tier
- **Cost:** Per-component monthly breakdown, total monthly, total annual
- **System-specific:** (e.g., GPU count, inference tokens/sec, recommendation RPS, pipeline throughput)

**Section C — Growth Scenarios**  
A scaling table with columns **1×, 3×, 5×, 10×** for all key metrics from Section B. All values must be formula-driven: `= [Section B cell] × [multiplier]`.

**Section D — Architecture Reference** *(text, no formulas)*  
Table: **Layer | Technology | Justification**. One row per architecture layer.

#### Sheet 3 — Pipeline *(if applicable)*
7–10 row table: **Stage | Component | Tech Stack | Latency SLO | Key Metric | Handoff Notes**. One pipeline stage per row.

#### Sheet 4 — Glossary
25+ terms relevant to this system.  
Columns: **Term | Definition | Formula (where applicable)**  
Include all domain-specific acronyms and all non-obvious formulas used in Section B.

---

### Output Format

1. State your scale assumptions and all derived constants (seconds/day, MAU, etc.)
2. Deliver the complete single-file HTML dashboard
3. Deliver the complete Python script to generate the Excel workbook
4. Run the Python script in bash and confirm the file was created with zero errors
5. Share the Excel file for download

### Quality Requirements

- All numbers must be self-consistent — changing one input must cascade correctly everywhere
- State units explicitly for every metric (RPS, TB, PB, Gbps, Tbps, $)
- Justify every technology choice in 1–2 sentences
- Round to 2 decimal places for storage and bandwidth; use integers for RPS and server counts
- No hardcoded values in Section B or C — every cell must derive from Section A

---

## Formulas Reference Sheet

These are the core formulas used across all capacity planning exercises. Reference them when filling in or reviewing the generated output.

| Metric | Formula |
|---|---|
| Write RPS | `(DAU × writes_per_user_per_day) / 86,400` |
| Read RPS | `(DAU × reads_per_user_per_day) / 86,400` |
| Peak RPS | `Avg RPS × peak_multiplier` |
| Daily storage (raw) | `writes_per_day × avg_object_size_bytes` |
| Daily storage (compressed) | `Daily raw × (1 − compression_ratio)` |
| Storage with replication | `Compressed × replication_factor` |
| Annual storage | `Daily (with replication) × 365` |
| Egress bandwidth | `Peak read RPS × avg_response_size_bytes × 8 / 1e12` (Tbps) |
| CDN traffic offloaded | `Egress × cache_hit_rate` |
| Origin bandwidth | `Egress × (1 − cache_hit_rate)` |
| DB shard count | `ceil(total_data_size / max_shard_size)` |
| Cache working set | `DAU × hot_data_per_user_bytes` |
| Kafka partitions | `ceil(Peak write RPS / throughput_per_partition)` |
| GPU count (AI) | `ceil(model_VRAM_GB / gpu_VRAM_GB) × replication_factor` |
| Inference RPS (AI) | `(DAU × prompts_per_user) / 86,400` |
| Tokens per second (AI) | `Inference RPS × avg_tokens_per_response` |

---

## Example Systems Built With This Template

| System | DAU | Peak RPS | Annual Storage | Monthly Cost (est.) |
|---|---|---|---|---|
| WhatsApp-like messaging | 2B | ~2.3M | ~14 PB | ~$4.2M |
| Instagram Reels | 500M | ~6.77M | ~1.2 EB | ~$12M |
| YouTube-like video | 500M | ~1.4M | ~600 PB | ~$8.5M |
| Netflix streaming | 250M | ~900K | ~450 PB | ~$6.1M |
| ChatGPT-scale AI | 100M | ~580K | ~15 PB | ~$22M |

*Note: All figures are estimates for educational/planning purposes.*

---

*Template by Anu Sasidharan · OrionVexa (orionvexa.ca) · github.com/anulsasidharan*
