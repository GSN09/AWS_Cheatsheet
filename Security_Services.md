# AWS Security, Identity & Compliance Services — When to Use Guide

## Service Comparison Matrix

| Service | Primary Use Case | Category | Scope | Typical Workloads |
|---------|-----------------|----------|-------|-------------------|
| **AWS IAM** | Fine-grained access control for AWS resources | Identity & Access Management | Global | User/role permissions, service-to-service auth, policy enforcement, least-privilege access |
| **Amazon Cognito** | User authentication and identity for applications | Application Identity | Regional | Mobile/web app sign-up/sign-in, social login federation, OAuth2/OIDC token management |
| **AWS Directory Service** | Managed Microsoft Active Directory in AWS | Directory & Federation | Regional | AD-dependent workloads, SSO for Windows, domain-joined EC2, LDAP applications |
| **AWS Organizations** | Multi-account management and governance | Account Governance | Global | SCPs, consolidated billing, account provisioning, organizational policy enforcement |
| **AWS RAM** | Share resources across AWS accounts | Resource Sharing | Regional | Cross-account subnet sharing, Transit Gateway sharing, License Manager configs |
| **AWS KMS** | Managed encryption key creation and control | Encryption & Key Management | Regional | Data encryption at rest, envelope encryption, key rotation, CMK management |
| **AWS CloudHSM** | Dedicated hardware security modules | Encryption & Key Management | Regional (VPC) | FIPS 140-2 Level 3 compliance, SSL/TLS offload, custom key stores, PKI |
| **AWS Secrets Manager** | Rotate and manage secrets (passwords, API keys) | Secrets Management | Regional | Database credentials rotation, API key storage, cross-account secret sharing |
| **AWS Certificate Manager** | Provision and manage SSL/TLS certificates | Certificate Management | Regional / Global (CloudFront) | HTTPS endpoints, ALB/NLB/CloudFront TLS, auto-renewal of public certs |
| **Amazon GuardDuty** | Intelligent threat detection from logs | Threat Detection | Regional (multi-region recommended) | Compromised instances, unauthorized access, cryptocurrency mining, recon detection |
| **Amazon Inspector** | Automated vulnerability scanning | Vulnerability Management | Regional | EC2 CVE scanning, ECR container image scanning, Lambda code scanning |
| **Amazon Detective** | Investigate and analyze security findings | Security Investigation | Regional | Root cause analysis of GuardDuty findings, visual security investigation |
| **Amazon Macie** | Discover and protect sensitive data in S3 | Data Protection | Regional | PII discovery, sensitive data classification, S3 bucket security posture |
| **AWS Security Hub** | Centralized security findings and compliance checks | Security Posture Management | Regional (cross-region aggregation) | Aggregated findings from GuardDuty/Inspector/Macie, CIS/PCI compliance dashboards |
| **AWS Audit Manager** | Continuous audit evidence collection | Compliance & Audit | Regional | SOC 2, PCI-DSS, HIPAA audit prep, evidence automation, control mapping |
| **AWS Artifact** | Access AWS compliance reports and agreements | Compliance Documentation | Global | SOC reports, ISO certifications, BAA/NDA agreements, third-party audits |
| **AWS WAF** | Web application firewall (Layer 7) | Perimeter Protection | Regional / Global (CloudFront) | SQL injection blocking, XSS prevention, rate limiting, bot control, geo-blocking |
| **AWS Shield** | DDoS protection | Perimeter Protection | Global | Volumetric/protocol/application-layer DDoS mitigation, SRT access (Advanced) |
| **AWS Network Firewall** | Managed network firewall (Layer 3–7) | Network Protection | Regional (VPC) | Stateful packet inspection, IDS/IPS, domain filtering, Suricata-compatible rules |
| **AWS Firewall Manager** | Centrally manage firewall rules across accounts | Security Policy Management | Global (via Organizations) | WAF, Shield, Network Firewall, Security Group policies across all accounts |

## Decision Criteria

| Decision Factor | Recommended Service |
|----------------|-------------------|
| Need to control who can access which AWS resources | **AWS IAM** |
| Need user sign-up/sign-in for a web or mobile app | **Amazon Cognito** |
| Need managed Active Directory for Windows workloads | **AWS Directory Service** |
| Need to govern multiple AWS accounts with policies | **AWS Organizations** |
| Need to share VPC subnets or resources across accounts | **AWS RAM** |
| Need to encrypt data with managed keys | **AWS KMS** |
| Need FIPS 140-2 Level 3 compliant key storage | **AWS CloudHSM** |
| Need to store and auto-rotate database passwords | **AWS Secrets Manager** |
| Need free, auto-renewing SSL/TLS certificates | **AWS Certificate Manager** |
| Need continuous threat detection from VPC/DNS/CloudTrail logs | **Amazon GuardDuty** |
| Need automated vulnerability scanning for EC2/containers/Lambda | **Amazon Inspector** |
| Need to investigate the root cause of a security finding | **Amazon Detective** |
| Need to find PII or sensitive data in S3 buckets | **Amazon Macie** |
| Need a single dashboard for all security findings + compliance | **AWS Security Hub** |
| Need to automate audit evidence collection | **AWS Audit Manager** |
| Need to download AWS compliance certifications (SOC, ISO) | **AWS Artifact** |
| Need to block malicious HTTP requests (SQLi, XSS, bots) | **AWS WAF** |
| Need always-on DDoS protection with SRT support | **AWS Shield** |
| Need stateful network inspection and IDS/IPS in a VPC | **AWS Network Firewall** |
| Need to enforce WAF/Shield/firewall rules across all accounts | **AWS Firewall Manager** |

