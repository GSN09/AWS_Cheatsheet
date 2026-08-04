# AWS Management & Governance Services — When to Use Guide

## Service Comparison Matrix

| Service | Primary Use Case | Category | Scope | Typical Workloads |
|---------|-----------------|----------|-------|-------------------|
| **AWS Trusted Advisor** | Automated best-practice checks across 5 pillars | Advisory & Recommendations | Account / Organization | Cost optimization, security gaps, fault tolerance, performance bottlenecks, service limit warnings |
| **Amazon CloudWatch** | Monitor metrics, logs, alarms, and dashboards | Monitoring & Observability | Regional | EC2 CPU/memory alerts, Lambda duration tracking, custom app metrics, log aggregation |
| **AWS CloudTrail** | Record all API activity (who did what, when) | Audit & Compliance | Regional / Global (org trail) | Security investigations, change tracking, compliance audits, forensic analysis |
| **AWS Config** | Track resource configuration changes and evaluate compliance rules | Configuration Management | Regional | Drift detection, compliance validation (e.g., "all S3 buckets must be encrypted"), change history |
| **AWS Systems Manager** | Operational management of EC2 and on-prem servers | Operations Management | Regional | Patch management, parameter store, run commands, session manager (no SSH), inventory |
| **AWS Health Dashboard** | Personalized view of AWS service events affecting you | Service Health | Global | Outage awareness, scheduled maintenance alerts, event-driven automation |
| **AWS Service Catalog** | Self-service portal of approved IT products | Governance & Provisioning | Regional | Pre-approved CloudFormation templates, standardized environments, developer self-service |
| **AWS Control Tower** | Automated multi-account governance setup | Account Governance | Organization-wide | Landing zone setup, guardrails (preventive + detective), account factory |

## Decision Criteria

| Decision Factor | Recommended Service |
|----------------|-------------------|
| Need automated checks for cost waste, security gaps, and limits | **AWS Trusted Advisor** |
| Need real-time metrics, logs, and alarms for resources | **Amazon CloudWatch** |
| Need to know who made what API call and when | **AWS CloudTrail** |
| Need to track resource configuration changes and enforce compliance rules | **AWS Config** |
| Need to patch, configure, or remotely access servers without SSH | **AWS Systems Manager** |
| Need to know if an AWS outage or maintenance affects your resources | **AWS Health Dashboard** |
| Need to give developers a self-service catalog of approved infrastructure | **AWS Service Catalog** |
| Need to set up a governed multi-account environment quickly | **AWS Control Tower** |

---

## AWS Trusted Advisor — Deep Dive

### The 5 Pillars of Trusted Advisor

| Pillar | What It Checks | Example Findings |
|--------|---------------|-----------------|
| **Cost Optimization** | Wasted spend, unused resources | Idle EC2 instances, underutilized EBS volumes, unassociated Elastic IPs, idle RDS instances |
| **Security** | Misconfigurations that expose risk | Open security groups (0.0.0.0/0 on port 22), no MFA on root, public S3 buckets, exposed access keys |
| **Fault Tolerance** | Single points of failure | No Multi-AZ on RDS, EBS snapshots not recent, ASG in single AZ, no Route 53 health checks |
| **Performance** | Bottlenecks and under-provisioned resources | Over-utilized EC2, CloudFront alternate domain not configured, high-utilization EBS volumes |
| **Service Limits** | Approaching AWS quotas | EC2 instance limit at 80%, VPCs approaching limit, EBS volume cap nearing |

### Trusted Advisor by Support Plan

| Check Category | Basic / Developer | Business / Enterprise |
|---------------|:-----------------:|:---------------------:|
| **Core security checks** | 7 checks (S3 bucket permissions, SG open ports, IAM use, MFA on root, EBS/RDS public snapshots) | All checks |
| **Cost optimization** | No | All checks |
| **Fault tolerance** | No | All checks |
| **Performance** | No | All checks |
| **Service limits** | All | All |
| **Programmatic access (API)** | No | Yes (Support API) |
| **CloudWatch integration** | No | Yes (metrics for checks) |
| **Refresh frequency** | Manual (weekly auto) | Programmatic refresh via API |

### Trusted Advisor vs Similar Services

| Criteria | Trusted Advisor | AWS Config | Security Hub | Compute Optimizer |
|----------|:--------------:|:----------:|:------------:|:-----------------:|
| **Focus** | Broad best-practice advice (5 pillars) | Resource compliance rules | Security findings aggregation | Right-sizing compute resources |
| **Actionable?** | Recommendations (manual action) | Rules with auto-remediation (via SSM) | Findings with severity scores | Specific resize recommendations |
| **Scope** | Account-wide summary | Per-resource config history | Cross-service security posture | EC2, Lambda, EBS, ASG |
| **Cost checks** | Yes | No | No | Yes (savings estimates) |
| **Security checks** | Basic (7 or all) | Custom rules (conformance packs) | Comprehensive (GuardDuty, Inspector, Macie) | No |
| **Requires** | Support plan (Business+ for full) | Config recorder enabled | Enabled per region | Opt-in |

