# C.O.R.E. Memory Management at Scale
### Enterprise Distributed Memory Architecture & Lifecycle Specification

> **System Scope**: Public architectural representation of a proposed memory subsystem for the C.O.R.E. ecosystem. It describes ingestion, mathematical decay, lifecycle classification, privacy-preserving synchronization, and multi-device conflict resolution. The separate C.O.R.E. Memory Intelligence repository contains the Applied Data Science implementation and empirical validation; this document does not claim that the full C.O.R.E. memory system is prototype-ready.

---

## 1. Executive Summary & Core Philosophy

Traditional conversational AI systems suffer from two fatal extremes: **Memory Overload** (hoarding all interactions indefinitely until vector search slows and cloud storage explodes) or **Blind Deletion** (wiping old memories via naive FIFO/TTL rules, destroying critical context).

C.O.R.E. proposes a biological, mathematically governed **Two-Tier Sovereign Memory Engine**:
1. **Behavioral / Episodic Memory (Local & Self-Decaying):** Manages tone, preferences, working habits, and daily context. Strictly device-bound with logarithmic decay ($S(t) = S_0 \cdot e^{-\lambda t}$) and ML-driven lifecycle classification (`KEEP`, `DECAY`, `BIN`).
2. **Factual Knowledge Base (`A.X.I.O.M.`):** Manages objective documents, research papers, and technical libraries across all devices using Matryoshka vector embeddings and ColPali visual patch retrieval.

```
                              C.O.R.E. MEMORY ECOSYSTEM
                                         │
                 ┌───────────────────────┴───────────────────────┐
                 ▼                                               ▼
   [CONVERSATION MEMORY: dgx_ecosystem]             [KNOWLEDGE BASE: A.X.I.O.M]
   • Scope: LOCAL TO NODE (Private)                 • Scope: SHARED ACROSS ALL NODES
   • Purpose: Shapes Tone & Behavior                • Purpose: Personal Library & Research
   • Data: Chat logs, voice preferences, habits     • Data: PDFs, Books, Audio, Video, Docs
   • Engine: Random Forest Decay + Metadata Sync   • Engine: Matryoshka RAG + ColPali + Qdrant
```

---

## 2. The Mathematical Semantic Decay Engine

To prevent database bloat, C.O.R.E. models memory degradation using an exponential decay function governed by topic criticality:

$$S(t) = S_0 \cdot e^{-\lambda_{\text{tag}} \cdot t}$$

Where:
* $S_0$: Initial semantic density / importance score $[0.0, 1.0]$ assigned at ingestion.
* $t$: Time elapsed in days since the last verified interaction (`RECALL`, `REFINE`, `INSPECT`).
* $\lambda_{\text{tag}}$: Logarithmic decay constant determined by a strict closed-taxonomy Enum.

### Closed-Taxonomy Decay Lookup Table

```python
from enum import Enum

class MemoryTopicTag(str, Enum):
    HEALTH = "health"         # λ = 0.005 (Half-life: 138 days) — High consequence
    FAMILY = "family"         # λ = 0.005 (Half-life: 138 days) — High consequence
    FINANCE = "finance"       # λ = 0.010 (Half-life: 69 days)  — High consequence
    CAREER = "career"         # λ = 0.020 (Half-life: 35 days)  — Medium consequence
    PROJECTS = "projects"     # λ = 0.030 (Half-life: 23 days)  — Medium consequence
    NOSTALGIA = "nostalgia"   # λ = 0.080 (Half-life: 9 days)   — Ephemeral
    CASUAL = "casual"         # λ = 0.150 (Half-life: 4.6 days) — Transient
```

* **The "Dirty Bit" Rescuing Protocol:** When a decaying memory is referenced in conversation (`is_dirty = TRUE`), its timestamp resets to $t=0$, restoring $S(t) = S_0$ and rescuing it from degradation.

---

## 3. Applied Machine Learning Lifecycle Classifier

Central DGX runs a serialized Scikit-Learn `RandomForestClassifier` (`train_model.py`) that evaluates memory resonance under real-world **90% signal sparsity** (where 90% of memories have no retrieval history).

```
Raw Interaction ──> Feature Matrix ──> RandomForest ──> cron_decision (KEEP / DECAY / BIN)
```

