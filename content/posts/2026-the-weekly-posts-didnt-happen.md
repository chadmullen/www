---
title: "The Weekly Posts Didn't Happen. The Building Did."
date: 2026-11-11
draft: false
summary: "In January I promised weekly posts. I wrote two. Here's what I was building instead, the posts I've now caught up on, and what's next."
tags: ["building in public", "career", "AI Agents", "AWS"]
---

In January I wrote that 2026 would be [the year of building in public](/posts/2026-goals/). More writing here, more code on GitHub. In February I [kicked off a 25-week series](/posts/week-01-kickoff/) and promised Week 2 the following week.

Week 2 never shipped. Neither did anything else on this site for about nine months.

I'd rather say that plainly than pretend otherwise. If you clicked the "Next up" link at the bottom of that post and hit a 404, sorry about that.

## What happened

The short version: the day job got very real, in a good way. Much of my year went to helping customers take generative AI from pilots into production, which meant time with their teams instead of time writing up what I'd learned.

I didn't stop building. I stopped writing about it. That's the part I want to fix, because the writing is where the lessons stick, for me and hopefully for you.

## Where the certifications stand

In January I said I'd earn the AWS Certified Generative AI Developer – Professional and renew my Solutions Architect Professional. Neither is done yet. Both are still on, with a deadline of the end of the year, and I've added a third:

- **AWS Certified Generative AI Developer – Professional.** The 24-project series was meant to get me there. The projects turned into real work instead of weekly posts, so I'm finishing the exam prep directly.
- **AWS Certified Solutions Architect – Professional (renewal).** This one has an actual expiration date, which helps.
- **[AWS Certified AI Business Strategist](https://aws.amazon.com/certification/certified-ai-business-strategist/).** New this year. Much of my work now is helping leaders decide where AI is worth the investment, not only how to build it, and I want the business side to be as solid as the technical side.

Three exams in about seven weeks is ambitious. I'll report back either way.

## What I was building

I went back through my repos and notes and wrote up the work that's most useful to other people. Each is dated to when the work happened:

- **[Open WebUI defaults are built for a laptop](/posts/tuning-open-webui-for-concurrency-on-aws/)** (June). Every setting I changed to run Open WebUI multi-pod on EKS with Amazon Bedrock, and why each one matters once real users show up.
- **[Data quality rules you can change without restarting the stream](/posts/hot-reload-data-quality-rules-flink-broadcast-state/)** (July). A metadata-driven pattern for real-time telemetry quality: rules as versioned config, hot-reloaded into Apache Flink with broadcast state.
- **[I stopped prompting and started supervising](/posts/an-ai-agent-team-for-every-poc/)** (September). The template I clone for every proof of concept: an orchestrator agent, specialist subagents, and a different model as adversarial reviewer.
- **[Agent skills are the new dotfiles](/posts/skills-are-the-new-dotfiles/)** (October). How I use skills.sh to give every coding agent the same playbook, pinned with a lockfile.

Looking back, there's a thread. In January the interesting question was which model to use. By fall it was how to make agents reliable: give them clear contracts, shared instructions, a second model to check their work, and a human who decides. Most of what I learned this year sits somewhere on that line.

## What I learned about building in public

**Weekly was the wrong cadence for me.** A 25-week plan is a great way to structure studying. As a publishing promise it was fragile: miss one week and the whole series feels broken, which makes it easier to miss the next one too.

**Write it down while it's fresh.** Every post above came out of notes I left in repos while doing the work: a config-changes file, a README, an architecture doc. The writing went fast because the thinking was already on disk. That habit is worth more than any schedule.

**Ship smaller.** A post doesn't need to be a tutorial with a repo attached. Sometimes "here are the 20 settings I changed and why" is the most useful thing on the internet that day.

## More to come

Here's what's on deck, with no promises about which week:

- **Open WebUI straight to Amazon Bedrock**: dropping LiteLLM and the Bedrock Access Gateway from the chat path now that Bedrock Runtime speaks the OpenAI Chat Completions API. Fewer moving parts, no proxy to run, and what you give up along the way.
- **Architecture diagrams with agents**: the house-style skill I use to get diagrams that make a point instead of just filling space. (You may notice the diagram on [my blog architecture post](/posts/building-a-static-blog-on-aws/) got an upgrade.)
If any of this is useful, or you want me to go deeper on one of these, tell me on [LinkedIn](https://www.linkedin.com/in/chadmullen). It helps to know what people want to read.

Thanks for sticking around. Let's build.
