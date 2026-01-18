# 05 – Sharding & Consistent Hashing

This chapter explains how databases scale horizontally using sharding, the
challenges that arise from data partitioning, and how consistent hashing solves
many of these problems in distributed systems.

---

## 1. Why Sharding Is Needed

As data volume grows, a single database server eventually becomes a bottleneck due to:
- Storage limits
- High read and write load
- Performance degradation

Sharding solves this by splitting data across multiple databases.

---

## 2. What Is Sharding?

**Sharding** is the process of dividing a large database into smaller,
independent partitions called **shards**.

- Each shard contains a subset of the data
- All shards share the same schema
- Data is distributed based on a shard key

---

## 3. Shard Key

A **shard key** determines how data is distributed across shards.

### Example
user_id % 4 → shard index

- If `user_id % 4 = 0` → Shard 0
- If `user_id % 4 = 1` → Shard 1
- And so on

Choosing the right shard key is critical for performance and scalability.

---

## 4. Benefits of Sharding

- Horizontal scalability
- Reduced load per database
- Improved performance
- Higher availability when combined with replication

---

## 5. Challenges of Sharding

### 5.1 Uneven Data Distribution (Hot Shards)

Some shards may receive significantly more traffic than others.

**Example:**
- A celebrity user generates massive traffic
- Their shard becomes overloaded

This is known as the **celebrity problem**.

---

### 5.2 Resharding

Resharding is required when:
1. A shard runs out of storage
2. Traffic distribution becomes uneven
3. New shards are added

Resharding involves:
- Changing the sharding function
- Moving data across shards
- Updating routing logic

This process is complex and expensive.

---

### 5.3 Cross-Shard Queries

- JOIN operations across shards are difficult
- Queries may require data from multiple shards
- Application-level joins are often required

---

## 6. Hash-Based Sharding

Hash-based sharding uses a hash function to distribute data.

### Example
hash(key) % N

Where:
- `key` is the shard key
- `N` is the number of shards

### Problem with Simple Hashing
When `N` changes:
- Almost all keys are remapped
- Massive data movement is required

This is inefficient and disruptive.

---

## 7. Consistent Hashing

**Consistent hashing** minimizes data movement when nodes are added or removed.

### Key Idea
- Keys and servers are placed on a logical hash ring
- Each key is assigned to the next server clockwise on the ring

When a server is added or removed:
- Only a small fraction of keys are remapped

---

## 8. Benefits of Consistent Hashing

- Minimizes data movement
- Simplifies scaling
- Improves fault tolerance
- Works well for distributed systems

Only **k / n** keys need to be remapped, where:
- `k` = number of keys
- `n` = number of servers

---

## 9. Where Consistent Hashing Is Used

Consistent hashing is widely used in:
- Load balancers
- Distributed caches
- Databases
- CDNs

---

## 10. Virtual Nodes (VNodes)

To improve load distribution:
- Each physical server is assigned multiple virtual nodes
- Virtual nodes are spread evenly on the hash ring

### Benefits
- Better load balancing
- Reduced impact when nodes join or leave
- More uniform data distribution

---

## 11. Sharding + Replication

Sharding is often combined with replication:
- Each shard has replicas
- Replicas provide fault tolerance
- Reads can be distributed across replicas

This provides both **scalability** and **high availability**.

---

## 12. Best Practices

- Choose shard keys carefully
- Avoid hot keys
- Monitor shard size and traffic
- Use consistent hashing to simplify scaling
- Combine sharding with replication

---

## 13. Key Takeaways

- Sharding enables horizontal database scaling
- Poor shard key selection causes performance issues
- Resharding is complex and costly
- Consistent hashing minimizes data movement
- Most large-scale systems rely on consistent hashing

---

### ✅ Status
This chapter completes the foundation for understanding how data is partitioned
and scaled in distributed systems.
