# Terminology
### Course Vocabulary, A to Z

Short definitions of the terms the course uses, with the module where each one is introduced or explained in most depth. For tool-specific file names, see [Tool Equivalents](TOOL_EQUIVALENTS.md). For the documents, see [Planning Documents](PLANNING_DOCUMENTS.md).

---

**Agentic Engineering** — Directing the system that builds software (planning documents, agents, verification) rather than directing each line of output. The opposite of vibe-coding your way to code you can't maintain. *(Module 1)*

**Anchor file** — The short file a tool reads at the start of every session: `CLAUDE.md`, `AGENTS.md`, or the tool's equivalent. It points to the other documents rather than containing them. *(Modules 1, 12)*

**Artifact (Antigravity)** — A plan, task list, diff, screenshot, or recording that the agent produces as evidence. You comment on it before the agent continues. *(Module 13)*

**Atomic unit** — The smallest meaningful change that can be built, verified, understood, and committed on its own before the next one starts. *(Modules 1, 3)*

**Auto-review** — Cursor's recommended run mode. Known-safe calls run, riskier commands run sandboxed, and anything else goes to a classifier that allows it, redirects it, or asks you. Not a security boundary. *(Module 6)*

**Brownfield / greenfield** — Brownfield means changing an existing codebase you didn't write (ZTM Music Player V2). Greenfield means starting from nothing (Waypoint). Brownfield work starts with a survey and Plan Mode. *(Modules 7, 8)*

**Bugbot** — Cursor's automated code review, which runs on every pull request: the outer loop. *(Modules 6, 8)*

**ChatGPT Astra** — OpenAI's tool for building 3D work from a prompt. It models objects procedurally in code, with no downloaded assets. *(Modules 12–14)*

**Catch-up** — Applying many simulation ticks in one batch, for example after the page was closed, so a long run takes seconds to compute. Only safe if the simulation is deterministic. *(Module 13)*

**Chunk** — The unit of storage in a semantic memory layer. The model's input ceiling forces long messages to be split, but results always collapse back to one row per message. *(Module 10)*

**CLI first, MCP when it earns it** — A connected MCP server costs context on every turn. A known CLI costs context only when it's run. Prefer the CLI unless the server adds something the CLI can't. *(Module 6)*

**Composer** — Cursor's own model, cheap enough per token to make thoroughness affordable. Use it for well-specified execution, and a frontier model for hard reasoning. *(Module 6)*

**Content model** — A list of what exists on each section of a page, with constraints for each field, written before anything about how it looks. *(Module 1)*

**Context7** — An MCP server (or CLI) that fetches current library documentation, so the agent doesn't rely on out-of-date training data. *(Modules 2, 6)*

**Contract** — `CONTRACT.md`. A statement of what done means, precise enough that two agents could build the same thing independently. It's the integration point for parallel work. *(Modules 3, 4)*

**Definition of verified** — The one-sentence check for each unit in `BUILD_SEQUENCE.md`, saying what you'd confirm before moving on. *(Module 4)*

**Designed ambiguity** — Different causes that look identical in the output: signal loss, a rate limit, stale data, and an outage can all return the same empty response. The contract names each cause, how to detect it, and how to treat it, before the build. *(Module 11)*

**Determinism** — Applying N ticks in one batch gives exactly the same result as applying N single ticks. That's what lets offline catch-up, browser prediction, and testing agree. *(Module 13)*

**Director** — You. You make the decisions, and the agent does the execution. *(Module 1)*

**Director's Audit** — Judging the output against its written spec, not against whether it looks plausible or runs. Then you ship it or send it back, and record the result in an audit log. *(Module 1, and the end of every project)*

**Embedding** — Text turned into a position in space, so similarity becomes distance and "find what's related" becomes "find the nearest neighbours." *(Module 10)*

**Frame budget** — The rendering time a 3D scene is allowed each frame, measured on a real mid-range phone after thermal throttling, not on your development machine. *(Modules 9, 14)*

**Golden Path** — The course's default stack, React, Next.js, and Supabase, unless a lesson says otherwise. *(README)*

**Honest degradation** — When a dependency runs out, such as a free-tier quota, the feature loses the part that depends on it and says so, rather than failing outright or pretending. *(Module 10)*

**Inner loop / outer loop** — The inner loop is the local checks you run all day: browser, `/review`, tests. The outer loop is cloud review at the pull request, which becomes part of the record. *(Modules 6, 8)*

**MCP (Model Context Protocol)** — The standard way to connect an agent to an outside tool or data source: a browser, documentation, a database. *(Module 2)*

**Method delta** — What a prompt adds on top of your documents. Prompts in these guides are often only the delta. Without your PRD, contract, or brief behind them, they won't produce your work. *(README)*

**Ogham** — An MCP server that gives Claude Code durable memory across sessions and tools. *(Module 2)*

**pgvector** — The Postgres extension that stores embeddings and runs nearest-neighbour search. Used with Supabase for VibeMail's memory layer. *(Module 10)*

**Plan Mode** — Having the agent research, ask questions, and produce an editable plan for approval before it writes any code. Use it when the parts interlock, and a direct prompt when the work is linear. *(Modules 2, 8, 14)*

**PostGIS** — The Postgres extension for location data. Storing location as `geography(Point, 4326)` is what makes Waypoint's nearby and on-screen queries clean. *(Module 7)*

**PRD (Product Requirements Document)** — What the product is, who it's for, what it must have, and how you'll know it succeeded. *(Module 1)*

**Pure tick function** — The simulation step as a function with no side effects: the same state in always gives the same state out. It can run on the server for real and in the browser as a prediction. *(Module 13)*

**Remote Control** — Driving a local agent session from another device, such as your phone. *(Module 3)*

**Roles (walkable world)** — The four jobs every world must fill: a showcase surface, a bio station, a contact station, and surroundings. Your vibe sentence decides what objects fill them. *(Module 14)*

**Routing** — Writing model choice into the repository: named agents, each pinned to a model tier and sandbox, so the expensive model doesn't do the cheap parts. *(Module 11)*

**RLS (Row-Level Security)** — Postgres policies that limit each user to their own rows. A connection made with the service role key bypasses them. *(Modules 4, 10, 13)*

**Service role key** — Supabase's admin key. It bypasses RLS, so server code using it must scope every query by owner itself. It must never reach the browser. *(Modules 4, 10)*

**Site contract** — Page structure, content inventory, and behaviour spec for a static site. The Module 1 forerunner of `CONTRACT.md`. *(Module 1)*

**Standing orders** — Instructions that apply to every session, kept in the anchor file and rules rather than repeated in prompts. *(Module 1)*

**Subagent** — A separate agent spawned for one piece of a task, working in its own context and reporting back. *(Modules 5, 6, 11)*

**The three standards** — The lens for judging any output: does it scale, is it secure, and do you know exactly why it works? *(Module 1)*

**Vibe sentence** — The one-sentence description of atmosphere that starts Module 1 and returns as a walkable world in Module 14. *(Modules 1, 14)*

**Worktree** — A separate git working folder on its own branch, so parallel agents don't share uncommitted files. *(Modules 3, 4, 6)*