### Common Trusted Advisor Findings & Actions

| Finding | Pillar | Action |
|---------|--------|--------|
| Idle EC2 instance (CPU < 2% for 14 days) | Cost | Stop/terminate or downsize |
| Unassociated Elastic IP | Cost | Release it ($0.005/hr when idle) |
| S3 bucket with public access | Security | Enable Block Public Access |
| Security Group allowing 0.0.0.0/0 on port 22 | Security | Restrict to specific IP/CIDR |
| No MFA on root account | Security | Enable MFA immediately |
| RDS without Multi-AZ | Fault Tolerance | Enable Multi-AZ |
| EBS volume without recent snapshot | Fault Tolerance | Create automated snapshot schedule |
| EC2 utilization > 90% sustained | Performance | Upsize instance or add to ASG |
| VPC limit at 80% | Service Limits | Request quota increase |

---

## Amazon CloudWatch — Deep Dive

### CloudWatch Components

| Component | Purpose | Example |
|-----------|---------|---------|
| **Metrics** | Numeric time-series data | EC2 CPUUtilization, RDS FreeStorageSpace, custom app metrics |
| **Alarms** | Trigger actions when metric breaches threshold | Scale out ASG when CPU > 70%, SNS alert when errors > 100 |
| **Logs** | Centralized log collection and querying | Application logs, VPC Flow Logs, Lambda execution logs |
| **Log Insights** | SQL-like query language for logs | "Show top 10 error messages in last hour" |
| **Dashboards** | Visual overview of key metrics | Ops dashboard with CPU, memory, latency, error rate |
| **Events / EventBridge** | React to state changes or schedule actions | Trigger Lambda when EC2 state changes to "stopped" |
| **Synthetics** | Canary scripts that monitor endpoints | Synthetic checks every 5 min on login page availability |
| **ServiceLens** | Trace-based service map (with X-Ray) | End-to-end latency visualization across microservices |

### CloudWatch Default vs Custom Metrics

| Type | Frequency | Examples | Cost |
|------|:---------:|---------|------|
| **Default (basic)** | 5 minutes | CPU, disk I/O, network (no memory/disk usage!) | Free |
| **Detailed monitoring** | 1 minute | Same as default, higher resolution | $$ |
| **Custom metrics** | Any (1 sec to 1 day) | Memory %, app queue depth, business KPIs | Per metric + per API call |

> **Exam trap:** CloudWatch does NOT monitor memory or disk space by default. You need the CloudWatch Agent for those.

---

## AWS CloudTrail — Deep Dive

### CloudTrail Event Types

| Event Type | What It Captures | Example | Default |
|------------|-----------------|---------|:-------:|
| **Management events** | Control plane operations (create, delete, modify) | `CreateBucket`, `RunInstances`, `AttachRolePolicy` | Enabled (90-day free lookup) |
| **Data events** | Data plane operations (read/write on resources) | `GetObject` on S3, `Invoke` on Lambda | Disabled (must enable, costs more) |
| **Insights events** | Unusual API activity patterns | Sudden spike in `TerminateInstances` calls | Disabled (opt-in) |

### CloudTrail vs Config vs CloudWatch

| Criteria | CloudTrail | AWS Config | CloudWatch |
|----------|:----------:|:----------:|:----------:|
| **Question answered** | "Who did what?" | "What changed and is it compliant?" | "How is it performing?" |
| **Data type** | API call logs | Resource configuration snapshots | Metrics, logs, alarms |
| **Example** | "gbatchu deleted the S3 bucket at 3pm" | "This SG was modified and now violates our rule" | "CPU hit 95% for 10 minutes" |
| **Retention** | 90 days (console) or indefinite (S3 trail) | As long as recorder is on | 15 months (metrics), configurable (logs) |

---

## AWS Config — Deep Dive

### Config Components

| Component | Purpose | Example |
|-----------|---------|---------|
| **Configuration recorder** | Continuously records resource configurations | Tracks every change to every SG, VPC, EC2, S3, IAM role |
| **Config rules** | Evaluate configurations against desired state | `s3-bucket-server-side-encryption-enabled`, `ec2-instance-no-public-ip` |
| **Conformance packs** | Pre-built bundles of rules for compliance frameworks | CIS AWS Foundations Benchmark, PCI-DSS, HIPAA |
| **Remediation** | Auto-fix non-compliant resources via SSM | Auto-enable encryption on non-compliant S3 buckets |
| **Aggregator** | Cross-account, cross-region compliance view | Org-wide dashboard of all non-compliant resources |

---

## AWS Systems Manager — Deep Dive

### Systems Manager Key Capabilities

