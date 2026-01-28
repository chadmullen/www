---
inclusion: fileMatch
fileMatchPattern: "terraform/**/*.tf"
---

# Terraform Standards

## Provider Configuration

- AWS provider version: `~> 5.0`
- Always use `us-east-1` for ACM certificates (CloudFront requirement)
- Use provider aliases when multiple regions are needed

## Resource Naming

- Use snake_case for resource names: `aws_s3_bucket.site`
- Prefix with purpose: `site`, `blog`, `github_actions`
- Use variables for repeated values (domain name, email)

## Tags

Apply consistent tags to all resources:

```hcl
tags = {
  Name    = var.domain_name
  Project = "personal-blog"
}
```

## Security

- S3 buckets: Always block public access, use CloudFront OAC
- IAM: Least privilege, scope permissions to specific resources
- No hardcoded secrets—use variables or AWS Secrets Manager
- Enable versioning on S3 buckets

## State Management

- Local state is acceptable for this personal project
- If using remote state, use S3 backend with encryption
- Never commit `.tfstate` files

## Cost Control

- Include AWS Budgets resource with alerts at 50%, 80%, 100%
- Use Cost Anomaly Detection for unexpected spikes
- Choose cost-effective options (PriceClass_100 for CloudFront)

## GitHub Actions Integration

- Use OIDC federation, not static credentials
- Scope IAM role to specific repository
- Minimal permissions: S3 write, CloudFront invalidation

## File Organization

```
terraform/
├── main.tf                    # All resources (small project)
├── terraform.tfvars.example   # Example variables (committed)
└── terraform.tfvars           # Actual values (gitignored)
```

For this project size, a single `main.tf` is fine. Split into modules only if complexity grows significantly.

## Output Values

Always output:
- S3 bucket name
- CloudFront distribution ID
- CloudFront domain name
- IAM role ARN for GitHub Actions
- ACM certificate ARN

These are needed for DNS setup and GitHub secrets.
