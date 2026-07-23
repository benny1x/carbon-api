# Carbon Lua API

Our scripts run on lua5.4 Press `` ` `` in-game for the console.

Available libraries: `base`, `table`, `string`, `math`, `coroutine`.  
Not available: `io`, `os`, `package`, `debug`.

---

## carbon

| Function | Params | Returns |
| --- | --- | --- |
| `carbon.tier()` | — | `"basic"` or `"premium"` |
| `carbon.is_premium()` | — | `bool` |
| `carbon.uuid()` | — | `string` |

```lua
if not carbon.is_premium() then
  notify.warn("Premium", "Requires Premium")
  return
end

print(carbon.tier(), carbon.uuid())
```

---

## print / console / log

| Function | Params | Returns |
| --- | --- | --- |
| `print(...)` | any args | — |
| `console.log(msg)` | `msg` string (optional) | — |
| `console.info(msg)` | `msg` string (optional) | — |
| `console.warn(msg)` | `msg` string (optional) | — |
| `console.error(msg)` | `msg` string (optional) | — |
| `console.clear()` | — | — |
| `log.info(msg)` | `msg` string (optional) | — |
| `log.warn(msg)` | `msg` string (optional) | — |
| `log.error(msg)` | `msg` string (optional) | — |

```lua
print("hello", 123)
console.info("loaded")
log.warn("something odd")
console.clear()
```

---

## notify

In-game toast. Also mirrored to console.

| Function | Params | Returns |
| --- | --- | --- |
| `notify.info(title, message)` | both optional, default title `"Script"` | — |
| `notify.success(title, message)` | same | — |
| `notify.warn(title, message)` | same | — |
| `notify.error(title, message)` | same | — |

```lua
notify.success("Carbon", "Script loaded")
notify.error("Fail", "something broke")
```

---

## menu

Widgets show under **Settings → Custom Scripts** for this script. Cap: **256** widgets.

| Function | Params | Returns |
| --- | --- | --- |
| `menu.toggle(label, desc, fn)` | `fn(state: bool)` required | — |
| `menu.action(label, desc, fn)` | `fn()` required | — |
| `menu.slider_int(label, desc, min, max, default, [fn])` | `fn(value: int)` optional. defaults: min `0`, max `100`, default `min` | — |
| `menu.slider_float(label, desc, min, max, default, [fn])` | `fn(value: float)` optional. defaults: min `0`, max `1`, default `min` | — |

```lua
local enabled = false

menu.toggle("Godmode", "Toggle example", function(state)
  enabled = state
  notify.info("Script", state and "on" or "off")
end)

menu.action("Heal", "Action example", function()
  notify.success("Script", "healed")
end)

menu.slider_int("Wanted", "0-5", 0, 5, 0, function(v)
  print("wanted", v)
end)

menu.slider_float("Speed", "multiplier", 0.0, 5.0, 1.0, function(v)
  print("speed", v)
end)
```

---

## script

| Function | Params | Returns |
| --- | --- | --- |
| `script.register_loop(fn)` | `fn()` runs every script tick | — |
| `script.force_host(script_name)` | script thread name, e.g. `"freemode"` | `bool` |

```lua
script.register_loop(function()
  -- every tick
end)

local ok = script.force_host("freemode")
```

---

## session

| Function | Params | Returns |
| --- | --- | --- |
| `session.launch(type)` | join type int (see constants) | `bool` |

Constants:

| Name | Value |
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

---

## globals

Script globals.

| Function | Params | Returns |
| --- | --- | --- |
| `globals.get_int(index)` | `index` int | `value, ok` |
| `globals.set_int(index, value)` | — | `ok` bool |
| `globals.get_float(index)` | — | `value, ok` |
| `globals.set_float(index, value)` | — | `ok` bool |

```lua
local v, ok = globals.get_int(1575044)
if ok then
  globals.set_int(1575044, v)
end
```

---

## locals

Script thread locals.

| Function | Params | Returns |
| --- | --- | --- |
| `locals.get_int(script_name, index)` | e.g. `"freemode"` | `value, ok` |
| `locals.set_int(script_name, index, value)` | — | `ok` bool |
| `locals.get_float(script_name, index)` | — | `value, ok` |
| `locals.set_float(script_name, index, value)` | — | `ok` bool |

```lua
local v, ok = locals.get_int("freemode", 0)
if ok then
  locals.set_int("freemode", 0, v)
