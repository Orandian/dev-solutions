# Amazon SNS (Simple Notification Service)

## What is Amazon SNS
Amazon SNS is a fully managed **publish–subscribe (pub/sub) messaging service** that allows messages to be **pushed** to multiple subscribers.

A producer publishes a message to a **topic**, and SNS delivers that message to all subscribed endpoints.

---

## Where SNS Fits in an Application
Typical architecture:

Service → SNS Topic → Subscribers

Subscribers can be:
- Amazon SQS
- AWS Lambda
- Email / SMS
- HTTP / HTTPS endpoints
- Mobile push notifications

SNS is ideal for **event fan-out**.

---

## SNS Message Flow
1. Publisher sends a message to an SNS topic
2. SNS pushes the message to all subscribers
3. Each subscriber processes the message independently

---

## SNS Usage Example
### Order Created Event
1. Order Service publishes `OrderCreated` to SNS
2. SNS delivers the message to:
   - Email Service
   - Analytics Service
   - Inventory Service
3. Order Service returns immediately

This enables loose coupling and scalability.

---

## Key Characteristics
- Push-based delivery
- One message → many consumers
- No long-term message storage
- Near real-time delivery

---

## Best Practices
- Combine SNS with SQS for durability
- Use message filtering
- Keep messages small
- Avoid heavy logic in SNS
