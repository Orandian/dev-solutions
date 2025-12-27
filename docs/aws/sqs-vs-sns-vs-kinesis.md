# SQS vs SNS vs Kinesis

## Overview
AWS provides multiple messaging and streaming services, each designed for different use cases.

---

## Comparison Table

| Feature | SQS | SNS | Kinesis |
|------|----|----|--------|
| Pattern | Queue | Pub/Sub | Stream |
| Delivery | Pull | Push | Pull |
| Consumers | One per message | Many | Many |
| Ordering | FIFO only | No | Yes (per shard) |
| Replay | No | No | Yes |
| Throughput | Medium | High | Very High |
| Use Case | Background jobs | Event notifications | Real-time analytics |

## When to Use SQS
- Background job processing
- Task queues
- Reliable message delivery

---

## When to Use SNS
- Event notifications
- Fan-out architectures
- Event-driven systems

---

## When to Use Kinesis
- Real-time analytics
- High-volume event streaming
- Ordered event processing

---

## Common Architecture
Event → SNS → SQS → Worker  
Logs → Kinesis → Flink → S3
