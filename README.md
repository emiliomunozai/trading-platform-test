# LLM Trading — Systems & Architecture Test

## Objective
Design and implement a near-real-time trading decision system that leverages LLMs for market analysis but still achieves sub-500ms decisions.

---

## Scenario
You are tasked with building a trading system with these constraints:

- **Data Ingestion**: Market news from 5 providers (Reuters, Bloomberg, AP, WSJ, FT).
- **LLM Agents**:
  - **News Sentiment Agent** → analyzes breaking news.  
  - **Market Context Agent** → evaluates current market conditions.  
  - **Risk Assessment Agent** → scores trade risk/reward.
- **LLM Latency**: Each inference takes 2–5 seconds.  
- **Decision Latency**: System must respond in <500ms (p95).  
- **Scale**: 100+ articles per minute during market hours.

**Key Challenge**  
How do you achieve <500ms decisions despite slow LLM inference?

---

## Deliverables

### Part 1 — System Design (30 min)
Create `system_design.md` (≤2 pages) covering:

1. **Latency Optimization Strategy**  
   - How to reconcile 2–5s LLM calls with <500ms decisions.  
   - Caching, pre-computation, fast-path vs slow-path.  
   - Trade-offs between accuracy and speed.

2. **Architecture Diagram + Data Flow**  
   - Show ingestion → processing → caching → decision API.  
   - Include latency budget per step.  
   - Identify bottlenecks & mitigation (parallelism, async).

3. **Multi-Agent Orchestration**  
   - How the 3 agents collaborate (parallel/sequential).  
   - Aggregation & consensus logic.  
   - Avoiding sequential 15s latency.

4. **Technology Stack (justify for latency)**  
   - Cache layer (Redis, vector DB, etc).  
   - Streaming/queue choice.  
   - Model hosting + versioning approach.

5. **Pre-computation & Cache Warming**  
   - What can be computed ahead of time.  
   - Cache invalidation & freshness strategies.  
   - Cold-start mitigation.

6. **Failure Modes & Graceful Degradation**  
   - When LLMs are slow/unavailable.  
   - Fallback to rules/smaller models.  
   - Circuit breakers & timeouts.

---

### Part 2 — Implementation Prototype (90 min)
Implement a simplified prototype (Docker Compose with 2 services):

- **Redis** — cache for pre-computed features or agent outputs.  (Find them in the data folder)
- **Trading API** — FastAPI service that:  
  - `POST /decide` returns a trading decision in <500ms.  
  - Uses cached/pre-computed values (simulate LLM outputs or use the ones in the data folder).
  - Use prices data from the folder as aditional indicators or to make hybrid vectors.
  - Includes latency, model version, cache hit/miss, fallback info.

**Infrastructure Requirements**
- Docker Compose with environment configs (dev/prod).  
- Health checks & resource limits.  
- Structured logging & basic monitoring (`/metrics` in Prometheus format).

**Acceptance Criteria**
- `docker-compose up --build` works.  
- P95 latency <500ms (simulate 30 requests).  
- Cache hit rate ≥80%.  
- No unhandled 500s.

### Part 3 — Data Pipeline Sketch (30 min)
- Sketch a simple Databricks pipeline (pipeline.py) that ingests news from a source (RSS feed, JSON API, or webpage), normalizes it, and loads it into a Delta table. Include methods for extract_from_news, transform, and load_to_cache, and provide a Databricks Job JSON to orchestrate it.
---
