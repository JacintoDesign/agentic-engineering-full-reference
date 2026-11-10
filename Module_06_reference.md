# Module 6 Reference Guide
### Cursor — Composer Model Fundamentals: Rules, Safety, the Agents Window, and the Inner Loop

Use this guide alongside the Module 6 videos.

_Model names, prices, plan allowances, and setting locations in Cursor change quickly. Confirm current values in Cursor's dashboard and docs before relying on a recording-era label._

---

## Table of Contents

1. [Why 'Cursor' and 'Composer' Deserve a Place in Your Working Stack](#why-cursor-and-composer-deserve-a-place-in-your-working-stack)
2. [Installing 'Cursor', Testing Out 'Composer 2.5'](#installing-cursor-testing-out-composer-25)
3. ['Composer', the Agents Window, /multitask, Subagents and Worktrees](#composer-the-agents-window-multitask-subagents-and-worktrees)
4. [How the Agents Window Actually Works](#how-the-agents-window-actually-works)
5. [Extending Functionality: CLI, MCP, Skills, Commands and Plugins](#extending-functionality-cli-mcp-skills-commands-and-plugins)
6. [Inner Loop: Design Mode, Cursor Browser, Review Agents](#inner-loop-design-mode-cursor-browser-review-agents)

---

## Core Concepts

**Match the model to the task.** Reasoning work — hard architecture, a subtle algorithm — justifies a frontier model. Well-specified execution is Composer's job, and most of building is execution.

**Cheap tokens make thoroughness affordable.** When an agent does the building, price per token decides what you can afford: re-reading the whole project, running agents in parallel, being thorough.

**Cheap and unwatched still drifts.** Glance at the usage meters the way you'd glance at a fuel gauge.

**Rules live in the right scope.** `AGENTS.md` holds what is always true for this project; the contract holds what is true for one feature; user rules hold what is true for you everywhere.

**You decide what runs unattended.** Read-only calls run freely; anything that writes, deletes, moves, or reaches outside the machine earns a check.

**The Agents Window is a control room, not a chat log.** Dispatch, watch, and review from one place; drop into the editor for close work.

**CLI first, MCP when it earns it.** A connected server costs context on every turn. A known CLI costs context only when invoked.

**Verification substitutes for domain command.** When you can't judge the output by looking, the contract and the tests judge it for you.

---

## Why 'Cursor' and 'Composer' Deserve a Place in Your Working Stack

### What changed

- Composer 2.5 is Cursor's own model, built on Moonshot's Kimi K2.5 checkpoint and trained for sustained, multi-step coding inside a real codebase.
- It is tuned to follow complex instructions reliably — contracts, design specs, acceptance criteria.
- Most turns finish in under thirty seconds, which makes several agents at once practical.

### The economics

| Item | Recording-era price |
|---|---|
| Composer 2.5 input | $0.50 per million tokens |
| Composer 2.5 output | $2.50 per million tokens |
| Fast variant | Same intelligence, higher price — still below frontier fast tiers |

### Watching the spend

Open **Plan and Usage** in the Cursor dashboard. Usage splits into two meters:

| Meter | What draws on it |
|---|---|
| Auto and Composer | Cursor's own models, from the plan's included pool |
| API | Frontier models, usage past your limits, and cloud agents |

The Composer meter filling is the plan working. The API meter moving when you didn't choose a frontier model is the earliest sign of unintended spend.

### Reasoning or execution?

```text
Hard thinking not yet done   → frontier model, pay the premium
Plan already written         → Composer 2.5, the default
```

Spending your most expensive model on well-specified execution is waste, not diligence.

### The module arc

- **Waypoint** — a greenfield map-guides app over PostGIS, built from an empty folder.
- **ZTM Music Player v2** — a brownfield refresh of an existing codebase, with the cloud review pipeline added.

---

## Installing 'Cursor', Testing Out 'Composer 2.5'

### Install and select the model

1. Install Cursor 3.7 or later and sign in.
2. Open the model picker and choose **Composer 2.5**.
3. Set it to **standard**, not fast. Fast is the same intelligence for more money; switch only when the wait genuinely breaks your focus.
4. Open **Plan and Usage** and locate both meters while they are quiet.

### AGENTS.md

Create `AGENTS.md` at the project root. Cursor reads it on every turn, and other agent tools read it too.

```markdown
# Project Rules

## Stack
- Next.js 16 (App Router), React 19, TypeScript strict mode
- Supabase for database, auth, and storage
- Deploy target: Vercel

## Conventions
- Server Components by default; Client Components only when interactivity requires it
- All database access through typed query functions, never inline in components
- No `any`. If a type is unknown, model it.
- Environment variables typed and validated at startup, never read raw

## Working agreement
- Read CONTRACT.md before implementing any feature
- When a task is ambiguous, ask before building — do not assume
- Make the smallest change that satisfies the requirement
- After any multi-file change, summarise what changed and why
```

Keep it short, specific, and true. A bloated rules file is as useless as none.

### Rule scopes

| Scope | Location | Holds |
|---|---|---|
| Project | `AGENTS.md` at the root, committed | Stack, conventions, working agreement |
| Feature | `CONTRACT.md` | Decisions for one build |
| Personal | User rules in Cursor settings | Habits that follow you across every project |
| Scoped | `.cursor/rules/` | Rules that fire only for certain files or by name |

### Run Mode

**Settings → Agents → Approvals and Execution**

| Mode | Behaviour |
|---|---|
| Auto-review (recommended) | Known-safe calls run; riskier shell commands run sandboxed; anything unsandboxable goes to a classifier that allows, redirects, or asks you |
| Allowlist | Only actions on your list run unattended; everything else pauses |
| Run Everything | No prompts; you accept the risk |

Known-safe, read-only commands:

```bash
git status          # what's changed
git diff            # the actual line-level changes
git log --oneline   # recent history
git branch          # which branch you're on
ls / pwd / cat      # look around the project
npm run lint        # read-only checks
```

Commands that earn a check:

```bash
git commit / git push     # rewrites history or the remote
git reset / git checkout  # can throw away uncommitted work
rm / mv                   # delete or move files
npm install               # downloads and runs third-party code
supabase db push          # changes the live database
```

Sort by what a command does — read or write — not by which tool it comes from.

### Permission files

| File | Purpose | Location |
|---|---|---|
| `permissions.json` | Plain-English steering for what Auto-review runs vs. reviews | `~/` for all projects, or the project's `.cursor/` |
| `sandbox.json` | Network domains and extra paths a sandboxed command may reach | Same |

You can tell the agent what you want in chat and let it edit `permissions.json` for you.

_Auto-review is not a security boundary._ Cursor still asks before deleting files, touching anything outside the workspace, or driving the browser.

### Hooks

A hook is a script Cursor runs at a chosen point in the agent loop — to observe, or to decide. Project hooks live in `.cursor/` and commit with the repo; personal hooks live in `~/.cursor/`.

`.cursor/hooks.json`:

```json
{
  "version": 1,
  "hooks": {
    "beforeShellExecution": [{ "command": ".cursor/hooks/audit.sh" }],
    "afterFileEdit":        [{ "command": ".cursor/hooks/audit.sh" }],
    "sessionEnd":           [{ "command": ".cursor/hooks/audit.sh" }]
  }
}
```

`.cursor/hooks/audit.sh`:

```bash
#!/bin/bash
json_input=$(cat)
echo "[$(date '+%F %T')] $json_input" >> /tmp/agent-audit.log
exit 0
```

```bash
chmod +x .cursor/hooks/audit.sh
tail -f /tmp/agent-audit.log
```

A `before` hook can return `{"permission":"deny"}` to stop an action outright — how a rule like "no force-pushes" becomes unskippable.

_The allowlist is a guideline the agent follows; a hook is a guarantee you enforce, or a record you keep._

### Commit the setup

```bash
git init
git add AGENTS.md .cursor/
git commit -m "chore: project rules and agent hooks"
```

Rules and guardrails land before any code.

---

## 'Composer', the Agents Window, /multitask, Subagents and Worktrees

### The mental model

| Piece | Role |
|---|---|
| Composer | The worker — reads across the codebase, plans a multi-file change, makes it, explains it |
| Agents Window | The control room — every session, its project, branch, location, history, and changes |
| Editor | Close, hands-on work — reading a file, a precise manual change |

### Parallel work

```text
/multitask
```

Runs several agents in parallel instead of queuing requests. Given a multi-step plan, **Build in Parallel** runs independent steps at once and keeps dependent steps in order.

_Three agents spend tokens three times as fast. Run the genuinely independent steps in parallel; don't fan out just because you can._

### Subagents and worktrees

- **Subagent** — a separate agent spawned for one piece of the task, in its own space, reporting back. Built in: research, shell, and browser. You can define your own.
- **Worktree** — an isolated copy of the project on its own branch. Each parallel agent works in its own copy; pull a finished one back with a click.

Cursor handles the bookkeeping. Deciding what is safe to run side by side is still yours.

### Local and cloud

- Local — close work, testing, review.
- Cloud — offloaded long jobs and automated review; cloud agents draw on the API meter.
- Move a session between them from the Agents Window, and pull a cloud result back down to test it yourself.

---

## How the Agents Window Actually Works

### Setup

Pick two small, unrelated repositories you can tinker with safely — personal projects or the provided practice repos — and open both so they are reachable from the Agents Window.

### Dispatch

| Repo | Job | How |
|---|---|---|
| First | Fix a copy typo and tidy the README's setup section | One agent from the Agents Window |
| Second | Bump an out-of-date dependency and add a small missing endpoint | `/multitask` — one subagent per piece, each in its own worktree |

_Across repos you dispatch separate agents; `/multitask` splits one request's independent parts into parallel subagents. Same instinct at two scales._

### Review what comes back

- Review each diff from the Agents Window before accepting it.
- Pull each subagent's worktree back into the main branch only once it is right.
- Parallel dispatch never means unattended trust.

The pattern — dispatch independent work in parallel, review each result as it lands — is the module's core rhythm on both projects.

---

## Extending Functionality: CLI, MCP, Skills, Commands and Plugins

### The customisation layer

| Layer | What it is | Use it for |
|---|---|---|
| MCP server | A bridge exposing an outside capability as tools | Structured access, or a service with no CLI |
| Skill | Packaged know-how loaded when a task matches, or by name | A pattern you've explained two or three times |
| Command | A reusable slash workflow | A repeatable process — e.g. `/audit`, built later in the Waypoint verification phase |
| Plugin | A bundle of skills, subagents, MCP servers, hooks, and rules | Installing a whole capability at once |

### The cost of MCP

- Tool definitions occupy context on every turn, even idle.
- Results tend to come back verbose.
- Cursor caps how many tools can be active at once.
- For a routine operation the agent can run from the command line, a CLI call is usually cheaper.

_A connected server earns its standing context cost only when it gives you something a known CLI can't._

### Context7, CLI-first

```bash
npx ctx7 setup --cursor
```

- Signs you in and generates a free API key for higher rate limits.
- Choose the **CLI and skills** option, not the MCP server.
- Installs a skill that makes the agent fetch current docs for a library question on its own.
- `npx ctx7 remove` undoes either install.

Open the skills panel after setup — the Context7 skill is a working example of a skill: a file the agent loads when relevant.

### Plugins

Browse Cursor's Marketplace, inspect what a plugin bundles, and install nothing yet. The CLIs and Context7 already cover both builds in this course; add a plugin when a build asks for it, not because the shelf is there.

---

## Inner Loop: Design Mode, Cursor Browser, Review Agents

### The surfaces

| Surface | Checks | How |
|---|---|---|
| Cursor browser | Behaviour — does the feature work in the running app | Agent runs the preview, clicks through, corrects itself |
| Design Mode | Visuals — direct by pointing | Click elements; multi-select to match one to another; voice queueing |
| Bugbot | Code — bugs and quality | `/review` or `/review-bugbot` locally; automatic on PRs |
| Security Review | Vulnerabilities — unsafe patterns, missing checks | `/review` or `/review-security` |
| `/simplify` | Quality — bloat, needless abstraction | Run at settling points; read before accepting |

_An agent that can see its own output checks against reality. One that can't checks against its own optimism._

### Local review commands

```text
/review            # choose Bugbot and/or Security Review
/review-bugbot     # Bugbot only
/review-security   # Security Review only
/simplify          # quality and efficiency pass over changed files
```

A diff reviewed locally is not billed again when the matching PR opens.

### Inner loop and outer loop

| Loop | Where | What runs | When |
|---|---|---|---|
| Inner | Your machine | Browser subagent, `/review`, `/simplify` | Every meaningful change |
| Outer | The cloud, on a PR | Bugbot (and team-plan Security Review / approval agents) | At the pull request |

_Inner loop on your machine as you build; outer loop in the cloud on the PR. The first keeps you fast; the second is the gate before anything merges._

For Waypoint, the inner loop plus your own review is enough. The ZTM Music Player refresh adds the outer loop.

### Setup checklist before Waypoint

- Composer 2.5 selected, standard variant.
- Both usage meters located.
- `AGENTS.md` committed at the project root.
- Run Mode chosen deliberately; permission and sandbox files only where needed.
- Optional audit hook committed in `.cursor/`.
- Context7 installed as CLI plus skill.
- You can name which surface checks behaviour, code, security, and quality.
