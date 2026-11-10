# Module 13 Reference Guide
### Google Antigravity — Mars Colony Simulator: Browser Proof, Shared Data, and Deterministic Time

Use this guide alongside the Module 13 videos.

_Antigravity settings, permission labels, and command locations move between releases. Use the values here to understand the intended posture, then confirm each one against the interface in front of you before granting access._

---

## Table of Contents

1. [Introducing the Companion App for Gemini's Latest Models](#introducing-the-companion-app-for-geminis-latest-models)
2. [Installing Google Antigravity - Testing IDE, Extension, Agents Mode, Terminal](#installing-google-antigravity---testing-ide-extension-agents-mode-terminal)
3. [Google Antigravity - Permissions, Settings & Browser Agent](#google-antigravity---permissions-settings--browser-agent)
4. [Planning a New Project: Mars Colony](#planning-a-new-project-mars-colony)
5. [Making a Scaffolding for the Web App, Artifacts and Browser Subagent](#making-a-scaffolding-for-the-web-app-artifacts-and-browser-subagent)
6. [Playtest Scenarios: Acceptance Criteria for a System that Moves](#playtest-scenarios-acceptance-criteria-for-a-system-that-moves)
7. [Building Out a 20x20 Isometric Grid](#building-out-a-20x20-isometric-grid)
8. [Revising Planning Documents to Adjust Game Parameters](#revising-planning-documents-to-adjust-game-parameters)
9. [Modifying the Database to Work for Two Separate Projects (Waypoint and Mars Colony)](#modifying-the-database-to-work-for-two-separate-projects-waypoint-and-mars-colony)
10. [Creating the Database Tables for the Mars Colony Project](#creating-the-database-tables-for-the-mars-colony-project)
11. [The Lazy Tick: Computing Time Instead of Spending it](#the-lazy-tick-computing-time-instead-of-spending-it)
12. [Colonists, Pathfinding Logic Refinement, Adding Help Modal](#colonists-pathfinding-logic-refinement-adding-help-modal)
13. [Running Our Playtest Workflow Scenario](#running-our-playtest-workflow-scenario)
14. [Building a Skill to Start a New Game and Survive as Long as it Can](#building-a-skill-to-start-a-new-game-and-survive-as-long-as-it-can)
15. [Fixing the Flaws in the Game Logic](#fixing-the-flaws-in-the-game-logic)
16. [Re-Running the Playtests After the Redesign](#re-running-the-playtests-after-the-redesign)
17. [Deploy with a Final Round Testing in Production](#deploy-with-a-final-round-testing-in-production)
18. [Bonus Lesson: Using ChatGPT Astra to Make 3D Models](#bonus-lesson-using-chatgpt-astra-to-make-3d-models)

---

## Core Concepts

**The browser is opt-in.** Use `/browser` explicitly whenever a claim depends on what the running app does. An agent won't check its own work in a browser unless you tell it to.

**Artifacts are review surfaces.** Plans, task lists, diffs, screenshots, and recordings are evidence you comment on before the agent continues.

**A recording proves a sequence.** A screenshot proves a moment; for a system that changes on its own clock, video is the only format that shows what you're trying to prove.

**Permissions define blast radius, not data ownership.** Settings decide whether a command may run outside the sandbox. Which tables it may touch lives in `DATABASE.md`.

**Verification follows time.** Playtest scenarios assert Given / When / Then trajectories, and catch-up compresses long runs into seconds.

**Determinism is a storage property.** Applying N ticks in one batch must equal applying N single ticks — which makes offline catch-up, browser prediction, and testing agree.

**The database is authoritative; the browser predicts.** The browser may run the same pure tick for display but never saves its prediction.

**Unattended work reports; it doesn't repair.** A verifier that fixes as it tests will always report success.

---

## Introducing the Companion App for Gemini's Latest Models

### What Antigravity 2.0 is

- A standalone, agent-first desktop app with no code editor — orchestrate there, open an editor to look closely.
- Conversations belong to a **project**, not a repository. A project carries settings, resources, and permissions and can span several folders.
- Permissions inherit from global defaults and are tightened per project.
- The Antigravity IDE still ships, but the Agent Manager is being removed from it.

### Artifacts and the browser subagent

| Artifact | Review use |
|---|---|
| Plan | Approve the approach before files change |
| Task list | Check scope and order |
| Diffs | Review changes |
| Walkthrough | End-of-run summary |
| Screenshot / recording | Visual and behavioural proof |

The browser subagent launches your real, installed Chrome with a debugging port and a dedicated profile, driven over the DevTools protocol.

### The verification ladder

| Rung | What verification stands in for |
|---|---|
| Waypoint (Cursor) | Domain command |
| Cosmos (Codex) | Trust in the signal |
| Mars Colony (Antigravity) | A still frame — the system ticks and degrades on its own |

### Verification layers in this module

| Layer | Evidence | Best for |
|---|---|---|
| Code and document review | Files, diffs, exact rules | Static constraints |
| Browser session | Interaction plus recording | Placement, sign-in, rendering |
| Playtest scenarios | Given / When / Then over ticks | Simulation and negative cases |
| Direct database read | Authoritative stored values | Browser/server disagreements |
| Cross-account check | Owned vs forbidden rows | RLS and ownership |
| Long run | Compressed survival over many ticks | Emergent failures and balance |
| Director's Audit | Pass/fail per governing rule | Completeness and undocumented behaviour |

---

## Installing Google Antigravity - Testing IDE, Extension, Agents Mode, Terminal

### Install

1. Install **Antigravity 2.0** and sign in.
2. Check your plan — the free tier's daily agent-request allowance is small, and image generation uses a separate quota.
3. Install the **editor extension** for the editor you already use, and sign in.
4. Skip the Antigravity IDE for this module.
5. Create a **project** and add a folder that already exists and runs — a dev server and an uncommitted change make the next lessons demonstrate something. An empty folder doesn't.

### Confirm the wiring

```text
Tell me what's in this project, whether it's a git repository, and
whether there's anything uncommitted right now. Don't change
anything.
```

### The surfaces

| Surface | Role |
|---|---|
| Antigravity 2.0 | Mission control — agents, artifacts, recordings, scheduled tasks; embedded terminal and git review |
| Editor extension | The same agents in your editor; shares the app's conversation thread |
| Antigravity IDE | A VS Code fork with agents; keeps its own history |
| `agy` CLI | Keyboard-driven and scriptable; replaced the Gemini CLI; its own history |
| SDK | Programmatic access in Python, TypeScript, and Go |
| Managed agents | Google-cloud agents — out of scope |

### What travels between surfaces

Run in the desktop app, then open the same project in your editor and run it again through `agy`:

```text
Describe this project in two sentences and list the files you can
see. Don't change anything.
```

| Travels everywhere | Desktop app ↔ extension only | Needs a display surface |
|---|---|---|
| Account, project settings, permissions, rules, workflows, skills, hooks | Conversation history | Browser recordings, artifact review |

Work in the desktop app with your own editor beside it; use `agy` for scripted work.

---

## Google Antigravity - Permissions, Settings & Browser Agent

In Antigravity, **Rules** are context files the agent reads. Permissions live in settings.

### Set globally, in General

Open **Settings → General → Global Permissions**, not a project's settings. Untouched project settings then read **"Inherit General"**. The colony gets exactly one project-level override later.

Commands run inside an isolated sandbox by default. The real decision is what may run *outside* it.

### Agent Settings

| Setting | Set to |
|---|---|
| Security Preset | Custom |
| Outside of folders file access policy | Always Ask |
| Terminal Command Auto Execution | Proceed In Sandbox |
| Enable Sandbox Mode (Preview) | On |

Proceed In Sandbox is only safe because sandbox mode is on. Check the toggle before the dropdown.

### Agent Behavior

| Setting | Set to |
|---|---|
| Artifact Review Policy | Always Ask |

### File and network permissions

- **File Access Rules** — leave at defaults.
- **Network Access Rules** — URLs any tool may fetch; separate from the browser subagent.

### Terminal Commands

Each entry is one command with its own Allow or Deny, added one row at a time.

Allow:

```text
ls, pwd, cat, grep, rg, find
git status, git diff, git log, git branch
npm install, npm run dev, npm run build, npm test
```

Deny:

```text
rm -rf
sudo
git push --force
curl … | sh          (anything piping a download into a shell)
wget … | bash
```

Test the Allow half:

```text
Run git status and tell me what's uncommitted. Don't change or
commit anything.
```

It should run without asking and name the uncommitted change.

### Commands Outside Sandbox

Leave it empty. Anything here runs against your real machine. Later, when `supabase` gets an Allow entry on the colony project, add explicit Deny entries for `supabase db reset` and `supabase db pull` if the panel supports them — the two commands that operate on the whole shared schema.

### MCP Tools

Leave empty. This module uses the **Supabase CLI** instead of an MCP server.

### The browser

The browser subagent drives your real Chrome — a hostile page loads in a real browser with a real profile. Install the extension now; Chrome must be your default browser.

| Setting | Set to |
|---|---|
| Browser Javascript Execution Policy | Request Review |
| Browser Actuation Rules | Edit → allow only `http://localhost:3000` |

Refuse later "always allow" prompts.

```text
/browser Go to http://localhost:3000, tell me what you see, and
click something. Record it.
```

### CLI equivalent

`agy` reads its own `settings.json`:

```json
{
  "toolPermission": "request-review",
  "enableTerminalSandbox": true,
  "notifications": true
}
```

None of these settings can say *which tables* are safe to touch — that is the next lesson's document.

---

## Planning a New Project: Mars Colony

### Create the repository

```text
Create a new GitHub repository called mars-colony under my account,
set this folder up as a git repository connected to it, add a
.gitignore for a TypeScript and Node project, and make an empty
first commit. Tell me the repository URL when you're done.
```

`gh repo create` isn't on the allowlist, so this should stop for review — approve it. Confirm the new project's settings read "Inherit General."

### Port the spec-interview skill

```text
Clone https://github.com/JacintoDesign/spec-interview-skill into a
temporary folder. Find where Antigravity expects global skills to
live, and copy SKILL.md and the assets folder there so the skill is
available to every project, not just this one. Then delete the
temporary clone. Tell me the path you copied it to.
```

Carry `SKILL.md` and `assets/` only — not the Codex-specific `openai.yaml`.

```text
Use the spec-interview skill. The project is a browser-based
isometric colony simulator on Mars.
```

The interview can't ask about exact rates or other tenants in the database — those you write yourself.

### Rules in Antigravity

| Location | Scope |
|---|---|
| Root `AGENTS.md` | Read directly |
| `.agents/rules/` | Workspace rules |
| `~/.gemini/GEMINI.md` | Global rules |

- Each rule file is capped at **12,000 characters**.
- Activation modes: Always On, Manual (@-mention), or Model decides. Architectural law is Always On.

### CONTRACT.md

```markdown
# Contract — Mars Colony

## World
- 20×20 grid, coordinates 0–19 on both axes.
- One building per tile. Buildings block movement.

## Ownership
- Every colony belongs to exactly one account.
- One colony per account. A colony is created on first sign-in and
  never reset thereafter.

## Buildings
- habitat: houses 2 colonists, draws 2 power/tick
- solar: produces 5 power/tick, draws 0
- scrubber: produces 4 oxygen/tick, draws 3 power/tick

## Resources
- oxygen and power are pools, 0–100, clamped at both ends.
- Each colonist consumes 3 oxygen/tick.
- Tick interval: 1 second of real time.

## Colonists
- Colonists move between buildings, one tile per tick, stopping on a
  free tile beside their destination.
- Movement is atmospheric, not mechanical. Production and consumption
  depend only on how many buildings and colonists exist — never on
  where a colonist is standing.
- Colonists do not block each other. Only buildings block movement.

## Health
- Colonists have health 0–100, starting at 100.
- If oxygen is 0 OR power is 0 at the end of a tick, every colonist
  loses 5 health.
- Otherwise colonists recover 1 health per tick, up to 100.

## State shape
ColonyState = { ownerId, buildings, colonists, oxygen, power, tick,
                lastTickAt }

## Time and catch-up
- The colony advances in real time whether or not anyone is watching.
- Time passing while nobody is connected is applied on next load, as
  a batch of ticks computed from lastTickAt.
- Catch-up is capped at 28,800 ticks (8 hours). Time beyond the cap
  is discarded.
- Applying N ticks in one batch must produce exactly the same state
  as applying N ticks one at a time.

## Persistence
- The database is the source of truth. A colony is restored from its
  last saved state on load, mid-degradation if that's where it was.

## Out of scope
- Combat, trade, multiple colonies per account, win or lose screens.
```

### AGENTS.md

```markdown
# AGENTS.md — Mars Colony

## Identity
- A browser-based isometric colony simulator. One colony per account.
- Not multiplayer, and not a strategy game — no combat, no trade.

## Stack
- TypeScript, Canvas 2D. No game engine, no WebGL library.
- Supabase for storage, accounts, and change subscriptions.
- Deploys to Vercel.

## Conventions
- The tick is one pure function: (state, n) -> state. No I/O inside it.
- Deterministic, fixed timestep. Any choice comes from a seeded value in state.
- The tick stays cheap enough to run tens of thousands of times at once.
- The server owns state; every write is server-side, owner from the session.
- The browser may run the tick forward for display only, and never saves it.
- Grid↔screen maths lives in one module, pure, used in both directions.
- Rendering reads state and never changes it.
- Game rendering and interface panels are separate modules.

## Never do
- Never run a scheduled job that ticks colonies nobody is watching.
- Never save a value the browser calculated.
- Never take an owner id from the browser instead of the session.
- Never invent a building type or resource that isn't in CONTRACT.md.
```

### DESIGN.md

```markdown
---
palette:
  terrain_base: "#6B3A26"
  terrain_light: "#8C5233"
  terrain_dark: "#4A2618"
  structure: "#D9DDE0"
  structure_accent: "#7FD4E0"
  warning: "#E0A030"
  critical: "#D94F3D"
type:
  family: system sans-serif
  numerals: tabular
thresholds:
  warning_below: 0.25
  critical_below: 0.10
---

## Direction
Isometric, drawn in code on a 2D canvas. No 3D, no game engine, no
image assets in the game surface.

## Palette
Terrain uses the three rust and ochre tones and nothing else, with
subtle per-tile variation so the ground isn't flat. Human-made
structures use the cold tones so they read against the warm ground
without needing an outline.

## Silhouette
Each building type is identifiable by shape alone: habitats rounded
and low, solar arrays flat and angular, scrubbers tall and narrow.
No labels needed.

## Depth
Everything sorts back to front, so buildings overlap correctly.

## State on screen
Solar arrays glow while power is above zero and go dark when the grid
fails. Scrubbers vent a faint plume while producing oxygen. The scene
tints with the oxygen level — clear when healthy, thin dusty haze as
it drops. Colonists stand upright at full health and slump as it
falls.

## Panels
Interface panels sit over the canvas, never inside it. Plain type,
tabular numerals so readings don't jitter. No shadows, no gradients,
no animated transition on a value change beyond the number itself
changing. The readout panel built next lesson follows this, and
once its id is set it must not change.

## Never do
Never animate anything on its own timer — if something moves, it
moved in the simulation. Never load an image asset into the game
surface; draw in code.
```

### DATABASE.md — the fourth document, written by hand

```markdown
# Database law

This database is shared by two unrelated applications.

## Namespacing
- This project owns tables prefixed `marscolony_` and nothing else.
- Tables prefixed `waypoint_` belong to a different application.
  Never read, write, alter or reference them.
- The auth tables and the shared profile table are common to both.
  Read them. Never alter their structure.

## Access
- Every `marscolony_` table has an owner column referencing the
  authenticated user.
- Row-level security is enabled on every `marscolony_` table, scoped
  to the current user for select, insert, update and delete.
- Application code uses the session's credentials so those policies
  apply. The service role key is not used by application code.

## Migrations
- Migration files are prefixed `marscolony_` and only ever contain
  statements affecting `marscolony_` tables.
- Never run a command operating on the whole remote schema — reset,
  pull, or equivalent. Those are project-wide.
- Never drop a table. Propose the change and stop.
```

Set `DATABASE.md`'s rule to Always On — and treat it as a request, not a wall.

### The pointer rule

`AGENTS.md` is read at the root; the other three aren't. Add `.agents/rules/project-docs.md`, Always On:

```markdown
Read these documents at the start of every task and treat them as
binding. They live at the repository root.

- CONTRACT.md  — what the colony is, and the numbers it runs on
- DESIGN.md    — how it must look
- DATABASE.md  — what this project may and may not touch

If any instruction conflicts with these, say so rather than
resolving it yourself.
```

### Check the documents are being read

```text
Three questions about the project documents. Don't write any code.

1. How much oxygen does a colony with 2 scrubbers and 3 colonists
   gain or lose each tick? Show the arithmetic.
2. Which database tables is this project allowed to write to, and
   what should you do if you find one that isn't on that list?
3. What colour is a resource reading below 10%?
```

A wrong answer means a vague document — fix the document, not the question.

---

## Making a Scaffolding for the Web App, Artifacts and Browser Subagent

One small task that exercises every artifact type.

```text
Set up an empty web app in this folder, following the project
documents. Then add one thing to it: a readout panel in the corner
of the page for checking what the game is doing internally.

- Plain text, one value per line, no styling beyond making it
  readable. It is not part of the game's interface.
- For now it lists tick, oxygen and power, with fixed placeholder
  values — 0, 80 and 65.
- Put a stable id on the panel's container so it can always be
  found, and don't change that id later.
- Nothing on it animates or fades. A value either changes or it
  doesn't.

Then start the app. Once it's running, /browser show me the panel
working. Record it.
```

With Artifact Review on Always Ask, review the plan and task list, comment to redirect, then approve.

- The readout panel's stable id becomes the verifier's only window into a canvas — explained when the grid arrives.
- A screenshot is correct proof for a static panel; recordings earn their place once state changes.

```text
Add two more lines to the panel: the signed-in account, and the
account that owns the colony. Both can say "none" for now. Same
plain-text format, and don't change the panel's id.
```

---

## Playtest Scenarios: Acceptance Criteria for a System that Moves

Trajectory assertions, not snapshots.

### Project layout

```text
mars-colony/
├── .agents/
│   ├── rules/
│   │   └── project-docs.md
│   ├── workflows/
│   │   └── playtest.md
│   └── skills/
│       └── playtest/
│           └── SKILL.md
├── CONTRACT.md
├── AGENTS.md
├── DESIGN.md
├── DATABASE.md
└── PLAYTEST_PLAN.md
```

### PLAYTEST_PLAN.md — first six scenarios

```markdown
## Scenario 1: Oxygen starvation degrades health
Given: 1 habitat, 1 colonist, no scrubbers, oxygen 20, power 100,
       colonist health 100.
When:  the simulation runs 60 ticks with no player action.
Then:  - oxygen reaches 0 before tick 60
       - after oxygen hits 0, colonist health decreases every tick
       - at tick 60, colonist health is below 100

## Scenario 2: A balanced colony holds steady
Given: 1 habitat, 1 colonist, 1 scrubber, 1 solar, oxygen 50, power 50.
When:  the simulation runs 60 ticks with no player action.
Then:  - neither oxygen nor power reaches 0
       - colonist health is 100 at tick 60 and never dropped

## Scenario 3: Recovery when a scrubber is added
Given: the end state of Scenario 1.
When:  the player places a scrubber and 30 ticks pass.
Then:  - oxygen rises above 0
       - colonist health increases every tick after oxygen recovers

## Scenario 4: Batch catch-up matches live ticking
Given: any colony state and a tick count N.
When:  N ticks are applied as one batch, and separately one at a
       time from the same starting state.
Then:  - both end states are identical in every field, including
         every colonist's position
       - the tick counter reads the same in both

## Scenario 5: Offline time is applied on return, and capped
Given: a draining colony, saved, with its last tick 90 minutes ago.
When:  the colony is loaded.
Then:  - roughly 5,400 ticks have been applied
       - both pools are still within 0–100
       - a colony last saved 30 days ago applies exactly 28,800
         ticks and no more

## Scenario 6: A colony is visible only to its owner
Given: two accounts, each with a colony in a different state.
When:  account A is signed in.
Then:  - account A sees only its own colony
       - a direct request for account B's colony returns nothing
       - signing out leaves no colony data readable at all
```

Include a steady-state scenario (2), not only failure and recovery.

### .agents/workflows/playtest.md

```markdown
Run every scenario in PLAYTEST_PLAN.md against the running colony.

/browser for all of this, and record the session.

Sign in as the test account before setting up anything.

For each scenario:
- Set up the starting state described in the scenario.
- Advance the colony by the number of ticks specified, using the
  catch-up path rather than waiting in real time.
- Take a reading of oxygen, power, and colonist health at every tick.
- Check each "Then" claim against what happened.
- Wait for the app to finish loading before recording any reading —
  a loading state is not a result.
- Check that the account shown on the page is the one you signed in
  as before recording any reading.

Report a table of expected versus observed for every tick where a
claim failed. Do not fix anything.
```

### The four load-bearing lines

| Line | Why |
|---|---|
| Literal `/browser` | Prose asking for a browser gets a suggestion, not a session |
| Wait for loading | Guards against recording a spinner as a result |
| Account check | Stops a scenario passing against the wrong colony |
| Do not fix anything | A verifier that repairs always reports success |

### When it doesn't register

```text
/playtest isn't available as a command. Fix it so I can run it.
```

On the recording release, the agent converted the workflow into `.agents/skills/playtest/SKILL.md`, reachable by the same `/playtest`. Confirm it autocompletes and commit whatever exists.

---

## Building Out a 20x20 Isometric Grid

### Document additions first

`DESIGN.md`:

```markdown
## Building Silhouette
Each building type is identifiable by shape alone: habitats rounded
and low, solar arrays flat and angular, scrubbers tall and narrow.
No labels needed to tell them apart.
```

`AGENTS.md` Never do:

```markdown
- Never render buildings without sorting back-to-front by grid
  position; overlap must always resolve correctly.
```

### Build prompt

```text
Build the game board, following the project documents.

Rendering:
- A 20×20 isometric grid on an HTML canvas, centred in the page.
- Each tile is a diamond. The tile under the mouse highlights.
- Put the grid-to-screen and screen-to-grid maths in one module, as
  pure functions with no canvas or DOM code in them.

Placing buildings:
- A toolbar to pick habitat, solar or scrubber.
- Clicking an empty tile places the selected building and draws it.
- Clicking a tile that already has a building does nothing.
- Clicking outside the grid does nothing.

How it should look — follow DESIGN.md. Draw each building as simple
extruded geometry on its tile, clearly different in shape per type.
Sort back to front so buildings overlap correctly. Draw everything
in code. Don't generate any images for this, and don't load any
image files — no sprites, no textures, no icons.

The readout panel that's already there:
- Keep its format and its id exactly as they are.
- Add a line listing every building placed and its tile.
- Add a line for the time of the last applied tick. It can say
  "never" until there's a simulation.

Then /browser check it: place one of each building type,
click an occupied tile to confirm nothing happens, and click just
outside the grid edge to confirm nothing happens. Record it.
```

### Concepts

| Concept | Meaning |
|---|---|
| Forward projection | Grid coordinates → screen position |
| Inverse projection | Mouse position → grid tile |
| Silhouette | Building type readable by shape alone |
| Render depth | Back-to-front sort so overlaps resolve |
| Readout panel | Canvas pixels can't be read; the panel is the verifier's only window into state |

Silhouette and render depth surfaced as gaps nobody had written down — the documents are extended to name them.

---

## Revising Planning Documents to Adjust Game Parameters

Documents extended before any code.

### CONTRACT.md additions

```markdown
## Starting State
- A new colony starts with oxygen 50, power 50, no buildings, no
  colonists.

## Colonist Arrivals
- A ship lands every 300 ticks, adding one colonist — capped by
  total habitat capacity (2 colonists per habitat).
- No landing occurs if capacity is already full.
- New colonists appear at the fixed landing zone, tile (0, 0).

## Ore
- ore is a stockpile, not a pool: 0 or above, no upper clamp.
- ore does not participate in the health rule. Only oxygen and
  power can trigger colonist health loss.
- Add ore to the state shape: ColonyState now includes an ore
  field alongside oxygen and power.

## Ore Reserve
- A fixed underground reserve of 500 ore, set once at colony
  creation, never replenished.
- Each tick the extractor produces, the reserve drops by the same
  amount produced.
- Once the reserve reaches 0, the extractor produces 0 ore/tick
  from then on. It still draws power — an idle extractor is not a
  free one.

## Buildings (extended)
- extractor: produces 3 ore/tick, draws 4 power/tick

## Placement Costs
- habitat: 20 power
- solar: 15 power
- scrubber: 15 power, 5 ore
- extractor: 25 power
- A placement is rejected if the account cannot afford its cost.
  Cost is deducted server-side the instant placement succeeds.

## Colonist Death
- A colonist whose health reaches 0 dies and is removed from the
  colony in the same tick.

## Game Over
- If the last colonist dies, colony status becomes "game_over."
  No further ticks are applied once set — every tick requested
  after that, including catch-up, returns the state unchanged.
- 1 sol = 1000 ticks. Sols survived is the tick count at the
  moment of game over, divided by 1000, rounded down.

## Ownership (amended)
- A colony in game_over status may be restarted by the same
  account, from the game-over screen only. This is the one
  exception to "never reset" — a player's own deliberate action,
  not something the server or an agent triggers on its own.
- Restarting returns oxygen, power, ore and the ore reserve to
  starting values, and clears buildings and colonists. Status
  returns to active.
```

### DESIGN.md additions

```markdown
## Building Silhouette (extended)
- extractor: wide and low, with an angled scoop silhouette —
  distinct from the other three at a glance.

## Game Over Screen
Plain text, same palette and type as the rest of the interface.
No colour deviation, no animation — a sol count and a single
button, nothing more.
```

### AGENTS.md Never-do additions

```markdown
- Never deduct or grant a placement cost on the client. The server
  confirms affordability and applies the deduction.
- Never restart a colony from anywhere but an explicit player
  action on the game-over screen. No automatic retry, no agent
  deciding a dead colony should start over.
```

The cost rule is written before a server exists to enforce it.

### Toolbar prompt

```text
Extend the building toolbar and placement controller, following the
updated project documents.

- Add a fourth button: extractor.
- Before placing any building, check power (and ore, where the
  building requires it) against its cost in CONTRACT.md. If
  insufficient, reject the placement and show the reason in the
  status bar — reuse the existing invalid-placement styling.
- On successful placement, deduct the cost.
- Add an ore line to the readout panel, same format as the rest —
  the /browser check below needs something to read.

This still lives entirely in local state for now, same as the rest
of the colony — there's no server to enforce it yet.

Then /browser check it: try to place a habitat with insufficient
power and confirm it's rejected with the right reason shown; place
one successfully and confirm the panel shows the cost deducted.
Record it.
```

The retrofit is client-local, and the prompt says so honestly.

### New scenarios

```markdown
## Scenario 7: Colonist arrivals respect habitat capacity
Given: 1 habitat (capacity 2), 0 colonists, last ship landed 0
       ticks ago.
When:  the colony advances 900 ticks using the catch-up path.
Then:  - exactly 2 colonists exist, not 3
       - both appeared at the landing zone tile
       - no landing occurred once capacity was reached

## Scenario 8: A colonist dies at zero health, and the colony ends
Given: 1 habitat, 1 colonist, no scrubbers, oxygen 0, power 0,
       colonist health 5.
When:  the simulation runs 2 ticks.
Then:  - the colonist's health reaches 0 and it is removed
       - colony status becomes game_over
       - a further 10 ticks change nothing in the state

## Scenario 9: Ore production stops when the reserve is exhausted
Given: 1 extractor, ore reserve 6, ore stockpile 0.
When:  the simulation runs 5 ticks.
Then:  - the reserve reaches 0 by tick 2 (3 ore/tick)
       - ore stockpile stops increasing once the reserve hits 0
       - power draw continues every tick regardless

## Scenario 10: Restarting after game over creates a fresh colony
Given: a colony in game_over status, with buildings, a depleted
       ore reserve, and drained oxygen and power from a previous
       run.
When:  the account restarts from the game-over screen.
Then:  - oxygen, power, ore and the ore reserve return to starting
         values
       - no buildings or colonists remain
       - colony status returns to active
```

Scenario 11, added with the account table, tests that best sols survived doesn't overwrite with a worse run and does update with a better one.

---

## Modifying the Database to Work for Two Separate Projects (Waypoint and Mars Colony)

The colony shares a Supabase project with Waypoint. Waypoint's tables are renamed to `waypoint_` in seven steps.

### 1. Grant the CLI on this project only

Authenticate the Supabase CLI outside the conversation and confirm it's linked to the right project. In the colony project's **Commands Outside Sandbox**, add `supabase` as Allow — and watch "Inherit General" change to an explicit value. This is the module's one project-level override.

### 2. Back up

```text
Using the Supabase CLI, dump the current database to a local file
with today's date in the name. Tell me the file path and how big
it is. Change nothing in the database.
```

### 3. Record the baseline

```text
/browser Start the Waypoint app and open it. Sign in, open a guide,
confirm the places and photos load, and confirm the map shows pins.
Record the session and tell me what you saw. Change nothing.
```

### 4. Plan the rename — a one-time exception

`DATABASE.md` is Always On, so the agent may push back. That's proof the rule is being read.

```text
One-time exception to DATABASE.md, authorised: you may modify
Waypoint's tables for this task only, in order to bring them into
the naming convention that document describes.

- List every table, function, and trigger in the public schema
  first.
- For each table, check whether it's genuinely shared — referenced
  by this colony project's own schema, not just similarly named.
  DATABASE.md says the auth tables and a shared profile table are
  common to both applications; don't assume a table is that one
  just because it's called "users" or anything else that sounds
  right. Check for an actual reference.
- Rename every table that isn't already prefixed marscolony_ or
  waypoint_ and isn't genuinely shared. Leave shared tables alone.
- For every function whose body references a table being renamed,
  update that reference. Triggers stay attached to their table
  through a rename automatically — don't assume that's true,
  confirm it once the migration applies.
- Don't create anything new and don't change any access rule.

Write it as one migration. Then search the entire Waypoint folder
for every reference to each renamed table's name as a string and
update them.

List the migration, every function you're updating and why, every
file you intend to change, and which table (if any) you decided was
shared and why. Stop there — don't apply anything yet.
```

The prompt discovers tables, functions, and sharedness rather than naming them.

### 5. Review and apply

Read the migration, the function updates, and the file list. Apply only once it matches.

### 6. Verify the schema

```text
Using the Supabase CLI, list every table in the public schema. Tell
me if any table doesn't start with marscolony_ or waypoint_, and
for each one that doesn't, whether it's a table this project's own
schema actually references. Then check every function's body for
any reference to a table's old name, and confirm every trigger is
still attached to its table.
```

### 7. Re-run the baseline

```text
/browser Start the Waypoint app again and repeat exactly the checks
you did before the rename: sign in, open a guide, confirm places
and photos load, confirm the map shows pins. Then create a brand
new guide and confirm it gets a slug automatically. Record it,
compare against the earlier recording, and tell me about any
difference.
```

Then hand the borrowed access back — the exception was for this task only.

---

## Creating the Database Tables for the Mars Colony Project

### Tables

```text
Set up the database for the colony, following DATABASE.md and
CONTRACT.md's State Shape section.

Create four tables, all with names starting marscolony_:
- one for user accounts: one row per account, created alongside
  auth.users, holding bestSolsSurvived starting at 0
- one for the colony itself: every field in the state shape except
  buildings and colonists
- one for buildings: type, grid position, and the owning colony
- one for colonists: grid position, health, destination, route, and
  the owning colony

Every table except the user-accounts one has an owner column
pointing at the account that owns the row.

Access rules:
- Turn on row-level security for all four.
- On each, a row can only be read or written by the account that
  owns it. Don't add any rule broader than that.

Write it as a migration file whose name starts marscolony_. Don't
create, change or refer to any table outside that naming. Show me
the migration before applying it.
```

The prompt points at `CONTRACT.md`'s state shape instead of listing fields. Each application gets its own users table.

### Sign-up, sign-in, and guest play

```text
Build sign-up and sign-in for the colony, following the project
documents.

- A sign-up screen and a sign-in screen, email and password, styled
  per DESIGN.md.
- Also offer "play as guest" using anonymous sign-in. A guest gets a
  real session and a real colony, and can turn that into a permanent
  account later by adding an email and password without losing
  anything.
- Every game screen needs a session. Anyone without one goes to the
  sign-in screen.
- The first time an account signs in, create one colony for it: the
  starting resource values from the contract, no buildings. If that
  account already has a colony, use the one it has. Never create a
  second, never reset an existing one.
- Work out who owns a row from the session on the server. Nothing
  about ownership comes from the browser.
- A sign-out control returning to the sign-in screen.

Then /browser check it: sign up as a new account, confirm
a fresh colony appears, place a building, sign out, sign back in,
and confirm the building is still there. Then do it all again as a
guest. Record both.
```

### Isolation from the other account's side

```text
Make a second test account with its own colony, in a different
state from the first.

Then, signed in as the first account:
- Confirm the page shows only the first account's colony.
- Using the Supabase CLI, try to read the second account's
  colony rows while signed in as the first. Tell me exactly what
  comes back.
- Sign out and try the same read with no session at all.

Report what each attempt returned. Change nothing.
```

---

## The Lazy Tick: Computing Time Instead of Spending it

Compute elapsed time instead of spending it. Nothing runs while nobody is watching.

### Invariants

- One pure tick function: `(state, n ticks) -> state`.
- The tick reads no clock and does no database or browser I/O.
- The database owns truth; the client may predict state but never own it.
- Catch-up is capped at eight hours (28,800 ticks).
- Restart is the single named exception to never-reset.

### Build prompt

```text
Make the colony run in time, following the project documents.

The tick:
- One pure function: (state, n ticks) -> state after n ticks.
  Applies the contract's numbers exactly — production, consumption,
  clamping, then health. Don't invent or round any rate.
- Colonist arrivals, ore reserve depletion, and colonist death all
  happen inside this same function, per CONTRACT.md.
- If removing a colonist leaves none remaining: set status to
  game_over, stop applying further ticks. Same transaction, compare
  sols survived to the account's bestSolsSurvived and update if
  beaten — the only write outside the colony's own table.
- No separate timers, no special-casing any of the above.
- Nothing but arithmetic inside it: no clock reads, no database
  calls.
- 100 ticks in one call must equal calling it a hundred times with
  one tick.
- Cheap enough to run tens of thousands of times without stalling
  the page.

Catching up on load:
- A server route: read the account's colony, compute whole ticks
  since it was last applied, apply them, save with the new time.
- Cap at 28,800 ticks. Discard anything older.
- Never reset automatically. The only reset is the restart button
  below.

While someone is playing:
- The browser runs the same tick function forward from the last
  server value, for display. Never save what it computes — discard
  it the moment a real value arrives.
- Server saves on every player action, otherwise every 15 seconds.
- Subscribe to this account's colony so a second tab stays in step.

On screen:
- Oxygen, power, ore, average colonist health, and everything in
  DESIGN.md's state-on-screen rules. All of it reads from state.
  None of it runs on its own timer.
- On game over, DESIGN.md's dedicated screen: sols this run, best
  sols survived, and Start New Colony.
- Restart follows the Ownership rule and reuses the colony-creation
  logic from sign-up. bestSolsSurvived lives on the account row,
  not the colony row — restart never touches it.

Then /browser check it, in order:
1. Drain oxygen to zero with no scrubbers, confirm health falls and
   the scene hazes, reload and confirm it continues rather than
   restarts.
2. Build a habitat, advance 300 ticks with catch-up, confirm a
   colonist appears at the landing zone.
3. Build an extractor, advance until the reserve hits 0, confirm
   production stops while power draw doesn't.
4. Starve a colonist to death, confirm the game-over screen shows
   the correct sol count.
5. Start New Colony, confirm oxygen/power/ore reset with no
   buildings or colonists, and best sols survived is unchanged.
6. Die faster than the first run — best sols stays the same. Die
   slower — best sols updates.

Record all six.
```

### Browser vs database

```text
Using the Supabase CLI, read the current account's colony and tell
me the oxygen, power, tick count, and time of the last applied
tick. Compare them with what the page is showing, and explain any
difference.
```

Small, consistent differences are the client prediction running ahead — not a bug.

---

## Colonists, Pathfinding Logic Refinement, Adding Help Modal

### Movement prompt

```text
Give the colonists movement, following the project documents.

- Colonists pick a destination building and walk to it, one tile per
  tick, along the shortest route available.
- Buildings block movement. A colonist walks to a free tile next to
  its destination, never onto the building itself.
- If no route exists, the colonist stays put rather than teleporting
  or walking through anything.
- Movement happens inside the tick function, so catching up on lost
  time moves colonists too.
- Every choice must be repeatable: which building a colonist picks,
  and how a route breaks a tie between two equally short paths. Same
  starting state and same number of ticks, same result every time.
- Work out a route once when a colonist picks a destination, then
  follow it a step per tick. Don't recalculate every tick.
- When a colonist is newly created by an arrival, assign it a
  destination in the same tick: the nearest habitat with unclaimed
  capacity. Claim that capacity immediately, not on arrival.
- Save each colonist's destination and route so a reload picks up
  where it left off.

Then /browser check it: block the direct line between two
buildings and confirm the route bends around and stops beside the
destination; wall a colonist in and confirm it stays put; advance
time by an hour, reload, and confirm colonists are where the
simulation says rather than where they started; then advance a
fresh colony 300 ticks and confirm the newly-landed colonist is
already walking toward a habitat rather than standing at the
landing zone. Record all four.
```

A* is verified against properties — bends around obstacles, stops beside the target, stays put when walled in, deterministic ties — not appearances.

### Parallel improvements

```text
Four improvements to the colony, which don't overlap. Follow
DESIGN.md for all of them.

1. Colonist appearance — draw colonists as distinct figures on their
   tiles, with a small health bar above each.
2. Resource display — a compact panel showing oxygen and power as
   bars that change colour as they approach zero.
3. Placement feedback — when a building is selected in the toolbar,
   show a ghost preview on the hovered tile before clicking.
4. Help modal — a "?" affordance in the bottom-right corner, open at
   any time, closeable. Explains the survival goal, every building's
   cost and effect, and the colonist arrival rule. Read every number
   from CONTRACT.md; don't hardcode any of them here.

These touch different parts of the codebase, so work on them in
parallel. /browser verify all four when they're done.
```

The agent splits the four non-overlapping tasks into subagents on its own. This isn't `/teamwork-preview` — that heavier command is top-tier-plan only and decides the role split for you.

| Parallelise | Keep with one owner |
|---|---|
| Non-overlapping UI improvements | Tick logic, state shape, ownership, schema |

### Panel separation

```text
Reorganize the diagnostic panel, following DESIGN.md.

- Split it into two blocks with a thin divider: session identity
  (signed-in account, colony owner) on top, kept small; full state
  below (tick, oxygen, power, ore, ore reserve, colonist health,
  buildings, last applied tick).
- Both blocks stay plain text, no colour, no animation. The panel's
  id doesn't change.

Then /browser check it: confirm every field is still readable at
the same id — nothing here should need any playtest scenario to
change, only how the panel looks.
```

---

## Running Our Playtest Workflow Scenario

```text
/playtest
```

The run signs in, checks which account's colony it is reading, and advances time by computing it — eleven scenarios finish in seconds.

### Eleven for eleven

A clean first run is the content, not an anticlimax: the build lessons were built against these exact claims.

| Scenario | Proves |
|---|---|
| 4 | Batch catch-up matches live ticking exactly |
| 6 | One account can't see another's colony |
| 11 | Personal best works in both directions |

### When the recording lies

| Failure | Guard |
|---|---|
| Page captured mid-load, spinner reported as success | Wait instruction; no async image assets; open the video if a run looks too clean |
| Page reading a few ticks ahead of the database | Expected client prediction — read the last-applied-tick value |

_Verification you don't spot-check is a nicer-looking assumption._

---

## Building a Skill to Start a New Game and Survive as Long as it Can

Eleven scenarios prove eleven claims. A long run asks how far a colony actually gets.

### Create the skill

```text
Create a new skill at .agents/skills/long-run-player/SKILL.md:

---
name: long-run-player
description: Plays a fresh colony as far as it survives, using
  catch-up to compress time, and reports the outcome. Never fixes
  anything it finds.
---

Sign in as the disposable test account. If it already has a colony
in game_over status, restart from the game-over screen first.

/browser to place the first habitat — the same first move any
player has to make, since nothing arrives without shelter to hold
it.

Then loop, checking state with the Supabase CLI rather than the
browser between rounds, so more rounds fit in the time you have:
- Advance the colony using the catch-up path, in chunks up to the
  28,800-tick cap.
- Check the colony's state via the CLI.
- If a building would help and is affordable, /browser to place
  it — the same call any player would make, using CONTRACT.md's
  numbers rather than a guess.
- Stop when the colony reaches game_over, or when 10 minutes of
  real time have passed, whichever comes first.

Report: sols survived, what the colony was doing when it stopped,
and anything observed that looked inconsistent with CONTRACT.md.
Do not fix anything. A finding here is something to bring back to
a session where you can review it, not something to change on
your own.
```

- Placement goes through the browser because it's the only interface for it.
- State checks go through the CLI so more rounds fit in the budget.
- You're the audience, not the operator.

### Run it

```text
Use the long-run-player skill.
```

### The finding

The colony reached a permanent equilibrium by tick **3,071** and had nothing left to do for **2.4 million** ticks. Every rule was followed; the rules had no reason to keep changing.

---

## Fixing the Flaws in the Game Logic

The redesign the long run demanded — not harder to build, impossible to finish building.

### What changed

| New | Detail |
|---|---|
| Food and farms | A third life-support resource, same shape as oxygen and power |
| Building condition | Operational, broken, buried; repair costs labour and ship-delivered electronics |
| Dust storms | Bury buildings; digging out costs nothing |
| Aging | Death on a seeded 12,000–18,000-tick clock |
| Rovers and economy | Garages, mining sites, asteroids, refineries, battery cells |
| Escorted arrivals | A landed colonist and their electronics are lost if no rover arrives within 150 ticks |

**Redesigned:** ore moves from one global reserve to per-tile deposits. An extractor draws only from its own tile.

Escort has no automatic fallback — population growth is tied to checking in. `PLAYTEST_PLAN.md` grows to **24 scenarios**; confirm all pass once the build is real.

### Build prompt

```text
Extend the colony to match the current CONTRACT.md, AGENTS.md,
DATABASE.md, and DESIGN.md — Tier 3 is fully specified there now:
food and farms, building breakage and repair, dust storms and
digging out, colonist aging, per-tile ore deposits replacing the
single reserve, escorted arrivals with ship-delivered electronics,
and the full rover/mining-site/asteroid/refinery/battery-cell
economy.

Follow DATABASE.md's Tier 3 Schema section exactly: real tables
for ore deposits and rovers, JSONB on the colony row for battery
cells, mining sites, the active asteroid, pending arrivals, and
the seed.

Update the help modal, Life Support Telemetry, and the diagnostic
panel to cover everything new, per DESIGN.md.

Then /browser check it: place a farm and confirm food behaves like
oxygen and power; force or wait for a building to break and confirm
repair needs both the colonist count and the electronics CONTRACT.md
specifies; confirm a dust storm buries something and digging it out
costs nothing; place an extractor on a dry tile and confirm it
produces nothing; place one on a tile with ore and confirm it
depletes that tile specifically; build a garage and refinery, refine
a cell, dispatch a rover to a mining site, and confirm it returns
with cargo; let a ship land and dispatch a rover to escort it,
confirming the colonist and their electronics arrive; then let a
second ship land with every rover busy, and confirm the arrival is
lost at tick 150. Record all of it.
```

---

## Re-Running the Playtests After the Redesign

The redesign changed what a colony can do, so both checks run again: the scenario suite to prove the new rules hold, and the long run to see how far a colony gets now.

### Garage-gated chunking

The long-run skill needs one change before it runs again. 28,800-tick chunks would let every ship time out mid-chunk, so the skill's strategy changes:

```text
Update the long-run-player skill's catch-up strategy:

- Before a garage exists, advance in chunks up to 5,000 ticks —
  there's no rover to dispatch yet, so speed costs nothing here.
- Once a garage exists, cap chunks at 300 ticks, matching the ship
  landing interval exactly. After each chunk, if a pending arrival
  exists and a rover is idle, dispatch it immediately, before
  advancing further.
- Everything else about the skill — the reporting, the "never fix
  anything" rule, the 10-minute budget — stays exactly as it was.
```

The 300-tick cap costs roughly two orders of magnitude of simulated scale once a garage exists — a real design tradeoff, not a bug.

### Run both again

```text
/playtest
```

```text
Use the long-run-player skill.
```

- `/playtest` now covers **24 scenarios**. All of them should pass before anything ships.
- The long run is the real question. The first one ended in a permanent equilibrium by tick 3,071. This time, the colony should still have decisions to make when the budget runs out.
- The skill still reports and never fixes. Bring any finding back to a session where you can review it.

### Already in place before shipping

- Life Support Telemetry has full parity with the diagnostic panel.
- The diagnostic panel is gated behind `?debug=true`.
- `.agents/workflows/playtest.md` appends the flag — confirmed with a clean `/playtest` run.

---

## Deploy with a Final Round Testing in Production

Deploy to Vercel, connected to the `mars-colony` repository. The database and catch-up route are already live, so the colony you ship is the colony you tested. Then test it again, on the live site.

### Public key, not service role

The live site must use the public key. A leaked service role here exposes every account's colony *and* everything belonging to Waypoint, which shares the database.

### Confirm it as a stranger would

1. Open a private window.
2. Choose the guest option.
3. Build a colony and let it tick.
4. Reload and watch it resume.
5. Close it, return in an hour, and watch the elapsed time land at once.

Someone who has never met you should walk that path without an account or an explanation.

### The Director's Audit, against production

Run it against the live site, not a local copy. Start with what no scenario could ask:

- Is a one-second tick too fast to react to, or too slow to feel alive?
- Does five health per tick make failure recoverable or hopeless?
- Is eight hours the right catch-up ceiling, or does returning to an afternoon of dying feel like punishment?
- Does a 150-tick escort window feel like pressure or an unfair trap?
- Does the board read clearly with forty buildings, some broken, some buried?
- The grid at its edges, rapid clicking during placement, frame rate in a large colony.

### Document-by-document pass

```text
Review the colony against CONTRACT.md, AGENTS.md, DESIGN.md,
DATABASE.md and PLAYTEST_PLAN.md.

For each rule in those documents, tell me whether the code
implements it — pass or fail, with the file and line where it's
handled.

Then check these specifically:
- Row-level security is on for every marscolony_ table, and no
  access rule is broader than "the account that owns the row".
- Nothing works out who owns a row from anything but the session.
- No value the browser calculated is ever saved to the database.
- Nothing in the codebase refers to a table that isn't a
  marscolony_ table.
- The catch-up cap is enforced, and the tick function does nothing
  but arithmetic — true of every system in it now, not just the
  original two resources.
- A pending arrival and their electronics can never be credited to
  the colony more than once, even if the same tick gets processed
  twice.

Flag anything in the documents that no code implements, and
anything in the code that no document asked for. Don't fix anything.
```

- A missing access rule breaks nothing you can see — your colony works, and so would everyone else's on your screen.
- Double credit looks, on any single playthrough, exactly like a colony doing well.

Record each finding as pass or fail, close them, and commit as `AUDIT_7.md`.

_Green scenarios prove the rules you wrote. The audit checks the rules you forgot to write — against the thing that's actually live._

### What carries forward

- State behaviour as trajectories, not snapshots.
- Let an agent drive the real thing and record it.
- Determinism as a precondition.
- Build a surface your verifier can read.
- Enforce ownership below your own code.
- Unattended work reports; it doesn't repair.
- The cheapest system computes what happened rather than sitting through it.

### End-of-project artifacts

`CONTRACT.md`, `AGENTS.md`, `DESIGN.md`, `DATABASE.md`, `PLAYTEST_PLAN.md` (24 scenarios), `.agents/` rules, workflow, and skills, `marscolony_` migrations, `AUDIT_7.md`, and the deployment.

---

## Bonus Lesson: Using ChatGPT Astra to Make 3D Models

Take the finished Antigravity colony to Astra, then do two things. Rebuild the 2D Canvas sprites as 3D models, and add a home screen that shows those models off before the player signs in. Reference build: [ztm-mars-colony-astra](https://github.com/JacintoDesign/ztm-mars-colony-astra).

### The rule this lesson keeps

**Only the picture changes. The simulation doesn't.** The tick function, contract rules, server actions, and database are left alone. Terrain gains height, craters, and rock formations, but that's visual only. Pathfinding, ore deposits, and where you can build all stay as they were. If a 3D change would alter what the game *does*, it's out of scope.

### Amend the standard first

The existing `DESIGN.md` says to draw everything on the 2D Canvas and never load image assets. The 3D edition doesn't quietly break that rule. It adds a dated section to the top of the document that **supersedes** the Canvas 2D scene requirements:

- Render the world with Three.js WebGL, using procedural meshes, physically lit materials, and soft shadows.
- Keep the existing flat telemetry UI.
- Give all seven buildings, the colonists, rovers, transport capsules, and meteorites real geometry. There are still no external model files.
- Use terrain raycasting for picking, and the shared grid-to-world transform for placement.
- Keep condition and production indicators state-driven. Only camera movement may update between simulation ticks.

A second addition replaces the floating fault cubes with steady warning signs projected onto the screen. A crossed-out yellow bolt means power is off. A red exclamation mark means broken, and an orange one means buried. Buried and broken take priority over power loss.

_The standard changes before the code does. A tool that ignores the written standard is a problem. A standard that's changed on purpose is a decision._

### The models

| Piece | Where it lives |
|---|---|
| Procedural models for every facility, colonist, rover, capsule, and meteorite | `src/assets/models/colony-models.ts` |
| The Three.js scene: terrain, lighting, shadows, picking | `src/engine/colony-scene.ts` |
| The renderer, now delegating to the scene instead of drawing sprites | `src/engine/renderer.ts` |

### The home screen

Signed-out players used to land straight on the sign-in form. Now they land on a home screen: a 3D colony running behind a "A new frontier." title and an **ENTER COLONY** button, and the button opens the sign-in form.

- **It's a decorative fixture.** `src/home-preview-state.ts` builds a fixed layout from its own seed. It is never loaded into the colony store and never sent to the server.
- **Real state replaces it on sign-in.** When the colony loads, the preview state is cleared. On sign-out, it comes back.

### The model showcase

`/model-preview.html` is a standalone, read-only page showing every model with annotations. It needs no account, no simulation loop, and no live colony. Use it to judge the models on their own, without having to play the game.

### Verify the renderer, not just the look

`/tests/renderer-check.html` is an isolated browser check that needs no Supabase account and makes no network writes. It confirms:

- Terrain picking hits all 400 tile centres.
- All seven buildings can be picked.
- Operational, broken, buried, and deactivated states each render.
- Rendering never changes the source fixture.

Then play it for real. Scroll to zoom, drag to pan, click buildings to inspect them, and check that touch pinch and pan still work.

### What to take from it

- **3D models come cheaply now. Keeping them consistent with the rest of the game still takes care.** Astra's one-shot modelling does the heavy lifting. The amended standard, the unchanged simulation, and the renderer check are what make it safe to ship.
- **Keep the preview surfaces separate from the live game.** The home screen and the showcase page are both pure fixtures, so they can't corrupt a colony.
