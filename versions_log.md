# Liminal Lore — Versions Log

| Version | Date | Type | Summary |
|---------|------|------|---------|
| V100B | Jul 25, 2026 | Snapshot | Pre-gigatoken backup. Baseline with config system, provider abstraction, RAG engine, agent identity, control deck UI. |
| V101A | Jul 25, 2026 | Release | Gigatoken integration + ZHARK/Telemetry tabs + GUI modernization. Trademark rename (Liminal Link → Liminal Lore). |
| V101B | Jul 25, 2026 | Patch | vw_deck.exe rewired to V101A directory. Dynamic project root, SpawnTool CWD fix, branding updates, recompiled. |
| V1.1B | Aug 9, 2026 | Patch | FS service path fix, Caveman middleware debug + diagnostics + UI indicator, Deck branding rename to Liminal Lore Deck, VW Nexus service index fix, rebuild. |

---

## V1.1B — Aug 9, 2026

### Summary
Fixed FS service "directory outside allowed path" error, debugged and instrumented the Caveman token compression middleware end-to-end, renamed all Deck branding from "VOID WALKERS" to "Liminal Lore Deck", fixed VW Nexus menu shortcut pointing to wrong service index, rebuilt `vw_deck.exe`.

### Files Changed
| File | Change |
|------|--------|
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/hermes-bridge.js` | `resolveConfig()` placeholder fix, `/caveman/test` endpoint, caveman log line |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/caveman_middleware.js` | Cache fix, diagnostic logging in `loadCavemanSkill` and `injectCaveman` |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/VoidWalkers_Chat_GUI.html` | Caveman toggle in DECK view, header indicator, branding rename, diagnostic logs |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/vw_deck.cpp` | Branding rename, VW Nexus index fix, StartSvc error logging |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/vw_deck.config.json` | `project_name` rename, version update |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/build-deck.bat` | Branding rename |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/build-master.bat` | Branding rename, hardcoded path fix |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/electron/README.md` | Path fix |
| `Liminal_Lore_V1.1B/liminal_lore_vw_deck/vw_deck.exe` | Recompiled |

---

## V101B — Jul 25, 2026

### Problem
`vw_deck.exe` was still wired to the old directory and old Web Portal GUI:
- `g_projectRoot` hardcoded to `C:\LuxAura\VoidWalkers_Project\Modern_X64`
- `SpawnTool()` passed `nullptr` as working directory — spawned tools couldn't find their scripts
- Version strings, window title, and tool labels still referenced old branding

### Fixes Applied (`vw_deck.cpp`)

| Fix | Details |
|-----|---------|
| **Dynamic project root** | Replaced hardcoded `g_projectRoot` with `InitProjectRoot()` — resolves from `GetModuleFileNameA` so exe always finds its own directory regardless of where it's placed |
| **SpawnTool CWD** | `CreateProcessA` now uses exe directory as working directory (was `nullptr`) — tools like `hermes-bridge.js`, `turboquant-liminal.py` are found correctly |
| **InitProjectRoot()** | New function, called at start of `WinMain` before any code using `g_projectRoot` (log file creation, build commands) |
| **Window title** | `v1.2` → `v1.01A — Liminal Lore` |
| **Log header** | Updated to `v1.01A Liminal Lore Log Start` |
| **Header comment** | `v1.0` → `v1.01A — Liminal Lore` |
| **Tool label** | "Neural Link" → "Liminal Lore" |
| **LaunchBrowser()** | Log/status messages "Neural Link" → "Liminal Lore Portal" |

### Compilation
- Compiler: g++ (MinGW-W64 via Strawberry Perl)
- Flags: `-O2 -std=c++17 -mwindows -static-libgcc -static-libstdc++`
- Output: `vw_deck.exe` — 720,696 bytes
- Status: ✅ Clean compile, no errors

### Files Changed
| File | Change |
|------|--------|
| `Liminal_Lore_V101A/liminal_lore_vw_deck/vw_deck.cpp` | Dynamic root, SpawnTool CWD, branding, version strings |
| `Liminal_Lore_V101A/liminal_lore_vw_deck/vw_deck.exe` | Recompiled from updated source |