end
```

---

## stats

`MPX_` / `MPx_` prefix auto-resolves to `MP0_` or `MP1_` from `MPPLY_LAST_MP_CHAR`.

| Function | Params | Returns |
| --- | --- | --- |
| `stats.get_int(name)` | stat name string | `value, ok` |
| `stats.set_int(name, value)` | — | `ok` bool |
| `stats.set_bool(name, value)` | `value` bool | `ok` bool |
| `stats.set_packed_bool(index, value)` | packed bool index | `true` |

```lua
local ch, ok = stats.get_int("MPPLY_LAST_MP_CHAR")
local driv = stats.get_int("MPX_SCRIPT_INCREASE_DRIV")

stats.set_int("MPX_SCRIPT_INCREASE_DRIV", 100)
stats.set_bool("MPX_SOME_BOOL", true)
stats.set_packed_bool(12345, true)
```

---

## util

| Function | Params | Returns |
| --- | --- | --- |
| `util.joaat(string)` | string (optional) | `integer` hash |
| `util.time()` | — | unix time **ms** |
| `util.require_natives()` | — | loads GTA native namespaces |

```lua
util.require_natives()

local hash = util.joaat("adder")
local now = util.time()
```

---

## natives

| Function | Params | Returns |
| --- | --- | --- |
| `natives.load_natives()` | — | same as `util.require_natives()` |
| `natives.are_natives_loaded()` | — | `bool` |
| `require_natives()` | global alias | same |

After load you get namespaces like `PLAYER`, `ENTITY`, `PED`, `VEHICLE`, etc.

```lua
util.require_natives()

local ped = PLAYER.PLAYER_PED_ID()
local pos = ENTITY.GET_ENTITY_COORDS(ped, false)
print(pos.x, pos.y, pos.z)
```

Vectors return as `{ x, y, z }`. Hash args accept int or string.

---

## native_call

Low-level invoker. Max **8** args. Accepts int / float / bool.

| Function | Params | Returns |
| --- | --- | --- |
| `native_call(hash, ...)` | native hash + up to 8 args | `integer` result |

```lua
local pid = native_call(0x259BE71D8A81D4FA) -- PLAYER_ID
```

Prefer `util.require_natives()` + `PLAYER.*` unless you need a raw hash call.

---

## memory

Bad addresses return `0` / no-op (SEH guarded).

| Function | Params | Returns |
| --- | --- | --- |
| `memory.read_u32(addr)` | address int | `integer` |
| `memory.read_u64(addr)` | address int | `integer` |
| `memory.write_u32(addr, value)` | — | — |

```lua
local v = memory.read_u32(addr)
memory.write_u32(addr, 1)
```

---

## player / ped

| Function | Params | Returns |
| --- | --- | --- |
| `player.local_id()` | — | local player index |
| `player.local_ped()` | — | local ped handle (`0` if missing) |
| `ped.local_coords()` | — | `x, y, z` |

```lua
local id = player.local_id()
local ped_h = player.local_ped()
local x, y, z = ped.local_coords()
print(id, ped_h, x, y, z)
```

---

## Full example

```lua
util.require_natives()

if not carbon.is_premium() then
  notify.warn("Premium", "Requires Premium")
  return
end

notify.success("My Script", "loaded · " .. carbon.tier())
print("uuid", carbon.uuid())

local on = false

menu.toggle("Loop Feature", "runs in register_loop", function(state)
  on = state
end)

menu.action("Coords", "dump local coords", function()
  local x, y, z = ped.local_coords()
  local ped_h = PLAYER.PLAYER_PED_ID()
  local pos = ENTITY.GET_ENTITY_COORDS(ped_h, false)
  print("api", x, y, z)
  print("native", pos.x, pos.y, pos.z)
  notify.info("Coords", string.format("%.1f %.1f %.1f", x, y, z))
end)

menu.slider_int("Wanted", "", 0, 5, 0, function(v)
  PLAYER.SET_PLAYER_WANTED_LEVEL(PLAYER.PLAYER_ID(), v, false)
  PLAYER.SET_PLAYER_WANTED_LEVEL_NOW(PLAYER.PLAYER_ID(), false)
end)

script.register_loop(function()
  if not on then return end
  -- per-tick work
end)
```
