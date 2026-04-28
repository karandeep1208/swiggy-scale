# Redesigned Architecture - SwiftEats

## Current Architecture (Monolith)

Users → Node.js → PostgreSQL

⚠️ DB pool exhausts at 400 RPS  
⚠️ No scaling  
⚠️ No caching  
⚠️ Sync payments block DB  

---

## New Architecture

Users
 │
 ▼
CloudFront CDN
 - Caches images, menus (TTL 5 min)
 │
 ▼
Application Load Balancer
 - SSL termination
 - Rate limit: 100 req/IP
 │
 ├── Node.js (Auto-scale 4–20)
 │      │
 │      ├── Redis Cluster
 │      │    - Menu cache (TTL 5m)
 │      │    - Promo lock (SETNX)
 │      │
 │      ├── PostgreSQL Primary (via PgBouncer)
 │      │    └── Read Replicas ×2
 │      │
 │      └── SQS Payment Queue
 │
 ▼
Payment Worker (ECS)
 - Reads SQS
 - Calls payment gateway
 - Updates DB
