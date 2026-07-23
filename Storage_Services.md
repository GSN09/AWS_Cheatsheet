# AWS Storage Services — When to Use Guide

## Service Comparison Matrix

| Service | Primary Use Case | Storage Type | Access Pattern | Durability | Typical Workloads |
|---------|-----------------|--------------|----------------|------------|-------------------|
| **Amazon EBS** | Block storage for EC2 instances | Block | Low-latency, single-instance attach (multi-attach for io2) | 99.999% | Databases, boot volumes, transactional workloads, high-IOPS applications |
| **Amazon EFS** | Shared file system across multiple instances | File (NFS) | Concurrent access from multiple EC2/containers | 99.999999999% (11 9s) | Content management, web serving, shared development environments, ML training data |
| **Amazon FSx** | Managed third-party file systems | File (SMB, Lustre, ONTAP, OpenZFS) | High-performance, protocol-specific | 99.999999999% (11 9s) | Windows workloads (FSx for Windows), HPC (Lustre), multi-protocol (ONTAP), Linux migrations (OpenZFS) |
| **Amazon S3** | General-purpose object storage | Object | HTTP/S API, any access pattern | 99.999999999% (11 9s) | Data lakes, backups, static websites, media hosting, analytics, application assets |
| **Amazon S3 Glacier** | Long-term archival storage | Object (archive) | Infrequent retrieval (minutes to hours) | 99.999999999% (11 9s) | Compliance archives, digital preservation, long-term backups, regulatory data retention |
| **AWS Backup** | Centralized backup management | Backup orchestration | Scheduled/on-demand backup and restore | Inherits from target service | Cross-service backup policies, compliance audits, disaster recovery orchestration |
| **AWS Storage Gateway** | Hybrid cloud storage bridge | File/Block/Tape (cached or stored) | On-premises applications accessing cloud storage | Inherits from S3/EBS | Lift-and-shift, backup-to-cloud, tiered storage, on-prem file shares backed by S3 |
| **AWS Transfer Family** | Managed file transfers via SFTP/FTPS/FTP/AS2 | File transfer protocol layer | Partner/customer file exchanges | Inherits from S3/EFS | B2B file exchanges, legacy SFTP migrations, partner data ingestion, EDI workflows |
| **AWS Snowball Edge** | Offline bulk data transfer (petabyte scale) | Physical appliance | Ship device to/from AWS | Inherits from S3 after import | Data center migrations (10–80 TB per device), edge computing in disconnected environments |
| **AWS Snowmobile** | Exabyte-scale data transfer | Physical shipping container | Ship container to/from AWS | Inherits from S3 after import | Full data center migrations (up to 100 PB per Snowmobile), massive one-time transfers |

## Decision Criteria

| Decision Factor | Recommended Service |
|----------------|-------------------|
| Need block storage for a database on EC2 | **Amazon EBS** |
| Need shared file access across multiple EC2 instances (Linux/NFS) | **Amazon EFS** |
| Need Windows-compatible shared file system (SMB) | **Amazon FSx for Windows File Server** |
| Need high-performance computing file system | **Amazon FSx for Lustre** |
| Need scalable object storage for unstructured data | **Amazon S3** |
| Need cheapest possible storage for rarely accessed archives | **Amazon S3 Glacier** |
| Need to centrally manage backups across AWS services | **AWS Backup** |
| Need on-premises apps to access cloud storage transparently | **AWS Storage Gateway** |
| Need managed SFTP/FTPS endpoint for file exchanges | **AWS Transfer Family** |
| Need to migrate 10–80 TB offline with no network dependency | **AWS Snowball Edge** |
| Need to migrate 10+ PB offline in a single operation | **AWS Snowmobile** |

## Cost Comparison (Relative)

| Service | Storage Cost | Access/Transfer Cost | Best For Budget |
|---------|-------------|---------------------|-----------------|
| **Amazon EBS** | $$ | Low (same-AZ) | Persistent instance storage |
| **Amazon EFS** | $$$ (Standard), $ (IA tier) | Per-request on IA | Shared but variable-access workloads |
| **Amazon FSx** | $$–$$$$ (varies by type) | Throughput-based | Protocol-specific workloads |
| **Amazon S3** | $ (Standard), ¢ (IA/One Zone) | Per-request + egress | High-scale, cost-effective storage |
| **Amazon S3 Glacier** | ¢ | $$ per retrieval | Store cheaply, retrieve rarely |
| **AWS Backup** | Priced per underlying storage | Restore fees | Consolidated policy management |
| **AWS Storage Gateway** | S3 pricing + gateway instance | Cache + transfer | Gradual cloud migration |
| **AWS Transfer Family** | Per-protocol endpoint + transfer | Per GB transferred | Managed protocol endpoints |
| **AWS Snowball Edge** | Per-device rental + shipping | Import to S3 free | Large offline migrations |
| **AWS Snowmobile** | Per-device rental + shipping | Import to S3 free | Massive offline migrations |
