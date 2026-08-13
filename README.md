# liminallore Current Version 1.1C (Fully working build)
<img width="1024" height="576" alt="image" src="https://github.com/user-attachments/assets/03a358cf-3bb5-4be6-ad91-765516237d46" />


Lux Aura Presents "Liminal Lore" Agentic Toolchain Harness Current Build 1.1B-GammaLanguageV1.1A-Robust
# Liminal Lore
**Autonomous agentic research and development suite — self-hosted, local-first, zero cloud dependency.**
Liminal Lore is a complete agentic toolchain for long-horizon autonomous research, multi-agent orchestration, and AI-assisted development. It runs entirely on local hardware using open-weight LLMs (via Ollama or any OpenAI-compatible endpoint). No API keys required for core functionality.

# Liminal Lore Release Document
## Version: v1.21A — August 13, 2026

**Author:** Lux Aura / Antigravity Suite  
**Target Architecture:** VoidWalkers Agentic Control Deck, Modern_X64  

---

### Executive Summary

**Liminal Lore v1.21A** is a production-ready, fully verified architecture update for the VoidWalkers agentic suite. It vendor-consolidates the toolchain modules (HAZ-01), integrates natively with **GammaLanguage v1.1C**, embeds the **Liminal Link 4-gate operations manager**, resolves Python 3.10/3.11 compatibility bugs in `setup_wizard.py`, and arms the VoidWalkers Chat GUI with live zero-copy AST & MAG telemetry.

---

### Key Technical Upgrades in v1.21A

1. **HAZ-01 Tool Fork Elimination & Clean Vendoring:**
   - Consolidated 15 historical tool forks across legacy build paths (`liminal_lore_build_v1`, `V1.1C`, `V100B`, `V101B`, `V101C`).
   - Vendored self-contained, canonical copies of `fubbu`, `zhark`, and `vw_nexus` directly within the suite directory, removing legacy import dependencies.

2. **GammaLanguage v1.1C Native Pipeline Integration:**
   - Full AST opcode dispatch for `?ZHARK`, `¿FUBBU_SYNC`, `~MCP`, `$MEM_QUERY`, `!MEM_COMMIT`, `@ON_ERROR`, and `^GIGATOKEN_PACK`.
   - Inline Caveman prompt compression (38.5% measured token reduction) on active worker LLM call sites.

3. **`setup_wizard.py` Python Pre-3.12 Syntax Hardening:**
   - Fixed nested double-quote syntax errors inside f-strings (`f"{info.get('status')}"`) at lines 667 and 675 of `setup_wizard.py`.
   - Restored zero-error execution for Python 3.10 and 3.11 environments.

4. **Liminal Link Operations Manager Integration (`liminal_link.py`):**
   - Verified the fallback resolution chain in `liminal_link.py`. `GAMMA_ROOT` cleanly resolves to the suite's internal GammaLanguage engine.
   - Monitors 4-gate verification status: C++ Suite (94 tests), Python E2E (93 tests), Rust FFI (5 tests), and Conformance (19/19 agreed).

5. **VoidWalkers Chat GUI Deck Upgrade (`VoidWalkers_Chat_GUI.html`):**
   - Added **Gamma AST & MAG Memory Inspector** tab with real-time zero-copy IPC packet stream visualization (`Local\VW_Nexus_AST_Bus`).
   - Integrated live token budget allocation gauges (60% Prompt / 25% RAG / 15% MAG).

---

### Verification Matrix

- **Setup Wizard Integrity:** `setup_wizard.py` executes without syntax errors on Python 3.10–3.12.
- **Liminal Link Fallback:** `liminal_link.py` resolves `GAMMA_ROOT` cleanly from within the suite.
- **Canonical Tool Integrity:** Vendored `fubbu`, `zhark`, and `vw_nexus` modules execute in isolation without missing dependencies.
- **Cross-Implementation Parity:** 19/19 corpus scripts agree between C++ AST VM and Python host.
- **GUI Telemetry:** HTML5 Deck correctly renders live zero-copy AST stream and MAG memory nodes.




Liminal Lore V1.1C — Autonomous Agentic SuiteLiminal Lore is a self-hosted, agentic development suite designed for multi-agent orchestration, local LLM inference, and pipeline automation. Version 1.1C brings full GammaLanguage v1.1C (A2A-DSL) integration across all core subsystems, enabling zero-copy token-optimal execution, bare-metal hardware interop, and directive routing.

