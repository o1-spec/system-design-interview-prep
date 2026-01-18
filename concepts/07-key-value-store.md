# 07 – Distributed Key-Value Store

This chapter explains how distributed key-value stores work, the design
challenges they solve, and the trade-offs involved in building scalable,
fault-tolerant storage systems.

---

## 1. What Is a Key-Value Store?

A **key-value store** is a database that stores data as:
(key → value)

- Keys are unique identifiers
- Values can be strings, objects, or binary data
- Access is typically done using `GET`, `PUT`, and `DELETE`

Key-value stores are widely used due to their simplicity and performance.

---

## 2. Why Use a Distributed Key-Value Store?

As data and traffic grow, a single-node key-value store becomes insufficient due to:
- Storage limits
- Throughput constraints
- Single point of failure

Distributed key-value stores address this by:
- Scaling horizontally
- Replicating data
- Tolerating node failures

---

## 3. Core Requirements

### Functional Requirements
- Store and retrieve data by key
- Support insert, update, and delete operations

### Non-Functional Requirements
- High availability
- Scalability
- Fault tolerance
- Low latency

---

## 4. CAP Theorem

The **CAP theorem** states that a distributed system can guarantee only **two out of three** properties:

- **Consistency (C):** All nodes see the same data at the same time
- **Availability (A):** Every request receives a response
- **Partition Tolerance (P):** System continues to operate despite network partitions

In practice, partition tolerance is mandatory, so systems choose between:
- **CP systems** (Consistency + Partition tolerance)
- **AP systems** (Availability + Partition tolerance)

---

## 5. Data Partitioning

To scale horizontally, data is partitioned across multiple nodes.

### Common Techniques
- Range-based partitioning
- Hash-based partitioning
- Consistent hashing

Consistent hashing minimizes data movement when nodes are added or removed.

---

## 6. Data Replication

Replication improves availability and durability by storing copies of data on
multiple nodes.

### Replication Strategies
- Leader–follower replication
- Multi-leader replication
- Quorum-based replication

Replication introduces consistency challenges that must be handled carefully.

---

## 7. Consistency Models

### Strong Consistency
- Reads always return the most recent write
- Higher latency
- Harder to scale

### Eventual Consistency
- Updates propagate asynchronously
- Reads may return stale data temporarily
- Better availability and scalability

---

## 8. Quorum Consensus

Quorum-based systems ensure consistency by requiring a minimum number of nodes
to agree before an operation succeeds.

### Example
Let:
- `N` = total replicas
- `W` = write quorum
- `R` = read quorum

Consistency is guaranteed if:
R + W > N

---

## 9. Conflict Resolution

In distributed systems, conflicts can occur due to concurrent updates.

### Common Techniques
- Last-write-wins (LWW)
- Versioning
- Vector clocks

Vector clocks help detect and resolve conflicting updates explicitly.

---

## 10. Failure Handling

Distributed key-value stores must handle:
- Node failures
- Network partitions
- Disk failures

### Techniques
- Data replication
- Gossip protocols for node discovery
- Automatic failover and recovery

---

## 11. Popular Distributed Key-Value Stores

Examples include:
- DynamoDB
- Cassandra
- Riak
- Redis (in clustered mode)

These systems prioritize availability and scalability.

---

## 12. Best Practices

- Choose consistency level based on application needs
- Use consistent hashing for partitioning
- Replicate data across multiple nodes
- Monitor latency and error rates
- Design for failure from day one

---

## 13. Key Takeaways

- Key-value stores are simple but powerful
- Distribution enables scalability and fault tolerance
- CAP theorem guides design trade-offs
- Consistency models affect user experience
- Most large-scale systems rely on distributed key-value stores

---

### ✅ Status
This chapter completes the core concepts needed to understand how scalable,
fault-tolerant key-value storage systems are designed.
