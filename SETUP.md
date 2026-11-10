# Before You Start
### Accounts, Installs, and Keys — Module by Module

Set these up before each module so you don't stall partway through a lesson.

_Plans, prices, free-tier limits, and minimum versions change often. The figures here were current when the course was recorded. Check each provider's own page before you pay for anything._

---

## Table of Contents

1. [The Short Version](#the-short-version)
2. [Paid Plans](#paid-plans)
3. [Free Accounts](#free-accounts)
4. [Local Installs](#local-installs)
5. [Module by Module](#module-by-module)
6. [Things That Carry Forward](#things-that-carry-forward)
7. [Supabase Project Budget](#supabase-project-budget)
8. [Keys and Secrets](#keys-and-secrets)

---

## The Short Version

- **You need one paid plan at a time, not four at once.** Each tool section runs on its own entry tier, so subscribe monthly when a section starts and cancel when it ends (Module 15 explains why monthly beats annual).
- **Everything else is free.** All the hosting, databases, APIs, and email used in the course run on free tiers.
- **Keep your earlier work.** Several later modules build on earlier projects. See [Things That Carry Forward](#things-that-carry-forward).

---

## Paid Plans

| Plan | Entry price | Needed for |
|---|---|---|
| Claude Pro (or higher) | $20/mo | Modules 1–5, 9, 10, 14 |
| Cursor Pro | $20/mo | Modules 6–8 |
| ChatGPT Plus (or higher) | $20/mo | Modules 11–14 (Codex, and ChatGPT Astra in 12–14) |
| Google AI Pro | ~$20/mo | Module 13 (Antigravity). The free tier exists, but its daily agent-request allowance is small |

US list prices. Module 15 compares what each entry tier actually buys.

---

## Free Accounts

| Service | Used for | First needed |
|---|---|---|
| GitHub | Every repository; forking in Module 8; PR review in Module 8 | Module 2 |
| Vercel (Hobby) | Deploying every project | Module 2 |
| Supabase (Free) | Ogham memory, VibeMail, Waypoint, Mars Colony | Module 2 |
| Google Cloud | Gmail API, Pub/Sub, and the OAuth client for VibeMail | Module 4 |
| Postman | Exercising the VibeMail API | Module 4 |
| Jamendo developer account | The `client_id` for ZTM Music Player V2 | Module 8 |
| Resend | Contact-form delivery for the portfolio | Module 9 |
| Google AI Studio | A free-tier Gemini API key (no payment method) | Module 10 |
| NASA API (`api.nasa.gov`) | The Cosmos key. Name and email only, and the key arrives by email | Module 11 |

No key needed: OpenFreeMap tiles (Module 7), WhereTheISS.at (Module 11), Google Fonts (Module 9), and the `gte-small` embedding model inside Supabase Edge Functions (Module 10).

---

## Local Installs

| Install | Command or source | First needed |
|---|---|---|
| Node.js (LTS) and npm | nodejs.org | Module 2 |
| git | git-scm.com, or your OS package manager | Module 2 |
| Claude Code CLI | `npm install -g @anthropic-ai/claude-code` | Module 2 |
| Vercel CLI | `npm install -g vercel` | Module 2 |
| uv (for `uvx`) | docs.astral.sh/uv. Runs the Ogham setup wizard | Module 2 |
| Ollama | ollama.com. Ogham's setup requires a working connection to it | Module 2 |
| Claude Code desktop app | claude.ai/download | Module 3 |
| Supabase CLI | `npm install -g supabase` | Module 4 |
| Cursor 3.7 or later | cursor.com | Module 6 |
| ChatGPT desktop app | chatgpt.com | Module 11 |
| Codex CLI | `npm install -g @openai/codex` | Module 11 |
| Antigravity 2.0 and its editor extension | antigravity.google | Module 13 |
| Google Chrome, set as your default browser, plus the Antigravity extension | google.com/chrome | Module 13 |
| gltf-transform | `npx @gltf-transform/cli` (run on demand) | Module 14 |

MCP servers are added from inside each lesson:

```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp     # Module 2
codex mcp add playwright -- npx @playwright/mcp@latest       # Module 11
```

---

## Module by Module

| Module | Paid plan | Accounts and keys | Installs | Bring with you |
|---|---|---|---|---|
| 1 — Claude: Planning System | Claude | — | A browser | A vibe sentence |
| 2 — Claude Code: Terminal | Claude | GitHub, Vercel, Supabase (for Ogham memory) | Node, git, Claude Code CLI, Vercel CLI, uv, Ollama | — |
| 3 — Claude Code: Desktop | Claude | GitHub | Claude Code desktop app | — |
| 4 — VibeMail Backend | Claude | Supabase, Vercel, Google Cloud (Gmail API + Pub/Sub, OAuth client in Testing mode), a Gmail account, Postman | Supabase CLI | — |
| 5 — VibeMail UI | Claude (with Claude Design) | Vercel | — | The deployed VibeMail backend |
| 6 — Cursor Fundamentals | Cursor | GitHub | Cursor | Any small project that runs |
| 7 — Waypoint | Cursor | Supabase (PostGIS enabled), Vercel | — | — |
| 8 — ZTM Music Player V2 | Cursor (Bugbot for the PR review) | GitHub, Jamendo `client_id`, Vercel | — The original ZTM Music Player: fork it, or push your own copy to a new repo |
| 9 — Cinematic Portfolio | Claude | Resend, Vercel | — | Real bio copy, three real projects with screenshots and live URLs, and a contact address |
| 10 — Semantic Memory | Claude | Supabase (Edge Functions), Google AI Studio (Gemini key), Vercel | — | The working VibeMail app from Modules 4–5 |
| 11 — Codex: Cosmos | ChatGPT (a plan with at least two model tiers) | NASA API key, Vercel | ChatGPT desktop app, Codex CLI, Playwright MCP | — |
| 12 — Codex: Bonus Projects | ChatGPT | — | — | Optionally, the Cosmos repository |
| 13 — Antigravity: Mars Colony | Google AI Pro; ChatGPT for the Astra lesson | Supabase (shared with Waypoint), GitHub, Vercel | Antigravity, its editor extension, Chrome | The Waypoint Supabase project |
| 14 — 3D Portfolio World | Claude; ChatGPT for the Astra comparison | Vercel | gltf-transform | The vibe sentence and screenshot from Module 1; `CONTENT.md` from Module 9; a real mid-range phone for testing |
| 15 — The Debrief | — | — | — | — |

---

## Things That Carry Forward

Don't delete these when a module ends.

| Keep | Made in | Used again in |
|---|---|---|
| Your vibe sentence, its generated HTML, and a screenshot | Module 1 | Module 14, where the same sentence becomes a walkable world |
| The VibeMail backend and its Supabase project | Module 4 | Module 5 (UI) and Module 10 (memory layer) |
| The VibeMail frontend | Module 5 | Module 10 |
| `CONTENT.md` and your project screenshots | Module 9 | Module 14 |
| The Waypoint Supabase project | Module 7 | Module 13, where the Mars Colony shares it |
| The Mars Colony repository | Module 13 | The ChatGPT Astra lesson at the end of Module 13 |

---

## Supabase Project Budget

The Supabase free plan allows **two active projects**. Free projects pause after a week of inactivity, and paused projects don't count.

| Project | Holds | Needs to be active in |
|---|---|---|
| Ogham | Cross-session memory for Claude Code | Whenever you use Ogham |
| VibeMail | The email app's data and its memory layer | Modules 4, 5, 10 |
| Waypoint | Waypoint, then the Mars Colony (with `waypoint_` and `marscolony_` table prefixes) | Modules 7, 13 |

That's three projects against a limit of two, so plan when each one is paused. The usual point is Module 7: pause one you aren't using before creating Waypoint. The same limit is why Module 13 shares the Waypoint database rather than creating a fourth project.

---

## Keys and Secrets

- **Every key goes in `.env.local` (or your platform's environment settings), never in the repository.** Commit an `.env.example` with the names and empty values.
- **Server-only keys never get a public prefix.** Don't put `NEXT_PUBLIC_` or `VITE_` on a secret. Those prefixes compile the value into the browser bundle.
- **Public identifiers are the exception.** The Jamendo `client_id` is read-only and public, so `VITE_JAMENDO_CLIENT_ID` is acceptable.
- **Never commit downloaded credentials.** That includes the Google Cloud OAuth JSON in Module 4.
- **Set production variables in Vercel before deploying,** and confirm the app checks them at startup.
