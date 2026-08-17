# C.O.R.E.
### Cognitive Operation and Reasoning Engine

> *C.O.R.E. is maintained as a private sovereign system because it runs exclusively on personal hardware and contains real-world credentials, private notes, and daily personal data. This repository publicly documents the architecture, engineering trade-offs, and system design specifications without exposing private operational credentials or personal memory databases.*

---

## What is C.O.R.E.?

C.O.R.E. is a **sovereign cross-device operating ecosystem** that originated as a digital companion and has evolved into an orchestration layer for multi-agent workflows, personal memory, and hardware tasks.

Two clients. One encrypted mesh. Zero third-party communication layers.

- **JAMES** — Mobile client (Flutter · Android). Mobile-first interactions, voice interface, and local offline GGUF inference.
- **DAVID** — Desktop workstation client (React + Python · Windows). Heavy compute, multi-agent swarms, and system orchestration.
- **Bridge** — Encrypted Tailscale mesh with store-and-forward offline queuing.
- **Memory Engine** — Two-tier architecture: Local behavioral memory ($S(t) = S_0 \cdot e^{-\lambda t}$) and shared multimodal RAG (`A.X.I.O.M.`).
- **Execution Depth Engine** — Dual-gear processing: `STANDARD` (1–3s latency) and `MAX_DEPTH` Swarm Overdrive (15–45s multi-agent synthesis).

---

## 🏛️ The Umbrella Ecosystem

C.O.R.E. serves as the central orchestrator that connects and powers my specialized intelligence subsystems:

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                              THE PERSONAL AI ECOSYSTEM                                 │
├────────────────────────────────────────────────────────────────────────────────────────┤
│                                                                                        │
│                      ┌──────────────────────────────────────────────────────────────┐  │
│                      │ [ CORE ENGINE PILLARS ]                                      │  │
│                      │ ┌──────────────────────────┐  ┌───────────────────────────┐  │  │
│                      │ │   C.O.R.E. Memory Engine │  │         A.X.I.O.M.        │  │  │
│                      │ │  BEHAVIORAL DECAY & SYNC │  │  OMNIMODAL RAG LIBRARY    │  │  │
│                      │ └──────────────────────────┘  └───────────────────────────┘  │  │
│        ( 00 )        │                                                              │  │
│       C.O.R.E.       ├──────────────────────────────────────────────────────────────┤  │
│  CENTRAL ORCHESTRATOR│ [ APPLIED CAPABILITIES ]                                     │  │
│                      │ ┌──────────────────────────┐  ┌───────────────────────────┐  │  │
│                      │ │       TLDR Shield        │  │       LaunchMintAI        │  │  │
│                      │ │ PRIVACY & INGESTION RISK │  │ FORENSIC MARKET CORTEX    │  │  │
│                      │ └──────────────────────────┘  └───────────────────────────┘  │  │
│                      │ ┌─────────────────────────────────────────────────────────┐  │  │
│                      │ │                    Future Expansion                     │  │  │
│                      │ │                 INFINITE NODE CAPACITY                  │  │  │
│                      │ └─────────────────────────────────────────────────────────┘  │  │
│                      └──────────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

