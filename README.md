![Terraform](https://img.shields.io/badge/Terraform-%3E%3D1.0-blue)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-Enabled-green)

# AWS ECR CI/CD with Terraform & GitHub Actions (OIDC)

Lightweight example showing how to build Docker images in GitHub Actions and push them to Amazon ECR using OIDC authentication — provisioned with Terraform.

---

**Table of Contents**

- [About](#about)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [GitHub Actions (OIDC) Setup](#github-actions-oidc-setup)
- [Verify](#verify)
- [Extending This Project](#extending-this-project)
- [Notes](#notes)

---

## About

This repository demonstrates a secure CI/CD flow using GitHub Actions with OIDC to authenticate to AWS and push Docker images to Amazon ECR. No long-lived AWS keys are required.

## Features

- Build Docker images in GitHub Actions
- Push images to Amazon ECR with OIDC
- Terraform for provisioning ECR and IAM resources
- Image scanning and lifecycle policy (configurable)

## Architecture

![Architecture diagram](https://github.com/user-attachments/assets/93faa46a-4f01-40d5-b879-23b66e37e97b)

High-level flow: developer push → GitHub Actions → authenticate via OIDC → build & tag image → push to ECR

## Prerequisites

- An AWS account with permissions to create ECR and IAM resources
- A GitHub repository
- Terraform installed (recommended >= 1.0)
- Docker installed locally (for local image builds)

## Quick Start

1. Initialize Terraform and apply to create ECR resources:

```bash
terraform init
terraform apply -auto-approve
```

2. Note the created ECR repository URI from the Terraform outputs (see `output.tf`).

3. Configure your GitHub Actions workflow (replace placeholders):

- `ACCOUNT_ID` – your AWS account ID
- `IAM_ROLE_ARN` – the IAM role ARN created for GitHub OIDC

```yaml
# Example (in .github/workflows/ci.yml)
- name: Publish to ECR
	uses: aws-actions/amazon-ecr-login@v1
# ... build, tag, push steps
```

## GitHub Actions (OIDC) Setup

High-level steps:

1. Create a GitHub OIDC provider in AWS.
2. Create an IAM role that trusts GitHub OIDC and restricts it to your repo/branch.
3. Attach minimum ECR permissions to the role (CreateRepository, PutImage, InitiateLayerUpload, CompleteLayerUpload, PutImageScanningConfiguration).

Why OIDC?

- Eliminates long-lived AWS credentials in CI
- Provides short-lived, secure tokens
- Enables least-privilege access

## Verify

- After a successful workflow run, open the AWS Console → ECR → Repositories and confirm the pushed image tagged with the commit SHA.

## Extending This Project

- Deploy images to EKS or ECS
- Add additional scanning (Trivy, Clair)
- Add multi-environment workflows (dev/stage/prod)

## Notes

- This repository is intentionally generic — no credentials or secrets are stored here.
- Replace all placeholders before use.

---


