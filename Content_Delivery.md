# AWS Networking & Content Delivery Services — When to Use Guide

## Service Comparison Matrix

| Service | Primary Use Case | Category | Scope | Protocol/Layer | Typical Workloads |
|---------|-----------------|----------|-------|----------------|-------------------|
| **Amazon CloudFront** | Global content delivery network (CDN) | Content Delivery | Global (400+ edge locations) | HTTP/S, WebSocket, RTMP | Static/dynamic website acceleration, video streaming, API caching, software distribution |
| **Amazon Route 53** | Scalable DNS and domain management | DNS & Traffic Routing | Global | DNS (UDP/TCP port 53) | Domain registration, health-check failover, geolocation routing, latency-based routing |
| **Amazon API Gateway** | Managed API endpoint for backend services | API Management | Regional / Edge-optimized | HTTP/S, REST, WebSocket | Serverless APIs (Lambda), microservices facades, rate limiting, API versioning |
| **Amazon VPC** | Isolated virtual network in AWS | Networking (Foundation) | Regional | All (L3/L4) | Network isolation, subnets, security groups, NACLs, private connectivity |
| **AWS Direct Connect** | Dedicated private network link to AWS | Hybrid Connectivity | Point-to-point (on-prem ↔ AWS) | Ethernet (1/10/100 Gbps) | Consistent low-latency hybrid apps, large data transfers, regulatory compliance |
| **AWS Elastic Load Balancing (ELB)** | Distribute traffic across targets | Load Balancing | Regional (cross-AZ) | HTTP/S (ALB), TCP/UDP (NLB), Classic | Web app scaling, microservices routing, TLS termination, gRPC/WebSocket balancing |
| **AWS Global Accelerator** | Accelerate global traffic via AWS backbone | Traffic Acceleration | Global (Anycast IPs) | TCP, UDP | Multi-region active-active, gaming, IoT, static IP requirements, instant failover |
| **AWS Transit Gateway** | Central hub connecting VPCs and on-prem networks | Network Connectivity | Regional (peering cross-region) | All (L3 routing) | Multi-VPC architectures, shared services VPC, hub-and-spoke topologies, cross-account networking |

## Decision Criteria

| Decision Factor | Recommended Service |
|----------------|-------------------|
| Need to cache and accelerate content at the edge globally | **Amazon CloudFront** |
| Need DNS resolution, domain management, or traffic policies | **Amazon Route 53** |
| Need a managed front door for REST/WebSocket APIs | **Amazon API Gateway** |
| Need an isolated network with subnets, routing, and security controls | **Amazon VPC** |
| Need a private, dedicated connection from on-prem to AWS | **AWS Direct Connect** |
| Need to distribute traffic across EC2, containers, or Lambda in a region | **AWS ELB** |
| Need static Anycast IPs and instant global failover | **AWS Global Accelerator** |
| Need to connect multiple VPCs and on-prem networks through a central hub | **AWS Transit Gateway** |

## ELB Type Selection Guide

| Load Balancer | Layer | Best For | Key Features |
|---------------|:-----:|----------|--------------|
| **Application Load Balancer (ALB)** | 7 | HTTP/HTTPS, gRPC, WebSocket | Path/host routing, weighted targets, Lambda targets, OIDC auth |
| **Network Load Balancer (NLB)** | 4 | Ultra-low latency TCP/UDP/TLS | Static IPs, millions of requests/sec, PrivateLink support |
| **Gateway Load Balancer (GWLB)** | 3 | Inline network appliances | Transparent inspection, firewalls, IDS/IPS appliance chaining |
| **Classic Load Balancer (CLB)** | 4/7 | Legacy EC2-Classic (avoid for new) | Basic L4/L7, being deprecated |

## CloudFront vs Global Accelerator

| Criteria | CloudFront | Global Accelerator |
|----------|-----------|-------------------|
| **Primary purpose** | Cache and deliver content | Accelerate TCP/UDP traffic |
| **Caching** | Yes (edge caching) | No (proxies, does not cache) |
| **Protocol** | HTTP/S only | TCP and UDP (any port) |
| **Static IPs** | No (uses DNS CNAME) | Yes (2 Anycast IPs) |
| **Failover speed** | DNS TTL-dependent | Instant (< 30s) |
| **Best for** | Websites, media, APIs with cacheable responses | Gaming, VoIP, IoT, multi-region active-active |

## Route 53 Routing Policies

| Policy | Use Case | How It Works |
|--------|----------|--------------|
| **Simple** | Single resource, no health checks | Returns one record |
| **Weighted** | A/B testing, blue/green deployments | Distributes by assigned weight |
| **Latency-based** | Global apps, lowest latency | Routes to lowest-latency region |
| **Failover** | Active-passive DR | Switches on health check failure |
| **Geolocation** | Compliance, localized content | Routes by user location |
| **Geoproximity** | Fine-tuned geographic routing | Routes by proximity with bias |
| **Multivalue Answer** | Simple load balancing across IPs | Returns multiple healthy IPs |

## Service Interaction Patterns

| Architecture Pattern | Services Involved |
|---------------------|-------------------|
| Global web application | Route 53 → CloudFront → ALB → EC2/ECS |
| Multi-region active-active | Route 53 → Global Accelerator → NLB (per region) → targets |
| Serverless API | Route 53 → CloudFront → API Gateway → Lambda |
| Hybrid connectivity | On-prem → Direct Connect → Transit Gateway → VPCs |
| Hub-and-spoke multi-VPC | Transit Gateway (hub) ↔ Spoke VPCs + VPN/Direct Connect |
| Network appliance inspection | GWLB → firewall/IDS appliances → target VPCs |

## Cost Comparison (Relative)

| Service | Pricing Model | Relative Cost | Notes |
|---------|--------------|:-------------:|-------|
| **Amazon CloudFront** | Per GB out + per request | $–$$ | Free tier: 1 TB/month, volume discounts available |
| **Amazon Route 53** | Per hosted zone + per query | $ | $0.50/zone/month, $0.40 per 1M queries |
| **Amazon API Gateway** | Per request + data transfer | $$ | REST: $3.50/1M requests, HTTP APIs cheaper |
| **Amazon VPC** | Free (data transfer charges apply) | $ | VPC itself is free; NAT Gateway, endpoints cost extra |
| **AWS Direct Connect** | Port-hour + data out | $$$ | 1 Gbps ~$0.30/hr, lower egress vs internet |
| **AWS ELB** | Per hour + per LCU (ALB) or NLCU (NLB) | $$ | ALB ~$0.0225/hr + LCU, NLB ~$0.0225/hr + NLCU |
| **AWS Global Accelerator** | Per hour + per GB (premium) | $$$ | $0.025/hr per accelerator + DT premium |
| **AWS Transit Gateway** | Per attachment-hour + per GB processed | $$ | $0.05/hr per attachment + $0.02/GB |
