# Notification System Design

This document describes the design of a scalable notification system that can
send notifications via push (mobile), SMS, and email, while remaining reliable,
low latency, and extensible.

---

## 1. Problem Statement

Design a notification system that:
- Sends notifications to users through multiple channels
- Supports high throughput (millions of notifications)
- Ensures reliability (retries, delivery guarantees)
- Allows user preferences (opt-in/out per channel)
- Scales horizontally and is fault tolerant

---

## 2. Requirements

### Functional Requirements
- Send notifications via:
  - Push notifications (iOS / Android)
  - SMS
  - Email
- Support single-user and bulk notifications
- Support user notification preferences
- Provide basic delivery status (optional)

### Non-Functional Requirements
- High availability
- Low latency for real-time notifications
- Reliability and retry mechanisms
- Scalability (spiky traffic)
- Observability (metrics, logs, monitoring)

---

## 3. High-Level Architecture
Client / Internal Services
→ Notification API
→ Message Queue
→ Notification Workers
→ Channel Providers (Push / SMS / Email)
→ User Devices / Inbox

Key idea: **decouple request ingestion from delivery** using a message queue.

---

## 4. Core Components

### 4.1 Notification API (Ingress)
Receives notification requests and performs:
- Authentication / authorization
- Request validation
- Rate limiting
- Preference checks (or defer to workers)
- Enqueueing messages to the queue

---

### 4.2 Message Queue
A queue buffers notifications and smooths traffic spikes.

**Why a queue is critical:**
- Prevents overload of downstream providers
- Enables retries without blocking callers
- Allows horizontal scaling of workers

---

### 4.3 Notification Workers (Delivery Service)
Workers consume messages from the queue and:
- Resolve user preferences
- Choose the correct channel(s)
- Apply templates
- Call external providers (APNs/FCM, SMS gateway, email provider)
- Handle retries and failures
- Emit logs/metrics/events

---

### 4.4 Channel Providers
Typical provider integrations:
- **Push:** APNs (iOS), FCM (Android)
- **SMS:** Twilio / local SMS gateway
- **Email:** SendGrid / SES / mail provider

Providers can be swapped without changing the core system.

---

## 5. Data Model

### 5.1 Notification Request
| Field | Description |
|---|---|
| notification_id | Unique ID |
| user_id | Target user |
| channel | push / sms / email |
| template_id | Template reference |
| payload | Template variables / message |
| priority | high / normal / low |
| created_at | Timestamp |

### 5.2 User Preferences
| Field | Description |
|---|---|
| user_id | Unique ID |
| push_enabled | boolean |
| sms_enabled | boolean |
| email_enabled | boolean |
| quiet_hours | optional schedule |
| topics | optional subscriptions |

---

## 6. Notification Flow

### 6.1 Sending a Notification
Service
→ Notification API
→ Queue
→ Worker
→ Provider
→ User

### 6.2 Preference Filtering
Worker
→ Preferences Store
→ Decide Channels
→ Send via Enabled Channels

---

## 7. Push Notifications (Mobile)

Push delivery requires:
- Device tokens (APNs/FCM tokens)
- Token refresh handling
- Invalid token cleanup

### Token Storage
| Field | Description |
|---|---|
| user_id | Unique ID |
| device_id | Device identifier |
| platform | iOS / Android |
| token | Push token |
| updated_at | Timestamp |

---

## 8. Reliability: Retries & DLQ

### Retry Strategy
- Retry transient errors (timeouts, 5xx)
- Exponential backoff
- Max retry count

### Dead Letter Queue (DLQ)
Messages that fail repeatedly are moved to a DLQ for:
- Investigation
- Manual replay
- Debugging
Queue
→ Worker (fails after N retries)
→ DLQ

---

## 9. Rate Limiting & Abuse Protection

Apply rate limits at:
- Notification API (per service / per user)
- Worker (per provider / per destination)

This prevents:
- Provider throttling
- Spam / abuse
- System overload

---

## 10. Templates & Localization

### Templates
- Store message templates by `template_id`
- Support variables (e.g., `{name}`, `{amount}`)
- Keep templates outside code for easy updates

### Localization
- Store templates per locale (e.g., `en`, `fr`, `yo`)
- Worker selects based on user preference

---

## 11. Scheduling & Quiet Hours (Optional)

Support delayed delivery:
- Schedule a notification for a future time
- Respect quiet hours (do not disturb)
- API
→ Scheduler (delayed queue)
→ Delivery Queue
→ Worker

---

## 12. Observability & Monitoring

Track:
- Notification throughput
- Delivery latency
- Success/failure rates by provider
- Retry counts
- DLQ size

Use:
- Logs (debug failures)
- Metrics (SLIs/SLOs)
- Alerts (provider outage, high failure rate)

---

## 13. Scaling Considerations

### Horizontal Scaling
- Scale Notification API statelessly behind a load balancer
- Scale workers based on queue depth

### Provider Limits
- Providers enforce rate limits
- Use batching (where possible)
- Use multiple providers if needed

---

## 14. Security Considerations

- Authenticate internal services sending notifications
- Validate payloads to avoid injection
- Encrypt sensitive data (e.g., phone numbers, emails)
- Avoid leaking PII in logs

---

## 15. Bottlenecks & Trade-offs

| Component | Issue | Mitigation |
|---|---|---|
| Queue | Backlog growth | Autoscale workers |
| Providers | Throttling/outages | Backoff + failover provider |
| Preferences lookup | Hot reads | Cache preferences |
| Push tokens | Token churn | Regular cleanup |

---

## 16. Key Takeaways

- Use a message queue to decouple ingestion from delivery
- Workers should be stateless and horizontally scalable
- Retries + DLQ are essential for reliability
- Preferences and templates make the system flexible
- Monitoring is critical due to external provider dependencies

---

### ✅ Status
This system design shows how to build a reliable, scalable notification system
with multiple channels and strong failure handling.
