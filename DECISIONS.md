# Decisions

Newest first. Material decisions only.

## D2 — 2026-10-25 — Dev toolchain spine to 1.26.0 (0.1.1)

`.carbon/spine` (the dev toolchain) c7ed462 (spine 1.24.1) → 846e156 (spine 1.26.0). `carbon validate` and `carbon lint` pass on the new pin, including the record-law rules 1.25.0 added (`decisions-append-only` against origin/main, `lab-isolation`). This repo has no `test/run.sh`, so `carbon gate` reports INCOMPLETE (no gate) and spine's new `gate-present` advisory says so; that is recorded here as a gap, not a pass. The family gate in carbon-adk grades this pack.

`carbon install --tool claude` rewrote the generated `.gitignore` block (tool dirs ignored by contents, `.claude/CLAUDE.md` re-included, the lock committable). That is changed tracked output, so the pack moves 0.1.0 → 0.1.1; no skill body changed. The bootstrap shim the bare install writes to `.claude/CLAUDE.md` names `carbon-spine`, not this repo, so it is not committed (a spine defect, routed).

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
