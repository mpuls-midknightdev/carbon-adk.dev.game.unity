---
name: unity-build-pipeline
description: >
  Make Unity builds reproducible and diagnosable — the editor is not the build,
  scripting defines and backend differ per target, content addressing must be
  decided before content exists, and a build that only one machine can produce
  is not a build.
version: 0.1.0
importance: common
keywords: [unity build reproducible, works in editor but not in build, il2cpp vs mono differences, unity scripting define symbols, addressables vs resources folder, unity build automation headless, unity player build from command line, stripping broke my build]
queries: ["it works in the Unity editor but fails in a player build, how do I stop that class of bug", "how should Unity content loading be structured before the project gets large"]
references: [unity-determinism, unity-version-control-hygiene, carbon-adk.dev.game.platform/platform-abstraction-seam]
---

# Unity builds: the editor is not the target

The editor runs managed code with full reflection, every asset loaded, and no
stripping. A player build may use a different scripting backend, strip code it
believes is unused, and load content through an entirely different path.
"Works in the editor" is therefore evidence about the editor and nothing else.

## The differences that actually bite

- **Scripting backend.** IL2CPP compiles ahead of time. Reflection over types
  never referenced statically, dynamic code generation, and some generic
  instantiations resolve in Mono and fail in IL2CPP — at runtime, on device.
- **Managed stripping.** The linker removes what it cannot see referenced.
  Anything reached only by name or reflection must be preserved explicitly, and
  the failure appears as a null far from the cause.
- **Scripting define symbols.** Per-target defines are a fork in the source. They
  belong behind the platform seam
  (`carbon-adk.dev.game.platform/platform-abstraction-seam`), not scattered
  through gameplay — a define in gameplay code is the same defect as a platform
  conditional in gameplay code.
- **Content loading.** How assets are addressed determines what can be patched,
  what ships, and what loads at startup. It is an architectural decision that is
  cheap before content exists and very expensive after.

## Rules

- **The build runs headless, from a command, on a clean checkout.** A build only
  one workstation can produce depends on that workstation's state — and the
  first time that matters is when the person who owns it is unavailable.
- **Build the real target early and often.** The first IL2CPP build of a mature
  project surfaces months of accumulated assumptions at once. The tenth surfaces
  one day's worth.
- **A build failure is a defect and closes with a gate check**
  (`carbon-adk.dev/gate-first-testing`). Stripping and backend failures are
  classes, not instances — covering the class means the next one is caught by
  the build, not by a tester.
- **Version the build output.** A build that cannot say which commit produced it
  cannot be bisected, and every bug report against it is guesswork.
