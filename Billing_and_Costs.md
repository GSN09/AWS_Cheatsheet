# AWS Billing & Cost Management — When to Use Guide

## Service Comparison Matrix

| Service | Primary Use Case | Category | Scope | Typical Workloads |
|---------|-----------------|----------|-------|-------------------|
| **AWS Cost Explorer** | Visualize, understand, and forecast AWS spend | Cost Analysis | Account / Organization | Trend analysis, cost breakdowns by service/tag/account, 12-month forecasting |
| **AWS Budgets** | Set spending thresholds and automated alerts/actions | Cost Control | Account / Organization | Monthly spend caps, RI/SP coverage tracking, automated shutdown of over-budget resources |
| **AWS Cost Anomaly Detection** | ML-powered identification of unexpected spend spikes | Cost Monitoring | Account / Organization | Early detection of runaway costs, misconfigured resources, orphaned infrastructure |
| **AWS Cost and Usage Report (CUR)** | Granular, line-item billing data delivered to S3 | Cost Reporting | Account / Organization | Custom dashboards (QuickSight/Athena), chargeback/showback, third-party tooling |
| **AWS Savings Plans** | Flexible commitment-based discounts ($/hr) | Cost Optimization | Account / Organization | Steady-state compute (EC2, Fargate, Lambda), flexible across instance families/regions |
| **Reserved Instances (RI)** | Capacity reservation + discount for specific instance types | Cost Optimization | Account / Organization | Predictable, always-on workloads (RDS, EC2, ElastiCache, Redshift, OpenSearch) |
| **AWS Compute Optimizer** | ML-driven right-sizing recommendations | Resource Optimization | Account / Organization | Over-provisioned EC2, Lambda memory tuning, EBS volume right-sizing, Auto Scaling groups |
| **AWS Billing Conductor** | Customize billing and create custom pricing for accounts | Billing Customization | Organization | Showback/chargeback for business units, margin management, custom rate cards |
| **AWS Pricing Calculator** | Estimate costs for new architectures before deployment | Cost Estimation | N/A (planning tool) | Pre-migration estimates, architecture comparisons, budget proposals |
| **AWS Free Tier** | No-cost usage for learning and experimentation | Cost Avoidance | Account | Dev/test environments, prototypes, learning new services |

## Decision Criteria

| Decision Factor | Recommended Service |
|----------------|-------------------|
| Need to visualize historical spend and forecast future costs | **AWS Cost Explorer** |
| Need alerts when spend exceeds a threshold | **AWS Budgets** |
| Need automatic detection of unexpected cost increases | **AWS Cost Anomaly Detection** |
| Need raw, line-item billing data for custom analytics | **AWS Cost and Usage Report (CUR)** |
| Need to reduce compute costs with flexible commitments | **AWS Savings Plans** |
| Need to reserve specific instance types for guaranteed capacity + discount | **Reserved Instances** |
| Need ML-based recommendations to right-size resources | **AWS Compute Optimizer** |
| Need to create custom billing views for internal teams | **AWS Billing Conductor** |
| Need to estimate costs before building anything | **AWS Pricing Calculator** |

## Cost Analysis & Monitoring Tools

### AWS Cost Explorer

| Feature | Description |
|---------|-------------|
| **Time range** | Up to 12 months historical, 12 months forecast |
| **Granularity** | Monthly, daily, hourly (hourly requires opt-in) |
| **Filters** | Service, linked account, tag, region, instance type, purchase option |
| **Visualizations** | Line charts, bar charts, cost breakdown tables |
| **Forecasting** | ML-based spend prediction with confidence intervals |
| **RI/SP reports** | Utilization, coverage, and recommendations |
| **Cost** | Free (API calls charged at $0.01 per request) |

### AWS Budgets

