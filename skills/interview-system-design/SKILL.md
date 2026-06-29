---
name: interview-system-design
description: Structure and deliver a system design interview answer. Use when preparing for or doing a system design interview, asked to design a large-scale system, or exploring architecture trade-offs. Triggers on "system design", "design a system", "design X at scale", "architecture interview", "how would you design", "scalability question".
disable-model-invocation: true
---

# System Design Interview

Deliver a structured, confident system design answer. The goal: show you can reason about trade-offs, not recite a memorized architecture.

## Arguments

`$ARGUMENTS` — the system to design (e.g., "design Twitter", "design a rate limiter", "design a URL shortener").

---

## The Framework (follow this order)

### 1. Clarify Requirements (2–3 min)

Before drawing anything, ask:

**Functional** (what it must do):
- What are the core user actions? (read, write, search, stream?)
- What is NOT in scope? (clarify explicitly)

**Non-functional** (how well it must do it):
- Scale: How many users? DAU? QPS? Read-heavy or write-heavy?
- Latency: What's acceptable p99? (real-time? eventual consistency OK?)
- Availability: 99.9%? 99.99%? (affects redundancy cost)
- Durability: Can we lose data? (affects storage choices)
- Consistency: Strong vs. eventual? (affects architecture significantly)

**State your assumptions** if the interviewer doesn't specify.

---

### 2. Capacity Estimation (2–3 min)

Rough numbers — order of magnitude is what matters:

```
DAU: X million users
Writes/sec: X (peak = 2–3x avg)
Reads/sec: X (often 10–100x writes for social apps)
Storage: X GB/day × 365 days × 5 years = Y TB
Bandwidth: X MB/request × Y req/sec = Z Gbps
```

Use these to drive decisions (do we need caching? sharding? CDN?).

---

### 3. High-Level Design (5–8 min)

Draw the happy path for the 2–3 most important flows:

```
Client → Load Balancer → API Servers → [Cache] → Database
                                    → [Queue] → Workers → Storage
```

Name concrete components:
- **API layer**: REST vs. GraphQL vs. gRPC (state your choice + why)
- **Database**: SQL (strong consistency, relations) vs. NoSQL (scale, flexibility) vs. both
- **Cache**: Redis/Memcached — what do you cache, what's the eviction policy?
- **Queue**: Kafka/SQS — for async workloads, decoupling, retries
- **CDN**: for static assets, media, geographic distribution
- **Storage**: S3/GCS for blobs, RDBMS for structured, search engine for full-text

---

### 4. Deep Dive — Pick 2–3 Hard Parts (10–15 min)

Pick the most interesting/difficult components and go deep. Common areas:

#### Data Model
```sql
-- Show a schema, even rough
users: id, username, email, created_at
posts: id, user_id, content, created_at
follows: follower_id, followee_id
```

#### API Design
```
POST /api/v1/posts        { content }
GET  /api/v1/feed         ?cursor=&limit=20
GET  /api/v1/users/:id/posts
```

#### Scalability Bottlenecks
- **Hot partitions**: celebrity problem in social graphs → fan-out on write vs. read
- **Database scaling**: read replicas → sharding (by user_id? consistent hash?) → CQRS
- **Cache invalidation**: write-through vs. write-around vs. write-behind
- **Rate limiting**: token bucket vs. leaky bucket, where to enforce (API gateway)

#### Failure Modes
- What happens if a service goes down? (circuit breaker, retry with backoff)
- What if the DB is overloaded? (connection pooling, read replicas, queue)
- What if the cache is cold? (thundering herd → mutex/semaphore, probabilistic early refresh)

---

### 5. Trade-offs Summary (2 min)

Always close with trade-offs — this is what separates senior candidates:

```
Decision: [chose X over Y]
Why: [specific reason — latency, cost, consistency, operational simplicity]
Cost: [what you give up]
```

Examples:
- "Chose eventual consistency → lower latency, but users may see stale feed for ~1s"
- "Chose SQL → ACID guarantees matter for payments, but horizontal scaling is harder"
- "Chose fan-out on write → faster reads, but expensive for users with 10M followers"

---

## Quick Reference: Common Design Patterns

| Problem | Solution |
|---------|----------|
| Read-heavy, cacheable | Redis + CDN, read replicas |
| Write-heavy, async OK | Kafka/SQS + worker pool |
| Global low latency | CDN, geo-distributed DB (CockroachDB, Spanner) |
| Large files/media | S3 + presigned URLs, chunked upload |
| Full-text search | Elasticsearch / OpenSearch |
| Rate limiting | Token bucket in Redis (INCR + EXPIRE) |
| Unique IDs at scale | Snowflake IDs, ULIDs |
| Leaderboard | Redis Sorted Sets (ZADD/ZRANK) |
| Session storage | Redis with TTL |
| Pub/sub notifications | WebSockets + Redis pub/sub or SSE |

## Numbers Every Engineer Should Know

| Operation | Latency |
|-----------|---------|
| L1 cache | 1 ns |
| RAM | 100 ns |
| SSD random read | 100 µs |
| Network round-trip (same DC) | 500 µs |
| HDD seek | 10 ms |
| Network round-trip (cross-region) | 150 ms |

Storage: 1 char = 1 byte; 1M rows × 1KB/row = 1 GB; 1B users = scale carefully.
