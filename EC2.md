# AWS Billing & EC2 Pricing — Quick Reference with Analogies

## Part 1: Billing & Cost Tools — Think of Running a Company

| Service | Real-World Analogy | One-Liner | Example |
|---------|-------------------|-----------|---------|
| **Consolidated Billing** | One family phone plan for all family members | One bill for all AWS accounts under an Organization | Parent account pays for dev, staging, and prod accounts. Also pools usage for volume discounts (e.g., combined S3 usage hits a cheaper tier faster). |
| **AWS Pricing Calculator** | A car configurator before you buy | "What will this cost?" estimator *before* you build | You're planning a 3-tier app. You plug in 2 ALBs, 4 EC2s, 1 RDS, and it tells you ~$1,200/month *before* you deploy anything. |
| **Cost Allocation Tags** | Labels on boxes in a shared warehouse | Tag resources so you know *who* or *what* caused the cost | Tag every resource with `Team=DataEngineering` or `Project=Phoenix`. Now your CUR/Cost Explorer can show: "Data Engineering spent $8K last month." |
| **AWS Cost Explorer** | Your bank's spending analytics dashboard | Look *backward* at what you spent, look *forward* with forecasts | "We spent 40% more on EC2 in June vs May — why?" Filter by tag, service, or account to find the answer. |
| **AWS Billing Conductor** | A landlord splitting the electricity bill with custom rules | Create custom billing views with your own pricing logic for internal teams | You want to charge Team A at a 10% markup and give Team B a 5% discount as an internal shared service. Billing Conductor lets you create those custom rate cards. |
| **AWS Budgets** | A spending alarm on your credit card | "Alert me (or *do something*) when I'm about to go over" | Set a $5,000/month budget. Get emailed at 80%. At 100%, auto-apply an SCP that blocks new EC2 launches. |
| **AWS Cost and Usage Report (CUR)** | Your itemized credit card statement (every single line item) | Raw, granular data dump of *every* charge, delivered to S3 | You need to build a custom QuickSight dashboard showing cost-per-API-call per microservice. CUR gives you the 100+ column CSV/Parquet to do it. |

### Memory Trick — Think Chronologically

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

---

## Part 2: EC2 Purchase Options — Think of Renting a Place to Live

| Option | Analogy | You Get | You Pay | Best For |
|--------|---------|---------|---------|----------|
| **On-Demand Instance** | Hotel room — walk in, pay by the night, leave anytime | Any instance, any time, no commitment | Full price per hour/second | Unpredictable workloads, dev/test, short spikes, prototyping |
| **Reserved Instance (RI)** | Signing a 1-year apartment lease — cheaper monthly rent | 1 or 3 year commitment on a specific instance type | Up to 72% off On-Demand | Steady-state workloads you *know* will run 24/7 (databases, core APIs) |
| **Dedicated Instance** | Renting a floor in a building — no other tenants on your floor, but you don't control which floor | Instance runs on hardware *not shared* with other AWS accounts | ~10% premium over On-Demand | Compliance/licensing that says "no multi-tenancy" but you don't care which exact physical server |
| **Dedicated Host** | Renting the *entire building* — you control every room, floor, and socket | A full physical server dedicated to you, with visibility into sockets/cores | Most expensive option | Bring-your-own-license (BYOL) for Windows Server, SQL Server, Oracle — licenses tied to specific physical cores/sockets |

### Key Differences Table

| Criteria | On-Demand | Reserved Instance | Dedicated Instance | Dedicated Host |
|----------|:---------:|:-----------------:|:-----------------:|:--------------:|
| **Commitment** | None | 1 or 3 years | None (or with RI) | None (or with RI) |
| **Hardware sharing** | Shared with other accounts | Shared with other accounts | NOT shared (your account only) | NOT shared (your physical server) |
| **Physical server visibility** | No | No | No | Yes (socket, core, host ID) |
| **BYOL support** | No | No | No | **Yes** |
| **Cost** | $$$$ (baseline) | $$ (up to 72% off) | $$$$+ (~10% premium) | $$$$$ (most expensive) |
| **Use case** | Flexibility | Predictable savings | Compliance isolation | License compliance (per-socket/core) |

### When Each Makes Sense — Real Examples

**On-Demand:**
> "We're load-testing for 3 days, then we're done." → Spin up 20 instances, tear them down Friday.

**Reserved Instance:**
> "Our production database cluster runs 24/7/365 on r6g.2xlarge." → Buy a 3-year RI, save 60%.

**Dedicated Instance:**
> "Our healthcare compliance officer says we can't share physical hardware with other companies, but we don't care about specific servers." → Dedicated Instance satisfies the audit.

**Dedicated Host:**
> "We have 500 Windows Server licenses tied to physical cores. We need to track which cores they run on." → Dedicated Host gives you the physical socket/core mapping for license audits.

### Memory Trick — The Isolation Spectrum

```
Most shared ←————————————————————————————→ Most isolated

On-Demand    Reserved Instance    Dedicated Instance    Dedicated Host
(shared HW)   (shared HW)        (your account only)   (your physical box)
(no commit)   (1-3yr commit)     (no core visibility)  (full core visibility)
(flexible)    (cheapest steady)  (compliance)          (BYOL licensing)
```

---

## Part 3: Common Exam / Interview Traps

| Question Pattern | Answer | Why People Get It Wrong |
|-----------------|--------|------------------------|
| "Which gives visibility into physical sockets?" | Dedicated Host | People confuse Dedicated Instance (no socket visibility) with Dedicated Host |
| "Which is needed for BYOL?" | Dedicated Host | Dedicated Instance gives isolation but NOT core/socket mapping needed for licenses |
