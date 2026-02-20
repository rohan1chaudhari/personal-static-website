+++
title = "Building Citadel: From a Shitty Laptop to a Personal App Hub"
date = 2026-02-20T00:00:00+01:00
draft = false
summary = "How I stopped paying for 20 subscriptions and started building software that serves me instead of shareholders."
+++

---

It started with an old laptop. A spare machine I had lying around, too slow to use for anything serious but too functional to throw away. I wanted to test OpenClaw—this new thing people were talking about—so I installed it there and hooked up Telegram. The idea was simple: use AI to develop a personal project without touching the machine itself.

The problem became obvious immediately. Every time I wanted to test my Next.js app, I had to open a browser on that shitty laptop. The lag. The fan noise. The pain of typing on a keyboard I didn't like. I avoided it like the plague.

Then I read a post by [@levelsio](https://twitter.com/levelsio). He mentioned ngrok and Tailscale—tools for exposing local servers to the internet. I set them up that evening. Suddenly I could write prompts from Telegram on my phone and see the results instantly in a browser on my actual computer. The feedback loop became addictive. Write prompt. Wait 30 seconds. Refresh. See changes. Repeat.

But with that speed came a new problem: I didn't want to work on just one app anymore. I had ideas for a notes app, a gym tracker, maybe a finance tool. Each one felt like it needed its own repo, its own deployment, its own database. The friction of starting stopped me from starting.

So I asked Claude Code to build something different: a master Next.js app that could host multiple sub-apps, each with its own database, storage, and isolation. Citadel was born.

---

## The Foundation (Feb 16, 16:24)

At 4:24 PM on February 16th, I initialized the monorepo skeleton. By 4:28 PM, I'd made the decision that would define everything: **each app gets its own SQLite database.** No shared schemas. No cross-app queries. Delete an app, delete one file. Simple.

The architecture came together fast—a Next.js host serving as both control plane and runtime. By 5 PM I had working prototypes for Smart Notes and Gym Tracker. By 6 PM they looked decent with Tailwind CSS. But decent wasn't enough. I kept going.

---

## Smart Notes Gets Real

The first version was just a list and a form. Useful, but boring. I wanted something I'd actually enjoy using.

5:24 PM: Note detail page. 5:33 PM: Soft deletes—because accidents happen. 5:36 PM: Trash view with restore and purge. 6:26 PM: Pinning. Each commit was small, but the cumulative effect was a notes app that felt alive.

Then 5:51 PM: **Voice notes.** A record button that captures audio on mobile, pipes it through ElevenLabs Scribe for transcription, and structures it into markdown. By 6:04 PM I'd tuned it to use the best models and keep the output faithful—no over-enthusiastic rewrites, just clean structured text.

Autosave landed at 6:23 PM, followed by undo-delete snackbars. By evening, Smart Notes wasn't a toy. It was the notes app I'd been looking for.

---

## Gym Tracker Finds Its Flow

February 16th, 11:25 PM. The initial workout logging flow was clunky. I reimagined it as a guided session logger: pick a day category (Push, Pull, Legs), then log sets one by one.

Voice-to-fill came the next morning. Tap the mic, say "bench press 185 for 8 reps, set 3," and it extracts the fields automatically. No form tapping. It just works.

Then analytics. Heatmaps showing consistency. Weekly stacked bars. Line charts tracking progression. I found myself opening the app just to look at my data.

By noon on February 17th, Gym Tracker felt like a native app running in my browser.

---

## The Paper Notebook Detour

February 17th, 1:21 PM. I got obsessed with making Smart Notes look like a physical paper notebook—textures, handwritten fonts, ruled lines. I committed the changes. Then I used it for 10 minutes and hated it. Legibility suffered. At 1:49 PM I reverted all seven commits. No regrets—you don't know until you try.

---

## Mobile Polish and the Scrum Board

February 18th started practical. Tags with automatic GPS and timestamping. A floating action button for mobile. Pinch-zoom disabled so it feels like an app, not a website.

Then I stripped the UI down. Removed home nav, brand headers, taglines. Clean surfaces. Focus on content.

The afternoon brought something bigger: **Scrum Board**. By 1:44 PM I had per-app boards with priorities. Assignees by 1:45 PM. Due dates by 2:02 PM. Manual ordering by 2:06 PM.

But I didn't want to manually move cards. I wanted an autopilot.

---

## The Autopilot Awakens

February 18th, 3:27 PM. I wrote the AUTOPILOT runbook—a cron-driven system where an AI agent picks up tasks, implements them, and reports back. By 3:47 PM the Scrum Board had autopilot states: `pending` → `in_progress` → `awaiting_review` → `done`.

The integration was clean. The Scrum Board got an "Agent Trigger" button. Cron jobs handled scheduling. Each run: pick a task, spawn a session, implement, commit with `autopilot(<app>): <description>`, report back.

By February 19th the autopilot was handling real work: upgrading Smart Notes with Tiptap, fixing mobile modals, adding app images. Each commit after 7:20 PM that day is autopilot work—tagged, traced, reviewable.

The system even blocked itself when I forgot to add acceptance criteria. Good guardrails.

---

## Mission Control and the iOS App

Around this time I came across Bhanu Teja's [Mission Control](https://bhanuteja.dev/mission-control) article. It articulated exactly the nascent idea in my head: using AI agents for development workflows. That pushed me further.

But I still hated opening my project in a browser. So I asked Claude Code to build me an iOS app that acts as a browser confined only to my URL. It strips away all the browser functionality I don't need. Just my superapp, fullscreen, like a native experience.

That's where I am now: an iOS app connecting to my local Citadel instance. Only I can access it. My data stays safe. I've rebuilt apps I was paying for. Simple CRUD tools with LLM integrations for now, but the foundation is there for more.

---

## What I Built

Four days. 80+ commits. Three working apps and a meta-layer:

- **Smart Notes** — Voice-to-markdown with tags, search, trash, rich editor
- **Gym Tracker** — Session-based logging with voice input and analytics
- **Scrum Board** — Kanban with autopilot integration
- **Citadel (host)** — Permissions, per-app SQLite isolation, audit logging, app registry

The architecture held. Per-app databases mean I can nuke Gym Tracker without touching my notes. Deny-by-default permissions mean apps can't wander. The autopilot means I can spec a feature and come back to a pull request.

---

## The Vision

Here's what I'm actually building toward: **a world where you have the apps you want, the way you want them.**

No more 20 subscriptions for simple tools that should be free. No more accepting whatever UI some product manager decided was best for "engagement." When you control the app, you control the experience.

And here's what excites me most: **interconnection**. When all your apps live in the same place—same data layer, same auth, same runtime—they can talk to each other in ways that SaaS never allows. Your gym tracker suggests notes about form. Your finance app references reading highlights. Everything becomes composable.

Citadel isn't just a personal app host. It's an argument that software should serve users, not shareholders.

---

## What's Next

- **Dockerize each app** — True isolation: each app in its own container, still managed by Citadel
- **Cron the agents** — Agents running on schedule, pulling tasks from the scrum board, implementing, reporting back. Currently blocked by API credit limits, but that's temporary

More apps coming: finance tracker, reading list, maybe a personal CRM.

Citadel started as a way to avoid spinning up VPS instances. It became something more interesting: a platform where I can *think* in apps, build them quickly, and let an agent handle the grunt work.

That's the story so far. The commits tell the rest.

---

*Last updated: Feb 20, 2026. Commit `70e4b15`.*