### Feature Engineering Space
1. `hardware_tier`: Device compute origin (1 = Desktop, 2 = Mobile, 3 = Peripheral).
2. `interaction_depth`: Number of conversational turns dedicated to the topic.
3. `initial_importance`: Beta-distributed semantic density $[0.0, 1.0]$.
4. `total_retrievals`: Aggregate historical access events.
5. `avg_sentiment`: Normalized user feedback resonance $[-1.0, +1.0]$ (imputed $0.0$ if unretrieved).
6. `topic_tag_*`: One-hot encoded categorical vector.

### Separate Applied Data Science Evidence
The C.O.R.E. Memory Intelligence project independently contains a synthetic-data study and financial simulation reporting a 41.46% scenario-based storage-cost reduction and a chi-square result of $p\text{-value} = 2.22 \times 10^{-267}$. Those results belong to that separate Applied Data Science project. They are not measured evidence that this public C.O.R.E. ecosystem architecture is complete or deployed.

---

## 4. Zero-Knowledge Metadata-Only Synchronization

The target privacy design keeps raw conversational transcripts and encryption keys within local device boundaries. Any implementation would need to verify external API handling, logs, backups, telemetry, synchronization metadata, and failure paths before making an absolute privacy claim.

```
[SATELLITE NODE: MOBILE / PC]                 [CENTRAL DGX NODE]
┌───────────────────────────────┐             ┌───────────────────────────────┐
│ RAW MEMORY STORAGE (Private): │             │ ML CLASSIFIER ENGINE:         │
│ • Actual Chat & Voice Text    │             │ • Receives Anonymized Metadata│
│ • Private Files & Documents   │             │ • Runs RandomForest Model     │
│ • Encryption Keys in RAM only │             │ • Computes Decay: S(t) = S₀e  │
└───────────────┬───────────────┘             └───────────────┬───────────────┘
                │                                             │
                │ Sends ONLY Anonymized Metadata Tuple        │
                │ (memory_id, topic, tier, depth, imp, lag)   │
                ├────────────────────────────────────────────►│
                │                                             │
                │ Returns Lifecycle Verdict                   │
                │ {"memory_id": "uuid-123", "verdict": "BIN"} │
                │◄────────────────────────────────────────────┤
                ▼
  Applies KEEP / DECAY / BIN 
  verdict to local raw database.
```

### Ephemeral RAM Key Security
* Local SQLite vaults are decrypted using ephemeral keys stored exclusively in volatile memory (RAM).
* The design proposes that a verified Tailscale mesh-heartbeat failure trigger RAM key zeroization, leaving the local disk cryptographically locked if the control is implemented and validated correctly.

---

## 5. Memory Overload & Blind Deletion Protections

C.O.R.E. prevents storage bloat and data loss through a multi-tier safeguard:

### 1. Bounded `BIN` Quota with Backpressure (Stops Overload)
* The `BIN` staging area has a strict physical storage cap (e.g., 500MB).
* When the quota is reached, C.O.R.E. pauses new memory ingestion and triggers a **UX Ultimatum**:
  > *"Local Memory Vault BIN limit reached. Please review and authorize permanent purge."*
* **Zero silent bloat:** The user maintains 100% sovereign deletion authority.

### 2. Hierarchical Topic-Ranked Eviction (Stops Blind Deletion)
When a purge is authorized, memories are evicted strictly by **Topic Vulnerability Weight**:
1. **Purged First:** `casual` small talk and transient greetings.
2. **Purged Second:** `nostalgia` and expired project logs.
3. **Shielded / Immune:** `health`, `family`, and `finance` records are shielded at the bottom of the stack and cannot be purged in bulk without explicit item-by-item manual override.

### 3. Semantic Archival Compression (10-Year Horizon)
To preserve permanent health and finance records over decades without storage growth, C.O.R.E. periodically synthesizes historical entries into **High-Density Semantic Profiles**:
* 50 doctor visit notes over 3 years $\rightarrow$ 1 structured medical profile saving 95% space with 0% data loss.

---

## 6. Distributed Conflict Resolution at Scale

In an ecosystem of multiple offline satellite nodes, C.O.R.E. resolves distributed edge cases through deterministic rules:

