# QNet API for mod developers

API version **1** · package `dev.cptgummiball.qnet.api` · mod id `qnet`

**Most mods need nothing from this page** if QNet uses the standard loader interfaces.

## Getting the API

`qnet-api-1.jar` and `qnet-api-1-javadoc.jar` from the [releases](../../../releases),
as a **compile-only** dependency. Do not ship the API or any QNet files in your mod. If
QNet is optional for your mod, check that the mod `qnet` is loaded before touching its
classes.

The license (section 5) allows compiling against the API and publishing mods that use it.

## What the API offers

The full overview with examples is on the wiki page
[For Mod Developers](https://github.com/CptGummiball/QNet/wiki/For-Mod-Developers). Since QNet 1.1.0 addons can also
build their own multiblocks on QNet's validation, get machine windows drawn by QNet, use QNet's upgrade rules and
telemetry, offer energy, items, fluids and gases of single-block machines on every side through QNet's loader
adapters, let QNet's gas burners burn their fuel gases, respect claims through QNet, and add pages to the in-game codex ([codex format 1](codex-format-1.md)).
