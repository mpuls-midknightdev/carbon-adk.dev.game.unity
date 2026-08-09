# Decisions

Newest first. Material decisions only.

## D1 — 2026-08-09 — Founding: the Unity engine layer, at sketch maturity

Spawned from `carbon-adk.dev.game` on the ENGINE axis, which is orthogonal to
the platform axis: a Unity project shipping to console pulls this pack and a
`.platform.*` pack, and neither implies the other.

**Altitude verdict** (`carbon-adk/module-altitude`). The noun test placed all
three skills here decisively — `.meta` files, `FixedUpdate`, IL2CPP and managed
stripping are entities that exist only inside Unity, so none can rise to
`.dev.game`.

The sibling test was the interesting one. `unity-determinism` looks like a
duplicate of `carbon-adk.dev.game/game-runtime-loop`, and would be one if it
restated the fixed-timestep law. It does not: the parent owns the LAW, and this
pack owns the three specific ways Unity's callback model breaks it
(`Time.deltaTime` in simulation, unspecified script execution order, same-frame
physics reads). Cite, never re-cover — restating the parent's law here would
create two sources of truth drifting under bump-to-change.

**Maturity: `sketch`.** Three laws that recur on every Unity project we have
run, but no outside reader and no falsifier beyond the family gate. Raised by
real use, ruled on by `carbon-adk.dev/ratify`.

**Ancestry** per `carbon-adk` D14: floor in `requires:`, is-a edge in
`submodules:`, unpinned coordinates in `sources:` for every ancestor plus the
harness.
