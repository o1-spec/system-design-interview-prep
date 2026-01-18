# System Design Interview Playbook

This playbook provides a **repeatable framework** for answering system design
interview questions clearly, confidently, and within time constraints.

It is designed to help you:
- Structure your answers under pressure
- Communicate trade-offs effectively
- Avoid common interview mistakes
- Adapt designs based on follow-up questions

---

## 1. The 45–60 Minute Interview Structure

A typical system design interview follows this flow:

| Phase | Time | Goal |
|----|----|----|
| Clarify Requirements | 5–10 min | Align on scope |
| High-Level Design | 10–15 min | Show architecture thinking |
| Deep Dive | 15–20 min | Demonstrate depth |
| Bottlenecks & Trade-offs | 5–10 min | Show senior judgment |
| Wrap-Up | 3–5 min | Summarize & propose improvements |

Always manage time deliberately.

---

## 2. Step-by-Step Answer Framework

### Step 1: Clarify the Problem (Never Skip This)

Ask questions before designing:
- Who are the users?
- What scale are we targeting?
- What features are in scope?
- What can we ignore?

**Example questions:**
- Is this global or regional?
- Do we need real-time updates?
- Read-heavy or write-heavy?
- Do we need strong consistency?

---

### Step 2: Define Requirements

Split requirements clearly:

#### Functional
- What the system must do

#### Non-Functional
- Scalability
- Availability
- Latency
- Durability
- Security

> Interviewers care more about **non-functional requirements**.

---

### Step 3: Back-of-the-Envelope Estimation (If Needed)

Estimate:
- QPS (average & peak)
- Storage growth
- Bandwidth
- Cache size

**Golden rule:**
Explain assumptions out loud — clarity > precision.

---

### Step 4: High-Level Architecture

Draw or describe:
- Clients
- Load balancers
- Core services
- Databases
- Caches
- Queues
- External dependencies

**Rule:**  
Start simple → evolve.

---

### Step 5: Data Model (Lightweight)

Define:
- Primary entities
- Keys
- Access patterns

Avoid over-modeling unless asked.

---

### Step 6: Deep Dive (Pick 1–2 Areas)

Common deep-dive areas:
- Caching strategy
- Database scaling
- Sharding
- Message delivery
- Failure handling

Let the interviewer guide this section.

---

### Step 7: Bottlenecks & Trade-offs

Explicitly discuss:
- What breaks first?
- Why this design was chosen
- What you sacrificed (consistency vs availability, cost vs latency)

This is where senior candidates stand out.

---

### Step 8: Wrap-Up & Improvements

End strong by proposing:
- Future optimizations
- Alternative designs
- Features to add with more time

---

## 3. Universal Design Patterns to Reuse

These patterns appear in almost every system:

### Load Balancer + Stateless Services
- Horizontal scaling
- Fault tolerance

### Cache-Aside Pattern
- Reduce database load
- Improve latency

### Message Queue
- Async processing
- Traffic smoothing
- Reliability

### Sharding + Replication
- Scale databases
- Improve availability

### CDN
- Global content delivery
- Reduce backend pressure

---

## 4. How to Handle Follow-Up Questions

When asked follow-ups:
1. Pause briefly
2. Restate the question
3. Explain trade-offs
4. Propose a clear solution

**Bad answer:** “It depends.”  
**Good answer:** “It depends — here are the trade-offs, and I’d choose X because…”

---

## 5. System-Specific Interview Focus

### URL Shortener
- Caching
- ID generation
- Hot key handling

### Web Crawler
- Politeness
- Deduplication
- Scheduling

### Notification System
- Reliability
- Retries
- Provider failures

### Chat System
- WebSockets
- Ordering
- Fan-out strategies

### Autocomplete
- Tries
- Ranking
- Caching hot prefixes

### Video Platform
- CDN
- Transcoding pipeline
- Cost optimization

### File Storage
- Metadata vs blob separation
- Sync
- Versioning

---

## 6. Common Interview Mistakes

Avoid these:
- Jumping into architecture too fast
- Ignoring non-functional requirements
- Over-engineering prematurely
- Not discussing trade-offs
- Running out of time

---

## 7. How to Practice Using This Repo

Recommended practice loop:
1. Pick a system
2. Spend 5 min clarifying requirements
3. Whiteboard high-level design
4. Deep dive one component
5. Answer follow-up questions
6. Review trade-offs

Repeat until it becomes automatic.

---

## 8. 60-Second Summary Template (Very Important)

Practice ending with this:

> “To summarize, the system uses a stateless service layer behind a load balancer,
> a cache to reduce read latency, a durable database with replication for availability,
> and a queue for async processing. The main trade-offs are X vs Y, and if we had more
> time I would improve Z.”

---

## 9. Final Advice

- Structure beats brilliance
- Clear communication beats complexity
- Trade-offs beat perfection
- Confidence comes from repetition

---

### ✅ Status
This playbook turns the repository into a **complete system design interview
preparation toolkit**, suitable for Big Tech, FinTech, and backend roles.
