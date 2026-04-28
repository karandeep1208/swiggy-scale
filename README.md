# Swiggy Scale Simulation

## Scenario
India vs Pakistan World Cup Final.  
10M users hit system in 60 seconds.  
Monolith backend crashes.

---

## Documents

| File | Description |
|-----|------------|
| FAILURE-CASCADE.md | Failure analysis with numbers |
| ARCHITECTURE.md | Scalable system design |
| COST-ESTIMATE.md | AWS cost breakdown |
| RUNBOOK.md | Incident response guide |

---

## Key Findings

- DB crashes at ~400 RPS  
- Node crashes at ~12K RPS  
- Traffic spike = 500K RPS  
- System overloaded by 40×  

---

## Architecture Overview

Multi-tier system using CDN, load balancer, Redis, read replicas, and async queue to handle 10M users.

---

## Tech Stack

Node.js, PostgreSQL, Redis, AWS
