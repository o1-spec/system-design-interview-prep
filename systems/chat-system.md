# Chat System Design

This document describes the design of a scalable real-time chat system that
supports 1:1 messaging and group chat, similar to WhatsApp/Signal/Telegram
(at a high level), focusing on architecture, data flow, scalability, and
trade-offs.

---

## 1. Problem Statement

Design a chat system that:
- Supports real-time messaging
- Works on mobile and web
- Supports 1:1 and group chat
- Supports multi-device sync
- Sends push notifications when users are offline
- Scales to millions of users

---

## 2. Requirements

### Functional Requirements
- Send and receive messages (1:1)
- Group chat (send message to a group)
- Message delivery to online users in real time
- Store messages for offline users and later delivery
- Push notifications for offline users
- Multi-device support (same user on multiple devices)

### Non-Functional Requirements
- Low latency (near real-time)
- High availability
- Scalability
- Durability (messages should not be lost)
- Observability (logs/metrics/monitoring)

---

## 3. High-Level Architecture
Client (Web/Mobile)
→ Load Balancer
→ Chat Gateway (WebSocket)
→ Chat Service
→ Message Queue
→ Message Store (DB)
→ Notification Service (Push)


Key idea: Use **WebSockets** for real-time delivery and a **message store** for
durability and offline delivery.

---

## 4. Client–Server Communication

### 4.1 HTTP vs WebSocket

- **HTTP** is request/response (not ideal for continuous real-time updates)
- **WebSocket** provides a persistent, bidirectional connection (ideal for chat)

### WebSocket Flow
Client
→ WebSocket Handshake (HTTP Upgrade)
→ Persistent Connection
→ Bi-directional Messaging


---

## 5. Core Components

### 5.1 Chat Gateway (WebSocket Layer)
Responsible for:
- Maintaining WebSocket connections
- Authenticating users on connect
- Routing messages to the correct chat service instance
- Handling reconnects and heartbeats

This layer should be **stateless** (besides active connections) and horizontally scalable.

---

### 5.2 Chat Service (Message Orchestration)
Responsible for:
- Validating messages
- Assigning message IDs
- Persisting messages
- Publishing to message queue for delivery
- Managing read receipts / delivery receipts (optional)

---

### 5.3 Message Queue
Used to:
- Decouple sending from delivery
- Smooth spikes in traffic
- Enable async fan-out for group chat
- Support retries

---

### 5.4 Message Store (Database)
Stores:
- Conversations
- Messages
- Delivery state (optional)
- Group membership (optional)

Common choices:
- NoSQL for high write throughput (e.g., Cassandra/Dynamo-style)
- Or SQL + careful indexing for smaller scale

---

### 5.5 Presence Service (Optional but common)
Tracks:
- Online/offline status
- Last seen time
- Active devices

Presence is usually eventually consistent.

---

### 5.6 Notification Service
Sends push notifications when:
- Recipient is offline
- Recipient has disabled in-app delivery (rare)
- Device is not connected

Push providers:
- APNs (iOS)
- FCM (Android)

---

## 6. Data Model (Simple)

### 6.1 Messages Table (or Collection)
| Field | Description |
|---|---|
| message_id | Unique ID |
| conversation_id | Chat thread ID |
| sender_id | Sender user ID |
| recipient_id | Recipient user ID (for 1:1) |
| group_id | Group ID (for group chat) |
| content | Text/media metadata |
| created_at | Timestamp |
| status | sent/delivered/read (optional) |

### 6.2 Conversations
| Field | Description |
|---|---|
| conversation_id | Unique ID |
| participants | List of users |
| type | 1:1 or group |
| created_at | Timestamp |

### 6.3 Group Membership (for group chat)
| Field | Description |
|---|---|
| group_id | Unique ID |
| user_id | Member |
| role | admin/member |
| joined_at | Timestamp |

---

## 7. Message Flow (1:1)

### 7.1 Online Recipient
Sender Client
→ WebSocket (Chat Gateway)
→ Chat Service
→ Message Store (persist)
→ Delivery Queue
→ Recipient Chat Gateway
→ Recipient Client (real-time)

### 7.2 Offline Recipient
Sender Client
→ Chat Service
→ Message Store (persist)
→ Notification Service (push)
→ Recipient comes online
→ Fetch undelivered messages
→ Deliver via WebSocket

---

## 8. Message Ordering & Delivery Guarantees

### Ordering
- Order messages per conversation using:
  - server timestamps, and/or
  - monotonically increasing message IDs per conversation

### Delivery Guarantees
Common approach:
- **At-least-once** delivery (with idempotency on client)
- Deduplicate using `message_id`

---

## 9. Group Chat (Fan-Out)

Two common approaches:

### 9.1 Fan-Out on Write
When sending:
- Write the message once
- Create deliveries for all group members
- Push to online members

**Pros:**
- Fast reads
- Good for active chats

**Cons:**
- Expensive for huge groups

---

### 9.2 Fan-Out on Read
When sending:
- Write message once to group
When reading:
- Each user queries the group timeline

**Pros:**
- Cheaper writes

**Cons:**
- Heavier reads
- More complex to compute unread state

---

## 10. Multi-Device Sync

A user may have multiple devices connected.
User A (Phone)
User A (Laptop)
User A (Tablet)

### Approach
- Maintain a list of active connections per user
- Deliver incoming messages to all active devices
- Track per-device read state (optional)

---

## 11. Storage & Scaling Strategy

### Partitioning
Partition messages by:
- `conversation_id` (common)
- or a hash of (conversation_id)

This keeps all messages in a chat near each other.

### Caching
Cache:
- recent conversations
- recent messages
- user presence

---

## 12. Handling Reconnects

Clients will disconnect due to:
- network changes
- app backgrounding
- device sleep

### Reconnect Flow
Client reconnects
→ Authenticate
→ Sync last delivered message ID
→ Fetch missing messages
→ Resume real-time stream

---

## 13. Observability

Monitor:
- connection counts
- message send latency
- delivery success/failure rates
- queue depth
- push notification success rate

Use:
- metrics + dashboards
- logs for debugging
- alerts for provider outages

---

## 14. Security Considerations

- Authenticate WebSocket connections (tokens)
- Rate limit message sending
- Prevent spam/abuse
- Encrypt sensitive fields at rest
- (Optional) End-to-end encryption (E2EE) at the application layer

---

## 15. Bottlenecks & Trade-offs

| Component | Bottleneck | Mitigation |
|---|---|---|
| WebSocket layer | Too many connections | Scale gateways horizontally |
| Message store | High write volume | Partitioning + NoSQL |
| Group fan-out | Expensive writes | Fan-out on read or hybrid |
| Push provider | Throttling/outage | Backoff + retries + failover |

---

## 16. Key Takeaways

- WebSockets are ideal for real-time chat delivery
- Durable message storage is required for offline users
- Message queues decouple delivery and improve reliability
- Group chat needs careful fan-out strategy
- Multi-device sync and reconnect logic are essential
- Monitoring and abuse protection matter at scale

---

### ✅ Status
This system design provides a complete high-level blueprint for building a
scalable chat system with real-time messaging and offline delivery.

## Interview Follow-Up Questions

1. How do you guarantee message ordering?
2. How would you support end-to-end encryption (E2EE)?
3. How do you handle message duplication?
4. How would you scale group chats with thousands of users?
5. How would you support message search?
6. How do you sync messages across multiple devices?
7. How do you handle reconnects and missed messages?
8. How would you support read receipts efficiently?
