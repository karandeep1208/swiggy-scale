# AWS Cost Estimate

## Baseline (Monthly)

EC2 t3.medium ×4  
$0.0416 × 4 × 720 = $119  

RDS db.r6g.large  
$0.182 × 720 = $131  

Read replicas ×2  
$0.182 × 2 × 720 = $262  

Redis ×3  
$0.166 × 3 × 720 = $358  

ALB  
≈ $56  

CloudFront (10TB)  
$0.0085 × 10,000GB = $85  

SQS  
$12  

### Total ≈ $1,023/month

---

## Peak Event (4 hours)

EC2 t3.2xlarge ×20  
$0.3328 × 20 × 4 = $26  

RDS upgrade  
$1.027 × 4 = $4  

CloudFront surge (50TB)  
$0.0085 × 50,000 = $425  

### Peak Cost ≈ $455

---

## Business Justification

Outage loss = ₹4.2 crore/min  
45 min outage = ₹189 crore  

Infra cost (~₹1 lakh) is negligible vs loss → justified
