# liminallore Current Version 1.1B (Fully working build)
<img width="1024" height="576" alt="image" src="https://github.com/user-attachments/assets/a5e277c5-71c9-4bb7-9d10-1467a57f1fb7" />

Lux Aura Presents "Liminal Lore" Agentic Toolchain Harness

# Liminal Lore

**Autonomous agentic research and development suite — self-hosted, local-first, zero cloud dependency.**

Liminal Lore is a complete agentic toolchain for long-horizon autonomous research, multi-agent orchestration, and AI-assisted development. It runs entirely on local hardware using open-weight LLMs (via Ollama or any OpenAI-compatible endpoint). No API keys required for core functionality.

Liminal Lore V1.1A Gamma Language (A2A-DSL): Replaced standard JSON/YAML communication with a native DSL, reducing token consumption by over 65%.  Zero-Copy AST Broadcasting: Replaced legacy WebSockets with a memory-mapped ring buffer (SharedMemoryBus) for zero-serialization IPC between local nodes and Python services.  GPU Vector Acceleration: Added Vulkan-based compute shaders to GPUComputeEngine to offload vector and Cosine Similarity calculations to the GPU.  Cross-Module AST Memoization: Implemented AST_Memoizer to eliminate redundant lexing cycles across modules.  Toolchain Integration: Updated Colibri MoE to support Gamma arrays and probabilistic expert pinning, while integrating Caveman compression for byte-exact state shifts.  Gamma AST Terminal: Added a dedicated GUI telemetry tab to monitor live AST packets flowing through shared memory in real time.  Performance Validations: Verified 0 dynamic heap allocations on the C++ hotpath and reached parser throughput exceeding 40,000 evaluations per second.  Liminal Lore Deck V1.1BDeck Rebranding: Renamed all "VOID WALKERS" references to "Liminal Lore Deck" across C++ window titles, menu bars, HTML headers, configuration files, and build scripts.  Caveman Middleware Diagnostics & Testing:Added comprehensive diagnostic logging throughout the compression and message injection pipeline.  Introduced the GET /caveman/test endpoint to inspect runtime settings, message modifications, and skill payloads.  Added a Caveman toggle and level selector to the DECK view toolbar, alongside an amber header status indicator (CAVEMAN: FULL / LITE / OFF).  Fixed a caching bug where empty skill files were permanently cached and blocked subsequent reloads.  System & Service Fixes:Fixed the FS service "directory outside allowed path" error by allowing path resolution to fall back to workspace project roots.  Corrected the VW Nexus menu shortcut in vw_deck.cpp to call service index 7 instead of Colibri Bridge (index 5).  Enhanced StartSvc() error reporting to output native Windows error codes via GetLastError().  Binary Build: Recompiled and verified vw_deck.exe using MinGW-W64. Added GammaLanguage 1.1A support.

What's New in Liminal Lore V1.1A 🚀
Welcome to Liminal Lore V1.1A, the most significant architectural evolution for the VoidWalkers Agentic toolchain to date. This release introduces the Gamma Language Integration, replacing bloated JSON payloads with a token-optimal, native machine language designed explicitly for silicon and AI agents.

Here is what's new and improved in this major release:

⚡ Core Engine: The Gamma Language
IMPORTANT

All inter-agent communication, tool calls, and LLM context streaming have migrated to the Gamma Language (A2A-DSL), yielding a >65% reduction in token consumption over standard JSON/YAML.

