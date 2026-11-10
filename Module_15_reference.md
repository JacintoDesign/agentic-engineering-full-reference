# Module 15 Reference Guide
### The Debrief — The Tools, the Board, the Bill, and What You're Holding

Use this guide alongside the Module 15 videos.

_Everything in this module is dated. Leaderboard positions, plan prices, and usage limits were compiled in early September 2026 and move weekly. Read the dimensions, not the winner — check current figures before acting on any of them._

---

## Table of Contents

1. [Four Tools, One Job](#four-tools-one-job)
2. [The Arena: What the Board Says](#the-arena-what-the-board-says)
3. [The Bill: What Your Subscription Buys](#the-bill-what-your-subscription-buys)
4. [What You're Holding](#what-youre-holding)

---

## Core Concepts

**The course ends at the capstone.** This module is a coda — the conversation after the work is done.

**Read the dimensions, not the winner.** The winner will have changed by the time you watch this; the questions won't.

**Parallel capability is table stakes.** The difference is whether a tool can tell you what all its agents are doing.

**You can't parallelise a decision. Only work.**

**When confidence intervals overlap, a ranking is a tie listed in an arbitrary order.** Pick the board that matches the job.

**Portability is your only real leverage.** Limits and prices move every few days; your documents and workflow don't have to.

**The prompts got shorter because the documents got thicker.** That trade is the skill.

---

## Four Tools, One Job

| Tool | Built in this course |
|---|---|
| Claude Code | The VibeMail backend, frontend, and memory layer |
| Cursor | Waypoint and the ZTM Music Player refresh |
| Codex | Cosmos and the bonus projects |
| Antigravity | The Mars Colony Simulator |

These are one operator's impressions on specific projects at a specific date — not a benchmark.

### Overall impressions

| Tool | Strengths | Weaknesses |
|---|---|---|
| **Codex** | Most polished desktop app; integrated browser with picture-in-picture and visible clicks; image generation; automatic Remote Control to any project; weekly limit resets; strong value on Plus; Astra impressive for 3D | — |
| **Cursor** | Polished Agents Window; `/multitask` and managed worktrees; Composer covers a heavy month on the base plan; no five-hour limit; Grok 4.6 strong and cheaper than frontier models; includes tokens for Claude and others | — |
| **Claude Code** | Best terminal experience; Claude Design useful; strong models | Most expensive — two months on Max; five-hour resets blocked work on the base plan; longest waits; desktop app still maturing (browser initially localhost-only, file explorer tied to git, Remote Control manual and finicky) |
| **Antigravity** | Agent Manager pulled into its own app; project-scoped settings excellent when repos differ | Thin when you want one standard everywhere; models needed more correction — the colony ran on Flash: fast, but much tweaking |

### The instructor's ranking

1. **Codex** — polished app, image generation, Astra, good value
2. **Cursor** — best value, polished IDE and Agents Window
3. **Claude Code** — cost and a less polished desktop app
4. **Antigravity** — weaker models

Your ranking depends on what you prioritise, and every position is in flux.

### When parallel actually pays

| Build | Parallel? | Why |
|---|---|---|
| Three magazine volumes (Codex bonus) | Yes | Shared skeleton in `CONTRACT.md`, committed to `main` before any worktree; threads differed only in style |
| VibeMail backend | Yes | `CONTRACT.md` specified every endpoint, shape, and error case first |
| The walkable world (capstone) | No | One scene graph — fog affects neon, neon affects ground, ground affects camera. No seam to cut along |

_You can't parallelise a decision. Only work._

### The browser, and watching an agent work

| Tool | Browser |
|---|---|
| Codex | In-app browser, live cursor, picture-in-picture; Remote Control reaches every session |
| Antigravity | Drives your installed Chrome — your logins and extensions, and your live sessions at risk |
| Cursor | Browser preview and Design Mode — point at a visual to specify a change |
| Claude Code | No native agent browser at the time — wire Playwright yourself |

Watching the cursor move is the difference between an agent claiming a flow works and demonstrating it.

### The trail an agent leaves behind

- **Antigravity** — walkthrough markdown with embedded screenshots and recordings: footage, not a summary.
- **Codex** — every source a conversation drew on: screenshots, docs, files read. Plus a commit-and-push button that writes its own message, and file access with no git repo configured.

### Convergence

All four now share the same shape:

- A task list with per-task models
- A planning mode before the diff
- A browser the agent can drive and you can watch
- An anchor file read before anything else

| Leads on | Tool |
|---|---|
| Orchestration and watching work happen | Codex |
| The terminal and quality of output | Claude Code |
| Building inside a predictable monthly ceiling | Cursor |
| The artifact it leaves behind | Antigravity |

---

## The Arena: What the Board Says

### Impressions to check

- Claude's models feel more effective, and slower.
- Antigravity's models are fast and need more corrections.
- Composer isn't the strongest model but covers a startling amount per month.
- Codex's image generation handles text inside images cleanly.

### The board changed shape

LMArena is now **Arena** (arena.ai/leaderboard), rebranded in early 2026. One list became many:

```text
Text · Agent · WebDev · Image-to-WebDev · Vision · Document · Search · Image · Video
```

The **Agent** board is the only public measurement of long-horizon, tool-using, multi-step work. It is scored as a **win rate**, not Elo — don't compare it with text scores.

### Agent board, early September 2026

| Position | Model |
|---|---|
| 1 | Claude Opus 5 (high effort) |
| 2 | Claude Opus 5 (max) |
| 3 | Claude Fable 5 |
| 4 | GPT-5.6 Sol |
| 5 | Claude Opus 4.8 |
| 6 | Kimi K3 |

Anthropic holds six of the top ten; Gemini doesn't appear in it.

### The text board is a tie

- The top nine sit within about fourteen points.
- Confidence intervals run roughly ±3 to ±11 and overlap.
- Rank inside the leading cluster reshuffles on its own as votes accumulate.

_When the confidence intervals overlap, you are not looking at a ranking. You are looking at a tie listed in an arbitrary order._

### Where models still separate

| Board | Reading |
|---|---|
| WebDev | Leader far clear of the field |
| Image-to-WebDev | Claude Opus 5 leads by a wide margin |
| Text-to-image, image edit | The model behind Codex's image generation ranks first |

Pick the board that matches the job — a general ranking can't tell you which model to point at a shader, a contract, or a screenshot.

### Open weights arrived

- Kimi K3 — sixth on Agent
- Qwen's flagship — second on WebDev
- GLM — Agent top ten

The gap between the best model you can host and the best you can rent is a fraction of what it was.

---

## The Bill: What Your Subscription Buys

### The entry tier, four ways

| Plan | Pool | Limits | Metering | Step up |
|---|---|---|---|---|
| **Claude Pro** — $20/mo | One pool across Code, web, desktop, mobile | Rolling 5-hour window + weekly cap, with a tighter frontier-model ceiling | No published token counts; relative multipliers only | Max 5x $100/mo; Max 20x $200/mo |
| **ChatGPT Plus** — $20/mo | Shared across web, CLI, IDE, mobile | 5-hour block + unpublished weekly limit | Wide published ranges per block; minimum tier for mid-task top-ups | Pro 5x $100/mo; Pro 20x $200/mo |
| **Cursor Pro** — $20/mo | Cursor's own models + $20 of third-party usage | Credit draw-down, no rigid prompt throttle | Auto bills at the routed model's rate — up to a ~20× spread in one request | Pro+ $60/mo; Ultra $200/mo |
| **Google AI Pro** — ~$20/mo | Antigravity compute across Google surfaces | 5-hour refresh under a weekly ceiling; hitting the ceiling halts refreshes | No published figures; metered by task weight | AI Ultra ~$100/mo; top tier ~$200/mo |

_Three of these four will not tell you the number. The one that does publishes a range whose top is six times its bottom._

Prices are US list prices — convert for your own budgeting.

### The shape of the year

| Metric | February–August 2026 |
|---|---|
| Dated limit changes on paid coding plans | 38, across 9 vendors — roughly one every five days |
| Outright decreases | 13 |
| Never announced | 2 |
| Vendor framing opposite to subscriber experience | 14 |
| Vendors changing nothing on existing paid tiers | 1 |

The dominant pattern: a temporary increase, extended repeatedly, settling below the level you'd grown used to.

| Vendor | Example |
|---|---|
| OpenAI | Removed Codex's five-hour limit in July; restored it for the $20 tier in late August while exempting higher tiers |
| Cursor | Raised included usage twice and repriced Auto in the same email, publishing no figure for either |
| Google | Cut Antigravity's free daily allowance by over 90%; moved the paid tier from a five-hour refresh to a weekly ceiling |

### The September 14 Anthropic change

The same pattern, from the vendor this course started with. It's the clearest case of one change being described in two opposite ways, and both descriptions are accurate.

Claude publishes relative multipliers, not token counts, so think of it as an index. Call the original allowance **100**. A temporary boost lifts it to **150**, and subscribers adjust their habits to that. The permanent level then settles at **125**.

| Compared against | Change | Who quotes it |
|---|---|---|
| The original baseline (100 → 125) | **+25%** | The announcement: "a permanent increase" |
| The level you'd been using (150 → 125) | **−17%** | Your sessions: you hit the limit sooner than last week |

_Both numbers are true. Which one you hear depends on which starting point the speaker picked._

**How to read any limit change:**

- **Ask what it's compared against.** "Up 25%" against the baseline and "down 17%" against your current level can describe the same change.
- **Find the permanent number.** The temporary level is the one you've got used to, and it's the one that ends.
- **Measure it in your own work.** Without published token counts, the only reliable measure is your own: how many sessions, or how much of a build, fits in a five-hour window and in a week. Check your plan's usage page before and after the change.

### Budgeting inside it

- **Never lock into annual commitments.** Pay monthly so you can leave the moment a plan is quietly diluted.
- **Treat AI providers like cloud commodities.** Keep prompts, context files, and agent instructions modular rather than hardwired into one environment.
- **Rotate your spend to follow peak value.** When a vendor cuts a quota or reprices a router, move your $20 to whoever is over-subsidising compute.
- **Stay fluid.** Loyalty subsidises the vendor's margin; portability is your leverage.
- **Keep your own meter.** Vendors won't publish the number, so record your own. Note what one typical task costs you (a module build, an audit, a long playtest) as a share of your window and your week. When a limit changes, re-run the same task and compare. That's the only before-and-after you can trust.

---

## What You're Holding

### The method, on the table

| Document | Job | Arrived in |
|---|---|---|
| PRD | What the product is, who it's for, what success looks like | Claude — Planning System |
| Content model | What exists, before how it looks | Claude — Planning System |
| `CONTRACT.md` | What done means, precisely enough for two agents to build independently | Site contract first; API contract in the VibeMail backend; every module after |
| `DESIGN.md` | Visual specification — tokens and reasoning | Throughout |
| `TEST_PLAN.md` | What gets checked and what passing looks like | Throughout |
| `BUILD_SEQUENCE.md` | The atomic order, each step with its own definition of verified | The VibeMail backend |
| Anchor file | Short, pointing rather than containing, read every session | `CLAUDE.md`, `AGENTS.md`, or the tool's equivalent |

### Two prompts

The first build prompt in the course:

> _"Read CLAUDE.md, the site contract, and the design standard. Build the navigation and overall page structure only — the sticky header, the page container layout, and the section spacing system. Do not build any content sections yet. Follow the navigation behaviour specified in the site contract exactly. Apply the colour palette and typography from the design standard. This is complete when the navigation renders correctly, transitions from transparent to solid at the specified scroll depth, and the page container layout is in place for the content sections that follow."_

A build prompt from the cinematic portfolio:

> _"Build the card reveal from the brief. Use ScrollTrigger.batch so the cards arrive as a wave."_

Same person. Harder task. A tenth of the words. Every constraint still applies — it lives in documents now instead of the message.

_The prompts got shorter because the documents got thicker. That trade is the skill._

Compare your own first prompt with your last one in your repository history.

### Spec-driven development

- The method now has an industry name and appears on Thoughtworks' Technology Radar; GitHub and AWS built tools around it.
- The fair critique: it's contract design and waterfall relabelled, and the value was always the thinking. That's consistent with this course — you made the decision, wrote it down, and handed the writing, not the deciding, to the agent.

### The inventory

**Shipped products**

| Product | Tool |
|---|---|
| VibeMail — engine, face, and memory | Claude Code and Claude Design |
| Waypoint | Cursor |
| ZTM Music Player v2 | Cursor |
| Cinematic motion portfolio | Claude Code |
| Cosmos | Codex |
| Three-volume magazine and the 1996 desktop | Codex |
| Mars Colony Simulator | Antigravity |
| The walkable world | Claude Code |

If someone asks what you can do, send links.

**Portable assets**

- The Director's Audit format
- The `/audit` user command
- The `spec-interview` skill
- Ogham, if you kept it running — cross-tool memory that doesn't care which agent is talking
- Your anchor-file templates
- Your `CONTRACT.md`, `DESIGN.md`, and `TEST_PLAN.md` shapes

### The drill for a tool that doesn't exist yet

Find these five things on day one, in this order:

| # | Question | Reference answers |
|---|---|---|
| 1 | **Anchor file** — what does it read automatically, and from where? | `CLAUDE.md`, `AGENTS.md`, `.cursor/rules`, Antigravity rules. `AGENTS.md` is now governed under the Linux Foundation, but check the specific tool. Put your existing anchor in it first. |
| 2 | **Planning mode** — how do you get a reviewable plan before any file is written? | Shift-Tab twice in Claude Code. With no planning mode, ask for a file list and build order first. |
| 3 | **Verification surface** — how does it show you the thing running? | In-app browser, preview pane, Playwright, a Chrome subagent. If none, wire one before the first real task. |
| 4 | **Model routing** — what granularity, and what's the price ratio? | Per task is best; Codex sets the bar. Know the cheap-to-expensive ratio — ~20× inside one Cursor request. |
| 5 | **Permission model** — what does it do without asking? | How to pre-approve commands, inspect approvals, and turn confirmations off when warranted. Defaults are rarely where you want them. |

_Five questions, one afternoon, and the tool is yours. Everything else is where the buttons are._

### The last instruction

Open a blank file. Write one sentence about a world that doesn't exist yet.

Then go and build it.
