# 🔗 DevOps & SRE Notes

## Paper: Artificial Intelligence and Its Application in Classification of Big Data

**File:** `2018-ai-bigdata-classification.pdf` | **Published:** IJIRCCE, Vol. 6, Aug 2018

---

## What the Paper Does

This paper examines how AI — specifically the Pollination-Based Optimization (PBO) algorithm — can classify large-scale structured and unstructured datasets more effectively. It frames this around two categories of Big Data technology:

**Operational Big Data** — Systems like MongoDB designed for real-time, interactive workloads where data is captured and stored continuously. NoSQL systems optimised for cloud-native architectures, enabling massive parallel computation at scale.

**Analytical Big Data** — Systems like MapReduce and MPP (Massively Parallel Processing) databases designed for retrospective, complex analysis across entire datasets. MapReduce is highlighted as complementary to SQL for large-scale batch analysis.

---

## Where It Connects to DevOps & SRE

### 1. The Operational vs Analytical Split — A Core Platform Engineering Decision

The paper's central distinction between operational and analytical Big Data systems is exactly the architectural decision SREs and platform engineers make when designing reliable data infrastructure:

| Paper Concept                         | SRE/Platform Engineering Equivalent                                |
| ------------------------------------- | ------------------------------------------------------------------ |
| Operational Big Data (MongoDB, NoSQL) | OLTP databases, event stores, real-time pipelines (Kafka, Kinesis) |
| Analytical Big Data (MapReduce, MPP)  | OLAP systems, data warehouses (BigQuery, Redshift, Snowflake)      |
| Hybrid deployment of both             | Lambda architecture, Kappa architecture                            |

The paper notes: _"These two classes of technology are complementary and frequently deployed together."_ This is exactly the hybrid architecture SREs operate — a real-time serving layer backed by a batch analytical layer — and managing reliability across both is a live SRE responsibility.

### 2. Cloud-Native Infrastructure for Scale

The paper explicitly discusses NoSQL Big Data systems being _"designed to take advantage of new cloud computing architectures that have emerged over the past decade to allow massive computations to be run inexpensively and efficiently."_

This maps directly to what SREs evaluate when choosing infrastructure:

- **Scalability** — can the system handle 10x traffic without manual intervention?
- **Cost efficiency** — are we over-provisioning compute for the actual workload?
- **Operational simplicity** — does the system reduce toil at scale?

These are the same trade-offs the paper discusses for Big Data systems, just applied to general infrastructure.

### 3. Data Privacy and Security as Operational Requirements

The paper states: _"you would require an infrastructure that can manage and process huge volumes of data in real time and can protect data privacy and security."_

In SRE this is a non-functional reliability requirement — security is treated as part of the reliability contract. Data systems that leak or corrupt data are failing their SLOs regardless of uptime. This framing — treating privacy and security as operational properties, not afterthoughts — is central to modern SRE practice.

### 4. Real-Time vs Batch Processing Trade-offs

The paper contrasts real-time data capture systems (MongoDB) against batch analysis systems (MapReduce). Managing this trade-off — when to process in real time vs when to batch — is a core SRE infrastructure decision affecting latency SLOs, cost, and system complexity.

The paper notes that _"some NoSQL systems can provide insights into patterns and trends based on real-time data with minimal coding"_ — which maps to managed streaming analytics services (Kinesis Data Analytics, Flink, Spark Streaming) that SREs evaluate for reducing operational overhead.

---

## What I Would Do Differently Today (SRE Perspective)

- Define explicit SLOs for both the operational layer (p99 write latency < 50ms) and the analytical layer (query results within 30s for datasets under 1TB)
- Add data pipeline observability — track lag, throughput, and error rates at each stage rather than treating the pipeline as a black box
- Implement dead-letter queues for failed classification jobs so no data is silently dropped
- Use schema validation at ingestion time (e.g. Apache Avro, Protocol Buffers) to prevent malformed data from corrupting downstream analytical results
- Build runbooks for common failure modes: replica lag, disk pressure on MongoDB nodes, MapReduce job failures

---

_Notes written: March 2026_