1. **[TLDR Shield](https://github.com/Jatin23K/TLDR-Shield)** — The *Privacy Ingestion Shield*. Scans policy documents and privacy risks across 6 pillars with verbatim DOM highlighting.
2. **[LaunchMintAI](https://github.com/Jatin23K/LaunchMintAI)** — The *Market Research Cortex*. Evaluates startup ideas and competitors using XGBoost models ($AUC = 0.8170$) and Monte Carlo simulations.
3. **C.O.R.E. Memory Engine** — The *Biological Memory Engine*. Predicts memory decay ($S(t) = S_0 \cdot e^{-\lambda t}$) via Random Forest under 90% data sparsity, validated to reduce cloud storage costs by **41.46%** ($p = 2.22 \times 10^{-267}$).
4. **[A.X.I.O.M.](https://github.com/Jatin23K/A.X.I.O.M)** — The *Enterprise Omnimodal RAG Library*. 9-module architecture featuring ColPali visual patch retrieval, Matryoshka embeddings (1536d→256d), and sub-15ms semantic caching.

---

## System Phases & Status

**Phase 1 — Field Operations · Live in Daily Use**
Both clients built and running. Tailscale bridge operational. Execution Depth Engine active. Model-Agnostic Plug-and-Play layer active with 100% benchmark regression pass rate.

| Component | Status | Implementation Details |
|---|---|---|
| JAMES (Mobile Client) | ✅ Live | Flutter · Android · 2 Personas (JAMES / JANIE) · Local GGUF Fallback |
| DAVID (Desktop Client) | ✅ Live | React + Python · Windows · 2 Personas (DAVID / DARA) · Multi-Agent Swarm |
| Encrypted Mesh Bridge | ✅ Operational | Tailscale VPN · Direct peer-to-peer · Store-and-Forward Queuing |
| Execution Depth Engine | ✅ Live | Dual-Gear (`STANDARD` 1-3s vs `MAX_DEPTH` Swarm Overdrive 15-45s) |
| Model Adapter & Normalizer | ✅ Live | `ModelAdapter` + `SchemaNormalizer` with 30-prompt automated benchmark |
| Ephemeral Vault Security | ✅ Operational | RAM-only decryption keys · Mesh heartbeat destruction |

**Phase 2 — Sovereign Ecosystem · Specification Designed**
Scaling toward a dedicated Always-On Central Node (DGX / Sovereign Server) managing the master Qdrant index, Risk-Gated cross-device command dispatcher, and Home Assistant / MQTT IoT protocol bus.

---

## Screenshots & Interface Proof

### JAMES vs JANIE — Mobile Client
One Flutter client. Two distinct cognitive profiles. UI color shifts reflect deep backend prompt, reasoning, and context reloads.

| JAMES (Analytical & Direct) | JANIE (Warm & Conversational) |
|---|---|
| ![JAMES Chat](./screenshots/james/james_chat.jpeg) | ![JANIE Chat](./screenshots/janie/janie_chat.jpeg) |
| ![JAMES Session](./screenshots/james/james_session.jpeg) | ![JANIE Session](./screenshots/janie/janie_session.jpeg) |
| ![JAMES Vault](./screenshots/james/james_vault.jpeg) | ![JANIE Vault](./screenshots/janie/janie_vault.jpeg) |

### DAVID vs DARA — Desktop Client
One React shell. One Python backend. Red → Green visual confirmation accompanies a full reasoning matrix context reload.

| DAVID (Analytical & Research) | DARA (Reflective Mentor) |
|---|---|
| ![DAVID Chat](./screenshots/david/david_chat.png) | ![DARA Chat](./screenshots/dara/dara_chat.png) |
| ![DAVID Cortex](./screenshots/david/david_cortex.png) | ![DARA Cortex](./screenshots/dara/dara_cortex.png) |
| ![DAVID Dashboard](./screenshots/david/david_dashboard.png) | ![DARA Dashboard](./screenshots/dara/dara_dashboard.png) |
| ![DAVID Tasks](./screenshots/david/david_tasks.png) | ![DARA Tasks](./screenshots/dara/dara_tasks.png) |

---

## Technical Architecture & Engineering Documents

| Document | What it covers |
|---|---|
| [ARCHITECTURE.md](./docs/ARCHITECTURE.md) | Topology, Execution Depth Engine, ModelAdapter abstraction, and trade-offs |
| [MEMORY_PIPELINE.md](./docs/MEMORY_PIPELINE.md) | Logarithmic decay math, Random Forest lifecycle classification, and Zero-Knowledge metadata sync |
| [EPHEMERAL_VAULT_SECURITY.md](./docs/EPHEMERAL_VAULT_SECURITY.md) | Tailscale heartbeat validation, RAM-only decryption keys, and offline node isolation |
| [PERSONA_ENGINE.md](./docs/PERSONA_ENGINE.md) | Dual-persona backend engine — JAMES/JANIE and DAVID/DARA prompt matrices |
| [INFORMATION_SOVEREIGNTY.md](./docs/INFORMATION_SOVEREIGNTY.md) | Autonomous monitoring, noise filtering, and personalized synthesis digests |

---

## Tech Stack

* **Mobile Client (JAMES):** Flutter · Dart · Android · Local Termux GGUF Inference
* **Desktop Client (DAVID):** React 18 · TypeScript · Python · FastAPI · DirectML GPU Acceleration
* **Bridge & Network:** Tailscale VPN · WireGuard Mesh Protocol · WebSocket Telemetry
* **Memory & Storage:** SQLite (Local Node Storage) · Qdrant Vector DB (Shared Knowledge) · Redis (Async Queue)
* **Model Layer:** Provider-Agnostic Abstraction (`ModelAdapter`) · Google Gemini API · Local Ollama / GGUF
* **Security Layer:** Ephemeral RAM Decryption Keys · Tailscale Heartbeat Verification · Zero-Knowledge Metadata Routing

---

## Status & Sovereignty

```
Phase 1: Field Operations Active (In Daily Personal Use)
Network: Tailscale Encrypted Mesh
Execution Depth: Dual-Gear (STANDARD / MAX_DEPTH)
License: Private Sovereign System — Runs on personal hardware with real credentials
```

---

*Built by Jatin Kumar — A sovereign personal operating ecosystem.*
