# Amazon Managed Service for Apache Flink

## What is Apache Flink
Apache Flink is a **real-time stream processing framework** for stateful and event-driven applications.

Amazon provides a fully managed Flink service.

---

## Where Flink Fits
Kinesis Data Streams → Flink → Output

Outputs:
- Another Kinesis stream
- S3
- Databases
- Monitoring systems

---

## Core Capabilities
- Stateful stream processing
- Event-time processing
- Windowing and aggregations
- Exactly-once processing

---

## Usage Example
### Fraud Detection
1. Transactions stream into Kinesis
2. Flink groups events by user
3. Rolling calculations detect anomalies
4. Alerts are generated in real time

---

## Key Characteristics
- Real-time processing
- Stateful applications
- Scales horizontally
- Managed infrastructure

---

## Best Practices
- Design efficient state
- Use checkpoints
- Monitor latency and backpressure
