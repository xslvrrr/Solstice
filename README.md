# Solstice

A Complementary-style "shader pack" for Roblox, in a single LocalScript.

Roblox doesn't let scripts run custom GLSL, so Solstice isn't a real fragment
shader. It drives everything the engine does expose (Lighting, Atmosphere,
Clouds, Sky, post-processing, Terrain water, local lights, part reflectance,
particles, beams, sound effects and a screen overlay) from one simulation. That
gets close to the look of Complementary Unbound with Euphoria Patches. It also
includes an in-game settings panel, saved and shareable configs, themes,
performance tiers and an adaptive quality governor.

## Features

| Tab | What it covers |
| --- | --- |
| **Atmosphere** | Dynamic time-of-day lighting with sun strength and night brightness, time-of-day colour, realistic (Future) lighting on request, dynamic or fixed clock, a custom atmosphere (fog, haze, sun glare, fog tint, morning mist, cave fog), light shafts, custom clouds (cover, thickness, tint, cloud shadows), custom stars, the Milky Way, shooting stars and meteor showers in any colour, and a full-sky aurora (crisp flickering rays over a soft curtain glow) with five palettes or your own gradient |
| **World** | Clear, rain, storm and snow weather, weather rotation with your own frequency and chances, wind, lightning with visible forked bolts, rainbows after rain or always, rain that keeps falling outside while you shelter, wet glossy surfaces, puddles (in rain or always; size, shine) with raindrop ripples, ground splashes, snow cover on terrain, fireflies and dust motes, indoor dust caught by the light, seasonal grass tints, a custom sun path and shadow softness |
| **Materials** | Custom sky reflections and ambient, a sheen for every material that goes all the way to a mirror, **real reflections** of the world and players in puddles and across every glossy floor in view, shadows from local lights, light intensity, flicker and warmth, **Future lights** (a flashlight or lantern, glowing neon, lightning that lights the world and throws shadows, bounce light), light halos and contact shadows for Voxel and ShadowMap, terrain water styles or a custom water colour, and an underwater shader (fog, lens warp, bubbles, muffled audio) |
| **Camera** | Bloom, lens flare, motion blur, replacing the game's own effects, depth of field with adaptive autofocus, auto exposure, tone mapping, Lightroom-style grading (exposure, contrast, highlights, shadows, whites, blacks, clarity, dehaze, vibrance, saturation, white balance, three colour wheels) with 16 presets, a Lightroom-style HSL colour mixer (masked to the colours you move) with smooth hue wheels and 15 LUTs, vignette, film grain, lens droplets and a speed FOV kick |
| **Fun** | Filters (Noir, Sepia, Dream, Vapor, Night vision, Frostbite, Inferno), a hue cycle through the rainbow or your own gradient, tilt-shift, cinematic bars, camera wobble, a replacement mouse cursor and shift-lock cursor, avatar effects (colour cycle through a gradient, reflectance, material, ghost, headless, Korblox legs) for you or everyone, time-lapse and sun/moon size |
| **Configs** | Save the look under a name, then load, update, rename or delete it (each asks first); share a look as a code and import other people's; your recent colours and gradient presets |
| **Settings** | The dock's edge (right, left, top or bottom), theme presets (Dark, Midnight, Graphite, Light, Paper), accent colour, UI tint, contrast, the panel key (press any key to bind it), adaptive quality and more |

## Installation

### Paste it into Studio

1. In the Explorer, open **StarterPlayer → StarterPlayerScripts**.
2. Insert a **LocalScript** and name it `Solstice`.
3. Paste the whole of [`SolsticeShaders.client.luau`](SolsticeShaders.client.luau) into it.
4. Press **Play**.

It also works from **StarterGui** or **StarterCharacterScripts**. On its first
run it moves itself into PlayerScripts, so dying and respawning never reset it.
Only one copy ever runs at a time.