⚡ Key Highlights in V1.1CGammaLanguage v1.1C Protocol: Native A2A-DSL integration across FUBBU, ZHARK, VW Nexus, and the C++ Deck host.  Caveman Token Compression: Reduces token overhead by ~65% across all LLM call sites while keeping commands and code byte-exact.  Gigatoken Fallback Engine: Shared counter featuring a gigatoken (Rust) → tiktoken → heuristic execution chain.  TurboQuant Pre-Flight Guard: Performs RAM pressure capacity checks before inference to dynamically adjust context bounds and prevent OOM errors.  Directive Routing & Hive Partitions: Intercepts and routes ?ZHARK research tasks and ¿FUBBU sidecar directives via #HIVE partitioned WebSocket channels.  

🌐 Network Ports & Service MapService NamePortProtocolOperational ScopeHermes Bridge8643  HTTP  Core API & Web Portal REST interface  TurboQuant8646  HTTP  KV-cache RAM capacity manager  Colibri Bridge8648  HTTP  MoE expert streaming & swapping router  VW Nexus Server8651  HTTP  Agent orchestration & status endpoint  VW Nexus Bus8652  WebSocket  Real-time AST frame & directive routing  

⚙️ New Hardware & FFI OpcodesV1.1C introduces five silicon-level opcodes to the C++ interpreter (A2A_Gamma.h):  RUST_OFFSET_LOAD (0xA0): Loads zero-copy memory struct offsets via repr(C) bitfields.  RUST_FFI_CALL (0xA1): Dispatches execution through PEAK function pointer tables.  ALIGN_BOUND (0xA2): Sets strict alignment boundaries for memory layout parity.  CP0_REG_MAP (0xA3): Binds CyberGrime MIPS assembly CP0 registers to HW[0..7] banks.  PSX_DMA_DISPATCH (0xA4): Routes PSXMatrix DMA channels with hardware interrupt triggers.  

🚀 Quick Start Guide1. InstallationInstall core dependencies:PowerShellpip install gigatoken tiktoken

V1.1C (August 10–11, 2026)Full-Suite A2A-DSL Integration: Brought full GammaLanguage v1.1C compatibility across FUBBU, ZHARK, VW Nexus, and the C++ Deck host (A2A_Gamma.h).  Universal Caveman Compression: Applied Caveman prompt compression across all 8 LLM call sites in FUBBU and ZHARK.  TurboQuant Pre-Flight Guard: Integrated RAM pressure capacity checks into FUBBU Worker to dynamically scale context bounds and prevent OOM errors.  Directive & Hive Routing: VW Nexus WebSocket bus support for routing ?ZHARK research directives, ¿FUBBU task decomposition sigils, and #HIVE partitioned channels.  New Hardware & FFI Opcodes:RUST_OFFSET_LOAD (0xA0): Zero-copy repr(C) Rust memory struct offset loading.  RUST_FFI_CALL (0xA1): Execution dispatch through PEAK function pointer tables.  ALIGN_BOUND (0xA2): Strict memory alignment boundary enforcement.  CP0_REG_MAP (0xA3): Direct mapping of CyberGrime MIPS assembly CP0 registers to HW[0..7] banks.  PSX_DMA_DISPATCH (0xA4): Hardware interrupt-triggered PSXMatrix DMA channel routing.  Setup Wizard Step 5c: Added an interactive step for configuring GammaLanguage toggles and writing environment variables.  Config & Manifest System: Added the gamma schema section with env var overrides and expanded release validation checks to include FUBBU and GammaLanguage components.  

