# QNet API for mod developers

API version **1** · package `dev.cptgummiball.qnet.api` · mod id `qnet`

**Most mods need nothing from this page** if QNet uses the standard loader interfaces.

## Getting the API

`qnet-api-1.jar` and `qnet-api-1-javadoc.jar` from the [releases](../../../releases),
as a **compile-only** dependency. Do not ship the API or any QNet files in your mod. If
QNet is optional for your mod, check that the mod `qnet` is loaded before touching its
classes.

The license (section 5) allows compiling against the API and publishing mods that use it.
