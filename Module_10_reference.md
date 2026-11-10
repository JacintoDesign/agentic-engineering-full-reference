# Module 10 Reference Guide
### Semantic Memory — Add AI Features to VibeMail: pgvector, Free-Tier Reasoning, and Honest Degradation

Use this guide alongside the Module 10 videos.

_Gemini's free-tier limits and Flash model names change without notice, and quota varies by project and account standing. Nothing in this module hardcodes a model string — it is read from `GEMINI_MODEL` — so confirm the current model ID and limits in AI Studio._

**This module costs nothing to build or run.** The embedding side has no API key at all. The reasoning side runs on a free tier that requires no payment method, so no account in this module can produce a surprise bill.

---

## Table of Contents

1. [Introducing the Memory Layer for the VibeMail Project](#introducing-the-memory-layer-for-the-vibemail-project)
2. [Embeddings, Similarity and pgvector](#embeddings-similarity-and-pgvector)
3. [Producing the MEMORY_CONTRACT.md Document](#producing-the-memory_contractmd-document)
4. [Creating Migration Script for the VibeMail Project](#creating-migration-script-for-the-vibemail-project)
5. [Deploy an Edge Function for the Chunking and Embedding](#deploy-an-edge-function-for-the-chunking-and-embedding)
6. [Creating a Helper Function for Backfilling Previous Emails](#creating-a-helper-function-for-backfilling-previous-emails)
7. [Creating the match_messages Database Function for Scoped Retrieval](#creating-the-match_messages-database-function-for-scoped-retrieval)
8. [Reading Messages with Gemini Flash Free Tier on Google AI Studio](#reading-messages-with-gemini-flash-free-tier-on-google-ai-studio)
9. [Improving the Search Bar Functionality to Cross Search](#improving-the-search-bar-functionality-to-cross-search)
10. [Combining Retrieval and Reasoning to Create Topic Summaries](#combining-retrieval-and-reasoning-to-create-topic-summaries)
11. [Creating a TEST_PLAN.md for Verification and Tuning](#creating-a-test_planmd-for-verification-and-tuning)
12. [Running a Director's Audit of the Memory Layer and Deploy](#running-a-directors-audit-of-the-memory-layer-and-deploy)

---

## Core Concepts

**Most apps store what happened; a memory layer stores what it meant.** An embedding turns text into a position in space, so similarity becomes distance and "find what's related" becomes "find nearest neighbours."

**Capture and reasoning are separate jobs.** Embedding runs inside a Supabase Edge Function with no key and no per-token charge. A language model only reads the handful of candidates retrieval already narrowed.

**The model's input ceiling shapes the data model.** `gte-small` reads roughly two thousand characters, so the base unit stays the message while the storage unit becomes the chunk.

**Chunks in, messages out.** Search runs over chunks but always collapses to one row per message, scored by its best chunk. A message appearing twice is a bug.

**Ownership is an argument, not a policy.** The server connects with the service-role key, so RLS is bypassed and `auth.uid()` is empty. Retrieval that relies on RLS here returns everyone's mail.

**A stale embedding doesn't throw.** It confidently returns the wrong thing. Re-embed on subject or body changes, skip label and read-status changes, and rebuild rather than update in place.

**Quota exhaustion is an operating condition.** `reason()` returns a result with an availability flag instead of throwing, so features lose a summary rather than the feature.

**Measure, then change.** Log raw scores, read the gap between related and unrelated ranges, then set the threshold and record why.

---

## Introducing the Memory Layer for the VibeMail Project

### The worked example

| Newsletter | Role |
|---|---|
| **Build Log** — "This week: agents, agents, agents" | A multi-story roundup with one detail — a new framework's rate limits — buried in paragraph four |
| **Frontier Weekly** | Covers the same launch from a different angle |
| **Signal & Noise** | Covers the same launch from a third angle |

### What VibeMail will do by the end

- Find the right issue by intent, even when the query shares no words with it.
- Show who else covered the story you're reading.
- Synthesise scattered mentions of a topic into one brief.

### The two halves and their costs

| Side | Model | Where it runs | Cost | Limits |
|---|---|---|---|---|
| Capture | `gte-small`, 384 dimensions | Inside a Supabase Edge Function | Edge CPU time only | No key, no quota, no per-token charge |
| Reasoning | Gemini Flash, from `GEMINI_MODEL` | Vercel function via `src/reason/` | Free tier, no payment method | Requests and tokens per minute and per day |

Exceeding the free quota returns a rate-limit response, not an invoice.

_The cheapest architecture isn't the one with the smallest bill. It's the one with no billing relationship at all._

---

## Embeddings, Similarity and pgvector

### Mental model

- An embedding model reads text and returns a vector — 384 numbers for `gte-small` — placing the text at a point in 384-dimensional space.
- Related meaning lands in the same neighbourhood; the model learned this from vast amounts of text.
- **Cosine similarity** compares direction, not length, so a two-line mention and a six-paragraph deep dive still match.
- Embed the query the same way as the emails, and the nearest vectors are the most relevant results.

### What pgvector adds

| Piece | Job |
|---|---|
| `vector(n)` column | Stores embeddings in Postgres |
| Distance operators | Measure closeness in SQL |
| HNSW index | A navigable graph that jumps to the right neighbourhood instead of scanning every row |

No separate vector service and no second bill — the Postgres you already run becomes the memory store.

### The constraint that shapes everything

Every embedding model has a maximum input length. Exceed it and it doesn't fail — it silently reads the beginning and ignores the rest. `gte-small` accepts roughly two thousand characters, and a five-story roundup is usually longer.

---

## Producing the MEMORY_CONTRACT.md Document

Four judgment calls, one document, no code until it is committed.

### What gets embedded

> "Create MEMORY_CONTRACT.md at the repo root. Start with a section called 'What gets embedded'. Write down that the base unit is the message but the storage unit is a chunk, because the embedding model can only read about two thousand characters at a time. Every chunk is embedded as the sender and subject on their own lines, then a blank line, then the chunk text — so the header is repeated on every chunk and no chunk is ever context-free. Bodies are split into roughly fifteen-hundred-character chunks with about two hundred characters of overlap so a sentence spanning a boundary isn't lost. Note we're counting characters, not tokens. Note the model is gte-small at 384 dimensions, English-only, and the only model this platform runs natively — swapping it later means self-hosting."

### Chunks in, messages out

> "Add a short section called 'Chunks in, messages out'. Search operates over chunks, but results are always collapsed to one row per message, and a message's score is its best — lowest distance — chunk. The user must never see chunk-level results."

### How retrieval is scoped

> "Add a section called 'How retrieval is scoped'. Three rules. Ownership: retrieval only ever returns the current user's mail, enforced by a row-level security policy on user_id equals auth.uid, not in application code. Recency: between two messages of similar relevance, the more recent ranks higher — a gentle down-weight on older matches, not a hard re-sort by date. Limits: return at most eight messages after collapsing chunks, and discard anything below a similarity threshold. Mark the result count and the threshold as starting values, with the exact threshold number left to be set once we know how we're scoring similarity, in the next lesson."

_The ownership rule is revised in Scoped Retrieval: because the server uses the service-role key, ownership becomes an explicit `user_id` argument, with RLS kept only as a backstop._

### When embeddings are invalidated

> "Add a section called 'When embeddings are invalidated'. On insert, a message is chunked and embedded before it can be retrieved. On an edit to the subject or body, delete all that message's chunks and rebuild them from scratch — not an update in place, because the new body may split into a different number of chunks. On delete, the chunks go with the message in the same transaction. Label changes and read-status flips do not trigger re-embedding, because they don't change what the message means."

### Degradation

> "Add a final section called 'Degradation'. Two cases. Cold start: because VibeMail already holds the user's mail, we solve day one with a resumable backfill that chunks and embeds the entire existing inbox up front — and while the index is still thin, features fall back to keyword search and never present a below-threshold match as a confident answer. Quota exhaustion: the reasoning provider runs on a free tier with rate limits, so any feature that calls it must still work without it — showing retrieved results without a summary rather than showing an error."

```bash
git add MEMORY_CONTRACT.md
git commit -m "spec: memory contract"
```

### Final contract decisions

| Decision | Recorded value |
|---|---|
| Base unit | The message |
| Storage unit | ~1,500-character chunks, ~200 characters overlap, counted in characters |
| Chunk composition | Sender and subject on their own lines, blank line, chunk text |
| Model | `gte-small`, 384 dimensions, English-only |
| Similarity operator | Inner product `<#>` on normalised vectors, matched between index and query |
| Ownership | Explicit `user_id` argument from the verified JWT, filtered in the function |
| Recency | Gentle down-weight on older matches; never a hard re-sort |
| Limits | At most 8 messages after collapsing; below-threshold matches discarded |
| Invalidation | Insert embeds; subject/body edit deletes and rebuilds; delete cascades; labels and read status do nothing |
| Degradation | Cold start → keyword search; quota exhaustion → results without a summary |

---

## Creating Migration Script for the VibeMail Project

A schema change to a table other features depend on is what Plan Mode is for. Switch into Plan Mode before dictating.

### Migration plan prompt

> "Read MEMORY_CONTRACT.md, then plan a migration for the memory layer. It needs to enable the pgvector extension, and create a table called message_chunks with: an id, a message_id referencing messages with cascade on delete, a user_id referencing the auth users table, a chunk index, the chunk text, an embedding column, and a created timestamp. The embedding column must be a vector of exactly 384 dimensions, matching the gte-small model. Add a unique constraint on message_id and chunk index together. Add an HNSW index on the embedding column using the inner product operator class, not cosine — every vector we store will be normalized to unit length, so inner product ranks results identically to cosine distance while being cheaper for Postgres to compute. Enable row-level security and add a policy restricting all access to rows where user_id equals auth.uid. Make sure the migration is safe to run more than once."

```bash
supabase db push
```

### Self-check prompt

> "Confirm five things against the live database and report only — don't fix anything. One: the vector extension is installed. Two: message_chunks exists and its embedding column is exactly 384 dimensions. Three: an HNSW index exists on the embedding column. Four: that index is built on the inner product operator class, not cosine. Five: row-level security is actually enabled on the table, not merely that a policy exists."

### The negative-score convention

pgvector's `<#>` returns the negative inner product:

| Score | Meaning |
|---|---|
| Close to `-1.0` | Near-perfect match |
| Near `0.0` | Unrelated |

Lower is better, the threshold is a ceiling, and a looser threshold is a higher number. Sorting the wrong direction returns the least relevant mail with total confidence and never errors.

### Schema checks

- A policy without RLS enabled does nothing; RLS enabled with no policy returns zero rows — which looks like broken retrieval later.
- Insert a 383-length vector deliberately and watch it get rejected.
- Column size and model output size are one number written in two places; swapping models means a new column definition and regenerating every vector.
- An index built on `<#>` is silently ignored by a query using `<=>`.

---

## Deploy an Edge Function for the Chunking and Embedding

```bash
supabase functions new embed
```

### Build prompt

> "Build the embed Edge Function. Read MEMORY_CONTRACT.md first and follow its splitting rules exactly. It needs to handle two shapes of request.
>
> If the request body is a plain sender, subject and body, chunk and embed the text per the contract and return an array of chunks, each with its index, its text, and its embedding. Nothing gets written to the database in this mode — it just returns vectors.
>
> If the request body looks like a Supabase Database Webhook payload — a type of INSERT or UPDATE, a table of messages, and a record — chunk and embed that record's sender, subject and body the same way, but then also write the result into message_chunks yourself, using a service-role Supabase client, tagged with the record's user id. If the type is UPDATE, first delete any existing chunks for that message id before inserting the new ones.
>
> Create the inference session once at module scope, not inside the request handler — instantiating it per request costs seconds. Use the gte-small model with mean pooling and normalisation both switched on.
>
> Split the body into roughly fifteen-hundred-character chunks with two hundred characters of overlap, and put the sender and subject at the top of every chunk before embedding it.
>
> Add an explicit guard: if any composed chunk would exceed the model's input limit, fail loudly rather than sending it — the model would otherwise truncate it silently instead of erroring."

| Setting | Why |
|---|---|
| Mean pooling | Compresses token-by-token output into one vector for the whole input |
| Normalisation | Scales to unit length — what makes inner product valid |
| Module-scope session | Per-request instantiation costs seconds |

Never let pooling or normalisation differ between calls, or vectors land in different spaces and stop matching.

```bash
supabase functions serve embed
supabase functions deploy embed
```

### Verification

- A long roundup returns several chunks, roughly one per story boundary.
- Every chunk starts with the sender and subject.
- Consecutive chunks share text at the boundary.
- Every vector is exactly 384 long; the same input twice gives identical vectors.
- A ten-thousand-character body fails loudly rather than truncating.
- Webhook mode with a hand-built `INSERT` payload lands rows under that user id; the same record as `UPDATE` with new body text leaves only the new chunks.
- Cold and warm call times are written down.
- Your longest newsletter — eight or more chunks — returns comfortably inside the 2-second CPU budget. If not, the fix is one chunk per request, not a bigger timeout.

---

## Creating a Helper Function for Backfilling Previous Emails

Embedding is called from application code, not a database trigger. `/embed` takes one piece of text and returns one vector; splitting, embedding, and saving happen in app code so chunks embed in parallel instead of sequentially inside a two-second budget.

### Write paths

| Must call the ingest helper | Must not re-embed |
|---|---|
| Pub/Sub webhook upsert | Mark-as-read |
| Initial sync | Label changes |
| Gmail history backfill | Inbox reconcile |
| Draft create | |
| Draft update | |

### Ingest helper prompt

> "Set up automatic embedding in application code, not a database trigger. Don't enable pg_net, don't add a Vault secret, and don't change the embed edge function — it still takes `{ text }` and returns `{ embedding }`.
>
> Add an ingest module under `src/memory`. Given a message's row id, user id, sender, subject and body, it should: split the body using the existing chunker, call `/embed` once per chunk in parallel with `Promise.all`, delete any chunks already stored for that message id, then insert the new ones. Every row gets that message's `user_id`. Use the service role key that's already in the environment to call `/embed`.
>
> Call this helper from the webhook upsert, initial sync, the Gmail history backfill, draft create, and draft update. Don't call it from label changes, mark-as-read, or inbox reconcile."

### Failure order

1. Save the message row first so it appears in the inbox immediately.
2. Embed.
3. On embedding failure: log it, return success to Pub/Sub, and don't advance the Gmail history bookmark — the next notification retries the range.

Because the helper deletes old chunks first, running it twice is harmless.

### Embedding backfill prompt

> "Write an embedding backfill as a Node script that runs on my machine — not an Edge Function and not a Vercel Function. Edge Functions stop after 150 seconds on the free tier, and looping over a whole inbox in one request will hit that long before it finishes. Vercel Functions have the same kind of limit.
>
> Find every message with no rows in `message_chunks`, run the same ingest helper the live code uses (sender, subject, `body_plain`), and save the chunks in batches. Since it only picks messages that have no chunks, I should be able to stop it halfway and start it again without losing my place. Log progress after each batch.
>
> This script must only ever call the embed function — never the reasoning provider.
>
> One more thing: it uses the service role key, which skips row-level security. So it has to set `user_id` explicitly from each message's owner."

- The embed-only rule is the module's only real money risk: an unattended loop pointed at the wrong model.
- Mislabelled `user_id` isn't caught by RLS or the cross-user test. Check the ids by hand after the first run.
- Don't confuse this with `POST /api/v1/sync/backfill`, which pulls older mail from Gmail.

### Change detection prompt

> "After messages are saved, only embed the ones whose subject or `body_plain` actually changed — not every row that got written. Before the upsert, look up the stored subject and body by `gmail_id`, and skip embedding when both match. Do this comparison in the ingest layer, not inside the embed function, and leave the embed function as it is. Don't re-embed on label changes, mark-as-read, or inbox reconcile. Draft create and draft update should always rebuild."

A Pub/Sub update rewrites the whole row, so "this was an update" says nothing about whether the words changed. Delete needs no code — `ON DELETE CASCADE` handles it.

### Verification

Check `message_chunks` directly; `/api/v1/messages/search` is still keyword search over `messages`.

- A new email has chunks as soon as the webhook finishes.
- Editing a draft's body replaces its chunks; the count may differ.
- Old-body phrases are gone; new text is present with sender and subject on every chunk.
- Deleting a message leaves no chunks.
- Mark-read and label changes leave chunk timestamps unmoved.
- After backfill: roughly 1.5–3× as many chunks as messages, no message without chunks, every chunk's `user_id` matching its message's owner.
- Kill the backfill halfway and restart it — it resumes rather than duplicating.

---

## Creating the match_messages Database Function for Scoped Retrieval

Retrieval lives in the database because the HNSW index only works inside Postgres, and one function can search chunks and return whole messages in one round trip.

### match_messages prompt

> "Write a Postgres function called match_messages. It takes a user id, a query embedding, a similarity threshold, and a maximum number of results (default 8).
>
> It searches message_chunks with the inner product operator `<#>` against the query embedding — not cosine. Every vector is stored normalized, so inner product ranks the same and costs less to compute.
>
> Filter by the user id parameter. Then group by message id, keeping each message's best (lowest) chunk score as its score. Drop any message whose best score is worse than the threshold. Join back to messages and return at most the requested number of rows: the message columns plus its score.
>
> When two messages score about the same, rank the more recent one higher. A gentle nudge for age — not a hard sort by date. Relevance leads, recency breaks ties.
>
> Do not use SECURITY DEFINER, and do not use auth.uid() or rely on row-level security for scoping. This server connects with the service role key, so RLS does not apply. Ownership comes only from the user id parameter.
>
> Put it in a migration, not the Supabase dashboard, so it's versioned."

```bash
npm run db:types
```

Regenerate types so `.rpc('match_messages')` is typed. Migrations live on the `schema` branch, and nothing is applied until `npm test` passes on `main`.

### The Build Log check

Describe something buried in a newsletter you know well, without quoting it. Embed the phrase with the same `/embed` function, then pass the vector to `match_messages`.

- It comes back at or near the top.
- It appears exactly once.
- Unrelated newsletters rank below it or fall away.
- A nonsense phrase returns nothing.
- Called with a different user's id, it returns zero rows. This check cannot be skipped — nowhere else is RLS absent as a safety net.

---

## Reading Messages with Gemini Flash Free Tier on Google AI Studio

### Install

```bash
npm install @google/genai
```

Install in `dependencies`, not `devDependencies`. The older `@google/generative-ai` package is legacy.

| Folder | Holds |
|---|---|
| `src/` | Server-side business logic, including `src/reason/` |
| `api/` | Thin Vercel handlers |
| `app/lib/` | Frontend — anything imported here can reach the browser bundle |

### Credentials

1. In [aistudio.google.com](https://aistudio.google.com/) → **API Keys → Create API key**, let it create a new project.
2. Leave that project with no billing account attached — that's the no-charge guarantee.
3. Confirm the Key Type is an auth key; regenerate a reused "Standard" key or one tagged `Blocked`.
4. Quota is per project, not per key — a second key in the same project buys nothing.

```dotenv
# .env — ignored
GEMINI_API_KEY=your_real_key_here
GEMINI_MODEL=the_current_flash_model_id

# .env.example — committed
GEMINI_API_KEY=
GEMINI_MODEL=
```

- Never prefix either variable `NEXT_PUBLIC_`.
- Don't name the key `GOOGLE_API_KEY`: the SDK auto-detects it, and the project already uses `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` for Gmail OAuth.

### reason() prompt

> "Create a reasoning module at `src/reason/index.ts`, with the Gemini implementation in `src/reason/gemini.ts`. Do not put any of this in `app/` — that's client code and the API key must never reach the browser.
>
> It exports one function, `reason`, that takes a system instruction, a prompt, and an array of retrieved messages as context, and returns a result object. Define real interfaces for the arguments and the result — no `any`.
>
> Use the `@google/genai` package. Read the key from `GEMINI_API_KEY` and the model from `GEMINI_MODEL` — don't hardcode either. Add both to `.env.example` with empty values. Never prefix them `NEXT_PUBLIC_`.
>
> Structure it so another provider could be added as a sibling file and picked with an environment variable, but only build Gemini now. Follow the pattern already in `src/providers/gmail/`.
>
> The provider must ground its answer strictly in the context array it's given and never invent a message or a source. No provider SDK may be imported anywhere outside its own implementation file."

The provider never touches the database and never decides what is relevant — it reads only what `match_messages` returned.

### Quota handling prompt

> "Add quota handling to the reasoning module. On a 429, retry with exponential backoff — waiting longer each attempt, with a little randomness added. Not immediate retries, which make rate limiting worse. Match the backoff pattern already in `withWriteRetry`.
>
> If it still fails after the retries, do not throw. Return a result object saying the summary is unavailable, so the caller can still show what retrieval found.
>
> Log quota failures distinctly from other errors, so I can tell 'we hit the limit' apart from 'something is broken'."

Elsewhere in VibeMail a provider failure throws a `ProviderError`. Here a missing summary is not a failed request, because retrieval already succeeded.

### Verification

```bash
grep -rn "@google/genai" src/ app/ api/
```

- A normal call returns text.
- The import appears in exactly one file under `src/reason/` and nowhere in `app/`.
- The key is server-side only and empty in `.env.example`.
- A bad key or a tight loop past the per-minute limit backs off, then returns the unavailable flag.
- A unit test mocks a 429 so the path is covered without burning quota.
- `GEMINI_API_KEY` and `GEMINI_MODEL` are added to Vercel before deploying.

### Free-tier caveats

- Free-tier prompts and responses may be used to improve Google's products — send nothing confidential.
- No SLA.
- No commercial use on the free tier; moving up is a one-line change behind this interface.
- Flash and Flash-Lite standard rows are free; Pro and preview models are typically paid-only.

---

## Improving the Search Bar Functionality to Cross Search

VibeMail has no server actions. Requests go from the frontend through the API layer that attaches the login token to an endpoint.

### Semantic search prompt

> "Add semantic search. When someone types a query, embed it, find the messages closest in meaning, and show them in the existing results list.
>
> Wire it up the same way the existing keyword search is wired. Don't use a server action.
>
> Embed the query with the exact same function and settings the messages were embedded with.
>
> Only ever return the signed-in user's mail. Use the retrieval function, threshold, and result count from `MEMORY_CONTRACT.md`."

| Constraint | Fails how |
|---|---|
| Same wiring as keyword search | Points at a working example instead of listing files |
| Same embedding function and settings | Quietly — a mismatched query matches nothing |
| Signed-in user's mail only | Badly — a cross-user leak |

Handler location, endpoint name, and response shape are left to the agent; check what it picked.

### Question answering prompt

> "When the query reads as a question rather than a lookup, pass the top results to the reasoning module and have it answer from them, naming which newsletters it used. It answers only from what it's given — if the answer isn't there, it says so.
>
> Only call it for questions. A plain lookup shows the list directly, with no model call."

### Fallbacks prompt

> "Add both fall backs. If nothing clears the threshold, fall back to keyword search and label those results as keyword matches. Never show a weak semantic match as a confident answer.
>
> If the reasoning module says it's unavailable, still show the results — just without the summary, and with a quiet note explaining why. Hitting a quota limit must never produce an error or an empty screen."

The keyword search matches subject, sender, recipient, and snippet — not the body. Don't describe it as full-text.

### Related panel prompt

> "Add a Related panel beside an open message: other messages about the same story, whoever sent them.
>
> Don't embed anything new — this message's vector is already in the database.
>
> Don't filter or boost by sender. Leave the open message out of its own list. Show three or four, and if nothing is close enough, say so rather than padding with weak matches."

Not filtering by sender is the design decision; Related is pure geometry over stored vectors with no model or embedding call.

### Feature map

| Feature | Retrieval | Model call | Degrades to |
|---|---|---|---|
| Semantic search | `match_messages` at the tuned threshold | Only for question-style queries | Labelled keyword matches |
| Question answering | Top results as context | One grounded call | Results without a summary, quiet note |
| Related | The open message's stored vector, no sender filter | None | Honest empty state |
| Topic digest | Looser threshold, higher count, no sender collapse | One call, short excerpts | Matching newsletters, no brief |

### Verification

- A question returns a grounded answer naming the newsletter.
- A lookup shows results with no model call.
- Semantic search finds what keyword search misses.
- A thin index falls back to labelled keyword results.
- With reasoning forced to fail, results show without a summary.
- A long roundup surfaces other newsletters covering the same launch.
- The open message never appears in its own Related list.
- A unique newsletter shows an honest empty state.
- A different account sees none of the first account's mail.
- The `/demo` route — mock data, no vectors — degrades rather than throwing.

---

## Combining Retrieval and Reasoning to Create Topic Summaries

### Digest prompt

> "Add a topic digest. Given a topic, find the mail about it across the whole archive — cast a wider net than search does, because the point is breadth rather than the single best answer. Then have the reasoning module write a short brief from it: what's been said, where sources agree, and any specific detail worth surfacing even if only one of them mentioned it. Say which newsletter each point came from, use nothing but what it was given, and send short excerpts rather than whole newsletters.
>
> Only ever the signed-in user's mail. Don't collapse results by sender — if three newsletters covered the same launch, all three belong in there. If nothing relevant comes back, don't call the model at all. If the model is unavailable, list the newsletters that matched instead.
>
> Wire it up like the other endpoints in this app."

| Decision | Why |
|---|---|
| No sender collapse | Several independent sources on one story is the point |
| Short excerpts | The most context-heavy call; free tiers cap tokens per minute |
| Nothing relevant → no call | Don't spend quota on an empty result |
| Model unavailable → list matches | The retrieval still has value |

### Digest UI prompt

> "Add a digest mode to the existing search bar rather than a new view: a small book icon that toggles it. When it's on, make it obvious — swap the bar's icon and placeholder — and send what's typed to the digest instead of search. Show the brief where search shows its answer, with its sources below. Keep it consistent with the rest of the app, and make the toggle reachable by keyboard."

- The icon set is a fixed list — add a book icon before using it.
- The answer panel already exists; almost none of this is new UI.
- Confirm the `/demo` route degrades rather than breaking.

---

## Creating a TEST_PLAN.md for Verification and Tuning

The Jest suite mocks the embed function and the reasoning provider, so it proves wiring, not retrieval quality. `TEST_PLAN.md` is the human half.

### Test plan prompt

> "Write `TEST_PLAN.md` for the memory layer, covering four things.
>
> Retrieval quality: for each of my known queries the right message lands in the top three; queries that share no words with their target still find it; keyword search misses things semantic search finds; unrelated queries return nothing; no message ever appears twice; and something buried deep in a long roundup is still findable.
>
> Feature behaviour: search, related mail, and the digest each work normally and degrade properly when there's nothing to find.
>
> Quota resilience: with the reasoning provider broken, search still shows results without a summary and the digest still lists the relevant newsletters. Nothing shows an error or a blank screen.
>
> Security: acting as one user, try every path against another user's mail — the retrieval function, the embed function, search, related, and the digest."

`npm test` must pass before the schema branch — carrying the chunks table and retrieval function — merges.

### Break reasoning on purpose

Use a bad API key, or fire calls past the per-minute limit to catch a real 429.

- Search shows results with a quiet note instead of a summary.
- The digest lists relevant newsletters with no brief.
- Related is unaffected — it never called a model.

### Measure scores

> "Run my known-good and known-bad queries against the retrieval function with the threshold switched off, and log the raw score for each. Group them so I can see the score range for genuinely related mail against the range where unrelated mail starts. Report only — don't change anything yet."

| Setting | Starting threshold | Starting count |
|---|---|---|
| Retrieval | `-0.82` | 8 |
| Digest | `-0.75` (looser) | 20 (wider) |

- Put the cutoff in the gap between the related and unrelated ranges.
- Try digest counts of 5, 8, and 12; stop where it begins repeating sources. Higher counts also spend more tokens per call.

### Record the decision

> "Update the retrieval and digest constants to the values I picked, and note in `MEMORY_CONTRACT.md` the ranges I measured and why I chose them."

"The threshold is −0.82" is inherited. "Related scored below about −0.85, unrelated started above about −0.6, so the cutoff is −0.75" is measured.

### Verification

- `TEST_PLAN.md` green, every finding fixed, `npm test` passing.
- Backfilled chunk owners confirmed by hand.

---

## Running a Director's Audit of the Memory Layer and Deploy

### Audit prompt

> "Run a Director's Audit of the memory layer against MEMORY_CONTRACT.md. Four areas, pass or fail per line, with evidence. Report only — don't fix anything. Write it to AUDIT_5.md.
>
> Structure: the embedding column matches the model's dimensions; the index and the retrieval function use the same operator; deleting a message deletes its chunks; ownership comes from an explicit user id on every path, not from auth.uid() or row-level security; and no provider SDK is imported outside its own file.
>
> Behaviour: chunking matches the contract, with the header on every chunk and nothing silently truncated; the tuned threshold and count are the ones actually in use; embedding runs on every path that writes a subject or body, and is skipped on label and read-status changes; and both fall backs work — keyword search on a thin index, results without a summary when the quota is gone.
>
> Reasoning: answers come only from the retrieved mail, and the digest never cites a newsletter it wasn't given.
>
> Security: one user can't reach another's mail on any path; no API key is reachable from the browser; and check what the embed function is exposed to, since it doesn't require a token."

- **Ownership is the most important line.** RLS is on, but the explicit `user_id` filter is the real check.
- **There is no trigger to audit.** The question is whether any write path was missed, and the answer is in the code.
- **The embed function has no token check.** It takes a string, returns a vector, and writes nothing — compute is exposed, not data.

### What doesn't ship with a push

| Item | Lives in | Failure if forgotten |
|---|---|---|
| `GEMINI_API_KEY`, `GEMINI_MODEL` | Vercel environment | Production behaves exactly like an exhausted quota |
| The embed function | Supabase | Production runs the old version |
| The production backfill | A script you run | Only mail received after go-live has vectors |

### Production verification

- Search by intent with a query sharing no words with its target.
- Open a long roundup and see who else covered the story.
- Run a topic digest that names more than one newsletter.
- Send yourself an email, then find it by meaning a moment later — the only check that proves the full live path.
- A topic with no coverage says so plainly in both search and the digest.
- The demo route works without signing in.
- Summaries missing everywhere while results appear → check the Vercel environment variables first.

### VibeMail, complete

| Module | Layer |
|---|---|
| Build the VibeMail Backend | The engine |
| Build the VibeMail Frontend | The face |
| Make VibeMail Remember | The memory |

### Before-shipping checklist

- `MEMORY_CONTRACT.md` answers what gets embedded, how retrieval is scoped, when embeddings are invalidated, and how the layer degrades, with numbers rather than adjectives.
- Vector dimension equals model output; index and query both use `<#>`.
- RLS is enabled, not merely policied; chunks cascade on delete.
- `match_messages` filters on a `user_id` argument, with no `auth.uid()` or `SECURITY DEFINER`.
- Every subject/body write path calls the ingest helper; label and read-status changes don't.
- The backfill is local, resumable, embed-only, and sets `user_id` from each owner.
- The provider SDK is imported in one file under `src/reason/`.
- 429s back off with jitter and return an unavailable flag.
- Threshold and count are measured, with ranges recorded.
- `AUDIT_5.md` written, every failing line fixed.
- Gemini variables in Vercel, embed function redeployed, production backfill run.
- A newly received email is findable by meaning in production.