| Budget Type | What It Tracks | Alert Options |
|-------------|---------------|---------------|
| **Cost budget** | Actual or forecasted dollar spend | Email, SNS, chatbot |
| **Usage budget** | Service usage (hours, GB, requests) | Email, SNS, chatbot |
| **RI utilization budget** | Reserved Instance utilization % | Email, SNS |
| **RI coverage budget** | % of running hours covered by RIs | Email, SNS |
| **Savings Plans utilization** | SP utilization % | Email, SNS |
| **Savings Plans coverage** | % of eligible spend covered by SPs | Email, SNS |

| Feature | Detail |
|---------|--------|
| **Budget actions** | Auto-apply IAM policy, auto-apply SCP, stop EC2/RDS instances |
| **Thresholds** | Multiple per budget (e.g., 80%, 90%, 100%) |
| **Frequency** | Monthly, quarterly, annually, or custom period |
| **Cost** | First 2 budgets free, then $0.02/budget/day |

### AWS Cost Anomaly Detection

| Feature | Description |
|---------|-------------|
| **Detection method** | ML models trained on your historical spend patterns |
| **Monitors** | AWS services, linked accounts, cost allocation tags, cost categories |
| **Alert thresholds** | Set minimum dollar impact or % deviation |
| **Notifications** | Email, SNS, Slack (via chatbot) |
| **Root cause** | Shows which service, account, region, or usage type caused the anomaly |
| **Response time** | Detects anomalies within 24 hours of occurrence |
| **Cost** | Free |

### AWS Cost and Usage Report (CUR)

| Feature | Description |
|---------|-------------|
| **Delivery** | S3 bucket (CSV or Parquet) |
| **Granularity** | Hourly, daily, or monthly |
| **Content** | Every line item: resource ID, tags, pricing, usage, discounts, taxes |
| **Integration** | Athena, QuickSight, Redshift, third-party tools (CloudHealth, Spot.io) |
| **Columns** | 100+ columns covering identity, billing, line item, product, pricing, reservation, savings plan |
| **Updates** | Refreshed up to 3x daily |
| **Cost** | Free (pay only for S3 storage and query tools) |

#### CUR Common Query Patterns

| Query Goal | Approach |
|-----------|----------|
| Cost per team/project | Filter by cost allocation tags |
| Unused Reserved Instances | Join RI line items where usage = 0 |
| Data transfer costs | Filter `product/productFamily = "Data Transfer"` |
| Spot vs On-Demand breakdown | Group by `lineItem/LineItemType` |
| Month-over-month comparison | Partition by `bill/BillingPeriodStartDate` |


## AWS Billing & EC2 Pricing — Quick Reference with Analogies

### Billing & Cost Tools — Think of Running a Company

| Service | Real-World Analogy | One-Liner | Example |
|---------|-------------------|-----------|---------|
| **Consolidated Billing** | One family phone plan for all family members | One bill for all AWS accounts under an Organization | Parent account pays for dev, staging, and prod accounts. Also pools usage for volume discounts (e.g., combined S3 usage hits a cheaper tier faster). |
| **AWS Pricing Calculator** | A car configurator before you buy | "What will this cost?" estimator *before* you build | You're planning a 3-tier app. You plug in 2 ALBs, 4 EC2s, 1 RDS, and it tells you ~$1,200/month *before* you deploy anything. |
| **Cost Allocation Tags** | Labels on boxes in a shared warehouse | Tag resources so you know *who* or *what* caused the cost | Tag every resource with `Team=DataEngineering` or `Project=Phoenix`. Now your CUR/Cost Explorer can show: "Data Engineering spent $8K last month." |
| **AWS Cost Explorer** | Your bank's spending analytics dashboard | Look *backward* at what you spent, look *forward* with forecasts | "We spent 40% more on EC2 in June vs May — why?" Filter by tag, service, or account to find the answer. |
| **AWS Billing Conductor** | A landlord splitting the electricity bill with custom rules | Create custom billing views with your own pricing logic for internal teams | You want to charge Team A at a 10% markup and give Team B a 5% discount as an internal shared service. Billing Conductor lets you create those custom rate cards. |
| **AWS Budgets** | A spending alarm on your credit card | "Alert me (or *do something*) when I'm about to go over" | Set a $5,000/month budget. Get emailed at 80%. At 100%, auto-apply an SCP that blocks new EC2 launches. |
| **AWS Cost and Usage Report (CUR)** | Your itemized credit card statement (every single line item) | Raw, granular data dump of *every* charge, delivered to S3 | You need to build a custom QuickSight dashboard showing cost-per-API-call per microservice. CUR gives you the 100+ column CSV/Parquet to do it. |

