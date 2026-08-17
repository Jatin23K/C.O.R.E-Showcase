# Memory Architecture & Pipeline — C.O.R.E.

## The Problem with Traditional AI Memory

Every commercial AI assistant suffers from the same fundamental flaws:
1. **The Context Window Amnesia:** When the session closes, the context vanishes. The next session starts from zero.
2. **The "Memory Pollution" Trap:** When systems naively store everything in a flat vector database, casual conversation fluff (*"I bought coffee today"*) pollutes serious factual research (*"Explain Matryoshka embeddings"*).
3. **Unchecked Storage Bloat:** Without mathematical decay, databases grow infinitely until latency degrades and cloud compute costs explode.

---

## C.O.R.E. Two-Tier Memory Architecture

To solve these problems, C.O.R.E. enforces a **strict architectural separation** between **Behavioral Memory** and **Factual Knowledge**:

```
                              C.O.R.E. MEMORY ECOSYSTEM
                                         │
                 ┌───────────────────────┴───────────────────────┐
                 ▼                                               ▼
   [CONVERSATION MEMORY: dgx_ecosystem]             [KNOWLEDGE BASE: A.X.I.O.M]
   • Scope: LOCAL TO NODE (Not shared)              • Scope: SHARED ACROSS ALL NODES
   • Purpose: Shapes Tone & Behavior                • Purpose: Personal Library & Research
   • Data: Chat logs, voice preferences, habits     • Data: PDFs, Books, Audio, Video, Docs
   • Engine: Random Forest Decay + Metadata Sync   • Engine: Matryoshka RAG + ColPali + Qdrant
```

---

## 1. Conversational Memory Pipeline (Local & Self-Decaying)

Conversational memories are **device-bound and private**. Raw chat text is never sent to a cloud server. Only anonymized behavioral metadata is synchronized to the Central Node for lifecycle evaluation.

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
  Central ML Classifier (RandomForest on DGX)
  Evaluates anonymized metadata → Outputs KEEP / DECAY / BIN
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
3. Central DGX runs the trained `RandomForestClassifier` on the numbers and returns `{"memory_id": "f83a-910c", "decision": "KEEP"}`.
4. **Result:** Central compute optimizes storage without ever seeing private personal conversations.

### Business & Mathematical Validation
* **Storage & Compute Cost Reduction:** Validated via A/B financial simulation across 30,000 synthetic memory records to generate a **41.46% net cost reduction** compared to standard indefinite retention.
* **Statistical Independence:** Chi-Square hypothesis testing confirmed classification validity with $p = 2.22 \times 10^{-267}$.

---

## 2. Factual Knowledge Base (A.X.I.O.M. Omnimodal RAG)

While conversation memory stays local, **objective knowledge** (research papers, technical manuals, market reports, video keyframes) is managed by the **A.X.I.O.M.** RAG subsystem and shared across all satellite nodes:

* **Matryoshka Adaptive Embeddings:** Dynamic vector slicing (1536d → 256d) for fast, low-latency pre-filtering in Qdrant.
* **ColPali Visual Retrieval:** Direct ViT patch embedding on raw PDF pages, eliminating OCR layout destruction.
* **Role-Scoped Redis Caching:** Sub-15ms cache hit latency cryptographically bound to permission hashes.
* **RAG Triad Observability:** Real-time monitoring of Context Relevance, Groundedness (SLA 1.00), and Answer Relevance with verbatim citation mapping.
