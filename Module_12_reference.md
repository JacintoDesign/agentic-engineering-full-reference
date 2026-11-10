# Module 12 Reference Guide
### Codex — Bonus Projects: An Interview Skill, a 1996 Desktop, Three Magazines, and ChatGPT Astra

Use this guide alongside the Module 12 videos.

_Codex surfaces such as Ultra, `/goal`, worktree settings, and `$skill-creator` are release-sensitive. Confirm current names and behaviour in the Codex docs._

---

## Table of Contents

1. [Using the Skill-Creator to Create AGENTS.md, CONTRACT.md, DESIGN.md](#using-the-skill-creator-to-create-agentsmd-contractmd-designmd)
2. [A 1996 Operating System in a Browser Tab (Chicago Project)](#a-1996-operating-system-in-a-browser-tab-chicago-project)
3. [Leveraging Image Generation to Make 3 Magazines About Web Development (Periodical Project)](#leveraging-image-generation-to-make-3-magazines-about-web-development-periodical-project)
4. [Introduction to ChatGPT Astra](#introduction-to-chatgpt-astra)

---

## Core Concepts

**You decide; the agent writes it down.** A spec generator that infers a project from a one-line description produces generic defaults — the failure `DESIGN.md` exists to prevent. The skill asks instead.

**Sparse is a rule, not a style.** `AGENTS.md` is loaded on every turn. Four sections, bullets only, nothing more.

**An unanswered question should leave a visible hole.** Missing answers become `TODO`, never a plausible default.

**Specs before the split.** `AGENTS.md`, `CONTRACT.md`, and `DESIGN.md` committed before any worktree exists are the only reason isolated threads produce one coherent thing.

**You can hand over the decomposition precisely because you didn't hand over the standards.**

**Convergence is proven by a script, not by looking.** A merge test written into `CONTRACT.md`'s DONE section, run with Playwright, catches what a screenshot can't.

**The image model does not set the type.** Generated art reserves zones; live HTML carries every word.

**A quote traces to a source or it doesn't run.** Verified once, then copied from the source ledger — never retyped.

---

## Using the Skill-Creator to Create AGENTS.md, CONTRACT.md, DESIGN.md

Builds the tool both remaining bonus projects open with.

### The sparse target

```markdown
# AGENTS.md — Cosmos

## Identity
Cosmos is a glassmorphic NASA data dashboard with five live panels.
Read-only: it displays public NASA data and stores nothing user-specific.

## Stack
- Next.js 16 (App Router), React 19, TypeScript strict
- No separate backend — the NASA key is read server-side from an environment
  variable, never client-side
- Responses cached with Next.js's fetch cache (next: revalidate), shared
  across visitors
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

The `DESIGN.md` target is the Google format — tokens as YAML frontmatter, reasoning as prose:

```markdown
---
name: Waypoint
colors:
  paper: "#F6F1E9"
  text: "#2A2521"
  accent: "#C4552D"
typography:
  heading: { fontFamily: Fraunces }
  body: { fontFamily: Inter }
---

## Overview
Editorial, warm, photo-forward — a printed travel guide, not a dashboard.
```

### Skill creation prompt

> "Use $skill-creator to create a Codex skill called spec-interview.
>
> The skill scaffolds three project documents — AGENTS.md, CONTRACT.md, and DESIGN.md — for a new project. It must not infer them from a description. It runs an interview first, in three passes, and writes only from the answers given.
>
> Pass one, AGENTS.md: what the project is in two lines and what it explicitly isn't; the stack; data sources and whether there's a backend; deploy target; file-layout and naming conventions; and the one shortcut that would gut the project if an agent took it.
>
> Pass two, CONTRACT.md: the pieces and what each must do; the states each piece has; which states could be confused for one another and must render differently; behaviour at each failure or edge case.
>
> Pass three, DESIGN.md: mood in one line; heading and body fonts — offer three Google Fonts pairings suited to the stated mood, and accept a custom pair; palette as base, text and one accent, with the accent's permitted uses; light, dark or both; density and corner radius; and whether shadows, gradients and transitions are permitted at all.
>
> Rules for the output. AGENTS.md has exactly four sections — Identity, Stack, Conventions, Never do — bullets only, no prose paragraphs, no section over eight lines, and Never-do rules must name specific forbidden shortcuts rather than state general principles. DESIGN.md follows the Google DESIGN.md format: machine-readable tokens as YAML frontmatter, reasoning as prose sections below. Never invent an answer that wasn't given — write TODO and say so in the summary.
>
> Store templates for all three documents in the skill's assets/ folder and fill them from the answers."

### The three passes

| Pass | Document | Asks for |
|---|---|---|
| 1 | `AGENTS.md` | Two-line identity and what it isn't, stack, data sources, backend, deploy target, conventions, the one gutting shortcut |
| 2 | `CONTRACT.md` | Pieces, their states, confusable states, failure and edge behaviour |
| 3 | `DESIGN.md` | Mood, three font pairings, palette and accent uses, light/dark, density and radius, whether shadows/gradients/transitions are allowed |

### Skill layout

```text
~/.codex/skills/spec-interview/
  SKILL.md         the interview, the rules, the output format
  assets/
    AGENTS.template.md
    CONTRACT.template.md
    DESIGN.template.md
```

### Prove it asks instead of guessing

```bash
codex
```

> "Invoke the spec-interview skill. The project is a browser-based drum machine."

- It should start asking, not return three finished documents.
- It should offer font pairings suited to the mood you give it.
- A deliberately skipped question should become a visible `TODO`.
- `AGENTS.md` stays four sections and under a screen.
- `DESIGN.md` frontmatter holds only values you stated.

If it drifts, correct `SKILL.md` through `$skill-creator` and re-run — don't hand-edit the output.

### A repo of its own

The skill doesn't belong in the `my-standards` Claude Code plugin — different product, different install mechanism.

```bash
mkdir spec-interview-skill && cd spec-interview-skill
cp -r ~/.codex/skills/spec-interview/* .
git init
git add .
git commit -m "spec-interview: interview-driven AGENTS.md, CONTRACT.md, and DESIGN.md generator"
```

Push it and add a short README: what it does, the three-pass shape, and the Google `DESIGN.md` format.

| Copy | Role |
|---|---|
| `~/.codex/skills/spec-interview/` | The live one — the only copy Codex reads |
| The GitHub repo | Reference material — nothing installs from it |

_The repo is what a student reads. The local copy is what Codex runs. Keep them in sync in that direction, never the other way._

### Skill vs custom agent

| | Custom agent (Cosmos) | Skill (spec-interview) |
|---|---|---|
| Location | `.codex/agents/` in one project | `~/.codex/skills/` |
| Model tier | Pinned | None |
| Used | Inside a build, as part of a declared team | By name, from any project — before a project exists |

---

## A 1996 Operating System in a Browser Tab (Chicago Project)

**Chicago** — a mid-nineties desktop environment in a browser tab: window manager, virtual filesystem, terminal, text editor, paint, and file browser, all sharing state. No API, no key, no network.

You don't decompose this project. No `PLAN.md`, no declared team. You describe the end state and hand the split to Ultra.

### The committed specs

`spec-interview` produced these off screen.

`AGENTS.md`:

```markdown
# AGENTS.md — Chicago

## Identity
Chicago is a mid-nineties desktop environment that runs entirely in a browser tab.
No backend, no network, no persistence — everything lives in memory for the session.

## Stack
- Next.js (App Router), React, TypeScript strict
- Tailwind for layout only — every visual value comes from DESIGN.md tokens
- A single state store at the root; no server, no database, no API calls
- Runs locally, never deployed

## Conventions
- One app per directory under app/components/{app-name}
- Virtual filesystem paths are DOS-style strings — C:\ root, backslashes, uppercase
- Any state another app might read lives in the root store, never in a component
- Visual values come from DESIGN.md tokens, never picked by hand

## Never do
- Never create a second state store, even a small one, for convenience
- Never use a shadow, border-radius, gradient, or transition — see DESIGN.md
- Never antialias UI type or use fractional pixel sizes
```

Both Never-do rules are load-bearing: four threads each needing a filesystem will each build one, and every modern agent instinct is to soften and round.

`DESIGN.md`:

```markdown
---
name: Chicago
colors:
  face: "#C0C0C0"
  faceLight: "#FFFFFF"
  faceShadow: "#808080"
  faceDark: "#000000"
  titleActive: "#000080"
  titleActiveText: "#FFFFFF"
  titleInactive: "#808080"
  desktop: "#008080"
  text: "#000000"
typography:
  ui: { fontFamily: "MS Sans Serif, Tahoma, sans-serif", size: 11, antialias: false }
  mono: { fontFamily: "Courier New, monospace", size: 12, antialias: false }
geometry:
  bevel: 2
  radius: 0
  focusOutline: "1px dotted #000000"
  iconGrid: 32
effects:
  shadow: none
  gradient: titlebar-only
  transition: none
---

## Bevel system
Raised controls take faceLight on their top and left edges, faceShadow on
bottom and right, at the bevel width. Sunken wells reverse it. Buttons swap
raised to sunken on mousedown — instantly, with no transition.

## Focus
A focused window is distinguished from an unfocused one by title bar colour
alone — titleActive against titleInactive. Never by shadow, blur, or scale.
```

`CONTRACT.md` excerpts:

```markdown
## Filesystem
Boots pre-seeded:
  C:\DOCUMENTS\README.TXT
  C:\DOCUMENTS\NOTES.TXT
  C:\PICTURES\SAMPLE.PNG
  C:\SYSTEM\ (empty)
Every operation, from any app, is visible to every other app without a
page refresh. There is exactly one filesystem. Two apps each holding
their own copy is a contract violation, not an implementation detail.

## Terminal
Commands: ls, cd, cat, mkdir, touch, rm, clear, and echo "text" > file.txt.
echo redirect creates the file if it doesn't exist, overwrites it if it
does, and the written content is immediately readable by cat and by the
editor — the single most important behaviour in the contract, since every
other check depends on it.
```

`CONTRACT.md` also carries a **DONE** section — the performable merge test `/goal` points to.

### Scaffold

> "Scaffold a new Next.js app with TypeScript and Tailwind. Nothing else yet — just the base project running, with a full-viewport flat teal background and no chrome. This is going to become a mid-nineties desktop environment; don't build any of it yet."

Commit the scaffold, then turn worktrees on in Settings and declare anything each checkout needs copied in, such as an env file or local config.

### The goal, handed to Ultra

Switch the model picker to **Ultra** first, then send the goal and handoff as one message:

```text
/goal Build a working desktop environment that runs entirely in the browser,
to AGENTS.md, CONTRACT.md, and DESIGN.md exactly.

A window manager, a virtual filesystem, and four apps: file browser, text
editor, terminal, paint. Behaviour, states, and every failure case are
CONTRACT.md's job. Every visual value is DESIGN.md's job. Nothing in this
goal restates either — read them instead of guessing from this summary.

DONE is CONTRACT.md's DONE section, run exactly as written.

Use Ultra to work toward this goal. Every thread reads AGENTS.md,
CONTRACT.md, and DESIGN.md before writing anything, and builds chrome
from DESIGN.md's values rather than inventing its own. Split this however
makes sense to you and give each piece its own worktree — decide the
division yourself. Report back once there's a desktop on screen with at
least one window that opens and drags.
```

Check progress:

```text
/goal
```

Compare Ultra's seams to the ones you'd have drawn. You'd likely split by app; Ultra may split by layer — filesystem and window manager underneath, apps on top — because that's where the coupling is.

_A declared team is a decomposition you designed once and reuse. Ultra is one made fresh, for a task that won't recur._

### The DONE check with Playwright

> "Use Playwright to run CONTRACT.md's DONE check against the running app, in both directions. First: open the text editor, create notes.txt in DOCUMENTS, type a line into it, and save. Open the file browser and confirm notes.txt is there without refreshing the page. Open the terminal, cd to DOCUMENTS, and cat notes.txt — confirm the line comes back. Then reverse it: in the terminal, echo a different line into second.txt, and open second.txt in the editor to confirm those exact contents load. Finally open paint, draw something, save it, and confirm it appears in the file browser. Report which steps passed and which failed, with a screenshot of each failure."

### Reconcile the filesystem

> "The editor and the terminal are each holding their own filesystem — they were built separately and never actually connected. There should be exactly one filesystem, owned in one place, that all four apps read and write through. Reconcile them and re-run CONTRACT.md's DONE check."

### Visual pass

> "Build a page showing one window from each of the four apps at the same size, side by side, with their title bars, menu bars, buttons, and scrollbars visible. Then check each against DESIGN.md's tokens and report any value that doesn't match."

> "The bevels are flat — they read as borders rather than raised and sunken surfaces. Every raised control needs a light face on top and left and a dark face on bottom and right, and every sunken well needs the reverse, exactly as DESIGN.md specifies. Buttons must visibly depress on mousedown."

> "The terminal is the piece that sells this. Give it a real prompt showing the current path, command history on the up arrow, tab completion, a block cursor, and a proper error line when a command doesn't exist."

Repeat whenever it creeps back in:

> "Remove every shadow, rounded corner, gradient, and transition you've added. Nothing in this environment fades, slides, or blurs — state changes are instant and drawn in pixels."

### What transfers

- When to reach for Ultra versus a declared team.
- Worktrees buy isolation; the bill arrives at merge time.
- A goal written as a pointer to a performable test is short and exact.
- Playwright verifies cross-app interaction sequences no screenshot can.

---

## Leveraging Image Generation to Make 3 Magazines About Web Development (Periodical Project)

Three magazines on the history of web development, and a coffee table to leave them on.

| Volume | Era |
|---|---|
| I — The Early Years | First web server to the browser wars: hand-written HTML, table layouts, animated GIFs, `<blink>`, "best viewed in Netscape" |
| II — The Modern Era | React to the component web: build tooling, SPAs, TypeScript, frameworks and the complexity backlash |
| III — AI-Augmented | Autocomplete to autonomy: Copilot, vibe coding, the tool explosion, agentic engineering |

Everything generated here is made once, saved, and committed. The site reads a folder of images and never calls a model again.

### Shared AGENTS.md excerpts

```markdown
## Stack
- Plain HTML, CSS, and vanilla JS — no framework, no build step, no npm install
- Every image is a static file loaded with a plain `<img>` tag

## Conventions
- Image filenames: {volume}-{spread}-{slug}.png, e.g. 02-04-react-arrives.png
- One HTML file per volume: volume-one.html, volume-two.html, volume-three.html
- Spread markup is generated into the volume file, not assembled at runtime
- Every quote is inserted from a verified line in that volume's SOURCES.md

## Never do
- Never let two volumes write to the same asset filename
- Never add a package.json, bundler, or framework dependency
- Never hand-write a quote directly into the markup
```

### Scaffold — bare files only

> "Create a static site — plain HTML, CSS, and vanilla JS. No framework, no bundler, no package.json. Just index.html, styles.css, app.js, and asset folders: assets/covers/, assets/spreads/, assets/textures/. index.html should be empty except for a comment. Do not build the home page, the pile view, any volume page, or any component — no masthead, no cover mockups, nothing styled. Bare files only."

If a looser run already built mastheads or covers, delete the output and reissue — don't patch work built ahead of the specs.

```bash
python3 -m http.server 8000
```

### Shared contract, three designs

`CONTRACT.md` holds only what is identical across volumes — masthead placement, trim proportions, cover grid, type scale, spread grid, pull-quote treatment — as numbers. Its DONE section: the three covers side by side must read as three issues of one magazine from three decades.

Each volume gets its own `DESIGN.md`:

```yaml
# Volume I — The Early Years          # Volume II — The Modern Era
colors:                                colors:
  paper:  "#EDE6D6"                      paper:  "#FFFFFF"
  ink:    "#1A1A1A"                      ink:    "#16181D"
  accent: "#D6482B"                      accent: "#4C6FFF"
typography:                            typography:
  heading: { fontFamily: Oswald }        heading: { fontFamily: Inter }
  body:    { fontFamily: Roboto Slab }   body:    { fontFamily: Inter }
illustration:                          illustration:
  medium:  flat-vector-diagram           medium:  flat-geometric
  texture: coarse-halftone               texture: none
  artefact: registration-drift           artefact: soft-gradient
  palette_limit: 4                       palette_limit: 6
```

```yaml
# Volume III — AI-Augmented
colors:
  paper:  "#0B0B0C"
  ink:    "#F2F2F0"
  accent: "#C6F24E"
typography:
  heading: { fontFamily: Instrument Serif }
  body:    { fontFamily: IBM Plex Sans }
illustration:
  medium:  generative-render
  texture: fine-grain
  artefact: soft-depth
  palette_limit: 3
```

### You draw the split this time

> "Create 3 separate Codex tasks, each using an app-managed Git worktree — name them volume-one, volume-two, and volume-three, matching the volume filenames in AGENTS.md. Don't use manual shell worktrees, and don't modify the main checkout. Each task reads AGENTS.md and CONTRACT.md before starting, then builds one volume's editorial plan — research with Browser from primary sources, plan eight to ten spreads with a headline, a short body section, and a pull quote each, and write that volume's own SOURCES file — SOURCES.volume-one.md, SOURCES.volume-two.md, or SOURCES.volume-three.md — with every quote's speaker, date, context, and source URL. Don't generate illustrations yet in any task; each one stops at the editorial plan.
>
> Task 1 — worktree volume-one, Volume I, The Early Years. Research the first web server through the browser wars: HTML by hand, tables for layout, animated GIFs, the `<blink>` tag, "best viewed in Netscape."
>
> Task 2 — worktree volume-two, Volume II, The Modern Era. Research React's arrival through the component web: build tooling, single-page applications, TypeScript's adoption, the framework era, and the complexity backlash.
>
> Task 3 — worktree volume-three, Volume III, AI-Augmented. Research autocomplete through autonomy: Copilot, the coining of vibe coding, the tool explosion, and the shift to agentic engineering."

| | 1996 desktop | Three volumes |
|---|---|---|
| Who splits | Ultra | You |
| Why worktrees | Coupled pieces built in isolation, reconciled at merge | Independent pieces with nothing shared |

### Verify every quote

> "Take every quote across all three editorial plans and verify each one against a primary source with Browser — the original post, the recorded talk, the published interview. For each, record speaker, date, context, exact wording, and the URL. Flag anything you cannot trace, and say specifically what's wrong."

> "Cut every quote that didn't trace and replace it from a verified source. Write it into that volume's own SOURCES file, with speaker, date, context, exact wording, and URL for every surviving quote."

### Covers

Run in each volume's worktree:

> "Generate the cover illustration for this volume only, to DESIGN.md exactly, composed around CONTRACT.md's reserved masthead and coverline zones. No text in the image. Save to assets/covers/."

> "Build a page showing the three covers side by side at equal size, and check each against CONTRACT.md's stated masthead placement, trim ratio, and type scale."

_The image model does not set the type._ A masthead baked into art is nearly right and can't be edited; a reserved zone gets a live, sharp, accessible DOM title.

### Spreads (Volume II shown)

> "Generate eight spread illustrations for Volume II, to DESIGN.md exactly — the same DESIGN.md the approved cover was made from. Each illustration is composed for its spread: reserve the space the headline and pull quote occupy per CONTRACT.md's grid. No text rendered in any image. Save to assets/spreads/."

> "Build volume-two.html: eight spreads, each combining that spread's illustration from assets/spreads/ with its headline, body copy, and pull quote from the editorial plan, laid out to CONTRACT.md's spread grid and type scale exactly, in this volume's DESIGN.md typefaces and colours. Every pull quote's text must match its entry in SOURCES.volume-two.md exactly — pull the wording from there, don't retype it."

### Merge and clean up

```bash
git checkout main
git merge volume-one
git merge volume-two
git merge volume-three
```

Confirm three covers in `assets/covers/` and all three `volume-*.html` files on `main`, then:

```bash
git worktree remove volume-one
git worktree remove volume-two
git worktree remove volume-three
```

### The coffee table

> "Build the home view: the three volumes in a pile on a coffee table surface. Overlapping, each rotated a few degrees differently, with real thickness on the spine edge, contact shadows where they rest on each other, and a soft ambient shadow on the table. It should look like three magazines someone put down, not three rectangles in a stack."

> "Clicking a volume opens it into the reading view: a two-page spread with a page-flip that has weight — perspective on the turning page, a curl at the outer edge, a shadow deepening in the gutter. Arrow keys and clicking the page edge both turn pages. Closing returns to the pile with the volume settling back into place."

### Final check

> "Use Playwright to open each volume and turn every page. For each spread, confirm the illustration matches the headline's subject, the pull quote's speaker appears in that volume's SOURCES file, and no text is baked into any image. Report anything that doesn't line up, with a screenshot."

---

## Introduction to ChatGPT Astra

A short showcase rather than a build. There's no planning system and no prompt-by-prompt walkthrough here. You look at three projects, each made from a **single one-shot prompt**, and see what that says about the tool. One-shot prompts to rebuild each are under [Build them yourself](#build-them-yourself).

### The takeaways

**One prompt, one finished piece.** Each showcase came from one prompt, with no plan file, contract, or follow-up loop behind it. That doesn't replace the director's workflow. It shows how much a single well-aimed prompt can produce now.

**It models 3D from nothing.** This is the standout. Every object in all three projects is procedural geometry, built in code. There are no downloaded models, textures, or image assets, and the results look good: proper materials, lighting, shadows, and detail.

**It verifies its own work.** The projects arrive with tests, browser checks, and performance work you didn't ask for. Read the READMEs to see how much it did unprompted.

### The three showcases

| Project | What it is | Worth noticing |
|---|---|---|
| [Cube Lab](https://github.com/JacintoDesign/3d-rubiks-cube) | A Rubik's Cube playground in vanilla HTML, CSS, and JavaScript with Three.js. No build step | Drag a cubie to turn its layer, 2–6 layers, scramble and solve, procedural carbon-fiber and brushed-metal textures, rounded geometry. Only the visible shell cubies are created, and exact grid coordinates prevent drift as layers turn |
| [ASTRA — Orbital Observatory](https://github.com/JacintoDesign/astra) | A cinematic orbital habitat above a shader-generated planet. Three.js and Vite | A segmented rotating habitat, a reactor, twelve solar wings, a docking module, and a supply tender. Four animated camera views, eclipse lighting, synthesized ambient audio, and bloom. The 633 meshes are batched into 23 draw groups, with a geometry test to confirm the batching |
| [Terra](https://github.com/JacintoDesign/3d-dioramas) | An interactive atlas of seven miniature dioramas. Three.js and Vite | Pine forest, desert oasis, alpine peaks, tropical coast, volcanic island, sakura garden, and lunar outpost, all generated from geometry. Orbit, auto-rotate, fullscreen, and keyboard navigation between exhibits |

### Build them yourself

Each prompt below is a single one-shot that rebuilds one of the showcases. They aren't the original prompts word for word. Each one names the stack, the core features, and the no-downloaded-assets rule, and leaves the visual polish to the model. Paste one into Astra in an empty folder.

To get the same self-checking the showcases arrived with, add this line to the end of any of them:

```text
Verify it in the browser, including at phone width, and fix any errors before finishing.
```

#### Cube Lab

```text
Build a Rubik's Cube playground in vanilla HTML, CSS and JavaScript using Three.js from a pinned CDN, with no build step (index.html, style.css, app.js).

- Drag a cubie to turn its layer; drag empty space to orbit; scroll or pinch to zoom; right-drag or two fingers to pan.
- Grid size from 2 to 6 layers.
- Buttons: Scramble (animated random sequence), Solve (reverses the recorded move history, including manual moves), Reset (solved cube and camera, keeping appearance settings), plus camera reset and auto-orbit.
- Live appearance controls: surface material (procedural carbon fiber and brushed metal, generated in code), lighting, rounded cubie geometry, adjustable stickers.
- Only create the visible outer shell of cubies, share geometry and materials, and use exact grid coordinates so turns never drift.
- Responsive layout for desktop and phone. No downloaded models or textures.
```

#### ASTRA — Orbital Observatory

```text
Build a cinematic, interactive 3D space observatory with Three.js and Vite: a procedurally modeled orbital space station floating above a shader-generated planet. No downloaded models, textures or audio files; everything is built in code.

- Station: a segmented rotating habitat ring with lit windows and trusses, a reactor, twelve solar wings with generated cell textures, radiator panels, a comms dish, docking modules, and a small supply ship on its own orbit.
- Planet: procedural ocean, continent and cloud shader with atmospheric glow; a seeded starfield; bloom post-processing.
- Four camera views (Overview, Habitat, Solar arrays, Reactor) with animated transitions and an info panel each; keys 1–4.
- Controls: drag to orbit, scroll or pinch to zoom, click parts to select them, pause rotation, eclipse lighting, opt-in synthesized ambient audio, and a cinematic mode on H (Escape exits).
- Batch static meshes into as few draw calls as possible while keeping animated parts independent, and add a test that checks the batching.
- Respect reduced-motion, stop rendering when the tab is hidden, and make it work on phones.
```

#### Terra

```text
Build "Terra", an interactive atlas of seven miniature 3D dioramas, with Three.js and Vite: pine forest, desert oasis, alpine peaks, tropical coast, volcanic island, sakura garden and lunar outpost.

- Each diorama is a small tabletop world on a base, generated entirely from code geometry: no downloaded models or images.
- A collection selector lets you switch worlds; arrow keys move between exhibits.
- Drag to orbit, scroll or pinch to zoom, toggle auto-rotate, reset the camera, and enter fullscreen.
- Give each world its own lighting and colour mood, with a polished museum-exhibit interface.
- Responsive: on narrow screens the collection becomes a horizontally scrolling strip.
```

### What to take from it

- **Code-built 3D is now a realistic one-shot target.** You'll use that in the next two modules: turning the Mars Colony's 2D sprites into models, and as a comparison build for the walkable world.
- **A one-shot is a demo, not a product.** These pieces stand alone. When 3D has to fit an existing contract, design standard, and simulation, the planning documents come back. Module 13 shows what that looks like.

### Where Astra appears again

| Module | Use |
|---|---|
| Antigravity — Build the Mars Colony Simulator | Rebuilding the colony's 2D sprites as 3D models, plus a 3D home screen |
| Capstone — Liftoff: The Walkable World | Building the same world from the course's existing documents and comparing it against the Claude Code build |
| Debrief — Four Tools Compared | Counted in Codex's favour in the overall ranking ("Astra impressive for 3D") |
