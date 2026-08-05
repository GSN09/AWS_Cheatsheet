# AWS Overview — Frameworks, Principles & Foundational Concepts

## Topic Comparison Matrix

| Topic | What It Is | Purpose | When to Reference | Key Takeaway |
|-------|-----------|---------|-------------------|--------------|
| **AWS Well-Architected Framework** | Six-pillar framework for building reliable, secure, efficient workloads | Evaluate and improve architectures against best practices | Architecture reviews, new project design, workload optimization | Every architecture decision maps to one or more pillars |
| **AWS Cloud Adoption Framework (CAF)** | Guidance framework for cloud migration and transformation | Align business and IT strategy for cloud adoption | Migration planning, organizational readiness, executive buy-in | Covers people, process, and technology across 6 perspectives |
| **AWS Design Principles** | Foundational architectural principles for cloud-native design | Guide design decisions toward scalable, resilient systems | Solution architecture, design reviews, pattern selection | Design for failure, decouple components, think parallel |
| **AWS Global Infrastructure** | Physical network of Regions, AZs, edge locations, and local zones | Understand deployment options and geographic reach | Region selection, HA/DR planning, latency optimization | 30+ Regions, 100+ AZs, 400+ edge locations |
| **AWS Disaster Recovery** | Strategies and patterns for business continuity | Plan for and recover from failures and disasters | DR planning, RPO/RTO requirements, resilience architecture | Four strategies ranging from cheap/slow to expensive/fast |
| **AWS Pricing** | Pay-as-you-go consumption-based pricing model | Understand cost drivers and optimization levers | Budgeting, cost optimization, architecture decisions | Pay for what you use, save by committing, transfer costs matter |
| **AWS Support Plans** | Tiered technical support and advisory services | Get help when you need it, at the right level | Project planning, operational readiness, choosing support tier | Higher tiers = faster response + dedicated guidance |

## Six Pillars of the Well-Architected Framework (OpEx SecReCo PSus)

| Pillar | Focus Area | Key Questions | Design Principles |
|--------|-----------|---------------|-------------------|
| **Operational Excellence** | Run and monitor systems, improve processes | How do you manage and automate changes? How do you respond to events? | Perform operations as code, make frequent small reversible changes, anticipate failure |
| **Security** | Protect data, systems, and assets | How do you manage identities? How do you detect and investigate security events? | Implement strong identity foundation, enable traceability, apply security at all layers, automate best practices |
| **Reliability** | Recover from failures, meet demand | How do you manage service quotas? How does your system adapt to changes in demand? | Automatically recover from failure, test recovery procedures, scale horizontally, stop guessing capacity |
| **Performance Efficiency** | Use resources efficiently as demand changes | How do you select the best-performing architecture? How do you monitor performance? | Democratize advanced technologies, go global in minutes, use serverless architectures, experiment more often |
| **Cost Optimization** | Avoid unnecessary costs | How do you govern usage? How do you decommission resources? | Implement cloud financial management, adopt a consumption model, measure overall efficiency, analyze and attribute expenditure |
| **Sustainability** | Minimize environmental impact | How do you select Regions to reduce carbon footprint? How do you optimize resource utilization? | Understand your impact, establish sustainability goals, maximize utilization, use managed services |

## AWS Cloud Adoption Framework (CAF) — Six Perspectives

| Perspective | Stakeholders | Focus | Key Capabilities |
|-------------|-------------|-------|-----------------|
| **Business** | CEO, CFO, COO, CIO | Ensure IT aligns with business outcomes | Strategy management, benefits realization, business risk management |
| **People** | CIO, HR, COO | Bridge technology and business through culture and organizational change | Culture evolution, transformational leadership, cloud fluency, change acceleration |
| **Governance** | CIO, CTO, CFO, CDO | Maximize cloud benefits while minimizing risk | Program/project management, benefits management, risk management, cloud financial management |
| **Platform** | CTO, architects, engineers | Build enterprise-grade, scalable cloud platform | Platform architecture, data architecture, platform engineering, provisioning automation |
| **Security** | CISO, CCO, security teams | Achieve confidentiality, integrity, and availability | Security governance, security assurance, identity and access management, threat detection |
| **Operations** | IT ops, SRE, infrastructure | Deliver cloud services at agreed-upon level | Observability, event management, incident management, change and release management |

<img width="977" height="391" alt="image" src="https://github.com/user-attachments/assets/c9d7bf97-408a-427e-92c2-f29ece9d1efc" />


## AWS Design Principles

| Principle | Description | Example |
|-----------|-------------|---------|
| **Design for failure** | Assume everything fails; design so the system survives it | Multi-AZ deployments, health checks, auto-recovery |
| **Decouple components** | Reduce interdependencies between components | SQS queues between services, event-driven architecture |
| **Implement elasticity** | Scale out/in automatically to match demand | Auto Scaling groups, serverless compute |
| **Think parallel** | Distribute work across many resources | Parallelized batch processing, horizontal scaling |
| **Keep dynamic data close to compute** | Minimize latency between data and processing | ElastiCache near app tier, same-AZ data placement |
| **Use the right storage for the job** | No one-size-fits-all storage solution | S3 for objects, DynamoDB for key-value, RDS for relational |
| **Security in every layer** | Apply defense in depth | WAF → Security Groups → NACLs → encryption at rest |
| **Leverage managed services** | Offload undifferentiated heavy lifting to AWS | RDS over self-managed DB, Lambda over EC2 for event handlers |

