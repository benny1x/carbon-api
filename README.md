# Carbon Lua API

Our scripts run on lua5.4 Press `` ` `` in-game for the console (its on the left of 1 on ur keyboard)

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





# Carbon Lua Natives

Call `util.require_natives()` (or `natives.load_natives()` / `require_natives()`) once, then use `NAMESPACE.NATIVE(...)`.

**47** namespaces · **6711** natives

Param codes: `int` `float` `bool` `string` `hash` `vector3` `{x,y,z}` `pointer` `void`

```lua
util.require_natives()
local ped = PLAYER.PLAYER_PED_ID()
local pos = ENTITY.GET_ENTITY_COORDS(ped, false)
print(pos.x, pos.y, pos.z)
```

## Contents

- [BUILTIN](#builtin) (26)
- [APPS](#apps) (17)
- [AUDIO](#audio) (280)
- [BRAIN](#brain) (11)
- [CAMERA](#camera) (247)
- [CLOCK](#clock) (16)
- [CUTSCENE](#cutscene) (56)
- [DATAFILE](#datafile) (57)
- [DECORATOR](#decorator) (12)
- [DLC](#dlc) (14)
- [ENTITY](#entity) (189)
- [EVENT](#event) (13)
- [EXTRAMETADATA](#extrametadata) (49)
- [FIRE](#fire) (21)
- [GRAPHICS](#graphics) (403)
- [GTA](#gta) (25)
- [HUD](#hud) (520)
- [IIS](#iis) (1)
- [INTERIOR](#interior) (45)
- [ITEMSETS](#itemsets) (9)
- [LANDINGPAGE](#landingpage) (4)
- [LOBBY](#lobby) (8)
- [LOCALIZATION](#localization) (3)
- [MISC](#misc) (345)
- [MONEY](#money) (357)
- [NETSHOPPING](#netshopping) (42)
- [NETWORK](#network) (889)
- [OBJECT](#object) (166)
- [PAD](#pad) (50)
- [PATH](#path) (60)
- [PED](#ped) (614)
- [PHYSICS](#physics) (49)
- [PLAYER](#player) (250)
- [RECORDING](#recording) (17)
- [REPLAY](#replay) (6)
- [SAVEMIGRATION](#savemigration) (8)
- [SCRIPT](#script) (38)
- [SECURITY](#security) (3)
- [SHAPETEST](#shapetest) (11)
- [SOCIALCLUB](#socialclub) (84)
- [STATS](#stats) (340)
- [STREAMING](#streaming) (124)
- [TASK](#task) (313)
- [VEHICLE](#vehicle) (775)
- [WATER](#water) (12)
- [WEAPON](#weapon) (123)
- [ZONE](#zone) (9)

## BUILTIN

| Native | Params | Returns |
| --- | --- | --- |
| `BUILTIN.WAIT` | — | blocked: Use script.yield instead |
| `BUILTIN.START_NEW_SCRIPT` | a: string, b: int | `int` |
| `BUILTIN.START_NEW_SCRIPT_WITH_ARGS` | a1: string, a2: pointer, a3: int, a4: int | `int` |
| `BUILTIN.START_NEW_SCRIPT_WITH_NAME_HASH` | a: hash, b: int | `int` |
| `BUILTIN.START_NEW_SCRIPT_WITH_NAME_HASH_AND_ARGS` | a1: hash, a2: pointer, a3: int, a4: int | `int` |
| `BUILTIN.TIMERA` | — | `int` |
| `BUILTIN.TIMERB` | — | `int` |
| `BUILTIN.SETTIMERA` | a: int | `void` |
| `BUILTIN.SETTIMERB` | a: int | `void` |
| `BUILTIN.TIMESTEP` | — | `float` |
| `BUILTIN.SIN` | a: float | `float` |
| `BUILTIN.COS` | a: float | `float` |
| `BUILTIN.SQRT` | a: float | `float` |
| `BUILTIN.POW` | a: float, b: float | `float` |
| `BUILTIN.LOG10` | a: float | `float` |
| `BUILTIN.VMAG` | a1: float, a2: float, a3: float | `float` |
| `BUILTIN.VMAG2` | a1: float, a2: float, a3: float | `float` |
| `BUILTIN.VDIST` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `float` |
| `BUILTIN.VDIST2` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `float` |
| `BUILTIN.SHIFT_LEFT` | a: int, b: int | `int` |
| `BUILTIN.SHIFT_RIGHT` | a: int, b: int | `int` |
| `BUILTIN.FLOOR` | a: float | `int` |
| `BUILTIN.CEIL` | a: float | `int` |
| `BUILTIN.ROUND` | a: float | `int` |
| `BUILTIN.TO_FLOAT` | a: int | `float` |
| `BUILTIN.SET_THIS_THREAD_PRIORITY` | a: int | `void` |

## APPS

| Native | Params | Returns |
| --- | --- | --- |
| `APPS.APP_DATA_VALID` | — | `bool` |
| `APPS.APP_GET_INT` | a: string | `int` |
| `APPS.APP_GET_FLOAT` | a: string | `float` |
| `APPS.APP_GET_STRING` | a: string | `string` |
| `APPS.APP_SET_INT` | a: string, b: int | `void` |
| `APPS.APP_SET_FLOAT` | a: string, b: float | `void` |
| `APPS.APP_SET_STRING` | a: string, b: string | `void` |
| `APPS.APP_SET_APP` | a: string | `void` |
| `APPS.APP_SET_BLOCK` | a: string | `void` |
| `APPS.APP_CLEAR_BLOCK` | — | `void` |
| `APPS.APP_CLOSE_APP` | — | `void` |
| `APPS.APP_CLOSE_BLOCK` | — | `void` |
| `APPS.APP_HAS_LINKED_SOCIAL_CLUB_ACCOUNT` | — | `bool` |
| `APPS.APP_HAS_SYNCED_DATA` | a: string | `bool` |
| `APPS.APP_SAVE_DATA` | — | `void` |
| `APPS.APP_GET_DELETED_FILE_STATUS` | — | `int` |
| `APPS.APP_DELETE_APP_DATA` | a: string | `bool` |

## AUDIO

| Native | Params | Returns |
| --- | --- | --- |
| `AUDIO.PLAY_PED_RINGTONE` | a1: string, a2: int, a3: bool | `void` |
| `AUDIO.IS_PED_RINGTONE_PLAYING` | a: int | `bool` |
| `AUDIO.STOP_PED_RINGTONE` | a: int | `void` |
| `AUDIO.IS_MOBILE_PHONE_CALL_ONGOING` | — | `bool` |
| `AUDIO.IS_MOBILE_INTERFERENCE_ACTIVE` | — | `bool` |
| `AUDIO.GET_CURRENT_TV_SHOW_PLAY_TIME` | — | `int` |
| `AUDIO.CREATE_NEW_SCRIPTED_CONVERSATION` | — | `void` |
| `AUDIO.ADD_LINE_TO_CONVERSATION` | a1: int, a2: string, a3: string, a4: int, a5: int, a6: bool, a7: bool, a8: bool, a9: bool, a10: int, a11: bool, a12: bool, a13: bool | `void` |
| `AUDIO.ADD_PED_TO_CONVERSATION` | a1: int, a2: int, a3: string | `void` |
| `AUDIO.SET_POSITION_FOR_NULL_CONV_PED` | a1: int, a2: float, a3: float, a4: float | `void` |
| `AUDIO.SET_ENTITY_FOR_NULL_CONV_PED` | a: int, b: int | `void` |
| `AUDIO.SET_MICROPHONE_POSITION` | a1: bool, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float | `void` |
| `AUDIO.SET_CONVERSATION_AUDIO_CONTROLLED_BY_ANIM` | a: bool | `void` |
| `AUDIO.SET_CONVERSATION_AUDIO_PLACEHOLDER` | a: bool | `void` |
| `AUDIO.START_SCRIPT_PHONE_CONVERSATION` | a: bool, b: bool | `void` |
| `AUDIO.PRELOAD_SCRIPT_PHONE_CONVERSATION` | a: bool, b: bool | `void` |
| `AUDIO.START_SCRIPT_CONVERSATION` | a1: bool, a2: bool, a3: bool, a4: bool | `void` |
| `AUDIO.PRELOAD_SCRIPT_CONVERSATION` | a1: bool, a2: bool, a3: bool, a4: bool | `void` |
| `AUDIO.START_PRELOADED_CONVERSATION` | — | `void` |
| `AUDIO.GET_IS_PRELOADED_CONVERSATION_READY` | — | `bool` |
| `AUDIO.IS_SCRIPTED_CONVERSATION_ONGOING` | — | `bool` |
| `AUDIO.IS_SCRIPTED_CONVERSATION_LOADED` | — | `bool` |
| `AUDIO.GET_CURRENT_SCRIPTED_CONVERSATION_LINE` | — | `int` |
| `AUDIO.PAUSE_SCRIPTED_CONVERSATION` | a: bool | `void` |
| `AUDIO.RESTART_SCRIPTED_CONVERSATION` | — | `void` |
| `AUDIO.STOP_SCRIPTED_CONVERSATION` | a: bool | `int` |
| `AUDIO.SKIP_TO_NEXT_SCRIPTED_CONVERSATION_LINE` | — | `void` |
| `AUDIO.INTERRUPT_CONVERSATION` | a1: int, a2: string, a3: string | `void` |
| `AUDIO.INTERRUPT_CONVERSATION_AND_PAUSE` | a1: int, a2: string, a3: string | `void` |
| `AUDIO.GET_VARIATION_CHOSEN_FOR_SCRIPTED_LINE` | a: pointer | `int` |
| `AUDIO.SET_NO_DUCKING_FOR_CONVERSATION` | a: bool | `void` |
| `AUDIO.REGISTER_SCRIPT_WITH_AUDIO` | a: int | `void` |
| `AUDIO.UNREGISTER_SCRIPT_WITH_AUDIO` | — | `void` |
| `AUDIO.REQUEST_MISSION_AUDIO_BANK` | a1: string, a2: bool, a3: int | `bool` |
| `AUDIO.REQUEST_AMBIENT_AUDIO_BANK` | a1: string, a2: bool, a3: int | `bool` |
| `AUDIO.REQUEST_SCRIPT_AUDIO_BANK` | a1: string, a2: bool, a3: int | `bool` |
| `AUDIO.HINT_MISSION_AUDIO_BANK` | a1: string, a2: bool, a3: int | `bool` |
| `AUDIO.HINT_AMBIENT_AUDIO_BANK` | a1: string, a2: bool, a3: int | `bool` |
| `AUDIO.HINT_SCRIPT_AUDIO_BANK` | a1: string, a2: bool, a3: int | `bool` |
| `AUDIO.RELEASE_MISSION_AUDIO_BANK` | — | `void` |
| `AUDIO.RELEASE_AMBIENT_AUDIO_BANK` | — | `void` |
| `AUDIO.RELEASE_NAMED_SCRIPT_AUDIO_BANK` | a: string | `void` |
| `AUDIO.RELEASE_SCRIPT_AUDIO_BANK` | — | `void` |
| `AUDIO.UNHINT_AMBIENT_AUDIO_BANK` | — | `void` |
| `AUDIO.UNHINT_SCRIPT_AUDIO_BANK` | — | `void` |
| `AUDIO.UNHINT_NAMED_SCRIPT_AUDIO_BANK` | a: string | `void` |
| `AUDIO.GET_SOUND_ID` | — | `int` |
| `AUDIO.RELEASE_SOUND_ID` | a: int | `void` |
| `AUDIO.PLAY_SOUND` | a1: int, a2: string, a3: string, a4: bool, a5: int, a6: bool | `void` |
| `AUDIO.PLAY_SOUND_FRONTEND` | a1: int, a2: string, a3: string, a4: bool | `void` |
| `AUDIO.PLAY_DEFERRED_SOUND_FRONTEND` | a: string, b: string | `void` |
| `AUDIO.PLAY_SOUND_FROM_ENTITY` | a1: int, a2: string, a3: int, a4: string, a5: bool, a6: int | `void` |
| `AUDIO.PLAY_SOUND_FROM_ENTITY_HASH` | a1: int, a2: hash, a3: int, a4: hash, a5: int, a6: int | `void` |
| `AUDIO.PLAY_SOUND_FROM_COORD` | a1: int, a2: string, a3: float, a4: float, a5: float, a6: string, a7: bool, a8: int, a9: bool | `void` |
| `AUDIO.UPDATE_SOUND_COORD` | a1: int, a2: float, a3: float, a4: float | `void` |
| `AUDIO.STOP_SOUND` | a: int | `void` |
| `AUDIO.GET_NETWORK_ID_FROM_SOUND_ID` | a: int | `int` |
| `AUDIO.GET_SOUND_ID_FROM_NETWORK_ID` | a: int | `int` |
| `AUDIO.SET_VARIABLE_ON_SOUND` | a1: int, a2: string, a3: float | `void` |
| `AUDIO.SET_VARIABLE_ON_STREAM` | a: string, b: float | `void` |
| `AUDIO.OVERRIDE_UNDERWATER_STREAM` | a: string, b: bool | `void` |
| `AUDIO.SET_VARIABLE_ON_UNDER_WATER_STREAM` | a: string, b: float | `void` |
| `AUDIO.HAS_SOUND_FINISHED` | a: int | `bool` |
| `AUDIO.PLAY_PED_AMBIENT_SPEECH_NATIVE` | a1: int, a2: string, a3: string, a4: int | `void` |
| `AUDIO.PLAY_PED_AMBIENT_SPEECH_AND_CLONE_NATIVE` | a1: int, a2: string, a3: string, a4: int | `void` |
| `AUDIO.PLAY_PED_AMBIENT_SPEECH_WITH_VOICE_NATIVE` | a1: int, a2: string, a3: string, a4: string, a5: bool | `void` |
| `AUDIO.PLAY_AMBIENT_SPEECH_FROM_POSITION_NATIVE` | a1: string, a2: string, a3: float, a4: float, a5: float, a6: string | `void` |
| `AUDIO.OVERRIDE_TREVOR_RAGE` | a: string | `void` |
| `AUDIO.RESET_TREVOR_RAGE` | — | `void` |
| `AUDIO.SET_PLAYER_ANGRY` | a: int, b: bool | `void` |
| `AUDIO.PLAY_PAIN` | a1: int, a2: int, a3: int, a4: int | `void` |
| `AUDIO.RELEASE_WEAPON_AUDIO` | — | `void` |
| `AUDIO.ACTIVATE_AUDIO_SLOWMO_MODE` | a: string | `void` |
| `AUDIO.DEACTIVATE_AUDIO_SLOWMO_MODE` | a: string | `void` |
| `AUDIO.SET_AMBIENT_VOICE_NAME` | a: int, b: string | `void` |
| `AUDIO.SET_AMBIENT_VOICE_NAME_HASH` | a: int, b: hash | `void` |
| `AUDIO.GET_AMBIENT_VOICE_NAME_HASH` | a: int | `hash` |
| `AUDIO.SET_PED_VOICE_FULL` | a: int | `void` |
| `AUDIO.SET_PED_RACE_AND_VOICE_GROUP` | a1: int, a2: int, a3: hash | `void` |
| `AUDIO.SET_PED_VOICE_GROUP` | a: int, b: hash | `void` |
| `AUDIO.SET_PED_VOICE_GROUP_FROM_RACE_TO_PVG` | a: int, b: hash | `void` |
| `AUDIO.SET_PED_GENDER` | a: int, b: bool | `void` |
| `AUDIO.STOP_CURRENT_PLAYING_SPEECH` | a: int | `void` |
| `AUDIO.STOP_CURRENT_PLAYING_AMBIENT_SPEECH` | a: int | `void` |
| `AUDIO.IS_AMBIENT_SPEECH_PLAYING` | a: int | `bool` |
| `AUDIO.IS_SCRIPTED_SPEECH_PLAYING` | a: int | `bool` |
| `AUDIO.IS_ANY_SPEECH_PLAYING` | a: int | `bool` |
| `AUDIO.IS_ANY_POSITIONAL_SPEECH_PLAYING` | — | `bool` |
| `AUDIO.DOES_CONTEXT_EXIST_FOR_THIS_PED` | a1: int, a2: string, a3: bool | `bool` |
| `AUDIO.IS_PED_IN_CURRENT_CONVERSATION` | a: int | `bool` |
| `AUDIO.SET_PED_IS_DRUNK` | a: int, b: bool | `void` |
| `AUDIO.PLAY_ANIMAL_VOCALIZATION` | a1: int, a2: int, a3: string | `void` |
| `AUDIO.IS_ANIMAL_VOCALIZATION_PLAYING` | a: int | `bool` |
| `AUDIO.SET_ANIMAL_MOOD` | a: int, b: int | `void` |
| `AUDIO.PLAY_PED_AUDIO_EVENT_ANIM` | a: int, b: string | `void` |
| `AUDIO.IS_MOBILE_PHONE_RADIO_ACTIVE` | — | `bool` |
| `AUDIO.SET_MOBILE_PHONE_RADIO_STATE` | a: bool | `void` |
| `AUDIO.GET_PLAYER_RADIO_STATION_INDEX` | — | `int` |
| `AUDIO.GET_PLAYER_RADIO_STATION_NAME` | — | `string` |
| `AUDIO.GET_RADIO_STATION_NAME` | a: int | `string` |
| `AUDIO.GET_PLAYER_RADIO_STATION_GENRE` | — | `int` |
| `AUDIO.IS_RADIO_RETUNING` | — | `bool` |
| `AUDIO.IS_RADIO_FADED_OUT` | — | `bool` |
| `AUDIO.SET_RADIO_RETUNE_UP` | — | `void` |
| `AUDIO.SET_RADIO_RETUNE_DOWN` | — | `void` |
| `AUDIO.SET_RADIO_TO_STATION_NAME` | a: string | `void` |
| `AUDIO.SET_VEH_RADIO_STATION` | a: int, b: string | `void` |
| `AUDIO.SET_VEH_HAS_NORMAL_RADIO` | a: int | `void` |
| `AUDIO.IS_VEHICLE_RADIO_ON` | a: int | `bool` |
| `AUDIO.SET_VEH_FORCED_RADIO_THIS_FRAME` | a: int | `void` |
| `AUDIO.SET_EMITTER_RADIO_STATION` | a1: string, a2: string, a3: int | `void` |
| `AUDIO.SET_STATIC_EMITTER_ENABLED` | a: string, b: bool | `void` |
| `AUDIO.LINK_STATIC_EMITTER_TO_ENTITY` | a: string, b: int | `void` |
| `AUDIO.SET_RADIO_TO_STATION_INDEX` | a: int | `void` |
| `AUDIO.SET_FRONTEND_RADIO_ACTIVE` | a: bool | `void` |
| `AUDIO.UNLOCK_MISSION_NEWS_STORY` | a: int | `void` |
| `AUDIO.IS_MISSION_NEWS_STORY_UNLOCKED` | a: int | `bool` |
| `AUDIO.GET_AUDIBLE_MUSIC_TRACK_TEXT_ID` | — | `int` |
| `AUDIO.PLAY_END_CREDITS_MUSIC` | a: bool | `void` |
| `AUDIO.SKIP_RADIO_FORWARD` | — | `void` |
| `AUDIO.FREEZE_RADIO_STATION` | a: string | `void` |
| `AUDIO.UNFREEZE_RADIO_STATION` | a: string | `void` |
| `AUDIO.SET_RADIO_AUTO_UNFREEZE` | a: bool | `void` |
| `AUDIO.SET_INITIAL_PLAYER_STATION` | a: string | `void` |
| `AUDIO.SET_USER_RADIO_CONTROL_ENABLED` | a: bool | `void` |
| `AUDIO.SET_RADIO_TRACK` | a: string, b: string | `void` |
| `AUDIO.SET_RADIO_TRACK_WITH_START_OFFSET` | a1: string, a2: string, a3: int | `void` |
| `AUDIO.SET_NEXT_RADIO_TRACK` | a1: string, a2: string, a3: string, a4: string | `void` |
| `AUDIO.SET_VEHICLE_RADIO_LOUD` | a: int, b: bool | `void` |
| `AUDIO.CAN_VEHICLE_RECEIVE_CB_RADIO` | a: int | `bool` |
| `AUDIO.SET_MOBILE_RADIO_ENABLED_DURING_GAMEPLAY` | a: bool | `void` |
| `AUDIO.DOES_PLAYER_VEH_HAVE_RADIO` | — | `bool` |
| `AUDIO.IS_PLAYER_VEH_RADIO_ENABLE` | — | `bool` |
| `AUDIO.SET_VEHICLE_RADIO_ENABLED` | a: int, b: bool | `void` |
| `AUDIO.SET_POSITIONED_PLAYER_VEHICLE_RADIO_EMITTER_ENABLED` | a: int | `void` |
| `AUDIO.SET_CUSTOM_RADIO_TRACK_LIST` | a1: string, a2: string, a3: bool | `void` |
| `AUDIO.CLEAR_CUSTOM_RADIO_TRACK_LIST` | a: string | `void` |
| `AUDIO.GET_NUM_UNLOCKED_RADIO_STATIONS` | — | `int` |
| `AUDIO.FIND_RADIO_STATION_INDEX` | a: hash | `int` |
| `AUDIO.SET_RADIO_STATION_MUSIC_ONLY` | a: string, b: bool | `void` |
| `AUDIO.SET_RADIO_FRONTEND_FADE_TIME` | a: float | `void` |
| `AUDIO.UNLOCK_RADIO_STATION_TRACK_LIST` | a: string, b: string | `void` |
| `AUDIO.LOCK_RADIO_STATION_TRACK_LIST` | a: string, b: string | `void` |
| `AUDIO.UPDATE_UNLOCKABLE_DJ_RADIO_TRACKS` | a: bool | `void` |
| `AUDIO.LOCK_RADIO_STATION` | a: string, b: bool | `void` |
| `AUDIO.SET_RADIO_STATION_AS_FAVOURITE` | a: string, b: bool | `void` |
| `AUDIO.IS_RADIO_STATION_FAVOURITED` | a: string | `bool` |
| `AUDIO.GET_NEXT_AUDIBLE_BEAT` | a1: pointer, a2: pointer, a3: pointer | `bool` |
| `AUDIO.FORCE_MUSIC_TRACK_LIST` | a1: string, a2: string, a3: int | `void` |
| `AUDIO.GET_CURRENT_TRACK_PLAY_TIME` | a: string | `int` |
| `AUDIO.GET_CURRENT_TRACK_SOUND_NAME` | a: string | `hash` |
| `AUDIO.SET_VEHICLE_MISSILE_WARNING_ENABLED` | a: int, b: bool | `void` |
| `AUDIO.SET_AMBIENT_ZONE_STATE` | a1: string, a2: bool, a3: bool | `void` |
| `AUDIO.CLEAR_AMBIENT_ZONE_STATE` | a: string, b: bool | `void` |
| `AUDIO.SET_AMBIENT_ZONE_LIST_STATE` | a1: string, a2: bool, a3: bool | `void` |
| `AUDIO.CLEAR_AMBIENT_ZONE_LIST_STATE` | a: string, b: bool | `void` |
| `AUDIO.SET_AMBIENT_ZONE_STATE_PERSISTENT` | a1: string, a2: bool, a3: bool | `void` |
| `AUDIO.SET_AMBIENT_ZONE_LIST_STATE_PERSISTENT` | a1: string, a2: bool, a3: bool | `void` |
| `AUDIO.IS_AMBIENT_ZONE_ENABLED` | a: string | `bool` |
| `AUDIO.REFRESH_CLOSEST_OCEAN_SHORELINE` | — | `void` |
| `AUDIO.SET_CUTSCENE_AUDIO_OVERRIDE` | a: string | `void` |
| `AUDIO.SET_VARIABLE_ON_SYNCH_SCENE_AUDIO` | a: string, b: float | `void` |
| `AUDIO.PLAY_POLICE_REPORT` | a: string, b: float | `int` |
| `AUDIO.CANCEL_ALL_POLICE_REPORTS` | — | `void` |
| `AUDIO.BLIP_SIREN` | a: int | `void` |
| `AUDIO.OVERRIDE_VEH_HORN` | a1: int, a2: bool, a3: int | `void` |
| `AUDIO.IS_HORN_ACTIVE` | a: int | `bool` |
| `AUDIO.SET_AGGRESSIVE_HORNS` | a: bool | `void` |
| `AUDIO.SET_RADIO_POSITION_AUDIO_MUTE` | a: bool | `void` |
| `AUDIO.SET_VEHICLE_CONVERSATIONS_PERSIST` | a: bool, b: bool | `void` |
| `AUDIO.SET_VEHICLE_CONVERSATIONS_PERSIST_NEW` | a1: bool, a2: bool, a3: bool | `void` |
| `AUDIO.IS_STREAM_PLAYING` | — | `bool` |
| `AUDIO.GET_STREAM_PLAY_TIME` | — | `int` |
| `AUDIO.LOAD_STREAM` | a: string, b: string | `bool` |
| `AUDIO.LOAD_STREAM_WITH_START_OFFSET` | a1: string, a2: int, a3: string | `bool` |
| `AUDIO.PLAY_STREAM_FROM_PED` | a: int | `void` |
| `AUDIO.PLAY_STREAM_FROM_VEHICLE` | a: int | `void` |
| `AUDIO.PLAY_STREAM_FROM_OBJECT` | a: int | `void` |
| `AUDIO.PLAY_STREAM_FRONTEND` | — | `void` |
| `AUDIO.PLAY_STREAM_FROM_POSITION` | a1: float, a2: float, a3: float | `void` |
| `AUDIO.STOP_STREAM` | — | `void` |
| `AUDIO.STOP_PED_SPEAKING` | a: int, b: bool | `void` |
| `AUDIO.BLOCK_ALL_SPEECH_FROM_PED` | a1: int, a2: bool, a3: bool | `void` |
| `AUDIO.STOP_PED_SPEAKING_SYNCED` | a: int, b: bool | `void` |
| `AUDIO.DISABLE_PED_PAIN_AUDIO` | a: int, b: bool | `void` |
| `AUDIO.IS_AMBIENT_SPEECH_DISABLED` | a: int | `bool` |
| `AUDIO.BLOCK_SPEECH_CONTEXT_GROUP` | a: string, b: int | `void` |
| `AUDIO.UNBLOCK_SPEECH_CONTEXT_GROUP` | a: string | `void` |
| `AUDIO.SET_SIREN_WITH_NO_DRIVER` | a: int, b: bool | `void` |
| `AUDIO.SET_SIREN_BYPASS_MP_DRIVER_CHECK` | a: int, b: bool | `void` |
| `AUDIO.TRIGGER_SIREN_AUDIO` | a: int | `void` |
| `AUDIO.SET_HORN_PERMANENTLY_ON` | a: int | `void` |
| `AUDIO.SET_HORN_ENABLED` | a: int, b: bool | `void` |
| `AUDIO.SET_AUDIO_VEHICLE_PRIORITY` | a: int, b: int | `void` |
| `AUDIO.SET_HORN_PERMANENTLY_ON_TIME` | a: int, b: float | `void` |
| `AUDIO.USE_SIREN_AS_HORN` | a: int, b: bool | `void` |
| `AUDIO.FORCE_USE_AUDIO_GAME_OBJECT` | a: int, b: string | `void` |
| `AUDIO.PRELOAD_VEHICLE_AUDIO_BANK` | a: hash | `void` |
| `AUDIO.SET_VEHICLE_STARTUP_REV_SOUND` | a1: int, a2: string, a3: string | `void` |
| `AUDIO.RESET_VEHICLE_STARTUP_REV_SOUND` | a: int | `void` |
| `AUDIO.SET_VEHICLE_FORCE_REVERSE_WARNING` | a: int, b: int | `void` |
| `AUDIO.IS_VEHICLE_AUDIBLY_DAMAGED` | a: int | `bool` |
| `AUDIO.SET_VEHICLE_AUDIO_ENGINE_DAMAGE_FACTOR` | a: int, b: float | `void` |
| `AUDIO.SET_VEHICLE_AUDIO_BODY_DAMAGE_FACTOR` | a: int, b: float | `void` |
| `AUDIO._FORCE_VEHICLE_ENGINE_SYNTH` | a: int, b: bool | `void` |
| `AUDIO.ENABLE_VEHICLE_FANBELT_DAMAGE` | a: int, b: bool | `void` |
| `AUDIO.ENABLE_VEHICLE_EXHAUST_POPS` | a: int, b: bool | `void` |
| `AUDIO.SET_VEHICLE_BOOST_ACTIVE` | a: int, b: bool | `void` |
| `AUDIO.SET_PLAYER_VEHICLE_ALARM_AUDIO_ACTIVE` | a: int, b: bool | `void` |
| `AUDIO.SET_SCRIPT_UPDATE_DOOR_AUDIO` | a: hash, b: bool | `void` |
| `AUDIO.PLAY_VEHICLE_DOOR_OPEN_SOUND` | a: int, b: int | `void` |
| `AUDIO.PLAY_VEHICLE_DOOR_CLOSE_SOUND` | a: int, b: int | `void` |
| `AUDIO.ENABLE_STALL_WARNING_SOUNDS` | a: int, b: bool | `void` |
| `AUDIO._ENABLE_DRAG_RACE_STATIONARY_WARNING_SOUNDS` | a: int, b: bool | `void` |
| `AUDIO.IS_GAME_IN_CONTROL_OF_MUSIC` | — | `bool` |
| `AUDIO.SET_GPS_ACTIVE` | a: bool | `void` |
| `AUDIO.PLAY_MISSION_COMPLETE_AUDIO` | a: string | `void` |
| `AUDIO.IS_MISSION_COMPLETE_PLAYING` | — | `bool` |
| `AUDIO.IS_MISSION_COMPLETE_READY_FOR_UI` | — | `bool` |
| `AUDIO.BLOCK_DEATH_JINGLE` | a: bool | `void` |
| `AUDIO.START_AUDIO_SCENE` | a: string | `bool` |
| `AUDIO.STOP_AUDIO_SCENE` | a: string | `void` |
| `AUDIO.STOP_AUDIO_SCENES` | — | `void` |
| `AUDIO.IS_AUDIO_SCENE_ACTIVE` | a: string | `bool` |
| `AUDIO.SET_AUDIO_SCENE_VARIABLE` | a1: string, a2: string, a3: float | `void` |
| `AUDIO.SET_AUDIO_SCRIPT_CLEANUP_TIME` | a: int | `void` |
| `AUDIO.ADD_ENTITY_TO_AUDIO_MIX_GROUP` | a1: int, a2: string, a3: float | `void` |
| `AUDIO.REMOVE_ENTITY_FROM_AUDIO_MIX_GROUP` | a: int, b: float | `void` |
| `AUDIO.AUDIO_IS_MUSIC_PLAYING` | — | `bool` |
| `AUDIO.AUDIO_IS_SCRIPTED_MUSIC_PLAYING` | — | `bool` |
| `AUDIO.PREPARE_MUSIC_EVENT` | a: string | `bool` |
| `AUDIO.CANCEL_MUSIC_EVENT` | a: string | `bool` |
| `AUDIO.TRIGGER_MUSIC_EVENT` | a: string | `bool` |
| `AUDIO.IS_MUSIC_ONESHOT_PLAYING` | — | `bool` |
| `AUDIO.GET_MUSIC_PLAYTIME` | — | `int` |
| `AUDIO.SET_GLOBAL_RADIO_SIGNAL_LEVEL` | a: int | `void` |
| `AUDIO.RECORD_BROKEN_GLASS` | a1: float, a2: float, a3: float, a4: float | `void` |
| `AUDIO.CLEAR_ALL_BROKEN_GLASS` | — | `void` |
| `AUDIO.SCRIPT_OVERRIDES_WIND_ELEVATION` | a: bool, b: int | `void` |
| `AUDIO.SET_PED_WALLA_DENSITY` | a: float, b: float | `void` |
| `AUDIO.SET_PED_INTERIOR_WALLA_DENSITY` | a: float, b: float | `void` |
| `AUDIO.FORCE_PED_PANIC_WALLA` | — | `void` |
| `AUDIO.PREPARE_ALARM` | a: string | `bool` |
| `AUDIO.START_ALARM` | a: string, b: bool | `void` |
| `AUDIO.STOP_ALARM` | a: string, b: bool | `void` |
| `AUDIO.STOP_ALL_ALARMS` | a: bool | `void` |
| `AUDIO.IS_ALARM_PLAYING` | a: string | `bool` |
| `AUDIO.GET_VEHICLE_DEFAULT_HORN` | a: int | `hash` |
| `AUDIO.GET_VEHICLE_DEFAULT_HORN_IGNORE_MODS` | a: int | `hash` |
| `AUDIO.SET_PED_FOOTSTEPS_EVENTS_ENABLED` | a: int, b: bool | `void` |
| `AUDIO.SET_PED_CLOTH_EVENTS_ENABLED` | a: int, b: bool | `void` |
| `AUDIO.OVERRIDE_PLAYER_GROUND_MATERIAL` | a: hash, b: bool | `void` |
| `AUDIO.USE_FOOTSTEP_SCRIPT_SWEETENERS` | a1: int, a2: bool, a3: hash | `void` |
| `AUDIO.OVERRIDE_MICROPHONE_SETTINGS` | a: hash, b: bool | `void` |
| `AUDIO.FREEZE_MICROPHONE` | — | `void` |
| `AUDIO.DISTANT_COP_CAR_SIRENS` | a: bool | `void` |
| `AUDIO.SET_SIREN_CAN_BE_CONTROLLED_BY_AUDIO` | a: int, b: bool | `void` |
| `AUDIO.ENABLE_STUNT_JUMP_AUDIO` | — | `void` |
| `AUDIO.SET_AUDIO_FLAG` | a: string, b: bool | `void` |
| `AUDIO.PREPARE_SYNCHRONIZED_AUDIO_EVENT` | a: string, b: int | `bool` |
| `AUDIO.PREPARE_SYNCHRONIZED_AUDIO_EVENT_FOR_SCENE` | a: int, b: string | `bool` |
| `AUDIO.PLAY_SYNCHRONIZED_AUDIO_EVENT` | a: int | `bool` |
| `AUDIO.STOP_SYNCHRONIZED_AUDIO_EVENT` | a: int | `bool` |
| `AUDIO.INIT_SYNCH_SCENE_AUDIO_WITH_POSITION` | a1: string, a2: float, a3: float, a4: float | `void` |
| `AUDIO.INIT_SYNCH_SCENE_AUDIO_WITH_ENTITY` | a: string, b: int | `void` |
| `AUDIO.SET_AUDIO_SPECIAL_EFFECT_MODE` | a: int | `void` |
| `AUDIO.SET_PORTAL_SETTINGS_OVERRIDE` | a: string, b: string | `void` |
| `AUDIO.SET_INDIVIDUAL_PORTAL_SETTINGS_OVERRIDE` | a1: hash, a2: int, a3: int, a4: string | `void` |
| `AUDIO.REMOVE_PORTAL_SETTINGS_OVERRIDE` | a: string | `void` |
| `AUDIO.REMOVE_INDIVIDUAL_PORTAL_SETTINGS_OVERRIDE` | a1: hash, a2: int, a3: int | `void` |
| `AUDIO.STOP_SMOKE_GRENADE_EXPLOSION_SOUNDS` | — | `void` |
| `AUDIO.GET_MUSIC_VOL_SLIDER` | — | `int` |
| `AUDIO.REQUEST_TENNIS_BANKS` | a: int | `void` |
| `AUDIO.UNREQUEST_TENNIS_BANKS` | — | `void` |
| `AUDIO.SET_SKIP_MINIGUN_SPIN_UP_AUDIO` | a: bool | `void` |
| `AUDIO.STOP_CUTSCENE_AUDIO` | — | `void` |
| `AUDIO.HAS_LOADED_MP_DATA_SET` | — | `bool` |
| `AUDIO.HAS_LOADED_SP_DATA_SET` | — | `bool` |
| `AUDIO.GET_VEHICLE_HORN_SOUND_INDEX` | a: int | `int` |
| `AUDIO.SET_VEHICLE_HORN_SOUND_INDEX` | a: int, b: int | `void` |

## BRAIN

| Native | Params | Returns |
| --- | --- | --- |
| `BRAIN.ADD_SCRIPT_TO_RANDOM_PED` | a1: string, a2: hash, a3: float, a4: float | `void` |
| `BRAIN.REGISTER_OBJECT_SCRIPT_BRAIN` | a1: string, a2: hash, a3: int, a4: float, a5: int, a6: int | `void` |
| `BRAIN.IS_OBJECT_WITHIN_BRAIN_ACTIVATION_RANGE` | a: int | `bool` |
| `BRAIN.REGISTER_WORLD_POINT_SCRIPT_BRAIN` | a1: string, a2: float, a3: int | `void` |
| `BRAIN.IS_WORLD_POINT_WITHIN_BRAIN_ACTIVATION_RANGE` | — | `bool` |
| `BRAIN.ENABLE_SCRIPT_BRAIN_SET` | a: int | `void` |
| `BRAIN.DISABLE_SCRIPT_BRAIN_SET` | a: int | `void` |
| `BRAIN.REACTIVATE_ALL_WORLD_BRAINS_THAT_ARE_WAITING_TILL_OUT_OF_RANGE` | — | `void` |
| `BRAIN.REACTIVATE_ALL_OBJECT_BRAINS_THAT_ARE_WAITING_TILL_OUT_OF_RANGE` | — | `void` |
| `BRAIN.REACTIVATE_NAMED_WORLD_BRAINS_WAITING_TILL_OUT_OF_RANGE` | a: string | `void` |
| `BRAIN.REACTIVATE_NAMED_OBJECT_BRAINS_WAITING_TILL_OUT_OF_RANGE` | a: string | `void` |

## CAMERA

| Native | Params | Returns |
| --- | --- | --- |
| `CAMERA.RENDER_SCRIPT_CAMS` | a1: bool, a2: bool, a3: int, a4: bool, a5: bool, a6: int | `void` |
| `CAMERA.STOP_RENDERING_SCRIPT_CAMS_USING_CATCH_UP` | a1: bool, a2: float, a3: int, a4: int | `void` |
| `CAMERA.CREATE_CAM` | a: string, b: bool | `int` |
| `CAMERA.CREATE_CAM_WITH_PARAMS` | a1: string, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: int | `int` |
| `CAMERA.CREATE_CAMERA` | a: hash, b: bool | `int` |
| `CAMERA.CREATE_CAMERA_WITH_PARAMS` | a1: hash, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: int | `int` |
| `CAMERA.DESTROY_CAM` | a: int, b: bool | `void` |
| `CAMERA.DESTROY_ALL_CAMS` | a: bool | `void` |
| `CAMERA.DOES_CAM_EXIST` | a: int | `bool` |
| `CAMERA.SET_CAM_ACTIVE` | a: int, b: bool | `void` |
| `CAMERA.IS_CAM_ACTIVE` | a: int | `bool` |
| `CAMERA.IS_CAM_RENDERING` | a: int | `bool` |
| `CAMERA.GET_RENDERING_CAM` | — | `int` |
| `CAMERA.GET_CAM_COORD` | a: int | `vector3` |
| `CAMERA.GET_CAM_ROT` | a: int, b: int | `vector3` |
| `CAMERA.GET_CAM_FOV` | a: int | `float` |
| `CAMERA.GET_CAM_NEAR_CLIP` | a: int | `float` |
| `CAMERA.GET_CAM_FAR_CLIP` | a: int | `float` |
| `CAMERA.GET_CAM_NEAR_DOF` | a: int | `float` |
| `CAMERA.GET_CAM_FAR_DOF` | a: int | `float` |
| `CAMERA.GET_CAM_DOF_STRENGTH` | a: int | `float` |
| `CAMERA.SET_CAM_PARAMS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: int, a11: int, a12: int | `void` |
| `CAMERA.SET_CAM_COORD` | a1: int, a2: float, a3: float, a4: float | `void` |
| `CAMERA.SET_CAM_ROT` | a1: int, a2: float, a3: float, a4: float, a5: int | `void` |
| `CAMERA.SET_CAM_FOV` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_NEAR_CLIP` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_FAR_CLIP` | a: int, b: float | `void` |
| `CAMERA.FORCE_CAM_FAR_CLIP` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_MOTION_BLUR_STRENGTH` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_NEAR_DOF` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_FAR_DOF` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_STRENGTH` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_PLANES` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `CAMERA.SET_CAM_USE_SHALLOW_DOF_MODE` | a: int, b: bool | `void` |
| `CAMERA.SET_USE_HI_DOF` | — | `void` |
| `CAMERA.SET_USE_HI_DOF_ON_SYNCED_SCENE_THIS_UPDATE` | — | `void` |
| `CAMERA.SET_CAM_DOF_OVERRIDDEN_FOCUS_DISTANCE` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_OVERRIDDEN_FOCUS_DISTANCE_BLEND_LEVEL` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_FNUMBER_OF_LENS` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_FOCAL_LENGTH_MULTIPLIER` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_FOCUS_DISTANCE_BIAS` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_MAX_NEAR_IN_FOCUS_DISTANCE` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_MAX_NEAR_IN_FOCUS_DISTANCE_BLEND_LEVEL` | a: int, b: float | `void` |
| `CAMERA.SET_CAM_DOF_SHOULD_KEEP_LOOK_AT_TARGET_IN_FOCUS` | a: int, b: bool | `void` |
| `CAMERA.ATTACH_CAM_TO_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool | `void` |
| `CAMERA.ATTACH_CAM_TO_PED_BONE` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: bool | `void` |
| `CAMERA.HARD_ATTACH_CAM_TO_PED_BONE` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool | `void` |
| `CAMERA.HARD_ATTACH_CAM_TO_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool | `void` |
| `CAMERA.ATTACH_CAM_TO_VEHICLE_BONE` | a1: int, a2: int, a3: int, a4: bool, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: bool | `void` |
| `CAMERA.DETACH_CAM` | a: int | `void` |
| `CAMERA.SET_CAM_INHERIT_ROLL_VEHICLE` | a: int, b: bool | `void` |
| `CAMERA.POINT_CAM_AT_COORD` | a1: int, a2: float, a3: float, a4: float | `void` |
| `CAMERA.POINT_CAM_AT_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool | `void` |
| `CAMERA.POINT_CAM_AT_PED_BONE` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: bool | `void` |
| `CAMERA.STOP_CAM_POINTING` | a: int | `void` |
| `CAMERA.SET_CAM_AFFECTS_AIMING` | a: int, b: bool | `void` |
| `CAMERA.SET_CAM_CONTROLS_MINI_MAP_HEADING` | a: int, b: bool | `void` |
| `CAMERA.SET_CAM_IS_INSIDE_VEHICLE` | a: int, b: bool | `void` |
| `CAMERA.ALLOW_MOTION_BLUR_DECAY` | a: int, b: bool | `void` |
| `CAMERA.SET_CAM_DEBUG_NAME` | a: int, b: string | `void` |
| `CAMERA.GET_DEBUG_CAM` | — | `int` |
| `CAMERA.ADD_CAM_SPLINE_NODE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int | `void` |
| `CAMERA.ADD_CAM_SPLINE_NODE_USING_CAMERA_FRAME` | a1: int, a2: int, a3: int, a4: int | `void` |
| `CAMERA.ADD_CAM_SPLINE_NODE_USING_CAMERA` | a1: int, a2: int, a3: int, a4: int | `void` |
| `CAMERA.ADD_CAM_SPLINE_NODE_USING_GAMEPLAY_FRAME` | a1: int, a2: int, a3: int | `void` |
| `CAMERA.SET_CAM_SPLINE_PHASE` | a: int, b: float | `void` |
| `CAMERA.GET_CAM_SPLINE_PHASE` | a: int | `float` |
| `CAMERA.GET_CAM_SPLINE_NODE_PHASE` | a: int | `float` |
| `CAMERA.SET_CAM_SPLINE_DURATION` | a: int, b: int | `void` |
| `CAMERA.SET_CAM_SPLINE_SMOOTHING_STYLE` | a: int, b: int | `void` |
| `CAMERA.GET_CAM_SPLINE_NODE_INDEX` | a: int | `int` |
| `CAMERA.SET_CAM_SPLINE_NODE_EASE` | a1: int, a2: int, a3: int, a4: float | `void` |
| `CAMERA.SET_CAM_SPLINE_NODE_VELOCITY_SCALE` | a1: int, a2: int, a3: float | `void` |
| `CAMERA.OVERRIDE_CAM_SPLINE_VELOCITY` | a1: int, a2: int, a3: float, a4: float | `void` |
| `CAMERA.OVERRIDE_CAM_SPLINE_MOTION_BLUR` | a1: int, a2: int, a3: float, a4: float | `void` |
| `CAMERA.SET_CAM_SPLINE_NODE_EXTRA_FLAGS` | a1: int, a2: int, a3: int | `void` |
| `CAMERA.IS_CAM_SPLINE_PAUSED` | a: int | `bool` |
| `CAMERA.INTERPOLATE_CAMERA_WITH_PARAMS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: int, a11: int, a12: int, a13: int | `void` |
| `CAMERA._ACTIVATE_CAM_WITH_INTERP_AND_FOV_CURVE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `CAMERA.SET_CAM_ACTIVE_WITH_INTERP` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `CAMERA.IS_CAM_INTERPOLATING` | a: int | `bool` |
| `CAMERA.SHAKE_CAM` | a1: int, a2: string, a3: float | `void` |
| `CAMERA.ANIMATED_SHAKE_CAM` | a1: int, a2: string, a3: string, a4: string, a5: float | `void` |
| `CAMERA.IS_CAM_SHAKING` | a: int | `bool` |
| `CAMERA.SET_CAM_SHAKE_AMPLITUDE` | a: int, b: float | `void` |
| `CAMERA.STOP_CAM_SHAKING` | a: int, b: bool | `void` |
| `CAMERA.SHAKE_SCRIPT_GLOBAL` | a: string, b: float | `void` |
| `CAMERA.ANIMATED_SHAKE_SCRIPT_GLOBAL` | a1: string, a2: string, a3: string, a4: float | `void` |
| `CAMERA.IS_SCRIPT_GLOBAL_SHAKING` | — | `bool` |
| `CAMERA.STOP_SCRIPT_GLOBAL_SHAKING` | a: bool | `void` |
| `CAMERA.TRIGGER_VEHICLE_PART_BROKEN_CAMERA_SHAKE` | a1: int, a2: int, a3: float | `void` |
| `CAMERA.PLAY_CAM_ANIM` | a1: int, a2: string, a3: string, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool, a11: int | `bool` |
| `CAMERA.IS_CAM_PLAYING_ANIM` | a1: int, a2: string, a3: string | `bool` |
| `CAMERA.SET_CAM_ANIM_CURRENT_PHASE` | a: int, b: float | `void` |
| `CAMERA.GET_CAM_ANIM_CURRENT_PHASE` | a: int | `float` |
| `CAMERA.PLAY_SYNCHRONIZED_CAM_ANIM` | a1: int, a2: int, a3: string, a4: string | `bool` |
| `CAMERA.SET_FLY_CAM_HORIZONTAL_RESPONSE` | a1: int, a2: float, a3: float, a4: float | `void` |
| `CAMERA.SET_FLY_CAM_VERTICAL_RESPONSE` | a1: int, a2: float, a3: float, a4: float | `void` |
| `CAMERA.SET_FLY_CAM_MAX_HEIGHT` | a: int, b: float | `void` |
| `CAMERA.SET_FLY_CAM_COORD_AND_CONSTRAIN` | a1: int, a2: float, a3: float, a4: float | `void` |
| `CAMERA.SET_FLY_CAM_VERTICAL_CONTROLS_THIS_UPDATE` | a: int | `void` |
| `CAMERA.WAS_FLY_CAM_CONSTRAINED_ON_PREVIOUS_UDPATE` | a: int | `bool` |
| `CAMERA.IS_SCREEN_FADED_OUT` | — | `bool` |
| `CAMERA.IS_SCREEN_FADED_IN` | — | `bool` |
| `CAMERA.IS_SCREEN_FADING_OUT` | — | `bool` |
| `CAMERA.IS_SCREEN_FADING_IN` | — | `bool` |
| `CAMERA.DO_SCREEN_FADE_IN` | a: int | `void` |
| `CAMERA.DO_SCREEN_FADE_OUT` | a: int | `void` |
| `CAMERA.SET_WIDESCREEN_BORDERS` | a: bool, b: int | `void` |
| `CAMERA.ARE_WIDESCREEN_BORDERS_ACTIVE` | — | `bool` |
| `CAMERA.GET_GAMEPLAY_CAM_COORD` | — | `vector3` |
| `CAMERA.GET_GAMEPLAY_CAM_ROT` | a: int | `vector3` |
| `CAMERA.GET_GAMEPLAY_CAM_FOV` | — | `float` |
| `CAMERA.SET_GAMEPLAY_CAM_MOTION_BLUR_SCALING_THIS_UPDATE` | a: float | `void` |
| `CAMERA.SET_GAMEPLAY_CAM_MAX_MOTION_BLUR_STRENGTH_THIS_UPDATE` | a: float | `void` |
| `CAMERA.GET_GAMEPLAY_CAM_RELATIVE_HEADING` | — | `float` |
| `CAMERA.SET_GAMEPLAY_CAM_RELATIVE_HEADING` | a: float | `void` |
| `CAMERA.GET_GAMEPLAY_CAM_RELATIVE_PITCH` | — | `float` |
| `CAMERA.SET_GAMEPLAY_CAM_RELATIVE_PITCH` | a: float, b: float | `void` |
| `CAMERA.RESET_GAMEPLAY_CAM_FULL_ATTACH_PARENT_TRANSFORM_TIMER` | — | `void` |
| `CAMERA.FORCE_CAMERA_RELATIVE_HEADING_AND_PITCH` | a1: float, a2: float, a3: float | `void` |
| `CAMERA.FORCE_BONNET_CAMERA_RELATIVE_HEADING_AND_PITCH` | a: float, b: float | `void` |
| `CAMERA.SET_FIRST_PERSON_SHOOTER_CAMERA_HEADING` | a: float | `void` |
| `CAMERA.SET_FIRST_PERSON_SHOOTER_CAMERA_PITCH` | a: float | `void` |
| `CAMERA.SET_SCRIPTED_CAMERA_IS_FIRST_PERSON_THIS_FRAME` | a: bool | `void` |
| `CAMERA.SHAKE_GAMEPLAY_CAM` | a: string, b: float | `void` |
| `CAMERA.IS_GAMEPLAY_CAM_SHAKING` | — | `bool` |
| `CAMERA.SET_GAMEPLAY_CAM_SHAKE_AMPLITUDE` | a: float | `void` |
| `CAMERA.STOP_GAMEPLAY_CAM_SHAKING` | a: bool | `void` |
| `CAMERA.SET_GAMEPLAY_CAM_FOLLOW_PED_THIS_UPDATE` | a: int | `void` |
| `CAMERA.IS_GAMEPLAY_CAM_RENDERING` | — | `bool` |
| `CAMERA.IS_INTERPOLATING_FROM_SCRIPT_CAMS` | — | `bool` |
| `CAMERA.IS_INTERPOLATING_TO_SCRIPT_CAMS` | — | `bool` |
| `CAMERA.SET_GAMEPLAY_CAM_ALTITUDE_FOV_SCALING_STATE` | a: bool | `void` |
| `CAMERA.DISABLE_GAMEPLAY_CAM_ALTITUDE_FOV_SCALING_THIS_UPDATE` | — | `void` |
| `CAMERA.IS_GAMEPLAY_CAM_LOOKING_BEHIND` | — | `bool` |
| `CAMERA.SET_GAMEPLAY_CAM_IGNORE_ENTITY_COLLISION_THIS_UPDATE` | a: int | `void` |
| `CAMERA.DISABLE_CAM_COLLISION_FOR_OBJECT` | a: int | `void` |
| `CAMERA.BYPASS_CAMERA_COLLISION_BUOYANCY_TEST_THIS_UPDATE` | — | `void` |
| `CAMERA.SET_GAMEPLAY_CAM_ENTITY_TO_LIMIT_FOCUS_OVER_BOUNDING_SPHERE_THIS_UPDATE` | a: int | `void` |
| `CAMERA.DISABLE_FIRST_PERSON_CAMERA_WATER_CLIPPING_TEST_THIS_UPDATE` | — | `void` |
| `CAMERA.SET_FOLLOW_CAM_IGNORE_ATTACH_PARENT_MOVEMENT_THIS_UPDATE` | — | `void` |
| `CAMERA.IS_SPHERE_VISIBLE` | a1: float, a2: float, a3: float, a4: float | `bool` |
| `CAMERA.IS_FOLLOW_PED_CAM_ACTIVE` | — | `bool` |
| `CAMERA.SET_FOLLOW_PED_CAM_THIS_UPDATE` | a: string, b: int | `bool` |
| `CAMERA.USE_SCRIPT_CAM_FOR_AMBIENT_POPULATION_ORIGIN_THIS_FRAME` | a: bool, b: bool | `void` |
| `CAMERA.SET_FOLLOW_PED_CAM_LADDER_ALIGN_THIS_UPDATE` | — | `void` |
| `CAMERA.SET_THIRD_PERSON_CAM_RELATIVE_HEADING_LIMITS_THIS_UPDATE` | a: float, b: float | `void` |
| `CAMERA.SET_THIRD_PERSON_CAM_RELATIVE_PITCH_LIMITS_THIS_UPDATE` | a: float, b: float | `void` |
| `CAMERA.SET_THIRD_PERSON_CAM_ORBIT_DISTANCE_LIMITS_THIS_UPDATE` | a: float, b: float | `void` |
| `CAMERA._GET_THIRD_PERSON_CAM_MIN_ORBIT_DISTANCE_SPRING` | — | `float` |
| `CAMERA._GET_THIRD_PERSON_CAM_MAX_ORBIT_DISTANCE_SPRING` | — | `float` |
| `CAMERA.SET_IN_VEHICLE_CAM_STATE_THIS_UPDATE` | a: int, b: int | `void` |
| `CAMERA.DISABLE_ON_FOOT_FIRST_PERSON_VIEW_THIS_UPDATE` | — | `void` |
| `CAMERA.DISABLE_FIRST_PERSON_FLASH_EFFECT_THIS_UPDATE` | — | `void` |
| `CAMERA.BLOCK_FIRST_PERSON_ORIENTATION_RESET_THIS_UPDATE` | — | `void` |
| `CAMERA.GET_FOLLOW_PED_CAM_ZOOM_LEVEL` | — | `int` |
| `CAMERA.GET_FOLLOW_PED_CAM_VIEW_MODE` | — | `int` |
| `CAMERA.SET_FOLLOW_PED_CAM_VIEW_MODE` | a: int | `void` |
| `CAMERA.IS_FOLLOW_VEHICLE_CAM_ACTIVE` | — | `bool` |
| `CAMERA.SET_FOLLOW_VEHICLE_CAM_HIGH_ANGLE_MODE_THIS_UPDATE` | a: bool | `void` |
| `CAMERA.SET_FOLLOW_VEHICLE_CAM_HIGH_ANGLE_MODE_EVERY_UPDATE` | a: bool, b: bool | `void` |
| `CAMERA.SET_TABLE_GAMES_CAMERA_THIS_UPDATE` | a: hash | `bool` |
| `CAMERA.GET_FOLLOW_VEHICLE_CAM_ZOOM_LEVEL` | — | `int` |
| `CAMERA.SET_FOLLOW_VEHICLE_CAM_ZOOM_LEVEL` | a: int | `void` |
| `CAMERA.GET_FOLLOW_VEHICLE_CAM_VIEW_MODE` | — | `int` |
| `CAMERA.SET_FOLLOW_VEHICLE_CAM_VIEW_MODE` | a: int | `void` |
| `CAMERA.GET_CAM_VIEW_MODE_FOR_CONTEXT` | a: int | `int` |
| `CAMERA.SET_CAM_VIEW_MODE_FOR_CONTEXT` | a: int, b: int | `void` |
| `CAMERA.GET_CAM_ACTIVE_VIEW_MODE_CONTEXT` | — | `int` |
| `CAMERA.USE_VEHICLE_CAM_STUNT_SETTINGS_THIS_UPDATE` | — | `void` |
| `CAMERA.USE_DEDICATED_STUNT_CAMERA_THIS_UPDATE` | a: string | `void` |
| `CAMERA.FORCE_VEHICLE_CAM_STUNT_SETTINGS_THIS_UPDATE` | — | `void` |
| `CAMERA.SET_FOLLOW_VEHICLE_CAM_SEAT_THIS_UPDATE` | a: int | `void` |
| `CAMERA.IS_AIM_CAM_ACTIVE` | — | `bool` |
| `CAMERA.IS_AIM_CAM_ACTIVE_IN_ACCURATE_MODE` | — | `bool` |
| `CAMERA.IS_FIRST_PERSON_AIM_CAM_ACTIVE` | — | `bool` |
| `CAMERA.DISABLE_AIM_CAM_THIS_UPDATE` | — | `void` |
| `CAMERA.GET_FIRST_PERSON_AIM_CAM_ZOOM_FACTOR` | — | `float` |
| `CAMERA.SET_FIRST_PERSON_AIM_CAM_ZOOM_FACTOR` | a: float | `void` |
| `CAMERA.SET_FIRST_PERSON_AIM_CAM_ZOOM_FACTOR_LIMITS_THIS_UPDATE` | a: float, b: float | `void` |
| `CAMERA.SET_FIRST_PERSON_AIM_CAM_RELATIVE_HEADING_LIMITS_THIS_UPDATE` | a: float, b: float | `void` |
| `CAMERA.SET_FIRST_PERSON_AIM_CAM_RELATIVE_PITCH_LIMITS_THIS_UPDATE` | a: float, b: float | `void` |
| `CAMERA.SET_FIRST_PERSON_AIM_CAM_NEAR_CLIP_THIS_UPDATE` | a: float | `void` |
| `CAMERA.SET_THIRD_PERSON_AIM_CAM_NEAR_CLIP_THIS_UPDATE` | a: float | `void` |
| `CAMERA.SET_ALLOW_CUSTOM_VEHICLE_DRIVE_BY_CAM_THIS_UPDATE` | a: bool | `void` |
| `CAMERA.FORCE_TIGHTSPACE_CUSTOM_FRAMING_THIS_UPDATE` | — | `void` |
| `CAMERA.GET_FINAL_RENDERED_CAM_COORD` | — | `vector3` |
| `CAMERA.GET_FINAL_RENDERED_CAM_ROT` | a: int | `vector3` |
| `CAMERA.GET_FINAL_RENDERED_REMOTE_PLAYER_CAM_ROT` | a: int, b: int | `vector3` |
| `CAMERA.GET_FINAL_RENDERED_CAM_FOV` | — | `float` |
| `CAMERA.GET_FINAL_RENDERED_REMOTE_PLAYER_CAM_FOV` | a: int | `float` |
| `CAMERA.GET_FINAL_RENDERED_CAM_NEAR_CLIP` | — | `float` |
| `CAMERA.GET_FINAL_RENDERED_CAM_FAR_CLIP` | — | `float` |
| `CAMERA.GET_FINAL_RENDERED_CAM_NEAR_DOF` | — | `float` |
| `CAMERA.GET_FINAL_RENDERED_CAM_FAR_DOF` | — | `float` |
| `CAMERA.GET_FINAL_RENDERED_CAM_MOTION_BLUR_STRENGTH` | — | `float` |
| `CAMERA.SET_GAMEPLAY_COORD_HINT` | a1: float, a2: float, a3: float, a4: int, a5: int, a6: int, a7: int | `void` |
| `CAMERA.SET_GAMEPLAY_PED_HINT` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: int, a7: int, a8: int | `void` |
| `CAMERA.SET_GAMEPLAY_VEHICLE_HINT` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: int, a7: int, a8: int | `void` |
| `CAMERA.SET_GAMEPLAY_OBJECT_HINT` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: int, a7: int, a8: int | `void` |
| `CAMERA.SET_GAMEPLAY_ENTITY_HINT` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: int, a7: int, a8: int, a9: int | `void` |
| `CAMERA.IS_GAMEPLAY_HINT_ACTIVE` | — | `bool` |
| `CAMERA.STOP_GAMEPLAY_HINT` | a: bool | `void` |
| `CAMERA.STOP_GAMEPLAY_HINT_BEING_CANCELLED_THIS_UPDATE` | a: bool | `void` |
| `CAMERA.STOP_CODE_GAMEPLAY_HINT` | a: bool | `void` |
| `CAMERA.IS_CODE_GAMEPLAY_HINT_ACTIVE` | — | `bool` |
| `CAMERA.SET_GAMEPLAY_HINT_FOV` | a: float | `void` |
| `CAMERA.SET_GAMEPLAY_HINT_FOLLOW_DISTANCE_SCALAR` | a: float | `void` |
| `CAMERA.SET_GAMEPLAY_HINT_BASE_ORBIT_PITCH_OFFSET` | a: float | `void` |
| `CAMERA.SET_GAMEPLAY_HINT_CAMERA_RELATIVE_SIDE_OFFSET` | a: float | `void` |
| `CAMERA.SET_GAMEPLAY_HINT_CAMERA_RELATIVE_VERTICAL_OFFSET` | a: float | `void` |
| `CAMERA.SET_GAMEPLAY_HINT_CAMERA_BLEND_TO_FOLLOW_PED_MEDIUM_VIEW_MODE` | a: bool | `void` |
| `CAMERA.SET_CINEMATIC_BUTTON_ACTIVE` | a: bool | `void` |
| `CAMERA.IS_CINEMATIC_CAM_RENDERING` | — | `bool` |
| `CAMERA.SHAKE_CINEMATIC_CAM` | a: string, b: float | `void` |
| `CAMERA.IS_CINEMATIC_CAM_SHAKING` | — | `bool` |
| `CAMERA.SET_CINEMATIC_CAM_SHAKE_AMPLITUDE` | a: float | `void` |
| `CAMERA.STOP_CINEMATIC_CAM_SHAKING` | a: bool | `void` |
| `CAMERA.DISABLE_CINEMATIC_BONNET_CAMERA_THIS_UPDATE` | — | `void` |
| `CAMERA.DISABLE_CINEMATIC_VEHICLE_IDLE_MODE_THIS_UPDATE` | — | `void` |
| `CAMERA.INVALIDATE_CINEMATIC_VEHICLE_IDLE_MODE` | — | `void` |
| `CAMERA.INVALIDATE_IDLE_CAM` | — | `void` |
| `CAMERA.IS_CINEMATIC_IDLE_CAM_RENDERING` | — | `bool` |
| `CAMERA.IS_CINEMATIC_FIRST_PERSON_VEHICLE_INTERIOR_CAM_RENDERING` | — | `bool` |
| `CAMERA.CREATE_CINEMATIC_SHOT` | a1: hash, a2: int, a3: bool, a4: int | `void` |
| `CAMERA.IS_CINEMATIC_SHOT_ACTIVE` | a: hash | `bool` |
| `CAMERA.STOP_CINEMATIC_SHOT` | a: hash | `void` |
| `CAMERA.FORCE_CINEMATIC_RENDERING_THIS_UPDATE` | a: bool | `void` |
| `CAMERA.SET_CINEMATIC_MODE_ACTIVE` | a: bool | `void` |
| `CAMERA.IS_IN_VEHICLE_MOBILE_PHONE_CAMERA_RENDERING` | — | `bool` |
| `CAMERA.DISABLE_CINEMATIC_SLOW_MO_THIS_UPDATE` | — | `bool` |
| `CAMERA.IS_BONNET_CINEMATIC_CAM_RENDERING` | — | `bool` |
| `CAMERA.IS_CINEMATIC_CAM_INPUT_ACTIVE` | — | `bool` |
| `CAMERA.IGNORE_MENU_PREFERENCE_FOR_BONNET_CAMERA_THIS_UPDATE` | — | `void` |
| `CAMERA.BYPASS_CUTSCENE_CAM_RENDERING_THIS_UPDATE` | — | `void` |
| `CAMERA.STOP_CUTSCENE_CAM_SHAKING` | a: int | `void` |
| `CAMERA.SET_CUTSCENE_CAM_FAR_CLIP_THIS_UPDATE` | a: float | `void` |
| `CAMERA.GET_FOCUS_PED_ON_SCREEN` | a1: float, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int | `int` |
| `CAMERA.DISABLE_NEAR_CLIP_SCAN_THIS_UPDATE` | — | `void` |
| `CAMERA.SET_CAM_DEATH_FAIL_EFFECT_STATE` | a: int | `void` |
| `CAMERA.SET_FIRST_PERSON_FLASH_EFFECT_TYPE` | a: int | `void` |
| `CAMERA.SET_FIRST_PERSON_FLASH_EFFECT_VEHICLE_MODEL_NAME` | a: string | `void` |
| `CAMERA.SET_FIRST_PERSON_FLASH_EFFECT_VEHICLE_MODEL_HASH` | a: hash | `void` |
| `CAMERA.IS_ALLOWED_INDEPENDENT_CAMERA_MODES` | — | `bool` |
| `CAMERA.CAMERA_PREVENT_COLLISION_SETTINGS_FOR_TRIPLEHEAD_IN_INTERIORS_THIS_UPDATE` | — | `void` |
| `CAMERA.REPLAY_GET_MAX_DISTANCE_ALLOWED_FROM_PLAYER` | — | `float` |

## CLOCK

| Native | Params | Returns |
| --- | --- | --- |
| `CLOCK.SET_CLOCK_TIME` | a1: int, a2: int, a3: int | `void` |
| `CLOCK.PAUSE_CLOCK` | a: bool | `void` |
| `CLOCK.ADVANCE_CLOCK_TIME_TO` | a1: int, a2: int, a3: int | `void` |
| `CLOCK.ADD_TO_CLOCK_TIME` | a1: int, a2: int, a3: int | `void` |
| `CLOCK.GET_CLOCK_HOURS` | — | `int` |
| `CLOCK.GET_CLOCK_MINUTES` | — | `int` |
| `CLOCK.GET_CLOCK_SECONDS` | — | `int` |
| `CLOCK.SET_CLOCK_DATE` | a1: int, a2: int, a3: int | `void` |
| `CLOCK.GET_CLOCK_DAY_OF_WEEK` | — | `int` |
| `CLOCK.GET_CLOCK_DAY_OF_MONTH` | — | `int` |
| `CLOCK.GET_CLOCK_MONTH` | — | `int` |
| `CLOCK.GET_CLOCK_YEAR` | — | `int` |
| `CLOCK.GET_MILLISECONDS_PER_GAME_MINUTE` | — | `int` |
| `CLOCK.GET_POSIX_TIME` | a1: pointer, a2: pointer, a3: pointer, a4: pointer, a5: pointer, a6: pointer | `void` |
| `CLOCK.GET_UTC_TIME` | a1: pointer, a2: pointer, a3: pointer, a4: pointer, a5: pointer, a6: pointer | `void` |
| `CLOCK.GET_LOCAL_TIME` | a1: pointer, a2: pointer, a3: pointer, a4: pointer, a5: pointer, a6: pointer | `void` |

## CUTSCENE

| Native | Params | Returns |
| --- | --- | --- |
| `CUTSCENE.REQUEST_CUTSCENE` | a: string, b: int | `void` |
| `CUTSCENE.REQUEST_CUTSCENE_WITH_PLAYBACK_LIST` | a1: string, a2: int, a3: int | `void` |
| `CUTSCENE.REMOVE_CUTSCENE` | — | `void` |
| `CUTSCENE.HAS_CUTSCENE_LOADED` | — | `bool` |
| `CUTSCENE.HAS_THIS_CUTSCENE_LOADED` | a: string | `bool` |
| `CUTSCENE.SET_SCRIPT_CAN_START_CUTSCENE` | a: int | `void` |
| `CUTSCENE.CAN_REQUEST_ASSETS_FOR_CUTSCENE_ENTITY` | — | `bool` |
| `CUTSCENE.IS_CUTSCENE_PLAYBACK_FLAG_SET` | a: int | `bool` |
| `CUTSCENE.SET_CUTSCENE_ENTITY_STREAMING_FLAGS` | a1: string, a2: int, a3: int | `void` |
| `CUTSCENE.REQUEST_CUT_FILE` | a: string | `void` |
| `CUTSCENE.HAS_CUT_FILE_LOADED` | a: string | `bool` |
| `CUTSCENE.REMOVE_CUT_FILE` | a: string | `void` |
| `CUTSCENE.GET_CUT_FILE_OFFSET` | a: string, b: int | `vector3` |
| `CUTSCENE.GET_CUT_FILE_CONCAT_COUNT` | a: string | `int` |
| `CUTSCENE.START_CUTSCENE` | a: int | `void` |
| `CUTSCENE.START_CUTSCENE_AT_COORDS` | a1: float, a2: float, a3: float, a4: int | `void` |
| `CUTSCENE.STOP_CUTSCENE` | a: bool | `void` |
| `CUTSCENE.STOP_CUTSCENE_IMMEDIATELY` | — | `void` |
| `CUTSCENE.SET_CUTSCENE_ORIGIN` | a1: float, a2: float, a3: float, a4: float, a5: int | `void` |
| `CUTSCENE.SET_CUTSCENE_ORIGIN_AND_ORIENTATION` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `void` |
| `CUTSCENE.GET_CUTSCENE_TIME` | — | `int` |
| `CUTSCENE.GET_CUTSCENE_PLAY_TIME` | — | `int` |
| `CUTSCENE.GET_CUTSCENE_TOTAL_DURATION` | — | `int` |
| `CUTSCENE.GET_CUTSCENE_END_TIME` | — | `int` |
| `CUTSCENE.GET_CUTSCENE_PLAY_DURATION` | — | `int` |
| `CUTSCENE.WAS_CUTSCENE_SKIPPED` | — | `bool` |
| `CUTSCENE.HAS_CUTSCENE_FINISHED` | — | `bool` |
| `CUTSCENE.IS_CUTSCENE_ACTIVE` | — | `bool` |
| `CUTSCENE.IS_CUTSCENE_PLAYING` | — | `bool` |
| `CUTSCENE.GET_CUTSCENE_SECTION_PLAYING` | — | `int` |
| `CUTSCENE.GET_ENTITY_INDEX_OF_CUTSCENE_ENTITY` | a: string, b: hash | `int` |
| `CUTSCENE.GET_CUTSCENE_CONCAT_SECTION_PLAYING` | — | `int` |
| `CUTSCENE.IS_CUTSCENE_AUTHORIZED` | a: string | `bool` |
| `CUTSCENE.DOES_CUTSCENE_HANDLE_EXIST` | a: int | `int` |
| `CUTSCENE.REGISTER_ENTITY_FOR_CUTSCENE` | a1: int, a2: string, a3: int, a4: hash, a5: int | `void` |
| `CUTSCENE.GET_ENTITY_INDEX_OF_REGISTERED_ENTITY` | a: string, b: hash | `int` |
| `CUTSCENE.SET_VEHICLE_MODEL_PLAYER_WILL_EXIT_SCENE` | a: hash | `void` |
| `CUTSCENE.SET_CUTSCENE_TRIGGER_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `void` |
| `CUTSCENE.CAN_SET_ENTER_STATE_FOR_REGISTERED_ENTITY` | a: string, b: hash | `bool` |
| `CUTSCENE.CAN_SET_EXIT_STATE_FOR_REGISTERED_ENTITY` | a: string, b: hash | `bool` |
| `CUTSCENE.CAN_SET_EXIT_STATE_FOR_CAMERA` | a: bool | `bool` |
| `CUTSCENE.SET_PAD_CAN_SHAKE_DURING_CUTSCENE` | a: bool | `void` |
| `CUTSCENE.SET_CUTSCENE_FADE_VALUES` | a1: bool, a2: bool, a3: bool, a4: bool | `void` |
| `CUTSCENE.SET_CUTSCENE_MULTIHEAD_FADE` | a1: bool, a2: bool, a3: bool, a4: bool | `void` |
| `CUTSCENE.SET_CUTSCENE_MULTIHEAD_FADE_MANUAL` | a: bool | `void` |
| `CUTSCENE.IS_MULTIHEAD_FADE_UP` | — | `bool` |
| `CUTSCENE.NETWORK_SET_MOCAP_CUTSCENE_CAN_BE_SKIPPED` | a: bool | `void` |
| `CUTSCENE.SET_CAR_GENERATORS_CAN_UPDATE_DURING_CUTSCENE` | a: bool | `void` |
| `CUTSCENE.CAN_USE_MOBILE_PHONE_DURING_CUTSCENE` | — | `bool` |
| `CUTSCENE.SET_CUTSCENE_CAN_BE_SKIPPED` | a: bool | `void` |
| `CUTSCENE.SET_CAN_DISPLAY_MINIMAP_DURING_CUTSCENE_THIS_UPDATE` | — | `void` |
| `CUTSCENE.SET_CUTSCENE_PED_COMPONENT_VARIATION` | a1: string, a2: int, a3: int, a4: int, a5: hash | `void` |
| `CUTSCENE.SET_CUTSCENE_PED_COMPONENT_VARIATION_FROM_PED` | a1: string, a2: int, a3: hash | `void` |
| `CUTSCENE.DOES_CUTSCENE_ENTITY_EXIST` | a: string, b: hash | `bool` |
| `CUTSCENE.SET_CUTSCENE_PED_PROP_VARIATION` | a1: string, a2: int, a3: int, a4: int, a5: hash | `void` |
| `CUTSCENE.HAS_CUTSCENE_CUT_THIS_FRAME` | — | `bool` |

## DATAFILE

| Native | Params | Returns |
| --- | --- | --- |
| `DATAFILE.DATAFILE_WATCH_REQUEST_ID` | a: int | `void` |
| `DATAFILE.DATAFILE_CLEAR_WATCH_LIST` | — | `void` |
| `DATAFILE.DATAFILE_IS_VALID_REQUEST_ID` | a: int | `bool` |
| `DATAFILE.DATAFILE_HAS_LOADED_FILE_DATA` | a: int | `bool` |
| `DATAFILE.DATAFILE_HAS_VALID_FILE_DATA` | a: int | `bool` |
| `DATAFILE.DATAFILE_SELECT_ACTIVE_FILE` | a: int, b: int | `bool` |
| `DATAFILE.DATAFILE_DELETE_REQUESTED_FILE` | a: int | `bool` |
| `DATAFILE.UGC_CREATE_CONTENT` | a1: pointer, a2: int, a3: string, a4: string, a5: string, a6: string, a7: bool, a8: int | `bool` |
| `DATAFILE.UGC_CREATE_MISSION` | a1: string, a2: string, a3: string, a4: string, a5: bool, a6: int | `bool` |
| `DATAFILE.UGC_UPDATE_CONTENT` | a1: string, a2: pointer, a3: int, a4: string, a5: string, a6: string, a7: string, a8: int | `bool` |
| `DATAFILE.UGC_UPDATE_MISSION` | a1: string, a2: string, a3: string, a4: string, a5: string, a6: int | `bool` |
| `DATAFILE.UGC_SET_PLAYER_DATA` | a1: string, a2: float, a3: string, a4: int | `bool` |
| `DATAFILE.DATAFILE_SELECT_UGC_DATA` | a: int, b: int | `bool` |
| `DATAFILE.DATAFILE_SELECT_UGC_STATS` | a1: int, a2: bool, a3: int | `bool` |
| `DATAFILE.DATAFILE_SELECT_UGC_PLAYER_DATA` | a: int, b: int | `bool` |
| `DATAFILE.DATAFILE_SELECT_CREATOR_STATS` | a: int, b: int | `bool` |
| `DATAFILE.DATAFILE_LOAD_OFFLINE_UGC` | a: string, b: int | `bool` |
| `DATAFILE.DATAFILE_CREATE` | a: int | `void` |
| `DATAFILE.DATAFILE_DELETE` | a: int | `void` |
| `DATAFILE.DATAFILE_STORE_MISSION_HEADER` | a: int | `void` |
| `DATAFILE.DATAFILE_FLUSH_MISSION_HEADER` | — | `void` |
| `DATAFILE.DATAFILE_GET_FILE_DICT` | a: int | `pointer` |
| `DATAFILE.DATAFILE_START_SAVE_TO_CLOUD` | a: string, b: int | `bool` |
| `DATAFILE.DATAFILE_UPDATE_SAVE_TO_CLOUD` | a: pointer | `bool` |
| `DATAFILE.DATAFILE_IS_SAVE_PENDING` | — | `bool` |
| `DATAFILE.DATAFILE_LOAD_OFFLINE_UGC_FOR_ADDITIONAL_DATA_FILE` | a: int, b: int | `bool` |
| `DATAFILE.DATAFILE_DELETE_FOR_ADDITIONAL_DATA_FILE` | a: int | `void` |
| `DATAFILE.DATAFILE_GET_FILE_DICT_FOR_ADDITIONAL_DATA_FILE` | a: int | `pointer` |
| `DATAFILE.DATADICT_SET_BOOL` | a1: pointer, a2: string, a3: bool | `void` |
| `DATAFILE.DATADICT_SET_INT` | a1: pointer, a2: string, a3: int | `void` |
| `DATAFILE.DATADICT_SET_FLOAT` | a1: pointer, a2: string, a3: float | `void` |
| `DATAFILE.DATADICT_SET_STRING` | a1: pointer, a2: string, a3: string | `void` |
| `DATAFILE.DATADICT_SET_VECTOR` | a1: pointer, a2: string, a3: float, a4: float, a5: float | `void` |
| `DATAFILE.DATADICT_CREATE_DICT` | a: pointer, b: string | `pointer` |
| `DATAFILE.DATADICT_CREATE_ARRAY` | a: pointer, b: string | `pointer` |
| `DATAFILE.DATADICT_GET_BOOL` | a: pointer, b: string | `bool` |
| `DATAFILE.DATADICT_GET_INT` | a: pointer, b: string | `int` |
| `DATAFILE.DATADICT_GET_FLOAT` | a: pointer, b: string | `float` |
| `DATAFILE.DATADICT_GET_STRING` | a: pointer, b: string | `string` |
| `DATAFILE.DATADICT_GET_VECTOR` | a: pointer, b: string | `vector3` |
| `DATAFILE.DATADICT_GET_DICT` | a: pointer, b: string | `pointer` |
| `DATAFILE.DATADICT_GET_ARRAY` | a: pointer, b: string | `pointer` |
| `DATAFILE.DATADICT_GET_TYPE` | a: pointer, b: string | `int` |
| `DATAFILE.DATAARRAY_ADD_BOOL` | a: pointer, b: bool | `void` |
| `DATAFILE.DATAARRAY_ADD_INT` | a: pointer, b: int | `void` |
| `DATAFILE.DATAARRAY_ADD_FLOAT` | a: pointer, b: float | `void` |
| `DATAFILE.DATAARRAY_ADD_STRING` | a: pointer, b: string | `void` |
| `DATAFILE.DATAARRAY_ADD_VECTOR` | a1: pointer, a2: float, a3: float, a4: float | `void` |
| `DATAFILE.DATAARRAY_ADD_DICT` | a: pointer | `pointer` |
| `DATAFILE.DATAARRAY_GET_BOOL` | a: pointer, b: int | `bool` |
| `DATAFILE.DATAARRAY_GET_INT` | a: pointer, b: int | `int` |
| `DATAFILE.DATAARRAY_GET_FLOAT` | a: pointer, b: int | `float` |
| `DATAFILE.DATAARRAY_GET_STRING` | a: pointer, b: int | `string` |
| `DATAFILE.DATAARRAY_GET_VECTOR` | a: pointer, b: int | `vector3` |
| `DATAFILE.DATAARRAY_GET_DICT` | a: pointer, b: int | `pointer` |
| `DATAFILE.DATAARRAY_GET_COUNT` | a: pointer | `int` |
| `DATAFILE.DATAARRAY_GET_TYPE` | a: pointer, b: int | `int` |

## DECORATOR

| Native | Params | Returns |
| --- | --- | --- |
| `DECORATOR.DECOR_SET_TIME` | a1: int, a2: string, a3: int | `bool` |
| `DECORATOR.DECOR_SET_BOOL` | a1: int, a2: string, a3: bool | `bool` |
| `DECORATOR.DECOR_SET_FLOAT` | a1: int, a2: string, a3: float | `bool` |
| `DECORATOR.DECOR_SET_INT` | a1: int, a2: string, a3: int | `bool` |
| `DECORATOR.DECOR_GET_BOOL` | a: int, b: string | `bool` |
| `DECORATOR.DECOR_GET_FLOAT` | a: int, b: string | `float` |
| `DECORATOR.DECOR_GET_INT` | a: int, b: string | `int` |
| `DECORATOR.DECOR_EXIST_ON` | a: int, b: string | `bool` |
| `DECORATOR.DECOR_REMOVE` | a: int, b: string | `bool` |
| `DECORATOR.DECOR_REGISTER` | a: string, b: int | `void` |
| `DECORATOR.DECOR_IS_REGISTERED_AS_TYPE` | a: string, b: int | `bool` |
| `DECORATOR.DECOR_REGISTER_LOCK` | — | `void` |

## DLC

| Native | Params | Returns |
| --- | --- | --- |
| `DLC.ARE_ANY_CCS_PENDING` | — | `bool` |
| `DLC.IS_DLC_PRESENT` | a: hash | `bool` |
| `DLC.DLC_CHECK_CLOUD_DATA_CORRECT` | — | `bool` |
| `DLC.GET_EXTRACONTENT_CLOUD_RESULT` | — | `int` |
| `DLC.DLC_CHECK_COMPAT_PACK_CONFIGURATION` | — | `bool` |
| `DLC.GET_EVER_HAD_BAD_PACK_ORDER` | — | `bool` |
| `DLC.GET_IS_LOADING_SCREEN_ACTIVE` | — | `bool` |
| `DLC.GET_IS_INITIAL_LOADING_SCREEN_ACTIVE` | — | `bool` |
| `DLC.HAS_CLOUD_REQUESTS_FINISHED` | a: pointer, b: int | `bool` |
| `DLC.ON_ENTER_SP` | — | `void` |
| `DLC.ON_ENTER_MP` | — | `void` |
| `DLC.ARE_USER_ENTITLEMENTS_UP_TO_DATE` | — | `bool` |
| `DLC.TRY_GET_USER_ENTITLEMENTS` | a: pointer | `bool` |
| `DLC.DECLARE_IN_MULTIPLAYER_THIS_FRAME` | — | `void` |

## ENTITY

| Native | Params | Returns |
| --- | --- | --- |
| `ENTITY.DOES_ENTITY_EXIST` | a: int | `bool` |
| `ENTITY.DOES_ENTITY_BELONG_TO_THIS_SCRIPT` | a: int, b: bool | `bool` |
| `ENTITY.DOES_ENTITY_HAVE_DRAWABLE` | a: int | `bool` |
| `ENTITY.DOES_ENTITY_HAVE_PHYSICS` | a: int | `bool` |
| `ENTITY.DOES_ENTITY_HAVE_SKELETON` | a: int | `bool` |
| `ENTITY.DOES_ENTITY_HAVE_ANIM_DIRECTOR` | a: int | `bool` |
| `ENTITY.HAS_ENTITY_ANIM_FINISHED` | a1: int, a2: string, a3: string, a4: int | `bool` |
| `ENTITY.HAS_ENTITY_BEEN_DAMAGED_BY_ANY_OBJECT` | a: int | `bool` |
| `ENTITY.HAS_ENTITY_BEEN_DAMAGED_BY_ANY_PED` | a: int | `bool` |
| `ENTITY.HAS_ENTITY_BEEN_DAMAGED_BY_ANY_VEHICLE` | a: int | `bool` |
| `ENTITY.HAS_ENTITY_BEEN_DAMAGED_BY_ENTITY` | a1: int, a2: int, a3: bool | `bool` |
| `ENTITY.HAS_ENTITY_CLEAR_LOS_TO_ENTITY` | a1: int, a2: int, a3: int | `bool` |
| `ENTITY.HAS_ENTITY_CLEAR_LOS_TO_ENTITY_ADJUST_FOR_COVER` | a1: int, a2: int, a3: int | `bool` |
| `ENTITY.HAS_ENTITY_CLEAR_LOS_TO_ENTITY_IN_FRONT` | a: int, b: int | `bool` |
| `ENTITY.HAS_ENTITY_COLLIDED_WITH_ANYTHING` | a: int | `bool` |
| `ENTITY._GET_LAST_ENTITY_HIT_BY_ENTITY` | a: int | `int` |
| `ENTITY.GET_LAST_MATERIAL_HIT_BY_ENTITY` | a: int | `hash` |
| `ENTITY.GET_COLLISION_NORMAL_OF_LAST_HIT_FOR_ENTITY` | a: int | `vector3` |
| `ENTITY.FORCE_ENTITY_AI_AND_ANIMATION_UPDATE` | a: int | `void` |
| `ENTITY.GET_ENTITY_ANIM_CURRENT_TIME` | a1: int, a2: string, a3: string | `float` |
| `ENTITY.GET_ENTITY_ANIM_TOTAL_TIME` | a1: int, a2: string, a3: string | `float` |
| `ENTITY.GET_ANIM_DURATION` | a: string, b: string | `float` |
| `ENTITY.GET_ENTITY_ATTACHED_TO` | a: int | `int` |
| `ENTITY.GET_ENTITY_COORDS` | a: int, b: bool | `vector3` |
| `ENTITY.GET_ENTITY_FORWARD_VECTOR` | a: int | `vector3` |
| `ENTITY.GET_ENTITY_FORWARD_X` | a: int | `float` |
| `ENTITY.GET_ENTITY_FORWARD_Y` | a: int | `float` |
| `ENTITY.GET_ENTITY_HEADING` | a: int | `float` |
| `ENTITY.GET_ENTITY_HEADING_FROM_EULERS` | a: int | `float` |
| `ENTITY.GET_ENTITY_HEALTH` | a: int | `int` |
| `ENTITY.GET_ENTITY_MAX_HEALTH` | a: int | `int` |
| `ENTITY.SET_ENTITY_MAX_HEALTH` | a: int, b: int | `void` |
| `ENTITY.GET_ENTITY_HEIGHT` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: bool | `float` |
| `ENTITY.GET_ENTITY_HEIGHT_ABOVE_GROUND` | a: int | `float` |
| `ENTITY.GET_ENTITY_MATRIX` | a1: int, a2: vector3, a3: vector3, a4: vector3, a5: vector3 | `void` |
| `ENTITY.GET_ENTITY_MODEL` | a: int | `hash` |
| `ENTITY.GET_OFFSET_FROM_ENTITY_GIVEN_WORLD_COORDS` | a1: int, a2: float, a3: float, a4: float | `vector3` |
| `ENTITY.GET_OFFSET_FROM_ENTITY_IN_WORLD_COORDS` | a1: int, a2: float, a3: float, a4: float | `vector3` |
| `ENTITY.GET_ENTITY_PITCH` | a: int | `float` |
| `ENTITY.GET_ENTITY_QUATERNION` | a1: int, a2: pointer, a3: pointer, a4: pointer, a5: pointer | `void` |
| `ENTITY.GET_ENTITY_ROLL` | a: int | `float` |
| `ENTITY.GET_ENTITY_ROTATION` | a: int, b: int | `vector3` |
| `ENTITY.GET_ENTITY_ROTATION_VELOCITY` | a: int | `vector3` |
| `ENTITY.GET_ENTITY_SCRIPT` | a: int, b: pointer | `string` |
| `ENTITY.GET_ENTITY_SPEED` | a: int | `float` |
| `ENTITY.GET_ENTITY_SPEED_VECTOR` | a: int, b: bool | `vector3` |
| `ENTITY.GET_ENTITY_UPRIGHT_VALUE` | a: int | `float` |
| `ENTITY.GET_ENTITY_VELOCITY` | a: int | `vector3` |
| `ENTITY.GET_OBJECT_INDEX_FROM_ENTITY_INDEX` | a: int | `int` |
| `ENTITY.GET_PED_INDEX_FROM_ENTITY_INDEX` | a: int | `int` |
| `ENTITY.GET_VEHICLE_INDEX_FROM_ENTITY_INDEX` | a: int | `int` |
| `ENTITY.GET_WORLD_POSITION_OF_ENTITY_BONE` | a: int, b: int | `vector3` |
| `ENTITY.GET_NEAREST_PLAYER_TO_ENTITY` | a: int | `int` |
| `ENTITY.GET_NEAREST_PLAYER_TO_ENTITY_ON_TEAM` | a: int, b: int | `int` |
| `ENTITY.GET_NEAREST_PARTICIPANT_TO_ENTITY` | a: int | `int` |
| `ENTITY.GET_ENTITY_TYPE` | a: int | `int` |
| `ENTITY.GET_ENTITY_POPULATION_TYPE` | a: int | `int` |
| `ENTITY.IS_AN_ENTITY` | a: int | `bool` |
| `ENTITY.IS_ENTITY_A_PED` | a: int | `bool` |
| `ENTITY.IS_ENTITY_A_MISSION_ENTITY` | a: int | `bool` |
| `ENTITY.IS_ENTITY_A_VEHICLE` | a: int | `bool` |
| `ENTITY.IS_ENTITY_AN_OBJECT` | a: int | `bool` |
| `ENTITY.IS_ENTITY_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool, a9: bool, a10: int | `bool` |
| `ENTITY.IS_ENTITY_AT_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool, a7: bool, a8: int | `bool` |
| `ENTITY.IS_ENTITY_ATTACHED` | a: int | `bool` |
| `ENTITY.IS_ENTITY_ATTACHED_TO_ANY_OBJECT` | a: int | `bool` |
| `ENTITY.IS_ENTITY_ATTACHED_TO_ANY_PED` | a: int | `bool` |
| `ENTITY.IS_ENTITY_ATTACHED_TO_ANY_VEHICLE` | a: int | `bool` |
| `ENTITY.IS_ENTITY_ATTACHED_TO_ENTITY` | a: int, b: int | `bool` |
| `ENTITY.IS_ENTITY_DEAD` | a: int, b: bool | `bool` |
| `ENTITY.IS_ENTITY_IN_AIR` | a: int | `bool` |
| `ENTITY.IS_ENTITY_IN_ANGLED_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: bool, a11: int | `bool` |
| `ENTITY.IS_ENTITY_IN_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool, a9: bool, a10: int | `bool` |
| `ENTITY.IS_ENTITY_IN_ZONE` | a: int, b: string | `bool` |
| `ENTITY.IS_ENTITY_IN_WATER` | a: int | `bool` |
| `ENTITY.GET_ENTITY_SUBMERGED_LEVEL` | a: int | `float` |
| `ENTITY.SET_ENTITY_REQUIRES_MORE_EXPENSIVE_RIVER_CHECK` | a: int, b: bool | `void` |
| `ENTITY.IS_ENTITY_ON_SCREEN` | a: int | `bool` |
| `ENTITY.IS_ENTITY_PLAYING_ANIM` | a1: int, a2: string, a3: string, a4: int | `bool` |
| `ENTITY.IS_ENTITY_STATIC` | a: int | `bool` |
| `ENTITY.IS_ENTITY_TOUCHING_ENTITY` | a: int, b: int | `bool` |
| `ENTITY.IS_ENTITY_TOUCHING_MODEL` | a: int, b: hash | `bool` |
| `ENTITY.IS_ENTITY_UPRIGHT` | a: int, b: float | `bool` |
| `ENTITY.IS_ENTITY_UPSIDEDOWN` | a: int | `bool` |
| `ENTITY.IS_ENTITY_VISIBLE` | a: int | `bool` |
| `ENTITY.IS_ENTITY_VISIBLE_TO_SCRIPT` | a: int | `bool` |
| `ENTITY.IS_ENTITY_OCCLUDED` | a: int | `bool` |
| `ENTITY.WOULD_ENTITY_BE_OCCLUDED` | a1: hash, a2: float, a3: float, a4: float, a5: bool | `bool` |
| `ENTITY.IS_ENTITY_WAITING_FOR_WORLD_COLLISION` | a: int | `bool` |
| `ENTITY.APPLY_FORCE_TO_ENTITY_CENTER_OF_MASS` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool, a7: bool, a8: bool, a9: bool | `void` |
| `ENTITY.APPLY_FORCE_TO_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: bool, a11: bool, a12: bool, a13: bool, a14: bool | `void` |
| `ENTITY.ATTACH_ENTITY_TO_ENTITY` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool, a11: bool, a12: bool, a13: bool, a14: int, a15: bool, a16: int | `void` |
| `ENTITY.ATTACH_ENTITY_BONE_TO_ENTITY_BONE` | a1: int, a2: int, a3: int, a4: int, a5: bool, a6: bool | `void` |
| `ENTITY.ATTACH_ENTITY_BONE_TO_ENTITY_BONE_Y_FORWARD` | a1: int, a2: int, a3: int, a4: int, a5: bool, a6: bool | `void` |
| `ENTITY.ATTACH_ENTITY_TO_ENTITY_PHYSICALLY` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: float, a15: bool, a16: bool, a17: bool, a18: bool, a19: int | `void` |
| `ENTITY.ATTACH_ENTITY_TO_ENTITY_PHYSICALLY_OVERRIDE_INVERSE_MASS` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: float, a15: bool, a16: bool, a17: bool, a18: bool, a19: int, a20: float, a21: float | `void` |
| `ENTITY.PROCESS_ENTITY_ATTACHMENTS` | a: int | `void` |
| `ENTITY.GET_ENTITY_BONE_INDEX_BY_NAME` | a: int, b: string | `int` |
| `ENTITY.CLEAR_ENTITY_LAST_DAMAGE_ENTITY` | a: int | `void` |
| `ENTITY.DELETE_ENTITY` | a: pointer | `void` |
| `ENTITY.DETACH_ENTITY` | a1: int, a2: bool, a3: bool | `void` |
| `ENTITY.FREEZE_ENTITY_POSITION` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_SHOULD_FREEZE_WAITING_ON_COLLISION` | a: int, b: bool | `void` |
| `ENTITY.PLAY_ENTITY_ANIM` | a1: int, a2: string, a3: string, a4: float, a5: bool, a6: bool, a7: bool, a8: float, a9: int | `bool` |
| `ENTITY.PLAY_SYNCHRONIZED_ENTITY_ANIM` | a1: int, a2: int, a3: string, a4: string, a5: float, a6: float, a7: int, a8: float | `bool` |
| `ENTITY.PLAY_SYNCHRONIZED_MAP_ENTITY_ANIM` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: float, a7: string, a8: string, a9: float, a10: float, a11: int, a12: float | `bool` |
| `ENTITY.STOP_SYNCHRONIZED_MAP_ENTITY_ANIM` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: float | `bool` |
| `ENTITY.STOP_ENTITY_ANIM` | a1: int, a2: string, a3: string, a4: float | `bool` |
| `ENTITY.STOP_SYNCHRONIZED_ENTITY_ANIM` | a1: int, a2: float, a3: bool | `bool` |
| `ENTITY.HAS_ANIM_EVENT_FIRED` | a: int, b: hash | `bool` |
| `ENTITY.FIND_ANIM_EVENT_PHASE` | a1: string, a2: string, a3: string, a4: pointer, a5: pointer | `bool` |
| `ENTITY.SET_ENTITY_ANIM_CURRENT_TIME` | a1: int, a2: string, a3: string, a4: float | `void` |
| `ENTITY.SET_ENTITY_ANIM_SPEED` | a1: int, a2: string, a3: string, a4: float | `void` |
| `ENTITY.SET_ENTITY_AS_MISSION_ENTITY` | a1: int, a2: bool, a3: bool | `void` |
| `ENTITY.SET_ENTITY_AS_NO_LONGER_NEEDED` | a: pointer | `void` |
| `ENTITY.SET_PED_AS_NO_LONGER_NEEDED` | a: pointer | `void` |
| `ENTITY.SET_VEHICLE_AS_NO_LONGER_NEEDED` | a: pointer | `void` |
| `ENTITY.SET_OBJECT_AS_NO_LONGER_NEEDED` | a: pointer | `void` |
| `ENTITY.SET_ENTITY_CAN_BE_DAMAGED` | a: int, b: bool | `void` |
| `ENTITY.GET_ENTITY_CAN_BE_DAMAGED` | a: int | `bool` |
| `ENTITY.SET_ENTITY_CAN_BE_DAMAGED_BY_RELATIONSHIP_GROUP` | a1: int, a2: bool, a3: int | `void` |
| `ENTITY.SET_ENTITY_CAN_ONLY_BE_DAMAGED_BY_SCRIPT_PARTICIPANTS` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_CAN_BE_TARGETED_WITHOUT_LOS` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_COLLISION` | a1: int, a2: bool, a3: bool | `void` |
| `ENTITY.GET_ENTITY_COLLISION_DISABLED` | a: int | `bool` |
| `ENTITY.SET_ENTITY_COMPLETELY_DISABLE_COLLISION` | a1: int, a2: bool, a3: bool | `void` |
| `ENTITY.SET_ENTITY_COORDS` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: bool | `void` |
| `ENTITY.SET_ENTITY_COORDS_WITHOUT_PLANTS_RESET` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: bool | `void` |
| `ENTITY.SET_ENTITY_COORDS_NO_OFFSET` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool | `void` |
| `ENTITY.SET_ENTITY_DYNAMIC` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_HEADING` | a: int, b: float | `void` |
| `ENTITY.SET_ENTITY_HEALTH` | a1: int, a2: int, a3: int, a4: hash | `void` |
| `ENTITY.SET_ENTITY_INVINCIBLE` | a1: int, a2: bool, a3: bool | `void` |
| `ENTITY.SET_ENTITY_IS_TARGET_PRIORITY` | a1: int, a2: bool, a3: float | `void` |
| `ENTITY.SET_ENTITY_LIGHTS` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_LOAD_COLLISION_FLAG` | a1: int, a2: bool, a3: int | `void` |
| `ENTITY.HAS_COLLISION_LOADED_AROUND_ENTITY` | a: int | `bool` |
| `ENTITY.SET_ENTITY_MAX_SPEED` | a: int, b: float | `void` |
| `ENTITY.SET_ENTITY_ONLY_DAMAGED_BY_PLAYER` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_ONLY_DAMAGED_BY_RELATIONSHIP_GROUP` | a1: int, a2: bool, a3: int | `void` |
| `ENTITY.SET_ENTITY_PROOFS` | a1: int, a2: bool, a3: bool, a4: bool, a5: bool, a6: bool, a7: bool, a8: bool, a9: bool | `void` |
| `ENTITY.GET_ENTITY_PROOFS` | a1: int, a2: pointer, a3: pointer, a4: pointer, a5: pointer, a6: pointer, a7: pointer, a8: pointer, a9: pointer | `bool` |
| `ENTITY.SET_ENTITY_QUATERNION` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `ENTITY.SET_ENTITY_RECORDS_COLLISIONS` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_ROTATION` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: bool | `void` |
| `ENTITY.SET_ENTITY_VISIBLE` | a1: int, a2: bool, a3: bool | `void` |
| `ENTITY.SET_ENTITY_WATER_REFLECTION_FLAG` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_MIRROR_REFLECTION_FLAG` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_VELOCITY` | a1: int, a2: float, a3: float, a4: float | `void` |
| `ENTITY.SET_ENTITY_ANGULAR_VELOCITY` | a1: int, a2: float, a3: float, a4: float | `void` |
| `ENTITY.SET_ENTITY_HAS_GRAVITY` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_LOD_DIST` | a: int, b: int | `void` |
| `ENTITY.GET_ENTITY_LOD_DIST` | a: int | `int` |
| `ENTITY.SET_ENTITY_ALPHA` | a1: int, a2: int, a3: bool | `void` |
| `ENTITY.GET_ENTITY_ALPHA` | a: int | `int` |
| `ENTITY.RESET_ENTITY_ALPHA` | a: int | `void` |
| `ENTITY.RESET_PICKUP_ENTITY_GLOW` | a: int | `void` |
| `ENTITY.SET_PICKUP_COLLIDES_WITH_PROJECTILES` | a: int, b: int | `void` |
| `ENTITY.SET_ENTITY_SORT_BIAS` | a: int, b: float | `void` |
| `ENTITY.SET_ENTITY_ALWAYS_PRERENDER` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_RENDER_SCORCHED` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_TRAFFICLIGHT_OVERRIDE` | a: int, b: int | `void` |
| `ENTITY.SET_ENTITY_IS_IN_VEHICLE` | a: int | `void` |
| `ENTITY.CREATE_MODEL_SWAP` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: hash, a7: bool | `void` |
| `ENTITY.REMOVE_MODEL_SWAP` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: hash, a7: bool | `void` |
| `ENTITY.CREATE_MODEL_HIDE` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: bool | `void` |
| `ENTITY.CREATE_MODEL_HIDE_EXCLUDING_SCRIPT_OBJECTS` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: bool | `void` |
| `ENTITY.REMOVE_MODEL_HIDE` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: bool | `void` |
| `ENTITY.CREATE_FORCED_OBJECT` | a1: float, a2: float, a3: float, a4: int, a5: hash, a6: bool | `void` |
| `ENTITY.REMOVE_FORCED_OBJECT` | a1: float, a2: float, a3: float, a4: float, a5: hash | `void` |
| `ENTITY.SET_ENTITY_NO_COLLISION_ENTITY` | a1: int, a2: int, a3: bool | `void` |
| `ENTITY._SET_ENTITY_NO_COLLISION_WITH_NETWORKED_ENTITY` | a: int, b: int | `void` |
| `ENTITY.SET_ENTITY_MOTION_BLUR` | a: int, b: bool | `void` |
| `ENTITY.SET_CAN_AUTO_VAULT_ON_ENTITY` | a: int, b: bool | `void` |
| `ENTITY.SET_CAN_CLIMB_ON_ENTITY` | a: int, b: bool | `void` |
| `ENTITY.SET_WAIT_FOR_COLLISIONS_BEFORE_PROBE` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_NOWEAPONDECALS` | a: int, b: bool | `void` |
| `ENTITY.SET_ENTITY_USE_MAX_DISTANCE_FOR_WATER_REFLECTION` | a: int, b: bool | `void` |
| `ENTITY.GET_ENTITY_BONE_ROTATION` | a: int, b: int | `vector3` |
| `ENTITY.GET_ENTITY_BONE_POSTION` | a: int, b: int | `vector3` |
| `ENTITY.GET_ENTITY_BONE_OBJECT_ROTATION` | a: int, b: int | `vector3` |
| `ENTITY.GET_ENTITY_BONE_OBJECT_POSTION` | a: int, b: int | `vector3` |
| `ENTITY.GET_ENTITY_BONE_COUNT` | a: int | `int` |
| `ENTITY.ENABLE_ENTITY_BULLET_COLLISION` | a: int | `void` |
| `ENTITY.SET_ENTITY_CAN_ONLY_BE_DAMAGED_BY_ENTITY` | a: int, b: int | `void` |
| `ENTITY.SET_ENTITY_CANT_CAUSE_COLLISION_DAMAGED_ENTITY` | a: int, b: int | `void` |
| `ENTITY.SET_ALLOW_MIGRATE_TO_SPECTATOR` | a: int, b: int | `void` |
| `ENTITY.GET_ENTITY_OF_TYPE_ATTACHED_TO_ENTITY` | a: int, b: hash | `int` |
| `ENTITY.SET_PICK_UP_BY_CARGOBOB_DISABLED` | a: int, b: bool | `void` |

## EVENT

| Native | Params | Returns |
| --- | --- | --- |
| `EVENT.SET_DECISION_MAKER` | a: int, b: hash | `void` |
| `EVENT.CLEAR_DECISION_MAKER_EVENT_RESPONSE` | a: hash, b: int | `void` |
| `EVENT.BLOCK_DECISION_MAKER_EVENT` | a: hash, b: int | `void` |
| `EVENT.UNBLOCK_DECISION_MAKER_EVENT` | a: hash, b: int | `void` |
| `EVENT.ADD_SHOCKING_EVENT_AT_POSITION` | a1: int, a2: float, a3: float, a4: float, a5: float | `int` |
| `EVENT.ADD_SHOCKING_EVENT_FOR_ENTITY` | a1: int, a2: int, a3: float | `int` |
| `EVENT.IS_SHOCKING_EVENT_IN_SPHERE` | a1: int, a2: float, a3: float, a4: float, a5: float | `bool` |
| `EVENT.REMOVE_SHOCKING_EVENT` | a: int | `bool` |
| `EVENT.REMOVE_ALL_SHOCKING_EVENTS` | a: bool | `void` |
| `EVENT.REMOVE_SHOCKING_EVENT_SPAWN_BLOCKING_AREAS` | — | `void` |
| `EVENT.SUPPRESS_SHOCKING_EVENTS_NEXT_FRAME` | — | `void` |
| `EVENT.SUPPRESS_SHOCKING_EVENT_TYPE_NEXT_FRAME` | a: int | `void` |
| `EVENT.SUPPRESS_AGITATION_EVENTS_NEXT_FRAME` | — | `void` |

## EXTRAMETADATA

| Native | Params | Returns |
| --- | --- | --- |
| `EXTRAMETADATA.GET_NUM_TATTOO_SHOP_DLC_ITEMS` | a: int | `int` |
| `EXTRAMETADATA.GET_TATTOO_SHOP_DLC_ITEM_DATA` | a1: int, a2: int, a3: pointer | `bool` |
| `EXTRAMETADATA.GET_TATTOO_SHOP_DLC_ITEM_INDEX` | a1: hash, a2: int, a3: int | `int` |
| `EXTRAMETADATA.INIT_SHOP_PED_COMPONENT` | a: pointer | `void` |
| `EXTRAMETADATA.INIT_SHOP_PED_PROP` | a: pointer | `void` |
| `EXTRAMETADATA.SETUP_SHOP_PED_APPAREL_QUERY` | a1: int, a2: int, a3: int, a4: int | `int` |
| `EXTRAMETADATA.SETUP_SHOP_PED_APPAREL_QUERY_TU` | a1: int, a2: int, a3: int, a4: bool, a5: int, a6: int | `int` |
| `EXTRAMETADATA.GET_SHOP_PED_QUERY_COMPONENT` | a: int, b: pointer | `void` |
| `EXTRAMETADATA.GET_SHOP_PED_QUERY_COMPONENT_INDEX` | a: hash | `int` |
| `EXTRAMETADATA.GET_SHOP_PED_COMPONENT` | a: hash, b: pointer | `void` |
| `EXTRAMETADATA.GET_SHOP_PED_QUERY_PROP` | a: int, b: pointer | `void` |
| `EXTRAMETADATA.GET_SHOP_PED_QUERY_PROP_INDEX` | a: hash | `int` |
| `EXTRAMETADATA.GET_SHOP_PED_PROP` | a: hash, b: pointer | `void` |
| `EXTRAMETADATA.GET_HASH_NAME_FOR_COMPONENT` | a1: int, a2: int, a3: int, a4: int | `hash` |
| `EXTRAMETADATA.GET_HASH_NAME_FOR_PROP` | a1: int, a2: int, a3: int, a4: int | `hash` |
| `EXTRAMETADATA.GET_SHOP_PED_APPAREL_VARIANT_COMPONENT_COUNT` | a: hash | `int` |
| `EXTRAMETADATA.GET_SHOP_PED_APPAREL_VARIANT_PROP_COUNT` | a: hash | `int` |
| `EXTRAMETADATA.GET_VARIANT_COMPONENT` | a1: hash, a2: int, a3: pointer, a4: pointer, a5: pointer | `void` |
| `EXTRAMETADATA.GET_VARIANT_PROP` | a1: hash, a2: int, a3: pointer, a4: pointer, a5: pointer | `void` |
| `EXTRAMETADATA.GET_SHOP_PED_APPAREL_FORCED_COMPONENT_COUNT` | a: hash | `int` |
| `EXTRAMETADATA.GET_SHOP_PED_APPAREL_FORCED_PROP_COUNT` | a: hash | `int` |
| `EXTRAMETADATA.GET_FORCED_COMPONENT` | a1: hash, a2: int, a3: pointer, a4: pointer, a5: pointer | `void` |
| `EXTRAMETADATA.GET_FORCED_PROP` | a1: hash, a2: int, a3: pointer, a4: pointer, a5: pointer | `void` |
| `EXTRAMETADATA.DOES_SHOP_PED_APPAREL_HAVE_RESTRICTION_TAG` | a1: hash, a2: hash, a3: int | `bool` |
| `EXTRAMETADATA.DOES_CURRENT_PED_COMPONENT_HAVE_RESTRICTION_TAG` | a1: int, a2: int, a3: hash | `bool` |
| `EXTRAMETADATA.DOES_CURRENT_PED_PROP_HAVE_RESTRICTION_TAG` | a1: int, a2: int, a3: hash | `bool` |
| `EXTRAMETADATA.SETUP_SHOP_PED_OUTFIT_QUERY` | a: int, b: bool | `int` |
| `EXTRAMETADATA.GET_SHOP_PED_QUERY_OUTFIT` | a: int, b: pointer | `void` |
| `EXTRAMETADATA.GET_SHOP_PED_OUTFIT` | a: int, b: pointer | `void` |
| `EXTRAMETADATA.GET_SHOP_PED_OUTFIT_LOCATE` | a: int | `int` |
| `EXTRAMETADATA.GET_SHOP_PED_OUTFIT_PROP_VARIANT` | a1: hash, a2: int, a3: pointer | `bool` |
| `EXTRAMETADATA.GET_SHOP_PED_OUTFIT_COMPONENT_VARIANT` | a1: hash, a2: int, a3: pointer | `bool` |
| `EXTRAMETADATA.GET_NUM_DLC_VEHICLES` | — | `int` |
| `EXTRAMETADATA.GET_DLC_VEHICLE_MODEL` | a: int | `hash` |
| `EXTRAMETADATA.GET_DLC_VEHICLE_DATA` | a: int, b: pointer | `bool` |
| `EXTRAMETADATA.GET_DLC_VEHICLE_FLAGS` | a: int | `int` |
| `EXTRAMETADATA.GET_NUM_DLC_WEAPONS` | — | `int` |
| `EXTRAMETADATA.GET_NUM_DLC_WEAPONS_SP` | — | `int` |
| `EXTRAMETADATA.GET_DLC_WEAPON_DATA` | a: int, b: pointer | `bool` |
| `EXTRAMETADATA.GET_DLC_WEAPON_DATA_SP` | a: int, b: pointer | `bool` |
| `EXTRAMETADATA.GET_NUM_DLC_WEAPON_COMPONENTS` | a: int | `int` |
| `EXTRAMETADATA.GET_NUM_DLC_WEAPON_COMPONENTS_SP` | a: int | `int` |
| `EXTRAMETADATA.GET_DLC_WEAPON_COMPONENT_DATA` | a1: int, a2: int, a3: pointer | `bool` |
| `EXTRAMETADATA.GET_DLC_WEAPON_COMPONENT_DATA_SP` | a1: int, a2: int, a3: pointer | `bool` |
| `EXTRAMETADATA.IS_CONTENT_ITEM_LOCKED` | a: hash | `bool` |
| `EXTRAMETADATA.IS_DLC_VEHICLE_MOD` | a: hash | `bool` |
| `EXTRAMETADATA.GET_DLC_VEHICLE_MOD_LOCK_HASH` | a: hash | `hash` |
| `EXTRAMETADATA.EXECUTE_CONTENT_CHANGESET_GROUP_FOR_ALL` | a: hash | `void` |
| `EXTRAMETADATA.REVERT_CONTENT_CHANGESET_GROUP_FOR_ALL` | a: hash | `void` |

## FIRE

| Native | Params | Returns |
| --- | --- | --- |
| `FIRE.START_SCRIPT_FIRE` | a1: float, a2: float, a3: float, a4: int, a5: bool | `int` |
| `FIRE.REMOVE_SCRIPT_FIRE` | a: int | `void` |
| `FIRE.START_ENTITY_FIRE` | a: int | `int` |
| `FIRE.STOP_ENTITY_FIRE` | a: int | `void` |
| `FIRE.IS_ENTITY_ON_FIRE` | a: int | `bool` |
| `FIRE.GET_NUMBER_OF_FIRES_IN_RANGE` | a1: float, a2: float, a3: float, a4: float | `int` |
| `FIRE.SET_FLAMMABILITY_MULTIPLIER` | a: float | `void` |
| `FIRE.STOP_FIRE_IN_RANGE` | a1: float, a2: float, a3: float, a4: float | `void` |
| `FIRE.GET_CLOSEST_FIRE_POS` | a1: vector3, a2: float, a3: float, a4: float | `bool` |
| `FIRE.ADD_EXPLOSION` | a1: float, a2: float, a3: float, a4: int, a5: float, a6: bool, a7: bool, a8: float, a9: bool | `void` |
| `FIRE.ADD_OWNED_EXPLOSION` | — | blocked: ADD_OWNED_EXPLOSION is no longer safe to use |
| `FIRE.ADD_EXPLOSION_WITH_USER_VFX` | a1: float, a2: float, a3: float, a4: int, a5: hash, a6: float, a7: bool, a8: bool, a9: float | `void` |
| `FIRE.IS_EXPLOSION_IN_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `bool` |
| `FIRE.IS_EXPLOSION_ACTIVE_IN_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `bool` |
| `FIRE.IS_EXPLOSION_IN_SPHERE` | a1: int, a2: float, a3: float, a4: float, a5: float | `bool` |
| `FIRE.GET_OWNER_OF_EXPLOSION_IN_SPHERE` | a1: int, a2: float, a3: float, a4: float, a5: float | `int` |
| `FIRE.IS_EXPLOSION_IN_ANGLED_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float | `bool` |
| `FIRE.GET_OWNER_OF_EXPLOSION_IN_ANGLED_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float | `int` |
| `FIRE._NETWORK_EXPECT_EXPLOSION_EVENTS_FOR_PLAYER` | a: bool, b: int | `void` |
| `FIRE._GET_MAXIMUM_NUMBER_OF_WATER_CANNONS` | — | `int` |
| `FIRE._GET_WATER_CANNON_COORDS` | a: int | `vector3` |

## GRAPHICS

| Native | Params | Returns |
| --- | --- | --- |
| `GRAPHICS.SET_DEBUG_LINES_AND_SPHERES_DRAWING_ACTIVE` | a: bool | `void` |
| `GRAPHICS.DRAW_DEBUG_LINE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: int | `void` |
| `GRAPHICS.DRAW_DEBUG_LINE_WITH_TWO_COLOURS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: int, a11: int, a12: int, a13: int, a14: int | `void` |
| `GRAPHICS.DRAW_DEBUG_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int, a8: int | `void` |
| `GRAPHICS.DRAW_DEBUG_BOX` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: int | `void` |
| `GRAPHICS.DRAW_DEBUG_CROSS` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int, a8: int | `void` |
| `GRAPHICS.DRAW_DEBUG_TEXT` | a1: string, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int, a8: int | `void` |
| `GRAPHICS.DRAW_DEBUG_TEXT_2D` | a1: string, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int, a8: int | `void` |
| `GRAPHICS.DRAW_LINE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: int | `void` |
| `GRAPHICS.DRAW_POLY` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: int, a11: int, a12: int, a13: int | `void` |
| `GRAPHICS.DRAW_TEXTURED_POLY` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: int, a11: int, a12: int, a13: int, a14: string, a15: string, a16: float, a17: float, a18: float, a19: float, a20: float, a21: float, a22: float, a23: float, a24: float | `void` |
| `GRAPHICS.DRAW_TEXTURED_POLY_WITH_THREE_COLOURS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: int, a14: float, a15: float, a16: float, a17: int, a18: float, a19: float, a20: float, a21: int, a22: string, a23: string, a24: float, a25: float, a26: float, a27: float, a28: float, a29: float, a30: float, a31: float, a32: float | `void` |
| `GRAPHICS.DRAW_BOX` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: int | `void` |
| `GRAPHICS.SET_BACKFACECULLING` | a: bool | `void` |
| `GRAPHICS.SET_DEPTHWRITING` | a: bool | `void` |
| `GRAPHICS.BEGIN_TAKE_MISSION_CREATOR_PHOTO` | — | `bool` |
| `GRAPHICS.GET_STATUS_OF_TAKE_MISSION_CREATOR_PHOTO` | — | `int` |
| `GRAPHICS.FREE_MEMORY_FOR_MISSION_CREATOR_PHOTO` | — | `void` |
| `GRAPHICS.LOAD_MISSION_CREATOR_PHOTO` | a1: pointer, a2: int, a3: int, a4: int | `bool` |
| `GRAPHICS.GET_STATUS_OF_LOAD_MISSION_CREATOR_PHOTO` | a: pointer | `int` |
| `GRAPHICS.BEGIN_CREATE_MISSION_CREATOR_PHOTO_PREVIEW` | — | `bool` |
| `GRAPHICS.GET_STATUS_OF_CREATE_MISSION_CREATOR_PHOTO_PREVIEW` | — | `int` |
| `GRAPHICS.FREE_MEMORY_FOR_MISSION_CREATOR_PHOTO_PREVIEW` | — | `void` |
| `GRAPHICS.BEGIN_TAKE_HIGH_QUALITY_PHOTO` | — | `bool` |
| `GRAPHICS.GET_STATUS_OF_TAKE_HIGH_QUALITY_PHOTO` | — | `int` |
| `GRAPHICS.FREE_MEMORY_FOR_HIGH_QUALITY_PHOTO` | — | `void` |
| `GRAPHICS.SET_TAKEN_PHOTO_IS_MUGSHOT` | a: bool | `void` |
| `GRAPHICS.SET_ARENA_THEME_AND_VARIATION_FOR_TAKEN_PHOTO` | a: int, b: int | `void` |
| `GRAPHICS.SET_ON_ISLAND_X_FOR_TAKEN_PHOTO` | a: int | `void` |
| `GRAPHICS.SAVE_HIGH_QUALITY_PHOTO` | a: int | `bool` |
| `GRAPHICS.GET_STATUS_OF_SAVE_HIGH_QUALITY_PHOTO` | — | `int` |
| `GRAPHICS.BEGIN_CREATE_LOW_QUALITY_COPY_OF_PHOTO` | a: int | `bool` |
| `GRAPHICS.GET_STATUS_OF_CREATE_LOW_QUALITY_COPY_OF_PHOTO` | a: int | `int` |
| `GRAPHICS.FREE_MEMORY_FOR_LOW_QUALITY_PHOTO` | — | `void` |
| `GRAPHICS.DRAW_LOW_QUALITY_PHOTO_TO_PHONE` | a: bool, b: bool | `void` |
| `GRAPHICS.GET_MAXIMUM_NUMBER_OF_PHOTOS` | — | `int` |
| `GRAPHICS.GET_MAXIMUM_NUMBER_OF_CLOUD_PHOTOS` | — | `int` |
| `GRAPHICS.GET_CURRENT_NUMBER_OF_CLOUD_PHOTOS` | — | `int` |
| `GRAPHICS.QUEUE_OPERATION_TO_CREATE_SORTED_LIST_OF_PHOTOS` | a: int | `bool` |
| `GRAPHICS.GET_STATUS_OF_SORTED_LIST_OPERATION` | a: int | `int` |
| `GRAPHICS.CLEAR_STATUS_OF_SORTED_LIST_OPERATION` | — | `void` |
| `GRAPHICS.DOES_THIS_PHOTO_SLOT_CONTAIN_A_VALID_PHOTO` | a: int | `bool` |
| `GRAPHICS.LOAD_HIGH_QUALITY_PHOTO` | a: int | `bool` |
| `GRAPHICS.GET_LOAD_HIGH_QUALITY_PHOTO_STATUS` | a: int | `int` |
| `GRAPHICS.DRAW_LIGHT_WITH_RANGEEX` | a1: float, a2: float, a3: float, a4: int, a5: int, a6: int, a7: float, a8: float, a9: float | `void` |
| `GRAPHICS.DRAW_LIGHT_WITH_RANGE` | a1: float, a2: float, a3: float, a4: int, a5: int, a6: int, a7: float, a8: float | `void` |
| `GRAPHICS.DRAW_SPOT_LIGHT` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: float, a11: float, a12: float, a13: float, a14: float | `void` |
| `GRAPHICS.DRAW_SHADOWED_SPOT_LIGHT` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: float, a11: float, a12: float, a13: float, a14: float, a15: int | `void` |
| `GRAPHICS.FADE_UP_PED_LIGHT` | a: float | `void` |
| `GRAPHICS.UPDATE_LIGHTS_ON_ENTITY` | a: int | `void` |
| `GRAPHICS.SET_LIGHT_OVERRIDE_MAX_INTENSITY_SCALE` | a: int | `void` |
| `GRAPHICS.GET_LIGHT_OVERRIDE_MAX_INTENSITY_SCALE` | — | `float` |
| `GRAPHICS.DRAW_MARKER` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: int, a15: int, a16: int, a17: int, a18: bool, a19: bool, a20: int, a21: bool, a22: string, a23: string, a24: bool | `void` |
| `GRAPHICS.DRAW_MARKER_EX` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: int, a15: int, a16: int, a17: int, a18: bool, a19: bool, a20: int, a21: bool, a22: string, a23: string, a24: bool, a25: bool, a26: bool | `void` |
| `GRAPHICS.DRAW_MARKER_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int, a8: float | `void` |
| `GRAPHICS.CREATE_CHECKPOINT` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: int, a11: int, a12: int, a13: int | `int` |
| `GRAPHICS.SET_CHECKPOINT_INSIDE_CYLINDER_HEIGHT_SCALE` | a: int, b: float | `void` |
| `GRAPHICS.SET_CHECKPOINT_INSIDE_CYLINDER_SCALE` | a: int, b: float | `void` |
| `GRAPHICS.SET_CHECKPOINT_CYLINDER_HEIGHT` | a1: int, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.SET_CHECKPOINT_RGBA` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `GRAPHICS.SET_CHECKPOINT_RGBA2` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `GRAPHICS.SET_CHECKPOINT_CLIPPLANE_WITH_POS_NORM` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `GRAPHICS.SET_CHECKPOINT_FORCE_OLD_ARROW_POINTING` | a: int | `void` |
| `GRAPHICS.SET_CHECKPOINT_DECAL_ROT_ALIGNED_TO_CAMERA_ROT` | a: int | `void` |
| `GRAPHICS.SET_CHECKPOINT_FORCE_DIRECTION` | a: int | `void` |
| `GRAPHICS.SET_CHECKPOINT_DIRECTION` | a1: int, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.DELETE_CHECKPOINT` | a: int | `void` |
| `GRAPHICS.DONT_RENDER_IN_GAME_UI` | a: bool | `void` |
| `GRAPHICS.FORCE_RENDER_IN_GAME_UI` | a: bool | `void` |
| `GRAPHICS.REQUEST_STREAMED_TEXTURE_DICT` | a: string, b: bool | `void` |
| `GRAPHICS.HAS_STREAMED_TEXTURE_DICT_LOADED` | a: string | `bool` |
| `GRAPHICS.SET_STREAMED_TEXTURE_DICT_AS_NO_LONGER_NEEDED` | a: string | `void` |
| `GRAPHICS.DRAW_RECT` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int, a8: int, a9: bool | `void` |
| `GRAPHICS.SET_SCRIPT_GFX_DRAW_BEHIND_PAUSEMENU` | a: bool | `void` |
| `GRAPHICS.SET_SCRIPT_GFX_DRAW_ORDER` | a: int | `void` |
| `GRAPHICS.SET_SCRIPT_GFX_ALIGN` | a: int, b: int | `void` |
| `GRAPHICS.RESET_SCRIPT_GFX_ALIGN` | — | `void` |
| `GRAPHICS.SET_SCRIPT_GFX_ALIGN_PARAMS` | a1: float, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.GET_SCRIPT_GFX_ALIGN_POSITION` | a1: float, a2: float, a3: pointer, a4: pointer | `void` |
| `GRAPHICS.GET_SAFE_ZONE_SIZE` | — | `float` |
| `GRAPHICS.DRAW_SPRITE` | a1: string, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int, a11: int, a12: bool, a13: int | `void` |
| `GRAPHICS.DRAW_SPRITE_ARX` | a1: string, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int, a11: int, a12: int, a13: int | `void` |
| `GRAPHICS.DRAW_SPRITE_NAMED_RENDERTARGET` | a1: string, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int, a11: int, a12: int | `void` |
| `GRAPHICS.DRAW_SPRITE_ARX_WITH_UV` | a1: string, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: int, a13: int, a14: int, a15: int, a16: int | `void` |
| `GRAPHICS.ADD_ENTITY_ICON` | a: int, b: string | `int` |
| `GRAPHICS.SET_ENTITY_ICON_VISIBILITY` | a: int, b: bool | `void` |
| `GRAPHICS.SET_ENTITY_ICON_COLOR` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `GRAPHICS.SET_DRAW_ORIGIN` | a1: float, a2: float, a3: float, a4: bool | `void` |
| `GRAPHICS.CLEAR_DRAW_ORIGIN` | — | `void` |
| `GRAPHICS.SET_BINK_MOVIE` | a: string | `int` |
| `GRAPHICS.PLAY_BINK_MOVIE` | a: int | `void` |
| `GRAPHICS.STOP_BINK_MOVIE` | a: int | `void` |
| `GRAPHICS.RELEASE_BINK_MOVIE` | a: int | `void` |
| `GRAPHICS.DRAW_BINK_MOVIE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int, a10: int | `void` |
| `GRAPHICS.SET_BINK_MOVIE_TIME` | a: int, b: float | `void` |
| `GRAPHICS.GET_BINK_MOVIE_TIME` | a: int | `float` |
| `GRAPHICS.SET_BINK_MOVIE_VOLUME` | a: int, b: float | `void` |
| `GRAPHICS.ATTACH_TV_AUDIO_TO_ENTITY` | a: int | `void` |
| `GRAPHICS.SET_BINK_MOVIE_AUDIO_FRONTEND` | a: int, b: bool | `void` |
| `GRAPHICS.SET_TV_AUDIO_FRONTEND` | a: bool | `void` |
| `GRAPHICS.SET_BINK_SHOULD_SKIP` | a: int, b: bool | `void` |
| `GRAPHICS.LOAD_MOVIE_MESH_SET` | a: string | `int` |
| `GRAPHICS.RELEASE_MOVIE_MESH_SET` | a: int | `void` |
| `GRAPHICS.QUERY_MOVIE_MESH_SET_STATE` | a: int | `int` |
| `GRAPHICS.GET_SCREEN_RESOLUTION` | a: pointer, b: pointer | `void` |
| `GRAPHICS.GET_ACTUAL_SCREEN_RESOLUTION` | a: pointer, b: pointer | `void` |
| `GRAPHICS.GET_ASPECT_RATIO` | a: bool | `float` |
| `GRAPHICS.GET_SCREEN_ASPECT_RATIO` | — | `float` |
| `GRAPHICS.GET_IS_WIDESCREEN` | — | `bool` |
| `GRAPHICS.GET_IS_HIDEF` | — | `bool` |
| `GRAPHICS.ADJUST_NEXT_POS_SIZE_AS_NORMALIZED_16_9` | — | `void` |
| `GRAPHICS.DISPLAY_LOADING_SCREEN_NOW` | a: bool | `void` |
| `GRAPHICS.SET_NIGHTVISION` | a: bool | `void` |
| `GRAPHICS.GET_REQUESTINGNIGHTVISION` | — | `bool` |
| `GRAPHICS.GET_USINGNIGHTVISION` | — | `bool` |
| `GRAPHICS.SET_EXPOSURETWEAK` | a: bool | `void` |
| `GRAPHICS.FORCE_EXPOSURE_READBACK` | a: bool | `void` |
| `GRAPHICS.OVERRIDE_NIGHTVISION_LIGHT_RANGE` | a: float | `void` |
| `GRAPHICS.SET_NOISEOVERIDE` | a: bool | `void` |
| `GRAPHICS.SET_NOISINESSOVERIDE` | a: float | `void` |
| `GRAPHICS.GET_SCREEN_COORD_FROM_WORLD_COORD` | a1: float, a2: float, a3: float, a4: pointer, a5: pointer | `bool` |
| `GRAPHICS.GET_TEXTURE_RESOLUTION` | a: string, b: string | `vector3` |
| `GRAPHICS.OVERRIDE_PED_CREW_LOGO_TEXTURE` | a1: int, a2: string, a3: string | `bool` |
| `GRAPHICS.SET_DISTANCE_BLUR_STRENGTH_OVERRIDE` | a: float | `void` |
| `GRAPHICS.SET_FLASH` | a1: float, a2: float, a3: float, a4: float, a5: float | `void` |
| `GRAPHICS.DISABLE_OCCLUSION_THIS_FRAME` | — | `void` |
| `GRAPHICS.SET_ARTIFICIAL_LIGHTS_STATE` | a: bool | `void` |
| `GRAPHICS.SET_ARTIFICIAL_VEHICLE_LIGHTS_STATE` | a: bool | `void` |
| `GRAPHICS.DISABLE_HDTEX_THIS_FRAME` | — | `void` |
| `GRAPHICS.CREATE_TRACKED_POINT` | — | `int` |
| `GRAPHICS.SET_TRACKED_POINT_INFO` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `GRAPHICS.IS_TRACKED_POINT_VISIBLE` | a: int | `bool` |
| `GRAPHICS.DESTROY_TRACKED_POINT` | a: int | `void` |
| `GRAPHICS.SET_GRASS_CULL_SPHERE` | a1: float, a2: float, a3: float, a4: float | `int` |
| `GRAPHICS.REMOVE_GRASS_CULL_SPHERE` | a: int | `void` |
| `GRAPHICS.PROCGRASS_ENABLE_CULLSPHERE` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `GRAPHICS.PROCGRASS_DISABLE_CULLSPHERE` | a: int | `void` |
| `GRAPHICS.PROCGRASS_IS_CULLSPHERE_ENABLED` | a: int | `bool` |
| `GRAPHICS.PROCGRASS_ENABLE_AMBSCALESCAN` | — | `void` |
| `GRAPHICS.PROCGRASS_DISABLE_AMBSCALESCAN` | — | `void` |
| `GRAPHICS.DISABLE_PROCOBJ_CREATION` | — | `void` |
| `GRAPHICS.ENABLE_PROCOBJ_CREATION` | — | `void` |
| `GRAPHICS.GRASSBATCH_ENABLE_FLATTENING_EXT_IN_SPHERE` | a1: float, a2: float, a3: float, a4: int, a5: float, a6: float, a7: float, a8: float | `void` |
| `GRAPHICS.GRASSBATCH_ENABLE_FLATTENING_IN_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `GRAPHICS.GRASSBATCH_DISABLE_FLATTENING` | — | `void` |
| `GRAPHICS.CASCADE_SHADOWS_INIT_SESSION` | — | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_CASCADE_BOUNDS` | a1: int, a2: bool, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: float | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_CASCADE_BOUNDS_SCALE` | a: float | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_ENTITY_TRACKER_SCALE` | a: float | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_SPLIT_Z_EXP_WEIGHT` | a: float | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_BOUND_POSITION` | a: int | `void` |
| `GRAPHICS.CASCADE_SHADOWS_ENABLE_ENTITY_TRACKER` | a: bool | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_SCREEN_SIZE_CHECK_ENABLED` | a: bool | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_SHADOW_SAMPLE_TYPE` | a: string | `void` |
| `GRAPHICS.CASCADE_SHADOWS_CLEAR_SHADOW_SAMPLE_TYPE` | — | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_AIRCRAFT_MODE` | a: bool | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_DYNAMIC_DEPTH_MODE` | a: bool | `void` |
| `GRAPHICS.CASCADE_SHADOWS_SET_DYNAMIC_DEPTH_VALUE` | a: float | `void` |
| `GRAPHICS.CASCADE_SHADOWS_ENABLE_FREEZER` | a: bool | `void` |
| `GRAPHICS.WATER_REFLECTION_SET_SCRIPT_OBJECT_VISIBILITY` | a: int | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_ENABLED` | a: bool | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_PATH` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_RADIUS` | a1: float, a2: float, a3: float | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_COLOUR` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int, a11: int, a12: int | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_TESSELLATION` | a: int, b: int | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_FIXED_CONTROL_POINT_ENABLE` | a: bool | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_FIXED_CONTROL_POINT` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: int, a8: int, a9: int | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_SHADER_PARAMS` | a1: float, a2: float, a3: float, a4: float, a5: float | `void` |
| `GRAPHICS.GOLF_TRAIL_SET_FACING` | a: bool | `void` |
| `GRAPHICS.GOLF_TRAIL_GET_MAX_HEIGHT` | — | `float` |
| `GRAPHICS.GOLF_TRAIL_GET_VISUAL_CONTROL_POINT` | a: int | `vector3` |
| `GRAPHICS.SET_SEETHROUGH` | a: bool | `void` |
| `GRAPHICS.GET_USINGSEETHROUGH` | — | `bool` |
| `GRAPHICS.SEETHROUGH_RESET` | — | `void` |
| `GRAPHICS.SEETHROUGH_SET_FADE_STARTDISTANCE` | a: float | `void` |
| `GRAPHICS.SEETHROUGH_SET_FADE_ENDDISTANCE` | a: float | `void` |
| `GRAPHICS.SEETHROUGH_GET_MAX_THICKNESS` | — | `float` |
| `GRAPHICS.SEETHROUGH_SET_MAX_THICKNESS` | a: float | `void` |
| `GRAPHICS.SEETHROUGH_SET_NOISE_MIN` | a: float | `void` |
| `GRAPHICS.SEETHROUGH_SET_NOISE_MAX` | a: float | `void` |
| `GRAPHICS.SEETHROUGH_SET_HILIGHT_INTENSITY` | a: float | `void` |
| `GRAPHICS.SEETHROUGH_SET_HIGHLIGHT_NOISE` | a: float | `void` |
| `GRAPHICS.SEETHROUGH_SET_HEATSCALE` | a: int, b: float | `void` |
| `GRAPHICS.SEETHROUGH_SET_COLOR_NEAR` | a1: int, a2: int, a3: int | `void` |
| `GRAPHICS.SET_MOTIONBLUR_MAX_VEL_SCALER` | a: float | `void` |
| `GRAPHICS.GET_MOTIONBLUR_MAX_VEL_SCALER` | — | `float` |
| `GRAPHICS.SET_FORCE_MOTIONBLUR` | a: bool | `void` |
| `GRAPHICS.TOGGLE_PLAYER_DAMAGE_OVERLAY` | a: bool | `void` |
| `GRAPHICS.RESET_ADAPTATION` | a: int | `void` |
| `GRAPHICS.TRIGGER_SCREENBLUR_FADE_IN` | a: float | `bool` |
| `GRAPHICS.TRIGGER_SCREENBLUR_FADE_OUT` | a: float | `bool` |
| `GRAPHICS.DISABLE_SCREENBLUR_FADE` | — | `void` |
| `GRAPHICS.GET_SCREENBLUR_FADE_CURRENT_TIME` | — | `float` |
| `GRAPHICS.IS_SCREENBLUR_FADE_RUNNING` | — | `bool` |
| `GRAPHICS.TOGGLE_PAUSED_RENDERPHASES` | a: bool | `void` |
| `GRAPHICS.GET_TOGGLE_PAUSED_RENDERPHASES_STATUS` | — | `bool` |
| `GRAPHICS.RESET_PAUSED_RENDERPHASES` | — | `void` |
| `GRAPHICS.GRAB_PAUSEMENU_OWNERSHIP` | — | `void` |
| `GRAPHICS.SET_HIDOF_OVERRIDE` | a1: bool, a2: bool, a3: float, a4: float, a5: float, a6: float | `void` |
| `GRAPHICS.SET_LOCK_ADAPTIVE_DOF_DISTANCE` | a: bool | `void` |
| `GRAPHICS.PHONEPHOTOEDITOR_TOGGLE` | a: bool | `bool` |
| `GRAPHICS.PHONEPHOTOEDITOR_IS_ACTIVE` | — | `bool` |
| `GRAPHICS.PHONEPHOTOEDITOR_SET_FRAME_TXD` | a: string, b: bool | `bool` |
| `GRAPHICS.START_PARTICLE_FX_NON_LOOPED_AT_COORD` | a1: string, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: bool, a11: bool | `bool` |
| `GRAPHICS.START_NETWORKED_PARTICLE_FX_NON_LOOPED_AT_COORD` | a1: string, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: bool, a11: bool, a12: bool | `bool` |
| `GRAPHICS.START_PARTICLE_FX_NON_LOOPED_ON_PED_BONE` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: float, a11: bool, a12: bool, a13: bool | `bool` |
| `GRAPHICS.START_NETWORKED_PARTICLE_FX_NON_LOOPED_ON_PED_BONE` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: float, a11: bool, a12: bool, a13: bool | `bool` |
| `GRAPHICS.START_PARTICLE_FX_NON_LOOPED_ON_ENTITY` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool, a11: bool, a12: bool | `bool` |
| `GRAPHICS.START_NETWORKED_PARTICLE_FX_NON_LOOPED_ON_ENTITY` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool, a11: bool, a12: bool | `bool` |
| `GRAPHICS.START_PARTICLE_FX_NON_LOOPED_ON_ENTITY_BONE` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: float, a11: bool, a12: bool, a13: bool | `bool` |
| `GRAPHICS.SET_PARTICLE_FX_NON_LOOPED_COLOUR` | a1: float, a2: float, a3: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_NON_LOOPED_ALPHA` | a: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_NON_LOOPED_SCALE` | a: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_NON_LOOPED_EMITTER_SIZE` | a1: float, a2: float, a3: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_FORCE_VEHICLE_INTERIOR` | a: bool | `void` |
| `GRAPHICS.START_PARTICLE_FX_LOOPED_AT_COORD` | a1: string, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: bool, a11: bool, a12: bool | `int` |
| `GRAPHICS.START_PARTICLE_FX_LOOPED_ON_PED_BONE` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: float, a11: bool, a12: bool, a13: bool | `int` |
| `GRAPHICS.START_PARTICLE_FX_LOOPED_ON_ENTITY` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool, a11: bool, a12: bool | `int` |
| `GRAPHICS.START_PARTICLE_FX_LOOPED_ON_ENTITY_BONE` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: float, a11: bool, a12: bool, a13: bool | `int` |
| `GRAPHICS.START_NETWORKED_PARTICLE_FX_LOOPED_ON_ENTITY` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool, a11: bool, a12: bool, a13: float, a14: float, a15: float, a16: float | `int` |
| `GRAPHICS.START_NETWORKED_PARTICLE_FX_LOOPED_ON_ENTITY_BONE` | a1: string, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: float, a11: bool, a12: bool, a13: bool, a14: float, a15: float, a16: float, a17: float | `int` |
| `GRAPHICS.STOP_PARTICLE_FX_LOOPED` | a: int, b: bool | `void` |
| `GRAPHICS.REMOVE_PARTICLE_FX` | a: int, b: bool | `void` |
| `GRAPHICS.REMOVE_PARTICLE_FX_FROM_ENTITY` | a: int | `void` |
| `GRAPHICS.REMOVE_PARTICLE_FX_IN_RANGE` | a1: float, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.FORCE_PARTICLE_FX_IN_VEHICLE_INTERIOR` | a: int, b: int | `void` |
| `GRAPHICS.DOES_PARTICLE_FX_LOOPED_EXIST` | a: int | `bool` |
| `GRAPHICS.SET_PARTICLE_FX_LOOPED_OFFSETS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_LOOPED_EVOLUTION` | a1: int, a2: string, a3: float, a4: bool | `void` |
| `GRAPHICS.PARTICLE_FX_LOOPED_COLOUR` | a1: int, a2: float, a3: float, a4: float, a5: bool | `void` |
| `GRAPHICS.SET_PARTICLE_FX_LOOPED_ALPHA` | a: int, b: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_LOOPED_SCALE` | a: int, b: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_LOOPED_FAR_CLIP_DIST` | a: int, b: float | `void` |
| `GRAPHICS._SET_PARTICLE_FX_LOOPED_CAMERA_BIAS` | a: int, b: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_CAM_INSIDE_VEHICLE` | a: bool | `void` |
| `GRAPHICS.SET_PARTICLE_FX_CAM_INSIDE_NONPLAYER_VEHICLE` | a: int, b: bool | `void` |
| `GRAPHICS.SET_PARTICLE_FX_SHOOTOUT_BOAT` | a: int | `void` |
| `GRAPHICS.CLEAR_PARTICLE_FX_SHOOTOUT_BOAT` | — | `void` |
| `GRAPHICS.SET_PARTICLE_FX_BLOOD_SCALE` | a: int | `void` |
| `GRAPHICS.DISABLE_IN_WATER_PTFX` | a: bool | `void` |
| `GRAPHICS.DISABLE_DOWNWASH_PTFX` | a: bool | `void` |
| `GRAPHICS.SET_PARTICLE_FX_SLIPSTREAM_LODRANGE_SCALE` | a: float | `void` |
| `GRAPHICS.ENABLE_CLOWN_BLOOD_VFX` | a: bool | `void` |
| `GRAPHICS.ENABLE_ALIEN_BLOOD_VFX` | a: bool | `void` |
| `GRAPHICS.SET_PARTICLE_FX_BULLET_IMPACT_SCALE` | a: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_BULLET_IMPACT_LODRANGE_SCALE` | a: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_BULLET_TRACE_NO_ANGLE_REJECT` | a: bool | `void` |
| `GRAPHICS.SET_PARTICLE_FX_BANG_SCRAPE_LODRANGE_SCALE` | a: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_FOOT_LODRANGE_SCALE` | a: float | `void` |
| `GRAPHICS.SET_PARTICLE_FX_FOOT_OVERRIDE_NAME` | a: string | `void` |
| `GRAPHICS.SET_SKIDMARK_RANGE_SCALE` | a: float | `void` |
| `GRAPHICS.SET_PTFX_FORCE_VEHICLE_INTERIOR_FLAG` | a: int | `void` |
| `GRAPHICS._REGISTER_PTFX_FOG_VOLUME` | a1: float, a2: float, a3: float, a4: float, a5: float | `void` |
| `GRAPHICS.REGISTER_POSTFX_BULLET_IMPACT` | a1: float, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.FORCE_POSTFX_BULLET_IMPACTS_AFTER_HUD` | a: bool | `void` |
| `GRAPHICS.USE_PARTICLE_FX_ASSET` | a: string | `void` |
| `GRAPHICS.SET_PARTICLE_FX_OVERRIDE` | a: string, b: string | `void` |
| `GRAPHICS.RESET_PARTICLE_FX_OVERRIDE` | a: string | `void` |
| `GRAPHICS._START_VEHICLE_PARTICLE_FX_LOOPED` | a1: int, a2: string, a3: bool, a4: bool, a5: bool | `int` |
| `GRAPHICS.SET_WEATHER_PTFX_USE_OVERRIDE_SETTINGS` | a: bool | `void` |
| `GRAPHICS.SET_WEATHER_PTFX_OVERRIDE_CURR_LEVEL` | a: float | `void` |
| `GRAPHICS.WASH_DECALS_IN_RANGE` | a1: float, a2: float, a3: float, a4: float, a5: float | `void` |
| `GRAPHICS.WASH_DECALS_FROM_VEHICLE` | a: int, b: float | `void` |
| `GRAPHICS.FADE_DECALS_IN_RANGE` | a1: float, a2: float, a3: float, a4: float, a5: float | `void` |
| `GRAPHICS.REMOVE_DECALS_IN_RANGE` | a1: float, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.REMOVE_DECALS_FROM_OBJECT` | a: int | `void` |
| `GRAPHICS.REMOVE_DECALS_FROM_OBJECT_FACING` | a1: int, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.REMOVE_DECALS_FROM_VEHICLE` | a: int | `void` |
| `GRAPHICS.ADD_DECAL` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: float, a15: float, a16: float, a17: float, a18: bool, a19: bool, a20: bool | `int` |
| `GRAPHICS.ADD_PETROL_DECAL` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `int` |
| `GRAPHICS.ADD_OIL_DECAL` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `int` |
| `GRAPHICS.START_PETROL_TRAIL_DECALS` | a: float | `void` |
| `GRAPHICS.ADD_PETROL_TRAIL_DECAL_INFO` | a1: float, a2: float, a3: float, a4: float | `void` |
| `GRAPHICS.END_PETROL_TRAIL_DECALS` | — | `void` |
| `GRAPHICS.REMOVE_DECAL` | a: int | `void` |
| `GRAPHICS.IS_DECAL_ALIVE` | a: int | `bool` |
| `GRAPHICS.GET_DECAL_WASH_LEVEL` | a: int | `float` |
| `GRAPHICS.SET_DISABLE_PETROL_DECALS_IGNITING_THIS_FRAME` | — | `void` |
| `GRAPHICS.SET_DISABLE_PETROL_DECALS_RECYCLING_THIS_FRAME` | — | `void` |
| `GRAPHICS.SET_DISABLE_DECAL_RENDERING_THIS_FRAME` | — | `void` |
| `GRAPHICS.GET_IS_PETROL_DECAL_IN_RANGE` | a1: float, a2: float, a3: float, a4: float | `bool` |
| `GRAPHICS.PATCH_DECAL_DIFFUSE_MAP` | a1: int, a2: string, a3: string | `void` |
| `GRAPHICS.UNPATCH_DECAL_DIFFUSE_MAP` | a: int | `void` |
| `GRAPHICS.MOVE_VEHICLE_DECALS` | a: int, b: int | `void` |
| `GRAPHICS.ADD_VEHICLE_CREW_EMBLEM` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: int, a15: int | `bool` |
| `GRAPHICS.ABORT_VEHICLE_CREW_EMBLEM_REQUEST` | a: pointer | `bool` |
| `GRAPHICS.REMOVE_VEHICLE_CREW_EMBLEM` | a: int, b: int | `void` |
| `GRAPHICS.GET_VEHICLE_CREW_EMBLEM_REQUEST_STATE` | a: int, b: int | `int` |
| `GRAPHICS.DOES_VEHICLE_HAVE_CREW_EMBLEM` | a: int, b: int | `bool` |
| `GRAPHICS.DISABLE_COMPOSITE_SHOTGUN_DECALS` | a: bool | `void` |
| `GRAPHICS.DISABLE_SCUFF_DECALS` | a: bool | `void` |
| `GRAPHICS.SET_DECAL_BULLET_IMPACT_RANGE_SCALE` | a: float | `void` |
| `GRAPHICS.OVERRIDE_INTERIOR_SMOKE_NAME` | a: string | `void` |
| `GRAPHICS.OVERRIDE_INTERIOR_SMOKE_LEVEL` | a: float | `void` |
| `GRAPHICS.OVERRIDE_INTERIOR_SMOKE_END` | — | `void` |
| `GRAPHICS.REGISTER_NOIR_LENS_EFFECT` | — | `void` |
| `GRAPHICS.DISABLE_VEHICLE_DISTANTLIGHTS` | a: bool | `void` |
| `GRAPHICS.RENDER_SHADOWED_LIGHTS_WITH_NO_SHADOWS` | a: bool | `void` |
| `GRAPHICS.REQUEST_EARLY_LIGHT_CHECK` | — | `void` |
| `GRAPHICS.USE_SNOW_FOOT_VFX_WHEN_UNSHELTERED` | a: bool | `void` |
| `GRAPHICS._FORCE_ALLOW_SNOW_FOOT_VFX_ON_ICE` | a: bool | `void` |
| `GRAPHICS.USE_SNOW_WHEEL_VFX_WHEN_UNSHELTERED` | a: bool | `void` |
| `GRAPHICS.DISABLE_REGION_VFX` | a: int | `void` |
| `GRAPHICS._FORCE_GROUND_SNOW_PASS` | a: bool | `void` |
| `GRAPHICS.PRESET_INTERIOR_AMBIENT_CACHE` | a: string | `void` |
| `GRAPHICS.SET_TIMECYCLE_MODIFIER` | a: string | `void` |
| `GRAPHICS.SET_TIMECYCLE_MODIFIER_STRENGTH` | a: float | `void` |
| `GRAPHICS.SET_TRANSITION_TIMECYCLE_MODIFIER` | a: string, b: float | `void` |
| `GRAPHICS.SET_TRANSITION_OUT_OF_TIMECYCLE_MODIFIER` | a: float | `void` |
| `GRAPHICS.CLEAR_TIMECYCLE_MODIFIER` | — | `void` |
| `GRAPHICS.GET_TIMECYCLE_MODIFIER_INDEX` | — | `int` |
| `GRAPHICS.GET_TIMECYCLE_TRANSITION_MODIFIER_INDEX` | — | `int` |
| `GRAPHICS.GET_IS_TIMECYCLE_TRANSITIONING_OUT` | — | `bool` |
| `GRAPHICS.PUSH_TIMECYCLE_MODIFIER` | — | `void` |
| `GRAPHICS.POP_TIMECYCLE_MODIFIER` | — | `void` |
| `GRAPHICS.SET_CURRENT_PLAYER_TCMODIFIER` | a: string | `void` |
| `GRAPHICS.SET_PLAYER_TCMODIFIER_TRANSITION` | a: float | `void` |
| `GRAPHICS.SET_NEXT_PLAYER_TCMODIFIER` | a: string | `void` |
| `GRAPHICS.ADD_TCMODIFIER_OVERRIDE` | a: string, b: string | `void` |
| `GRAPHICS.CLEAR_ALL_TCMODIFIER_OVERRIDES` | a: string | `void` |
| `GRAPHICS.SET_EXTRA_TCMODIFIER` | a: string | `void` |
| `GRAPHICS.CLEAR_EXTRA_TCMODIFIER` | — | `void` |
| `GRAPHICS.GET_EXTRA_TCMODIFIER` | — | `int` |
| `GRAPHICS.ENABLE_MOON_CYCLE_OVERRIDE` | a: float | `void` |
| `GRAPHICS.DISABLE_MOON_CYCLE_OVERRIDE` | — | `void` |
| `GRAPHICS.REQUEST_SCALEFORM_MOVIE` | a: string | `int` |
| `GRAPHICS.REQUEST_SCALEFORM_MOVIE_WITH_IGNORE_SUPER_WIDESCREEN` | a: string | `int` |
| `GRAPHICS.REQUEST_SCALEFORM_MOVIE_INSTANCE` | a: string | `int` |
| `GRAPHICS.REQUEST_SCALEFORM_MOVIE_SKIP_RENDER_WHILE_PAUSED` | a: string | `int` |
| `GRAPHICS.HAS_SCALEFORM_MOVIE_LOADED` | a: int | `bool` |
| `GRAPHICS._HAS_SCALEFORM_MOVIE_NAMED_LOADED` | a: pointer, b: string | `bool` |
| `GRAPHICS.IS_ACTIVE_SCALEFORM_MOVIE_DELETING` | a: int | `bool` |
| `GRAPHICS.IS_SCALEFORM_MOVIE_DELETING` | a: int | `bool` |
| `GRAPHICS.HAS_SCALEFORM_MOVIE_FILENAME_LOADED` | a: string | `bool` |
| `GRAPHICS.HAS_SCALEFORM_CONTAINER_MOVIE_LOADED_INTO_PARENT` | a: int | `bool` |
| `GRAPHICS.SET_SCALEFORM_MOVIE_AS_NO_LONGER_NEEDED` | a: pointer | `void` |
| `GRAPHICS._SET_SCALEFORM_MOVIE_NAMED_AS_NO_LONGER_NEEDED` | a: int, b: string | `void` |
| `GRAPHICS.SET_SCALEFORM_MOVIE_TO_USE_SYSTEM_TIME` | a: int, b: bool | `void` |
| `GRAPHICS.SET_SCALEFORM_MOVIE_TO_USE_LARGE_RT` | a: int, b: bool | `void` |
| `GRAPHICS.SET_SCALEFORM_MOVIE_TO_USE_SUPER_LARGE_RT` | a: int, b: bool | `void` |
| `GRAPHICS.DRAW_SCALEFORM_MOVIE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: int, a8: int, a9: int, a10: int | `void` |
| `GRAPHICS.DRAW_SCALEFORM_MOVIE_FULLSCREEN` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `GRAPHICS.DRAW_SCALEFORM_MOVIE_FULLSCREEN_MASKED` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `GRAPHICS.DRAW_SCALEFORM_MOVIE_3D` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: int | `void` |
| `GRAPHICS.DRAW_SCALEFORM_MOVIE_3D_SOLID` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: int | `void` |
| `GRAPHICS.CALL_SCALEFORM_MOVIE_METHOD` | a: int, b: string | `void` |
| `GRAPHICS.CALL_SCALEFORM_MOVIE_METHOD_WITH_NUMBER` | a1: int, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `GRAPHICS.CALL_SCALEFORM_MOVIE_METHOD_WITH_STRING` | a1: int, a2: string, a3: string, a4: string, a5: string, a6: string, a7: string | `void` |
| `GRAPHICS.CALL_SCALEFORM_MOVIE_METHOD_WITH_NUMBER_AND_STRING` | a1: int, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: string, a9: string, a10: string, a11: string, a12: string | `void` |
| `GRAPHICS.BEGIN_SCALEFORM_SCRIPT_HUD_MOVIE_METHOD` | a: int, b: string | `bool` |
| `GRAPHICS.BEGIN_SCALEFORM_MOVIE_METHOD` | a: int, b: string | `bool` |
| `GRAPHICS.BEGIN_SCALEFORM_MOVIE_METHOD_ON_FRONTEND` | a: string | `bool` |
| `GRAPHICS.BEGIN_SCALEFORM_MOVIE_METHOD_ON_FRONTEND_HEADER` | a: string | `bool` |
| `GRAPHICS.END_SCALEFORM_MOVIE_METHOD` | — | `void` |
| `GRAPHICS.END_SCALEFORM_MOVIE_METHOD_RETURN_VALUE` | — | `int` |
| `GRAPHICS.IS_SCALEFORM_MOVIE_METHOD_RETURN_VALUE_READY` | a: int | `bool` |
| `GRAPHICS.GET_SCALEFORM_MOVIE_METHOD_RETURN_VALUE_INT` | a: int | `int` |
| `GRAPHICS.GET_SCALEFORM_MOVIE_METHOD_RETURN_VALUE_BOOL` | a: int | `bool` |
| `GRAPHICS.GET_SCALEFORM_MOVIE_METHOD_RETURN_VALUE_STRING` | a: int | `string` |
| `GRAPHICS.SCALEFORM_MOVIE_METHOD_ADD_PARAM_INT` | a: int | `void` |
| `GRAPHICS.SCALEFORM_MOVIE_METHOD_ADD_PARAM_FLOAT` | a: float | `void` |
| `GRAPHICS.SCALEFORM_MOVIE_METHOD_ADD_PARAM_BOOL` | a: bool | `void` |
| `GRAPHICS.BEGIN_TEXT_COMMAND_SCALEFORM_STRING` | a: string | `void` |
| `GRAPHICS.END_TEXT_COMMAND_SCALEFORM_STRING` | — | `void` |
| `GRAPHICS.END_TEXT_COMMAND_UNPARSED_SCALEFORM_STRING` | — | `void` |
| `GRAPHICS.SCALEFORM_MOVIE_METHOD_ADD_PARAM_LITERAL_STRING` | a: string | `void` |
| `GRAPHICS.SCALEFORM_MOVIE_METHOD_ADD_PARAM_TEXTURE_NAME_STRING` | a: string | `void` |
| `GRAPHICS.SCALEFORM_MOVIE_METHOD_ADD_PARAM_PLAYER_NAME_STRING` | a: string | `void` |
| `GRAPHICS.DOES_LATEST_BRIEF_STRING_EXIST` | a: int | `bool` |
| `GRAPHICS.SCALEFORM_MOVIE_METHOD_ADD_PARAM_LATEST_BRIEF_STRING` | a: int | `void` |
| `GRAPHICS.REQUEST_SCALEFORM_SCRIPT_HUD_MOVIE` | a: int | `void` |
| `GRAPHICS.HAS_SCALEFORM_SCRIPT_HUD_MOVIE_LOADED` | a: int | `bool` |
| `GRAPHICS.REMOVE_SCALEFORM_SCRIPT_HUD_MOVIE` | a: int | `void` |
| `GRAPHICS.PASS_KEYBOARD_INPUT_TO_SCALEFORM` | a: int | `bool` |
| `GRAPHICS.SET_TV_CHANNEL` | a: int | `void` |
| `GRAPHICS.GET_TV_CHANNEL` | — | `int` |
| `GRAPHICS.SET_TV_VOLUME` | a: float | `void` |
| `GRAPHICS.GET_TV_VOLUME` | — | `float` |
| `GRAPHICS.DRAW_TV_CHANNEL` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: int, a7: int, a8: int, a9: int | `void` |
| `GRAPHICS.SET_TV_CHANNEL_PLAYLIST` | a1: int, a2: string, a3: bool | `void` |
| `GRAPHICS.SET_TV_CHANNEL_PLAYLIST_AT_HOUR` | a1: int, a2: string, a3: int | `void` |
| `GRAPHICS._SET_TV_CHANNEL_PLAYLIST_DIRTY` | a: int, b: bool | `int` |
| `GRAPHICS.CLEAR_TV_CHANNEL_PLAYLIST` | a: int | `void` |
| `GRAPHICS.IS_PLAYLIST_ON_CHANNEL` | a: int, b: int | `bool` |
| `GRAPHICS.IS_TVSHOW_CURRENTLY_PLAYING` | a: hash | `bool` |
| `GRAPHICS.ENABLE_MOVIE_KEYFRAME_WAIT` | a: bool | `void` |
| `GRAPHICS.SET_TV_PLAYER_WATCHING_THIS_FRAME` | a: int | `void` |
| `GRAPHICS.GET_CURRENT_TV_CLIP_NAMEHASH` | — | `hash` |
| `GRAPHICS.ENABLE_MOVIE_SUBTITLES` | a: bool | `void` |
| `GRAPHICS.UI3DSCENE_IS_AVAILABLE` | — | `bool` |
| `GRAPHICS.UI3DSCENE_PUSH_PRESET` | a: string | `bool` |
| `GRAPHICS.UI3DSCENE_ASSIGN_PED_TO_SLOT` | a1: string, a2: int, a3: int, a4: float, a5: float, a6: float | `bool` |
| `GRAPHICS.UI3DSCENE_CLEAR_PATCHED_DATA` | — | `void` |
| `GRAPHICS.UI3DSCENE_MAKE_PUSHED_PRESET_PERSISTENT` | a: bool | `void` |
| `GRAPHICS.TERRAINGRID_ACTIVATE` | a: bool | `void` |
| `GRAPHICS.TERRAINGRID_SET_PARAMS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float | `void` |
| `GRAPHICS.TERRAINGRID_SET_COLOURS` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int, a11: int, a12: int | `void` |
| `GRAPHICS.ANIMPOSTFX_PLAY` | a1: string, a2: int, a3: bool | `void` |
| `GRAPHICS.ANIMPOSTFX_STOP` | a: string | `void` |
| `GRAPHICS.ANIMPOSTFX_GET_CURRENT_TIME` | a: string | `float` |
| `GRAPHICS.ANIMPOSTFX_IS_RUNNING` | a: string | `bool` |
| `GRAPHICS.ANIMPOSTFX_STOP_ALL` | — | `void` |
| `GRAPHICS.ANIMPOSTFX_STOP_AND_FLUSH_REQUESTS` | a: string | `void` |

## GTA

| Native | Params | Returns |
| --- | --- | --- |
| `GTA.CREATE_MOBILE_PHONE` | a: int | `void` |
| `GTA.DESTROY_MOBILE_PHONE` | — | `void` |
| `GTA.SET_MOBILE_PHONE_SCALE` | a: float | `void` |
| `GTA.SET_MOBILE_PHONE_ROTATION` | a1: float, a2: float, a3: float, a4: int | `void` |
| `GTA.GET_MOBILE_PHONE_ROTATION` | a: vector3, b: int | `void` |
| `GTA.SET_MOBILE_PHONE_POSITION` | a1: float, a2: float, a3: float | `void` |
| `GTA.GET_MOBILE_PHONE_POSITION` | a: vector3 | `void` |
| `GTA.SCRIPT_IS_MOVING_MOBILE_PHONE_OFFSCREEN` | a: bool | `void` |
| `GTA.CAN_PHONE_BE_SEEN_ON_SCREEN` | — | `bool` |
| `GTA.SET_MOBILE_PHONE_DOF_STATE` | a: bool | `void` |
| `GTA.CELL_SET_INPUT` | a: int | `void` |
| `GTA.CELL_HORIZONTAL_MODE_TOGGLE` | a: bool | `void` |
| `GTA.CELL_CAM_ACTIVATE` | a: bool, b: bool | `void` |
| `GTA.CELL_CAM_ACTIVATE_SELFIE_MODE` | a: bool | `void` |
| `GTA.CELL_CAM_ACTIVATE_SHALLOW_DOF_MODE` | a: bool | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_SIDE_OFFSET_SCALING` | a: float | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_HORZ_PAN_OFFSET` | a: float | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_VERT_PAN_OFFSET` | a: float | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_ROLL_OFFSET` | a: float | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_DISTANCE_SCALING` | a: float | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_HEAD_YAW_OFFSET` | a: float | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_HEAD_ROLL_OFFSET` | a: float | `void` |
| `GTA.CELL_CAM_SET_SELFIE_MODE_HEAD_PITCH_OFFSET` | a: float | `void` |
| `GTA.CELL_CAM_IS_CHAR_VISIBLE_NO_FACE_CHECK` | a: int | `bool` |
| `GTA.GET_MOBILE_PHONE_RENDER_ID` | a: pointer | `void` |

## HUD

| Native | Params | Returns |
| --- | --- | --- |
| `HUD.BEGIN_TEXT_COMMAND_BUSYSPINNER_ON` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_BUSYSPINNER_ON` | a: int | `void` |
| `HUD.BUSYSPINNER_OFF` | — | `void` |
| `HUD.PRELOAD_BUSYSPINNER` | — | `void` |
| `HUD.BUSYSPINNER_IS_ON` | — | `bool` |
| `HUD.BUSYSPINNER_IS_DISPLAYING` | — | `bool` |
| `HUD.DISABLE_PAUSEMENU_SPINNER` | a: bool | `void` |
| `HUD.SET_MOUSE_CURSOR_THIS_FRAME` | — | `void` |
| `HUD.SET_MOUSE_CURSOR_STYLE` | a: int | `void` |
| `HUD.SET_MOUSE_CURSOR_VISIBLE` | a: bool | `void` |
| `HUD.IS_MOUSE_ROLLED_OVER_INSTRUCTIONAL_BUTTONS` | — | `bool` |
| `HUD.GET_MOUSE_EVENT` | a1: int, a2: pointer, a3: pointer, a4: pointer | `bool` |
| `HUD.THEFEED_ONLY_SHOW_TOOLTIPS` | a: bool | `void` |
| `HUD.THEFEED_SET_SCRIPTED_MENU_HEIGHT` | a: float | `void` |
| `HUD.THEFEED_HIDE` | — | `void` |
| `HUD.THEFEED_HIDE_THIS_FRAME` | — | `void` |
| `HUD.THEFEED_SHOW` | — | `void` |
| `HUD.THEFEED_FLUSH_QUEUE` | — | `void` |
| `HUD.THEFEED_REMOVE_ITEM` | a: int | `void` |
| `HUD.THEFEED_FORCE_RENDER_ON` | — | `void` |
| `HUD.THEFEED_FORCE_RENDER_OFF` | — | `void` |
| `HUD.THEFEED_PAUSE` | — | `void` |
| `HUD.THEFEED_RESUME` | — | `void` |
| `HUD.THEFEED_IS_PAUSED` | — | `bool` |
| `HUD.THEFEED_REPORT_LOGO_ON` | — | `void` |
| `HUD.THEFEED_REPORT_LOGO_OFF` | — | `void` |
| `HUD.THEFEED_GET_LAST_SHOWN_PHONE_ACTIVATABLE_FEED_ID` | — | `int` |
| `HUD.THEFEED_AUTO_POST_GAMETIPS_ON` | — | `void` |
| `HUD.THEFEED_AUTO_POST_GAMETIPS_OFF` | — | `void` |
| `HUD.THEFEED_SET_BACKGROUND_COLOR_FOR_NEXT_POST` | a: int | `void` |
| `HUD.THEFEED_SET_RGBA_PARAMETER_FOR_NEXT_MESSAGE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `HUD.THEFEED_SET_FLASH_DURATION_PARAMETER_FOR_NEXT_MESSAGE` | a: int | `void` |
| `HUD.THEFEED_SET_VIBRATE_PARAMETER_FOR_NEXT_MESSAGE` | a: bool | `void` |
| `HUD.THEFEED_RESET_ALL_PARAMETERS` | — | `void` |
| `HUD.THEFEED_FREEZE_NEXT_POST` | — | `void` |
| `HUD.THEFEED_CLEAR_FROZEN_POST` | — | `void` |
| `HUD.THEFEED_SET_SNAP_FEED_ITEM_POSITIONS` | a: bool | `void` |
| `HUD.THEFEED_UPDATE_ITEM_TEXTURE` | a1: string, a2: string, a3: string, a4: string | `void` |
| `HUD.BEGIN_TEXT_COMMAND_THEFEED_POST` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_STATS` | a1: string, a2: int, a3: bool, a4: int, a5: bool, a6: string, a7: string | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_MESSAGETEXT` | a1: string, a2: string, a3: bool, a4: int, a5: string, a6: string | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_MESSAGETEXT_SUBTITLE_LABEL` | a1: string, a2: string, a3: bool, a4: int, a5: string, a6: string | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_MESSAGETEXT_TU` | a1: string, a2: string, a3: bool, a4: int, a5: string, a6: string, a7: float | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_MESSAGETEXT_WITH_CREW_TAG` | a1: string, a2: string, a3: bool, a4: int, a5: string, a6: string, a7: float, a8: string | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_MESSAGETEXT_WITH_CREW_TAG_AND_ADDITIONAL_ICON` | a1: string, a2: string, a3: bool, a4: int, a5: string, a6: string, a7: float, a8: string, a9: int, a10: int | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_TICKER` | a: bool, b: bool | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_TICKER_FORCED` | a: bool, b: bool | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_TICKER_WITH_TOKENS` | a: bool, b: bool | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_AWARD` | a1: string, a2: string, a3: int, a4: int, a5: string | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_CREWTAG` | a1: bool, a2: bool, a3: pointer, a4: int, a5: bool, a6: bool, a7: int, a8: int, a9: int, a10: int | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_CREWTAG_WITH_GAME_NAME` | a1: bool, a2: bool, a3: pointer, a4: int, a5: bool, a6: bool, a7: int, a8: string, a9: int, a10: int, a11: int | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_UNLOCK` | a1: string, a2: int, a3: string | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_UNLOCK_TU` | a1: string, a2: int, a3: string, a4: int | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_UNLOCK_TU_WITH_COLOR` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_MPTICKER` | a: bool, b: bool | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_CREW_RANKUP_WITH_LITERAL_FLAG` | a1: string, a2: string, a3: string, a4: bool, a5: bool | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_VERSUS_TU` | a1: string, a2: string, a3: int, a4: string, a5: string, a6: int, a7: int, a8: int | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_REPLAY` | a1: int, a2: int, a3: string | `int` |
| `HUD.END_TEXT_COMMAND_THEFEED_POST_REPLAY_INPUT` | a1: int, a2: string, a3: string | `int` |
| `HUD.BEGIN_TEXT_COMMAND_PRINT` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_PRINT` | a: int, b: bool | `void` |
| `HUD.BEGIN_TEXT_COMMAND_IS_MESSAGE_DISPLAYED` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_IS_MESSAGE_DISPLAYED` | — | `bool` |
| `HUD.BEGIN_TEXT_COMMAND_DISPLAY_TEXT` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_DISPLAY_TEXT` | a1: float, a2: float, a3: int | `void` |
| `HUD.BEGIN_TEXT_COMMAND_GET_SCREEN_WIDTH_OF_DISPLAY_TEXT` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_GET_SCREEN_WIDTH_OF_DISPLAY_TEXT` | a: bool | `float` |
| `HUD.BEGIN_TEXT_COMMAND_GET_NUMBER_OF_LINES_FOR_STRING` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_GET_NUMBER_OF_LINES_FOR_STRING` | a: float, b: float | `int` |
| `HUD.BEGIN_TEXT_COMMAND_DISPLAY_HELP` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_DISPLAY_HELP` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `HUD.BEGIN_TEXT_COMMAND_IS_THIS_HELP_MESSAGE_BEING_DISPLAYED` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_IS_THIS_HELP_MESSAGE_BEING_DISPLAYED` | a: int | `bool` |
| `HUD.BEGIN_TEXT_COMMAND_SET_BLIP_NAME` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_SET_BLIP_NAME` | a: int | `void` |
| `HUD.BEGIN_TEXT_COMMAND_ADD_DIRECTLY_TO_PREVIOUS_BRIEFS` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_ADD_DIRECTLY_TO_PREVIOUS_BRIEFS` | a: bool | `void` |
| `HUD.BEGIN_TEXT_COMMAND_CLEAR_PRINT` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_CLEAR_PRINT` | — | `void` |
| `HUD.BEGIN_TEXT_COMMAND_OVERRIDE_BUTTON_TEXT` | a: string | `void` |
| `HUD.END_TEXT_COMMAND_OVERRIDE_BUTTON_TEXT` | a: int | `void` |
| `HUD.ADD_TEXT_COMPONENT_INTEGER` | a: int | `void` |
| `HUD.ADD_TEXT_COMPONENT_FLOAT` | a: float, b: int | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_TEXT_LABEL` | a: string | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_TEXT_LABEL_HASH_KEY` | a: hash | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_BLIP_NAME` | a: int | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_PLAYER_NAME` | a: string | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_TIME` | a: int, b: int | `void` |
| `HUD.ADD_TEXT_COMPONENT_FORMATTED_INTEGER` | a: int, b: bool | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_PHONE_NUMBER` | a: string, b: int | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_WEBSITE` | a: string | `void` |
| `HUD.ADD_TEXT_COMPONENT_SUBSTRING_KEYBOARD_DISPLAY` | a: string | `void` |
| `HUD.SET_COLOUR_OF_NEXT_TEXT_COMPONENT` | a: int | `void` |
| `HUD.GET_CHARACTER_FROM_AUDIO_CONVERSATION_FILENAME` | a1: string, a2: int, a3: int | `string` |
| `HUD.GET_CHARACTER_FROM_AUDIO_CONVERSATION_FILENAME_WITH_BYTE_LIMIT` | a1: string, a2: int, a3: int, a4: int | `string` |
| `HUD.GET_CHARACTER_FROM_AUDIO_CONVERSATION_FILENAME_BYTES` | a1: string, a2: int, a3: int | `string` |
| `HUD.GET_FILENAME_FOR_AUDIO_CONVERSATION` | a: string | `string` |
| `HUD.CLEAR_PRINTS` | — | `void` |
| `HUD.CLEAR_BRIEF` | — | `void` |
| `HUD.CLEAR_ALL_HELP_MESSAGES` | — | `void` |
| `HUD.CLEAR_THIS_PRINT` | a: string | `void` |
| `HUD.CLEAR_SMALL_PRINTS` | — | `void` |
| `HUD.DOES_TEXT_BLOCK_EXIST` | a: string | `bool` |
| `HUD.REQUEST_ADDITIONAL_TEXT` | a: string, b: int | `void` |
| `HUD.REQUEST_ADDITIONAL_TEXT_FOR_DLC` | a: string, b: int | `void` |
| `HUD.HAS_ADDITIONAL_TEXT_LOADED` | a: int | `bool` |
| `HUD.CLEAR_ADDITIONAL_TEXT` | a: int, b: bool | `void` |
| `HUD.IS_STREAMING_ADDITIONAL_TEXT` | a: int | `bool` |
| `HUD.HAS_THIS_ADDITIONAL_TEXT_LOADED` | a: string, b: int | `bool` |
| `HUD.IS_MESSAGE_BEING_DISPLAYED` | — | `bool` |
| `HUD.DOES_TEXT_LABEL_EXIST` | a: string | `bool` |
| `HUD.GET_FIRST_N_CHARACTERS_OF_LITERAL_STRING` | a: string, b: int | `string` |
| `HUD.GET_LENGTH_OF_STRING_WITH_THIS_TEXT_LABEL` | a: string | `int` |
| `HUD.GET_LENGTH_OF_LITERAL_STRING` | a: string | `int` |
| `HUD.GET_LENGTH_OF_LITERAL_STRING_IN_BYTES` | a: string | `int` |
| `HUD.GET_STREET_NAME_FROM_HASH_KEY` | a: hash | `string` |
| `HUD.IS_HUD_PREFERENCE_SWITCHED_ON` | — | `bool` |
| `HUD.IS_RADAR_PREFERENCE_SWITCHED_ON` | — | `bool` |
| `HUD.IS_SUBTITLE_PREFERENCE_SWITCHED_ON` | — | `bool` |
| `HUD.DISPLAY_HUD` | a: bool | `void` |
| `HUD.DISPLAY_HUD_WHEN_NOT_IN_STATE_OF_PLAY_THIS_FRAME` | — | `void` |
| `HUD.DISPLAY_HUD_WHEN_PAUSED_THIS_FRAME` | — | `void` |
| `HUD.DISPLAY_RADAR` | a: bool | `void` |
| `HUD.SET_FAKE_SPECTATOR_MODE` | a: bool | `void` |
| `HUD.GET_FAKE_SPECTATOR_MODE` | — | `bool` |
| `HUD.IS_HUD_HIDDEN` | — | `bool` |
| `HUD.IS_RADAR_HIDDEN` | — | `bool` |
| `HUD.IS_MINIMAP_RENDERING` | — | `bool` |
| `HUD.USE_VEHICLE_TARGETING_RETICULE` | a: int | `void` |
| `HUD._USE_VEHICLE_TARGETING_RETICULE_ON_VEHICLES` | a: bool | `void` |
| `HUD.ADD_VALID_VEHICLE_HIT_HASH` | a: int | `void` |
| `HUD.CLEAR_VALID_VEHICLE_HIT_HASHES` | — | `void` |
| `HUD.SET_BLIP_ROUTE` | a: int, b: bool | `void` |
| `HUD.CLEAR_ALL_BLIP_ROUTES` | — | `void` |
| `HUD.SET_BLIP_ROUTE_COLOUR` | a: int, b: int | `void` |
| `HUD.SET_FORCE_SHOW_GPS` | a: bool | `void` |
| `HUD.SET_USE_SET_DESTINATION_IN_PAUSE_MAP` | a: bool | `void` |
| `HUD.SET_BLOCK_WANTED_FLASH` | a: bool | `void` |
| `HUD.ADD_NEXT_MESSAGE_TO_PREVIOUS_BRIEFS` | a: bool | `void` |
| `HUD.FORCE_NEXT_MESSAGE_TO_PREVIOUS_BRIEFS_LIST` | a: int | `void` |
| `HUD.SET_RADAR_ZOOM_PRECISE` | a: float | `void` |
| `HUD.SET_RADAR_ZOOM` | a: int | `void` |
| `HUD.SET_RADAR_ZOOM_TO_BLIP` | a: int, b: float | `void` |
| `HUD.SET_RADAR_ZOOM_TO_DISTANCE` | a: float | `void` |
| `HUD.UPDATE_RADAR_ZOOM_TO_BLIP` | — | `void` |
| `HUD.GET_HUD_COLOUR` | a1: int, a2: pointer, a3: pointer, a4: pointer, a5: pointer | `void` |
| `HUD.SET_SCRIPT_VARIABLE_HUD_COLOUR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `HUD.SET_SECOND_SCRIPT_VARIABLE_HUD_COLOUR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `HUD.REPLACE_HUD_COLOUR` | a: int, b: int | `void` |
| `HUD.REPLACE_HUD_COLOUR_WITH_RGBA` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `HUD.SET_ABILITY_BAR_VISIBILITY` | a: bool | `void` |
| `HUD.SET_ALLOW_ABILITY_BAR` | a: bool | `void` |
| `HUD.FLASH_ABILITY_BAR` | a: int | `void` |
| `HUD.SET_ABILITY_BAR_VALUE` | a: float, b: float | `void` |
| `HUD.FLASH_WANTED_DISPLAY` | a: bool | `void` |
| `HUD.FORCE_OFF_WANTED_STAR_FLASH` | a: bool | `void` |
| `HUD.SET_CUSTOM_MP_HUD_COLOR` | a: int | `void` |
| `HUD.GET_RENDERED_CHARACTER_HEIGHT` | a: float, b: int | `float` |
| `HUD.SET_TEXT_SCALE` | a: float, b: float | `void` |
| `HUD.SET_TEXT_COLOUR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `HUD.SET_TEXT_CENTRE` | a: bool | `void` |
| `HUD.SET_TEXT_RIGHT_JUSTIFY` | a: bool | `void` |
| `HUD.SET_TEXT_JUSTIFICATION` | a: int | `void` |
| `HUD.SET_TEXT_LINE_HEIGHT_MULT` | a: float | `void` |
| `HUD.SET_TEXT_WRAP` | a: float, b: float | `void` |
| `HUD.SET_TEXT_LEADING` | a: int | `void` |
| `HUD.SET_TEXT_PROPORTIONAL` | a: bool | `void` |
| `HUD.SET_TEXT_FONT` | a: int | `void` |
| `HUD.SET_TEXT_DROP_SHADOW` | — | `void` |
| `HUD.SET_TEXT_DROPSHADOW` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `HUD.SET_TEXT_OUTLINE` | — | `void` |
| `HUD.SET_TEXT_EDGE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `HUD.SET_TEXT_RENDER_ID` | a: int | `void` |
| `HUD.GET_DEFAULT_SCRIPT_RENDERTARGET_RENDER_ID` | — | `int` |
| `HUD.REGISTER_NAMED_RENDERTARGET` | a: string, b: bool | `bool` |
| `HUD.IS_NAMED_RENDERTARGET_REGISTERED` | a: string | `bool` |
| `HUD.RELEASE_NAMED_RENDERTARGET` | a: string | `bool` |
| `HUD.LINK_NAMED_RENDERTARGET` | a: hash | `void` |
| `HUD.GET_NAMED_RENDERTARGET_RENDER_ID` | a: string | `int` |
| `HUD.IS_NAMED_RENDERTARGET_LINKED` | a: hash | `bool` |
| `HUD.CLEAR_HELP` | a: bool | `void` |
| `HUD.IS_HELP_MESSAGE_ON_SCREEN` | — | `bool` |
| `HUD.HAS_SCRIPT_HIDDEN_HELP_THIS_FRAME` | — | `bool` |
| `HUD.IS_HELP_MESSAGE_BEING_DISPLAYED` | — | `bool` |
| `HUD.IS_HELP_MESSAGE_FADING_OUT` | — | `bool` |
| `HUD.SET_HELP_MESSAGE_STYLE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `HUD.GET_STANDARD_BLIP_ENUM_ID` | — | `int` |
| `HUD.GET_WAYPOINT_BLIP_ENUM_ID` | — | `int` |
| `HUD.GET_NUMBER_OF_ACTIVE_BLIPS` | — | `int` |
| `HUD.GET_NEXT_BLIP_INFO_ID` | a: int | `int` |
| `HUD.GET_FIRST_BLIP_INFO_ID` | a: int | `int` |
| `HUD.GET_CLOSEST_BLIP_INFO_ID` | a: int | `int` |
| `HUD.GET_BLIP_INFO_ID_COORD` | a: int | `vector3` |
| `HUD.GET_BLIP_INFO_ID_DISPLAY` | a: int | `int` |
| `HUD.GET_BLIP_INFO_ID_TYPE` | a: int | `int` |
| `HUD.GET_BLIP_INFO_ID_ENTITY_INDEX` | a: int | `int` |
| `HUD.GET_BLIP_INFO_ID_PICKUP_INDEX` | a: int | `int` |
| `HUD.GET_BLIP_FROM_ENTITY` | a: int | `int` |
| `HUD.ADD_BLIP_FOR_RADIUS` | a1: float, a2: float, a3: float, a4: float | `int` |
| `HUD.ADD_BLIP_FOR_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float | `int` |
| `HUD.ADD_BLIP_FOR_ENTITY` | a: int | `int` |
| `HUD.ADD_BLIP_FOR_PICKUP` | a: int | `int` |
| `HUD.ADD_BLIP_FOR_COORD` | a1: float, a2: float, a3: float | `int` |
| `HUD.TRIGGER_SONAR_BLIP` | a1: float, a2: float, a3: float, a4: float, a5: int | `void` |
| `HUD.ALLOW_SONAR_BLIPS` | a: bool | `void` |
| `HUD.SET_BLIP_COORDS` | a1: int, a2: float, a3: float, a4: float | `void` |
| `HUD.GET_BLIP_COORDS` | a: int | `vector3` |
| `HUD.SET_BLIP_SPRITE` | a: int, b: int | `void` |
| `HUD.GET_BLIP_SPRITE` | a: int | `int` |
| `HUD.SET_COP_BLIP_SPRITE` | a: int, b: float | `void` |
| `HUD.SET_COP_BLIP_SPRITE_AS_STANDARD` | — | `void` |
| `HUD.SET_BLIP_NAME_FROM_TEXT_FILE` | a: int, b: string | `void` |
| `HUD.SET_BLIP_NAME_TO_PLAYER_NAME` | a: int, b: int | `void` |
| `HUD.SET_BLIP_ALPHA` | a: int, b: int | `void` |
| `HUD.GET_BLIP_ALPHA` | a: int | `int` |
| `HUD.SET_BLIP_FADE` | a1: int, a2: int, a3: int | `void` |
| `HUD.GET_BLIP_FADE_DIRECTION` | a: int | `int` |
| `HUD.SET_BLIP_ROTATION` | a: int, b: int | `void` |
| `HUD.SET_BLIP_ROTATION_WITH_FLOAT` | a: int, b: float | `void` |
| `HUD.GET_BLIP_ROTATION` | a: int | `int` |
| `HUD.SET_BLIP_FLASH_TIMER` | a: int, b: int | `void` |
| `HUD.SET_BLIP_FLASH_INTERVAL` | a: int, b: int | `void` |
| `HUD.SET_BLIP_COLOUR` | a: int, b: int | `void` |
| `HUD.SET_BLIP_SECONDARY_COLOUR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `HUD.GET_BLIP_COLOUR` | a: int | `int` |
| `HUD.GET_BLIP_HUD_COLOUR` | a: int | `int` |
| `HUD.IS_BLIP_SHORT_RANGE` | a: int | `bool` |
| `HUD.IS_BLIP_ON_MINIMAP` | a: int | `bool` |
| `HUD.DOES_BLIP_HAVE_GPS_ROUTE` | a: int | `bool` |
| `HUD.SET_BLIP_HIDDEN_ON_LEGEND` | a: int, b: bool | `void` |
| `HUD.SET_BLIP_HIGH_DETAIL` | a: int, b: bool | `void` |
| `HUD.SET_BLIP_AS_MISSION_CREATOR_BLIP` | a: int, b: bool | `void` |
| `HUD.IS_MISSION_CREATOR_BLIP` | a: int | `bool` |
| `HUD.GET_NEW_SELECTED_MISSION_CREATOR_BLIP` | — | `int` |
| `HUD.IS_HOVERING_OVER_MISSION_CREATOR_BLIP` | — | `bool` |
| `HUD.SHOW_START_MISSION_INSTRUCTIONAL_BUTTON` | a: bool | `void` |
| `HUD.SHOW_CONTACT_INSTRUCTIONAL_BUTTON` | a: bool | `void` |
| `HUD._SHOW_PURCHASE_INSTRUCTIONAL_BUTTON` | a: bool | `void` |
| `HUD.RELOAD_MAP_MENU` | — | `void` |
| `HUD.SET_BLIP_MARKER_LONG_DISTANCE` | a: int, b: int | `void` |
| `HUD.SET_BLIP_FLASHES` | a: int, b: bool | `void` |
| `HUD.SET_BLIP_FLASHES_ALTERNATE` | a: int, b: bool | `void` |
| `HUD.IS_BLIP_FLASHING` | a: int | `bool` |
| `HUD.SET_BLIP_AS_SHORT_RANGE` | a: int, b: bool | `void` |
| `HUD.SET_BLIP_SCALE` | a: int, b: float | `void` |
| `HUD.SET_BLIP_SCALE_2D` | a1: int, a2: float, a3: float | `void` |
| `HUD.SET_BLIP_PRIORITY` | a: int, b: int | `void` |
| `HUD.SET_BLIP_DISPLAY` | a: int, b: int | `void` |
| `HUD.SET_BLIP_CATEGORY` | a: int, b: int | `void` |
| `HUD.REMOVE_BLIP` | a: pointer | `void` |
| `HUD.SET_BLIP_AS_FRIENDLY` | a: int, b: bool | `void` |
| `HUD.PULSE_BLIP` | a: int | `void` |
| `HUD.SHOW_NUMBER_ON_BLIP` | a: int, b: int | `void` |
| `HUD.HIDE_NUMBER_ON_BLIP` | a: int | `void` |
| `HUD.SHOW_HEIGHT_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SHOW_TICK_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SHOW_GOLD_TICK_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SHOW_FOR_SALE_ICON_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SHOW_HEADING_INDICATOR_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SHOW_OUTLINE_INDICATOR_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SHOW_FRIEND_INDICATOR_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SHOW_CREW_INDICATOR_ON_BLIP` | a: int, b: bool | `void` |
| `HUD.SET_BLIP_EXTENDED_HEIGHT_THRESHOLD` | a: int, b: bool | `void` |
| `HUD.SET_BLIP_SHORT_HEIGHT_THRESHOLD` | a: int, b: int | `void` |
| `HUD.SET_BLIP_USE_HEIGHT_INDICATOR_ON_EDGE` | a: int, b: int | `void` |
| `HUD.SET_BLIP_AS_MINIMAL_ON_EDGE` | a: int, b: bool | `void` |
| `HUD.SET_RADIUS_BLIP_EDGE` | a: int, b: bool | `void` |
| `HUD.DOES_BLIP_EXIST` | a: int | `bool` |
| `HUD.SET_WAYPOINT_OFF` | — | `void` |
| `HUD.DELETE_WAYPOINTS_FROM_THIS_PLAYER` | — | `void` |
| `HUD.REFRESH_WAYPOINT` | — | `void` |
| `HUD.IS_WAYPOINT_ACTIVE` | — | `bool` |
| `HUD.SET_NEW_WAYPOINT` | a: float, b: float | `void` |
| `HUD.GET_WAYPOINT_CLEAR_ON_ARRIVAL_MODE` | — | `int` |
| `HUD.SET_WAYPOINT_CLEAR_ON_ARRIVAL_MODE` | a: int | `void` |
| `HUD.SET_BLIP_BRIGHT` | a: int, b: bool | `void` |
| `HUD.SET_BLIP_SHOW_CONE` | a1: int, a2: bool, a3: int | `void` |
| `HUD.REMOVE_COP_BLIP_FROM_PED` | a: int | `void` |
| `HUD.SETUP_FAKE_CONE_DATA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int | `void` |
| `HUD.REMOVE_FAKE_CONE_DATA` | a: int | `void` |
| `HUD.CLEAR_FAKE_CONE_ARRAY` | — | `void` |
| `HUD._SET_BLIP_GPS_ROUTE_DISPLAY_DISTANCE` | a1: int, a2: int, a3: bool | `void` |
| `HUD.SET_MINIMAP_COMPONENT` | a1: int, a2: bool, a3: int | `bool` |
| `HUD.SET_MINIMAP_SONAR_SWEEP` | a: bool | `void` |
| `HUD.SHOW_ACCOUNT_PICKER` | — | `void` |
| `HUD.GET_MAIN_PLAYER_BLIP_ID` | — | `int` |
| `HUD.SET_PM_WARNINGSCREEN_ACTIVE` | a: bool | `void` |
| `HUD.HIDE_LOADING_ON_FADE_THIS_FRAME` | — | `void` |
| `HUD.SET_RADAR_AS_INTERIOR_THIS_FRAME` | a1: hash, a2: float, a3: float, a4: int, a5: int | `void` |
| `HUD.SET_INSIDE_VERY_SMALL_INTERIOR` | a: bool | `void` |
| `HUD.SET_INSIDE_VERY_LARGE_INTERIOR` | a: bool | `void` |
| `HUD.SET_RADAR_AS_EXTERIOR_THIS_FRAME` | — | `void` |
| `HUD.SET_FAKE_PAUSEMAP_PLAYER_POSITION_THIS_FRAME` | a: float, b: float | `void` |
| `HUD.SET_FAKE_GPS_PLAYER_POSITION_THIS_FRAME` | a1: float, a2: float, a3: float | `void` |
| `HUD.IS_PAUSEMAP_IN_INTERIOR_MODE` | — | `bool` |
| `HUD.HIDE_MINIMAP_EXTERIOR_MAP_THIS_FRAME` | — | `void` |
| `HUD.HIDE_MINIMAP_INTERIOR_MAP_THIS_FRAME` | — | `void` |
| `HUD.SET_USE_ISLAND_MAP` | a: bool | `void` |
| `HUD._SET_PAUSE_EXTERIOR_RENDERING_WHILE_IN_INTERIOR` | — | `void` |
| `HUD.DONT_TILT_MINIMAP_THIS_FRAME` | — | `void` |
| `HUD.DONT_ZOOM_MINIMAP_WHEN_RUNNING_THIS_FRAME` | — | `void` |
| `HUD.DONT_ZOOM_MINIMAP_WHEN_SNIPING_THIS_FRAME` | — | `void` |
| `HUD.SET_WIDESCREEN_FORMAT` | a: int | `void` |
| `HUD.DISPLAY_AREA_NAME` | a: bool | `void` |
| `HUD.DISPLAY_CASH` | a: bool | `void` |
| `HUD.USE_FAKE_MP_CASH` | a: bool | `void` |
| `HUD.CHANGE_FAKE_MP_CASH` | a: int, b: int | `void` |
| `HUD.DISPLAY_AMMO_THIS_FRAME` | a: bool | `void` |
| `HUD.DISPLAY_SNIPER_SCOPE_THIS_FRAME` | — | `void` |
| `HUD.HIDE_HUD_AND_RADAR_THIS_FRAME` | — | `void` |
| `HUD.ALLOW_DISPLAY_OF_MULTIPLAYER_CASH_TEXT` | a: bool | `void` |
| `HUD.SET_MULTIPLAYER_WALLET_CASH` | — | `void` |
| `HUD.REMOVE_MULTIPLAYER_WALLET_CASH` | — | `void` |
| `HUD.SET_MULTIPLAYER_BANK_CASH` | — | `void` |
| `HUD.REMOVE_MULTIPLAYER_BANK_CASH` | — | `void` |
| `HUD.SET_MULTIPLAYER_HUD_CASH` | a: int, b: bool | `void` |
| `HUD.REMOVE_MULTIPLAYER_HUD_CASH` | — | `void` |
| `HUD.HIDE_HELP_TEXT_THIS_FRAME` | — | `void` |
| `HUD.IS_IME_IN_PROGRESS` | — | `bool` |
| `HUD.DISPLAY_HELP_TEXT_THIS_FRAME` | a: string, b: bool | `void` |
| `HUD.HUD_FORCE_WEAPON_WHEEL` | a: bool | `void` |
| `HUD.HUD_FORCE_SPECIAL_VEHICLE_WEAPON_WHEEL` | — | `void` |
| `HUD.HUD_SUPPRESS_WEAPON_WHEEL_RESULTS_THIS_FRAME` | — | `void` |
| `HUD.HUD_GET_WEAPON_WHEEL_CURRENTLY_HIGHLIGHTED` | — | `hash` |
| `HUD.HUD_SET_WEAPON_WHEEL_TOP_SLOT` | a: hash | `void` |
| `HUD.HUD_GET_WEAPON_WHEEL_TOP_SLOT` | a: int | `hash` |
| `HUD.HUD_SHOWING_CHARACTER_SWITCH_SELECTION` | a: bool | `void` |
| `HUD.SET_GPS_FLAGS` | a: int, b: float | `void` |
| `HUD.CLEAR_GPS_FLAGS` | — | `void` |
| `HUD.SET_RACE_TRACK_RENDER` | a: bool | `void` |
| `HUD.CLEAR_GPS_RACE_TRACK` | — | `void` |
| `HUD.START_GPS_CUSTOM_ROUTE` | a1: int, a2: bool, a3: bool | `void` |
| `HUD.ADD_POINT_TO_GPS_CUSTOM_ROUTE` | a1: float, a2: float, a3: float | `void` |
| `HUD.SET_GPS_CUSTOM_ROUTE_RENDER` | a1: bool, a2: int, a3: int | `void` |
| `HUD.CLEAR_GPS_CUSTOM_ROUTE` | — | `void` |
| `HUD.START_GPS_MULTI_ROUTE` | a1: int, a2: bool, a3: bool | `void` |
| `HUD.ADD_POINT_TO_GPS_MULTI_ROUTE` | a1: float, a2: float, a3: float | `void` |
| `HUD.SET_GPS_MULTI_ROUTE_RENDER` | a: bool | `void` |
| `HUD.CLEAR_GPS_MULTI_ROUTE` | — | `void` |
| `HUD.CLEAR_GPS_PLAYER_WAYPOINT` | — | `void` |
| `HUD.SET_GPS_FLASHES` | a: bool | `void` |
| `HUD.SET_PLAYER_ICON_COLOUR` | a: int | `void` |
| `HUD.FLASH_MINIMAP_DISPLAY` | — | `void` |
| `HUD.FLASH_MINIMAP_DISPLAY_WITH_COLOR` | a: int | `void` |
| `HUD.TOGGLE_STEALTH_RADAR` | a: bool | `void` |
| `HUD.SET_MINIMAP_IN_SPECTATOR_MODE` | a: bool, b: int | `void` |
| `HUD.SET_MISSION_NAME` | a: bool, b: string | `void` |
| `HUD.SET_MISSION_NAME_FOR_UGC_MISSION` | a: bool, b: string | `void` |
| `HUD.SET_DESCRIPTION_FOR_UGC_MISSION_EIGHT_STRINGS` | a1: bool, a2: string, a3: string, a4: string, a5: string, a6: string, a7: string, a8: string, a9: string | `void` |
| `HUD.SET_MINIMAP_BLOCK_WAYPOINT` | a: bool | `void` |
| `HUD.SET_MINIMAP_IN_PROLOGUE` | a: bool | `void` |
| `HUD.SET_MINIMAP_BACKGROUND_HIDDEN` | a: bool | `void` |
| `HUD.SET_MINIMAP_HIDE_FOW` | a: bool | `void` |
| `HUD.GET_MINIMAP_FOW_DISCOVERY_RATIO` | — | `float` |
| `HUD.GET_MINIMAP_FOW_COORDINATE_IS_REVEALED` | a1: float, a2: float, a3: float | `bool` |
| `HUD.SET_MINIMAP_FOW_DO_NOT_UPDATE` | a: bool | `void` |
| `HUD.SET_MINIMAP_FOW_REVEAL_COORDINATE` | a1: float, a2: float, a3: float | `void` |
| `HUD.SET_MINIMAP_GOLF_COURSE` | a: int | `void` |
| `HUD.SET_MINIMAP_GOLF_COURSE_OFF` | — | `void` |
| `HUD.LOCK_MINIMAP_ANGLE` | a: int | `void` |
| `HUD.UNLOCK_MINIMAP_ANGLE` | — | `void` |
| `HUD.LOCK_MINIMAP_POSITION` | a: float, b: float | `void` |
| `HUD.UNLOCK_MINIMAP_POSITION` | — | `void` |
| `HUD.SET_FAKE_MINIMAP_MAX_ALTIMETER_HEIGHT` | a1: float, a2: bool, a3: int | `void` |
| `HUD.SET_HEALTH_HUD_DISPLAY_VALUES` | a1: int, a2: int, a3: bool | `void` |
| `HUD.SET_MAX_HEALTH_HUD_DISPLAY` | a: int | `void` |
| `HUD.SET_MAX_ARMOUR_HUD_DISPLAY` | a: int | `void` |
| `HUD.SET_BIGMAP_ACTIVE` | a: bool, b: bool | `void` |
| `HUD.IS_HUD_COMPONENT_ACTIVE` | a: int | `bool` |
| `HUD.IS_SCRIPTED_HUD_COMPONENT_ACTIVE` | a: int | `bool` |
| `HUD.HIDE_SCRIPTED_HUD_COMPONENT_THIS_FRAME` | a: int | `void` |
| `HUD.SHOW_SCRIPTED_HUD_COMPONENT_THIS_FRAME` | a: int | `void` |
| `HUD.IS_SCRIPTED_HUD_COMPONENT_HIDDEN_THIS_FRAME` | a: int | `bool` |
| `HUD.HIDE_HUD_COMPONENT_THIS_FRAME` | a: int | `void` |
| `HUD.IS_HUD_COMPONENT_HIDDEN_THIS_FRAME` | a: int | `bool` |
| `HUD.SHOW_HUD_COMPONENT_THIS_FRAME` | a: int | `void` |
| `HUD.HIDE_STREET_AND_CAR_NAMES_THIS_FRAME` | — | `void` |
| `HUD.RESET_RETICULE_VALUES` | — | `void` |
| `HUD.RESET_HUD_COMPONENT_VALUES` | a: int | `void` |
| `HUD.SET_HUD_COMPONENT_POSITION` | a1: int, a2: float, a3: float | `void` |
| `HUD.GET_HUD_COMPONENT_POSITION` | a: int | `vector3` |
| `HUD.CLEAR_REMINDER_MESSAGE` | — | `void` |
| `HUD.GET_HUD_SCREEN_POSITION_FROM_WORLD_POSITION` | a1: float, a2: float, a3: float, a4: pointer, a5: pointer | `int` |
| `HUD.OPEN_REPORTUGC_MENU` | — | `void` |
| `HUD.FORCE_CLOSE_REPORTUGC_MENU` | — | `void` |
| `HUD.IS_REPORTUGC_MENU_OPEN` | — | `bool` |
| `HUD.IS_FLOATING_HELP_TEXT_ON_SCREEN` | a: int | `bool` |
| `HUD.SET_FLOATING_HELP_TEXT_SCREEN_POSITION` | a1: int, a2: float, a3: float | `void` |
| `HUD.SET_FLOATING_HELP_TEXT_WORLD_POSITION` | a1: int, a2: float, a3: float, a4: float | `void` |
| `HUD.SET_FLOATING_HELP_TEXT_TO_ENTITY` | a1: int, a2: int, a3: float, a4: float | `void` |
| `HUD.SET_FLOATING_HELP_TEXT_STYLE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `HUD.CLEAR_FLOATING_HELP` | a: int, b: bool | `void` |
| `HUD.CREATE_MP_GAMER_TAG_WITH_CREW_COLOR` | a1: int, a2: string, a3: bool, a4: bool, a5: string, a6: int, a7: int, a8: int, a9: int | `void` |
| `HUD.IS_MP_GAMER_TAG_MOVIE_ACTIVE` | — | `bool` |
| `HUD.CREATE_FAKE_MP_GAMER_TAG` | a1: int, a2: string, a3: bool, a4: bool, a5: string, a6: int | `int` |
| `HUD.REMOVE_MP_GAMER_TAG` | a: int | `void` |
| `HUD.IS_MP_GAMER_TAG_ACTIVE` | a: int | `bool` |
| `HUD.IS_MP_GAMER_TAG_FREE` | a: int | `bool` |
| `HUD.SET_MP_GAMER_TAG_VISIBILITY` | a1: int, a2: int, a3: bool, a4: int | `void` |
| `HUD.SET_ALL_MP_GAMER_TAGS_VISIBILITY` | a: int, b: bool | `void` |
| `HUD.SET_MP_GAMER_TAGS_SHOULD_USE_VEHICLE_HEALTH` | a: int, b: bool | `void` |
| `HUD.SET_MP_GAMER_TAGS_SHOULD_USE_POINTS_HEALTH` | a: int, b: bool | `void` |
| `HUD.SET_MP_GAMER_TAGS_POINT_HEALTH` | a1: int, a2: int, a3: int | `void` |
| `HUD.SET_MP_GAMER_TAG_COLOUR` | a1: int, a2: int, a3: int | `void` |
| `HUD.SET_MP_GAMER_TAG_HEALTH_BAR_COLOUR` | a: int, b: int | `void` |
| `HUD.SET_MP_GAMER_TAG_ALPHA` | a1: int, a2: int, a3: int | `void` |
| `HUD.SET_MP_GAMER_TAG_WANTED_LEVEL` | a: int, b: int | `void` |
| `HUD.SET_MP_GAMER_TAG_NUM_PACKAGES` | a: int, b: int | `void` |
| `HUD.SET_MP_GAMER_TAG_NAME` | a: int, b: string | `void` |
| `HUD.IS_UPDATING_MP_GAMER_TAG_NAME_AND_CREW_DETAILS` | a: int | `bool` |
| `HUD.SET_MP_GAMER_TAG_BIG_TEXT` | a: int, b: string | `void` |
| `HUD.GET_CURRENT_WEBPAGE_ID` | — | `int` |
| `HUD.GET_CURRENT_WEBSITE_ID` | — | `int` |
| `HUD.GET_GLOBAL_ACTIONSCRIPT_FLAG` | a: int | `int` |
| `HUD.RESET_GLOBAL_ACTIONSCRIPT_FLAG` | a: int | `void` |
| `HUD.IS_WARNING_MESSAGE_READY_FOR_CONTROL` | — | `bool` |
| `HUD.SET_WARNING_MESSAGE` | a1: string, a2: int, a3: string, a4: bool, a5: int, a6: string, a7: string, a8: bool, a9: int | `void` |
| `HUD.SET_WARNING_MESSAGE_WITH_HEADER` | a1: string, a2: string, a3: int, a4: string, a5: bool, a6: int, a7: pointer, a8: pointer, a9: bool, a10: int | `void` |
| `HUD.SET_WARNING_MESSAGE_WITH_HEADER_AND_SUBSTRING_FLAGS` | a1: string, a2: string, a3: int, a4: string, a5: bool, a6: int, a7: int, a8: string, a9: string, a10: bool, a11: int | `void` |
| `HUD.SET_WARNING_MESSAGE_WITH_HEADER_EXTENDED` | a1: string, a2: string, a3: int, a4: string, a5: bool, a6: int, a7: pointer, a8: pointer, a9: bool, a10: int, a11: int | `void` |
| `HUD.SET_WARNING_MESSAGE_WITH_HEADER_AND_SUBSTRING_FLAGS_EXTENDED` | a1: string, a2: string, a3: int, a4: int, a5: string, a6: bool, a7: int, a8: int, a9: string, a10: string, a11: bool, a12: int | `void` |
| `HUD.GET_WARNING_SCREEN_MESSAGE_HASH` | — | `hash` |
| `HUD.SET_WARNING_MESSAGE_OPTION_ITEMS` | a1: int, a2: string, a3: int, a4: int, a5: int, a6: int | `bool` |
| `HUD.SET_WARNING_MESSAGE_OPTION_HIGHLIGHT` | a: int | `bool` |
| `HUD.REMOVE_WARNING_MESSAGE_OPTION_ITEMS` | — | `void` |
| `HUD.IS_WARNING_MESSAGE_ACTIVE` | — | `bool` |
| `HUD.CLEAR_DYNAMIC_PAUSE_MENU_ERROR_MESSAGE` | — | `void` |
| `HUD.CUSTOM_MINIMAP_SET_ACTIVE` | a: bool | `void` |
| `HUD.CUSTOM_MINIMAP_SET_BLIP_OBJECT` | a: int | `void` |
| `HUD.CUSTOM_MINIMAP_CREATE_BLIP` | a1: float, a2: float, a3: float | `int` |
| `HUD.CUSTOM_MINIMAP_CLEAR_BLIPS` | — | `void` |
| `HUD.FORCE_SONAR_BLIPS_THIS_FRAME` | — | `bool` |
| `HUD.GET_NORTH_BLID_INDEX` | — | `int` |
| `HUD.DISPLAY_PLAYER_NAME_TAGS_ON_BLIPS` | a: bool | `void` |
| `HUD.DRAW_FRONTEND_BACKGROUND_THIS_FRAME` | — | `void` |
| `HUD.DRAW_HUD_OVER_FADE_THIS_FRAME` | — | `void` |
| `HUD.ACTIVATE_FRONTEND_MENU` | a1: hash, a2: bool, a3: int | `void` |
| `HUD.RESTART_FRONTEND_MENU` | a: hash, b: int | `void` |
| `HUD.GET_CURRENT_FRONTEND_MENU_VERSION` | — | `hash` |
| `HUD.SET_PAUSE_MENU_ACTIVE` | a: bool | `void` |
| `HUD.DISABLE_FRONTEND_THIS_FRAME` | — | `void` |
| `HUD.SUPPRESS_FRONTEND_RENDERING_THIS_FRAME` | — | `void` |
| `HUD.ALLOW_PAUSE_WHEN_NOT_IN_STATE_OF_PLAY_THIS_FRAME` | — | `void` |
| `HUD.SET_FRONTEND_ACTIVE` | a: bool | `void` |
| `HUD.IS_PAUSE_MENU_ACTIVE` | — | `bool` |
| `HUD.IS_STORE_PENDING_NETWORK_SHUTDOWN_TO_OPEN` | — | `bool` |
| `HUD.GET_PAUSE_MENU_STATE` | — | `int` |
| `HUD.GET_PAUSE_MENU_POSITION` | — | `vector3` |
| `HUD.IS_PAUSE_MENU_RESTARTING` | — | `bool` |
| `HUD.FORCE_SCRIPTED_GFX_WHEN_FRONTEND_ACTIVE` | a: string | `void` |
| `HUD.PAUSE_MENUCEPTION_GO_DEEPER` | a: int | `void` |
| `HUD.PAUSE_MENUCEPTION_THE_KICK` | — | `void` |
| `HUD.PAUSE_TOGGLE_FULLSCREEN_MAP` | a: int | `void` |
| `HUD.PAUSE_MENU_ACTIVATE_CONTEXT` | a: hash | `void` |
| `HUD.PAUSE_MENU_DEACTIVATE_CONTEXT` | a: hash | `void` |
| `HUD.PAUSE_MENU_IS_CONTEXT_ACTIVE` | a: hash | `bool` |
| `HUD.PAUSE_MENU_IS_CONTEXT_MENU_ACTIVE` | — | `bool` |
| `HUD.PAUSE_MENU_GET_HAIR_COLOUR_INDEX` | — | `int` |
| `HUD.PAUSE_MENU_GET_MOUSE_HOVER_INDEX` | — | `int` |
| `HUD.PAUSE_MENU_GET_MOUSE_HOVER_UNIQUE_ID` | — | `int` |
| `HUD.PAUSE_MENU_GET_MOUSE_CLICK_EVENT` | a1: pointer, a2: pointer, a3: pointer | `bool` |
| `HUD.PAUSE_MENU_REDRAW_INSTRUCTIONAL_BUTTONS` | a: int | `void` |
| `HUD.PAUSE_MENU_SET_BUSY_SPINNER` | a1: bool, a2: int, a3: int | `void` |
| `HUD.PAUSE_MENU_SET_WARN_ON_TAB_CHANGE` | a: bool | `void` |
| `HUD.PAUSE_MENU_SET_CLOUD_BUSY_SPINNER` | a1: string, a2: bool, a3: bool | `void` |
| `HUD.PAUSE_MENU_CLEAR_CLOUD_BUSY_SPINNER` | — | `void` |
| `HUD.IS_FRONTEND_READY_FOR_CONTROL` | — | `bool` |
| `HUD.TAKE_CONTROL_OF_FRONTEND` | — | `void` |
| `HUD.RELEASE_CONTROL_OF_FRONTEND` | — | `void` |
| `HUD.CODE_WANTS_SCRIPT_TO_TAKE_CONTROL` | — | `bool` |
| `HUD.GET_SCREEN_CODE_WANTS_SCRIPT_TO_CONTROL` | — | `int` |
| `HUD.IS_NAVIGATING_MENU_CONTENT` | — | `bool` |
| `HUD.HAS_MENU_TRIGGER_EVENT_OCCURRED` | — | `bool` |
| `HUD.HAS_MENU_LAYOUT_CHANGED_EVENT_OCCURRED` | — | `bool` |
| `HUD.SET_SAVEGAME_LIST_UNIQUE_ID` | a: int | `void` |
| `HUD.GET_MENU_LAYOUT_CHANGED_EVENT_DETAILS` | a1: pointer, a2: pointer, a3: pointer | `void` |
| `HUD.GET_PM_PLAYER_CREW_COLOR` | a1: pointer, a2: pointer, a3: pointer | `bool` |
| `HUD.GET_MENU_PED_INT_STAT` | a: int, b: pointer | `bool` |
| `HUD.GET_CHARACTER_MENU_PED_INT_STAT` | a1: int, a2: pointer, a3: int | `bool` |
| `HUD.GET_MENU_PED_MASKED_INT_STAT` | a1: hash, a2: pointer, a3: int, a4: bool | `bool` |
| `HUD.GET_CHARACTER_MENU_PED_MASKED_INT_STAT` | a1: hash, a2: pointer, a3: int, a4: int, a5: bool | `bool` |
| `HUD.GET_MENU_PED_FLOAT_STAT` | a: hash, b: pointer | `bool` |
| `HUD.GET_CHARACTER_MENU_PED_FLOAT_STAT` | a1: float, a2: pointer, a3: bool | `bool` |
| `HUD.GET_MENU_PED_BOOL_STAT` | a: hash, b: pointer | `bool` |
| `HUD.CLEAR_PED_IN_PAUSE_MENU` | — | `void` |
| `HUD.GIVE_PED_TO_PAUSE_MENU` | a: int, b: int | `void` |
| `HUD.SET_PAUSE_MENU_PED_LIGHTING` | a: bool | `void` |
| `HUD.SET_PAUSE_MENU_PED_SLEEP_STATE` | a: bool | `void` |
| `HUD.OPEN_ONLINE_POLICIES_MENU` | — | `void` |
| `HUD.ARE_ONLINE_POLICIES_UP_TO_DATE` | — | `bool` |
| `HUD.IS_ONLINE_POLICIES_MENU_ACTIVE` | — | `bool` |
| `HUD.OPEN_SOCIAL_CLUB_MENU` | a: hash | `void` |
| `HUD.CLOSE_SOCIAL_CLUB_MENU` | — | `void` |
| `HUD.SET_SOCIAL_CLUB_TOUR` | a: string | `void` |
| `HUD.IS_SOCIAL_CLUB_ACTIVE` | — | `bool` |
| `HUD.SET_TEXT_INPUT_BOX_ENABLED` | a: bool | `void` |
| `HUD.FORCE_CLOSE_TEXT_INPUT_BOX` | — | `void` |
| `HUD.SET_ALLOW_COMMA_ON_TEXT_INPUT` | a: int | `void` |
| `HUD.OVERRIDE_MP_TEXT_CHAT_TEAM_STRING` | a: hash | `void` |
| `HUD.IS_MP_TEXT_CHAT_TYPING` | — | `bool` |
| `HUD.CLOSE_MP_TEXT_CHAT` | — | `void` |
| `HUD.MP_TEXT_CHAT_IS_TEAM_JOB` | a: int | `void` |
| `HUD.OVERRIDE_MP_TEXT_CHAT_COLOR` | a: int, b: int | `void` |
| `HUD.MP_TEXT_CHAT_DISABLE` | a: bool | `void` |
| `HUD.FLAG_PLAYER_CONTEXT_IN_TOURNAMENT` | a: bool | `void` |
| `HUD.SET_PED_HAS_AI_BLIP` | a: int, b: bool | `void` |
| `HUD.SET_PED_HAS_AI_BLIP_WITH_COLOUR` | a1: int, a2: bool, a3: int | `void` |
| `HUD.DOES_PED_HAVE_AI_BLIP` | a: int | `bool` |
| `HUD.SET_PED_AI_BLIP_GANG_ID` | a: int, b: int | `void` |
| `HUD.SET_PED_AI_BLIP_HAS_CONE` | a: int, b: bool | `void` |
| `HUD.SET_PED_AI_BLIP_FORCED_ON` | a: int, b: bool | `void` |
| `HUD.SET_PED_AI_BLIP_NOTICE_RANGE` | a: int, b: float | `void` |
| `HUD.SET_PED_AI_BLIP_SPRITE` | a: int, b: int | `void` |
| `HUD.GET_AI_PED_PED_BLIP_INDEX` | a: int | `int` |
| `HUD.GET_AI_PED_VEHICLE_BLIP_INDEX` | a: int | `int` |
| `HUD.HAS_DIRECTOR_MODE_BEEN_LAUNCHED_BY_CODE` | — | `bool` |
| `HUD.SET_DIRECTOR_MODE_LAUNCHED_BY_SCRIPT` | — | `void` |
| `HUD.SET_PLAYER_IS_IN_DIRECTOR_MODE` | a: bool | `void` |
| `HUD.SET_DIRECTOR_MODE_AVAILABLE` | a: bool | `void` |
| `HUD.HIDE_HUDMARKERS_THIS_FRAME` | — | `void` |

## IIS

| Native | Params | Returns |
| --- | --- | --- |
| `IIS.SET_SHOULD_LAUNCH_IIS` | — | `void` |

## INTERIOR

| Native | Params | Returns |
| --- | --- | --- |
| `INTERIOR.GET_INTERIOR_HEADING` | a: int | `float` |
| `INTERIOR.GET_INTERIOR_LOCATION_AND_NAMEHASH` | a1: int, a2: vector3, a3: pointer | `void` |
| `INTERIOR.GET_INTERIOR_GROUP_ID` | a: int | `int` |
| `INTERIOR.GET_OFFSET_FROM_INTERIOR_IN_WORLD_COORDS` | a1: int, a2: float, a3: float, a4: float | `vector3` |
| `INTERIOR.IS_INTERIOR_SCENE` | — | `bool` |
| `INTERIOR.IS_VALID_INTERIOR` | a: int | `bool` |
| `INTERIOR.CLEAR_ROOM_FOR_ENTITY` | a: int | `void` |
| `INTERIOR.FORCE_ROOM_FOR_ENTITY` | a1: int, a2: int, a3: hash | `void` |
| `INTERIOR.GET_ROOM_KEY_FROM_ENTITY` | a: int | `hash` |
| `INTERIOR.GET_KEY_FOR_ENTITY_IN_ROOM` | a: int | `hash` |
| `INTERIOR.GET_INTERIOR_FROM_ENTITY` | a: int | `int` |
| `INTERIOR.RETAIN_ENTITY_IN_INTERIOR` | a: int, b: int | `void` |
| `INTERIOR.CLEAR_INTERIOR_STATE_OF_ENTITY` | a: int | `void` |
| `INTERIOR.FORCE_ACTIVATING_TRACKING_ON_ENTITY` | a: int, b: int | `void` |
| `INTERIOR.FORCE_ROOM_FOR_GAME_VIEWPORT` | a: int, b: hash | `void` |
| `INTERIOR.SET_ROOM_FOR_GAME_VIEWPORT_BY_NAME` | a: string | `void` |
| `INTERIOR.SET_ROOM_FOR_GAME_VIEWPORT_BY_KEY` | a: hash | `void` |
| `INTERIOR.GET_ROOM_KEY_FOR_GAME_VIEWPORT` | — | `hash` |
| `INTERIOR.CLEAR_ROOM_FOR_GAME_VIEWPORT` | — | `void` |
| `INTERIOR.GET_INTERIOR_FROM_PRIMARY_VIEW` | — | `int` |
| `INTERIOR.GET_INTERIOR_AT_COORDS` | a1: float, a2: float, a3: float | `int` |
| `INTERIOR.ADD_PICKUP_TO_INTERIOR_ROOM_BY_NAME` | a: int, b: string | `void` |
| `INTERIOR.PIN_INTERIOR_IN_MEMORY` | a: int | `void` |
| `INTERIOR.UNPIN_INTERIOR` | a: int | `void` |
| `INTERIOR.IS_INTERIOR_READY` | a: int | `bool` |
| `INTERIOR.SET_INTERIOR_IN_USE` | a: int | `bool` |
| `INTERIOR.GET_INTERIOR_AT_COORDS_WITH_TYPE` | a1: float, a2: float, a3: float, a4: string | `int` |
| `INTERIOR.GET_INTERIOR_AT_COORDS_WITH_TYPEHASH` | a1: float, a2: float, a3: float, a4: hash | `int` |
| `INTERIOR.ACTIVATE_INTERIOR_GROUPS_USING_CAMERA` | — | `void` |
| `INTERIOR.IS_COLLISION_MARKED_OUTSIDE` | a1: float, a2: float, a3: float | `bool` |
| `INTERIOR.GET_INTERIOR_FROM_COLLISION` | a1: float, a2: float, a3: float | `int` |
| `INTERIOR.ENABLE_STADIUM_PROBES_THIS_FRAME` | a: bool | `void` |
| `INTERIOR.ACTIVATE_INTERIOR_ENTITY_SET` | a: int, b: string | `void` |
| `INTERIOR.DEACTIVATE_INTERIOR_ENTITY_SET` | a: int, b: string | `void` |
| `INTERIOR.IS_INTERIOR_ENTITY_SET_ACTIVE` | a: int, b: string | `bool` |
| `INTERIOR.SET_INTERIOR_ENTITY_SET_TINT_INDEX` | a1: int, a2: string, a3: int | `void` |
| `INTERIOR.REFRESH_INTERIOR` | a: int | `void` |
| `INTERIOR.ENABLE_EXTERIOR_CULL_MODEL_THIS_FRAME` | a: hash | `void` |
| `INTERIOR.ENABLE_SHADOW_CULL_MODEL_THIS_FRAME` | a: hash | `void` |
| `INTERIOR.DISABLE_INTERIOR` | a: int, b: bool | `void` |
| `INTERIOR.IS_INTERIOR_DISABLED` | a: int | `bool` |
| `INTERIOR.CAP_INTERIOR` | a: int, b: bool | `void` |
| `INTERIOR.IS_INTERIOR_CAPPED` | a: int | `bool` |
| `INTERIOR.DISABLE_METRO_SYSTEM` | a: bool | `void` |
| `INTERIOR.SET_IS_EXTERIOR_ONLY` | a: int, b: bool | `void` |

## ITEMSETS

| Native | Params | Returns |
| --- | --- | --- |
| `ITEMSETS.CREATE_ITEMSET` | a: bool | `int` |
| `ITEMSETS.DESTROY_ITEMSET` | a: int | `void` |
| `ITEMSETS.IS_ITEMSET_VALID` | a: int | `bool` |
| `ITEMSETS.ADD_TO_ITEMSET` | a: int, b: int | `bool` |
| `ITEMSETS.REMOVE_FROM_ITEMSET` | a: int, b: int | `void` |
| `ITEMSETS.GET_ITEMSET_SIZE` | a: int | `int` |
| `ITEMSETS.GET_INDEXED_ITEM_IN_ITEMSET` | a: int, b: int | `int` |
| `ITEMSETS.IS_IN_ITEMSET` | a: int, b: int | `bool` |
| `ITEMSETS.CLEAN_ITEMSET` | a: int | `void` |

## LANDINGPAGE

| Native | Params | Returns |
| --- | --- | --- |
| `LANDINGPAGE.IS_LANDING_PAGE_ACTIVE` | — | `bool` |
| `LANDINGPAGE.SET_SHOULD_LAUNCH_LANDING_PAGE` | a: int | `void` |
| `LANDINGPAGE.SET_SHOULD_DISMISS_LANDING_PAGE` | — | `void` |
| `LANDINGPAGE.GET_LANDING_PAGE_SELECTED_CHARACTER_SLOT` | — | `int` |

## LOBBY

| Native | Params | Returns |
| --- | --- | --- |
| `LOBBY.LOBBY_AUTO_MULTIPLAYER_MENU` | — | `bool` |
| `LOBBY.LOBBY_AUTO_MULTIPLAYER_FREEMODE` | — | `bool` |
| `LOBBY.LOBBY_SET_AUTO_MULTIPLAYER` | a: bool | `void` |
| `LOBBY.LOBBY_AUTO_MULTIPLAYER_EVENT` | — | `bool` |
| `LOBBY.LOBBY_SET_AUTO_MULTIPLAYER_EVENT` | a: bool | `void` |
| `LOBBY.LOBBY_AUTO_MULTIPLAYER_RANDOM_JOB` | — | `bool` |
| `LOBBY.LOBBY_SET_AUTO_MP_RANDOM_JOB` | a: bool | `void` |
| `LOBBY.SHUTDOWN_SESSION_CLEARS_AUTO_MULTIPLAYER` | a: bool | `void` |

## LOCALIZATION

| Native | Params | Returns |
| --- | --- | --- |
| `LOCALIZATION.LOCALIZATION_GET_SYSTEM_LANGUAGE` | — | `int` |
| `LOCALIZATION.GET_CURRENT_LANGUAGE` | — | `int` |
| `LOCALIZATION.LOCALIZATION_GET_SYSTEM_DATE_TYPE` | — | `int` |

## MISC

| Native | Params | Returns |
| --- | --- | --- |
| `MISC.GET_ALLOCATED_STACK_SIZE` | — | `int` |
| `MISC.GET_NUMBER_OF_FREE_STACKS_OF_THIS_SIZE` | a: int | `int` |
| `MISC.SET_RANDOM_SEED` | a: int | `void` |
| `MISC.SET_TIME_SCALE` | a: float | `void` |
| `MISC.SET_MISSION_FLAG` | a: bool | `void` |
| `MISC.GET_MISSION_FLAG` | — | `bool` |
| `MISC.SET_RANDOM_EVENT_FLAG` | a: bool | `void` |
| `MISC.GET_RANDOM_EVENT_FLAG` | — | `bool` |
| `MISC.GET_CONTENT_TO_LOAD` | — | `string` |
| `MISC.ACTIVITY_FEED_CREATE` | a: string, b: string | `void` |
| `MISC.ACTIVITY_FEED_ADD_SUBSTRING_TO_CAPTION` | a: string | `void` |
| `MISC.ACTIVITY_FEED_ADD_LITERAL_SUBSTRING_TO_CAPTION` | a: string | `void` |
| `MISC.ACTIVITY_FEED_ADD_INT_TO_CAPTION` | a: int | `void` |
| `MISC.ACTIVITY_FEED_LARGE_IMAGE_URL` | a: string | `void` |
| `MISC.ACTIVITY_FEED_ACTION_START_WITH_COMMAND_LINE` | a: string, b: string | `void` |
| `MISC.ACTIVITY_FEED_ACTION_START_WITH_COMMAND_LINE_ADD` | a: string | `void` |
| `MISC.ACTIVITY_FEED_POST` | — | `void` |
| `MISC.ACTIVITY_FEED_ONLINE_PLAYED_WITH_POST` | a: string | `void` |
| `MISC.HAS_RESUMED_FROM_SUSPEND` | — | `bool` |
| `MISC.SET_SCRIPT_HIGH_PRIO` | a: bool | `void` |
| `MISC.SET_THIS_IS_A_TRIGGER_SCRIPT` | a: bool | `void` |
| `MISC.INFORM_CODE_OF_CONTENT_ID_OF_CURRENT_UGC_MISSION` | a: string | `void` |
| `MISC.GET_BASE_ELEMENT_LOCATION_FROM_METADATA_BLOCK` | a1: pointer, a2: pointer, a3: int, a4: bool | `bool` |
| `MISC.GET_PREV_WEATHER_TYPE_HASH_NAME` | — | `hash` |
| `MISC.GET_NEXT_WEATHER_TYPE_HASH_NAME` | — | `hash` |
| `MISC.IS_PREV_WEATHER_TYPE` | a: string | `bool` |
| `MISC.IS_NEXT_WEATHER_TYPE` | a: string | `bool` |
| `MISC.SET_WEATHER_TYPE_PERSIST` | a: string | `void` |
| `MISC.SET_WEATHER_TYPE_NOW_PERSIST` | a: string | `void` |
| `MISC.SET_WEATHER_TYPE_NOW` | a: string | `void` |
| `MISC.SET_WEATHER_TYPE_OVERTIME_PERSIST` | a: string, b: float | `void` |
| `MISC.SET_RANDOM_WEATHER_TYPE` | — | `void` |
| `MISC.CLEAR_WEATHER_TYPE_PERSIST` | — | `void` |
| `MISC.CLEAR_WEATHER_TYPE_NOW_PERSIST_NETWORK` | a: int | `void` |
| `MISC.GET_CURR_WEATHER_STATE` | a1: pointer, a2: pointer, a3: pointer | `void` |
| `MISC.SET_CURR_WEATHER_STATE` | a1: hash, a2: hash, a3: float | `void` |
| `MISC.SET_OVERRIDE_WEATHER` | a: string | `void` |
| `MISC.SET_OVERRIDE_WEATHEREX` | a: string, b: bool | `void` |
| `MISC.CLEAR_OVERRIDE_WEATHER` | — | `void` |
| `MISC.WATER_OVERRIDE_SET_SHOREWAVEAMPLITUDE` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_SHOREWAVEMINAMPLITUDE` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_SHOREWAVEMAXAMPLITUDE` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_OCEANNOISEMINAMPLITUDE` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_OCEANWAVEAMPLITUDE` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_OCEANWAVEMINAMPLITUDE` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_OCEANWAVEMAXAMPLITUDE` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_RIPPLEBUMPINESS` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_RIPPLEMINBUMPINESS` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_RIPPLEMAXBUMPINESS` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_RIPPLEDISTURB` | a: float | `void` |
| `MISC.WATER_OVERRIDE_SET_STRENGTH` | a: float | `void` |
| `MISC.WATER_OVERRIDE_FADE_IN` | a: float | `void` |
| `MISC.WATER_OVERRIDE_FADE_OUT` | a: float | `void` |
| `MISC.SET_WIND` | a: float | `void` |
| `MISC.SET_WIND_SPEED` | a: float | `void` |
| `MISC.GET_WIND_SPEED` | — | `float` |
| `MISC.SET_WIND_DIRECTION` | a: float | `void` |
| `MISC.GET_WIND_DIRECTION` | — | `vector3` |
| `MISC.SET_RAIN` | a: float | `void` |
| `MISC.GET_RAIN_LEVEL` | — | `float` |
| `MISC.SET_SNOW` | a: float | `void` |
| `MISC.GET_SNOW_LEVEL` | — | `float` |
| `MISC.FORCE_LIGHTNING_FLASH` | — | `void` |
| `MISC.SET_CLOUD_SETTINGS_OVERRIDE` | a: string | `void` |
| `MISC.PRELOAD_CLOUD_HAT` | a: string | `void` |
| `MISC.LOAD_CLOUD_HAT` | a: string, b: float | `void` |
| `MISC.UNLOAD_CLOUD_HAT` | a: string, b: float | `void` |
| `MISC.UNLOAD_ALL_CLOUD_HATS` | — | `void` |
| `MISC.SET_CLOUDS_ALPHA` | a: float | `void` |
| `MISC.GET_CLOUDS_ALPHA` | — | `float` |
| `MISC.GET_GAME_TIMER` | — | `int` |
| `MISC.GET_FRAME_TIME` | — | `float` |
| `MISC.GET_SYSTEM_TIME_STEP` | — | `float` |
| `MISC.GET_FRAME_COUNT` | — | `int` |
| `MISC.GET_RANDOM_FLOAT_IN_RANGE` | a: float, b: float | `float` |
| `MISC.GET_RANDOM_INT_IN_RANGE` | a: int, b: int | `int` |
| `MISC.GET_RANDOM_MWC_INT_IN_RANGE` | a: int, b: int | `int` |
| `MISC.GET_GROUND_Z_FOR_3D_COORD` | a1: float, a2: float, a3: float, a4: pointer, a5: bool, a6: bool | `bool` |
| `MISC.GET_GROUND_Z_AND_NORMAL_FOR_3D_COORD` | a1: float, a2: float, a3: float, a4: pointer, a5: vector3 | `bool` |
| `MISC.GET_GROUND_Z_EXCLUDING_OBJECTS_FOR_3D_COORD` | a1: float, a2: float, a3: float, a4: pointer, a5: bool, a6: bool | `bool` |
| `MISC.ASIN` | a: float | `float` |
| `MISC.ACOS` | a: float | `float` |
| `MISC.TAN` | a: float | `float` |
| `MISC.ATAN` | a: float | `float` |
| `MISC.ATAN2` | a: float, b: float | `float` |
| `MISC.GET_DISTANCE_BETWEEN_COORDS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool | `float` |
| `MISC.GET_ANGLE_BETWEEN_2D_VECTORS` | a1: float, a2: float, a3: float, a4: float | `float` |
| `MISC.GET_HEADING_FROM_VECTOR_2D` | a: float, b: float | `float` |
| `MISC.GET_RATIO_OF_CLOSEST_POINT_ON_LINE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool | `float` |
| `MISC.GET_CLOSEST_POINT_ON_LINE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool | `vector3` |
| `MISC.GET_LINE_PLANE_INTERSECTION` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: pointer | `bool` |
| `MISC.GET_POINT_AREA_OVERLAP` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int, a11: int, a12: int, a13: int, a14: int | `bool` |
| `MISC.SET_BIT` | a: pointer, b: int | `void` |
| `MISC.CLEAR_BIT` | a: pointer, b: int | `void` |
| `MISC.GET_HASH_KEY` | a: string | `hash` |
| `MISC.SLERP_NEAR_QUATERNION` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: pointer, a11: pointer, a12: pointer, a13: pointer | `void` |
| `MISC.IS_AREA_OCCUPIED` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool, a9: bool, a10: bool, a11: bool, a12: int, a13: bool | `bool` |
| `MISC.IS_AREA_OCCUPIED_SLOW` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int, a11: int, a12: int, a13: int | `bool` |
| `MISC.IS_POSITION_OCCUPIED` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: bool, a9: bool, a10: int, a11: bool | `bool` |
| `MISC.IS_POINT_OBSCURED_BY_A_MISSION_ENTITY` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `bool` |
| `MISC.CLEAR_AREA` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: bool | `void` |
| `MISC.CLEAR_AREA_LEAVE_VEHICLE_HEALTH` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: bool | `void` |
| `MISC.CLEAR_AREA_OF_VEHICLES` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: bool, a9: bool, a10: bool, a11: int | `void` |
| `MISC.CLEAR_ANGLED_AREA_OF_VEHICLES` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool, a9: bool, a10: bool, a11: bool, a12: bool, a13: int, a14: int | `void` |
| `MISC.CLEAR_AREA_OF_OBJECTS` | a1: float, a2: float, a3: float, a4: float, a5: int | `void` |
| `MISC.CLEAR_AREA_OF_PEDS` | a1: float, a2: float, a3: float, a4: float, a5: int | `void` |
| `MISC.CLEAR_AREA_OF_COPS` | a1: float, a2: float, a3: float, a4: float, a5: int | `void` |
| `MISC.CLEAR_AREA_OF_PROJECTILES` | a1: float, a2: float, a3: float, a4: float, a5: int | `void` |
| `MISC.CLEAR_SCENARIO_SPAWN_HISTORY` | — | `void` |
| `MISC.SET_SAVE_MENU_ACTIVE` | a: bool | `void` |
| `MISC.GET_STATUS_OF_MANUAL_SAVE` | — | `int` |
| `MISC.SET_CREDITS_ACTIVE` | a: bool | `void` |
| `MISC.SET_CREDITS_FADE_OUT_WITH_SCREEN` | a: bool | `void` |
| `MISC.HAVE_CREDITS_REACHED_END` | — | `bool` |
| `MISC.ARE_CREDITS_RUNNING` | — | `bool` |
| `MISC.TERMINATE_ALL_SCRIPTS_WITH_THIS_NAME` | a: string | `void` |
| `MISC.NETWORK_SET_SCRIPT_IS_SAFE_FOR_NETWORK_GAME` | — | `void` |
| `MISC.ADD_HOSPITAL_RESTART` | a1: float, a2: float, a3: float, a4: float, a5: int | `int` |
| `MISC.DISABLE_HOSPITAL_RESTART` | a: int, b: bool | `void` |
| `MISC.ADD_POLICE_RESTART` | a1: float, a2: float, a3: float, a4: float, a5: int | `int` |
| `MISC.DISABLE_POLICE_RESTART` | a: int, b: bool | `void` |
| `MISC.SET_RESTART_COORD_OVERRIDE` | a1: float, a2: float, a3: float, a4: float | `void` |
| `MISC.CLEAR_RESTART_COORD_OVERRIDE` | — | `void` |
| `MISC.PAUSE_DEATH_ARREST_RESTART` | a: bool | `void` |
| `MISC.IGNORE_NEXT_RESTART` | a: bool | `void` |
| `MISC.SET_FADE_OUT_AFTER_DEATH` | a: bool | `void` |
| `MISC.SET_FADE_OUT_AFTER_ARREST` | a: bool | `void` |
| `MISC.SET_FADE_IN_AFTER_DEATH_ARREST` | a: bool | `void` |
| `MISC.SET_FADE_IN_AFTER_LOAD` | a: bool | `void` |
| `MISC.REGISTER_SAVE_HOUSE` | a1: float, a2: float, a3: float, a4: float, a5: string, a6: int, a7: int | `int` |
| `MISC.SET_SAVE_HOUSE` | a1: int, a2: bool, a3: bool | `void` |
| `MISC.OVERRIDE_SAVE_HOUSE` | a1: bool, a2: float, a3: float, a4: float, a5: float, a6: bool, a7: float, a8: float | `bool` |
| `MISC.GET_SAVE_HOUSE_DETAILS_AFTER_SUCCESSFUL_LOAD` | a1: vector3, a2: pointer, a3: pointer, a4: pointer | `bool` |
| `MISC.DO_AUTO_SAVE` | — | `void` |
| `MISC.GET_IS_AUTO_SAVE_OFF` | — | `bool` |
| `MISC.IS_AUTO_SAVE_IN_PROGRESS` | — | `bool` |
| `MISC.HAS_CODE_REQUESTED_AUTOSAVE` | — | `bool` |
| `MISC.CLEAR_CODE_REQUESTED_AUTOSAVE` | — | `void` |
| `MISC.BEGIN_REPLAY_STATS` | a: int, b: int | `void` |
| `MISC.ADD_REPLAY_STAT_VALUE` | a: int | `void` |
| `MISC.END_REPLAY_STATS` | — | `void` |
| `MISC.HAVE_REPLAY_STATS_BEEN_STORED` | — | `bool` |
| `MISC.GET_REPLAY_STAT_MISSION_ID` | — | `int` |
| `MISC.GET_REPLAY_STAT_MISSION_TYPE` | — | `int` |
| `MISC.GET_REPLAY_STAT_COUNT` | — | `int` |
| `MISC.GET_REPLAY_STAT_AT_INDEX` | a: int | `int` |
| `MISC.CLEAR_REPLAY_STATS` | — | `void` |
| `MISC.QUEUE_MISSION_REPEAT_LOAD` | — | `bool` |
| `MISC.QUEUE_MISSION_REPEAT_SAVE` | — | `bool` |
| `MISC.QUEUE_MISSION_REPEAT_SAVE_FOR_BENCHMARK_TEST` | — | `bool` |
| `MISC.GET_STATUS_OF_MISSION_REPEAT_SAVE` | — | `int` |
| `MISC.IS_MEMORY_CARD_IN_USE` | — | `bool` |
| `MISC.SHOOT_SINGLE_BULLET_BETWEEN_COORDS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: bool, a9: hash, a10: int, a11: bool, a12: bool, a13: float | `void` |
| `MISC.SHOOT_SINGLE_BULLET_BETWEEN_COORDS_IGNORE_ENTITY` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: bool, a9: hash, a10: int, a11: bool, a12: bool, a13: float, a14: int, a15: int | `void` |
| `MISC.SHOOT_SINGLE_BULLET_BETWEEN_COORDS_IGNORE_ENTITY_NEW` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: bool, a9: hash, a10: int, a11: bool, a12: bool, a13: float, a14: int, a15: bool, a16: bool, a17: int, a18: bool, a19: int, a20: int, a21: int | `void` |
| `MISC.GET_MODEL_DIMENSIONS` | a1: hash, a2: vector3, a3: vector3 | `void` |
| `MISC.SET_FAKE_WANTED_LEVEL` | a: int | `void` |
| `MISC.GET_FAKE_WANTED_LEVEL` | — | `int` |
| `MISC.USING_MISSION_CREATOR` | a: bool | `void` |
| `MISC.ALLOW_MISSION_CREATOR_WARP` | a: bool | `void` |
| `MISC.SET_MINIGAME_IN_PROGRESS` | a: bool | `void` |
| `MISC.IS_MINIGAME_IN_PROGRESS` | — | `bool` |
| `MISC.IS_THIS_A_MINIGAME_SCRIPT` | — | `bool` |
| `MISC.IS_SNIPER_INVERTED` | — | `bool` |
| `MISC.SHOULD_USE_METRIC_MEASUREMENTS` | — | `bool` |
| `MISC.GET_PROFILE_SETTING` | a: int | `int` |
| `MISC.ARE_STRINGS_EQUAL` | a: string, b: string | `bool` |
| `MISC.COMPARE_STRINGS` | a1: string, a2: string, a3: bool, a4: int | `int` |
| `MISC.ABSI` | a: int | `int` |
| `MISC.ABSF` | a: float | `float` |
| `MISC.IS_SNIPER_BULLET_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `bool` |
| `MISC.IS_PROJECTILE_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool | `bool` |
| `MISC.IS_PROJECTILE_TYPE_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: bool | `bool` |
| `MISC.IS_PROJECTILE_TYPE_IN_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: bool | `bool` |
| `MISC.IS_PROJECTILE_TYPE_WITHIN_DISTANCE` | a1: float, a2: float, a3: float, a4: hash, a5: float, a6: bool | `bool` |
| `MISC.GET_COORDS_OF_PROJECTILE_TYPE_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: hash, a8: vector3, a9: bool | `bool` |
| `MISC.GET_COORDS_OF_PROJECTILE_TYPE_IN_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: hash, a9: vector3, a10: bool | `bool` |
| `MISC.GET_COORDS_OF_PROJECTILE_TYPE_WITHIN_DISTANCE` | a1: int, a2: hash, a3: float, a4: vector3, a5: bool | `bool` |
| `MISC.GET_PROJECTILE_OF_PROJECTILE_TYPE_WITHIN_DISTANCE` | a1: int, a2: hash, a3: float, a4: vector3, a5: pointer, a6: bool | `bool` |
| `MISC.IS_BULLET_IN_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool | `bool` |
| `MISC.IS_BULLET_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: bool | `bool` |
| `MISC.IS_BULLET_IN_BOX` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool | `bool` |
| `MISC.HAS_BULLET_IMPACTED_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool | `bool` |
| `MISC.HAS_BULLET_IMPACTED_IN_BOX` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool | `bool` |
| `MISC.IS_ORBIS_VERSION` | — | `bool` |
| `MISC.IS_DURANGO_VERSION` | — | `bool` |
| `MISC.IS_XBOX360_VERSION` | — | `bool` |
| `MISC.IS_PS3_VERSION` | — | `bool` |
| `MISC.IS_PC_VERSION` | — | `bool` |
| `MISC.IS_STEAM_VERSION` | — | `bool` |
| `MISC._IS_XBOXPC_VERSION` | — | `bool` |
| `MISC.IS_AUSSIE_VERSION` | — | `bool` |
| `MISC.IS_JAPANESE_VERSION` | — | `bool` |
| `MISC.IS_XBOX_PLATFORM` | — | `bool` |
| `MISC.IS_SCARLETT_VERSION` | — | `bool` |
| `MISC.IS_SCE_PLATFORM` | — | `bool` |
| `MISC.IS_PROSPERO_VERSION` | — | `bool` |
| `MISC.IS_STRING_NULL` | a: string | `bool` |
| `MISC.IS_STRING_NULL_OR_EMPTY` | a: string | `bool` |
| `MISC.STRING_TO_INT` | a: string, b: pointer | `bool` |
| `MISC.SET_BITS_IN_RANGE` | a1: pointer, a2: int, a3: int, a4: int | `void` |
| `MISC.GET_BITS_IN_RANGE` | a1: int, a2: int, a3: int | `int` |
| `MISC.ADD_STUNT_JUMP` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: float, a15: float, a16: int, a17: int, a18: int | `int` |
| `MISC.ADD_STUNT_JUMP_ANGLED` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: float, a15: float, a16: float, a17: float, a18: int, a19: int, a20: int | `int` |
| `MISC.TOGGLE_SHOW_OPTIONAL_STUNT_JUMP_CAMERA` | a: bool | `void` |
| `MISC.DELETE_STUNT_JUMP` | a: int | `void` |
| `MISC.ENABLE_STUNT_JUMP_SET` | a: int | `void` |
| `MISC.DISABLE_STUNT_JUMP_SET` | a: int | `void` |
| `MISC.SET_STUNT_JUMPS_CAN_TRIGGER` | a: bool | `void` |
| `MISC.IS_STUNT_JUMP_IN_PROGRESS` | — | `bool` |
| `MISC.IS_STUNT_JUMP_MESSAGE_SHOWING` | — | `bool` |
| `MISC.GET_NUM_SUCCESSFUL_STUNT_JUMPS` | — | `int` |
| `MISC.GET_TOTAL_SUCCESSFUL_STUNT_JUMPS` | — | `int` |
| `MISC.CANCEL_STUNT_JUMP` | — | `void` |
| `MISC.SET_GAME_PAUSED` | a: bool | `void` |
| `MISC.SET_THIS_SCRIPT_CAN_BE_PAUSED` | a: bool | `void` |
| `MISC.SET_THIS_SCRIPT_CAN_REMOVE_BLIPS_CREATED_BY_ANY_SCRIPT` | a: bool | `void` |
| `MISC.HAS_CHEAT_WITH_HASH_BEEN_ACTIVATED` | a: hash, b: int | `bool` |
| `MISC.HAS_PC_CHEAT_WITH_HASH_BEEN_ACTIVATED` | a: hash | `bool` |
| `MISC.OVERRIDE_FREEZE_FLAGS` | a: bool | `void` |
| `MISC.SET_INSTANCE_PRIORITY_MODE` | a: int | `void` |
| `MISC.SET_INSTANCE_PRIORITY_HINT` | a: int | `void` |
| `MISC.IS_FRONTEND_FADING` | — | `bool` |
| `MISC.POPULATE_NOW` | — | `void` |
| `MISC.GET_INDEX_OF_CURRENT_LEVEL` | — | `int` |
| `MISC.SET_GRAVITY_LEVEL` | a: int | `void` |
| `MISC.START_SAVE_DATA` | a1: pointer, a2: int, a3: bool | `void` |
| `MISC.STOP_SAVE_DATA` | — | `void` |
| `MISC.GET_SIZE_OF_SAVE_DATA` | a: bool | `int` |
| `MISC.REGISTER_INT_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_INT64_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_ENUM_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_FLOAT_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_BOOL_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_TEXT_LABEL_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_TEXT_LABEL_15_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_TEXT_LABEL_23_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_TEXT_LABEL_31_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.REGISTER_TEXT_LABEL_63_TO_SAVE` | a: pointer, b: string | `void` |
| `MISC.START_SAVE_STRUCT_WITH_SIZE` | a1: pointer, a2: int, a3: string | `void` |
| `MISC.STOP_SAVE_STRUCT` | — | `void` |
| `MISC.START_SAVE_ARRAY_WITH_SIZE` | a1: pointer, a2: int, a3: string | `void` |
| `MISC.STOP_SAVE_ARRAY` | — | `void` |
| `MISC.COPY_SCRIPT_STRUCT` | a1: pointer, a2: pointer, a3: int | `void` |
| `MISC.ENABLE_DISPATCH_SERVICE` | a: int, b: bool | `void` |
| `MISC.K_DISPATCH_SERVICE_RESOURCE_CREATION` | a: int, b: bool | `void` |
| `MISC.GET_NUMBER_RESOURCES_ALLOCATED_TO_WANTED_LEVEL` | a: int | `int` |
| `MISC.CREATE_INCIDENT` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: float, a7: pointer, a8: int, a9: int | `bool` |
| `MISC.CREATE_INCIDENT_WITH_ENTITY` | a1: int, a2: int, a3: int, a4: float, a5: pointer, a6: int, a7: int | `bool` |
| `MISC.DELETE_INCIDENT` | a: int | `void` |
| `MISC.IS_INCIDENT_VALID` | a: int | `bool` |
| `MISC.SET_INCIDENT_REQUESTED_UNITS` | a1: int, a2: int, a3: int | `void` |
| `MISC.SET_IDEAL_SPAWN_DISTANCE_FOR_INCIDENT` | a: int, b: float | `void` |
| `MISC.FIND_SPAWN_POINT_IN_DIRECTION` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: vector3 | `bool` |
| `MISC.ADD_POP_MULTIPLIER_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: bool | `int` |
| `MISC.DOES_POP_MULTIPLIER_AREA_EXIST` | a: int | `bool` |
| `MISC.REMOVE_POP_MULTIPLIER_AREA` | a: int, b: bool | `void` |
| `MISC.IS_POP_MULTIPLIER_AREA_NETWORKED` | a: int | `bool` |
| `MISC.ADD_POP_MULTIPLIER_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool | `int` |
| `MISC.DOES_POP_MULTIPLIER_SPHERE_EXIST` | a: int | `bool` |
| `MISC.REMOVE_POP_MULTIPLIER_SPHERE` | a: int, b: bool | `void` |
| `MISC.ENABLE_TENNIS_MODE` | a1: int, a2: bool, a3: bool | `void` |
| `MISC.IS_TENNIS_MODE` | a: int | `bool` |
| `MISC.PLAY_TENNIS_SWING_ANIM` | a1: int, a2: string, a3: string, a4: float, a5: float, a6: bool | `void` |
| `MISC.GET_TENNIS_SWING_ANIM_COMPLETE` | a: int | `bool` |
| `MISC.GET_TENNIS_SWING_ANIM_CAN_BE_INTERRUPTED` | a: int | `bool` |
| `MISC.GET_TENNIS_SWING_ANIM_SWUNG` | a: int | `bool` |
| `MISC.PLAY_TENNIS_DIVE_ANIM` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool | `void` |
| `MISC.SET_TENNIS_MOVE_NETWORK_SIGNAL_FLOAT` | a1: int, a2: string, a3: float | `void` |
| `MISC.RESET_DISPATCH_SPAWN_LOCATION` | — | `void` |
| `MISC.SET_DISPATCH_SPAWN_LOCATION` | a1: float, a2: float, a3: float | `void` |
| `MISC.RESET_DISPATCH_IDEAL_SPAWN_DISTANCE` | — | `void` |
| `MISC.SET_DISPATCH_IDEAL_SPAWN_DISTANCE` | a: float | `void` |
| `MISC.RESET_DISPATCH_TIME_BETWEEN_SPAWN_ATTEMPTS` | a: int | `void` |
| `MISC.SET_DISPATCH_TIME_BETWEEN_SPAWN_ATTEMPTS` | a: int, b: float | `void` |
| `MISC.SET_DISPATCH_TIME_BETWEEN_SPAWN_ATTEMPTS_MULTIPLIER` | a: int, b: float | `void` |
| `MISC.ADD_DISPATCH_SPAWN_ANGLED_BLOCKING_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `int` |
| `MISC.ADD_DISPATCH_SPAWN_SPHERE_BLOCKING_AREA` | a1: float, a2: float, a3: float, a4: float | `int` |
| `MISC.REMOVE_DISPATCH_SPAWN_BLOCKING_AREA` | a: int | `void` |
| `MISC.RESET_DISPATCH_SPAWN_BLOCKING_AREAS` | — | `void` |
| `MISC.RESET_WANTED_RESPONSE_NUM_PEDS_TO_SPAWN` | — | `void` |
| `MISC.SET_WANTED_RESPONSE_NUM_PEDS_TO_SPAWN` | a: int, b: int | `void` |
| `MISC.ADD_TACTICAL_NAV_MESH_POINT` | a1: float, a2: float, a3: float | `void` |
| `MISC.CLEAR_TACTICAL_NAV_MESH_POINTS` | — | `void` |
| `MISC.SET_RIOT_MODE_ENABLED` | a: bool | `void` |
| `MISC.DISPLAY_ONSCREEN_KEYBOARD_WITH_LONGER_INITIAL_STRING` | a1: int, a2: string, a3: pointer, a4: string, a5: string, a6: string, a7: string, a8: string, a9: string, a10: string, a11: string, a12: int | `void` |
| `MISC.DISPLAY_ONSCREEN_KEYBOARD` | a1: int, a2: string, a3: string, a4: string, a5: string, a6: string, a7: string, a8: int | `void` |
| `MISC.UPDATE_ONSCREEN_KEYBOARD` | — | `int` |
| `MISC.GET_ONSCREEN_KEYBOARD_RESULT` | — | `string` |
| `MISC.CANCEL_ONSCREEN_KEYBOARD` | — | `void` |
| `MISC.NEXT_ONSCREEN_KEYBOARD_RESULT_WILL_DISPLAY_USING_THESE_FONTS` | a: int | `void` |
| `MISC.ACTION_MANAGER_ENABLE_ACTION` | a: hash, b: bool | `void` |
| `MISC.GET_REAL_WORLD_TIME` | — | `int` |
| `MISC.SUPRESS_RANDOM_EVENT_THIS_FRAME` | a: int, b: bool | `void` |
| `MISC.SET_EXPLOSIVE_AMMO_THIS_FRAME` | a: int | `void` |
| `MISC.SET_FIRE_AMMO_THIS_FRAME` | a: int | `void` |
| `MISC.SET_EXPLOSIVE_MELEE_THIS_FRAME` | a: int | `void` |
| `MISC.SET_SUPER_JUMP_THIS_FRAME` | a: int | `void` |
| `MISC.SET_BEAST_JUMP_THIS_FRAME` | a: int | `void` |
| `MISC.SET_FORCED_JUMP_THIS_FRAME` | a: int | `void` |
| `MISC.HAS_GAME_INSTALLED_THIS_SESSION` | — | `bool` |
| `MISC.SET_TICKER_JOHNMARSTON_IS_DONE` | — | `void` |
| `MISC.ARE_PROFILE_SETTINGS_VALID` | — | `bool` |
| `MISC.PREVENT_ARREST_STATE_THIS_FRAME` | — | `void` |
| `MISC.FORCE_GAME_STATE_PLAYING` | — | `void` |
| `MISC.SCRIPT_RACE_INIT` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MISC.SCRIPT_RACE_SHUTDOWN` | — | `void` |
| `MISC.SCRIPT_RACE_PLAYER_HIT_CHECKPOINT` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MISC.SCRIPT_RACE_GET_PLAYER_SPLIT_TIME` | a1: int, a2: pointer, a3: pointer | `bool` |
| `MISC.START_END_USER_BENCHMARK` | — | `void` |
| `MISC.STOP_END_USER_BENCHMARK` | — | `void` |
| `MISC.RESET_END_USER_BENCHMARK` | — | `void` |
| `MISC.SAVE_END_USER_BENCHMARK` | — | `void` |
| `MISC.UI_STARTED_END_USER_BENCHMARK` | — | `bool` |
| `MISC.LANDING_SCREEN_STARTED_END_USER_BENCHMARK` | — | `bool` |
| `MISC.IS_COMMANDLINE_END_USER_BENCHMARK` | — | `bool` |
| `MISC.GET_BENCHMARK_ITERATIONS` | — | `int` |
| `MISC.GET_BENCHMARK_PASS` | — | `int` |
| `MISC.RESTART_GAME` | — | `void` |
| `MISC.QUIT_GAME` | — | `void` |
| `MISC.HAS_ASYNC_INSTALL_FINISHED` | — | `bool` |
| `MISC.CLEANUP_ASYNC_INSTALL` | — | `void` |
| `MISC.PLM_IS_IN_CONSTRAINED_MODE` | — | `bool` |
| `MISC.PLM_GET_CONSTRAINED_DURATION_MS` | — | `int` |
| `MISC.SET_PLAYER_IS_IN_ANIMAL_FORM` | a: bool | `void` |
| `MISC.GET_IS_PLAYER_IN_ANIMAL_FORM` | — | `bool` |
| `MISC.SET_PLAYER_IS_REPEATING_A_MISSION` | a: bool | `void` |
| `MISC.DISABLE_SCREEN_DIMMING_THIS_FRAME` | — | `void` |
| `MISC.GET_CITY_DENSITY` | — | `float` |
| `MISC.USE_ACTIVE_CAMERA_FOR_TIMESLICING_CENTRE` | — | `void` |
| `MISC.GET_SCRIPT_ROUTER_CONTEXT` | a: pointer | `bool` |
| `MISC.SET_SCRIPT_ROUTER_LINK` | a: pointer | `bool` |
| `MISC.HAS_PENDING_SCRIPT_ROUTER_LINK` | — | `bool` |
| `MISC.CLEAR_SCRIPT_ROUTER_LINK` | — | `void` |
| `MISC.REPORT_INVALID_SCRIPT_ROUTER_ARGUMENT` | a: string | `void` |
| `MISC.SET_ACTIVITY_SCRIPT_ROUTING_ENABLED` | a: bool | `void` |
| `MISC.IS_SESSION_INITIALIZED` | — | `bool` |
| `MISC.GET_CHOSEN_CRIMINAL_CAREER` | — | `int` |
| `MISC.HAS_FINALIZED_CHOSEN_CRIMINAL_CAREER` | — | `bool` |
| `MISC.GET_CHOSEN_MP_CHARACTER_SLOT` | — | `int` |
| `MISC.RESET_CHOSEN_MP_CHARACTER_SLOT` | — | `void` |
| `MISC.SET_CONTENT_ID_INDEX` | a: hash, b: int | `void` |
| `MISC.GET_CONTENT_ID_INDEX` | a: hash | `int` |
| `MISC._SET_CONTENT_PROP_TYPE` | a: hash, b: int | `void` |
| `MISC._GET_CONTENT_PROP_TYPE` | a: hash | `int` |

## MONEY

| Native | Params | Returns |
| --- | --- | --- |
| `MONEY.NETWORK_INITIALIZE_CASH` | a: int, b: int | `void` |
| `MONEY.NETWORK_DELETE_CHARACTER` | — | blocked: Not allowed |
| `MONEY.NETWORK_MANUAL_DELETE_CHARACTER` | — | blocked: Not allowed |
| `MONEY.NETWORK_GET_PLAYER_IS_HIGH_EARNER` | — | `bool` |
| `MONEY.NETWORK_CLEAR_CHARACTER_WALLET` | a: int | `void` |
| `MONEY.NETWORK_GIVE_PLAYER_JOBSHARE_CASH` | a: int, b: pointer | `void` |
| `MONEY.NETWORK_RECEIVE_PLAYER_JOBSHARE_CASH` | a: int, b: pointer | `void` |
| `MONEY.NETWORK_CAN_SHARE_JOB_CASH` | — | `bool` |
| `MONEY.NETWORK_REFUND_CASH` | a1: int, a2: string, a3: string, a4: bool | `void` |
| `MONEY.NETWORK_DEDUCT_CASH` | a1: int, a2: string, a3: string, a4: bool, a5: bool, a6: bool | `void` |
| `MONEY.NETWORK_MONEY_CAN_BET` | a1: int, a2: bool, a3: bool | `bool` |
| `MONEY.NETWORK_CAN_BET` | a: int | `bool` |
| `MONEY.NETWORK_CASINO_CAN_BET` | a: hash | `bool` |
| `MONEY.NETWORK_CASINO_CAN_BET_PVC` | — | `bool` |
| `MONEY.NETWORK_CASINO_CAN_BET_AMOUNT` | a: int | `bool` |
| `MONEY.NETWORK_CASINO_CAN_BUY_CHIPS_PVC` | — | `bool` |
| `MONEY.NETWORK_CASINO_BUY_CHIPS` | a: int, b: int | `bool` |
| `MONEY.NETWORK_CASINO_SELL_CHIPS` | a: int, b: int | `bool` |
| `MONEY.NETWORK_DEFER_CASH_TRANSACTIONS_UNTIL_SHOP_SAVE` | — | `void` |
| `MONEY.CAN_PAY_AMOUNT_TO_BOSS` | a1: int, a2: int, a3: int, a4: pointer | `bool` |
| `MONEY.NETWORK_EARN_FROM_PICKUP` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_CASHING_OUT` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_GANGATTACK_PICKUP` | a: int | `void` |
| `MONEY.NETWORK_EARN_ASSASSINATE_TARGET_KILLED` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_ROB_ARMORED_CARS` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_CRATE_DROP` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_BETTING` | a: int, b: string | `void` |
| `MONEY.NETWORK_EARN_FROM_JOB` | a: int, b: string | `void` |
| `MONEY.NETWORK_EARN_FROM_JOBX2` | a: int, b: string | `void` |
| `MONEY.NETWORK_EARN_FROM_PREMIUM_JOB` | a: int, b: string | `void` |
| `MONEY.NETWORK_EARN_FROM_BEND_JOB` | a: int, b: string | `void` |
| `MONEY.NETWORK_EARN_FROM_CHALLENGE_WIN` | a1: int, a2: pointer, a3: bool | `void` |
| `MONEY.NETWORK_EARN_FROM_BOUNTY` | a1: int, a2: pointer, a3: pointer, a4: int | `void` |
| `MONEY.NETWORK_EARN_FROM_IMPORT_EXPORT` | a: int, b: hash | `void` |
| `MONEY.NETWORK_EARN_FROM_HOLDUPS` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_PROPERTY` | a: int, b: hash | `void` |
| `MONEY.NETWORK_EARN_FROM_AI_TARGET_KILL` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FROM_NOT_BADSPORT` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_VEHICLE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int | `void` |
| `MONEY.NETWORK_EARN_FROM_PERSONAL_VEHICLE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int | `void` |
| `MONEY.NETWORK_EARN_FROM_DAILY_OBJECTIVES` | a1: int, a2: string, a3: int | `void` |
| `MONEY.NETWORK_EARN_FROM_AMBIENT_JOB` | a1: int, a2: string, a3: pointer | `void` |
| `MONEY.NETWORK_EARN_FROM_JOB_BONUS` | a1: int, a2: pointer, a3: pointer | `void` |
| `MONEY.NETWORK_EARN_FROM_CRIMINAL_MASTERMIND` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_HEIST_AWARD` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_FIRST_TIME_BONUS` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_GOON` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_BOSS` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_AGENCY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_FROM_WAREHOUSE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FROM_CONTRABAND` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FROM_DESTROYING_CONTRABAND` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_FROM_SMUGGLER_WORK` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `MONEY.NETWORK_EARN_FROM_HANGAR_TRADE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_PURCHASE_CLUB_HOUSE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FROM_BUSINESS_PRODUCT` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_FROM_VEHICLE_EXPORT` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_SMUGGLER_AGENCY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_BOUNTY_HUNTER_REWARD` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_BUSINESS_BATTLE` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_CLUB_MANAGEMENT_PARTICIPATION` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FROM_FMBB_PHONECALL_MISSION` | a: int | `void` |
| `MONEY.NETWORK_EARN_FROM_BUSINESS_HUB_SELL` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_FROM_FMBB_BOSS_WORK` | a: int | `void` |
| `MONEY.NETWORK_EARN_FMBB_WAGE_BONUS` | a: int | `void` |
| `MONEY.NETWORK_CAN_SPEND_MONEY` | a1: int, a2: bool, a3: bool, a4: bool, a5: int, a6: int | `bool` |
| `MONEY.NETWORK_CAN_SPEND_MONEY2` | a1: int, a2: bool, a3: bool, a4: bool, a5: pointer, a6: int, a7: int | `bool` |
| `MONEY.NETWORK_BUY_ITEM` | a1: int, a2: hash, a3: int, a4: int, a5: bool, a6: string, a7: int, a8: int, a9: int, a10: bool | `void` |
| `MONEY.NETWORK_SPENT_TAXI` | a1: int, a2: bool, a3: bool, a4: int, a5: int | `void` |
| `MONEY.NETWORK_PAY_EMPLOYEE_WAGE` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_PAY_MATCH_ENTRY_FEE` | a1: int, a2: string, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPENT_BETTING` | a1: int, a2: int, a3: string, a4: bool, a5: bool | `void` |
| `MONEY.NETWORK_SPENT_WAGER` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_SPENT_IN_STRIPCLUB` | a1: int, a2: bool, a3: int, a4: bool | `void` |
| `MONEY.NETWORK_BUY_HEALTHCARE` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_BUY_AIRSTRIKE` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_BUY_BACKUP_GANG` | a1: int, a2: int, a3: bool, a4: bool, a5: int | `void` |
| `MONEY.NETWORK_BUY_HELI_STRIKE` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_AMMO_DROP` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_BUY_BOUNTY` | a1: int, a2: int, a3: bool, a4: bool, a5: int | `void` |
| `MONEY.NETWORK_BUY_PROPERTY` | a1: int, a2: hash, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_BUY_SMOKES` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_HELI_PICKUP` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_BOAT_PICKUP` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_BULL_SHARK` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_CASH_DROP` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_HIRE_MUGGER` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_ROBBED_BY_MUGGER` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_HIRE_MERCENARY` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_BUY_WANTEDLEVEL` | a1: int, a2: pointer, a3: bool, a4: bool, a5: int | `void` |
| `MONEY.NETWORK_SPENT_BUY_OFFTHERADAR` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_BUY_REVEAL_PLAYERS` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_CARWASH` | a1: int, a2: int, a3: int, a4: bool, a5: bool | `void` |
| `MONEY.NETWORK_SPENT_CINEMA` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPENT_TELESCOPE` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_HOLDUPS` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_BUY_PASSIVE_MODE` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_BANK_INTEREST` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_PROSTITUTES` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_ARREST_BAIL` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_PAY_VEHICLE_INSURANCE_PREMIUM` | a1: int, a2: hash, a3: pointer, a4: bool, a5: bool | `void` |
| `MONEY.NETWORK_SPENT_CALL_PLAYER` | a1: int, a2: pointer, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPENT_BOUNTY` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_FROM_ROCKSTAR` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPEND_EARNED_FROM_BANK_AND_WALLETS` | a: int | `int` |
| `MONEY.PROCESS_CASH_GIFT` | a1: pointer, a2: pointer, a3: string | `string` |
| `MONEY.NETWORK_SPENT_MOVE_SUBMARINE` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_SPENT_PLAYER_HEALTHCARE` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPENT_NO_COPS` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_CARGO_SOURCING` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `MONEY.NETWORK_SPENT_REQUEST_JOB` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPENT_REQUEST_HEIST` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_BUY_FAIRGROUND_RIDE` | a1: int, a2: int, a3: bool, a4: bool, a5: int | `void` |
| `MONEY.NETWORK_ECONOMY_HAS_FIXED_CRAZY_NUMBERS` | — | `bool` |
| `MONEY.NETWORK_SPENT_JOB_SKIP` | a1: int, a2: string, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPENT_BOSS_GOON` | a1: int, a2: bool, a3: bool | `bool` |
| `MONEY.NETWORK_SPEND_GOON` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_SPEND_BOSS` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_SPENT_MOVE_YACHT` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_RENAME_ORGANIZATION` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_BUY_CONTRABAND_MISSION` | a1: int, a2: int, a3: hash, a4: bool, a5: bool | `void` |
| `MONEY.NETWORK_SPENT_PA_SERVICE_HELI` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PA_SERVICE_VEHICLE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PA_SERVICE_SNACK` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PA_SERVICE_DANCER` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PA_SERVICE_IMPOUND` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_SPENT_PA_HELI_PICKUP` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_OFFICE_PROPERTY` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_OFFICE_PROPERTY` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_WAREHOUSE_PROPERTY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_WAREHOUSE_PROPERTY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_IMPEXP_WAREHOUSE_PROPERTY` | a1: int, a2: pointer, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_IMPEXP_WAREHOUSE_PROPERTY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_TRADE_IMPEXP_WAREHOUSE_PROPERTY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_ORDER_WAREHOUSE_VEHICLE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_ORDER_BODYGUARD_VEHICLE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_JUKEBOX` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_CLUB_HOUSE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_CLUB_HOUSE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_BUSINESS_PROPERTY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_BUSINESS_PROPERTY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_TRADE_BUSINESS_PROPERTY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_MC_ABILITY` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPENT_PAY_BUSINESS_SUPPLIES` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_CHANGE_APPEARANCE` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_SPENT_VEHICLE_EXPORT_MODS` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_OFFICE_GARAGE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_OFFICE_GARAGE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_IMPORT_EXPORT_REPAIR` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_HANGAR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_HANGAR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_HANGAR_UTILITY_CHARGES` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_HANGAR_STAFF_CHARGES` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_BUY_TRUCK` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_TRUCK` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_BUY_BUNKER` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPRADE_BUNKER` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_FROM_SELL_BUNKER` | a: int, b: hash | `void` |
| `MONEY.NETWORK_SPENT_BALLISTIC_EQUIPMENT` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_EARN_RDR_BONUS` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_WAGE_PAYMENT` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_WAGE_PAYMENT_BONUS` | a: int | `void` |
| `MONEY.NETWORK_SPENT_BUY_BASE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_BASE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_BUY_TILTROTOR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_TILTROTOR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_EMPLOY_ASSASSINS` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_GANGOPS_CANNON` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_GANGOPS_SKIP_MISSION` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_CASINO_HEIST_SKIP_MISSION` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_SELL_BASE` | a: int, b: hash | `void` |
| `MONEY.NETWORK_EARN_TARGET_REFUND` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_GANGOPS_WAGES` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_GANGOPS_WAGES_BONUS` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_DAR_CHALLENGE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_DOOMSDAY_FINALE_BONUS` | a: int, b: hash | `void` |
| `MONEY.NETWORK_EARN_GANGOPS_AWARD` | a1: int, a2: string, a3: int | `void` |
| `MONEY.NETWORK_EARN_GANGOPS_ELITE` | a1: int, a2: string, a3: int | `void` |
| `MONEY.NETWORK_SERVICE_EARN_GANGOPS_RIVAL_DELIVERY` | a: int | `void` |
| `MONEY.NETWORK_SPEND_GANGOPS_START_STRAND` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPEND_GANGOPS_TRIP_SKIP` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_EARN_GANGOPS_PREP_PARTICIPATION` | a: int | `void` |
| `MONEY.NETWORK_EARN_GANGOPS_SETUP` | a: int, b: string | `void` |
| `MONEY.NETWORK_EARN_GANGOPS_FINALE` | a: int, b: string | `void` |
| `MONEY.NETWORK_SPEND_GANGOPS_REPAIR_COST` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_NIGHTCLUB` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_NIGHTCLUB_DANCING` | a: int | `void` |
| `MONEY.NETWORK_EARN_BB_EVENT_BONUS` | a: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_HACKER_TRUCK` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_HACKER_TRUCK` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_HACKER_TRUCK` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_PURCHASE_NIGHTCLUB_AND_WAREHOUSE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_UPGRADE_NIGHTCLUB_AND_WAREHOUSE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_NIGHTCLUB_AND_WAREHOUSE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `MONEY.NETWORK_SPEND_NIGHTCLUB_AND_WAREHOUSE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPENT_RDR_HATCHET_BONUS` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_NIGHTCLUB_ENTRY_FEE` | a1: int, a2: int, a3: int, a4: bool, a5: bool | `void` |
| `MONEY.NETWORK_SPEND_NIGHTCLUB_BAR_DRINK` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPEND_BOUNTY_HUNTER_MISSION` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPENT_REHIRE_DJ` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPENT_ARENA_JOIN_SPECTATOR` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_EARN_ARENA_SKILL_LEVEL_PROGRESSION` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_ARENA_CAREER_PROGRESSION` | a: int, b: int | `void` |
| `MONEY.NETWORK_SPEND_MAKE_IT_RAIN` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_SPEND_BUY_ARENA` | a1: int, a2: bool, a3: bool, a4: string | `void` |
| `MONEY.NETWORK_SPEND_UPGRADE_ARENA` | a1: int, a2: bool, a3: bool, a4: string | `void` |
| `MONEY.NETWORK_SPEND_ARENA_SPECTATOR_BOX` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `MONEY.NETWORK_SPEND_SPIN_THE_WHEEL_PAYMENT` | a1: int, a2: int, a3: bool | `void` |
| `MONEY.NETWORK_EARN_SPIN_THE_WHEEL_CASH` | a: int | `void` |
| `MONEY.NETWORK_SPEND_ARENA_PREMIUM` | a1: int, a2: bool, a3: bool | `void` |
| `MONEY.NETWORK_EARN_ARENA_WAR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_ARENA_WAR_ASSASSINATE_TARGET` | a: int | `void` |
| `MONEY.NETWORK_EARN_ARENA_WAR_EVENT_CARGO` | a: int | `void` |
| `MONEY.NETWORK_EARN_RC_TIME_TRIAL` | a: int | `void` |
| `MONEY.NETWORK_EARN_DAILY_OBJECTIVE_EVENT` | a: int | `void` |
| `MONEY.NETWORK_SPEND_CASINO_MEMBERSHIP` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPEND_BUY_CASINO` | a1: int, a2: bool, a3: bool, a4: pointer | `void` |
| `MONEY.NETWORK_SPEND_UPGRADE_CASINO` | a1: int, a2: bool, a3: bool, a4: pointer | `void` |
| `MONEY.NETWORK_SPEND_CASINO_GENERIC` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_TIME_TRIAL_WIN` | a: int | `void` |
| `MONEY.NETWORK_EARN_COLLECTABLES_ACTION_FIGURES` | a: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_COLLECTABLE_COMPLETED_COLLECTION` | a: int | `void` |
| `MONEY.NETWORK_EARN_SELL_PRIZE_VEHICLE` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_MISSION_REWARD` | a: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_STORY_MISSION_REWARD` | a: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_MISSION_PARTICIPATION` | a: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_AWARD` | a: int, b: hash | `void` |
| `MONEY.NETWORK_SPEND_BUY_ARCADE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_UPGRADE_ARCADE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_CASINO_HEIST` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int, a11: int | `void` |
| `MONEY.NETWORK_SPEND_ARCADE_MGMT` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPEND_PLAY_ARCADE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPEND_ARCADE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_HEIST` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `MONEY.NETWORK_EARN_UPGRADE_ARCADE` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_ARCADE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `MONEY.NETWORK_EARN_COLLECTABLES` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_CHALLENGE` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_CASINO_HEIST_AWARDS` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_EARN_COLLECTABLE_ITEM` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_COLLECTABLE_COMPLETED_COLLECTION` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_YATCH_MISSION` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_DISPATCH_CALL` | a: int, b: int | `void` |
| `MONEY.NETWORK_SPEND_BEACH_PARTY` | a: int | `void` |
| `MONEY.NETWORK_SPEND_SUBMARINE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `MONEY.NETWORK_SPEND_CASINO_CLUB` | a1: int, a2: int, a3: bool, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int | `void` |
| `MONEY.NETWORK_SPEND_BUY_SUB` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_UPGRADE_SUB` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_ISLAND_HEIST` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_ISLAND_HEIST` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `MONEY.NETWORK_EARN_BEACH_PARTY_LOST_FOUND` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_FROM_ISLAND_HEIST_DJ_MISSION` | a: int, b: int | `void` |
| `MONEY.NETWORK_SPEND_CAR_CLUB_MEMBERSHIP` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPEND_CAR_CLUB_BAR` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPEND_AUTOSHOP_MODIFY` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPEND_CAR_CLUB_TAKEOVER` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_BUY_AUTOSHOP` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_UPGRADE_AUTOSHOP` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_AUTOSHOP_BUSINESS` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_AUTOSHOP_INCOME` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_CARCLUB_MEMBERSHIP` | a: int | `void` |
| `MONEY.NETWORK_EARN_DAILY_VEHICLE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_DAILY_VEHICLE_BONUS` | a: int | `void` |
| `MONEY.NETWORK_EARN_TUNER_AWARD` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_TUNER_ROBBERY` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_EARN_UPGRADE_AUTOSHOP` | a: int, b: int | `void` |
| `MONEY.NETWORK_SPEND_INTERACTION_MENU_ABILITY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_SET_COMMON_FIELDS` | a1: int, a2: int, a3: int, a4: bool | `void` |
| `MONEY.NETWORK_SPEND_SET_DISCOUNT` | a: bool | `void` |
| `MONEY.NETWORK_SPEND_BUY_AGENCY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_UPGRADE_AGENCY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_AGENCY` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPEND_HIDDEN` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_SOURCE_BIKE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_COMP_SUV` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_SUV_FST_TRVL` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY.NETWORK_SPEND_SUPPLY` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_BIKE_SHOP` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_SPEND_VEHICLE_REQUESTED` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `MONEY.NETWORK_SPEND_GUNRUNNING` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_AGENCY_SAFE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_AWARD_CONTRACT` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_AGENCY_CONTRACT` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_AWARD_PHONE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_AGENCY_PHONE` | a1: int, a2: int, a3: int | `void` |
| `MONEY.NETWORK_EARN_AWARD_FIXER_MISSION` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FIXER_PREP` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FIXER_FINALE` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FIXER_AGENCY_SHORT_TRIP` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_AWARD_SHORT_TRIP` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_FIXER_RIVAL_DELIVERY` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_UPGRADE_AGENCY` | a: int, b: int | `void` |
| `MONEY.NETWORK_SPEND_APARTMENT_UTILITIES` | a1: int, a2: bool, a3: bool, a4: pointer | `void` |
| `MONEY.NETWORK_SPEND_BUSINESS_PROPERTY_FEES` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_SIGHTSEEING_REWARD` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY.NETWORK_EARN_BIKER_SHOP` | a: int, b: int | `void` |
| `MONEY.NETWORK_EARN_BIKER` | a: int | `void` |
| `MONEY.NETWORK_YOHAN_SOURCE_GOODS` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY._NETWORK_SPEND_BUY_MFGARAGE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY._NETWORK_SPEND_UPGRADE_MFGARAGE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY._NETWORK_SPEND_BUY_SUPPLIES` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY._NETWORK_SPEND_BUY_ACID_LAB` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY._NETWORK_SPEND_UPGRADE_ACID_LAB_EQUIPMENT` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY._NETWORK_SPEND_UPGRADE_ACID_LAB_ARMOR` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY._NETWORK_SPEND_UPGRADE_ACID_LAB_SCOOP` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY._NETWORK_SPEND_UPGRADE_ACID_LAB_MINES` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY._NETWORK_SPEND_RENAME_ACID_LAB` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY._NETWORK_SPEND_RENAME_ACID_PRODUCT` | a1: int, a2: int, a3: int, a4: int | `void` |
| `MONEY._NETWORK_EARN_AWARD_JUGGALO_MISSION` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_AWARD_ACID_LAB` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_AWARD_DAILY_STASH` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_AWARD_DEAD_DROP` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_AWARD_RANDOM_EVENT` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_AWARD_TAXI` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_STREET_DEALER` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_SELL_ACID` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_SETUP_PARTICIPATION_ACID_LAB` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_SOURCE_PARTICIPATION_ACID_LAB` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_SELL_PARTICIPATION_ACID_LAB` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_JUGGALO_STORY_MISSION` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_JUGGALO_STORY_MISSION_PARTICIPATION` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_FOOLIGAN_JOB` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_FOOLIGAN_JOB_PARTICIPATION` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_TAXI_JOB` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_DAILY_STASH_HOUSE_COMPLETED` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_DAILY_STASH_HOUSE_PARTICIPATION` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_AVENGER` | a: int, b: int | `void` |
| `MONEY._NETWORK_EARN_SMUGGLER_OPS` | a1: int, a2: int, a3: int | `void` |
| `MONEY._NETWORK_EARN_BONUS_OBJECTIVE` | a1: int, a2: int, a3: int | `void` |
| `MONEY._NETWORK_EARN_PROGRESS_HUB` | a: int, b: int | `void` |
| `MONEY._NETWORK_SPENT_AIR_FREIGHT` | a1: int, a2: bool, a3: bool, a4: int, a5: int, a6: int, a7: int | `void` |
| `MONEY._NETWORK_SPENT_SKIP_CARGO_SOURCE_SETUP` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `MONEY._NETWORK_SPENT_STEALTH_MODULE` | a1: int, a2: bool, a3: bool, a4: hash | `void` |
| `MONEY._NETWORK_SPENT_MISSILE_JAMMER` | a1: int, a2: bool, a3: bool, a4: hash | `void` |
| `MONEY._NETWORK_SPENT_GENERIC` | a1: int, a2: bool, a3: bool, a4: hash, a5: hash, a6: string, a7: string, a8: pointer | `void` |
| `MONEY._NETWORK_EARN_GENERIC` | a1: int, a2: hash, a3: string, a4: string, a5: pointer | `void` |
| `MONEY._NETWORK_CLEAR_TRANSACTION_TELEMETRY_NONCE` | — | `void` |
| `MONEY.NETWORK_GET_VC_BANK_BALANCE` | — | `int` |
| `MONEY.NETWORK_GET_VC_WALLET_BALANCE` | a: int | `int` |
| `MONEY.NETWORK_GET_VC_BALANCE` | — | `int` |
| `MONEY.NETWORK_GET_EVC_BALANCE` | — | `int` |
| `MONEY.NETWORK_GET_PVC_BALANCE` | — | `int` |
| `MONEY.NETWORK_GET_STRING_WALLET_BALANCE` | a: int | `string` |
| `MONEY.NETWORK_GET_STRING_BANK_BALANCE` | — | `string` |
| `MONEY.NETWORK_GET_STRING_BANK_WALLET_BALANCE` | a: int | `string` |
| `MONEY.NETWORK_GET_CAN_SPEND_FROM_WALLET` | a: int, b: int | `bool` |
| `MONEY.NETWORK_GET_CAN_SPEND_FROM_BANK` | a: int | `bool` |
| `MONEY.NETWORK_GET_CAN_SPEND_FROM_BANK_AND_WALLET` | a: int, b: int | `bool` |
| `MONEY.NETWORK_GET_PVC_TRANSFER_BALANCE` | — | `int` |
| `MONEY.NETWORK_GET_CAN_TRANSFER_CASH` | a: int | `bool` |
| `MONEY.NETWORK_CAN_RECEIVE_PLAYER_CASH` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `MONEY.NETWORK_GET_REMAINING_TRANSFER_BALANCE` | — | `int` |
| `MONEY.WITHDRAW_VC` | a: int | `int` |
| `MONEY.DEPOSIT_VC` | a: int | `bool` |
| `MONEY.HAS_VC_WITHDRAWAL_COMPLETED` | a: int | `bool` |
| `MONEY.WAS_VC_WITHDRAWAL_SUCCESSFUL` | a: int | `bool` |
| `MONEY.NETWORK_GET_MP_WINDFALL_AVAILABLE` | — | `bool` |

## NETSHOPPING

| Native | Params | Returns |
| --- | --- | --- |
| `NETSHOPPING.NET_GAMESERVER_USE_SERVER_TRANSACTIONS` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_CATALOG_ITEM_IS_VALID` | a: string | `bool` |
| `NETSHOPPING.NET_GAMESERVER_CATALOG_ITEM_KEY_IS_VALID` | a: hash | `bool` |
| `NETSHOPPING.NET_GAMESERVER_GET_PRICE` | a1: hash, a2: hash, a3: bool | `int` |
| `NETSHOPPING.NET_GAMESERVER_CATALOG_IS_VALID` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_IS_CATALOG_CURRENT` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_GET_CATALOG_CLOUD_CRC` | — | `hash` |
| `NETSHOPPING.NET_GAMESERVER_REFRESH_SERVER_CATALOG` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_RETRIEVE_CATALOG_REFRESH_STATUS` | a: pointer | `bool` |
| `NETSHOPPING.NET_GAMESERVER_INIT_SESSION` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_RETRIEVE_INIT_SESSION_STATUS` | a: pointer | `bool` |
| `NETSHOPPING.NET_GAMESERVER_START_SESSION` | a: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_START_SESSION_PENDING` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_RETRIEVE_START_SESSION_STATUS` | a: pointer | `bool` |
| `NETSHOPPING.NET_GAMESERVER_RETRIEVE_SESSION_ERROR_CODE` | a: pointer | `bool` |
| `NETSHOPPING.NET_GAMESERVER_IS_SESSION_VALID` | a: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_CLEAR_SESSION` | a: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_SESSION_APPLY_RECEIVED_DATA` | a: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_IS_SESSION_REFRESH_PENDING` | — | `bool` |
| `NETSHOPPING._NET_GAMESERVER_FLAG_FOR_SESSION_RESTART` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_START_SESSION_RESTART` | a: bool, b: bool | `bool` |
| `NETSHOPPING.NET_GAMESERVER_TRANSACTION_IN_PROGRESS` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_GET_SESSION_STATE_AND_STATUS` | a: pointer, b: pointer | `bool` |
| `NETSHOPPING.NET_GAMESERVER_BASKET_START` | a1: pointer, a2: hash, a3: hash, a4: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_BASKET_END` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_BASKET_IS_ACTIVE` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_BASKET_ADD_ITEM` | a: pointer, b: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_BASKET_IS_FULL` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_BASKET_APPLY_SERVER_DATA` | a: int, b: pointer | `bool` |
| `NETSHOPPING.NET_GAMESERVER_CHECKOUT_START` | a: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_CHECKOUT_PENDING` | a: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_BEGIN_SERVICE` | a1: pointer, a2: hash, a3: hash, a4: hash, a5: int, a6: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_END_SERVICE` | a: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_DELETE_CHARACTER` | — | blocked: Not allowed |
| `NETSHOPPING.NET_GAMESERVER_DELETE_CHARACTER_GET_STATUS` | — | `int` |
| `NETSHOPPING.NET_GAMESERVER_DELETE_SET_TELEMETRY_NONCE_SEED` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_TRANSFER_BANK_TO_WALLET` | a: int, b: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_TRANSFER_WALLET_TO_BANK` | a: int, b: int | `bool` |
| `NETSHOPPING.NET_GAMESERVER_TRANSFER_BANK_TO_WALLET_GET_STATUS` | — | `int` |
| `NETSHOPPING.NET_GAMESERVER_TRANSFER_WALLET_TO_BANK_GET_STATUS` | — | `int` |
| `NETSHOPPING.NET_GAMESERVER_TRANSFER_CASH_SET_TELEMETRY_NONCE_SEED` | — | `bool` |
| `NETSHOPPING.NET_GAMESERVER_SET_TELEMETRY_NONCE_SEED` | a: int | `bool` |

## NETWORK

| Native | Params | Returns |
| --- | --- | --- |
| `NETWORK.GET_ONLINE_VERSION` | — | `string` |
| `NETWORK.NETWORK_IS_SIGNED_IN` | — | `bool` |
| `NETWORK.NETWORK_IS_SIGNED_ONLINE` | — | `bool` |
| `NETWORK.NETWORK_IS_NP_AVAILABLE` | — | `bool` |
| `NETWORK.NETWORK_IS_NP_PENDING` | — | `bool` |
| `NETWORK.NETWORK_GET_NP_UNAVAILABLE_REASON` | — | `int` |
| `NETWORK.NETWORK_IS_CONNETED_TO_NP_PRESENCE` | — | `bool` |
| `NETWORK.NETWORK_IS_LOGGED_IN_TO_PSN` | — | `bool` |
| `NETWORK.NETWORK_HAS_VALID_ROS_CREDENTIALS` | — | `bool` |
| `NETWORK.NETWORK_IS_REFRESHING_ROS_CREDENTIALS` | — | `bool` |
| `NETWORK.NETWORK_IS_CLOUD_AVAILABLE` | — | `bool` |
| `NETWORK.NETWORK_HAS_SOCIAL_CLUB_ACCOUNT` | — | `bool` |
| `NETWORK.NETWORK_ARE_SOCIAL_CLUB_POLICIES_CURRENT` | — | `bool` |
| `NETWORK.NETWORK_IS_HOST` | — | `bool` |
| `NETWORK.NETWORK_GET_HOST_PLAYER_INDEX` | — | `int` |
| `NETWORK.NETWORK_WAS_GAME_SUSPENDED` | — | `bool` |
| `NETWORK.NETWORK_HAVE_ONLINE_PRIVILEGES` | — | `bool` |
| `NETWORK._NETWORK_MULTIPLAYER_CROSSPLAY_NOT_ALLOWED` | — | `bool` |
| `NETWORK.NETWORK_HAS_AGE_RESTRICTIONS` | — | `bool` |
| `NETWORK.NETWORK_HAVE_USER_CONTENT_PRIVILEGES` | a: int | `bool` |
| `NETWORK.NETWORK_HAVE_COMMUNICATION_PRIVILEGES` | a: int, b: int | `bool` |
| `NETWORK._NETWORK_HAVE_PLATFORM_COMMUNICATION_PRIVILEGES` | — | `bool` |
| `NETWORK.NETWORK_CHECK_ONLINE_PRIVILEGES` | a: int, b: bool | `bool` |
| `NETWORK.NETWORK_CHECK_USER_CONTENT_PRIVILEGES` | a1: int, a2: int, a3: bool | `bool` |
| `NETWORK.NETWORK_CHECK_COMMUNICATION_PRIVILEGES` | a1: int, a2: int, a3: bool | `bool` |
| `NETWORK.NETWORK_CHECK_TEXT_COMMUNICATION_PRIVILEGES` | a1: int, a2: int, a3: int | `bool` |
| `NETWORK.NETWORK_IS_USING_ONLINE_PROMOTION` | — | `bool` |
| `NETWORK.NETWORK_SHOULD_SHOW_PROMOTION_ALERT_SCREEN` | — | `bool` |
| `NETWORK.NETWORK_HAS_SOCIAL_NETWORKING_SHARING_PRIV` | — | `bool` |
| `NETWORK.NETWORK_GET_AGE_GROUP` | — | `int` |
| `NETWORK.NETWORK_CHECK_PRIVILEGES` | a1: int, a2: int, a3: int | `bool` |
| `NETWORK.NETWORK_IS_PRIVILEGE_CHECK_IN_PROGRESS` | — | `bool` |
| `NETWORK.NETWORK_SET_PRIVILEGE_CHECK_RESULT_NOT_NEEDED` | — | `void` |
| `NETWORK.NETWORK_RESOLVE_PRIVILEGE_USER_CONTENT` | — | `bool` |
| `NETWORK.NETWORK_HAVE_PLATFORM_SUBSCRIPTION` | — | `bool` |
| `NETWORK.NETWORK_IS_PLATFORM_SUBSCRIPTION_CHECK_PENDING` | — | `bool` |
| `NETWORK.NETWORK_SHOW_ACCOUNT_UPGRADE_UI` | — | `void` |
| `NETWORK.NETWORK_IS_SHOWING_SYSTEM_UI_OR_RECENTLY_REQUESTED_UPSELL` | — | `bool` |
| `NETWORK.NETWORK_NEED_TO_START_NEW_GAME_BUT_BLOCKED` | — | `bool` |
| `NETWORK.NETWORK_CAN_BAIL` | — | `bool` |
| `NETWORK.NETWORK_BAIL` | a1: int, a2: int, a3: int | `void` |
| `NETWORK.NETWORK_ON_RETURN_TO_SINGLE_PLAYER` | — | `void` |
| `NETWORK.NETWORK_TRANSITION_START` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `NETWORK.NETWORK_TRANSITION_ADD_STAGE` | a1: hash, a2: int, a3: int, a4: int, a5: int | `bool` |
| `NETWORK.NETWORK_TRANSITION_FINISH` | a1: int, a2: int, a3: int | `bool` |
| `NETWORK.NETWORK_CAN_ACCESS_MULTIPLAYER` | a: pointer | `bool` |
| `NETWORK.NETWORK_CHECK_CAN_ACCESS_AND_ALERT` | — | `bool` |
| `NETWORK._NETWORK_GET_ACCESS_CODE_LABEL_HEADING` | a: int | `string` |
| `NETWORK._NETWORK_GET_ACCESS_CODE_LABEL_BODY` | a: int | `string` |
| `NETWORK.NETWORK_IS_MULTIPLAYER_DISABLED` | — | `bool` |
| `NETWORK.NETWORK_CAN_ENTER_MULTIPLAYER` | — | `bool` |
| `NETWORK.NETWORK_SESSION_DO_FREEROAM_QUICKMATCH` | a1: int, a2: int, a3: int | `bool` |
| `NETWORK.NETWORK_SESSION_DO_FRIEND_MATCHMAKING` | a1: int, a2: int, a3: int | `bool` |
| `NETWORK.NETWORK_SESSION_DO_CREW_MATCHMAKING` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `NETWORK.NETWORK_SESSION_DO_ACTIVITY_QUICKMATCH` | a1: int, a2: int, a3: int, a4: int, a5: int | `bool` |
| `NETWORK.NETWORK_SESSION_HOST` | a1: int, a2: int, a3: bool | `bool` |
| `NETWORK.NETWORK_SESSION_HOST_CLOSED` | a: int, b: int | `bool` |
| `NETWORK.NETWORK_SESSION_HOST_FRIENDS_ONLY` | a: int, b: int | `bool` |
| `NETWORK.NETWORK_SESSION_IS_CLOSED_FRIENDS` | — | `bool` |
| `NETWORK.NETWORK_SESSION_IS_CLOSED_CREW` | — | `bool` |
| `NETWORK.NETWORK_SESSION_IS_SOLO` | — | `bool` |
| `NETWORK.NETWORK_SESSION_IS_PRIVATE` | — | `bool` |
| `NETWORK._NETWORK_SESSION_LEAVE_INCLUDING_REASON` | a: int, b: int | `bool` |
| `NETWORK.NETWORK_SESSION_LEAVE` | a: int | `bool` |
| `NETWORK.NETWORK_SESSION_KICK_PLAYER` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_GET_KICK_VOTE` | a: int | `bool` |
| `NETWORK.NETWORK_SESSION_RESERVE_SLOTS_TRANSITION` | a1: int, a2: int, a3: int | `bool` |
| `NETWORK.NETWORK_JOIN_PREVIOUSLY_FAILED_SESSION` | — | `bool` |
| `NETWORK.NETWORK_JOIN_PREVIOUSLY_FAILED_TRANSITION` | — | `bool` |
| `NETWORK.NETWORK_SESSION_SET_MATCHMAKING_GROUP` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_SET_MATCHMAKING_GROUP_MAX` | a: int, b: int | `void` |
| `NETWORK.NETWORK_SESSION_GET_MATCHMAKING_GROUP_FREE` | a: int | `int` |
| `NETWORK.NETWORK_SESSION_ADD_ACTIVE_MATCHMAKING_GROUP` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_SET_UNIQUE_CREW_LIMIT` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_GET_UNIQUE_CREW_LIMIT` | — | `int` |
| `NETWORK.NETWORK_SESSION_SET_UNIQUE_CREW_LIMIT_TRANSITION` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_SET_UNIQUE_CREW_ONLY_CREWS_TRANSITION` | a: bool | `void` |
| `NETWORK.NETWORK_SESSION_SET_CREW_LIMIT_MAX_MEMBERS_TRANSITION` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_SET_MATCHMAKING_PROPERTY_ID` | a: bool | `void` |
| `NETWORK.NETWORK_SESSION_SET_MATCHMAKING_MENTAL_STATE` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_SET_NUM_BOSSES` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_SET_SCRIPT_VALIDATE_JOIN` | — | `void` |
| `NETWORK.NETWORK_SESSION_VALIDATE_JOIN` | a: bool | `void` |
| `NETWORK.NETWORK_ADD_FOLLOWERS` | a: pointer, b: int | `void` |
| `NETWORK.NETWORK_CLEAR_FOLLOWERS` | — | `void` |
| `NETWORK.NETWORK_GET_GLOBAL_MULTIPLAYER_CLOCK` | a1: pointer, a2: pointer, a3: pointer | `void` |
| `NETWORK.NETWORK_SESSION_SET_GAMEMODE` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_GET_HOST_AIM_PREFERENCE` | — | `int` |
| `NETWORK.NETWORK_FIND_GAMERS_IN_CREW` | a: int | `bool` |
| `NETWORK.NETWORK_FIND_MATCHED_GAMERS` | a1: int, a2: float, a3: float, a4: float | `bool` |
| `NETWORK.NETWORK_IS_FINDING_GAMERS` | — | `bool` |
| `NETWORK.NETWORK_DID_FIND_GAMERS_SUCCEED` | — | `bool` |
| `NETWORK.NETWORK_GET_NUM_FOUND_GAMERS` | — | `int` |
| `NETWORK.NETWORK_GET_FOUND_GAMER` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_CLEAR_FOUND_GAMERS` | — | `void` |
| `NETWORK.NETWORK_QUEUE_GAMER_FOR_STATUS` | a: pointer | `bool` |
| `NETWORK.NETWORK_GET_GAMER_STATUS_FROM_QUEUE` | — | `bool` |
| `NETWORK.NETWORK_IS_GETTING_GAMER_STATUS` | — | `bool` |
| `NETWORK.NETWORK_DID_GET_GAMER_STATUS_SUCCEED` | — | `bool` |
| `NETWORK.NETWORK_GET_GAMER_STATUS_RESULT` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_CLEAR_GET_GAMER_STATUS` | — | `void` |
| `NETWORK.NETWORK_SESSION_JOIN_INVITE` | — | `void` |
| `NETWORK.NETWORK_SESSION_CANCEL_INVITE` | — | `void` |
| `NETWORK.NETWORK_SESSION_FORCE_CANCEL_INVITE` | — | `void` |
| `NETWORK.NETWORK_HAS_PENDING_INVITE` | — | `bool` |
| `NETWORK.NETWORK_HAS_CONFIRMED_INVITE` | — | `bool` |
| `NETWORK.NETWORK_REQUEST_INVITE_CONFIRMED_EVENT` | — | `bool` |
| `NETWORK.NETWORK_SESSION_WAS_INVITED` | — | `bool` |
| `NETWORK.NETWORK_SESSION_GET_INVITER` | a: pointer | `void` |
| `NETWORK.NETWORK_SESSION_IS_AWAITING_INVITE_RESPONSE` | — | `bool` |
| `NETWORK.NETWORK_SESSION_IS_DISPLAYING_INVITE_CONFIRMATION` | — | `bool` |
| `NETWORK.NETWORK_SUPPRESS_INVITE` | a: bool | `void` |
| `NETWORK.NETWORK_BLOCK_INVITES` | a: bool | `void` |
| `NETWORK.NETWORK_BLOCK_JOIN_QUEUE_INVITES` | a: bool | `void` |
| `NETWORK.NETWORK_SET_CAN_RECEIVE_RS_INVITES` | a: bool | `void` |
| `NETWORK.NETWORK_STORE_INVITE_THROUGH_RESTART` | — | `void` |
| `NETWORK.NETWORK_ALLOW_INVITE_PROCESS_IN_PLAYER_SWITCH` | a: bool | `void` |
| `NETWORK.NETWORK_SET_SCRIPT_READY_FOR_EVENTS` | a: bool | `void` |
| `NETWORK.NETWORK_IS_OFFLINE_INVITE_PENDING` | — | `bool` |
| `NETWORK.NETWORK_CLEAR_OFFLINE_INVITE_PENDING` | — | `void` |
| `NETWORK._NETWORK_INVITE_GET_JOIN_FAIL_REASON` | — | `string` |
| `NETWORK._NETWORK_INVITE_CLEAR_JOIN_FAIL_REASON` | — | `void` |
| `NETWORK.NETWORK_SESSION_HOST_SINGLE_PLAYER` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_LEAVE_SINGLE_PLAYER` | — | `void` |
| `NETWORK.NETWORK_IS_GAME_IN_PROGRESS` | — | `bool` |
| `NETWORK.NETWORK_IS_SESSION_ACTIVE` | — | `bool` |
| `NETWORK.NETWORK_IS_IN_SESSION` | — | `bool` |
| `NETWORK._NETWORK_IS_AMERICAS_VERSION` | — | `bool` |
| `NETWORK.NETWORK_IS_SESSION_STARTED` | — | `bool` |
| `NETWORK.NETWORK_IS_SESSION_BUSY` | — | `bool` |
| `NETWORK.NETWORK_CAN_SESSION_END` | — | `bool` |
| `NETWORK.NETWORK_GET_GAME_MODE` | — | `int` |
| `NETWORK.NETWORK_SESSION_MARK_VISIBLE` | a: bool | `void` |
| `NETWORK.NETWORK_SESSION_IS_VISIBLE` | — | `bool` |
| `NETWORK.NETWORK_SESSION_BLOCK_JOIN_REQUESTS` | a: bool | `void` |
| `NETWORK.NETWORK_SESSION_CHANGE_SLOTS` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SESSION_GET_PRIVATE_SLOTS` | — | `int` |
| `NETWORK.NETWORK_SESSION_VOICE_HOST` | — | `bool` |
| `NETWORK.NETWORK_SESSION_VOICE_LEAVE` | — | `bool` |
| `NETWORK.NETWORK_SESSION_VOICE_CONNECT_TO_PLAYER` | a: pointer | `void` |
| `NETWORK.NETWORK_SESSION_VOICE_RESPOND_TO_REQUEST` | a: bool, b: int | `void` |
| `NETWORK.NETWORK_SESSION_VOICE_SET_TIMEOUT` | a: int | `void` |
| `NETWORK.NETWORK_SESSION_IS_IN_VOICE_SESSION` | — | `bool` |
| `NETWORK.NETWORK_SESSION_IS_VOICE_SESSION_ACTIVE` | — | `bool` |
| `NETWORK.NETWORK_SESSION_IS_VOICE_SESSION_BUSY` | — | `bool` |
| `NETWORK.NETWORK_SEND_TEXT_MESSAGE` | a: string, b: pointer | `bool` |
| `NETWORK.NETWORK_SET_ACTIVITY_SPECTATOR` | a: bool | `void` |
| `NETWORK.NETWORK_IS_ACTIVITY_SPECTATOR` | — | `bool` |
| `NETWORK.NETWORK_SET_ACTIVITY_PLAYER_MAX` | a: int | `void` |
| `NETWORK.NETWORK_SET_ACTIVITY_SPECTATOR_MAX` | a: int | `void` |
| `NETWORK.NETWORK_GET_ACTIVITY_PLAYER_NUM` | a: bool | `int` |
| `NETWORK.NETWORK_IS_ACTIVITY_SPECTATOR_FROM_HANDLE` | a: pointer | `bool` |
| `NETWORK.NETWORK_HOST_TRANSITION` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: bool, a7: bool, a8: int, a9: int, a10: int | `bool` |
| `NETWORK.NETWORK_DO_TRANSITION_QUICKMATCH` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `bool` |
| `NETWORK.NETWORK_DO_TRANSITION_QUICKMATCH_ASYNC` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `bool` |
| `NETWORK.NETWORK_DO_TRANSITION_QUICKMATCH_WITH_GROUP` | a1: int, a2: int, a3: int, a4: int, a5: pointer, a6: int, a7: int, a8: int | `bool` |
| `NETWORK.NETWORK_JOIN_GROUP_ACTIVITY` | — | `bool` |
| `NETWORK.NETWORK_CLEAR_GROUP_ACTIVITY` | — | `void` |
| `NETWORK.NETWORK_RETAIN_ACTIVITY_GROUP` | — | `void` |
| `NETWORK.NETWORK_IS_TRANSITION_CLOSED_FRIENDS` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_CLOSED_CREW` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_SOLO` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_PRIVATE` | — | `bool` |
| `NETWORK.NETWORK_GET_NUM_TRANSITION_NON_ASYNC_GAMERS` | — | `int` |
| `NETWORK.NETWORK_MARK_AS_PREFERRED_ACTIVITY` | a: bool | `void` |
| `NETWORK.NETWORK_MARK_AS_WAITING_ASYNC` | a: bool | `void` |
| `NETWORK.NETWORK_SET_IN_PROGRESS_FINISH_TIME` | a: int | `void` |
| `NETWORK.NETWORK_SET_TRANSITION_CREATOR_HANDLE` | a: pointer | `void` |
| `NETWORK.NETWORK_CLEAR_TRANSITION_CREATOR_HANDLE` | — | `void` |
| `NETWORK.NETWORK_INVITE_GAMERS_TO_TRANSITION` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_SET_GAMER_INVITED_TO_TRANSITION` | a: pointer | `void` |
| `NETWORK.NETWORK_LEAVE_TRANSITION` | — | `bool` |
| `NETWORK.NETWORK_LAUNCH_TRANSITION` | — | `bool` |
| `NETWORK.NETWORK_SET_DO_NOT_LAUNCH_FROM_JOIN_AS_MIGRATED_HOST` | a: bool | `void` |
| `NETWORK.NETWORK_CANCEL_TRANSITION_MATCHMAKING` | — | `void` |
| `NETWORK.NETWORK_BAIL_TRANSITION` | a1: int, a2: int, a3: int | `void` |
| `NETWORK.NETWORK_DO_TRANSITION_TO_GAME` | a: bool, b: int | `bool` |
| `NETWORK.NETWORK_DO_TRANSITION_TO_NEW_GAME` | a1: bool, a2: int, a3: bool | `bool` |
| `NETWORK.NETWORK_DO_TRANSITION_TO_FREEMODE` | a1: pointer, a2: int, a3: bool, a4: int, a5: bool | `bool` |
| `NETWORK.NETWORK_DO_TRANSITION_TO_NEW_FREEMODE` | a1: pointer, a2: int, a3: int, a4: bool, a5: bool, a6: bool | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_TO_GAME` | — | `bool` |
| `NETWORK.NETWORK_GET_TRANSITION_MEMBERS` | a: pointer, b: int | `int` |
| `NETWORK.NETWORK_APPLY_TRANSITION_PARAMETER` | a: int, b: int | `void` |
| `NETWORK.NETWORK_APPLY_TRANSITION_PARAMETER_STRING` | a1: int, a2: string, a3: bool | `void` |
| `NETWORK.NETWORK_SEND_TRANSITION_GAMER_INSTRUCTION` | a1: pointer, a2: string, a3: int, a4: int, a5: bool | `bool` |
| `NETWORK.NETWORK_MARK_TRANSITION_GAMER_AS_FULLY_JOINED` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_HOST` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_HOST_FROM_HANDLE` | a: pointer | `bool` |
| `NETWORK.NETWORK_GET_TRANSITION_HOST` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_IN_TRANSITION` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_STARTED` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_BUSY` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_MATCHMAKING` | — | `bool` |
| `NETWORK.NETWORK_IS_TRANSITION_LEAVE_POSTPONED` | — | `bool` |
| `NETWORK.NETWORK_TRANSITION_SET_IN_PROGRESS` | a: int | `void` |
| `NETWORK.NETWORK_TRANSITION_SET_CONTENT_CREATOR` | a: int | `void` |
| `NETWORK.NETWORK_TRANSITION_SET_ACTIVITY_ISLAND` | a: int | `void` |
| `NETWORK.NETWORK_OPEN_TRANSITION_MATCHMAKING` | — | `void` |
| `NETWORK.NETWORK_CLOSE_TRANSITION_MATCHMAKING` | — | `void` |
| `NETWORK.NETWORK_IS_TRANSITION_OPEN_TO_MATCHMAKING` | — | `bool` |
| `NETWORK.NETWORK_SET_TRANSITION_VISIBILITY_LOCK` | a: bool, b: bool | `void` |
| `NETWORK.NETWORK_IS_TRANSITION_VISIBILITY_LOCKED` | — | `bool` |
| `NETWORK.NETWORK_SET_TRANSITION_ACTIVITY_ID` | a: int | `void` |
| `NETWORK.NETWORK_CHANGE_TRANSITION_SLOTS` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_TRANSITION_BLOCK_JOIN_REQUESTS` | a: bool | `void` |
| `NETWORK.NETWORK_HAS_PLAYER_STARTED_TRANSITION` | a: int | `bool` |
| `NETWORK.NETWORK_ARE_TRANSITION_DETAILS_VALID` | a: int | `bool` |
| `NETWORK.NETWORK_JOIN_TRANSITION` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_INVITED_GAMER_TO_TRANSITION` | a: pointer | `bool` |
| `NETWORK.NETWORK_HAS_TRANSITION_INVITE_BEEN_ACKED` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_ACTIVITY_SESSION` | — | `bool` |
| `NETWORK.NETWORK_DISABLE_REALTIME_MULTIPLAYER` | — | `void` |
| `NETWORK.NETWORK_OVERRIDE_REALTIME_MULTIPLAYER_CONTROL_DISABLE` | — | `void` |
| `NETWORK.NETWORK_DISABLE_REALTIME_MULTIPLAYER_SPECTATOR` | — | `void` |
| `NETWORK.NETWORK_SET_PRESENCE_SESSION_INVITES_BLOCKED` | a: bool | `void` |
| `NETWORK.NETWORK_SEND_INVITE_VIA_PRESENCE` | a1: pointer, a2: string, a3: int, a4: int | `bool` |
| `NETWORK.NETWORK_SEND_TRANSITION_INVITE_VIA_PRESENCE` | a1: pointer, a2: string, a3: int, a4: int | `bool` |
| `NETWORK.ION_INVITE_VIA_PRESENCE` | a1: pointer, a2: string, a3: int, a4: int | `bool` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_INDEX_BY_ID` | a: int | `int` |
| `NETWORK.NETWORK_GET_NUM_PRESENCE_INVITES` | — | `int` |
| `NETWORK.NETWORK_ACCEPT_PRESENCE_INVITE` | a: int | `bool` |
| `NETWORK.NETWORK_REMOVE_PRESENCE_INVITE` | a: int | `bool` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_ID` | a: int | `int` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_INVITER` | a: int | `string` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_HANDLE` | a: int, b: pointer | `bool` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_SESSION_ID` | a: int | `int` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_CONTENT_ID` | a: int | `string` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_PLAYLIST_LENGTH` | a: int | `int` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_PLAYLIST_CURRENT` | a: int | `int` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_FROM_ADMIN` | a: int | `bool` |
| `NETWORK.NETWORK_GET_PRESENCE_INVITE_IS_TOURNAMENT` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_FOLLOW_INVITE` | — | `bool` |
| `NETWORK.NETWORK_ACTION_FOLLOW_INVITE` | — | `bool` |
| `NETWORK.NETWORK_CLEAR_FOLLOW_INVITE` | — | `bool` |
| `NETWORK.NETWORK_REMOVE_AND_CANCEL_ALL_INVITES` | — | `void` |
| `NETWORK.NETWORK_REMOVE_TRANSITION_INVITE` | a: pointer | `void` |
| `NETWORK.NETWORK_REMOVE_ALL_TRANSITION_INVITE` | — | `void` |
| `NETWORK.NETWORK_REMOVE_AND_CANCEL_ALL_TRANSITION_INVITES` | — | `void` |
| `NETWORK.NETWORK_INVITE_GAMERS` | a1: pointer, a2: int, a3: pointer, a4: int | `bool` |
| `NETWORK.NETWORK_HAS_INVITED_GAMER` | a: pointer | `bool` |
| `NETWORK.NETWORK_HAS_MADE_INVITE_DECISION` | a: pointer | `bool` |
| `NETWORK.NETWORK_GET_INVITE_REPLY_STATUS` | a: int | `int` |
| `NETWORK.NETWORK_GET_CURRENTLY_SELECTED_GAMER_HANDLE_FROM_INVITE_MENU` | a: pointer | `bool` |
| `NETWORK.NETWORK_SET_CURRENTLY_SELECTED_GAMER_HANDLE_FROM_INVITE_MENU` | a: pointer | `bool` |
| `NETWORK.NETWORK_SET_INVITE_ON_CALL_FOR_INVITE_MENU` | a: pointer | `void` |
| `NETWORK.NETWORK_CHECK_DATA_MANAGER_SUCCEEDED_FOR_HANDLE` | a: int, b: pointer | `bool` |
| `NETWORK.NETWORK_CHECK_DATA_MANAGER_FOR_HANDLE` | a: int, b: pointer | `bool` |
| `NETWORK.NETWORK_SET_INVITE_FAILED_MESSAGE_FOR_INVITE_MENU` | a: pointer, b: pointer | `void` |
| `NETWORK.FILLOUT_PM_PLAYER_LIST` | a1: pointer, a2: int, a3: int | `bool` |
| `NETWORK.FILLOUT_PM_PLAYER_LIST_WITH_NAMES` | a1: pointer, a2: pointer, a3: int, a4: int | `bool` |
| `NETWORK.REFRESH_PLAYER_LIST_STATS` | a: int | `bool` |
| `NETWORK.NETWORK_SET_CURRENT_DATA_MANAGER_HANDLE` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_IN_PLATFORM_PARTY` | — | `bool` |
| `NETWORK.NETWORK_GET_PLATFORM_PARTY_MEMBER_COUNT` | — | `int` |
| `NETWORK.NETWORK_GET_PLATFORM_PARTY_MEMBERS` | a: pointer, b: int | `int` |
| `NETWORK.NETWORK_IS_IN_PLATFORM_PARTY_CHAT` | — | `bool` |
| `NETWORK.NETWORK_IS_CHATTING_IN_PLATFORM_PARTY` | a: pointer | `bool` |
| `NETWORK.NETWORK_CAN_QUEUE_FOR_PREVIOUS_SESSION_JOIN` | — | `bool` |
| `NETWORK.NETWORK_IS_QUEUING_FOR_SESSION_JOIN` | — | `bool` |
| `NETWORK.NETWORK_CLEAR_QUEUED_JOIN_REQUEST` | — | `void` |
| `NETWORK.NETWORK_SEND_QUEUED_JOIN_REQUEST` | — | `void` |
| `NETWORK.NETWORK_REMOVE_ALL_QUEUED_JOIN_REQUESTS` | — | `void` |
| `NETWORK.NETWORK_SEED_RANDOM_NUMBER_GENERATOR` | a: int | `void` |
| `NETWORK.NETWORK_GET_RANDOM_INT` | — | `int` |
| `NETWORK.NETWORK_GET_RANDOM_INT_RANGED` | a: int, b: int | `int` |
| `NETWORK._NETWORK_GET_RANDOM_FLOAT_RANGED` | a: float, b: float | `float` |
| `NETWORK.NETWORK_PLAYER_IS_CHEATER` | — | `bool` |
| `NETWORK.NETWORK_PLAYER_GET_CHEATER_REASON` | — | `int` |
| `NETWORK.NETWORK_PLAYER_IS_BADSPORT` | — | `bool` |
| `NETWORK.REMOTE_CHEATER_PLAYER_DETECTED` | a1: int, a2: int, a3: int | `bool` |
| `NETWORK.BAD_SPORT_PLAYER_LEFT_DETECTED` | a1: pointer, a2: int, a3: int | `bool` |
| `NETWORK.NETWORK_ADD_INVALID_OBJECT_MODEL` | a: hash, b: int | `void` |
| `NETWORK.NETWORK_REMOVE_INVALID_OBJECT_MODEL` | a: hash | `void` |
| `NETWORK.NETWORK_CLEAR_INVALID_OBJECT_MODELS` | — | `void` |
| `NETWORK.NETWORK_APPLY_PED_SCAR_DATA` | a: int, b: int | `void` |
| `NETWORK.NETWORK_SET_THIS_SCRIPT_IS_NETWORK_SCRIPT` | a1: int, a2: bool, a3: int | `void` |
| `NETWORK.NETWORK_TRY_TO_SET_THIS_SCRIPT_IS_NETWORK_SCRIPT` | a1: int, a2: bool, a3: int | `bool` |
| `NETWORK.NETWORK_GET_THIS_SCRIPT_IS_NETWORK_SCRIPT` | — | `bool` |
| `NETWORK.NETWORK_GET_MAX_NUM_PARTICIPANTS` | — | `int` |
| `NETWORK.NETWORK_GET_NUM_PARTICIPANTS` | — | `int` |
| `NETWORK.NETWORK_GET_SCRIPT_STATUS` | — | `int` |
| `NETWORK.NETWORK_REGISTER_HOST_BROADCAST_VARIABLES` | a1: pointer, a2: int, a3: string | `void` |
| `NETWORK.NETWORK_REGISTER_PLAYER_BROADCAST_VARIABLES` | a1: pointer, a2: int, a3: string | `void` |
| `NETWORK.NETWORK_REGISTER_HIGH_FREQUENCY_HOST_BROADCAST_VARIABLES` | a1: int, a2: int, a3: int | `void` |
| `NETWORK.NETWORK_REGISTER_HIGH_FREQUENCY_PLAYER_BROADCAST_VARIABLES` | a1: int, a2: int, a3: int | `void` |
| `NETWORK.NETWORK_FINISH_BROADCASTING_DATA` | — | `void` |
| `NETWORK._NETWORK_GET_HOST_BROADCAST_DATA_SIZE_UNSYNCED` | a1: hash, a2: int, a3: hash, a4: int | `int` |
| `NETWORK._NETWORK_GET_PLAYER_BROADCAST_DATA_SIZE_UNSYNCED` | a1: hash, a2: int, a3: hash, a4: int | `int` |
| `NETWORK._NETWORK_GET_BROADCAST_DATA_HOST_UPDATE_SIZE` | a1: hash, a2: int, a3: hash, a4: int | `int` |
| `NETWORK._NETWORK_GET_BROADCAST_DATA_PLAYER_UPDATE_SIZE` | a1: hash, a2: int, a3: hash, a4: int | `int` |
| `NETWORK.NETWORK_HAS_RECEIVED_HOST_BROADCAST_DATA` | — | `bool` |
| `NETWORK.NETWORK_GET_PLAYER_INDEX` | a: int | `int` |
| `NETWORK.NETWORK_GET_PARTICIPANT_INDEX` | a: int | `int` |
| `NETWORK.NETWORK_GET_PLAYER_INDEX_FROM_PED` | a: int | `int` |
| `NETWORK.NETWORK_GET_NUM_CONNECTED_PLAYERS` | — | `int` |
| `NETWORK.NETWORK_IS_PLAYER_CONNECTED` | a: int | `bool` |
| `NETWORK.NETWORK_GET_TOTAL_NUM_PLAYERS` | — | `int` |
| `NETWORK.NETWORK_IS_PARTICIPANT_ACTIVE` | a: int | `bool` |
| `NETWORK.NETWORK_IS_PLAYER_ACTIVE` | a: int | `bool` |
| `NETWORK.NETWORK_IS_PLAYER_A_PARTICIPANT` | a: int | `bool` |
| `NETWORK.NETWORK_IS_HOST_OF_THIS_SCRIPT` | — | `bool` |
| `NETWORK.NETWORK_GET_HOST_OF_THIS_SCRIPT` | — | `int` |
| `NETWORK.NETWORK_GET_HOST_OF_SCRIPT` | a1: string, a2: int, a3: int | `int` |
| `NETWORK.NETWORK_GET_HOST_OF_THREAD` | a: int | `int` |
| `NETWORK.NETWORK_SET_MISSION_FINISHED` | — | `void` |
| `NETWORK.NETWORK_IS_SCRIPT_ACTIVE` | a1: string, a2: int, a3: bool, a4: int | `bool` |
| `NETWORK.NETWORK_IS_SCRIPT_ACTIVE_BY_HASH` | a1: hash, a2: int, a3: bool, a4: int | `bool` |
| `NETWORK.NETWORK_IS_THREAD_A_NETWORK_SCRIPT` | a: int | `bool` |
| `NETWORK.NETWORK_GET_NUM_SCRIPT_PARTICIPANTS` | a1: string, a2: int, a3: int | `int` |
| `NETWORK.NETWORK_GET_INSTANCE_ID_OF_THIS_SCRIPT` | — | `int` |
| `NETWORK.NETWORK_GET_POSITION_HASH_OF_THIS_SCRIPT` | — | `hash` |
| `NETWORK.NETWORK_IS_PLAYER_A_PARTICIPANT_ON_SCRIPT` | a1: int, a2: string, a3: int | `bool` |
| `NETWORK.NETWORK_PREVENT_SCRIPT_HOST_MIGRATION` | — | `void` |
| `NETWORK.NETWORK_REQUEST_TO_BE_HOST_OF_THIS_SCRIPT` | — | `void` |
| `NETWORK.PARTICIPANT_ID` | — | `int` |
| `NETWORK.PARTICIPANT_ID_TO_INT` | — | `int` |
| `NETWORK.NETWORK_GET_KILLER_OF_PLAYER` | a: int, b: pointer | `int` |
| `NETWORK.NETWORK_GET_DESTROYER_OF_NETWORK_ID` | a: int, b: pointer | `int` |
| `NETWORK.NETWORK_GET_DESTROYER_OF_ENTITY` | a: int, b: pointer | `int` |
| `NETWORK.NETWORK_GET_ASSISTED_KILL_OF_ENTITY` | a1: int, a2: int, a3: pointer | `bool` |
| `NETWORK.NETWORK_GET_ASSISTED_DAMAGE_OF_ENTITY` | a1: int, a2: int, a3: pointer | `bool` |
| `NETWORK.NETWORK_GET_ENTITY_KILLER_OF_PLAYER` | a: int, b: pointer | `int` |
| `NETWORK.NETWORK_SET_CURRENT_PUBLIC_CONTENT_ID` | a: string | `void` |
| `NETWORK.NETWORK_SET_CURRENT_CHAT_OPTION` | a: int | `void` |
| `NETWORK.NETWORK_SET_CURRENT_SPAWN_LOCATION_OPTION` | a: hash | `void` |
| `NETWORK.NETWORK_SET_VEHICLE_DRIVEN_IN_TEST_DRIVE` | a: bool | `void` |
| `NETWORK.NETWORK_SET_VEHICLE_DRIVEN_LOCATION` | a: hash | `void` |
| `NETWORK.NETWORK_RESURRECT_LOCAL_PLAYER` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: int, a9: int | `void` |
| `NETWORK.NETWORK_SET_LOCAL_PLAYER_INVINCIBLE_TIME` | a: int | `void` |
| `NETWORK.NETWORK_IS_LOCAL_PLAYER_INVINCIBLE` | — | `bool` |
| `NETWORK.NETWORK_DISABLE_INVINCIBLE_FLASHING` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_PATCH_POST_CUTSCENE_HS4F_TUN_ENT` | a: int | `void` |
| `NETWORK.NETWORK_SET_LOCAL_PLAYER_SYNC_LOOK_AT` | a: bool | `void` |
| `NETWORK.NETWORK_HAS_ENTITY_BEEN_REGISTERED_WITH_THIS_THREAD` | a: int | `bool` |
| `NETWORK.NETWORK_GET_NETWORK_ID_FROM_ENTITY` | a: int | `int` |
| `NETWORK.NETWORK_GET_ENTITY_FROM_NETWORK_ID` | a: int | `int` |
| `NETWORK.NETWORK_GET_ENTITY_IS_NETWORKED` | a: int | `bool` |
| `NETWORK.NETWORK_GET_ENTITY_IS_LOCAL` | a: int | `bool` |
| `NETWORK.NETWORK_REGISTER_ENTITY_AS_NETWORKED` | a: int | `void` |
| `NETWORK.NETWORK_UNREGISTER_NETWORKED_ENTITY` | a: int | `void` |
| `NETWORK.NETWORK_DOES_NETWORK_ID_EXIST` | a: int | `bool` |
| `NETWORK.NETWORK_DOES_ENTITY_EXIST_WITH_NETWORK_ID` | a: int | `bool` |
| `NETWORK.NETWORK_REQUEST_CONTROL_OF_NETWORK_ID` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_CONTROL_OF_NETWORK_ID` | a: int | `bool` |
| `NETWORK.NETWORK_IS_NETWORK_ID_REMOTELY_CONTROLLED` | a: int | `bool` |
| `NETWORK.NETWORK_REQUEST_CONTROL_OF_ENTITY` | a: int | `bool` |
| `NETWORK.NETWORK_REQUEST_CONTROL_OF_DOOR` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_CONTROL_OF_ENTITY` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_CONTROL_OF_PICKUP` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_CONTROL_OF_DOOR` | a: hash | `bool` |
| `NETWORK.NETWORK_IS_DOOR_NETWORKED` | a: hash | `bool` |
| `NETWORK.VEH_TO_NET` | a: int | `int` |
| `NETWORK.PED_TO_NET` | a: int | `int` |
| `NETWORK.OBJ_TO_NET` | a: int | `int` |
| `NETWORK.NET_TO_VEH` | a: int | `int` |
| `NETWORK.NET_TO_PED` | a: int | `int` |
| `NETWORK.NET_TO_OBJ` | a: int | `int` |
| `NETWORK.NET_TO_ENT` | a: int | `int` |
| `NETWORK.NETWORK_GET_LOCAL_HANDLE` | a: pointer, b: int | `void` |
| `NETWORK.NETWORK_HANDLE_FROM_USER_ID` | a1: string, a2: pointer, a3: int | `void` |
| `NETWORK.NETWORK_HANDLE_FROM_MEMBER_ID` | a1: string, a2: pointer, a3: int | `void` |
| `NETWORK.NETWORK_HANDLE_FROM_PLAYER` | a1: int, a2: pointer, a3: int | `void` |
| `NETWORK.NETWORK_HASH_FROM_PLAYER_HANDLE` | a: int | `hash` |
| `NETWORK.NETWORK_HASH_FROM_GAMER_HANDLE` | a: pointer | `hash` |
| `NETWORK.NETWORK_HANDLE_FROM_FRIEND` | a1: int, a2: pointer, a3: int | `void` |
| `NETWORK.NETWORK_GAMERTAG_FROM_HANDLE_START` | a: pointer | `bool` |
| `NETWORK.NETWORK_GAMERTAG_FROM_HANDLE_PENDING` | — | `bool` |
| `NETWORK.NETWORK_GAMERTAG_FROM_HANDLE_SUCCEEDED` | — | `bool` |
| `NETWORK.NETWORK_GET_GAMERTAG_FROM_HANDLE` | a: pointer | `string` |
| `NETWORK.NETWORK_DISPLAYNAMES_FROM_HANDLES_START` | a: pointer, b: int | `int` |
| `NETWORK.NETWORK_GET_DISPLAYNAMES_FROM_HANDLES` | a1: int, a2: int, a3: int | `int` |
| `NETWORK.NETWORK_ARE_HANDLES_THE_SAME` | a: pointer, b: pointer | `bool` |
| `NETWORK.NETWORK_IS_HANDLE_VALID` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_GET_PLAYER_FROM_GAMER_HANDLE` | a: pointer | `int` |
| `NETWORK.NETWORK_MEMBER_ID_FROM_GAMER_HANDLE` | a: pointer | `string` |
| `NETWORK.NETWORK_IS_GAMER_IN_MY_SESSION` | a: pointer | `bool` |
| `NETWORK.NETWORK_SHOW_PROFILE_UI` | a: pointer | `void` |
| `NETWORK.NETWORK_PLAYER_GET_NAME` | a: int | `string` |
| `NETWORK.NETWORK_PLAYER_GET_USERID` | a: int, b: pointer | `string` |
| `NETWORK.NETWORK_PLAYER_IS_ROCKSTAR_DEV` | a: int | `bool` |
| `NETWORK.NETWORK_PLAYER_INDEX_IS_CHEATER` | a: int | `bool` |
| `NETWORK.NETWORK_ENTITY_GET_OBJECT_ID` | a: int | `int` |
| `NETWORK.NETWORK_GET_ENTITY_FROM_OBJECT_ID` | a: int | `int` |
| `NETWORK.NETWORK_IS_INACTIVE_PROFILE` | a: pointer | `bool` |
| `NETWORK._NETWORK_LOAD_GAMER_DISPLAY_NAME` | a: pointer | `string` |
| `NETWORK.NETWORK_GET_MAX_FRIENDS` | — | `int` |
| `NETWORK.NETWORK_GET_FRIEND_COUNT` | — | `int` |
| `NETWORK.NETWORK_GET_FRIEND_NAME` | a: int | `string` |
| `NETWORK.NETWORK_GET_FRIEND_DISPLAY_NAME` | a: int | `string` |
| `NETWORK.NETWORK_IS_FRIEND_ONLINE` | a: string | `bool` |
| `NETWORK.NETWORK_IS_FRIEND_HANDLE_ONLINE` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_FRIEND_IN_SAME_TITLE` | a: string | `bool` |
| `NETWORK.NETWORK_IS_FRIEND_IN_MULTIPLAYER` | a: string | `bool` |
| `NETWORK.NETWORK_IS_FRIEND` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_PENDING_FRIEND` | a: int | `bool` |
| `NETWORK.NETWORK_IS_ADDING_FRIEND` | — | `bool` |
| `NETWORK.NETWORK_ADD_FRIEND` | a: pointer, b: string | `bool` |
| `NETWORK.NETWORK_IS_FRIEND_INDEX_ONLINE` | a: int | `bool` |
| `NETWORK.NETWORK_SET_PLAYER_IS_PASSIVE` | a: bool | `void` |
| `NETWORK.NETWORK_GET_PLAYER_OWNS_WAYPOINT` | a: int | `bool` |
| `NETWORK.NETWORK_CAN_SET_WAYPOINT` | — | `bool` |
| `NETWORK.NETWORK_IGNORE_REMOTE_WAYPOINTS` | — | `void` |
| `NETWORK._NETWORK_DOES_COMMUNICATION_GROUP_HAVE_PERMISSION` | a: int | `bool` |
| `NETWORK._NETWORK_DOES_COMMUNICATION_GROUP_HAVE_SETTINGS_ENABLED` | a: int | `bool` |
| `NETWORK._NETWORK_GET_COMMUNICATION_GROUP_FLAGS` | a: int | `int` |
| `NETWORK._NETWORK_GET_COMMUNICATION_GROUP_DEFAULT_FLAGS` | a: int | `int` |
| `NETWORK._NETWORK_GET_COMMUNICATION_GROUP_VALUE` | a: int | `int` |
| `NETWORK._NETWORK_SET_COMMUNICATION_GROUP_FLAGS` | a: int, b: int | `void` |
| `NETWORK._NETWORK_HAS_PLAYER_PASSED_CHECK_TYPE` | a: int, b: pointer | `bool` |
| `NETWORK.NETWORK_IS_PLAYER_ON_BLOCKLIST` | a: pointer | `bool` |
| `NETWORK.NETWORK_SET_SCRIPT_AUTOMUTED` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_AUTOMUTE_OVERRIDE` | — | `bool` |
| `NETWORK.NETWORK_HAS_HEADSET` | — | `bool` |
| `NETWORK.NETWORK_SET_LOOK_AT_TALKERS` | a: bool | `void` |
| `NETWORK.NETWORK_IS_PUSH_TO_TALK_ACTIVE` | — | `bool` |
| `NETWORK.NETWORK_GAMER_HAS_HEADSET` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_GAMER_TALKING` | a: pointer | `bool` |
| `NETWORK.NETWORK_PERMISSIONS_HAS_GAMER_RECORD` | a: pointer | `bool` |
| `NETWORK.NETWORK_CAN_COMMUNICATE_WITH_GAMER` | a: pointer | `bool` |
| `NETWORK.NETWORK_CAN_TEXT_CHAT_WITH_GAMER` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_GAMER_MUTED_BY_ME` | a: pointer | `bool` |
| `NETWORK.NETWORK_AM_I_MUTED_BY_GAMER` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_GAMER_BLOCKED_BY_ME` | a: pointer | `bool` |
| `NETWORK.NETWORK_AM_I_BLOCKED_BY_GAMER` | a: pointer | `bool` |
| `NETWORK.NETWORK_CAN_VIEW_GAMER_USER_CONTENT` | a: pointer | `bool` |
| `NETWORK.NETWORK_HAS_VIEW_GAMER_USER_CONTENT_RESULT` | a: pointer | `bool` |
| `NETWORK._NETWORK_CAN_TEXT_FROM_GAMER_BE_VIEWED` | a: pointer | `bool` |
| `NETWORK.NETWORK_CAN_PLAY_MULTIPLAYER_WITH_GAMER` | a: pointer | `bool` |
| `NETWORK.NETWORK_CAN_GAMER_PLAY_MULTIPLAYER_WITH_ME` | a: pointer | `bool` |
| `NETWORK.NETWORK_CAN_SEND_LOCAL_INVITE` | a: ", a2: ", a3: pointer | `bool` |
| `NETWORK.NETWORK_CAN_RECEIVE_LOCAL_INVITE` | a: pointer | `bool` |
| `NETWORK.NETWORK_IS_PLAYER_TALKING` | a: int | `bool` |
| `NETWORK.NETWORK_PLAYER_HAS_HEADSET` | a: int | `bool` |
| `NETWORK.NETWORK_IS_PLAYER_MUTED_BY_ME` | a: int | `bool` |
| `NETWORK.NETWORK_AM_I_MUTED_BY_PLAYER` | a: int | `bool` |
| `NETWORK.NETWORK_IS_PLAYER_BLOCKED_BY_ME` | a: int | `bool` |
| `NETWORK.NETWORK_AM_I_BLOCKED_BY_PLAYER` | a: int | `bool` |
| `NETWORK.NETWORK_GET_PLAYER_LOUDNESS` | a: int | `float` |
| `NETWORK.NETWORK_SET_TALKER_PROXIMITY` | a: float | `void` |
| `NETWORK.NETWORK_GET_TALKER_PROXIMITY` | — | `float` |
| `NETWORK.NETWORK_SET_VOICE_ACTIVE` | a: bool | `void` |
| `NETWORK.NETWORK_REMAIN_IN_GAME_CHAT` | a: bool | `void` |
| `NETWORK.NETWORK_OVERRIDE_TRANSITION_CHAT` | a: bool | `void` |
| `NETWORK.NETWORK_SET_TEAM_ONLY_CHAT` | a: bool | `void` |
| `NETWORK.NETWORK_SET_SCRIPT_CONTROLLING_TEAMS` | a: int | `void` |
| `NETWORK.NETWORK_SET_SAME_TEAM_AS_LOCAL_PLAYER` | a: int, b: int | `bool` |
| `NETWORK.NETWORK_OVERRIDE_TEAM_RESTRICTIONS` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SET_OVERRIDE_SPECTATOR_MODE` | a: bool | `void` |
| `NETWORK.NETWORK_SET_OVERRIDE_TUTORIAL_SESSION_CHAT` | a: bool | `void` |
| `NETWORK.NETWORK_SET_PROXIMITY_AFFECTS_TEAM` | a: bool | `void` |
| `NETWORK.NETWORK_SET_NO_SPECTATOR_CHAT` | a: bool | `void` |
| `NETWORK.NETWORK_SET_IGNORE_SPECTATOR_CHAT_LIMITS_SAME_TEAM` | a: bool | `void` |
| `NETWORK.NETWORK_OVERRIDE_CHAT_RESTRICTIONS` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_OVERRIDE_SEND_RESTRICTIONS` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_OVERRIDE_SEND_RESTRICTIONS_ALL` | a: bool | `void` |
| `NETWORK.NETWORK_OVERRIDE_RECEIVE_RESTRICTIONS` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_OVERRIDE_RECEIVE_RESTRICTIONS_ALL` | a: bool | `void` |
| `NETWORK.NETWORK_SET_VOICE_CHANNEL` | a: int | `void` |
| `NETWORK.NETWORK_CLEAR_VOICE_CHANNEL` | — | `void` |
| `NETWORK.NETWORK_APPLY_VOICE_PROXIMITY_OVERRIDE` | a1: float, a2: float, a3: float | `void` |
| `NETWORK.NETWORK_CLEAR_VOICE_PROXIMITY_OVERRIDE` | — | `void` |
| `NETWORK.NETWORK_ENABLE_VOICE_BANDWIDTH_RESTRICTION` | a: int | `void` |
| `NETWORK.NETWORK_DISABLE_VOICE_BANDWIDTH_RESTRICTION` | a: int | `void` |
| `NETWORK.NETWORK_GET_MUTE_COUNT_FOR_PLAYER` | a1: int, a2: pointer, a3: pointer | `void` |
| `NETWORK.NETWORK_SET_SPECTATOR_TO_NON_SPECTATOR_TEXT_CHAT` | a: bool | `void` |
| `NETWORK.NETWORK_TEXT_CHAT_IS_TYPING` | — | `bool` |
| `NETWORK.SHUTDOWN_AND_LAUNCH_SINGLE_PLAYER_GAME` | — | `void` |
| `NETWORK.SHUTDOWN_AND_LOAD_MOST_RECENT_SAVE` | — | `bool` |
| `NETWORK.NETWORK_SET_FRIENDLY_FIRE_OPTION` | a: bool | `void` |
| `NETWORK.NETWORK_SET_RICH_PRESENCE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `NETWORK.NETWORK_SET_RICH_PRESENCE_STRING` | a: int, b: string | `void` |
| `NETWORK.NETWORK_GET_TIMEOUT_TIME` | — | `int` |
| `NETWORK.NETWORK_LEAVE_PED_BEHIND_BEFORE_WARP` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: bool | `void` |
| `NETWORK.NETWORK_LEAVE_PED_BEHIND_BEFORE_CUTSCENE` | a: int, b: bool | `void` |
| `NETWORK.REMOVE_ALL_STICKY_BOMBS_FROM_ENTITY` | a: int, b: int | `void` |
| `NETWORK.NETWORK_KEEP_ENTITY_COLLISION_DISABLED_AFTER_ANIM_SCENE` | a: int, b: int | `void` |
| `NETWORK.NETWORK_IS_ANY_PLAYER_NEAR` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `bool` |
| `NETWORK.NETWORK_CLAN_SERVICE_IS_VALID` | — | `bool` |
| `NETWORK.NETWORK_CLAN_PLAYER_IS_ACTIVE` | a: pointer | `bool` |
| `NETWORK.NETWORK_CLAN_PLAYER_GET_DESC` | a1: pointer, a2: int, a3: pointer | `bool` |
| `NETWORK.NETWORK_CLAN_IS_ROCKSTAR_CLAN` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_CLAN_GET_UI_FORMATTED_TAG` | a1: pointer, a2: int, a3: string | `void` |
| `NETWORK.NETWORK_CLAN_GET_LOCAL_MEMBERSHIPS_COUNT` | — | `int` |
| `NETWORK.NETWORK_CLAN_GET_MEMBERSHIP_DESC` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_CLAN_DOWNLOAD_MEMBERSHIP` | a: pointer | `bool` |
| `NETWORK.NETWORK_CLAN_DOWNLOAD_MEMBERSHIP_PENDING` | a: pointer | `bool` |
| `NETWORK.NETWORK_CLAN_ANY_DOWNLOAD_MEMBERSHIP_PENDING` | — | `bool` |
| `NETWORK.NETWORK_CLAN_REMOTE_MEMBERSHIPS_ARE_IN_CACHE` | a: pointer | `bool` |
| `NETWORK.NETWORK_CLAN_GET_MEMBERSHIP_COUNT` | a: pointer | `int` |
| `NETWORK.NETWORK_CLAN_GET_MEMBERSHIP_VALID` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_CLAN_GET_MEMBERSHIP` | a1: pointer, a2: pointer, a3: int | `bool` |
| `NETWORK.NETWORK_CLAN_JOIN` | a: int | `bool` |
| `NETWORK.NETWORK_CLAN_CREWINFO_GET_STRING_VALUE` | a: string, b: string | `bool` |
| `NETWORK.NETWORK_CLAN_CREWINFO_GET_CREWRANKTITLE` | a: int, b: string | `bool` |
| `NETWORK.NETWORK_CLAN_HAS_CREWINFO_METADATA_BEEN_RECEIVED` | — | `bool` |
| `NETWORK.NETWORK_CLAN_GET_EMBLEM_TXD_NAME` | a: pointer, b: string | `bool` |
| `NETWORK.NETWORK_CLAN_REQUEST_EMBLEM` | a: int | `bool` |
| `NETWORK.NETWORK_CLAN_IS_EMBLEM_READY` | a: int, b: pointer | `bool` |
| `NETWORK.NETWORK_CLAN_RELEASE_EMBLEM` | a: int | `void` |
| `NETWORK.NETWORK_GET_PRIMARY_CLAN_DATA_CLEAR` | — | `bool` |
| `NETWORK.NETWORK_GET_PRIMARY_CLAN_DATA_CANCEL` | — | `void` |
| `NETWORK.NETWORK_GET_PRIMARY_CLAN_DATA_START` | a: pointer, b: int | `bool` |
| `NETWORK.NETWORK_GET_PRIMARY_CLAN_DATA_PENDING` | — | `bool` |
| `NETWORK.NETWORK_GET_PRIMARY_CLAN_DATA_SUCCESS` | — | `bool` |
| `NETWORK.NETWORK_GET_PRIMARY_CLAN_DATA_NEW` | a: pointer, b: pointer | `bool` |
| `NETWORK.SET_NETWORK_ID_CAN_MIGRATE` | a: int, b: bool | `void` |
| `NETWORK.SET_NETWORK_ID_EXISTS_ON_ALL_MACHINES` | a: int, b: bool | `void` |
| `NETWORK.SET_NETWORK_ID_ALWAYS_EXISTS_FOR_PLAYER` | a1: int, a2: int, a3: bool | `void` |
| `NETWORK.SET_NETWORK_ID_CAN_BE_REASSIGNED` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SET_ENTITY_CAN_BLEND` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SET_OBJECT_CAN_BLEND_WHEN_FIXED` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SET_ENTITY_ONLY_EXISTS_FOR_PARTICIPANTS` | a: int, b: bool | `void` |
| `NETWORK.SET_NETWORK_ID_VISIBLE_IN_CUTSCENE` | a1: int, a2: bool, a3: bool | `void` |
| `NETWORK.SET_NETWORK_ID_VISIBLE_IN_CUTSCENE_HACK` | a1: int, a2: bool, a3: bool | `void` |
| `NETWORK.SET_NETWORK_ID_VISIBLE_IN_CUTSCENE_REMAIN_HACK` | a1: int, a2: int, a3: int | `void` |
| `NETWORK.SET_NETWORK_CUTSCENE_ENTITIES` | a: bool | `void` |
| `NETWORK.ARE_CUTSCENE_ENTITIES_NETWORKED` | — | `bool` |
| `NETWORK.SET_NETWORK_ID_PASS_CONTROL_IN_TUTORIAL` | a: int, b: bool | `void` |
| `NETWORK._NETWORK_SET_TUTORIAL_SPECIAL_SESSION` | a: bool | `void` |
| `NETWORK._NETWORK_IS_SPECIAL_TUTORIAL_SESSION` | — | `bool` |
| `NETWORK.IS_NETWORK_ID_OWNED_BY_PARTICIPANT` | a: int | `bool` |
| `NETWORK.SET_REMOTE_PLAYER_VISIBLE_IN_CUTSCENE` | a: int, b: bool | `void` |
| `NETWORK.SET_LOCAL_PLAYER_VISIBLE_IN_CUTSCENE` | a: bool, b: bool | `void` |
| `NETWORK.SET_LOCAL_PLAYER_INVISIBLE_LOCALLY` | a: bool | `void` |
| `NETWORK.SET_LOCAL_PLAYER_VISIBLE_LOCALLY` | a: bool | `void` |
| `NETWORK.SET_PLAYER_INVISIBLE_LOCALLY` | a: int, b: bool | `void` |
| `NETWORK.SET_PLAYER_VISIBLE_LOCALLY` | a: int, b: bool | `void` |
| `NETWORK.FADE_OUT_LOCAL_PLAYER` | a: bool | `void` |
| `NETWORK.NETWORK_FADE_OUT_ENTITY` | a1: int, a2: bool, a3: bool | `void` |
| `NETWORK.NETWORK_FADE_IN_ENTITY` | a1: int, a2: bool, a3: int | `void` |
| `NETWORK.NETWORK_IS_PLAYER_FADING` | a: int | `bool` |
| `NETWORK.NETWORK_IS_ENTITY_FADING` | a: int | `bool` |
| `NETWORK.IS_PLAYER_IN_CUTSCENE` | a: int | `bool` |
| `NETWORK.SET_ENTITY_VISIBLE_IN_CUTSCENE` | a1: int, a2: bool, a3: bool | `void` |
| `NETWORK.SET_ENTITY_LOCALLY_INVISIBLE` | a: int | `void` |
| `NETWORK.SET_ENTITY_LOCALLY_VISIBLE` | a: int | `void` |
| `NETWORK.IS_DAMAGE_TRACKER_ACTIVE_ON_NETWORK_ID` | a: int | `bool` |
| `NETWORK.ACTIVATE_DAMAGE_TRACKER_ON_NETWORK_ID` | a: int, b: bool | `void` |
| `NETWORK.IS_DAMAGE_TRACKER_ACTIVE_ON_PLAYER` | a: int | `bool` |
| `NETWORK.ACTIVATE_DAMAGE_TRACKER_ON_PLAYER` | a: int, b: bool | `void` |
| `NETWORK.IS_SPHERE_VISIBLE_TO_ANOTHER_MACHINE` | a1: float, a2: float, a3: float, a4: float | `bool` |
| `NETWORK.IS_SPHERE_VISIBLE_TO_PLAYER` | a1: int, a2: float, a3: float, a4: float, a5: float | `bool` |
| `NETWORK.RESERVE_NETWORK_MISSION_OBJECTS` | a: int | `void` |
| `NETWORK.RESERVE_NETWORK_MISSION_PEDS` | a: int | `void` |
| `NETWORK.RESERVE_NETWORK_MISSION_VEHICLES` | a: int | `void` |
| `NETWORK.RESERVE_LOCAL_NETWORK_MISSION_OBJECTS` | a: int | `void` |
| `NETWORK.RESERVE_LOCAL_NETWORK_MISSION_PEDS` | a: int | `void` |
| `NETWORK.RESERVE_LOCAL_NETWORK_MISSION_VEHICLES` | a: int | `void` |
| `NETWORK.CAN_REGISTER_MISSION_OBJECTS` | a: int | `bool` |
| `NETWORK.CAN_REGISTER_MISSION_PEDS` | a: int | `bool` |
| `NETWORK.CAN_REGISTER_MISSION_VEHICLES` | a: int | `bool` |
| `NETWORK.CAN_REGISTER_MISSION_PICKUPS` | a: int | `bool` |
| `NETWORK.CAN_REGISTER_MISSION_DOORS` | a: int | `bool` |
| `NETWORK.CAN_REGISTER_MISSION_ENTITIES` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `NETWORK.GET_NUM_RESERVED_MISSION_OBJECTS` | a: bool, b: int | `int` |
| `NETWORK.GET_NUM_RESERVED_MISSION_PEDS` | a: bool, b: int | `int` |
| `NETWORK.GET_NUM_RESERVED_MISSION_VEHICLES` | a: bool, b: int | `int` |
| `NETWORK.GET_NUM_CREATED_MISSION_OBJECTS` | a: bool | `int` |
| `NETWORK.GET_NUM_CREATED_MISSION_PEDS` | a: bool | `int` |
| `NETWORK.GET_NUM_CREATED_MISSION_VEHICLES` | a: bool | `int` |
| `NETWORK.GET_RESERVED_MISSION_ENTITIES_IN_AREA` | a1: float, a2: float, a3: float, a4: int, a5: pointer, a6: pointer, a7: pointer | `void` |
| `NETWORK.GET_MAX_NUM_NETWORK_OBJECTS` | — | `int` |
| `NETWORK.GET_MAX_NUM_NETWORK_PEDS` | — | `int` |
| `NETWORK.GET_MAX_NUM_NETWORK_VEHICLES` | — | `int` |
| `NETWORK.GET_MAX_NUM_NETWORK_PICKUPS` | — | `int` |
| `NETWORK.NETWORK_SET_OBJECT_SCOPE_DISTANCE` | a: int, b: float | `void` |
| `NETWORK.NETWORK_ALLOW_CLONING_WHILE_IN_TUTORIAL` | a: int, b: int | `void` |
| `NETWORK.NETWORK_SET_TASK_CUTSCENE_INSCOPE_MULTIPLER` | a: float | `void` |
| `NETWORK._NETWORK_HIDE_ENTITY_IN_TUTORIAL_SESSION` | a: int, b: bool | `void` |
| `NETWORK.GET_NETWORK_TIME` | — | `int` |
| `NETWORK.GET_NETWORK_TIME_ACCURATE` | — | `int` |
| `NETWORK.HAS_NETWORK_TIME_STARTED` | — | `bool` |
| `NETWORK.GET_TIME_OFFSET` | a: int, b: int | `int` |
| `NETWORK.IS_TIME_LESS_THAN` | a: int, b: int | `bool` |
| `NETWORK.IS_TIME_MORE_THAN` | a: int, b: int | `bool` |
| `NETWORK.IS_TIME_EQUAL_TO` | a: int, b: int | `bool` |
| `NETWORK.GET_TIME_DIFFERENCE` | a: int, b: int | `int` |
| `NETWORK.GET_TIME_AS_STRING` | a: int | `string` |
| `NETWORK.GET_CLOUD_TIME_AS_STRING` | — | `string` |
| `NETWORK.GET_CLOUD_TIME_AS_INT` | — | `int` |
| `NETWORK.CONVERT_POSIX_TIME` | a: int, b: pointer | `void` |
| `NETWORK.NETWORK_SET_IN_SPECTATOR_MODE` | a: bool, b: int | `void` |
| `NETWORK.NETWORK_SET_IN_SPECTATOR_MODE_EXTENDED` | a1: bool, a2: int, a3: bool | `void` |
| `NETWORK.NETWORK_SET_IN_FREE_CAM_MODE` | a: bool | `void` |
| `NETWORK.NETWORK_SET_ANTAGONISTIC_TO_PLAYER` | a: bool, b: int | `void` |
| `NETWORK.NETWORK_IS_IN_SPECTATOR_MODE` | — | `bool` |
| `NETWORK.NETWORK_SET_IN_MP_CUTSCENE` | a: bool, b: bool | `void` |
| `NETWORK.NETWORK_IS_IN_MP_CUTSCENE` | — | `bool` |
| `NETWORK.NETWORK_IS_PLAYER_IN_MP_CUTSCENE` | a: int | `bool` |
| `NETWORK.NETWORK_HIDE_PROJECTILE_IN_CUTSCENE` | — | `void` |
| `NETWORK.SET_NETWORK_VEHICLE_RESPOT_TIMER` | a1: int, a2: int, a3: int, a4: int | `void` |
| `NETWORK.IS_NETWORK_VEHICLE_RUNNING_RESPOT_TIMER` | a: int | `bool` |
| `NETWORK.SET_NETWORK_VEHICLE_AS_GHOST` | a: int, b: bool | `void` |
| `NETWORK.SET_NETWORK_VEHICLE_MAX_POSITION_DELTA_MULTIPLIER` | a: int, b: float | `void` |
| `NETWORK.SET_NETWORK_ENABLE_HIGH_SPEED_EDGE_FALL_DETECTION` | a: int, b: bool | `void` |
| `NETWORK.SET_LOCAL_PLAYER_AS_GHOST` | a: bool, b: bool | `void` |
| `NETWORK.IS_ENTITY_A_GHOST` | a: int | `bool` |
| `NETWORK.SET_NON_PARTICIPANTS_OF_THIS_SCRIPT_AS_GHOSTS` | a: bool | `void` |
| `NETWORK.SET_REMOTE_PLAYER_AS_GHOST` | a: int, b: bool | `void` |
| `NETWORK.SET_GHOST_ALPHA` | a: int | `void` |
| `NETWORK.RESET_GHOST_ALPHA` | — | `void` |
| `NETWORK.SET_ENTITY_GHOSTED_FOR_GHOST_PLAYERS` | a: int, b: bool | `void` |
| `NETWORK.SET_INVERT_GHOSTING` | a: bool | `void` |
| `NETWORK.IS_ENTITY_IN_GHOST_COLLISION` | a: int | `bool` |
| `NETWORK._SET_FREEMODE_REPORT_DATA` | a: pointer, b: pointer | `void` |
| `NETWORK.IS_OBJECT_REASSIGNMENT_IN_PROGRESS` | — | `bool` |
| `NETWORK.USE_PLAYER_COLOUR_INSTEAD_OF_TEAM_COLOUR` | a: bool | `void` |
| `NETWORK.NETWORK_CREATE_SYNCHRONISED_SCENE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: bool, a9: bool, a10: float, a11: float, a12: float | `int` |
| `NETWORK.NETWORK_ADD_PED_TO_SYNCHRONISED_SCENE` | a1: int, a2: int, a3: string, a4: string, a5: float, a6: float, a7: int, a8: int, a9: float, a10: int | `void` |
| `NETWORK.NETWORK_ADD_PED_TO_SYNCHRONISED_SCENE_WITH_IK` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int | `void` |
| `NETWORK.NETWORK_ADD_ENTITY_TO_SYNCHRONISED_SCENE` | a1: int, a2: int, a3: string, a4: string, a5: float, a6: float, a7: int | `void` |
| `NETWORK.NETWORK_ADD_MAP_ENTITY_TO_SYNCHRONISED_SCENE` | a1: int, a2: hash, a3: float, a4: float, a5: float, a6: float, a7: string, a8: float, a9: float, a10: int | `void` |
| `NETWORK.NETWORK_ADD_SYNCHRONISED_SCENE_CAMERA` | a1: int, a2: string, a3: string | `void` |
| `NETWORK.NETWORK_ATTACH_SYNCHRONISED_SCENE_TO_ENTITY` | a1: int, a2: int, a3: int | `void` |
| `NETWORK.NETWORK_START_SYNCHRONISED_SCENE` | a: int | `void` |
| `NETWORK.NETWORK_STOP_SYNCHRONISED_SCENE` | a: int | `void` |
| `NETWORK.NETWORK_GET_LOCAL_SCENE_FROM_NETWORK_ID` | a: int | `int` |
| `NETWORK.NETWORK_FORCE_LOCAL_USE_OF_SYNCED_SCENE_CAMERA` | a: int | `void` |
| `NETWORK.NETWORK_ALLOW_REMOTE_SYNCED_SCENE_LOCAL_PLAYER_REQUESTS` | a: int | `void` |
| `NETWORK.NETWORK_FIND_LARGEST_BUNCH_OF_PLAYERS` | a: int, b: int | `bool` |
| `NETWORK.NETWORK_START_RESPAWN_SEARCH_FOR_PLAYER` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int | `bool` |
| `NETWORK.NETWORK_START_RESPAWN_SEARCH_IN_ANGLED_AREA_FOR_PLAYER` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: int | `bool` |
| `NETWORK.NETWORK_QUERY_RESPAWN_RESULTS` | a: pointer | `int` |
| `NETWORK.NETWORK_CANCEL_RESPAWN_SEARCH` | — | `void` |
| `NETWORK.NETWORK_GET_RESPAWN_RESULT` | a1: int, a2: vector3, a3: pointer | `void` |
| `NETWORK.NETWORK_GET_RESPAWN_RESULT_FLAGS` | a: int | `int` |
| `NETWORK.NETWORK_START_SOLO_TUTORIAL_SESSION` | — | `void` |
| `NETWORK.NETWORK_ALLOW_GANG_TO_JOIN_TUTORIAL_SESSION` | a: int, b: int | `void` |
| `NETWORK.NETWORK_END_TUTORIAL_SESSION` | — | `void` |
| `NETWORK.NETWORK_IS_IN_TUTORIAL_SESSION` | — | `bool` |
| `NETWORK.NETWORK_WAITING_POP_CLEAR_TUTORIAL_SESSION` | — | `bool` |
| `NETWORK.NETWORK_IS_TUTORIAL_SESSION_CHANGE_PENDING` | — | `bool` |
| `NETWORK.NETWORK_GET_PLAYER_TUTORIAL_SESSION_INSTANCE` | a: int | `int` |
| `NETWORK.NETWORK_ARE_PLAYERS_IN_SAME_TUTORIAL_SESSION` | a: int, b: int | `bool` |
| `NETWORK.NETWORK_BLOCK_PROXY_MIGRATION_BETWEEN_TUTORIAL_SESSIONS` | a: int | `void` |
| `NETWORK.NETWORK_CONCEAL_PLAYER` | a1: int, a2: bool, a3: bool | `void` |
| `NETWORK.NETWORK_IS_PLAYER_CONCEALED` | a: int | `bool` |
| `NETWORK.NETWORK_CONCEAL_ENTITY` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_IS_ENTITY_CONCEALED` | a: int | `bool` |
| `NETWORK.NETWORK_OVERRIDE_CLOCK_TIME` | a1: int, a2: int, a3: int | `void` |
| `NETWORK.NETWORK_OVERRIDE_CLOCK_RATE` | a: int | `void` |
| `NETWORK.NETWORK_CLEAR_CLOCK_TIME_OVERRIDE` | — | `void` |
| `NETWORK._NETWORK_CLEAR_CLOCK_SYNC_TIME_OVERRIDE` | a: bool, b: int | `void` |
| `NETWORK.NETWORK_SYNC_CLOCK_TIME_OVERRIDE` | — | `void` |
| `NETWORK.NETWORK_IS_CLOCK_TIME_OVERRIDDEN` | — | `bool` |
| `NETWORK.NETWORK_ADD_ENTITY_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `int` |
| `NETWORK.NETWORK_ADD_ENTITY_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `int` |
| `NETWORK.NETWORK_ADD_CLIENT_ENTITY_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `int` |
| `NETWORK.NETWORK_ADD_CLIENT_ENTITY_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `int` |
| `NETWORK.NETWORK_REMOVE_ENTITY_AREA` | a: int | `bool` |
| `NETWORK.NETWORK_ENTITY_AREA_DOES_EXIST` | a: int | `bool` |
| `NETWORK.NETWORK_ENTITY_AREA_HAVE_ALL_REPLIED` | a: int | `bool` |
| `NETWORK.NETWORK_ENTITY_AREA_IS_OCCUPIED` | a: int | `bool` |
| `NETWORK.NETWORK_USE_HIGH_PRECISION_BLENDING` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SET_CUSTOM_ARENA_BALL_PARAMS` | a: int | `void` |
| `NETWORK.NETWORK_ENTITY_USE_HIGH_PRECISION_ROTATION` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_REQUEST_CLOUD_BACKGROUND_SCRIPTS` | — | `bool` |
| `NETWORK.NETWORK_IS_CLOUD_BACKGROUND_SCRIPT_REQUEST_PENDING` | — | `bool` |
| `NETWORK.NETWORK_REQUEST_CLOUD_TUNABLES` | — | `void` |
| `NETWORK.NETWORK_IS_TUNABLE_CLOUD_REQUEST_PENDING` | — | `bool` |
| `NETWORK.NETWORK_GET_TUNABLE_CLOUD_CRC` | — | `int` |
| `NETWORK._NETWORK_SET_TUNABLES_REGISTRATION_CONTEXTS` | a: pointer | `void` |
| `NETWORK._NETWORK_CLEAR_TUNABLES_REGISTRATION_CONTEXTS` | — | `void` |
| `NETWORK._NETWORK_GET_TUNABLES_REGISTRATION_BOOL` | a: hash, b: bool | `bool` |
| `NETWORK._NETWORK_GET_TUNABLES_REGISTRATION_INT` | a: hash, b: int | `int` |
| `NETWORK._NETWORK_GET_TUNABLES_REGISTRATION_FLOAT` | a: hash, b: float | `float` |
| `NETWORK.NETWORK_DOES_TUNABLE_EXIST` | a: string, b: string | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_INT` | a1: string, a2: string, a3: pointer | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_FLOAT` | a1: string, a2: string, a3: pointer | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_BOOL` | a: string, b: string | `bool` |
| `NETWORK.NETWORK_DOES_TUNABLE_EXIST_HASH` | a: hash, b: hash | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_MODIFICATION_DETECTION_CLEAR` | — | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_INT_HASH` | a1: hash, a2: hash, a3: pointer | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_INT_MODIFICATION_DETECTION_REGISTRATION_HASH` | a1: hash, a2: hash, a3: pointer | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_FLOAT_HASH` | a1: hash, a2: hash, a3: pointer | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_FLOAT_MODIFICATION_DETECTION_REGISTRATION_HASH` | a1: hash, a2: hash, a3: pointer | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_BOOL_HASH` | a: hash, b: hash | `bool` |
| `NETWORK.NETWORK_ACCESS_TUNABLE_BOOL_MODIFICATION_DETECTION_REGISTRATION_HASH` | a1: hash, a2: hash, a3: pointer | `bool` |
| `NETWORK.NETWORK_TRY_ACCESS_TUNABLE_BOOL_HASH` | a1: hash, a2: hash, a3: bool | `bool` |
| `NETWORK.NETWORK_GET_CONTENT_MODIFIER_LIST_ID` | a: hash | `int` |
| `NETWORK.NETWORK_GET_BONE_ID_OF_FATAL_HIT` | — | `int` |
| `NETWORK.NETWORK_RESET_BODY_TRACKER` | — | `void` |
| `NETWORK.NETWORK_GET_NUMBER_BODY_TRACKER_HITS` | — | `int` |
| `NETWORK.NETWORK_HAS_BONE_BEEN_HIT_BY_KILLER` | a: int | `bool` |
| `NETWORK.NETWORK_SET_ATTRIBUTE_DAMAGE_TO_PLAYER` | a: int, b: int | `bool` |
| `NETWORK.NETWORK_TRIGGER_DAMAGE_EVENT_FOR_ZERO_DAMAGE` | a: int, b: bool | `void` |
| `NETWORK._NETWORK_TRIGGER_DAMAGE_EVENT_FOR_ZERO_PED_DAMAGE` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_TRIGGER_DAMAGE_EVENT_FOR_ZERO_WEAPON_HASH` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SET_NO_LONGER_NEEDED` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_EXPLODE_VEHICLE` | a1: int, a2: bool, a3: bool, a4: int | `bool` |
| `NETWORK.NETWORK_EXPLODE_HELI` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `NETWORK.NETWORK_USE_LOGARITHMIC_BLENDING_THIS_FRAME` | a: int | `void` |
| `NETWORK.NETWORK_OVERRIDE_COORDS_AND_HEADING` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `NETWORK.NETWORK_ENABLE_EXTRA_VEHICLE_ORIENTATION_BLEND_CHECKS` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_DISABLE_PROXIMITY_MIGRATION` | a: int | `void` |
| `NETWORK.NETWORK_SET_PLAYER_MENTAL_STATE` | a: int | `void` |
| `NETWORK.NETWORK_SET_MINIMUM_RANK_FOR_MISSION` | a: bool | `void` |
| `NETWORK.NETWORK_CACHE_LOCAL_PLAYER_HEAD_BLEND_DATA` | — | `void` |
| `NETWORK.NETWORK_HAS_CACHED_PLAYER_HEAD_BLEND_DATA` | a: int | `bool` |
| `NETWORK.NETWORK_APPLY_CACHED_PLAYER_HEAD_BLEND_DATA` | a: int, b: int | `bool` |
| `NETWORK._NETWORK_SET_IGNORE_VEHICLE_RAMMED_BY_NON_VEHICLE` | a: bool | `void` |
| `NETWORK.GET_NUM_COMMERCE_ITEMS` | — | `int` |
| `NETWORK.IS_COMMERCE_DATA_VALID` | — | `bool` |
| `NETWORK.TRIGGER_COMMERCE_DATA_FETCH` | a: int | `void` |
| `NETWORK.IS_COMMERCE_DATA_FETCH_IN_PROGRESS` | — | `bool` |
| `NETWORK.GET_COMMERCE_ITEM_ID` | a: int | `string` |
| `NETWORK.GET_COMMERCE_ITEM_NAME` | a: int | `string` |
| `NETWORK.GET_COMMERCE_PRODUCT_PRICE` | a: int | `string` |
| `NETWORK.GET_COMMERCE_ITEM_NUM_CATS` | a: int | `int` |
| `NETWORK.GET_COMMERCE_ITEM_CAT` | a: int, b: int | `string` |
| `NETWORK.RESERVE_COMMERCE_STORE_PURCHASE_LOCATION` | a: int | `void` |
| `NETWORK.OPEN_COMMERCE_STORE` | a1: string, a2: string, a3: int, a4: bool | `void` |
| `NETWORK.CHECKOUT_COMMERCE_PRODUCT` | a1: string, a2: int, a3: bool | `void` |
| `NETWORK.IS_COMMERCE_STORE_OPEN` | — | `bool` |
| `NETWORK.SET_STORE_ENABLED` | a: bool | `void` |
| `NETWORK.REQUEST_COMMERCE_ITEM_IMAGE` | a: int | `bool` |
| `NETWORK.RELEASE_ALL_COMMERCE_ITEM_IMAGES` | — | `void` |
| `NETWORK.GET_COMMERCE_ITEM_TEXTURENAME` | a: int | `string` |
| `NETWORK.IS_STORE_AVAILABLE_TO_USER` | — | `bool` |
| `NETWORK.DELAY_MP_STORE_OPEN` | — | `void` |
| `NETWORK.RESET_STORE_NETWORK_GAME_TRACKING` | — | `void` |
| `NETWORK.IS_USER_OLD_ENOUGH_TO_ACCESS_STORE` | — | `bool` |
| `NETWORK.SET_LAST_VIEWED_SHOP_ITEM` | a1: hash, a2: int, a3: hash | `void` |
| `NETWORK.GET_USER_PREMIUM_ACCESS` | — | `int` |
| `NETWORK.GET_USER_STARTER_ACCESS` | — | `int` |
| `NETWORK.CLOUD_DELETE_MEMBER_FILE` | a: string | `int` |
| `NETWORK.CLOUD_HAS_REQUEST_COMPLETED` | a: int | `bool` |
| `NETWORK.CLOUD_DID_REQUEST_SUCCEED` | a: int | `bool` |
| `NETWORK.CLOUD_CHECK_AVAILABILITY` | — | `void` |
| `NETWORK.CLOUD_IS_CHECKING_AVAILABILITY` | — | `bool` |
| `NETWORK.CLOUD_GET_AVAILABILITY_CHECK_RESULT` | — | `bool` |
| `NETWORK.GET_CONTENT_TO_LOAD_TYPE` | — | `int` |
| `NETWORK.GET_IS_LAUNCH_FROM_LIVE_AREA` | — | `bool` |
| `NETWORK.GET_IS_LIVE_AREA_LAUNCH_WITH_CONTENT` | — | `bool` |
| `NETWORK.CLEAR_SERVICE_EVENT_ARGUMENTS` | — | `void` |
| `NETWORK.UGC_COPY_CONTENT` | a: pointer, b: pointer | `bool` |
| `NETWORK.UGC_IS_CREATING` | — | `bool` |
| `NETWORK.UGC_HAS_CREATE_FINISHED` | — | `bool` |
| `NETWORK.UGC_DID_CREATE_SUCCEED` | — | `bool` |
| `NETWORK.UGC_GET_CREATE_RESULT` | — | `int` |
| `NETWORK.UGC_GET_CREATE_CONTENT_ID` | — | `string` |
| `NETWORK.UGC_CLEAR_CREATE_RESULT` | — | `void` |
| `NETWORK.UGC_QUERY_MY_CONTENT` | a1: int, a2: int, a3: pointer, a4: int, a5: int, a6: int | `bool` |
| `NETWORK.UGC_QUERY_BY_CATEGORY` | a1: int, a2: int, a3: int, a4: string, a5: int, a6: bool | `bool` |
| `NETWORK.UGC_QUERY_BY_CONTENT_ID` | a1: string, a2: bool, a3: string | `bool` |
| `NETWORK.UGC_QUERY_BY_CONTENT_IDS` | a1: pointer, a2: int, a3: bool, a4: string | `bool` |
| `NETWORK.UGC_QUERY_MOST_RECENTLY_CREATED_CONTENT` | a1: int, a2: int, a3: string, a4: int | `bool` |
| `NETWORK.UGC_GET_BOOKMARKED_CONTENT` | a1: int, a2: int, a3: string, a4: pointer | `bool` |
| `NETWORK.UGC_GET_MY_CONTENT` | a1: int, a2: int, a3: string, a4: pointer | `bool` |
| `NETWORK.UGC_GET_FRIEND_CONTENT` | a1: int, a2: int, a3: string, a4: pointer | `bool` |
| `NETWORK.UGC_GET_CREW_CONTENT` | a1: int, a2: int, a3: int, a4: string, a5: pointer | `bool` |
| `NETWORK.UGC_GET_GET_BY_CATEGORY` | a1: int, a2: int, a3: int, a4: string, a5: pointer | `bool` |
| `NETWORK.UGC_GET_GET_BY_CONTENT_ID` | a: string, b: string | `bool` |
| `NETWORK.UGC_GET_GET_BY_CONTENT_IDS` | a1: pointer, a2: int, a3: string | `bool` |
| `NETWORK.UGC_GET_MOST_RECENTLY_CREATED_CONTENT` | a1: int, a2: int, a3: pointer, a4: pointer | `bool` |
| `NETWORK.UGC_GET_MOST_RECENTLY_PLAYED_CONTENT` | a1: int, a2: int, a3: pointer, a4: pointer | `bool` |
| `NETWORK.UGC_GET_TOP_RATED_CONTENT` | a1: int, a2: int, a3: pointer, a4: pointer | `bool` |
| `NETWORK.UGC_CANCEL_QUERY` | — | `void` |
| `NETWORK.UGC_IS_GETTING` | — | `bool` |
| `NETWORK.UGC_HAS_GET_FINISHED` | — | `bool` |
| `NETWORK.UGC_DID_GET_SUCCEED` | — | `bool` |
| `NETWORK.UGC_WAS_QUERY_FORCE_CANCELLED` | — | `bool` |
| `NETWORK.UGC_GET_QUERY_RESULT` | — | `int` |
| `NETWORK.UGC_GET_CONTENT_NUM` | — | `int` |
| `NETWORK.UGC_GET_CONTENT_TOTAL` | — | `int` |
| `NETWORK.UGC_GET_CONTENT_HASH` | — | `hash` |
| `NETWORK.UGC_CLEAR_QUERY_RESULTS` | — | `void` |
| `NETWORK.UGC_GET_CONTENT_USER_ID` | a: int | `string` |
| `NETWORK.UGC_GET_CONTENT_CREATOR_GAMER_HANDLE` | a: int, b: pointer | `bool` |
| `NETWORK.UGC_GET_CONTENT_CREATED_BY_LOCAL_PLAYER` | a: int | `bool` |
| `NETWORK.UGC_GET_CONTENT_USER_NAME` | a: int | `string` |
| `NETWORK.UGC_GET_CONTENT_IS_USING_SC_NICKNAME` | a: int | `bool` |
| `NETWORK.UGC_GET_CONTENT_CATEGORY` | a: int | `int` |
| `NETWORK.UGC_GET_CONTENT_ID` | a: int | `string` |
| `NETWORK.UGC_GET_ROOT_CONTENT_ID` | a: int | `string` |
| `NETWORK.UGC_GET_CONTENT_NAME` | a: int | `string` |
| `NETWORK.UGC_GET_CONTENT_DESCRIPTION` | a: int | `string` |
| `NETWORK.UGC_GET_CONTENT_DESCRIPTION_HASH` | a: int | `int` |
| `NETWORK.UGC_GET_CONTENT_PATH` | a: int, b: int | `string` |
| `NETWORK.UGC_GET_CONTENT_UPDATED_DATE` | a: int, b: pointer | `void` |
| `NETWORK.UGC_GET_CONTENT_FILE_VERSION` | a: int, b: int | `int` |
| `NETWORK.UGC_GET_CONTENT_HAS_LO_RES_PHOTO` | a: int | `bool` |
| `NETWORK.UGC_GET_CONTENT_HAS_HI_RES_PHOTO` | a: int | `bool` |
| `NETWORK.UGC_GET_CONTENT_LANGUAGE` | a: int | `int` |
| `NETWORK.UGC_GET_CONTENT_IS_PUBLISHED` | a: int | `bool` |
| `NETWORK.UGC_GET_CONTENT_IS_VERIFIED` | a: int | `bool` |
| `NETWORK.UGC_GET_CONTENT_RATING` | a: int, b: int | `float` |
| `NETWORK.UGC_GET_CONTENT_RATING_COUNT` | a: int, b: int | `int` |
| `NETWORK.UGC_GET_CONTENT_RATING_POSITIVE_COUNT` | a: int, b: int | `int` |
| `NETWORK.UGC_GET_CONTENT_RATING_NEGATIVE_COUNT` | a: int, b: int | `int` |
| `NETWORK.UGC_GET_CONTENT_HAS_PLAYER_RECORD` | a: int | `bool` |
| `NETWORK.UGC_GET_CONTENT_HAS_PLAYER_BOOKMARKED` | a: int | `bool` |
| `NETWORK.UGC_REQUEST_CONTENT_DATA_FROM_INDEX` | a: int, b: int | `int` |
| `NETWORK.UGC_REQUEST_CONTENT_DATA_FROM_PARAMS` | a1: string, a2: string, a3: int, a4: int, a5: int | `int` |
| `NETWORK.UGC_REQUEST_CACHED_DESCRIPTION` | a: int | `int` |
| `NETWORK.UGC_IS_DESCRIPTION_REQUEST_IN_PROGRESS` | a: int | `bool` |
| `NETWORK.UGC_HAS_DESCRIPTION_REQUEST_FINISHED` | a: int | `bool` |
| `NETWORK.UGC_DID_DESCRIPTION_REQUEST_SUCCEED` | a: int | `bool` |
| `NETWORK.UGC_GET_CACHED_DESCRIPTION` | a: int, b: int | `string` |
| `NETWORK.UGC_RELEASE_CACHED_DESCRIPTION` | a: int | `bool` |
| `NETWORK.UGC_RELEASE_ALL_CACHED_DESCRIPTIONS` | — | `void` |
| `NETWORK.UGC_HAS_PERMISSION_TO_WRITE` | — | `bool` |
| `NETWORK.UGC_PUBLISH` | a1: string, a2: string, a3: string | `bool` |
| `NETWORK.UGC_SET_BOOKMARKED` | a1: string, a2: bool, a3: string | `bool` |
| `NETWORK.UGC_SET_DELETED` | a1: pointer, a2: bool, a3: string | `bool` |
| `NETWORK.UGC_IS_MODIFYING` | — | `bool` |
| `NETWORK.UGC_HAS_MODIFY_FINISHED` | — | `bool` |
| `NETWORK.UGC_DID_MODIFY_SUCCEED` | — | `bool` |
| `NETWORK.UGC_GET_MODIFY_RESULT` | — | `int` |
| `NETWORK.UGC_CLEAR_MODIFY_RESULT` | — | `void` |
| `NETWORK.UGC_GET_CREATORS_BY_USER_ID` | a: pointer, b: pointer | `bool` |
| `NETWORK.UGC_HAS_QUERY_CREATORS_FINISHED` | — | `bool` |
| `NETWORK.UGC_DID_QUERY_CREATORS_SUCCEED` | — | `bool` |
| `NETWORK.UGC_GET_CREATOR_NUM` | — | `int` |
| `NETWORK.UGC_LOAD_OFFLINE_QUERY` | a: int | `bool` |
| `NETWORK.UGC_CLEAR_OFFLINE_QUERY` | — | `void` |
| `NETWORK.UGC_SET_QUERY_DATA_FROM_OFFLINE` | a: bool | `void` |
| `NETWORK.UGC_SET_USING_OFFLINE_CONTENT` | a: bool | `void` |
| `NETWORK.UGC_IS_LANGUAGE_SUPPORTED` | a: int | `bool` |
| `NETWORK.FACEBOOK_POST_COMPLETED_HEIST` | a1: string, a2: int, a3: int | `bool` |
| `NETWORK.FACEBOOK_POST_CREATE_CHARACTER` | — | `bool` |
| `NETWORK.FACEBOOK_POST_COMPLETED_MILESTONE` | a: int | `bool` |
| `NETWORK.FACEBOOK_HAS_POST_COMPLETED` | — | `bool` |
| `NETWORK.FACEBOOK_DID_POST_SUCCEED` | — | `bool` |
| `NETWORK.FACEBOOK_CAN_POST_TO_FACEBOOK` | — | `bool` |
| `NETWORK.TEXTURE_DOWNLOAD_REQUEST` | a1: pointer, a2: string, a3: string, a4: bool | `int` |
| `NETWORK.TITLE_TEXTURE_DOWNLOAD_REQUEST` | a1: string, a2: string, a3: bool | `int` |
| `NETWORK.UGC_TEXTURE_DOWNLOAD_REQUEST` | a1: string, a2: int, a3: int, a4: int, a5: string, a6: bool | `int` |
| `NETWORK.TEXTURE_DOWNLOAD_RELEASE` | a: int | `void` |
| `NETWORK.TEXTURE_DOWNLOAD_HAS_FAILED` | a: int | `bool` |
| `NETWORK.TEXTURE_DOWNLOAD_GET_NAME` | a: int | `string` |
| `NETWORK.GET_STATUS_OF_TEXTURE_DOWNLOAD` | a: int | `int` |
| `NETWORK.NETWORK_CHECK_ROS_LINK_WENTDOWN_NOT_NET` | — | `bool` |
| `NETWORK.NETWORK_SHOULD_SHOW_STRICT_NAT_WARNING` | — | `bool` |
| `NETWORK.NETWORK_IS_CABLE_CONNECTED` | — | `bool` |
| `NETWORK.NETWORK_HAVE_SCS_PRIVATE_MSG_PRIV` | — | `bool` |
| `NETWORK.NETWORK_HAVE_ROS_SOCIAL_CLUB_PRIV` | — | `bool` |
| `NETWORK.NETWORK_HAVE_ROS_BANNED_PRIV` | — | `bool` |
| `NETWORK.NETWORK_HAVE_ROS_CREATE_TICKET_PRIV` | — | `bool` |
| `NETWORK.NETWORK_HAVE_ROS_MULTIPLAYER_PRIV` | — | `bool` |
| `NETWORK.NETWORK_HAVE_ROS_LEADERBOARD_WRITE_PRIV` | — | `bool` |
| `NETWORK.NETWORK_HAS_ROS_PRIVILEGE` | a: int | `bool` |
| `NETWORK.NETWORK_HAS_ROS_PRIVILEGE_END_DATE` | a1: int, a2: pointer, a3: pointer | `bool` |
| `NETWORK.NETWORK_HAS_ROS_PRIVILEGE_PLAYED_LAST_GEN` | — | `bool` |
| `NETWORK.NETWORK_HAS_ROS_PRIVILEGE_SPECIAL_EDITION_CONTENT` | — | `bool` |
| `NETWORK._NETWORK_HAS_ROS_PRIVILEGE_MP_TEXT_COMMUNICATION` | — | `bool` |
| `NETWORK._NETWORK_HAS_ROS_PRIVILEGE_MP_VOICE_COMMUNICATION` | — | `bool` |
| `NETWORK._NETWORK_HAS_ROS_PRIVILEGE_REPORTING` | — | `bool` |
| `NETWORK.NETWORK_START_USER_CONTENT_PERMISSIONS_CHECK` | a: pointer | `int` |
| `NETWORK.NETWORK_SKIP_RADIO_RESET_NEXT_CLOSE` | — | `void` |
| `NETWORK.NETWORK_SKIP_RADIO_RESET_NEXT_OPEN` | — | `void` |
| `NETWORK.NETWORK_SKIP_RADIO_WARNING` | — | `bool` |
| `NETWORK.NETWORK_FORCE_LOCAL_PLAYER_SCAR_SYNC` | — | `void` |
| `NETWORK.NETWORK_DISABLE_LEAVE_REMOTE_PED_BEHIND` | a: bool | `void` |
| `NETWORK.NETWORK_ALLOW_REMOTE_ATTACHMENT_MODIFICATION` | a: int, b: bool | `void` |
| `NETWORK.NETWORK_SHOW_CHAT_RESTRICTION_MSC` | a: int | `void` |
| `NETWORK.NETWORK_SHOW_PSN_UGC_RESTRICTION` | — | `void` |
| `NETWORK.NETWORK_IS_TITLE_UPDATE_REQUIRED` | — | `bool` |
| `NETWORK.NETWORK_QUIT_MP_TO_DESKTOP` | — | `void` |
| `NETWORK.NETWORK_IS_CONNECTED_VIA_RELAY` | a: int | `bool` |
| `NETWORK.NETWORK_GET_AVERAGE_LATENCY` | a: int | `float` |
| `NETWORK.NETWORK_GET_AVERAGE_PING` | a: int | `float` |
| `NETWORK.NETWORK_GET_AVERAGE_PACKET_LOSS` | a: int | `float` |
| `NETWORK.NETWORK_GET_NUM_UNACKED_RELIABLES` | a: int | `int` |
| `NETWORK.NETWORK_GET_UNRELIABLE_RESEND_COUNT` | a: int | `int` |
| `NETWORK.NETWORK_REPORT_CODE_TAMPER` | — | blocked: Not allowed |
| `NETWORK._NETWORK_GET_DUMP_OF_ASSET_VERIFIER` | a: pointer | `void` |
| `NETWORK.NETWORK_GET_LAST_ENTITY_POS_RECEIVED_OVER_NETWORK` | a: int | `vector3` |
| `NETWORK.NETWORK_GET_LAST_PLAYER_POS_RECEIVED_OVER_NETWORK` | a: int | `vector3` |
| `NETWORK.NETWORK_GET_LAST_VEL_RECEIVED_OVER_NETWORK` | a: int | `vector3` |
| `NETWORK.NETWORK_GET_PREDICTED_VELOCITY` | a: int, b: float | `vector3` |
| `NETWORK.NETWORK_DUMP_NET_IF_CONFIG` | — | `void` |
| `NETWORK.NETWORK_GET_SIGNALLING_INFO` | a: pointer | `void` |
| `NETWORK.NETWORK_GET_NET_STATISTICS_INFO` | a: pointer | `void` |
| `NETWORK.NETWORK_GET_PLAYER_ACCOUNT_ID` | a: int | `int` |
| `NETWORK.NETWORK_POST_UDS_ACTIVITY_START` | a: string | `void` |
| `NETWORK.NETWORK_POST_UDS_ACTIVITY_END` | a1: string, a2: int, a3: int | `void` |
| `NETWORK.NETWORK_POST_UDS_ACTIVITY_RESUME` | a: string | `void` |
| `NETWORK.NETWORK_POST_UDS_ACTIVITY_RESUME_WITH_TASKS` | a: string, b: pointer | `void` |
| `NETWORK.NETWORK_POST_UDS_ACTIVITY_AVAILABILITY_CHANGE` | a: pointer | `void` |
| `NETWORK.NETWORK_POST_UDS_ACTIVITY_TERMINATE` | — | `void` |
| `NETWORK.NETWORK_HAS_SC_MEMBERSHIP_INFO` | — | `bool` |
| `NETWORK.NETWORK_HAS_SC_MEMBERSHIP` | — | `bool` |
| `NETWORK.NETWORK_GET_SC_MEMBERSHIP_INFO` | a: pointer | `void` |
| `NETWORK.NETWORK_UGC_NAV` | a: int, b: int | `void` |
| `NETWORK._NETWORK_GET_GAME_RESTART_REASON` | — | `int` |
| `NETWORK._NETWORK_CONFIRM_GAME_RESTART` | — | `void` |
| `NETWORK._NETWORK_GET_GAME_RESTART_REASON_MESSAGE_LABEL` | — | `string` |
| `NETWORK._NETWORK_GET_BATTLEYE_ERROR_MESSAGE_LABEL` | a: int | `string` |

## OBJECT

| Native | Params | Returns |
| --- | --- | --- |
| `OBJECT.CREATE_OBJECT` | a1: hash, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool | `int` |
| `OBJECT.CREATE_OBJECT_NO_OFFSET` | a1: hash, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: int | `int` |
| `OBJECT.DELETE_OBJECT` | a: pointer | `void` |
| `OBJECT.PLACE_OBJECT_ON_GROUND_PROPERLY` | a: int | `bool` |
| `OBJECT.PLACE_OBJECT_ON_GROUND_OR_OBJECT_PROPERLY` | a: int | `bool` |
| `OBJECT.ROTATE_OBJECT` | a1: int, a2: float, a3: float, a4: bool | `bool` |
| `OBJECT.SLIDE_OBJECT` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool | `bool` |
| `OBJECT.SET_OBJECT_TARGETTABLE` | a1: int, a2: bool, a3: int | `void` |
| `OBJECT._SET_OBJECT_TARGETTABLE_BY_PLAYER` | a1: int, a2: bool, a3: bool | `void` |
| `OBJECT.SET_OBJECT_FORCE_VEHICLES_TO_AVOID` | a: int, b: bool | `void` |
| `OBJECT.GET_CLOSEST_OBJECT_OF_TYPE` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: bool, a7: bool, a8: bool | `int` |
| `OBJECT.HAS_OBJECT_BEEN_BROKEN` | a: int, b: int | `bool` |
| `OBJECT.HAS_CLOSEST_OBJECT_OF_TYPE_BEEN_BROKEN` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: int | `bool` |
| `OBJECT.HAS_CLOSEST_OBJECT_OF_TYPE_BEEN_COMPLETELY_DESTROYED` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: bool | `bool` |
| `OBJECT.GET_HAS_OBJECT_BEEN_COMPLETELY_DESTROYED` | a: int | `bool` |
| `OBJECT.GET_OFFSET_FROM_COORD_AND_HEADING_IN_WORLD_COORDS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `vector3` |
| `OBJECT.GET_COORDS_AND_ROTATION_OF_CLOSEST_OBJECT_OF_TYPE` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: vector3, a7: vector3, a8: int | `bool` |
| `OBJECT.SET_STATE_OF_CLOSEST_DOOR_OF_TYPE` | a1: hash, a2: float, a3: float, a4: float, a5: bool, a6: float, a7: bool | `void` |
| `OBJECT.GET_STATE_OF_CLOSEST_DOOR_OF_TYPE` | a1: hash, a2: float, a3: float, a4: float, a5: pointer, a6: pointer | `void` |
| `OBJECT.SET_LOCKED_UNSTREAMED_IN_DOOR_OF_TYPE` | a1: hash, a2: float, a3: float, a4: float, a5: bool, a6: float, a7: float, a8: float | `void` |
| `OBJECT.PLAY_OBJECT_AUTO_START_ANIM` | a: int | `void` |
| `OBJECT.ADD_DOOR_TO_SYSTEM` | a1: hash, a2: hash, a3: float, a4: float, a5: float, a6: bool, a7: bool, a8: bool, a9: int | `void` |
| `OBJECT.REMOVE_DOOR_FROM_SYSTEM` | a: hash, b: int | `void` |
| `OBJECT.DOOR_SYSTEM_SET_DOOR_STATE` | a1: hash, a2: int, a3: bool, a4: bool | `void` |
| `OBJECT.DOOR_SYSTEM_GET_DOOR_STATE` | a: hash | `int` |
| `OBJECT.DOOR_SYSTEM_GET_DOOR_PENDING_STATE` | a: hash | `int` |
| `OBJECT.DOOR_SYSTEM_SET_AUTOMATIC_RATE` | a1: hash, a2: float, a3: bool, a4: bool | `void` |
| `OBJECT.DOOR_SYSTEM_SET_AUTOMATIC_DISTANCE` | a1: hash, a2: float, a3: bool, a4: bool | `void` |
| `OBJECT.DOOR_SYSTEM_SET_OPEN_RATIO` | a1: hash, a2: float, a3: bool, a4: bool | `void` |
| `OBJECT.DOOR_SYSTEM_GET_AUTOMATIC_DISTANCE` | a: hash | `float` |
| `OBJECT.DOOR_SYSTEM_GET_OPEN_RATIO` | a: hash | `float` |
| `OBJECT.DOOR_SYSTEM_GET_IS_SPRING_REMOVED` | a: hash | `bool` |
| `OBJECT.DOOR_SYSTEM_SET_SPRING_REMOVED` | a1: hash, a2: bool, a3: bool, a4: bool | `void` |
| `OBJECT.DOOR_SYSTEM_SET_HOLD_OPEN` | a: hash, b: bool | `void` |
| `OBJECT.DOOR_SYSTEM_SET_DOOR_OPEN_FOR_RACES` | a: hash, b: bool | `void` |
| `OBJECT.IS_DOOR_REGISTERED_WITH_SYSTEM` | a: hash | `bool` |
| `OBJECT.IS_DOOR_CLOSED` | a: hash | `bool` |
| `OBJECT.OPEN_ALL_BARRIERS_FOR_RACE` | a: bool | `void` |
| `OBJECT.CLOSE_ALL_BARRIERS_FOR_RACE` | — | `void` |
| `OBJECT.DOOR_SYSTEM_GET_IS_PHYSICS_LOADED` | a: int | `bool` |
| `OBJECT.DOOR_SYSTEM_FIND_EXISTING_DOOR` | a1: float, a2: float, a3: float, a4: hash, a5: pointer | `bool` |
| `OBJECT.IS_GARAGE_EMPTY` | a1: hash, a2: bool, a3: int | `bool` |
| `OBJECT.IS_PLAYER_ENTIRELY_INSIDE_GARAGE` | a1: hash, a2: int, a3: float, a4: int | `bool` |
| `OBJECT.IS_PLAYER_PARTIALLY_INSIDE_GARAGE` | a1: hash, a2: int, a3: int | `bool` |
| `OBJECT.ARE_ENTITIES_ENTIRELY_INSIDE_GARAGE` | a1: hash, a2: bool, a3: bool, a4: bool, a5: int | `bool` |
| `OBJECT.IS_ANY_ENTITY_ENTIRELY_INSIDE_GARAGE` | a1: hash, a2: bool, a3: bool, a4: bool, a5: int | `bool` |
| `OBJECT.IS_OBJECT_ENTIRELY_INSIDE_GARAGE` | a1: hash, a2: int, a3: float, a4: int | `bool` |
| `OBJECT.IS_OBJECT_PARTIALLY_INSIDE_GARAGE` | a1: hash, a2: int, a3: int | `bool` |
| `OBJECT.CLEAR_GARAGE` | a: hash, b: bool | `void` |
| `OBJECT.CLEAR_OBJECTS_INSIDE_GARAGE` | a1: hash, a2: bool, a3: bool, a4: bool, a5: bool | `void` |
| `OBJECT.DISABLE_TIDYING_UP_IN_GARAGE` | a: int, b: bool | `void` |
| `OBJECT.ENABLE_SAVING_IN_GARAGE` | a: hash, b: bool | `void` |
| `OBJECT.CLOSE_SAFEHOUSE_GARAGES` | — | `void` |
| `OBJECT.DOES_OBJECT_OF_TYPE_EXIST_AT_COORDS` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: bool | `bool` |
| `OBJECT.IS_POINT_IN_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: bool, a12: bool | `bool` |
| `OBJECT.SET_OBJECT_ALLOW_LOW_LOD_BUOYANCY` | a: int, b: bool | `void` |
| `OBJECT.SET_OBJECT_PHYSICS_PARAMS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float | `void` |
| `OBJECT.GET_OBJECT_FRAGMENT_DAMAGE_HEALTH` | a: int, b: bool | `float` |
| `OBJECT.SET_ACTIVATE_OBJECT_PHYSICS_AS_SOON_AS_IT_IS_UNFROZEN` | a: int, b: bool | `void` |
| `OBJECT.IS_ANY_OBJECT_NEAR_POINT` | a1: float, a2: float, a3: float, a4: float, a5: bool | `bool` |
| `OBJECT.IS_OBJECT_NEAR_POINT` | a1: hash, a2: float, a3: float, a4: float, a5: float | `bool` |
| `OBJECT.REMOVE_OBJECT_HIGH_DETAIL_MODEL` | a: int | `void` |
| `OBJECT.BREAK_OBJECT_FRAGMENT_CHILD` | a1: int, a2: int, a3: bool | `void` |
| `OBJECT.DAMAGE_OBJECT_FRAGMENT_CHILD` | a1: int, a2: int, a3: int | `void` |
| `OBJECT.FIX_OBJECT_FRAGMENT` | a: int | `void` |
| `OBJECT.TRACK_OBJECT_VISIBILITY` | a: int | `void` |
| `OBJECT.IS_OBJECT_VISIBLE` | a: int | `bool` |
| `OBJECT.SET_OBJECT_IS_SPECIAL_GOLFBALL` | a: int, b: bool | `void` |
| `OBJECT.SET_OBJECT_TAKES_DAMAGE_FROM_COLLIDING_WITH_BUILDINGS` | a: int, b: bool | `void` |
| `OBJECT.ALLOW_DAMAGE_EVENTS_FOR_NON_NETWORKED_OBJECTS` | a: bool | `void` |
| `OBJECT.SET_CUTSCENES_WEAPON_FLASHLIGHT_ON_THIS_FRAME` | a: int, b: bool | `void` |
| `OBJECT.GET_RAYFIRE_MAP_OBJECT` | a1: float, a2: float, a3: float, a4: float, a5: string | `int` |
| `OBJECT.SET_STATE_OF_RAYFIRE_MAP_OBJECT` | a: int, b: int | `void` |
| `OBJECT.GET_STATE_OF_RAYFIRE_MAP_OBJECT` | a: int | `int` |
| `OBJECT.DOES_RAYFIRE_MAP_OBJECT_EXIST` | a: int | `bool` |
| `OBJECT.GET_RAYFIRE_MAP_OBJECT_ANIM_PHASE` | a: int | `float` |
| `OBJECT.CREATE_PICKUP` | a1: hash, a2: float, a3: float, a4: float, a5: int, a6: int, a7: bool, a8: hash | `int` |
| `OBJECT.CREATE_PICKUP_ROTATE` | a1: hash, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int, a11: bool, a12: hash | `int` |
| `OBJECT.FORCE_PICKUP_ROTATE_FACE_UP` | — | `void` |
| `OBJECT.SET_CUSTOM_PICKUP_WEAPON_HASH` | a: hash, b: int | `void` |
| `OBJECT.CREATE_AMBIENT_PICKUP` | a1: hash, a2: float, a3: float, a4: float, a5: int, a6: int, a7: hash, a8: bool, a9: bool | `int` |
| `OBJECT.CREATE_NON_NETWORKED_AMBIENT_PICKUP` | a1: hash, a2: float, a3: float, a4: float, a5: int, a6: int, a7: hash, a8: bool, a9: bool | `int` |
| `OBJECT.BLOCK_PLAYERS_FOR_AMBIENT_PICKUP` | a: int, b: int | `void` |
| `OBJECT.CREATE_PORTABLE_PICKUP` | a1: hash, a2: float, a3: float, a4: float, a5: bool, a6: hash | `int` |
| `OBJECT.CREATE_NON_NETWORKED_PORTABLE_PICKUP` | a1: hash, a2: float, a3: float, a4: float, a5: bool, a6: hash | `int` |
| `OBJECT.ATTACH_PORTABLE_PICKUP_TO_PED` | a: int, b: int | `void` |
| `OBJECT.DETACH_PORTABLE_PICKUP_FROM_PED` | a: int | `void` |
| `OBJECT.FORCE_PORTABLE_PICKUP_LAST_ACCESSIBLE_POSITION_SETTING` | a: int | `void` |
| `OBJECT.HIDE_PORTABLE_PICKUP_WHEN_DETACHED` | a: int, b: bool | `void` |
| `OBJECT.SET_MAX_NUM_PORTABLE_PICKUPS_CARRIED_BY_PLAYER` | a: hash, b: int | `void` |
| `OBJECT.SET_LOCAL_PLAYER_CAN_COLLECT_PORTABLE_PICKUPS` | a: bool | `void` |
| `OBJECT.GET_SAFE_PICKUP_COORDS` | a1: float, a2: float, a3: float, a4: float, a5: float | `vector3` |
| `OBJECT.ADD_EXTENDED_PICKUP_PROBE_AREA` | a1: float, a2: float, a3: float, a4: float | `void` |
| `OBJECT.CLEAR_EXTENDED_PICKUP_PROBE_AREAS` | — | `void` |
| `OBJECT.GET_PICKUP_COORDS` | a: int | `vector3` |
| `OBJECT.SUPPRESS_PICKUP_SOUND_FOR_PICKUP` | a: int, b: int | `void` |
| `OBJECT.REMOVE_ALL_PICKUPS_OF_TYPE` | a: hash | `void` |
| `OBJECT.HAS_PICKUP_BEEN_COLLECTED` | a: int | `bool` |
| `OBJECT.REMOVE_PICKUP` | a: int | `void` |
| `OBJECT.CREATE_MONEY_PICKUPS` | a1: float, a2: float, a3: float, a4: int, a5: int, a6: hash | `void` |
| `OBJECT.DOES_PICKUP_EXIST` | a: int | `bool` |
| `OBJECT.DOES_PICKUP_OBJECT_EXIST` | a: int | `bool` |
| `OBJECT.GET_PICKUP_OBJECT` | a: int | `int` |
| `OBJECT.IS_OBJECT_A_PICKUP` | a: int | `bool` |
| `OBJECT.IS_OBJECT_A_PORTABLE_PICKUP` | a: int | `bool` |
| `OBJECT.DOES_PICKUP_OF_TYPE_EXIST_IN_AREA` | a1: hash, a2: float, a3: float, a4: float, a5: float | `bool` |
| `OBJECT.SET_PICKUP_REGENERATION_TIME` | a: int, b: int | `void` |
| `OBJECT.FORCE_PICKUP_REGENERATE` | a: int | `void` |
| `OBJECT.SET_PLAYER_PERMITTED_TO_COLLECT_PICKUPS_OF_TYPE` | a1: int, a2: hash, a3: bool | `void` |
| `OBJECT.SET_LOCAL_PLAYER_PERMITTED_TO_COLLECT_PICKUPS_WITH_MODEL` | a: hash, b: bool | `void` |
| `OBJECT.ALLOW_ALL_PLAYERS_TO_COLLECT_PICKUPS_OF_TYPE` | a: hash | `void` |
| `OBJECT.SET_TEAM_PICKUP_OBJECT` | a1: int, a2: int, a3: bool | `void` |
| `OBJECT.PREVENT_COLLECTION_OF_PORTABLE_PICKUP` | a1: int, a2: bool, a3: bool | `void` |
| `OBJECT.SET_PICKUP_OBJECT_GLOW_WHEN_UNCOLLECTABLE` | a: int, b: bool | `void` |
| `OBJECT._SET_PICKUP_GLOW_DISABLED` | a: int, b: bool | `void` |
| `OBJECT.SET_PICKUP_GLOW_OFFSET` | a: int, b: float | `void` |
| `OBJECT.SET_PICKUP_OBJECT_GLOW_OFFSET` | a1: int, a2: float, a3: bool | `void` |
| `OBJECT.SET_OBJECT_GLOW_IN_SAME_TEAM` | a: int | `void` |
| `OBJECT.SET_PICKUP_OBJECT_ARROW_MARKER` | a: int, b: bool | `void` |
| `OBJECT.ALLOW_PICKUP_ARROW_MARKER_WHEN_UNCOLLECTABLE` | a: int, b: bool | `void` |
| `OBJECT.GET_DEFAULT_AMMO_FOR_WEAPON_PICKUP` | a: hash | `int` |
| `OBJECT.SET_PICKUP_GENERATION_RANGE_MULTIPLIER` | a: float | `void` |
| `OBJECT.GET_PICKUP_GENERATION_RANGE_MULTIPLIER` | — | `float` |
| `OBJECT.SET_ONLY_ALLOW_AMMO_COLLECTION_WHEN_LOW` | a: bool | `void` |
| `OBJECT.SET_PICKUP_UNCOLLECTABLE` | a: int, b: bool | `void` |
| `OBJECT.SET_PICKUP_TRANSPARENT_WHEN_UNCOLLECTABLE` | a: int, b: bool | `void` |
| `OBJECT.SET_PICKUP_HIDDEN_WHEN_UNCOLLECTABLE` | a: int, b: bool | `void` |
| `OBJECT.SET_PICKUP_OBJECT_TRANSPARENT_WHEN_UNCOLLECTABLE` | a: int, b: bool | `void` |
| `OBJECT.SET_PICKUP_OBJECT_ALPHA_WHEN_TRANSPARENT` | a: int | `void` |
| `OBJECT.SET_PORTABLE_PICKUP_PERSIST` | a: int, b: bool | `void` |
| `OBJECT.ALLOW_PORTABLE_PICKUP_TO_MIGRATE_TO_NON_PARTICIPANTS` | a: int, b: bool | `void` |
| `OBJECT.FORCE_ACTIVATE_PHYSICS_ON_UNFIXED_PICKUP` | a: int, b: bool | `void` |
| `OBJECT.ALLOW_PICKUP_BY_NONE_PARTICIPANT` | a: int, b: bool | `void` |
| `OBJECT.SUPPRESS_PICKUP_REWARD_TYPE` | a: int, b: bool | `void` |
| `OBJECT.CLEAR_ALL_PICKUP_REWARD_TYPE_SUPPRESSION` | — | `void` |
| `OBJECT.CLEAR_PICKUP_REWARD_TYPE_SUPPRESSION` | a: int | `void` |
| `OBJECT.RENDER_FAKE_PICKUP_GLOW` | a1: float, a2: float, a3: float, a4: int | `void` |
| `OBJECT.SET_PICKUP_OBJECT_COLLECTABLE_IN_VEHICLE` | a: int | `void` |
| `OBJECT.SET_PICKUP_TRACK_DAMAGE_EVENTS` | a: int, b: bool | `void` |
| `OBJECT.SET_ENTITY_FLAG_SUPPRESS_SHADOW` | a: int, b: bool | `void` |
| `OBJECT.SET_ENTITY_FLAG_RENDER_SMALL_SHADOW` | a: int, b: bool | `void` |
| `OBJECT.GET_WEAPON_TYPE_FROM_PICKUP_TYPE` | a: hash | `hash` |
| `OBJECT.GET_PICKUP_TYPE_FROM_WEAPON_HASH` | a: hash | `hash` |
| `OBJECT.IS_PICKUP_WEAPON_OBJECT_VALID` | a: int | `bool` |
| `OBJECT.GET_OBJECT_TINT_INDEX` | a: int | `int` |
| `OBJECT.SET_OBJECT_TINT_INDEX` | a: int, b: int | `void` |
| `OBJECT.SET_TINT_INDEX_CLOSEST_BUILDING_OF_TYPE` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: int | `bool` |
| `OBJECT.SET_PROP_TINT_INDEX` | a: int, b: int | `void` |
| `OBJECT.SET_PROP_LIGHT_COLOR` | a1: int, a2: bool, a3: int, a4: int, a5: int | `bool` |
| `OBJECT.IS_PROP_LIGHT_OVERRIDEN` | a: int | `bool` |
| `OBJECT.SET_OBJECT_IS_VISIBLE_IN_MIRRORS` | a: int, b: bool | `void` |
| `OBJECT.SET_OBJECT_SPEED_BOOST_AMOUNT` | a: int, b: int | `void` |
| `OBJECT.SET_OBJECT_SPEED_BOOST_DURATION` | a: int, b: float | `void` |
| `OBJECT.CONVERT_OLD_PICKUP_TYPE_TO_NEW` | a: hash | `hash` |
| `OBJECT.SET_FORCE_OBJECT_THIS_FRAME` | a1: float, a2: float, a3: float, a4: float | `void` |
| `OBJECT.ONLY_CLEAN_UP_OBJECT_WHEN_OUT_OF_RANGE` | a: int | `void` |
| `OBJECT.SET_DISABLE_COLLISIONS_BETWEEN_CARS_AND_CAR_PARACHUTE` | a: int | `void` |
| `OBJECT.SET_PROJECTILES_SHOULD_EXPLODE_ON_CONTACT` | a: int, b: int | `void` |
| `OBJECT.SET_DRIVE_ARTICULATED_JOINT` | a1: int, a2: bool, a3: int | `void` |
| `OBJECT.SET_DRIVE_ARTICULATED_JOINT_WITH_INFLICTOR` | a1: int, a2: bool, a3: int, a4: int | `void` |
| `OBJECT.SET_OBJECT_IS_A_PRESSURE_PLATE` | a: int, b: bool | `void` |
| `OBJECT.SET_WEAPON_IMPACTS_APPLY_GREATER_FORCE` | a: int, b: bool | `void` |
| `OBJECT.GET_IS_ARTICULATED_JOINT_AT_MIN_ANGLE` | a: int, b: int | `bool` |
| `OBJECT.GET_IS_ARTICULATED_JOINT_AT_MAX_ANGLE` | a: int, b: int | `bool` |
| `OBJECT.SET_IS_OBJECT_ARTICULATED` | a: int, b: bool | `void` |
| `OBJECT.SET_IS_OBJECT_BALL` | a: int, b: bool | `void` |

## PAD

| Native | Params | Returns |
| --- | --- | --- |
| `PAD.IS_CONTROL_ENABLED` | a: int, b: int | `bool` |
| `PAD.IS_CONTROL_PRESSED` | a: int, b: int | `bool` |
| `PAD.IS_CONTROL_RELEASED` | a: int, b: int | `bool` |
| `PAD.IS_CONTROL_JUST_PRESSED` | a: int, b: int | `bool` |
| `PAD.IS_CONTROL_JUST_RELEASED` | a: int, b: int | `bool` |
| `PAD._IS_CONTROL_HELD_DOWN` | a1: int, a2: int, a3: int | `bool` |
| `PAD.GET_CONTROL_VALUE` | a: int, b: int | `int` |
| `PAD.GET_CONTROL_NORMAL` | a: int, b: int | `float` |
| `PAD.SET_USE_ADJUSTED_MOUSE_COORDS` | a: bool | `void` |
| `PAD.GET_CONTROL_UNBOUND_NORMAL` | a: int, b: int | `float` |
| `PAD.SET_CONTROL_VALUE_NEXT_FRAME` | a1: int, a2: int, a3: float | `bool` |
| `PAD.IS_DISABLED_CONTROL_PRESSED` | a: int, b: int | `bool` |
| `PAD.IS_DISABLED_CONTROL_RELEASED` | a: int, b: int | `bool` |
| `PAD.IS_DISABLED_CONTROL_JUST_PRESSED` | a: int, b: int | `bool` |
| `PAD.IS_DISABLED_CONTROL_JUST_RELEASED` | a: int, b: int | `bool` |
| `PAD.GET_DISABLED_CONTROL_NORMAL` | a: int, b: int | `float` |
| `PAD.GET_DISABLED_CONTROL_UNBOUND_NORMAL` | a: int, b: int | `float` |
| `PAD.GET_CONTROL_HOW_LONG_AGO` | a: int | `int` |
| `PAD.IS_USING_KEYBOARD_AND_MOUSE` | a: int | `bool` |
| `PAD.IS_USING_CURSOR` | a: int | `bool` |
| `PAD.SET_CURSOR_POSITION` | a: float, b: float | `bool` |
| `PAD.IS_USING_REMOTE_PLAY` | a: int | `bool` |
| `PAD.HAVE_CONTROLS_CHANGED` | a: int | `bool` |
| `PAD.GET_CONTROL_INSTRUCTIONAL_BUTTONS_STRING` | a1: int, a2: int, a3: bool | `string` |
| `PAD.GET_CONTROL_GROUP_INSTRUCTIONAL_BUTTONS_STRING` | a1: int, a2: int, a3: bool | `string` |
| `PAD.SET_CONTROL_LIGHT_EFFECT_COLOR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `PAD.CLEAR_CONTROL_LIGHT_EFFECT` | a: int | `void` |
| `PAD.SET_CONTROL_SHAKE` | a1: int, a2: int, a3: int | `void` |
| `PAD.SET_CONTROL_TRIGGER_SHAKE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `PAD.STOP_CONTROL_SHAKE` | a: int | `void` |
| `PAD.SET_CONTROL_SHAKE_SUPPRESSED_ID` | a: int, b: int | `void` |
| `PAD.CLEAR_CONTROL_SHAKE_SUPPRESSED_ID` | a: int | `void` |
| `PAD.IS_LOOK_INVERTED` | — | `bool` |
| `PAD.IS_MOUSE_LOOK_INVERTED` | — | `bool` |
| `PAD.GET_LOCAL_PLAYER_AIM_STATE` | — | `int` |
| `PAD.GET_LOCAL_PLAYER_GAMEPAD_AIM_STATE` | — | `int` |
| `PAD.GET_IS_USING_ALTERNATE_HANDBRAKE` | — | `bool` |
| `PAD.GET_IS_USING_ALTERNATE_DRIVEBY` | — | `bool` |
| `PAD.GET_ALLOW_MOVEMENT_WHILE_ZOOMED` | — | `bool` |
| `PAD.SET_PLAYERPAD_SHAKES_WHEN_CONTROLLER_DISABLED` | a: bool | `void` |
| `PAD.SET_INPUT_EXCLUSIVE` | a: int, b: int | `void` |
| `PAD.DISABLE_CONTROL_ACTION` | a1: int, a2: int, a3: bool | `void` |
| `PAD.ENABLE_CONTROL_ACTION` | a1: int, a2: int, a3: bool | `void` |
| `PAD.DISABLE_ALL_CONTROL_ACTIONS` | a: int | `void` |
| `PAD.ENABLE_ALL_CONTROL_ACTIONS` | a: int | `void` |
| `PAD.INIT_PC_SCRIPTED_CONTROLS` | a: string | `bool` |
| `PAD.SWITCH_PC_SCRIPTED_CONTROLS` | a: string | `bool` |
| `PAD.SHUTDOWN_PC_SCRIPTED_CONTROLS` | — | `void` |
| `PAD.ALLOW_ALTERNATIVE_SCRIPT_CONTROLS_LAYOUT` | a: int | `void` |
| `PAD._GET_GAMEPAD_TYPE` | — | `int` |

## PATH

| Native | Params | Returns |
| --- | --- | --- |
| `PATH.SET_ROADS_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool | `void` |
| `PATH.SET_ROADS_IN_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool, a9: bool, a10: bool | `void` |
| `PATH.SET_PED_PATHS_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: int | `void` |
| `PATH.GET_SAFE_COORD_FOR_PED` | a1: float, a2: float, a3: float, a4: bool, a5: vector3, a6: int | `bool` |
| `PATH.GET_CLOSEST_VEHICLE_NODE` | a1: float, a2: float, a3: float, a4: vector3, a5: int, a6: float, a7: float | `bool` |
| `PATH.GET_CLOSEST_MAJOR_VEHICLE_NODE` | a1: float, a2: float, a3: float, a4: vector3, a5: float, a6: float | `bool` |
| `PATH.GET_CLOSEST_VEHICLE_NODE_WITH_HEADING` | a1: float, a2: float, a3: float, a4: vector3, a5: pointer, a6: int, a7: float, a8: float | `bool` |
| `PATH.GET_NTH_CLOSEST_VEHICLE_NODE` | a1: float, a2: float, a3: float, a4: int, a5: vector3, a6: int, a7: float, a8: float | `bool` |
| `PATH.GET_NTH_CLOSEST_VEHICLE_NODE_ID` | a1: float, a2: float, a3: float, a4: int, a5: int, a6: float, a7: float | `int` |
| `PATH.GET_NTH_CLOSEST_VEHICLE_NODE_WITH_HEADING` | a1: float, a2: float, a3: float, a4: int, a5: vector3, a6: pointer, a7: pointer, a8: int, a9: float, a10: float | `bool` |
| `PATH.GET_NTH_CLOSEST_VEHICLE_NODE_ID_WITH_HEADING` | a1: float, a2: float, a3: float, a4: int, a5: vector3, a6: pointer, a7: int, a8: float, a9: float | `int` |
| `PATH.GET_NTH_CLOSEST_VEHICLE_NODE_FAVOUR_DIRECTION` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: vector3, a9: pointer, a10: int, a11: float, a12: float | `bool` |
| `PATH.GET_VEHICLE_NODE_PROPERTIES` | a1: float, a2: float, a3: float, a4: pointer, a5: pointer | `bool` |
| `PATH.IS_VEHICLE_NODE_ID_VALID` | a: int | `bool` |
| `PATH.GET_VEHICLE_NODE_POSITION` | a: int, b: vector3 | `void` |
| `PATH.GET_VEHICLE_NODE_IS_GPS_ALLOWED` | a: int | `bool` |
| `PATH.GET_VEHICLE_NODE_IS_SWITCHED_OFF` | a: int | `bool` |
| `PATH.GET_CLOSEST_ROAD` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: vector3, a7: vector3, a8: pointer, a9: pointer, a10: pointer, a11: bool | `bool` |
| `PATH.LOAD_ALL_PATH_NODES` | a: bool | `bool` |
| `PATH.SET_ALLOW_STREAM_PROLOGUE_NODES` | a: bool | `void` |
| `PATH.SET_ALLOW_STREAM_HEIST_ISLAND_NODES` | a: int | `void` |
| `PATH.ARE_NODES_LOADED_FOR_AREA` | a1: float, a2: float, a3: float, a4: float | `bool` |
| `PATH.REQUEST_PATH_NODES_IN_AREA_THIS_FRAME` | a1: float, a2: float, a3: float, a4: float | `bool` |
| `PATH.SET_ROADS_BACK_TO_ORIGINAL` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `void` |
| `PATH.SET_ROADS_BACK_TO_ORIGINAL_IN_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int | `void` |
| `PATH.SET_AMBIENT_PED_RANGE_MULTIPLIER_THIS_FRAME` | a: float | `void` |
| `PATH.ADJUST_AMBIENT_PED_SPAWN_DENSITIES_THIS_FRAME` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `PATH.SET_PED_PATHS_BACK_TO_ORIGINAL` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `void` |
| `PATH.GET_RANDOM_VEHICLE_NODE` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: bool, a8: vector3, a9: pointer | `bool` |
| `PATH.GET_SPAWN_COORDS_FOR_VEHICLE_NODE` | a1: int, a2: float, a3: float, a4: float, a5: vector3, a6: pointer | `void` |
| `PATH.GET_STREET_NAME_AT_COORD` | a1: float, a2: float, a3: float, a4: pointer, a5: pointer | `void` |
| `PATH.GENERATE_DIRECTIONS_TO_COORD` | a1: float, a2: float, a3: float, a4: bool, a5: pointer, a6: pointer, a7: pointer | `int` |
| `PATH.SET_IGNORE_NO_GPS_FLAG` | a: bool | `void` |
| `PATH.SET_IGNORE_NO_GPS_FLAG_UNTIL_FIRST_NORMAL_NODE` | a: bool | `void` |
| `PATH.SET_GPS_DISABLED_ZONE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `void` |
| `PATH.GET_GPS_BLIP_ROUTE_LENGTH` | — | `int` |
| `PATH.GET_POS_ALONG_GPS_TYPE_ROUTE` | a1: vector3, a2: bool, a3: float, a4: int | `bool` |
| `PATH.GET_GPS_BLIP_ROUTE_FOUND` | — | `bool` |
| `PATH.GET_ROAD_BOUNDARY_USING_HEADING` | a1: float, a2: float, a3: float, a4: float, a5: vector3 | `bool` |
| `PATH.GET_POSITION_BY_SIDE_OF_ROAD` | a1: float, a2: float, a3: float, a4: int, a5: vector3 | `bool` |
| `PATH.IS_POINT_ON_ROAD` | a1: float, a2: float, a3: float, a4: int | `bool` |
| `PATH.GET_NEXT_GPS_DISABLED_ZONE_INDEX` | — | `int` |
| `PATH.SET_GPS_DISABLED_ZONE_AT_INDEX` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `void` |
| `PATH.CLEAR_GPS_DISABLED_ZONE_AT_INDEX` | a: int | `void` |
| `PATH.ADD_NAVMESH_REQUIRED_REGION` | a1: float, a2: float, a3: float | `void` |
| `PATH.REMOVE_NAVMESH_REQUIRED_REGIONS` | — | `void` |
| `PATH.IS_NAVMESH_REQUIRED_REGION_IN_USE` | — | `bool` |
| `PATH.DISABLE_NAVMESH_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool | `void` |
| `PATH.ARE_ALL_NAVMESH_REGIONS_LOADED` | — | `bool` |
| `PATH.IS_NAVMESH_LOADED_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `bool` |
| `PATH.GET_NUM_NAVMESHES_EXISTING_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `int` |
| `PATH.ADD_NAVMESH_BLOCKING_OBJECT` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool, a9: int | `int` |
| `PATH.UPDATE_NAVMESH_BLOCKING_OBJECT` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int | `void` |
| `PATH.REMOVE_NAVMESH_BLOCKING_OBJECT` | a: int | `void` |
| `PATH.DOES_NAVMESH_BLOCKING_OBJECT_EXIST` | a: int | `bool` |
| `PATH.GET_APPROX_HEIGHT_FOR_POINT` | a: float, b: float | `float` |
| `PATH.GET_APPROX_HEIGHT_FOR_AREA` | a1: float, a2: float, a3: float, a4: float | `float` |
| `PATH.GET_APPROX_FLOOR_FOR_POINT` | a: float, b: float | `float` |
| `PATH.GET_APPROX_FLOOR_FOR_AREA` | a1: float, a2: float, a3: float, a4: float | `float` |
| `PATH.CALCULATE_TRAVEL_DISTANCE_BETWEEN_POINTS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `float` |

## PED

| Native | Params | Returns |
| --- | --- | --- |
| `PED.CREATE_PED` | a1: int, a2: hash, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool | `int` |
| `PED.DELETE_PED` | a: pointer | `void` |
| `PED.CLONE_PED` | a1: int, a2: bool, a3: bool, a4: bool | `int` |
| `PED.CLONE_PED_ALT` | a1: int, a2: bool, a3: bool, a4: bool, a5: bool | `int` |
| `PED.CLONE_PED_TO_TARGET` | a: int, b: int | `void` |
| `PED.CLONE_PED_TO_TARGET_ALT` | a1: int, a2: int, a3: bool | `void` |
| `PED.IS_PED_IN_VEHICLE` | a1: int, a2: int, a3: bool | `bool` |
| `PED.IS_PED_IN_MODEL` | a: int, b: hash | `bool` |
| `PED.IS_PED_IN_ANY_VEHICLE` | a: int, b: bool | `bool` |
| `PED.IS_COP_PED_IN_AREA_3D` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `bool` |
| `PED.IS_PED_INJURED` | a: int | `bool` |
| `PED.IS_PED_HURT` | a: int | `bool` |
| `PED.IS_PED_FATALLY_INJURED` | a: int | `bool` |
| `PED.IS_PED_DEAD_OR_DYING` | a: int, b: bool | `bool` |
| `PED.IS_CONVERSATION_PED_DEAD` | a: int | `bool` |
| `PED.IS_PED_AIMING_FROM_COVER` | a: int | `bool` |
| `PED.IS_PED_RELOADING` | a: int | `bool` |
| `PED.IS_PED_A_PLAYER` | a: int | `bool` |
| `PED.CREATE_PED_INSIDE_VEHICLE` | a1: int, a2: int, a3: hash, a4: int, a5: bool, a6: bool | `int` |
| `PED.SET_PED_DESIRED_HEADING` | a: int, b: float | `void` |
| `PED.FORCE_ALL_HEADING_VALUES_TO_ALIGN` | a: int | `void` |
| `PED.IS_PED_FACING_PED` | a1: int, a2: int, a3: float | `bool` |
| `PED.IS_PED_IN_MELEE_COMBAT` | a: int | `bool` |
| `PED.IS_PED_STOPPED` | a: int | `bool` |
| `PED.IS_PED_SHOOTING_IN_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool, a9: bool | `bool` |
| `PED.IS_ANY_PED_SHOOTING_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool | `bool` |
| `PED.IS_PED_SHOOTING` | a: int | `bool` |
| `PED.SET_PED_ACCURACY` | a: int, b: int | `void` |
| `PED.GET_PED_ACCURACY` | a: int | `int` |
| `PED.SET_AMBIENT_LAW_PED_ACCURACY_MODIFIER` | a: float | `void` |
| `PED.IS_PED_MODEL` | a: int, b: hash | `bool` |
| `PED.EXPLODE_PED_HEAD` | a: int, b: hash | `void` |
| `PED.REMOVE_PED_ELEGANTLY` | a: pointer | `void` |
| `PED.ADD_ARMOUR_TO_PED` | a: int, b: int | `void` |
| `PED.SET_PED_ARMOUR` | a: int, b: int | `void` |
| `PED.SET_PED_INTO_VEHICLE` | a1: int, a2: int, a3: int | `void` |
| `PED.SET_PED_ALLOW_VEHICLES_OVERRIDE` | a: int, b: bool | `void` |
| `PED.CAN_CREATE_RANDOM_PED` | a: bool | `bool` |
| `PED.CREATE_RANDOM_PED` | a1: float, a2: float, a3: float | `int` |
| `PED.CREATE_RANDOM_PED_AS_DRIVER` | a: int, b: bool | `int` |
| `PED.CAN_CREATE_RANDOM_DRIVER` | — | `bool` |
| `PED.CAN_CREATE_RANDOM_BIKE_RIDER` | — | `bool` |
| `PED.SET_PED_MOVE_ANIMS_BLEND_OUT` | a: int | `void` |
| `PED.SET_PED_CAN_BE_DRAGGED_OUT` | a: int, b: bool | `void` |
| `PED.SET_PED_ALLOW_HURT_COMBAT_FOR_ALL_MISSION_PEDS` | a: bool | `void` |
| `PED.IS_PED_MALE` | a: int | `bool` |
| `PED.IS_PED_HUMAN` | a: int | `bool` |
| `PED.GET_VEHICLE_PED_IS_IN` | a: int, b: bool | `int` |
| `PED.RESET_PED_LAST_VEHICLE` | a: int | `void` |
| `PED.SET_PED_DENSITY_MULTIPLIER_THIS_FRAME` | a: float | `void` |
| `PED.SET_SCENARIO_PED_DENSITY_MULTIPLIER_THIS_FRAME` | a: float, b: float | `void` |
| `PED.SUPPRESS_AMBIENT_PED_AGGRESSIVE_CLEANUP_THIS_FRAME` | — | `void` |
| `PED.SET_SCRIPTED_CONVERSION_COORD_THIS_FRAME` | a1: float, a2: float, a3: float | `void` |
| `PED.SET_PED_NON_CREATION_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `void` |
| `PED.CLEAR_PED_NON_CREATION_AREA` | — | `void` |
| `PED.INSTANTLY_FILL_PED_POPULATION` | — | `void` |
| `PED.IS_PED_ON_MOUNT` | a: int | `bool` |
| `PED.GET_MOUNT` | a: int | `int` |
| `PED.IS_PED_ON_VEHICLE` | a: int | `bool` |
| `PED.IS_PED_ON_SPECIFIC_VEHICLE` | a: int, b: int | `bool` |
| `PED.SET_PED_MONEY` | a: int, b: int | `void` |
| `PED.GET_PED_MONEY` | a: int | `int` |
| `PED.SET_HEALTH_SNACKS_CARRIED_BY_ALL_NEW_PEDS` | a: float, b: int | `void` |
| `PED.SET_AMBIENT_PEDS_DROP_MONEY` | a: bool | `void` |
| `PED._SET_BLOCK_AMBIENT_PEDS_FROM_DROPPING_WEAPONS_THIS_FRAME` | — | `void` |
| `PED.SET_BLOCKING_OF_NON_TEMPORARY_EVENTS_FOR_AMBIENT_PEDS_THIS_FRAME` | a: bool | `void` |
| `PED.SET_PED_SUFFERS_CRITICAL_HITS` | a: int, b: bool | `void` |
| `PED.SET_PED_UPPER_BODY_DAMAGE_ONLY` | a: int, b: bool | `void` |
| `PED.IS_PED_SITTING_IN_VEHICLE` | a: int, b: int | `bool` |
| `PED.IS_PED_SITTING_IN_ANY_VEHICLE` | a: int | `bool` |
| `PED.IS_PED_ON_FOOT` | a: int | `bool` |
| `PED.IS_PED_ON_ANY_BIKE` | a: int | `bool` |
| `PED.IS_PED_PLANTING_BOMB` | a: int | `bool` |
| `PED.GET_DEAD_PED_PICKUP_COORDS` | a1: int, a2: float, a3: float | `vector3` |
| `PED.IS_PED_IN_ANY_BOAT` | a: int | `bool` |
| `PED.IS_PED_IN_ANY_SUB` | a: int | `bool` |
| `PED.IS_PED_IN_ANY_HELI` | a: int | `bool` |
| `PED.IS_PED_IN_ANY_PLANE` | a: int | `bool` |
| `PED.IS_PED_IN_FLYING_VEHICLE` | a: int | `bool` |
| `PED.SET_PED_DIES_IN_WATER` | a: int, b: bool | `void` |
| `PED.GET_PED_DIES_IN_WATER` | a: int | `bool` |
| `PED.SET_PED_DIES_IN_SINKING_VEHICLE` | a: int, b: bool | `void` |
| `PED.GET_PED_ARMOUR` | a: int | `int` |
| `PED.SET_PED_STAY_IN_VEHICLE_WHEN_JACKED` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_BE_SHOT_IN_VEHICLE` | a: int, b: bool | `void` |
| `PED.GET_PED_LAST_DAMAGE_BONE` | a: int, b: pointer | `bool` |
| `PED.CLEAR_PED_LAST_DAMAGE_BONE` | a: int | `void` |
| `PED.SET_AI_WEAPON_DAMAGE_MODIFIER` | a: float | `void` |
| `PED.RESET_AI_WEAPON_DAMAGE_MODIFIER` | — | `void` |
| `PED.SET_AI_MELEE_WEAPON_DAMAGE_MODIFIER` | a: float | `void` |
| `PED.RESET_AI_MELEE_WEAPON_DAMAGE_MODIFIER` | — | `void` |
| `PED.SET_TREAT_AS_AMBIENT_PED_FOR_DRIVER_LOCKON` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_BE_TARGETTED` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_BE_TARGETTED_BY_TEAM` | a1: int, a2: int, a3: bool | `void` |
| `PED.SET_PED_CAN_BE_TARGETTED_BY_PLAYER` | a1: int, a2: int, a3: bool | `void` |
| `PED.SET_ALLOW_LOCKON_TO_PED_IF_FRIENDLY` | a: int, b: bool | `void` |
| `PED.SET_USE_CAMERA_HEADING_FOR_DESIRED_DIRECTION_LOCK_ON_TEST` | a: int, b: bool | `void` |
| `PED.IS_PED_IN_ANY_POLICE_VEHICLE` | a: int | `bool` |
| `PED.FORCE_PED_TO_OPEN_PARACHUTE` | a: int | `void` |
| `PED.IS_PED_IN_PARACHUTE_FREE_FALL` | a: int | `bool` |
| `PED.IS_PED_FALLING` | a: int | `bool` |
| `PED.IS_PED_JUMPING` | a: int | `bool` |
| `PED.IS_PED_LANDING` | a: int | `bool` |
| `PED.IS_PED_DOING_A_BEAST_JUMP` | a: int | `bool` |
| `PED.IS_PED_CLIMBING` | a: int | `bool` |
| `PED.IS_PED_VAULTING` | a: int | `bool` |
| `PED.IS_PED_DIVING` | a: int | `bool` |
| `PED.IS_PED_JUMPING_OUT_OF_VEHICLE` | a: int | `bool` |
| `PED.IS_PED_OPENING_DOOR` | a: int | `bool` |
| `PED.GET_PED_PARACHUTE_STATE` | a: int | `int` |
| `PED.GET_PED_PARACHUTE_LANDING_TYPE` | a: int | `int` |
| `PED.SET_PED_PARACHUTE_TINT_INDEX` | a: int, b: int | `void` |
| `PED.GET_PED_PARACHUTE_TINT_INDEX` | a: int, b: pointer | `void` |
| `PED.SET_PED_RESERVE_PARACHUTE_TINT_INDEX` | a: int, b: int | `void` |
| `PED.CREATE_PARACHUTE_BAG_OBJECT` | a1: int, a2: bool, a3: bool | `int` |
| `PED.SET_PED_DUCKING` | a: int, b: bool | `void` |
| `PED.IS_PED_DUCKING` | a: int | `bool` |
| `PED.IS_PED_IN_ANY_TAXI` | a: int | `bool` |
| `PED.SET_PED_ID_RANGE` | a: int, b: float | `void` |
| `PED.SET_PED_HIGHLY_PERCEPTIVE` | a: int, b: bool | `void` |
| `PED.SET_COP_PERCEPTION_OVERRIDES` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `PED.SET_PED_INJURED_ON_GROUND_BEHAVIOUR` | a: int, b: float | `void` |
| `PED.DISABLE_PED_INJURED_ON_GROUND_BEHAVIOUR` | a: int | `void` |
| `PED.SET_PED_SEEING_RANGE` | a: int, b: float | `void` |
| `PED.SET_PED_HEARING_RANGE` | a: int, b: float | `void` |
| `PED.SET_PED_VISUAL_FIELD_MIN_ANGLE` | a: int, b: float | `void` |
| `PED.SET_PED_VISUAL_FIELD_MAX_ANGLE` | a: int, b: float | `void` |
| `PED.SET_PED_VISUAL_FIELD_MIN_ELEVATION_ANGLE` | a: int, b: float | `void` |
| `PED.SET_PED_VISUAL_FIELD_MAX_ELEVATION_ANGLE` | a: int, b: float | `void` |
| `PED.SET_PED_VISUAL_FIELD_PERIPHERAL_RANGE` | a: int, b: float | `void` |
| `PED.SET_PED_VISUAL_FIELD_CENTER_ANGLE` | a: int, b: float | `void` |
| `PED.GET_PED_VISUAL_FIELD_CENTER_ANGLE` | a: int | `float` |
| `PED.SET_PED_STEALTH_MOVEMENT` | a1: int, a2: bool, a3: string | `void` |
| `PED.GET_PED_STEALTH_MOVEMENT` | a: int | `bool` |
| `PED.CREATE_GROUP` | a: int | `int` |
| `PED.SET_PED_AS_GROUP_LEADER` | a: int, b: int | `void` |
| `PED.SET_PED_AS_GROUP_MEMBER` | a: int, b: int | `void` |
| `PED.SET_PED_CAN_TELEPORT_TO_GROUP_LEADER` | a1: int, a2: int, a3: bool | `void` |
| `PED.REMOVE_GROUP` | a: int | `void` |
| `PED.REMOVE_PED_FROM_GROUP` | a: int | `void` |
| `PED.IS_PED_GROUP_MEMBER` | a: int, b: int | `bool` |
| `PED.IS_PED_HANGING_ON_TO_VEHICLE` | a: int | `bool` |
| `PED.SET_GROUP_SEPARATION_RANGE` | a: int, b: float | `void` |
| `PED.SET_PED_MIN_GROUND_TIME_FOR_STUNGUN` | a: int, b: int | `void` |
| `PED.IS_PED_PRONE` | a: int | `bool` |
| `PED.IS_PED_IN_COMBAT` | a: int, b: int | `bool` |
| `PED.GET_PED_TARGET_FROM_COMBAT_PED` | a: int, b: int | `int` |
| `PED.CAN_PED_IN_COMBAT_SEE_TARGET` | a: int, b: int | `bool` |
| `PED.IS_PED_DOING_DRIVEBY` | a: int | `bool` |
| `PED.IS_PED_JACKING` | a: int | `bool` |
| `PED.IS_PED_BEING_JACKED` | a: int | `bool` |
| `PED.IS_PED_BEING_STUNNED` | a: int, b: int | `bool` |
| `PED.GET_PEDS_JACKER` | a: int | `int` |
| `PED.GET_JACK_TARGET` | a: int | `int` |
| `PED.IS_PED_FLEEING` | a: int | `bool` |
| `PED.IS_PED_IN_COVER` | a: int, b: bool | `bool` |
| `PED.IS_PED_IN_COVER_FACING_LEFT` | a: int | `bool` |
| `PED.IS_PED_IN_HIGH_COVER` | a: int | `bool` |
| `PED.IS_PED_GOING_INTO_COVER` | a: int | `bool` |
| `PED.SET_PED_PINNED_DOWN` | a1: int, a2: bool, a3: int | `bool` |
| `PED._HAS_PED_CLEAR_LOS_TO_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: int, a7: bool, a8: bool | `bool` |
| `PED.GET_SEAT_PED_IS_TRYING_TO_ENTER` | a: int | `int` |
| `PED.GET_VEHICLE_PED_IS_TRYING_TO_ENTER` | a: int | `int` |
| `PED.GET_PED_SOURCE_OF_DEATH` | a: int | `int` |
| `PED.GET_PED_CAUSE_OF_DEATH` | a: int | `hash` |
| `PED.GET_PED_TIME_OF_DEATH` | a: int | `int` |
| `PED.COUNT_PEDS_IN_COMBAT_WITH_TARGET` | a: int | `int` |
| `PED.COUNT_PEDS_IN_COMBAT_WITH_TARGET_WITHIN_RADIUS` | a1: int, a2: float, a3: float, a4: float, a5: float | `int` |
| `PED.SET_PED_RELATIONSHIP_GROUP_DEFAULT_HASH` | a: int, b: hash | `void` |
| `PED.SET_PED_RELATIONSHIP_GROUP_HASH` | a: int, b: hash | `void` |
| `PED.SET_RELATIONSHIP_BETWEEN_GROUPS` | a1: int, a2: hash, a3: hash | `void` |
| `PED.CLEAR_RELATIONSHIP_BETWEEN_GROUPS` | a1: int, a2: hash, a3: hash | `void` |
| `PED.ADD_RELATIONSHIP_GROUP` | a: string, b: pointer | `bool` |
| `PED.REMOVE_RELATIONSHIP_GROUP` | a: hash | `void` |
| `PED.DOES_RELATIONSHIP_GROUP_EXIST` | a: hash | `bool` |
| `PED.GET_RELATIONSHIP_BETWEEN_PEDS` | a: int, b: int | `int` |
| `PED.GET_PED_RELATIONSHIP_GROUP_DEFAULT_HASH` | a: int | `hash` |
| `PED.GET_PED_RELATIONSHIP_GROUP_HASH` | a: int | `hash` |
| `PED.GET_RELATIONSHIP_BETWEEN_GROUPS` | a: hash, b: hash | `int` |
| `PED.SET_RELATIONSHIP_GROUP_AFFECTS_WANTED_LEVEL` | a: hash, b: bool | `void` |
| `PED.TELL_GROUP_PEDS_IN_AREA_TO_ATTACK` | a1: int, a2: int, a3: float, a4: hash, a5: int, a6: int | `void` |
| `PED.SET_PED_CAN_BE_TARGETED_WITHOUT_LOS` | a: int, b: bool | `void` |
| `PED.SET_PED_TO_INFORM_RESPECTED_FRIENDS` | a1: int, a2: float, a3: int | `void` |
| `PED.IS_PED_RESPONDING_TO_EVENT` | a: int, b: int | `bool` |
| `PED.GET_POS_FROM_FIRED_EVENT` | a1: int, a2: int, a3: pointer | `bool` |
| `PED.SET_PED_FIRING_PATTERN` | a: int, b: hash | `void` |
| `PED.SET_PED_SHOOT_RATE` | a: int, b: int | `void` |
| `PED.SET_COMBAT_FLOAT` | a1: int, a2: int, a3: float | `void` |
| `PED.GET_COMBAT_FLOAT` | a: int, b: int | `float` |
| `PED.GET_GROUP_SIZE` | a1: int, a2: pointer, a3: pointer | `void` |
| `PED.DOES_GROUP_EXIST` | a: int | `bool` |
| `PED.GET_PED_GROUP_INDEX` | a: int | `int` |
| `PED.IS_PED_IN_GROUP` | a: int | `bool` |
| `PED.GET_PLAYER_PED_IS_FOLLOWING` | a: int | `int` |
| `PED.SET_GROUP_FORMATION` | a: int, b: int | `void` |
| `PED.SET_GROUP_FORMATION_SPACING` | a1: int, a2: float, a3: float, a4: float | `void` |
| `PED.RESET_GROUP_FORMATION_DEFAULT_SPACING` | a: int | `void` |
| `PED.GET_VEHICLE_PED_IS_USING` | a: int | `int` |
| `PED.GET_VEHICLE_PED_IS_ENTERING` | a: int | `int` |
| `PED.SET_PED_GRAVITY` | a: int, b: bool | `void` |
| `PED._SET_PED_SURVIVES_BEING_OUT_OF_WATER` | a: int, b: bool | `bool` |
| `PED.APPLY_DAMAGE_TO_PED` | a1: int, a2: int, a3: bool, a4: int, a5: hash | `void` |
| `PED.GET_TIME_PED_DAMAGED_BY_WEAPON` | a: int, b: hash | `int` |
| `PED.SET_PED_ALLOWED_TO_DUCK` | a: int, b: bool | `void` |
| `PED.SET_PED_NEVER_LEAVES_GROUP` | a: int, b: bool | `void` |
| `PED.GET_PED_TYPE` | a: int | `int` |
| `PED.SET_PED_AS_COP` | a: int, b: bool | `void` |
| `PED.SET_PED_HEALTH_PENDING_LAST_DAMAGE_EVENT_OVERRIDE_FLAG` | a: bool | `void` |
| `PED.SET_PED_MAX_HEALTH` | a: int, b: int | `void` |
| `PED.GET_PED_MAX_HEALTH` | a: int | `int` |
| `PED.SET_PED_MAX_TIME_IN_WATER` | a: int, b: float | `void` |
| `PED.SET_PED_MAX_TIME_UNDERWATER` | a: int, b: float | `void` |
| `PED.SET_CORPSE_RAGDOLL_FRICTION` | a: int, b: float | `void` |
| `PED.SET_PED_VEHICLE_FORCED_SEAT_USAGE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `PED.CLEAR_ALL_PED_VEHICLE_FORCED_SEAT_USAGE` | a: int | `void` |
| `PED.SET_PED_CAN_BE_KNOCKED_OFF_BIKE` | a: int, b: int | `void` |
| `PED.SET_PED_CAN_BE_KNOCKED_OFF_VEHICLE` | a: int, b: int | `void` |
| `PED.CAN_KNOCK_PED_OFF_VEHICLE` | a: int | `bool` |
| `PED.KNOCK_PED_OFF_VEHICLE` | a: int | `void` |
| `PED.SET_PED_COORDS_NO_GANG` | a1: int, a2: float, a3: float, a4: float | `void` |
| `PED.GET_PED_AS_GROUP_MEMBER` | a: int, b: int | `int` |
| `PED.GET_PED_AS_GROUP_LEADER` | a: int | `int` |
| `PED.SET_PED_KEEP_TASK` | a: int, b: bool | `void` |
| `PED.SET_PED_ALLOW_MINOR_REACTIONS_AS_MISSION_PED` | a: int, b: bool | `void` |
| `PED.IS_PED_SWIMMING` | a: int | `bool` |
| `PED.IS_PED_SWIMMING_UNDER_WATER` | a: int | `bool` |
| `PED.SET_PED_COORDS_KEEP_VEHICLE` | a1: int, a2: float, a3: float, a4: float | `void` |
| `PED.SET_PED_DIES_IN_VEHICLE` | a: int, b: bool | `void` |
| `PED.SET_CREATE_RANDOM_COPS` | a: bool | `void` |
| `PED.SET_CREATE_RANDOM_COPS_NOT_ON_SCENARIOS` | a: bool | `void` |
| `PED.SET_CREATE_RANDOM_COPS_ON_SCENARIOS` | a: bool | `void` |
| `PED.CAN_CREATE_RANDOM_COPS` | — | `bool` |
| `PED.SET_PED_AS_ENEMY` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_SMASH_GLASS` | a1: int, a2: bool, a3: bool | `void` |
| `PED.IS_PED_IN_ANY_TRAIN` | a: int | `bool` |
| `PED.IS_PED_GETTING_INTO_A_VEHICLE` | a: int | `bool` |
| `PED.SET_ENABLE_HANDCUFFS` | a: int, b: bool | `void` |
| `PED.SET_ENABLE_BOUND_ANKLES` | a: int, b: bool | `void` |
| `PED.SET_ENABLE_SCUBA` | a: int, b: bool | `void` |
| `PED.SET_CAN_ATTACK_FRIENDLY` | a1: int, a2: bool, a3: bool | `void` |
| `PED.GET_PED_ALERTNESS` | a: int | `int` |
| `PED.SET_PED_ALERTNESS` | a: int, b: int | `void` |
| `PED.SET_PED_GET_OUT_UPSIDE_DOWN_VEHICLE` | a: int, b: bool | `void` |
| `PED.SET_PED_MOVEMENT_CLIPSET` | a1: int, a2: string, a3: float | `void` |
| `PED.RESET_PED_MOVEMENT_CLIPSET` | a: int, b: float | `void` |
| `PED.SET_PED_STRAFE_CLIPSET` | a: int, b: string | `void` |
| `PED.RESET_PED_STRAFE_CLIPSET` | a: int | `void` |
| `PED.SET_PED_WEAPON_MOVEMENT_CLIPSET` | a: int, b: string | `void` |
| `PED.RESET_PED_WEAPON_MOVEMENT_CLIPSET` | a: int | `void` |
| `PED.SET_PED_DRIVE_BY_CLIPSET_OVERRIDE` | a: int, b: string | `void` |
| `PED.CLEAR_PED_DRIVE_BY_CLIPSET_OVERRIDE` | a: int | `void` |
| `PED.SET_PED_MOTION_IN_COVER_CLIPSET_OVERRIDE` | a: int, b: string | `void` |
| `PED.CLEAR_PED_MOTION_IN_COVER_CLIPSET_OVERRIDE` | a: int | `void` |
| `PED.CLEAR_PED_FALL_UPPER_BODY_CLIPSET_OVERRIDE` | a: int | `void` |
| `PED.SET_PED_IN_VEHICLE_CONTEXT` | a: int, b: hash | `void` |
| `PED.RESET_PED_IN_VEHICLE_CONTEXT` | a: int | `void` |
| `PED.IS_SCRIPTED_SCENARIO_PED_USING_CONDITIONAL_ANIM` | a1: int, a2: string, a3: string | `bool` |
| `PED.SET_PED_ALTERNATE_WALK_ANIM` | a1: int, a2: string, a3: string, a4: float, a5: bool | `void` |
| `PED.CLEAR_PED_ALTERNATE_WALK_ANIM` | a: int, b: float | `void` |
| `PED.SET_PED_ALTERNATE_MOVEMENT_ANIM` | a1: int, a2: int, a3: string, a4: string, a5: float, a6: bool | `void` |
| `PED.CLEAR_PED_ALTERNATE_MOVEMENT_ANIM` | a1: int, a2: int, a3: float | `void` |
| `PED.SET_PED_GESTURE_GROUP` | a: int, b: string | `void` |
| `PED.GET_ANIM_INITIAL_OFFSET_POSITION` | a1: string, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: int | `vector3` |
| `PED.GET_ANIM_INITIAL_OFFSET_ROTATION` | a1: string, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: int | `vector3` |
| `PED.GET_PED_DRAWABLE_VARIATION` | a: int, b: int | `int` |
| `PED.GET_NUMBER_OF_PED_DRAWABLE_VARIATIONS` | a: int, b: int | `int` |
| `PED.GET_PED_TEXTURE_VARIATION` | a: int, b: int | `int` |
| `PED.GET_NUMBER_OF_PED_TEXTURE_VARIATIONS` | a1: int, a2: int, a3: int | `int` |
| `PED.GET_NUMBER_OF_PED_PROP_DRAWABLE_VARIATIONS` | a: int, b: int | `int` |
| `PED.GET_NUMBER_OF_PED_PROP_TEXTURE_VARIATIONS` | a1: int, a2: int, a3: int | `int` |
| `PED.GET_PED_PALETTE_VARIATION` | a: int, b: int | `int` |
| `PED.GET_MP_OUTFIT_DATA_FROM_METADATA` | a: pointer, b: pointer | `bool` |
| `PED.GET_FM_MALE_SHOP_PED_APPAREL_ITEM_INDEX` | a: int | `int` |
| `PED.GET_FM_FEMALE_SHOP_PED_APPAREL_ITEM_INDEX` | a: int | `int` |
| `PED.IS_PED_COMPONENT_VARIATION_VALID` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `PED.SET_PED_COMPONENT_VARIATION` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `PED.SET_PED_RANDOM_COMPONENT_VARIATION` | a: int, b: int | `void` |
| `PED.SET_PED_RANDOM_PROPS` | a: int | `void` |
| `PED.SET_PED_DEFAULT_COMPONENT_VARIATION` | a: int | `void` |
| `PED.SET_PED_BLEND_FROM_PARENTS` | a1: int, a2: int, a3: int, a4: float, a5: float | `void` |
| `PED.SET_PED_HEAD_BLEND_DATA` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: float, a9: float, a10: float, a11: bool | `void` |
| `PED.GET_PED_HEAD_BLEND_DATA` | a: int, b: pointer | `bool` |
| `PED.UPDATE_PED_HEAD_BLEND_DATA` | a1: int, a2: float, a3: float, a4: float | `void` |
| `PED.SET_HEAD_BLEND_EYE_COLOR` | a: int, b: int | `void` |
| `PED.GET_HEAD_BLEND_EYE_COLOR` | a: int | `int` |
| `PED.SET_PED_HEAD_OVERLAY` | a1: int, a2: int, a3: int, a4: float | `void` |
| `PED.GET_PED_HEAD_OVERLAY` | a: int, b: int | `int` |
| `PED.GET_PED_HEAD_OVERLAY_NUM` | a: int | `int` |
| `PED.SET_PED_HEAD_OVERLAY_TINT` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `PED.SET_PED_HAIR_TINT` | a1: int, a2: int, a3: int | `void` |
| `PED.GET_NUM_PED_HAIR_TINTS` | — | `int` |
| `PED.GET_NUM_PED_MAKEUP_TINTS` | — | `int` |
| `PED.GET_PED_HAIR_TINT_COLOR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `PED.GET_PED_MAKEUP_TINT_COLOR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `PED.IS_PED_HAIR_TINT_FOR_CREATOR` | a: int | `bool` |
| `PED.GET_DEFAULT_SECONDARY_TINT_FOR_CREATOR` | a: int | `int` |
| `PED.IS_PED_LIPSTICK_TINT_FOR_CREATOR` | a: int | `bool` |
| `PED.IS_PED_BLUSH_TINT_FOR_CREATOR` | a: int | `bool` |
| `PED.IS_PED_HAIR_TINT_FOR_BARBER` | a: int | `bool` |
| `PED.GET_DEFAULT_SECONDARY_TINT_FOR_BARBER` | a: int | `int` |
| `PED.IS_PED_LIPSTICK_TINT_FOR_BARBER` | a: int | `bool` |
| `PED.IS_PED_BLUSH_TINT_FOR_BARBER` | a: int | `bool` |
| `PED.IS_PED_BLUSH_FACEPAINT_TINT_FOR_BARBER` | a: int | `bool` |
| `PED.GET_TINT_INDEX_FOR_LAST_GEN_HAIR_TEXTURE` | a1: hash, a2: int, a3: int | `int` |
| `PED.SET_PED_MICRO_MORPH` | a1: int, a2: int, a3: float | `void` |
| `PED.HAS_PED_HEAD_BLEND_FINISHED` | a: int | `bool` |
| `PED.FINALIZE_HEAD_BLEND` | a: int | `void` |
| `PED.SET_HEAD_BLEND_PALETTE_COLOR` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `PED.DISABLE_HEAD_BLEND_PALETTE_COLOR` | a: int | `void` |
| `PED.GET_PED_HEAD_BLEND_FIRST_INDEX` | a: int | `int` |
| `PED.GET_PED_HEAD_BLEND_NUM_HEADS` | a: int | `int` |
| `PED.SET_PED_PRELOAD_VARIATION_DATA` | a1: int, a2: int, a3: int, a4: int | `int` |
| `PED.HAS_PED_PRELOAD_VARIATION_DATA_FINISHED` | a: int | `bool` |
| `PED.RELEASE_PED_PRELOAD_VARIATION_DATA` | a: int | `void` |
| `PED.SET_PED_PRELOAD_PROP_DATA` | a1: int, a2: int, a3: int, a4: int | `int` |
| `PED.HAS_PED_PRELOAD_PROP_DATA_FINISHED` | a: int | `bool` |
| `PED.RELEASE_PED_PRELOAD_PROP_DATA` | a: int | `void` |
| `PED.GET_PED_PROP_INDEX` | a1: int, a2: int, a3: int | `int` |
| `PED.SET_PED_PROP_INDEX` | a1: int, a2: int, a3: int, a4: int, a5: bool, a6: int | `void` |
| `PED.KNOCK_OFF_PED_PROP` | a1: int, a2: bool, a3: bool, a4: bool, a5: bool | `void` |
| `PED.CLEAR_PED_PROP` | a1: int, a2: int, a3: int | `void` |
| `PED.CLEAR_ALL_PED_PROPS` | a: int, b: int | `void` |
| `PED.DROP_AMBIENT_PROP` | a: int | `void` |
| `PED.GET_PED_PROP_TEXTURE_INDEX` | a: int, b: int | `int` |
| `PED.CLEAR_PED_PARACHUTE_PACK_VARIATION` | a: int | `void` |
| `PED.SET_PED_SCUBA_GEAR_VARIATION` | a: int | `void` |
| `PED.CLEAR_PED_SCUBA_GEAR_VARIATION` | a: int | `void` |
| `PED.IS_USING_PED_SCUBA_GEAR_VARIATION` | a: int | `bool` |
| `PED.SET_BLOCKING_OF_NON_TEMPORARY_EVENTS` | a: int, b: bool | `void` |
| `PED.SET_PED_BOUNDS_ORIENTATION` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float | `void` |
| `PED.REGISTER_TARGET` | a: int, b: int | `void` |
| `PED.REGISTER_HATED_TARGETS_AROUND_PED` | a: int, b: float | `void` |
| `PED.GET_RANDOM_PED_AT_COORD` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `int` |
| `PED.GET_CLOSEST_PED` | a1: float, a2: float, a3: float, a4: float, a5: bool, a6: bool, a7: pointer, a8: bool, a9: bool, a10: int | `bool` |
| `PED.SET_SCENARIO_PEDS_TO_BE_RETURNED_BY_NEXT_COMMAND` | a: bool | `void` |
| `PED.GET_CAN_PED_BE_GRABBED_BY_SCRIPT` | a1: int, a2: bool, a3: bool, a4: bool, a5: bool, a6: bool, a7: bool, a8: bool, a9: int | `bool` |
| `PED.SET_DRIVER_RACING_MODIFIER` | a: int, b: float | `void` |
| `PED.SET_DRIVER_ABILITY` | a: int, b: float | `void` |
| `PED.SET_DRIVER_AGGRESSIVENESS` | a: int, b: float | `void` |
| `PED.CAN_PED_RAGDOLL` | a: int | `bool` |
| `PED.SET_PED_TO_RAGDOLL` | a1: int, a2: int, a3: int, a4: int, a5: bool, a6: bool, a7: bool | `bool` |
| `PED.SET_PED_TO_RAGDOLL_WITH_FALL` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: float | `bool` |
| `PED.SET_PED_RAGDOLL_ON_COLLISION` | a: int, b: bool | `void` |
| `PED.IS_PED_RAGDOLL` | a: int | `bool` |
| `PED.IS_PED_RUNNING_RAGDOLL_TASK` | a: int | `bool` |
| `PED.SET_PED_RAGDOLL_FORCE_FALL` | a: int | `void` |
| `PED.RESET_PED_RAGDOLL_TIMER` | a: int | `void` |
| `PED.SET_PED_CAN_RAGDOLL` | a: int, b: bool | `void` |
| `PED.IS_PED_RUNNING_MELEE_TASK` | a: int | `bool` |
| `PED.IS_PED_RUNNING_MOBILE_PHONE_TASK` | a: int | `bool` |
| `PED.IS_MOBILE_PHONE_TO_PED_EAR` | a: int | `bool` |
| `PED.SET_RAGDOLL_BLOCKING_FLAGS` | a: int, b: int | `void` |
| `PED.CLEAR_RAGDOLL_BLOCKING_FLAGS` | a: int, b: int | `void` |
| `PED.SET_PED_ANGLED_DEFENSIVE_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: bool | `void` |
| `PED.SET_PED_SPHERE_DEFENSIVE_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: bool, a7: bool | `void` |
| `PED.SET_PED_DEFENSIVE_SPHERE_ATTACHED_TO_PED` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: bool | `void` |
| `PED.SET_PED_DEFENSIVE_SPHERE_ATTACHED_TO_VEHICLE` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: bool | `void` |
| `PED.SET_PED_DEFENSIVE_AREA_ATTACHED_TO_PED` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: bool, a11: bool | `void` |
| `PED.SET_PED_DEFENSIVE_AREA_DIRECTION` | a1: int, a2: float, a3: float, a4: float, a5: bool | `void` |
| `PED.REMOVE_PED_DEFENSIVE_AREA` | a: int, b: bool | `void` |
| `PED.GET_PED_DEFENSIVE_AREA_POSITION` | a: int, b: bool | `vector3` |
| `PED.IS_PED_DEFENSIVE_AREA_ACTIVE` | a: int, b: bool | `bool` |
| `PED.SET_PED_PREFERRED_COVER_SET` | a: int, b: int | `void` |
| `PED.REMOVE_PED_PREFERRED_COVER_SET` | a: int | `void` |
| `PED.REVIVE_INJURED_PED` | a: int | `void` |
| `PED.RESURRECT_PED` | a: int | `void` |
| `PED.SET_PED_NAME_DEBUG` | a: int, b: string | `void` |
| `PED.GET_PED_EXTRACTED_DISPLACEMENT` | a: int, b: bool | `vector3` |
| `PED.SET_PED_DIES_WHEN_INJURED` | a: int, b: bool | `void` |
| `PED.SET_PED_ENABLE_WEAPON_BLOCKING` | a: int, b: bool | `void` |
| `PED.SPECIAL_FUNCTION_DO_NOT_USE` | a: int, b: bool | `void` |
| `PED.RESET_PED_VISIBLE_DAMAGE` | a: int | `void` |
| `PED.APPLY_PED_BLOOD_DAMAGE_BY_ZONE` | a1: int, a2: int, a3: float, a4: float, a5: int | `void` |
| `PED.APPLY_PED_BLOOD` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: string | `void` |
| `PED.APPLY_PED_BLOOD_BY_ZONE` | a1: int, a2: int, a3: float, a4: float, a5: string | `void` |
| `PED.APPLY_PED_BLOOD_SPECIFIC` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: int, a8: float, a9: string | `void` |
| `PED.APPLY_PED_DAMAGE_DECAL` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: bool, a10: string | `void` |
| `PED.APPLY_PED_DAMAGE_PACK` | a1: int, a2: string, a3: float, a4: float | `void` |
| `PED.CLEAR_PED_BLOOD_DAMAGE` | a: int | `void` |
| `PED.CLEAR_PED_BLOOD_DAMAGE_BY_ZONE` | a: int, b: int | `void` |
| `PED.HIDE_PED_BLOOD_DAMAGE_BY_ZONE` | a1: int, a2: int, a3: bool | `void` |
| `PED.CLEAR_PED_DAMAGE_DECAL_BY_ZONE` | a1: int, a2: int, a3: string | `void` |
| `PED.GET_PED_DECORATIONS_STATE` | a: int | `int` |
| `PED.MARK_PED_DECORATIONS_AS_CLONED_FROM_LOCAL_PLAYER` | a: int, b: bool | `void` |
| `PED.CLEAR_PED_WETNESS` | a: int | `void` |
| `PED.SET_PED_WETNESS_HEIGHT` | a: int, b: float | `void` |
| `PED.SET_PED_WETNESS_ENABLED_THIS_FRAME` | a: int | `void` |
| `PED.SET_PED_WETNESS` | a: int, b: float | `void` |
| `PED.CLEAR_PED_ENV_DIRT` | a: int | `void` |
| `PED.SET_PED_SWEAT` | a: int, b: float | `void` |
| `PED.ADD_PED_DECORATION_FROM_HASHES` | a1: int, a2: hash, a3: hash | `void` |
| `PED.ADD_PED_DECORATION_FROM_HASHES_IN_CORONA` | a1: int, a2: hash, a3: hash | `void` |
| `PED.GET_PED_DECORATION_ZONE_FROM_HASHES` | a: hash, b: hash | `int` |
| `PED.CLEAR_PED_DECORATIONS` | a: int | `void` |
| `PED.CLEAR_PED_DECORATIONS_LEAVE_SCARS` | a: int | `void` |
| `PED.WAS_PED_SKELETON_UPDATED` | a: int | `bool` |
| `PED.GET_PED_BONE_COORDS` | a1: int, a2: int, a3: float, a4: float, a5: float | `vector3` |
| `PED.CREATE_NM_MESSAGE` | a: bool, b: int | `void` |
| `PED.GIVE_PED_NM_MESSAGE` | a: int | `void` |
| `PED.ADD_SCENARIO_BLOCKING_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool, a9: bool, a10: bool, a11: int | `int` |
| `PED.REMOVE_SCENARIO_BLOCKING_AREAS` | — | `void` |
| `PED.REMOVE_SCENARIO_BLOCKING_AREA` | a: int, b: bool | `void` |
| `PED.SET_SCENARIO_PEDS_SPAWN_IN_SPHERE_AREA` | a1: float, a2: float, a3: float, a4: float, a5: int | `void` |
| `PED.DOES_SCENARIO_BLOCKING_AREA_EXISTS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `bool` |
| `PED.IS_PED_USING_SCENARIO` | a: int, b: string | `bool` |
| `PED.IS_PED_USING_ANY_SCENARIO` | a: int | `bool` |
| `PED.SET_PED_PANIC_EXIT_SCENARIO` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `PED.TOGGLE_SCENARIO_PED_COWER_IN_PLACE` | a: int, b: bool | `void` |
| `PED.TRIGGER_PED_SCENARIO_PANICEXITTOFLEE` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `PED.SET_PED_SHOULD_PLAY_DIRECTED_NORMAL_SCENARIO_EXIT` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `PED.SET_PED_SHOULD_PLAY_NORMAL_SCENARIO_EXIT` | a: int | `void` |
| `PED.SET_PED_SHOULD_PLAY_IMMEDIATE_SCENARIO_EXIT` | a: int | `void` |
| `PED.SET_PED_SHOULD_PLAY_FLEE_SCENARIO_EXIT` | a1: int, a2: int, a3: int, a4: int | `bool` |
| `PED.SET_PED_SHOULD_IGNORE_SCENARIO_EXIT_COLLISION_CHECKS` | a: int, b: bool | `void` |
| `PED.SET_PED_SHOULD_IGNORE_SCENARIO_NAV_CHECKS` | a: int, b: bool | `void` |
| `PED.SET_PED_SHOULD_PROBE_FOR_SCENARIO_EXITS_IN_ONE_FRAME` | a: int, b: bool | `void` |
| `PED.IS_PED_GESTURING` | a: int | `bool` |
| `PED.RESET_FACIAL_IDLE_ANIM` | a: int | `void` |
| `PED.PLAY_FACIAL_ANIM` | a1: int, a2: string, a3: string | `void` |
| `PED.SET_FACIAL_CLIPSET` | a: int, b: string | `void` |
| `PED.SET_FACIAL_IDLE_ANIM_OVERRIDE` | a1: int, a2: string, a3: string | `void` |
| `PED.CLEAR_FACIAL_IDLE_ANIM_OVERRIDE` | a: int | `void` |
| `PED.SET_PED_CAN_PLAY_GESTURE_ANIMS` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_PLAY_VISEME_ANIMS` | a1: int, a2: bool, a3: bool | `void` |
| `PED.SET_PED_IS_IGNORED_BY_AUTO_OPEN_DOORS` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_PLAY_AMBIENT_ANIMS` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_PLAY_AMBIENT_BASE_ANIMS` | a: int, b: bool | `void` |
| `PED.TRIGGER_IDLE_ANIMATION_ON_PED` | a: int | `void` |
| `PED.SET_PED_CAN_ARM_IK` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_BODY_RECOIL_IK` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_HEAD_IK` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_LEG_IK` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_TORSO_IK` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_TORSO_REACT_IK` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_TORSO_VEHICLE_IK` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_USE_AUTO_CONVERSATION_LOOKAT` | a: int, b: bool | `void` |
| `PED.IS_PED_HEADTRACKING_PED` | a: int, b: int | `bool` |
| `PED.IS_PED_HEADTRACKING_ENTITY` | a: int, b: int | `bool` |
| `PED.SET_PED_PRIMARY_LOOKAT` | a: int, b: int | `void` |
| `PED.SET_PED_CLOTH_PIN_FRAMES` | a: int, b: int | `void` |
| `PED.SET_PED_CLOTH_PACKAGE_INDEX` | a: int, b: int | `void` |
| `PED.SET_PED_CLOTH_PRONE` | a: int, b: bool | `void` |
| `PED.SET_PED_CONFIG_FLAG` | a1: int, a2: int, a3: bool | `void` |
| `PED.SET_PED_RESET_FLAG` | a1: int, a2: int, a3: bool | `void` |
| `PED.GET_PED_CONFIG_FLAG` | a1: int, a2: int, a3: bool | `bool` |
| `PED.GET_PED_RESET_FLAG` | a: int, b: int | `bool` |
| `PED.SET_PED_GROUP_MEMBER_PASSENGER_INDEX` | a: int, b: int | `void` |
| `PED.SET_PED_CAN_EVASIVE_DIVE` | a: int, b: bool | `void` |
| `PED.IS_PED_EVASIVE_DIVING` | a: int, b: pointer | `bool` |
| `PED.SET_PED_SHOOTS_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: bool | `void` |
| `PED.SET_PED_MODEL_IS_SUPPRESSED` | a: hash, b: bool | `void` |
| `PED.STOP_ANY_PED_MODEL_BEING_SUPPRESSED` | — | `void` |
| `PED.SET_PED_CAN_BE_TARGETED_WHEN_INJURED` | a: int, b: bool | `void` |
| `PED.SET_PED_GENERATES_DEAD_BODY_EVENTS` | a: int, b: bool | `void` |
| `PED.BLOCK_PED_FROM_GENERATING_DEAD_BODY_EVENTS_WHEN_DEAD` | a: int, b: bool | `void` |
| `PED.SET_PED_WILL_ONLY_ATTACK_WANTED_PLAYER` | a: int, b: int | `void` |
| `PED.SET_PED_CAN_RAGDOLL_FROM_PLAYER_IMPACT` | a: int, b: bool | `void` |
| `PED.GIVE_PED_HELMET` | a1: int, a2: bool, a3: int, a4: int | `void` |
| `PED.REMOVE_PED_HELMET` | a: int, b: bool | `void` |
| `PED.IS_PED_TAKING_OFF_HELMET` | a: int | `bool` |
| `PED.SET_PED_HELMET` | a: int, b: bool | `void` |
| `PED.SET_PED_HELMET_FLAG` | a: int, b: int | `void` |
| `PED.SET_PED_HELMET_PROP_INDEX` | a1: int, a2: int, a3: bool | `void` |
| `PED.SET_PED_HELMET_VISOR_PROP_INDICES` | a1: int, a2: bool, a3: int, a4: int | `void` |
| `PED.IS_PED_HELMET_VISOR_UP` | a: int | `bool` |
| `PED.SET_PED_HELMET_TEXTURE_INDEX` | a: int, b: int | `void` |
| `PED.IS_PED_WEARING_HELMET` | a: int | `bool` |
| `PED.CLEAR_PED_STORED_HAT_PROP` | a: int | `void` |
| `PED.GET_PED_HELMET_STORED_HAT_PROP_INDEX` | a: int | `int` |
| `PED.GET_PED_HELMET_STORED_HAT_TEX_INDEX` | a: int | `int` |
| `PED.IS_CURRENT_HEAD_PROP_A_HELMET` | a: int | `bool` |
| `PED.SET_PED_TO_LOAD_COVER` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_COWER_IN_COVER` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_PEEK_IN_COVER` | a: int, b: bool | `void` |
| `PED.SET_PED_PLAYS_HEAD_ON_HORN_ANIM_WHEN_DIES_IN_VEHICLE` | a: int, b: bool | `void` |
| `PED.SET_PED_LEG_IK_MODE` | a: int, b: int | `void` |
| `PED.SET_PED_MOTION_BLUR` | a: int, b: bool | `void` |
| `PED.SET_PED_CAN_SWITCH_WEAPON` | a: int, b: bool | `void` |
| `PED.SET_PED_DIES_INSTANTLY_IN_WATER` | a: int, b: bool | `void` |
| `PED.SET_LADDER_CLIMB_INPUT_STATE` | a: int, b: int | `void` |
| `PED.STOP_PED_WEAPON_FIRING_WHEN_DROPPED` | a: int | `void` |
| `PED.SET_SCRIPTED_ANIM_SEAT_OFFSET` | a: int, b: float | `void` |
| `PED.SET_PED_COMBAT_MOVEMENT` | a: int, b: int | `void` |
| `PED.GET_PED_COMBAT_MOVEMENT` | a: int | `int` |
| `PED.SET_PED_COMBAT_ABILITY` | a: int, b: int | `void` |
| `PED.SET_PED_COMBAT_RANGE` | a: int, b: int | `void` |
| `PED.GET_PED_COMBAT_RANGE` | a: int | `int` |
| `PED.SET_PED_COMBAT_ATTRIBUTES` | a1: int, a2: int, a3: bool | `void` |
| `PED.SET_PED_TARGET_LOSS_RESPONSE` | a: int, b: int | `void` |
| `PED.IS_PED_PERFORMING_MELEE_ACTION` | a: int | `bool` |
| `PED.IS_PED_PERFORMING_STEALTH_KILL` | a: int | `bool` |
| `PED.IS_PED_PERFORMING_A_COUNTER_ATTACK` | a: int | `bool` |
| `PED.IS_PED_BEING_STEALTH_KILLED` | a: int | `bool` |
| `PED.GET_MELEE_TARGET_FOR_PED` | a: int | `int` |
| `PED.WAS_PED_KILLED_BY_STEALTH` | a: int | `bool` |
| `PED.WAS_PED_KILLED_BY_TAKEDOWN` | a: int | `bool` |
| `PED.WAS_PED_KNOCKED_OUT` | a: int | `bool` |
| `PED.SET_PED_FLEE_ATTRIBUTES` | a1: int, a2: int, a3: bool | `void` |
| `PED.SET_PED_COWER_HASH` | a: int, b: string | `void` |
| `PED.SET_PED_STEERS_AROUND_DEAD_BODIES` | a: int, b: bool | `void` |
| `PED.SET_PED_STEERS_AROUND_PEDS` | a: int, b: bool | `void` |
| `PED.SET_PED_STEERS_AROUND_OBJECTS` | a: int, b: bool | `void` |
| `PED.SET_PED_STEERS_AROUND_VEHICLES` | a: int, b: bool | `void` |
| `PED.SET_PED_IS_AVOIDED_BY_OTHERS` | a: int, b: bool | `void` |
| `PED.SET_PED_INCREASED_AVOIDANCE_RADIUS` | a: int | `void` |
| `PED.SET_PED_BLOCKS_PATHING_WHEN_DEAD` | a: int, b: bool | `void` |
| `PED.SET_PED_NO_TIME_DELAY_BEFORE_SHOT` | a: int | `void` |
| `PED.IS_ANY_PED_NEAR_POINT` | a1: float, a2: float, a3: float, a4: float | `bool` |
| `PED.FORCE_PED_AI_AND_ANIMATION_UPDATE` | a1: int, a2: bool, a3: bool | `void` |
| `PED.IS_PED_HEADING_TOWARDS_POSITION` | a1: int, a2: float, a3: float, a4: float, a5: float | `bool` |
| `PED.REQUEST_PED_VISIBILITY_TRACKING` | a: int | `void` |
| `PED.REQUEST_PED_VEHICLE_VISIBILITY_TRACKING` | a: int, b: bool | `void` |
| `PED.REQUEST_PED_RESTRICTED_VEHICLE_VISIBILITY_TRACKING` | a: int, b: bool | `void` |
| `PED.REQUEST_PED_USE_SMALL_BBOX_VISIBILITY_TRACKING` | a: int, b: bool | `void` |
| `PED.IS_TRACKED_PED_VISIBLE` | a: int | `bool` |
| `PED.GET_TRACKED_PED_PIXELCOUNT` | a: int | `int` |
| `PED.IS_PED_TRACKED` | a: int | `bool` |
| `PED.HAS_PED_RECEIVED_EVENT` | a: int, b: int | `bool` |
| `PED.CAN_PED_SEE_HATED_PED` | a: int, b: int | `bool` |
| `PED.CAN_PED_SHUFFLE_TO_OR_FROM_TURRET_SEAT` | a: int, b: pointer | `bool` |
| `PED.CAN_PED_SHUFFLE_TO_OR_FROM_EXTRA_SEAT` | a: int, b: pointer | `bool` |
| `PED.GET_PED_BONE_INDEX` | a: int, b: int | `int` |
| `PED.GET_PED_RAGDOLL_BONE_INDEX` | a: int, b: int | `int` |
| `PED.SET_PED_ENVEFF_SCALE` | a: int, b: float | `void` |
| `PED.GET_PED_ENVEFF_SCALE` | a: int | `float` |
| `PED.SET_ENABLE_PED_ENVEFF_SCALE` | a: int, b: bool | `void` |
| `PED.SET_PED_ENVEFF_CPV_ADD` | a: int, b: float | `void` |
| `PED.SET_PED_ENVEFF_COLOR_MODULATOR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `PED.SET_PED_EMISSIVE_SCALE` | a: int, b: float | `void` |
| `PED.GET_PED_EMISSIVE_SCALE` | a: int | `float` |
| `PED.IS_PED_SHADER_READY` | a: int | `bool` |
| `PED.SET_PED_ENABLE_CREW_EMBLEM` | a: int, b: bool | `void` |
| `PED.REQUEST_RAGDOLL_BOUNDS_UPDATE` | a: int, b: int | `void` |
| `PED.SET_PED_AO_BLOB_RENDERING` | a: int, b: bool | `void` |
| `PED.IS_PED_SHELTERED` | a: int | `bool` |
| `PED.CREATE_SYNCHRONIZED_SCENE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `int` |
| `PED.CREATE_SYNCHRONIZED_SCENE_AT_MAP_OBJECT` | a1: float, a2: float, a3: float, a4: float, a5: hash | `int` |
| `PED.IS_SYNCHRONIZED_SCENE_RUNNING` | a: int | `bool` |
| `PED.SET_SYNCHRONIZED_SCENE_ORIGIN` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: bool | `void` |
| `PED.SET_SYNCHRONIZED_SCENE_PHASE` | a: int, b: float | `void` |
| `PED.GET_SYNCHRONIZED_SCENE_PHASE` | a: int | `float` |
| `PED.SET_SYNCHRONIZED_SCENE_RATE` | a: int, b: float | `void` |
| `PED.GET_SYNCHRONIZED_SCENE_RATE` | a: int | `float` |
| `PED.SET_SYNCHRONIZED_SCENE_LOOPED` | a: int, b: bool | `void` |
| `PED.IS_SYNCHRONIZED_SCENE_LOOPED` | a: int | `bool` |
| `PED.SET_SYNCHRONIZED_SCENE_HOLD_LAST_FRAME` | a: int, b: bool | `void` |
| `PED.IS_SYNCHRONIZED_SCENE_HOLD_LAST_FRAME` | a: int | `bool` |
| `PED.ATTACH_SYNCHRONIZED_SCENE_TO_ENTITY` | a1: int, a2: int, a3: int | `void` |
| `PED.DETACH_SYNCHRONIZED_SCENE` | a: int | `void` |
| `PED.TAKE_OWNERSHIP_OF_SYNCHRONIZED_SCENE` | a: int | `void` |
| `PED.FORCE_PED_MOTION_STATE` | a1: int, a2: hash, a3: bool, a4: int, a5: bool | `bool` |
| `PED.GET_PED_CURRENT_MOVE_BLEND_RATIO` | a1: int, a2: pointer, a3: pointer | `bool` |
| `PED.SET_PED_MAX_MOVE_BLEND_RATIO` | a: int, b: float | `void` |
| `PED.SET_PED_MIN_MOVE_BLEND_RATIO` | a: int, b: float | `void` |
| `PED.SET_PED_MOVE_RATE_OVERRIDE` | a: int, b: float | `void` |
| `PED.SET_PED_MOVE_RATE_IN_WATER_OVERRIDE` | a: int, b: float | `void` |
| `PED.PED_HAS_SEXINESS_FLAG_SET` | a: int, b: int | `bool` |
| `PED.GET_PED_NEARBY_VEHICLES` | a: int, b: pointer | `int` |
| `PED.GET_PED_NEARBY_PEDS` | a1: int, a2: pointer, a3: int | `int` |
| `PED.HAVE_ALL_STREAMING_REQUESTS_COMPLETED` | a: int | `bool` |
| `PED.IS_PED_USING_ACTION_MODE` | a: int | `bool` |
| `PED.SET_PED_USING_ACTION_MODE` | a1: int, a2: bool, a3: int, a4: string | `void` |
| `PED.SET_MOVEMENT_MODE_OVERRIDE` | a: int, b: string | `void` |
| `PED.SET_PED_CAPSULE` | a: int, b: float | `void` |
| `PED.REGISTER_PEDHEADSHOT` | a: int | `int` |
| `PED.REGISTER_PEDHEADSHOT_HIRES` | a: int | `int` |
| `PED.REGISTER_PEDHEADSHOT_TRANSPARENT` | a: int | `int` |
| `PED.UNREGISTER_PEDHEADSHOT` | a: int | `void` |
| `PED.IS_PEDHEADSHOT_VALID` | a: int | `bool` |
| `PED.IS_PEDHEADSHOT_READY` | a: int | `bool` |
| `PED.GET_PEDHEADSHOT_TXD_STRING` | a: int | `string` |
| `PED.REQUEST_PEDHEADSHOT_IMG_UPLOAD` | a: int | `bool` |
| `PED.RELEASE_PEDHEADSHOT_IMG_UPLOAD` | a: int | `void` |
| `PED.IS_PEDHEADSHOT_IMG_UPLOAD_AVAILABLE` | — | `bool` |
| `PED.HAS_PEDHEADSHOT_IMG_UPLOAD_FAILED` | — | `bool` |
| `PED.HAS_PEDHEADSHOT_IMG_UPLOAD_SUCCEEDED` | — | `bool` |
| `PED.SET_PED_HEATSCALE_OVERRIDE` | a: int, b: float | `void` |
| `PED.DISABLE_PED_HEATSCALE_OVERRIDE` | a: int | `void` |
| `PED.SPAWNPOINTS_START_SEARCH` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: int, a7: float, a8: int | `void` |
| `PED.SPAWNPOINTS_START_SEARCH_IN_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: float, a10: int | `void` |
| `PED.SPAWNPOINTS_CANCEL_SEARCH` | — | `void` |
| `PED.SPAWNPOINTS_IS_SEARCH_ACTIVE` | — | `bool` |
| `PED.SPAWNPOINTS_IS_SEARCH_COMPLETE` | — | `bool` |
| `PED.SPAWNPOINTS_IS_SEARCH_FAILED` | — | `bool` |
| `PED.SPAWNPOINTS_GET_NUM_SEARCH_RESULTS` | — | `int` |
| `PED.SPAWNPOINTS_GET_SEARCH_RESULT` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `PED.SPAWNPOINTS_GET_SEARCH_RESULT_FLAGS` | a: int, b: pointer | `void` |
| `PED.SET_IK_TARGET` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int | `void` |
| `PED.FORCE_INSTANT_LEG_IK_SETUP` | a: int | `void` |
| `PED.REQUEST_ACTION_MODE_ASSET` | a: string | `void` |
| `PED.HAS_ACTION_MODE_ASSET_LOADED` | a: string | `bool` |
| `PED.REMOVE_ACTION_MODE_ASSET` | a: string | `void` |
| `PED.REQUEST_STEALTH_MODE_ASSET` | a: string | `void` |
| `PED.HAS_STEALTH_MODE_ASSET_LOADED` | a: string | `bool` |
| `PED.REMOVE_STEALTH_MODE_ASSET` | a: string | `void` |
| `PED.SET_PED_LOD_MULTIPLIER` | a: int, b: float | `void` |
| `PED.SET_PED_CAN_LOSE_PROPS_ON_DAMAGE` | a1: int, a2: bool, a3: int | `void` |
| `PED.SET_FORCE_FOOTSTEP_UPDATE` | a: int, b: bool | `void` |
| `PED.SET_FORCE_STEP_TYPE` | a1: int, a2: bool, a3: int, a4: int | `void` |
| `PED.IS_ANY_HOSTILE_PED_NEAR_POINT` | a1: int, a2: float, a3: float, a4: float, a5: float | `bool` |
| `PED.SET_PED_CAN_PLAY_IN_CAR_IDLES` | a: int, b: bool | `void` |
| `PED.IS_TARGET_PED_IN_PERCEPTION_AREA` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float | `bool` |
| `PED.SET_POP_CONTROL_SPHERE_THIS_FRAME` | a1: float, a2: float, a3: float, a4: float, a5: float | `void` |
| `PED.FORCE_ZERO_MASS_IN_COLLISIONS` | a: int | `void` |
| `PED.SET_DISABLE_HIGH_FALL_DEATH` | a: int, b: bool | `void` |
| `PED.SET_PED_PHONE_PALETTE_IDX` | a: int, b: int | `void` |
| `PED.SET_PED_STEER_BIAS` | a: int, b: float | `void` |
| `PED.IS_PED_SWITCHING_WEAPON` | a: int | `bool` |
| `PED.SET_PED_TREATED_AS_FRIENDLY` | a1: int, a2: int, a3: int | `void` |
| `PED.SET_DISABLE_PED_MAP_COLLISION` | a: int | `void` |
| `PED.ENABLE_MP_LIGHT` | a: int, b: bool | `void` |
| `PED.GET_MP_LIGHT_ENABLED` | a: int | `bool` |
| `PED.CLEAR_COVER_POINT_FOR_PED` | a: int | `void` |
| `PED.SET_ALLOW_STUNT_JUMP_CAMERA` | a: int, b: bool | `void` |

## PHYSICS

| Native | Params | Returns |
| --- | --- | --- |
| `PHYSICS.ADD_ROPE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: float, a10: float, a11: float, a12: bool, a13: bool, a14: bool, a15: float, a16: bool, a17: pointer | `int` |
| `PHYSICS.DELETE_ROPE` | a: pointer | `void` |
| `PHYSICS.DELETE_CHILD_ROPE` | a: int | `void` |
| `PHYSICS.DOES_ROPE_EXIST` | a: pointer | `bool` |
| `PHYSICS.ROPE_DRAW_ENABLED` | a: pointer, b: bool | `void` |
| `PHYSICS.ROPE_DRAW_SHADOW_ENABLED` | a: pointer, b: bool | `void` |
| `PHYSICS.LOAD_ROPE_DATA` | a: int, b: string | `void` |
| `PHYSICS.PIN_ROPE_VERTEX` | a1: int, a2: int, a3: float, a4: float, a5: float | `void` |
| `PHYSICS.UNPIN_ROPE_VERTEX` | a: int, b: int | `void` |
| `PHYSICS.GET_ROPE_VERTEX_COUNT` | a: int | `int` |
| `PHYSICS.ATTACH_ENTITIES_TO_ROPE` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: bool, a12: bool, a13: pointer, a14: pointer | `void` |
| `PHYSICS.ATTACH_ROPE_TO_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool | `void` |
| `PHYSICS.DETACH_ROPE_FROM_ENTITY` | a: int, b: int | `void` |
| `PHYSICS.ROPE_SET_UPDATE_PINVERTS` | a: int | `void` |
| `PHYSICS.ROPE_SET_UPDATE_ORDER` | a: int, b: int | `void` |
| `PHYSICS.ROPE_SET_SMOOTH_REELIN` | a: int, b: bool | `void` |
| `PHYSICS.IS_ROPE_ATTACHED_AT_BOTH_ENDS` | a: pointer | `bool` |
| `PHYSICS.GET_ROPE_LAST_VERTEX_COORD` | a: int | `vector3` |
| `PHYSICS.GET_ROPE_VERTEX_COORD` | a: int, b: int | `vector3` |
| `PHYSICS.START_ROPE_WINDING` | a: int | `void` |
| `PHYSICS.STOP_ROPE_WINDING` | a: int | `void` |
| `PHYSICS.START_ROPE_UNWINDING_FRONT` | a: int | `void` |
| `PHYSICS.STOP_ROPE_UNWINDING_FRONT` | a: int | `void` |
| `PHYSICS.ROPE_CONVERT_TO_SIMPLE` | a: int | `void` |
| `PHYSICS.ROPE_LOAD_TEXTURES` | — | `void` |
| `PHYSICS.ROPE_ARE_TEXTURES_LOADED` | — | `bool` |
| `PHYSICS.ROPE_UNLOAD_TEXTURES` | — | `void` |
| `PHYSICS.DOES_SCRIPT_OWN_ROPE` | a: int | `bool` |
| `PHYSICS.ROPE_ATTACH_VIRTUAL_BOUND_GEOM` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float, a13: float, a14: float | `void` |
| `PHYSICS.ROPE_CHANGE_SCRIPT_OWNER` | a1: int, a2: bool, a3: bool | `void` |
| `PHYSICS.ROPE_SET_REFFRAMEVELOCITY_COLLIDERORDER` | a: int, b: int | `void` |
| `PHYSICS.ROPE_GET_DISTANCE_BETWEEN_ENDS` | a: int | `float` |
| `PHYSICS.ROPE_FORCE_LENGTH` | a: int, b: float | `void` |
| `PHYSICS.ROPE_RESET_LENGTH` | a: int, b: float | `void` |
| `PHYSICS.APPLY_IMPULSE_TO_CLOTH` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `PHYSICS.SET_DAMPING` | a1: int, a2: int, a3: float | `void` |
| `PHYSICS.GET_DAMPING` | a: int, b: int | `vector3` |
| `PHYSICS.ACTIVATE_PHYSICS` | a: int | `void` |
| `PHYSICS.SET_CGOFFSET` | a1: int, a2: float, a3: float, a4: float | `void` |
| `PHYSICS.GET_CGOFFSET` | a: int | `vector3` |
| `PHYSICS.SET_CG_AT_BOUNDCENTER` | a: int | `void` |
| `PHYSICS.BREAK_ENTITY_GLASS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: int, a11: bool | `void` |
| `PHYSICS.GET_IS_ENTITY_A_FRAG` | a: int | `bool` |
| `PHYSICS.SET_DISABLE_BREAKING` | a: int, b: bool | `void` |
| `PHYSICS.RESET_DISABLE_BREAKING` | a: int | `void` |
| `PHYSICS.SET_DISABLE_FRAG_DAMAGE` | a: int, b: bool | `void` |
| `PHYSICS.SET_USE_KINEMATIC_PHYSICS` | a: int, b: bool | `void` |
| `PHYSICS.SET_IN_STUNT_MODE` | a: bool | `void` |
| `PHYSICS.SET_IN_ARENA_MODE` | a: bool | `void` |

## PLAYER

| Native | Params | Returns |
| --- | --- | --- |
| `PLAYER.GET_PLAYER_PED` | a: int | `int` |
| `PLAYER.GET_PLAYER_PED_SCRIPT_INDEX` | a: int | `int` |
| `PLAYER.SET_PLAYER_MODEL` | a: int, b: hash | `void` |
| `PLAYER.CHANGE_PLAYER_PED` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `PLAYER.GET_PLAYER_RGB_COLOUR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `PLAYER.GET_NUMBER_OF_PLAYERS` | — | `int` |
| `PLAYER.GET_PLAYER_TEAM` | a: int | `int` |
| `PLAYER.SET_PLAYER_TEAM` | a: int, b: int | `void` |
| `PLAYER.GET_NUMBER_OF_PLAYERS_IN_TEAM` | a: int | `int` |
| `PLAYER.GET_PLAYER_NAME` | a: int | `string` |
| `PLAYER.GET_WANTED_LEVEL_RADIUS` | a: int | `float` |
| `PLAYER.GET_PLAYER_WANTED_CENTRE_POSITION` | a: int | `vector3` |
| `PLAYER.SET_PLAYER_WANTED_CENTRE_POSITION` | a1: int, a2: vector3, a3: bool, a4: bool | `void` |
| `PLAYER.GET_WANTED_LEVEL_THRESHOLD` | a: int | `int` |
| `PLAYER.SET_PLAYER_WANTED_LEVEL` | a1: int, a2: int, a3: bool | `void` |
| `PLAYER.SET_PLAYER_WANTED_LEVEL_NO_DROP` | a1: int, a2: int, a3: bool | `void` |
| `PLAYER.SET_PLAYER_WANTED_LEVEL_NOW` | a: int, b: bool | `void` |
| `PLAYER.ARE_PLAYER_FLASHING_STARS_ABOUT_TO_DROP` | a: int | `bool` |
| `PLAYER.ARE_PLAYER_STARS_GREYED_OUT` | a: int | `bool` |
| `PLAYER.IS_WANTED_AND_HAS_BEEN_SEEN_BY_COPS` | a: int | `bool` |
| `PLAYER.SET_DISPATCH_COPS_FOR_PLAYER` | a: int, b: bool | `void` |
| `PLAYER.IS_PLAYER_WANTED_LEVEL_GREATER` | a: int, b: int | `bool` |
| `PLAYER.CLEAR_PLAYER_WANTED_LEVEL` | a: int | `void` |
| `PLAYER.IS_PLAYER_DEAD` | a: int | `bool` |
| `PLAYER.IS_PLAYER_PRESSING_HORN` | a: int | `bool` |
| `PLAYER.SET_PLAYER_CONTROL` | a1: int, a2: bool, a3: int | `void` |
| `PLAYER.GET_PLAYER_WANTED_LEVEL` | a: int | `int` |
| `PLAYER.SET_MAX_WANTED_LEVEL` | a: int | `void` |
| `PLAYER.SET_POLICE_RADAR_BLIPS` | a: bool | `void` |
| `PLAYER.SET_POLICE_IGNORE_PLAYER` | a: int, b: bool | `void` |
| `PLAYER.IS_PLAYER_PLAYING` | a: int | `bool` |
| `PLAYER.SET_EVERYONE_IGNORE_PLAYER` | a: int, b: bool | `void` |
| `PLAYER.SET_ALL_RANDOM_PEDS_FLEE` | a: int, b: bool | `void` |
| `PLAYER.SET_ALL_RANDOM_PEDS_FLEE_THIS_FRAME` | a: int | `void` |
| `PLAYER.SET_ALL_NEUTRAL_RANDOM_PEDS_FLEE` | a: int, b: bool | `void` |
| `PLAYER.SET_ALL_NEUTRAL_RANDOM_PEDS_FLEE_THIS_FRAME` | a: int | `void` |
| `PLAYER.SET_LAW_PEDS_CAN_ATTACK_NON_WANTED_PLAYER_THIS_FRAME` | a: int | `void` |
| `PLAYER.SET_IGNORE_LOW_PRIORITY_SHOCKING_EVENTS` | a: int, b: bool | `void` |
| `PLAYER.SET_WANTED_LEVEL_MULTIPLIER` | a: float | `void` |
| `PLAYER.SET_WANTED_LEVEL_DIFFICULTY` | a: int, b: float | `void` |
| `PLAYER.RESET_WANTED_LEVEL_DIFFICULTY` | a: int | `void` |
| `PLAYER.GET_WANTED_LEVEL_TIME_TO_ESCAPE` | — | `int` |
| `PLAYER.SET_WANTED_LEVEL_HIDDEN_ESCAPE_TIME` | a1: int, a2: int, a3: int | `void` |
| `PLAYER.RESET_WANTED_LEVEL_HIDDEN_ESCAPE_TIME` | a: int | `void` |
| `PLAYER.START_FIRING_AMNESTY` | a: int | `void` |
| `PLAYER.REPORT_CRIME` | a1: int, a2: int, a3: int | `void` |
| `PLAYER.SUPPRESS_CRIME_THIS_FRAME` | a: int, b: int | `void` |
| `PLAYER.UPDATE_WANTED_POSITION_THIS_FRAME` | a: int | `void` |
| `PLAYER.SUPPRESS_LOSING_WANTED_LEVEL_IF_HIDDEN_THIS_FRAME` | a: int | `void` |
| `PLAYER.ALLOW_EVASION_HUD_IF_DISABLING_HIDDEN_EVASION_THIS_FRAME` | a: int, b: int | `void` |
| `PLAYER.FORCE_START_HIDDEN_EVASION` | a: int | `void` |
| `PLAYER.SUPPRESS_WITNESSES_CALLING_POLICE_THIS_FRAME` | a: int | `void` |
| `PLAYER.REPORT_POLICE_SPOTTED_PLAYER` | a: int | `void` |
| `PLAYER.SET_LAW_RESPONSE_DELAY_OVERRIDE` | a: float | `void` |
| `PLAYER.RESET_LAW_RESPONSE_DELAY_OVERRIDE` | — | `void` |
| `PLAYER.CAN_PLAYER_START_MISSION` | a: int | `bool` |
| `PLAYER.IS_PLAYER_READY_FOR_CUTSCENE` | a: int | `bool` |
| `PLAYER.IS_PLAYER_TARGETTING_ENTITY` | a: int, b: int | `bool` |
| `PLAYER.GET_PLAYER_TARGET_ENTITY` | a: int, b: pointer | `bool` |
| `PLAYER.IS_PLAYER_FREE_AIMING` | a: int | `bool` |
| `PLAYER.IS_PLAYER_FREE_AIMING_AT_ENTITY` | a: int, b: int | `bool` |
| `PLAYER.GET_ENTITY_PLAYER_IS_FREE_AIMING_AT` | a: int, b: pointer | `bool` |
| `PLAYER.SET_PLAYER_LOCKON_RANGE_OVERRIDE` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_CAN_DO_DRIVE_BY` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_CAN_BE_HASSLED_BY_GANGS` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_CAN_USE_COVER` | a: int, b: bool | `void` |
| `PLAYER.GET_MAX_WANTED_LEVEL` | — | `int` |
| `PLAYER.IS_PLAYER_TARGETTING_ANYTHING` | a: int | `bool` |
| `PLAYER.SET_PLAYER_SPRINT` | a: int, b: bool | `void` |
| `PLAYER.RESET_PLAYER_STAMINA` | a: int | `void` |
| `PLAYER.RESTORE_PLAYER_STAMINA` | a: int, b: float | `void` |
| `PLAYER.GET_PLAYER_SPRINT_STAMINA_REMAINING` | a: int | `float` |
| `PLAYER.GET_PLAYER_SPRINT_TIME_REMAINING` | a: int | `float` |
| `PLAYER.GET_PLAYER_UNDERWATER_TIME_REMAINING` | a: int | `float` |
| `PLAYER.SET_PLAYER_UNDERWATER_BREATH_PERCENT_REMAINING` | a: int, b: float | `float` |
| `PLAYER.GET_PLAYER_GROUP` | a: int | `int` |
| `PLAYER.GET_PLAYER_MAX_ARMOUR` | a: int | `int` |
| `PLAYER.IS_PLAYER_CONTROL_ON` | a: int | `bool` |
| `PLAYER.GET_ARE_CAMERA_CONTROLS_DISABLED` | — | `bool` |
| `PLAYER.IS_PLAYER_SCRIPT_CONTROL_ON` | a: int | `bool` |
| `PLAYER.IS_PLAYER_CLIMBING` | a: int | `bool` |
| `PLAYER.IS_PLAYER_BEING_ARRESTED` | a: int, b: bool | `bool` |
| `PLAYER.RESET_PLAYER_ARREST_STATE` | a: int | `void` |
| `PLAYER.GET_PLAYERS_LAST_VEHICLE` | — | `int` |
| `PLAYER.GET_PLAYER_INDEX` | — | `int` |
| `PLAYER.INT_TO_PLAYERINDEX` | a: int | `int` |
| `PLAYER.INT_TO_PARTICIPANTINDEX` | a: int | `int` |
| `PLAYER.GET_TIME_SINCE_PLAYER_HIT_VEHICLE` | a: int | `int` |
| `PLAYER.GET_TIME_SINCE_PLAYER_HIT_PED` | a: int | `int` |
| `PLAYER.GET_TIME_SINCE_PLAYER_DROVE_ON_PAVEMENT` | a: int | `int` |
| `PLAYER.GET_TIME_SINCE_PLAYER_DROVE_AGAINST_TRAFFIC` | a: int | `int` |
| `PLAYER.IS_PLAYER_FREE_FOR_AMBIENT_TASK` | a: int | `bool` |
| `PLAYER.PLAYER_ID` | — | `int` |
| `PLAYER.PLAYER_PED_ID` | — | `int` |
| `PLAYER.NETWORK_PLAYER_ID_TO_INT` | — | `int` |
| `PLAYER.HAS_FORCE_CLEANUP_OCCURRED` | a: int | `bool` |
| `PLAYER.FORCE_CLEANUP` | a: int | `void` |
| `PLAYER.FORCE_CLEANUP_FOR_ALL_THREADS_WITH_THIS_NAME` | a: string, b: int | `void` |
| `PLAYER.FORCE_CLEANUP_FOR_THREAD_WITH_THIS_ID` | a: int, b: int | `void` |
| `PLAYER.GET_CAUSE_OF_MOST_RECENT_FORCE_CLEANUP` | — | `int` |
| `PLAYER.SET_PLAYER_MAY_ONLY_ENTER_THIS_VEHICLE` | a: int, b: int | `void` |
| `PLAYER.SET_PLAYER_MAY_NOT_ENTER_ANY_VEHICLE` | a: int | `void` |
| `PLAYER.GIVE_ACHIEVEMENT_TO_PLAYER` | a: int | `bool` |
| `PLAYER.SET_ACHIEVEMENT_PROGRESS` | a: int, b: int | `bool` |
| `PLAYER.GET_ACHIEVEMENT_PROGRESS` | a: int | `int` |
| `PLAYER.HAS_ACHIEVEMENT_BEEN_PASSED` | a: int | `bool` |
| `PLAYER.IS_PLAYER_ONLINE` | — | `bool` |
| `PLAYER.IS_PLAYER_LOGGING_IN_NP` | — | `bool` |
| `PLAYER.DISPLAY_SYSTEM_SIGNIN_UI` | a: bool | `void` |
| `PLAYER.IS_SYSTEM_UI_BEING_DISPLAYED` | — | `bool` |
| `PLAYER.SET_PLAYER_INVINCIBLE` | a: int, b: bool | `void` |
| `PLAYER.GET_PLAYER_INVINCIBLE` | a: int | `bool` |
| `PLAYER.GET_PLAYER_DEBUG_INVINCIBLE` | a: int | `bool` |
| `PLAYER.SET_PLAYER_INVINCIBLE_BUT_HAS_REACTIONS` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_CAN_COLLECT_DROPPED_MONEY` | a: int, b: bool | `void` |
| `PLAYER.REMOVE_PLAYER_HELMET` | a: int, b: bool | `void` |
| `PLAYER.GIVE_PLAYER_RAGDOLL_CONTROL` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_LOCKON` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_TARGETING_MODE` | a: int | `void` |
| `PLAYER.GET_PLAYER_TARGETING_MODE` | — | `int` |
| `PLAYER.SET_PLAYER_TARGET_LEVEL` | a: int | `void` |
| `PLAYER.GET_IS_USING_FPS_THIRD_PERSON_COVER` | — | `bool` |
| `PLAYER.GET_IS_USING_HOOD_CAMERA` | — | `bool` |
| `PLAYER.CLEAR_PLAYER_HAS_DAMAGED_AT_LEAST_ONE_PED` | a: int | `void` |
| `PLAYER.HAS_PLAYER_DAMAGED_AT_LEAST_ONE_PED` | a: int | `bool` |
| `PLAYER.CLEAR_PLAYER_HAS_DAMAGED_AT_LEAST_ONE_NON_ANIMAL_PED` | a: int | `void` |
| `PLAYER.HAS_PLAYER_DAMAGED_AT_LEAST_ONE_NON_ANIMAL_PED` | a: int | `bool` |
| `PLAYER.SET_AIR_DRAG_MULTIPLIER_FOR_PLAYERS_VEHICLE` | a: int, b: float | `void` |
| `PLAYER.SET_SWIM_MULTIPLIER_FOR_PLAYER` | a: int, b: float | `void` |
| `PLAYER.SET_RUN_SPRINT_MULTIPLIER_FOR_PLAYER` | a: int, b: float | `void` |
| `PLAYER.GET_TIME_SINCE_LAST_ARREST` | — | `int` |
| `PLAYER.GET_TIME_SINCE_LAST_DEATH` | — | `int` |
| `PLAYER.ASSISTED_MOVEMENT_CLOSE_ROUTE` | — | `void` |
| `PLAYER.ASSISTED_MOVEMENT_FLUSH_ROUTE` | — | `void` |
| `PLAYER.SET_PLAYER_FORCED_AIM` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_FORCED_ZOOM` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_FORCE_SKIP_AIM_INTRO` | a: int, b: bool | `void` |
| `PLAYER.DISABLE_PLAYER_FIRING` | a: int, b: bool | `void` |
| `PLAYER.DISABLE_PLAYER_THROW_GRENADE_WHILE_USING_GUN` | — | `void` |
| `PLAYER.SET_DISABLE_AMBIENT_MELEE_MOVE` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_MAX_ARMOUR` | a: int, b: int | `void` |
| `PLAYER.SPECIAL_ABILITY_ACTIVATE` | a: int, b: int | `void` |
| `PLAYER.SET_SPECIAL_ABILITY_MP` | a1: int, a2: int, a3: int | `void` |
| `PLAYER.SPECIAL_ABILITY_DEACTIVATE_MP` | a: int, b: int | `void` |
| `PLAYER.SPECIAL_ABILITY_DEACTIVATE` | a: int, b: int | `void` |
| `PLAYER.SPECIAL_ABILITY_DEACTIVATE_FAST` | a: int, b: int | `void` |
| `PLAYER.SPECIAL_ABILITY_RESET` | a: int, b: int | `void` |
| `PLAYER.SPECIAL_ABILITY_CHARGE_ON_MISSION_FAILED` | a: int, b: int | `void` |
| `PLAYER.SPECIAL_ABILITY_CHARGE_SMALL` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `PLAYER.SPECIAL_ABILITY_CHARGE_MEDIUM` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `PLAYER.SPECIAL_ABILITY_CHARGE_LARGE` | a1: int, a2: bool, a3: bool, a4: int | `void` |
| `PLAYER.SPECIAL_ABILITY_CHARGE_CONTINUOUS` | a1: int, a2: int, a3: int | `void` |
| `PLAYER.SPECIAL_ABILITY_CHARGE_ABSOLUTE` | a1: int, a2: int, a3: bool, a4: int | `void` |
| `PLAYER.SPECIAL_ABILITY_CHARGE_NORMALIZED` | a1: int, a2: float, a3: bool, a4: int | `void` |
| `PLAYER.SPECIAL_ABILITY_FILL_METER` | a1: int, a2: bool, a3: int | `void` |
| `PLAYER.SPECIAL_ABILITY_DEPLETE_METER` | a1: int, a2: bool, a3: int | `void` |
| `PLAYER.SPECIAL_ABILITY_LOCK` | a: hash, b: int | `void` |
| `PLAYER.SPECIAL_ABILITY_UNLOCK` | a: hash, b: int | `void` |
| `PLAYER.IS_SPECIAL_ABILITY_UNLOCKED` | a: hash | `bool` |
| `PLAYER.IS_SPECIAL_ABILITY_ACTIVE` | a: int, b: int | `bool` |
| `PLAYER.IS_SPECIAL_ABILITY_METER_FULL` | a: int, b: int | `bool` |
| `PLAYER.ENABLE_SPECIAL_ABILITY` | a1: int, a2: bool, a3: int | `void` |
| `PLAYER.IS_SPECIAL_ABILITY_ENABLED` | a: int, b: int | `bool` |
| `PLAYER.SET_SPECIAL_ABILITY_MULTIPLIER` | a: float | `void` |
| `PLAYER.UPDATE_SPECIAL_ABILITY_FROM_STAT` | a: int, b: int | `void` |
| `PLAYER.GET_IS_PLAYER_DRIVING_ON_HIGHWAY` | a: int | `bool` |
| `PLAYER.GET_IS_PLAYER_DRIVING_WRECKLESS` | a: int, b: int | `bool` |
| `PLAYER.GET_IS_MOPPING_AREA_FREE_IN_FRONT_OF_PLAYER` | a: int, b: float | `bool` |
| `PLAYER.START_PLAYER_TELEPORT` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: bool, a7: bool, a8: bool | `void` |
| `PLAYER.UPDATE_PLAYER_TELEPORT` | a: int | `bool` |
| `PLAYER.STOP_PLAYER_TELEPORT` | — | `void` |
| `PLAYER.IS_PLAYER_TELEPORT_ACTIVE` | — | `bool` |
| `PLAYER.GET_PLAYER_CURRENT_STEALTH_NOISE` | a: int | `float` |
| `PLAYER.SET_PLAYER_HEALTH_RECHARGE_MULTIPLIER` | a: int, b: float | `void` |
| `PLAYER.GET_PLAYER_HEALTH_RECHARGE_MAX_PERCENT` | a: int | `float` |
| `PLAYER.SET_PLAYER_HEALTH_RECHARGE_MAX_PERCENT` | a: int, b: float | `void` |
| `PLAYER.DISABLE_PLAYER_HEALTH_RECHARGE` | a: int | `void` |
| `PLAYER.SET_PLAYER_FALL_DISTANCE_TO_TRIGGER_RAGDOLL_OVERRIDE` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_WEAPON_DAMAGE_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_WEAPON_DEFENSE_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_WEAPON_MINIGUN_DEFENSE_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_MELEE_WEAPON_DAMAGE_MODIFIER` | a1: int, a2: float, a3: bool | `void` |
| `PLAYER.SET_PLAYER_MELEE_WEAPON_DEFENSE_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_VEHICLE_DAMAGE_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_VEHICLE_DEFENSE_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_MAX_EXPLOSIVE_DAMAGE` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_EXPLOSIVE_DAMAGE_MODIFIER` | a: int, b: int | `void` |
| `PLAYER.SET_PLAYER_WEAPON_TAKEDOWN_DEFENSE_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_PARACHUTE_TINT_INDEX` | a: int, b: int | `void` |
| `PLAYER.GET_PLAYER_PARACHUTE_TINT_INDEX` | a: int, b: pointer | `void` |
| `PLAYER.SET_PLAYER_RESERVE_PARACHUTE_TINT_INDEX` | a: int, b: int | `void` |
| `PLAYER.GET_PLAYER_RESERVE_PARACHUTE_TINT_INDEX` | a: int, b: pointer | `void` |
| `PLAYER.SET_PLAYER_PARACHUTE_PACK_TINT_INDEX` | a: int, b: int | `void` |
| `PLAYER.GET_PLAYER_PARACHUTE_PACK_TINT_INDEX` | a: int, b: pointer | `void` |
| `PLAYER.SET_PLAYER_HAS_RESERVE_PARACHUTE` | a: int | `void` |
| `PLAYER.GET_PLAYER_HAS_RESERVE_PARACHUTE` | a: int | `bool` |
| `PLAYER.SET_PLAYER_CAN_LEAVE_PARACHUTE_SMOKE_TRAIL` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_PARACHUTE_SMOKE_TRAIL_COLOR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `PLAYER.GET_PLAYER_PARACHUTE_SMOKE_TRAIL_COLOR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `PLAYER.SET_PLAYER_PHONE_PALETTE_IDX` | a: int, b: int | `void` |
| `PLAYER.SET_PLAYER_NOISE_MULTIPLIER` | a: int, b: float | `void` |
| `PLAYER.SET_PLAYER_SNEAKING_NOISE_MULTIPLIER` | a: int, b: float | `void` |
| `PLAYER.CAN_PED_HEAR_PLAYER` | a: int, b: int | `bool` |
| `PLAYER.SIMULATE_PLAYER_INPUT_GAIT` | a1: int, a2: float, a3: int, a4: float, a5: bool, a6: bool, a7: int | `void` |
| `PLAYER.RESET_PLAYER_INPUT_GAIT` | a: int | `void` |
| `PLAYER.SET_AUTO_GIVE_PARACHUTE_WHEN_ENTER_PLANE` | a: int, b: bool | `void` |
| `PLAYER.SET_AUTO_GIVE_SCUBA_GEAR_WHEN_EXIT_VEHICLE` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_STEALTH_PERCEPTION_MODIFIER` | a: int, b: float | `void` |
| `PLAYER.IS_REMOTE_PLAYER_IN_NON_CLONED_VEHICLE` | a: int | `bool` |
| `PLAYER.INCREASE_PLAYER_JUMP_SUPPRESSION_RANGE` | a: int | `void` |
| `PLAYER.SET_PLAYER_SIMULATE_AIMING` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_CLOTH_PIN_FRAMES` | a: int, b: int | `void` |
| `PLAYER.SET_PLAYER_CLOTH_PACKAGE_INDEX` | a: int | `void` |
| `PLAYER.SET_PLAYER_CLOTH_LOCK_COUNTER` | a: int | `void` |
| `PLAYER.PLAYER_ATTACH_VIRTUAL_BOUND` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float | `void` |
| `PLAYER.PLAYER_DETACH_VIRTUAL_BOUND` | — | `void` |
| `PLAYER.HAS_PLAYER_BEEN_SPOTTED_IN_STOLEN_VEHICLE` | a: int | `bool` |
| `PLAYER.IS_PLAYER_BATTLE_AWARE` | a: int | `bool` |
| `PLAYER.GET_PLAYER_RECEIVED_BATTLE_EVENT_RECENTLY` | a1: int, a2: int, a3: bool | `bool` |
| `PLAYER.EXTEND_WORLD_BOUNDARY_FOR_PLAYER` | a1: float, a2: float, a3: float | `void` |
| `PLAYER.RESET_WORLD_BOUNDARY_FOR_PLAYER` | — | `void` |
| `PLAYER.IS_PLAYER_RIDING_TRAIN` | a: int | `bool` |
| `PLAYER.HAS_PLAYER_LEFT_THE_WORLD` | a: int | `bool` |
| `PLAYER.SET_PLAYER_LEAVE_PED_BEHIND` | a: int, b: bool | `void` |
| `PLAYER.SET_PLAYER_PARACHUTE_VARIATION_OVERRIDE` | a1: int, a2: int, a3: int, a4: int, a5: bool | `void` |
| `PLAYER.CLEAR_PLAYER_PARACHUTE_VARIATION_OVERRIDE` | a: int | `void` |
| `PLAYER._OVERRIDE` | a: int, b: hash | `void` |
| `PLAYER.SET_PLAYER_RESERVE_PARACHUTE_MODEL_OVERRIDE` | a: int, b: hash | `void` |
| `PLAYER.GET_PLAYER_PARACHUTE_MODEL_OVERRIDE` | a: int | `hash` |
| `PLAYER.GET_PLAYER_RESERVE_PARACHUTE_MODEL_OVERRIDE` | a: int | `hash` |
| `PLAYER.CLEAR_PLAYER_PARACHUTE_MODEL_OVERRIDE` | a: int | `void` |
| `PLAYER.CLEAR_PLAYER_RESERVE_PARACHUTE_MODEL_OVERRIDE` | a: int | `void` |
| `PLAYER.SET_PLAYER_PARACHUTE_PACK_MODEL_OVERRIDE` | a: int, b: hash | `void` |
| `PLAYER.CLEAR_PLAYER_PARACHUTE_PACK_MODEL_OVERRIDE` | a: int | `void` |
| `PLAYER.DISABLE_PLAYER_VEHICLE_REWARDS` | a: int | `void` |
| `PLAYER.SET_PLAYER_SPECTATED_VEHICLE_RADIO_OVERRIDE` | a: bool | `void` |
| `PLAYER.SET_PLAYER_BLUETOOTH_STATE` | a: int, b: bool | `void` |
| `PLAYER.IS_PLAYER_BLUETOOTH_ENABLE` | a: int | `bool` |
| `PLAYER.DISABLE_CAMERA_VIEW_MODE_CYCLE` | a: int | `void` |
| `PLAYER.GET_PLAYER_FAKE_WANTED_LEVEL` | a: int | `int` |
| `PLAYER.SET_PLAYER_CAN_DAMAGE_PLAYER` | a1: int, a2: int, a3: bool | `void` |
| `PLAYER.SET_APPLY_WAYPOINT_OF_PLAYER` | a: int, b: int | `void` |
| `PLAYER.IS_PLAYER_VEHICLE_WEAPON_TOGGLED_TO_NON_HOMING` | a: int | `bool` |
| `PLAYER.SET_PLAYER_VEHICLE_WEAPON_TO_NON_HOMING` | a: int | `void` |
| `PLAYER.SET_PLAYER_HOMING_DISABLED_FOR_ALL_VEHICLE_WEAPONS` | a: int, b: int | `void` |
| `PLAYER.ADD_PLAYER_TARGETABLE_ENTITY` | a: int, b: int | `void` |
| `PLAYER.REMOVE_PLAYER_TARGETABLE_ENTITY` | a: int, b: int | `void` |
| `PLAYER.SET_PLAYER_PREVIOUS_VARIATION_DATA` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `PLAYER.REMOVE_SCRIPT_FIRE_POSITION` | — | `void` |
| `PLAYER.SET_SCRIPT_FIRE_POSITION` | a1: float, a2: float, a3: float | `void` |

## RECORDING

| Native | Params | Returns |
| --- | --- | --- |
| `RECORDING.REPLAY_START_EVENT` | a: int | `void` |
| `RECORDING.REPLAY_STOP_EVENT` | — | `void` |
| `RECORDING.REPLAY_CANCEL_EVENT` | — | `void` |
| `RECORDING.REPLAY_RECORD_BACK_FOR_TIME` | a1: float, a2: float, a3: int | `void` |
| `RECORDING.REPLAY_CHECK_FOR_EVENT_THIS_FRAME` | a: string, b: int | `void` |
| `RECORDING.REPLAY_PREVENT_RECORDING_THIS_FRAME` | — | `void` |
| `RECORDING.REPLAY_RESET_EVENT_INFO` | — | `void` |
| `RECORDING.REPLAY_DISABLE_CAMERA_MOVEMENT_THIS_FRAME` | — | `void` |
| `RECORDING.RECORD_GREATEST_MOMENT` | a1: int, a2: int, a3: int | `void` |
| `RECORDING.START_REPLAY_RECORDING` | a: int | `void` |
| `RECORDING.STOP_REPLAY_RECORDING` | — | `void` |
| `RECORDING.CANCEL_REPLAY_RECORDING` | — | `void` |
| `RECORDING.SAVE_REPLAY_RECORDING` | — | `bool` |
| `RECORDING.IS_REPLAY_RECORDING` | — | `bool` |
| `RECORDING.IS_REPLAY_INITIALIZED` | — | `bool` |
| `RECORDING.IS_REPLAY_AVAILABLE` | — | `bool` |
| `RECORDING.IS_REPLAY_RECORD_SPACE_AVAILABLE` | a: bool | `bool` |

## REPLAY

| Native | Params | Returns |
| --- | --- | --- |
| `REPLAY.REGISTER_EFFECT_FOR_REPLAY_EDITOR` | a: string, b: bool | `void` |
| `REPLAY.REPLAY_SYSTEM_HAS_REQUESTED_A_SCRIPT_CLEANUP` | — | `bool` |
| `REPLAY.SET_SCRIPTS_HAVE_CLEANED_UP_FOR_REPLAY_SYSTEM` | — | `void` |
| `REPLAY.SET_REPLAY_SYSTEM_PAUSED_FOR_SAVE` | a: bool | `void` |
| `REPLAY.REPLAY_CONTROL_SHUTDOWN` | — | `void` |
| `REPLAY.ACTIVATE_ROCKSTAR_EDITOR` | a: int | `void` |

## SAVEMIGRATION

| Native | Params | Returns |
| --- | --- | --- |
| `SAVEMIGRATION.SAVEMIGRATION_IS_MP_ENABLED` | — | `bool` |
| `SAVEMIGRATION.SAVEMIGRATION_MP_REQUEST_ACCOUNTS` | — | `bool` |
| `SAVEMIGRATION.SAVEMIGRATION_MP_GET_ACCOUNTS_STATUS` | — | `int` |
| `SAVEMIGRATION.SAVEMIGRATION_MP_NUM_ACCOUNTS` | — | `int` |
| `SAVEMIGRATION.SAVEMIGRATION_MP_GET_ACCOUNT` | a: int, b: pointer | `bool` |
| `SAVEMIGRATION.SAVEMIGRATION_MP_REQUEST_STATUS` | — | `bool` |
| `SAVEMIGRATION.SAVEMIGRATION_MP_GET_STATUS` | — | `int` |
| `SAVEMIGRATION.SAVEMIGRATION_MP_IS_PLATFORM_GENERATION` | a: int | `bool` |

## SCRIPT

| Native | Params | Returns |
| --- | --- | --- |
| `SCRIPT.REQUEST_SCRIPT` | a: string | `void` |
| `SCRIPT.SET_SCRIPT_AS_NO_LONGER_NEEDED` | a: string | `void` |
| `SCRIPT.HAS_SCRIPT_LOADED` | a: string | `bool` |
| `SCRIPT.DOES_SCRIPT_EXIST` | a: string | `bool` |
| `SCRIPT.REQUEST_SCRIPT_WITH_NAME_HASH` | a: hash | `void` |
| `SCRIPT.SET_SCRIPT_WITH_NAME_HASH_AS_NO_LONGER_NEEDED` | a: hash | `void` |
| `SCRIPT.HAS_SCRIPT_WITH_NAME_HASH_LOADED` | a: hash | `bool` |
| `SCRIPT.DOES_SCRIPT_WITH_NAME_HASH_EXIST` | a: hash | `bool` |
| `SCRIPT.TERMINATE_THREAD` | a: int | `void` |
| `SCRIPT.IS_THREAD_ACTIVE` | a: int | `bool` |
| `SCRIPT.GET_NAME_OF_SCRIPT_WITH_THIS_ID` | a: int | `string` |
| `SCRIPT.SCRIPT_THREAD_ITERATOR_RESET` | — | `void` |
| `SCRIPT.SCRIPT_THREAD_ITERATOR_GET_NEXT_THREAD_ID` | — | `int` |
| `SCRIPT.GET_ID_OF_THIS_THREAD` | — | `int` |
| `SCRIPT.TERMINATE_THIS_THREAD` | — | `void` |
| `SCRIPT.GET_NUMBER_OF_THREADS_RUNNING_THE_SCRIPT_WITH_THIS_HASH` | a: hash | `int` |
| `SCRIPT.GET_THIS_SCRIPT_NAME` | — | `string` |
| `SCRIPT.GET_HASH_OF_THIS_SCRIPT_NAME` | — | `hash` |
| `SCRIPT.GET_NUMBER_OF_EVENTS` | a: int | `int` |
| `SCRIPT.GET_EVENT_EXISTS` | a: int, b: int | `bool` |
| `SCRIPT.GET_EVENT_AT_INDEX` | a: int, b: int | `int` |
| `SCRIPT.GET_EVENT_DATA` | a1: int, a2: int, a3: pointer, a4: int | `bool` |
| `SCRIPT.TRIGGER_SCRIPT_EVENT` | a1: int, a2: pointer, a3: int, a4: int | `void` |
| `SCRIPT.SHUTDOWN_LOADING_SCREEN` | — | `void` |
| `SCRIPT.SET_NO_LOADING_SCREEN` | a: bool | `void` |
| `SCRIPT.GET_NO_LOADING_SCREEN` | — | `bool` |
| `SCRIPT.SET_LOADING_SCREEN_BLANK` | — | `bool` |
| `SCRIPT.COMMIT_TO_LOADINGSCREEN_SELCTION` | — | `void` |
| `SCRIPT.BG_IS_EXITFLAG_SET` | — | `bool` |
| `SCRIPT.BG_SET_EXITFLAG_RESPONSE` | — | `void` |
| `SCRIPT.BG_START_CONTEXT_HASH` | a: hash | `void` |
| `SCRIPT.BG_END_CONTEXT_HASH` | a: hash | `void` |
| `SCRIPT.BG_START_CONTEXT` | a: string | `void` |
| `SCRIPT.BG_END_CONTEXT` | a: string | `void` |
| `SCRIPT.BG_DOES_LAUNCH_PARAM_EXIST` | a: int, b: string | `bool` |
| `SCRIPT.BG_GET_LAUNCH_PARAM_VALUE` | a: int, b: string | `int` |
| `SCRIPT.BG_GET_SCRIPT_ID_FROM_NAME_HASH` | a: hash | `int` |
| `SCRIPT._SEND_TU_SCRIPT_EVENT_NEW` | a1: int, a2: pointer, a3: int, a4: int, a5: hash | `void` |

## SECURITY

| Native | Params | Returns |
| --- | --- | --- |
| `SECURITY.REGISTER_SCRIPT_VARIABLE` | — | blocked: Not allowed |
| `SECURITY.UNREGISTER_SCRIPT_VARIABLE` | — | blocked: Not allowed |
| `SECURITY.FORCE_CHECK_SCRIPT_VARIABLES` | — | blocked: Not allowed |

## SHAPETEST

| Native | Params | Returns |
| --- | --- | --- |
| `SHAPETEST.START_SHAPE_TEST_LOS_PROBE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int | `int` |
| `SHAPETEST.START_EXPENSIVE_SYNCHRONOUS_SHAPE_TEST_LOS_PROBE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: int, a9: int | `int` |
| `SHAPETEST.START_SHAPE_TEST_BOUNDING_BOX` | a1: int, a2: int, a3: int | `int` |
| `SHAPETEST.START_SHAPE_TEST_BOX` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: int, a11: int, a12: int, a13: int | `int` |
| `SHAPETEST.START_SHAPE_TEST_BOUND` | a1: int, a2: int, a3: int | `int` |
| `SHAPETEST.START_SHAPE_TEST_CAPSULE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int | `int` |
| `SHAPETEST.START_SHAPE_TEST_SWEPT_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int, a10: int | `int` |
| `SHAPETEST.START_SHAPE_TEST_MOUSE_CURSOR_LOS_PROBE` | a1: vector3, a2: vector3, a3: int, a4: int, a5: int | `int` |
| `SHAPETEST.GET_SHAPE_TEST_RESULT` | a1: int, a2: pointer, a3: vector3, a4: vector3, a5: pointer | `int` |
| `SHAPETEST.GET_SHAPE_TEST_RESULT_INCLUDING_MATERIAL` | a1: int, a2: pointer, a3: vector3, a4: vector3, a5: pointer, a6: pointer | `int` |
| `SHAPETEST.RELEASE_SCRIPT_GUID_FROM_ENTITY` | a: int | `void` |

## SOCIALCLUB

| Native | Params | Returns |
| --- | --- | --- |
| `SOCIALCLUB.SC_INBOX_GET_TOTAL_NUM_MESSAGES` | — | `int` |
| `SOCIALCLUB.SC_INBOX_GET_MESSAGE_TYPE_AT_INDEX` | a: int | `hash` |
| `SOCIALCLUB.SC_INBOX_GET_MESSAGE_IS_READ_AT_INDEX` | a: int | `bool` |
| `SOCIALCLUB.SC_INBOX_SET_MESSAGE_AS_READ_AT_INDEX` | a: int | `bool` |
| `SOCIALCLUB.SC_INBOX_MESSAGE_GET_DATA_INT` | a1: int, a2: string, a3: pointer | `bool` |
| `SOCIALCLUB.SC_INBOX_MESSAGE_GET_DATA_BOOL` | a: int, b: string | `bool` |
| `SOCIALCLUB.SC_INBOX_MESSAGE_GET_DATA_STRING` | a1: int, a2: string, a3: string | `bool` |
| `SOCIALCLUB.SC_INBOX_MESSAGE_DO_APPLY` | a: int | `bool` |
| `SOCIALCLUB.SC_INBOX_MESSAGE_GET_RAW_TYPE_AT_INDEX` | a: int | `string` |
| `SOCIALCLUB.SC_INBOX_MESSAGE_PUSH_GAMER_T0_RECIP_LIST` | a: pointer | `void` |
| `SOCIALCLUB.SC_INBOX_SEND_UGCSTATUPDATE_TO_RECIP_LIST` | a: pointer | `void` |
| `SOCIALCLUB.SC_INBOX_MESSAGE_GET_UGCDATA` | a: int, b: pointer | `bool` |
| `SOCIALCLUB.SC_INBOX_GET_BOUNTY_DATA_AT_INDEX` | a: int, b: pointer | `bool` |
| `SOCIALCLUB.SC_EMAIL_RETRIEVE_EMAILS` | a: int, b: int | `void` |
| `SOCIALCLUB.SC_EMAIL_GET_RETRIEVAL_STATUS` | — | `int` |
| `SOCIALCLUB.SC_EMAIL_GET_NUM_RETRIEVED_EMAILS` | — | `int` |
| `SOCIALCLUB.SC_EMAIL_GET_EMAIL_AT_INDEX` | a: int, b: pointer | `bool` |
| `SOCIALCLUB._SC_EMAIL_MARKETING_EMAIL_OPENED` | a: int, b: hash | `void` |
| `SOCIALCLUB.SC_EMAIL_DELETE_EMAILS` | a: pointer, b: int | `void` |
| `SOCIALCLUB.SC_EMAIL_MESSAGE_PUSH_GAMER_TO_RECIP_LIST` | a: pointer | `void` |
| `SOCIALCLUB.SC_EMAIL_MESSAGE_CLEAR_RECIP_LIST` | — | `void` |
| `SOCIALCLUB.SC_EMAIL_SEND_EMAIL` | a: string | `void` |
| `SOCIALCLUB.SC_EMAIL_SET_CURRENT_EMAIL_TAG` | a: int | `bool` |
| `SOCIALCLUB.SC_CACHE_NEW_ROCKSTAR_MSGS` | a: bool | `void` |
| `SOCIALCLUB.SC_HAS_NEW_ROCKSTAR_MSG` | — | `bool` |
| `SOCIALCLUB.SC_GET_NEW_ROCKSTAR_MSG` | — | `string` |
| `SOCIALCLUB.SC_PRESENCE_ATTR_SET_INT` | a: hash, b: int | `bool` |
| `SOCIALCLUB.SC_PRESENCE_ATTR_SET_FLOAT` | a: hash, b: float | `bool` |
| `SOCIALCLUB.SC_PRESENCE_ATTR_SET_STRING` | a: hash, b: string | `bool` |
| `SOCIALCLUB.SC_PRESENCE_SET_ACTIVITY_RATING` | a: int, b: float | `bool` |
| `SOCIALCLUB.SC_GAMERDATA_GET_INT` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_GAMERDATA_GET_FLOAT` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_GAMERDATA_GET_BOOL` | a: string | `bool` |
| `SOCIALCLUB.SC_GAMERDATA_GET_STRING` | a: string, b: string | `bool` |
| `SOCIALCLUB.SC_GAMERDATA_GET_ACTIVE_XP_BONUS` | a: pointer | `bool` |
| `SOCIALCLUB.SC_PROFANITY_CHECK_STRING` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_PROFANITY_CHECK_STRING_UGC` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_PROFANITY_GET_CHECK_IS_VALID` | a: int | `bool` |
| `SOCIALCLUB.SC_PROFANITY_GET_CHECK_IS_PENDING` | a: int | `bool` |
| `SOCIALCLUB.SC_PROFANITY_GET_STRING_PASSED` | a: int | `bool` |
| `SOCIALCLUB.SC_PROFANITY_GET_STRING_STATUS` | a: int | `int` |
| `SOCIALCLUB.SC_PROFANITY_GET_PROFANE_WORD` | a: int, b: string | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_CHECK_STRING` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_CHECK_IS_VALID` | a: int | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_CHECK_IS_PENDING` | a: int | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_COUNT` | a: int | `int` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_PLATE` | a: int, b: int | `string` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_PLATE_DATA` | a: int, b: int | `string` |
| `SOCIALCLUB.SC_LICENSEPLATE_SET_PLATE_DATA` | a1: string, a2: string, a3: pointer | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_ADD` | a1: string, a2: pointer, a3: pointer | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_ADD_IS_PENDING` | a: int | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_ADD_STATUS` | a: int | `int` |
| `SOCIALCLUB.SC_LICENSEPLATE_ISVALID` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_ISVALID_IS_PENDING` | a: int | `bool` |
| `SOCIALCLUB.SC_LICENSEPLATE_GET_ISVALID_STATUS` | a: int | `int` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_IS_ACTIVE` | — | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EVENT_ID` | — | `int` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_INT` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_FLOAT` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_STRING` | a: string, b: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_DISPLAY_NAME` | a: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_IS_ACTIVE_FOR_TYPE` | a: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EVENT_ID_FOR_TYPE` | a: string | `int` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_INT_FOR_TYPE` | a1: string, a2: pointer, a3: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_FLOAT_FOR_TYPE` | a1: string, a2: pointer, a3: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_STRING_FOR_TYPE` | a1: string, a2: string, a3: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_DISPLAY_NAME_FOR_TYPE` | a: string, b: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_IS_ACTIVE_BY_ID` | a: int | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_INT_BY_ID` | a1: int, a2: string, a3: pointer | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_FLOAT_BY_ID` | a1: int, a2: string, a3: pointer | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_EXTRA_DATA_STRING_BY_ID` | a1: int, a2: string, a3: string | `bool` |
| `SOCIALCLUB.SC_COMMUNITY_EVENT_GET_DISPLAY_NAME_BY_ID` | a: int, b: string | `bool` |
| `SOCIALCLUB.SC_TRANSITION_NEWS_SHOW` | a: int | `bool` |
| `SOCIALCLUB.SC_TRANSITION_NEWS_SHOW_TIMED` | a: int, b: int | `bool` |
| `SOCIALCLUB.SC_TRANSITION_NEWS_SHOW_NEXT_ITEM` | — | `bool` |
| `SOCIALCLUB.SC_TRANSITION_NEWS_HAS_EXTRA_DATA_TU` | — | `bool` |
| `SOCIALCLUB.SC_TRANSITION_NEWS_GET_EXTRA_DATA_INT_TU` | a: string, b: pointer | `bool` |
| `SOCIALCLUB.SC_TRANSITION_NEWS_END` | — | `void` |
| `SOCIALCLUB.SC_PAUSE_NEWS_INIT_STORY_TYPE` | a: int, b: bool | `bool` |
| `SOCIALCLUB.SC_PAUSE_NEWS_GET_PENDING_STORY` | a: int | `bool` |
| `SOCIALCLUB.SC_PAUSE_NEWS_SHUTDOWN` | — | `void` |
| `SOCIALCLUB.SC_ACCOUNT_INFO_GET_NICKNAME` | — | `string` |
| `SOCIALCLUB.SC_ACHIEVEMENT_INFO_STATUS` | a: pointer | `bool` |
| `SOCIALCLUB.SC_HAS_ACHIEVEMENT_BEEN_PASSED` | a: int | `bool` |

## STATS

| Native | Params | Returns |
| --- | --- | --- |
| `STATS.STAT_CLEAR_SLOT_FOR_RELOAD` | a: int | `bool` |
| `STATS.STAT_LOAD` | a: int | `bool` |
| `STATS.STAT_SAVE` | a1: int, a2: bool, a3: int, a4: bool | `bool` |
| `STATS.STAT_SET_OPEN_SAVETYPE_IN_JOB` | a: int | `void` |
| `STATS.STAT_LOAD_PENDING` | a: int | `bool` |
| `STATS.STAT_SAVE_PENDING` | — | `bool` |
| `STATS.STAT_SAVE_PENDING_OR_REQUESTED` | — | `bool` |
| `STATS.STAT_DELETE_SLOT` | a: int | `bool` |
| `STATS.STAT_SLOT_IS_LOADED` | a: int | `bool` |
| `STATS.STAT_CLOUD_SLOT_LOAD_FAILED` | a: int | `bool` |
| `STATS.STAT_CLOUD_SLOT_LOAD_FAILED_CODE` | a: int | `int` |
| `STATS.STAT_SET_BLOCK_SAVES` | a: bool | `void` |
| `STATS.STAT_GET_BLOCK_SAVES` | — | `bool` |
| `STATS.STAT_CLOUD_SLOT_SAVE_FAILED` | a: int | `bool` |
| `STATS.STAT_CLEAR_PENDING_SAVES` | a: int | `void` |
| `STATS.STAT_LOAD_DIRTY_READ_DETECTED` | — | `bool` |
| `STATS.STAT_CLEAR_DIRTY_READ_DETECTED` | — | `void` |
| `STATS.STAT_GET_LOAD_SAFE_TO_PROGRESS_TO_MP_FROM_SP` | — | `bool` |
| `STATS._GET_STAT_HASH_FOR_CHARACTER_STAT` | a1: int, a2: int, a3: int | `hash` |
| `STATS.STAT_SET_INT` | a1: hash, a2: int, a3: bool | `bool` |
| `STATS.STAT_SET_FLOAT` | a1: hash, a2: float, a3: bool | `bool` |
| `STATS.STAT_SET_BOOL` | a1: hash, a2: bool, a3: bool | `bool` |
| `STATS.STAT_SET_GXT_LABEL` | a1: hash, a2: string, a3: bool | `bool` |
| `STATS.STAT_SET_DATE` | a1: hash, a2: pointer, a3: int, a4: bool | `bool` |
| `STATS.STAT_SET_STRING` | a1: hash, a2: string, a3: bool | `bool` |
| `STATS.STAT_SET_POS` | a1: hash, a2: float, a3: float, a4: float, a5: bool | `bool` |
| `STATS.STAT_SET_MASKED_INT` | a1: hash, a2: int, a3: int, a4: int, a5: bool | `bool` |
| `STATS.STAT_SET_USER_ID` | a1: hash, a2: string, a3: bool | `bool` |
| `STATS.STAT_SET_CURRENT_POSIX_TIME` | a: hash, b: bool | `bool` |
| `STATS.STAT_GET_INT` | a1: hash, a2: pointer, a3: int | `bool` |
| `STATS.STAT_GET_FLOAT` | a1: hash, a2: pointer, a3: int | `bool` |
| `STATS.STAT_GET_BOOL` | a1: hash, a2: pointer, a3: int | `bool` |
| `STATS.STAT_GET_DATE` | a1: hash, a2: pointer, a3: int, a4: int | `bool` |
| `STATS.STAT_GET_STRING` | a: hash, b: int | `string` |
| `STATS.STAT_GET_POS` | a1: hash, a2: pointer, a3: pointer, a4: pointer, a5: int | `bool` |
| `STATS.STAT_GET_MASKED_INT` | a1: hash, a2: pointer, a3: int, a4: int, a5: int | `bool` |
| `STATS.STAT_GET_USER_ID` | a: hash | `string` |
| `STATS.STAT_GET_LICENSE_PLATE` | a: hash | `string` |
| `STATS.STAT_SET_LICENSE_PLATE` | a: hash, b: string | `bool` |
| `STATS.STAT_INCREMENT` | a: hash, b: float | `void` |
| `STATS.STAT_COMMUNITY_START_SYNCH` | — | `bool` |
| `STATS.STAT_COMMUNITY_SYNCH_IS_PENDING` | — | `bool` |
| `STATS.STAT_COMMUNITY_GET_HISTORY` | a1: hash, a2: int, a3: pointer | `bool` |
| `STATS.STAT_RESET_ALL_ONLINE_CHARACTER_STATS` | a: int | `void` |
| `STATS.STAT_LOCAL_RESET_ALL_ONLINE_CHARACTER_STATS` | a: int | `void` |
| `STATS.STAT_GET_NUMBER_OF_DAYS` | a: hash | `int` |
| `STATS.STAT_GET_NUMBER_OF_HOURS` | a: hash | `int` |
| `STATS.STAT_GET_NUMBER_OF_MINUTES` | a: hash | `int` |
| `STATS.STAT_GET_NUMBER_OF_SECONDS` | a: hash | `int` |
| `STATS.STAT_SET_PROFILE_SETTING_VALUE` | a: int, b: int | `void` |
| `STATS.STATS_CHARACTER_CREATION_OUTFIT_SELECTED` | a: int | `void` |
| `STATS.STATS_COMPLETED_CHARACTER_CREATION` | a: int | `void` |
| `STATS.PACKED_STAT_GET_INT_STAT_INDEX` | a: int | `int` |
| `STATS.GET_PACKED_INT_STAT_KEY` | a1: int, a2: bool, a3: bool, a4: int | `hash` |
| `STATS.GET_PACKED_TU_INT_STAT_KEY` | a1: int, a2: bool, a3: bool, a4: int | `hash` |
| `STATS.GET_PACKED_NG_INT_STAT_KEY` | a1: int, a2: bool, a3: bool, a4: int, a5: string | `hash` |
| `STATS.GET_PACKED_STAT_BOOL_CODE` | a: int, b: int | `bool` |
| `STATS.GET_PACKED_STAT_INT_CODE` | a: int, b: int | `int` |
| `STATS.SET_PACKED_STAT_BOOL_CODE` | a1: int, a2: bool, a3: int | `void` |
| `STATS.SET_PACKED_STAT_INT_CODE` | a1: int, a2: int, a3: int | `void` |
| `STATS.PLAYSTATS_BACKGROUND_SCRIPT_ACTION` | a: string, b: int | `void` |
| `STATS._PLAYSTATS_FLOW_LOW` | a1: float, a2: float, a3: float, a4: string, a5: bool, a6: int | `void` |
| `STATS._PLAYSTATS_FLOW_MEDIUM` | a1: float, a2: float, a3: float, a4: string, a5: bool, a6: int | `void` |
| `STATS._PLAYSTATS_FLOW_HIGH` | a1: float, a2: float, a3: float, a4: string, a5: bool, a6: int | `void` |
| `STATS.PLAYSTATS_NPC_INVITE` | a: string | `void` |
| `STATS.PLAYSTATS_AWARD_XP` | a1: int, a2: hash, a3: hash | `void` |
| `STATS.PLAYSTATS_RANK_UP` | a: int | `void` |
| `STATS.PLAYSTATS_STARTED_SESSION_IN_OFFLINEMODE` | — | `void` |
| `STATS.PLAYSTATS_ACTIVITY_DONE` | a1: int, a2: int, a3: int | `void` |
| `STATS.PLAYSTATS_LEAVE_JOB_CHAIN` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_MISSION_STARTED` | a1: string, a2: int, a3: int, a4: bool | `void` |
| `STATS.PLAYSTATS_MISSION_OVER` | a1: string, a2: int, a3: int, a4: bool, a5: bool, a6: bool | `void` |
| `STATS.PLAYSTATS_MISSION_CHECKPOINT` | a1: string, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_RANDOM_MISSION_DONE` | a1: string, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_ROS_BET` | a1: int, a2: int, a3: int, a4: float | `void` |
| `STATS.PLAYSTATS_RACE_CHECKPOINT` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_CREATE_MATCH_HISTORY_ID_2` | a: pointer, b: pointer | `bool` |
| `STATS.PLAYSTATS_MATCH_STARTED` | a1: int, a2: int, a3: int | `void` |
| `STATS.PLAYSTATS_SHOP_ITEM` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_CRATE_DROP_MISSION_DONE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int | `void` |
| `STATS.PLAYSTATS_CRATE_CREATED` | a1: float, a2: float, a3: float | `void` |
| `STATS.PLAYSTATS_HOLD_UP_MISSION_DONE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_IMPORT_EXPORT_MISSION_DONE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_RACE_TO_POINT_MISSION_DONE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_ACQUIRED_HIDDEN_PACKAGE` | a: int | `void` |
| `STATS.PLAYSTATS_WEBSITE_VISITED` | a: hash, b: int | `void` |
| `STATS.PLAYSTATS_FRIEND_ACTIVITY` | a: int, b: bool | `void` |
| `STATS.PLAYSTATS_ODDJOB_DONE` | a1: int, a2: int, a3: bool | `void` |
| `STATS.PLAYSTATS_PROP_CHANGE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_CLOTH_CHANGE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_WEAPON_MODE_CHANGE` | a1: hash, a2: hash, a3: hash | `void` |
| `STATS.PLAYSTATS_CHEAT_APPLIED` | a: string | `void` |
| `STATS.PLAYSTATS_JOB_ACTIVITY_END` | a1: pointer, a2: pointer, a3: pointer, a4: pointer | `void` |
| `STATS.PLAYSTATS_JOB_BEND` | a1: pointer, a2: pointer, a3: pointer, a4: pointer | `void` |
| `STATS.PLAYSTATS_JOB_LTS_END` | a1: pointer, a2: pointer, a3: pointer, a4: pointer | `void` |
| `STATS.PLAYSTATS_JOB_LTS_ROUND_END` | a1: pointer, a2: pointer, a3: pointer, a4: pointer | `void` |
| `STATS.PLAYSTATS_QUICKFIX_TOOL` | a: int, b: string | `void` |
| `STATS.PLAYSTATS_IDLE_KICK` | a: int | `void` |
| `STATS.PLAYSTATS_SET_JOIN_TYPE` | a: int | `void` |
| `STATS.PLAYSTATS_HEIST_SAVE_CHEAT` | a: hash, b: int | `void` |
| `STATS.PLAYSTATS_APPEND_DIRECTOR_METRIC` | a: pointer | `void` |
| `STATS.PLAYSTATS_AWARD_BAD_SPORT` | a: int | `void` |
| `STATS.PLAYSTATS_PEGASUS_AS_PERSONAL_AIRCRAFT` | a: hash | `void` |
| `STATS._PLAYSTATS_SHOPMENU_NAV` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_CHALLENGES` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_VEHICLETARGET` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_URBANWARFARE` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_CHECKPOINTCOLLECTION` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_ATOB` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_PENNEDIN` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_PASSTHEPARCEL` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_HOTPROPERTY` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_DEADDROP` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_KINGOFTHECASTLE` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_CRIMINALDAMAGE` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_COMPETITIVEURBANWARFARE` | a: int | `void` |
| `STATS.PLAYSTATS_FM_EVENT_HUNTBEAST` | a: int | `void` |
| `STATS.PLAYSTATS_PIMENU_HIDE_OPTIONS` | a: pointer | `void` |
| `STATS._PLAYSTATS_NAMED_USER_CONTENT` | a1: bool, a2: int, a3: int, a4: int, a5: int, a6: string, a7: int | `void` |
| `STATS.LEADERBOARDS_GET_NUMBER_OF_COLUMNS` | a: int, b: int | `int` |
| `STATS.LEADERBOARDS_GET_COLUMN_ID` | a1: int, a2: int, a3: int | `int` |
| `STATS.LEADERBOARDS_GET_COLUMN_TYPE` | a1: int, a2: int, a3: int | `int` |
| `STATS.LEADERBOARDS_READ_CLEAR_ALL` | — | `int` |
| `STATS.LEADERBOARDS_READ_CLEAR` | a1: int, a2: int, a3: int | `int` |
| `STATS.LEADERBOARDS_READ_PENDING` | a1: int, a2: int, a3: int | `bool` |
| `STATS.LEADERBOARDS_READ_ANY_PENDING` | — | `bool` |
| `STATS.LEADERBOARDS_READ_SUCCESSFUL` | a1: int, a2: int, a3: int | `bool` |
| `STATS.LEADERBOARDS2_READ_FRIENDS_BY_ROW` | a1: pointer, a2: pointer, a3: int, a4: bool, a5: int, a6: int | `bool` |
| `STATS.LEADERBOARDS2_READ_BY_HANDLE` | a: pointer, b: pointer | `bool` |
| `STATS.LEADERBOARDS2_READ_BY_RANK` | a1: pointer, a2: int, a3: int | `bool` |
| `STATS.LEADERBOARDS2_READ_BY_RADIUS` | a1: pointer, a2: int, a3: pointer | `bool` |
| `STATS.LEADERBOARDS2_READ_BY_SCORE_INT` | a1: pointer, a2: int, a3: int | `bool` |
| `STATS.LEADERBOARDS2_READ_BY_SCORE_FLOAT` | a1: pointer, a2: float, a3: int | `bool` |
| `STATS.LEADERBOARDS2_READ_RANK_PREDICTION` | a1: pointer, a2: pointer, a3: pointer | `bool` |
| `STATS.LEADERBOARDS2_READ_BY_PLAFORM` | a1: pointer, a2: string, a3: string | `bool` |
| `STATS.LEADERBOARDS2_READ_GET_ROW_DATA_START` | a: pointer | `bool` |
| `STATS.LEADERBOARDS2_READ_GET_ROW_DATA_END` | — | `void` |
| `STATS.LEADERBOARDS2_READ_GET_ROW_DATA_INFO` | a: int, b: pointer | `bool` |
| `STATS.LEADERBOARDS2_READ_GET_ROW_DATA_INT` | a: int, b: int | `int` |
| `STATS.LEADERBOARDS2_READ_GET_ROW_DATA_FLOAT` | a: int, b: int | `float` |
| `STATS.LEADERBOARDS2_WRITE_DATA` | a: pointer | `bool` |
| `STATS.LEADERBOARDS_WRITE_ADD_COLUMN` | a1: int, a2: int, a3: float | `void` |
| `STATS.LEADERBOARDS_WRITE_ADD_COLUMN_LONG` | a1: int, a2: int, a3: int | `void` |
| `STATS.LEADERBOARDS_CACHE_DATA_ROW` | a: pointer | `bool` |
| `STATS.LEADERBOARDS_CLEAR_CACHE_DATA` | — | `void` |
| `STATS.LEADERBOARDS_CLEAR_CACHE_DATA_ID` | a: int | `void` |
| `STATS.LEADERBOARDS_GET_CACHE_EXISTS` | a: int | `bool` |
| `STATS.LEADERBOARDS_GET_CACHE_TIME` | a: int | `int` |
| `STATS.LEADERBOARDS_GET_CACHE_NUMBER_OF_ROWS` | a: int | `int` |
| `STATS.LEADERBOARDS_GET_CACHE_DATA_ROW` | a1: int, a2: int, a3: pointer | `bool` |
| `STATS.PRESENCE_EVENT_UPDATESTAT_INT` | a1: hash, a2: int, a3: int | `void` |
| `STATS.PRESENCE_EVENT_UPDATESTAT_FLOAT` | a1: hash, a2: float, a3: int | `void` |
| `STATS.PRESENCE_EVENT_UPDATESTAT_INT_WITH_STRING` | a1: hash, a2: int, a3: int, a4: string | `void` |
| `STATS.GET_PLAYER_HAS_DRIVEN_ALL_VEHICLES` | — | `bool` |
| `STATS.SET_HAS_POSTED_ALL_VEHICLES_DRIVEN` | — | `void` |
| `STATS.SET_PROFILE_SETTING_PROLOGUE_COMPLETE` | — | `void` |
| `STATS.SET_PROFILE_SETTING_SP_CHOP_MISSION_COMPLETE` | — | `void` |
| `STATS.SET_PROFILE_SETTING_CREATOR_RACES_DONE` | a: int | `void` |
| `STATS.SET_PROFILE_SETTING_CREATOR_DM_DONE` | a: int | `void` |
| `STATS.SET_PROFILE_SETTING_CREATOR_CTF_DONE` | a: int | `void` |
| `STATS.SET_JOB_ACTIVITY_ID_STARTED` | a: int, b: int | `void` |
| `STATS.SET_FREEMODE_PROLOGUE_DONE` | a: int, b: int | `void` |
| `STATS.SET_FREEMODE_STRAND_PROGRESSION_STATUS` | a: int, b: int | `void` |
| `STATS.STAT_NETWORK_INCREMENT_ON_SUICIDE` | a: int, b: float | `void` |
| `STATS.STAT_SET_CHEAT_IS_ACTIVE` | — | `void` |
| `STATS.LEADERBOARDS2_WRITE_DATA_FOR_EVENT_TYPE` | a: pointer, b: pointer | `bool` |
| `STATS.FORCE_CLOUD_MP_STATS_DOWNLOAD_AND_OVERWRITE_LOCAL_SAVE` | — | `void` |
| `STATS.STAT_MIGRATE_CLEAR_FOR_RESTART` | — | `void` |
| `STATS.STAT_MIGRATE_SAVEGAME_START` | a: string | `bool` |
| `STATS.STAT_MIGRATE_SAVEGAME_GET_STATUS` | — | `int` |
| `STATS.STAT_MIGRATE_CHECK_ALREADY_DONE` | — | `bool` |
| `STATS.STAT_MIGRATE_CHECK_START` | — | `bool` |
| `STATS.STAT_MIGRATE_CHECK_GET_IS_PLATFORM_AVAILABLE` | a: int | `int` |
| `STATS.STAT_MIGRATE_CHECK_GET_PLATFORM_STATUS` | a: int, b: pointer | `int` |
| `STATS.STAT_GET_SAVE_MIGRATION_STATUS` | a: pointer | `int` |
| `STATS.STAT_SAVE_MIGRATION_CANCEL_PENDING_OPERATION` | — | `bool` |
| `STATS.STAT_GET_CANCEL_SAVE_MIGRATION_STATUS` | — | `int` |
| `STATS.STAT_SAVE_MIGRATION_CONSUME_CONTENT` | a1: hash, a2: string, a3: string | `bool` |
| `STATS.STAT_GET_SAVE_MIGRATION_CONSUME_CONTENT_STATUS` | a: pointer | `int` |
| `STATS.STAT_ENABLE_STATS_TRACKING` | — | `void` |
| `STATS.STAT_DISABLE_STATS_TRACKING` | — | `void` |
| `STATS.STAT_IS_STATS_TRACKING_ENABLED` | — | `bool` |
| `STATS.STAT_START_RECORD_STAT` | a: int, b: int | `bool` |
| `STATS.STAT_STOP_RECORD_STAT` | — | `bool` |
| `STATS.STAT_GET_RECORDED_VALUE` | a: pointer | `bool` |
| `STATS.STAT_IS_RECORDING_STAT` | — | `bool` |
| `STATS.STAT_GET_CURRENT_NEAR_MISS_NOCRASH_PRECISE` | — | `int` |
| `STATS.STAT_GET_CURRENT_REAR_WHEEL_DISTANCE` | — | `float` |
| `STATS.STAT_GET_CURRENT_FRONT_WHEEL_DISTANCE` | — | `float` |
| `STATS.STAT_GET_CURRENT_JUMP_DISTANCE` | — | `float` |
| `STATS.STAT_GET_CURRENT_DRIVE_NOCRASH_DISTANCE` | — | `float` |
| `STATS.STAT_GET_CURRENT_SPEED` | — | `float` |
| `STATS.STAT_GET_CURRENT_DRIVING_REVERSE_DISTANCE` | — | `float` |
| `STATS.STAT_GET_CURRENT_SKYDIVING_DISTANCE` | — | `float` |
| `STATS.STAT_GET_CHALLENGE_FLYING_DIST` | — | `float` |
| `STATS.STAT_GET_FLYING_ALTITUDE` | a: pointer | `bool` |
| `STATS.STAT_IS_PLAYER_VEHICLE_ABOVE_OCEAN` | — | `bool` |
| `STATS.STAT_GET_VEHICLE_BAIL_DISTANCE` | — | `float` |
| `STATS.STAT_ROLLBACK_SAVE_MIGRATION` | — | `bool` |
| `STATS.SET_HAS_SPECIALEDITION_CONTENT` | a: int | `void` |
| `STATS.SET_SAVE_MIGRATION_TRANSACTION_ID_WARNING` | a: int | `void` |
| `STATS.GET_BOSS_GOON_UUID` | a1: int, a2: pointer, a3: pointer | `void` |
| `STATS.PLAYSTATS_BW_BOSSONBOSSDEATHMATCH` | a: int | `void` |
| `STATS.PLAYSTATS_BW_YATCHATTACK` | a: int | `void` |
| `STATS.PLAYSTATS_BW_HUNT_THE_BOSS` | a: int | `void` |
| `STATS.PLAYSTATS_BW_SIGHTSEER` | a: int | `void` |
| `STATS.PLAYSTATS_BW_ASSAULT` | a: int | `void` |
| `STATS.PLAYSTATS_BW_BELLY_OF_THE_BEAST` | a: int | `void` |
| `STATS.PLAYSTATS_BW_HEAD_HUNTER` | a: int | `void` |
| `STATS.PLAYSTATS_BW_FRAGILE_GOODS` | a: int | `void` |
| `STATS.PLAYSTATS_BW_AIR_FREIGHT` | a: int | `void` |
| `STATS.PLAYSTATS_BC_CAR_JACKING` | a: int | `void` |
| `STATS.PLAYSTATS_BC_SMASH_AND_GRAB` | a: int | `void` |
| `STATS.PLAYSTATS_BC_PROTECTION_RACKET` | a: int | `void` |
| `STATS.PLAYSTATS_BC_MOST_WANTED` | a: int | `void` |
| `STATS.PLAYSTATS_BC_FINDERS_KEEPERS` | a: int | `void` |
| `STATS.PLAYSTATS_BC_POINT_TO_POINT` | a: int | `void` |
| `STATS.PLAYSTATS_BC_CASHING` | a: int | `void` |
| `STATS.PLAYSTATS_BC_SALVAGE` | a: int | `void` |
| `STATS.PLAYSTATS_SPENT_PI_CUSTOM_LOADOUT` | a: int | `void` |
| `STATS.PLAYSTATS_BUY_CONTRABAND_MISSION` | a: pointer | `void` |
| `STATS.PLAYSTATS_SELL_CONTRABAND_MISSION` | a: pointer | `void` |
| `STATS.PLAYSTATS_DEFEND_CONTRABAND_MISSION` | a: pointer | `void` |
| `STATS.PLAYSTATS_RECOVER_CONTRABAND_MISSION` | a: pointer | `void` |
| `STATS.PLAYSTATS_HIT_CONTRABAND_DESTROY_LIMIT` | a: int | `void` |
| `STATS.START_BEING_BOSS` | — | blocked: Not allowed |
| `STATS.START_BEING_GOON` | — | blocked: Not allowed |
| `STATS.END_BEING_BOSS` | — | blocked: Not allowed |
| `STATS.END_BEING_GOON` | — | blocked: Not allowed |
| `STATS.HIRED_LIMO` | — | blocked: Not allowed |
| `STATS.ORDER_BOSS_VEHICLE` | — | blocked: Not allowed |
| `STATS.CHANGE_UNIFORM` | — | blocked: Not allowed |
| `STATS.SEND_METRIC_GHOSTING_TO_PLAYER` | — | blocked: Not allowed |
| `STATS.SEND_METRIC_VIP_POACH` | — | blocked: Not allowed |
| `STATS.SEND_METRIC_PUNISH_BODYGUARD` | — | blocked: Not allowed |
| `STATS.PLAYSTATS_START_TRACKING_STUNTS` | — | `void` |
| `STATS.PLAYSTATS_STOP_TRACKING_STUNTS` | — | `void` |
| `STATS.PLAYSTATS_MISSION_ENDED` | a: int | `void` |
| `STATS.PLAYSTATS_IMPEXP_MISSION_ENDED` | a: int | `void` |
| `STATS.PLAYSTATS_CHANGE_MC_ROLE` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `STATS.PLAYSTATS_CHANGE_MC_OUTFIT` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_SWITCH_MC_EMBLEM` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_MC_REQUEST_BIKE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_MC_KILLED_RIVAL_MC_MEMBER` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_ABANDONED_MC` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_EARNED_MC_POINTS` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `STATS.PLAYSTATS_MC_FORMATION_ENDS` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `STATS.PLAYSTATS_MC_CLUBHOUSE_ACTIVITY` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int | `void` |
| `STATS.PLAYSTATS_RIVAL_BEHAVIOR` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int | `void` |
| `STATS.PLAYSTATS_COPY_RANK_INTO_NEW_SLOT` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `STATS.PLAYSTATS_DUPE_DETECTED` | a: pointer | `void` |
| `STATS.PLAYSTATS_BAN_ALERT` | a: int | `void` |
| `STATS.PLAYSTATS_GUNRUNNING_MISSION_ENDED` | a: pointer | `void` |
| `STATS.PLAYSTATS_GUNRUNNING_RND` | a: int | `void` |
| `STATS.PLAYSTATS_BUSINESS_BATTLE_ENDED` | a: int | `void` |
| `STATS.PLAYSTATS_WAREHOUSE_MISSION_ENDED` | a: int | `void` |
| `STATS.PLAYSTATS_NIGHTCLUB_MISSION_ENDED` | a: int | `void` |
| `STATS.PLAYSTATS_DJ_USAGE` | a: int, b: int | `void` |
| `STATS.PLAYSTATS_MINIGAME_USAGE` | a1: int, a2: int, a3: int | `void` |
| `STATS.PLAYSTATS_STONE_HATCHET_ENDED` | a: pointer | `void` |
| `STATS.PLAYSTATS_SMUGGLER_MISSION_ENDED` | a: pointer | `void` |
| `STATS.PLAYSTATS_FM_HEIST_PREP_ENDED` | a: pointer | `void` |
| `STATS.PLAYSTATS_INSTANCED_HEIST_ENDED` | a1: pointer, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_DAR_CHECKPOINT` | a: pointer | `void` |
| `STATS.PLAYSTATS_ENTER_SESSION_PACK` | a: pointer | `void` |
| `STATS.PLAYSTATS_DRONE_USAGE` | a1: int, a2: int, a3: int | `void` |
| `STATS.PLAYSTATS_SPIN_WHEEL` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_ARENA_WARS_SPECTATOR` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_ARENA_WARS_ENDED` | a: pointer | `void` |
| `STATS.PLAYSTATS_SWITCH_PASSIVE_MODE` | a1: bool, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_COLLECTIBLE_PICKED_UP` | a1: int, a2: hash, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int, a11: int | `void` |
| `STATS.PLAYSTATS_CASINO_STORY_MISSION_ENDED` | a: int, b: int | `void` |
| `STATS.PLAYSTATS_CASINO_CHIP` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_ROULETTE` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_BLACKJACK` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_THREE_CARD_POKER` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_SLOT_MACHINE` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_INSIDE_TRACK` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_LUCKY_SEVEN` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_ROULETTE_LIGHT` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_BLACKJACK_LIGHT` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_THREE_CARD_POKER_LIGHT` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_SLOT_MACHINE_LIGHT` | a: int | `void` |
| `STATS.PLAYSTATS_CASINO_INSIDE_TRACK_LIGHT` | a: int | `void` |
| `STATS.PLAYSTATS_ARCADE_GAME` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int | `void` |
| `STATS.PLAYSTATS_ARCADE_LOVE_MATCH` | a: int, b: int | `void` |
| `STATS.PLAYSTATS_FREEMODE_CASINO_MISSION_ENDED` | a: pointer | `void` |
| `STATS.PLAYSTATS_HEIST3_DRONE` | a: int | `void` |
| `STATS.PLAYSTATS_HEIST3_HACK` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `STATS.PLAYSTATS_NPC_PHONE` | a: pointer | `void` |
| `STATS.PLAYSTATS_ARCADE_CABINET` | a: int | `void` |
| `STATS.PLAYSTATS_HEIST3_FINALE` | a: int | `void` |
| `STATS.PLAYSTATS_HEIST3_PREP` | a: int | `void` |
| `STATS.PLAYSTATS_MASTER_CONTROL` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_QUIT_MODE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_MISSION_VOTE` | a: int | `void` |
| `STATS.PLAYSTATS_NJVS_VOTE` | a: int | `void` |
| `STATS._PLAYSTATS_DEATH_INFO` | a1: int, a2: int, a3: int, a4: bool, a5: int, a6: int | `void` |
| `STATS.PLAYSTATS_FM_MISSION_END` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_HEIST4_PREP` | a: int | `void` |
| `STATS.PLAYSTATS_HEIST4_FINALE` | a: int | `void` |
| `STATS.PLAYSTATS_HEIST4_HACK` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_SUB_WEAP` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_FAST_TRVL` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int, a10: int, a11: int | `void` |
| `STATS.PLAYSTATS_HUB_ENTRY` | a: int | `void` |
| `STATS.PLAYSTATS_DJ_MISSION_ENDED` | a: int | `void` |
| `STATS.PLAYSTATS_ROBBERY_PREP` | a: int | `void` |
| `STATS.PLAYSTATS_ROBBERY_FINALE` | a: int | `void` |
| `STATS.PLAYSTATS_EXTRA_EVENT` | a: int | `void` |
| `STATS.PLAYSTATS_CARCLUB_POINTS` | a: int | `void` |
| `STATS.PLAYSTATS_CARCLUB_CHALLENGE` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS.PLAYSTATS_CARCLUB_PRIZE` | a: int, b: hash | `void` |
| `STATS.PLAYSTATS_AWARD_NAV` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STATS._PLAYSTATS_INIT_MULTIPLAYER` | a1: int, a2: int, a3: int | `void` |
| `STATS.PLAYSTATS_INST_MISSION_END` | a: int | `void` |
| `STATS.PLAYSTATS_HUB_EXIT` | a: int | `void` |
| `STATS.PLAYSTATS_LP_NAV` | a: pointer | `void` |
| `STATS._PLAYSTATS_LP_SUBTILE` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_VEH_DEL` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `STATS.PLAYSTATS_INVENTORY` | a: int | `void` |
| `STATS._PLAYSTATS_ACID_MISSION_END` | a: int | `void` |
| `STATS._PLAYSTATS_ACID_RND` | a: int | `void` |
| `STATS._PLAYSTATS_IDLE` | a1: int, a2: int, a3: int | `void` |
| `STATS._PLAYSTATS_PLAYER_STYLE` | a: int | `void` |
| `STATS._PLAYSTATS_RANDOM_EVENT` | a: int | `void` |
| `STATS._PLAYSTATS_PH_SNAPSHOT` | a: int | `void` |
| `STATS._PLAYSTATS_PH_PROGRESS_TIERS` | a: int | `void` |
| `STATS._PLAYSTATS_PH_NAV` | a: int | `void` |
| `STATS._PLAYSTATS_PH_ACTIVITY` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int, a7: int, a8: int | `void` |
| `STATS._PLAYSTATS_PH_PROGRESS_OBJECTIVES` | a: int | `void` |
| `STATS._PLAYSTATS_ALERT` | a: pointer | `void` |
| `STATS._PLAYSTATS_ATTRITION_STAGE_END` | a: int | `void` |
| `STATS._PLAYSTATS_SHOWROOM_NAV` | a1: int, a2: int, a3: hash | `void` |
| `STATS._PLAYSTATS_SHOWROOM_OVERVIEW` | a: pointer | `void` |
| `STATS._PLAYSTATS_PIMENU_NAV` | a: pointer | `void` |
| `STATS._PLAYSTATS_RECOVER_VEHICLE` | a: pointer | `void` |
| `STATS._PLAYSTATS_SCRIPT_EVENT_FPOM` | a: pointer | `void` |
| `STATS._PLAYSTATS_LOBBY_EXIT` | a: pointer | `void` |
| `STATS._PLAYSTATS_LOBBY_STARTED` | a: pointer | `void` |
| `STATS._PLAYSTATS_CREATOR_END` | a: pointer | `void` |

## STREAMING

| Native | Params | Returns |
| --- | --- | --- |
| `STREAMING.LOAD_ALL_OBJECTS_NOW` | — | `void` |
| `STREAMING.LOAD_SCENE` | a1: float, a2: float, a3: float | `void` |
| `STREAMING.NETWORK_UPDATE_LOAD_SCENE` | — | `bool` |
| `STREAMING.IS_NETWORK_LOADING_SCENE` | — | `bool` |
| `STREAMING.SET_INTERIOR_ACTIVE` | a: int, b: bool | `void` |
| `STREAMING.REQUEST_MODEL` | a: hash | `void` |
| `STREAMING.REQUEST_MENU_PED_MODEL` | a: hash | `void` |
| `STREAMING.HAS_MODEL_LOADED` | a: hash | `bool` |
| `STREAMING.REQUEST_MODELS_IN_ROOM` | a: int, b: string | `void` |
| `STREAMING.SET_MODEL_AS_NO_LONGER_NEEDED` | a: hash | `void` |
| `STREAMING.IS_MODEL_IN_CDIMAGE` | a: hash | `bool` |
| `STREAMING.IS_MODEL_VALID` | a: hash | `bool` |
| `STREAMING.IS_MODEL_A_PED` | a: hash | `bool` |
| `STREAMING.IS_MODEL_A_VEHICLE` | a: hash | `bool` |
| `STREAMING.REQUEST_COLLISION_AT_COORD` | a1: float, a2: float, a3: float | `void` |
| `STREAMING.REQUEST_COLLISION_FOR_MODEL` | a: hash | `void` |
| `STREAMING.HAS_COLLISION_FOR_MODEL_LOADED` | a: hash | `bool` |
| `STREAMING.REQUEST_ADDITIONAL_COLLISION_AT_COORD` | a1: float, a2: float, a3: float | `void` |
| `STREAMING.DOES_ANIM_DICT_EXIST` | a: string | `bool` |
| `STREAMING.REQUEST_ANIM_DICT` | a: string | `void` |
| `STREAMING.HAS_ANIM_DICT_LOADED` | a: string | `bool` |
| `STREAMING.REMOVE_ANIM_DICT` | a: string | `void` |
| `STREAMING.REQUEST_ANIM_SET` | a: string | `void` |
| `STREAMING.HAS_ANIM_SET_LOADED` | a: string | `bool` |
| `STREAMING.REMOVE_ANIM_SET` | a: string | `void` |
| `STREAMING.REQUEST_CLIP_SET` | a: string | `void` |
| `STREAMING.HAS_CLIP_SET_LOADED` | a: string | `bool` |
| `STREAMING.REMOVE_CLIP_SET` | a: string | `void` |
| `STREAMING.REQUEST_IPL` | a: string | `void` |
| `STREAMING.REMOVE_IPL` | a: string | `void` |
| `STREAMING.IS_IPL_ACTIVE` | a: string | `bool` |
| `STREAMING.SET_STREAMING` | a: bool | `void` |
| `STREAMING.LOAD_GLOBAL_WATER_FILE` | a: int | `void` |
| `STREAMING.GET_GLOBAL_WATER_FILE` | — | `int` |
| `STREAMING.SET_GAME_PAUSES_FOR_STREAMING` | a: bool | `void` |
| `STREAMING.SET_REDUCE_PED_MODEL_BUDGET` | a: bool | `void` |
| `STREAMING.SET_REDUCE_VEHICLE_MODEL_BUDGET` | a: bool | `void` |
| `STREAMING.SET_DITCH_POLICE_MODELS` | a: bool | `void` |
| `STREAMING.GET_NUMBER_OF_STREAMING_REQUESTS` | — | `int` |
| `STREAMING.REQUEST_PTFX_ASSET` | — | `void` |
| `STREAMING.HAS_PTFX_ASSET_LOADED` | — | `bool` |
| `STREAMING.REMOVE_PTFX_ASSET` | — | `void` |
| `STREAMING.REQUEST_NAMED_PTFX_ASSET` | a: string | `void` |
| `STREAMING.HAS_NAMED_PTFX_ASSET_LOADED` | a: string | `bool` |
| `STREAMING.REMOVE_NAMED_PTFX_ASSET` | a: string | `void` |
| `STREAMING.SET_VEHICLE_POPULATION_BUDGET` | a: int | `void` |
| `STREAMING.SET_PED_POPULATION_BUDGET` | a: int | `void` |
| `STREAMING.CLEAR_FOCUS` | — | `void` |
| `STREAMING.SET_FOCUS_POS_AND_VEL` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `void` |
| `STREAMING.SET_FOCUS_ENTITY` | a: int | `void` |
| `STREAMING.IS_ENTITY_FOCUS` | a: int | `bool` |
| `STREAMING.SET_RESTORE_FOCUS_ENTITY` | a: int | `void` |
| `STREAMING.SET_MAPDATACULLBOX_ENABLED` | a: string, b: bool | `void` |
| `STREAMING.SET_ALL_MAPDATA_CULLED` | a: int | `void` |
| `STREAMING.STREAMVOL_CREATE_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int | `int` |
| `STREAMING.STREAMVOL_CREATE_FRUSTUM` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int | `int` |
| `STREAMING.STREAMVOL_CREATE_LINE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `int` |
| `STREAMING.STREAMVOL_DELETE` | a: int | `void` |
| `STREAMING.STREAMVOL_HAS_LOADED` | a: int | `bool` |
| `STREAMING.STREAMVOL_IS_VALID` | a: int | `bool` |
| `STREAMING.IS_STREAMVOL_ACTIVE` | — | `bool` |
| `STREAMING.NEW_LOAD_SCENE_START` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int | `bool` |
| `STREAMING.NEW_LOAD_SCENE_START_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: int | `bool` |
| `STREAMING.NEW_LOAD_SCENE_STOP` | — | `void` |
| `STREAMING.IS_NEW_LOAD_SCENE_ACTIVE` | — | `bool` |
| `STREAMING.IS_NEW_LOAD_SCENE_LOADED` | — | `bool` |
| `STREAMING.IS_SAFE_TO_START_PLAYER_SWITCH` | — | `bool` |
| `STREAMING.START_PLAYER_SWITCH` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STREAMING.STOP_PLAYER_SWITCH` | — | `void` |
| `STREAMING.IS_PLAYER_SWITCH_IN_PROGRESS` | — | `bool` |
| `STREAMING.GET_PLAYER_SWITCH_TYPE` | — | `int` |
| `STREAMING.GET_IDEAL_PLAYER_SWITCH_TYPE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `int` |
| `STREAMING.GET_PLAYER_SWITCH_STATE` | — | `int` |
| `STREAMING.GET_PLAYER_SHORT_SWITCH_STATE` | — | `int` |
| `STREAMING.SET_PLAYER_SHORT_SWITCH_STYLE` | a: int | `void` |
| `STREAMING.GET_PLAYER_SWITCH_JUMP_CUT_INDEX` | — | `int` |
| `STREAMING.SET_PLAYER_SWITCH_OUTRO` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int | `void` |
| `STREAMING.SET_PLAYER_SWITCH_ESTABLISHING_SHOT` | a: string | `void` |
| `STREAMING.ALLOW_PLAYER_SWITCH_PAN` | — | `void` |
| `STREAMING.ALLOW_PLAYER_SWITCH_OUTRO` | — | `void` |
| `STREAMING.ALLOW_PLAYER_SWITCH_ASCENT` | — | `void` |
| `STREAMING.ALLOW_PLAYER_SWITCH_DESCENT` | — | `void` |
| `STREAMING.IS_SWITCH_READY_FOR_DESCENT` | — | `bool` |
| `STREAMING.ENABLE_SWITCH_PAUSE_BEFORE_DESCENT` | — | `void` |
| `STREAMING.DISABLE_SWITCH_OUTRO_FX` | — | `void` |
| `STREAMING.SWITCH_TO_MULTI_FIRSTPART` | a1: int, a2: int, a3: int | `void` |
| `STREAMING.SWITCH_TO_MULTI_SECONDPART` | a: int | `void` |
| `STREAMING.IS_SWITCH_TO_MULTI_FIRSTPART_FINISHED` | — | `bool` |
| `STREAMING.GET_PLAYER_SWITCH_INTERP_OUT_DURATION` | — | `int` |
| `STREAMING.GET_PLAYER_SWITCH_INTERP_OUT_CURRENT_TIME` | — | `int` |
| `STREAMING.IS_SWITCH_SKIPPING_DESCENT` | — | `bool` |
| `STREAMING.SET_SCENE_STREAMING_TRACKS_CAM_POS_THIS_FRAME` | — | `void` |
| `STREAMING.GET_LODSCALE` | — | `float` |
| `STREAMING.OVERRIDE_LODSCALE_THIS_FRAME` | a: float | `void` |
| `STREAMING.REMAP_LODSCALE_RANGE_THIS_FRAME` | a1: float, a2: float, a3: float, a4: float | `void` |
| `STREAMING.SUPPRESS_HD_MAP_STREAMING_THIS_FRAME` | — | `void` |
| `STREAMING.SET_RENDER_HD_ONLY` | a: bool | `void` |
| `STREAMING.FORCE_ALLOW_TIME_BASED_FADING_THIS_FRAME` | — | `void` |
| `STREAMING.IPL_GROUP_SWAP_START` | a: string, b: string | `void` |
| `STREAMING.IPL_GROUP_SWAP_CANCEL` | — | `void` |
| `STREAMING.IPL_GROUP_SWAP_IS_READY` | — | `bool` |
| `STREAMING.IPL_GROUP_SWAP_FINISH` | — | `void` |
| `STREAMING.IPL_GROUP_SWAP_IS_ACTIVE` | — | `bool` |
| `STREAMING.PREFETCH_SRL` | a: string | `void` |
| `STREAMING.IS_SRL_LOADED` | — | `bool` |
| `STREAMING.BEGIN_SRL` | — | `void` |
| `STREAMING.END_SRL` | — | `void` |
| `STREAMING.SET_SRL_TIME` | a: float | `void` |
| `STREAMING.SET_SRL_POST_CUTSCENE_CAMERA` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `STREAMING.SET_SRL_READAHEAD_TIMES` | a1: int, a2: int, a3: int, a4: int | `void` |
| `STREAMING.SET_SRL_LONG_JUMP_MODE` | a: bool | `void` |
| `STREAMING.SET_SRL_FORCE_PRESTREAM` | a: int | `void` |
| `STREAMING.SET_HD_AREA` | a1: float, a2: float, a3: float, a4: float | `void` |
| `STREAMING.CLEAR_HD_AREA` | — | `void` |
| `STREAMING.INIT_CREATOR_BUDGET` | — | `void` |
| `STREAMING.SHUTDOWN_CREATOR_BUDGET` | — | `void` |
| `STREAMING.ADD_MODEL_TO_CREATOR_BUDGET` | a: hash | `bool` |
| `STREAMING.REMOVE_MODEL_FROM_CREATOR_BUDGET` | a: hash | `void` |
| `STREAMING.GET_USED_CREATOR_BUDGET` | — | `float` |
| `STREAMING._GET_MODEL_ADDITIONAL_COST` | a: hash | `float` |
| `STREAMING._GET_TOTAL_MODEL_COST` | a: hash | `float` |
| `STREAMING.SET_ISLAND_ENABLED` | a: string, b: bool | `void` |
| `STREAMING._SET_SPHERICAL_STREAM_DISTANT_HILODS_THIS_FRAME` | — | `void` |
| `STREAMING.IS_GAME_INSTALLED` | — | `bool` |

## TASK

| Native | Params | Returns |
| --- | --- | --- |
| `TASK.TASK_PAUSE` | a: int, b: int | `void` |
| `TASK.TASK_STAND_STILL` | a: int, b: int | `void` |
| `TASK.TASK_JUMP` | a1: int, a2: bool, a3: bool, a4: bool | `void` |
| `TASK.TASK_COWER` | a: int, b: int | `void` |
| `TASK.TASK_HANDS_UP` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `TASK.UPDATE_TASK_HANDS_UP_DURATION` | a: int, b: int | `void` |
| `TASK.TASK_OPEN_VEHICLE_DOOR` | a1: int, a2: int, a3: int, a4: int, a5: float | `void` |
| `TASK.TASK_ENTER_VEHICLE` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: int, a7: string | `void` |
| `TASK.TASK_LEAVE_VEHICLE` | a1: int, a2: int, a3: int | `void` |
| `TASK.TASK_GET_OFF_BOAT` | a: int, b: int | `void` |
| `TASK.TASK_SKY_DIVE` | a: int, b: bool | `void` |
| `TASK.TASK_PARACHUTE` | a1: int, a2: bool, a3: bool | `void` |
| `TASK.TASK_PARACHUTE_TO_TARGET` | a1: int, a2: float, a3: float, a4: float | `void` |
| `TASK.SET_PARACHUTE_TASK_TARGET` | a1: int, a2: float, a3: float, a4: float | `void` |
| `TASK.SET_PARACHUTE_TASK_THRUST` | a: int, b: float | `void` |
| `TASK.TASK_RAPPEL_FROM_HELI` | a: int, b: float | `void` |
| `TASK.TASK_VEHICLE_DRIVE_TO_COORD` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: int, a8: hash, a9: int, a10: float, a11: float | `void` |
| `TASK.TASK_VEHICLE_DRIVE_TO_COORD_LONGRANGE` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: int, a8: float | `void` |
| `TASK.TASK_VEHICLE_DRIVE_WANDER` | a1: int, a2: int, a3: float, a4: int | `void` |
| `TASK.TASK_FOLLOW_TO_OFFSET_OF_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: int, a8: float, a9: bool | `void` |
| `TASK.TASK_GO_STRAIGHT_TO_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: float, a8: float | `void` |
| `TASK.TASK_GO_STRAIGHT_TO_COORD_RELATIVE_TO_ENTITY` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: int | `void` |
| `TASK.TASK_ACHIEVE_HEADING` | a1: int, a2: float, a3: int | `void` |
| `TASK.TASK_FLUSH_ROUTE` | — | `void` |
| `TASK.TASK_EXTEND_ROUTE` | a1: float, a2: float, a3: float | `void` |
| `TASK.TASK_FOLLOW_POINT_ROUTE` | a1: int, a2: float, a3: int | `void` |
| `TASK.TASK_GO_TO_ENTITY` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: int | `void` |
| `TASK.TASK_SMART_FLEE_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: bool, a8: bool | `void` |
| `TASK.TASK_SMART_FLEE_PED` | a1: int, a2: int, a3: float, a4: int, a5: bool, a6: bool | `void` |
| `TASK.TASK_REACT_AND_FLEE_PED` | a: int, b: int | `void` |
| `TASK.TASK_SHOCKING_EVENT_REACT` | a: int, b: int | `void` |
| `TASK.TASK_WANDER_IN_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `TASK.TASK_WANDER_STANDARD` | a1: int, a2: float, a3: int | `void` |
| `TASK.TASK_WANDER_SPECIFIC` | a1: int, a2: string, a3: string, a4: float | `void` |
| `TASK.TASK_VEHICLE_PARK` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: int, a8: float, a9: bool | `void` |
| `TASK.TASK_STEALTH_KILL` | a1: int, a2: int, a3: hash, a4: float, a5: int | `void` |
| `TASK.TASK_PLANT_BOMB` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `TASK.TASK_SHARK_CIRCLE_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float | `void` |
| `TASK.TASK_FOLLOW_NAV_MESH_TO_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: float, a8: int, a9: float | `void` |
| `TASK.TASK_FOLLOW_NAV_MESH_TO_COORD_ADVANCED` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: float, a8: int, a9: float, a10: float, a11: float, a12: float | `void` |
| `TASK.SET_PED_PATH_CAN_USE_CLIMBOVERS` | a: int, b: bool | `void` |
| `TASK.SET_PED_PATH_CAN_USE_LADDERS` | a: int, b: bool | `void` |
| `TASK.SET_PED_PATH_CAN_DROP_FROM_HEIGHT` | a: int, b: bool | `void` |
| `TASK.SET_PED_PATH_CLIMB_COST_MODIFIER` | a: int, b: float | `void` |
| `TASK.SET_PED_PATH_MAY_ENTER_WATER` | a: int, b: bool | `void` |
| `TASK.SET_PED_PATH_PREFER_TO_AVOID_WATER` | a: int, b: bool | `void` |
| `TASK.SET_PED_PATH_AVOID_FIRE` | a: int, b: bool | `void` |
| `TASK.SET_GLOBAL_MIN_BIRD_FLIGHT_HEIGHT` | a: float | `void` |
| `TASK.GET_NAVMESH_ROUTE_DISTANCE_REMAINING` | a1: int, a2: pointer, a3: pointer | `int` |
| `TASK.GET_NAVMESH_ROUTE_RESULT` | a: int | `int` |
| `TASK.IS_CONTROLLED_VEHICLE_UNABLE_TO_GET_TO_ROAD` | a: int | `bool` |
| `TASK.TASK_GO_TO_COORD_ANY_MEANS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: bool, a8: int, a9: float | `void` |
| `TASK.TASK_GO_TO_COORD_ANY_MEANS_EXTRA_PARAMS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: bool, a8: int, a9: float, a10: float, a11: float, a12: int, a13: float | `void` |
| `TASK.TASK_GO_TO_COORD_ANY_MEANS_EXTRA_PARAMS_WITH_CRUISE_SPEED` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int, a7: bool, a8: int, a9: float, a10: float, a11: float, a12: int, a13: float, a14: float | `void` |
| `TASK.TASK_PLAY_ANIM` | a1: int, a2: string, a3: string, a4: float, a5: float, a6: int, a7: int, a8: float, a9: bool, a10: bool, a11: bool | `void` |
| `TASK.TASK_PLAY_ANIM_ADVANCED` | a1: int, a2: string, a3: string, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: int, a13: int, a14: float, a15: int, a16: int | `void` |
| `TASK.STOP_ANIM_TASK` | a1: int, a2: string, a3: string, a4: float | `void` |
| `TASK.TASK_SCRIPTED_ANIMATION` | a1: int, a2: pointer, a3: pointer, a4: pointer, a5: float, a6: float | `void` |
| `TASK.PLAY_ENTITY_SCRIPTED_ANIM` | a1: int, a2: pointer, a3: pointer, a4: pointer, a5: float, a6: float | `void` |
| `TASK.STOP_ANIM_PLAYBACK` | a1: int, a2: int, a3: bool | `void` |
| `TASK.SET_ANIM_WEIGHT` | a1: int, a2: float, a3: int, a4: int, a5: bool | `void` |
| `TASK.SET_ANIM_PHASE` | a1: int, a2: float, a3: int, a4: bool | `void` |
| `TASK.SET_ANIM_RATE` | a1: int, a2: float, a3: int, a4: bool | `void` |
| `TASK.SET_ANIM_LOOPED` | a1: int, a2: bool, a3: int, a4: bool | `void` |
| `TASK.TASK_PLAY_PHONE_GESTURE_ANIMATION` | a1: int, a2: string, a3: string, a4: string, a5: float, a6: float, a7: bool, a8: bool | `void` |
| `TASK.TASK_STOP_PHONE_GESTURE_ANIMATION` | a: int, b: float | `void` |
| `TASK.IS_PLAYING_PHONE_GESTURE_ANIM` | a: int | `bool` |
| `TASK.GET_PHONE_GESTURE_ANIM_CURRENT_TIME` | a: int | `float` |
| `TASK.GET_PHONE_GESTURE_ANIM_TOTAL_TIME` | a: int | `float` |
| `TASK.TASK_VEHICLE_PLAY_ANIM` | a1: int, a2: string, a3: string | `void` |
| `TASK.TASK_LOOK_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int | `void` |
| `TASK.TASK_LOOK_AT_ENTITY` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `TASK.TASK_CLEAR_LOOK_AT` | a: int | `void` |
| `TASK.OPEN_SEQUENCE_TASK` | a: pointer | `void` |
| `TASK.CLOSE_SEQUENCE_TASK` | a: int | `void` |
| `TASK.TASK_PERFORM_SEQUENCE` | a: int, b: int | `void` |
| `TASK.TASK_PERFORM_SEQUENCE_LOCALLY` | a: int, b: int | `void` |
| `TASK.CLEAR_SEQUENCE_TASK` | a: pointer | `void` |
| `TASK.SET_SEQUENCE_TO_REPEAT` | a: int, b: bool | `void` |
| `TASK.SET_SEQUENCE_PREVENT_MIGRATION` | a: int | `void` |
| `TASK.GET_SEQUENCE_PROGRESS` | a: int | `int` |
| `TASK.GET_IS_TASK_ACTIVE` | a: int, b: int | `bool` |
| `TASK.GET_SCRIPT_TASK_STATUS` | a: int, b: hash | `int` |
| `TASK.GET_ACTIVE_VEHICLE_MISSION_TYPE` | a: int | `int` |
| `TASK.TASK_LEAVE_ANY_VEHICLE` | a1: int, a2: int, a3: int | `void` |
| `TASK.TASK_AIM_GUN_SCRIPTED` | a1: int, a2: hash, a3: bool, a4: bool | `void` |
| `TASK.TASK_AIM_GUN_SCRIPTED_WITH_TARGET` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: int, a7: bool, a8: bool | `void` |
| `TASK.UPDATE_TASK_AIM_GUN_SCRIPTED_TARGET` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool | `void` |
| `TASK.GET_CLIP_SET_FOR_SCRIPTED_GUN_TASK` | a: int | `string` |
| `TASK.TASK_AIM_GUN_AT_ENTITY` | a1: int, a2: int, a3: int, a4: bool | `void` |
| `TASK.TASK_TURN_PED_TO_FACE_ENTITY` | a1: int, a2: int, a3: int | `void` |
| `TASK.TASK_AIM_GUN_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: bool, a7: bool | `void` |
| `TASK.TASK_SHOOT_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: hash | `void` |
| `TASK.TASK_SHUFFLE_TO_NEXT_VEHICLE_SEAT` | a1: int, a2: int, a3: bool | `void` |
| `TASK.CLEAR_PED_TASKS` | a: int | `void` |
| `TASK.CLEAR_PED_SCRIPT_TASK_IF_RUNNING_THREAT_RESPONSE_NON_TEMP_TASK` | a: int | `void` |
| `TASK.CLEAR_PED_SECONDARY_TASK` | a: int | `void` |
| `TASK.TASK_EVERYONE_LEAVE_VEHICLE` | a: int | `void` |
| `TASK.TASK_GOTO_ENTITY_OFFSET` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: int | `void` |
| `TASK.TASK_GOTO_ENTITY_OFFSET_XY` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: float, a8: int | `void` |
| `TASK.TASK_TURN_PED_TO_FACE_COORD` | a1: int, a2: float, a3: float, a4: float, a5: int | `void` |
| `TASK.TASK_VEHICLE_TEMP_ACTION` | a1: int, a2: int, a3: int, a4: int | `void` |
| `TASK.TASK_VEHICLE_MISSION` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: int, a7: float, a8: float, a9: bool | `void` |
| `TASK.TASK_VEHICLE_MISSION_PED_TARGET` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: int, a7: float, a8: float, a9: bool | `void` |
| `TASK.TASK_VEHICLE_MISSION_COORS_TARGET` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: int, a7: float, a8: int, a9: float, a10: float, a11: bool | `void` |
| `TASK.TASK_VEHICLE_ESCORT` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: int, a7: float, a8: int, a9: float | `void` |
| `TASK.TASK_VEHICLE_FOLLOW` | a1: int, a2: int, a3: int, a4: float, a5: int, a6: int | `void` |
| `TASK.TASK_VEHICLE_CHASE` | a: int, b: int | `void` |
| `TASK.TASK_VEHICLE_HELI_PROTECT` | a1: int, a2: int, a3: int, a4: float, a5: int, a6: float, a7: int, a8: int | `void` |
| `TASK.SET_TASK_VEHICLE_CHASE_BEHAVIOR_FLAG` | a1: int, a2: int, a3: bool | `void` |
| `TASK.SET_TASK_VEHICLE_CHASE_IDEAL_PURSUIT_DISTANCE` | a: int, b: float | `void` |
| `TASK.TASK_HELI_CHASE` | a1: int, a2: int, a3: float, a4: float, a5: float | `void` |
| `TASK.TASK_PLANE_CHASE` | a1: int, a2: int, a3: float, a4: float, a5: float | `void` |
| `TASK.TASK_PLANE_LAND` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float | `void` |
| `TASK.CLEAR_DEFAULT_PRIMARY_TASK` | a: int | `void` |
| `TASK.CLEAR_PRIMARY_VEHICLE_TASK` | a: int | `void` |
| `TASK.CLEAR_VEHICLE_CRASH_TASK` | a: int | `void` |
| `TASK.TASK_PLANE_GOTO_PRECISE_VTOL` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: int, a7: int, a8: bool, a9: float, a10: bool | `void` |
| `TASK.TASK_SUBMARINE_GOTO_AND_STOP` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: bool | `void` |
| `TASK.TASK_HELI_MISSION` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: float, a7: float, a8: int, a9: float, a10: float, a11: float, a12: int, a13: int, a14: float, a15: int | `void` |
| `TASK.TASK_HELI_ESCORT_HELI` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float | `void` |
| `TASK.TASK_PLANE_MISSION` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: float, a7: float, a8: int, a9: float, a10: float, a11: float, a12: float, a13: float, a14: bool | `void` |
| `TASK.TASK_PLANE_TAXI` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `TASK.TASK_BOAT_MISSION` | a1: int, a2: int, a3: int, a4: int, a5: float, a6: float, a7: float, a8: int, a9: float, a10: int, a11: float, a12: int | `void` |
| `TASK.TASK_DRIVE_BY` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: float, a8: int, a9: bool, a10: hash | `void` |
| `TASK.SET_DRIVEBY_TASK_TARGET` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float | `void` |
| `TASK.CLEAR_DRIVEBY_TASK_UNDERNEATH_DRIVING_TASK` | a: int | `void` |
| `TASK.IS_DRIVEBY_TASK_UNDERNEATH_DRIVING_TASK` | a: int | `bool` |
| `TASK.CONTROL_MOUNTED_WEAPON` | a: int | `bool` |
| `TASK.SET_MOUNTED_WEAPON_TARGET` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: int, a8: bool | `void` |
| `TASK.IS_MOUNTED_WEAPON_TASK_UNDERNEATH_DRIVING_TASK` | a: int | `bool` |
| `TASK.TASK_USE_MOBILE_PHONE` | a1: int, a2: bool, a3: int | `void` |
| `TASK.TASK_USE_MOBILE_PHONE_TIMED` | a: int, b: int | `void` |
| `TASK.TASK_CHAT_TO_PED` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float, a7: float, a8: float | `void` |
| `TASK.TASK_WARP_PED_INTO_VEHICLE` | a1: int, a2: int, a3: int | `void` |
| `TASK.TASK_SHOOT_AT_ENTITY` | a1: int, a2: int, a3: int, a4: hash | `void` |
| `TASK.TASK_CLIMB` | a: int, b: bool | `void` |
| `TASK.TASK_CLIMB_LADDER` | a: int, b: bool | `void` |
| `TASK.TASK_RAPPEL_DOWN_WALL_USING_CLIPSET_OVERRIDE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: string, a11: int, a12: int | `void` |
| `TASK.GET_TASK_RAPPEL_DOWN_WALL_STATE` | a: int | `int` |
| `TASK.CLEAR_PED_TASKS_IMMEDIATELY` | a: int | `void` |
| `TASK.TASK_PERFORM_SEQUENCE_FROM_PROGRESS` | a1: int, a2: int, a3: int, a4: int | `void` |
| `TASK.SET_NEXT_DESIRED_MOVE_STATE` | a: float | `void` |
| `TASK.SET_PED_DESIRED_MOVE_BLEND_RATIO` | a: int, b: float | `void` |
| `TASK.GET_PED_DESIRED_MOVE_BLEND_RATIO` | a: int | `float` |
| `TASK.TASK_GOTO_ENTITY_AIMING` | a1: int, a2: int, a3: float, a4: float | `void` |
| `TASK.TASK_SET_DECISION_MAKER` | a: int, b: hash | `void` |
| `TASK.TASK_SET_SPHERE_DEFENSIVE_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `TASK.TASK_CLEAR_DEFENSIVE_AREA` | a: int | `void` |
| `TASK.TASK_PED_SLIDE_TO_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float | `void` |
| `TASK.TASK_PED_SLIDE_TO_COORD_HDG_RATE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `void` |
| `TASK.ADD_COVER_POINT` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int, a8: bool | `int` |
| `TASK.REMOVE_COVER_POINT` | a: int | `void` |
| `TASK.DOES_SCRIPTED_COVER_POINT_EXIST_AT_COORDS` | a1: float, a2: float, a3: float | `bool` |
| `TASK.GET_SCRIPTED_COVER_POINT_COORDS` | a: int | `vector3` |
| `TASK.ADD_SCRIPTED_COVER_AREA` | a1: float, a2: float, a3: float, a4: float | `void` |
| `TASK.TASK_COMBAT_PED` | a1: int, a2: int, a3: int, a4: int | `void` |
| `TASK.TASK_COMBAT_PED_TIMED` | a1: int, a2: int, a3: int, a4: int | `void` |
| `TASK.TASK_SEEK_COVER_FROM_POS` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: bool | `void` |
| `TASK.TASK_SEEK_COVER_FROM_PED` | a1: int, a2: int, a3: int, a4: bool | `void` |
| `TASK.TASK_SEEK_COVER_TO_COVER_POINT` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: int, a7: bool | `void` |
| `TASK.TASK_SEEK_COVER_TO_COORDS` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: bool | `void` |
| `TASK.TASK_PUT_PED_DIRECTLY_INTO_COVER` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: bool, a7: float, a8: bool, a9: bool, a10: int, a11: bool | `void` |
| `TASK.TASK_WARP_PED_DIRECTLY_INTO_COVER` | a1: int, a2: int, a3: bool, a4: bool, a5: bool, a6: int | `void` |
| `TASK.TASK_EXIT_COVER` | a1: int, a2: int, a3: float, a4: float, a5: float | `void` |
| `TASK.TASK_PUT_PED_DIRECTLY_INTO_MELEE` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: int | `void` |
| `TASK.TASK_TOGGLE_DUCK` | a: int, b: int | `void` |
| `TASK.TASK_GUARD_CURRENT_POSITION` | a1: int, a2: float, a3: float, a4: bool | `void` |
| `TASK.TASK_GUARD_ASSIGNED_DEFENSIVE_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `void` |
| `TASK.TASK_GUARD_SPHERE_DEFENSIVE_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: float, a9: float, a10: float, a11: float | `void` |
| `TASK.TASK_STAND_GUARD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: string | `void` |
| `TASK.SET_DRIVE_TASK_CRUISE_SPEED` | a: int, b: float | `void` |
| `TASK.SET_DRIVE_TASK_MAX_CRUISE_SPEED` | a1: int, a2: float, a3: bool | `void` |
| `TASK.SET_DRIVE_TASK_DRIVING_STYLE` | a: int, b: int | `void` |
| `TASK.ADD_COVER_BLOCKING_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool, a9: bool, a10: bool | `void` |
| `TASK.REMOVE_ALL_COVER_BLOCKING_AREAS` | — | `void` |
| `TASK.REMOVE_COVER_BLOCKING_AREAS_AT_POSITION` | a1: float, a2: float, a3: float | `void` |
| `TASK.REMOVE_SPECIFIC_COVER_BLOCKING_AREAS` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool, a9: bool, a10: bool | `void` |
| `TASK.TASK_START_SCENARIO_IN_PLACE` | a1: int, a2: string, a3: int, a4: bool | `void` |
| `TASK.TASK_START_SCENARIO_AT_POSITION` | a1: int, a2: string, a3: float, a4: float, a5: float, a6: float, a7: int, a8: bool, a9: bool | `void` |
| `TASK.TASK_USE_NEAREST_SCENARIO_TO_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int | `void` |
| `TASK.TASK_USE_NEAREST_SCENARIO_TO_COORD_WARP` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int | `void` |
| `TASK.TASK_USE_NEAREST_SCENARIO_CHAIN_TO_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int | `void` |
| `TASK.TASK_USE_NEAREST_SCENARIO_CHAIN_TO_COORD_WARP` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int | `void` |
| `TASK.DOES_SCENARIO_EXIST_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: bool | `bool` |
| `TASK.DOES_SCENARIO_OF_TYPE_EXIST_IN_AREA` | a1: float, a2: float, a3: float, a4: string, a5: float, a6: bool | `bool` |
| `TASK.IS_SCENARIO_OCCUPIED` | a1: float, a2: float, a3: float, a4: float, a5: bool | `bool` |
| `TASK.PED_HAS_USE_SCENARIO_TASK` | a: int | `bool` |
| `TASK.PLAY_ANIM_ON_RUNNING_SCENARIO` | a1: int, a2: string, a3: string | `void` |
| `TASK.DOES_SCENARIO_GROUP_EXIST` | a: string | `bool` |
| `TASK.IS_SCENARIO_GROUP_ENABLED` | a: string | `bool` |
| `TASK.SET_SCENARIO_GROUP_ENABLED` | a: string, b: bool | `void` |
| `TASK.RESET_SCENARIO_GROUPS_ENABLED` | — | `void` |
| `TASK.SET_EXCLUSIVE_SCENARIO_GROUP` | a: string | `void` |
| `TASK.RESET_EXCLUSIVE_SCENARIO_GROUP` | — | `void` |
| `TASK.IS_SCENARIO_TYPE_ENABLED` | a: string | `bool` |
| `TASK.SET_SCENARIO_TYPE_ENABLED` | a: string, b: bool | `void` |
| `TASK.RESET_SCENARIO_TYPES_ENABLED` | — | `void` |
| `TASK.IS_PED_ACTIVE_IN_SCENARIO` | a: int | `bool` |
| `TASK.IS_PED_PLAYING_BASE_CLIP_IN_SCENARIO` | a: int | `bool` |
| `TASK.SET_PED_CAN_PLAY_AMBIENT_IDLES` | a1: int, a2: bool, a3: bool | `void` |
| `TASK.TASK_COMBAT_HATED_TARGETS_IN_AREA` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: int | `void` |
| `TASK.TASK_COMBAT_HATED_TARGETS_AROUND_PED` | a1: int, a2: float, a3: int | `void` |
| `TASK.TASK_COMBAT_HATED_TARGETS_AROUND_PED_TIMED` | a1: int, a2: float, a3: int, a4: int | `void` |
| `TASK.TASK_THROW_PROJECTILE` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: bool | `void` |
| `TASK.TASK_SWAP_WEAPON` | a: int, b: bool | `void` |
| `TASK.TASK_RELOAD_WEAPON` | a: int, b: bool | `void` |
| `TASK.IS_PED_GETTING_UP` | a: int | `bool` |
| `TASK.TASK_WRITHE` | a1: int, a2: int, a3: int, a4: int, a5: bool, a6: int | `void` |
| `TASK.IS_PED_IN_WRITHE` | a: int | `bool` |
| `TASK.OPEN_PATROL_ROUTE` | a: string | `void` |
| `TASK.CLOSE_PATROL_ROUTE` | — | `void` |
| `TASK.ADD_PATROL_ROUTE_NODE` | a1: int, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int | `void` |
| `TASK.ADD_PATROL_ROUTE_LINK` | a: int, b: int | `void` |
| `TASK.CREATE_PATROL_ROUTE` | — | `void` |
| `TASK.DELETE_PATROL_ROUTE` | a: string | `void` |
| `TASK.GET_PATROL_TASK_INFO` | a1: int, a2: pointer, a3: pointer | `bool` |
| `TASK.TASK_PATROL` | a1: int, a2: string, a3: int, a4: bool, a5: bool | `void` |
| `TASK.TASK_STAY_IN_COVER` | a: int | `void` |
| `TASK.ADD_VEHICLE_SUBTASK_ATTACK_COORD` | a1: int, a2: float, a3: float, a4: float | `void` |
| `TASK.ADD_VEHICLE_SUBTASK_ATTACK_PED` | a: int, b: int | `void` |
| `TASK.TASK_VEHICLE_SHOOT_AT_PED` | a1: int, a2: int, a3: float | `void` |
| `TASK.TASK_VEHICLE_AIM_AT_PED` | a: int, b: int | `void` |
| `TASK.TASK_VEHICLE_SHOOT_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `TASK.TASK_VEHICLE_AIM_AT_COORD` | a1: int, a2: float, a3: float, a4: float | `void` |
| `TASK.TASK_VEHICLE_GOTO_NAVMESH` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: int, a8: float | `void` |
| `TASK.TASK_GO_TO_COORD_WHILE_AIMING_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: float, a11: float, a12: bool, a13: int, a14: bool, a15: hash | `void` |
| `TASK.TASK_GO_TO_COORD_WHILE_AIMING_AT_ENTITY` | a1: int, a2: float, a3: float, a4: float, a5: int, a6: float, a7: bool, a8: float, a9: float, a10: bool, a11: int, a12: bool, a13: hash, a14: int | `void` |
| `TASK.TASK_GO_TO_COORD_AND_AIM_AT_HATED_ENTITIES_NEAR_COORD` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: bool, a10: float, a11: float, a12: bool, a13: int, a14: int, a15: hash | `void` |
| `TASK.TASK_GO_TO_ENTITY_WHILE_AIMING_AT_COORD` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: float, a9: float, a10: bool, a11: bool, a12: hash | `void` |
| `TASK.TASK_GO_TO_ENTITY_WHILE_AIMING_AT_ENTITY` | a1: int, a2: int, a3: int, a4: float, a5: bool, a6: float, a7: float, a8: bool, a9: bool, a10: hash, a11: ", a12: " | `void` |
| `TASK.SET_HIGH_FALL_TASK` | a1: int, a2: int, a3: int, a4: int | `void` |
| `TASK.REQUEST_WAYPOINT_RECORDING` | a: string | `void` |
| `TASK.GET_IS_WAYPOINT_RECORDING_LOADED` | a: string | `bool` |
| `TASK.REMOVE_WAYPOINT_RECORDING` | a: string | `void` |
| `TASK.WAYPOINT_RECORDING_GET_NUM_POINTS` | a: string, b: pointer | `bool` |
| `TASK.WAYPOINT_RECORDING_GET_COORD` | a1: string, a2: int, a3: vector3 | `bool` |
| `TASK.WAYPOINT_RECORDING_GET_SPEED_AT_POINT` | a: string, b: int | `float` |
| `TASK.WAYPOINT_RECORDING_GET_CLOSEST_WAYPOINT` | a1: string, a2: float, a3: float, a4: float, a5: pointer | `bool` |
| `TASK.TASK_FOLLOW_WAYPOINT_RECORDING` | a1: int, a2: string, a3: int, a4: int, a5: int | `void` |
| `TASK.IS_WAYPOINT_PLAYBACK_GOING_ON_FOR_PED` | a: int | `bool` |
| `TASK.GET_PED_WAYPOINT_PROGRESS` | a: int | `int` |
| `TASK.SET_PED_WAYPOINT_PROGRESS` | a: int, b: int | `void` |
| `TASK.GET_PED_WAYPOINT_DISTANCE` | a: int | `float` |
| `TASK.SET_PED_WAYPOINT_ROUTE_OFFSET` | a1: int, a2: float, a3: float, a4: float | `bool` |
| `TASK.GET_WAYPOINT_DISTANCE_ALONG_ROUTE` | a: string, b: int | `float` |
| `TASK.WAYPOINT_PLAYBACK_GET_IS_PAUSED` | a: int | `bool` |
| `TASK.WAYPOINT_PLAYBACK_PAUSE` | a1: int, a2: bool, a3: bool | `void` |
| `TASK.WAYPOINT_PLAYBACK_RESUME` | a1: int, a2: bool, a3: int, a4: int | `void` |
| `TASK.WAYPOINT_PLAYBACK_OVERRIDE_SPEED` | a1: int, a2: float, a3: bool | `void` |
| `TASK.WAYPOINT_PLAYBACK_USE_DEFAULT_SPEED` | a: int | `void` |
| `TASK.USE_WAYPOINT_RECORDING_AS_ASSISTED_MOVEMENT_ROUTE` | a1: string, a2: bool, a3: float, a4: float | `void` |
| `TASK.WAYPOINT_PLAYBACK_START_AIMING_AT_PED` | a1: int, a2: int, a3: bool | `void` |
| `TASK.WAYPOINT_PLAYBACK_START_AIMING_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: bool | `void` |
| `TASK.WAYPOINT_PLAYBACK_START_SHOOTING_AT_PED` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `TASK.WAYPOINT_PLAYBACK_START_SHOOTING_AT_COORD` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: hash | `void` |
| `TASK.WAYPOINT_PLAYBACK_STOP_AIMING_OR_SHOOTING` | a: int | `void` |
| `TASK.ASSISTED_MOVEMENT_REQUEST_ROUTE` | a: string | `void` |
| `TASK.ASSISTED_MOVEMENT_REMOVE_ROUTE` | a: string | `void` |
| `TASK.ASSISTED_MOVEMENT_IS_ROUTE_LOADED` | a: string | `bool` |
| `TASK.ASSISTED_MOVEMENT_SET_ROUTE_PROPERTIES` | a: string, b: int | `void` |
| `TASK.ASSISTED_MOVEMENT_OVERRIDE_LOAD_DISTANCE_THIS_FRAME` | a: float | `void` |
| `TASK.TASK_VEHICLE_FOLLOW_WAYPOINT_RECORDING` | a1: int, a2: int, a3: string, a4: int, a5: int, a6: int, a7: int, a8: float, a9: bool, a10: float | `void` |
| `TASK.IS_WAYPOINT_PLAYBACK_GOING_ON_FOR_VEHICLE` | a: int | `bool` |
| `TASK.GET_VEHICLE_WAYPOINT_PROGRESS` | a: int | `int` |
| `TASK.GET_VEHICLE_WAYPOINT_TARGET_POINT` | a: int | `int` |
| `TASK.VEHICLE_WAYPOINT_PLAYBACK_PAUSE` | a: int | `void` |
| `TASK.VEHICLE_WAYPOINT_PLAYBACK_GET_IS_PAUSED` | a: int | `bool` |
| `TASK.VEHICLE_WAYPOINT_PLAYBACK_RESUME` | a: int | `void` |
| `TASK.VEHICLE_WAYPOINT_PLAYBACK_USE_DEFAULT_SPEED` | a: int | `void` |
| `TASK.VEHICLE_WAYPOINT_PLAYBACK_OVERRIDE_SPEED` | a: int, b: float | `void` |
| `TASK.TASK_SET_BLOCKING_OF_NON_TEMPORARY_EVENTS` | a: int, b: bool | `void` |
| `TASK.TASK_FORCE_MOTION_STATE` | a1: int, a2: hash, a3: bool | `void` |
| `TASK.TASK_MOVE_NETWORK_BY_NAME` | a1: int, a2: string, a3: float, a4: bool, a5: string, a6: int | `void` |
| `TASK.TASK_MOVE_NETWORK_ADVANCED_BY_NAME` | a1: int, a2: string, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: int, a10: float, a11: bool, a12: string, a13: int | `void` |
| `TASK.TASK_MOVE_NETWORK_BY_NAME_WITH_INIT_PARAMS` | a1: int, a2: string, a3: pointer, a4: float, a5: bool, a6: string, a7: int | `void` |
| `TASK.TASK_MOVE_NETWORK_ADVANCED_BY_NAME_WITH_INIT_PARAMS` | a1: int, a2: string, a3: pointer, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: int, a11: float, a12: bool, a13: string, a14: int | `void` |
| `TASK.IS_TASK_MOVE_NETWORK_ACTIVE` | a: int | `bool` |
| `TASK.IS_TASK_MOVE_NETWORK_READY_FOR_TRANSITION` | a: int | `bool` |
| `TASK.REQUEST_TASK_MOVE_NETWORK_STATE_TRANSITION` | a: int, b: string | `bool` |
| `TASK.SET_EXPECTED_CLONE_NEXT_TASK_MOVE_NETWORK_STATE` | a: int, b: string | `bool` |
| `TASK.GET_TASK_MOVE_NETWORK_STATE` | a: int | `string` |
| `TASK.SET_TASK_MOVE_NETWORK_ANIM_SET` | a1: int, a2: hash, a3: hash | `void` |
| `TASK.SET_TASK_MOVE_NETWORK_SIGNAL_FLOAT` | a1: int, a2: string, a3: float | `void` |
| `TASK.SET_TASK_MOVE_NETWORK_SIGNAL_LOCAL_FLOAT` | a1: int, a2: string, a3: float | `void` |
| `TASK.SET_TASK_MOVE_NETWORK_SIGNAL_FLOAT_LERP_RATE` | a1: int, a2: string, a3: float | `void` |
| `TASK.SET_TASK_MOVE_NETWORK_SIGNAL_BOOL` | a1: int, a2: string, a3: bool | `void` |
| `TASK.GET_TASK_MOVE_NETWORK_SIGNAL_FLOAT` | a: int, b: string | `float` |
| `TASK.GET_TASK_MOVE_NETWORK_SIGNAL_BOOL` | a: int, b: string | `bool` |
| `TASK.GET_TASK_MOVE_NETWORK_EVENT` | a: int, b: string | `bool` |
| `TASK.SET_TASK_MOVE_NETWORK_ENABLE_COLLISION_ON_NETWORK_CLONE_WHEN_FIXED` | a: int, b: bool | `bool` |
| `TASK._SET_SCRIPT_TASK_ENABLE_COLLISION_ON_NETWORK_CLONE_WHEN_FIXED` | a: int, b: bool | `void` |
| `TASK._SET_AMBIENT_PED_ENABLE_COLLISION_ON_NETWORK_CLONE_WHEN_FIXED` | a: int, b: bool | `void` |
| `TASK.IS_MOVE_BLEND_RATIO_STILL` | a: int | `bool` |
| `TASK.IS_MOVE_BLEND_RATIO_WALKING` | a: int | `bool` |
| `TASK.IS_MOVE_BLEND_RATIO_RUNNING` | a: int | `bool` |
| `TASK.IS_MOVE_BLEND_RATIO_SPRINTING` | a: int | `bool` |
| `TASK.IS_PED_STILL` | a: int | `bool` |
| `TASK.IS_PED_WALKING` | a: int | `bool` |
| `TASK.IS_PED_RUNNING` | a: int | `bool` |
| `TASK.IS_PED_SPRINTING` | a: int | `bool` |
| `TASK.IS_PED_STRAFING` | a: int | `bool` |
| `TASK.TASK_SYNCHRONIZED_SCENE` | a1: int, a2: int, a3: string, a4: string, a5: float, a6: float, a7: int, a8: int, a9: float, a10: int | `void` |
| `TASK.TASK_AGITATED_ACTION_CONFRONT_RESPONSE` | a: int, b: int | `void` |
| `TASK.TASK_SWEEP_AIM_ENTITY` | a1: int, a2: string, a3: string, a4: string, a5: string, a6: int, a7: int, a8: float, a9: float | `void` |
| `TASK.UPDATE_TASK_SWEEP_AIM_ENTITY` | a: int, b: int | `void` |
| `TASK.TASK_SWEEP_AIM_POSITION` | a1: int, a2: string, a3: string, a4: string, a5: string, a6: int, a7: float, a8: float, a9: float, a10: float, a11: float | `void` |
| `TASK.UPDATE_TASK_SWEEP_AIM_POSITION` | a1: int, a2: float, a3: float, a4: float | `void` |
| `TASK.TASK_ARREST_PED` | a: int, b: int | `void` |
| `TASK.IS_PED_RUNNING_ARREST_TASK` | a: int | `bool` |
| `TASK.IS_PED_BEING_ARRESTED` | a: int | `bool` |
| `TASK.UNCUFF_PED` | a: int | `void` |
| `TASK.IS_PED_CUFFED` | a: int | `bool` |

## VEHICLE

| Native | Params | Returns |
| --- | --- | --- |
| `VEHICLE.CREATE_VEHICLE` | a1: hash, a2: float, a3: float, a4: float, a5: float, a6: bool, a7: bool, a8: bool | `int` |
| `VEHICLE.DELETE_VEHICLE` | a: pointer | `void` |
| `VEHICLE.SET_VEHICLE_ALLOW_HOMING_MISSLE_LOCKON` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE._GET_VEHICLE_ALLOW_HOMING_MISSLE_LOCKON_SYNCED` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_ALLOW_HOMING_MISSLE_LOCKON_SYNCED` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_ALLOW_NO_PASSENGERS_LOCKON` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_HOMING_LOCKON_STATE` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_HOMING_LOCKEDONTO_STATE` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_HOMING_LOCKEDONTO_STATE` | a: int, b: int | `void` |
| `VEHICLE.IS_VEHICLE_MODEL` | a: int, b: hash | `bool` |
| `VEHICLE.DOES_SCRIPT_VEHICLE_GENERATOR_EXIST` | a: int | `bool` |
| `VEHICLE.CREATE_SCRIPT_VEHICLE_GENERATOR` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: hash, a8: int, a9: int, a10: int, a11: int, a12: bool, a13: bool, a14: bool, a15: bool, a16: bool, a17: int | `int` |
| `VEHICLE.DELETE_SCRIPT_VEHICLE_GENERATOR` | a: int | `void` |
| `VEHICLE.SET_SCRIPT_VEHICLE_GENERATOR` | a: int, b: bool | `void` |
| `VEHICLE.SET_ALL_VEHICLE_GENERATORS_ACTIVE_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool, a8: bool | `void` |
| `VEHICLE.SET_ALL_VEHICLE_GENERATORS_ACTIVE` | — | `void` |
| `VEHICLE.SET_ALL_LOW_PRIORITY_VEHICLE_GENERATORS_ACTIVE` | a: bool | `void` |
| `VEHICLE.SET_VEHICLE_GENERATOR_AREA_OF_INTEREST` | a1: float, a2: float, a3: float, a4: float | `void` |
| `VEHICLE.CLEAR_VEHICLE_GENERATOR_AREA_OF_INTEREST` | — | `void` |
| `VEHICLE.SET_VEHICLE_ON_GROUND_PROPERLY` | a: int, b: float | `bool` |
| `VEHICLE.SET_VEHICLE_USE_CUTSCENE_WHEEL_COMPRESSION` | a1: int, a2: bool, a3: bool, a4: bool | `bool` |
| `VEHICLE.IS_VEHICLE_STUCK_ON_ROOF` | a: int | `bool` |
| `VEHICLE.ADD_VEHICLE_UPSIDEDOWN_CHECK` | a: int | `void` |
| `VEHICLE.REMOVE_VEHICLE_UPSIDEDOWN_CHECK` | a: int | `void` |
| `VEHICLE.IS_VEHICLE_STOPPED` | a: int | `bool` |
| `VEHICLE.GET_VEHICLE_NUMBER_OF_PASSENGERS` | a1: int, a2: bool, a3: bool | `int` |
| `VEHICLE.GET_VEHICLE_MAX_NUMBER_OF_PASSENGERS` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_MODEL_NUMBER_OF_SEATS` | a: hash | `int` |
| `VEHICLE.IS_SEAT_WARP_ONLY` | a: int, b: int | `bool` |
| `VEHICLE.IS_TURRET_SEAT` | a: int, b: int | `bool` |
| `VEHICLE.DOES_VEHICLE_ALLOW_RAPPEL` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_DENSITY_MULTIPLIER_THIS_FRAME` | a: float | `void` |
| `VEHICLE.SET_RANDOM_VEHICLE_DENSITY_MULTIPLIER_THIS_FRAME` | a: float | `void` |
| `VEHICLE.SET_PARKED_VEHICLE_DENSITY_MULTIPLIER_THIS_FRAME` | a: float | `void` |
| `VEHICLE.SET_DISABLE_RANDOM_TRAINS_THIS_FRAME` | a: bool | `void` |
| `VEHICLE.SET_AMBIENT_VEHICLE_RANGE_MULTIPLIER_THIS_FRAME` | a: float | `void` |
| `VEHICLE.SET_FAR_DRAW_VEHICLES` | a: bool | `void` |
| `VEHICLE.SET_NUMBER_OF_PARKED_VEHICLES` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_DOORS_LOCKED` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_INDIVIDUAL_DOORS_LOCKED` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.SET_VEHICLE_HAS_MUTED_SIRENS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOORS_LOCKED_FOR_PLAYER` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.GET_VEHICLE_DOORS_LOCKED_FOR_PLAYER` | a: int, b: int | `bool` |
| `VEHICLE.SET_VEHICLE_DOORS_LOCKED_FOR_ALL_PLAYERS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOORS_LOCKED_FOR_NON_SCRIPT_PLAYERS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOORS_LOCKED_FOR_TEAM` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOORS_LOCKED_FOR_ALL_TEAMS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DONT_TERMINATE_TASK_WHEN_ACHIEVED` | a: int | `void` |
| `VEHICLE._SET_VEHICLE_MAX_LAUNCH_ENGINE_REVS` | a: int, b: float | `void` |
| `VEHICLE._GET_VEHICLE_THROTTLE` | a: int | `float` |
| `VEHICLE.EXPLODE_VEHICLE` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_OUT_OF_CONTROL` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_TIMED_EXPLOSION` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.ADD_VEHICLE_PHONE_EXPLOSIVE_DEVICE` | a: int | `void` |
| `VEHICLE.CLEAR_VEHICLE_PHONE_EXPLOSIVE_DEVICE` | — | `void` |
| `VEHICLE.HAS_VEHICLE_PHONE_EXPLOSIVE_DEVICE` | — | `bool` |
| `VEHICLE.DETONATE_VEHICLE_PHONE_EXPLOSIVE_DEVICE` | — | `void` |
| `VEHICLE.HAVE_VEHICLE_REAR_DOORS_BEEN_BLOWN_OPEN_BY_STICKYBOMB` | a: int | `bool` |
| `VEHICLE.SET_TAXI_LIGHTS` | a: int, b: bool | `void` |
| `VEHICLE.IS_TAXI_LIGHT_ON` | a: int | `bool` |
| `VEHICLE.IS_VEHICLE_IN_GARAGE_AREA` | a: string, b: int | `bool` |
| `VEHICLE.SET_VEHICLE_COLOURS` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.SET_VEHICLE_FULLBEAM` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_IS_RACING` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_CUSTOM_PRIMARY_COLOUR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `VEHICLE.GET_VEHICLE_CUSTOM_PRIMARY_COLOUR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `VEHICLE.CLEAR_VEHICLE_CUSTOM_PRIMARY_COLOUR` | a: int | `void` |
| `VEHICLE.GET_IS_VEHICLE_PRIMARY_COLOUR_CUSTOM` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_CUSTOM_SECONDARY_COLOUR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `VEHICLE.GET_VEHICLE_CUSTOM_SECONDARY_COLOUR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `VEHICLE.CLEAR_VEHICLE_CUSTOM_SECONDARY_COLOUR` | a: int | `void` |
| `VEHICLE.GET_IS_VEHICLE_SECONDARY_COLOUR_CUSTOM` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_ENVEFF_SCALE` | a: int, b: float | `void` |
| `VEHICLE.GET_VEHICLE_ENVEFF_SCALE` | a: int | `float` |
| `VEHICLE.SET_CAN_RESPRAY_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE.SET_GOON_BOSS_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE.SET_OPEN_REAR_DOORS_ON_EXPLOSION` | a: int, b: bool | `void` |
| `VEHICLE.FORCE_SUBMARINE_SURFACE_MODE` | a: int, b: bool | `void` |
| `VEHICLE.FORCE_SUBMARINE_NEURTAL_BUOYANCY` | a: int, b: int | `void` |
| `VEHICLE.SET_SUBMARINE_CRUSH_DEPTHS` | a1: int, a2: bool, a3: float, a4: float, a5: float | `void` |
| `VEHICLE.GET_SUBMARINE_IS_UNDER_DESIGN_DEPTH` | a: int | `bool` |
| `VEHICLE.GET_SUBMARINE_NUMBER_OF_AIR_LEAKS` | a: int | `int` |
| `VEHICLE.SET_BOAT_IGNORE_LAND_PROBES` | a: int, b: int | `void` |
| `VEHICLE._SET_BOUNDS_AFFECT_WATER_PROBES` | a: int, b: bool | `void` |
| `VEHICLE.SET_BOAT_ANCHOR` | a: int, b: bool | `void` |
| `VEHICLE.CAN_ANCHOR_BOAT_HERE` | a: int | `bool` |
| `VEHICLE.CAN_ANCHOR_BOAT_HERE_IGNORE_PLAYERS` | a: int | `bool` |
| `VEHICLE.SET_BOAT_REMAINS_ANCHORED_WHILE_PLAYER_IS_DRIVER` | a: int, b: bool | `void` |
| `VEHICLE.SET_FORCE_LOW_LOD_ANCHOR_MODE` | a: int, b: bool | `void` |
| `VEHICLE.SET_BOAT_LOW_LOD_ANCHOR_DISTANCE` | a: int, b: float | `void` |
| `VEHICLE.IS_BOAT_ANCHORED` | a: int | `bool` |
| `VEHICLE.SET_BOAT_SINKS_WHEN_WRECKED` | a: int, b: bool | `void` |
| `VEHICLE.SET_BOAT_WRECKED` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_SIREN` | a: int, b: bool | `void` |
| `VEHICLE.IS_VEHICLE_SIREN_ON` | a: int | `bool` |
| `VEHICLE.IS_VEHICLE_SIREN_AUDIO_ON` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_STRONG` | a: int, b: bool | `void` |
| `VEHICLE.REMOVE_VEHICLE_STUCK_CHECK` | a: int | `void` |
| `VEHICLE.GET_VEHICLE_COLOURS` | a1: int, a2: pointer, a3: pointer | `void` |
| `VEHICLE.IS_VEHICLE_SEAT_FREE` | a1: int, a2: int, a3: bool | `bool` |
| `VEHICLE.GET_PED_IN_VEHICLE_SEAT` | a1: int, a2: int, a3: bool | `int` |
| `VEHICLE.GET_LAST_PED_IN_VEHICLE_SEAT` | a: int, b: int | `int` |
| `VEHICLE.GET_VEHICLE_LIGHTS_STATE` | a1: int, a2: pointer, a3: pointer | `bool` |
| `VEHICLE.IS_VEHICLE_TYRE_BURST` | a1: int, a2: int, a3: bool | `bool` |
| `VEHICLE.SET_VEHICLE_FORWARD_SPEED` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_FORWARD_SPEED_XY` | a: int, b: float | `void` |
| `VEHICLE.BRING_VEHICLE_TO_HALT` | a1: int, a2: float, a3: int, a4: bool | `void` |
| `VEHICLE.SET_VEHICLE_STEER_FOR_BUILDINGS` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_CAUSES_SWERVING` | a: int, b: bool | `void` |
| `VEHICLE.SET_IGNORE_PLANES_SMALL_PITCH_CHANGE` | a: int, b: int | `void` |
| `VEHICLE.STOP_BRINGING_VEHICLE_TO_HALT` | a: int | `void` |
| `VEHICLE.IS_VEHICLE_BEING_BROUGHT_TO_HALT` | a: int | `bool` |
| `VEHICLE.LOWER_FORKLIFT_FORKS` | a: int | `void` |
| `VEHICLE.SET_FORKLIFT_FORK_HEIGHT` | a: int, b: float | `void` |
| `VEHICLE.IS_ENTITY_ATTACHED_TO_HANDLER_FRAME` | a: int, b: int | `bool` |
| `VEHICLE.IS_ANY_ENTITY_ATTACHED_TO_HANDLER_FRAME` | a: int | `bool` |
| `VEHICLE.FIND_HANDLER_VEHICLE_CONTAINER_IS_ATTACHED_TO` | a: int | `int` |
| `VEHICLE.IS_HANDLER_FRAME_LINED_UP_WITH_CONTAINER` | a: int, b: int | `bool` |
| `VEHICLE.ATTACH_CONTAINER_TO_HANDLER_FRAME_WHEN_LINED_UP` | a: int, b: int | `void` |
| `VEHICLE.DETACH_CONTAINER_FROM_HANDLER_FRAME` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_DISABLE_HEIGHT_MAP_AVOIDANCE` | a: int, b: bool | `void` |
| `VEHICLE._SET_PLANE_AVOIDS_OTHERS` | a: int, b: bool | `void` |
| `VEHICLE.SET_BOAT_DISABLE_AVOIDANCE` | a: int, b: bool | `void` |
| `VEHICLE.IS_HELI_LANDING_AREA_BLOCKED` | a: int | `bool` |
| `VEHICLE.SET_SHORT_SLOWDOWN_FOR_LANDING` | a: int | `void` |
| `VEHICLE.SET_HELI_TURBULENCE_SCALAR` | a: int, b: float | `void` |
| `VEHICLE.SET_CAR_BOOT_OPEN` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_TYRE_BURST` | a1: int, a2: int, a3: bool, a4: float | `void` |
| `VEHICLE.SET_VEHICLE_DOORS_SHUT` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_TYRES_CAN_BURST` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_TYRES_CAN_BURST` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_WHEELS_CAN_BREAK` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOOR_OPEN` | a1: int, a2: int, a3: bool, a4: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOOR_AUTO_LOCK` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.SET_FLEEING_VEHICLES_USE_SWITCHED_OFF_NODES` | a: int | `void` |
| `VEHICLE.REMOVE_VEHICLE_WINDOW` | a: int, b: int | `void` |
| `VEHICLE.ROLL_DOWN_WINDOWS` | a: int | `void` |
| `VEHICLE.ROLL_DOWN_WINDOW` | a: int, b: int | `void` |
| `VEHICLE.ROLL_UP_WINDOW` | a: int, b: int | `void` |
| `VEHICLE.SMASH_VEHICLE_WINDOW` | a: int, b: int | `void` |
| `VEHICLE.FIX_VEHICLE_WINDOW` | a: int, b: int | `void` |
| `VEHICLE.POP_OUT_VEHICLE_WINDSCREEN` | a: int | `void` |
| `VEHICLE.POP_OFF_VEHICLE_ROOF_WITH_IMPULSE` | a1: int, a2: float, a3: float, a4: float | `void` |
| `VEHICLE.SET_VEHICLE_LIGHTS` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_USE_PLAYER_LIGHT_SETTINGS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_HEADLIGHT_SHADOWS` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_ALARM` | a: int, b: bool | `void` |
| `VEHICLE.START_VEHICLE_ALARM` | a: int | `void` |
| `VEHICLE.IS_VEHICLE_ALARM_ACTIVATED` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_INTERIORLIGHT` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_FORCE_INTERIORLIGHT` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_LIGHT_MULTIPLIER` | a: int, b: float | `void` |
| `VEHICLE.ATTACH_VEHICLE_TO_TRAILER` | a1: int, a2: int, a3: float | `void` |
| `VEHICLE.ATTACH_VEHICLE_ON_TO_TRAILER` | a1: int, a2: int, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: float, a12: float | `void` |
| `VEHICLE.STABILISE_ENTITY_ATTACHED_TO_HELI` | a1: int, a2: int, a3: float | `void` |
| `VEHICLE.DETACH_VEHICLE_FROM_TRAILER` | a: int | `void` |
| `VEHICLE.IS_VEHICLE_ATTACHED_TO_TRAILER` | a: int | `bool` |
| `VEHICLE.SET_TRAILER_INVERSE_MASS_SCALE` | a: int, b: float | `void` |
| `VEHICLE.SET_TRAILER_LEGS_RAISED` | a: int | `void` |
| `VEHICLE.SET_TRAILER_LEGS_LOWERED` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_TYRE_FIXED` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_NUMBER_PLATE_TEXT` | a: int, b: string | `void` |
| `VEHICLE.GET_VEHICLE_NUMBER_PLATE_TEXT` | a: int | `string` |
| `VEHICLE.GET_NUMBER_OF_VEHICLE_NUMBER_PLATES` | — | `int` |
| `VEHICLE.SET_VEHICLE_NUMBER_PLATE_TEXT_INDEX` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_NUMBER_PLATE_TEXT_INDEX` | a: int | `int` |
| `VEHICLE.SET_RANDOM_TRAINS` | a: bool | `void` |
| `VEHICLE.CREATE_MISSION_TRAIN` | a1: int, a2: float, a3: float, a4: float, a5: bool, a6: int, a7: int | `int` |
| `VEHICLE.SWITCH_TRAIN_TRACK` | a: int, b: bool | `void` |
| `VEHICLE.SET_TRAIN_TRACK_SPAWN_FREQUENCY` | a: int, b: int | `void` |
| `VEHICLE.ALLOW_TRAIN_TO_BE_REMOVED_BY_POPULATION` | a: int | `void` |
| `VEHICLE.DELETE_ALL_TRAINS` | — | `void` |
| `VEHICLE.SET_TRAIN_SPEED` | a: int, b: float | `void` |
| `VEHICLE.SET_TRAIN_CRUISE_SPEED` | a: int, b: float | `void` |
| `VEHICLE.SET_RANDOM_BOATS` | a: bool | `void` |
| `VEHICLE.SET_RANDOM_BOATS_MP` | a: bool | `void` |
| `VEHICLE.SET_GARBAGE_TRUCKS` | a: bool | `void` |
| `VEHICLE.DOES_VEHICLE_HAVE_STUCK_VEHICLE_CHECK` | a: int | `bool` |
| `VEHICLE.GET_VEHICLE_RECORDING_ID` | a: int, b: string | `int` |
| `VEHICLE.REQUEST_VEHICLE_RECORDING` | a: int, b: string | `void` |
| `VEHICLE.HAS_VEHICLE_RECORDING_BEEN_LOADED` | a: int, b: string | `bool` |
| `VEHICLE.REMOVE_VEHICLE_RECORDING` | a: int, b: string | `void` |
| `VEHICLE.GET_POSITION_OF_VEHICLE_RECORDING_ID_AT_TIME` | a: int, b: float | `vector3` |
| `VEHICLE.GET_POSITION_OF_VEHICLE_RECORDING_AT_TIME` | a1: int, a2: float, a3: string | `vector3` |
| `VEHICLE.GET_ROTATION_OF_VEHICLE_RECORDING_ID_AT_TIME` | a: int, b: float | `vector3` |
| `VEHICLE.GET_ROTATION_OF_VEHICLE_RECORDING_AT_TIME` | a1: int, a2: float, a3: string | `vector3` |
| `VEHICLE.GET_TOTAL_DURATION_OF_VEHICLE_RECORDING_ID` | a: int | `float` |
| `VEHICLE.GET_TOTAL_DURATION_OF_VEHICLE_RECORDING` | a: int, b: string | `float` |
| `VEHICLE.GET_POSITION_IN_RECORDING` | a: int | `float` |
| `VEHICLE.GET_TIME_POSITION_IN_RECORDING` | a: int | `float` |
| `VEHICLE.START_PLAYBACK_RECORDED_VEHICLE` | a1: int, a2: int, a3: string, a4: bool | `void` |
| `VEHICLE.START_PLAYBACK_RECORDED_VEHICLE_WITH_FLAGS` | a1: int, a2: int, a3: string, a4: int, a5: int, a6: int | `void` |
| `VEHICLE.FORCE_PLAYBACK_RECORDED_VEHICLE_UPDATE` | a: int, b: bool | `void` |
| `VEHICLE.STOP_PLAYBACK_RECORDED_VEHICLE` | a: int | `void` |
| `VEHICLE.PAUSE_PLAYBACK_RECORDED_VEHICLE` | a: int | `void` |
| `VEHICLE.UNPAUSE_PLAYBACK_RECORDED_VEHICLE` | a: int | `void` |
| `VEHICLE.IS_PLAYBACK_GOING_ON_FOR_VEHICLE` | a: int | `bool` |
| `VEHICLE.IS_PLAYBACK_USING_AI_GOING_ON_FOR_VEHICLE` | a: int | `bool` |
| `VEHICLE.GET_CURRENT_PLAYBACK_FOR_VEHICLE` | a: int | `int` |
| `VEHICLE.SKIP_TO_END_AND_STOP_PLAYBACK_RECORDED_VEHICLE` | a: int | `void` |
| `VEHICLE.SET_PLAYBACK_SPEED` | a: int, b: float | `void` |
| `VEHICLE.START_PLAYBACK_RECORDED_VEHICLE_USING_AI` | a1: int, a2: int, a3: string, a4: float, a5: int | `void` |
| `VEHICLE.SKIP_TIME_IN_PLAYBACK_RECORDED_VEHICLE` | a: int, b: float | `void` |
| `VEHICLE.SET_PLAYBACK_TO_USE_AI` | a: int, b: int | `void` |
| `VEHICLE.SET_PLAYBACK_TO_USE_AI_TRY_TO_REVERT_BACK_LATER` | a1: int, a2: int, a3: int, a4: bool | `void` |
| `VEHICLE.SET_ADDITIONAL_ROTATION_FOR_RECORDED_VEHICLE_PLAYBACK` | a1: int, a2: float, a3: float, a4: float, a5: int | `void` |
| `VEHICLE.SET_POSITION_OFFSET_FOR_RECORDED_VEHICLE_PLAYBACK` | a1: int, a2: float, a3: float, a4: float | `void` |
| `VEHICLE.SET_GLOBAL_POSITION_OFFSET_FOR_RECORDED_VEHICLE_PLAYBACK` | a1: int, a2: float, a3: float, a4: float | `void` |
| `VEHICLE.SET_SHOULD_LERP_FROM_AI_TO_FULL_RECORDING` | a: int, b: bool | `void` |
| `VEHICLE.EXPLODE_VEHICLE_IN_CUTSCENE` | a: int, b: bool | `void` |
| `VEHICLE.ADD_VEHICLE_STUCK_CHECK_WITH_WARP` | a1: int, a2: float, a3: int, a4: bool, a5: bool, a6: bool, a7: int | `void` |
| `VEHICLE.SET_VEHICLE_MODEL_IS_SUPPRESSED` | a: hash, b: bool | `void` |
| `VEHICLE.GET_RANDOM_VEHICLE_IN_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: int | `int` |
| `VEHICLE.GET_RANDOM_VEHICLE_FRONT_BUMPER_IN_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int | `int` |
| `VEHICLE.GET_RANDOM_VEHICLE_BACK_BUMPER_IN_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: int, a6: int, a7: int | `int` |
| `VEHICLE.GET_CLOSEST_VEHICLE` | a1: float, a2: float, a3: float, a4: float, a5: hash, a6: int | `int` |
| `VEHICLE.GET_TRAIN_CARRIAGE` | a: int, b: int | `int` |
| `VEHICLE._SET_MAKE_TRAIN_SCAN_FOR_BLOCKING_ENTITIES` | a: int, b: bool | `void` |
| `VEHICLE._GET_MISSION_TRAIN_CONFIG_INDEX_BY_NAME` | a: string | `int` |
| `VEHICLE.IS_MISSION_TRAIN` | a: int | `bool` |
| `VEHICLE.DELETE_MISSION_TRAIN` | a: pointer | `void` |
| `VEHICLE.SET_MISSION_TRAIN_COORDS` | a1: int, a2: float, a3: float, a4: float | `void` |
| `VEHICLE.IS_THIS_MODEL_A_BOAT` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_JETSKI` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_PLANE` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_HELI` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_CAR` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_TRAIN` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_BIKE` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_BICYCLE` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_A_QUADBIKE` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_AN_AMPHIBIOUS_CAR` | a: hash | `bool` |
| `VEHICLE.IS_THIS_MODEL_AN_AMPHIBIOUS_QUADBIKE` | a: hash | `bool` |
| `VEHICLE.SET_HELI_BLADES_FULL_SPEED` | a: int | `void` |
| `VEHICLE.SET_HELI_BLADES_SPEED` | a: int, b: float | `void` |
| `VEHICLE.FORCE_SUB_THROTTLE_FOR_TIME` | a1: int, a2: float, a3: float | `void` |
| `VEHICLE.SET_VEHICLE_CAN_BE_TARGETTED` | a: int, b: bool | `void` |
| `VEHICLE.SET_DONT_ALLOW_PLAYER_TO_ENTER_VEHICLE_IF_LOCKED_FOR_PLAYER` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_CAN_BE_VISIBLY_DAMAGED` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_HAS_UNBREAKABLE_LIGHTS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_RESPECTS_LOCKS_WHEN_HAS_DRIVER` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_CAN_EJECT_PASSENGERS_IF_LOCKED` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_DIRT_LEVEL` | a: int | `float` |
| `VEHICLE.SET_VEHICLE_DIRT_LEVEL` | a: int, b: float | `void` |
| `VEHICLE.GET_DOES_VEHICLE_HAVE_DAMAGE_DECALS` | a: int | `bool` |
| `VEHICLE.IS_VEHICLE_DOOR_FULLY_OPEN` | a: int, b: int | `bool` |
| `VEHICLE.SET_VEHICLE_ENGINE_ON` | a1: int, a2: bool, a3: bool, a4: bool | `void` |
| `VEHICLE.SET_VEHICLE_UNDRIVEABLE` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_PROVIDES_COVER` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOOR_CONTROL` | a1: int, a2: int, a3: int, a4: float | `void` |
| `VEHICLE.SET_VEHICLE_DOOR_LATCHED` | a1: int, a2: int, a3: bool, a4: bool, a5: bool | `void` |
| `VEHICLE.GET_VEHICLE_DOOR_ANGLE_RATIO` | a: int, b: int | `float` |
| `VEHICLE.GET_PED_USING_VEHICLE_DOOR` | a: int, b: int | `int` |
| `VEHICLE.SET_VEHICLE_DOOR_SHUT` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_DOOR_BROKEN` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_CAN_BREAK` | a: int, b: bool | `void` |
| `VEHICLE.DOES_VEHICLE_HAVE_ROOF` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_REMOVE_AGGRESSIVE_CARJACK_MISSION` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_AVOID_PLAYER_VEHICLE_RIOT_VAN_MISSION` | a: int | `void` |
| `VEHICLE.SET_CARJACK_MISSION_REMOVAL_PARAMETERS` | a: int, b: int | `void` |
| `VEHICLE.IS_BIG_VEHICLE` | a: int | `bool` |
| `VEHICLE.GET_NUMBER_OF_VEHICLE_COLOURS` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_COLOUR_COMBINATION` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_COLOUR_COMBINATION` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_XENON_LIGHT_COLOR_INDEX` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_XENON_LIGHT_COLOR_INDEX` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_IS_CONSIDERED_BY_PLAYER` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_WILL_FORCE_OTHER_VEHICLES_TO_STOP` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_ACT_AS_IF_HAS_SIREN_ON` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_USE_MORE_RESTRICTIVE_SPAWN_CHECKS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_MAY_BE_USED_BY_GOTO_POINT_ANY_MEANS` | a: int, b: bool | `void` |
| `VEHICLE.GET_RANDOM_VEHICLE_MODEL_IN_MEMORY` | a1: bool, a2: pointer, a3: pointer | `void` |
| `VEHICLE.GET_VEHICLE_DOOR_LOCK_STATUS` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_INDIVIDUAL_DOOR_LOCK_STATUS` | a: int, b: int | `int` |
| `VEHICLE.IS_VEHICLE_DOOR_DAMAGED` | a: int, b: int | `bool` |
| `VEHICLE.SET_DOOR_ALLOWED_TO_BE_BROKEN_OFF` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.IS_VEHICLE_BUMPER_BOUNCING` | a: int, b: bool | `bool` |
| `VEHICLE.IS_VEHICLE_BUMPER_BROKEN_OFF` | a: int, b: bool | `bool` |
| `VEHICLE.IS_COP_VEHICLE_IN_AREA_3D` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float | `bool` |
| `VEHICLE.IS_VEHICLE_ON_ALL_WHEELS` | a: int | `bool` |
| `VEHICLE.GET_VEHICLE_MODEL_VALUE` | a: hash | `int` |
| `VEHICLE.GET_VEHICLE_LAYOUT_HASH` | a: int | `hash` |
| `VEHICLE.GET_IN_VEHICLE_CLIPSET_HASH_FOR_SEAT` | a: int, b: int | `hash` |
| `VEHICLE.SET_RENDER_TRAIN_AS_DERAILED` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_EXTRA_COLOURS` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.GET_VEHICLE_EXTRA_COLOURS` | a1: int, a2: pointer, a3: pointer | `void` |
| `VEHICLE.SET_VEHICLE_EXTRA_COLOUR_5` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_EXTRA_COLOUR_5` | a: int, b: pointer | `void` |
| `VEHICLE.SET_VEHICLE_EXTRA_COLOUR_6` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_EXTRA_COLOUR_6` | a: int, b: pointer | `void` |
| `VEHICLE.STOP_ALL_GARAGE_ACTIVITY` | — | `void` |
| `VEHICLE.SET_VEHICLE_FIXED` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_DEFORMATION_FIXED` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_CAN_ENGINE_MISSFIRE` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_CAN_LEAK_OIL` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_CAN_LEAK_PETROL` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_VEHICLE_PETROL_TANK_FIRES` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_VEHICLE_PETROL_TANK_DAMAGE` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_VEHICLE_ENGINE_FIRES` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_LIMIT_SPEED_WHEN_PLAYER_INACTIVE` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_STOP_INSTANTLY_WHEN_PLAYER_INACTIVE` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_PRETEND_OCCUPANTS` | a: int, b: bool | `void` |
| `VEHICLE.REMOVE_VEHICLES_FROM_GENERATORS_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int | `void` |
| `VEHICLE.SET_VEHICLE_STEER_BIAS` | a: int, b: float | `void` |
| `VEHICLE.IS_VEHICLE_EXTRA_TURNED_ON` | a: int, b: int | `bool` |
| `VEHICLE.SET_VEHICLE_EXTRA` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.DOES_EXTRA_EXIST` | a: int, b: int | `bool` |
| `VEHICLE.IS_EXTRA_BROKEN_OFF` | a: int, b: int | `bool` |
| `VEHICLE.SET_CONVERTIBLE_ROOF` | a: int, b: bool | `void` |
| `VEHICLE.LOWER_CONVERTIBLE_ROOF` | a: int, b: bool | `void` |
| `VEHICLE.RAISE_CONVERTIBLE_ROOF` | a: int, b: bool | `void` |
| `VEHICLE.GET_CONVERTIBLE_ROOF_STATE` | a: int | `int` |
| `VEHICLE.IS_VEHICLE_A_CONVERTIBLE` | a: int, b: bool | `bool` |
| `VEHICLE.TRANSFORM_TO_SUBMARINE` | a: int, b: bool | `bool` |
| `VEHICLE.TRANSFORM_TO_CAR` | a: int, b: bool | `void` |
| `VEHICLE.IS_VEHICLE_IN_SUBMARINE_MODE` | a: int | `bool` |
| `VEHICLE.IS_VEHICLE_STOPPED_AT_TRAFFIC_LIGHTS` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_DAMAGE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: bool | `void` |
| `VEHICLE.SET_VEHICLE_OCCUPANTS_TAKE_EXPLOSIVE_DAMAGE` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_ENGINE_HEALTH` | a: int | `float` |
| `VEHICLE.SET_VEHICLE_ENGINE_HEALTH` | a: int, b: float | `void` |
| `VEHICLE.SET_PLANE_ENGINE_HEALTH` | a: int, b: float | `void` |
| `VEHICLE.GET_VEHICLE_PETROL_TANK_HEALTH` | a: int | `float` |
| `VEHICLE.SET_VEHICLE_PETROL_TANK_HEALTH` | a: int, b: float | `void` |
| `VEHICLE.IS_VEHICLE_STUCK_TIMER_UP` | a1: int, a2: int, a3: int | `bool` |
| `VEHICLE.RESET_VEHICLE_STUCK_TIMER` | a: int, b: int | `void` |
| `VEHICLE.IS_VEHICLE_DRIVEABLE` | a: int, b: bool | `bool` |
| `VEHICLE.SET_VEHICLE_HAS_BEEN_OWNED_BY_PLAYER` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_NEEDS_TO_BE_HOTWIRED` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_BLIP_THROTTLE_RANDOMLY` | a: int, b: bool | `void` |
| `VEHICLE.SET_POLICE_FOCUS_WILL_TRACK_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE.START_VEHICLE_HORN` | a1: int, a2: int, a3: hash, a4: bool | `void` |
| `VEHICLE.SET_VEHICLE_IN_CAR_MOD_SHOP` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_HAS_STRONG_AXLES` | a: int, b: bool | `void` |
| `VEHICLE.GET_DISPLAY_NAME_FROM_VEHICLE_MODEL` | a: hash | `string` |
| `VEHICLE.GET_MAKE_NAME_FROM_VEHICLE_MODEL` | a: hash | `string` |
| `VEHICLE.GET_VEHICLE_DEFORMATION_AT_POS` | a1: int, a2: float, a3: float, a4: float | `vector3` |
| `VEHICLE.SET_VEHICLE_LIVERY` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_LIVERY` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_LIVERY_COUNT` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_LIVERY2` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_LIVERY2` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_LIVERY2_COUNT` | a: int | `int` |
| `VEHICLE.IS_VEHICLE_WINDOW_INTACT` | a: int, b: int | `bool` |
| `VEHICLE.ARE_ALL_VEHICLE_WINDOWS_INTACT` | a: int | `bool` |
| `VEHICLE.ARE_ANY_VEHICLE_SEATS_FREE` | a: int | `bool` |
| `VEHICLE.RESET_VEHICLE_WHEELS` | a: int, b: bool | `void` |
| `VEHICLE.IS_HELI_PART_BROKEN` | a1: int, a2: bool, a3: bool, a4: bool | `bool` |
| `VEHICLE.GET_HELI_MAIN_ROTOR_HEALTH` | a: int | `float` |
| `VEHICLE.GET_HELI_TAIL_ROTOR_HEALTH` | a: int | `float` |
| `VEHICLE.GET_HELI_TAIL_BOOM_HEALTH` | a: int | `float` |
| `VEHICLE.SET_HELI_MAIN_ROTOR_HEALTH` | a: int, b: float | `void` |
| `VEHICLE.SET_HELI_TAIL_ROTOR_HEALTH` | a: int, b: float | `void` |
| `VEHICLE.SET_HELI_TAIL_BOOM_CAN_BREAK_OFF` | a: int, b: bool | `bool` |
| `VEHICLE.SET_VEHICLE_NAME_DEBUG` | a: int, b: string | `void` |
| `VEHICLE.SET_VEHICLE_EXPLODES_ON_HIGH_EXPLOSION_DAMAGE` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_EXPLODES_ON_EXPLOSION_DAMAGE_AT_ZERO_BODY_HEALTH` | a: int, b: bool | `void` |
| `VEHICLE.SET_ALLOW_VEHICLE_EXPLODES_ON_CONTACT` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DISABLE_TOWING` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_HAS_LANDING_GEAR` | a: int | `bool` |
| `VEHICLE.CONTROL_LANDING_GEAR` | a: int, b: int | `void` |
| `VEHICLE.GET_LANDING_GEAR_STATE` | a: int | `int` |
| `VEHICLE.IS_ANY_VEHICLE_NEAR_POINT` | a1: float, a2: float, a3: float, a4: float | `bool` |
| `VEHICLE.REQUEST_VEHICLE_HIGH_DETAIL_MODEL` | a: int | `void` |
| `VEHICLE._GET_VEHICLE_MODEL_NUM_DRIVE_GEARS` | a: hash | `int` |
| `VEHICLE._GET_VEHICLE_MAX_DRIVE_GEAR_COUNT` | a: int | `int` |
| `VEHICLE._GET_IS_VEHICLE_ELECTRIC` | a: hash | `bool` |
| `VEHICLE._GET_VEHICLE_DRIVETRAIN_TYPE` | a: hash | `int` |
| `VEHICLE._GET_VEHICLE_CURRENT_DRIVE_GEAR` | a: int | `int` |
| `VEHICLE._GET_VEHICLE_CURRENT_REV_RATIO` | a: int | `float` |
| `VEHICLE.REMOVE_VEHICLE_HIGH_DETAIL_MODEL` | a: int | `void` |
| `VEHICLE.IS_VEHICLE_HIGH_DETAIL` | a: int | `bool` |
| `VEHICLE.REQUEST_VEHICLE_ASSET` | a: hash, b: int | `void` |
| `VEHICLE.HAS_VEHICLE_ASSET_LOADED` | a: int | `bool` |
| `VEHICLE.REMOVE_VEHICLE_ASSET` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_TOW_TRUCK_ARM_POSITION` | a: int, b: float | `void` |
| `VEHICLE._SET_ATTACHED_VEHICLE_TO_TOW_TRUCK_ARM` | a: int, b: int | `void` |
| `VEHICLE.ATTACH_VEHICLE_TO_TOW_TRUCK` | a1: int, a2: int, a3: bool, a4: float, a5: float, a6: float | `void` |
| `VEHICLE.DETACH_VEHICLE_FROM_TOW_TRUCK` | a: int, b: int | `void` |
| `VEHICLE.DETACH_VEHICLE_FROM_ANY_TOW_TRUCK` | a: int | `bool` |
| `VEHICLE.IS_VEHICLE_ATTACHED_TO_TOW_TRUCK` | a: int, b: int | `bool` |
| `VEHICLE.GET_ENTITY_ATTACHED_TO_TOW_TRUCK` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_AUTOMATICALLY_ATTACHES` | a1: int, a2: bool, a3: int | `int` |
| `VEHICLE.SET_VEHICLE_BULLDOZER_ARM_POSITION` | a1: int, a2: float, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_TANK_TURRET_POSITION` | a1: int, a2: float, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_TURRET_TARGET` | a1: int, a2: bool, a3: float, a4: float, a5: float, a6: bool | `void` |
| `VEHICLE.SET_VEHICLE_TANK_STATIONARY` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_TURRET_SPEED_THIS_FRAME` | a: int, b: float | `void` |
| `VEHICLE.DISABLE_VEHICLE_TURRET_MOVEMENT_THIS_FRAME` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_FLIGHT_NOZZLE_POSITION` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_FLIGHT_NOZZLE_POSITION_IMMEDIATE` | a: int, b: float | `void` |
| `VEHICLE.GET_VEHICLE_FLIGHT_NOZZLE_POSITION` | a: int | `float` |
| `VEHICLE.SET_DISABLE_VERTICAL_FLIGHT_MODE_TRANSITION` | a: int, b: bool | `void` |
| `VEHICLE.GENERATE_VEHICLE_CREATION_POS_FROM_PATHS` | a1: vector3, a2: int, a3: vector3, a4: int, a5: int, a6: int, a7: int, a8: int, a9: int | `bool` |
| `VEHICLE.SET_VEHICLE_BURNOUT` | a: int, b: bool | `void` |
| `VEHICLE.IS_VEHICLE_IN_BURNOUT` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_REDUCE_GRIP` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_REDUCE_GRIP_LEVEL` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_INDICATOR_LIGHTS` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.SET_VEHICLE_BRAKE_LIGHTS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_TAIL_LIGHTS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_HANDBRAKE` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_BRAKE` | a: int, b: bool | `void` |
| `VEHICLE.INSTANTLY_FILL_VEHICLE_POPULATION` | — | `void` |
| `VEHICLE.HAS_INSTANT_FILL_VEHICLE_POPULATION_FINISHED` | — | `bool` |
| `VEHICLE.NETWORK_ENABLE_EMPTY_CROWDING_VEHICLES_REMOVAL` | a: bool | `void` |
| `VEHICLE.NETWORK_CAP_EMPTY_CROWDING_VEHICLES_REMOVAL` | a: int | `void` |
| `VEHICLE.GET_VEHICLE_TRAILER_VEHICLE` | a: int, b: pointer | `bool` |
| `VEHICLE._GET_VEHICLE_TRAILER_PARENT_VEHICLE` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_USES_LARGE_REAR_RAMP` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_RUDDER_BROKEN` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_TAIL_BROKEN` | a: int, b: bool | `void` |
| `VEHICLE.SET_CONVERTIBLE_ROOF_LATCH_STATE` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_ESTIMATED_MAX_SPEED` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_MAX_BRAKING` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_MAX_TRACTION` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_ACCELERATION` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_MODEL_ESTIMATED_MAX_SPEED` | a: hash | `float` |
| `VEHICLE.GET_VEHICLE_MODEL_MAX_BRAKING` | a: hash | `float` |
| `VEHICLE.GET_VEHICLE_MODEL_MAX_BRAKING_MAX_MODS` | a: hash | `float` |
| `VEHICLE.GET_VEHICLE_MODEL_MAX_TRACTION` | a: hash | `float` |
| `VEHICLE.GET_VEHICLE_MODEL_ACCELERATION` | a: hash | `float` |
| `VEHICLE.GET_VEHICLE_MODEL_ACCELERATION_MAX_MODS` | a: hash | `float` |
| `VEHICLE.GET_FLYING_VEHICLE_MODEL_AGILITY` | a: hash | `float` |
| `VEHICLE.GET_BOAT_VEHICLE_MODEL_AGILITY` | a: hash | `float` |
| `VEHICLE.GET_VEHICLE_CLASS_ESTIMATED_MAX_SPEED` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_CLASS_MAX_TRACTION` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_CLASS_MAX_AGILITY` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_CLASS_MAX_ACCELERATION` | a: int | `float` |
| `VEHICLE.GET_VEHICLE_CLASS_MAX_BRAKING` | a: int | `float` |
| `VEHICLE.ADD_ROAD_NODE_SPEED_ZONE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: bool | `int` |
| `VEHICLE.REMOVE_ROAD_NODE_SPEED_ZONE` | a: int | `bool` |
| `VEHICLE.OPEN_BOMB_BAY_DOORS` | a: int | `void` |
| `VEHICLE.CLOSE_BOMB_BAY_DOORS` | a: int | `void` |
| `VEHICLE.GET_ARE_BOMB_BAY_DOORS_OPEN` | a: int | `bool` |
| `VEHICLE.IS_VEHICLE_SEARCHLIGHT_ON` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_SEARCHLIGHT` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE.DOES_VEHICLE_HAVE_SEARCHLIGHT` | a: int | `bool` |
| `VEHICLE.IS_ENTRY_POINT_FOR_SEAT_CLEAR` | a1: int, a2: int, a3: int, a4: bool, a5: bool | `bool` |
| `VEHICLE.GET_ENTRY_POINT_POSITION` | a: int, b: int | `vector3` |
| `VEHICLE.CAN_SHUFFLE_SEAT` | a: int, b: int | `bool` |
| `VEHICLE.GET_NUM_MOD_KITS` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_MOD_KIT` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_MOD_KIT` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_WHEEL_TYPE` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_WHEEL_TYPE` | a: int, b: int | `void` |
| `VEHICLE.GET_NUM_MOD_COLORS` | a: int, b: bool | `int` |
| `VEHICLE.SET_VEHICLE_MOD_COLOR_1` | a1: int, a2: int, a3: int, a4: int | `void` |
| `VEHICLE.SET_VEHICLE_MOD_COLOR_2` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.GET_VEHICLE_MOD_COLOR_1` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `VEHICLE.GET_VEHICLE_MOD_COLOR_2` | a1: int, a2: pointer, a3: pointer | `void` |
| `VEHICLE.GET_VEHICLE_MOD_COLOR_1_NAME` | a: int, b: bool | `string` |
| `VEHICLE.GET_VEHICLE_MOD_COLOR_2_NAME` | a: int | `string` |
| `VEHICLE.HAVE_VEHICLE_MODS_STREAMED_IN` | a: int | `bool` |
| `VEHICLE.IS_VEHICLE_MOD_GEN9_EXCLUSIVE` | a1: int, a2: int, a3: int | `bool` |
| `VEHICLE.SET_VEHICLE_MOD` | a1: int, a2: int, a3: int, a4: bool | `void` |
| `VEHICLE.GET_VEHICLE_MOD` | a: int, b: int | `int` |
| `VEHICLE.GET_VEHICLE_MOD_VARIATION` | a: int, b: int | `int` |
| `VEHICLE.GET_NUM_VEHICLE_MODS` | a: int, b: int | `int` |
| `VEHICLE.REMOVE_VEHICLE_MOD` | a: int, b: int | `void` |
| `VEHICLE.TOGGLE_VEHICLE_MOD` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.IS_TOGGLE_MOD_ON` | a: int, b: int | `bool` |
| `VEHICLE.GET_MOD_TEXT_LABEL` | a1: int, a2: int, a3: int | `string` |
| `VEHICLE.GET_MOD_SLOT_NAME` | a: int, b: int | `string` |
| `VEHICLE.GET_LIVERY_NAME` | a: int, b: int | `string` |
| `VEHICLE.GET_VEHICLE_MOD_MODIFIER_VALUE` | a1: int, a2: int, a3: int | `int` |
| `VEHICLE.GET_VEHICLE_MOD_IDENTIFIER_HASH` | a1: int, a2: int, a3: int | `hash` |
| `VEHICLE.PRELOAD_VEHICLE_MOD` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.HAS_PRELOAD_MODS_FINISHED` | a: int | `bool` |
| `VEHICLE.RELEASE_PRELOAD_MODS` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_TYRE_SMOKE_COLOR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `VEHICLE.GET_VEHICLE_TYRE_SMOKE_COLOR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `VEHICLE.SET_VEHICLE_WINDOW_TINT` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_WINDOW_TINT` | a: int | `int` |
| `VEHICLE.GET_NUM_VEHICLE_WINDOW_TINTS` | — | `int` |
| `VEHICLE.GET_VEHICLE_COLOR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `VEHICLE.GET_VEHICLE_COLOURS_WHICH_CAN_BE_SET` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_CAUSE_OF_DESTRUCTION` | a: int | `hash` |
| `VEHICLE.OVERRIDE_PLANE_DAMAGE_THREHSOLD` | a: int, b: float | `void` |
| `VEHICLE._SET_TRANSMISSION_REDUCED_GEAR_RATIO` | a: int, b: bool | `void` |
| `VEHICLE._GET_VEHICLE_DESIRED_DRIVE_GEAR` | a: int | `int` |
| `VEHICLE.GET_IS_LEFT_VEHICLE_HEADLIGHT_DAMAGED` | a: int | `bool` |
| `VEHICLE.GET_IS_RIGHT_VEHICLE_HEADLIGHT_DAMAGED` | a: int | `bool` |
| `VEHICLE.GET_BOTH_VEHICLE_HEADLIGHTS_DAMAGED` | a: int | `bool` |
| `VEHICLE.MODIFY_VEHICLE_TOP_SPEED` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_MAX_SPEED` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_STAYS_FROZEN_WHEN_CLEANED_UP` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_ACT_AS_IF_HIGH_SPEED_FOR_FRAG_SMASHING` | a: int, b: bool | `void` |
| `VEHICLE.SET_PEDS_CAN_FALL_OFF_THIS_VEHICLE_FROM_LARGE_FALL_DAMAGE` | a1: int, a2: bool, a3: float | `void` |
| `VEHICLE.ADD_VEHICLE_COMBAT_ANGLED_AVOIDANCE_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `int` |
| `VEHICLE.REMOVE_VEHICLE_COMBAT_AVOIDANCE_AREA` | a: int | `void` |
| `VEHICLE.IS_ANY_PED_RAPPELLING_FROM_HELI` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_CHEAT_POWER_INCREASE` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_INFLUENCES_WANTED_LEVEL` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_IS_WANTED` | a: int, b: bool | `void` |
| `VEHICLE.SWING_BOAT_BOOM_TO_RATIO` | a: int, b: float | `void` |
| `VEHICLE.SWING_BOAT_BOOM_FREELY` | a: int, b: bool | `void` |
| `VEHICLE.ALLOW_BOAT_BOOM_TO_ANIMATE` | a: int, b: bool | `void` |
| `VEHICLE.GET_BOAT_BOOM_POSITION_RATIO` | a: int | `float` |
| `VEHICLE.DISABLE_PLANE_AILERON` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE.GET_IS_VEHICLE_ENGINE_RUNNING` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_USE_ALTERNATE_HANDLING` | a: int, b: bool | `void` |
| `VEHICLE.SET_BIKE_ON_STAND` | a1: int, a2: float, a3: float | `void` |
| `VEHICLE.SET_VEHICLE_NOT_STEALABLE_AMBIENTLY` | a: int, b: bool | `void` |
| `VEHICLE.LOCK_DOORS_WHEN_NO_LONGER_NEEDED` | a: int | `void` |
| `VEHICLE.SET_LAST_DRIVEN_VEHICLE` | a: int | `void` |
| `VEHICLE.GET_LAST_DRIVEN_VEHICLE` | — | `int` |
| `VEHICLE.CLEAR_LAST_DRIVEN_VEHICLE` | — | `void` |
| `VEHICLE.SET_VEHICLE_HAS_BEEN_DRIVEN_FLAG` | a: int, b: bool | `void` |
| `VEHICLE.SET_TASK_VEHICLE_GOTO_PLANE_MIN_HEIGHT_ABOVE_TERRAIN` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_LOD_MULTIPLIER` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_CAN_SAVE_IN_GARAGE` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_NUM_OF_BROKEN_OFF_PARTS` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_NUM_OF_BROKEN_LOOSEN_PARTS` | a: int | `int` |
| `VEHICLE.SET_FORCE_VEHICLE_ENGINE_DAMAGE_BY_BULLET` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_GENERATES_ENGINE_SHOCKING_EVENTS` | a: int, b: bool | `void` |
| `VEHICLE.COPY_VEHICLE_DAMAGES` | a: int, b: int | `void` |
| `VEHICLE.DISABLE_VEHICLE_EXPLOSION_BREAK_OFF_PARTS` | — | `void` |
| `VEHICLE.SET_LIGHTS_CUTOFF_DISTANCE_TWEAK` | a: float | `void` |
| `VEHICLE.SET_VEHICLE_SHOOT_AT_TARGET` | a1: int, a2: int, a3: float, a4: float, a5: float | `void` |
| `VEHICLE.GET_VEHICLE_LOCK_ON_TARGET` | a: int, b: pointer | `bool` |
| `VEHICLE.SET_FORCE_HD_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_CUSTOM_PATH_NODE_STREAMING_RADIUS` | a: int, b: float | `void` |
| `VEHICLE.GET_VEHICLE_PLATE_TYPE` | a: int | `int` |
| `VEHICLE.TRACK_VEHICLE_VISIBILITY` | a: int | `void` |
| `VEHICLE.IS_VEHICLE_VISIBLE` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_GRAVITY` | a: int, b: bool | `void` |
| `VEHICLE.SET_ENABLE_VEHICLE_SLIPSTREAMING` | a: bool | `void` |
| `VEHICLE.SET_VEHICLE_SLIPSTREAMING_SHOULD_TIME_OUT` | a: bool | `void` |
| `VEHICLE.GET_VEHICLE_CURRENT_TIME_IN_SLIP_STREAM` | a: int | `float` |
| `VEHICLE.IS_VEHICLE_PRODUCING_SLIP_STREAM` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_INACTIVE_DURING_PLAYBACK` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_ACTIVE_DURING_PLAYBACK` | a: int, b: bool | `void` |
| `VEHICLE.IS_VEHICLE_SPRAYABLE` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_ENGINE_CAN_DEGRADE` | a: int, b: bool | `void` |
| `VEHICLE.DISABLE_VEHCILE_DYNAMIC_AMBIENT_SCALES` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.ENABLE_VEHICLE_DYNAMIC_AMBIENT_SCALES` | a: int | `void` |
| `VEHICLE.IS_PLANE_LANDING_GEAR_INTACT` | a: int | `bool` |
| `VEHICLE.ARE_PLANE_PROPELLERS_INTACT` | a: int | `bool` |
| `VEHICLE.SET_PLANE_PROPELLER_HEALTH` | a: int, b: float | `bool` |
| `VEHICLE.SET_VEHICLE_CAN_DEFORM_WHEELS` | a: int, b: bool | `void` |
| `VEHICLE.IS_VEHICLE_STOLEN` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_IS_STOLEN` | a: int, b: bool | `void` |
| `VEHICLE.SET_PLANE_TURBULENCE_MULTIPLIER` | a: int, b: float | `void` |
| `VEHICLE.ARE_WINGS_OF_PLANE_INTACT` | a: int | `bool` |
| `VEHICLE.ALLOW_AMBIENT_VEHICLES_TO_AVOID_ADVERSE_CONDITIONS` | a: int | `void` |
| `VEHICLE.DETACH_VEHICLE_FROM_CARGOBOB` | a: int, b: int | `void` |
| `VEHICLE.DETACH_VEHICLE_FROM_ANY_CARGOBOB` | a: int | `bool` |
| `VEHICLE.DETACH_ENTITY_FROM_CARGOBOB` | a: int, b: int | `bool` |
| `VEHICLE.IS_VEHICLE_ATTACHED_TO_CARGOBOB` | a: int, b: int | `bool` |
| `VEHICLE.GET_VEHICLE_ATTACHED_TO_CARGOBOB` | a: int | `int` |
| `VEHICLE.GET_ENTITY_ATTACHED_TO_CARGOBOB` | a: int | `int` |
| `VEHICLE.ATTACH_VEHICLE_TO_CARGOBOB` | a1: int, a2: int, a3: int, a4: float, a5: float, a6: float | `void` |
| `VEHICLE.ATTACH_ENTITY_TO_CARGOBOB` | a1: int, a2: int, a3: int, a4: int, a5: int, a6: int | `void` |
| `VEHICLE.SET_CARGOBOB_FORCE_DONT_DETACH_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE.SET_CARGOBOB_EXCLUDE_FROM_PICKUP_ENTITY` | a: int, b: int | `void` |
| `VEHICLE.CAN_CARGOBOB_PICK_UP_ENTITY` | a: int, b: int | `bool` |
| `VEHICLE.GET_ATTACHED_PICK_UP_HOOK_POSITION` | a: int | `vector3` |
| `VEHICLE.DOES_CARGOBOB_HAVE_PICK_UP_ROPE` | a: int | `bool` |
| `VEHICLE.CREATE_PICK_UP_ROPE_FOR_CARGOBOB` | a: int, b: int | `void` |
| `VEHICLE.REMOVE_PICK_UP_ROPE_FOR_CARGOBOB` | a: int | `void` |
| `VEHICLE.SET_PICKUP_ROPE_LENGTH_FOR_CARGOBOB` | a1: int, a2: float, a3: float, a4: bool | `void` |
| `VEHICLE.SET_PICKUP_ROPE_LENGTH_WITHOUT_CREATING_ROPE_FOR_CARGOBOB` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_ROPE_DAMPING_MULTIPLIER` | a: int, b: int | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_ROPE_TYPE` | a: int, b: int | `void` |
| `VEHICLE.DOES_CARGOBOB_HAVE_PICKUP_MAGNET` | a: int | `bool` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_ACTIVE` | a: int, b: bool | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_STRENGTH` | a: int, b: float | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_FALLOFF` | a: int, b: float | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_REDUCED_STRENGTH` | a: int, b: float | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_REDUCED_FALLOFF` | a: int, b: float | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_PULL_STRENGTH` | a: int, b: float | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_PULL_ROPE_LENGTH` | a: int, b: float | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_SET_TARGETED_MODE` | a: int, b: int | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_SET_AMBIENT_MODE` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE.SET_CARGOBOB_PICKUP_MAGNET_ENSURE_PICKUP_ENTITY_UPRIGHT` | a: int, b: bool | `void` |
| `VEHICLE.DOES_VEHICLE_HAVE_WEAPONS` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_WILL_TELL_OTHERS_TO_HURRY` | a: int, b: bool | `void` |
| `VEHICLE.DISABLE_VEHICLE_WEAPON` | a1: bool, a2: hash, a3: int, a4: int | `void` |
| `VEHICLE.IS_VEHICLE_WEAPON_DISABLED` | a1: hash, a2: int, a3: int | `bool` |
| `VEHICLE.SET_VEHICLE_USED_FOR_PILOT_SCHOOL` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_ACTIVE_FOR_PED_NAVIGATION` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_CLASS` | a: int | `int` |
| `VEHICLE.GET_VEHICLE_CLASS_FROM_NAME` | a: hash | `int` |
| `VEHICLE.SET_PLAYERS_LAST_VEHICLE` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_CAN_BE_USED_BY_FLEEING_PEDS` | a: int, b: bool | `void` |
| `VEHICLE.SET_AIRCRAFT_PILOT_SKILL_NOISE_SCALAR` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_DROPS_MONEY_WHEN_BLOWN_UP` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_KEEP_ENGINE_ON_WHEN_ABANDONED` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_IMPATIENCE_TIMER` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_HANDLING_OVERRIDE` | a: int, b: hash | `void` |
| `VEHICLE.SET_VEHICLE_EXTENDED_REMOVAL_RANGE` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_STEERING_BIAS_SCALAR` | a: int, b: float | `void` |
| `VEHICLE.SET_HELI_CONTROL_LAGGING_RATE_SCALAR` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_FRICTION_OVERRIDE` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_WHEELS_CAN_BREAK_OFF_WHEN_BLOW_UP` | a: int, b: bool | `void` |
| `VEHICLE.ARE_PLANE_CONTROL_PANELS_INTACT` | a: int, b: bool | `bool` |
| `VEHICLE.SET_VEHICLE_CEILING_HEIGHT` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_NO_EXPLOSION_DAMAGE_FROM_DRIVER` | a: int, b: bool | `void` |
| `VEHICLE.CLEAR_VEHICLE_ROUTE_HISTORY` | a: int | `void` |
| `VEHICLE.DOES_VEHICLE_EXIST_WITH_DECORATOR` | a: string | `int` |
| `VEHICLE.SET_VEHICLE_AI_CAN_USE_EXCLUSIVE_SEATS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_EXCLUSIVE_DRIVER` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.IS_PED_EXCLUSIVE_DRIVER_OF_VEHICLE` | a1: int, a2: int, a3: pointer | `bool` |
| `VEHICLE.DISABLE_INDIVIDUAL_PLANE_PROPELLER` | a: int, b: int | `void` |
| `VEHICLE._ENABLE_INDIVIDUAL_PLANE_PROPELLER` | a: int, b: int | `void` |
| `VEHICLE.SET_VEHICLE_FORCE_AFTERBURNER` | a: int, b: bool | `void` |
| `VEHICLE.SET_DONT_PROCESS_VEHICLE_GLASS` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_WANTED_CONES_RESPONSE` | a: int, b: bool | `void` |
| `VEHICLE.SET_USE_DESIRED_Z_CRUISE_SPEED_FOR_LANDING` | a: int, b: bool | `void` |
| `VEHICLE.SET_ARRIVE_DISTANCE_OVERRIDE_FOR_VEHICLE_PERSUIT_ATTACK` | a: int, b: float | `void` |
| `VEHICLE.SET_VEHICLE_READY_FOR_CLEANUP` | a: int | `void` |
| `VEHICLE.SET_DISTANT_CARS_ENABLED` | a: bool | `void` |
| `VEHICLE.SET_VEHICLE_NEON_COLOUR` | a1: int, a2: int, a3: int, a4: int | `void` |
| `VEHICLE.SET_VEHICLE_NEON_INDEX_COLOUR` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_NEON_COLOUR` | a1: int, a2: pointer, a3: pointer, a4: pointer | `void` |
| `VEHICLE.SET_VEHICLE_NEON_ENABLED` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.GET_VEHICLE_NEON_ENABLED` | a: int, b: int | `bool` |
| `VEHICLE.SET_AMBIENT_VEHICLE_NEON_ENABLED` | a: bool | `void` |
| `VEHICLE.SUPPRESS_NEONS_ON_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_SUPERDUMMY` | a: int, b: bool | `void` |
| `VEHICLE.REQUEST_VEHICLE_DIAL` | a: int | `void` |
| `VEHICLE.GET_VEHICLE_BODY_HEALTH` | a: int | `float` |
| `VEHICLE.SET_VEHICLE_BODY_HEALTH` | a: int, b: float | `void` |
| `VEHICLE.GET_VEHICLE_SIZE` | a1: int, a2: vector3, a3: vector3 | `void` |
| `VEHICLE.GET_FAKE_SUSPENSION_LOWERING_AMOUNT` | a: int | `float` |
| `VEHICLE.SET_CAR_HIGH_SPEED_BUMP_SEVERITY_MULTIPLIER` | a: float | `void` |
| `VEHICLE.GET_NUMBER_OF_VEHICLE_DOORS` | a: int | `int` |
| `VEHICLE.SET_HYDRAULICS_CONTROL` | a: int, b: bool | `void` |
| `VEHICLE.SET_CAN_ADJUST_GROUND_CLEARANCE` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_HEALTH_PERCENTAGE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float | `float` |
| `VEHICLE.GET_VEHICLE_IS_MERCENARY` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_BROKEN_PARTS_DONT_AFFECT_AI_HANDLING` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_KERS_ALLOWED` | a: int, b: bool | `void` |
| `VEHICLE.GET_VEHICLE_HAS_KERS` | a: int | `bool` |
| `VEHICLE.SET_PLANE_RESIST_TO_EXPLOSION` | a: int, b: bool | `void` |
| `VEHICLE.SET_HELI_RESIST_TO_EXPLOSION` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_BMX_EXTRA_TRICK_FORCES` | a: int | `void` |
| `VEHICLE.SET_HYDRAULIC_SUSPENSION_RAISE_FACTOR` | a1: int, a2: int, a3: float | `void` |
| `VEHICLE.GET_HYDRAULIC_SUSPENSION_RAISE_FACTOR` | a: int, b: int | `float` |
| `VEHICLE.SET_CAN_USE_HYDRAULICS` | a: int, b: bool | `void` |
| `VEHICLE.SET_HYDRAULIC_VEHICLE_STATE` | a: int, b: int | `void` |
| `VEHICLE.SET_HYDRAULIC_WHEEL_STATE` | a1: int, a2: int, a3: int, a4: float, a5: int | `void` |
| `VEHICLE.HAS_VEHICLE_PETROLTANK_SET_ON_FIRE_BY_ENTITY` | a: int, b: int | `bool` |
| `VEHICLE.CLEAR_VEHICLE_PETROLTANK_FIRE_CULPRIT` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_BOBBLEHEAD_VELOCITY` | a1: float, a2: float, a3: float | `void` |
| `VEHICLE.GET_VEHICLE_IS_DUMMY` | a: int | `bool` |
| `VEHICLE.SET_VEHICLE_DAMAGE_SCALE` | a: int, b: float | `bool` |
| `VEHICLE._SET_VEHICLE_EXPLOSIVE_DAMAGE_SCALE` | a: int, b: float | `int` |
| `VEHICLE.SET_VEHICLE_WEAPON_DAMAGE_SCALE` | a: int, b: float | `bool` |
| `VEHICLE.SET_DISABLE_DAMAGE_WITH_PICKED_UP_ENTITY` | a: int, b: int | `bool` |
| `VEHICLE.SET_VEHICLE_USES_MP_PLAYER_DAMAGE_MULTIPLIER` | a: int, b: int | `void` |
| `VEHICLE.SET_BIKE_EASY_TO_LAND` | a: int, b: bool | `void` |
| `VEHICLE.SET_INVERT_VEHICLE_CONTROLS` | a: int, b: bool | `void` |
| `VEHICLE.SET_SPEED_BOOST_EFFECT_DISABLED` | a: bool | `void` |
| `VEHICLE.SET_SLOW_DOWN_EFFECT_DISABLED` | a: bool | `void` |
| `VEHICLE.SET_FORMATION_LEADER` | a1: int, a2: float, a3: float, a4: float, a5: float | `void` |
| `VEHICLE.RESET_FORMATION_LEADER` | — | `void` |
| `VEHICLE.GET_IS_BOAT_CAPSIZED` | a: int | `bool` |
| `VEHICLE.SET_ALLOW_RAMMING_SOOP_OR_RAMP` | a: int, b: int | `void` |
| `VEHICLE.SET_SCRIPT_RAMP_IMPULSE_SCALE` | a: int, b: float | `void` |
| `VEHICLE.GET_IS_DOOR_VALID` | a: int, b: int | `bool` |
| `VEHICLE.SET_SCRIPT_ROCKET_BOOST_RECHARGE_TIME` | a: int, b: float | `void` |
| `VEHICLE.IS_ROCKET_BOOST_ACTIVE` | a: int | `bool` |
| `VEHICLE.SET_ROCKET_BOOST_ACTIVE` | a: int, b: bool | `void` |
| `VEHICLE.GET_HAS_RETRACTABLE_WHEELS` | a: int | `bool` |
| `VEHICLE.GET_IS_WHEELS_RETRACTED` | a: int | `bool` |
| `VEHICLE.SET_WHEELS_EXTENDED_INSTANTLY` | a: int | `void` |
| `VEHICLE.SET_WHEELS_RETRACTED_INSTANTLY` | a: int | `void` |
| `VEHICLE.GET_CAR_HAS_JUMP` | a: int | `bool` |
| `VEHICLE.SET_USE_HIGHER_CAR_JUMP` | a: int, b: bool | `void` |
| `VEHICLE.SET_CLEAR_FREEZE_WAITING_ON_COLLISION_ONCE_PLAYER_ENTERS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_WEAPON_RESTRICTED_AMMO` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.GET_VEHICLE_WEAPON_RESTRICTED_AMMO` | a: int, b: int | `int` |
| `VEHICLE.GET_VEHICLE_HAS_PARACHUTE` | a: int | `bool` |
| `VEHICLE.GET_VEHICLE_CAN_DEPLOY_PARACHUTE` | a: int | `bool` |
| `VEHICLE.VEHICLE_START_PARACHUTING` | a: int, b: bool | `void` |
| `VEHICLE.IS_VEHICLE_PARACHUTE_DEPLOYED` | a: int | `bool` |
| `VEHICLE.VEHICLE_SET_RAMP_AND_RAMMING_CARS_TAKE_DAMAGE` | a: int, b: bool | `void` |
| `VEHICLE.VEHICLE_SET_ENABLE_RAMP_CAR_SIDE_IMPULSE` | a: int, b: int | `void` |
| `VEHICLE.VEHICLE_SET_ENABLE_NORMALISE_RAMP_CAR_VERTICAL_VELOCTIY` | a: int, b: int | `void` |
| `VEHICLE.VEHICLE_SET_JET_WASH_FORCE_ENABLED` | a: int | `void` |
| `VEHICLE.SET_VEHICLE_WEAPON_CAN_TARGET_OBJECTS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_USE_BOOST_BUTTON_FOR_WHEEL_RETRACT` | a: bool | `void` |
| `VEHICLE._SET_VEHICLE_USE_HORN_BUTTON_FOR_NITROUS` | a: bool | `void` |
| `VEHICLE.VEHICLE_SET_PARACHUTE_MODEL_OVERRIDE` | a: int, b: hash | `void` |
| `VEHICLE.VEHICLE_SET_PARACHUTE_MODEL_TINT_INDEX` | a: int, b: int | `void` |
| `VEHICLE.VEHICLE_SET_OVERRIDE_EXTENABLE_SIDE_RATIO` | a: int, b: int | `int` |
| `VEHICLE.VEHICLE_SET_EXTENABLE_SIDE_TARGET_RATIO` | a: int, b: int | `int` |
| `VEHICLE.VEHICLE_SET_OVERRIDE_SIDE_RATIO` | a: int, b: int | `int` |
| `VEHICLE.GET_ALL_VEHICLES` | a: pointer | `int` |
| `VEHICLE.SET_CARGOBOB_EXTA_PICKUP_RANGE` | a: int, b: int | `void` |
| `VEHICLE.SET_OVERRIDE_VEHICLE_DOOR_TORQUE` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.SET_WHEELIE_ENABLED` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_HELI_EXPLODE_FROM_BODY_DAMAGE` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_EXPLODE_FROM_BODY_DAMAGE_ON_COLLISION` | a: int, b: bool | `void` |
| `VEHICLE._SET_DISABLE_EXPLODE_FROM_BODY_DAMAGE_RECEIVED_BY_AI_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE.SET_TRAILER_ATTACHMENT_ENABLED` | a: int, b: int | `void` |
| `VEHICLE.SET_ROCKET_BOOST_FILL` | a: int, b: float | `void` |
| `VEHICLE.SET_GLIDER_ACTIVE` | a: int, b: bool | `void` |
| `VEHICLE.SET_SHOULD_RESET_TURRET_IN_SCRIPTED_CAMERAS` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DISABLE_COLLISION_UPON_CREATION` | a: int, b: bool | `void` |
| `VEHICLE.SET_GROUND_EFFECT_REDUCES_DRAG` | a: bool | `void` |
| `VEHICLE.SET_DISABLE_MAP_COLLISION` | a: int | `void` |
| `VEHICLE.SET_DISABLE_PED_STAND_ON_TOP` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_DAMAGE_SCALES` | a1: int, a2: int, a3: int, a4: int, a5: int | `void` |
| `VEHICLE.SET_PLANE_SECTION_DAMAGE_SCALE` | a1: int, a2: int, a3: int | `void` |
| `VEHICLE.SET_PLANE_CONTROL_SECTIONS_SHOULD_BREAK_OFF_FROM_EXPLOSIONS` | a: int, b: bool | `void` |
| `VEHICLE.SET_HELI_CAN_PICKUP_ENTITY_THAT_HAS_PICK_UP_DISABLED` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_BOMB_AMMO` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_BOMB_AMMO` | a: int | `int` |
| `VEHICLE.SET_VEHICLE_COUNTERMEASURE_AMMO` | a: int, b: int | `void` |
| `VEHICLE.GET_VEHICLE_COUNTERMEASURE_AMMO` | a: int | `int` |
| `VEHICLE.SET_HELI_COMBAT_OFFSET` | a1: int, a2: float, a3: float, a4: float | `void` |
| `VEHICLE.GET_CAN_VEHICLE_BE_PLACED_HERE` | a1: int, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: int, a9: int | `bool` |
| `VEHICLE.SET_DISABLE_AUTOMATIC_CRASH_TASK` | a: int, b: bool | `void` |
| `VEHICLE.SET_SPECIAL_FLIGHT_MODE_RATIO` | a: int, b: float | `void` |
| `VEHICLE.SET_SPECIAL_FLIGHT_MODE_TARGET_RATIO` | a: int, b: float | `void` |
| `VEHICLE.SET_SPECIAL_FLIGHT_MODE_ALLOWED` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_HOVER_MODE_FLIGHT` | a: int, b: bool | `void` |
| `VEHICLE.GET_OUTRIGGERS_DEPLOYED` | a: int | `bool` |
| `VEHICLE.FIND_SPAWN_COORDINATES_FOR_HELI` | a: int | `vector3` |
| `VEHICLE.SET_DEPLOY_FOLDING_WINGS` | a1: int, a2: bool, a3: bool | `void` |
| `VEHICLE.ARE_FOLDING_WINGS_DEPLOYED` | a: int | `bool` |
| `VEHICLE._SET_DEPLOY_MISSILE_BAYS` | a: int, b: bool | `void` |
| `VEHICLE._ARE_MISSILE_BAYS_DEPLOYED` | a: int | `bool` |
| `VEHICLE.SET_DIP_STRAIGHT_DOWN_WHEN_CRASHING_PLANE` | a: int, b: bool | `void` |
| `VEHICLE.SET_TURRET_HIDDEN` | a1: int, a2: int, a3: bool | `void` |
| `VEHICLE.SET_HOVER_MODE_WING_RATIO` | a: int, b: float | `void` |
| `VEHICLE.SET_DISABLE_TURRET_MOVEMENT` | a: int, b: int | `void` |
| `VEHICLE.SET_FORCE_FIX_LINK_MATRICES` | a: int | `void` |
| `VEHICLE.SET_TRANSFORM_RATE_FOR_ANIMATION` | a: int, b: float | `void` |
| `VEHICLE.SET_TRANSFORM_TO_SUBMARINE_USES_ALTERNATE_INPUT` | a: int, b: bool | `void` |
| `VEHICLE.SET_VEHICLE_COMBAT_MODE` | a: bool | `void` |
| `VEHICLE.SET_VEHICLE_DETONATION_MODE` | a: bool | `void` |
| `VEHICLE.SET_VEHICLE_SHUNT_ON_STICK` | a: bool | `void` |
| `VEHICLE._IS_VEHICLE_ON_BOOST_PAD` | a: int | `bool` |
| `VEHICLE.GET_IS_VEHICLE_SHUNTING` | a: int | `bool` |
| `VEHICLE.GET_HAS_VEHICLE_BEEN_HIT_BY_SHUNT` | a: int | `bool` |
| `VEHICLE.GET_LAST_SHUNT_VEHICLE` | a: int | `int` |
| `VEHICLE.SET_DISABLE_VEHICLE_EXPLOSIONS_DAMAGE` | a: bool | `void` |
| `VEHICLE.SET_OVERRIDE_NITROUS_LEVEL` | a1: int, a2: bool, a3: float, a4: float, a5: float, a6: bool | `void` |
| `VEHICLE._SET_NITROUS_IS_VISIBLE` | a: int, b: bool | `void` |
| `VEHICLE._SET_OVERRIDE_TRACTION_LOSS_MULTIPLIER` | a: int, b: float | `void` |
| `VEHICLE._SET_DRIFT_SLIP_ANGLE_LIMITS` | a1: int, a2: float, a3: float, a4: float | `void` |
| `VEHICLE._SET_MINIMUM_TIME_BETWEEN_GEAR_SHIFTS` | a: int, b: int | `void` |
| `VEHICLE.FULLY_CHARGE_NITROUS` | a: int | `void` |
| `VEHICLE._SET_REMAINING_NITROUS_DURATION` | a: int, b: float | `void` |
| `VEHICLE._GET_REMAINING_NITROUS_DURATION` | a: int | `float` |
| `VEHICLE.IS_NITROUS_ACTIVE` | a: int | `bool` |
| `VEHICLE.CLEAR_NITROUS` | a: int | `void` |
| `VEHICLE.SET_NITROUS_IS_ACTIVE` | a: int, b: bool | `void` |
| `VEHICLE.SET_INCREASE_WHEEL_CRUSH_DAMAGE` | a: int, b: bool | `void` |
| `VEHICLE.SET_DISABLE_WEAPON_BLADE_FORCES` | a: bool | `void` |
| `VEHICLE.SET_USE_DOUBLE_CLICK_FOR_CAR_JUMP` | a: bool | `void` |
| `VEHICLE.GET_DOES_VEHICLE_HAVE_TOMBSTONE` | a: int | `bool` |
| `VEHICLE.HIDE_TOMBSTONE` | a: int, b: bool | `void` |
| `VEHICLE.APPLY_EMP_EFFECT` | a: int | `void` |
| `VEHICLE.GET_IS_VEHICLE_DISABLED_BY_EMP` | a: int | `bool` |
| `VEHICLE.SET_DISABLE_RETRACTING_WEAPON_BLADES` | a: bool | `void` |
| `VEHICLE.GET_TYRE_HEALTH` | a: int, b: int | `float` |
| `VEHICLE.SET_TYRE_HEALTH` | a1: int, a2: int, a3: float | `void` |
| `VEHICLE.GET_TYRE_WEAR_RATE` | a: int, b: int | `float` |
| `VEHICLE.SET_TYRE_WEAR_RATE` | a1: int, a2: int, a3: float | `void` |
| `VEHICLE.SET_TYRE_WEAR_RATE_SCALE` | a1: int, a2: int, a3: float | `void` |
| `VEHICLE.SET_TYRE_MAXIMUM_GRIP_DIFFERENCE_DUE_TO_WEAR_RATE` | a1: int, a2: int, a3: float | `void` |
| `VEHICLE.SET_AIRCRAFT_IGNORE_HIGHTMAP_OPTIMISATION` | a: int, b: int | `void` |
| `VEHICLE.SET_REDUCED_SUSPENSION_FORCE` | a: int, b: bool | `void` |
| `VEHICLE.SET_DRIFT_TYRES` | a: int, b: bool | `void` |
| `VEHICLE.GET_DRIFT_TYRES_SET` | a: int | `bool` |
| `VEHICLE.NETWORK_USE_HIGH_PRECISION_TRAIN_BLENDING` | a: int, b: bool | `void` |
| `VEHICLE.SET_CHECK_FOR_ENOUGH_ROOM_FOR_PED` | a: int, b: bool | `void` |
| `VEHICLE._SET_ALLOW_COLLISION_WHEN_IN_VEHICLE` | a: int, b: bool | `void` |
| `VEHICLE._GET_VEHICLE_MAX_EXHAUST_BONE_COUNT` | — | `int` |
| `VEHICLE._GET_VEHICLE_EXHAUST_BONE` | a1: int, a2: int, a3: pointer, a4: pointer | `bool` |

## WATER

| Native | Params | Returns |
| --- | --- | --- |
| `WATER.GET_WATER_HEIGHT` | a1: float, a2: float, a3: float, a4: pointer | `bool` |
| `WATER.GET_WATER_HEIGHT_NO_WAVES` | a1: float, a2: float, a3: float, a4: pointer | `bool` |
| `WATER.TEST_PROBE_AGAINST_WATER` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: vector3 | `bool` |
| `WATER.TEST_PROBE_AGAINST_ALL_WATER` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: int, a8: pointer | `int` |
| `WATER.TEST_VERTICAL_PROBE_AGAINST_ALL_WATER` | a1: float, a2: float, a3: float, a4: int, a5: pointer | `int` |
| `WATER.MODIFY_WATER` | a1: float, a2: float, a3: float, a4: float | `void` |
| `WATER.ADD_EXTRA_CALMING_QUAD` | a1: float, a2: float, a3: float, a4: float, a5: float | `int` |
| `WATER.REMOVE_EXTRA_CALMING_QUAD` | a: int | `void` |
| `WATER.SET_DEEP_OCEAN_SCALER` | a: float | `void` |
| `WATER.GET_DEEP_OCEAN_SCALER` | — | `float` |
| `WATER.SET_CALMED_WAVE_HEIGHT_SCALER` | a: float | `void` |
| `WATER.RESET_DEEP_OCEAN_SCALER` | — | `void` |

## WEAPON

| Native | Params | Returns |
| --- | --- | --- |
| `WEAPON.ENABLE_LASER_SIGHT_RENDERING` | a: bool | `void` |
| `WEAPON.GET_WEAPON_COMPONENT_TYPE_MODEL` | a: hash | `hash` |
| `WEAPON.GET_WEAPONTYPE_MODEL` | a: hash | `hash` |
| `WEAPON.GET_WEAPONTYPE_SLOT` | a: hash | `hash` |
| `WEAPON.GET_WEAPONTYPE_GROUP` | a: hash | `hash` |
| `WEAPON.GET_WEAPON_COMPONENT_VARIANT_EXTRA_COUNT` | a: hash | `int` |
| `WEAPON.GET_WEAPON_COMPONENT_VARIANT_EXTRA_MODEL` | a: hash, b: int | `hash` |
| `WEAPON.SET_CURRENT_PED_WEAPON` | a1: int, a2: hash, a3: bool | `void` |
| `WEAPON.GET_CURRENT_PED_WEAPON` | a1: int, a2: pointer, a3: bool | `bool` |
| `WEAPON.GET_CURRENT_PED_WEAPON_ENTITY_INDEX` | a: int, b: bool | `int` |
| `WEAPON.GET_BEST_PED_WEAPON` | a: int, b: bool | `hash` |
| `WEAPON.SET_CURRENT_PED_VEHICLE_WEAPON` | a: int, b: hash | `bool` |
| `WEAPON.GET_CURRENT_PED_VEHICLE_WEAPON` | a: int, b: pointer | `bool` |
| `WEAPON.SET_PED_CYCLE_VEHICLE_WEAPONS_ONLY` | a: int | `void` |
| `WEAPON.IS_PED_ARMED` | a: int, b: int | `bool` |
| `WEAPON.IS_WEAPON_VALID` | a: hash | `bool` |
| `WEAPON.HAS_PED_GOT_WEAPON` | a1: int, a2: hash, a3: bool | `bool` |
| `WEAPON.IS_PED_WEAPON_READY_TO_SHOOT` | a: int | `bool` |
| `WEAPON.GET_PED_WEAPONTYPE_IN_SLOT` | a: int, b: hash | `hash` |
| `WEAPON.GET_AMMO_IN_PED_WEAPON` | a: int, b: hash | `int` |
| `WEAPON.ADD_AMMO_TO_PED` | a1: int, a2: hash, a3: int | `void` |
| `WEAPON.SET_PED_AMMO` | a1: int, a2: hash, a3: int, a4: bool | `void` |
| `WEAPON.SET_PED_INFINITE_AMMO` | a1: int, a2: bool, a3: hash | `void` |
| `WEAPON.SET_PED_INFINITE_AMMO_CLIP` | a: int, b: bool | `void` |
| `WEAPON.SET_PED_STUN_GUN_FINITE_AMMO` | a: int, b: int | `void` |
| `WEAPON.GIVE_WEAPON_TO_PED` | a1: int, a2: hash, a3: int, a4: bool, a5: bool | `void` |
| `WEAPON.GIVE_DELAYED_WEAPON_TO_PED` | a1: int, a2: hash, a3: int, a4: bool | `void` |
| `WEAPON.REMOVE_ALL_PED_WEAPONS` | a: int, b: bool | `void` |
| `WEAPON.REMOVE_WEAPON_FROM_PED` | a: int, b: hash | `void` |
| `WEAPON.HIDE_PED_WEAPON_FOR_SCRIPTED_CUTSCENE` | a: int, b: bool | `void` |
| `WEAPON.SET_PED_CURRENT_WEAPON_VISIBLE` | a1: int, a2: bool, a3: bool, a4: bool, a5: bool | `void` |
| `WEAPON.SET_PED_DROPS_WEAPONS_WHEN_DEAD` | a: int, b: bool | `void` |
| `WEAPON.HAS_PED_BEEN_DAMAGED_BY_WEAPON` | a1: int, a2: hash, a3: int | `bool` |
| `WEAPON.CLEAR_PED_LAST_WEAPON_DAMAGE` | a: int | `void` |
| `WEAPON.HAS_ENTITY_BEEN_DAMAGED_BY_WEAPON` | a1: int, a2: hash, a3: int | `bool` |
| `WEAPON.CLEAR_ENTITY_LAST_WEAPON_DAMAGE` | a: int | `void` |
| `WEAPON.SET_PED_DROPS_WEAPON` | a: int | `void` |
| `WEAPON.SET_PED_DROPS_INVENTORY_WEAPON` | a1: int, a2: hash, a3: float, a4: float, a5: float, a6: int | `void` |
| `WEAPON.GET_MAX_AMMO_IN_CLIP` | a1: int, a2: hash, a3: bool | `int` |
| `WEAPON._GET_TIME_BEFORE_VEHICLE_WEAPON_RELOAD_FINISHES` | a: int, b: int | `int` |
| `WEAPON._HAS_WEAPON_RELOADING_IN_VEHICLE` | a: int, b: int | `bool` |
| `WEAPON._GET_VEHICLE_WEAPON_RELOAD_TIME` | a: int, b: int | `float` |
| `WEAPON._GET_AMMO_IN_VEHICLE_WEAPON_CLIP` | a1: int, a2: int, a3: pointer | `bool` |
| `WEAPON.GET_AMMO_IN_CLIP` | a1: int, a2: hash, a3: pointer | `bool` |
| `WEAPON._SET_AMMO_IN_VEHICLE_WEAPON_CLIP` | a1: int, a2: int, a3: int | `bool` |
| `WEAPON.SET_AMMO_IN_CLIP` | a1: int, a2: hash, a3: int | `bool` |
| `WEAPON.GET_MAX_AMMO` | a1: int, a2: hash, a3: pointer | `bool` |
| `WEAPON.GET_MAX_AMMO_BY_TYPE` | a1: int, a2: hash, a3: pointer | `bool` |
| `WEAPON.ADD_PED_AMMO_BY_TYPE` | a1: int, a2: hash, a3: int | `void` |
| `WEAPON.SET_PED_AMMO_BY_TYPE` | a1: int, a2: hash, a3: int | `void` |
| `WEAPON.GET_PED_AMMO_BY_TYPE` | a: int, b: hash | `int` |
| `WEAPON.SET_PED_AMMO_TO_DROP` | a: int, b: int | `void` |
| `WEAPON.SET_PICKUP_AMMO_AMOUNT_SCALER` | a: float | `void` |
| `WEAPON.GET_PED_AMMO_TYPE_FROM_WEAPON` | a: int, b: hash | `hash` |
| `WEAPON.GET_PED_ORIGINAL_AMMO_TYPE_FROM_WEAPON` | a: int, b: hash | `hash` |
| `WEAPON.GET_PED_LAST_WEAPON_IMPACT_COORD` | a: int, b: vector3 | `bool` |
| `WEAPON.SET_PED_GADGET` | a1: int, a2: hash, a3: bool | `void` |
| `WEAPON.GET_IS_PED_GADGET_EQUIPPED` | a: int, b: hash | `bool` |
| `WEAPON.GET_SELECTED_PED_WEAPON` | a: int | `hash` |
| `WEAPON.EXPLODE_PROJECTILES` | a1: int, a2: hash, a3: bool | `void` |
| `WEAPON.REMOVE_ALL_PROJECTILES_OF_TYPE` | a: hash, b: bool | `void` |
| `WEAPON.GET_LOCKON_DISTANCE_OF_CURRENT_PED_WEAPON` | a: int | `float` |
| `WEAPON.GET_MAX_RANGE_OF_CURRENT_PED_WEAPON` | a: int | `float` |
| `WEAPON.HAS_VEHICLE_GOT_PROJECTILE_ATTACHED` | a1: int, a2: int, a3: hash, a4: int | `bool` |
| `WEAPON.GIVE_WEAPON_COMPONENT_TO_PED` | a1: int, a2: hash, a3: hash | `void` |
| `WEAPON.REMOVE_WEAPON_COMPONENT_FROM_PED` | a1: int, a2: hash, a3: hash | `void` |
| `WEAPON.HAS_PED_GOT_WEAPON_COMPONENT` | a1: int, a2: hash, a3: hash | `bool` |
| `WEAPON.IS_PED_WEAPON_COMPONENT_ACTIVE` | a1: int, a2: hash, a3: hash | `bool` |
| `WEAPON.REFILL_AMMO_INSTANTLY` | a: int | `bool` |
| `WEAPON.MAKE_PED_RELOAD` | a: int | `bool` |
| `WEAPON._TRIGGER_VEHICLE_WEAPON_RELOAD` | a1: int, a2: int, a3: int | `bool` |
| `WEAPON.REQUEST_WEAPON_ASSET` | a1: hash, a2: int, a3: int | `void` |
| `WEAPON.HAS_WEAPON_ASSET_LOADED` | a: hash | `bool` |
| `WEAPON.REMOVE_WEAPON_ASSET` | a: hash | `void` |
| `WEAPON.CREATE_WEAPON_OBJECT` | a1: hash, a2: int, a3: float, a4: float, a5: float, a6: bool, a7: float, a8: int, a9: int, a10: int | `int` |
| `WEAPON.GIVE_WEAPON_COMPONENT_TO_WEAPON_OBJECT` | a: int, b: hash | `void` |
| `WEAPON.REMOVE_WEAPON_COMPONENT_FROM_WEAPON_OBJECT` | a: int, b: hash | `void` |
| `WEAPON.HAS_WEAPON_GOT_WEAPON_COMPONENT` | a: int, b: hash | `bool` |
| `WEAPON.GIVE_WEAPON_OBJECT_TO_PED` | a: int, b: int | `void` |
| `WEAPON.DOES_WEAPON_TAKE_WEAPON_COMPONENT` | a: hash, b: hash | `bool` |
| `WEAPON.GET_WEAPON_OBJECT_FROM_PED` | a: int, b: bool | `int` |
| `WEAPON.GIVE_LOADOUT_TO_PED` | a: int, b: hash | `void` |
| `WEAPON.SET_PED_WEAPON_TINT_INDEX` | a1: int, a2: hash, a3: int | `void` |
| `WEAPON.GET_PED_WEAPON_TINT_INDEX` | a: int, b: hash | `int` |
| `WEAPON.SET_WEAPON_OBJECT_TINT_INDEX` | a: int, b: int | `void` |
| `WEAPON.GET_WEAPON_OBJECT_TINT_INDEX` | a: int | `int` |
| `WEAPON.GET_WEAPON_TINT_COUNT` | a: hash | `int` |
| `WEAPON.SET_PED_WEAPON_COMPONENT_TINT_INDEX` | a1: int, a2: hash, a3: hash, a4: int | `void` |
| `WEAPON.GET_PED_WEAPON_COMPONENT_TINT_INDEX` | a1: int, a2: hash, a3: hash | `int` |
| `WEAPON.SET_WEAPON_OBJECT_COMPONENT_TINT_INDEX` | a1: int, a2: hash, a3: int | `void` |
| `WEAPON.GET_WEAPON_OBJECT_COMPONENT_TINT_INDEX` | a: int, b: hash | `int` |
| `WEAPON.GET_PED_WEAPON_CAMO_INDEX` | a: int, b: hash | `int` |
| `WEAPON.SET_WEAPON_OBJECT_CAMO_INDEX` | a: int, b: int | `void` |
| `WEAPON.GET_WEAPON_HUD_STATS` | a: hash, b: pointer | `bool` |
| `WEAPON.GET_WEAPON_COMPONENT_HUD_STATS` | a: hash, b: pointer | `bool` |
| `WEAPON.GET_WEAPON_DAMAGE` | a: hash, b: hash | `float` |
| `WEAPON.GET_WEAPON_CLIP_SIZE` | a: hash | `int` |
| `WEAPON.GET_WEAPON_TIME_BETWEEN_SHOTS` | a: hash | `float` |
| `WEAPON.SET_PED_CHANCE_OF_FIRING_BLANKS` | a1: int, a2: float, a3: float | `void` |
| `WEAPON.SET_PED_SHOOT_ORDNANCE_WEAPON` | a: int, b: float | `int` |
| `WEAPON.REQUEST_WEAPON_HIGH_DETAIL_MODEL` | a: int | `void` |
| `WEAPON._SET_WEAPON_PED_DAMAGE_MODIFIER` | a: hash, b: float | `void` |
| `WEAPON.SET_WEAPON_DAMAGE_MODIFIER` | a: hash, b: float | `void` |
| `WEAPON.SET_WEAPON_AOE_MODIFIER` | a: hash, b: float | `void` |
| `WEAPON.SET_WEAPON_EFFECT_DURATION_MODIFIER` | a: hash, b: float | `void` |
| `WEAPON.IS_PED_CURRENT_WEAPON_SILENCED` | a: int | `bool` |
| `WEAPON.IS_FLASH_LIGHT_ON` | a: int | `bool` |
| `WEAPON.SET_FLASH_LIGHT_FADE_DISTANCE` | a: float | `bool` |
| `WEAPON.SET_FLASH_LIGHT_ACTIVE_HISTORY` | a: int, b: bool | `void` |
| `WEAPON.SET_WEAPON_ANIMATION_OVERRIDE` | a: int, b: hash | `void` |
| `WEAPON.GET_WEAPON_DAMAGE_TYPE` | a: hash | `int` |
| `WEAPON.SET_EQIPPED_WEAPON_START_SPINNING_AT_FULL_SPEED` | a: int | `void` |
| `WEAPON.CAN_USE_WEAPON_ON_PARACHUTE` | a: hash | `bool` |
| `WEAPON.CREATE_AIR_DEFENCE_SPHERE` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: hash | `int` |
| `WEAPON.CREATE_AIR_DEFENCE_ANGLED_AREA` | a1: float, a2: float, a3: float, a4: float, a5: float, a6: float, a7: float, a8: float, a9: float, a10: float, a11: hash | `int` |
| `WEAPON.REMOVE_AIR_DEFENCE_SPHERE` | a: int | `bool` |
| `WEAPON.REMOVE_ALL_AIR_DEFENCE_SPHERES` | — | `void` |
| `WEAPON.SET_PLAYER_TARGETTABLE_FOR_AIR_DEFENCE_SPHERE` | a1: int, a2: int, a3: bool | `void` |
| `WEAPON.IS_AIR_DEFENCE_SPHERE_IN_AREA` | a1: float, a2: float, a3: float, a4: float, a5: pointer | `bool` |
| `WEAPON.FIRE_AIR_DEFENCE_SPHERE_WEAPON_AT_POSITION` | a1: int, a2: float, a3: float, a4: float | `void` |
| `WEAPON.DOES_AIR_DEFENCE_SPHERE_EXIST` | a: int | `bool` |
| `WEAPON.SET_CAN_PED_SELECT_INVENTORY_WEAPON` | a1: int, a2: hash, a3: bool | `void` |
| `WEAPON.SET_CAN_PED_SELECT_ALL_WEAPONS` | a: int, b: bool | `void` |

## ZONE

| Native | Params | Returns |
| --- | --- | --- |
| `ZONE.GET_ZONE_AT_COORDS` | a1: float, a2: float, a3: float | `int` |
| `ZONE.GET_ZONE_FROM_NAME_ID` | a: string | `int` |
| `ZONE.GET_ZONE_POPSCHEDULE` | a: int | `int` |
| `ZONE.GET_NAME_OF_ZONE` | a1: float, a2: float, a3: float | `string` |
| `ZONE.SET_ZONE_ENABLED` | a: int, b: bool | `void` |
| `ZONE.GET_ZONE_SCUMMINESS` | a: int | `int` |
| `ZONE.OVERRIDE_POPSCHEDULE_VEHICLE_MODEL` | a: int, b: hash | `void` |
| `ZONE.CLEAR_POPSCHEDULE_OVERRIDE_VEHICLE_MODEL` | a: int | `void` |
| `ZONE.GET_HASH_OF_MAP_AREA_AT_COORDS` | a1: float, a2: float, a3: float | `hash` |


