# Planning Documents
### Every Document in the Course, What It's For, and a Blank Template

The course's documents are introduced one module at a time. This page puts them all in one place, with a blank starting template for each, so you can reuse them on your own projects.

_The templates are starting shapes, not forms to fill in mechanically. In the course, an agent drafts most of these from your answers, and you direct the result. Each module's guide has the exact prompt._

---

## Table of Contents

1. [The Rules for Every Document](#the-rules-for-every-document)
2. [At a Glance](#at-a-glance)
3. [Which Projects Use Which](#which-projects-use-which)
4. [Product and Content](#product-and-content) — vibe sentence, PRD, content model, `CONTENT.md`
5. [Contracts](#contracts) — site contract, `CONTRACT.md`, `MEMORY_CONTRACT.md`
6. [Order and Verification](#order-and-verification) — `BUILD_SEQUENCE.md`, `TEST_PLAN.md`, `PLAYTEST_PLAN.md`
7. [Look and Feel](#look-and-feel) — design standard, `DESIGN.md`, `MOTION_BRIEF.md`, `WORLD_BRIEF.md`
8. [Standing Orders](#standing-orders) — the anchor file, `DATABASE.md`
9. [The Audit Log](#the-audit-log)

---

## The Rules for Every Document

- **Commit it before the code.** A document written after the build only describes what happened.
- **Fix the document, not the chat.** If the agent got something wrong because the document left room for it, change the document. A correction in chat is forgotten next session.
- **Real numbers, not adjectives.** "Fog starts at 8 m" can be audited. "Moody fog" can't.
- **Decisions go in documents; execution goes to the agent.** A column type, a failure state, or a palette is a decision. The SQL, the component, and the shader are execution.
- **Two people should be able to build the same thing from it.** If they couldn't, it isn't specific enough yet.
- **One home per fact.** Point from one document to another rather than copying, so nothing drifts out of sync.

---

## At a Glance

| Document | Answers | First appears |
|---|---|---|
| Vibe sentence | What should this feel like? | Module 1 |
| PRD | What is it, who is it for, and what does success look like? | Module 1 |
| Content model | What exists, before how it looks? | Module 1 |
| Site contract | What's on the page, and how does it behave? | Module 1 |
| Design standard / `DESIGN.md` | What does it look like, in tokens and rules? | Module 1, then Module 5 onwards |
| Anchor file (`CLAUDE.md` / `AGENTS.md`) | What's always true about this project? | Module 1 |
| `CONTRACT.md` | What does done mean, precisely enough for two agents to build it independently? | Module 4 |
| `BUILD_SEQUENCE.md` | In what order, and how is each step verified? | Module 4 |
| `TEST_PLAN.md` | What gets checked, and what does passing look like? | Module 7 |
| `MOTION_BRIEF.md` | What moves, why, and what deliberately doesn't? | Module 9 |
| `CONTENT.md` | What's the real copy? | Module 9 |
| `MEMORY_CONTRACT.md` | What gets embedded, and how is retrieval scoped? | Module 10 |
| `DATABASE.md` | Which tables can this project touch? | Module 13 |
| `PLAYTEST_PLAN.md` | How does the system behave over time? | Module 13 |
| `WORLD_BRIEF.md` | What does the world look like, in numbers? | Module 14 |
| Audit log | Did the output match the spec, and what was fixed? | Module 1 |

---

## Which Projects Use Which

| Project | Documents |
|---|---|
| Vibe site (Module 1) | PRD, content model, site contract, design standard, `CLAUDE.md`, audit log |
| VibeMail (Modules 4–5, 10) | `CONTRACT.md`, `BUILD_SEQUENCE.md`, `CLAUDE.md`, `DESIGN.md`, `MEMORY_CONTRACT.md`, `TEST_PLAN.md` |
| Waypoint (Module 7) | PRD, `CONTRACT.md`, `TEST_PLAN.md`, `DESIGN.md`, `AGENTS.md` |
| ZTM Music Player V2 (Module 8) | `CONTRACT.md`, `AGENTS.md`, `DESIGN.md`, `TEST_PLAN.md` |
| Cinematic portfolio (Module 9) | `CONTENT.md`, `MOTION_BRIEF.md`, `CLAUDE.md` |
| Cosmos (Module 11) | `AGENTS.md`, `CONTRACT.md`, `DESIGN.md`, `PLAN.md` |
| Mars Colony (Module 13) | `CONTRACT.md`, `AGENTS.md`, `DESIGN.md`, `DATABASE.md`, `PLAYTEST_PLAN.md` |
| Walkable world (Module 14) | `WORLD_BRIEF.md`, `CONTENT.md`, `CLAUDE.md` (provided) |

---

## Product and Content

### Vibe sentence

One sentence describing the atmosphere you want. It follows the project from Module 1 to Module 14.

```text
[A place, a time, a mood, and one sensory detail — in one sentence.]
```

Save the sentence, the page it generated, and a screenshot.

### PRD

**Job:** what the product is, who it's for, and what success looks like.

```markdown
# PRD — [Product]

## Vision
[One paragraph: what this is and why it exists.]

## User Personas
- [Who] — [what they need from it]

## Feature List
### Must-Have
- [Feature]
### Should-Have
- [Feature]
### Nice-to-Have
- [Feature]

## Technical Constraints
- [Stack, hosting, data, and anything ruled out]

## Design Standard Summary
[Two or three sentences pointing at the design standard.]

## Success Criteria
- [Something you can check, not a feeling]
```

**Director's test:** Is every Must-Have necessary? Could you tell from the success criteria whether you'd shipped?

### Content model

**Job:** what exists, section by section, before anything about how it looks.

```markdown
## [Section name]
- [Field] — [length, format, or tone constraint]
- [Field] — [constraint]
```

**Director's test:** Does every Must-Have appear? Does every field have a constraint? Is any section unjustified by the PRD?

### `CONTENT.md`

**Job:** the real copy, so the agent never invents any. It's written by you, not drafted by the agent.

```markdown
# Content — [Site]

## About
[Real bio copy. Intended audience: …]

## Projects
### [Project name]
- Category / year:
- One-line description:
- Stack:
- Live URL:
- Screenshot: [path]

## Contact
- [Channel]: [address or link]
```

**Rule:** If something's missing, the agent stops and asks. It never fills in placeholders.

---

## Contracts

### Site contract

**Job:** page structure plus how every important interaction behaves.

```markdown
## [Section]
Purpose: [one sentence]

Content:
- [field] — [type; constraint]

Behaviour:
- [navigation, scroll, hover, mobile, and transition rules]
```

### `CONTRACT.md`

**Job:** what done means, precisely enough that two agents could build it independently. Drafted by an agent that asks questions one at a time until it's 95% confident.

```markdown
# Contract — [Project]

## Acceptance criteria
- [Checkable statement of done]

## Data model
- [Entity]: [fields, types, ownership]

## Interfaces
### [Endpoint, panel, or action]
- Input:
- Output:
- Errors: [each named cause, how it's detected, how it's treated]

## Rules
- [Behaviour that must always hold]

## Out of scope
- [What this project deliberately doesn't do]
```

**Worth naming explicitly:**
- Every failure state, separately (designed ambiguity, Module 11)
- Who owns each row
- Every number the system uses, such as rates, limits, and grid sizes (Module 13)

### `MEMORY_CONTRACT.md`

**Job:** the decisions behind a semantic memory layer (Module 10).

```markdown
# Memory Contract

## What gets embedded
[Base unit vs storage unit, and why — e.g. message vs chunk.]

## Chunks in, messages out
[How results collapse to one row per item, and how each is scored.]

## How retrieval is scoped
[Ownership passed as an argument — never trusted to RLS on a service-role connection.]

## When embeddings are refreshed
[Which edits re-embed, which don't.]

## Threshold
[The measured value, and the score ranges it was chosen from.]

## Degradation
[What each feature does when the reasoning quota runs out.]
```

---

## Order and Verification

### `BUILD_SEQUENCE.md`

**Job:** the atomic order. Each unit comes with one sentence saying what verified looks like.

```markdown
# Build Sequence

1. [Unit] — [what you'd check to confirm it before moving on]
2. [Unit] — [check]
3. [Unit] — [check]
```

**Rule:** Build, verify, understand, and commit each unit before starting the next.

### `TEST_PLAN.md`

**Job:** what gets checked, and what passing looks like. Write it before the code, from the contract.

```markdown
# [Project] Test Plan

## [Area]
- [Scenario]: [setup] → [expected result]
- Edge: [empty / zero / expired / over-limit case] → [expected result]

## Automated
- [What runs without you, and the command]

## Director's review
- [What only a human judges: is it good, not just correct]
```

**Rule:** If something other than you can't run it, it isn't a check yet.

### `PLAYTEST_PLAN.md`

**Job:** behaviour over time, for anything with its own clock (Module 13).

```markdown
## Scenario [n]: [name]
Given: [starting state, with numbers]
When:  [actions, or N ticks with no action]
Then:  - [assertion about the trajectory]
       - [assertion at the end]
```

Include negative cases: actions that must be refused, and states that must never happen.

---

## Look and Feel

### Design standard / `DESIGN.md`

**Job:** the visual specification, as tokens plus the reasoning behind them. From Module 11 on, it uses the Google DESIGN.md format: machine-readable tokens in YAML frontmatter, with reasoning in prose below.

```markdown
---
colors:
  background: "#______"
  foreground: "#______"
  accent: "#______"
typography:
  display: "[family], [weights]"
  body: "[family], [weights]"
spacing:
  base: [n]px
---

# [Project] Design System

## Overview
[The sensibility, in two or three sentences.]

## Colour
[Usage rules for each token.]

## Typography
[Scale, line height, tracking.]

## Space and texture
[Spacing, depth, restraint.]

## Motion
[Durations, easing, hover, reduced motion.]

## Accessibility floor
[Contrast, focus, legibility.]

## Do / Don't
- DO …
- DON'T …
```

**Rule:** The image is the compass and the written spec is the law. Pull concrete tokens out of a reference image so the build can be audited against them. When the standard has to change, add a dated section that supersedes the old rule (see the 3D edition in Module 13).

### `MOTION_BRIEF.md`

**Job:** what moves, why, and what deliberately doesn't (Module 9). It records outcomes, not implementation values.

```markdown
# Motion Brief — [Name]

## Look
Feel: [two or three sentences]
Palette: [exact hex values]
Type: [display + body]
Space: [dense or airy; corners]

## Motion direction
[The shared rule: slow and weighted, or crisp and quick.]

## [Section]
- Treatment: [or still, by decision]
- Why: [reason]
  -> Transition into [next]: [treatment or deliberately nothing]

## Excluded
- [Effect considered and cut, with reason]
```

### `WORLD_BRIEF.md`

**Job:** a 3D world's mood turned into confirmed numbers (Module 14).

```markdown
# World Brief — [Name]

## Atmosphere
[Fog colour and distances, sky, time of day — numbers.]

## Scale and layout
[Walkable bounds, street length, eye height, walk speed.]

## Materials and light
[Ground, emissive values, fill light, post-processing.]

## Content objects
- Showcase surface: [object, position]
- Bio station: [object, position]
- Contact station: [object, position]
- Surroundings: [atmosphere only, clickable nowhere]

## Navigation
[Controls on desktop and touch; the guided path.]

## Frame budget
[Target on a real mid-range phone; the turn-down order.]
```

---

## Standing Orders

### The anchor file

**Job:** short and read every session. It points to the other documents rather than containing them. The name depends on the tool. See [Tool Equivalents](TOOL_EQUIVALENTS.md).

```markdown
# [Project]

## Identity
- [What this is, in one line]

## Stack
- [Framework, database, hosting]

## Conventions
- [Where things live, naming, state handling]
- Source of truth: CONTRACT.md, DESIGN.md, TEST_PLAN.md

## Never do
- [A specific forbidden shortcut, not a general principle]
```

**Rules (from Module 12):** exactly these four sections, bullets only, and no section over eight lines.

### `DATABASE.md`

**Job:** which tables this project may touch, for a database shared between applications (Module 13). It's written by hand.

```markdown
# Database law

## Namespacing
- This project owns tables prefixed `[prefix]_` and nothing else.
- Tables prefixed `[other]_` belong to another application. Never read, write, alter, or reference them.

## Access
- Every `[prefix]_` table has an owner column and row-level security scoped to the current user.
- Application code uses the session's credentials, not the service role key.

## Migrations
- Migration files only touch `[prefix]_` tables.
- Never run a project-wide command (reset, pull, or equivalent).
- Never drop a table. Propose the change and stop.
```

**Rule:** Permissions decide whether a command may run. This document decides which tables it may touch. Treat it as a request, not a wall, and back up first.

---

## The Audit Log

**Job:** the record of the Director's Audit, judging the output against the spec rather than against "it runs."

```markdown
# Director's Audit Log

## [Project] — [date]
Auditor: Director + Reviewer Agent

### Structural
[Finding and resolution — or: Clean]

### Consistency
[Finding and resolution — or: Clean]

### Visual quality
[Finding and resolution — or: Clean]

### Reviewer agent findings
[Anything caught that wasn't in your own review]

### Status
[Verified against: which documents]
```
