# Carbon Scripting API

Documentation for custom Lua scripts.

## Contents

- [Getting started](#getting-started)
  - [Quick start](#quick-start)
- [carbon](#carbon)
- [console / print / log](#console--print--log)
- [debug](#debug)
- [notify](#notify)
- [menu](#menu)
- [script](#script)
- [events](#events)
- [session](#session)
- [globals](#globals)
- [locals](#locals)
- [stats](#stats)
- [hud](#hud)
- [natives](#natives)
  - [1. Typed namespaces (best default)](#1-typed-namespaces-best-default)
  - [Typed native argument types](#typed-native-argument-types)
  - [Typed native return types](#typed-native-return-types)
  - [2. Invoke.Lua (reusable raw calls)](#2-invokelua-reusable-raw-calls)
  - [3. native_call (one-off raw calls)](#3-native_call-one-off-raw-calls)
  - [Which method should I use?](#which-method-should-i-use)
- [native_call](#native_call)
  - [Return type codes](#return-type-codes)
  - [Argument types](#argument-types)
  - [Examples](#examples)
  - [Common mistakes](#common-mistakes)
  - [Errors](#errors)
- [scan](#scan)
- [hook](#hook)
  - [Native hooks](#native-hooks)
  - [Detour hooks](#detour-hooks)
- [memory](#memory)
- [player](#player)
- [players](#players)
- [ped](#ped)
- [entity](#entity)
- [vehicle](#vehicle)
- [streaming](#streaming)
- [util](#util)
- [keybind](#keybind)
- [input](#input)
- [draw](#draw)
- [dx / directx](#dx--directx)
- [imgui](#imgui)
- [lang](#lang)
- [fs](#fs)
- [http](#http)
- [carbon.feature](#carbonfeature)
- [business](#business)
  - [Cargo & warehouses](#cargo--warehouses)
  - [Factories, hub, hangar](#factories-hub-hangar)
  - [Safes, CEO/MC, teleport](#safes-ceomc-teleport)
- [Example script](#example-script)

*Developer documentation*

**Build powerful scripts for GTA Online**

Carbon provides a complete Lua API for menus, gameplay automation, session tools, drawing, networking, and more. Load your script from **Settings → Custom Scripts** and press `` ` `` in-game to open the script console.

| Feature | Details |
| --- | --- |
| **Lua 5.4** | Modern scripting with a curated standard library |
| **In-game UI** | Register toggles, sliders, and actions in the menu |
| **Live session data** | Players, entities, stats, and business tools |

## Getting started

Every script starts by loading natives, registering menu options, and optionally running a loop for continuous logic.

> [!TIP]
> **Included:** `base`, `table`, `string`, `math`, `coroutine`, `utf8`
> **Not available:** `io`, `os`, `package`, `debug` — use the `fs` API for file storage instead.

> [!NOTE]
> **Plan limits per script:** up to `512` menu widgets · `8` background threads · `8` event handlers · `8` concurrent HTTP requests.

### Quick start

```lua
util.require_natives()
notify.success("My Script", "loaded")

menu.toggle("Feature", "Runs each tick", function(on)
  _G.feature_on = on
end)

script.register_loop(function()
  if _G.feature_on then
    -- per-tick work
  end
end)
```

## carbon

Read account details and subscription status for the signed-in Carbon user.

#### `carbon.tier()`

Returns the user's subscription tier string.

**Returns** `string` — `"basic"` or `"premium"`

#### `carbon.is_premium()`

Checks whether the current account has premium access.

**Returns** `bool`

#### `carbon.uuid()`

Returns the authenticated user's UUID.

**Returns** `string`

#### `carbon.username()`

Returns the authenticated user's Carbon account username.

**Returns** `string`

```lua
if not carbon.is_premium() then
  notify.warn("Premium", "Requires Premium")
  return
end
print(carbon.tier(), carbon.uuid())
```

## console / print / log

Debug your script with the in-game console. Press `` ` `` to open it. Notifications from `notify.*` also appear here.

| Symbol | Arguments | Returns | Notes |
| --- | --- | --- | --- |
| `print(...)` | Any values (concatenated) | — | Tagged `PRINT` in console |
| `console.log(msg)` | `msg` string, optional | — | Print level |
| `console.info(msg)` | `msg` string, optional | — | Info level |
| `console.warn(msg)` | `msg` string, optional | — | Warn level |
| `console.error(msg)` | `msg` string, optional | — | Error level |
| `console.clear()` | — | — | Clears console buffer |
| `console.open()` | — | — | Opens the in-game console |
| `console.close()` | — | — | Closes the in-game console |
| `console.toggle()` | — | — | Toggles the console |
| `console.is_open()` | — | `bool` | Returns whether the console is visible |
| `console.lines([limit])` | optional max line count | `{ level, source, message }[]` | Reads recent console output |
| `log.info(msg)` | `msg` string, optional | — | Alias for console info |
| `log.warn(msg)` | `msg` string, optional | — | Alias for console warn |
| `log.error(msg)` | `msg` string, optional | — | Alias for console error |

```lua
print("hello", 123)
console.info("loaded")
log.warn("unexpected state")
console.open()
for _, line in ipairs(console.lines(10)) do
  print(line.level, line.source, line.message)
end
```

## debug

Convenience helpers for working with the in-game console from scripts.

| Function | Returns | Notes |
| --- | --- | --- |
| `debug.open_console()` | — | Open the console overlay |
| `debug.close_console()` | — | Close the console overlay |
| `debug.toggle_console()` | — | Toggle the console overlay |
| `debug.is_console_open()` | `bool` | Visibility state |
| `debug.clear_console()` | — | Clear all console lines |
| `debug.console_lines([limit])` | `{ level, source, message }[]` | Read the most recent output |

```lua
debug.open_console()
local lines = debug.console_lines(25)
for _, line in ipairs(lines) do
  if line.level == "error" then
    notify.error(line.source, line.message)
  end
end
```

## notify

In-game toast notifications. Both title and message are optional; default title is `"Script"`.

| Function | Style |
| --- | --- |
| `notify.info(title, message)` | Blue |
| `notify.success(title, message)` | Green |
| `notify.warn(title, message)` | Yellow |
| `notify.error(title, message)` | Red |

```lua
notify.success("Carbon", "Script loaded")
notify.error("Fail", "Could not start mission script")
```

## menu

Add controls to your script's page in **Settings → Custom Scripts**. Register widgets when the script loads — up to `512` per script.

#### `menu.context(name)` / `menu.end_context()`

Temporarily register subsequent controls on an existing Carbon page instead of the script's own page. Controls registered in an `online_player_*` context receive the current selected player ID in their callback. The ID is `-1` when there is no individual target.

Available contexts include:

`self`, `self_weapons`, `self_outfit`, `self_visuals`, `online_player_social`, `online_player_safe`, `online_player_troll`, `online_player_vehicle`, `vehicle`, `vehicle_spawn`, `vehicle_editor`, `vehicle_handling`, `world`, `world_session`, `world_spawn`, `world_traffic`, `recovery`, `protections`, `social`, and `settings`.

The context is shared by both Carbon menu frontends, so a control appears in the matching page regardless of which UI is active. Context controls should be leaf controls (`toggle`, `action`, sliders, combos, groups, or separators); use normal script-page subtabs and folders for navigation.

#### `menu.toggle(label, desc, fn)`

Checkbox row. Callback receives `state: bool`.

In an `online_player_*` context the callback is `fn(state: bool, player_id: int)`.

#### `menu.action(label, desc, fn)`

Button row. Callback receives no args.

In an `online_player_*` context the callback is `fn(player_id: int)`.

#### `menu.slider_int(label, desc, min, max, default, [fn])`

Integer slider. Optional callback `fn(value: int)`. Defaults: min `0`, max `100`, default `min`.

In an `online_player_*` context the callback is `fn(value: int, player_id: int)`.

#### `menu.slider_apply_int(label, desc, min, max, default, fn)`

Left/Right adjusts value; Enter runs `fn(value)`.

In an `online_player_*` context the callback is `fn(value: int, player_id: int)`.

#### `menu.slider_float(label, desc, min, max, default, [fn])`

Float slider. Defaults: min `0.0`, max `1.0`, default `min`.

In an `online_player_*` context the callback is `fn(value: number, player_id: int)`.

#### `menu.toggle_slider_int(label, desc, default_on, min, max, default, [fn])`

Toggle + int slider combo. Callback receives toggle state when changed.

The slider value callback receives `fn(value, player_id)` in an `online_player_*` context; the toggle callback remains `fn(state, player_id)`.

#### `menu.toggle_slider_float(label, desc, default_on, min, max, default, [fn])`

Toggle + float slider combo.

#### `menu.combo(label, desc, items, default, [fn])`

`items` is a string array. Optional `fn(index: int)` — 0-based index.

In an `online_player_*` context the callback is `fn(index: int, player_id: int)`.

#### `menu.subtab(label, [desc])` / `menu.end_subtab()`

Top-level subtabs render as a tab bar; nested subtabs open as pages.

#### `menu.folder(label, [desc])` / `menu.end_folder()`

Nested page container. Prefer subtabs for primary navigation.

#### `menu.separator(label)`

Section header inside the current subtab.

#### `menu.group(label)`

Visual group label.

#### `menu.set_details(title, rows, [tag])`

Details card. `rows` = `{ { label, value } }` or `{ { label = "…", value = "…" } }`. Alias: `menu.details`.

#### `menu.clear_details()`

Removes the details card.

```lua
local enabled = false

menu.subtab("Main", "Primary controls")
menu.toggle("Godmode", "Toggle invincibility", function(state)
  enabled = state
  notify.info("Godmode", state and "on" or "off")
end)
menu.slider_int("Wanted", "0–5", 0, 5, 0, function(v)
  player.set_wanted(v)
end)
menu.set_details("Status", {
  { "Tier", carbon.tier() },
  { label = "Premium", value = carbon.is_premium() },
}, "live")
menu.end_subtab()

-- Add a control to the existing selected-player Troll page.
menu.context("online_player_troll")
menu.action("Example Action", "Performs the action on the selected player.", function(player_id)
  if player_id < 0 then return end
  print("Selected player: " .. tostring(player_id))
end)
menu.toggle("Example Toggle", "Keeps the selected player effect active.", function(state, player_id)
  if player_id < 0 then return end
  print("Toggle for " .. tostring(player_id) .. ": " .. tostring(state))
end)
menu.end_context()
```

## script

Schedule recurring logic, start GTA mission scripts, and run background tasks with delays.

| Function | Arguments | Returns | Description |
| --- | --- | --- | --- |
| `script.register_loop(fn)` | `fn()` | — | Runs every game tick. Alias: `script.loop`. Replaces any prior loop. |
| `script.run(fn)` | `fn()` | `bool` | Starts a background task. Alias: `script.create_thread`. Supports `script.wait`. |
| `script.wait(ms)` | milliseconds | — | Pauses the current background task. Aliases: `script.yield_ms`, `util.wait`, `util.yield_ms`. |
| `script.yield()` | — | — | Pause until the next tick (`wait(0)`). |
| `script.force_host(name)` | script name | `bool` | Take host of a networked script, e.g. `"freemode"`. |
| `script.is_running(name)` | script name | `bool` | Alias: `script.running`. |
| `script.start(name)` | mission script name | `bool` | Starts a networked script, e.g. `"gb_contraband_sell"`. |

> [!NOTE]
> **Note:** `script.wait` must be called inside `script.run` or `script.register_loop` — not at the top level when the script loads.

```lua
script.register_loop(function()
  -- runs every tick
end)

script.run(function()
  script.start("gb_smuggler")
  script.wait(500)
  script.force_host("freemode")
end)
```

## events

Session player join/leave callbacks. Max `8` handlers per script; re-registering the same event name replaces the prior handler.

#### `events.on(name, fn)`

Register an event handler.

| Parameter | Description |
| --- | --- |
| `name` | `"player_join"` — callback `fn(id: int, name: string)`
`"player_leave"` — callback `fn(id: int)` |

```lua
events.on("player_join", function(id, name)
  notify.info("Join", name .. " (" .. id .. ")")
end)
events.on("player_leave", function(id)
  console.log("left", id)
end)
```

## session

Launch or leave online sessions.

#### `session.launch(type)`

Join or create a session. Returns `bool`.

| Constant | Value |
| --- | --- |
| `session.join_public` | `0` |
| `session.new_public` | `1` |
| `session.closed_crew` | `2` |
| `session.crew` | `3` |
| `session.closed_friends` | `6` |
| `session.find_friend` | `9` |
| `session.solo` | `10` |
| `session.invite_only` | `11` |
| `session.join_crew` | `12` |
| `session.sc_tv` | `13` |
| `session.leave_online` | `-1` |

```lua
session.launch(session.invite_only)
session.launch(session.solo)
```

## globals

Read/write script globals by index.

| Function | Returns |
| --- | --- |
| `globals.get_int(index)` | `value: int`, `ok: bool` |
| `globals.set_int(index, value)` | `ok: bool` |
| `globals.get_float(index)` | `value: float`, `ok: bool` |
| `globals.set_float(index, value)` | `ok: bool` |

```lua
local v, ok = globals.get_int(1575044)
if ok then globals.set_int(1575044, v) end
```

## locals

Read and write locals on a named GTA script (e.g. `"freemode"`).

| Function | Returns |
| --- | --- |
| `locals.get_int(script, index)` | `value`, `ok` |
| `locals.set_int(script, index, value)` | `ok` |
| `locals.get_float(script, index)` | `value`, `ok` |
| `locals.set_float(script, index, value)` | `ok` |

```lua
local v, ok = locals.get_int("freemode", 0)
if ok then locals.set_int("freemode", 0, v) end
```

## stats

Read and write character stats. `MPX_` / `MPx_` prefixes automatically resolve to the active character slot.

| Function | Returns | Notes |
| --- | --- | --- |
| `stats.get_int(name)` | `value`, `ok` |  |
| `stats.get_bool(name)` | `value`, `ok` |  |
| `stats.get_float(name)` | `value`, `ok` |  |
| `stats.set_int(name, value)` | `ok` |  |
| `stats.set_bool(name, value)` | `ok` |  |
| `stats.set_float(name, value)` | `ok` |  |
| `stats.get_masked_int(name, offset, length)` | `value`, `ok` | Bitfield read |
| `stats.set_masked_int(name, value, offset, length)` | `ok` | Bitfield write |
| `stats.get_packed_int(index)` | `int` |  |
| `stats.set_packed_int(index, value)` | `true` |  |
| `stats.set_packed_bool(index, value)` | `true` |  |
| `stats.start_being_boss([version])` | `true` | CEO `2`, MC `3` |
| `stats.end_being_boss([reason])` | `true` | Default reason `5` |

```lua
local ch = stats.get_int("MPPLY_LAST_MP_CHAR")
stats.set_int("MPX_SCRIPT_INCREASE_DRIV", 100)
stats.set_packed_bool(12345, true)
```

## hud

GTA feed ticker. Labels are GXT names (e.g. `HNG_TOT_TCK_1`, `SRC_CRG_TICKER_2`).

| Function | Description |
| --- | --- |
| `hud.begin_ticker(label)` | Start building a feed item |
| `hud.add_int(n)` | Append integer component |
| `hud.add_label(gxt)` | Append GXT substring |
| `hud.add_text(text)` | Append player-name style substring |
| `hud.end_ticker()` | Post the feed item |

```lua
hud.begin_ticker("HNG_TOT_TCK_1")
hud.add_label("TTYPE_NARC")
hud.add_int(50)
hud.end_ticker()
```

## natives

GTA V natives are the game's built-in functions (spawn entities, move peds, read coords, etc.). Carbon exposes them to Lua in **three** ways — pick the one that fits your task.

> [!TIP]
> **Recommended for almost all scripts:** typed namespaces (`PLAYER.PLAYER_PED_ID()`). They are named, typed, and documented in [lua-natives.md](lua-natives.md).

### 1. Typed namespaces (best default)

Call `util.require_natives()` once at the top of your script. This registers globals like `PLAYER`, `ENTITY`, `PED`, `VEHICLE`, `GRAPHICS`, etc. Each function has the correct parameter and return types already.

| Function | Returns | Description |
| --- | --- | --- |
| `util.require_natives()` | nothing | Load all typed native namespaces. Safe to call multiple times. |
| `natives.load_natives()` | nothing | Same as `util.require_natives()` |
| `require_natives()` | nothing | Global alias — same call |
| `natives.are_natives_loaded()` | `bool` | `true` after a successful load |

```lua
-- Always call this first
util.require_natives()

-- Get local player ped handle (integer)
local ped = PLAYER.PLAYER_PED_ID()

-- Get world position — returns a table { x, y, z }
local pos = ENTITY.GET_ENTITY_COORDS(ped, false)
print(pos.x, pos.y, pos.z)

-- Hash parameters accept integer OR string (joaat hash)
VEHICLE.CREATE_VEHICLE("adder", pos.x, pos.y, pos.z, 0.0, true, false)
```

### Typed native argument types

| Lua value | Native param type | Notes |
| --- | --- | --- |
| `integer` | int / entity / player id | Handles, IDs, flags, enums |
| `number` (non-integer) | float | Coords, headings, speed, etc. |
| `true` / `false` | bool | Also accepts `0` / `1` |
| `string` | string | Labels, model names, anim dicts |
| `string` or `integer` | hash | Model/weapon hashes — strings are auto-joaat'd |
| `{ x, y, z }` or `{ x=, y=, z= }` | vector3 | Position / rotation vectors |
| `nil` | pointer | Null pointer out-params |

### Typed native return types

| Return in Lua | Meaning | Example native |
| --- | --- | --- |
| `integer` | Handle, ID, count | `PLAYER.PLAYER_PED_ID()` |
| `number` | Float value | `ENTITY.GET_ENTITY_HEADING(ped)` |
| `bool` | Success / state | `ENTITY.DOES_ENTITY_EXIST(handle)` |
| `string` | Text result | Label natives |
| `{ x, y, z }` | Vector3 | `ENTITY.GET_ENTITY_COORDS(ped, false)` |
| nothing | Void native | `ENTITY.SET_ENTITY_COORDS(...)` |

### 2. Invoke.Lua (reusable raw calls)

Creates a Lua function bound to one native hash. Useful when you call the same hash repeatedly and want a typed return without loading all namespaces.

#### `Invoke.Lua(hash, return_type?)`

Returns a function you call with native arguments.

| Parameter | Description |
| --- | --- |
| `hash` | Native hash — `integer` or hex `string` (e.g. `"0x259BE71D8A81D4FA"`) |
| `return_type` | Optional single-character code (default `"i"`). Same codes as [native_call](#native_call). |

**Returns** `function(...)` — call with native args

```lua
local player_id = Invoke.Lua(0x259BE71D8A81D4FA)       -- PLAYER_ID, returns int
local get_coords = Invoke.Lua(0x3FEF770D40960D5A, "v") -- GET_ENTITY_COORDS, returns vector
local ped = Invoke.Lua(0xD80958FC74E988A6)()          -- PLAYER_PED_ID
local pos = get_coords(ped, false)
```

### 3. native_call (one-off raw calls)

See the dedicated [native_call](#native_call) section. Use when you need a single hash call and don't want to create a closure. For everyday scripting, prefer typed namespaces.

> [!NOTE]
> **Full native reference:** [lua-natives.md](lua-natives.md) lists all **6711** natives across **47** namespaces with parameter types.

### Which method should I use?

| Situation | Use |
| --- | --- |
| Normal gameplay scripting | `util.require_natives()` + `PLAYER.*` / `ENTITY.*` |
| Native exists in lua-natives.md | Typed namespace — always prefer this |
| Same hash called many times in a loop | `Invoke.Lua(hash, return_type)` |
| One-off hash from a dump / unknown native | `native_call(hash, return_type, ...)` |
| Hooking / intercepting natives | [hook.native](#hook) — separate API |

## native_call

Low-level native invoker. Calls any GTA native by its **hash value** (not by name). Supports full argument types and typed return values.

> [!TIP]
> **Prefer typed natives** (`PLAYER.PLAYER_PED_ID()`) whenever the native is listed in [lua-natives.md](lua-natives.md). Use `native_call` only for hashes not yet in the typed list, prototyping, or porting external scripts that already use raw hashes.

#### `native_call(hash, ...)`

Call a native. Default return type is `integer`.

| Parameter | Description |
| --- | --- |
| `hash` | **Required.** Native hash as `integer` (e.g. `0x259BE71D8A81D4FA`) or hex `string` (e.g. `"0x259BE71D8A81D4FA"`). *Not* the native name — `"PLAYER_ID"` will error. |
| `...` | Native arguments in order. See argument types below. |

**Returns** `integer` by default (return code `"i"`)

#### `native_call(hash, return_type, ...)`

Call a native and decode the result with an explicit return type.

| Parameter | Description |
| --- | --- |
| `hash` | Same as above |
| `return_type` | Single-character string telling Carbon how to decode the native result. Must be exactly one character. |
| `...` | Native arguments (start after `return_type`) |

### Return type codes

Pass as the second argument when it differs from the default. If you omit it, Carbon assumes `"i"` (integer).

| Code | Lua return type | When to use |
| --- | --- | --- |
| `"i"` | `integer` | Handles, player IDs, entity IDs, counts (default) |
| `"h"` | `integer` | Hash values returned by the native |
| `"f"` | `number` | Float results — heading, health %, distance, etc. |
| `"b"` | `bool` | True/false results — `DOES_ENTITY_EXIST`, etc. |
| `"s"` | `string` or `nil` | Text / label pointers returned by the native |
| `"v"` | `{ x, y, z }` | Vector3 — coords, rotation, velocity |
| `"p"` | `integer` | Raw pointer address (advanced) |
| `"n"` | nothing | Void natives — no return value pushed |

### Argument types

Pass arguments in the exact order the native expects. Unsupported types produce a Lua error — they are never silently ignored.

| Lua value | Passed as | Example |
| --- | --- | --- |
| `integer` | 64-bit integer | `ped`, `0`, `player_id` |
| `number` (has decimal) | float | `90.0`, `1.5` |
| `true` / `false` | bool | `true`, `false` |
| `string` | C string pointer | `"adder"`, `"FMMC"` — use for string params, not for the hash itself |
| `{ x=1, y=2, z=3 }` or `{1,2,3}` | Vector3 pointer | Position / direction args |
| `nil` | null pointer | Optional out-params |

### Examples

```lua
-- PLAYER_ID() → integer player index (0–31)
local pid = native_call(0x259BE71D8A81D4FA)
-- same with hex string hash:
local pid2 = native_call("0x259BE71D8A81D4FA")

-- PLAYER_PED_ID() → ped entity handle
local ped = native_call(0xD80958FC74E988A6)

-- GET_ENTITY_COORDS(ped, alive) → vector — MUST use return type "v"
local pos = native_call(0x3FEF770D40960D5A, "v", ped, false)
print(pos.x, pos.y, pos.z)

-- GET_ENTITY_HEADING(ped) → float — MUST use return type "f"
local heading = native_call(0xE83D4F9BA2A38914, "f", ped)

-- DOES_ENTITY_EXIST(handle) → bool — MUST use return type "b"
local exists = native_call(0x7239B21A38F536BA, "b", ped)

-- SET_ENTITY_COORDS(ped, x, y, z, ...) → void — use return type "n"
native_call(0x06843DA7060A026B, "n", ped, pos.x, pos.y, pos.z, false, false, false, true)
```

### Common mistakes

| Mistake | Problem | Fix |
| --- | --- | --- |
| `native_call("PLAYER_PED_ID")` | Hash arg must be numeric hex, not a native name string | Use `PLAYER.PLAYER_PED_ID()` or pass `0xD80958FC74E988A6` |
| Missing `"v"` on coord natives | Gets a garbage integer instead of `{x,y,z}` | Add `"v"` as second arg: `native_call(hash, "v", ...)` |
| Missing `"f"` on float natives | Gets wrong integer bits instead of a float | Add `"f"` as second arg |
| Missing `"b"` on bool natives | Gets `0`/`1` integer instead of `true`/`false` | Add `"b"` as second arg |
| Using `util.joaat("name")` as hash | Joaat ≠ native hash for most natives | Copy the hash from [lua-natives.md](lua-natives.md) or a native DB |
| Calling before natives/invoker ready | Error: `invoker_not_ready` | Call from `script.register_loop` or after game load, not at file top-level before session is ready |

### Errors

| Error | Meaning |
| --- | --- |
| `invoker_not_ready` | Game native table not initialized yet — retry later in a loop |
| `bad_hash` | Hash string is not valid hex (e.g. you passed a native name) |
| `bad_arg` | Argument type not supported (table without x/y/z, userdata, etc.) |
| `too_many_vectors` | More than 8 vector arguments in one call |

> [!NOTE]
> **Equivalent typed calls** — the examples above as typed natives (recommended):
> util.require_natives()
> local ped = PLAYER.PLAYER_PED_ID()
> local pos = ENTITY.GET_ENTITY_COORDS(ped, false)
> local heading = ENTITY.GET_ENTITY_HEADING(ped)
> local exists = ENTITY.DOES_ENTITY_EXIST(ped)

## scan

Search game memory for byte patterns. Use `?` or `??` as wildcards.

| Function | Returns | Description |
| --- | --- | --- |
| `scan.find(pattern, [module])` | `address` or `nil` | First match. Default module `"game"`. |
| `scan.find_all(pattern, [module])` | `address[]` | All matches |
| `scan.rip(addr, offset, size)` | `address` or `nil` | Resolve a relative address from an instruction |
| `scan.readable(addr, [size])` | `bool` | Default size `8` bytes |
| `scan.module([module])` | `{ base, size }` or `nil` | Module info |
| `scan.script(name, pattern)` | `offset` or `nil` | Pattern search inside a script |

```lua
local addr = scan.find("48 8B 05 ? ? ? ? 48 85 C0")
local mod = scan.module("game")
local target = scan.rip(addr, 3, 7)
local pc = scan.script("freemode", "2D 01 09 00 00 5D")
```

## hook

Intercept GTA native calls or memory functions. Hooks are tied to your script and removed automatically when it unloads.

### Native hooks

| Function | Returns |
| --- | --- |
| `hook.native(hash, fn)` | `bool` or `nil, err` |
| `hook.native_remove(hash)` | `bool` |
| `hook.native_blacklisted(hash)` | `bool, reason?` |
| `hook.remove_all()` | `bool` |

Callback receives `ctx` userdata. Return `hook.SKIP_ORIGINAL` (`false`) to skip the original; `hook.CALL_ORIGINAL` (`true`, default) to chain through.

| ctx method | Description |
| --- | --- |
| `ctx:arg_count()` | Number of arguments |
| `ctx:get_arg_int(i)` / `get_arg_uint` / `get_arg_float` / `get_arg_bool(i)` | Read argument |
| `ctx:set_arg_int(i, v)` / `set_arg_float(i, v)` | Rewrite argument before original |
| `ctx:set_return_int(v)` / `set_return_bool(v)` / `set_return_float(v)` | Set return value |

> [!NOTE]
> Certain sensitive natives (commerce, recovery, network session) cannot be hooked and will return an error.

### Detour hooks

| Function | Returns |
| --- | --- |
| `hook.detour(addr, [fn])` | `{ id, target, original }` or `nil, err` |
| `hook.detour_remove(id)` | `bool` |

> [!NOTE]
> Some memory regions are protected and cannot be hooked for stability and security.

```lua
local HASH = util.joaat("GET_PLAYER_PED")
hook.native(HASH, function(ctx)
  if ctx:get_arg_int(0) == 999 then
    ctx:set_return_int(0)
    return hook.SKIP_ORIGINAL
  end
  return hook.CALL_ORIGINAL
end)
```

## memory

Read and write game memory by address. Invalid reads return `0`; invalid writes are safely ignored.

| Function | Returns |
| --- | --- |
| `memory.read_u8(addr)` | `integer` |
| `memory.read_u16(addr)` | `integer` |
| `memory.read_u32(addr)` | `integer` |
| `memory.read_u64(addr)` | `integer` |
| `memory.read_i32(addr)` | `integer` (signed) |
| `memory.read_f32(addr)` | `float` |
| `memory.write_u32(addr, value)` | — |
| `memory.write_u64(addr, value)` | — |
| `memory.write_f32(addr, value)` | — |

```lua
local v = memory.read_u32(addr)
memory.write_u32(addr, 1)
```

## player

Local player helpers. Legacy aliases `local_id` / `local_ped` map to `id` / `ped`.

| Function | Returns |
| --- | --- |
| `player.id()` / `local_id()` | Local player index |
| `player.ped()` / `local_ped()` | Local ped handle (`0` if missing) |
| `player.name()` | Display name string |
| `player.coords()` | `x, y, z` |
| `player.set_coords(x,y,z)` / `teleport(x,y,z)` | `bool` |
| `player.teleport_blip(sprite)` | `bool` |
| `player.heading()` | `float` |
| `player.set_heading(h)` | `bool` |
| `player.vehicle()` | Vehicle handle or `0` |
| `player.is_in_vehicle()` | `bool` |
| `player.wanted()` | `int` |
| `player.set_wanted(level)` | `bool` — clamped 0–5 |
| `player.clear_wanted()` | `bool` |
| `player.health()` / `set_health(n)` | `int` / `bool` |
| `player.armour()` / `set_armour(n)` | `int` / `bool` |
| `player.is_host()` | `bool` |
| `player.godmode([state])` | `bool` — get or set invincibility |

```lua
local x, y, z = player.coords()
player.set_wanted(0)
player.godmode(true)
```

## players

Online session player list and per-player queries.

| Function | Returns |
| --- | --- |
| `players.count()` | Valid player count |
| `players.list()` | `int[]` of player IDs |
| `players.get(id)` | Player table or `nil` |
| `players.user()` | Local player ID |
| `players.host()` | Session host player ID |
| `players.ped(id)` | Ped handle |
| `players.name(id)` | Name string |
| `players.coords(id)` | `x, y, z` |
| `players.rid(id)` | Rockstar ID |
| `players.health(id)` / `armour` / `wanted` | `int` |
| `players.vehicle(id)` | Vehicle handle |
| `players.distance(id)` | Distance in meters |
| `players.is_valid(id)` / `exists(id)` | `bool` |
| `players.is_friend(id)` | `bool` |
| `players.is_host(id)` | `bool` |

`players.selected_id()` returns the currently selected individual player ID, or `-1` when no individual player is selected. `players.selected()` returns the same player table as `players.get(id)`, or `nil` for no individual target.

`players.get` returns: `id, ped, name, valid, in_vehicle, health, max_health, armour, wanted, rank, rid, is_friend, x, y, z, coords {x,y,z}, vehicle`

```lua
for _, id in ipairs(players.list()) do
  local p = players.get(id)
  if p and p.valid then
    print(p.name, p.coords.x, p.coords.y, p.coords.z)
  end
end
```

## ped

Ped-specific helpers. `ped.local_coords()` is shorthand for local ped position.

| Function | Returns |
| --- | --- |
| `ped.local_coords()` | `x, y, z` |
| `ped.coords(handle)` | `x, y, z` |
| `ped.armour(handle)` / `set_armour(handle, n)` | `int` / `bool` |
| `ped.vehicle(handle)` | Vehicle handle |
| `ped.into_vehicle(handle, veh, [seat])` | `bool` — default seat `-1` |
| `ped.is_in_vehicle(handle)` | `bool` |

## entity

Generic entity operations by handle.

| Function | Returns |
| --- | --- |
| `entity.exists(handle)` | `bool` |
| `entity.coords(handle)` / `get_coords` | `x, y, z` |
| `entity.set_coords(handle, x,y,z)` | `bool` |
| `entity.heading(handle)` / `set_heading(handle, h)` | `float` / `bool` |
| `entity.rotation(handle, [order])` / `set_rotation` | `rx,ry,rz` / `bool` |
| `entity.velocity(handle)` / `set_velocity` | `x,y,z` / `bool` |
| `entity.speed(handle)` | `float` |
| `entity.model(handle)` / `get_model` | Model hash |
| `entity.health(handle)` / `max_health` / `set_health` | `int` / `bool` |
| `entity.is_dead(handle)` | `bool` |
| `entity.is_ped` / `is_vehicle` / `is_object` | `bool` |
| `entity.is_visible(handle)` / `set_visible(handle, state)` | `bool` |
| `entity.alpha(handle)` / `set_alpha` / `reset_alpha` | `int` / `bool` |
| `entity.freeze(handle, state)` | `bool` |
| `entity.set_collision(handle, state)` | `bool` |
| `entity.set_invincible(handle, state)` | `bool` |
| `entity.set_gravity(handle, state)` | `bool` |
| `entity.get_offset(handle, ox,oy,oz)` | World coords from offset |
| `entity.apply_force(handle, fx,fy,fz)` | `bool` |
| `entity.request_control(handle, [tries])` | `bool` — default `20` tries |
| `entity.delete(handle)` | `bool` |

```lua
local ped = player.ped()
if entity.exists(ped) then
  local x,y,z = entity.coords(ped)
  entity.set_invincible(ped, true)
end
```

## vehicle

Vehicle helpers. `vehicle.get` accepts a ped handle or player ID.

| Function | Returns |
| --- | --- |
| `vehicle.get(ped_or_player)` | Vehicle handle |
| `vehicle.repair(handle)` | `bool` |
| `vehicle.engine(handle, on)` / `set_engine` | `bool` |
| `vehicle.plate(handle)` / `set_plate(handle, text)` | `string` / `bool` |
| `vehicle.speed(handle)` | `float` |
| `vehicle.set_forward_speed(handle, speed)` | `bool` |
| `vehicle.lock(handle, [level])` | `bool` — default level `2` |
| `vehicle.unlock(handle)` | `bool` |
| `vehicle.on_ground(handle)` | `bool` |
| `vehicle.god(handle, state)` | `bool` |
| `vehicle.engine_health(handle)` | `float` |

```lua
local veh = vehicle.get(player.ped())
if veh ~= 0 then
  vehicle.repair(veh)
  vehicle.set_plate(veh, "CARBON")
end
```

## streaming

Model streaming. Hash accepts string model name or integer hash.

| Function | Returns |
| --- | --- |
| `streaming.request_model(model)` | `true` |
| `streaming.has_model_loaded(model)` | `bool` |
| `streaming.release_model(model)` | — |

```lua
streaming.request_model("adder")
while not streaming.has_model_loaded("adder") do
  script.wait(0)
end
```

## util

General helpers, timing, and math utilities.

| Function | Returns | Notes |
| --- | --- | --- |
| `util.joaat(string)` | `integer` | Jenkins one-at-a-time hash |
| `util.time()` | `integer` | Unix time in **milliseconds** |
| `util.now_ms()` | `integer` | Milliseconds since system start |
| `util.local_time()` | `hour, minute, second, day, month, year` | Real wall-clock local time (not the in-game clock) |
| `util.frame_count()` | `integer` | GTA frame count |
| `util.game_timer()` | `integer` | `GET_GAME_TIMER` |
| `util.session_started()` / `is_session_started()` | `bool` |  |
| `util.world_ready()` | `bool` |  |
| `util.is_loading()` | `bool` | Loading screen or world not ready |
| `util.clamp(v, lo, hi)` | `number` |  |
| `util.lerp(a, b, t)` | `number` |  |
| `util.distance(ax,ay,az, bx,by,bz)` | `number` | 3D distance |
| `util.v3([x,y,z])` | `{x,y,z}` | Build vector table |
| `util.toast(msg, [title])` | — | Alias for info notify |
| `util.require_natives()` | — | Load native namespaces |
| `util.wait(ms)` / `yield_ms` / `yield()` | — | Script thread yield |
| `util.create_thread(fn)` | `bool` | Start a background task |
| `util.world_to_screen(x,y,z)` | `ok, px, py, nx, ny` | Same as `dx.world_to_screen` |
| `util.starts_with` / `ends_with` / `contains` / `trim` / `split` / `replace` | varies | String helpers (also on `lang`) |

```lua
local hash = util.joaat("adder")
local now = util.time()
if util.world_ready() then -- safe to touch world end
```

## keybind

Register script keybinds, let users capture them directly in the menu, and react to presses without building your own combo picker.

| Function | Returns | Notes |
| --- | --- | --- |
| `keybind.register(id, fn)` | `bool` | Register a bind using `id` as the display label |
| `keybind.register(id, label, fn)` | `bool` | Register a bind with a custom user-facing label |
| `keybind.capture(id)` | `bool` | Open the built-in key capture prompt for that bind |
| `keybind.is_capturing()` | `bool` | True while any capture prompt (from this or any script) is still open. There's no separate "cancelled" event -- poll this after `keybind.capture`; once it goes false, check `keybind.get(id)`: a vk of 0 means the user pressed Escape instead of a key |
| `keybind.get(id)` | `vk, name` | Returns the current bound key code and display name |
| `keybind.clear(id)` | `bool` | Remove the current key from that bind |
| `keybind.list()` | `{ id, label, vk, name }[]` | List all binds registered by the script |

> [!NOTE]
> The callback you pass to `keybind.register` runs whenever the bound key is pressed. It receives `vk` and `name`.

```lua
keybind.register("panic", "Panic Mode", function(vk, name)
  notify.warn("Panic", "Pressed " .. name)
end)

menu.action("Set Panic Key", "Press Enter, then choose a key", function()
  keybind.capture("panic")
end)

local vk, name = keybind.get("panic")
```

## input

Poll keyboard and mouse input using Windows key codes.

| Function | Returns |
| --- | --- |
| `input.is_key_down(vk)` | `bool` |
| `input.is_key_just_pressed(vk)` | `bool` — edge triggered |
| `input.is_mouse_down([btn])` | `bool` — `0` left, `1` right, `2` middle |

```lua
if input.is_key_just_pressed(0x74) then -- F5
  notify.info("Input", "F5 pressed")
end
```

## draw

GTA HUD drawing. Coordinates are normalized screen space (`0.0–1.0`). Call from `script.register_loop`.

| Function | Arguments |
| --- | --- |
| `draw.text(text, x, y, [scale], [r,g,b,a], [font], [centred])` | HUD text. Default scale `0.35`, font `4` |
| `draw.rect(x, y, w, h, [r,g,b,a])` | Normalized rect |
| `draw.line_3d(ax,ay,az, bx,by,bz, [r,g,b,a])` | World line |
| `draw.world_to_screen(x,y,z)` | `ok, px, py, nx, ny` |

```lua
draw.text("Carbon", 0.5, 0.05, 0.5, 255, 255, 255, 220, 4, true)
```

## dx / directx

Draw overlays on screen using pixel coordinates. Commands are queued each frame. Alias: `directx`.

| Function | Notes |
| --- | --- |
| `dx.text(text, x, y, [r,g,b,a], [size])` | Default size `16` |
| `dx.rect(x, y, w, h, [r,g,b,a], [filled], [thickness])` | Default filled `true` |
| `dx.line(x1,y1, x2,y2, [r,g,b,a], [thickness])` |  |
| `dx.circle(x, y, radius, [r,g,b,a], [filled], [thickness])` | Default filled `false` |
| `dx.world_to_screen(x,y,z)` | `ok, px, py, norm_x, norm_y` |
| `dx.resolution()` | `width, height` |
| `dx.mouse_pos()` | `x, y` screen position |

```lua
local ok, sx, sy = dx.world_to_screen(x, y, z)
if ok then
  dx.circle(sx, sy, 6, 255, 80, 80, 255, true)
end
```

## imgui

Build a script-owned Dear ImGui window with a safe retained bridge. Lua
declares controls from `script.register_loop`; Carbon replays them during the
renderer frame and returns input changes on the next loop tick. Scripts do not
call Dear ImGui directly from the game thread.

Lua runtimes are created, resumed, and destroyed exclusively on ScriptTick;
Load/Stop/Run buttons only queue lifecycle requests. Syntax errors, missing API
members, invalid arguments, and coroutine failures are contained to the script
and reported in the script console instead of escaping into the game process.

The bridge supports up to `512` commands, `16` windows, `256` interactive
widgets, and `64` tab bars per script. Call `begin_frame` and `end_frame` once
per loop. `end_window`, `end_child`, `end_tab_bar`, and `end_tab_item` should
always be called after their matching begin call; this keeps the renderer state
balanced even when a begin call returns `false`.

| Function | Returns | Notes |
| --- | --- | --- |
| `imgui.begin_frame()` / `end_frame()` | — | Start and publish one declaration frame |
| `imgui.bind(window_id, vk)` | `bool` | Toggle a window on a Windows virtual-key press; `0x79` is F10 |
| `imgui.is_open(window_id)` | `bool` | Current visibility |
| `imgui.set_open(window_id, state)` / `toggle_open` | `bool` | Control visibility |
| `imgui.begin_window(id, title, [vk], [width], [height], [flags])` | `bool` | Returns the previous-frame visible state |
| `imgui.end_window()` | — | Close the current window |
| `imgui.begin_child(id, [width], [height], [border], [flags])` | `bool` | Scrollable child region. Border uses `imgui.colors.border`; rounding uses `imgui.style.child_rounding`, not `window_rounding` |
| `imgui.end_child()` | — | Close the child region |
| `imgui.text(text)` / `text_colored(text,r,g,b,a)` | — | Text uses UTF-8 strings when the active font contains the glyph |
| `imgui.separator()` / `spacing()` / `same_line([x],[spacing])` | — | Layout helpers |
| `imgui.button(label, [id], [width], [height])` | `bool` | Click result is delivered on the next loop tick |
| `imgui.checkbox(label, value, [id])` | `value, changed` | Returned value is the source of truth |
| `imgui.toggle(label, value, [id], [r], [g], [b])` | `value, changed` | Pill-style on/off switch, drawn manually (not a stock checkbox). `r,g,b` (0-255) recolor the "on" state and knob glow, default `206,23,255` |
| `imgui.slider_int(label, value, min, max, [id])` | `value, changed` | Integer slider |
| `imgui.slider_float(label, value, min, max, [id])` | `value, changed` | Float slider |
| `imgui.input_int(label, value, [step], [id])` | `value, changed` | Type-in integer box. Default `step` `1` |
| `imgui.input_float(label, value, [step], [id])` | `value, changed` | Type-in float box. Default `step` `0.0` (no +/- buttons) |
| `imgui.combo(label, value, items, [id])` | `value, changed` | `items` is a 1-based Lua string array; value is 0-based |
| `imgui.is_image_ready(path)` | `ready, failed, error` | Kicks off loading the same as `imgui.image` would, without drawing anything. Poll this to build a preload/loading screen. `error` is a human-readable reason, set only when `failed` |
| `imgui.image(path, width, height, [rounding], [id], [tint_r, tint_g, tint_b, tint_a])` | — | Draws a PNG/JPG loaded from the script's `fs` sandbox (same rules as `fs.*`, cannot escape it). `rounding` in pixels — pass `width/2` for a circular avatar. Loads once per path and caches the GPU texture; draws an empty placeholder box while loading or on failure. `tint_*` (each 0-255, default 255) multiplies the texture's own colors — e.g. a white silhouette PNG tinted to any accent color at draw time; defaults to opaque white, i.e. the texture's native colors unchanged |
| `imgui.begin_tab_bar(id, [flags])` / `end_tab_bar()` | `bool` / — | Tab container |
| `imgui.begin_tab_item(label, [icon], [id])` / `end_tab_item()` | `bool` / — | Returns the previous render's active item for its enclosing tab bar. Icon is display text; Carbon's bundled icon glyphs are available through `imgui.icons` |
| `imgui.begin_group()` / `end_group()` | `bool` / — | Group layout |
| `imgui.dummy(width, height)` | — | Reserve layout space |
| `imgui.set_next_window_size(width, height)` | — | First-use window size |
| `imgui.set_next_window_pos(x, y)` | — | First-use screen position |
| `imgui.set_next_window_focus()` | — | Forces the next `begin_window` to the front/focused, every call. Needed for a floating popup window (e.g. a dropdown list) that must receive clicks even though another window (like the main menu) may be drawn/focused elsewhere the same frame |
| `imgui.push_style_color(index,r,g,b,a)` / `pop_style_color([count])` | — | Color components are `0.0–1.0` |
| `imgui.push_style_var(index,value[,value2])` / `pop_style_var([count])` | — | One value for floats, two for `ImVec2` values |

Low-level drawing and hit-testing, for building fully custom widgets. Coordinates are absolute screen pixels (get a start point from `get_cursor_screen_pos` or `invisible_button`'s return). Colors are `r,g,b,a` 0-255, matching `dx.*`.

| Function | Returns | Notes |
| --- | --- | --- |
| `imgui.draw_line(x1,y1,x2,y2,r,g,b,a,[thickness])` | — | |
| `imgui.draw_rect(x1,y1,x2,y2,r,g,b,a,[rounding],[thickness])` | — | Outline only |
| `imgui.draw_rect_filled(x1,y1,x2,y2,r,g,b,a,[rounding])` | — | |
| `imgui.draw_circle(x,y,radius,r,g,b,a,[thickness])` | — | Outline only |
| `imgui.draw_circle_filled(x,y,radius,r,g,b,a)` | — | |
| `imgui.draw_bezier(x1,y1,cx1,cy1,cx2,cy2,x2,y2,r,g,b,a,[thickness])` | — | Cubic Bezier, 4 control points |
| `imgui.draw_text(x,y,text,r,g,b,a,[size])` | — | |
| `imgui.get_cursor_screen_pos(id)` | `x, y` | Screen position at this point in the layout, from the previous frame |
| `imgui.set_cursor_screen_pos(x, y)` | — | Jump the layout cursor to an absolute screen position — lets you overlay custom content instead of only flowing top-to-bottom |
| `imgui.draw_image(path, x1, y1, x2, y2, [rounding], [id], [tint_r, tint_g, tint_b, tint_a])` | — | Same loader/cache as `imgui.image`, but drawn at absolute coordinates without consuming layout space — for backgrounds and overlays. `tint_*` behaves the same as on `imgui.image` |
| `imgui.invisible_button(id, width, height)` | `clicked, hovered, active, x, y` | Hit-test region with no visuals — draw your own on top. `x,y` is where it was placed |
| `imgui.calc_text_size(text, [id], [size])` | `width, height` | Pixel size of `text` in the current font, from the previous frame. `size` (font size in px, same units as `draw_text`'s size override) scales the result to match a `draw_text`/`text`-style render at that size -- without it, the measurement uses the font's own native loaded size, which silently diverges from a size-overridden render and causes layout drift (e.g. dividers/underlines misaligned against text drawn at a different size) |
| `imgui.get_content_region_avail(id)` | `width, height` | Remaining space in the current window/child, from the previous frame |
| `imgui.set_next_item_width(width)` | — | Sets the width of the next `slider_int`/`slider_float`/`input_int`/`input_float`/`combo` |
| `imgui.draw_rect_filled_gradient(x1,y1,x2,y2, r1,g1,b1,a1, r2,g2,b2,a2, [vertical])` | — | Two-stop gradient fill. Chain several calls edge-to-edge for a multi-stop gradient (e.g. a hue slider) |

```lua
local clicked, hovered, active, x, y = imgui.invisible_button("my_knob", 12, 12)
imgui.draw_circle_filled(x + 6, y + 6, 6, hovered and 255 or 200, 255, 255, 255)
```

Custom fonts, loaded from the script's `fs` sandbox (same rules as `fs.*`). Loading happens once per real frame, right before that frame's `ImGui::NewFrame()` — never mid-frame — so it can't corrupt anything already drawn. That means a font takes a frame or two to become available; poll `imgui.is_font_ready` (same pattern as `imgui.is_image_ready`) before relying on it, e.g. to gate a loading screen.

| Function | Returns | Notes |
| --- | --- | --- |
| `imgui.load_font(path, size, [id])` | — | Requests a TTF be loaded at `size` px. Safe to call every frame — a no-op once already requested |
| `imgui.is_font_ready(id)` | `ready, failed, error` | `error` is a human-readable reason, set only when `failed` |
| `imgui.push_font(id)` | — | Unknown/not-yet-ready id pushes the default font, not an error |
| `imgui.pop_font()` | — | |

```lua
imgui.load_font("Arial-Bold.ttf", 14.0, "bold")
-- later, once imgui.is_font_ready("bold") is true:
imgui.push_font("bold")
imgui.text("Heading")
imgui.pop_font()
```

The `imgui.colors`, `imgui.style`, `imgui.window_flags`, and `imgui.tab_flags`
tables expose the matching Dear ImGui enum values. `imgui.icons` contains
Carbon's bundled Font Awesome strings, while ordinary UTF-8 text is also
accepted.

```lua
local open = false
local enabled = false
local approach = 0

imgui.bind("random", 0x79) -- F10

script.register_loop(function()
  imgui.begin_frame()
  imgui.set_next_window_size(760, 500)

  if imgui.begin_window("random", "random heist thing") then
    imgui.text_colored("MISSION CONTROL", 0.35, 0.85, 1.0, 1.0)
    imgui.separator()
    local changed
    enabled, changed = imgui.checkbox("Controller enabled", enabled, "controller")
    approach, changed = imgui.combo("Approach", approach, { "Quiet", "Aggressive", "Flexible" }, "approach")

    if imgui.begin_tab_bar("heists") then
      local active = imgui.begin_tab_item("Overview", "O", "overview")
      if active then imgui.text("Ready for a heist plan.") end
      imgui.end_tab_item()
      active = imgui.begin_tab_item("Cayo Perico", "C", "cayo")
      if active then imgui.text("Cayo controls can be added here.") end
      imgui.end_tab_item()
      imgui.end_tab_bar()
    end
  end
  imgui.end_window()
  imgui.end_frame()
end)
```

## lang

Lightweight string utilities (ASCII case folding). Same helpers are also on `util`.

| Function | Returns |
| --- | --- |
| `lang.starts_with(s, prefix)` | `bool` |
| `lang.ends_with(s, suffix)` | `bool` |
| `lang.contains(s, sub)` | `bool` |
| `lang.trim(s)` | `string` |
| `lang.split(s, [delim])` | `string[]` — default delim `","` |
| `lang.replace(s, find, repl)` | `string` |
| `lang.lower(s)` / `upper(s)` | `string` |

## fs

Store script data (like images, fonts, etc) in Carbon's secure local folder (\AppData\Local\Carbon\data\lua). Paths are relative and cannot escape the script directory. Max file size: `8 MB`.

> [!TIP]
> Prefix a path with `cache:` (e.g. `"cache:avatar.png"`) to read from Carbon's shared cache directory instead of your script's own sandbox — e.g. the avatar Carbon's own menu already downloads for every user. Same traversal protections apply. Works with `imgui.image`/`imgui.draw_image`/`fs.*`.

| Function | Returns |
| --- | --- |
| `fs.root()` | Absolute sandbox root path |
| `fs.exists(path)` | `bool` |
| `fs.is_dir(path)` | `bool` |
| `fs.mkdir(path)` | `bool` |
| `fs.read(path)` | `string` or `nil` |
| `fs.write(path, data)` | `bool` |
| `fs.append(path, data)` | `bool` |
| `fs.delete(path)` | `bool` |
| `fs.list([rel])` | Filename array |

```lua
fs.write("config.txt", "enabled=1")
local cfg = fs.read("config.txt")
```

## http

Make HTTP requests in the background. Up to `8` requests can run at once. Your callback fires when the response is ready. Alias: `async_http`.

| Function | Callback | Returns |
| --- | --- | --- |
| `http.get(url, fn)` | `fn(status, body)` | `bool` — job queued |
| `http.post(url, body, fn)` | `fn(status, body)` | `bool` |
| `http.request(method, url, body?, fn)` | `fn(status, body)` | `bool` |

> [!NOTE]
> On failure, callback receives error string as second arg instead of body. POST sends `Content-Type: application/json`.

```lua
http.get("https://api.example.com/status", function(status, body)
  print(status, body)
end)
```

## carbon.feature

Control built-in Carbon menu features from your script.

| Function | Returns |
| --- | --- |
| `carbon.feature.set(cat, id, value)` | `bool` |
| `carbon.feature.get(cat, id)` | `bool` or `nil` |
| `carbon.feature.toggle(cat, id)` | `bool` |
| `carbon.feature.trigger(cat, id)` | `bool` |
| `carbon.feature.enable(cat, id)` / `disable(cat, id)` | `bool` |
| `carbon.feature.list(cat)` | `string[]` of feature IDs |
| `carbon.enable_by_name(cat, id)` / `disable_by_name` | `bool` — top-level aliases |

```lua
carbon.feature.set("self", "godmode", true)
carbon.feature.trigger("self", "heal")
local ids = carbon.feature.list("self")
```

## business

Manage MC businesses, cargo warehouses, nightclub stock, hangar cargo, safes, and CEO/MC registration from your script.

### Cargo & warehouses

| Function | Returns |
| --- | --- |
| `business.cargo_stock()` | `stock, capacity, value` |
| `business.cargo_warehouses()` | Array of warehouse tables |
| `business.cargo_set(slot, stock)` | New stock count |
| `business.cargo_fill()` | Crates filled |
| `business.cargo_fill_value(dollars)` | `stock, value` |
| `business.cargo_clear()` | Cleared count |
| `business.cargo_value_of(crates)` | Sale value |

### Factories, hub, hangar

| Function | Returns |
| --- | --- |
| `business.factories()` | Factory array |
| `business.factory_set(slot, stock)` | `bool` |
| `business.factory_fill()` | Count filled |
| `business.hub_goods()` | Nightclub goods array |
| `business.hub_set(index, stock)` | `bool` |
| `business.hub_fill()` | Count filled |
| `business.hangar_id()` | `id, stock` |
| `business.hangar_fill()` / `hangar_clear()` | `bool` |
| `business.resupply()` | Factories resupplied |
| `business.bunker_research()` | `bool` |

### Safes, CEO/MC, teleport

| Function | Returns |
| --- | --- |
| `business.safes()` | Array of `{ name, cash }` |
| `business.claim_safes()` | `true` |
| `business.nightclub_popularity()` | `pct, raw` |
| `business.set_nightclub_popularity(pct)` | `bool` |
| `business.boss_type()` | Label string |
| `business.register([kind])` | `bool` — `"ceo"` or `"mc"` |
| `business.retire()` | `true` |
| `business.teleport(key, [slot])` | `bool` |
| `business.script_running(name)` | `bool` |
| `business.snapshot()` | Summary table with `rows` |
| `business.stat_int(name)` | Resolved MPX stat int |

`business.teleport` keys: `warehouse`, `nightclub`, `nightclub_safe`, `hangar`, `arcade`, `agency`, `clubhouse`, `salvage`, `bail`, `garment`, `car_wash`, `factory`

```lua
local stock, cap, value = business.cargo_stock()
business.factory_fill()
business.teleport("nightclub")
local snap = business.snapshot()
```

## Example script

A complete starter script showing menus, session events, player overlays, and notifications.

```lua
util.require_natives()

if not carbon.is_premium() then
  notify.warn("Premium", "Requires Premium")
  return
end

notify.success("My Script", "loaded · " .. carbon.tier())

local overlay = false

menu.toggle("Player overlay", "Draw names in HUD", function(s) overlay = s end)

menu.action("Coords", "Print local position", function()
  local x, y, z = player.coords()
  notify.info("Coords", string.format("%.1f %.1f %.1f", x, y, z))
end)

events.on("player_join", function(id, name)
  console.log("join", id, name)
end)

script.register_loop(function()
  if overlay then
    for i, id in ipairs(players.list()) do
      local p = players.get(id)
      if p and p.valid then
        draw.text(p.name, 0.02, 0.10 + i * 0.025, 0.30, 255, 255, 255, 200)
      end
    end
  end
end)
```

---

Carbon · Scripting API Reference · Lua 5.4
 
