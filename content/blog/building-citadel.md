+++
title = "Building Citadel: A Local-First Personal App Hub in 4 Days"
date = 2026-02-20T00:00:00+01:00
draft = false
summary = "How I went from an empty repo to a self-hosted platform running multiple apps with an AI autopilot—commit by commit."
+++

---

## The Origin Story

This didn't start as a grand project. It started because I was testing OpenClaw when it became popular—just using Telegram to develop a personal project hosted on an old spare laptop at home. The laptop was so slow and boring that I didn't want to touch it once OpenClaw was set up. Originally, I had to test my Next.js app on the local browser on that shitty laptop. It was super painful.

That's when I discovered ngrok and Tailscale after reading a post by [@levelsio](https://twitter.com/levelsio). Suddenly I could write prompts from Telegram on my phone and test immediately in my browser on a real machine. The feedback loop became addictive.

With that speed, I didn't want to work on just one app anymore. I asked Claude Code to make a master Next.js app that could host multiple sub-apps with different databases, storage, and isolation. Citadel was born.

But then I got annoyed by switching contexts in Telegram. I came across Bhanu Teja's [Mission Control](https://bhanuteja.dev/mission-control) article—it was exactly the nascent idea in my head, but now battle-tested and written down. That pushed me further.

Now I hated opening my project in a browser at all. So I asked Claude Code to build me an iOS app that acts as a browser but is confined only to my URL. It strips away all the browser functionality I don't need. Just my superapp, fullscreen, like a native experience.

That's where I am now: an iOS app connecting to my local superapp that only I can access. My personal data stays safe (not that it's a challenge for anyone to hack), and I've rebuilt apps I was paying for. They're simple CRUD apps with LLM integrations for now, but the foundation is there for much more.

---

## Day 1: The Foundation (Feb 16, 16:24)

It started with a simple idea: I wanted a single place to run small personal apps without spinning up a new VPS for every little tool. At 4:24 PM, I initialized the monorepo skeleton. By 4:28, I'd already made the key architectural decision that would define everything after: **each app gets its own SQLite database**, completely isolated from the others. No shared schemas, no cross-app queries. If I delete an app, I delete one file. Simple.

The host came together quickly—a Next.js app that would serve as both the control plane and the runtime. I sketched out the first two apps that same afternoon: **Smart Notes** for capturing thoughts, and **Gym Tracker** for logging workouts. Both followed the same pattern: a minimal UI, a simple API, and their own `db.sqlite` file tucked away in `data/apps/<appId>/`.

By 5 PM, I had working prototypes. By 6 PM, they looked decent—Tailwind CSS tied it all together. But "decent" wasn't enough. I kept going.

---

## Day 1 Evening: Smart Notes Gets Real

The first version of Smart Notes was just a list and a form. Useful, but boring. I wanted something I'd actually enjoy using.

At 5:24 PM, I added a proper note detail page. Then soft deletes (5:33 PM)—because accidents happen. Then a trash view with restore and purge (5:36 PM). Search highlighting (5:36 PM). Pinning (6:26 PM). Each commit was small, but the cumulative effect was a notes app that felt *alive*.

The big leap came at 5:51 PM: **voice notes**. I added a record button that captures audio on mobile, pipes it through ElevenLabs Scribe for transcription, and structures it into markdown. By 6:04 PM, I'd tuned it to use the best models and keep the output faithful—no over-enthusiastic "meeting notes" rewrites, just clean structured text.

Autosave landed at 6:23 PM, followed by undo-delete snackbars. By the end of the evening, Smart Notes wasn't a toy anymore. It was the notes app I'd been looking for.

---

## Day 2: Gym Tracker Finds Its Flow

Feb 16, 11:25 PM. I turned to Gym Tracker with fresh eyes. The initial "log a workout" flow was clunky. I reimagined it as a **guided session logger**: you pick a day category (Push, Pull, Legs, etc.), then log sets one by one with voice-to-fill support.

Voice-to-fill is one of those features that sounds gimmicky until you try it. At 10:19 AM on Feb 17, I added it: tap the mic, say "bench press 185 for 8 reps, set 3," and it extracts the fields automatically. No form tapping. It just works.

Then came the analytics (Feb 17, 12:12 AM). Heatmaps showing workout consistency. Weekly stacked bars. Line charts tracking exercise progression over time. I found myself opening the app just to look at my data.

The UI got the same treatment as Smart Notes—mobile-first, sticky navigation, app-like feel. By noon on Feb 17, Gym Tracker felt like a native app running in my browser.

