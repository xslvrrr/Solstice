# Solstice

A Complementary-style "shader pack" for Roblox, in a single LocalScript.

Roblox doesn't let scripts run custom GLSL, so Solstice isn't a real fragment
shader. It drives everything the engine does expose (Lighting, Atmosphere,
Clouds, Sky, post-processing, Terrain water, local lights, part reflectance,
particles, beams, sound effects and a screen overlay) from one simulation. That
gets close to the look of Complementary Unbound with Euphoria Patches. It also
includes an in-game settings panel, performance tiers and an adaptive quality
governor.

## Features

| Tab | What it covers |
| --- | --- |
| **Atmosphere** | Time-of-day light colour, dynamic or fixed clock, day/night cycle, atmospheric fog (with a tint colour) and distance haze, light shafts, morning mist, cave fog, volumetric clouds and cloud shadows, fading stars, the Milky Way, shooting stars and meteor showers in any colour, and a full-sky aurora with five palettes or your own gradient |
| **World** | Clear, rain, storm and snow weather, lightning with visible forked bolts, rainbows as the rain clears, rain that keeps falling outside while you shelter, wet glossy surfaces, puddles with raindrop ripples, ground splashes, snow cover on terrain, fireflies and dust motes, indoor dust caught by the light, seasonal grass tints, and a custom sun path |
| **Materials** | Sky reflections and ambient, a sheen for every material that goes all the way to a mirror, shadows from local lights (Future), light intensity, flicker and warmth, light halos and contact shadows for Voxel and ShadowMap, terrain water styles or a custom water colour, and an underwater shader (fog, lens warp, bubbles, muffled audio) |
| **Camera** | Bloom, lens flare, motion blur, depth of field with adaptive autofocus, auto exposure, tone mapping, Lightroom-style grading (exposure, contrast, highlights, shadows, whites, blacks, clarity, dehaze, vibrance, saturation, white balance, three colour-picker wheels) with 16 presets, a Lightroom-style HSL colour mixer, vignette, film grain, lens droplets and a speed FOV kick |
| **Fun** | Filters (Noir, Sepia, Dream, Vapor, Night vision, Frostbite, Inferno), hue cycle, tilt-shift, cinematic bars, camera wobble, crosshairs in any colour, time-lapse and sun/moon size |

## Installation

### Paste it into Studio

1. In the Explorer, open **StarterPlayer → StarterPlayerScripts**.
2. Insert a **LocalScript** and name it `Solstice`.
3. Paste the whole of [`SolsticeShaders.client.luau`](SolsticeShaders.client.luau) into it.
4. Press **Play**.

It also works from **StarterGui** or **StarterCharacterScripts**. On its first
run it moves itself into PlayerScripts, so dying and respawning never reset it.
Only one copy ever runs at a time.

### Sync with Rojo

