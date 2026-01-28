# Technology Stack

## Core Technologies

- **Static Site Generator**: Hugo (extended version 0.128.0)
- **Theme**: PaperMod (installed as git submodule)
- **Infrastructure as Code**: Terraform (~> 5.0)
- **CI/CD**: GitHub Actions
- **Hosting**: AWS (S3 + CloudFront + Route 53 + ACM)

## AWS Architecture

- **S3**: Static file storage with versioning and encryption
- **CloudFront**: CDN with Origin Access Control (OAC)
- **ACM**: SSL/TLS certificates (must be in us-east-1)
- **Route 53**: DNS management
- **IAM**: GitHub Actions OIDC authentication
- **Budgets**: Cost monitoring and alerts
- **Cost Explorer**: Anomaly detection

## Common Commands

### Local Development

```bash
# Start local development server
hugo server -D

# Build site (production)
hugo --minify

# Create new post
hugo new posts/my-post-name.md
```

### Infrastructure Management

```bash
# Initialize Terraform
cd terraform
terraform init

# Plan infrastructure changes
terraform plan

# Apply infrastructure changes
terraform apply

# View outputs (bucket name, CloudFront ID, etc.)
terraform output
```

### Deployment

Deployment is automated via GitHub Actions on push to `main` branch. Manual trigger available via workflow_dispatch.

## Configuration Files

- `hugo.yaml`: Site configuration, theme settings, menu structure
- `terraform/main.tf`: Complete AWS infrastructure definition
- `.github/workflows/deploy.yml`: CI/CD pipeline
- `terraform/terraform.tfvars`: Terraform variables (gitignored, use .example as template)

## Dependencies

- Hugo extended version (required for SCSS processing)
- Terraform >= 1.0
- AWS CLI (for local testing)
- Git submodules (for PaperMod theme)

## Cache Strategy

- Static assets (CSS, JS, images): 1 year cache (`max-age=31536000`)
- HTML, XML, JSON: 1 hour cache (`max-age=3600`)
- CloudFront invalidation on deployment
