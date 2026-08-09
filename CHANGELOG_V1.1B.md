# Changelog — Liminal Lore Deck V1.1B

**Date:** August 9, 2026  
**Type:** Patch  
**Previous:** V1.1A (Jul 25, 2026)

---

## Overview

This patch fixes the FS service path resolution error, debugs and instruments the Caveman token compression middleware end-to-end, renames all Deck branding from "VOID WALKERS" to "Liminal Lore Deck", fixes the VW Nexus menu shortcut pointing to the wrong service index, and rebuilds `vw_deck.exe`.

---

## Bug Fixes

### 1. FS Service "Directory Outside Allowed Path"
**File:** `hermes-bridge.js` — `resolveConfig()` / `loadActiveConfig()`

Config files using `${PROJECT_ROOT}` placeholder were not being resolved, causing the FS service to reject all directory listings with "directory outside allowed path". The `resolveConfig()` function now falls back to the workspace `project_root` when the config has unresolved placeholders. `loadActiveConfig()` matches workspace by `id` or `name` and passes the fallback root.

### 2. Caveman `loadCavemanSkill` Cache Bug
**File:** `caveman_middleware.js` — `loadCavemanSkill()`

Empty or missing skill files were permanently cached as `''`, preventing any retry on subsequent calls. If a skill file was initially missing or empty (e.g. during setup), the middleware would never attempt to reload it. Fixed by only caching non-empty results — empty results return `''` but are not stored in the cache, allowing retry.

### 3. VW Nexus Menu Shortcut — Wrong Service Index
**File:** `vw_deck.cpp` line 809

The Tools menu "VW Nexus" handler (`case 1606`) called `StartSvc(5)`, which starts **Colibri Bridge** (index 5 in `g_svcDefs`), not VW Nexus (index 7). Fixed to `StartSvc(7)`. Note: "Start All Services" (menu ID 1001) was already correct — it iterates all services with `autoStart == true`, and VW Nexus has `autoStart: true`.

### 4. Caveman Toggle Not Visible in Deck View
**File:** `VoidWalkers_Chat_GUI.html`

The Caveman toggle was only in the sidebar, which is hidden in DECK view. Added a toggle checkbox and level selector to the DECK view toolbar. Also added `loadDeckConfig()` call on page load to initialize toggle state from backend config/localStorage, and `initAgentToggles()` call after config load.

---

## New Features

### Caveman Diagnostic Logging
**Files:** `caveman_middleware.js`, `hermes-bridge.js`, `VoidWalkers_Chat_GUI.html`

Added comprehensive `console.log` statements throughout the Caveman pipeline:
- `loadCavemanSkill()` logs on successful load (level, length, path) and warns on failure
- `injectCaveman()` logs at every decision point: skip (disabled), skip (no skill), skip (below min tokens), success (injected into existing system message), success (created new system message)
- Bridge chat handler logs `[CAVEMAN] Settings:` before injection (settings JSON + message count)
- `syncCavemanToBackend()` logs backend response JSON
- `sendMessage()` logs caveman active/inactive state before sending

### `/caveman/test` Endpoint
**File:** `hermes-bridge.js`

New `GET /caveman/test` endpoint — runs `injectCaveman()` on a sample message and returns JSON with:
- `settings` — current caveman runtime settings
- `skill_loaded` — boolean
- `skill_length` — character count
- `skill_preview` — first 200 chars
- `original_messages` / `injected_messages` — before/after comparison
- `system_message_modified` — boolean

Visit `http://127.0.0.1:8643/caveman/test` in a browser to verify caveman is working.

### Caveman Status Indicator in Header
**File:** `VoidWalkers_Chat_GUI.html`

Added amber CAVEMAN indicator to the header status bar. Shows `CAVEMAN: FULL` / `CAVEMAN: LITE` / `CAVEMAN: OFF` etc. Updated in `initAgentToggles()`, `toggleAgentFeature()`, and `onCavemanLevelChange()` so it always reflects current state.

### StartSvc Error Logging
**File:** `vw_deck.cpp` — `StartSvc()`

Failed service starts now log the Windows error code and message via `GetLastError()` + `FormatMessageA()`. Previously only showed "Failed to start [name]" with no reason.

---

## Branding Rename

All "VOID WALKERS" / "VoidWalkers" references updated to "Liminal Lore Deck":

| Location | Old | New |
|----------|-----|-----|
| `vw_deck.cpp` window title | `VOID WALKERS // Command Deck v1.2` | `Liminal Lore Deck // v1.2` |
| `vw_deck.cpp` menu bar | `VOID WALKERS // v1.2` | `Liminal Lore Deck // v1.2` |
| `vw_deck.cpp` log header | `VoidWalkers Command Deck` | `Liminal Lore Deck` |
| `vw_deck.cpp` header comment | `VoidWalkers Command Deck v1.0` | `Liminal Lore Deck v1.0` |
| HTML `<title>` | `VOID WALKERS // Liminal Lore` | `Liminal Lore Deck` |
| HTML `<h1>` | `Void Walkers` | `Liminal Lore Deck` |
| HTML subtitle | `Liminal Lore Interface // v1.0` | `Neural Link Interface // v1.0` |
| HTML settings modal | `VOID WALKERS // Settings` | `Liminal Lore Deck // Settings` |
| `vw_deck.config.json` | `VoidWalkers` | `Liminal Lore Deck` |
| `build-deck.bat` | `VoidWalkers Command Deck` | `Liminal Lore Deck` |

---

## Build

- **Compiler:** g++ (MinGW-W64)
- **Flags:** `-O2 -std=c++17 -mwindows -static-libgcc -static-libstdc++`
- **Status:** Clean compile, no errors
- **Output:** `vw_deck.exe`

---

## Files Changed

| File | Changes |
|------|---------|
| `hermes-bridge.js` | `resolveConfig()` placeholder fix, `loadActiveConfig()` workspace matching, `/caveman/test` endpoint, caveman log line in chat handler |
| `caveman_middleware.js` | `loadCavemanSkill()` cache fix (no permanent empty cache), diagnostic logging in `loadCavemanSkill` and `injectCaveman` |
| `VoidWalkers_Chat_GUI.html` | Caveman toggle in DECK view toolbar, `loadDeckConfig()` on page load, `initAgentToggles()` after config, header CAVEMAN indicator, `syncCavemanToBackend()` response logging, `sendMessage()` caveman status log, branding rename (title, h1, subtitle, settings modal) |
| `vw_deck.cpp` | Branding rename (window title, menu bar, log header, comment), VW Nexus `StartSvc(5)` → `StartSvc(7)`, `StartSvc()` error logging with `GetLastError` |
| `vw_deck.config.json` | `project_name`: `VoidWalkers` → `Liminal Lore Deck` |
| `build-deck.bat` | Banner branding rename |
| `build-master.bat` | Branding rename, hardcoded `liminal_link_vw_deck` path replaced with `.` |
| `electron/README.md` | Path reference updated to `Liminal_Lore_V1.1B/liminal_lore_vw_deck/electron` |
| `vw_deck.exe` | Recompiled from updated source |
