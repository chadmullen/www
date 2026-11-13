---
title: "The Weekly Posts Didn't Happen. The Building Did."
date: 2026-11-11
draft: false
summary: "In January I planned weekly posts. That cadence didn't hold, but the building did. Here's what I shared this year, where my certifications stand, and what's next."
tags: ["building in public", "career", "AI Agents", "AWS"]
---

In January I wrote that 2026 would be [the year of building in public](/posts/2026-goals/). In February I [kicked off a 25-week series](/posts/week-01-kickoff/) with a post every week.

The weekly cadence didn't hold. The building did, and I shared pieces of it along the way. With the year winding down, here's an honest look at what I planned, what happened instead, and what's next.

## What happened

The day job got very real, in a good way. Much of my year went to helping customers take generative AI from pilots into production. That work shaped almost everything I wrote about: the problems that kept coming up with customers became the projects I built, and the projects became the posts.

So instead of 25 weekly lessons on a study plan, I ended up with fewer, deeper write-ups that came straight out of real work.

## Where the certifications stand

In January I said I'd earn the AWS Certified Generative AI Developer – Professional and renew my Solutions Architect Professional. Neither is done yet. Both are still on, with a deadline of the end of the year, and I've added a third:

- **AWS Certified Generative AI Developer – Professional.** The project series was meant to get me there. The projects turned into real work instead of a weekly checklist, so I'm finishing the exam prep directly.
- **AWS Certified Solutions Architect – Professional (renewal).** This one has an actual expiration date, which helps.
- **[AWS Certified AI Business Strategist](https://aws.amazon.com/certification/certified-ai-business-strategist/).** New this year. Much of my work now is helping leaders decide where AI is worth the investment, not only how to build it, and I want the business side to be as solid as the technical side.

Three exams in about seven weeks is ambitious. I'll report back either way.

## What I shared this year

- **[Open WebUI defaults are built for a laptop](/posts/tuning-open-webui-for-concurrency-on-aws/)** (June). Every setting I changed to run Open WebUI multi-pod on EKS with Amazon Bedrock, and why each one matters once real users show up.
- **[Data quality rules you can change without restarting the stream](/posts/hot-reload-data-quality-rules-flink-broadcast-state/)** (July). A metadata-driven pattern for real-time telemetry quality: rules as versioned config, hot-reloaded into Apache Flink with broadcast state.
- **[A private, enterprise Open WebUI on AWS](/posts/open-webui-enterprise-architecture-on-aws/)** (August). The full architecture: EKS, Aurora with pgvector, ElastiCache, S3 and the Bedrock Access Gateway, with every Bedrock model behind one private front door.
- **[I stopped prompting and started supervising](/posts/an-ai-agent-team-for-every-poc/)** (September). The template I clone for every proof of concept: an orchestrator agent, specialist subagents, and a different model as adversarial reviewer.
- **[Agent skills are the new dotfiles](/posts/skills-are-the-new-dotfiles/)** (October). How I use skills.sh to give every coding agent the same playbook, pinned with a lockfile.

Looking back, there's a thread. In January the interesting question was which model to use. By fall it was how to make AI systems reliable: give agents clear contracts and shared instructions, have a second model check their work, and keep a human deciding. Most of what I learned this year sits somewhere on that line.

## What I learned about building in public

**Weekly was the wrong cadence for me.** A 25-week plan is a great way to structure studying. As a publishing promise it was fragile: miss one week and the whole series feels broken, which makes it easier to miss the next one too.

**The best posts come from real work.** Every post above started as notes I kept while building: a config-changes file, a README, a security note, an architecture doc. When the thinking is already on disk, the writing goes fast. That habit is worth more than any schedule.

**Ship smaller.** A post doesn't need to be a tutorial with a repo attached. Sometimes "here are the 20 settings I changed and why" is the most useful thing on the internet that day.

## More to come

Here's what's on deck, with no promises about which week:

- **Open WebUI straight to Amazon Bedrock**: the third step after LiteLLM and the Bedrock Access Gateway. Bedrock Runtime now speaks the OpenAI Chat Completions API, so the chat path may not need a proxy at all. Fewer moving parts, and an honest look at what you give up.
- **Architecture diagrams with agents**: the house-style skill I use to get diagrams that make a point instead of just filling space. (You may notice the diagram on [my blog architecture post](/posts/building-a-static-blog-on-aws/) got an upgrade.)

If any of this is useful, or you want me to go deeper on one of these, tell me on [LinkedIn](https://www.linkedin.com/in/chadmullen). It helps to know what people want to read.

Thanks for sticking around. Let's build.
