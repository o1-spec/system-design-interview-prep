# Web Crawler System Design

This document describes the design of a scalable web crawler similar to those
used by search engines to discover, fetch, and index web content efficiently
and responsibly.

---

## 1. Problem Statement

Design a system that:
- Crawls billions of web pages
- Downloads and parses web content
- Avoids crawling duplicate pages
- Respects politeness and website rules
- Scales horizontally and remains fault tolerant

---

## 2. Requirements

### Functional Requirements
- Discover new URLs
- Download HTML content
- Parse and extract links
- Store crawled content
- Avoid duplicate crawling

### Non-Functional Requirements
- High scalability
- Fault tolerance
- Low latency per fetch
- Politeness and fairness

---

## 3. High-Level Architecture
Seed URLs
→ URL Frontier
→ Downloader
→ Parser
→ Content Store

Supporting components:
- URL deduplication service
- Scheduler
- Metadata store

---

## 4. URL Frontier

The **URL frontier** manages which URLs should be crawled next.

### Responsibilities
- Store pending URLs
- Prioritize URLs
- Enforce politeness policies
- Avoid duplicates

### Queue Strategy
- Breadth-first search (BFS)
- Priority-based queues

---

## 5. Politeness & Robots.txt

### Politeness
- Limit request rate per host
- Avoid overwhelming servers

### robots.txt
- Defines which paths can be crawled
- Crawlers must fetch and respect `robots.txt` rules

---

## 6. URL Deduplication

To avoid crawling the same URL multiple times:
- Normalize URLs
- Use hashing
- Store visited URLs in a set or Bloom filter

Deduplication reduces wasted bandwidth and storage.

---

## 7. Downloader

The **downloader** fetches web pages over HTTP.

### Responsibilities
- Fetch HTML content
- Handle timeouts and retries
- Follow redirects

### Challenges
- Slow or unreachable hosts
- Handling HTTP errors
- Managing large-scale concurrency

---

## 8. HTML Parsing

The **parser**:
- Extracts hyperlinks
- Removes unnecessary content
- Identifies metadata

Extracted URLs are sent back to the URL frontier.

---

## 9. Storage Layer

### Content Store
- Stores raw HTML
- Stores parsed content

### Metadata Store
- URL status
- Crawl timestamps
- HTTP response codes

Storage must be scalable and durable.

---

## 10. Handling Spider Traps

**Spider traps** are infinite or near-infinite URL spaces.

### Examples
- Calendar links
- Auto-generated URLs

### Mitigation
- URL depth limits
- URL pattern filtering
- Crawl budget per domain

---

## 11. Scheduling & Prioritization

Crawlers schedule URLs based on:
- Page importance
- Freshness requirements
- Domain priority

This improves crawl efficiency and relevance.

---

## 12. Distributed Crawling

To scale:
- Use multiple crawler workers
- Partition URLs across workers
- Share deduplication state

### Worker Flow
Scheduler
→ Worker Node
→ Downloader
→ Parser
→ Storage

---

## 13. Fault Tolerance

- Retry failed downloads
- Detect and replace failed workers
- Persist crawler state to avoid data loss

---

## 14. Monitoring & Metrics

Track:
- Crawl rate
- Error rate
- Queue size
- Bandwidth usage

Monitoring ensures crawler health and efficiency.

---

## 15. Bottlenecks & Trade-offs

| Area | Challenge | Mitigation |
|---|---|---|
| URL frontier | Huge queue size | Partitioning |
| Deduplication | Memory usage | Bloom filters |
| Downloader | Network latency | Async I/O |
| Storage | Data volume | Compression |

---

## 16. Key Takeaways

- Web crawlers are highly distributed systems
- URL frontier is the heart of the crawler
- Politeness and fairness are critical
- Deduplication saves massive resources
- Monitoring is essential at scale

---

### ✅ Status
This system design demonstrates how large-scale crawling systems operate
efficiently while respecting web standards.

## Interview Follow-Up Questions

1. How do you ensure the crawler does not overload a website?
2. How would you prioritize which pages to crawl first?
3. How would you detect and avoid spider traps?
4. How do you handle duplicate or near-duplicate pages?
5. How would you make the crawler fault tolerant?
6. How would you scale the URL frontier?
7. How would you update crawled pages periodically?
8. How would you store and index crawled content efficiently?
