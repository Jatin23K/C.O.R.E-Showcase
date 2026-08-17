# Technical Architecture — C.O.R.E.

## 1. System Overview

C.O.R.E. is designed as a **sovereign, multi-device personal operating ecosystem** — a system that belongs entirely to one person, runs on their hardware, communicates over an encrypted mesh, and isolates private memory from public knowledge.

The architecture is governed by three non-negotiable engineering principles:

1. **Data Sovereignty** — No personal conversations, daily memory, or encryption keys are routed through third-party platforms.
2. **Capability Self-Awareness & Graceful Degradation** — Every satellite node operates autonomously offline and delegates heavy compute to the central brain based on hardware capability thresholds.
3. **Model Independence** — Application logic is fully decoupled from LLM providers via a provider-agnostic adapter layer.

---

## 2. Network Topology: Hub-and-Spoke Mesh

```
═════════════════════════════════════════════════════════════════════════════════════════
                         HARDWARE NODES (Tailscale Encrypted Mesh)
═════════════════════════════════════════════════════════════════════════════════════════
  [MOBILE NODE: JAMES]              [CENTRAL NODE: DGX]              [DESKTOP NODE: DAVID]
  (Android Phone) ◄════════════════► (Always-On Server) ◄════════════════► (Windows PC)
                      Tailscale Mesh                     Tailscale Mesh
                                           │
═══════════════════════════════════════════╪═════════════════════════════════════════════
                   C.O.R.E. BRAIN & SOFTWARE EXTENSION MATRIX
═══════════════════════════════════════════╪═════════════════════════════════════════════
                                           │
                ┌──────────────────────────┴──────────────────────────┐
                │             C.O.R.E. CENTRAL ORCHESTRATOR           │
                │             • Cognitive Router & Swarm Engine       │
                ├──────────────────────────┬──────────────────────────┤
                │                          │                          │
                ▼                          ▼                          ▼
   ┌────────────────────────┐ ┌────────────────────────┐ ┌────────────────────────┐
   │  MEMORY ENGINE (Core)  │ │   A.X.I.O.M. (Core)    │ │ SOFTWARE EXTENSIONS    │
   │  • Behavioral Decay    │ │  • Omnimodal RAG       │ │ (Direct API / Webhook) │
   │  • S(t) = S₀ · e^(-λt) │ │  • Personal Library    │ │                        │
   │  • Zero-Knowledge Sync │ │  • Qdrant Vector Index │ │ ├── TLDR Shield        │
   └────────────────────────┘ └────────────────────────┘ │ ├── LaunchMintAI       │
                                                         │ └── Future Plugins     │
                                                         └────────────────────────┘
```

### Store-and-Forward Offline Task Delegation
1. When a user issues a heavy request on a resource-constrained satellite node (e.g. Android mobile running a 3B model):
2. The local node inspects task complexity vs. local RAM limits.
3. If the task exceeds local capabilities and Central Node is online, it delegates the task over Tailscale.
4. If Central Node is offline, the satellite node notifies the user, executes a lightweight local summary, and queues the full command in a local `DeferredTaskQueue`.
5. Upon Tailscale heartbeat reconnection, queued tasks auto-flush to Central Node for background processing.

---

## 3. Cognitive Engine: Dual-Gear Execution Depth

C.O.R.E. features a dynamic **Execution Depth Engine** that scales test-time compute based on the complexity and sensitivity of the prompt:

```
                      [USER PROMPT / TASK INPUT]
                                  │
                                  ▼
                     [EXECUTION DEPTH RESOLVER]
              Inspects query intent, sensitivity, & flags
                                  │
                 ┌────────────────┴────────────────┐
                 │                                 │
                 ▼                                 ▼
      [GEAR 1: STANDARD MODE]           [GEAR 2: MAX_DEPTH OVERDRIVE]
      • Latency: 1 – 3 seconds          • Latency: 15 – 45 seconds
      • Single-pass direct inference    • Multi-Agent Swarm Decomposition
      • Fast tool execution             • Monte Carlo Risk Modeling
      • Conversational banter           • Adversarial Skeptic Reflection
```

* **`STANDARD` Gear:** Used for daily conversation, quick queries, device toggles, and voice interactions.
* **`MAX_DEPTH` Gear (Swarm Overdrive):** Deploys a specialized sub-agent swarm:
  * **IB Banker:** Rigorous unit economics and cash flow modeling.
  * **Detective:** Adversarial edge case auditing and risk analysis.
  * **Threat Hunter:** Security vulnerability and failure mode analysis.
  * **Business Intelligence:** Market sizing and macro trend analysis.
  * **Skeptic:** Synthesizes competing agent outputs and eliminates hallucinations.

---

## 4. Model-Agnostic Abstraction Layer

To ensure complete freedom from vendor lock-in and support rapid model swapping (e.g. Gemini 2.5 → DeepSeek-R1 → Qwen 2.5):

```
[Raw Model Response] ──> [SchemaNormalizer] ──> [ModelAdapter] ──> [Application Core]
```

* **`SchemaNormalizer`:**
  * Strips internal `<thought>...</thought>` scratchpad blocks (preventing TTS engines from reading reasoning tags over voice channels).
  * Cleans markdown JSON code fences (` ```json `).
  * Extracts inline `[ACTION: ...]` execution tokens.
* **Automated Regression Suite (`benchmark_model_swap.py`):**
  * Evaluates any newly swapped model against 30 multi-modal test prompts (JSON formatting, tool calling, persona adherence, edge cases).
  * Enforces a minimum **90% benchmark pass rate** before a new model is deployed to live production.

---

## 5. Security & Ephemeral Keys

* **Ephemeral RAM Decryption Keys:** Local SQLite databases are decrypted using keys that exist exclusively in volatile memory (RAM).
* **Heartbeat-Triggered Zeroization:** If a satellite node loses its encrypted Tailscale heartbeat (network severance, physical theft, or power-off), RAM keys are immediately wiped, rendering local storage cryptographically inaccessible.
* **Risk-Gated Action Dispatcher:**
  * *Low-Risk Actions (Lights, AC, Music):* Auto-executed across nodes.
  * *High-Risk Actions (Door locks, Security cameras, Storage wiping):* Remote execution is blocked; requires cryptographic approval on the primary device.
