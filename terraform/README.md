# Terraform Configuration for ECR CI/CD Demo

This directory contains Terraform configuration to set up Amazon Elastic Container Registry (ECR) infrastructure for the CI/CD demo application.

## Overview

The Terraform configuration creates and manages an ECR repository with automated image lifecycle policies and security scanning.

## Prerequisites

- **Terraform**: >= 1.5.0
- **AWS Provider**: ~> 5.0
- **AWS Credentials**: Configured with appropriate permissions
- **AWS Region**: ap-south-1 (Asia Pacific - Mumbai)

## Files

- `main.tf` - Main Terraform configuration (ECR repository and lifecycle policy)
- `outputs.tf` - Output values exported from the configuration
- `terraform.tfvars` - Variables file (if using custom values)
- `.terraform/` - Terraform working directory (auto-generated)
- `terraform.tfstate` - State file (stores infrastructure state)

## Resources Created

### ECR Repository (`aws_ecr_repository.this`)

**Name**: `demo-app`

**Configuration**:
- **Image Tag Mutability**: IMMUTABLE - Prevents overwriting image tags once pushed
- **Image Scanning**: Enabled on push - Automatically scans images for vulnerabilities
- **Encryption**: AES256 - Server-side encryption for image data

### ECR Lifecycle Policy (`aws_ecr_lifecycle_policy.cleanup`)

**Purpose**: Automatic cleanup of old Docker images

**Policy Details**:
- Keeps the last 5 images
- Expires (deletes) older images automatically
- Reduces storage costs and keeps repositories clean

## Outputs

| Output | Description |
|--------|-------------|
| `ecr_repository_url` | The URL of the ECR repository for pushing/pulling images |

## Usage

### Initialize Terraform

```bash
cd terraform/
terraform init
```

### Plan Deployment

```bash
terraform plan
```

This shows what resources will be created or modified.

### Apply Configuration

```bash
terraform apply
```

Confirms and creates the resources in AWS.

### Get Outputs

```bash
terraform output
```

Retrieve the ECR repository URL:

```bash
terraform output ecr_repository_url
```

### Destroy Resources

```bash
terraform destroy
```

Removes all Terraform-managed resources from AWS.

## Configuration Details

### Provider Configuration

```terraform
provider "aws" {
  region = "ap-south-1"
}
```

Change the region if you need to deploy in a different AWS region. Update this before running `terraform apply`.

### State Management

By default, Terraform stores state locally in `terraform.tfstate`. For production:

1. **Remote State**: Use S3 backend for collaborative development
2. **State Locking**: Enable DynamoDB locking to prevent conflicts
3. **Version Control**: Never commit `terraform.tfstate` to git

Add to `main.tf`:

```terraform
terraform {
  backend "s3" {
    bucket         = "your-terraform-state-bucket"
    key            = "ecr-demo/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

## Security Considerations

1. **Image Scanning**: Enabled to detect vulnerabilities in pushed images
2. **Immutable Tags**: Prevents accidental or malicious image overwrites
3. **Encryption**: AES256 encryption protects image data at rest
4. **Lifecycle Policy**: Limits repository size and removes old images

## Integration with CI/CD

This ECR repository is used by the GitHub Actions workflow (`build-and-push-ecr.yml`) to store Docker images.

1. The workflow builds a Docker image locally
2. Tags it with the commit SHA
3. Pushes it to this ECR repository
4. The lifecycle policy automatically manages old images

## Troubleshooting

### Terraform Init Fails

- Ensure AWS credentials are configured: `aws configure`
- Verify AWS region is correct and accessible

### Apply Fails

- Check IAM permissions for ECR operations
- Verify the repository name `demo-app` isn't already in use

### State Issues

- Never manually edit `terraform.tfstate`
- Use `terraform state` commands for state management
- For recovery, use `terraform refresh` to sync state with AWS

## Useful Commands

```bash
# View current infrastructure
terraform show

# List resources in state
terraform state list

# View specific resource details
terraform state show aws_ecr_repository.this

# Format configuration files
terraform fmt

# Validate configuration
terraform validate

# Get module/provider information
terraform get
```

## Next Steps

1. Review the configuration and adjust region/settings as needed
2. Run `terraform plan` to preview changes
3. Run `terraform apply` to create resources
4. Use the ECR repository URL in your CI/CD workflow
5. Monitor images and lifecycle policy execution in AWS Console

## References

- [AWS ECR Terraform Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecr_repository)
- [ECR Lifecycle Policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html)
- [Terraform Documentation](https://www.terraform.io/docs)
