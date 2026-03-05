# WeirdUtils

This package provides many pre-built DLLs for enhancing the vanilla WoW gameplay experience, aimed in particular at ease of use and accessibility but also bug fixes.

You may get all features by installing `weirdutils.dll`, or choose any selection of features via individual DLLs.  
On Turtle WoW, place your chosen DLLs next to your `WoW.exe` and add them to your `dlls.txt`. For other versions you will need some sort of DLL loader.  

---

## Features

### Transmog Fix

Eliminates FPS drops caused by rapid equipment visual updates when transmogged items lose durability. No configuration needed, install and forget.

**DLL:** `transmogfix.dll`

---

### Custom Data/ Assets

Enables loading loose game asset files (models, textures, etc.) from the `Data/` directory without repacking MPQ archives. Place files in `Data/` mirroring the game's internal paths (e.g. `Data/Character/Troll/Female/TrollFemale.m2`) and they will be used instead of the MPQ version.

Also allows multi-character patch archive names (e.g. `patch-12.mpq`, `patch-jimbo.mpq`).

Patch archives are sorted case-insensitively by filename — last in the sort gets highest priority, and all patches override the base archives.

No configuration needed, install and forget.

**DLL:** `customassets.dll`

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

## Why No Source Code?

This project is distributed as pre-built DLLs only. The source code is not and will not be made publicly available.

These DLLs work by hooking deeply into the game client's internals: memory layout, function addresses, rendering pipeline, input handling, and more.  
While every feature here is built for legitimate quality-of-life use, the underlying techniques touch on too many core mechanisms that are trivially abusable.  
Publishing the source would be handing a candy store to bad actors: the same hooks and patterns used to render a raid marker or fix a crash can be repurposed for cheats, exploits, and in particular automation with minimal effort.
