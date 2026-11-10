# Module 7 Reference Guide
### Cursor — Build Waypoint (PostGIS Map App): Contract, MapLibre, Verification, and Ship

Use this guide alongside the Module 7 videos.

_MapLibre, Supabase, and Cursor surfaces change between releases. Confirm current method names through Context7 rather than trusting a recording-era call._

---

## Table of Contents

1. [Project 1: 'Waypoint' - Producing the First Planning Document (PRD)](#project-1-waypoint---producing-the-first-planning-document-prd)
2. [Writing the CONTRACT.md File](#writing-the-contractmd-file)
3. [Writing the TEST_PLAN.md File](#writing-the-test_planmd-file)
4. [The Visual Reference: DESIGN.md](#the-visual-reference-designmd)
5. [Adding the Custom Map Style](#adding-the-custom-map-style)
6. [Directing 'Composer' to Turn the Contract's Data Model Into a Real Database](#directing-composer-to-turn-the-contracts-data-model-into-a-real-database)
7. [Building Nearby and On-Screen Location Queries for 'Waypoint'](#building-nearby-and-on-screen-location-queries-for-waypoint)
8. [Verify the Location Backend with an Inner Loop](#verify-the-location-backend-with-an-inner-loop)
9. [Building the Map Interface: MapLibre and the Styled Map](#building-the-map-interface-maplibre-and-the-styled-map)
10. [Applying DESIGN.md to our UI, Testing with Integrated Browser](#applying-designmd-to-our-ui-testing-with-integrated-browser)
11. [Adding the Sync List and Photos to Our Project](#adding-the-sync-list-and-photos-to-our-project)
12. [Adding Authentication and Guide Editing Functionality](#adding-authentication-and-guide-editing-functionality)
13. [Publishing a Guide, the Arrival Animation, Final Pass](#publishing-a-guide-the-arrival-animation-final-pass)
14. [Automated Testing and the Audit Command (Inner Loop)](#automated-testing-and-the-audit-command-inner-loop)
15. [Reviewing the Project as a 'Director' after the Automated Checks](#reviewing-the-project-as-a-director-after-the-automated-checks)

---

## Core Concepts

**Keep the product narrow on purpose.** Waypoint's depth is geographic, not a pile of features.

**One column type is the project.** Storing location as `geography(Point, 4326)` is what makes nearby, on-screen, and click-to-drop clean. Two number columns quietly cap what the app can ever do.

**You make the decision; the agent writes the code.** The column type is judgement and belongs in the contract. The location SQL is execution and belongs to Composer.

**A check runs without your eyes.** If something other than you can't run it, it isn't a check yet.

**The edge cases are the point.** The happy path is what the agent gets right on its own; empty results, zero places, and expired links are where confidently-wrong code hides.

**Location filtering belongs in the database.** Use the spatial index; never load every place and hide the off-screen ones in code.

**The map is the interface.** A default map makes the whole app generic; a styled map pulls the rest up with it.

**Correct and right are different questions.** The automated layer answers "is it correct"; the director's review answers "is it good."

---

## Project 1: 'Waypoint' - Producing the First Planning Document (PRD)

A curated map-guides app: a styled map plus photo-forward place cards, private by default, shareable at a public link. Single author, no social features.

### PRD.md

```markdown
# Waypoint — Product Requirements

## What it is
A tool for creating curated, photo-forward map guides. A guide is a
styled map plus a set of place cards. Guides are private by default
and shareable by public link.

## Who it's for
A single author who knows a place well and wants to share it — no
accounts beyond the author, no social features.

## Core views
1. Guide list — the author's guides, with cover images
2. Guide editor — a map where the author drops and edits places
3. Guide viewer — the read view, synced map and place cards
4. Public guide — the shareable read-only view at a slug URL
5. Place detail — a place's photos, notes, category, rating

## Out of scope (v1)
- Following, feeds, comments, saves
- Multi-author guides
- Real-time collaboration
```

### Director's review checklist

- One page, answering what it is, who it's for, and its parts.
- Every core view is a screen the data model will trace back to.
- Out of scope is explicit — it stops both you and the agent from sprawling.
- No database decisions yet; the product is defined before the data is.

---

## Writing the CONTRACT.md File

### Data model

Add to `CONTRACT.md`, tracing each table to a screen:

```markdown
## Data model

users     — id, handle, display_name
guides    — id, user_id, title, description, cover_photo_url,
            is_public, slug
places    — id, guide_id, name, address, notes, rating,
            category, location, sort_order
photos    — id, place_id, storage_path, caption, sort_order
```

### The location column

```text
location  geography(Point, 4326)
```

| Part | Meaning |
|---|---|
| `geography` | Maths on a sphere — the curved earth, correctly |
| `Point` | A place is a single point |
| `4326` | The standard GPS coordinate system |

PostGIS — a Postgres extension Supabase supports — supplies the type and the distance functions.

### Rules

```markdown
## Rules
- Enable the PostGIS extension before any location work
- `location` is geography(Point, 4326) — never two number columns
- Add a spatial (GiST) index on places.location
- Row Level Security on every table: a user reads/writes only
  their own guides; public guides are readable by anyone when is_public
- Slugs are unique, URL-safe, generated when a guide is created
```

| Term | Meaning |
|---|---|
| Spatial index | Organises location data so "what's near here?" jumps to the right area instead of scanning every row |
| Row Level Security | Decides, row by row, who may read or write — what keeps a private guide private |

Commit `CONTRACT.md`. Leave the column type open and Composer reaches for two numbers.

---

## Writing the TEST_PLAN.md File

### From feeling to check

> Given places at known coordinates, asking for places near a point 500 m from place A and 5 km from place B returns A, leaves out B, and reports A's distance within 5% of 500 m.

### TEST_PLAN.md

```markdown
# Waypoint Test Plan

## Location backend
- Nearby: a point 500m from A and 5km from B returns A, omits B;
  A's distance within 5% of 500m
- On-screen: a map area covering A but not B returns A only
- Empty result: a query far from all places returns nothing, not an error

## Interface behaviour
- Hovering a place card highlights its pin on the map
- Clicking a pin scrolls its card into view
- A guide with 0 places opens the editor without error
- Photos load via temporary links; an expired link refreshes, doesn't break

## Access rules
- A logged-out visitor cannot read a private guide
- A logged-out visitor can read a public guide by its link
- A user cannot edit a guide they don't own
```

### Director's review checklist

- Every line is observable and traces to the contract.
- Each check returns a clear pass or fail for a subagent.
- Empty results, zero places, and expired links are covered.
- Organised by what can go wrong: location, interface, access.

Three documents now define Waypoint: brief, contract, test plan — product, structure, proof.

---

## The Visual Reference: DESIGN.md

A `DESIGN.md` is a README for your design: exact values as machine-readable tokens at the top, reasoning as prose below, in Google's open-sourced format. It joins `AGENTS.md` at the root — one for how the agent behaves, one for how things look.

### Decide the look

1. **Mood** — warm, editorial, a printed travel guide rather than a dashboard. Light, not dark.
2. **Type** — on Google Fonts, pick a high-contrast serif for names and a clean sans for body and controls.
3. **Palette** — build it in Coolors or Realtime Colors: a warm paper base, a warm near-black for text, one accent. Preview readability before committing.

_A vibe can't be checked; a named font and a colour value can._

### DESIGN.md prompt

> "Write a DESIGN.md for Waypoint in the Google DESIGN.md format. Headings in Fraunces, body in Inter, both Google Fonts. Paper base #F6F1E9, text #2A2521, one accent #C4552D used only for pins, active states, and links. Editorial, warm, photo-forward — a printed travel guide, not a dashboard."

Expected shape:

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
...
```

Load the file into a `DESIGN.md` previewer to confirm the tokens render as chosen, fix anything that drifted, and commit.

_You decide the look; the agent writes it down._

---

## Adding the Custom Map Style

### The tooling

| Tool | Job |
|---|---|
| MapLibre GL JS | Renders the map in the browser — open source, no key, no usage limits |
| OpenFreeMap | Free, unlimited vector tiles from OpenStreetMap — no key, no watermark |
| Maputnik | MapLibre's open-source visual style editor |

### Load the base

In Maputnik: **Open → Load from URL**

```text
https://tiles.openfreemap.org/styles/positron
```

### Layer edits

| Layer(s) | Change |
|---|---|
| `background` | Paper base `#F6F1E9` — the most important change |
| `water`, `waterway` | Soft, desaturated `#E3E4DC` |
| `park`, `landcover`, `landcover_wood`, `landuse_residential` | A whisper of green, around `#ECEBDD` |
| `building` | Hide (or `#EFEAE0` if kept) |
| `road`, `highway`, `tunnel` | Pale warm `#FCFAF5`, thinner lines |
| `railway`, `aeroway`, `road_shield_us`, `airport` | Hide |
| `label`, `waterway_line_label` | Remove POI and minor labels; keep only high-level place names, muted |
| `boundary` | Mute or hide |

Confirm nothing on the base map uses the accent `#C4552D` — the accent belongs to pins added in code.

**Save → Export** to `/styles/waypoint-style.json`.

### Finish DESIGN.md

```markdown
## Map
Waypoint's custom base map.
- Custom MapLibre style at /styles/waypoint-style.json
- Muted base; land matches {colors.paper} so map and page read as one surface
- No clutter; only the author's pins are shown
- {colors.accent} reserved for pins and active states; the base map uses none of it
```

Add to `AGENTS.md` under the working agreement:

```text
Read DESIGN.md before any interface work; match the visual spec exactly.
```

A rule that only matters for interface files is a candidate for a scoped rule in `.cursor/rules`. Commit the style file and the updated documents.

---

## Directing 'Composer' to Turn the Contract's Data Model Into a Real Database

### Free-plan project limit

The Supabase free plan allows two active projects. Free projects pause after a week of inactivity, and paused projects don't count. Pause a project you aren't using, then create Waypoint.

### Link the project

```bash
supabase link
```

Pick Waypoint. Every `supabase` command — including the migration push — now acts on the real database.

### Migration prompt

> Build the Waypoint database as specified in CONTRACT.md. Turn on the PostGIS extension first. Create the migration with the tables, the geography(Point, 4326) location column on places, and the spatial index. Set up Row Level Security per the contract's rules. Show me the migration before applying it.

### Review the migration for shape

- Four tables, matching the contract.
- `places.location` is `geography`, not two numbers.
- The GiST index is present.
- RLS is enabled with the contract's access rules.

You are not checking PostGIS syntax — you are checking the decision was honoured.

### Apply and confirm

- In the Supabase table editor: four tables, `places.location` shown as geography.
- Ask Composer to confirm the PostGIS extension is on and the index exists.
- Commit the migration file.

---

## Building Nearby and On-Screen Location Queries for 'Waypoint'

The two queries are independent — different endpoints sharing only the existing table and index — so they run in parallel.

### Parallel build prompt

> With `/multitask`, build two location endpoints in parallel, each in its own worktree, following `CONTRACT.md` and `TEST_PLAN.md`. First, nearby: given a point and a radius, return the places within that radius, each with its distance, nearest first — use `st_dwithin` to filter and `st_distance` to measure, against the indexed `location` column. Second, on-screen: given the current map bounds, return the places inside that rectangle — a bounding-box test on the same column. Do the filtering in the database, not in application code, and match the existing data-access pattern.

### The nearby query shape

```sql
select *,
  st_distance(location, st_point(:lng, :lat)::geography) as metres
from places
where st_dwithin(location, st_point(:lng, :lat)::geography, :radius)
order by metres
limit 50;
```

| Function | Job |
|---|---|
| `st_dwithin` | Keeps only places within the radius, on a sphere |
| `st_distance` | Reports the true distance in metres |
| `order by metres` | Nearest first |

### The on-screen query

A bounding-box test against the same indexed column, returning only pins inside the current map view.

The endpoint shape these two establish is the repeated pattern worth capturing as a skill the next time it repeats.

---

## Verify the Location Backend with an Inner Loop

### Apply the parallel build's migrations

```bash
supabase db push
```

Watch both migrations land in order, then confirm both functions exist in the Supabase dashboard.

_Code that isn't migrated is code that doesn't exist yet — however green the tests looked inside the worktree._

### Connect the environment

From **Project Settings → API**, copy the project URL, the anon key, and the service-role key into `.env.local`.

```dotenv
# .env.local — ignored (typical Next.js names; match your scaffold)
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
```

- Confirm `.env.local` is gitignored before pasting real values.
- Commit `.env.example` with names only.
- The service-role key bypasses RLS: server-side only, never in the client, never in the repo.

### Seed the test scenario

> Add a re-runnable seed that inserts the TEST_PLAN.md scenario — a reference point, place A 500m away, place B 5km away — under a clearly labelled test guide. Use upsert or clean-then-insert so a rerun doesn't duplicate, then confirm the rows landed.

### Behaviour check

> Act as a test agent against the seeded scenario. Hit `GET /api/places/nearby` for the reference point and confirm place A returns, place B is omitted, and A's distance is within 5% of 500m. Hit `GET /api/places/in-bounds` for a box around that point and confirm the right places return. Confirm the empty case returns nothing rather than erroring. Report pass or fail for each, with the actual numbers.

### Code check

```text
/review-bugbot
/review-security
```

| Check | Catches |
|---|---|
| Subagent against endpoints | Wrong places returned, crashes on empty |
| Bugbot | Wrong radius units, missing index, off-by-one |
| Security Review | Access paths that don't enforce the contract's privacy |

A failure becomes a grounded instruction — "nearby reports distances four times too large; check the radius units" — rather than "it feels off."

Authentication is deliberately still ahead: the RLS policies exist, but `auth.uid()` resolves to a real person only once the author signs in.

---

## Building the Map Interface: MapLibre and the Styled Map

### Commit command

Type a slash and save this as a reusable command:

> Review the current changes with git diff, stage the modified and new files, write a concise commit message in imperative mood summarising what changed, commit, and push to the current branch — setting upstream if the branch has none. Report the commit hash and branch. Never stage gitignored files or anything that looks like a secret — no .env, no keys.

Keep it to committing. Run `/simplify` separately at settling points and read its suggestions before committing.

_A good command does one thing you can fire without thinking._

### Map component

> Add a map component using MapLibre. Load the custom style at /styles/waypoint-style.json per DESIGN.md. It runs in the browser, so it's a Client Component. Centre it on the guide's places, or a sensible default if the guide is empty.

If the default MapLibre look appears, the style didn't load — fix that first. The map should be boxed into its own Client Component rather than spreading client code.

### Live pins

> Fetch places from the on-screen endpoint for the current map area and show them as pins in the accent colour from DESIGN.md. Refetch when the map stops moving.

### Informal reference check

- The map sits back the way the reference does.
- The accent appears only on pins.
- Map and page read as one warm surface.
- Note each drift as fix-now or log-for-review.

---

## Applying DESIGN.md to our UI, Testing with Integrated Browser

### Global foundation

> Apply DESIGN.md as global styles at the app root. Set the paper background and default text colour on the body, load Fraunces for headings and Inter for body from the token set, and apply the base spacing and container width. This is the foundation every page inherits — don't style individual components here, just the global base.

_Foundation before furniture._

### Browser workflow

| Capability | Use |
|---|---|
| Built-in Chromium panel | The running dev server beside your code, reloading live |
| Multiple tabs | Viewer and editor side by side; public link beside the owner's view |
| Docked dev tools | Console for errors and failed requests; inspector for live DOM and applied styles |
| Agent-readable console/network | "Photos aren't showing" becomes "the signed-URL request returns 403" |
| Agent-driven sessions | Open a guide, hover a card, click a pin, report what was observed |

Build a piece, have the agent exercise it in the browser, read the result, move on.

---

## Adding the Sync List and Photos to Our Project

### Real content seed

Place real photos in `seed/photos/`, then:

> Write a content seed script, run with the service-role key, that: clears the existing test rows; ensures a private place-photos storage bucket exists; creates the author and one real guide; inserts its places with real coordinates, names, notes, categories, and ratings; uploads each image from seed/photos to the bucket with storage.upload; and inserts a photos row storing the returned path, not the file. Make it re-runnable — clean then insert — so it's safe to run twice.

The database holds the path; the bucket holds the file.

### Synced viewer

> Build the guide viewer: place cards in a scrolling list beside the map. Hovering a card highlights its pin; clicking a pin scrolls its card into view and highlights it. Meet the interface checks in TEST_PLAN.md.

- One shared `active` place drives both the card and the pin.
- Optional refinement: as the list scrolls, a slow `panTo` keeps the active place framed, driven off the same state.

### Design Mode polish

Open the preview, enable Design Mode, multi-select the active card and its pin, and say the change. Queue the next tweak by voice while the agent works.

### Signed photo URLs

> Serve the place photos from the private bucket with signed URLs made on the server. Per TEST_PLAN.md, an expired link must quietly refresh, not break.

The bucket's access rules should mirror the guide's privacy — the same logic as the table RLS, applied to files.

### Directions link

> On each place, add a "Directions" link built from its coordinates — a plain geo link that opens the device's default map app. No maps SDK, no API key.

---

## Adding Authentication and Guide Editing Functionality

### Where the password lives

| Table | Holds |
|---|---|
| `auth.users` | Managed identity — email and one-way hashed password |
| `public.users` | Your profile — handle, display name; shares the id |

A password your code can regenerate isn't a secret.

### Replace the placeholder author

> Replace the seeded placeholder author with a real account created via supabase.auth.admin.createUser, with email_confirm true and no password derived in the script. Point the public.users profile and the guide's ownership at the returned id, and remove the old placeholder. Idempotent — reuse the account if the email already exists.

### Set the password out of band

> Write a one-off task that sets my author password with supabase.auth.admin.updateUserById, run with the service-role key, taking the password as an argument. Don't commit it.

### Lock the door

In the Supabase dashboard under **Authentication**, turn off **Allow new users to sign up**. Not building a sign-up screen isn't enough; the API stays open by default.

### Sign-in screen

> Build a minimal sign-in screen following DESIGN.md. Protect the authoring routes so only a signed-in author reaches them; the public /g/[slug] route stays open to anyone with no session.

Signed in, `auth.uid()` resolves and you see only your guides. Signed out, only public ones remain.

### Guide list

> Build the guide list as the signed-in author's home: a cover-forward grid of their guides per DESIGN.md, each opening to edit, plus a way to create a new guide. Follow the guide-card component in DESIGN.md.

One component, two audiences: RLS filters to yours-or-public.

### Editor

> Build the guide editor per the brief. Clicking the map drops a new place at that point; a form captures name, notes, category, rating, and photos. Photo upload reuses the existing pipeline — upload to the same private bucket via storage.upload, store only the path, and display through the signed URLs already built. Support multiple photos per place with reorder and remove, using the sort_order column. Places can be edited and removed. Save the location as the geography type from the contract's data model.

Click-to-drop converts screen coordinates into a real point in the geography column — the creating side of the decision that powered the nearby query.

---

## Publishing a Guide, the Arrival Animation, Final Pass

### Publishing

> Add publishing: a guide can be switched public. Public guides open at /g/[slug] as a read-only view. Make slugs per the contract — unique and URL-safe. Enforce the access rules from TEST_PLAN.md.

Slug traps: duplicate titles, spaces, and punctuation. Privacy bugs bite at publishing — a private guide must return not-found to anyone but the owner.

### The arrival

> When a public guide loads, start the map as a 3D globe and animate a single flyTo that frames the guide's places, then hold the flat, muted style from DESIGN.md. Use MapLibre's globe projection and flyTo. Run it once on load, never on later interaction.

Record it in `DESIGN.md` under `## Motion` as the single exception, so the agent doesn't spread animation elsewhere.

### The final pass

- Select an element in the live app; adjust spacing, colour, and type from the design sidebar — every nudge writes to code.
- Close gaps against `DESIGN.md`: card padding to the spacing token, headings in Fraunces, active pin and card sharing the accent, paper base everywhere.
- Stop when the interface matches the committed document, not when it "feels finished."

### The shape of done

Walk all five screens — guide list, editor, viewer, public guide, place detail. Each exists, traces to the brief, and matches `DESIGN.md`. Feature-complete and on-spec is still not confirmed.

---

## Automated Testing and the Audit Command (Inner Loop)

### Create `/audit`

Save as a **user command** in `~/.cursor/commands/` so it is available in every project:

```markdown
# /audit

Run the full pre-ship review:
1. Drive the running preview and check every behaviour and access
   item in TEST_PLAN.md; report pass/fail with what was observed.
2. Compare the running UI against DESIGN.md and the reference;
   report specific visual differences.
3. Check load performance: images that shift layout or re-download,
   full-size assets served where thumbnails belong, slow first paint;
   report specific offenders.
4. Review the whole codebase for quality and security — the
   entire project, not a diff.
5. Collect all findings into a single list, each marked
   fix-now, log-for-v1, or confirmed-good.
```

_Capture a workflow as a command the moment you realise you'll run it again._

### What each step catches

| Step | Example finding |
|---|---|
| Behaviour and access | Private guide readable logged out; empty guide crashes |
| Visual | Accent leaking onto category badges; headings lighter than reference |
| Performance | Guide-list photos shifting layout and re-downloading each visit |
| Whole-codebase review | Login paths and storage rules — not just the last diff |

The audit finds; it doesn't fix. Send fix-now items back to Composer as specific instructions and re-run the affected checks.

---

## Reviewing the Project as a 'Director' after the Automated Checks

### What the review is for

- Is this actually good, or merely correct?
- Does it feel like the editorial product you set out to make?
- Would you be proud to share the link?
- Is something subtly off that no check caught?

### Walking the review

1. Make a real guide and publish it.
2. Open the public link in a private window, as a stranger would.
3. Pan, hover cards, click pins, open dropdowns.
4. Fix visual hitches in Design Mode; fix structural ones with a targeted prompt.
5. Use it again to confirm the feel actually changed.

Image pop-in fix:

> The guide-list photos shift the layout as they load and re-download every visit. Reserve space with explicit aspect ratios, serve thumbnail sizes instead of originals, and cache the signed URLs so they don't re-fetch each time. Keep the private-bucket, signed-URL security model.

Commit the running list as `AUDIT_3_WAYPOINT.md` — each finding, its verdict, fixed or accepted-for-v1.

### Ship

```text
/review
```

> Prepare Waypoint to deploy on Vercel. Confirm environment variables are validated at startup, the production Supabase keys are set, and the map style loads in the production build. Walk me through the deploy.

### Production checks

- Open the live production URL, not local.
- Map style loads; pins appear from real data.
- Sign-in works; private guides stay private.
- Public `/g/[slug]` opens for a logged-out visitor with the globe arrival.
- Signed photo URLs load and refresh.
- Production Supabase environment variables are present.

### Clean break

Commit everything, note the live link, and close the project before opening the next one.

### End-of-project artifacts

`PRD.md`, `CONTRACT.md`, `TEST_PLAN.md`, `DESIGN.md`, `AGENTS.md`, `/styles/waypoint-style.json`, migrations, seeds, the `/audit` user command, `AUDIT_3_WAYPOINT.md`, and the deployment.
