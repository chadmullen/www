# Build to Certify: 6 Months, 24 Projects, One AWS GenAI Certification

The AWS Certified Generative AI Developer – Professional (AIP-C01) exam dropped in November 2025, and it's the real deal. Not a foundational "do you know what a prompt is" certification — this one validates whether you can actually build production GenAI systems on AWS. Vector stores, RAG pipelines, agentic architectures, guardrails, cost optimization, the works.

I'm going to pass it. And I'm going to do it by building 24 real projects over the next six months, documenting every step publicly.

## Why Build in Public?

Certifications are useful. Hands-on experience is better. Both together? That's the combination that enables delivering real impact.

The AIP-C01 exam guide reads like a blueprint for modern GenAI development: foundation model integration, retrieval-augmented generation, multi-agent systems, security controls, operational optimization. These aren't theoretical concepts — they're the patterns I'm already working with as an AWS Solutions Architect. The certification is a forcing function to go deeper on each one, systematically.

Building in public adds accountability. It also means anyone following this series gets working code, not just theory. Every week ships a GitHub repo you can fork and deploy.

## The 6-Month Plan

I've mapped 25 weekly posts directly to the five exam domains, weighted by how much each domain contributes to the exam:

| Domain | Weight | Weeks |
|--------|--------|-------|
| FM Integration, Data Management & Compliance | 31% | 2–8 |
| Implementation & Integration | 26% | 9–14 |
| AI Safety, Security & Governance | 20% | 15–18 |
| Operational Efficiency & Optimization | 12% | 19–21 |
| Testing, Validation & Troubleshooting | 11% | 22–25 |

Domain 1 gets the most time because it carries the most weight — and because RAG and vector stores are foundational to everything else. By the time I hit the agentic AI projects in Month 3, I'll have a working retrieval pipeline to plug agents into.

## What's Coming

**Month 1: Foundations & Model Integration**
We start with model selection. Week 2 builds a dashboard that pulls benchmark data from [Artificial Analysis](https://artificialanalysis.ai/) and compares it against Bedrock model metadata. Real data, real comparisons — not vibes. Then we build dynamic model routing (Week 3) and a multimodal data pipeline (Week 4).

**Month 2: RAG & Vector Stores**
The technical core. I'll deploy the same dataset to OpenSearch, Aurora pgvector, and Bedrock Knowledge Bases, then benchmark all three. We'll dig into chunking strategies, build a production RAG pipeline with hybrid search and reranking, and implement prompt governance with Bedrock Prompt Management.

**Month 3: Agentic AI & Implementation**
Agents with Strands SDK, multi-agent orchestration with AWS Agent Squad, MCP servers for tool integration. Week 11 is a head-to-head comparison: classic function-based approach vs. agentic approach, same problem, using the Artificial Analysis data from Week 2. One post, clear verdict on when each pattern makes sense.

**Month 4: Enterprise Integration & Security**
Streaming APIs, async processing, resilience patterns. Then a GenAI gateway architecture with CI/CD. The back half focuses on security: guardrails deep dive, PII protection, VPC isolation, the full hardening playbook.

**Month 5: Governance & Optimization**
AI governance frameworks, responsible AI implementation, then the cost and performance work — token optimization, semantic caching, model tiering, auto-scaling tuned for GenAI traffic patterns.

**Month 6: Testing, Monitoring & Exam Prep**
Observability dashboards, FM evaluation suites, RAG and agent test harnesses, troubleshooting runbooks. Week 25 is the retrospective: what worked, what didn't, and exam day results.

## A Realistic Note

This is the plan. Life and day-to-day work will get in the way — I'll probably miss a week here and there. I'm going to do my best to stay on track, but I'm not going to pretend this will be a perfect 25-week streak.

This field moves fast — new models, new tools, new capabilities shipping constantly. That's part of what makes it exciting to build in. As I learn, this plan may evolve to incorporate what's new. What you're looking at is the current target, the happy path. I'll adapt as needed and document why.

## The GitHub Structure

Everything lives in public repos under a consistent structure:

- **[aip-c01-study-plan](https://github.com/chadmullen/aip-c01-study-plan)** — The parent repo. Series index, cross-project links, overall README. This is where you star and follow.
- **Per-week repos** — Each project gets its own repo (e.g., `fm-comparison-dashboard`, `vector-store-showdown`). Focused, forkable, deployable.

Every repo includes:
- README with the companion blog post link
- `/cdk` or `/terraform` for infrastructure as code
- `/src` for application code
- `/docs` for architecture diagrams

## Who This Is For

If you're preparing for the AIP-C01 exam, you'll get 24 hands-on projects that map directly to exam objectives. Fork them, deploy them, break them, learn from them.

If you're building GenAI applications on AWS and don't care about the certification, you'll still get production-ready patterns for RAG, agents, security, and operations.

If you're just curious where AWS GenAI development is heading, follow along. The exam guide is essentially a roadmap of what AWS considers production-ready GenAI architecture in 2026.

## Let's Build

Week 2 drops next week: the FM Comparison Dashboard. We'll pull model benchmark data from Artificial Analysis, combine it with Bedrock model metadata, and build something you can actually use to make informed model selection decisions.

The parent repo is live. The plan is set. Six months from now, I'll either have a new certification and 24 shipped projects — or I'll have 24 shipped projects and a very educational exam failure story.

Either way, we're building.

Let's go!

---

*This is Week 1 of the Build to Certify series. Follow the [GitHub repo](https://github.com/chadmullen/aip-c01-study-plan) for code and the blog for weekly posts. Next up: [Week 2 — Choosing the Right FM: Building a Model Comparison Dashboard](/week-02-fm-comparison-dashboard).*
