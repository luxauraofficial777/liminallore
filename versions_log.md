# Liminal Lore — Versions Log
## V101A → V101B (Liminal Lore)
### Date: July 25, 2026

---

## Summary

V101B fixes three critical issues that prevented the Liminal Lore suite from functioning:
1. **FS "Path outside allowed directories"** — Hermes bridge failed to resolve relative `project_root` to an absolute path
2. **ZHARK "is Nexus running?"** — Nexus API lacked `/zhark/status`, `/zhark/start`, `/zhark/stop` endpoints
3. **Nexus crash on startup** — `provider_nexus.py` had wrong path calculation for `provider_registry` import

---

## Files Modified

### 1. `hermes-bridge.js` (lines 17-24, 51-57)

**`resolveConfig()` — project_root absolute path resolution**
```js
// V101A: project_root stayed as "../../.." — fileAccessGate received literal "${project_root}" strings
// V101B: resolves to absolute path via path.resolve(__dirname, projectRoot)
let projectRoot = rawConfig.project_root || path.dirname(path.dirname(__dirname));
projectRoot = path.resolve(__dirname, projectRoot);
```

**`loadActiveConfig()` — workspace config fallback**
```js
// V101A: blindly used workspace config path even if file didn't exist
// V101B: checks fs.existsSync, falls back to __dirname/vw_deck.config.json
let configPath = path.join(__dirname, 'vw_deck.config.json');
if (ws) {
  const wsConfigPath = path.join(ws.project_root, ws.config_file);
  if (fs.existsSync(wsConfigPath)) configPath = wsConfigPath;
}
```

**Result:** `allowed_prefixes` now resolve to `C:\LuxAura\VoidWalkers_Project\Modern_X64` instead of `${PROJECT_ROOT}`. File browsing works.

---

### 2. `vw_nexus/api.py` (lines 550-581, 1428-1467, 1942-1944)

**New GET endpoints:**
- `GET /zhark/status` — Returns ZHARK cycle state (running, phase, iteration, hypotheses, evidence, strategies, causal_map, events)
- `GET /fubbu/status` — Stub endpoint for FUBBU worker telemetry
- `GET /tokenizer/status` — Stub endpoint for tokenizer/gigatoken info

**New POST endpoints:**
- `POST /zhark/start` — Starts a ZHARK research cycle with a question, returns initial cycle state with 5 phases (observe → hypothesize → test → analyze → synthesize)
- `POST /zhark/stop` — Stops the active ZHARK cycle

**Nexus `/status` response updated:**
```python
# V101B added:
"tokenizer": {"engine": "gigatoken", "backend": "gigatoken", "loaded": False, "vocab_size": 0},
```

**Result:** GUI ZHARK panel can now poll `/zhark/status` and start/stop cycles without "is Nexus running?" errors.

---

### 3. `vw_nexus/provider_nexus.py` (lines 19, 37)

**`_AGENT_DIR` path calculation fix:**
```python
# V101A: parent.parent.parent.parent = Modern_X64 (wrong — no AGENT/distilled_agents here)
# V101B: parent.parent.parent.parent.parent = VoidWalkers_Project (correct)
_AGENT_DIR = Path(__file__).parent.parent.parent.parent.parent / "AGENT" / "distilled_agents"
```

**Config path fix:**
```python
# V101A: "tools" / "liminal_lore_vw_deck" / "vw_deck.config.json"
# V101B: "tools" / "Liminal_Lore_V101B" / "liminal_lore_vw_deck" / "vw_deck.config.json"
config_path = str(project_root / "Modern_X64" / "tools" / "Liminal_Lore_V101B" / "liminal_lore_vw_deck" / "vw_deck.config.json")
```

**Result:** Nexus starts without `ModuleNotFoundError: No module named 'provider_registry'`.

---

### 4. `workspaces.json` (line 3)

```json
// V101A: "active_workspace": "VoidWalkers HD"  (display name — no matching workspace ID)
// V101B: "active_workspace": "vw_hd"            (actual workspace ID)
"active_workspace": "vw_hd",
```

**Result:** `loadActiveConfig()` correctly finds the workspace by ID, falls back to local config if workspace config path doesn't exist.

---

### 5. `vw_deck.config.json` (lines 7, 264-276)

**Version string:**
```json
// V101A: "version": "v3.0"
// V101B: "version": "v1.01B"
```

**project_root:**
```json
// V101A: "project_root": "${PROJECT_ROOT}"  (unresolved placeholder)
// V101B: "project_root": "../../.."          (relative, resolved by hermes-bridge.js)
```

**allowed_prefixes:** Use `${project_root}` (lowercase) which `resolveConfig()` replaces with the absolute path.

---

### 6. `vw_deck.cpp` (lines 2, 390, 516, 521)

**Version strings updated:**
- Header comment: `v1.01A` → `v1.01B`
- Window title: `v1.01A` → `v1.01B`
- Log start banner: `v1.01A` → `v1.01B`
- Init message: `v1.01A` → `v1.01B`

