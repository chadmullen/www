---
title: "Building a Static Blog on AWS: The Architecture"
date: 2025-01-28
draft: false
summary: "A walkthrough of hosting a Hugo blog on S3 and CloudFront—simple, inexpensive, and fast."
tags: ["AWS", "CloudFront", "S3", "Hugo", "Architecture"]
---

I recently rebuilt my personal site as a static blog hosted on AWS. Here's the architecture and why I made these choices.

## Why Static?

For a personal blog, static site generators hit the sweet spot:

- **Cost**: Pennies per month on S3/CloudFront
- **Performance**: CDN-cached HTML is as fast as it gets
- **Security**: No server to patch, no database to secure
- **Simplicity**: Markdown files in a Git repo

I chose [Hugo](https://gohugo.io/) for the generator—it's fast, has great themes, and the learning curve is gentle.

## The Architecture

Here's the whole thing: how a page gets to you, and how a new post gets published.

![Architecture of this blog. Serving: the browser resolves DNS with Route 53, requests the page over HTTPS from CloudFront (ACM certificate in us-east-1, a CloudFront Function that redirects the apex domain to www), and on a cache miss CloudFront reads from a private S3 bucket through Origin Access Control. Publishing: a push to main triggers GitHub Actions, which builds with Hugo, assumes a repo-scoped IAM role through OIDC, syncs to S3 and invalidates the CloudFront cache.](/images/posts/static-blog-architecture.svg)

### Components

**Route 53** handles DNS, pointing the domain to CloudFront via an ALIAS record.

**CloudFront** serves as the CDN, caching content at 450+ edge locations worldwide. It handles HTTPS termination and protects the origin.

**ACM** provides free SSL certificates. Must be provisioned in us-east-1 for CloudFront compatibility.

**S3** stores the static files. The bucket is private—CloudFront accesses it through Origin Access Control (OAC), not public URLs.


## Cost Breakdown

For a low-traffic personal blog, expect roughly:

| Service | Monthly Cost |
|---------|-------------|
| S3 | ~$0.03 |
| CloudFront | ~$0.10 |
| Route 53 | $0.50 |
| **Total** | **< $1.00** |

This assumes modest traffic. Even at 100K page views/month, you're likely under $5.

If you have questions or want to discuss the architecture, find me on [LinkedIn](https://www.linkedin.com/in/chadmullen).
