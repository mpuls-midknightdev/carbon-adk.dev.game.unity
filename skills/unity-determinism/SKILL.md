---
name: unity-determinism
description: >
  Get a deterministic simulation out of Unity's callback model — simulation runs
  in FixedUpdate on a fixed timestep, presentation interpolates in Update, and
  nothing that affects state may read Time.deltaTime, script execution order, or
  physics results from the same frame it wrote them.
version: 0.1.0
importance: core
keywords: [unity fixedupdate vs update, unity determinism, physics inconsistent between runs, unity script execution order problems, time deltatime in gameplay logic, replay desync unity, frame rate affects gameplay unity, unity fixed timestep]
queries: ["my Unity gameplay behaves differently at different frame rates, what is the rule", "how do I make a Unity simulation reproducible enough to replay"]
references: [unity-build-pipeline, carbon-adk.dev.game/game-runtime-loop, carbon-adk.dev.game/experiential-game-qa]
---

# Determinism in Unity: simulation is not presentation

`carbon-adk.dev.game/game-runtime-loop` states the law — fixed timestep, one
code path, live ≡ replay ≡ headless by hash. Unity's callback model makes that
law easy to break by accident, in three specific ways.

## The three leaks

**1. `Time.deltaTime` inside simulation.** `Update` runs at whatever rate the
frame took. Any state change scaled by it is frame-rate dependent, which means
it is machine dependent, which means it is not reproducible. Simulation belongs
in `FixedUpdate`, where the step is constant. `Update` is for reading input and
drawing — it may interpolate toward simulation state, never mutate it.

**2. Script execution order.** Unity's default order between MonoBehaviours is
unspecified. Logic that works because component A happened to run before B is
correct by luck, and re-orders when someone adds a script. If order matters,
either make it explicit in the project's execution order settings, or — better —
remove the dependency by having one system call its parts in a written order
rather than relying on the engine to schedule them.

**3. Reading physics in the frame that wrote it.** Collision callbacks, raycasts
against just-moved bodies, and transform reads after a write in the same step
all depend on internal ordering. Read simulation state from your own model, not
from the engine's mid-step view of itself.

## The discipline

- **Simulation state is yours, not the scene's.** The scene is a view. When the
  authoritative state lives in plain objects you own, determinism is a property
  you can test; when it lives in Transforms, it is a property you can only hope
  for.
- **One accumulator, catch-up capped.** Fixed steps consumed from an accumulator,
  with a ceiling on steps per frame so a hitch degrades into slow motion rather
  than a spiral.
- **Randomness enters through one door**, seeded and stored with the run. Unity's
  global `Random` is shared state that anything can advance — including editor
  tooling — so a simulation that reads it is not reproducible.
- **Prove it, don't assert it.** Run the same inputs twice and hash the state.
  `carbon-adk.dev.game/experiential-game-qa` treats this as a delivery-surface
  check, not a unit test.

## Why this is worth the discipline

Determinism is not an end in itself — it is what makes replays, network
prediction, automated play-through testing, and reproducible bug reports
possible at all. Every one of those is unavailable to a project that reads
`Time.deltaTime` in its damage calculation.
