# Shared Responsibility Matrix — ShopEase

This matrix maps AWS's Shared Responsibility Model onto the specific components used in the ShopEase architecture (see `architecture.md`). Responsibility is assessed **per component**, not as a single blanket statement for the whole app.

Legend: **AWS** = AWS's responsibility | **You** = Customer's responsibility | **Shared** = Both parties have a role

| # | Control Area | Component(s) | Responsible Party | Notes |
|---|---|---|---|---|
| 1 | Physical data center security | All | **AWS** | Facilities, power, physical access controls |
| 2 | Hypervisor / host infrastructure | EC2 (web/app tiers) | **AWS** | AWS secures and patches the virtualization layer |
| 3 | Global network infrastructure | All | **AWS** | Backbone routers, edge locations, core network |
| 4 | Guest OS patching | EC2 web & app servers | **You** | You choose the AMI and must patch the OS yourself (unmanaged EC2) |
| 5 | Web server software (Nginx) config & patching | Web tier | **You** | Installed and maintained by the customer on EC2 |
| 6 | Application code security | App tier | **You** | Input validation, dependency updates, secure coding practices |
| 7 | Database engine patching | RDS PostgreSQL | **AWS** | Managed service — AWS applies engine patches (customer schedules maintenance window) |
| 8 | Database OS patching | RDS PostgreSQL | **AWS** | Underlying OS is fully abstracted from customer in RDS |
| 9 | Database schema, queries, access control | RDS PostgreSQL | **You** | Table design, least-privilege DB users, query security |
| 10 | Network ACLs / Security Groups | VPC (all tiers) | **You** | You define subnet segmentation and SG rules shown in the diagram |
| 11 | VPC design & subnet isolation | VPC | **You** | Public/private/isolated subnet layout is a customer design decision |
| 12 | IAM users, roles, and policies | All | **You** | Least-privilege role design, MFA enforcement, key rotation |
| 13 | Data encryption at rest | EBS, RDS, S3 | **Shared** | AWS provides the encryption mechanism (KMS); **you** must enable it and manage key policies |
| 14 | Data encryption in transit | ALB, CloudFront | **Shared** | AWS provides TLS termination capability; **you** must configure/enforce HTTPS-only and valid certs |
| 15 | DDoS protection (infrastructure layer) | CloudFront, ALB | **AWS** | AWS Shield Standard is automatic and free |
| 16 | DDoS protection (application layer) | WAF rules | **You** | You must write/tune WAF rules for app-specific threats (e.g., login brute force) |
| 17 | Backup configuration & retention | RDS, S3 | **You** | AWS provides the backup *feature*; you configure retention windows and verify restores |
| 18 | Backup infrastructure durability | RDS, S3 | **AWS** | AWS guarantees the durability of stored backup data |
| 19 | Auto Scaling configuration | Web & App ASGs | **You** | Scaling policies, thresholds, and health checks are customer-defined |
| 20 | Monitoring & alerting setup | CloudWatch | **You** | AWS provides the service; you configure alarms, dashboards, log retention |
| 21 | Compliance certifications (e.g., ISO, SOC 2) of underlying infrastructure | All | **AWS** | AWS maintains and provides audit reports (via AWS Artifact) |
| 22 | Compliance of the *application* (e.g., PCI-DSS scope for payment data) | App/DB tier | **You** | You are responsible for how you handle/store customer payment data |
| 23 | Client-side security (end user device/browser) | N/A | **Neither** | Outside both parties' control, though app should follow secure coding practices to mitigate |

## Summary by Tier

| Tier | AWS-Managed Share | Customer-Managed Share |
|---|---|---|
| Web (EC2-based) | Low (hardware, hypervisor, network backbone) | High (OS, web server, patching, config) |
| App (EC2/ECS-based) | Low–Medium | High (code, IAM, scaling, monitoring) |
| DB (RDS-managed) | High (OS, engine patching, HA infra) | Medium (schema, access control, query security, encryption enablement) |

**Conclusion:** Moving from unmanaged EC2 (web/app tiers) to a managed service (RDS) measurably shifts operational burden toward AWS — but network design, access control, encryption configuration, and data governance remain the customer's responsibility across every tier.
