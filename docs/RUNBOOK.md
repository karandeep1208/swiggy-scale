# Incident Runbook - SwiftEats

## STEP 1: DETECT

- ALB 5xx > 5% → CRITICAL  
- DB connections > 80% → WARNING  
- CPU > 80% → SCALE  
- Redis memory > 75% → WARNING  
- SQS queue > 10K → ALERT  
- P99 latency > 2s → ALERT  

---

## STEP 2: TRIAGE

1. Check DB connections  
   → High → DB issue  

2. Check CPU  
   → High → scaling issue  

3. Check Redis misses  
   → High → cache issue  

4. Check SQS queue  
   → High → worker issue  

---

## STEP 3: RESPOND

### DB Exhaustion
- Scale RDS or increase pool  
- Restart PgBouncer  

### Compute Saturation
- Increase EC2 instances  

### Cache Miss Spike
- Warm cache  
- Check TTL  

### Payment Queue Backlog
- Scale workers  
- Check payment API  

---

## STEP 4: ROLLBACK

Criteria:
- 5xx > 20% for 5 min  

Command:
aws ecs update-service \
--cluster prod \
--service api \
--task-definition previous  

---

## STEP 5: POSTMORTEM

Include:
- Timeline  
- Root cause  
- Impact  
- Fixes  
- Action items (owner + date)  
