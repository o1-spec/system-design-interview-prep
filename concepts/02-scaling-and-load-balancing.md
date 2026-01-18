# 02 – Scaling, Load Balancing & Capacity Estimation

This chapter builds on the fundamentals and focuses on how systems scale, how
traffic is distributed safely, and how engineers estimate capacity using
back-of-the-envelope calculations.

---

## 1. Why Scaling Matters

As traffic grows, a single server can no longer handle:
- Increased request volume
- Higher latency expectations
- Reliability requirements

Scaling ensures:
- High availability
- Fault tolerance
- Predictable performance under load

---

## 2. Vertical Scaling vs Horizontal Scaling

### Vertical Scaling (Scale Up)
Increase the resources of a single server.

**Examples:**
- More CPU
- More RAM
- Faster disks

**Advantages:**
- Simple to implement
- No architectural changes

**Limitations:**
- Hard hardware limits
- Single point of failure
- Downtime during upgrades

---

### Horizontal Scaling (Scale Out)
Add more servers to share the workload.

**Advantages:**
- Better fault tolerance
- Higher availability
- Near-linear scalability

**Trade-offs:**
- Increased system complexity
- Requires load balancing
- Needs stateless services

> Modern distributed systems rely primarily on horizontal scaling.

---

## 3. Load Balancers

A load balancer distributes incoming traffic across multiple backend servers.

### Responsibilities
- Distribute traffic evenly
- Detect unhealthy servers
- Route traffic away from failures

### Benefits
- Prevents server overload
- Enables failover
- Improves response time

### Typical Flow
Client
→ DNS
→ Load Balancer
→ Web Server Pool

If one server fails, traffic is automatically routed to healthy servers.

---

## 4. Database Scaling Overview

### Read Scaling
- Use **replicas (slaves)** to handle read traffic
- Master handles writes
- Slaves handle reads

### Write Scaling
- Harder to scale
- Often requires:
  - Sharding
  - Partitioning
  - Specialized databases

---

## 5. Back-of-the-Envelope Estimation

Back-of-the-envelope estimation helps engineers:
- Plan capacity
- Set realistic expectations
- Identify bottlenecks early
- Guide architectural trade-offs

---

## 6. Power-of-Two Reference

| Unit | Value |
|----|----|
| KB | 10³ bytes |
| MB | 10⁶ bytes |
| GB | 10⁹ bytes |
| TB | 10¹² bytes |
| PB | 10¹⁵ bytes |

Engineers often approximate using powers of two for faster estimation.

---

## 7. Availability Numbers

High availability means a system remains operational for long periods.

| Availability | Downtime / Year |
|------------|----------------|
| 99% | ~3.65 days |
| 99.9% | ~8.8 hours |
| 99.99% | ~52 minutes |
| 99.999% | ~5 minutes |

---

## 8. Common Estimation Metrics

System design interviews frequently ask you to estimate:

- QPS (Queries Per Second)
- Peak QPS
- Storage requirements
- Bandwidth usage
- Cache size
- Number of servers

---

## 9. Estimation Order (Golden Rule)

Always estimate in this order:

1. Traffic
2. Storage
3. Bandwidth
4. Memory / Cache
5. Growth

Following this order prevents confusion and missed constraints.

---

## 10. Example: Video Platform Estimation

**Assumptions:**
- Daily Active Users (DAU)
- Views per user per day
- Average video length
- Bitrate distribution
- Peak concurrency
- CDN hit rate

**Key calculations:**
- Total daily views
- Average bitrate (weighted)
- Peak concurrent users
- Peak bandwidth
- Storage growth per day

Walk through assumptions clearly with the interviewer to stay aligned.

---

## 11. Key Takeaways

- Horizontal scaling is the foundation of large systems
- Load balancers are essential for fault tolerance
- Databases scale reads more easily than writes
- Estimation is about clarity, not precision
- Communicating assumptions is as important as the math

---

### ✅ Status
This chapter provides the foundation for designing systems that scale
predictably and reliably.