**Service definitions (line 239):** VW Nexus already present with `autoStart=true` — no change needed.

---

### 7. `VoidWalkers_Chat_GUI.html` (lines 1758, 6864)

**Version strings updated:**
- Subtitle: `Liminal Lore // v1.01A` → `v1.01B`
- Suite init log: `v1.01A` → `v1.01B`

---

### 8. `build-deck.bat` (full rewrite)

**V101B improvements:**
- Kills running `vw_deck.exe` before compiling (avoids file lock)
- Compiles to `vw_deck_new.exe` then swaps to `vw_deck.exe`
- Fallback rename to `.bak` if delete fails
- Updated build title to `v1.01B`

---

### 9. `electron/` copies updated

- `electron/vw_deck.config.json` — same fixes as root copy
- `electron/liminal_settings.json` — absolute paths for `project_root`, `allowed_roots`, `study_dir`

---

## New Files

| File | Purpose |
|------|---------|
| `start-all-services.bat` | Standalone script to kill old processes and start FS, Bridge, Nexus, TurboQuant, Ollama in order |

---

## Verification Results (2026-07-25 13:48)

```
BRIDGE (8643): ONLINE
  project_root: ../../..
  file_access.mode: allowlist
  allowed_prefixes: ['C:\LuxAura\VoidWalkers_Project\Modern_X64', ...]

NEXUS (8651): ONLINE, /zhark/status OK
  running: False, phase: IDLE

FS SERVICE (8644): ONLINE
  allowed_roots: ['C:/LuxAura/VoidWalkers_Project/Modern_X64']

LIST-DIR via bridge: OK
```

All three original errors resolved:
- ✅ No more "Path outside allowed directories"
- ✅ No more "Failed to start ZHARK — is Nexus running?"
- ✅ Nexus starts without import crash

---

## Note: QA Testing Harness (Experimental)

The `vw_qa_harness.py` script in the deck directory is an **experimental** testing
tool included for development convenience. It has **not been formally tested**
in the release build and should not be considered a production QA gate.

- It connects to all running services and exercises endpoints, but edge cases,
  error recovery, and service startup paths are not fully validated.
- The `--start` flag kills and restarts processes aggressively — use with caution.
- The ZHARK live cycle test makes real LLM calls to Ollama and can take several
  minutes to complete.
- Treat it as a developer debugging aid, not a certified test suite.



| Version | Date | Type | Summary |
|---------|------|------|---------|
| V100B | Jul 25, 2026 | Snapshot | Pre-gigatoken backup. Baseline Liminal Lore with config system, provider abstraction, RAG engine, agent identity, control deck UI. |
| V101A | Jul 25, 2026 | Release | Gigatoken integration + GUI modernization. |

---

## V101A — Jul 25, 2026

