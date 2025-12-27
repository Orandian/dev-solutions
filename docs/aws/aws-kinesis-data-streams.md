# Amazon Kinesis Data Streams

## What is Kinesis Data Streams
Amazon Kinesis Data Streams is a **real-time streaming data service** designed for **high-throughput event ingestion and processing**.

It allows multiple consumers to read the same data stream independently.

---

## Where Kinesis Fits
Producers → Kinesis Data Stream → Consumers

Used for:
- Clickstream data
- Logs & metrics
- IoT data
- Financial transactions

---

## Core Concepts
### Shards
- Streams are divided into shards
- Shards control throughput and parallelism
- Ordering is guaranteed per shard

### Data Retention
- Data retained for 24 hours to 7 days
- Consumers can replay data

---

## Usage Example
### Website Click Tracking
1. Frontend sends click events to Kinesis
2. Events are stored in shards
3. Consumers process data for:
   - Real-time dashboards
   - Analytics
   - Storage

---

## Key Characteristics
- Pull-based consumption
- High throughput
- Ordered data per shard
- Replayable events

---

## Best Practices
- Choose shard count carefully
- Use partition keys wisely
- Monitor lag and throughput
