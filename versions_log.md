# Liminal Lore — Versions Log

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
