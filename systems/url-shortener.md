# URL Shortener System Design

This document describes the design of a scalable URL shortening service similar
to bit.ly or tinyurl, focusing on requirements, architecture, data modeling,
and scalability trade-offs.

---

## 1. Problem Statement

Design a system that:
- Converts long URLs into short URLs
- Redirects users from short URLs to original URLs
- Scales to handle high read traffic
- Remains highly available and low latency

---

## 2. Requirements

### Functional Requirements
- Generate a short URL from a long URL
- Redirect users to the original URL
- Support custom aliases (optional)
- Track basic analytics (optional)

### Non-Functional Requirements
- High availability
- Low latency redirects
- Scalability (read-heavy)
- Durability (URLs should not be lost)

---

## 3. Capacity Estimation (Back-of-the-Envelope)

**Assumptions:**
- 100 million URLs created per day
- Read-to-write ratio = 100:1
- Average URL length ≈ 100 bytes
- Short URL length ≈ 7 characters

**Estimates:**
- Write QPS ≈ 1,200
- Read QPS ≈ 120,000
- Storage per URL ≈ 150 bytes
- Daily storage ≈ 15 GB

---

## 4. High-Level Architecture
Client
→ DNS
→ Load Balancer
→ API Servers
→ Cache
→ Database

- API servers handle URL creation and redirection
- Cache stores hot short URLs
- Database stores persistent mappings

---

## 5. API Design

### Create Short URL
POST /api/shorten
Request: { longUrl }
Response: { shortUrl }

### Redirect
GET /{shortUrl}
→ HTTP 301 / 302 redirect to longUrl

---

## 6. URL Redirection (301 vs 302)

- **301 (Permanent Redirect)**
  - Cached by browsers
  - Better for SEO
- **302 (Temporary Redirect)**
  - Not cached
  - Allows analytics tracking

Most URL shorteners use **302** to retain control and collect metrics.

---

## 7. Data Model

### URL Table
| Field | Description |
|---|---|
| short_url | Primary key |
| long_url | Original URL |
| created_at | Creation timestamp |
| expires_at | Optional expiration |
| user_id | Optional owner |

---

## 8. Short URL Generation

### Option 1: Auto-Increment ID + Base62 Encoding
- Generate unique numeric ID
- Encode ID using Base62 (`[0-9][a-z][A-Z]`)
- Resulting URL length ≈ 6–8 characters

**Pros:**
- Simple
- No collisions

**Cons:**
- Predictable IDs

---

### Option 2: Hashing
- Hash long URL (e.g., MD5/SHA)
- Take first N characters

**Pros:**
- Deterministic

**Cons:**
- Collision handling required

---

## 9. Base62 Encoding Example
ID = 125
Base62 → cb

Using Base62 keeps URLs short and readable.

---

## 10. Caching Strategy
Redirect Request
→ Cache Lookup
├── Hit → Redirect immediately
└── Miss → Query Database → Update Cache → Redirect

- Cache hot URLs
- Use TTL for eviction
- Cache significantly reduces database load

---

## 11. Database Scaling

- Use a **key-value store** (short_url → long_url)
- Replicate database for high availability
- Shard by short_url if dataset grows large

---

## 12. Handling Collisions

If collisions occur:
- Check database before inserting
- Retry with a new key
- Maintain uniqueness constraint on `short_url`

---

## 13. Analytics (Optional)

Track:
- Click count
- Timestamp
- Referrer
- Geographic data

Analytics should be processed **asynchronously** using message queues.

---

## 14. Security Considerations

- Rate limit URL creation
- Validate URLs to prevent abuse
- Protect against phishing and malicious redirects

---

## 15. Bottlenecks & Trade-offs

| Component | Issue | Mitigation |
|---|---|---|
| Database | High read load | Caching |
| ID generation | Collision risk | Base62 + uniqueness check |
| Cache | Single point of failure | Replicated cache |
| Analytics | Latency | Async processing |

---

## 16. Key Takeaways

- URL shorteners are read-heavy systems
- Caching is critical for performance
- Base62 encoding is a common ID strategy
- 302 redirects allow better analytics
- Simple design scales well with the right abstractions

---

### ✅ Status
This system design demonstrates core principles of scalability, caching,
and distributed system trade-offs.

