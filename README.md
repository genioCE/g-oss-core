# g-oss-core · Lakehouse foundation (MinIO + Nessie + Trino + Iceberg)

**Audience:** O&G CIOs/CTOs and platform leads  
**Goal:** Replace expensive, proprietary warehouses with an *open table format* lakehouse that you control.

---

## Executive summary
- **What it is:** An open, vendor-neutral data foundation: **Apache Iceberg** tables stored in **MinIO** (S3‑compatible object storage), queried by **Trino**. **Project Nessie** acts as the REST catalog/“git for data.”
- **What it replaces:** Snowflake/Redshift/BigQuery for many analytics workloads; Hive Metastore + Parquet “data swamp.”
- **Outcomes:** Lower $/TB, engine choice (Trino/Spark/Presto), safe schema evolution, ACID tables, time travel, and branch-based development of data.

## Where it fits
```
[Producers] → (Kafka/Files/DBs) → [ICEBERG TABLES on MinIO] ⇄ [Trino SQL]
                                      ^ Nessie catalog (branches/tags/commits)
```
`g-oss-core` is the **data plane** every other module targets.

## Typical O&G use cases
- Production accounting marts (daily/weekly summaries).
- SCADA analytics mirrors (read-only) for reliability tracking.
- Ad hoc engineering queries across years of well files + SCADA.
- Finance/ops reconciliations with versioned, immutable tables.

## What’s included
- **MinIO** (S3‑compatible object storage) with ready buckets.
- **Project Nessie** catalog for Iceberg (branching/time travel).
- **Trino** with Iceberg connector preconfigured.

## Pilot SLOs (defaults)
- **Query availability:** ≥ 99.9% in business hours.
- **Iceberg commit latency:** < 5s typical for small commits.
- **Catalog integrity:** strongly consistent via Nessie commits.

## Security & compliance
- Supports **encryption in transit**, bucket policies, and IAM integration via reverse proxies/Keycloak (in `g-oss-security`).  
- **Auditability:** Nessie branches/tags provide a reproducible history of table states (useful for SOX/financial reconciliations).  
- **OT guardrail:** This layer is *analytics only*; no writes to control systems.

## Cost model
- Storage: object storage at commodity prices.  
- Compute: Trino scales horizontally; idle costs are minimal.  
- Ops: small platform/SRE footprint; optional vendor support contracts.

## Deployment options
- **POC:** Docker Compose (this repo).  
- **Prod:** Kubernetes with Helm (publish chart from this layout).  
- Integrates with `g-oss-cdc`, `g-oss-batch`, `g-oss-bi` out of the box.

## KPIs for leadership
- $/TB stored and queried, query success rate, average query time, branch-based promotion lead time (dev→prod).

## Risks & limits
- Complex SQL/ML still needs Trino/Spark tuning and table layout (partitioning, clustering).  
- Avoid mixing raw Parquet folders with managed Iceberg tables.

## Quick start
```bash
cp .env.example .env
docker compose up -d
# MinIO: http://localhost:9001  • Trino: http://localhost:8081 • Nessie: http://localhost:19120
```