#### Memory Trick — Think Chronologically

```
BEFORE you build  →  Pricing Calculator ("How much will it cost?")
WHILE you run     →  Cost Allocation Tags ("Who's spending what?")
                     Cost Explorer ("Show me the trends")
                     Cost Anomaly Detection ("Something's wrong")
WHEN you overspend → Budgets ("Stop me at $X")
AFTER the month   →  CUR ("Give me every line item")
ACROSS accounts   →  Consolidated Billing ("One bill for everyone")
CUSTOM billing    →  Billing Conductor ("Charge teams differently")
```


## Savings Plans / Reserved Instances & Compute Optimizer

### Savings Plans vs Reserved Instances

| Criteria | Savings Plans | Reserved Instances |
|----------|:------------:|:-----------------:|
| **Commitment** | $/hr spend commitment | Specific instance type reservation |
| **Flexibility** | Across instance families, sizes, OS, regions, tenancy | Fixed to instance type (convertible RI has some flexibility) |
| **Discount** | Up to 72% | Up to 72% |
| **Term** | 1 year or 3 years | 1 year or 3 years |
| **Payment options** | All upfront, partial upfront, no upfront | All upfront, partial upfront, no upfront |
| **Applies to** | EC2, Fargate, Lambda (Compute SP) / EC2 only (EC2 SP) | EC2, RDS, ElastiCache, Redshift, OpenSearch, MemoryDB |
| **Capacity reservation** | No (unless paired with On-Demand Capacity Reservation) | Yes (Zonal RIs guarantee capacity) |
| **Marketplace resale** | No | Yes (Standard RIs only) |
| **Recommendation** | Preferred for compute flexibility | Use when you need capacity reservation or for non-EC2 services (RDS, ElastiCache) |

### Savings Plan Types

| Type | Scope | Flexibility | Max Discount |
|------|-------|-------------|:------------:|
| **Compute Savings Plan** | EC2, Fargate, Lambda | Any instance family, size, AZ, region, OS, tenancy | Up to 66% |
| **EC2 Instance Savings Plan** | EC2 only | Any size, OS, tenancy within a specific family + region | Up to 72% |
| **SageMaker Savings Plan** | SageMaker | Any instance family, size, region, component | Up to 64% |

### Reserved Instance Types

| Type | Flexibility | Can Modify | Can Exchange | Marketplace |
|------|-------------|:----------:|:------------:|:-----------:|
| **Standard RI** | Fixed instance type, platform, tenancy | AZ, scope, network platform | No | Yes |
| **Convertible RI** | Exchange for different instance type/family/OS/tenancy | AZ, scope, network platform | Yes (equal or greater value) | No |

### AWS Compute Optimizer

| Feature | Description |
|---------|-------------|
| **Resources analyzed** | EC2 instances, Auto Scaling groups, EBS volumes, Lambda functions, ECS on Fargate, commercial software licenses |
| **Data source** | CloudWatch metrics (CPU, memory, network, disk) over 14 days (or 93 days with enhanced monitoring) |
| **Recommendations** | Right-size (downsize/upsize), instance family change, graviton migration |
| **Savings estimate** | Dollar and percentage savings per recommendation |
| **Risk level** | Rates each recommendation: over-provisioned, under-provisioned, optimized |
| **Integration** | Cost Explorer, Organizations (org-wide recommendations) |
| **Cost** | Free (enhanced 93-day metrics: $0.0003360 per resource per hour) |

#### Compute Optimizer Recommendation Examples

