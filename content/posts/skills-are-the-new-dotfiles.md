---
title: "Agent Skills Are the New Dotfiles"
date: 2026-10-07
draft: false
summary: "How I use skills.sh and the skills CLI to give every coding agent the same playbook: install per project, pin with a lockfile, and layer house style on top of shared skills."
tags: ["AI Agents", "Agent Skills", "skills.sh", "Claude Code", "Kiro", "Developer Productivity"]
---

Every developer I know has a dotfiles repo. Shell aliases, editor config, git settings. You carry it from machine to machine because re-teaching every new laptop how you work is a waste of time.

Agent skills are that idea for coding agents. Instead of re-explaining how you like diagrams drawn, or how a plan should be stress-tested, you write it down once as a `SKILL.md` and every agent that supports skills picks it up.

This fall I started treating skills the way I treat dotfiles, and [skills.sh](https://skills.sh) is what made it practical.

## What a skill is

A skill is a folder with a `SKILL.md` file: YAML front matter with a `name` and a `description`, then markdown instructions. It can also hold scripts, references and examples. The description matters most. It's what the agent reads to decide whether the skill applies to what you just asked.

```markdown
---
name: grill-me
description: A relentless interview to sharpen a plan or design.
---
```

That's it. No SDK, no plugin API. It's markdown in a folder, which is exactly why it travels so well.

## skills.sh and the skills CLI

[skills.sh](https://skills.sh) is a directory of published skills, and the [`skills` CLI](https://github.com/vercel-labs/skills) installs them into whatever agent you use (Claude Code, Codex, Cursor, OpenCode and a long list of others).

The first one I added to every project was Matt Pocock's `grill-me`:

```bash
npx skills add mattpocock/skills --skill grill-me
```

It installs into the project's `.agents/skills/` folder, so it lives in the repo next to the code. Add `-g` to install it for your user account across every project instead.

Other commands I use:

```bash
npx skills find diagram
npx skills list
npx skills check
npx skills update
```

`find` searches the directory from the terminal. `check` and `update` keep installed skills current. I also keep the `find-skills` skill installed globally, so when I ask an agent "is there a skill for this?" it can search for me.

## grill-me is the one I'd start with

`grill-me` turns the agent into a skeptical interviewer that keeps asking about your plan until the weak spots show. It's set to run only when you call it, which is what you want: you decide when a plan is ready to be grilled.

I run it before any real build now. In my POC template, step one after decomposing the work is "grill the plan." Finding a bad assumption in a conversation costs a few minutes. Finding it after three subagents built on top of it costs an afternoon.

It's in three of my repos so far: my POC template, a rules-engine prototype and an Open WebUI reference deployment.

## Pin it like a dependency

When the CLI installs a project skill it writes `skills-lock.json`:

```json
{
  "version": 1,
  "skills": {
    "grill-me": {
      "source": "mattpocock/skills",
      "sourceType": "github",
      "skillPath": "skills/productivity/grill-me/SKILL.md",
      "computedHash": "9cdbb4b8f7a3..."
    }
  }
}
```

That `computedHash` covers the files the CLI installed, so the tooling can tell when what's on disk no longer matches what you pinned. (My lockfile is version 1; current CLI releases write version 2.)

This matters more than it looks. A skill is instructions your agent will follow, sometimes with scripts it will run. That's a supply chain. Treat it like one:

- Read a skill before you install it. It's markdown, so it takes two minutes.
- Commit the lockfile so everyone on the project gets the same version.
- Review skill updates the way you'd review a dependency bump.

## Layer house style on top of shared skills

The pattern I like most: a general-purpose skill installed globally, with a small project skill on top that holds my opinions.

For architecture diagrams I have a global `excalidraw-diagram` skill that knows the Excalidraw JSON format, a palette and a render-and-check loop. On top of it, my diagrams repo has an `arch-diagram` skill with my house recipe: C4 levels, a required project brief before any boxes get drawn, and a short list of non-negotiables. It says up front which wins when they disagree:

> Where the two conflict, this file wins on style and content; that one wins on JSON mechanics.

That one sentence prevents a lot of confusion. The shared skill handles the mechanics, I own the taste, and the agent knows who to listen to.

Two of my non-negotiables, for flavor:

1. **Brief before boxes.** Nothing goes in a diagram that isn't in the brief.
2. **Diagrams argue, not decorate.** Every diagram makes a point.

## Make it work across tools

I switch between Kiro, Claude Code and Codex depending on the job, so a skill that only works in one of them isn't worth much to me. Two habits keep things portable:

- Keep project skills in `.agents/skills/` and symlink them into tool-specific folders (for example `.claude/skills/grill-me -> ../../.agents/skills/grill-me`). One copy, every tool.
- Point every tool's instruction file (`CLAUDE.md`, Kiro steering docs) at one shared `AGENTS.md`, and keep skills focused on how to do a task, not on one tool's quirks.

## Where to start

1. Browse [skills.sh](https://skills.sh) and install one skill you'd actually use this week. `grill-me` is a good first pick.
2. Read it before you install it.
3. Commit `skills-lock.json`.
4. Write one skill of your own for the thing you explain to agents most often. Run `npx skills init` to get a template.

Your dotfiles taught your machine how you work. Skills do the same for your agents.
