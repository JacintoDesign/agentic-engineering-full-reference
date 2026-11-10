# Module 8 Reference Guide
### Cursor — ZTM Music Player V2: Brownfield Planning, Jamendo, WebGL, and the Outer Loop

Use this guide alongside the Module 8 videos.

_Jamendo endpoints, Cursor plan features, and Bugbot billing change. Confirm the API against live docs and check the usage page before relying on a recording-era value._

---

## Table of Contents

1. [Opening the Existing Codebase for ZTM Music Player](#opening-the-existing-codebase-for-ztm-music-player)
2. [Writing the Planning Documents](#writing-the-planning-documents)
3. [Rebuilding the Core with Music Streaming using Jamendo](#rebuilding-the-core-with-music-streaming-using-jamendo)
4. [Using Debug Mode to Revise our Visualizer](#using-debug-mode-to-revise-our-visualizer)
5. [Finishing the Refresh and Starting the Verification Process](#finishing-the-refresh-and-starting-the-verification-process)
6. [The Cloud Outer Loop: Opening a Pull Request](#the-cloud-outer-loop-opening-a-pull-request)
7. [Deploying to Vercel, Final Testing and Fixes](#deploying-to-vercel-final-testing-and-fixes)

---

## Core Concepts

**An existing codebase is the harder case.** The work is understanding the old code well enough to change it safely, inside patterns the agent didn't write.

**Heavy reading is where cheap tokens pay off most.** Understanding code you didn't write is the long, token-heavy job Composer 2.5 is priced for.

**Change, Keep, Remove.** A refresh contract makes a three-way call on everything that exists. The Keep list is the brakes that stop a refresh becoming a rewrite.

**The contract decides once; AGENTS.md rules every time.** Across a long, multi-step rebuild, rules said in chat are forgotten by the third task.

**Plan Mode before diffs.** On someone else's code, a wrong approach silently breaks things three files away. Review the approach while it is still one editable document.

**Drift is good blank-folder work in the wrong place.** Point the agent back at the written rule rather than re-explaining.

**An error tells you where to look; a silence doesn't.** Debug Mode manufactures runtime evidence when the app misbehaves and says nothing.

**Inner loop keeps you fast; outer loop is part of the record.** Local checks all day; cloud review at the pull request.

---

## Opening the Existing Codebase for ZTM Music Player

### Create a separate repository

- Name it something like `ztm-music-player-v2`.
- Someone else's starter: fork it.
- Your own repo: GitHub won't fork into the same account — create a new repo and push the code across.
- The original stays deployed and untouched.

Open the new repo in Cursor and let Composer index it before asking anything.

### Survey prompt

> Survey this codebase. Summarise: the overall structure, how audio playback and the queue work, how the music source is connected, how state is managed, how components are organised, and how the visualizer is built. Then flag anything dated, fragile, or likely to fight a refresh.

Read it with a pen. Pay most attention to:

- How the existing music connection works
- Where state and data-access patterns live
- What the visualizer currently does

### The music source reality

The starter plays only locally uploaded MP3s. The refresh adds **Jamendo** — around half a million royalty-free Creative Commons tracks with a public API — as the primary source, and keeps local upload as a secondary path. A source-agnostic track model lets the player, queue, and UI work the same for either.

### Plan Mode

| Gate | Question |
|---|---|
| Review-as-it-lands (Waypoint) | "Show me the change before you apply it." |
| Plan Mode (refresh) | "Show me the approach before you write it." |

Plan Mode researches the codebase, asks questions, and produces an editable plan — files, order, approach — for approval before execution. A bounded, spec-dictated task doesn't need it; an uncertain approach across unfamiliar code does.

---

## Writing the Planning Documents

The agent that ran the survey drafts all four documents; you review and decide.

| Document | Says |
|---|---|
| `CONTRACT.md` | What changes |
| `AGENTS.md` | How to work here, across every task |
| `DESIGN.md` | How it should look |
| `TEST_PLAN.md` | How you'll know it worked — and that nothing broke |

### Refresh contract prompt

> Based on your survey, draft a CONTRACT.md for this refresh, with sections Change, Keep, Remove, plus short Rules and Out-of-scope. The central change is adding Jamendo streaming so the app plays from a real royalty-free catalogue instead of only locally-uploaded files. Alongside that: modernise the interface so it looks and feels like Spotify — dark, album-art-forward; make the audio visualisations markedly better, exploring a richer WebGL approach with three.js, with performance as a hard requirement, not an afterthought; and propose any other improvements that clearly move this from demo to portfolio quality. Under Keep, name what already works and shouldn't be rewritten. Propose the calls and flag anything risky; I'll make the final decisions.

Example result:

```markdown
# ZTM Music Player v2 — Refresh Contract

## Change
- Add Jamendo as the primary source — real, royalty-free, genuinely streamable
- Refactor playback, queue, search, and library to be source-agnostic (Jamendo or local)
- Add discovery: search, genre/mood stations, a small curated home
- Lift the visual design to portfolio quality — modern, Spotify-like (see DESIGN.md)
- Replace the visualizer with a WebGL / three.js build — audio-reactive, album-art-tinted, 60fps, paused when idle

## Keep
- The overall structure and navigation that works today
- The single-source player state — extend it, don't replace it
- The centralized data-access pattern — mirror it for the network client
- The local upload pipeline — retained as a secondary source under the library
- Component patterns that are sound — match them, don't replace them

## Remove
- The old Canvas 2D visualizer — replaced by the WebGL build
- The `window.__player_*` globals — replaced by context callbacks
- The Font Awesome CDN — replaced by tree-shakeable icons
- Anything the survey flagged as fragile and unused

## Rules
- Understand the existing pattern before changing it
- Match existing conventions unless this contract says otherwise
- All network access lives behind one typed client — no scattered fetch
- No secrets in the repo; the Jamendo client_id comes from an env var
```

### AGENTS.md prompt

> Write an AGENTS.md for this repository from what your survey found — the conventions worth keeping (where player state lives, how data access is centralised, the component and type patterns), plus rules for the Jamendo work (one typed client, secrets in an env var, confirm the API against current docs, not memory), the visualiser (WebGL with three.js, reading the analyser the player provider owns — treat performance as a contract: one render loop, no per-frame React state, paused when idle, reduced-motion respected, GPU resources disposed on unmount), and a drift self-check to run before finishing any task. Keep it tight; this is the rulebook every future task reads.

Example result:

```markdown
# AGENTS.md — ZTM Music Player v2

## Architecture to match (extend these, don't invent parallel ones)
- Player state lives in ONE place: the player hook/context. All playback,
  queue, shuffle/repeat, volume flow through it. No second state store.
- The Web Audio graph — source → analyser → gain → output — lives in the
  player provider, created ONCE. The visualizer reads the analyser; it never
  makes its own (createMediaElementSource attaches once per audio element).
- Network access lives in ONE typed client (src/services/jamendo). No fetch
  scattered through components.
- Local persistence stays behind the existing IndexedDB module.
- Types live in types.ts. One source-agnostic Track shape that both Jamendo
  and local map onto, so the player doesn't care where a track came from.

## Jamendo rules
- Base URL https://api.jamendo.com/v3.0; client_id on every request.
- Confirm endpoints/params against CURRENT live docs (not memory) — via
  context7's Jamendo library or @Docs pointed at developer.jamendo.com.
- Stream via /tracks/file?id={id}&action=stream&audioformat=mp32 — don't
  cache the raw audio URL; artwork from the image field.
- crossOrigin="anonymous" on the audio element BEFORE src, or the analyser
  reads silence.
- Keep Creative Commons attribution visible.

## Visualizer rules
- WebGL (three.js + React Three Fiber), lazy-loaded on Now Playing.
- One render loop, reading analyser data via refs — no per-frame React state.
- Pause when not playing, tab hidden, or routed away; dispose GPU resources
  on unmount.

## Guarding against drift (self-check before finishing any task)
1. Did I extend an existing pattern or invent a parallel one? (Extend.)
2. Did I touch anything on the CONTRACT.md Keep list? (Don't.)
3. Is all network access still behind the one client? (It must be.)
4. Is player state — and the audio graph — still in one place? (It must be.)
```

### DESIGN.md: generate, then make it yours

```bash
npx getdesign@latest add spotify
```

Then add the project-specific target:

> Append a project section to the generated DESIGN.md defining what "better" means for this app: the now-playing area and album art as the single visual anchor, controls secondary; album-art-driven colour tinting ambient surfaces only, green reserved for play; a signature audio-reactive visualiser — richer than the original, exploring a WebGL/three.js treatment — that holds 60fps and pauses when idle; and defined interaction states (resting / hover / playing, loading, empty) so nothing reads as a default.

| Target | Meaning |
|---|---|
| The anchor | Now-playing area and album art; controls secondary; navigation quiet |
| Album-art colour | Dominant artwork colour tints ambient surfaces only; green reserved for play |
| The visualizer | Audio-reactive, artwork-tinted, WebGL, 60fps, paused when idle |
| Interaction states | Resting, hover, playing, loading, and empty all designed |

### Test plan prompt

> Draft a TEST_PLAN.md from the contract and survey. A "New behaviour (Jamendo)" section — search returns playable tracks, streaming playback with the transport controls, the queue, genre/mood stations, library persistence, the visualiser reacting to the real audio. A "Still works (must not break)" section for what the refresh mustn't regress — navigation, the now-playing bar, shuffle/repeat, session restore, everything on the Keep list. And edge cases — no search results, an unavailable track, a missing API key, an empty queue. Write each check so a subagent can drive the running preview and confirm it.

Example result:

```markdown
# ZTM Music Player v2 Test Plan

## New behaviour (Jamendo)
- Search returns Jamendo tracks; results are playable
- Playing a track streams audio and updates the now-playing display
- The queue adds, reorders, and advances correctly
- Genre/mood stations load and play
- Saving and removing from the library sticks
- The visualizer reacts to the REAL streaming audio and is tinted by album art
- Now Playing shows the artist and a "via Jamendo" credit

## Still works (must not break)
- Navigation is unchanged from before
- The now-playing bar persists across routes and on mobile
- Shuffle, repeat, and session restore behave as they did
- Everything in the "Keep" list behaves as it did

## Edge cases
- A search with no results shows an empty state, not an error
- Playing an unavailable track fails gracefully and the queue advances
- A missing Jamendo client_id fails at startup with a clear message
- An empty queue and the end of the queue behave sensibly
```

Commit all four documents together.

---

## Rebuilding the Core with Music Streaming using Jamendo

### One feature branch

```bash
git checkout -b refresh
```

All rebuild work lands on `refresh`, so `main` keeps the original and the refresh arrives as one reviewable change.

### Plan Mode prompt

> In Plan mode: plan the refresh against CONTRACT.md, AGENTS.md, DESIGN.md and TEST_PLAN.md — the Jamendo swap, then rebuilding playback, queue, search, library, and discovery. Research the existing code first. List the files you'll touch, the order, and where you'll extend existing patterns rather than replace them. Ask me anything ambiguous before writing the plan.

### Reading the plan

- **Wrong:** rewriting something on the Keep list, a second state store, the queue built before playback.
- **Quiet scope calls:** tests added, number of visualizer presets, where playlists live — accept or change them out loud.
- **Thin:** sections that meet the docs without exceeding them, such as a vague "browse."

### Amend, then scope execution

> Amend the plan before executing. In the discovery phase, be specific: stations built from genre and mood tags, not only Jamendo's fixed radios; a curated home explicitly ordered by popularity, not a vague browse. And in the client, request artwork at a size large enough for a full-bleed now-playing hero — the design leans on that image.

> Approved with those amendments. Execute the plan in order, up to but not including the visualizer phase — stop there; the visualizer is a separate pass. Check each phase's gate before moving to the next, and show me the Jamendo client and Track type before wiring them in.

_A plan you rubber-stamp is theatre; a plan you argue with is direction._

### Jamendo credentials

1. Go to `devportal.jamendo.com` and create a developer account.
2. Create an application and accept the API terms.
3. Copy the private `client_id`. The default read-only plan covers search, tracks, radios, and streaming.
4. Jamendo's public test key `709fa152` works for a quick sanity check; register your own for the real build.

```dotenv
# .env  — gitignored, never committed
VITE_JAMENDO_CLIENT_ID=your_client_id_here

# .env.example  — committed: the shape, not the value
VITE_JAMENDO_CLIENT_ID=
```

A `VITE_` variable is compiled into the browser bundle. A read-only Jamendo `client_id` is a public identifier, so that's acceptable. A true secret could never live in a `VITE_` variable.

### Jamendo client prompt

> Add Jamendo as the primary music source, per CONTRACT.md and AGENTS.md, and keep local upload as a secondary path. Use the current Jamendo API docs from a live-docs source (context7's Jamendo library or @Docs on developer.jamendo.com) — don't rely on memory for the endpoints or params; stream via /tracks/file?action=stream. Build one typed client: search, track details, streaming links, artwork, and stations. Introduce one source-agnostic Track type that both Jamendo and local files map onto, so the player doesn't care where a track came from. Match the existing data-access conventions rather than inventing a new pattern. Show me the client and the Track type before wiring them in.

Flag now, before the visualizer pass:

- The audio source node and analyser live in the player provider, created once.
- The audio element sets `crossOrigin="anonymous"` before its `src`.

### Discovery prompt

> Build out discovery: genre and mood stations from tag search alongside Jamendo's radios, and a curated home ordered by popularity so the app opens on real, alive content. Pull artwork at a size that holds up as a full-bleed hero. Keep it all behind the one typed client.

Then use it: try to find music you'd actually listen to.

### Build order

```text
Jamendo client + Track type
  → Playback
    → Queue
  → Search    (independent of library)
  → Library   (independent of search)
  → Discovery (after playback is solid)
```

Each piece lands on `refresh` and is checked against its test-plan section before the next is built on top.

### Drift signs and correction

| Sign | Correction |
|---|---|
| A second state store appears | "Re-read AGENTS.md; you've added a second state store where it says there's one." |
| `fetch` inside a component | Point back at the one-client rule |
| A Keep-list component rewritten | Point back at `CONTRACT.md` Keep |
| A new file where the rules say extend | Point back at "extend, don't invent" |

---

## Using Debug Mode to Revise our Visualizer

### The target is already written

- Three presets, artwork-driven colour, green as accent only.
- 60fps desktop, 30fps floor on mobile.
- Capped device-pixel ratio; draw-call and particle budgets.
- No render loop when paused or hidden.

### Execution prompt

> Execute the visualizer phase of the plan. Stop at its gate and report.

A plan that makes later prompts shorter was worth writing. Watch for the agent building its own audio graph inside the visualizer — that is drift; point it back at `AGENTS.md`.

Shipping one genuinely good preset and logging the other two as follow-ups is a legitimate call if three runs long.

### Renders but doesn't react

Symptom: geometry, colour, and motion all render; nothing reacts to the music; no console error. Likely cause: `crossOrigin="anonymous"` missing or set after `src`, so the analyser reads zeros.

Switch to **Debug Mode**:

> The visualizer renders but never reacts to the audio — no errors in the console. Find the root cause.

Debug Mode generates hypotheses, instruments the code, asks you to reproduce, pinpoints the cause from observed runtime data, then removes its instrumentation.

| Situation | Use |
|---|---|
| You have a stack trace | Normal agent — the error is the runtime information |
| Wrong behaviour, no error | Debug Mode |

### Two reviews of one build

**Is it alive?**

- Play tracks with real dynamics; it responds to the music, not just animates.
- Colour follows the artwork.
- Cycle every preset.

**Is it free?**

- Pause the music — the render loop stops.
- Switch tabs and return.
- Navigate away from Now Playing — GPU resources are disposed.
- Watch the performance panel frame rate against the contract's number.

Commit the visualizer as its own checkpoint on `refresh`.

---

## Finishing the Refresh and Starting the Verification Process

### Polish phase

> Execute the final polish phase of the plan.

Expected scope: manifest and service worker, theme setting, attribution footer, README, and removals — the old canvas visualizer, the icon CDN, deprecated metadata packages, unreachable routes.

- **README** — fix or cut every claim about features that don't exist.
- **Attribution** — "Music powered by Jamendo," the artist link, and the licence are Creative Commons terms, not polish.

### Run the inner loop

```text
/audit
```

The user command from Waypoint carries over. On a refresh, weight the **Still works** section most heavily — those failures are quiet, in the corner you weren't watching.

1. Triage findings.
2. Fix the must-fixes.
3. Re-run, including Still works — a fix can break something too.
4. Commit so the branch is clean.

---

## The Cloud Outer Loop: Opening a Pull Request

### Inner vs outer

| Inner loop | Outer loop |
|---|---|
| Local, fast, cheap, private | Cloud, on the shared PR |
| Findings are yours alone | Findings are inline comments on the record |
| Runs against the app in front of you | Reads the full change in the context of the whole codebase |

### Enable Bugbot first

In the Cursor dashboard → **Bugbot**: connect GitHub, enable Bugbot for `ztm-music-player-v2`, and leave the trigger on **automatic for every PR**. Do this before the PR exists, or it opens to silence.

### Open the PR

> Push the refresh branch and open a pull request against main. Write a clear description: what changed, what was kept, what to watch for in review. Note that this adds Jamendo streaming as the primary source while keeping local upload, and point reviewers at CONTRACT.md and AGENTS.md.

Opening the PR triggers Bugbot. To re-run manually, comment on the PR:

```text
bugbot run
```

or

```text
cursor review
```

Pushing a fix triggers a new review automatically.

### Reading the review

- Most useful on a refresh: comments tracing how the change rippled — Jamendo work touching something it shouldn't, a streamed URL or env key the old local code never handled.
- Real findings get fixed on the branch and pushed.
- Noise gets the thread resolved.
- A quiet review after a clean inner loop is the inner loop working.

### Cost

- Cloud Bugbot bills on top of the plan, on the premium meter.
- Use the local inner loop during the build; bring in the cloud at the PR.
- Dedicated cloud Security Review and an automated approval agent are team-plan features. On an individual plan the outer loop is Bugbot plus your own decision to merge.

---

## Deploying to Vercel, Final Testing and Fixes

### The refresh question

Is it genuinely better than what it replaced?

- Music streams where before it only played uploaded files.
- Discovery — stations and a curated home worth exploring, not a search box with extra steps.
- Visualizer — alive and reacting, not busier and slower.
- Visual lift — a clear step up against `DESIGN.md`, not a reshuffle.

### Walk, fix, merge

1. Search for music you want; start a station; build and play a queue; save to the library; watch the visualizer.
2. Keep a running list; sort fix-now from log-for-later.
3. Send fix-now items to Composer — Design Mode for visual ones — and push them through Bugbot again.
4. Commit the list as `AUDIT_3_ZTMV2.md`.
5. Merge only when your own review clears it. The gate is you.

### Deploy

Deploy as a **new** Vercel project connected to `ztm-music-player-v2`, so the original deployment stays untouched.

> Prepare ZTM Music Player v2 to deploy on Vercel. Confirm the environment variables — including the Jamendo client_id — are validated at startup, and the production build streams audio and runs the visualizer correctly. Walk me through the deploy.

### Production checks

- Search, start a station, play, and queue on the live URL.
- Streamed audio feeds the analyser in production (cross-origin rules especially).
- `VITE_JAMENDO_CLIENT_ID` is set in Vercel.
- Attribution is visible.
- The original app's deployment is unchanged.

### What carries forward from the Cursor modules

- Match the model to the task.
- Work from the Agents Window; decide what's independent yourself.
- Set the safety model deliberately.
- Extend the tool only where it earns it — Context7, a generated design spec made yours, a rules file, a skill, a command.
- Plan and verify harder the more the agent builds.
- Two loops — local inner, cloud outer — with your judgement as the merge gate.

### End-of-project artifacts

Refresh `CONTRACT.md`, `AGENTS.md`, `DESIGN.md`, `TEST_PLAN.md`, the approved plan, the Jamendo client and source-agnostic Track model, the WebGL visualizer, the reviewed PR, `AUDIT_3_ZTMV2.md`, and the deployment.
