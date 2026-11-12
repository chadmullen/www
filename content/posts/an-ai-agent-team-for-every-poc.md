---
title: "I Stopped Prompting and Started Supervising: An AI Agent Team for Every POC"
date: 2026-09-23
draft: false
summary: "The golden template I now clone for every proof of concept: Claude Code as orchestrator, a bench of specialist subagents, a different model as adversarial reviewer, and one AGENTS.md they all read."
tags: ["AI Agents", "Claude Code", "Kiro", "AIDLC", "Developer Productivity"]
---

For most of this year my AI-assisted workflow looked like this: open a chat, describe the thing, paste errors back in, repeat. It worked. It also meant I was the integration layer, the reviewer, the project manager and the person remembering what we decided two hours ago.

So I built a template repo that flips the roles. I supervise. An orchestrator agent decomposes the work and dispatches a team. A different model red-teams the result. I clone it for every new proof of concept.

## The team

| Role | Who | Job |
|---|---|---|
| Supervisor | Me | Direction, approvals, tie-breaks |
| Orchestrator + lead dev | Claude Code | Decompose, dispatch, integrate, drive to done |
| Specialists | Subagents in `.claude/agents/` | db, frontend, api, ai, iac, security, test, plus a generic implementer |
| Adversarial reviewer | Codex / GPT | Independent red-team pass on the integrated diff |
| Spec author (optional) | Kiro | Requirements, design and tasks up front |

The specialists are short markdown files, each with one area of expertise and a few hard rules. The `iac-engineer` reads and plans Terraform but never applies without approval. The `test-engineer` surfaces defects and is explicitly told not to weaken a test to make it pass. The `security-engineer` owns the STRIDE threat model and hunts for leaked secrets.

## The loop

![The orchestrated agent loop. A human supervisor owns direction, approvals and tie-breaks. The orchestrator decomposes the goal, grills the plan, dispatches specialist workers in parallel, integrates their reports, sends the diff to a different model for adversarial review, then hardens and verifies against AGENTS.md. Review findings go back to the team.](/images/posts/agent-team-loop.svg)

1. **Decompose** the goal into independent, well-scoped tasks.
2. **Grill the plan** before writing code. I use Matt Pocock's `grill-me` skill, which interviews you relentlessly about a plan until the holes show. It's the cheapest bug fix in the whole process.
3. **Dispatch** one worker per independent task, in parallel. Dependent tasks wait.
4. **Integrate.** The orchestrator collects reports and reconciles conflicts.
5. **Adversarial review** by a different model family.
6. **Harden**, then **verify** against the success criteria written in `AGENTS.md`.

## The worker contract

This is the part that made the biggest difference. Every worker gets:

- exactly one scoped task,
- its acceptance check,
- and the instruction "report, don't guess."

Every worker returns three things: files changed, how it verified, open questions. The orchestrator never assumes a worker's result. It waits for the report.

Before I wrote that contract down, subagents did what models do when they're unsure: they filled gaps with plausible guesses. With it, I get "I couldn't confirm the table name, here's my question" instead of a confident migration against a table that doesn't exist.

## Why the reviewer is a different model

Claude implements. GPT reviews. On purpose.

A model reviewing its own family's output tends to share its blind spots, so it's convinced by the same mistakes. A different model family catches a different set of failures. I run that pass manually in a separate tool, which also forces me to look at the diff with fresh eyes before it goes anywhere.

## One source of truth for every tool

`AGENTS.md` holds the canonical instructions: project overview, conventions, success criteria, what not to touch. Everything else points back to it:

- `CLAUDE.md` imports `AGENTS.md` and adds orchestrator notes.
- `.kiro/steering/` docs point back to `AGENTS.md`.
- Shared skills live in `.agents/skills/` and are symlinked into `.claude/skills/`.

I switch tools often. Kiro for specs, Claude Code for orchestration, Codex for review. If the guidance lived in one tool's proprietary format, every switch would mean rewriting it. Plain markdown in the repo means every agent reads the same rules.

## What else ships in the template

- A Terraform skeleton with S3 and DynamoDB remote state and no resources, so nothing gets deployed by accident.
- A Python 3.12 app skeleton with pytest, wired into a `Makefile` (`make install`, `make test`, `make lint`, `make tf-init`).
- A C4 diagram recipe and prompts under `docs/architecture/`.
- A STRIDE threat-model template under `docs/security/`.
- An example `.mcp.json` with AWS documentation and Terraform MCP servers.

Starting a new POC is: copy the repo, rename the package, fill in the project overview in `AGENTS.md`, `make test`, then grill the plan.

## What changed for me

The surprise wasn't speed, though it is faster. It's that my job moved up a level. I spend my time on the decomposition, the acceptance criteria and the tie-breaks, which is where my judgment actually matters, and much less time pasting stack traces into a chat window.

If you want to try it, start small: write `AGENTS.md`, define two specialists, and add the "report, don't guess" contract. That alone changes how the agents behave.
