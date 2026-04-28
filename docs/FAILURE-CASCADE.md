# Failure Cascade Analysis - SwiftEats

## Section 1: Traffic Simulation

Total users notified = 180,000,000  
Click rate = 8% → 14,400,000 users  

Assume 10M users active in first 60 seconds  

Each user makes 3 API calls:
- GET /restaurants
- GET /restaurant/:id
- POST /orders  

Peak RPS = (10,000,000 × 3) / 60  
= 500,000 RPS  

System capacity = ~12,000 RPS  
Demand = 500,000 RPS → **41× overload**

---

## Section 2: Capacity Numbers

PostgreSQL:
- max_connections = 100  

Node.js:
- Max ~12,000 RPS  
- Heap limit ~4GB → OOM at ~15K queued requests  

Payment calls:
- 200ms – 2000ms (avg ~800ms)  

### DB Pool Exhaustion Math

Assume:
- 70% normal queries (20ms)
- 30% payment queries (800ms)

Connections used =
(0.7 × RPS × 0.02) + (0.3 × RPS × 0.8)

At 300 RPS:
= (0.7×300×0.02) + (0.3×300×0.8)
= 4.2 + 72
= 76 connections

At ~400 RPS:
≈ 100 connections → **POOL EXHAUSTED**

---

## Section 3: Failure Cascade

### Failure 1: PostgreSQL Connection Exhaustion (CRITICAL)
- Trigger: ~400 RPS
- Symptom: DB rejects connections
- Effect: All requests fail → Node queues explode

---

### Failure 2: Node.js Event Loop Saturation (CRITICAL)
- Trigger: ~12,000 RPS
- Symptom: response time 50ms → 5s+
- Effect: memory fills → OOM crash

---

### Failure 3: Synchronous Payment Amplification (HIGH)
- Trigger: concurrent with Failure 1
- Symptom: slow payments
- Effect: DB connections held longer → faster exhaustion

---

### Failure 4: Promo Code Race Condition (HIGH)
- Trigger: high concurrency
- Symptom: oversold promos
- Effect: financial loss + inconsistent state

---

### Failure 5: No CDN (NIC Saturation) (CRITICAL)
- Trigger: image traffic spike
- 10M users × 20 images × 200KB = 40TB/min
- Effect: network saturated → API unreachable

---

## Section 4: Timeline

T+0s: Notification sent  
T+3s: DB pool exhausted  
T+5s: Response time spikes  
T+8s: Requests failing  
T+10s: Payment timeouts  
T+12s: Promo oversold  
T+15s: NIC saturated  
T+18s: Node crash (OOM)  
T+20s: System down  
T+45m: Root cause identified  
T+2h: System restored  