Zero-Copy AST Broadcasting (IPC)
Memory-Mapped Ring Buffers: Replaced legacy WebSockets with the new SharedMemoryBus (Local\VW_Nexus_AST_Bus). ASTNode structures are transferred instantly across local agent nodes via shared RAM with absolute zero serialization overhead.
Python Bridge Support: The Python VWNexusService now binds directly to the memory-mapped bus, providing high-throughput IPC natively to the Python layer.
GPU Compute Vector Operations
Vector evaluations and Cosine Similarity checks (#VEC, COSINE_SIM) within the Gamma parser are now heavily accelerated.
Introduced Vulkan-based Compute Shaders in the GPUComputeEngine to offload deep tensor math directly to the GPU without stalling the main execution thread.
Cross-Module AST Memoization
Added the AST_Memoizer to eliminate redundant lexing across different modules.
State chunks (such as @INVARIANT guards verified by FUBBU) can now safely share pointers with ZHARK instances directly in memory.
🧠 Toolchain Adaptations
Colibri MoE: Probabilistic Routing
The Colibri layer's routing logic (routing.py) now inherently understands Gamma Arrays.
Agents can specify probabilistic expert pinning (e.g. &PIN_EXPERT([("nex", 0.7), ("lux", 0.3)])), preparing the toolchain for simultaneous, blended inference environments.
Caveman Compression
Integrated tight with Gamma syntax, stripping conversational filler, preamble, and excessive indentation to guarantee 100% byte-exact state shifts with minimal context bloat.
🖥️ Deck GUI Enhancements
TIP

Use the new Gamma AST Terminal to monitor active data pipelines.

Gamma AST Terminal Tab: Added a dedicated telemetry terminal to the VoidWalkers Chat GUI.
Operators can now visualize the live stream of zero-copy AST packets as they flow through the memory-mapped ring buffer in real-time.
🛠️ Performance Validations
Zero-Heap Allocation: The C++ Hotpath has been fully validated for 0 dynamic heap allocations per parsing cycle.
Throughput: End-to-end testing confirms parser throughput exceeding ~40,000 evaluations per second on local environments.
Thank you for diving into V1.1A! Happy developing in the Void.

---

## What's In the Box

Liminal Lore is not a single application — it's a **suite of five interoperable tools** that work together as a research and development platform:

| Tool | Role | Port(s) |
|------|------|---------|
| **VW Nexus** | Universal orchestration layer — agent registry, task queue, event bus, RAG | 8651 (HTTP), 8652 (WS) |
| **VW RAG** | Retrieval-augmented generation engine — FTS5 + optional vector search | (via Nexus) |
| **ZHARK** | Autonomous long-horizon research engine — Virtual CSO | (CLI-driven) |
| **FUBBU** | Local multi-agent sidecar — Router-Worker-Verifier loop | (via Nexus) |
| **VW Deck** | GUI + bridge — chat interface, agent control deck, file services | 8643-8647 |

```
                    ┌──────────────┐
                    │   VW Deck    │
                    │  (GUI +      │
                    │   Bridge)    │
                    └──────┬───────┘
                           │ HTTP/WS
                    ┌──────▼───────┐
                    │   VW Nexus   │
                    │ (Orchestrator│◄──── FUBBU Sidecar
                    │  + Task Queue│       (Router-Worker-
                    │  + Event Bus)│        Verifier)
                    └──────┬───────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
        ┌─────▼─────┐ ┌───▼────┐  ┌────▼─────┐
        │  VW RAG   │ │ ZHARK  │  │  Agents  │
        │ (Retrieval)│ │(Virtual│  │ (Cascade,│
        │           │ │  CSO)  │  │  Ollama, │
        └───────────┘ └────────┘  │  etc.)   │
                                  └──────────┘
```

---

## Tool Summaries

### VW Nexus — Universal Orchestration Layer

The central nervous system of the suite. VW Nexus provides:

- **Agent Registry** — Tracks all active agents with heartbeats, capabilities, and state
- **Task Queue** — DAG-dependency-aware task scheduling with parallel execution
- **Event Bus** — Inter-agent pub/sub messaging
- **MCP Server** — Model Context Protocol tool exposure for IDE integration
- **State Store** — SQLite-backed persistent state for cycles, sessions, telemetry
- **Lock Manager** — File-level locking to prevent agent conflicts
- **Session Persistence** — Resumable agent sessions with checkpoint/restore
- **WebSocket Server** — Real-time push notifications for deck UI
- **Remote Bridge** — Connects to remote Nexus instances for distributed work

**30+ Python modules** covering API, agent identity, committee consensus, governance, metrics, telemetry export, and more.

**CLI:** `python -m vw_nexus --port 8651`

### VW RAG — Retrieval-Augmented Generation

A hybrid retrieval engine that combines:

- **SQLite FTS5** — BM25 keyword search (always available, no GPU needed)
- **Optional vector similarity** — sentence-transformers embeddings (graceful fallback to keyword-only)
- **Reciprocal rank fusion** — Merges keyword + vector results with recency boost
- **Multi-corpus support** — Separate indices for different knowledge domains
- **Graph index** — Relationship-aware retrieval across project components
- **Auto-tagging** — 15 topic categories for filtered retrieval

Indexes files, source code, documentation, and study materials. Queryable via Nexus API or CLI.

**CLI:** `python -m vw_rag index|query|status`

### ZHARK — Autonomous Long-Horizon Research Engine

Our domestic equivalent to Sakana Marlin. ZHARK acts as a **Virtual CSO**, running multi-hour investigation cycles autonomously:

- **Hypothesis Graph** — DAG of testable hypotheses with weighted evidence, contradiction detection, and confidence scoring
- **Causal Mapper** — Extracts causal relationships (direct, mediated, confounded, feedback, inhibitor) and identifies leverage points
- **Strategy Synthesizer** — Converts findings into executive-ready options with risk level, timeframe, confidence, and leading indicators
- **Verification Buffer** — Inference-time compute scaling: generates multiple candidates, scores consistency, tracks convergence across revisions
- **Web Gatherer** — Autonomous DuckDuckGo search + content extraction (no API key needed)
- **State Management** — SQLite checkpoint/resume every cycle for multi-session continuity

**Orchestration loop:** `HYPOTHESIS → GATHER → ANALYZE → VERIFY → CONTRADICT → REFINE → CAUSAL → SYNTHESIZE`

A full investigation runs 50 iterations × ~8 min = **~6.5 hours autonomous**, then produces a markdown + JSON strategy report.

**CLI:** `python -m zhark run "Your research question"`

### FUBBU — Fully Unmanaged Backbone Bridge Unit

A self-hosted local multi-agent sidecar that runs as an independent OS process alongside VW Nexus. Implements a **Router-Worker-Verifier loop** using local open-weight models:

- **Router** (qwen2.5-coder:7b) — Task decomposition + RAG context retrieval + worker assignment
- **Worker** (deepseek-coder:6.7b) — Heavy inference: MIPS analysis, binary diffing, code review
- **Verifier** (qwen2.5-coder:7b) — Structural hard-gate checks + advisory soft-gate confidence scoring

**Process isolation:** Runs as subprocess, never imported into Nexus. Separate SQLite DBs. File lock prevents dual instances. All Nexus communication via HTTP API only.

**Pipeline:** Nexus → Router (decompose) → RAG (context) → Worker (execute) → Verifier (check) → Nexus (verified result). Failed verification sends feedback to Worker for retry.

**CLI:** `python -m fubbu start|status|topology|test-connection`

### VW Deck — GUI + Bridge + Config System

The user-facing layer. Three components:

**Hermes Bridge** (`hermes-bridge.js`) — Node.js HTTP server that proxies LLM requests, manages file system access, and bridges between the GUI and backend services. Supports Ollama, OpenAI-compatible, and OpenRouter providers.

**Chat GUI** (`VoidWalkers_Chat_GUI.html`) — Full-featured web UI with:
- Chat interface with streaming responses
- Agent Control Deck — live agent cards, task DAG, file locks, RAG query, event feed
- Theme system (void, ice, ember)
- Hardware tier auto-detection
- VPN monitoring integration

**Config System** (`config/`) — Layered configuration:
- `config_loader.py` — `.env` → YAML → defaults resolution with `${project_root}` variable expansion
- `provider_abstraction.py` — Dynamic LLM provider detection (Ollama, OpenAI-compat, OpenRouter, custom)
- `setup_wizard.py` — Interactive setup, validation, and shipping finalization (`--ship` mode)

**Electron app** (`electron/`) — Desktop wrapper with preload script, package.json, and all configs.

**Native deck** (`vw_deck.cpp`) — C++ source for native desktop variant (compile via `build-deck.bat`).

---
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


## Quick Start

### Prerequisites

- **Python** >= 3.11
- **Node.js** >= 18
- **Ollama** (recommended) — `ollama serve` + `ollama pull qwen2.5-coder:7b`
- **C++ compiler** (optional, for native deck) — MSVC or MinGW

### Install

```powershell
cd liminal_lore_build_v1

# 1. Install Python packages
pip install -e .

# 2. Run setup wizard
cd liminal_lore_vw_deck
python -m config.setup_wizard

# 3. (Optional) Install Electron deps
cd electron
npm install
```

### Run

```powershell
# Terminal 1: Start VW Nexus
python -m vw_nexus

# Terminal 2: Start Hermes bridge
node liminal_lore_vw_deck\hermes-bridge.js

# Terminal 3: Open GUI
# → Open liminal_lore_vw_deck\VoidWalkers_Chat_GUI.html in browser
#   OR: cd liminal_lore_vw_deck\electron && npm start

# Terminal 4: (Optional) Start FUBBU sidecar
python -m fubbu start

# Terminal 5: (Optional) Run ZHARK research
python -m zhark run "What are the key technology trends for 2026?"
```

---

## Configuration

### Layered Config System

Configuration is resolved in priority order:

1. **`.env`** — Environment variables (API keys, URLs, project root)
2. **`liminal_lore.yaml`** — User config (providers, models, bridge settings)
3. **Defaults** — Built-in sensible defaults

**API keys are NEVER stored in config files** — only the *name* of the environment variable is stored. The `.env` file holds the actual secrets and should never be committed to version control.

### Key Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PROJECT_ROOT` | (required) | Absolute path to your project root |
| `LIMINAL_LORE_ACTIVE_PROVIDER` | `ollama` | LLM provider: `ollama`, `openai_compatible`, `openrouter`, `custom` |
| `LIMINAL_LORE_ACTIVE_MODEL` | (provider default) | Model name to use |
| `OLLAMA_BASE_URL` | `http://127.0.0.1:11434` | Ollama API URL |
| `NEXUS_API_KEY` | (optional) | VW Nexus authentication key |
| `ZHARK_MAX_ITERATIONS` | `50` | ZHARK max research cycles |
| `ZHARK_CONVERGENCE_THRESHOLD` | `0.75` | ZHARK stop threshold |

### Setup Wizard Commands

```powershell
python -m config.setup_wizard               # Interactive setup
python -m config.setup_wizard --check       # Validate config only
python -m config.setup_wizard --generate    # Generate template files
python -m config.setup_wizard --ship        # Shipping finalization check
```

---

## Supported LLM Providers

| Provider | API Format | Auth Required | Default URL |
|----------|-----------|---------------|-------------|
| **Ollama** | Native | No | `http://127.0.0.1:11434` |
| **OpenAI-compatible** | `/v1/chat/completions` | Optional | User-defined |
| **OpenRouter** | `/chat/completions` | Yes | `https://openrouter.ai/api/v1` |
| **Custom** | Auto-detect | Optional | User-defined |

Provider is auto-detected from the base URL. Ollama (port 11434) uses native API. OpenRouter uses `/chat/completions`. Everything else uses OpenAI-compatible `/v1/chat/completions`.

---

## Architecture at a Glance

```
liminal_lore_build_v1/
├── pyproject.toml                  # Python project + CLI entry points
├── tool_manifest.yaml              # Tool registry
├── BUILD_INSTALL.md                # Build/install instructions
├── README.md                       # This file
│
├── zhark/                          # ZHARK Virtual CSO (10 files)
├── vw_nexus/                       # VW Nexus orchestrator (32 files)
├── vw_rag/                         # VW RAG engine (9 files)
├── fubbu/                          # FUBBU sidecar (12 files)
├── liminal_lore_vw_deck/           # VW Deck GUI + bridge + config (40 files)
│   ├── VoidWalkers_Chat_GUI.html   # Main web UI
│   ├── hermes-bridge.js            # HTTP bridge server
│   ├── vw_deck.cpp                 # Native C++ deck source
│   ├── config/                     # Config system (4 modules)
│   └── electron/                   # Electron desktop app
└── mcp_server/tools/               # MCP YAML tool definitions (16 files)
```

---

## Data Flow

```
User question
    │
    ▼
VW Deck (GUI) ──HTTP──► Hermes Bridge ──► LLM Provider (Ollama)
    │                                       │
    │                                       ▼
    │                                    Response
    │                                       │
    ├──► VW Nexus ◄── Agent registration ──┤
    │      │                                │
    │      ├── Task Queue (DAG)             │
    │      ├── Event Bus (pub/sub)          │
    │      ├── RAG Query ──► VW RAG         │
    │      ├── Lock Manager                 │
    │      └── Telemetry                    │
    │                                       │
    ├──► FUBBU Sidecar                      │
    │      Router → Worker → Verifier ──────┘
    │      (claims tasks, runs inference,
    │       verifies, posts results back)
    │
    └──► ZHARK (autonomous)
           Hypothesis → Web Search → Analyze
           → Verify (N candidates) → Causal Map
           → Strategy Synthesis → Report
```

---

## Security

- **No hardcoded API keys** — credentials only in `.env`, never in config files or source
- **No hardcoded paths** — all paths use `${project_root}` variable resolution
- **File access allowlist** — VW Deck only accesses files under `PROJECT_ROOT`
- **Execution safety** — configurable allowed/blocked command patterns
- **VPN disabled by default** — opt-in only for public release
- **Nexus API auth** — optional `NEXUS_API_KEY` for API authentication

---

## CLI Reference

```powershell
# VW Nexus
python -m vw_nexus                              # Start orchestrator (port 8651)
python -m vw_nexus --port 8651 --ws-port 8652   # Custom ports

# VW RAG
python -m vw_rag index                          # Index project files
python -m vw_rag query "search term"            # Query the index
python -m vw_rag status                         # Show index stats

# ZHARK
python -m zhark run "research question"         # Run autonomous research
python -m zhark run "question" --max-iterations 100  # Custom iterations
python -m zhark status                          # Show current status
python -m zhark cycles                          # List all cycles
python -m zhark resume <cycle_id>               # Resume interrupted cycle
python -m zhark report <cycle_id>               # View cycle report

# FUBBU
python -m fubbu start                           # Start sidecar
python -m fubbu status                          # Show sidecar status
python -m fubbu topology                        # Export topology graph
python -m fubbu test-connection                 # Test Nexus + Ollama

# Config
python -m config.setup_wizard                   # Interactive setup
python -m config.setup_wizard --check           # Validate
python -m config.setup_wizard --generate        # Generate templates
python -m config.setup_wizard --ship            # Shipping check
```

---

# Liminal Lore V101A — Gigatoken Enhanced

> **Liminal Lore** is a self-hosted, agentic game development suite with multi-agent
> orchestration, local LLM inference, and pipeline automation.

## What's New in V101A

### Gigatoken Integration
- **Shared token counter** (`shared/token_counter.py`) with gigatoken → tiktoken → heuristic fallback chain
- **Exact token counting** across all components: RAG chunker, LLM provider, Colibri bridge, TurboQuant, ZHARK, FUBBU
- **Pre-flight token checks** in FUBBU Worker, Router, and Verifier with automatic prompt truncation
- **KV cache prediction** in TurboQuant based on token counts
- **Throughput-aware expert swapping** in Colibri Bridge

### New GUI Tabs
- **ZHARK Tab**: Autonomous research orchestrator panel with:
  - Research question display
  - Hypothesis graph with status tracking (proposed, testing, confirmed, refuted)
  - Cycle progress visualization
  - Evidence and contradiction feed
  - Strategic options panel
  - Causal map
  - Event log
  - Start/stop cycle controls

- **Global Telemetry Tab**: System health dashboard with:
  - Token throughput (aggregate + per-expert tokens/sec)
  - Tokenizer engine display (gigatoken backend)
  - RAM usage with warning/critical thresholds
  - Pagefile usage bars
  - KV cache prediction from TurboQuant
  - Colibri expert metrics (hot experts, cache hits/misses, swap events)
  - Provider status (active provider, model, context window, prompt tokens)
  - FUBBU worker stats (active, completed, failed)
  - Nexus agent counts (registered agents, active tasks, file locks)

### GUI Modernization
- Version updated to v1.01A
- Context gauge now visible in status bar (powered by gigatoken)
- Tokenizer badge added to status bar
- Suite version updated to v1.01A
- All "Liminal Link" references replaced with "Liminal Lore" (trademark compliance)

## Directory Structure

```
Liminal_Lore_V101A/
├── README.md                          # This file
├── docs/
│   └── GIGATOKEN_INTEGRATION_SPEC.md  # Full gigatoken integration specification
├── shared/
│   └── token_counter.py               # Shared token counter (gigatoken → tiktoken → heuristic)
├── liminal_lore_vw_deck/              # Main Liminal Lore application
│   ├── VoidWalkers_Chat_GUI.html      # Web portal GUI (v1.01A with ZHARK + Telemetry tabs)
│   ├── VoidWalkers_Suite.html         # Electron suite HTML
│   ├── hermes-bridge.js               # Hermes API bridge server
│   ├── colibri_bridge.py              # Colibri expert streaming bridge
│   ├── turboquant-liminal.py          # TurboQuant KV cache manager
│   ├── vw_nexus_service.py            # VW Nexus HTTP service
│   ├── vw_deck.cpp                    # Native Win32 deck host
│   ├── vw_deck.config.json            # Deck configuration
│   ├── liminal_settings.json          # FS service settings
│   ├── SETUP_GUIDE.md                 # Setup & extension guide
│   ├── config/                        # Configuration package
│   │   ├── config_loader.py           # Layered config loader
│   │   ├── provider_abstraction.py    # LLM provider abstraction
│   │   ├── setup_wizard.py            # Interactive setup wizard
│   │   └── __init__.py
│   ├── electron/                      # Electron desktop wrapper
│   ├── colibri/                       # Colibri C/Rust core
│   ├── scripts/                       # Utility scripts
│   └── data/                          # Runtime data
├── vw_nexus/                          # VW Nexus agent orchestration
├── vw_rag/                            # RAG engine (FTS5 + vector search)
├── zhark/                             # ZHARK autonomous research orchestrator
├── fubbu/                             # FUBBU router-worker-verifier system
└── docs/                              # Documentation
```

## Key Ports

| Service         | Port  | Protocol |
|-----------------|-------|----------|
| Hermes Bridge   | 8643  | HTTP     |
| TurboQuant      | 8646  | HTTP     |
| Colibri Bridge  | 8648  | HTTP     |
| VW Nexus        | 8651  | HTTP     |
| VW Nexus WS     | 8652  | WebSocket|

## Quick Start

1. **Install dependencies**:
   ```powershell
   pip install gigatoken tiktoken
   ```

2. **Run the setup wizard**:
   ```powershell
   cd liminal_lore_vw_deck
   python -m config.setup_wizard
   ```

3. **Start the Hermes bridge**:
   ```powershell
   cd liminal_lore_vw_deck
   node hermes-bridge.js
   ```

4. **Open the GUI**:
   Navigate to `http://127.0.0.1:8643` in your browser

## Gigatoken Integration Points

| Component         | Integration                                      |
|-------------------|--------------------------------------------------|
| RAG Chunker       | Token-based chunking replaces char-based         |
| LLM Provider      | `count_tokens`, `fits_in_context`, `budget_for_context` |
| Colibri Bridge    | Throughput tracking, throughput-aware swaps      |
| TurboQuant        | KV cache prediction from token counts            |
| ZHARK             | Prompt budgeting with `_budget_prompt`           |
| FUBBU Worker      | Pre-flight token check + prompt truncation       |
| FUBBU Router      | Pre-flight token check + prompt truncation       |
| FUBBU Verifier    | Pre-flight token check + prompt truncation       |

## Token Counter Fallback Chain

```
gigatoken (Rust, fastest)
    ↓ (if unavailable)
tiktoken (Python, BPE)
    ↓ (if unavailable)
heuristic (len(text) // 4)
```

MPORTANT --- VECTORDB not included in V101A. DB must be rebuilt:

markdown
## Rebuilding the RAG Index
 
The RAG index databases (`*.db`) are excluded from this repository due to size.
Rebuild them locally after cloning:
 
```powershell
cd vw_rag
python -m vw_rag index --root ../liminal_lore_vw_deck
This indexes all source files into SQLite FTS5 tables for keyword search. Indexing ~866 files / ~46,700 chunks takes approximately 2 minutes.

For multi-corpus indexing (separate indexes per project area):




powershell
python -m vw_rag index --multi-corpus
Verify index status:




powershell
python -m vw_rag status
Optional: Vector Similarity Search
For semantic search (in addition to keyword search), install sentence-transformers:




powershell
pip install sentence-transformers
Then rebuild the index — vector embeddings will be generated automatically. Without this package, the RAG engine falls back to keyword-only search (BM25).
## License

Apache 2.0 — Lux Aura - Liminal Lore - VoidWalkers Project

---

## Version

**Liminal Lore v100, v100B, v101A, v101B, v101C* — Build 1
Integrated Tech:
TurboQuant https://github.com/0xsero/turboquant (V100A,100B,V101A,V101B)
Colibri https://github.com/JustVugg/colibri (V100A,100B,V101A,V101B)
gigatoken https://github.com/marcelroed/gigatoken (V101A,V101B)
Caveman https://github.com/juliusbrussee/caveman (V101C)

---

<div align="center">

**LUX AURA**

*Autonomous agentic systems for the curious.*

[🌐 Bandcamp](https://luxaura.bandcamp.com) · [📘 Facebook](https://facebook.com/LuxAuraOfficial) · [💻 GitHub](https://github.com/luxauraofficial777) · [▶️ YouTube](https://youtube.com/c/LuxAuraOfficial)

</div>
