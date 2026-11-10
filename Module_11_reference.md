# Module 11 Reference Guide
### Codex — Build Cosmos (NASA Dashboard): Agent Routing, Designed Ambiguity, and Browser Verification

Use this guide alongside the Module 11 videos.

_Codex model names, settings locations, Rules syntax, and plugin commands change. Keep the workflow, but confirm current names in the Codex docs before committing them to config._

---

## Table of Contents

1. [Introducing the Companion App for ChatGPT's Latest Models](#introducing-the-companion-app-for-chatgpts-latest-models)
2. [Installing Codex Application](#installing-codex-application)
3. [Starting Our Cosmos Project, AGENTS.md, Rules and Memories](#starting-our-cosmos-project-agentsmd-rules-and-memories)
4. [Agent Routing with a Subagent Team](#agent-routing-with-a-subagent-team)
5. [Sourcing Data from the NASA APIs](#sourcing-data-from-the-nasa-apis)
6. [Creating the CONTRACT.md and PLAN.md](#creating-the-contractmd-and-planmd)
7. [Checking the CONTRACT.md Against Live API Documentation](#checking-the-contractmd-against-live-api-documentation)
8. [Image Generation with gpt-image-2: The Mission Control Concept Look](#image-generation-with-gpt-image-2-the-mission-control-concept-look)
9. [Building Project Assets with Image Generation](#building-project-assets-with-image-generation)
10. [First Build: The Shell and the APOD Panel](#first-build-the-shell-and-the-apod-panel)
11. [Improving Our App Shell](#improving-our-app-shell)
12. [Verifying the Build: Playwright and Browser](#verifying-the-build-playwright-and-browser)
13. [Designed Ambiguity: The ISS Panel](#designed-ambiguity-the-iss-panel)
14. [Designed Ambiguity: NEO and Space Weather](#designed-ambiguity-neo-and-space-weather)
15. [The EONET Panel and a Consistency Pass](#the-eonet-panel-and-a-consistency-pass)
16. [Reviewing the Quality of the Code and the Director's Audit](#reviewing-the-quality-of-the-code-and-the-directors-audit)
17. [Checking Everything Deployed in Production](#checking-everything-deployed-in-production)

---

## Core Concepts

**Routing writes model choice into the repo.** Named agents in `.codex/agents/`, each pinned to a model tier and a sandbox, make the decision once instead of per prompt.

**Routing doesn't reduce the work.** It stops the expensive model doing the cheap parts of it.

**AGENTS.md states intent; Rules enforce it.** For anything costly to get wrong, write both.

**Designed ambiguity.** Signal loss, a rate limit, stale data, and an outage can wear the same empty response. The contract names each cause, how to detect it, and its treatment before the build.

**Verification stands in for trusting the signal.** The output is empty; that isn't in dispute. The question is why.

**The image is the compass; the written spec is the law.** Generate a reference, then extract concrete tokens into `DESIGN.md` so the build can be audited.

**Generate fixed assets once.** Backgrounds, patches, markers, and state icons are committed files, never runtime model calls.

**Reach for the tool that matches the job.** Browser and Playwright cover a website; Computer Use's OS-level access isn't needed.

---

## Introducing the Companion App for ChatGPT's Latest Models

### Surfaces

| Surface | Role in this module |
|---|---|
| Desktop app | Home base — build-and-look work, Memories across threads |
| IDE extension | One detour: the diff next to the file |
| CLI | Scripted commands — `codex review`, `codex execpolicy`, `codex mcp` |
| Cloud | Not used here |

All surfaces share the same project config.

### Capabilities

| Capability | Use |
|---|---|
| Browser | Read live documentation; glance at your running app |
| Image generation | Direct the visual reference and generate committed assets |
| Playwright MCP | Scripted, repeatable verification with network mocking |
| Computer Use | Deliberately not used — requires Accessibility permission this project doesn't need |

### Model tiers

| Tier | Job |
|---|---|
| Flagship | Judgement calls |
| Balanced | Everyday building — the thread you drive |
| Fast and cheap | High-volume checking |

Model names turn over every few months. Check Codex's model page before committing them to config.

### Cosmos

| Panel | Source | Ambiguity or edge |
|---|---|---|
| APOD | NASA APOD | Empty vs error; the archive floor is neither |
| ISS | WhereTheISS.at | 2XX with no position = signal loss; thrown/non-2XX = failed call |
| NEO | NASA NeoWs | Over-limit response vs successful empty sky |
| Space Weather | NASA DONKI GST | Stale observation vs current reading |
| EONET | EONET v3 GeoJSON | Successful no-active-events vs error |

### The verification ladder

| Rung | What verification stands in for |
|---|---|
| Waypoint (Cursor) | Domain command — you can't judge spatial SQL by reading it |
| Cosmos (Codex) | Trust in the signal — the response is empty, and the cause is the question |
| Mars Colony (Antigravity) | A still frame — the system ticks on its own |

---

## Installing Codex Application

### Install

1. Install the ChatGPT desktop app and sign in.
2. Check that your plan includes at least two model tiers.
3. Install the CLI:

```bash
npm install -g @openai/codex
codex login
```

### Import an existing setup

Desktop: **Settings › Import**. CLI: `/import`.

| Imported from Claude Code / Cowork | Lands as |
|---|---|
| Instruction files | `AGENTS.md` |
| Project memories | Codex Memories |
| Slash commands | Skills |
| MCP configuration and hooks | Carried over |
| Subagents | Codex agents |
| Project folders, last 30 days of chats | Available in Codex |

The import copies; nothing in your existing setup moves. Cursor import is newer and narrower — check what's supported.

### Check before you trust it

- Permissions on imported skills and agents.
- MCP servers — usually need re-authentication.
- Hooks — behaviour can differ after import.
- Follow any status card Codex shows.

With nothing to import, skip the flow — you lose nothing.

---

## Starting Our Cosmos Project, AGENTS.md, Rules and Memories

Create a new Codex project on an empty folder and initialise git.

### Scaffold and fill AGENTS.md

```text
/init
```

> "Fill in AGENTS.md for Cosmos: a glassmorphic NASA data dashboard with five live panels, read-only, no user-specific data stored. Stack is Next.js 16 with the App Router, React 19, TypeScript strict, deployed to Vercel — no separate backend, the NASA key is read server-side from an environment variable and responses are cached with Next.js's own fetch cache. Conventions: one panel per directory under app/panels/{name}, every panel renders at least three explicit states — ready, empty, error — data fetched in server components with the NASA key never reaching the client, and visual values come from DESIGN.md once it exists. And three never-do rules: never inline or commit the NASA API key, never collapse two different failure causes into one generic 'no data' state, never invent a NASA response shape without checking it against CONTRACT.md."

Target:

```markdown
# AGENTS.md — Cosmos

## Identity
Cosmos is a glassmorphic NASA data dashboard with five live panels.
Read-only: it displays public NASA data and stores nothing user-specific.

## Stack
- Next.js 16 (App Router), React 19, TypeScript strict
- No separate backend — the NASA key is read server-side from an environment variable, never client-side
- Responses cached with Next.js's fetch cache (next: revalidate), shared across visitors
- Deploys to Vercel

## Conventions
- One panel per directory under app/panels/{name}
- Every panel renders at least three explicit states: ready, empty, error
- Data fetched in server components; never expose the NASA key to the client
- Visual values come from DESIGN.md once it exists

## Never do
- Never inline a NASA API key in client code or commit it to the repo
- Never collapse two different failure causes into one generic "no data" state
- Never invent a NASA response shape — check it against CONTRACT.md
```

The middle never-do rule protects the whole module.

### Rules: enforced intent

`.codex/rules/default.rules`:

```python
prefix_rule(
    pattern = ["git", "add", ".env"],
    decision = "forbidden",
    justification = "Secrets never enter the repo. Use Vercel environment variables.",
)
```

| Decision | Behaviour |
|---|---|
| `allow` | Runs |
| `prompt` | Asks first |
| `forbidden` | Blocked |

The most restrictive matching rule wins, and compound commands such as `git add . && git add .env` are split and judged separately.

```bash
codex execpolicy check --pretty --rules .codex/rules/default.rules -- git add .env
```

Rules are experimental — confirm the current syntax. Commit `.codex/rules/`.

### Three instruction layers

| Layer | Scope | Holds |
|---|---|---|
| Custom Instructions (Settings › Personalization) | Every chat on your account | Personal, durable preferences |
| `AGENTS.md` | This project, committed | Project rules worth version-controlling |
| Memories (project settings) | This project, accumulated | What Codex picks up along the way |

_If it matters, commit it. Memory is a convenience, not a contract._

---

## Agent Routing with a Subagent Team

### How agents work

- Built-in agents: `explorer`, `worker`, `default`.
- Custom agents: one TOML file each in `.codex/agents/` (project) or `~/.codex/agents/` (personal).
- Routing fields: `model`, `model_reasoning_effort`, `sandbox_mode`. Omitted fields inherit from the main session.
- Reference: `developers.openai.com/codex/subagents` — confirm the URL hasn't moved.
- Codex only spawns agents when asked. Agent workflows use more tokens overall; the saving is that most run on a cheap model.

### Team prompt

Codex can drift toward creating a Skill when you ask for an agent. Say exactly what you want:

> "Create three custom agents for this project — real custom agents, TOML files under .codex/agents/, not Skills. First, docs_researcher: reads live API documentation only, read-only, never edits files, pin it to the cheapest model tier. Second, panel_verifier: opens a running panel in a browser and checks it against our specs, also read-only, same cheap tier. Third, reviewer: reviews code for correctness and for any place two different failure causes got collapsed into one state, pin it to the flagship tier — this is the one judgement call worth paying for. Give each one a name, a description of when to use it, and clear instructions."

`.codex/agents/docs-researcher.toml`:

```toml
name = "docs_researcher"
description = "Checks external API documentation to confirm endpoints, fields, and limits."
model = "gpt-5.6-luna"
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Confirm endpoints, parameters, response fields, and rate limits against official docs.
Answer concisely and always include the URL you found it on.
Never edit files.
"""
```

`.codex/agents/panel-verifier.toml`:

```toml
name = "panel_verifier"
description = "Verifies one dashboard panel's states against the contract and aesthetic spec."
model = "gpt-5.6-luna"
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Verify one panel only. Drive it into each state named in CONTRACT.md.
Screenshot each state and compare against DESIGN.md.
Report what you saw and any deviation. Never fix anything.
"""
```

`.codex/agents/reviewer.toml`:

```toml
name = "reviewer"
description = "Reviews code for correctness, missed failure cases, and contract violations."
model = "gpt-5.6-sol"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Review like an owner. Prioritise correctness and any place two different
failure causes have been collapsed into one state.
Lead with concrete findings. Skip style-only comments.
"""
```

`.codex/config.toml`:

```toml
[agents]
max_threads = 5
```

### Project layout

```text
.codex/
  rules/
    default.rules
  agents/
    docs-researcher.toml
    panel-verifier.toml
    reviewer.toml
  config.toml
```

```text
# .gitignore
.codex/sessions/
.codex/*.jsonl
```

Config you wrote is committed; state Codex generated on your machine is ignored. Confirm the local-state paths in the docs.

### Declared team vs Max vs Ultra

| Option | What it does | Reach for it when |
|---|---|---|
| Max | More reasoning time on one hard task | Depth, not width |
| Ultra | Automatic fan-out — Codex splits the work | A big, one-off task |
| Declared team | Your committed split, tiers, and permissions | The same kind of work recurs |

---

## Sourcing Data from the NASA APIs

### Choosing from the catalog

Browse `api.nasa.gov`. The initial picks:

```text
APOD — hero image panel
ISS — live position (WhereTheISS.at, not api.nasa.gov)
Asteroids NeoWs — near-Earth object tracker
DONKI — space weather index
EPIC — full-Earth image
```

- NeoWs over SSD/CNEOS — a close-approach feed rather than orbital-mechanics research.
- **Open Notify rejected for ISS:** served over HTTP (blocked as mixed content on an HTTPS Vercel page), and partly wound down.
- **WhereTheISS.at chosen:** HTTPS, no key, and a documented failure convention the ISS ambiguity depends on.
- EPIC is replaced by EONET in the next lesson.

### Get the NASA key

Sign up at `api.nasa.gov` — name and email; the key arrives by email.

```dotenv
# .env.local — not committed
NASA_API_KEY=your_real_key_here
```

```dotenv
# .env.example — committed
NASA_API_KEY=
```

### Extend the rule to .env.local

```bash
codex execpolicy check --pretty --rules .codex/rules/default.rules -- git add .env.local
```

If it isn't forbidden, add:

```python
prefix_rule(
    pattern = ["git", "add", ".env.local"],
    decision = "forbidden",
    justification = "Secrets never enter the repo. Use Vercel environment variables.",
)
```

### Research with docs_researcher

> "Using docs_researcher, look at the documentation for APOD, NeoWs, and DONKI's Geomagnetic Storm endpoint on api.nasa.gov; EONET at eonet.gsfc.nasa.gov; and WhereTheISS.at at wheretheiss.at/w/developer. For each one, tell me the fields a dashboard would realistically use, and the rate limit. Two things specifically: for WhereTheISS.at, exactly how it signals a failed request. For EONET, which API version is current — check whether the version you land on is the latest or a deprecated one. Keep it to what we'd render."

APOD, NeoWs, and DONKI share one key and one hourly cap, so every call to them must be cached.

---

## Creating the CONTRACT.md and PLAN.md

### Two changes owned on camera

1. **MapLibre added to the stack** — no account or token, keeping the project at one credential.
2. **EPIC dropped for EONET** — EPIC duplicated APOD's large-image job and publishes on a lag; EONET adds a second map with live natural events.

> "Add MapLibre GL JS to the stack section of AGENTS.md — we'll use it for both map panels. Note that it's chosen partly because it needs no account or token, which keeps us at exactly one credential in this project. Leave everything else in the file alone."

### Contract prompt

> "Write CONTRACT.md. For each of our five sources, give the endpoint, fields, and limit docs_researcher found when we chose the sources. Then write three ambiguity states as acceptance criteria: ISS — signal loss (resolves, no position: dimmed last-known, 'out of tracking range', no retry) vs failed call (fails outright: explicit error, retry). NEO — rate limit ('data paused — limit reached', reset time) vs empty sky (accurate all-clear). Space Weather — stale (observed time past our freshness threshold: muted, 'last observed' plus age, never a live colour) vs live (reads as live in its band). Also note APOD, NeoWs, and DONKI share one key and hourly cap, so every call to them runs through a cached server component. Structure it so a later browser check can confirm each criterion directly."

### CONTRACT.md

```markdown
# CONTRACT.md — Cosmos

## Sources

### APOD
Endpoint: NASA APOD
Fields used: title, url, media_type, explanation, date
Limit: NASA key, hourly cap

### ISS
Endpoint: api.wheretheiss.at/v1/satellites/25544
Fields used: latitude, longitude, altitude, velocity, visibility, timestamp
Limit: no key required; be reasonable with polling frequency

### NEO
Endpoint: NASA NeoWs feed
Fields used: close-approach list, estimated_diameter, is_potentially_hazardous
Limit: NASA key, hourly cap

### Space Weather
Endpoint: api.nasa.gov/DONKI/GST
Fields used: gstID, startTime, allKpIndex array — each with kpIndex and
observedTime
Bands: Kp 0-3 quiet, 4-5 active, 6-9 storm
Limit: NASA key, hourly cap

### EONET
Endpoint: eonet.gsfc.nasa.gov/api/v3/events/geojson (v3 — v2.1 is deprecated)
Fields used: per event — id, title, categories, geometry (GeoJSON Point or
Polygon), closed (null while open), magnitudeValue/Unit where present
Params: status=open, days, limit, category
Limit: no key required, own host

## Designed ambiguities

### ISS — signal loss vs failed call
Cause: request returns 2XX but carries no usable position vs request returns
non-2XX with error and status properties, throws, or times out.
Treatment: signal loss holds last known position, dimmed, "out of tracking
range", no retry. Failed call shows an explicit error with a retry.

### NEO — rate limit vs empty sky
Cause: over-limit response vs a successful response with a genuinely empty list.
Treatment: rate limit shows "data paused — limit reached" with reset time.
Empty sky shows an accurate all-clear.

### Space Weather — stale vs live
Cause: the most recent Kp observedTime is older than the freshness threshold
vs a reading inside it. GST only records storms, so an old storm's Kp is not
current conditions and must never be presented as though it were.
Treatment: stale is muted and colourless, shows "last observed" with age, never
uses a live band colour. Fresh reads as live in its Kp band. No storm records in
the window at all reads as quiet, which is accurate rather than empty.
```

### Plan Mode

> "/plan Read AGENTS.md and CONTRACT.md. I want the file and component structure, how data gets fetched server-side and cached with Next.js's own fetch cache, what order to build the five panels in and why, and for each panel exactly which states it renders and how it detects each one."

Interrogate the plan rather than accepting the first pass — build order, detection rules per state, and cache placement. Then:

> "That's approved. Write it to PLAN.md and commit it."

---

## Checking the CONTRACT.md Against Live API Documentation

Use **Browser**, not Computer Use — the task needs a website, not OS-level access.

> "Read CONTRACT.md. For each of the five NASA data sources, use docs_researcher with Browser to open the official documentation and check what I've written: the endpoint path, the required parameters, the names of the fields we use, and the current rate limit. List anything that doesn't match, with the URL you found it on. Don't change the contract yet — just tell me."

Review each mismatch and decide which to accept. Then:

> "Update CONTRACT.md with the corrections we just accepted. Put the source URL next to each change so we can trace it later."

A wrong assumption caught here costs an edit; caught after the build, it costs a rebuild.

---

## Image Generation with gpt-image-2: The Mission Control Concept Look

### Test the glass small

> "Generate a single frosted glass panel, floating on a plain dark navy background — no other content, just the panel material. Show how translucent it is, the border, whether it has an inner glow, with cool cyan accent light. Give me three variations, from subtle to more pronounced."

Compare the three in **Canvas view** and pick one before generating a full scene.

### Full concept

> "Generate a dark, glassmorphic mission-control dashboard viewed straight on, using the glass panel treatment we just settled on — [describe the one you picked: how translucent, the border, the glow]. A grid of frosted-glass panels over a deep navy-to-black gradient with a faint star field. One large hero panel top-left, four smaller panels around it. Cool cyan and amber accent light, thin hairline borders, monospace labels. Calm and precise — an observatory at night, not sci-fi, not neon."

| Viewer | Use |
|---|---|
| Focused view | One image large enough to judge |
| Canvas view | Attempts side by side — better, or just different? |

### Extract DESIGN.md

> "Look at the reference image and write DESIGN.md in the standard token-plus-prose format. Frontmatter tokens for the palette as hex values and the typography. Then prose sections below. Give me the glass treatment as concrete numbers — background opacity, blur radius, border colour and weight, and the active-panel glow. Add the spacing rhythm and the rules for when accent colours get used. And finish with a short note on how a small state icon should look: single colour, glyph only, simple enough to read at 24px."

Example:

```markdown
---
name: Cosmos
colors:
  background: "#0a0e1a"
  panel: "#141a2e"
  accent: "#5fd4ff"
  glow: "#7ee6ff"
typography:
  heading: { fontFamily: "Space Grotesk" }
  body: { fontFamily: "Inter" }
---

## Overview
Mission Control at night — calm, precise, observatory-grade. Dark
glassmorphic panels over a deep navy field, cyan accent light used
sparingly, never decoratively.

## Glass treatment
Panel background: rgba(20, 26, 46, 0.6), blur 24px, border 1px solid
rgba(255, 255, 255, 0.08). Active-panel glow: 0 0 32px accent at 15%
opacity.

## Icons
State icons are single-colour glyphs only, no fill, legible at 24px.
```

Commit the reference image and `DESIGN.md` together.

---

## Building Project Assets with Image Generation

Identity assets now; functional assets later, the moment before the state that needs them.

### Background

> "Generate the Cosmos background: a deep navy-to-black radial gradient with a faint, even star field, no focal point, meant to sit behind frosted-glass panels. Save it into the public assets folder as a web-optimised file and wire it into the shell."

### Mission patches

> "Generate five simple mission patches, one per panel — APOD, ISS, near-earth objects, space weather, natural events. Keep them consistent with the reference: monochrome with a single cyan accent, simple enough to read small. Save them alongside the background."

Compare all five in Canvas view — a patch at the wrong weight or a hair off the accent is obvious side by side.

```text
public/assets/
  background.webp
  patch-apod.webp   patch-iss.webp   patch-neo.webp
  patch-weather.webp   patch-eonet.webp
```

### Asset schedule

| Asset | Generated |
|---|---|
| Background, patches | Now — identity |
| ISS marker, signal-loss and failed-call icons | Before the ISS panel |
| Rate-limit, all-clear, and stale icons | Before NEO and Space Weather |
| EONET category markers, no-events icon | Before EONET |
| Favicon | At deployment |

---

## First Build: The Shell and the APOD Panel

### Build prompt

> "Scaffold the Next.js app first — App Router, TypeScript strict, in this directory — then build the Cosmos shell and the APOD panel following PLAN.md. Use AGENTS.md for conventions and DESIGN.md for every visual value.
>
> The shell: the five-panel grid over the committed background, with the glass treatment from the spec.
>
> The APOD panel goes in the hero slot and needs all three states from CONTRACT.md — the ready state with the image, title and explanation; the empty state; and the error state. Some days APOD returns a video instead of an image, so the ready state has to handle that too.
>
> Fetch the data in a server component, reading the NASA key from an environment variable — it never reaches the client. Cache the response with Next.js's fetch cache so repeat visits don't re-hit the API. Don't build the other four panels yet."

### Visual correction

> "The panel border is heavier than the spec says and the active glow is barely visible. Pull both back to the values in DESIGN.md."

### First-slice checklist

- Shell grid over the committed background.
- Glass values match `DESIGN.md`.
- APOD ready handles both image and video `media_type`.
- Empty and error states exist in code.
- The NASA key is read server-side only.
- The response is cached.

---

## Improving Our App Shell

### Borrow a background technique, identified first

```bash
codex --add-dir /path/to/vibemail-fullstack
```

> "Look at /path/to/vibemail-fullstack/app/styles/ and app/components/ds/ — there's a background effect in there. Tell me exactly how it's built: is it a real shader running on canvas or WebGL, or is it CSS doing the work? I want to understand the actual mechanism before we decide how to bring it into Cosmos."

> "Now build the same background technique for Cosmos's shell, restyled to our palette — deep navy to black, the cyan and amber accents from DESIGN.md, nothing from vibemail's color scheme. Keep it as an enhancement layered over the existing static background, not a replacement, so the shell still renders correctly if this is ever turned off. Confirm it doesn't fight the glass panels for visual attention — they're the thing someone's actually looking at."

### Date navigation with a real floor

> "Add left and right chevron navigation to the APOD panel. Right moves forward a day, left moves back. Two real boundaries to handle, not just disable-and-forget: the right chevron can't go past today, since APOD has no future entries. The left chevron has a hard floor — the archive starts June 16, 1995 — and going further back than that isn't an error, it's just the edge of what exists. Give that boundary its own quiet, disabled treatment, distinct from the empty and error states already in CONTRACT.md. Update the URL or route so a specific date is shareable."

### One expanded view, built once

> "Build a full-page expanded view for panels, starting with APOD. Clicking the panel opens it full-screen: the full image at its actual resolution, the complete explanation text with no truncation, and the same three states — ready, empty, error — at this larger scale. Structure it so any other panel can opt into the same expanded view with minimal panel-specific code — the expansion mechanism itself shouldn't need rebuilding per panel, only the content each panel puts inside it."

Leave these changes uncommitted — they are reviewed together in the next lesson.

---

## Verifying the Build: Playwright and Browser

### Review before committing

> "/review"

CLI equivalent:

```bash
codex review --uncommitted
```

Fix what's real, dismiss style opinions, then commit the background, navigation, and expanded view as one unit.

### The IDE extension

Install the extension and open Cosmos in your IDE — same `AGENTS.md`, same config.

> "Open the APOD panel and walk me through what the last commit changed, as inline comments I can click through."

### Install Playwright MCP

```bash
codex mcp add playwright -- npx @playwright/mcp@latest
```

Start a new session, then confirm:

```text
/mcp
```

The browser opens headed by default.

| Tool | Reach for it when |
|---|---|
| Playwright MCP | A check you want to re-run and trust; network mocking |
| Browser | A one-off conversational look at `localhost` |

### Verify APOD

> "Check the APOD panel against DESIGN.md and CONTRACT.md. Open the running app, screenshot the ready state, and tell me whether the palette, glass treatment and active glow match the spec. Then drive the panel into its empty state and its error state and screenshot each one. Report anything that doesn't match."

> "Fix those two, then run the same check again."

Commit when APOD passes clean. A state nobody has looked at is a state you're hoping about.

---

## Designed Ambiguity: The ISS Panel

### Assets for the states

> "Generate a top-down International Space Station marker — solar arrays clearly readable in silhouette, single colour with the cyan accent, transparent background, legible at about 40px. Then two state icons in the same style, readable at 24px: a satellite fading into dots, for signal loss, and a broken link, for a failed call."

### Build prompt

> "Install MapLibre GL JS, then build the ISS panel following PLAN.md. Ready state: a MapLibre map with the ISS marker from our assets at the current position, the footprint value drawn as a translucent circle around it — that value is a diameter in kilometres, so it's a real coverage area, not decoration. Add a small readout of latitude, longitude, altitude and velocity, and tint the marker differently when visibility reports the station is in daylight versus eclipsed. Keep the marker a fixed size regardless of zoom.
>
> Then the two cases from CONTRACT.md, and they must stay separate.
>
> Signal loss — the request came back fine but there's no usable position: keep the ISS marker at its last known position, dimmed and desaturated with a faint trailing path behind it, pair it with the signal-loss icon, and show a quiet 'out of tracking range' note, with no retry button. This isn't an error and shouldn't look like one.
>
> Failed call — the request threw, timed out, or came back non-2XX: show a clear error state with the connection-failed icon and a retry.
>
> Tell them apart by the response status, not by looking at the position data. Don't merge them into one state.
>
> Then wire this panel into the full-page expanded view built for APOD — same pattern APOD already uses, minimal panel-specific code. The one thing worth getting right here: the map itself should fill the expanded view, not just render bigger inside the same small frame. Let MapLibre resize into the full space rather than scaling up a fixed-size canvas."

### The two causes

| Case | Detection | Treatment |
|---|---|---|
| Signal loss | 2XX, no usable position | Last-known marker dimmed with trailing path, signal-loss icon, "out of tracking range", no retry |
| Failed call | Thrown, timed out, or non-2XX | Explicit error, broken-link icon, retry |

### The trap: `suppress_response_codes`

WhereTheISS.at accepts a `suppress_response_codes` parameter. Set to true, every response comes back HTTP 200 — failures included, with an `error` property in the body. The detection rule is "2XX means it resolved," so that flag would make every failed call look like signal loss: a stale last-known position calmly displayed while the API is down. Never send it.

### Verify by mocking

> "Use panel_verifier to force both cases by mocking the position request's response — first a 2XX response carrying no usable position, then a failed response. Screenshot each and show me them side by side."

---

## Designed Ambiguity: NEO and Space Weather

### NEO icons

> "Generate two state icons in our established style, readable at 24px: a paused clock, for a rate limit, and a checkmark, for an all-clear reading."

### NEO panel

> "Build the NEO panel following PLAN.md. Ready state lists the close approaches with size and hazard flag.
>
> Then two separate empty-looking states. If we've hit the rate limit, pair the limit-reached icon with 'data paused — limit reached' and show when it resets. If the call succeeded and there genuinely are no objects, pair the all-clear icon with a note that the sky is clear.
>
> Decide which is which from the over-limit response, not by checking whether the list is empty. Keep them as distinct states.
>
> Then wire this panel into the full-page expanded view. The compact list can show a bar for diameter and a rounded miss distance to save space — the expanded view has room for the real numbers, so show the full miss distance and the full diameter range there instead of the compact version."

### Stale icon

> "Generate one more state icon in the same style: an hourglass, for stale data."

### Space Weather panel

> "Build the Space Weather panel following PLAN.md, using DONKI's GST endpoint. Take the most recent kpIndex from the allKpIndex array along with its observedTime.
>
> Ready state is an arc gauge, 0 to 9, and it changes character across three bands rather than just moving a needle. Kp 0 to 3 is quiet: cool cyan, low fill, still. Kp 4 to 5 is active: amber, arc filling further, a subtle pulse. Kp 6 to 9 is storm: red-shifted, near-full arc, and this should carry the most visual weight of anything on the dashboard. If there are no storm records in the window at all, that reads as quiet — accurate, not empty.
>
> Then the stale case. Compare observedTime to now. If it's older than the threshold in CONTRACT.md, drain the gauge of all three band colours — muted and grey — pair it with the stale-data icon, and show 'last observed' with the age. GST only records storms, so an old storm's Kp is not current conditions and must never be shown as though it were.
>
> A stale reading must never look like a live one, in any band.
>
> Then wire this panel into the full-page expanded view, same pattern as the others. There's not much more data to show here than the gauge already has, so keep the expanded view simple — the gauge larger, the observation time and band explained in a line of text, rather than inventing content to fill the space."

| Kp band | Character |
|---|---|
| 0–3 quiet | Cool cyan, low fill, still |
| 4–5 active | Amber, further fill, subtle pulse |
| 6–9 storm | Red-shifted, near-full, heaviest weight on the dashboard |
| Stale (any band) | Muted grey, hourglass, "last observed" with age |

_Stale data is the failure that looks like success. The only defence is to check freshness and show it._

### Verify both

> "Use panel_verifier on both new panels, mocking each API response rather than waiting for the real conditions to occur. For NEO, mock an over-limit response and then a successful empty one. For Space Weather, mock a fresh reading and then one with an observation time from six hours ago. Screenshot all four and show me each pair together."

Each pair must look clearly different. If an hours-old reading renders like a live one, the freshness check failed.

---

## The EONET Panel and a Consistency Pass

### Markers and icon

> "Generate four natural-event category markers, same treatment as the ISS marker but simpler — these sit at roughly 20px and there may be dozens on screen at once: wildfire, severe storm, volcano, and iceberg. Then one more state icon in our established style: a calm globe, for no active events tracked."

### EONET panel

> "Build the EONET panel following PLAN.md. Use the GeoJSON endpoint at eonet.gsfc.nasa.gov/api/v3/events/geojson with status=open, and feed it straight into MapLibre as a source — no reshaping.
>
> Ready state: category markers from our assets placed at each event's geometry, using the wildfire, storm, volcano and iceberg markers. Anything outside those four categories gets a neutral marker. A small count of active events somewhere in the panel.
>
> The empty state is real data, not a failure: if there are no open events in the window, pair the no-events icon with a note saying no active events are being tracked. Don't render it as an error.
>
> Then wire this panel into the full-page expanded view — same map-fills-the-frame treatment as ISS, not a scaled-up small view. Then have panel_verifier check it, the same way it just checked ISS and NEO."

The feed is consumed in its native GeoJSON shape — no translation layer between it and the map.

### Consistency pass

> "Capture all five panels in their ready state and put them side by side. Compare them against DESIGN.md — palette, glass treatment, border weight, accent usage, type scale, loading behaviour. Tell me every place a panel disagrees with another panel or with the spec."

> "Fix those, using the DESIGN.md values as the source of truth. Then re-capture all five so I can confirm."

---

## Reviewing the Quality of the Code and the Director's Audit

Nothing already checked gets re-checked. The audit is assembled from what is known.

### Range review from the first line of code

> "Find the commit right before the Cosmos shell and APOD panel were first built, and give me its short SHA."

```bash
codex review --base {the SHA from above}
```

### Whole-codebase review

> "Have the reviewer agent read the whole codebase, not just this range, and look specifically for anywhere two different failure causes ended up collapsed into one state."

### Audit prompt

> "Write AUDIT_6.md, pulling together everything already checked and adding what hasn't been.
>
> For consistency and code correctness — the AGENTS.md rules against collapsing failure causes and exposing the key — don't re-derive this. Pull it directly from what the reviewer agent and the range review already found, and summarize it here.
>
> For structural integrity, confirm the build followed PLAN.md's sequence and architecture, and reference the panel_verifier results from the EONET and consistency lesson rather than re-running them.
>
> Visual quality is the one dimension nobody's checked yet: does it match DESIGN.md, checked against the reference image dimension by dimension? Flag anything a screenshot can't show.
>
> For security, reviewer already confirmed credentials stay server-side in the code itself — that's covered too. What's still open is the mechanical scan reviewer can't do by reading source: git history, the client bundle, and runtime logs, for anything that could expose the NASA key. If the Security plugin isn't installed, install it with `codex plugin add security` and run that scan now. Show findings by severity, and for each one whether it's actually reachable in production or just theoretically possible.
>
> Be specific everywhere. Don't give me vague impressions."

### Audit dimensions

| Dimension | Source |
|---|---|
| Consistency and code correctness | Reviewer agent and range review |
| Structural integrity | `PLAN.md` and earlier panel_verifier runs |
| Visual quality | New — `DESIGN.md` against the reference image |
| Security | Reviewer for source; Security plugin for history, bundle, and logs |

Write deviations precisely: "the stale treatment uses the live accent colour, so a stale reading reads as current" — not "the weather panel feels off." Fix what is reachable in production, note the rest, and commit `AUDIT_6.md`.

---

## Checking Everything Deployed in Production

Two jobs, two tools: image generation for the favicon, Playwright for the share image.

### Favicon

> "Generate a favicon for Cosmos: a simplified version of the mission-patch style, on a transparent background, clear enough to read at 16px. Save it into the public assets folder."

### Share image

> "Use the Playwright MCP tools to screenshot the running Cosmos dashboard, all five panels in their ready state, at 1200 by 630. Save it into the public assets folder."

> "Wire the favicon into the site's metadata, and the screenshot into the Open Graph tags so it shows up when someone shares the link."

### Deploy

> "Deploy Cosmos to Vercel. The NASA key goes in as an environment variable on Vercel — it never goes in the repo."

### Second audit, aimed at production

> "Use panel_verifier against the deployed URL instead of localhost for all five panels. Confirm each one's ready state loads with real data, and re-run the ISS, NEO, and Space Weather ambiguity checks against production — mock the same failure conditions and confirm the same treatments still render. Report anything that behaves differently from what we saw locally."

### Production checklist

- All five panels load real data on the deployed URL.
- Every ambiguity state renders its own treatment in production.
- `NASA_API_KEY` set in Vercel only; absent from the bundle and history.
- Favicon visible in a browser tab; Open Graph image appears when the link is shared.
- APOD date URLs are shareable.

### End-of-project artifacts

`AGENTS.md`, `.codex/` rules, agents, and config, `CONTRACT.md` with source URLs, `PLAN.md`, the reference image and `DESIGN.md`, `public/assets/`, the five panels with every state, `AUDIT_6.md`, and the deployment.
