# 06 – Rate Limiter

This chapter explains why rate limiting is essential in system design, the common
algorithms used to implement rate limiters, and how rate limiters are designed
in large-scale distributed systems.

---

## 1. What Is a Rate Limiter?

A **rate limiter** controls how many requests a client can make to a system
within a given time window.

Rate limiters are used to:
- Protect backend services from overload
- Prevent abuse and denial-of-service attacks
- Ensure fair usage among clients
- Improve system stability

---

## 2. Where Rate Limiters Are Used

Rate limiters are commonly placed:
- At the API gateway
- Behind a load balancer
- On individual services
- At the client level

---

## 3. Basic Rate Limiting Flow
Client Request
→ Rate Limiter
├── Allowed → Forward Request to Service
└── Rejected → Return HTTP 429 (Too Many Requests)

---

## 4. Rate Limiting Requirements

### Functional Requirements
- Limit number of requests per user or IP
- Allow burst traffic up to a threshold
- Return clear error responses when exceeded

### Non-Functional Requirements
- Low latency
- High availability
- Accuracy under concurrency
- Scalability

---

## 5. Rate Limiting Algorithms

### 5.1 Token Bucket

- Tokens are added to a bucket at a fixed rate
- Each request consumes one token
- Requests are rejected when tokens run out

**Pros:**
- Allows short bursts
- Simple to implement

**Cons:**
- Requires shared state in distributed systems

---

### 5.2 Leaky Bucket

- Requests are processed at a fixed rate
- Excess requests are queued or dropped

**Pros:**
- Smooth request rate

**Cons:**
- Does not handle bursts well

---

### 5.3 Fixed Window Counter

- Count requests in a fixed time window
- Reset counter at window boundary

**Pros:**
- Easy to implement

**Cons:**
- Traffic spikes at window boundaries

---

### 5.4 Sliding Window Log

- Store timestamps of requests
- Reject requests exceeding the limit

**Pros:**
- Accurate rate limiting

**Cons:**
- High memory usage

---

### 5.5 Sliding Window Counter

- Hybrid of fixed window and sliding window
- Uses weighted counts from adjacent windows

**Pros:**
- Balanced accuracy and efficiency

**Cons:**
- Slightly more complex

---

## 6. Distributed Rate Limiting

In distributed systems:
- Requests hit multiple servers
- Rate limiter state must be shared

### Common Approaches
- Centralized datastore (e.g., Redis)
- In-memory + synchronization
- Token distribution via gateway

---

## 7. Redis-Based Rate Limiter

Redis is commonly used because:
- Low latency
- Atomic operations
- Supports expiration

### Example Flow
Client
→ API Gateway
→ Rate Limiter (Redis)
→ Backend Service

Atomic operations prevent race conditions under high concurrency.

---

## 8. Handling Rate Limit Exceeded

When the limit is exceeded:
- Return HTTP status code **429**
- Include retry information in headers

**Common headers:**
- `Retry-After`
- `X-RateLimit-Remaining`
- `X-RateLimit-Reset`

---

## 9. Rate Limiting Dimensions

Rate limits can be applied based on:
- IP address
- User ID
- API key
- Endpoint
- Geographic region

---

## 10. Challenges in Rate Limiting

- Clock synchronization issues
- Race conditions
- Hot keys
- High memory usage
- Accurate counting at scale

---

## 11. Best Practices

- Apply rate limiting as close to clients as possible
- Use Redis or similar for distributed limits
- Allow reasonable burst traffic
- Monitor rejection rates
- Provide clear error messages

---

## 12. Key Takeaways

- Rate limiting protects systems from abuse and overload
- Different algorithms fit different use cases
- Distributed rate limiting requires shared state
- Redis is a common production solution
- Clear communication improves developer experience

---

### ✅ Status
This chapter provides a complete foundation for understanding and designing
rate limiters in scalable systems.
