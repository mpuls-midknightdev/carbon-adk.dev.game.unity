---
name: unity-version-control-hygiene
description: >
  Keep a Unity project mergeable — .meta files are the identity system and must
  travel with their asset, Library/ is derived and never committed, and scenes
  and prefabs need force-text plus deliberate ownership because they merge badly
  even when git says they merged.
version: 0.1.0
importance: core
keywords: [unity meta files in git, unity gitignore what to commit, scene merge conflicts unity, prefab merge conflict, unity library folder version control, missing script references after pull, unity asset guid changed, why did my references break, unity smart merge]
queries: ["what does a Unity project need in .gitignore and why", "our scene merges keep silently breaking references, what is the discipline"]
references: [unity-build-pipeline, carbon-adk.dev.game/game-runtime-loop]
---

# Unity version control: the .meta file IS the reference

Unity does not reference assets by path. It references them by the GUID inside
the asset's `.meta` file. Every "the reference broke and I don't know why" bug
traces back to that one fact.

**The law.** A `.meta` file is not metadata — it is identity. It is committed
with its asset, in the same commit, always. An asset without its meta gets a new
GUID on the next import, and every reference to it silently becomes None.

## What is committed and what is derived

Committed: `Assets/` including every `.meta`, `Packages/manifest.json` and
`Packages/packages-lock.json`, `ProjectSettings/`.

Derived, never committed: `Library/`, `Temp/`, `Obj/`, `Build/`, `Logs/`,
`UserSettings/`, and the IDE project files Unity regenerates. This is the same
three-bucket split every package manager makes — `Packages/manifest.json` is the
declaration, `packages-lock.json` is the lock, `Library/PackageCache/` is the
cache. Delete `Library/` and Unity rebuilds it; that is the test for whether
something belongs there.

## Rules that prevent the expensive bugs

- **Never move or rename assets outside the editor.** The editor moves the meta
  with the asset; a shell `mv` does not, and the GUID is lost.
- **Force text serialization**, and enable Unity's merge tool for scenes and
  prefabs. Without it, YAML merges that git reports as clean are routinely
  semantically broken.
- **A clean git merge on a scene is not a working scene.** Open it. This is a
  case where the version control system genuinely cannot verify the result, so a
  human must.
- **Own scenes and prefabs deliberately.** Two people editing one scene is a
  process problem, not a tooling problem — split the scene, or serialize access.
  Large shared scenes are the single biggest source of lost work in Unity teams.
- **Commit `packages-lock.json`.** It is the lock; excluding it makes every
  machine resolve independently and turns "works on mine" into a real answer.

## The recovery that is not a recovery

When references break after a pull, the instinct is to reassign them by hand.
That fixes the symptom and hides the cause — usually a meta that did not travel
or an asset moved outside the editor. Find which, fix that, and re-pull. Hand
reassignment on a shared branch propagates the damage.
