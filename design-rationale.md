# Design Rationale — ShopEase Responsibility Assignments

This document explains *why* each major responsibility assignment in `shared-responsibility-matrix.md` was made the way it was.

## Why EC2 pushes more responsibility to the customer
The web and app tiers run on **unmanaged EC2 instances**. AWS's responsibility stops at the hypervisor — everything above that (OS, installed software, patches, runtime, application code) is the customer's job. This is the core trade-off of IaaS: more control, more responsibility.

**Design choice:** We used EC2 for the web/app tiers instead of a fully-managed PaaS (like Elastic Beanstalk or Fargate-only) because the assignment wanted a "classic" 3-tier design where the responsibility split is easy to observe and explain. If ShopEase used ECS Fargate instead, the OS-patching row would shift from "You" to "AWS," since Fargate abstracts away the underlying host.

## Why RDS shifts responsibility to AWS
RDS is a **managed service** — AWS handles the underlying OS, database engine installation, and patching. The customer only interacts with the database at the schema/query/access-control level. This is why rows 7–8 in the matrix are AWS's responsibility, while row 9 (schema/access control) remains ours — RDS doesn't stop us from writing an insecure schema or over-privileging a DB user.

## Why encryption is "Shared" rather than one-sided
AWS provides the *mechanism* for encryption (KMS, TLS support on ALB/CloudFront), but does not enable it by default in most cases, nor does it choose key rotation policies. A customer who never turns on RDS encryption at rest has AWS's tooling available but has not fulfilled their side of the responsibility. This is a common real-world misconfiguration point, so it's called out explicitly as "Shared" rather than assumed.

## Why network segmentation is entirely the customer's job
AWS provides the VPC primitive, but the actual **design decisions** — which subnets are public vs. private vs. isolated, what Security Group rules exist, how the DB tier is walled off from the internet — are 100% customer-driven. This is reflected in the architecture diagram: the DB subnet has no route to the Internet Gateway at all, which was a deliberate design choice, not something AWS enforces automatically.

## Why DDoS protection is split into two rows
AWS Shield Standard automatically protects against common infrastructure-layer DDoS attacks (SYN floods, reflection attacks) with no configuration needed — that's AWS's responsibility. But protecting against **application-layer** abuse (e.g., credential stuffing against the login endpoint, scraping) requires the customer to write and tune WAF rules specific to ShopEase's own attack surface. Treating "DDoS protection" as a single row would obscure this distinction.

## What this exercise demonstrates
The Shared Responsibility Model isn't a fixed line — it moves depending on **which AWS service you choose for each component**. The same application (ShopEase) has a different responsibility profile at the web tier (mostly ours) than at the database tier (mostly AWS's), purely because of the service choice (EC2 vs. RDS). Documenting this per-component, rather than as one blanket statement, is the actual point of the assignment.
