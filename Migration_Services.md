# AWS Migration Services — When to Use Guide

## Service Comparison Matrix

| Service | Primary Use Case | Migration Type | Data/Workload Target | Transfer Method | Typical Workloads |
|---------|-----------------|----------------|---------------------|-----------------|-------------------|
| **AWS Database Migration Service (DMS)** | Migrate databases to AWS with minimal downtime | Database migration & replication | Relational DBs, NoSQL, data warehouses | Network (continuous replication) | Homogeneous/heterogeneous DB migrations, ongoing replication, consolidation |
| **AWS DataSync** | Automated data transfer between on-prem and AWS storage | File/object data transfer | S3, EFS, FSx, on-prem NFS/SMB | Network (agent-based, accelerated) | NAS migrations, cold data archival, recurring data workflows, hybrid storage sync |
| **AWS Server Migration Service (SMS)** | Lift-and-shift of on-premises VMs to AWS | Server/VM migration | EC2 AMIs from VMware, Hyper-V, Azure VMs | Network (incremental replication) | VM-to-EC2 migrations, phased server cutovers, large-scale rehost projects |
| **AWS Snowball Edge** | Offline bulk data transfer (10–80 TB per device) | Physical data transport | S3 (post-import) | Physical appliance shipping | Large dataset migrations without network, edge computing in disconnected sites |
| **AWS Snowmobile** | Exabyte-scale offline data transfer (up to 100 PB) | Physical data transport | S3 (post-import) | Shipping container (truck) | Full data center evacuations, massive media libraries, seismic/genomic datasets |
| **AWS Data Transfer Terminal** | High-speed physical data upload at AWS facilities | Physical/network hybrid | S3, other AWS services | Customer brings devices to AWS facility | One-time massive transfers when network is too slow but Snowball isn't needed |
| **Migration Evaluator** | Build a business case for cloud migration | Assessment & planning | Cost modeling, TCO analysis | Agent-based discovery (no data moved) | Executive buy-in, migration planning, right-sizing, TCO/ROI comparisons |
| **AWS Migration Strategies (6 R's)** | Framework for deciding how to migrate each workload | Strategic planning | All workload types | N/A (decision framework) | Portfolio analysis, migration wave planning, modernization roadmaps |

## The 6 R's Migration Strategies

| Strategy | Description | When to Use | Effort Level | Example |
|----------|-------------|-------------|:------------:|---------|
| **Rehost** (Lift & Shift) | Move as-is to AWS with no code changes | Quick migration, minimal risk tolerance | Low | VM → EC2, database → RDS with same engine |
| **Replatform** (Lift, Tinker & Shift) | Minor optimizations without changing core architecture | Quick wins with managed services | Low–Medium | Move DB to RDS, add ALB, use ElastiCache |
| **Repurchase** (Drop & Shop) | Replace with a SaaS or marketplace solution | Legacy or COTS apps with better cloud-native alternatives | Medium | On-prem CRM → Salesforce, on-prem email → SES/WorkMail |
| **Refactor** (Re-architect) | Redesign using cloud-native patterns | Need scalability, agility, or cost optimization | High | Monolith → microservices, move to serverless |
| **Retire** | Decommission apps no longer needed | Redundant, unused, or end-of-life systems | None | Legacy reporting tool replaced by new analytics platform |
| **Retain** (Revisit) | Keep on-premises for now | Not ready, recently upgraded, or compliance constraints | None | Mainframe with no migration path yet |

## Decision Criteria

| Decision Factor | Recommended Service |
|----------------|-------------------|
| Need to migrate a database with minimal downtime | **AWS DMS** |
| Need to move large file datasets (NFS/SMB) to S3/EFS/FSx | **AWS DataSync** |
| Need to lift-and-shift VMware/Hyper-V VMs to EC2 | **AWS SMS** |
| Need to move 10–80 TB offline per shipment | **AWS Snowball Edge** |
| Need to move 10+ PB in a single operation | **AWS Snowmobile** |
| Have physical media to upload at an AWS location | **AWS Data Transfer Terminal** |
| Need to build a migration business case with TCO analysis | **Migration Evaluator** |
| Need to decide rehost vs. refactor vs. retire per workload | **6 R's Framework** |

## Service Comparison by Scenario

| Scenario | Network-Based Option | Offline Option | Planning Tool |
|----------|---------------------|----------------|---------------|
| Database migration | AWS DMS | N/A | Migration Evaluator |
| File/storage migration | AWS DataSync | Snowball Edge / Snowmobile | Migration Evaluator |
| Server/VM migration | AWS SMS | N/A | Migration Evaluator |
| 50 TB one-time transfer | AWS DataSync (if bandwidth allows) | Snowball Edge (1 device) | N/A |
| 500 TB one-time transfer | Not practical over network | Snowball Edge (multiple) | N/A |
| 50+ PB data center move | Not practical over network | Snowmobile | Migration Evaluator |

## Transfer Speed Comparison

| Service | Transfer Rate | Best For |
|---------|-------------|----------|
| **AWS DMS** | Depends on source DB and network (continuous CDC) | Ongoing database sync |
| **AWS DataSync** | Up to 10 Gbps per agent (parallelized) | Recurring or large file transfers |
| **AWS SMS** | Incremental replication over network | Phased VM cutovers |
| **AWS Snowball Edge** | ~100 TB in ~1 week (including shipping) | Large offline transfers |
| **AWS Snowmobile** | ~100 PB in weeks (including logistics) | Exabyte-scale moves |
| **AWS Data Transfer Terminal** | Up to 400 Gbps at AWS facility | Fast physical uploads |

## Cost Comparison (Relative)

| Service | Pricing Model | Relative Cost | Notes |
|---------|--------------|:-------------:|-------|
| **AWS DMS** | Per-instance-hour + storage | $$ | Free for same-engine migrations (limited) |
| **AWS DataSync** | Per GB transferred | $ | No agent licensing fee |
| **AWS SMS** | Free (pay only for S3 snapshots + EC2) | $ | Low cost for VM migrations |
| **AWS Snowball Edge** | Per-device rental + shipping | $$ | Cost-effective above ~10 TB |
| **AWS Snowmobile** | Custom pricing | $$$$ | Only cost-effective at 10+ PB |
| **AWS Data Transfer Terminal** | Per-hour facility use + data transfer | $$–$$$ | Situational |
| **Migration Evaluator** | Free | Free | AWS-funded assessment tool |
