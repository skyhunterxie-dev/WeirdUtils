# WeirdUtils

This package provides many pre-built DLLs for enhancing the vanilla 1.12 client WoW gameplay experience, aimed in particular at ease of use and accessibility but also bug fixes.

You may get all features by installing `weirdutils.dll`, or choose any selection of features via individual DLLs.  
On Turtle WoW, place your chosen DLLs next to your `WoW.exe` and add them to your `dlls.txt`. For other versions you will need some sort of DLL loader.  

---

## Features

### PNG Screenshots

Saves screenshots as compressed PNG files instead of the default uncompressed TGA format. Runs on a background thread with no frame drops.

Controlled via the `screenshotQuality` CVar (saved to config.wtf):

- `/script SetCVar("screenshotQuality", "6")` -- set compression level (1 = fast, 9 = smallest, default 6)
- `/script SetCVar("screenshotQuality", "0")` -- disable PNG, use original TGA format

**DLL:** `pngscreenshots.dll`

---

### Transmog Fix

Eliminates FPS drops caused by rapid equipment visual updates when transmogged items lose durability. No configuration needed, install and forget.

**DLL:** `transmogfix.dll`

---

### Custom Data/ Assets

Enables loading loose game asset files (models, textures, etc.) from the `Data/` directory without repacking MPQ archives. Place files in `Data/` mirroring the game's internal paths (e.g. `Data/Character/Troll/Female/TrollFemale.m2`) and they will be used instead of the MPQ version.

Also allows multi-character patch archive names (e.g. `patch-12.mpq`, `patch-jimbo.mpq`).

Patch archives are sorted case-insensitively by filename - last in the sort gets highest priority, and all patches override the base archives.

No configuration needed, install and forget.

**DLL:** `customassets.dll`

---

### Utility Minimap Trackings

Adds TBC/WotLK-style minimap tracking icons for NPC types, game objects, and quest givers.
Replaces the native tracking dropdown with a combined menu showing both spell tracking and NPC category tracking.
Can be disabled from the normal AddOn menu. Preferences saved per-character.

- Click the minimap tracking icon to open the dropdown
- Check/uncheck NPC categories to toggle their minimap icons
- Spell tracking (Hunter tracking, Find Herbs, etc.) remains available alongside NPC tracking
- "Hide in Cities" toggle suppresses NPC icons in capital cities

Tracks various npc types and useful objects like Oranges and Brainwasher and Mailbox.

**DLL:** `minimapicons.dll`

---

### Clickthrough

Makes interactable objects and NPCs clickable through players and units that are blocking them. When you click a player or unit that's standing on top of a vendor, mailbox, or other interactable target, the click passes through to the target behind them.

- Players blocking interactable NPCs (vendors, trainers, flight masters, bankers, quest givers, etc.) or game objects become transparent to clicks
- Players blocking lootable corpses become transparent to clicks
- Units (pets, NPCs) blocking interactable game objects become transparent to clicks
- Disabled inside battlegrounds to prevent targeting objectives through enemy players

No configuration needed, install and forget.

**DLL:** `clickthrough.dll`

---

### Log Sessions

Organizes the combat, raw combat, and chat logs into per-character directories with timestamped filenames:

```
Logs\<Realm>\<Character>\WoWChatLog_YYYYMMDD_HHMMSS.txt
Logs\<Realm>\<Character>\WoWCombatLog_YYYYMMDD_HHMMSS.txt
Logs\<Realm>\<Character>\WoWRawCombatLog_YYYYMMDD_HHMMSS.txt (superwow only)
```

Every character login begins with a marker line (`COMBATLOG_SESSION` or `CHAT_SESSION`) identifying the character and realm.
If a log file for the same character was written to within the last 60 minutes, the same logfile will be used instead of creating a new one.

Lua API for addon developers:

- `GetCombatLogPath()` -- returns the current combat log file path
- `GetChatLogPath()` -- returns the current chat log file path

No other configuration needed, install and forget.

**DLL:** `logsessions.dll`

---

### SuperWoW Heal Text Fix

Fixes duplicate floating heal numbers caused by SuperWoW 1.5. Only relevant if you use SuperWoW. No configuration needed, install and forget.

**DLL:** `healtextfix.dll`

---

### Big Cursor

Upscales the hardware cursor for improved visibility without losing sharpness. Supports fractional scales from 1.0 (off) to 4.0.

- `/script SetCursorScale(1.2)` -- set cursor scale (default 1.2x)
- `/script SetCursorScale(1)` -- disable (use original 32x32 cursor)

This value is saved to the `cursorScale` CVar in tenths: `/script SetCVar("cursorScale", "15")` for 1.5x.

Lua API for addon developers:

- `SetCursorScale(n)` -- set scale factor (1.0–4.0), takes effect on next cursor change
- `GetCursorScale()` -- returns current scale factor

**DLL:** `bigcursor.dll`

---

### Performance