| Capability | Purpose | Example |
|-----------|---------|---------|
| **Session Manager** | Browser-based shell access (no SSH, no bastion) | Connect to private EC2 without opening port 22 |
| **Patch Manager** | Automated OS patching with maintenance windows | Patch all production Linux instances every Sunday 2am |
| **Parameter Store** | Hierarchical config/secrets storage | Store DB connection strings, feature flags, API endpoints |
| **Run Command** | Execute commands across fleets without SSH | Run `yum update` on 500 instances simultaneously |
| **State Manager** | Ensure instances stay in desired state | Ensure CloudWatch Agent is always installed and running |
| **Inventory** | Collect metadata about instances and software | List all installed packages across entire fleet |
| **Automation** | Runbooks for operational tasks | Auto-restart failed instances, create golden AMIs |
| **OpsCenter** | Centralized operational issue tracking | Aggregate issues from CloudWatch, Config, Trusted Advisor |

### Parameter Store vs Secrets Manager

| Criteria | Parameter Store | Secrets Manager |
|----------|:--------------:|:---------------:|
| **Cost** | Free (standard), $0.05/10K API calls (advanced) | $0.40/secret/month + API calls |
| **Auto-rotation** | No (manual Lambda) | Yes (built-in for RDS, Redshift, DocumentDB) |
| **Max size** | 8 KB (standard), 8 KB (advanced) | 64 KB |
| **Cross-account** | No | Yes |
| **Best for** | Config values, feature flags, non-rotating secrets | Database passwords, API keys that need rotation |

---

## AWS Health Dashboard

### Two Views

| View | What It Shows | Example |
|------|--------------|---------|
| **Service Health Dashboard** | Global AWS service status (public) | "EC2 degraded in us-east-1" (affects everyone) |
| **Personal Health Dashboard** | Events that affect *your* resources specifically | "Your RDS instance in us-west-2 has scheduled maintenance on Saturday" |

### Health + EventBridge Integration

```
Personal Health Dashboard event → EventBridge rule → Lambda → Slack notification
```

> Automate responses to AWS events affecting your account (e.g., auto-failover when AZ degradation detected).

---

## AWS Control Tower vs Organizations

| Criteria | AWS Organizations | AWS Control Tower |
|----------|:-----------------:|:-----------------:|
| **Purpose** | Multi-account management + billing | Automated landing zone with governance |
| **SCPs** | You write them manually | Pre-built guardrails (mandatory + optional) |
| **Account creation** | Manual or API | Account Factory (self-service with guardrails) |
| **Baseline** | You configure everything | Pre-configured (logging, security, networking) |
| **Drift detection** | No | Yes (alerts when guardrails are violated) |
| **Relationship** | Foundation layer | Built *on top of* Organizations |

---

## Service Interaction Map

```
                    ┌─────────────────────┐
                    │   Control Tower      │ (governs)
                    │   + Organizations    │
                    └──────────┬──────────┘
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
   ┌──────▼──────┐    ┌───────▼───────┐    ┌──────▼──────┐
   │  CloudTrail  │    │  AWS Config    │    │  Trusted    │
   │  (who did    │    │  (what changed │    │  Advisor    │
   │   what?)     │    │   & compliant?)│    │  (best      │
   └──────┬──────┘    └───────┬───────┘    │  practices) │
          │                    │            └──────┬──────┘
          └─────────┬──────────┘                   │
                    │                              │
             ┌──────▼──────┐              ┌───────▼───────┐
             │  CloudWatch  │              │  Health       │
             │  (metrics +  │              │  Dashboard    │
             │   alarms)    │              │  (AWS events) │
             └──────┬──────┘              └───────────────┘
                    │
             ┌──────▼──────┐
             │  Systems     │
             │  Manager     │
             │  (act on it) │
             └─────────────┘
```

---

## Cost Comparison (Relative)

| Service | Pricing Model | Relative Cost | Notes |
|---------|--------------|:-------------:|-------|
| **AWS Trusted Advisor** | Included with Support Plan | Free–included | Full checks require Business+ support ($100+/mo) |
| **Amazon CloudWatch** | Per metric, per alarm, per GB logs | $–$$ | Default metrics free; custom/detailed costs add up |
| **AWS CloudTrail** | First management trail free; data events per 100K events | $ | S3 storage costs for trail logs |
| **AWS Config** | Per rule evaluation + per config item recorded | $$ | $0.003/rule eval, $0.001/config item |
| **AWS Systems Manager** | Mostly free (advanced features cost) | $–$$ | Session Manager, Run Command = free; advanced params = paid |
| **AWS Health Dashboard** | Free | Free | Included with every account |
| **AWS Service Catalog** | Per API call | $ | Minimal cost; CloudFormation stack costs apply |
| **AWS Control Tower** | Free (underlying services cost) | $ | Pays for Config rules, CloudTrail, S3 logs underneath |