Then open section 0 at the top of the script, the [control panel](#for-developers),
to choose which features players get and what they start with.

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

[`SolsticeShaders.exploit.luau`](SolsticeShaders.exploit.luau) is the same
client-side cosmetic shader pack for executor environments. It only renders:
it reads no game state and touches no other players. Load it with:

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/xslvrrr/Solstice/refs/heads/main/SolsticeShaders.exploit.luau"))()
```

or paste the whole file into the executor and run it. What differs from the
LocalScript:

- **Hot reload.** Running it again unloads the previous copy first (restoring
  the place's lighting).
- **Everything saves to file.** The live settings, every config, your recent
  colours and your gradient presets are JSON files in the executor's workspace
  folder (`Solstice/` by default, `CONFIG.ConfigFolder`), so they survive
  rejoins. Without a file API it falls back to the session.
- **Copy buttons** in *Configs → Share* use the executor's clipboard.
- **Console API.** `getgenv().SolsticeShaders` exposes `saveConfig(name)`,
  `loadConfig(name)`, `deleteConfig(name)`, `listConfigs()`, `exportLook()`
  and `unload()`.
- It parents its interface through `gethui()` / `CoreGui` when available, so a
  respawn or a GUI-resetting game doesn't take it down.

## Using it

- **On launch** the Solstice island opens at the screen edge: a sun rises over
  a horizon, the name writes itself in, you're greeted for the time of day and
  shown the panel key. Then the island folds itself into the dock.
- **Open the dock:** move the pointer to the middle of the dock's screen edge
  (the right one by default), or press **F6**. The key also works in first
  person, where the mouse is locked. To change it, click *Settings →
  Behaviour → Panel key* and press the key you want (Escape keeps the old
  one). On touch screens, tap the edge.
- **Open a section:** click an icon and the dock stretches into a settings
  panel. The sub-tabs under the header switch between that section's parts,
  for example *Camera → Lens / Focus / Exposure / Grading / Mixer / Film*.
  Options too long to sit side by side (like *Real reflections*) roll past one
  at a time with the arrows.
- **Close it:** click the active icon again, the close button, or anywhere in
  the game world.
- **Under the divider:** *Settings* (the panel's edge, theme and keys), and the
  power icon, which turns every effect off and restores the place's own
  lighting.
- **Right-click** a saved config or a gradient preset for more options.
- **Hover** the coloured cost pips beside a setting, the load bar or the
  lighting chip in the header, or the frame rate in the footer, to see what
  they mean.
- **Frame rate:** the bottom-left of the panel footer shows it, with a dot
  that is green at your target frame rate, yellow a little under and red well
  under.

Settings survive respawns and script restarts during a session.

### From vanilla to anything

Every setting starts at a value that leaves the place exactly as Roblox
renders it. Solstice runs but touches nothing until you change something, so
the base config makes no difference. That includes the place's own Atmosphere,
Sky and Clouds, its wind, its post effects, its lighting style and the mouse
cursor: each is only taken over while a setting asks for it, and handed
straight back after.

From there each feature goes as far as you like; most ranges run well past
what looks natural. **Reset** in the panel footer puts the look back to vanilla
(it asks first; your configs and interface settings stay).

### Configs

*Configs → Saved*: type a name and **Save** the look. Each saved config can be
loaded, updated with the current look, renamed or deleted, and each of those
asks for confirmation first. A config holds the look only, not the Settings
tab.

*Configs → Share*: **Copy** the current look as a code (one line of text) and
send it to anyone; **Import** pastes someone else's code, names it and offers
to load it.

### Reflections

*Materials → Sheen* is Part.Reflectance: every material reflects the sky, up
to a perfect mirror. *Materials → Mirrors → Real reflections* is the other
kind: real reflections of the structures and players around you.

- **Puddles:** puddles reflect what is around and above them.
- **Floors:** also every glossy floor in view, as glossy as their material
  (glass, marble and tiles strongly, plain plastic and wood a little, grass
  and sand not at all), their Reflectance and the rain make them. *Floor
  gloss* scales it.

Floors are found by a fan of rays falling from points around and ahead of
your character. The glossy surfaces they land on are grouped by height, so a
floor made of many tiles is one floor, and the two best groups reflect. The
rays start from your character rather than the camera, so jumping changes
nothing.

*Reflection detail* is a level of detail, not a count:

- **Near things** are copied in full, with their meshes and decals.
- **Further ones** become plain blocks of their size and colour.
- **Small things** drop out first as they get further away.

Higher detail keeps more, further out, and lets floor reflections reach
further (see below). A reflection never contains another reflection. *Fun →
Avatar → Reflectance* is the plain sky sheen.

How it works: Roblox can only draw a scene twice through a ViewportFrame, so
each surface gets a ViewportFrame on a SurfaceGui holding mirrored copies of
the nearby structures (by level of detail, see above) and
players, with a camera aimed so each reflected point lands where your eye's
ray meets the surface. A ViewportFrame only draws when its GUI lives under
PlayerGui (or the core GUI), so the SurfaceGuis sit there and are shown on
their surfaces through `Adornee`. Because a reflection is drawn on the surface
itself, anything in front of it hides it. Terrain, particles and effects
aren't reflected.

One such camera can't cover a floor: its lens can't open wider than 120
degrees, so across its narrow side it reaches only about 1.6 times your eye's
height from the point below your eye. So a floor is drawn as a fan of wedges
around that point, each with its own reflection, with its long reach running
out along the wedge. Narrow wedges reach further, so Solstice picks the wedge
count that reaches the *Reflection range* (within the detail budget). Only the
wedges you can see are drawn, and each reflects only what lies in its
direction. A gradient cuts each wedge exactly along its edge, so neighbours
meet without a seam or overlap. The wedges are fixed to the world, so turning
the camera just brings new ones in at the edges. Reflections fade out over the
last part of the range rather than stopping at an edge. With the camera low
(first person) the fan reaches about 50 studs at the default detail; from a
normal third-person height it covers about 100.
*Distortion* makes the reflection sway at two speeds, bends it slightly as if
the surface weren't quite still, and drifts a soft ripple sheen across it.
*Height fade* fades things as they rise from the surface.

### Colours

Colour settings open a picker inside the row: a saturation/value square and a
hue bar, a hex field, and your recently used colours (scroll sideways). Edits
show live; **Confirm** keeps the colour (and adds it to your recent colours),
while **Cancel**, or closing the row, puts the old one back. Nothing is saved
until you confirm. The grading wheels use a hue/saturation field, since a
wheel only picks a hue and how much of it to add.

Gradient settings (custom auroras, the hue cycle, the avatar colour cycle) open
the gradient editor:

- Click the bar to add a stop, and drag a handle to move it.
- Set the selected stop's exact position, or remove it.
- Pick its colour below.
- **Reverse** or **Distribute** the stops.
- **Save preset** names your gradient. Right-click your own presets to rename
  or delete them.

Like the picker, the editor only keeps what you **Confirm**. Recent colours and
presets last the session in the LocalScript, and survive rejoins in the
executor build (or with [your own storage](#keeping-configs-for-good)).

*Camera → Mixer* works like Lightroom's HSL panel: eight colour bands, each
with a smooth hue wheel (drag round it to choose what that colour becomes),
saturation and luminance. *LUT* fills all of them with a finished look (Teal &
orange, Autumn, Kodachrome, Cyberpunk, Matrix and more); editing any band
afterwards turns it into Custom.

Roblox's only colour post effect is one global tint, saturation and contrast,
so no script can pick out "just the reds" on screen. *Mixer works on* picks
how the mix gets there:

- **Scene colours** (the default) masks it the only way Roblox allows: the
  exact per-colour mix is applied to the scene's own colours (the light, fog,
  clouds, water, lamps, terrain and nearby parts). Only the colours you moved
  change; everything else on screen is left alone. Every original is recorded
  and put back.
- **Whole screen** is a fitted grading layer, which also reaches textures and
  the skybox: a grid of rays samples the colours in view, each sample goes
  through the exact mix, and the colour balance, saturation and exposure that
  best reproduce the result become the layer. Each sample counts for the share
  of the screen it stands for, so a colour that barely appears barely moves
  the screen. The layer only multiplies (tint and exposure, never added
  brightness, which lifts the blacks and flattens the image) and is bounded.
- **Colours + screen** does both.

*Mixer strength* scales every band's shift: at 2x a shift keeps pushing past
the slider's end, as bold as an editor's sliders.

### Cursor

*Fun → Camera → Cursor* replaces the mouse pointer (a dot, cross or circle in
any colour and size, the shift-lock reticle, or the arrow). *Shift-lock cursor*
is chosen separately and applies while the mouse is locked to the centre
(shift lock or first person). So you can keep the arrow in shift lock, or use
the reticle everywhere. While the panel is open, the game's own cursor comes
back.

### Performance

Settings that cost real frame time are *tiers* (Off / Low / … / Ultra). Each
tier has a load score from 0 to 4, shown as coloured pips next to the setting.
The scores add up to the **load meter** in the panel header.

*Settings → Behaviour → Adaptive quality* watches your frame rate. When it drops
below the target, it lowers tiers a step at a time (it never turns them off),
and raises them again once there's headroom. While it's holding tiers down, the
meter shows "eased" and the footer says so.

### Lighting technology

Solstice detects which lighting pipeline is actually running (Future,
ShadowMap, Voxel or Compatibility) and checks your graphics quality level. The
chip in the panel header shows the result. Settings that only exist in Future
lighting, such as shadows from local lights and the Future lights, are locked
with a badge on other pipelines. They don't silently do nothing.

*Atmosphere → Light → Realistic lighting* switches the place to the Realistic
(Future) lighting style when it isn't already using it. The chip then reads
"Future (forced)", every Future-only row unlocks, and the Voxel recreations
(halos, contact shadows) step aside. Switching it off puts the place's own
style back. On Voxel and ShadowMap, *Materials → Voxel* recreates two Future
looks: halos around lamps and contact shadows under characters.

### The interface

*Settings → Layout → Position* puts the dock on any screen edge: right, left,
top (right at the top of the screen, level with Roblox's top-bar buttons) or
bottom. The
panel opens from it, and the reveal zone, magnification and tooltips follow.
On the top and bottom edges the panel is wide and short, with its rows in two
columns.

*Settings → Theme* has five presets: Dark, Midnight, Graphite, Light and
Paper. You can also set:

- **Accent:** the colour of buttons, toggles and highlights.
- **UI tint:** how much the accent colours the panel's own surfaces.
- **Contrast:** how far apart the layers sit, and how strong text is.

Text colours always adapt. Each is checked against every surface it appears on
and pushed lighter or darker until it reaches readable contrast (7:1 for main
text, 4.5:1 for secondary, 3:1 for hints). Text on accent buttons switches
between white and near-black, whichever reads better.

### When something breaks

Every feature runs on its own, so an error in one (a broken avatar, a part
the game deleted mid-frame) never stops the others or switches the shaders
off.

- **A feature that keeps failing** is set aside: it stops running, and what it
  changed is put back where possible.
- **A notification appears** in the corner (bottom right, or top right with
  the dock on the bottom). It names the feature and offers **Turn off** and
  **Retry**.
- **Switching the shaders off and on** retries everything.
- **If a whole frame keeps failing,** the shaders restore the place, switch
  off and tell you, with a button to turn them back on.

Switching off and restoring also run step by step, so one failing step can't
leave the place half restored.

### Korblox legs

*Fun → Avatar → Korblox leg* uses the official Korblox Deathspeaker legs:

- **Right:** the bony right leg.
- **Left:** the left leg from the same bundle.
- **Both:** each leg on its own side.

They come from the official body-part assets: Right Leg 139607718 (meshes
9598310133/38/28, texture 902843398) and Left Leg 139607673 (meshes
9598310131/37/18, texture 902842271), plus the R6 CharacterMeshes. Each R15
piece hangs from the same hip, knee or ankle joint as the player's own and
follows their body scale. A LocalScript can't change a MeshPart's mesh, so the
leg is drawn by parts that follow the real one, which is hidden. Only you see
it.

## For developers

### The control panel

Section 0 at the very top of the script, `CONFIG`, is the control panel.
Nothing below it needs editing to choose features, defaults or storage.

**Switch features off** with `Features`. A key is a tab, a sub-tab or a single
setting. A switched-off setting disappears from the panel and stays at its
default for every player, so you can force a value by giving it a default
below.

```lua
Features = {
	configs = true,          -- the Configs tab
	ui = true,               -- the Settings tab
	fun = false,             -- the whole Fun tab
	["cam.mixer"] = false,   -- the Camera > Mixer sub-tab
	["mat.mirrors"] = false, -- one setting: Real reflections
},
```

Tab ids: `atmo`, `world`, `mat`, `cam`, `fun`, `configs`, `ui`. Setting ids are
`tab.key`, as written in section 7 (for example `Toggle("dynamic", ...)` in the
Atmosphere tab is `atmo.dynamic`).

**Change what players start with** with `Defaults`. Players start there, and
*Reset* returns there.

```lua
Defaults = {
	["atmo.dynamic"] = true,
	["world.weather"] = "Rain",        -- choices and tiers take the option's name
	["cam.bloom"] = "Full",
	["atmo.fogTint"] = "FFD9B0",       -- colours take "RRGGBB" or a Color3
	["ui.side"] = "Left",              -- the dock on the left
	["ui.accent"] = Color3.fromRGB(240, 140, 70),
	["atmo.auroraGradient"] = {        -- gradients take { position, Color3 } stops
		{ 0, Color3.fromRGB(255, 80, 170) },
		{ 1, Color3.fromRGB(90, 255, 150) },
	},
},
```

The fastest way to write that table: set the look up in game, open
**Configs → Share → This look as defaults** and press **Print as defaults**.
The `Defaults` table for exactly that look is printed to the output (F9),
ready to paste. A value that doesn't fit its setting is reported as a warning
at start-up, not silently ignored.

The rest of `CONFIG`:

| Option | Default | Purpose |
| --- | --- | --- |
| `Storage` | `nil` | Where configs are kept (see below). |
| `StartEnabled` | `true` | Start with the shaders on. |
| `ToggleKey` | `Enum.KeyCode.F6` | The panel key players start with (they can bind any other in Settings). |
| `PersistSettings` | `true` | Remember each player's settings across respawns. |
| `MinQualityForFuture` | `4` | Graphics quality level below which Future-only rows are locked. |
| `LoadBudget` | `48` | Sum of load scores that fills the load meter. |
| `ParticleTexture` | built-in smoke texture | Soft round texture used for rain, snow, dust, fireflies, halos, contact shadows and the aurora's glow. |
| `WaterTag` | `"Water"` | CollectionService tag that marks parts as water volumes for the underwater effect. Terrain water is always detected. |
| `IconAssets` | `{}` | Optional image IDs for the dock icons, keyed by tab id (`atmo`, `world`, `mat`, `cam`, `fun`, `configs`, `ui`, `power`). By default the icons are drawn from frames. |

The adaptive quality target and the panel's position, theme and key are
player settings now (the Settings tab), so their defaults go in `Defaults`
(`ui.targetFps`, `ui.side`, `ui.theme`, `ui.toggleKey`, ...). A key setting
takes the key's name, such as `["ui.toggleKey"] = "RightShift"`, or an
`Enum.KeyCode`.

### Keeping configs for good

A LocalScript can't write files, so by default saved configs, recent colours
and gradient presets last the session (on attributes of the player). To keep
them across sessions, give `CONFIG.Storage` four functions that talk to your
server. Paths look like `configs/Night.json`, `library/recent.json` and
`autosave.json`. For example, a RemoteFunction in front of a DataStore:

```lua
-- In CONFIG (section 0 of the LocalScript):
Storage = (function()
	local remote = game:GetService("ReplicatedStorage"):WaitForChild("SolsticeStorage")
	return {
		read = function(path) return remote:InvokeServer("read", path) end,
		write = function(path, text) remote:InvokeServer("write", path, text) end,
		delete = function(path) remote:InvokeServer("delete", path) end,
		list = function(folder) return remote:InvokeServer("list", folder) end,
	}
end)(),
```

```lua
-- A Script in ServerScriptService:
local DataStoreService = game:GetService("DataStoreService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local store = DataStoreService:GetDataStore("SolsticeFiles")
local remote = Instance.new("RemoteFunction")
remote.Name = "SolsticeStorage"
remote.Parent = ReplicatedStorage

-- Every file of a player in one DataStore key, cached while they play and
-- written back when they leave (and every two minutes).
local cache, dirty = {}, {}

local function files(player)
	if not cache[player] then
		local ok, data = pcall(store.GetAsync, store, "player_" .. player.UserId)
		cache[player] = ok and type(data) == "table" and data or {}
	end
	return cache[player]
end

local function flush(player)
	if dirty[player] and cache[player] then
		dirty[player] = nil
		pcall(store.SetAsync, store, "player_" .. player.UserId, cache[player])
	end
end

remote.OnServerInvoke = function(player, action, path, text)
	if type(path) ~= "string" or #path > 120 then
		return nil
	end
	local data = files(player)
	if action == "read" then
		return data[path]
	elseif action == "write" and type(text) == "string" and #text < 100000 then
		data[path] = text
		dirty[player] = true
	elseif action == "delete" then
		data[path] = nil
		dirty[player] = true
	elseif action == "list" then
		local names, prefix = {}, path .. "/"
		for name in data do
			if name:sub(1, #prefix) == prefix and not name:find("/", #prefix + 1, true) then
				table.insert(names, name:sub(#prefix + 1))
			end
		end
		return names
	end
	return nil
end

Players.PlayerRemoving:Connect(function(player)
	flush(player)
	cache[player], dirty[player] = nil, nil
end)
game:BindToClose(function()
	for player in cache do
		flush(player)
	end
end)
task.spawn(function()
	while true do
		task.wait(120)
		for player in cache do
			flush(player)
		end
	end
end)
```

### EditableImage

The generated radial vignette, film grain, soft contact-shadow blob and the
mixer's smooth hue wheels use `EditableImage`. Once the experience is
published, that API has to be allowed in the experience's settings. Without
it:

- The vignette falls back to edge gradients.
- Contact shadows use the stock particle texture.
- The hue wheels are built from short gradient arcs instead.
- The *Film grain* row is locked.

## Leaves no trace

Solstice either creates what it touches (and destroys it when switched off) or
records the original value first and puts it back afterwards (the moment the
setting that needed it is switched off, not only when the shaders stop). That
covers:

- Lighting properties, the clock, and terrain water and material colours.
- The place's Atmosphere, Sky and Clouds, its wind, lighting style and post
  effects.
- Light shadows, brightness and colour.
- Part and avatar reflectance, material, transparency and colour.
- The mouse cursor, ambient reverb and the camera's field of view.

The records are also stored on the instances themselves. If a copy of the
script dies without cleaning up, the next copy restores the place before it
starts. If the game deletes something Solstice made, it rebuilds itself.

## Project layout

```
SolsticeShaders.client.luau   the whole shader pack (one LocalScript)
SolsticeShaders.exploit.luau  the executor build (file-saved configs)
default.project.json          Rojo project: syncs the LocalScript to StarterPlayerScripts
model.project.json            Rojo project: builds the LocalScript as a standalone model
```

The script is split into numbered sections. Search for the banners
(`-- 0. CONTROL PANEL` … `-- 22. BOOT`). The header comment at the top of
the file maps them out.

## Development

The script type-checks cleanly with [luau-lsp](https://github.com/JohnnyMorganz/luau-lsp)
using the Roblox definitions:

```sh
curl -L -o globalTypes.d.luau \
  https://raw.githubusercontent.com/JohnnyMorganz/luau-lsp/main/scripts/globalTypes.None.d.luau
luau-lsp analyze --platform=roblox --definitions=globalTypes.d.luau SolsticeShaders.client.luau
```

The executor build is the LocalScript plus a small set of changes (header,
single-copy guard, GUI parent, file storage, clipboard and console API). It
checks the same way, apart from the executor's own globals (`getgenv`,
`gethui`), which the Roblox definitions don't know about.
