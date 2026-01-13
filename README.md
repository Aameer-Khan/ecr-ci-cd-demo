Title
AWS ECR CI/CD with Terraform and GitHub Actions (OIDC)

Overview

This project demonstrates a production-grade CI/CD workflow where Docker images are built and pushed to Amazon ECR using GitHub Actions and OIDC authentication. Infrastructure is provisioned using Terraform. No long-lived AWS credentials are used.

Architecture

Flow explained.

• Developer pushes code
• GitHub Actions runs CI
• OIDC authenticates to AWS
• Docker image built
• Image pushed to ECR
• ECR stores immutable images

Prerequisites

• AWS account
• GitHub account
• Terraform installed
• Docker installed
• IAM role configured for GitHub OIDC

Step 1. Provision ECR using Terraform

Commands.

cd terraform
terraform init
terraform apply


Result.

• ECR repository created
• Image scanning enabled
• Lifecycle policy applied

Step 2. Configure IAM Role for GitHub OIDC

High level steps.

• Create OIDC provider for GitHub
• Create IAM role
• Restrict by repo and branch
• Attach ECR permissions

Why this matters.

• No AWS keys
• Short-lived credentials
• Secure by default

Step 3. Configure GitHub Actions

Update workflow file.

Replace.

• <ACCOUNT_ID>
• <IAM_ROLE_ARN>

Commit to main branch.

Step 4. CI/CD in action

What happens on push.

Build step 1
• Checkout source

Build step 2
• Authenticate to AWS using OIDC

Build step 3
• Build Docker image

Build step 4
• Tag image with commit SHA

Build step 5
• Push image to ECR

Step 5. Verify

Check in AWS Console.

• ECR → Repositories
• Image with commit SHA tag

Security Highlights

• No static AWS credentials
• OIDC based authentication
• Least privilege IAM
• Immutable image tags

What this project proves (Interview section)

• Understanding of CI/CD pipelines
• Secure cloud authentication
• Terraform for infra automation
• Container lifecycle management
• Production DevOps practices

How to extend this project

• Deploy image to EKS
• Add Trivy image scanning
• Add Helm deployment
• Add multi-environment support

Final Notes

This repository is designed to be forked and reused. All sensitive values are placeholders and must be provided by the user.