The repository is a [Rojo](https://rojo.space) project:

```sh
rojo serve                     # live-sync into StarterPlayerScripts/Solstice
rojo build -o Solstice.rbxl    # or build a place file
```

To build a model you can drop into any place (or publish to the Creator Store):

```sh
rojo build model.project.json -o Solstice.rbxm
```

### Executor build

[`SolsticeShaders.exploit.luau`](SolsticeShaders.exploit.luau) is a standalone
edition for executor environments. It is the same client-side cosmetic shader
pack — it only renders, it reads no game state and touches no other players —
repackaged so it loads from an executor instead of from a LocalScript:

- **Load it** with `loadstring(game:HttpGet("<raw url>"))()`, or paste the
  whole file into the executor and run it. Running it again hot-reloads: the
  previous copy unloads itself (restoring the place's lighting) first.
- **Configs save to file.** Settings are stored as JSON in the executor's
  workspace folder (`Solstice/` by default) instead of on a player attribute.
  **Configs** is a tab in the dock like any other: under *Saved* you name and
  **Save** the current look, then **Load** or **Delete** saved configs later.
  The last one you saved or loaded comes back automatically on launch.
- **Colours and gradients persist.** Recently used colours and the gradient
  presets you save are written to `Solstice/library/`, so they survive
  rejoins. *Configs → Library* lists them and lets you clear or delete them.
- **Console API.** `getgenv().SolsticeShaders` exposes `saveConfig(name)`,
  `loadConfig(name)`, `deleteConfig(name)`, `listConfigs()` and `unload()`.
- It parents its interface through `gethui()` / `CoreGui` when available so a
  respawn or a GUI-resetting game does not take it down, and falls back to
  `PlayerGui` otherwise. When the executor exposes no file API, configs cannot
  be saved to disk and the Configs tab says so; the shaders still run and
  settings persist for the session.

The `CONFIG` block near the top adds `ConfigFolder` (where the files live) and
`AutoLoadLastConfig` (load the last config on launch) on top of the options
below.

## Using it

- **Open the dock:** move the pointer to the middle of the right-hand screen
  edge, or press **F6**. F6 also works in first person, where the mouse is
  locked. On touch screens, tap the right edge.
- **Open a section:** click an icon and the dock stretches into a settings
  panel. The sub-tabs under the header switch between that section's parts,
  for example *Camera → Lens / Focus / Exposure / Grading / Mixer / Film*.
- **Close it:** click the active icon again, the close button, or anywhere in
  the game world.
- **Switch everything off:** the power icon at the bottom of the dock turns
  every effect off and restores the place's own lighting.

Settings survive respawns and script restarts during a session. They're
stored on an attribute of the local player.

### Vanilla to all out

The **Look** slider in the panel footer sets how much Solstice changes:

- **Vanilla** (far left, the default): the place exactly as Roblox renders it.
  The engine doesn't run at all, so the base config makes no difference.
- **Your look** (the tick in the middle): what your settings describe.
  Between Vanilla and here, every value Solstice writes is blended from the
  place's own value towards yours, so the look fades in smoothly.
- **All out** (far right): past the middle, the look settings (fog, haze,
  shafts, bloom, sheen, stars, aurora, grade punch and so on) are pushed
  towards their strongest. They're never lowered, and the rows still show what
  you set. Performance tiers aren't touched; pair it with the Ultra profile.

### Colours

Colour settings open a picker inside the row: a saturation/value square and a
hue bar, a hex field, and your recently used colours. The grading wheels use a
hue/saturation field, since a wheel only picks a hue and how much of it to add.

*Atmosphere → Aurora → Colours → Custom* adds a gradient editor: click the bar
to add a stop, drag a handle to move it, pick its colour below, and save the
result as a preset. Recent colours and saved gradients last the session in the
LocalScript and survive rejoins in the executor build.

*Camera → Mixer* works like Lightroom's HSL panel: eight colour bands, each
with a hue shift, saturation and luminance. Roblox has no per-pixel hue
remapping, so the mixer recolours the scene itself: the light, fog, clouds,
water, lamps and terrain, and (with *Recolour parts* on) parts around you.
Textures and decals keep their colours. Every original is recorded and put
back when you reset the mixer or switch off.

### Performance

Settings that cost real frame time are *tiers* (Off / Low / … / Ultra). Each
tier has a load score from 0 to 4, shown as coloured pips next to the setting.
The scores add up to the **load meter** in the panel header.

- **Profiles:** Low, Med, High and Ultra in the footer set every tier at once.
- **Adaptive:** when this is on, the governor watches your frame rate. If it
  drops below the target, the governor lowers tiers a step at a time (it never
  turns them off). It raises them again once there's headroom. While it's
  holding tiers down, the meter shows "eased".

### Lighting technology

Solstice detects which lighting pipeline is actually running (Future,
ShadowMap, Voxel or Compatibility) and checks your graphics quality level. The
chip in the panel header shows the result. Settings that only exist in Future
lighting, such as shadows from local lights, are locked with a badge on other
pipelines. They don't silently do nothing. On Voxel and ShadowMap, *Materials →
Voxel* recreates two Future looks: halos around lamps and contact shadows under
characters.

By default the script asks the engine for the Realistic (Future) lighting
style when the place isn't already using it. It puts the place's own style
back when it stops. Set `RequestRealisticLighting = false` to turn this off.

## Configuration

The `CONFIG` table near the top of the script (section 2) holds the settings
a developer is most likely to change:

| Option | Default | Purpose |
| --- | --- | --- |
| `StartEnabled` | `true` | Start with the shaders on. |
| `DefaultLook` | `0` | Where the Look slider starts (and where Reset puts it): `0` is Vanilla, `0.5` your look, `1` all out. Set `0.5` if players should see the shaders without touching the slider. |
| `ToggleKey` | `Enum.KeyCode.F6` | Key that opens and closes the panel. |
| `SuppressGameEffects` | `true` | Switch off the place's own post effects (Bloom, ColorCorrection, …) while Solstice runs, so they don't stack with its own. They're switched back on afterwards. |
| `RequestRealisticLighting` | `true` | Ask for the Realistic (Future) lighting style, as described above. |
| `MinQualityForFuture` | `4` | Graphics quality level below which Future-only rows are locked. |
| `AdaptiveTargetFps` | `50` | Frame rate the Adaptive governor tries to hold. |
| `LoadBudget` | `48` | Sum of load scores that fills the load meter. |
| `ParticleTexture` | built-in smoke texture | Soft round texture used for rain, snow, dust, fireflies, halos and contact shadows. |
| `WaterTag` | `"Water"` | CollectionService tag that marks parts as water volumes for the underwater effect. Terrain water is always detected. |
| `PersistSettings` | `true` | Remember each player's settings across respawns. |
| `IconAssets` | `{}` | Optional image IDs for the dock icons, keyed by tab id (`atmo`, `world`, `mat`, `cam`, `fun`, `power`, and `configs` in the executor build). By default the icons are drawn from frames. |

### EditableImage

The generated radial vignette, film grain and soft contact-shadow blob use
`EditableImage`. Once the experience is published, that API has to be allowed
in the experience's settings. Without it, the vignette falls back to edge
gradients, contact shadows use the stock particle texture and the *Film grain*
row is locked.

## Leaves no trace

Solstice either creates what it touches (and destroys it when switched off) or
records the original value first and puts it back afterwards. That covers
Lighting properties, the clock, terrain water and material colours, the
place's Atmosphere, Sky and Clouds, light shadows, brightness and colour, part
reflectance and colour, ambient reverb and the camera's field of view. The records are
also stored on the instances themselves. If a copy of the script dies without
cleaning up, the next copy restores the place before it starts. If the game
deletes something Solstice made, it rebuilds itself.

## Project layout

```
SolsticeShaders.client.luau   the whole shader pack (one LocalScript)
SolsticeShaders.exploit.luau  the executor build (file-saved configs + UI)
default.project.json          Rojo project: syncs the LocalScript to StarterPlayerScripts
model.project.json            Rojo project: builds the LocalScript as a standalone model
```

The script is split into numbered sections. Search for the banners
(`-- 1. SERVICES & GUARDS` … `-- 22. BOOT`). The header comment at the top of
the file maps them out.

## Development

The script type-checks cleanly with [luau-lsp](https://github.com/JohnnyMorganz/luau-lsp)
using the Roblox definitions:

```sh
curl -L -o globalTypes.d.luau \
  https://raw.githubusercontent.com/JohnnyMorganz/luau-lsp/main/scripts/globalTypes.None.d.luau
luau-lsp analyze --platform=roblox --definitions=globalTypes.d.luau SolsticeShaders.client.luau
```

The executor build checks the same way, apart from the executor's own globals
(`getgenv`, `gethui`), which the Roblox definitions don't know about.
