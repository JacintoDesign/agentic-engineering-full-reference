# Tool Equivalents
### The Same Ideas Under Four Names — Claude Code, Cursor, Codex, and Antigravity

The course teaches one method in four tools. Each tool names things its own way. Use this table to translate, and to carry the method into a fifth tool.

_File locations and feature names were current when the course was recorded and change between releases. A dash means the course didn't cover it in that tool, not that the tool can't do it. Check the tool's docs before relying on a path._

---

## Table of Contents

1. [Project Instructions](#project-instructions)
2. [Safety and Permissions](#safety-and-permissions)
3. [Planning and Delegation](#planning-and-delegation)
4. [Reusable Capabilities](#reusable-capabilities)
5. [Verification and Review](#verification-and-review)
6. [Working Away From the Desk](#working-away-from-the-desk)
7. [What Never Changes](#what-never-changes)
8. [Moving to a Fifth Tool](#moving-to-a-fifth-tool)

---

## Project Instructions

| Concept | Claude Code | Cursor | Codex | Antigravity |
|---|---|---|---|---|
| Anchor file, read every session | `CLAUDE.md` | `AGENTS.md` | `AGENTS.md` | `AGENTS.md` at the root |
| Personal rules for every project | `~/.claude/CLAUDE.md` | User rules in Cursor settings | — | `~/.gemini/GEMINI.md` |
| Rules for certain files or tasks | `CLAUDE.md` files in subfolders | `.cursor/rules/` | — | `.agents/rules/`: Always On, Manual (@-mention), or Model decides. 12,000-character cap per file |
| Committed project config | `.claude/settings.json` | `.cursor/` | `.codex/config.toml` | Project settings |
| Personal or local overrides | `.claude/settings.local.json` | — | Local session state under `.codex/` (gitignored) | — |
| Cross-session memory | `/memory`, plus Ogham MCP | — | — | — |

**The pointer rule.** An anchor file is read automatically, but the other documents aren't. In each tool, the anchor (or an always-on rule) must say where `CONTRACT.md`, `DESIGN.md`, and the rest live and that they're the source of truth. Module 13 adds `.agents/rules/project-docs.md` for exactly this reason.

---

## Safety and Permissions

| Concept | Claude Code | Cursor | Codex | Antigravity |
|---|---|---|---|---|
| What runs without asking | `permissions.allow` / `deny` in `.claude/settings.json` | Run Mode (Settings → Agents → Approvals and Execution). Auto-review recommended | `.codex/rules/*.rules` | Commands Outside Sandbox, set per project |
| Enforcing intent | Hooks that block | Hooks that decide | Rules that enforce what `AGENTS.md` states | Rules plus permissions |
| What the agent may touch in data | `CONTRACT.md` and the anchor file | `CONTRACT.md` and `AGENTS.md` | `AGENTS.md` and Rules | `DATABASE.md` as an Always On rule |

_Permissions decide whether a command may run. They don't decide what it does once it runs. Data ownership always lives in a document._

---

## Planning and Delegation

| Concept | Claude Code | Cursor | Codex | Antigravity |
|---|---|---|---|---|
| Plan before code | Plan Mode (`Shift+Tab`) | Plan Mode | `/plan` | Plans and task lists, shown as artifacts you comment on |
| Choosing the model | Per session | Composer for execution, a frontier model for hard reasoning, or Auto | Pinned per named agent | — |
| Subagents | Subagents | Built-in research, shell, and browser subagents | Named agents in `.codex/agents/*.toml`, each pinned to a model tier and a sandbox | Subagents, including the browser subagent |
| Split one request into parallel parts | Parallel sessions | `/multitask` | Ultra (automatic fan-out) or a declared team | Parallel agents |
| More depth on one hard task | — | A frontier model | Max | — |
| Isolation for parallel work | One git worktree per session | Worktrees | Worktrees | — |

---

## Reusable Capabilities

| Concept | Claude Code | Cursor | Codex | Antigravity |
|---|---|---|---|---|
| Saved prompts and commands | `.claude/commands/*.md` | — | — | `.agents/workflows/*.md` (for example `/playtest`) |
| Skills | Skills, often bundled in plugins | Skills | `.codex/skills/<name>/SKILL.md` | `.agents/skills/<name>/SKILL.md` |
| Bundled capabilities | Plugins | Plugins (skills, subagents, MCP servers, hooks, rules) | — | — |
| Hooks | `hooks` in `.claude/settings.json` | `.cursor/hooks.json` | Hooks (behaviour can differ after import) | — |
| Adding an MCP server | `claude mcp add …` | MCP settings, but CLI first | `codex mcp add …` | — |
| Live library docs | Context7 MCP | Context7 via the `ctx` CLI | A `docs_researcher` agent | — |
| Image generation | — | — | Built in | Built in, on a separate quota |
| 3D from a prompt | — | — | ChatGPT Astra | — |

---

## Verification and Review

| Concept | Claude Code | Cursor | Codex | Antigravity |
|---|---|---|---|---|
| Seeing the running app | Desktop preview pane, configured in `.claude/launch.json` | The Cursor browser | Integrated browser, or Playwright MCP | `/browser`: the subagent drives your real Chrome |
| Proof of a sequence | Screenshots | Screenshots | Screenshots | Recordings, the only proof for a system on its own clock |
| Local review (inner loop) | A reviewer subagent | `/review`, `/simplify` | A reviewer agent (`.codex/agents/reviewer.toml`) | Comments on artifacts |
| Review on the PR (outer loop) | PR monitoring in the desktop app | Bugbot, automatic on every PR | — | — |

---

## Working Away From the Desk

| Concept | Claude Code | Cursor | Codex | Antigravity |
|---|---|---|---|---|
| Control from your phone | Remote Control | — | Automatic Remote Control to any project | — |
| Scheduled or long-running work | Routines, `/goal` | Cloud agents (billed to the API meter) | — | Long-run playtests, with catch-up compressing time |
| Usage shape (entry tier) | Rolling 5-hour window plus a weekly cap | Credit draw-down | 5-hour block plus an unpublished weekly limit | 5-hour refresh under a weekly ceiling |

---

## What Never Changes

These carry across every tool, and they're what the course is really teaching:

- **The documents.** `CONTRACT.md`, `DESIGN.md`, `TEST_PLAN.md`, and the rest are plain Markdown. Only the anchor file's name changes.
- **Plan before you build** when the parts interlock, and give a direct prompt when the work is linear.
- **Atomic units.** Build, verify, understand, and commit one at a time.
- **Verification you didn't write by eye.** Tests, browser checks, and playtests.
- **The Director's Audit.** Judge the output against the spec, then ship it or send it back.

---

## Moving to a Fifth Tool

When a new tool arrives, ask these questions in order:

1. **What file does it read every session?** Rename your anchor file to that, without changing the content.
2. **Where do scoped and personal rules go?**
3. **How do I make it plan before it writes?**
4. **How do I control what runs without asking?** Set this before the first build.
5. **Can it see the running app?** If not, add a browser MCP.
6. **How is usage metered, and where do I see it?**

Then rebuild something you've already built, from the same documents (Module 14 does exactly this with ChatGPT Astra). Where the new tool drifts and the old one didn't, that's the tool. Where both drift the same way, that's the document.
