# Infinite Yield UI Base

A stripped-down, customizable UI framework for client-side Roblox Luau runtimes, extracted from [Infinite Yield](https://github.com/EdgeIY/infiniteyield). It keeps the menu, command bar, settings panels and executor compatibility layer, and removes the built-in commands so you can build your own on top.

> **Note:** This project is a derivative of Infinite Yield. See [Credits](#credits) and make sure you follow the original project's license terms before publishing.

---

## Features

- **Command bar** with prefix key, autocomplete, command history and a hover tooltip
- **Slide-up menu** with snappy open/close animations that never close while you drag it
- **Draggable window** with zero-lag, screen-clamped movement (mouse and touch)
- **Settings panel:** theme editor, keybinds, aliases, waypoints, event binds
- **Notifications, chat logs and join logs**
- **Server info window** (`;serverinfo`)
- **Persistent settings** saved to a file in the executor workspace
- **Executor compatibility layer** (`missing()` fallbacks, `cloneref`, `gethui`, `queue_on_teleport`, etc.) so it degrades gracefully on weaker executors
- **Mobile support** with a quick-access button and touch dragging
- **Clean unload** via `;exit` (every tracked connection is disconnected and the GUI destroyed)

---

## Requirements

- A client-side Luau executor with standard UNC-style functions. These are used when available and have fallbacks where possible:
  `gethui`, `cloneref`, `writefile` / `readfile` / `isfile` / `makefolder`, `getcustomasset`, `queue_on_teleport`, `setclipboard`
- File functions are needed to save settings and cache icon assets. Without them the UI still works, but nothing persists.

This project is **not** for Roblox Studio.

---

## Installation

```lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/nondevelopers/Infinite-Yield-UI/main/EgdeUI.luau"))()
```

Or paste the contents of `EgdeUI.lua` straight into your executor.

---

## Usage

| Action | How |
|---|---|
| Focus the command bar | Press the prefix key (default `;`) |
| Run a command | Type it and press Enter |
| Open the menu | Hover over it, or press the prefix |
| Move the menu | Drag the title bar |
| Open settings | Click the gear icon |

### Built-in commands

| Command | Aliases | Description |
|---|---|---|
| `exit` | `close`, `unload` | Closes the UI and unloads the script |
| `print [text]` | `echo` | Prints to the console (F9) and shows a notification, useful for testing |
| `serverinfo` | `info`, `sinfo` | Opens the server info window |
| `hideiy` / `showiy` | `unhideiy` | Hide or show the menu |
| `keepiy` / `unkeepiy` / `togglekeepiy` | | Persist the UI across teleports |
| `discord` | `support`, `help` | Copies the Discord invite |

---

## Customization

Search the source (Ctrl+F) for these tags to jump straight to the right spot:

| Tag | What it controls |
|---|---|
| `[CFG:NAME]` | Menu title, version string, credits text |
| `[CFG:LAYOUT]` | Menu size and starting position |
| `[CFG:ICONS]` | Asset folder and icon files |
| `[CFG:TOPBAR]` | Icon buttons in the menu header |
| `[CFG:SETTINGS]` | Buttons in the settings panel |
| `[CFG:DEFAULTS]` | Default prefix, scale, colors, saved settings |
| `[CFG:SAVEFILE]` | Name of the settings save file (`IY_FE.iy`) |
| `[CFG:DRAG]` | Drag behavior |
| `[CFG:ANIM]` | Slide speeds and hover delay |
| `[CFG:COMMANDS]` | Where to add commands and command-list entries |
| `[CFG:INTRO]` | Startup logo animation |

### Add a command

Register the handler:

```lua
addcmd('hello', {'hi'}, function(args, speaker)
    notify('Hello', 'Hi ' .. (args[1] or speaker.Name))
end)
```

Then add it to the list shown in the menu:

```lua
CMDs[#CMDs + 1] = {NAME = 'hello [name]', DESC = 'Says hello'}
```

### Add a settings button

```lua
local MySetting = makeSettingsButton("My Setting", getcustomasset("infiniteyield/assets/settings.png"))
MySetting.Position = UDim2.new(0, 5, 0, 235)
MySetting.Size = UDim2.new(1, -10, 0, 25)
MySetting.Parent = SettingsHolder
MySetting.MouseButton1Click:Connect(function()
    notify("My Setting", "Clicked!")
end)
```

Raise `SettingsHolder.CanvasSize` if you add rows past the visible area.

### Rename the menu

Change the string at `Title.Text = ...` under `[CFG:NAME]`.

### Connections and cleanup

For connections to global services (input, run loop, players, mouse), use `IYConnect(signal, fn)` instead of `signal:Connect(fn)`. This lets `;exit` disconnect them when the UI unloads.

---

## Project structure

`IY_UI_Services.lua` is a single file, in this order:

1. Executor shims (`missing()` fallbacks)
2. `Services` table and service globals
3. UI construction
4. Settings, saves, popups and `notify()`
5. Command bar, `execCmd`, `addcmd`
6. Editors (keybinds, aliases, waypoints, plugins) and startup

---

## What was removed

To keep this a base rather than a full admin script, the following are intentionally not included:

- The built-in command implementations (movement, ESP, player, tool commands, etc.)
- The command list entries (the list starts nearly empty)
- The plugin loader. `LoadPlugin` is a stub, so plugin files will not load.
- The update check and server announcement popup

---

## Known issues

- This build was assembled by trimming the original script and has had limited testing. If a button errors with `attempt to call a nil value`, the function behind it was probably removed and needs a stub or your own implementation.
- Behavior varies between executors depending on which functions they support.

---

## Disclaimer

Scripts that run in a game client may violate Roblox's Terms of Use and can lead to account action. Use at your own risk. This project is provided as-is, without warranty.

---

## Credits

- **Infinite Yield** by Edge, Zwolf, Moon, Toon, Peyton, ATP and contributors: original UI and code
- Modifications (UI/services extraction, drag fix, animation tuning, `exit` and `print` commands, customization guide): *your name here*

Replace the links, names and placeholders above with your own before publishing.

