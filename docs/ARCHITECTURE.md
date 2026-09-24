# Technical Architecture — C.O.R.E.

## 1. System Overview

C.O.R.E. is designed as a **sovereign, multi-device personal operating ecosystem** — a system intended to belong entirely to one person, run on their hardware, communicate over an encrypted mesh, and isolate private memory from public knowledge. This document describes the public architecture representation; it is not a claim that the full ecosystem is prototype-ready.

The architecture is governed by three non-negotiable engineering principles:

1. **Data Sovereignty** — Personal conversations, daily memory, and encryption keys should remain under user-controlled boundaries. External model APIs are an explicit dependency where used; the device bridge is designed without a third-party relay.
2. **Capability Self-Awareness & Graceful Degradation** — The target design lets each satellite detect hardware limits, provide a degraded local path, and delegate heavy compute when a trusted central node is available. This behavior remains subject to field testing.
3. **Model Independence** — Application logic is fully decoupled from LLM providers via a provider-agnostic adapter layer.

---

## 2. Network Topology: Phase 1 Bridge & Phase 2 Star-Hub

```
═══════════════════════════════════════════════════════════════════════════════════════════════════
PHASE 1: DIRECT PEER-TO-PEER BRIDGE (ACTIVE TESTBENCH & HARDWARE PROFILING)
═══════════════════════════════════════════════════════════════════════════════════════════════════
  [MOBILE NODE: JAMES]                                                [DESKTOP NODE: DAVID]
  (Android Flutter Client) ◄═════════ Tailscale WireGuard P2P ════════► (Windows FastAPI Host)

═══════════════════════════════════════════════════════════════════════════════════════════════════
PHASE 2: SOVEREIGN STAR-HUB TOPOLOGY (ROADMAP SPECIFICATION)
═══════════════════════════════════════════════════════════════════════════════════════════════════
                                ┌───────────────────────────────────┐
                                │ CENTRAL NODE (STANDALONE HARDWARE)│
                                │ • Always-On 24/7 Headless Host    │
                                │ • Master Qdrant & SQLite Store    │
                                └─────────────────┬─────────────────┘
                                                  │
                 ═══════════════════ Tailscale Encrypted Mesh ═══════════════════
                   │                      │                 │                  │
                   ▼                      ▼                 ▼                  ▼
          [MOBILE: JAMES]         [WORKSTATION: DAVID] [SMART HOME HUB]   [REMOTE NODE]
          (Android Client)        (Windows PC Host)    (Home Assistant)   (Auxiliary Compute)
                                                  │
══════════════════════════════════════════════════╪════════════════════════════════════════════════
                   C.O.R.E. BRAIN & SOFTWARE EXTENSION MATRIX
══════════════════════════════════════════════════╪════════════════════════════════════════════════
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

### Store-and-Forward Offline Task Delegation — Phase 2 Target Flow
The following is a proposed behavior for the future central-node architecture, not a claim that the complete flow is currently deployed:
1. When a user issues a heavy request on a resource-constrained satellite node (e.g. Android mobile running a small local model):
2. The local node inspects task complexity vs. local RAM limits.
3. If the task exceeds local capabilities and Central Node is online, it delegates the task over Tailscale.
4. If Central Node is offline, the satellite node notifies the user, executes a lightweight local summary, and queues the full command in a local `DeferredTaskQueue`.
5. Upon Tailscale heartbeat reconnection, queued tasks can be reconciled and submitted to the Central Node, subject to idempotency, authorization, and conflict-resolution checks.

---

## 3. Cognitive Engine: Dual-Gear Execution Depth

C.O.R.E. specifies a dynamic **Execution Depth Engine** that would scale test-time compute based on the complexity and sensitivity of the prompt. The latency values below are design budgets, not public production measurements:

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

To reduce vendor coupling and support controlled model swapping (e.g. Gemini 2.5 → DeepSeek-R1 → Qwen 2.5):

```
[Raw Model Response] ──> [SchemaNormalizer] ──> [ModelAdapter] ──> [Application Core]
```

* **`SchemaNormalizer`:**
  * Strips internal `<thought>...</thought>` scratchpad blocks (preventing TTS engines from reading reasoning tags over voice channels).
  * Cleans markdown JSON code fences (` ```json `).
  * Extracts inline `[ACTION: ...]` execution tokens.
* **Proposed automated regression gate (`benchmark_model_swap.py`):**
  * Evaluates any newly swapped model against 30 multi-modal test prompts (JSON formatting, tool calling, persona adherence, edge cases).
  * Defines a minimum **90% benchmark pass rate** as a future promotion gate; it is not a claim of a currently deployed production model.

---

## 5. Security & Ephemeral Keys

* **Ephemeral RAM Decryption Keys:** The design proposes that local SQLite databases be decrypted using keys that exist exclusively in volatile memory (RAM).
* **Heartbeat-Triggered Zeroization:** The design proposes that a verified Tailscale heartbeat failure trigger key zeroization. The timing, completeness, and resistance to compromise require implementation and adversarial validation.
* **Risk-Gated Action Dispatcher:**
  * *Low-Risk Actions (Lights, AC, Music):* Auto-executed across nodes.
  * *High-Risk Actions (Door locks, Security cameras, Storage wiping):* Remote execution is blocked; requires cryptographic approval on the primary device.
