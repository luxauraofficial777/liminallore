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
