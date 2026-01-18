
# 01 – System Design Fundamentals

This chapter introduces the core building blocks of scalable system design, starting
from a single-server setup and evolving into distributed, highly available systems.

---

## 1. Single Server Architecture

### Request Flow
A basic system starts with a single server handling all requests.

**Flow:**
- User (Browser / Mobile App)
  → DNS  
  → Web Server  
  → Database  
  → Response (HTML / JSON)


- The client sends a domain name to the DNS (Domain Name System).
- DNS returns the server’s IP address.
- The client sends an HTTP request to the web server.
- The server processes the request and returns a response.

### Traffic Definition
Traffic refers to the volume of requests and responses exchanged between clients
and servers.

**Sources of traffic:**
- Web applications (browser-based)
- Mobile applications (HTTP/HTTPS)

---

## 2. Databases

### Relational Databases (RDBMS)
- Data stored in tables (rows and columns)
- Tables are related using relationships
- Supports JOIN operations
- Strong consistency guarantees

**Examples:**
- MySQL
- PostgreSQL
- Oracle
- SQL Server

### Non-Relational Databases (NoSQL)
- Data stored as:
  - Key-value pairs
  - Documents
  - Graphs
- No JOIN operations
- Optimized for scale and low latency

**Use NoSQL when:**
- Data is unstructured
- Massive scale is required
- Very low latency is needed

---

## 3. Scaling Strategies

### Vertical Scaling
Increase CPU or memory on a single server.

**Pros:**
- Simple
- Works well for low traffic

**Limitations:**
- Hardware limits
- Single point of failure
- Poor performance under heavy load

### Horizontal Scaling
Add more servers to the system.

**Benefits:**
- Higher availability
- Better fault tolerance
- Scales almost indefinitely

---

## 4. Load Balancer

A load balancer distributes incoming traffic across multiple servers.

**Benefits:**
- Prevents server overload
- Enables failover
- Improves availability

**Key behavior:**
- If one server fails, traffic is routed to healthy servers
- Traffic is evenly distributed

> Load balancers are ineffective in vertical scaling because there is only one server.

---

## 5. Database Replication

### Master–Slave Architecture
- Master handles all write operations
- Slaves replicate data and handle read operations

**Advantages:**
- Better read performance
- High availability
- Disaster recovery

**Failure handling:**
- Slave failure → reads go to master
- Master failure → slave is promoted

**Drawbacks:**
- Replication lag
- Temporary write unavailability during failover

---

## 6. Caching

### What is a Cache?
A cache stores frequently accessed or expensive data in memory to reduce latency.

### Cache Flow
1. Server checks cache
2. Cache hit → return data
3. Cache miss → query database
4. Store result in cache
5. Return response

### Cache Considerations
- Cache expiration
- Consistency
- Eviction policies
- Avoid single points of failure

---

## 7. Content Delivery Network (CDN)

A CDN is a network of geographically distributed servers that deliver static content.

### How it works
- User requests static content
- Nearest CDN server responds
- If content is missing, CDN fetches from origin and caches it

### CDN vs Cache

| CDN | Cache |
|----|------|
| Global | Local |
| Static content | Any data |
| Edge servers | Internal infrastructure |

### Considerations
- Cost
- Cache expiry strategy

---

## 8. Stateless Web Tier

### Stateful Architecture
- Session stored on the server
- Requires sticky sessions
- Server failure causes session loss

### Stateless Architecture
- No session data stored on servers
- Session stored in shared storage (SQL / NoSQL)
- Any server can handle any request

**Benefits:**
- Easier to scale
- More resilient
- Simplifies load balancing

---

## 9. Data Centers

### Multi-Data-Center Design
- Traffic distributed across multiple geographic regions
- Each data center has its own servers, databases, and caches

**Benefits:**
- Disaster recovery
- Lower latency
- High availability

**Challenges:**
- Traffic routing
- Data consistency
- Deployment complexity

---

## 10. Message Queues

A message queue enables asynchronous communication between services.

### Components
- Producer
- Queue
- Consumer

### Benefits
- Decouples services
- Improves scalability
- Increases reliability

---

## 11. Observability and Automation

### Logging
- Detect errors
- Trace request flows
- Debug failures

### Metrics
- Latency
- Throughput (QPS)
- Availability

### Automation
- Required at scale
- Improves system reliability
- Enables self-healing systems

---

## 12. Sharding (Horizontal Database Scaling)

### What is Sharding?
Splitting a large database into smaller partitions called shards.

### Shard Key Example
user_id % 4 → shard index

### Sharding Challenges
- Hot keys (celebrity problem)
- Resharding complexity
- Difficult JOIN operations

> Consistent hashing is commonly used to solve these issues.

---

## 13. Key Takeaways

- Start simple, then scale horizontally
- Keep the web tier stateless
- Cache aggressively
- Use load balancers and CDNs
- Design for failure
- Monitor and automate everything