## AWS Disaster Recovery Strategies

| Strategy | RPO | RTO | Cost | Description |
|----------|:---:|:---:|:----:|-------------|
| **Backup & Restore** | Hours | Hours | $ | Back up data to S3/Glacier, restore when needed. Lowest cost, longest recovery. |
| **Pilot Light** | Minutes | 10s of minutes | $$ | Core infrastructure always running (DB replication), scale up on failover. |
| **Warm Standby** | Seconds–Minutes | Minutes | $$$ | Scaled-down but fully functional copy running in DR region. Scale up on failover. |
| **Multi-Site Active-Active** | Near zero | Near zero (seconds) | $$$$ | Full production in 2+ regions. Traffic served from both. Instant failover. |

### DR Strategy Selection Guide

| Requirement | Recommended Strategy |
|-------------|---------------------|
| Budget-constrained, can tolerate hours of downtime | **Backup & Restore** |
| Need faster recovery but want to minimize always-on costs | **Pilot Light** |
| Need minutes-level RTO with reduced capacity acceptable initially | **Warm Standby** |
| Zero tolerance for downtime, mission-critical workload | **Multi-Site Active-Active** |

## AWS Global Infrastructure

| Component | Count (approx.) | Purpose |
|-----------|:--------------:|---------|
| **Regions** | 33+ | Geographically isolated clusters of data centers |
| **Availability Zones (AZs)** | 105+ | Isolated data centers within a region (1+ per AZ) |
| **Edge Locations** | 400+ | CloudFront CDN, Route 53 DNS, and Global Accelerator points of presence |
| **Local Zones** | 30+ | AWS infrastructure closer to end users for low-latency workloads |
| **Wavelength Zones** | 30+ | AWS compute embedded in 5G carrier networks |
| **Outposts** | Customer-deployed | AWS infrastructure in customer on-premises data centers |

### Region Selection Criteria

| Factor | Consideration |
|--------|--------------|
| **Compliance** | Data residency and regulatory requirements |
| **Latency** | Proximity to end users |
| **Service availability** | Not all services available in all regions |
| **Pricing** | Costs vary by region (us-east-1 often cheapest) |
| **DR strategy** | Choose DR region with geographic separation |

## AWS Pricing — Key Concepts

| Concept | Description | Optimization Strategy |
|---------|-------------|----------------------|
| **Pay-as-you-go** | No upfront commitment, pay for what you consume | Right-size resources, turn off unused instances |
| **Reserved capacity** | Commit for 1–3 years, save up to 72% | Stable, predictable workloads (RDS, EC2, Redshift) |
| **Savings Plans** | Flexible commitment ($/hr), save up to 72% | Compute/EC2/SageMaker workloads with flexible instance types |
| **Spot Instances** | Use spare EC2 capacity, save up to 90% | Fault-tolerant, flexible workloads (batch, CI/CD, HPC) |
| **Free Tier** | 12-month free, always free, or trial offers | Dev/test, learning, prototyping |
| **Data Transfer** | Inbound free, outbound charged, inter-AZ costs apply | Keep traffic within AZ, use VPC endpoints, CloudFront for egress |

### Pricing Tools

| Tool | Purpose |
|------|---------|
| **AWS Pricing Calculator** | Estimate monthly costs for new architectures |
| **AWS Cost Explorer** | Analyze historical spend and forecast future costs |
| **AWS Budgets** | Set spend alerts and automated actions |
| **AWS Cost and Usage Report** | Granular billing data for custom analysis |
| **Savings Plans** | Recommend and manage commitment-based discounts |

## AWS Support Plans

| Plan | Price | Response Time (Critical) | Key Features |
|------|:-----:|:------------------------:|--------------|
| **Basic** | Free | None (docs + forums only) | Trusted Advisor (7 checks), Personal Health Dashboard |
| **Developer** | $29/mo or 3% of usage | 12 hours (general) | Business-hours email support, 1 primary contact |
| **Business** | $100/mo or 5–10% of usage | < 1 hour (production down) | 24/7 phone/chat, all Trusted Advisor checks, AWS Support API |
| **Enterprise On-Ramp** | $5,500/mo or 10% of usage | < 30 min (business-critical) | TAM pool, Concierge, Infrastructure Event Management (1/yr) |
| **Enterprise** | $15,000/mo or 3–10% of usage | < 15 min (business-critical) | Designated TAM, Concierge, Infrastructure Event Management, training |

### Support Plan Selection Guide

| Scenario | Recommended Plan |
|----------|-----------------|
| Learning/experimenting, no production workloads | **Basic** |
| Development workloads, non-urgent questions | **Developer** |
| Production workloads, need 24/7 support | **Business** |
| Business-critical workloads, want advisory support | **Enterprise On-Ramp** |
| Mission-critical workloads, need dedicated TAM | **Enterprise** |

### Shared Responsibility Model
<img width="1148" height="720" alt="image" src="https://github.com/user-attachments/assets/93402356-cc1c-4fca-b97f-91b58ffdc959" />