---

## The Paper Notebook Detour (Feb 17, 1:21 PM)

Sometimes you chase an idea that doesn't pan out. I got obsessed with making Smart Notes look like a physical paper notebook—textures, handwritten fonts, ruled lines. I committed the changes: textures, Patrick Hand font, full analog redesign.

Then I used it for 10 minutes and hated it. Legibility suffered. The novelty wore off fast. At 1:49 PM, I reverted all seven commits. The app went back to clean, readable, digital-native UI. No regrets—you don't know until you try.

---

## Day 3: Tags, Mobile Polish, and the Scrum Board (Feb 18)

Feb 18 started with practical improvements. Tags for Smart Notes (12:43 AM)—with automatic GPS and timestamp tagging. A floating action button for mobile (12:46 AM). Pinch-zoom disabled so it feels like an app, not a website (1:08 AM).

Then I stripped the UI down: removed the home nav, the brand headers, the taglines. Clean surfaces. Focus on content.

The afternoon brought something bigger: **Scrum Board**. I needed a way to track tasks across all these apps. By 1:44 PM, I had per-app boards with priorities. Assignees by 1:45 PM. Due dates by 2:02 PM. Manual ordering by 2:06 PM. Modal task editors by 12:49 PM.

But I didn't want to *manually* move cards. I wanted an autopilot.

---

## Day 4: The Autopilot Awakens (Feb 18-20)

Feb 18, 3:27 PM. I wrote the AUTOPILOT runbook—a cron-driven system where an AI agent picks up tasks, implements them, and reports back. By 3:47 PM, the Scrum Board was upgraded with autopilot states: `pending` → `in_progress` → `awaiting_review` → `done`.

The integration was surprisingly clean. The Scrum Board got an "Agent Trigger" button (7:43 PM). Cron jobs handled scheduling. Each autopilot run:

1. Picks a todo task
2. Spawns an isolated session
3. Implements the feature
4. Commits with `autopilot(<app>): <description>`
5. Reports back to the task

By Feb 19, the autopilot was handling real work: upgrading Smart Notes with a Tiptap editor, fixing mobile modals, adding app images to the home page. Each commit in the log after Feb 19 7:20 PM is autopilot work—tagged, traced, reviewable.

The system even blocked itself when I forgot to add acceptance criteria (Feb 20, 12:22 AM). Good guardrails.

---

## What I Built

Four days. 80+ commits. Three working apps and a meta-layer that manages them:

- **Smart Notes** — Voice-to-markdown notes with tags, search, trash, and a proper rich editor
- **Gym Tracker** — Session-based workout logging with voice input and analytics dashboards
- **Scrum Board** — Kanban boards with autopilot integration for AI-driven development
- **Citadel (host)** — Permission system, per-app SQLite isolation, audit logging, app registry

The architecture held up. Per-app databases mean I can nuke Gym Tracker without touching my notes. The deny-by-default permission model means apps can't wander. The autopilot means I can spec a feature and come back to a pull request.

---

## What I'd Do Differently

The paper notebook detour taught me to test UI changes faster. I should've prototyped in the browser before committing seven reverts.

The autopilot needed clearer acceptance criteria from the start. It's good that it blocks on vague tasks, but I should've documented the format earlier.

---

## The Stack

- **Runtime**: Next.js 15, React, TypeScript
- **Database**: SQLite (one per app), accessed via `node:sqlite`
- **Styling**: Tailwind CSS v3
- **AI**: ElevenLabs Scribe (STT), various LLMs for structuring and autopilot
- **Orchestration**: OpenClaw cron jobs for autopilot scheduling

---

## What's Next

The autopilot is still learning. I'm refining the prompt engineering, adding more guardrails, and building out the review flow. The next apps are already specced: a finance tracker, a reading list, maybe a personal CRM.

But there's more coming:

- **Dockerize each app** — Right now everything runs in the same Next.js process. I want true isolation: each app in its own container, still managed by the Citadel host.

- **Cron the agents to pick tasks from the scrum board** — The autopilot is mostly manual right now. I want agents running on a schedule, pulling tasks, implementing them, and reporting back. Currently blocked by credit limits and API expenses, but that's temporary.

Citadel started as a way to avoid spinning up new VPS instances. It became something more interesting: a platform where I can *think* in apps, build them quickly, and let an agent handle the grunt work.

That's the story so far. The commits tell the rest.

---

*Last updated: Feb 20, 2026. Commit `70e4b15`.*
