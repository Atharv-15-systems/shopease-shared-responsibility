# ShopEase — 3-Tier Web Application: Shared Responsibility Mapping

## Overview
This repository documents the architecture and Shared Responsibility Matrix for **ShopEase**, a fictional e-commerce web application deployed on AWS using a classic 3-tier design (Web / App / DB).

The goal of this assignment is to demonstrate understanding of the **AWS Shared Responsibility Model** by mapping it directly onto a specific, real architecture rather than repeating AWS's generic diagram.

## Repository Structure
```
├── README.md                          # This file
├── architecture.md                    # Architecture diagram + tier breakdown
├── shared-responsibility-matrix.md    # Control-by-control responsibility mapping
└── docs/
    └── design-rationale.md            # Why each control was assigned this way
```

## Application Summary
| Attribute | Detail |
|---|---|
| Application | ShopEase (e-commerce web app) |
| Cloud Provider | AWS |
| Tiers | Web, Application, Database |
| Deployment Model | IaaS/PaaS hybrid (EC2 + managed RDS) |

## How to Use This Repo
1. Start with `architecture.md` to understand the system design.
2. Review `shared-responsibility-matrix.md` for the control-by-control breakdown.
3. Read `docs/design-rationale.md` for the reasoning behind each responsibility assignment.

## Key Takeaway
Even within a single application, responsibility shifts **per component**: unmanaged EC2 instances push more responsibility onto the customer (OS patching, hardening), while managed services like RDS shift infrastructure and engine-patching responsibility to AWS. This repo shows that split explicitly rather than treating "shared responsibility" as a single blanket answer for the whole app.