Engine-level optimizations that reduce CPU time on math, rendering helpers, file lookups, and data decompression. No visual difference, no configuration needed. Included in `weirdutils.dll`.

- **SIMD Math** - replaces 20+ internal math functions with SSE/AVX equivalents covering skeletal animation, particle rendering, frustum culling, collision detection, text glyph caching, and float-to-integer conversion
- **Data Decompression** - swaps the game's 2004-era zlib with a modern library (2.2x faster). Loading screen times reduced by at least 13%
- **MPQ File Cache** - caches archive file lookups so repeat file opens skip the archive chain walk. Saving 50-160ms every 15 seconds during heavy gameplay
- **Timer Calibration** - recalibrates the OS performance counter for accurate animation timing. Ported from [VanillaFixes](https://github.com/hannesmann/vanillafixes)

Most noticeable in cities, raids, and during zone transitions.

**DLL:** `weirdperformance.dll`

---

## Why No Source Code?

This project is distributed as pre-built DLLs only. The source code is not and will not be made publicly available.

These DLLs work by hooking deeply into the game client's internals: memory layout, function addresses, rendering pipeline, input handling, and more.  
While every feature here is built for legitimate quality-of-life use, the underlying techniques touch on too many core mechanisms that are trivially abusable.  
Publishing the source would be handing a candy store to bad actors: the same hooks and patterns used to render a raid marker or fix a crash can be repurposed for cheats, exploits, and in particular automation with minimal effort.

---

## Developer Notes
### Runtime Module Control API

WeirdUtils exports three functions for querying and disabling modules at runtime in case other devs find their dll's in conflict.

#### Exported Functions

| Function | Signature | Description |
|---|---|---|
| `WeirdUtils_IsModuleActive` | `int __cdecl (const char *name)` | Returns 1 if the module is compiled in and currently hooked, 0 otherwise |
| `WeirdUtils_DisableModule` | `int __cdecl (const char *name)` | Unhooks the named module. Returns 1 if found, 0 otherwise |
| `WeirdUtils_DisableAll` | `int __cdecl (void)` | Unhooks all modules. Returns count of modules disabled |

Module names are case-insensitive and match the released dll names:

`customassets`, `logsessions`, `transmogfix`, `minimapicons`, `healtextfix`, `bigcursor`, `pngscreenshots`, `clickthrough`, `weirdperformance`

There is no re-enable API.

#### C/C++ Header

A header-only `include/weirdutils_api.h` is provided that handles DLL discovery and runtime resolution automatically. No .lib file needed:

```c
#include "weirdutils_api.h"

// Returns 0 if WeirdUtils isn't loaded - safe to call unconditionally
if (WeirdUtils_IsModuleActive("transmogfix"))
    WeirdUtils_DisableModule("transmogfix");
```

The header tries all known DLL names (`weirdutils.dll`, `minimapicons.dll`, etc.) via `GetModuleHandleA`, so it works regardless of which DLL variant is loaded.

#### Raw GetProcAddress

If you prefer not to use the header:

```c
HMODULE hMod = GetModuleHandleA("weirdutils.dll");
if (hMod) {
    typedef int (__cdecl *IsActiveFn)(const char *);
    IsActiveFn isActive = (IsActiveFn)GetProcAddress(hMod, "WeirdUtils_IsModuleActive");
    if (isActive && isActive("transmogfix")) {
        typedef int (__cdecl *DisableFn)(const char *);
        DisableFn disable = (DisableFn)GetProcAddress(hMod, "WeirdUtils_DisableModule");
        if (disable) disable("transmogfix");
    }
}
```

### Version Query API

WeirdUtils registers a Lua global table and query function for addon developers to detect which modules are loaded and their versions. Available from the login screen onward.

#### `GetWeirdUtilsVersion()`

Returns the `WeirdUtils` table containing all enabled modules and their version strings:

```lua
local modules = GetWeirdUtilsVersion()
for name, version in pairs(modules) do
    print(name .. " v" .. version)
end
```

#### `GetWeirdUtilsVersion("modulename")`

Returns the version string for a specific module, or `nil` if not loaded:

```lua
if GetWeirdUtilsVersion("minimapicons") then
    -- Minimap Icons is available
end

local ver = GetWeirdUtilsVersion("weirdperformance")  -- "1.0" or nil
```

The `WeirdUtils` table is additive - if multiple independent DLLs are loaded (e.g. `minimapicons.dll` and `clickthrough.dll` separately), each adds its own modules to the shared table.

---

### Module Mutexes

Each module also holds a named mutex while active: `Local\WeirdUtils_<name>_<PID>` (e.g. `Local\WeirdUtils_transmogfix_12345`). The exception is transmogfix, which uses `Local\TransmogCoalesceHook_<PID>` for legacy reasons.

If you see the mutex, the module is loaded - and can use the Runtime Module Control API to disable it. If you don't see it, the module isn't active and you're free to hook those functions yourself.
