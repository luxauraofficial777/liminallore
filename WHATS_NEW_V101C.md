# What's New in Liminal Lore V101C

> **V101C** — Caveman Token Compression + Full Suite Integration
> Released Jul 29, 2026

---

## Headline Feature: Caveman Token Compression

V101C integrates [Caveman](https://github.com/juliusbrussee/caveman) into the Liminal Lore toolchain. Caveman injects a terse "caveman speak" skill prompt into LLM requests, cutting output tokens by ~65% without losing technical accuracy. Code blocks, commands, and error strings stay byte-for-byte exact.

### Compression Levels

| Level | Token Savings | Description |
|-------|--------------|-------------|
| `off` | 0% | Passthrough — no compression |
| `lite` | ~20-30% | Drop filler/hedging. Keep articles + full sentences |
| `full` | ~50-60% | Drop articles, fragments OK, short synonyms |
| `ultra` | ~65%+ | Strip conjunctions, one word when enough |
| `wenyan` | ~80-90% | Classical Chinese (文言文) |

### Runtime Toggle

No restart needed. Control Caveman live via HTTP:

```bash
# Check current settings
curl http://127.0.0.1:8643/caveman

# Switch to ultra
curl -X POST http://127.0.0.1:8643/caveman \
  -H "Content-Type: application/json" \
  -d '{"level": "ultra"}'

# Turn off
curl -X POST http://127.0.0.1:8643/caveman \
  -H "Content-Type: application/json" \
  -d '{"enabled": false}'

# View cumulative savings
curl http://127.0.0.1:8643/caveman/stats
```

Also configurable via `.env` (`CAVEMAN_ENABLED`, `CAVEMAN_LEVEL`), `liminal_lore.yaml`, or the setup wizard (new Step 5b).

---

## Full Suite Architecture — All Components Interconnected

V100C wires the entire Liminal Lore suite together. Every component is proxied through Hermes Bridge, monitored in telemetry, and managed by the C++ deck.

### Service Port Map

| Service | Port | Auto-Start | Description |
|---------|------|------------|-------------|
| Ollama LLM | 11434 | ✓ | Local model inference (monks) |
| FS Service | 8644 | ✓ | File system proxy + access gate |
| Hermes Bridge | 8643 | ✓ | Central proxy, SSE streaming, telemetry |
| TurboQuant | 8646 | ✓ | Dynamic KV-cache scaling |
| Runpod Bridge | 8647 | ✓ | Cloud GPU provider harness |
| Colibri Bridge | 8648 | ✓ | Expert streaming, LFRU, throughput |
| VW Nexus | 8651 | ✓ | Agent registry, task queue, event bus, RAG |
| FUBBU Sidecar | 8653 | ✗ | Router-Worker-Verifier pipeline |
| ZHARK CSO | 8654 | ✗ | Autonomous long-horizon research |
| VPN Monitor | 8645 | ✗ | Optional VPN health check |
| Caveman | (via Hermes) | ✓ | Token compression (in-process) |

### Interconnection Diagram

```
                    ┌─────────────────────────────────────────────────┐
                    │              VW Deck (C++ / Electron)            │
                    │  Service Orchestrator + Telemetry Dashboard      │
                    └────────────────────┬────────────────────────────┘
                                         │
                    ┌────────────────────▼────────────────────┐
                    │           Hermes Bridge (:8643)          │
                    │  • SSE proxy to LLM providers             │
                    │  • Caveman skill injection                │
                    │  • RAG context injection                  │
                    │  • Proxy routes to all services           │
                    │  • Telemetry aggregation (10s poll)       │
                    │  • /health/all — full service check        │
                    └──┬──────┬──────┬──────┬──────┬──────┬─────┘
                       │      │      │      │      │      │
            ┌──────────┘      │      │      │      │      └──────────────┐
            ▼                 ▼      ▼      ▼      ▼                     ▼
     ┌─────────────┐  ┌──────────┐ ┌────────┐ ┌────────┐ ┌──────────┐ ┌──────────┐
     │ TurboQuant  │  │ Colibri  │ │ Nexus  │ │ FUBBU  │ │ ZHARK    │ │ VW RAG   │
     │   :8646     │  │  :8648   │ │ :8651  │ │ :8653  │ │  :8654   │ │ (Nexus)  │
     │             │  │          │ │        │ │        │ │          │ │          │
     │ KV predict  │  │ Expert   │ │ Agent  │ │ Router→ │ │ Hypo-   │ │ FTS5 +   │
     │ Caveman-    │  │ LFRU     │ │ Reg    │ │ Worker→│ │ thesis  │ │ vector   │
     │ aware       │  │ Caveman- │ │ Task   │ │ Verify │ │ graph   │ │ hybrid   │
     │             │  │ aware    │ │ Queue  │ │ pipeline│ │ Causal  │ │ retrieval│
     │             │  │          │ │ Event  │ │        │ │ map     │ │          │
     │             │  │          │ │ Bus    │ │        │ │ Strategy│ │          │
     │             │  │          │ │ RAG    │ │        │ │ synth   │ │          │
     └─────────────┘  └──────────┘ └────────┘ └────────┘ └──────────┘ └──────────┘
```

### Hermes Bridge Proxy Routes

All services are proxied through Hermes Bridge on port 8643:

| Route Prefix | Target Port | Service |
|-------------|-------------|---------|
| `/tq/` | 8646 | TurboQuant |
| `/liminal/` | 8645 | Liminal Lore / VPN Monitor |
| `/runpod/` | 8647 | Runpod Bridge |
| `/nexus/` | 8651 | VW Nexus (agent registry, tasks, RAG, events) |
| `/colibri/` | 8648 | Colibri Bridge (expert status, throughput) |
| `/fubbu/` | 8653 | FUBBU sidecar (router-worker-verifier) |
| `/zhark/` | 8654 | ZHARK Virtual CSO (research orchestrator) |
| `/caveman` | (in-process) | Caveman compression control |
| `/caveman/stats` | (in-process) | Caveman telemetry |

### Telemetry Aggregation

Hermes Bridge polls all services every 10 seconds and exposes a unified telemetry snapshot at `GET /nexus/telemetry`:

```json
{
  "nexus":     { "alive": true, "agents": 3, "tasks": {...}, "sessions": 2 },
  "turboquant":{ "alive": true, "ram_pct": 62.3, "mode": "dynamic" },
  "ollama":    { "alive": true, "models": [...] },
  "colibri":   { "alive": true, "hot_expert": 1, "cache_hits": 142, ... },
  "fubbu":     { "alive": true, "active_tasks": 2, "completed_tasks": 17 },
  "zhark":     { "alive": true, "cycles": 12, "convergence": 0.73 },
  "rag":       { "alive": true, "total_chunks": 46700 },
  "caveman":   { "tokens_saved": 15420, "responses": 37, "compression_ratio": 0.62 },
  "alerts":    [...]
}
```

### `/health/all` Endpoint

Full service health check — used by the C++ deck and browser UI:

```
GET http://127.0.0.1:8643/health/all
→ { "ok": true, "services": { "bridge": {...}, "fs_service": {...}, ... } }
```

Services checked: bridge, fs_service, liminal_lore, turboquant, runpod_bridge, colibri, vw_nexus, caveman.

---

## Component Details

### VW Nexus (`tools/vw_nexus/`)
- **Port**: 8651 (HTTP) + 8652 (WebSocket)
- **Role**: Universal orchestration layer — agent registry, task queue with DAG dependencies, file lock manager, event bus, RAG endpoint proxy
- **Key modules**: `api.py`, `agent_registry.py`, `task_queue.py`, `lock_manager.py`, `event_bus.py`, `state_store.py`, `websocket_server.py`, `agent_identity.py`, `governor.py`, `committee.py`
- **Config**: `nexus.url` in `config_loader.py`, `NEXUS_API_KEY` env var
- **Deck integration**: `vw_nexus_service.py` wrapper launches Nexus with correct PYTHONPATH from deck directory

### VW RAG (`tools/vw_rag/`)
- **Port**: Via Nexus (:8651/rag)
- **Role**: Hybrid knowledge retrieval — SQLite FTS5 BM25 + optional sentence-transformers vector similarity
- **Index**: 866 files, 46,700 chunks, auto-tagged across 15 topic categories
- **Key modules**: `chunker.py`, `embedder.py`, `indexer.py`, `retriever.py`, `multi_corpus.py`, `graph_index.py`
- **CLI**: `python -m vw_rag index|query|status`
- **Nexus endpoints**: `POST /rag`, `POST /rag/index`, `GET /rag/status`
- **Hermes integration**: RAG context injected before Caveman in the message pipeline via `injectContextIntoMessages()`

### FUBBU (`tools/fubbu/`)
- **Port**: 8653
- **Role**: Self-hosted multi-agent sidecar — Router→Worker→Verifier pipeline for debugging tasks
- **Pipeline**: Router (qwen2.5-coder:7b) decomposes task → Worker (deepseek-coder:6.7b) analyzes → Verifier checks structural + semantic correctness
- **Nexus integration**: Polls Nexus for ready tasks, reports results back, mirrors telemetry events to event bus
- **RAG integration**: Router queries RAG for context before building worker prompts
- **Key modules**: `fubbu_router.py`, `fubbu_worker.py`, `fubbu_verifier.py`, `fubbu_state.py`, `fubbu_telemetry.py`, `fubbu_topography.py`, `fubbu_nexus_client.py`
- **Config**: `fubbu_config.yaml` — all local model endpoints, no external API keys

### ZHARK (`tools/zhark/`)
- **Port**: 8654
- **Role**: Autonomous long-horizon research orchestrator (Virtual CSO)
- **Loop**: Hypothesis → Gather → Analyze → Verify → Contradict → Refine → Causal → Synthesize
- **State**: Checkpointed to SQLite every cycle for resume capability
- **Config via env vars**: `ZHARK_MAX_ITERATIONS`, `ZHARK_CONVERGENCE_THRESHOLD`, `ZHARK_MAX_DEPTH`, `ZHARK_CYCLE_DELAY`, `ZHARK_VERIFY_CANDIDATES`
- **Key modules**: `zhark_orchestrator.py`, `zhark_hypothesis_graph.py`, `zhark_causal_mapper.py`, `zhark_strategy_synthesizer.py`, `zhark_state.py`, `zhark_web_gatherer.py`, `zhark_verify.py`
- **Token counting**: Uses Gigatoken via `shared.token_counter` with heuristic fallback

### Caveman (`caveman_middleware.js` + `caveman_levels/`)
- **Port**: In-process (Hermes Bridge :8643)
- **Role**: Token compression via terse prompt injection
- **Levels**: off, lite, full, ultra, wenyan
- **Config**: `caveman` section in `config_loader.py`, `CAVEMAN_ENABLED`/`CAVEMAN_LEVEL` env vars
- **Coordination**: TurboQuant uses `caveman_enabled` for KV prediction; Colibri normalizes throughput for compressed tokens

### Colibri (`colibri_bridge.py`)
- **Port**: 8648
- **Role**: Expert model streaming with LFRU (Least Frequently Recently Used) heat scoring
- **Caveman coordination**: `access()` normalizes compressed token counts (÷0.35) so LFRU scores reflect true productivity
- **Endpoints**: `/colibri/status`, `/colibri/health`, `/colibri/throughput`

### TurboQuant (`turboquant-liminal.py`)
- **Port**: 8646
- **Role**: Dynamic KV-cache scaling based on system RAM
- **Caveman coordination**: `predict_kv_cache_size()` accepts `caveman_enabled` — output tokens × 0.35 multiplier
- **Endpoints**: `/tq/status`, `/tq/predict?tokens=4096&model=...&caveman=true`

---

## New Files (V100C)

- **`caveman_middleware.js`** — Token compression middleware. Loads skill prompts, injects into messages, estimates savings, supports runtime level changes.
- **`caveman_levels/`** — Skill prompt files per compression level (`off.txt`, `lite.txt`, `full.txt`, `ultra.txt`, `wenyan.txt`).

---

## Modified Files (V100C)

### `hermes-bridge.js`
- Imports `caveman_middleware`
- Injects caveman skill after RAG context, before provider routing
- New endpoints: `GET /caveman`, `GET /caveman/stats`, `POST /caveman`
- **New proxy routes**: `/nexus/`, `/colibri/`, `/fubbu/`, `/zhark/` — all services now reachable through Hermes
- **Telemetry**: Now fetches FUBBU (:8653), ZHARK (:8654), and RAG (via Nexus :8651/rag/status) in parallel with existing services
- **Alerts**: Amber alerts for FUBBU, ZHARK, and RAG when unreachable
- Caveman + Colibri + VW Nexus added to `/health/all` service list
- New telemetry counters: `caveman_tokens_saved`, `caveman_responses`, `caveman_compression_ratio`

### `vw_deck.cpp`
- **Service definitions**: Added FUBBU Sidecar (:8653) and ZHARK CSO (:8654) to `g_svcDefs[]`
- **TelemetryState struct**: Added `fubbuAlive`, `fubbuTasksActive`, `fubbuTasksDone`, `zharkAlive`, `zharkCycles`, `zharkConvergence`, `ragAlive`, `ragChunkCount`
- **FetchTelemetry()**: Parses FUBBU, ZHARK, and RAG from telemetry JSON
- **Service indicators**: FUBBU, ZHARK, and RAG dots in the deck dashboard

### `vw_deck.config.json`
- New top-level `caveman` config section
- **New tool_registry entries**: `vw_nexus`, `vw_rag`, `fubbu`, `zhark`, `nexus_status`, `caveman_toggle`

### `config/config_loader.py`
- New `caveman` defaults block in `_DEFAULTS`
- `CAVEMAN_ENABLED` and `CAVEMAN_LEVEL` env var overrides
- New `get_caveman_config()` method
- Caveman included in `export_template()`

### `config/setup_wizard.py`
- New Step 5b: interactive compression level selection
- Caveman section added to `.env` template
- ZHARK env vars documented in `.env` template
- Caveman section added to `liminal_lore.yaml` template
- Component check includes ZHARK and FUBBU presence detection

### `turboquant-liminal.py`
- `predict_kv_cache_size()` accepts `caveman_enabled` parameter
- Compressed output = slower KV cache growth (output tokens × 0.35)
- `/tq/predict` endpoint accepts `caveman=true` query param

### `colibri_bridge.py`
- `ExpertState.access()` accepts `caveman_compressed` flag
- Throughput normalized: compressed token count ÷ 0.35 → true productivity
- `/colibri/throughput` POST accepts `caveman_compressed` body field
- LFRU swap decisions now reflect real work, not raw compressed counts

### `build-deck.bat`
- Version string updated to V100C (Caveman Token Compression Edition)

### `SETUP_GUIDE.md`
- New "Caveman Token Compression (V100C)" section with levels, config, runtime toggle, and service coordination docs

---

## Service Coordination

Caveman operates simultaneously with all suite services:

```
User Prompt
    │
    ▼
hermes-bridge.js (:8643)
    │
    ├─ RAG context injection (injectContextIntoMessages)
    │   └─ Queries VW RAG via Nexus (:8651/rag)
    ├─ Caveman skill injection (caveman.injectCaveman)
    │
    ▼
LLM Provider (Ollama / OpenRouter / Hermes / FUBBU Worker)
    │
    ▼
Compressed Response
    │
    ├─ Caveman savings telemetry (tokens_saved, ratio)
    ├─ TurboQuant: KV prediction uses caveman_enabled
    ├─ Colibri: throughput normalized for LFRU
    ├─ FUBBU: Verifier checks output, Router feeds back on reject
    ├─ ZHARK: Causal map updated, hypothesis graph refined
    └─ Nexus: Event bus mirrors all step events
```

### TurboQuant + Caveman
Compressed output means fewer tokens enter the KV cache. `predict_kv_cache_size()` applies a 0.35× multiplier to the output portion.

### Colibri + Caveman
Expert swap decisions use LFRU scores weighted by throughput. Caveman-compressed responses normalize (÷0.35) so LFRU scores reflect true productivity.

### FUBBU + Nexus + RAG
FUBBU's Router queries RAG (via Nexus) for relevant context before decomposing tasks. The Worker uses a larger model for analysis. The Verifier checks structural correctness. All steps are mirrored to the Nexus event bus.

### ZHARK + Gigatoken
ZHARK uses Gigatoken (`shared.token_counter`) for exact token counting in its research loop, with heuristic fallback. Hypothesis convergence is measured by average confidence across the graph.

### Gigatoken
When available, exact token counting replaces the heuristic (len/4) in `caveman_middleware.js`. The middleware accepts an optional `tokenCounter` object with a `count()` method.

---

## Telemetry

### Caveman Counters

| Counter | Description |
|---------|-------------|
| `caveman_tokens_saved` | Cumulative estimated tokens saved across all responses |
| `caveman_responses` | Number of responses processed with caveman active |
| `caveman_compression_ratio` | Running average compression ratio (0-1) |

### Full Suite Telemetry (via `GET /nexus/telemetry`)

| Section | Fields | Source |
|---------|--------|--------|
| `nexus` | alive, agents, tasks, sessions | VW Nexus :8651 |
| `turboquant` | alive, ram_pct, mode | TurboQuant :8646 |
| `ollama` | alive, models | Ollama :11434 |
| `colibri` | alive, hot_expert, cache_hits, experts | Colibri :8648 |
| `fubbu` | alive, active_tasks, completed_tasks | FUBBU :8653 |
| `zhark` | alive, cycles, convergence | ZHARK :8654 |
| `rag` | alive, total_chunks | VW RAG via Nexus :8651 |
| `caveman` | tokens_saved, responses, compression_ratio | In-process |
| `heartbeat` | alive, ram_pressure, notes | Resonance file |

### C++ Deck Dashboard

The `vw_deck.exe` telemetry panel now shows service indicators for:
Bridge, Nexus, TurboQuant, Ollama, Colibri, **FUBBU**, **ZHARK**, **RAG**, Harness, KoboldCpp, Heartbeat, FS Service.

---

## Setup

### New Install
```powershell
cd Modern_X64\tools\Liminal_Lore_V100C\liminal_lore_vw_deck
python -m config.setup_wizard
```

The wizard checks for all components: VW RAG, VW Nexus, VW Deck, ZHARK, and FUBBU.

### Existing Config
Add to `.env`:
```
# Caveman
CAVEMAN_ENABLED=true
CAVEMAN_LEVEL=full

# ZHARK (optional)
ZHARK_MAX_ITERATIONS=50
ZHARK_CONVERGENCE_THRESHOLD=0.75
ZHARK_MAX_DEPTH=5
ZHARK_CYCLE_DELAY=5
ZHARK_VERIFY_CANDIDATES=3

# Nexus (optional)
NEXUS_API_KEY=
LIMINAL_LORE_NEXUS_URL=http://127.0.0.1:8651
```

Or add to `liminal_lore.yaml`:
```yaml
caveman:
  enabled: true
  level: full
  track_savings: true
  compress_context: false
  exclude_roles: ["tool", "function"]
  min_prompt_tokens: 50

nexus:
  url: "http://127.0.0.1:8651"
  api_key_env: "NEXUS_API_KEY"
```

### Starting Services

```powershell
# Core (auto-started by vw_deck.exe)
# Ollama, FS Service, Hermes Bridge, TurboQuant, Runpod Bridge, Colibri, VW Nexus

# Optional (manual start or via deck)
python -m fubbu          # FUBBU sidecar on :8653
python -m zhark          # ZHARK CSO on :8654
python -m vw_rag index   # Build/rebuild RAG index
```

---

## Rollback

To disable Caveman without removing code:

```bash
curl -X POST http://127.0.0.1:8643/caveman \
  -H "Content-Type: application/json" \
  -d '{"enabled": false}'
```

Or set `CAVEMAN_ENABLED=false` in `.env` and restart the bridge.

FUBBU and ZHARK are opt-in (autoStart=false in `vw_deck.cpp`). They don't launch unless explicitly started from the deck or CLI.
