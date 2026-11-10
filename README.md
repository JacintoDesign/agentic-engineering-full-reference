# Agentic Engineering Full Course Reference

Student reference for the **Agentic Engineering** course. Keep these guides open while you work so you can grab a prompt, command, or concept without scrubbing back through video.

This is not a tutorial and not a codebase. It is the companion sheet for directing agents to plan, build, verify, and ship real software — rather than vibe-coding your way to output you cannot maintain.

## The discipline

Agentic Engineering is a shift in altitude: you stop directing *what* gets built and start directing *the system that builds it*, from planning through verification and ship.

The director's workflow is the through-line of the whole course:

- A planning system: PRD, content model, site contract, design standard, and standing orders (`CLAUDE.md` and equivalents)
- A three-standards evaluation lens: does it scale, is it secure, and do you know exactly why it works
- A Director's Audit loop: freeze the spec, judge the output against it, then ship or send it back

You will build and deploy real products along the way — typically a production backend, a connected frontend, and a motion-led portfolio.

## How this repo is organized

Each module file is a lesson-by-lesson reference: core concepts, copy-paste prompts, CLI commands, and the checklists used in class.

<table>
  <thead>
    <tr>
      <th width="140" align="left">Guide</th>
      <th align="left">Focus</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td width="140" nowrap><a href="Module_01_reference.md">Module&nbsp;1</a></td>
      <td>Claude — planning system, project documents, browser build, and the Director's Audit</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_02_reference.md">Module&nbsp;2</a></td>
      <td>Claude Code in the terminal — setup, memory, permissions, verification, routines, goals, plugins, and Vercel tooling</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_03_reference.md">Module&nbsp;3</a></td>
      <td>Claude Code desktop app — visual diffs, parallel sessions, Remote Control, and atomic shipping</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_04_reference.md">Module&nbsp;4</a></td>
      <td>VibeMail backend — architecture, parallel server/schema work, deployment, and production verification</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_05_reference.md">Module&nbsp;5</a></td>
      <td>Claude Design and Claude Code — VibeMail UI specification, handoff, migration, integration, and release</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_06_reference.md">Module&nbsp;6</a></td>
      <td>Cursor: Composer model fundamentals — rules, auto-run safety, the Agents Window, extensions, and the inner loop</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_07_reference.md">Module&nbsp;7</a></td>
      <td>Cursor: Build Waypoint — a PostGIS map-guides app built from contract and test plan to verified ship</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_08_reference.md">Module&nbsp;8</a></td>
      <td>Cursor: ZTM Music Player V2 — brownfield planning, Jamendo, a WebGL visualizer, and the cloud review loop</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_09_reference.md">Module&nbsp;9</a></td>
      <td>Cinematic portfolio — motion direction, shaders, GSAP choreography, audit, contact route, and deployment</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_10_reference.md">Module&nbsp;10</a></td>
      <td>Semantic Memory: add AI features to VibeMail — a pgvector memory layer, free-tier reasoning, scoped retrieval, and honest degradation</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_11_reference.md">Module&nbsp;11</a></td>
      <td>Codex: Build Cosmos (NASA dashboard) — routed agents, live-source contracts, designed ambiguity, and browser verification</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_12_reference.md">Module&nbsp;12</a></td>
      <td>Codex: bonus projects — an interview skill, a 1996 desktop via Ultra, a three-volume magazine, and ChatGPT Astra</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_13_reference.md">Module&nbsp;13</a></td>
      <td>Google Antigravity: Mars Colony Simulator — browser proof, shared-database safety, deterministic time, and long-run playtesting</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_14_reference.md">Module&nbsp;14</a></td>
      <td>Creating a 3D portfolio world — a walkable 3D portfolio directed from a world brief, audited on real devices, and shipped</td>
    </tr>
    <tr>
      <td nowrap><a href="Module_15_reference.md">Module&nbsp;15</a></td>
      <td>The Debrief — four tools compared, the leaderboards, the bill, and the drill for the next tool</td>
    </tr>
  </tbody>
</table>

### Companion pages

| Page | Use it for |
|---|---|
| [Before You Start](SETUP.md) | Accounts, installs, and keys for each module, plus what to keep for later modules |
| [Planning Documents](PLANNING_DOCUMENTS.md) | Every document in the course, what it's for, and a blank template for each |
| [Tool Equivalents](TOOL_EQUIVALENTS.md) | The same concepts across Claude Code, Cursor, Codex, and Antigravity |
| [Terminology](TERMINOLOGY.md) | Course vocabulary, A to Z |
| [Project Links](PROJECT_LINKS.md) | The instructor's finished builds, with repos and live deployments for every module |

## Course map

The course is toolchain-agnostic in principle and Claude-native at the start.

**Modules 1–5** are the Claude foundation: the planning system, then Claude Code (terminal and desktop) and Claude Design, carrying VibeMail from backend to deployed frontend.

**Modules 6–8** move the same discipline into Cursor and Composer 2.5 — fundamentals, a greenfield map app, and a brownfield refresh.

**Module 9** is the cinematic motion portfolio, and **Module 10** closes VibeMail out with its memory layer, built entirely on free infrastructure.

**Modules 11–13** cover Codex (Cosmos plus bonus projects) and Antigravity (the Mars Colony Simulator).

**Module 14** is the walkable-world capstone, and **Module 15** is the debrief comparing all four tools.

## What this course does not teach

- Programming fundamentals, HTML/CSS/JavaScript, or basic web architecture from scratch — those are assumed
- AI video generation — the motion work produces everything in code. Image generation appears only as a design and asset tool in the Codex modules. A shader costs nothing, ships as kilobytes, and responds to the visitor in a way a generated clip never can

## How to use it

1. Open the module you are currently in.
2. Use the table of contents to jump to the lesson you are on.
3. Copy prompts and commands as written, then adapt them to *your* product, brief, and standing orders — not the instructor's worked example.
4. Treat planning documents as the source of truth. Prompts in these guides are often the method delta; without your PRD, contract, design standard, or motion brief behind them, they will not produce your work.

## Prerequisites

You should already be comfortable with:

- HTML, CSS, and JavaScript
- Basic web architecture (frontend vs backend, APIs, git, deploying a site)

The Golden Path used across course projects is React, Next.js, and Supabase unless a lesson explicitly says otherwise.