| Current State | Recommendation | Potential Savings |
|--------------|----------------|:-----------------:|
| m5.2xlarge running at 10% CPU | Downsize to m5.large | ~75% |
| gp2 EBS volume with low IOPS | Switch to gp3 with baseline IOPS | ~20% |
| Lambda at 1024 MB, using 200 MB | Reduce to 256 MB | ~75% (duration may increase) |
| c5.xlarge running steady 24/7 | Apply Savings Plan + Graviton (c7g.xlarge) | ~50–60% |

## Cost Optimization Workflow

| Stage | Tool | Action |
|-------|------|--------|
| **1. Visibility** | Cost Explorer + CUR | Understand where money is going |
| **2. Alerting** | Budgets + Cost Anomaly Detection | Get notified before costs spiral |
| **3. Right-sizing** | Compute Optimizer | Eliminate waste from over-provisioned resources |
| **4. Commitment** | Savings Plans + Reserved Instances | Lock in discounts for steady-state workloads |
| **5. Architecture** | Well-Architected (Cost Pillar) | Design for cost efficiency (serverless, spot, tiered storage) |
| **6. Governance** | Organizations + Billing Conductor + Tags | Enforce tagging, allocate costs, set guardrails |

## Cost Allocation Best Practices

| Practice | Tool | Benefit |
|----------|------|---------|
| Tag all resources with cost allocation tags | AWS Organizations tag policies | Enables per-team/project cost breakdown |
| Enable Cost Categories | Cost Explorer | Group costs by business logic (BU, environment, app) |
| Use linked accounts per environment | AWS Organizations | Natural cost boundary (dev/stage/prod) |
| Automate unused resource cleanup | Trusted Advisor + Lambda | Eliminate idle ELBs, unattached EBS, old snapshots |
| Review monthly with Cost Intelligence Dashboard | CUR + QuickSight | Executive-level cost visibility |

## Cost Comparison (Relative)

| Service | Pricing | Notes |
|---------|---------|-------|
| **AWS Cost Explorer** | Free (API: $0.01/request) | Included with every account |
| **AWS Budgets** | First 2 free, then $0.02/budget/day | ~$0.60/month per extra budget |
| **AWS Cost Anomaly Detection** | Free | No charge |
| **AWS Cost and Usage Report** | Free (S3 + query tool costs) | Parquet format reduces storage cost |
| **AWS Savings Plans** | No fee (commitment-based) | Pay the committed $/hr regardless of usage |
| **Reserved Instances** | No fee (commitment-based) | Pay whether you use it or not |
| **AWS Compute Optimizer** | Free (enhanced metrics: $0.0003360/resource/hr) | Enhanced = 93-day lookback |
| **AWS Billing Conductor** | $0.50 per linked account/month | Only for accounts in billing groups |
| **AWS Pricing Calculator** | Free | Web-based planning tool |

## Common Exam / Interview Traps

| Question Pattern | Answer | Why People Get It Wrong |
|-----------------|--------|------------------------|
| "Which gives visibility into physical sockets?" | Dedicated Host | People confuse Dedicated Instance (no socket visibility) with Dedicated Host |
| "Which is needed for BYOL?" | Dedicated Host | Dedicated Instance gives isolation but NOT core/socket mapping needed for licenses |
| "What pools volume discounts across accounts?" | Consolidated Billing | People think it's a separate discount program — it's just billing aggregation |
| "What alerts you *before* you overspend?" | AWS Budgets | People confuse with Cost Explorer (which only shows what already happened) |
| "What gives raw line-item data for custom analysis?" | CUR | Cost Explorer is visual; CUR is the raw data behind it |
| "What estimates costs *before* deployment?" | Pricing Calculator | Cost Explorer only works on *existing* usage |
| "How to track cost per team?" | Cost Allocation Tags | Not Consolidated Billing (that's per-account, not per-team within an account) |
| "How to charge teams at custom rates?" | Billing Conductor | Not Cost Allocation Tags (those categorize, they don't re-price) |
