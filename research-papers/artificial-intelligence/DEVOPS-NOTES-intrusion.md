# 🔗 DevOps & SRE Notes

## Paper: Artificial Intelligence and Its Associated Applications _(Section 3.1.2)_

**File:** `2018-ai-associated-applications.pdf` | **Published:** IJIRCCE, Vol. 6, Apr 2018

> **Note:** This DevOps relevance is specific to **Section 3.1.2** of this paper — _Fuzzy Inference System in Intrusion Detection System_. The rest of the paper covers broader AI applications across other domains.

---

## What Section 3.1.2 Does

This section proposes a network intrusion detection system combining two ML paradigms:

1. **Artificial Neural Networks (ANN)** — for learning traffic patterns and detecting anomalies
2. **Fuzzy Inference System (FIS)** — for handling uncertainty and ambiguity in network behaviour classification

Implementation details:

- **SNORT** is used for real-time IP traffic analysis and packet logging during the training phase
- A **signature pattern database** is built using Protocol Analysis and Neuro-Fuzzy learning
- The model is validated against the **1998 DARPA Intrusion Detection Evaluation Dataset** and TCP raw data
- The system classifies traffic in real time as normal, suspicious, or malicious

---

## Where It Connects to DevOps & SRE

### 1. Network Observability — You Can't Secure What You Can't See

The paper's use of SNORT for real-time packet logging and traffic analysis is exactly what network observability means in an SRE context. Before you can detect intrusions, you need full visibility into what's flowing through your network — the same principle that drives SRE investment in distributed tracing, flow logs, and network telemetry.

| Paper Component            | SRE/DevSecOps Equivalent                                                       |
| -------------------------- | ------------------------------------------------------------------------------ |
| SNORT packet logging       | VPC Flow Logs, eBPF-based network telemetry, Zeek                              |
| Signature pattern database | Threat intelligence feeds, SIEM rule sets                                      |
| Neuro-Fuzzy classification | ML-based anomaly detection (AWS GuardDuty, Datadog Security Monitoring, Falco) |
| DARPA dataset validation   | Red team exercises, chaos engineering for security                             |

### 2. Rule-Based vs Anomaly-Based Detection — The Same Debate SREs Have

The paper combines signature-based detection (known bad patterns) with anomaly-based detection (deviation from learned normal behaviour). This is a live debate in DevSecOps:

- **Signature-based** (like SNORT rules): low false-positive rate, misses zero-days
- **Anomaly-based** (ML-driven): catches novel threats, higher false-positive rate, needs tuning

Modern security tools (Falco, GuardDuty, Datadog CSM) use exactly this hybrid approach — the paper was working on this problem in 2018. The challenge of tuning anomaly detection to reduce alert fatigue without missing real threats is a constant SRE/security engineering problem.

### 3. Protocol Analysis as a Form of Structured Observability

The paper builds its signature database using Protocol Analysis — inspecting network traffic at the protocol level to understand what normal looks like. In SRE this maps to:

- **Structured logging** — parsing application output into queryable fields rather than treating logs as opaque text
- **Protocol-aware proxies** (Envoy, Istio) — understanding HTTP, gRPC, and database protocol semantics to make smarter routing and observability decisions
- **Service mesh telemetry** — observing traffic at L7 rather than just L4

### 4. Real-Time Detection vs Batch Analysis

The system performs real-time traffic analysis during the detection phase. In SRE, the choice between real-time detection (streaming, low latency) and batch analysis (higher accuracy, delayed) is a fundamental reliability architecture decision. Alert latency is a component of your overall incident response SLO — the faster you detect, the faster you can mitigate.

### 5. Validation Against Known Datasets → Chaos Engineering Mindset

Validating the model against the DARPA intrusion detection dataset is a form of controlled testing against known failure scenarios. In SRE this maps to chaos engineering — deliberately injecting known failure modes to verify that your detection and response systems actually work before a real incident occurs.

---

## What I Would Do Differently Today (SRE Perspective)

- Move from batch model retraining to online learning so the detection model adapts to evolving attack patterns without manual retraining cycles
- Instrument the detection system itself — track detection latency, false positive rate, and missed detections as operational metrics with their own SLOs
- Add explicit runbooks for each alert type: what does a "suspicious" classification mean, what is the on-call response, when to escalate?
- Integrate with a SIEM (Splunk, Elastic Security) to correlate network-level detections with host and application logs for richer incident context
- Define a detection SLO: intrusion events should be flagged within N seconds of occurrence with a false positive rate below X%

---

_Notes written: March 2026_
