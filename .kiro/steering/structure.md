# Project Structure

## Directory Organization

```
.
├── .github/workflows/     # CI/CD automation
│   └── deploy.yml         # Hugo build and AWS deployment
├── .kiro/                 # Kiro AI assistant configuration
│   └── steering/          # Project guidance documents
├── archetypes/            # Content templates for new posts
│   └── default.md         # Default frontmatter template
├── assets/css/            # Custom CSS overrides
│   └── extended/          # Theme extension styles
├── content/               # All site content (Markdown)
│   ├── posts/             # Blog posts
│   ├── about.md           # About page
│   ├── search.md          # Search page
│   └── 404.md             # Custom 404 page
├── data/                  # Data files for Hugo templates
├── docs/                  # Local deployment documentation (gitignored)
├── layouts/               # Custom Hugo templates and partials
│   └── partials/          # Theme overrides (e.g., analytics)
├── static/                # Static assets served as-is
│   └── images/            # Images, profile photo, etc.
├── terraform/             # Infrastructure as Code
│   ├── main.tf            # Complete AWS infrastructure
│   └── terraform.tfvars.example  # Variable template
├── themes/                # Hugo themes (git submodules)
│   └── PaperMod/          # PaperMod theme
├── hugo.yaml              # Hugo site configuration
└── .gitignore             # Git exclusions
```

## Key Conventions

### Content Organization

- Blog posts go in `content/posts/` with kebab-case filenames
- Each post requires frontmatter: title, date, draft status, summary, tags
- Set `draft: false` when ready to publish
- Use relative paths for images: `/images/filename.png`

### Infrastructure

- All AWS resources defined in single `terraform/main.tf` file
- Terraform state can be local or remote (S3 backend commented out)
- Domain-specific values in `terraform.tfvars` (not committed)
- GitHub secrets required: `AWS_ROLE_ARN`, `S3_BUCKET_NAME`, `CLOUDFRONT_DISTRIBUTION_ID`

### Theme Customization

- PaperMod theme installed as git submodule (don't modify directly)
- Custom CSS in `assets/css/extended/custom.css`
- Template overrides in `layouts/partials/`
- Theme configuration in `hugo.yaml` under `params`

### Deployment Flow

1. Push to `main` branch triggers GitHub Actions
2. Hugo builds site to `public/` directory
3. Files sync to S3 with appropriate cache headers
4. CloudFront cache invalidated for immediate updates

## Ignored Files

- `docs/` - Personal deployment notes (local only)
- `terraform/terraform.tfvars` - Contains sensitive configuration
- `public/` - Hugo build output (regenerated on each build)
- `resources/` - Hugo cache directory
- `.hugo_build.lock` - Hugo build lock file

## Important Notes

- ACM certificates must be created in us-east-1 for CloudFront
- Theme is a git submodule - clone with `--recursive` flag
- Profile image should be placed at `static/images/profile.jpg`
- CloudFront distribution uses OAC (not legacy OAI) for S3 access