V1.1A-B (August 9, 2026)Caveman Diagnostic Tools: Added comprehensive pipeline logging, diagnostic state outputs, and the /caveman/test endpoint.  Toolbar & Status Toggles: Added a Caveman level toggle and checkbox directly to the DECK view toolbar, alongside a header indicator.  Error & Service Instrumentations: Enhanced StartSvc() error reporting using native Windows GetLastError() formatting.  Core Fixes: Corrected FS service path resolution, fixed skill loading empty-cache lockouts, and repaired the VW Nexus menu service shortcut index.  Branding Rename: Completed naming migration from "VOID WALKERS" to "Liminal Lore Deck" across all C++, HTML, and config files.  V1.1A (July/August 2026)Gamma Language Engine (A2A-DSL): Replaced JSON/YAML inter-agent payloads with native sigil-based A2A-DSL, yielding >65% token reductions.  Zero-Copy AST IPC: Implemented memory-mapped ring buffers (SharedMemoryBus) for zero-allocation ASTNode transfers between local agents.  GPU Compute Vector Acceleration: Integrated Vulkan-based compute shaders (GPUComputeEngine) for accelerated #VEC embedding math and SIMD Cosine Similarity calculations.  AST Memoization: Added AST_Memoizer to enable direct cross-module AST pointer sharing without re-lexing.  Colibri Probabilistic Expert Routing: Enabled expert array pinning for blended, multi-expert inference environments.  Gamma AST Terminal: Added a live AST packet stream monitor to the Chat GUI.  

V101ABC (July 25, 2026)Gigatoken Integration: Added shared token counter (shared/token_counter.py) with a three-tier fallback chain (gigatoken → tiktoken → heuristic).  Exact Token Budgeting: Wired exact token counting and pre-flight prompt checks into RAG Chunker, LLM Provider, Colibri Bridge, TurboQuant, ZHARK, and FUBBU Worker/Router/Verifier.  ZHARK GUI Tab: Dedicated autonomous research orchestrator panel featuring hypothesis graph visualizations, cycle progress tracking, strategic option panels, and causal mapping.  Global Telemetry GUI Tab: System health dashboard monitoring token throughput, RAM & KV cache usage, Colibri expert swaps, provider status, FUBBU workers, and Nexus agents.  Status Bar Updates: Made the Gigatoken Context Usage gauge visible and added a GIGATOKEN status badge.  Compliance & Path Standardization: Updated all instances of "Liminal Link" to "Liminal Lore" across code, menus, and path structures.  V100B (July 25, 2026)Baseline Architecture: Initial snapshot providing the core Liminal Lore framework, configuration system, LLM provider abstraction, RAG engine, agent identity layer, and Control Deck UI.  

### 2. Configuration
Launch the interactive setup wizard (includes Step 5c for GammaLanguage feature toggles):
```powershell
cd liminal_lore_vw_deck
python -m config.setup_wizard
```[cite: 4]

### 3. Launch Services
Start the Hermes Bridge server[cite: 4]:
```powershell
cd liminal_lore_vw_deck
node hermes-bridge.js
```[cite: 4]

### 4. Access UI
Navigate to `[http://127.0.0.1:8643](http://127.0.0.1:8643)` in your browser to access the control deck, ZHARK research portal, and telemetry dashboards[cite: 4, 5].

---

## 📁 Repository Structure

Liminal_Lore_V1.1C/
├── liminal_lore_vw_deck/     # Main C++ Deck Host, GUI, and Bridges
│   ├── VoidWalkers_Chat_GUI.html # Web Portal Control Deck
│   ├── hermes-bridge.js          # API Bridge Server
│   ├── vw_deck.cpp               # Win32 Host Executable
│   └── gamma/A2A_Gamma.h         # Inline A2A-DSL Interpreter
├── fubbu/                        # Context Containment & Task Decomposer
├── zhark/                        # Autonomous Research Orchestrator
├── vw_nexus/                     # WebSocket Directive Bus & RAG Router
└── shared/                       # Token Counters & Shared Win32 Headers

**Liminal Lore v100, v100B, v101A, v101B, v101C* — Build 1
Integrated Tech:
TurboQuant https://github.com/0xsero/turboquant (V100A,100B,V101A,V101B)
Colibri https://github.com/JustVugg/colibri (V100A,100B,V101A,V101B)
gigatoken https://github.com/marcelroed/gigatoken (V101A,V101B)
Caveman https://github.com/juliusbrussee/caveman (V101C
GammaLanguage https://github.com/luxauraofficial777/gammalanguage (V1.1ABC)

---

<div align="center">

**LUX AURA**

*Autonomous agentic systems for the curious.*

[🌐 Bandcamp](https://luxaura.bandcamp.com) · [📘 Facebook](https://facebook.com/LuxAuraOfficial) · [💻 GitHub](https://github.com/luxauraofficial777) · [▶️ YouTube](https://youtube.com/c/LuxAuraOfficial)

</div>