### Gigatoken Integration
- Created `shared/token_counter.py` — shared token counter with fallback chain: gigatoken → tiktoken → heuristic (len//4)
- Wired gigatoken into **RAG chunker** (`vw_rag/chunker.py`) — token-based chunking replaces char-based; exact token counts replace heuristic
- Wired gigatoken into **LLM provider** (`liminal_lore_vw_deck/config/provider_abstraction.py`) — added `count_tokens`, `fits_in_context`, `budget_for_context`, `get_context_window` methods; `generate()` now returns `prompt_tokens_local`
- Wired gigatoken into **Colibri bridge** (`liminal_lore_vw_deck/colibri_bridge.py`) — token throughput tracking on `ExpertState`, throughput-aware swap decisions, `/colibri/throughput` GET+POST endpoints
- Wired gigatoken into **TurboQuant** (`liminal_lore_vw_deck/turboquant-liminal.py`) — KV cache prediction function, `/tq/predict` HTTP endpoint
- Wired gigatoken into **ZHARK orchestrator** (`zhark/zhark_orchestrator.py`) — `_budget_prompt` helper for prompt token budgeting
- Wired gigatoken into **FUBBU Worker** (`fubbu/fubbu_worker.py`) — pre-flight token check + prompt truncation in `_call_ollama`
- Wired gigatoken into **FUBBU Router** (`fubbu/fubbu_router.py`) — pre-flight token check + prompt truncation in `_call_router_model`
- Wired gigatoken into **FUBBU Verifier** (`fubbu/fubbu_verifier.py`) — pre-flight token check + prompt truncation in `_call_verifier_model`
- Created `docs/GIGATOKEN_INTEGRATION_SPEC.md` — full integration specification (architecture, API, integration points, fallback, config, testing, rollout, rollback)

### GUI — New Tabs
- **ZHARK tab** — autonomous research orchestrator panel:
  - Research question display with context
  - Hypothesis graph with status badges (proposed, testing, confirmed, refuted) and support/confidence scores
  - Cycle progress visualization with done/active/pending step states
  - Evidence & contradictions feed (support vs contradiction border colors)
  - Strategic options panel with feasibility ratings
  - Causal map (cause → effect with strength and type)
  - ZHARK event log
  - Start/stop cycle controls with research question input
  - Polls `/zhark/status` on Nexus (port 8651) every 3s

- **Global Telemetry tab** — system health dashboard (6-panel grid):
  - Token throughput: tokenizer engine, aggregate tokens/sec, total tokens, per-expert TPS breakdown
  - RAM & KV cache: RAM % with color-coded bar, pagefile %, KV cache prediction, TurboQuant mode
  - Colibri experts: hot expert count, cache hits/misses, swap events, per-expert TPS list
  - Provider status: active provider, model, context window, last prompt token count
  - FUBBU workers: active workers, tasks completed, tasks failed
  - Nexus agents: registered agents, active tasks, file locks
  - Polls Colibri (8648), TurboQuant (8646), Nexus (8651), and Hermes bridge (8643) every 3s

### GUI — Modernization
- Version label updated: `v1.0` → `v1.01A — Gigatoken Enhanced`
- Context gauge made visible in status bar (was `display:none`), retitled "Gigatoken Context Usage"
- Tokenizer badge (`GIGATOKEN`) added to status bar
- Suite init version updated to `v1.01A`
- `switchView()` extended to handle `zhark` and `telemetry` views with proper show/hide, polling start/stop
- New CSS: `#zharkPanel`, `.zhark-layout` (3-column grid), `.hyp-item`, `.hyp-status`, `.evidence-item`, `.strategy-item`, `.causal-node`, `.cycle-step`, `#telemetryPanel`, `.tel-grid` (3x2 grid), `.tel-metric`, `.tel-bar`, `.tel-bar-fill`, `.tel-expert-row`

### Trademark Compliance
- All "Liminal Link" text references replaced with "Liminal Lore" across:
  - `VoidWalkers_Chat_GUI.html` (settings panel comment)
  - `vw_deck.cpp` (embedded browser comment)
  - `shared/Win32Menu.hpp` (about dialog)
  - `SETUP_GUIDE.md` (header description)
- All `liminal_link_vw_deck` path references updated to `liminal_lore_vw_deck` across:
  - `vw_nexus/provider_nexus.py`, `vw_nexus/internal_mcp_client.py`
  - `vw_deck.config.json` (root + electron copies)
  - `config/setup_wizard.py`, `SETUP_GUIDE.md`, `electron/README.md`
  - `build-master.bat`, `bridge_stdout.log`, `electron/dist/builder-debug.yml`
- All `Liminal_Link_V100B` references updated to `Liminal_Lore_V100B` in gigatoken spec
- Folder renamed from `Liminal_Link_V101A` to `Liminal_Lore_V101A`

### Files Changed
| File | Change |
|------|--------|
| `shared/token_counter.py` | Created (gigatoken integration module) |
| `vw_rag/chunker.py` | Token-based chunking + exact counts |
| `liminal_lore_vw_deck/config/provider_abstraction.py` | Token counting methods added |
| `liminal_lore_vw_deck/colibri_bridge.py` | Throughput tracking + endpoints |
| `liminal_lore_vw_deck/turboquant-liminal.py` | KV cache prediction + endpoint |
| `zhark/zhark_orchestrator.py` | Prompt budgeting helper |
| `fubbu/fubbu_worker.py` | Pre-flight token check |
| `fubbu/fubbu_router.py` | Pre-flight token check |
| `fubbu/fubbu_verifier.py` | Pre-flight token check |
| `liminal_lore_vw_deck/VoidWalkers_Chat_GUI.html` | ZHARK tab, Telemetry tab, modernization, trademark fixes |
| `liminal_lore_vw_deck/vw_deck.cpp` | Trademark fix |
| `shared/Win32Menu.hpp` | Trademark fix |
| `liminal_lore_vw_deck/SETUP_GUIDE.md` | Trademark fix + path fix |
| `liminal_lore_vw_deck/vw_deck.config.json` | Path fix |
| `liminal_lore_vw_deck/electron/vw_deck.config.json` | Path fix |
| `liminal_lore_vw_deck/electron/README.md` | Path fix |
| `liminal_lore_vw_deck/config/setup_wizard.py` | Path fix |
| `liminal_lore_vw_deck/build-master.bat` | Path fix |
| `vw_nexus/provider_nexus.py` | Path fix |
| `vw_nexus/internal_mcp_client.py` | Path fix |
| `docs/GIGATOKEN_INTEGRATION_SPEC.md` | Path fix |
| `README.md` | Created |
| `versions_log.md` | Created (this file) |

### Stats
- GUI file: 241KB → 271KB (+30KB of new HTML/CSS/JS)
- GUI lines: ~6,169 → ~6,872 (+703 lines)
- New view tabs: 2 (ZHARK, TELEMETRY)
- Total view tabs: 5 (CHAT, DECK, ZHARK, TELEMETRY, SUITE)
- Gigatoken integration points: 8 components
- Trademark references removed: all "Liminal Link" instances across 10+ files
