# 04 – Databases & Replication

This chapter focuses on how databases are used in system design, the trade-offs
between different database types, and how replication improves performance,
availability, and reliability.

---

## 1. Role of Databases in System Design

Databases are responsible for:
- Persisting application data
- Supporting read and write operations
- Maintaining data integrity and consistency

As systems scale, database design often becomes the **primary bottleneck**, making
replication and partitioning essential.

---

## 2. Relational Databases (RDBMS)

Relational databases store data in **tables with rows and columns**, where tables
are connected through defined relationships.

### Characteristics
- Fixed schema
- ACID guarantees
- Supports JOIN operations
- Strong consistency

### Common Use Cases
- Financial systems
- Transaction-heavy applications
- Systems requiring strict consistency

### Examples
- MySQL
- PostgreSQL
- Oracle
- SQL Server

---

## 3. Non-Relational Databases (NoSQL)

Non-relational databases store data in flexible formats such as:
- Key–value pairs
- Documents
- Wide-column stores
- Graphs

### Characteristics
- Schema flexibility
- Horizontal scalability
- High availability
- Low latency

### Limitations
- No JOIN operations
- Eventual consistency in many systems

### Common Use Cases
- Massive-scale systems
- Unstructured or semi-structured data
- Real-time applications

---

## 4. Why Database Replication Is Needed

As traffic increases, a single database cannot efficiently handle:
- High read volume
- High availability requirements
- Disaster recovery needs

Replication addresses these challenges by copying data across multiple databases.

---

## 5. Master–Slave Replication Architecture

In a master–slave setup:
- The **master** handles all write operations
- **Slaves (replicas)** copy data from the master
- Slaves serve read requests

### Read / Write Flow
Web Server
├── WRITE → Master Database
└── READ → Replica (Slave) Databases

---

## 6. Advantages of Database Replication

- Improved read performance
- Higher availability
- Fault tolerance
- Disaster recovery
- Scalability for read-heavy workloads

---

## 7. Replication Challenges

### Replication Lag
- Slaves may not reflect the latest data immediately
- Can lead to stale reads

### Write Availability
- If the master fails, writes stop temporarily
- Requires failover mechanisms

### Data Consistency
- Reads from replicas may return outdated data
- Applications must handle eventual consistency

---

## 8. Failover and Recovery

### Slave Failure
- Reads are redirected to the master temporarily
- Failed slave can be rebuilt and rejoined

### Master Failure
- A slave is promoted to become the new master
- Other slaves replicate from the new master

Failover systems must be **automated** to reduce downtime.

---

## 9. Scaling Reads vs Writes

### Scaling Reads
- Add more replicas
- Distribute read traffic
- Relatively easy

### Scaling Writes
- Much harder
- Often requires:
  - Sharding
  - Partitioning
  - Application-level coordination

---

## 10. Database Tier Separation

As systems grow:
- Databases should not live on the same server as web applications
- Databases require independent scaling and failure handling

This separation improves:
- Reliability
- Performance
- Security

---

## 11. Replication Across Data Centers

To handle regional outages:
- Replicate data across multiple data centers
- Avoid placing all replicas in one location

### Benefits
- Disaster resilience
- Higher availability

### Trade-offs
- Increased replication latency
- More complex consistency handling

---

## 12. When to Use SQL vs NoSQL

### Choose SQL When:
- Strong consistency is required
- Data relationships are complex
- Transactions are critical

### Choose NoSQL When:
- Scalability is the top priority
- Data is unstructured
- Low latency is required at massive scale

---

## 13. Best Practices

- Separate read and write workloads
- Monitor replication lag
- Automate failover
- Avoid single points of failure
- Choose consistency models deliberately

---

## 14. Key Takeaways

- Databases are central to system performance
- Replication improves availability and read scalability
- Writes are harder to scale than reads
- Replication introduces consistency challenges
- Good database design is critical to scalable systems

---

### ✅ Status
This chapter establishes the foundation for understanding how databases scale
and how replication enables high availability.