### 1. "Split-Brain" Multi-Device Clashes
When two nodes record contradictory facts while offline:
1. **Explicit Directive Override:** A direct voice/text command always beats passive background telemetry.
2. **Domain Authority Matrix:**
   * **Desktop (DAVID):** Master authority on `coding`, `system_architecture`, `finance`.
   * **Mobile (JAMES):** Master authority on `location`, `daily_habits`, `health`.
3. **Tiebreaker:** Cryptographic Lamport Timestamp (Last-Write-Wins).

### 2. "Stale Belief" Contradiction Prevention
To prevent older preferences (e.g., $1,000 laptop budget in 2024) from conflicting with new preferences ($4,000 budget in 2026) during vector search:
1. **Temporal Recency Multiplier:**
   $$\text{Final Retrieval Score} = \text{CosineSimilarity} \times e^{-\gamma \cdot (t_{\text{now}} - t_{\text{memory}})}$$
2. **Entity Supercedence (`is_superseded = TRUE`):** When mutable attributes are updated, older records are flagged as superseded and excluded from active RAG prompts while preserved in cold historical archives.

### 3. Vector Drift & Embedding Migration Pipeline
To prevent historical vectors in Qdrant from breaking when embedding models are upgraded:
1. **Decoupled Relational Storage:** Raw text and JSON metadata are canonically stored in SQLite/PostgreSQL independently of vector points.
2. **Blue/Green Re-Indexing:** Central DGX runs an asynchronous background re-embedding batch job to build a new collection (`axiom_v2`), flipping the search pointer with zero downtime.

---

## 7. Insulating Memory from LLM Flaws (The 5 Deterministic Firewalls)

Because LLMs are probabilistic, non-deterministic, and prone to hallucinations, C.O.R.E. enforces a fundamental architectural axiom:
> **"Use the LLM for linguistic translation and entity extraction; use deterministic software and classical ML for storage, lifecycle, and retrieval."**

```
[USER INPUT] ──> [LLM Extractor] ──> [FIREWALL 1: SchemaNormalizer] ──> [FIREWALL 2: Confidence Floor]
                                                                                   │
                                                                                   ▼
                                                                      [LOCAL ENCRYPTED SQLITE]
                                                                                   │
                                                                                   ▼
[LIFECYCLE DECISIONS] ◄── [FIREWALL 4: Classical ML (Random Forest)] ◄── [FIREWALL 3: Math Decay S(t)]
(Zero LLM Deletion Authority)
```

### The 5 Architectural Firewalls:

1. **Deterministic Machine Learning for Lifecycle (Zero LLM Deletion Authority):**
   * The LLM is **never** asked whether a memory should be kept, decayed, or deleted. Asking an LLM creates temperature-dependent inconsistency.
   * All lifecycle actions (`KEEP`, `DECAY`, `BIN`) are computed exclusively by the deterministic **Scikit-Learn Random Forest Classifier** and the exponential decay formula ($S(t) = S_0 \cdot e^{-\lambda t}$).

