# 🔗 DevOps & SRE Notes

## Paper: Structuring and Design of Home Automation System Using IoT

**File:** `2018-iot-home-automation.pdf` | **Published:** IJRTER, Vol. 4, May 2018

---

## What the Paper Does

This paper designs a home automation system using a Raspberry Pi as the core processing unit, an ESP8266 Wi-Fi module for wireless data transmission, and cloud storage for persisting sensor data. Users can control household appliances remotely via a mobile or web interface from anywhere in the world.

Key technical elements:

- Raspberry Pi as the central compute node
- ESP8266 Wi-Fi chip transmitting sensor data to a cloud server
- Variable voltage regulators recording electrical consumption data
- Real-time alerting when anomalous activity is detected at home
- Double-encrypted remote command execution to prevent interception
- Digital lock mechanism with encrypted authentication codes

---

## Where It Connects to DevOps & SRE

### 1. Observability Pipeline Architecture

The paper implements a **sensor → agent → cloud → alert → action** pipeline. This is structurally identical to a modern infrastructure observability stack:

| Paper Component                    | DevOps/SRE Equivalent                                        |
| ---------------------------------- | ------------------------------------------------------------ |
| Hardware sensors (voltage, motion) | Application metrics, system metrics                          |
| ESP8266 Wi-Fi module               | Metrics agent (e.g. Prometheus Node Exporter, Datadog Agent) |
| Cloud storage backend              | Time-series database (Prometheus, InfluxDB, CloudWatch)      |
| Anomaly alert to user              | Alerting rules (PagerDuty, Alertmanager, OpsGenie)           |
| Remote command execution           | Runbook automation, remote remediation                       |

The fundamental design decision — decouple data collection from data storage, push to a centralised backend, alert on thresholds — is the same pattern used in every production monitoring stack today.

### 2. Real-Time Alerting on Anomalous Conditions

The paper explicitly states: _"Whenever any unusual activity is noticed the user will be notified by the system."_ This is the same concept as SRE alerting on SLO breach or anomalous system behaviour. The challenge of reducing false positives (alerting only on genuinely unusual activity, not noise) is one SREs manage constantly with alert tuning.

### 3. Secure Remote Access & Encrypted Command Execution

The paper addresses a classic SRE/security concern: _"even when the cloud or the database is compromised, the security code is not visible or understandable by the hacker. The code from the client side is double encrypted so that it cannot be tapped by a middle man."_

This maps to:

- **Secrets management** (Vault, AWS Secrets Manager)
- **Encrypted API communication** (TLS, mTLS)
- **Zero-trust access control** for remote operations
- **Preventing MITM attacks** in infrastructure automation pipelines

### 4. Edge-to-Cloud Data Architecture

The Raspberry Pi acts as an **edge compute node** — processing data locally before transmitting to cloud. This is exactly the edge computing pattern used in modern infrastructure for reducing latency and bandwidth costs. SREs working on IoT-scale or distributed infrastructure deal with this trade-off constantly.

### 5. Energy Monitoring & Cost Optimisation

The system records energy consumption data in real time and optimises appliance usage. In cloud SRE, this maps to **cost observability** — tracking infrastructure resource consumption, identifying waste, and automating cost optimisation. FinOps is an increasingly core SRE responsibility.

---

## What I Would Do Differently Today (SRE Perspective)

- Replace the custom cloud backend with a managed time-series service (e.g. AWS Timestream, InfluxDB Cloud) to reduce operational burden
- Add structured logging with correlation IDs across the sensor-to-cloud pipeline for easier debugging
- Implement exponential backoff on the Wi-Fi module's cloud upload retries to handle network partitions gracefully
- Add an SLO for alert delivery latency (e.g. anomaly detected → user notified within 30 seconds)
- Use a message queue (MQTT broker) between sensors and the cloud backend to decouple producers from consumers and handle bursts

---

_Notes written: March 2026_
