# Autocomplete System Design

This document describes the design of a scalable autocomplete (typeahead) system
similar to Google search suggestions, focusing on correctness, ranking, low
latency, and scalability.

---

## 1. Problem Statement

Design an autocomplete system that:
- Suggests queries as the user types
- Returns top suggestions quickly (low latency)
- Ranks suggestions by popularity/frequency
- Handles large-scale traffic and datasets
- Updates suggestions as new query trends emerge

---

## 2. Requirements

### Functional Requirements
- Given a prefix, return top `k` suggestions
- Suggestions should be ranked (e.g., most frequent first)
- Support updates (new queries, changing popularity)
- Support multiple languages/locales (optional)

### Non-Functional Requirements
- Very low latency (often < 100 ms)
- High availability
- Scalability (read-heavy)
- Efficient storage
- Observability (metrics/logs)

---

## 3. High-Level Architecture
Client
→ Load Balancer
→ Autocomplete Service
→ Cache
→ Suggestion Store (Trie / Index)

Offline/async pipeline:
Query Logs
→ Aggregation
→ Build/Update Index (Trie)
→ Deploy to Serving Layer

---

## 4. API Design

### Get Suggestions
GET /api/suggest?prefix=<text>&k=10
Response: { suggestions: [ ... ] }

---

## 5. Core Data Structure: Trie

A **Trie (prefix tree)** is commonly used for autocomplete because it supports:
- Fast prefix lookup
- Efficient storage of shared prefixes

### Trie Concept (Simplified)
Prefix: "ca"
Trie paths: c → a → ...

Each node can store:
- Child links
- End-of-word marker
- Popularity score / frequency
- Top `k` suggestions for that prefix (optional optimization)

---

## 6. Basic Serving Flow
User Types Prefix
→ Autocomplete Service
→ Cache Lookup
├── Hit → Return Suggestions
└── Miss → Trie Lookup → Update Cache → Return Suggestions

Caching is critical because many prefixes repeat (e.g., "a", "th", "how").

---

## 7. Ranking Suggestions

Suggestions are typically ranked by:
- Frequency (how often a query is searched)
- Recency (trending queries)
- Personalization (optional)
- Location/language (optional)

### Simple Ranking Approach
- Store frequency counts per query
- Return top `k` highest-frequency matches for a prefix

---

## 8. Building the Suggestion Index (Offline Pipeline)

Autocomplete relies on an offline pipeline that processes query logs.

### Pipeline Flow
Search Query Logs
→ Clean/Normalize (lowercase, trim, dedupe)
→ Count Frequencies
→ Generate Top Queries per Prefix
→ Build Trie / Index
→ Deploy to Serving Nodes

This keeps the serving layer fast and lightweight.

---

## 9. Updating the Index

Index update strategies:
- **Batch updates** (e.g., daily/hourly)
- **Near real-time updates** (streaming) for trending queries

Trade-off:
- Batch is simpler
- Real-time captures trends faster but is more complex

---

## 10. Scaling the Serving Layer

### 10.1 Stateless Service
The Autocomplete Service should be stateless and horizontally scalable behind a load balancer.

### 10.2 Partitioning (Sharding)
When the trie/index is too large for a single machine:
- Partition by prefix range (e.g., a–f, g–m, n–z)
- Or partition by hashing prefixes

---

## 11. Trie Sharding Example
Router
├── Shard 1: a–f
├── Shard 2: g–m
└── Shard 3: n–z

Or:
shard_id = hash(prefix) % N

---

## 12. Caching Strategy

Cache at multiple layers:
- Client-side cache (optional)
- CDN for API responses (careful with personalization)
- Server-side cache (Redis/Memcached)

### What to Cache
- Top suggestions for popular prefixes (e.g., "a", "th", "how")
- Results for recently requested prefixes

Use TTL to prevent stale suggestions.

---

## 13. Storage Considerations

Autocomplete index can store:
- Trie nodes
- Frequencies
- Precomputed top `k` results per node (faster reads, more storage)

### Common Approach
Store at each trie node:
- The top `k` suggestions for that prefix

This makes lookup O(length(prefix)).

---

## 14. Handling Large Datasets

Techniques:
- Compression of trie nodes
- Store only top `k` suggestions per node
- Limit maximum prefix depth
- Use disk-based indexes + memory caching (hybrid)

---

## 15. Observability & Metrics

Monitor:
- Latency (p50/p95/p99)
- Cache hit rate
- QPS
- Error rate
- Index build failures
- Shard hot spots

---

## 16. Bottlenecks & Trade-offs

| Component | Issue | Mitigation |
|---|---|---|
| Trie lookup | Large memory usage | Compress trie / shard |
| Cache | Stale results | TTL + periodic refresh |
| Hot prefixes | Overload | Cache + shard balancing |
| Ranking | Complex signals | Start with frequency + recency |

---

## 17. Key Takeaways

- Autocomplete is a read-heavy, latency-sensitive system
- Tries enable fast prefix search
- Caching is essential for hot prefixes
- Offline pipelines build and refresh indexes
- Sharding is needed when the index grows large
- Ranking typically starts with frequency, then adds more signals

---

### ✅ Status
This system design provides a strong blueprint for building a scalable
autocomplete system with fast lookups, ranking, and reliable updates.

## Interview Follow-Up Questions

1. How would you personalize autocomplete suggestions per user?
2. How do you handle trending or breaking queries?
3. How would you update the index in near real-time?
4. How would you support multiple languages?
5. How would you reduce memory usage of the trie?
6. How do you handle extremely hot prefixes?
7. How would you shard the system globally?
8. How do you evaluate suggestion quality?

