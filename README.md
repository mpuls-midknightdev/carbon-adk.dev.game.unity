# carbon-adk.dev.game.unity

Unity-specific laws: the `.meta` file as the identity system, determinism under
Unity's callback model, and builds that are reproducible off one machine.

**Maturity: `sketch`** (`carbon-adk.dev/proving-ground`). Founded with the three
laws that recur on every Unity project regardless of genre. No outside reader
yet; depth accrues from real use rather than from filling the shelf.

Part of the [carbon-adk family](https://github.com/mpuls-midknightdev/carbon-adk);
repo name == manifest namespace.

## Skills

| skill | what it carries |
|---|---|
| `unity-version-control-hygiene` | `.meta` IS the reference, not metadata; what is committed vs derived; a clean scene merge is not a working scene |
| `unity-determinism` | simulation in FixedUpdate, presentation in Update; the three leaks — deltaTime, execution order, same-frame physics reads |
| `unity-build-pipeline` | the editor is not the target; backend, stripping, defines and content addressing; a one-machine build is not a build |

## Ancestry

Parent is `carbon-adk.dev.game` — the game-development process laws this pack
specializes. Pulling this brings `.dev.game`, `.dev`, `carbon-adk` and the
harness; it brings no siblings, so `.platform` and `.unreal` stay opt-in.

Engine and platform are **separate axes**. A Unity project shipping to console
pulls this pack AND the relevant `.platform.*` module; neither implies the other.

## Consuming

```bash
node .carbon/synapse/bin/carbon-synapse --root . --tool claude
```

## Dev toolchain

`.carbon/spine` (submodule, dev-only):
`NODE_OPTIONS='--experimental-strip-types' node .carbon/spine/bin/carbon validate|lint`
