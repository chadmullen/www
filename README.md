# chadmullen.com

Personal blog built with Hugo and hosted on AWS (S3 + CloudFront).

## Quick Start

### Prerequisites

- [Hugo](https://gohugo.io/installation/) (extended version)
- [Terraform](https://www.terraform.io/downloads)
- AWS CLI configured with appropriate credentials

### Local Development

```bash
# Clone with submodules (for theme)
git clone --recursive https://github.com/chadmullen/chadmullen-blog.git
cd chadmullen-blog

# Install the PaperMod theme
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

# Run local server
hugo server -D

# Visit http://localhost:1313
```

### Creating a New Post

```bash
hugo new posts/my-new-post.md
```

Edit the file in `content/posts/`, set `draft: false` when ready to publish.

## Deployment

### One-Time Infrastructure Setup

1. Copy and configure Terraform variables:

```bash
cd terraform
cp terraform.tfvars.example terraform.tfvars
# Edit terraform.tfvars with your email for alerts
```

2. Deploy infrastructure:

```bash
terraform init
terraform plan
terraform apply
```

3. Complete DNS validation for ACM certificate (add CNAME records shown in AWS Console)

4. Point your domain to CloudFront:
   - For apex domain (chadmullen.com): Create an ALIAS record pointing to the CloudFront distribution
   - For www: Create a CNAME pointing to the CloudFront distribution

5. Add GitHub repository secrets:
   - `AWS_ROLE_ARN`: From Terraform output `github_actions_role_arn`
   - `S3_BUCKET_NAME`: From Terraform output `s3_bucket_name`
   - `CLOUDFRONT_DISTRIBUTION_ID`: From Terraform output `cloudfront_distribution_id`

### Ongoing Deployments

Push to `main` branch. GitHub Actions handles the rest.

## Configuration

### Analytics

1. Sign up for Cloudflare Web Analytics (privacy-friendly, no cookies)
2. Add your site and get your token
3. Replace `YOUR_CLOUDFLARE_TOKEN` in:
   - `hugo.yaml`
   - `layouts/partials/extend_head.html`

### Profile Image

Add your profile photo as `static/images/profile.jpg`

## Structure

```
.
├── archetypes/          # Templates for new content
├── content/
│   ├── posts/           # Blog posts
│   ├── about.md         # About page
│   └── search.md        # Search page
├── layouts/
│   └── partials/        # Custom theme overrides
├── static/
│   └── images/          # Static images
├── terraform/           # AWS infrastructure
├── .github/workflows/   # CI/CD
└── hugo.yaml            # Site configuration
```

## Cost

Expected monthly cost for low-traffic blog: < $1

Budget alerts configured at 50%, 80%, and 100% of $10/month threshold.

## License

Content is © Chad Mullen. Code is MIT licensed.