## Security Layers Architecture

| Layer | Service | What It Protects |
|-------|---------|-----------------|
| **Edge / Perimeter** | CloudFront + AWS WAF + AWS Shield | DDoS, bot traffic, malicious HTTP requests |
| **Network** | VPC + Network Firewall + Security Groups + NACLs | Unauthorized network access, lateral movement |
| **Identity** | IAM + Cognito + Directory Service + Organizations | Unauthorized users, excessive permissions |
| **Data** | KMS + CloudHSM + Macie + Secrets Manager + ACM | Data exposure, unencrypted data, leaked secrets |
| **Detection** | GuardDuty + Inspector + Detective + Security Hub | Threats, vulnerabilities, compromised resources |
| **Governance** | Organizations + Audit Manager + Artifact + Firewall Manager | Compliance gaps, policy drift, audit failures |

## Encryption Services Comparison

| Criteria | AWS KMS | AWS CloudHSM | AWS Secrets Manager |
|----------|---------|--------------|---------------------|
| **Purpose** | Managed encryption keys | Dedicated HSM appliances | Store/rotate secrets |
| **Key ownership** | AWS manages HSM, you manage keys | You own and manage HSMs | Secrets (not raw keys) |
| **FIPS 140-2** | Level 2 (some Level 3) | Level 3 | N/A (uses KMS internally) |
| **Pricing** | Per key + per API call | Per HSM-hour ($$$) | Per secret + per API call |
| **Use case** | General encryption, S3/EBS/RDS | Regulatory compliance, custom key stores | Database creds, API tokens |
| **Auto-rotation** | Annual (optional) | Manual | Every 30/60/90 days (configurable) |

## Threat Detection & Response Flow

| Stage | Service | Function |
|-------|---------|----------|
| **Detect** | Amazon GuardDuty | Identifies threats from logs (VPC Flow, DNS, CloudTrail, S3, EKS) |
| **Scan** | Amazon Inspector | Finds software vulnerabilities (CVEs) in EC2, ECR, Lambda |
| **Classify** | Amazon Macie | Discovers sensitive data exposure in S3 |
| **Aggregate** | AWS Security Hub | Normalizes and prioritizes findings from all detection services |
| **Investigate** | Amazon Detective | Provides visual analysis and root cause for findings |
| **Respond** | EventBridge + Lambda/SSM | Automated remediation based on findings |

## WAF vs Shield vs Network Firewall

| Criteria | AWS WAF | AWS Shield | AWS Network Firewall |
|----------|---------|-----------|---------------------|
| **Layer** | 7 (HTTP/S) | 3/4/7 (DDoS) | 3–7 (stateful inspection) |
| **Protects** | ALB, CloudFront, API Gateway, AppSync | All AWS resources (Standard), specific (Advanced) | VPC traffic (east-west and north-south) |
| **Threats** | SQLi, XSS, bots, IP reputation | Volumetric DDoS, SYN floods, reflection attacks | Malware C2 domains, IDS/IPS signatures, protocol anomalies |
| **Custom rules** | Yes (rate, geo, regex, managed rule groups) | No (automated mitigation) | Yes (Suricata-compatible rules) |
| **Cost** | Per rule + per request | Standard: free / Advanced: $3,000/month + DRT | Per endpoint-hour + per GB processed |

## Cost Comparison (Relative)

| Service | Pricing Model | Relative Cost | Notes |
|---------|--------------|:-------------:|-------|
| **AWS IAM** | Free | Free | No charge for IAM itself |
| **Amazon Cognito** | Per MAU (monthly active user) | $ | First 50K MAU free |
| **AWS Directory Service** | Per hour (Small/Large/Enterprise) | $$–$$$ | Enterprise AD starts ~$0.15/hr |
| **AWS Organizations** | Free | Free | No charge |
| **AWS RAM** | Free | Free | No charge for sharing |
| **AWS KMS** | Per key ($1/mo) + per 10K requests | $ | Symmetric keys cheapest |
| **AWS CloudHSM** | Per HSM-hour (~$1.60/hr) | $$$$ | Min 2 for HA |
| **AWS Secrets Manager** | Per secret ($0.40/mo) + per 10K API calls | $ | Cheaper than manual rotation |
| **AWS Certificate Manager** | Free (public certs) | Free | Private CA costs $400/mo |
| **Amazon GuardDuty** | Per volume of logs analyzed | $–$$ | Volume discounts, 30-day free trial |
| **Amazon Inspector** | Per instance/image scanned | $ | Per assessment run |
| **Amazon Detective** | Per GB of data ingested | $$ | 30-day free trial |
| **Amazon Macie** | Per bucket evaluated + per GB scanned | $–$$ | First 1 GB/month free |
| **AWS Security Hub** | Per finding ingested + per compliance check | $ | 30-day free trial |
| **AWS Audit Manager** | Per resource assessment | $ | Pay per active assessment |
| **AWS Artifact** | Free | Free | Download compliance docs |
| **AWS WAF** | Per Web ACL + per rule + per request | $$ | Managed rule groups extra |
| **AWS Shield** | Standard: free / Advanced: $3,000/mo | Free–$$$$ | Advanced includes WAF credits + DRT |
| **AWS Network Firewall** | Per endpoint-hour + per GB | $$$ | ~$0.395/hr per endpoint |
| **AWS Firewall Manager** | Per policy per region | $$ | $100/policy/region/month |
