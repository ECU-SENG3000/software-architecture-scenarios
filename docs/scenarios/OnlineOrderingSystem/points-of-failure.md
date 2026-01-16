# 🌐 Distributed Application Scenario

**"Order Processing Pipeline with Queue Backed Microservices"**

A retail platform processes customer orders using a distributed, event-driven architecture:

1. **Frontend Web App** → sends order to
2. **API Gateway** → forwards to
3. **Order Service** → writes order to
4. **Message Queue** (e.g., Azure Queue, SQS, RabbitMQ) → consumed by
5. **Payment Service** → calls
6. **External Payment Provider** → returns result to
7. **Order Service** → updates
8. **Database** → emits
9. **Order Events** → consumed by
10. **Notification Service** (email/SMS)
11. **Analytics Service**
12. **Inventory Service**

Each component is independently deployed, scaled, and versioned.


---

## 💥 All Possible Failure Points

Organized by layer so you can drop this directly into a lecture or architecture review.

### 🧩 1. Frontend → API Gateway

- Network latency or packet loss
- TLS handshake failures
- DNS resolution failures
- API Gateway throttling
- CORS misconfiguration
- Stale client-side caches
- Browser timeouts

### 🚪 2. API Gateway → Order Service

- Gateway routing misconfiguration
- JWT token validation failures
- Rate limiting or WAF blocking
- Order Service instance unavailable
- Order Service cold start delays
- Serialization/deserialization errors
- Request body too large
- Timeout between gateway and service

### 📦 3. Order Service → Database

- Connection pool exhaustion
- Deadlocks
- Slow queries causing cascading timeouts
- Transaction conflicts
- Schema drift between services
- Database failover events
- Partial writes (e.g., order saved but audit log not written)
- Lost writes due to optimistic concurrency failures

### 📬 4. Order Service → Message Queue

- Queue unavailable
- Queue throttling
- Message size too large
- Duplicate message enqueue due to retry logic
- Message not acknowledged due to timeout
- Poison messages causing repeated retries
- Incorrect visibility timeout leading to double processing
- Queue depth spikes causing backpressure

### ⚙️ 5. Message Queue → Payment Service

- Consumer crashes mid-processing
- Consumer takes too long → message becomes visible again
- Consumer scaling too aggressively → thundering herd
- Consumer scaling too slowly → backlog grows
- Message lock renewal failures
- Incorrect retry policy (too aggressive or too lenient)
- Messages delivered out of order
- Consumer reading from wrong queue or wrong version of schema

### 💳 6. Payment Service → External Payment Provider

**This is where the nastiest failures happen.**

- Provider downtime
- Provider rate limits
- Provider API version mismatch
- Network timeouts
- Partial failures (charge succeeded but response lost)
- Idempotency key not used → duplicate charges
- Provider returns 200 OK but with error payload
- Provider returns 500 but charge actually succeeded
- Provider latency spikes → cascading timeouts
- TLS certificate rotation issues

### 🔁 7. Retry Logic & Timeouts

**This is where distributed systems get dangerous.**

#### Timeouts

- Too short → false failures
- Too long → thread starvation
- Mismatched timeouts between layers → retry storms
- Timeout + retry → duplicate side effects

#### Retries

- Exponential backoff misconfigured
- No jitter → synchronized retry waves
- Retrying non-idempotent operations
- Retrying after downstream is already saturated

### 🧨 8. "Releasing Too Many Queue Transactions After Downtime"

**This is a classic failure mode.**

#### Scenario

Payment Service is down for 30 minutes. Queue accumulates 50,000 messages. Service comes back online and auto-scales to 50 instances.

#### Failure Points

- Thundering herd: all instances pull messages at once
- Downstream provider overwhelmed → more failures → more retries
- Visibility timeouts expire → messages reappear → double processing
- Duplicate charges if idempotency not enforced
- Database overwhelmed by update events
- Inventory Service receives thousands of events at once
- Notification Service sends duplicate emails
- Analytics Service receives out-of-order events
- Queue auto-scaling triggers cost explosion
- Circuit breakers open → cascading failures across microservices

**This is exactly why distributed systems need:**

- backpressure
- rate limiting
- circuit breakers
- idempotency
- dead letter queues
- message deduplication
- controlled drain modes after downtime

### 🧵 9. Event Bus → Downstream Services

- Event schema mismatch
- Event versioning issues
- Event ordering lost
- Duplicate events
- Event bus outage
- Consumer lag
- Consumer crashes
- Event replay causing duplicate side effects
- Eventual consistency delays confusing users

### 📣 10. Notification Service

- Email/SMS provider downtime
- Provider throttling
- Duplicate notifications
- Wrong template version
- Notification sent before payment confirmed
- Notification lost due to transient network failure

### 📊 11. Analytics Service

- Event ingestion lag
- Out-of-order events
- Duplicate events
- Schema mismatch
- Data lake write failures
- Batch job failures
- Partial ingestion leading to incorrect dashboards

### 📦 12. Inventory Service

- Race conditions updating stock
- Double decrements due to duplicate messages
- Stock not updated due to consumer crash
- Event replay causing negative inventory
- Database deadlocks
- Cache invalidation failures

### 🧰 13. Infrastructure & Platform Failures

- Container orchestrator (Kubernetes) node failures
- Pod evictions
- Misconfigured liveness/readiness probes
- Autoscaler misfires
- Network partition between services
- DNS propagation delays
- Certificate expiration
- Cloud region outage
- Misconfigured secrets or key rotation

---

## 🧠 Summary Table

| Layer | Failure Types |
|-------|---------------|
| Frontend | Network, browser, CORS, timeouts |
| API Gateway | Routing, auth, throttling |
| Order Service | DB issues, serialization, timeouts |
| Queue | Visibility, duplicates, poison messages |
| Payment Service | External API failures, idempotency |
| Retry Logic | Storms, thundering herd, duplicates |
| Event Bus | Ordering, schema, lag |
| Notification | Provider failures, duplicates |
| Inventory | Race conditions, double decrements |
| Infra | DNS, certs, autoscaling, partitions |
