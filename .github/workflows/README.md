# GitHub Actions Workflows

## Build and Push Docker Image to ECR

This workflow automates the process of building a Docker image and pushing it to Amazon Elastic Container Registry (ECR).

### Trigger

- **Event**: Push to the `main` branch
- **Runs on**: Ubuntu latest

### Workflow Steps

1. **Checkout source code**
   - Retrieves the latest code from the repository

2. **Configure AWS credentials using OIDC**
   - Authenticates with AWS using OpenID Connect (OIDC)
   - Uses the specified IAM role for secure credential handling
   - Region: `ap-south-1` (Asia Pacific - Mumbai)

3. **Login to Amazon ECR**
   - Authenticates with Amazon ECR using AWS credentials

4. **Build Docker image**
   - Builds a Docker image tagged as `demo-app`

5. **Tag Docker image**
   - Tags the Docker image with the commit SHA for version tracking
   - Target: `<ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/demo-app:${{ github.sha }}`

6. **Push Docker image to ECR**
   - Pushes the tagged Docker image to Amazon ECR

### Prerequisites

Before using this workflow, you must:

1. **Set up an IAM Role for OIDC**
   - Create an IAM role that trusts the GitHub OIDC provider
   - Attach permissions for ECR operations (push/pull)

2. **Replace Configuration Values**
   - Replace `<REPLACE_WITH_IAM_ROLE_ARN>` with your actual IAM role ARN
   - Replace `<ACCOUNT_ID>` with your AWS account ID in both the tag and push commands

3. **Create an ECR Repository**
   - Create a repository named `demo-app` in Amazon ECR

### Permissions Required

The workflow uses GitHub's OIDC token for authentication:
- `id-token: write` - Allows generating OIDC tokens
- `contents: read` - Allows reading repository contents

### Environment Variables

- `AWS_REGION`: `ap-south-1`
- `IMAGE_NAME`: `demo-app`

### Example Configuration

To fully configure this workflow:

```yaml
# Replace in the workflow file:
role-to-assume: arn:aws:iam::123456789012:role/github-actions-ecr-role
# And in the tag/push commands:
123456789012.dkr.ecr.ap-south-1.amazonaws.com/demo-app:${{ github.sha }}
```

### Output

Upon successful execution:
- Docker image is built locally
- Image is tagged with commit SHA for traceability
- Image is pushed to Amazon ECR for deployment

### Troubleshooting

- **Authentication Issues**: Ensure the OIDC role ARN is correct and has appropriate permissions
- **ECR Push Failures**: Verify the repository exists and the AWS account ID is correct
- **Build Failures**: Check the Dockerfile for syntax errors or missing dependencies