2. **Pre-Ingestion Schema Normalizer & Pydantic Firewall:**
   * Before any memory reaches SQLite, `SchemaNormalizer` strips reasoning tags (`<thought>...</thought>`), strips markdown fences (` ```json `), and enforces closed-set `MemoryTopicTag` Enums.
   * Any invalid tag invented by the LLM is automatically rejected and mapped to a safe default.

3. **Provisional Staging & Confidence Floors (Stops Hallucinated Ingestion):**
   * Extracted facts require an **Extraction Confidence Score ($\ge 0.85$)**:
     * **Score $\ge 0.85$ (Direct/Definitive statement):** Persisted to long-term memory.
     * **Score $< 0.85$ (Speculative/Casual statement):** Staged as `provisional_memory` with high decay ($\lambda = 0.15$), naturally auto-expiring in 14 days unless explicitly re-verified.

4. **Strict Context Budgeting (Stops "Lost-in-the-Middle" Distraction):**
   * C.O.R.E. enforces a **Hard Token Cap (Max 1,200 tokens / Top 3–5 memories)** on context injection.
   * Memories are pre-ranked by the Temporal Recency Multiplier ($e^{-\gamma \Delta t}$), preventing prompt clutter and attention degradation.

5. **The `REFINE` Self-Correction Feedback Loop:**
   * If the LLM ever misinterprets a fact, user corrections (*"No, I work in FinTech, not EdTech"*) trigger an immediate `REFINE` event.
   * The erroneous record is marked `is_superseded = TRUE` (silenced from active RAG) and the corrected fact is written with maximum confidence ($S_0 = 1.0$).

---

## 8. Relational Database Schema Contracts

### Master PostgreSQL / SQLite DDL

```sql
-- 1. Hardware Nodes Registry
CREATE TABLE nodes (
    node_id VARCHAR(64) PRIMARY KEY,
    device_name VARCHAR(100) NOT NULL,
    hardware_tier INT CHECK (hardware_tier IN (1, 2, 3)), -- 1: DGX/PC, 2: Mobile, 3: IoT
    node_role VARCHAR(20) CHECK (node_role IN ('parent', 'peripheral')),
    tailscale_ip VARCHAR(45) UNIQUE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- 2. Persistent Memory Vault
CREATE TABLE memories (
    memory_id UUID PRIMARY KEY,
    origin_node_id VARCHAR(64) REFERENCES nodes(node_id),
    topic_tag VARCHAR(32) NOT NULL,
    initial_importance FLOAT CHECK (initial_importance BETWEEN 0.0 AND 1.0),
    interaction_depth INT DEFAULT 1,
    is_dirty BOOLEAN DEFAULT FALSE,
    is_superseded BOOLEAN DEFAULT FALSE,
    cron_decision VARCHAR(10) DEFAULT 'KEEP' CHECK (cron_decision IN ('KEEP', 'DECAY', 'BIN')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    last_retrieved_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    content_encrypted TEXT NOT NULL
);

-- 3. Behavioral Feedback & Retrieval Logs
CREATE TABLE retrieval_logs (
    log_id BIGSERIAL PRIMARY KEY,
    memory_id UUID REFERENCES memories(memory_id) ON DELETE CASCADE,
    retrieval_node_id VARCHAR(64) REFERENCES nodes(node_id),
    interaction_type VARCHAR(20) CHECK (interaction_type IN ('RECALL', 'REFINE', 'INSPECT')),
    feedback_score FLOAT CHECK (feedback_score BETWEEN -1.0 AND 1.0),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Indexing for Fast Edge Search & Garbage Collection
CREATE INDEX idx_memories_topic_decay ON memories (topic_tag, cron_decision, last_retrieved_at);
CREATE INDEX idx_memories_superseded ON memories (is_superseded);
CREATE INDEX idx_logs_memory_id ON retrieval_logs (memory_id);
```

---

## 9. Summary Comparison Matrix

| Feature | Naive Chatbot Memory | C.O.R.E. Memory at Scale |
| :--- | :--- | :--- |
| **Storage Strategy** | Flat vector dump (Memory Pollution) | **Two-Tier Domain Isolation** (Local Behavioral $\neq$ Shared A.X.I.O.M.) |
| **Lifecycle Model** | Infinite bloat or dumb FIFO deletion | **Mathematical Logarithmic Decay ($S(t)$) + Random Forest ML** |
| **Storage Optimization**| 0% (Accumulates indefinitely) | **41.46% scenario reported by separate Memory Intelligence study** |
| **Privacy Architecture** | Plaintext cloud sync | **Zero-Knowledge Metadata-Only Sync + RAM Ephemeral Keys** |
| **Offline Multi-Device**| Fails / Overwrites data | **Store-and-Forward Queues + Domain Authority Routing** |
| **Fact Contradiction** | Hallucinates conflicting dates/budgets | **Temporal Recency Multiplier ($e^{-\gamma \Delta t}$) + Entity Supercedence** |
| **Vector Upgrades** | Broken database / manual rewrite | **Decoupled Relational Source + Blue/Green Background Re-indexing** |
| **LLM Insulation** | Raw unshielded LLM deletes & corrupts | **5 Deterministic Firewalls (Zero LLM deletion authority)** |

---

*Public Architectural Representation | C.O.R.E. Sovereign Intelligence Layer*
