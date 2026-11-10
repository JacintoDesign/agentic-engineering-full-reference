# Module 14 Reference Guide
### Creating a 3D Portfolio World — A Walkable World, Directed, Audited, and Shipped

Use this guide alongside the Module 14 videos.

_Helper names and peer-compatible versions in React Three Fiber, drei, Three.js, and post-processing change. Confirm current package contracts before starting the build._

---

## Table of Contents

1. [Using Your Vibe Sentence to Create a World You Can Walk Through](#using-your-vibe-sentence-to-create-a-world-you-can-walk-through)
2. [Planning Documents including WORLD_BRIEF.md](#planning-documents-including-world_briefmd)
3. [Plan Mode, Building our World Shell](#plan-mode-building-our-world-shell)
4. [Building Navigation, Walking, Look Controls](#building-navigation-walking-look-controls)
5. [Building the City: Towers, Traffic, Street Life](#building-the-city-towers-traffic-street-life)
6. [Doing the Atmosphere Pass, Rain & Neon](#doing-the-atmosphere-pass-rain--neon)
7. [Adding Billboard to Showcase Projects](#adding-billboard-to-showcase-projects)
8. [Adding About & Contact Stations](#adding-about--contact-stations)
9. [Adding Splash Page & Sound](#adding-splash-page--sound)
10. [Director's Audit After Deploying to Production](#directors-audit-after-deploying-to-production)
11. [Added More Projects, Comparing with ChatGPT Astra Version](#added-more-projects-comparing-with-chatgpt-astra-version)
12. [Provided Project Rulebook (CLAUDE.md)](#provided-project-rulebook-claudemd)

---

## Core Concepts

**Roles, not objects.** Every world has a showcase surface, a bio station, a contact station, and surroundings. Your sentence and screenshot decide what fills them.

**Divergence is the success condition.** If your world looks like the instructor's, you handed over the wrong inputs.

**A world needs a look, not a behaviour spec.** `WORLD_BRIEF.md` turns mood into confirmed numbers.

**Plan Mode when parts interlock; a direct prompt when work is linear.** The test is whether getting the order or boundaries wrong costs an afternoon.

**Navigation before scenery.** Everything after is judged at eye height, from the pavement, while moving.

**Generate what you can describe in a sentence; download what you can't.** And weigh how close anyone gets.

**Bloom spreads light; it doesn't add it.** Darkness is fixed with emissive and fill, never with bloom.

**A world isn't verified until someone walks it.** On a phone, as a stranger.

---

## Using Your Vibe Sentence to Create a World You Can Walk Through

### Find the screenshot

Open the screenshot of the flat page your vibe sentence produced in Module 1 — one prompt deep, no specification behind it. The capstone renders the same sentence as a place you walk through.

### What you're building

| Role | Carries | Instructor's world | Other worlds |
|---|---|---|---|
| Showcase surface | Projects, one at a time, each with a door to the live deployment | Video wall at the end of the street | Projection dome; framed prints |
| Bio station | About, walk-up overlay | Studio booth | Field desk; reading desk |
| Contact station | Channels, walk-up overlay — and always in the nav | Row of mailboxes | Radio set; noticeboard |
| Surroundings | Nothing — atmosphere only, clickable nowhere | Towers, traffic, vending machines | Dunes; receding shelves |

### The five rules that don't change

1. Projects appear one at a time.
2. Everything is generated from `CONTENT.md`.
3. Doors open the real deployment.
4. Contact is always reachable from the nav as well as in the world.
5. Surroundings never carry content.

Choose three or four projects, not everything you've made.

### How you'll build it

- Claude Desktop, Code tab, live preview open — Claude sees the world the way you do.
- A plain code editor beside it, only for reading files before commit.
- React Three Fiber, plus the shader craft from the cinematic portfolio module.
- One thing at a time, verified before the next.
- **Batch by category, not by object** — never batch a step whose purpose is reacting to the one before.

### Generate or download?

| Question | Generate | Download |
|---|---|---|
| Can you describe the shape in a sentence? | Shop front, vending machine, car at night | Motorcycle, statue, machinery up close |
| How close does anyone get? | Seen at distance | Stood next to |

Generated things stay tunable — change a colour in the brief and every painted facade updates.

---

## Planning Documents including WORLD_BRIEF.md

### Draft it from your inputs

> Here's my vibe sentence, and a screenshot of the page it made in the first module. Write me a `WORLD_BRIEF.md` for a 3D version I can walk through — a short street, first person. Real numbers, not adjectives.

### Choose the content objects

> What should the three content surfaces be in this world? One shows my projects, one at a time, with a link to each live site. One holds my About. One holds my contact details. Suggest objects that belong here, and where they go.

> The showcase surface here is [your idea]. What should the bio and contact stations be, to match?

### Direct it

Push back until the brief is yours: atmosphere, scale, materials, lighting, post-processing, navigation, and content placement, all as numbers.

### Documents brought in as-is

| Document | Source | Governs |
|---|---|---|
| `WORLD_BRIEF.md` | New — drafted by Claude, directed by you | Every atmospheric, material, light, and scale value; layout; the content objects; navigation |
| `CONTENT.md` | From the cinematic portfolio | Projects, bio, and contact copy |
| `CLAUDE.md` | Provided handout | Architecture, interaction, performance, accessibility, build discipline |

### Checklist

- Vibe sentence and screenshot located.
- `CONTENT.md` and the provided `CLAUDE.md` in a new repository.
- Three or four real projects selected; no invented content or links.
- Brief committed before implementation.

---

## Plan Mode, Building our World Shell

Scaffold Next.js in the Code tab and add React Three Fiber, drei, and `@react-three/postprocessing`.

### Plan only the shell

> Plan **only the world shell**: the scaffold, a typed `lib/world.ts` holding every value from the brief, the canvas, the ground and its reflective material, fog, and the ground geometry with the walkable bounds. Nothing else — no scenery, no navigation, no effects, no content. Give me the file list and build order before you write anything.

### When the plan finds holes in the brief

> For each gap: suggest a value, say where you got it from, and tell me **what I'd see if it were wrong in each direction**. Then split them — which are look decisions I should make, and which are technical details you should just decide?

| Kind | Who decides | Where it lives |
|---|---|---|
| Look decision | You | `WORLD_BRIEF.md` |
| Technique | The agent | Noted in code |

### The wet ground is the signature

```tsx
<mesh rotation={[-Math.PI / 2, 0, 0]}>
  <planeGeometry args={[WORLD.street.width, WORLD.street.length]} />
  <MeshReflectorMaterial
    blur={[400, 100]}                    // heavy blur — wet, not mirrored
    resolution={WORLD.ground.reflectionRes}
    mixStrength={WORLD.ground.mixStrength}
    roughness={WORLD.ground.roughness}
    metalness={WORLD.ground.metalness}
    color={WORLD.palette.wetPavement}
  />
</mesh>
```

- Fog is what lets the world be small — something closes the far edge, so there's no horizon to build.
- Every value reads from `lib/world.ts`, the typed mirror of the brief.

### The brief catches up before commit

> Update `WORLD_BRIEF.md` with everything you decided this lesson. One line of reasoning each. Show me the diff before committing.

A decision that lives only in a code comment means the brief has started lying.

---

## Building Navigation, Walking, Look Controls

### Plan only the controller

> Plan **only the player controller**: drag to look, walking from keyboard and touch, the clamp on the walkable bounds, and reduced motion. No collision, no pointer lock. Show me how keyboard and touch end up in the same place before you write anything.

### Drag-to-look, not pointer lock

- Pointer events on the canvas; yaw and pitch on refs; pitch clamped short of vertical.
- One model on desktop and touch.
- `PointerLockControls` contests Escape with the overlays, hides the cursor needed for doors, and must be released and recaptured around every locked view.

### Walking

- Camera-relative, flattened to the ground plane.
- WASD and arrows on desktop; an on-screen stick on touch; both feed one intent vector.
- Eye height from the brief.

### The clamp

```ts
pos.x = clamp(pos.x, -WORLD.strip.halfWidth, WORLD.strip.halfWidth)
pos.z = clamp(pos.z, WORLD.strip.zMin, WORLD.strip.zMax)
```

Collision boxes come later, as objects are placed. Boxes leak at corners; a clamp can't.

### Reduced motion, written in now

- No camera easing, head bob, or entry sweep.
- Everything stays reachable.
- Verified on the deployed build at the audit.

Walk the empty street before adding anything.

---

## Building the City: Towers, Traffic, Street Life

Density is the difference between a place and a diorama.

### Towers: paint the facade

```tsx
const tex = paintFacade({ cols, rows, palette: WORLD.palette.windows })

<meshStandardMaterial
  map={tex}
  emissiveMap={tex}
  emissive="#ffffff"
  emissiveIntensity={WORLD.city.windowEmissive}
/>
```

The same canvas image is both colour map and glow map, so lit windows emit light.

### Facades pass

> **Facades, both jobs in one pass.** **Towers:** boxes with facades painted on a canvas — a grid of mostly dark windows, some lit. Use that same image as **both** the colour map and the glow map, so the lit ones give off light. Work the window grid out from each building's real height, rounded to a few sizes so similar towers share one image. Varied heights along the lots, instanced. **Storefronts:** along the bottom four metres of both facades. Per unit a recessed doorway, a roller shutter — most closed, a few open — an awning over some, and a sign box above the door. Vary the widths, leave a couple of gaps. Sign boxes and spill light from open shutters are emissive, colours from the brief's accent list. Collision boxes on everything solid as it goes up. All values from the brief.

Storefronts are the part visitors actually walk past.

### Loose-objects pass

> **Everything loose, one pass.** **Traffic:** cars as boxes — a body, a smaller cabin, glowing faces front and back, a coloured light underneath hitting the road. Run them in the lanes both ways, spaced unevenly, nothing overlapping. **One `useFrame` for all of them**, not one per car. No collision — they're outside where the visitor can walk. **Props:** vending machines against the walls, a food cart with a warm light inside, parked scooters, crates, cones, a bin. Boxes and cylinders, glowing panels where something lights up. No collision on small props. Instance anything repeated, and don't space them evenly. Skip the audit pass on this one — it's scenery, and nothing here can break the world.

Review bulk scenery as a diff, walk past it, adjust.

### One model, done properly

**Read the licence before the mesh.** Sources: Sketchfab, CGTrader, Poly Haven, Kenney, Quaternius.

| Licence | Meaning |
|---|---|
| CC0 | Nothing required |
| CC-BY | A credit line you must add |
| Editorial use only | Not for this |
| "Free to download" | Not the same as free to publish |
| "Not retrievable individually" | A `.glb` in `public/` fails it — anyone can type the URL |

Record the source and licence next to the file. On a retrieval clause, ask the seller whether web embedding is fine, or choose CC0.

### Compress, then optionally inline

> This model is 38MB. Get it under 2MB. It's seen from two to ten metres away, at night, so it only has to hold up at that distance. Use gltf-transform, and tell me what each step saved.

> After compression, inline the model into the bundle rather than serving it from `public/`, and load it from there. Tell me what it added to the bundle size.

### Instance it

Anything repeated uses `InstancedMesh` or drei's `<Instances>`.

---

## Doing the Atmosphere Pass, Rain & Neon

### Give every pool of light a source

Walk the street and look down. For each bright pool on the wet ground, decide before prompting:

| Pool | Fix |
|---|---|
| Tight, coloured, localised | Give it a real source above — a sign, lamp, lit doorway |
| Broad and soft | Widen and dim it, so it reads as off-frame spill |

_A strong reflection is a claim that something is there. Either make the claim true or stop making it._

### Rain, neon, and light sources — one pass

> **Rain:** instanced geometry — thin flat shapes in a box that follows the camera, sent back to the top when they fall past the bottom, so it rains wherever the visitor is. Density, speed and streak length from the brief. One `useFrame` for all of them, half the count on phones. **Neon:** emissive materials at the brief's values — sign faces, edge strips, light spilling from open shutters. **Light sources:** [the pools you found, and what you decided for each]. Don't touch the bloom settings — that is the next step.

The neon will look like coloured plastic. Leave the emissive colours alone — nothing is blooming it yet.

### Post-processing — its own prompt

> Add the bloom pass and a vignette using the threshold, strength and radius from the brief. Nothing else in the chain yet.

Reflective ground, fog, and bright rectangles become a city at 3am in the rain. Walk it at eye height.

### If it is still too dark

> The street reads too dark at eye height. Raise the emissive values and the fill light in `WORLD_BRIEF.md`, not the bloom — bloom spreads light rather than adding it. Show me the diff before you apply it.

> Add a soft haze cone under the three or four brightest signs so their light has visible shape in the air. Cheap additive geometry, not a volumetric pass — we are already near the draw-call ceiling.

### Check against the brief

Compare the screen to `WORLD_BRIEF.md` and close the gap in that direction. If the brief is wrong, change it deliberately, then rebuild to it. Remember the bloom threshold — it decides the next lesson.

### Know your number

The scene is now as expensive as it will get. Walk the street and write the frame rate down as a baseline. If it's catastrophic on your own machine, reflection resolution is the first dial.

---

## Adding Billboard to Showcase Projects

### Plan only the showcase surface

> Plan **only the showcase surface**: one renderer that draws a project from `CONTENT.md`, the canvas image for its title, blurb and tags, prev/next through the list, the door that opens the live link, and the locked camera view. Nothing else. Show me the component boundaries and where state lives before you write anything.

### One renderer over a data list

- One slide renderer pointed at an index into the project list — never hand-built slides.
- Adding, removing, or replacing a project in `CONTENT.md` changes the wall with no component edit.

_Build it so that swapping a project is a data edit, not a construction job._

### Text: paint it on a canvas

`Text3D` ships a font and glyph geometry; troika adds a dependency. A canvas with `fillText` and word wrap, mapped onto a plane, is crisper at distance and costs one texture. Canvas painting now solves facades, slides, and station signs.

### The screenshot that sets the street on fire

A bright portfolio screenshot sails over the bloom threshold and becomes a white slab lighting the street. Dim the texture, or the material's emissive contribution, below the knee. Never turn the bloom down to fix it.

_Once you add a glow pass, brightness stops being a style choice and starts being a statement about what is a light._

### Doors

- Live URL and repo link on the slide; raycast the click; open in a new tab.
- Anything live **fails quietly** — a resting glow or fallback image, never a spinner, an error, or a white plane.

### The locked view

- Clicking the screen glides the camera to a fixed head-on pose; movement suspends.
- Prev/next from the keyboard or an on-screen bar; Escape or Back returns control.
- Camera distance derived from field of view and screen shape, so it fits on a phone.
- Keep the camera code general — target pose, ease, release — for the guided path.

_At the moment the content matters, the world stops being a game._

---

## Adding About & Contact Stations

### Plan the stations and how they connect

> Plan the two stations and how they connect. I need a mode system for whether the visitor can move, a way to register the stations so only one opens at a time, a prompt that shows up when you're near one, a fix for clicks accidentally firing during a camera drag, nav buttons for all three sections, and a guided path between them that works on desktop and touch. Show me the state shape first, and everywhere it touches existing code.

### The mode machine

```ts
export type Mode = 'loading' | 'play' | 'locked' | 'overlay'
export const canControl = (s: State) => s.mode === 'play'
```

Nothing moves unless `canControl` says so — otherwise a visitor typing in the contact form walks across the world.

### One owner of the interact key

```ts
const station = nearestStation(playerPosition())
if (station) openOverlay(station.id)
```

- Stations register on mount with a position and radius.
- One manager reads the key, picks the nearest station in range, and fires once per press.
- Stations never listen for the key themselves — two in overlapping range would both fire.

### The prompt

A fading prompt appears when the visitor is within a station's radius.

### The click guard

```ts
const DRAG_SLOP_PX = 6
const isTap = (e: { delta: number }) => e.delta <= DRAG_SLOP_PX
```

Look-drag and click share the left button. Past a few pixels of travel, it was a camera turn, not a tap — the guard drag-to-look has owed since navigation.

### Nav and guided path

- Nav buttons for the showcase, bio, and contact — contact always reachable.
- A "next stop" guided path gliding between all three, reusing the locked-view camera, on desktop and touch.
- Escape closes whatever overlay is open; nothing else claims it.

---

## Adding Splash Page & Sound

A black canvas tells a visitor nothing is coming. The gate covers the download, unlocks audio, and sets the tone.

### Entry gate

> Add an entry gate — a full-screen panel over the canvas, in the world's palette, with its name and a single button. Keep the button disabled until the scene is ready. **Start the AudioContext inside that button's click handler, not anywhere after it** — browsers only allow it on a real gesture. On dismiss, move the mode from `loading` to `play` so control goes live.

### Generated sound

> Generate the rain with the Web Audio API — no files. Filtered noise, looped, with a slow wobble underneath so it doesn't read as static. **Keep it soft and low** — roll the high end off so there's no hiss, and set the gain quiet enough that it sits under everything rather than filling the room. Route it through a gain node. Start it when the gate is dismissed, and put a mute toggle **bottom right**, visible from the first frame and remembered in `localStorage`.

Read it as a shape: a desert wants low wind; a winter library wants room tone. One continuous sound that belongs to the place, generated, killable in one click.

### Controls hint

> After the gate dismisses, show a small panel listing the controls — drag to look, WASD or arrows to walk, E to interact, and the next-stop button. Dismissed with a click, and remembered in `localStorage` so a returning visitor isn't shown it again. Leave a question-mark button **bottom right, beside the mute**, that reopens the same panel at any time.

### What it weighs

> Run a build and show me the chunks with their gzipped sizes. Is the 3D engine already split into its own chunk, or is it bundled with application code?

A 3D world ships megabytes. Split the engine into its own chunk so it caches independently, and let the gate cover the wait.

---

## Director's Audit After Deploying to Production

### Auditing a place

| Dimension | Against | Check |
|---|---|---|
| Structural | `WORLD_BRIEF.md` | Showcase where the brief says; both stations; surroundings to layout; every door works |
| Consistency | `CLAUDE.md` | Stack constraints and never-do rules; `three` pinned with its reason; no atmospheric values inline |
| Visual | `WORLD_BRIEF.md` | Screenshot plus brief to Claude for divergence — then walk it |
| Content | `CONTENT.md` | Every project and link matches; data edit changes the wall; nothing leaked into surroundings |
| Code | Reviewer Agent | Frame-loop allocation, `setState` in `useFrame`, wall resilience, collision, clamp, guided path |

**The freeze test:** stand still and take a screenshot. If a still frame at eye level is unremarkable, the problem is layout and light — no amount of rain fixes it.

### Push a preview first

Production is minified, tree-shaken, chunked, and compressed — verify that, not the dev build. A preview URL is also the easy way onto a phone.

_A build that succeeds is not a world that renders. Open the URL._ WebGL fails silently to a black canvas.

### The verification pass — on the preview URL

**Frame budget on a real mid-range phone:**

| Platform | Tools |
|---|---|
| Desktop | DevTools Rendering → FPS meter; Performance panel; `WebGLRenderer.info` for draw calls and triangles |
| Android | Remote debugging via `chrome://inspect` |
| iPhone | Safari's Develop menu |

Let it run — thermal throttling to 35fps after twenty seconds is the real result. Watch memory; a mobile browser discards a page that climbs too far.

If under budget, turn down in this order:

1. Reflection resolution — halve it
2. Full-screen effect density (rain) — halve on mobile
3. Device pixel ratio — cap lower on mobile
4. Draw calls — instance harder before deleting
5. Bloom strength — slightly, on mobile

**Reduced motion:** enable it (macOS Accessibility or DevTools emulation) and walk the world. Nothing swings or eases; everything stays reachable.

**The ten-second test:** hand your phone to someone who has never seen the site. If they can't find your email in ten seconds without walking, the nav has failed.

**The real question:** not "does this look impressive?" but "would the person you built this for reach out?"

Fix, push, re-test against the new preview. When it all passes, commit `AUDIT_10.md` to the Project's knowledge base beside your earlier audit log.

_The build adds. The audit subtracts. A produced world is the result of both._

### Ship it

1. Point a domain you own at the deployment.
2. Confirm the certificate.
3. Load it cold on your phone, in a browser with no history.
4. Walk the live site end to end.

### The last image of the course

Put the Module 1 screenshot beside a screenshot of the world. Same sentence, same seed. The first was generated; the second was specified, built, verified, and shipped.

---

## Added More Projects, Comparing with ChatGPT Astra Version

Build the same world a second time, in Astra, from the same documents. The point isn't to find a winner. It's to test whether your planning system is **portable**: whether `WORLD_BRIEF.md`, `CONTENT.md`, and the rulebook carry the world to a different tool, or whether the world only ever lived in one tool's session history.

### Set it up fairly

- **Same inputs.** `WORLD_BRIEF.md`, `CONTENT.md`, your project media, and the rulebook. Rename `CLAUDE.md` to `AGENTS.md` (or whatever anchor file the tool reads) without editing the content. If you have to rewrite a rule to make it work, write that down. It's a finding.
- **Fresh repository.** Don't point Astra at the Claude Code build. It should build from the documents, not copy the code.
- **Same build order.** Shell, navigation, city, atmosphere, showcase, stations, threshold. Astra's one-shot strength will tempt you to ask for the whole world in one prompt. Try that once if you're curious, but run the real comparison in the same order you used before, so each stage can be judged against the brief.

### The opening prompt

> Read `WORLD_BRIEF.md`, `CONTENT.md`, and `AGENTS.md` before writing any code. These are the source of truth. Don't invent content, change confirmed numbers, or add features the brief doesn't list. Summarise the world, its four roles, the frame budget, and the build order back to me, and list anything in the documents you find ambiguous. Then stop and wait for my go-ahead.

That summary is your first check. If it gets the roles, the budget, or the order wrong, fix the document rather than correcting it in chat.

### Compare side by side

| Dimension | What to check |
|---|---|
| Brief fidelity | Do the confirmed numbers (fog, palette, eye height, walkable bounds, frame budget) appear as written? |
| Content fidelity | Is every name, link, and description from `CONTENT.md`? Any invented copy or URLs? |
| Rulebook adherence | Are there any `setState` calls inside `useFrame`? Are shaders in their own files? Is the scenery clickable anywhere it shouldn't be? |
| 3D quality | Models, materials, and lighting at eye height. This is where Astra's modelling strength should show |
| Navigation and reach | Can a stranger reach every surface and open every door, on desktop and on a phone? |
| Frame budget | Measured on the same mid-range phone, after twenty seconds of thermal throttling |
| Bundle | Gzipped chunk sizes, and whether the 3D engine is split into its own chunk |
| Effort and cost | Prompts sent, corrections made, and usage spent to reach the same point |

Run the same Director's Audit on both builds. The same checklist and the same devices are the only way the comparison means anything.

### Reading the result

- **Where Astra beats the brief, that's the tool.** Its procedural modelling may produce better-looking objects from the same sentence. Note where, and whether it stays within the frame budget.
- **Where both builds drift the same way, that's the document.** A shared failure means the brief or rulebook left room for it. Tighten the document, not the prompt.
- **Where only one build drifts, that's the tool's adherence.** Some tools follow standing orders better than others. That's worth knowing before you choose one for your next project.

_A planning system that only works in one tool is a habit, not a system._

### Checklist

- [ ] The same documents were used for both builds, and any changes are recorded
- [ ] The read-back summary matched the brief before any code was written
- [ ] Both builds went through the same Director's Audit on the same devices
- [ ] Frame budget, bundle size, and effort were recorded for each
- [ ] Each difference is classed as a tool difference or a document gap
- [ ] Any document gaps were fixed in the documents themselves

---

## Provided Project Rulebook (CLAUDE.md)

The reusable handout that governs the walkable world. Adapt only where it conflicts with your confirmed world; keep its architecture and performance constraints intact.

A walkable 3D world, moved through in first person at eye level, that doubles as a portfolio. Small on purpose: crossable in under a minute, with something closing the far edge so there is no horizon to build.

### The three content surfaces

Content lives in exactly three places, and nowhere else. These are roles — the objects that fill them are defined in `WORLD_BRIEF.md`.

| Role | Carries |
|---|---|
| **Showcase surface** | The projects — one at a time, from `CONTENT.md`, each with a door to its live deployment |
| **Bio station** | About — a walk-up overlay, also reachable from the top nav |
| **Contact station** | The channels — a walk-up overlay, also reachable from the top nav |
| **Surroundings** | Nothing. Pure atmosphere. |

Whatever the objects are: one project at a time · everything generated from `CONTENT.md` · doors open the real deployment · contact always in the nav as well as in the world · **surroundings carry nothing** — no names on scenery, nothing clickable in the backdrop.

### Source of truth

| Document | Governs |
|---|---|
| `WORLD_BRIEF.md` | Every atmospheric, material, light and scale value; layout; what the three content surfaces are; navigation |
| `CONTENT.md` | The projects — name, description, tech, live URL, GitHub, screenshot path |

> **Atmospheric values are never invented and never hardcoded inline.** Every colour, fog density, emissive intensity, roughness, post-processing setting, reflection strength and scale figure comes from `WORLD_BRIEF.md`, read through a typed `lib/world.ts` that mirrors it.

If a value is needed and the brief does not contain it, **stop — then propose a value, say what it was derived from, and say what would be visible if it were wrong in each direction.** Do not write it until it is confirmed. The rule guards against silent invention, not against proposing: a bare list of gaps is not usable by someone who has no basis for choosing between them.

Sort what you raise. Look decisions belong to the person and go in the brief; pure technique is yours to decide and note in the code.

**Decisions made during a build go back into the brief before the commit** — resolved gaps, contradictions found, judgement calls where the brief left a shape open. A decision that lives only in a code comment means the brief has started lying, and everything downstream still reads it as true.

**Project content is never invented either.** Names, descriptions, links and screenshots come from `CONTENT.md`. No placeholders, no lorem, no invented URLs.

### Stack

- **Next.js 16** (App Router) · **React 19** · **TypeScript** (strict)
- **three** + **@react-three/fiber** + **@react-three/drei** + **@react-three/postprocessing**
- **Tailwind** — 2D overlay UI only (nav top left, controls bottom right, overlays, gate, hint). Never inside the Canvas.
- **Vercel**

Install the four 3D packages together so they resolve as a set. Then **pin `three` to an exact version and write the reason beside it** — these packages peer-depend in narrow ranges, and a mismatch surfaces weeks later on an unrelated `npm update`, looking like application code is at fault. A pin without a stated reason gets deleted by whoever reads it next.

Never add a dependency without asking.

### React Three Fiber

Each of these exists because breaking it produces a failure that does not resemble its cause.

- **Client components only.** Any file importing `three`, `@react-three/fiber`, `drei` or `postprocessing` starts with `'use client'`.
- **The Canvas never server-renders.** Load it through `next/dynamic` with `ssr: false`; SSR throws on `window` and the error points nowhere useful.
- **One Canvas** for the whole world. A second one carries its own renderer and scene graph and halves the frame rate for nothing.

**Frame-loop discipline** — the rule most likely to cost an evening:

- Per-frame work goes in `useFrame`.
- **Never `setState` inside `useFrame`.** Sixty React renders a second consumes the frame budget, and the symptom looks like a problem with whatever is most visible on screen.
- **Never allocate inside a frame loop.** Hoist `Vector3`, `Color`, `Matrix4` to module scope and mutate in place.
- Animate by mutating refs, not by re-rendering.
- Read UI state in the loop with a **direct store read**, not a subscription.

```tsx
const tmp = new THREE.Vector3()          // hoisted once

useFrame((state, delta) => {
  tmp.set(0, Math.sin(state.clock.elapsedTime), 0)
  meshRef.current.position.lerp(tmp, delta)   // mutate, don't setState
})
```

**Allocation and reuse.** Geometries and materials are created once and reused, never constructed in a render body. Repeated geometry uses `InstancedMesh` or drei's `<Instances>` — with any density this is not optional. Prefer declarative primitives so disposal is handled; anything constructed manually must be disposed manually.

**Shaders.** GLSL lives in its own files under `shaders/`, never as strings scattered through components. Uniforms update through a ref inside `useFrame`, never through React state. Anything covering the full screen every frame is the first thing to eat the budget — keep it cheap.

**Text.** Use canvas textures, not 3D type. `Text3D` and troika mean shipping a font and paying for glyph geometry; a painted canvas is crisper at distance and costs one texture.

### State and interaction

The world and the DOM are two layers joined by one store.

**The mode machine.** One store holds the mode; one selector answers whether the visitor may move.

```ts
export type Mode = 'loading' | 'play' | 'locked' | 'overlay'
export const canControl = (s: State) => s.mode === 'play'
```

**Nothing moves unless that selector says so. No exceptions.** Otherwise a visitor typing in a contact form walks across the world while doing it — invisible while building, because you never type in your own form.

**One owner of the interact key.** Stations register on mount with a position and radius. A single manager reads the key, finds the **nearest** station in range, and opens it. Fires once on press — holding the key does nothing. Never let each station listen for the key itself — two in overlapping range both fire.

**The click guard.** Look-drag and click share the left mouse button, so a drag ending over an interactive object would otherwise click it on release. R3F reports pointer travel as `delta`; past a few pixels it was a camera turn, not a tap.

**Escape** closes whatever overlay is open. Nothing else may claim it.

### Navigation

Defined in `WORLD_BRIEF.md`, and non-negotiable in one respect: **a stranger must be able to reach every surface and open every door, on any device.**

- **Looking** — drag-to-look: pointer events on the canvas, yaw and pitch on refs, pitch clamped short of vertical. **One model on desktop and touch.** Do not use `PointerLockControls`: it contests `Escape` with the overlays, hides the cursor needed for clicking doors, and must be released and recaptured around every locked view.
- **Walking** — camera-relative, flattened to the ground plane. WASD and arrows on desktop, an on-screen stick on touch, both feeding one intent vector.
- **Eye height** from `WORLD_BRIEF.md`.
- **Collision** — bounding boxes on anything solid, added as objects are placed, **plus a hard clamp on the walkable bounds.** Boxes leak at corners; a clamp cannot. Scenery the visitor can pass through is fine and cheaper.
- **The guided path** — a "next stop" route gliding between the showcase surface and the two stations, so a visitor who does not want to walk still reaches everything. Reuses the locked-view camera; keep that general (target pose, ease, release).
- **No physics engine.** A capsule and a clamp are enough.

### Reduced motion

Honour `prefers-reduced-motion: reduce` everywhere. A first-person world owes this more than a scroll page does, because the whole camera moves.

- No camera easing, no head bob, no entry sweep — cut straight to control.
- Locked view and guided path move without acceleration, or jump.
- Ambient motion calms; post-processing flourishes come off.
- **Everything remains fully reachable.** Reduced motion never means reduced access.

Build it in with the movement code, not as a retrofit.

### The showcase surface

- Generated from `CONTENT.md`, **never hardcoded.** One renderer, rendered per project; nothing on it tied to a particular project.
- Adding, removing or replacing a project in `CONTENT.md` must change it with **no component edits.** If it requires touching a component, the abstraction is wrong.
- A **door** opens the project's live URL in a new tab.
- The screenshot from `CONTENT.md` is the image. **Dim it below the bloom threshold** — a bright image on an emissive surface blows through the knee and becomes a white smear that lights the whole scene.
- Anything live **fails quietly** — a resting state or the fallback image on slow fetch, rate limit or downtime. Never a spinner, an error, or a blank white plane.

### Structure

```text
app/
  layout.tsx
  page.tsx                  // dynamic import of World, ssr: false
components/
  world/
    World.tsx               // the single <Canvas>
    Ground.tsx
    Atmosphere.tsx          // fog, lights, environment
    Effects.tsx             // post-processing
    Surroundings.tsx        // scenery — carries no content
    Showcase.tsx            // the projects
    Stations.tsx            // bio + contact
  player/
    Player.tsx              // movement + clamp
    Camera.tsx              // drag-to-look, locked view, guided path
  ui/                       // 2D overlay only
    Gate.tsx                // entry gate + audio unlock
    Nav.tsx
    Overlays.tsx
    ControlsHint.tsx
lib/
  world.ts                  // typed mirror of WORLD_BRIEF.md
  store.ts                  // mode machine
  stations.ts               // registry
  audio.ts
  textures/                 // canvas painters
shaders/
```

### Build discipline

**Plan Mode when the parts interlock; a direct prompt when the work is linear.** The test is not how big the task is — it is whether getting the order or the boundaries wrong costs an afternoon. A controller where two input paths must converge, or a store other code already gates on, earns a plan. A list of scenery props does not.

**Scope every plan to the task, never to the project.** This file and `WORLD_BRIEF.md` both describe the whole world; an unscoped request returns a plan for the finished site that nobody can review. Name what is in scope, name what is explicitly out, and ask for something checkable — the file list and build order, or the component boundaries and where state lives.

Then work in verifiable batches. **Group by category, not by object** — everything on the facades in one pass, everything loose in another — and verify the whole group in the preview before starting the next. Never batch a step whose purpose is reacting to the one before it.

On bulk scenery, a full audit pass is usually not worth its time; save that rigour for anything the visitor can interact with or collide against.

One task per conversation.

Commit the brief and the code together. They describe the same world and should move as one thing.

The order is not arbitrary:

1. **Shell** — ground, atmosphere basics, layout and walkable bounds.
2. **Navigation** — walking, looking, clamp, reduced motion. Built _before_ the scenery, so everything after is judged the way a visitor meets it: at eye height, while moving.
3. **Surroundings** — bulk work, reviewed as a diff. Bounding boxes go on as things are placed.
4. **Atmosphere** — the effects pass. Measure the frame budget here, once the scene is visually complete.
5. **Content** — the showcase surface, then the stations. The guided path lands with the stations, since it needs destinations.
6. **Threshold** — gate, sound, controls hint.

Nothing has a look of its own; it has a look in _that_ world, next to its neighbour, at eye height — which cannot be judged until all three exist.

### Performance

The frame budget lives in `WORLD_BRIEF.md`. **Test on a mid-range phone, not the development machine.**

Turn things down in this order:

1. **Reflection resolution** — the single biggest dial
2. **Post-processing passes**
3. **Full-screen effect density** — halve on mobile as a matter of course
4. **Draw calls** — instance harder before deleting anything
5. **Shadow maps** — off unless the brief calls for them

Also cap device pixel ratio on mobile.

**On weight, be honest.** A 3D world ships megabytes. Split the engine into its own chunk so it caches independently of application code, and let the entry gate cover the wait.

### Never

- Never invent or hardcode an atmospheric value — read it from `WORLD_BRIEF.md`
- Never `setState` inside `useFrame`
- Never allocate inside a frame loop
- Never mount a second Canvas, or server-render the Canvas
- Never add a dependency without asking
- Never use placeholder content — names, links and screenshots come from `CONTENT.md`
- Never hardcode a project into the showcase surface
- Never put content in the surroundings
- Never let the visitor move while an overlay is open
- Never let a station listen for the interact key itself
- Never use `PointerLockControls` — `Escape` belongs to the overlays
- Never ship motion that ignores `prefers-reduced-motion`
- Never ship without walking the world on a phone

### Definition of done

- **Structural** — showcase surface, both stations and surroundings present and to the brief; every door works
- **Consistency** — this file's rules held throughout; `three` pinned with its reason
- **Visual** — the render matches `WORLD_BRIEF.md`, checked against the document _and_ by walking it
- **Content** — every project matches `CONTENT.md`; adding or removing one changes the world with no component edit; nothing has leaked into the surroundings
- **Code** — frame budget met on a real phone, reduced motion honoured, the showcase fails gracefully, collision and clamp hold, the guided path reaches everything

Plus the test that decides whether any of it works: **a stranger, on a phone, finds your contact details in ten seconds without walking anywhere.**
