# liminallore
Lux Aura Presents "Liminal Lore" Agentic Toolchain Harness

# Liminal Lore

**Autonomous agentic research and development suite — self-hosted, local-first, zero cloud dependency.**

Liminal Lore is a complete agentic toolchain for long-horizon autonomous research, multi-agent orchestration, and AI-assisted development. It runs entirely on local hardware using open-weight LLMs (via Ollama or any OpenAI-compatible endpoint). No API keys required for core functionality.

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

## License

Apache 2.0 — LuxAura.

---

## Version

**Liminal Lore v100, v100B, v101A* — Build 1
Integrated Tech:
TurboQuant https://github.com/0xsero/turboquant (V100,100B,V101A)
Colibri https://github.com/JustVugg/colibri (V100,100B,V101A)
gigatoken https://github.com/marcelroed/gigatoken (V101A)

---

<div align="center">

**LUX AURA**

*Autonomous agentic systems for the curious.*

[🌐 Bandcamp](https://luxaura.bandcamp.com) · [📘 Facebook](https://facebook.com/LuxAuraOfficial) · [💻 GitHub](https://github.com/luxauraofficial777) · [▶️ YouTube](https://youtube.com/c/LuxAuraOfficial)

</div>
