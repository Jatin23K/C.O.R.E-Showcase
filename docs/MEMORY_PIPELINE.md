# Memory Architecture & Pipeline — C.O.R.E. Public Design Representation

## The Problem with Traditional AI Memory

Every commercial AI assistant suffers from the same fundamental flaws:
1. **The Context Window Amnesia:** When the session closes, the context vanishes. The next session starts from zero.
2. **The "Memory Pollution" Trap:** When systems naively store everything in a flat vector database, casual conversation fluff (*"I bought coffee today"*) pollutes serious factual research (*"Explain Matryoshka embeddings"*).
3. **Unchecked Storage Bloat:** Without mathematical decay, databases grow infinitely until latency degrades and cloud compute costs explode.

---

## C.O.R.E. Two-Tier Memory Architecture

To solve these problems, C.O.R.E. proposes a **strict architectural separation** between **Behavioral Memory** and **Factual Knowledge**. This is a design representation; the full ecosystem pipeline is not being claimed as prototype-ready.

```
                              C.O.R.E. MEMORY ECOSYSTEM
                                         │
                 ┌───────────────────────┴───────────────────────┐
                 ▼                                               ▼
   [CONVERSATION MEMORY: dgx_ecosystem]             [KNOWLEDGE BASE: A.X.I.O.M]
   • Scope: LOCAL TO NODE (Not shared)              • Scope: SHARED ACROSS ALL NODES
   • Purpose: Shapes Tone & Behavior                • Purpose: Personal Library & Research
   • Data: Chat logs, voice preferences, habits     • Data: PDFs, Books, Audio, Video, Docs
   • Engine: Proposed lifecycle classifier + metadata sync   • Engine: Proposed Matryoshka RAG + ColPali + Qdrant
```

---

## 1. Conversational Memory Pipeline (Local & Self-Decaying)

The target design keeps conversational memories **device-bound and private**. Raw chat text should remain local while only the minimum metadata required for lifecycle evaluation is synchronized to a future trusted node. This boundary requires implementation, logging, backup, and external-API validation.

```
User Message / Voice Interaction
          │
          ▼
  Short-term Session Vault (Encrypted SQLite)
  Records raw interaction depth & initial importance
          │
          ▼ (Event-Driven & Nightly CRON)
  Logarithmic Semantic Decay: S(t) = S₀ · e^(-λt)
  Topic tags govern decay rates (e.g. Health vs Nostalgia)
          │
          ▼
  Proposed Lifecycle Classifier (future central node)
  Evaluates approved metadata → Outputs KEEP / DECAY / BIN
          │
          ▼
  Long-Term Staged Persistence
  KEEP  ──> Reinforced in active local memory
  DECAY ──> Compressed semantic summary
  BIN   ──> Staged in local quota (User-governed deletion)
```

### Logarithmic Topic-Specific Decay ($S(t) = S_0 \cdot e^{-\lambda_{tag} t}$)
Not all experiences degrade at the same rate. C.O.R.E. applies topic-specific decay constants ($\lambda$):

| Topic Tag | Decay Rate ($\lambda$) | Behavior & Rationale |
|---|---|---|
| `health`, `family`, `finance` | **Low $\lambda$ (Slow Decay)** | High personal consequence. Preserved indefinitely. |
| `career`, `projects`, `goals` | **Medium $\lambda$ (Standard)** | Relevant while active; degrades if unreferenced for months. |
| `nostalgia`, `casual`, `gossip` | **High $\lambda$ (Fast Decay)** | Transient sentiment. Decays rapidly toward `BIN`. |

### Zero-Knowledge Metadata-Only Sync
To preserve total user privacy:
1. The satellite node keeps the raw text encrypted locally.
2. The satellite node sends **only an anonymized metadata vector** to the Central DGX Node:
   ```json
   {
     "memory_id": "f83a-910c",
     "topic_tag": "finance",
     "hardware_tier": 2,
     "interaction_depth": 5,
     "initial_importance": 0.85,
     "days_since_last_retrieval": 14
   }
   ```
3. A future trusted node could run the lifecycle classifier on the approved metadata and return `{"memory_id": "f83a-910c", "decision": "KEEP"}`.
4. **Design goal:** Central compute optimizes lifecycle decisions without receiving private conversational content. This requires validation rather than assumption.

### Separate Applied Data Science Validation
The separate C.O.R.E. Memory Intelligence project contains the synthetic-data A/B financial simulation across 30,000 memory records and reports a 41.46% scenario-based net cost reduction plus a chi-square result of $p = 2.22 \times 10^{-267}$. Those results should be discussed as evidence from that separate project, not as a measured deployment result for this public C.O.R.E. architecture.

---

## 2. Factual Knowledge Base (A.X.I.O.M. Omnimodal RAG)

While conversation memory stays local, **objective knowledge** (research papers, technical manuals, market reports, video keyframes) is managed by the **A.X.I.O.M.** RAG subsystem and shared across all satellite nodes:

* **Matryoshka Adaptive Embeddings:** Proposed dynamic vector slicing (1536d → 256d) for fast pre-filtering in Qdrant; quality and latency require benchmarking.
* **ColPali Visual Retrieval:** Proposed direct ViT patch embedding on raw PDF pages to preserve layout that OCR can lose.
* **Role-Scoped Redis Caching:** Proposed permission-scoped caching with sub-15ms as a design target, not a measured result.
* **RAG Triad Observability:** Proposed monitoring of Context Relevance, Groundedness, and Answer Relevance with citation mapping; a 1.00 threshold is a policy target requiring evaluation.
