# AWS Cloud Engineering Roadmap

> **A structured, hands-on guide to AWS — tracking practical progress across AWS infrastructure, DevOps automation, container orchestration, and applied ML services.**

---

## About This Repository

This repository is a hands-on AWS Cloud Engineering roadmap. It shows **how to actually use AWS** — from spinning up your first EC2 instance to architecting production-ready, secure, and cost-optimized systems on the AWS platform.

Every topic here is about real usage, not theory: how to configure AWS services, wire them together, automate them, and operate them the way cloud teams do in production.

**What you'll find here:**
- **Topic-specific learning tracks** — organized by AWS domain (Compute, Networking, Security, DevOps, and more)
- **Hands-on labs & configurations** — Terraform modules, Kubernetes manifests, CI/CD pipelines, and security policies built directly on AWS
- **Architecture notes & diagrams** — documenting how AWS services fit together and the trade-offs behind each design
- **Project documentation** — real-world AWS scenarios, not tutorial walkthroughs

---

## How to Use This Roadmap

Each AWS folder is a step on the roadmap. Follow them in order or jump to the service you need:

```text
AWS Cloud Engineering Roadmap/
├── Compute/                    # How to use EC2, Auto Scaling, Load Balancers, Lambda
├── Databases/                  # How to use RDS, DynamoDB — data modeling & migration
├── DevOps/                     # How to automate with CI/CD pipelines, GitHub Actions, CodePipeline
├── Kubernetes/                 # How to run containers on EKS — manifests, Helm charts, service mesh
├── Machine Learning/           # How to integrate AWS ML services into your workflows
│   ├── Amazon Bedrock/         # How to use foundation models via AWS Bedrock
│   ├── Amazon Lex/             # How to build conversational AI & chatbots on AWS
│   └── Amazon Transcribe/      # How to build speech-to-text pipelines on AWS
├── Networking & Content Delivery/
│   ├── Amazon VPC/             # How to design subnets, routing, and VPC peering
│   └── Three-Tier/             # How to build a classic 3-tier architecture on AWS
├── Security/                   # How to secure AWS — IAM, encryption, compliance, threat modeling
└── Terraform/                  # How to manage AWS infrastructure as code — modules & state
```

---

## What You'll Learn to Do on AWS

Each folder shows concrete, working examples of AWS in action. As you progress, you'll learn to:

- **Provision AWS infrastructure** — reproducible, version-controlled setups with Terraform
- **Design AWS architectures** — VPC layouts, 3-tier stacks, and EKS clusters, drawn out and explained
- **Automate on AWS** — Bash, Python, and CI/CD workflows that deploy and manage AWS resources
- **Secure AWS environments** — least-privilege IAM policies, KMS encryption, AWS Config compliance checks
- **Integrate AWS AI services** — Bedrock, Lex, and Transcribe embedded into real cloud workflows
- **Document your work** — problem statements, technical decisions, and lessons learned for every project

---

## AWS Skills Covered

| AWS Domain | Key Services & Tools |
|---|---|
| **Cloud Infrastructure** | EC2, S3, Lambda, CloudFormation, Cost Optimization |
| **Networking** | VPC, Subnets, Route 53, ALB/NLB, CloudFront |
| **Security** | IAM Policies, KMS, AWS Config, Security Groups, WAF |
| **Infrastructure as Code** | Terraform on AWS — state management, modules, workspaces |
| **DevOps & CI/CD** | GitHub Actions, Docker, AWS CodePipeline, CloudWatch monitoring |
| **Container Orchestration** | Amazon EKS, Kubernetes, Helm, Horizontal Pod Autoscaling |
| **Applied ML on AWS** | Amazon Bedrock, Amazon Lex, Amazon Transcribe |

---

## Why This Matters for Hiring

> Recruiters and technical reviewers spend **5–10 minutes** reviewing a portfolio. This roadmap is designed to answer three questions fast:
> 1. **Can you use AWS?** → Service-by-service, hands-on coverage of the platform
> 2. **Can you build real things on AWS?** → Working Terraform, K8s manifests, and architecture docs
> 3. **How do you organize technical work?** → Consistent structure, meaningful commits, living documentation

---

## Connect

> *This is a living repository — updated regularly as I build new AWS skills and complete hands-on projects.*

If you're a recruiter, hiring manager, or fellow cloud engineer, feel free to explore any section. This roadmap is my proof of progress toward real AWS cloud engineering work.
