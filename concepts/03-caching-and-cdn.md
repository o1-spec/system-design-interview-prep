# 03 – Caching & Content Delivery Networks (CDN)

This chapter explains how caching and Content Delivery Networks (CDNs) improve
system performance, reduce latency, and lower load on backend services.

---

## 1. Why Caching Matters

As systems scale, repeatedly fetching the same data from databases becomes:
- Slow
- Expensive
- A bottleneck under high traffic

Caching improves performance by storing frequently accessed or
expensive-to-compute data in memory so it can be served quickly.

---

## 2. What Is a Cache?

A **cache** is a temporary storage layer that holds data in memory for fast access.

### Characteristics
- Much faster than disk-based storage
- Stores frequently accessed data
- Reduces database load
- Improves response time

---

## 3. Cache Request Flow
Client Request
→ Web Server
→ Cache
├── Cache Hit → Return Data
└── Cache Miss → Query Database → Store in Cache → Return Data

---

## 4. When to Use a Cache

Caching is most effective when:
- Data is read frequently
- Data changes infrequently
- Database queries are expensive
- Low latency is required

Avoid caching when:
- Data changes constantly
- Strong consistency is required at all times

---

## 5. Cache Design Considerations

### Cache Expiration
Cached data should not live forever.
- Time-based expiration (TTL)
- Manual invalidation on updates

### Cache Consistency
Cached data may become stale.
Common strategies:
- Write-through cache
- Write-back cache
- Cache invalidation on writes

### Eviction Policy
When cache is full, data must be removed.

Common policies:
- Least Recently Used (LRU)
- Least Frequently Used (LFU)
- First In, First Out (FIFO)

### Fault Tolerance
- A single cache is a single point of failure
- Use replicated or distributed caches

---

## 6. Cache Placement Strategies

### Client-Side Cache
- Browser cache
- Mobile application cache

### Server-Side Cache
- In-memory cache (e.g., Redis, Memcached)
- Shared across multiple servers

### Database Cache
- Query result caching
- Reduces database load

---

## 7. Content Delivery Network (CDN)

A **Content Delivery Network (CDN)** is a network of geographically distributed
servers that deliver static content to users from the closest location.

---

## 8. How a CDN Works
User
→ DNS
→ CDN Edge Server
├── Cache Hit → Return Static Content
└── Cache Miss → Fetch from Origin Server → Cache → Return Content


Static content includes:
- Images
- Videos
- CSS
- JavaScript files

---

## 9. CDN vs Cache

| CDN | Cache |
|---|---|
| Global distribution | Local or regional |
| Optimized for static content | Can store any data |
| Edge servers worldwide | Inside infrastructure |
| Reduces latency globally | Reduces backend load |

A CDN behaves like a cache but operates at a **global scale**.

---

## 10. CDN Design Considerations

### Cost
- Charged by bandwidth usage
- More traffic leads to higher cost

### Cache Expiry
- Set appropriate expiration headers
- Balance freshness vs performance

### Consistency
- Static content may take time to update globally
- Asset versioning helps (e.g., `app.v2.js`)

---

## 11. Integrating Cache and CDN in Architecture
Client
→ CDN
→ Load Balancer
→ Web Server
→ Cache
→ Database

### Benefits
- CDN handles static content
- Cache handles dynamic but frequently accessed data
- Database load is minimized

---

## 12. Common Caching Pitfalls

- Caching too aggressively
- Poor invalidation strategy
- Treating cache as a source of truth
- Not handling cache failures gracefully

> Cache is an optimization, not a replacement for the database.

---

## 13. Best Practices

- Cache hot data aggressively
- Use TTLs to avoid stale data
- Replicate caches to avoid single points of failure
- Combine CDN and cache for optimal performance
- Monitor cache hit/miss ratios

---

## 14. Key Takeaways

- Caching dramatically improves latency and scalability
- CDNs reduce global latency for static content
- Cache invalidation is one of the hardest problems in system design
- A solid caching strategy is critical for high-traffic systems

---

### ✅ Status
This chapter completes the performance optimization foundation using
caching and CDNs.
