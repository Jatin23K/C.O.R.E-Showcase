# Memory Pipeline — C.O.R.E.

## The Problem with AI Memory

Every AI assistant today has the same memory model: the conversation window. When the window closes, the memory is gone. The next session starts blank. The AI does not know you — it knows the last N tokens.

C.O.R.E. was built to solve this. A digital companion that forgets you after every session is not a companion. It is a stateless API.

---

## Phase 1 Pipeline — Live

```
User Message / Interaction
          │
          ▼
  Short-term Memory (SQLite)
  Active session store
  Everything recorded immediately
          │
          ▼ (nightly — CRON job)
  Memory Filter
  Classify: Keep vs Discard
  Rules: recurrence, emotional weight, factual value
          │
          ▼
  Long-term Memory (SQLite)
  Persistent store — survives sessions
  Indexed by timestamp, topic, context tag
          │
          ▼
  Retrieved on next session start
  Injected into system context
  The AI knows what it knew yesterday
```

### Short-term Memory

Everything that happens in an active session is written to the short-term SQLite store in real time. No filtering at write time — the goal is zero data loss during the session. The filter runs after, not during.

### Nightly CRON Filter

A scheduled job runs each night. It reads the short-term store and applies classification logic:

- **Keep** — Facts about the user (name, preferences, ongoing projects), recurring topics, explicitly important events
- **Discard** — Small talk, repeated filler, context that has no future relevance

What survives moves to long-term. What does not is pruned. The short-term store is then cleared for the next day.

The filter logic is heuristic-based in Phase 1. Phase 2 introduces an ML classifier.

### Long-term Memory

The persistent SQLite store. Survives machine reboots, client restarts, and system updates. Read at session start and injected into the model context. The AI's knowledge of you accumulates over time.

---

## Phase 2 Pipeline — Designed

The Phase 1 pipeline is a direct write-read loop. Phase 2 decouples capture from processing.

```
JAMES (Mobile Capture)
          │
          ▼
  Redis Queue (Bridge Buffer)
  Zero-latency write on mobile
  No blocking — fire and forget
          │
          ▼ (async — DAVID processes)
  Adaptive Chunking Engine
  ├── Conversation input → Semantic chunking
  ├── Code / structured input → AST chunking
  └── Factual data → Entity extraction
          │
          ▼
  Qdrant Vector Store
  Semantic search across long-term memory
  Retrieval by similarity, not just keyword
          │
          ▼
  Long-term SQLite (unchanged)
  Still the source of truth
  Qdrant is the retrieval layer, not the store
```

### Why Redis as the bridge buffer?

In Phase 1, JAMES writes directly to the DAVID-side SQLite over the bridge. If the bridge is slow or DAVID is under load, the write blocks — the JAMES UI freezes waiting for confirmation.

Redis decouples the write from the process. JAMES drops the memory item into the Redis queue and returns immediately. DAVID processes the queue asynchronously. Zero-latency UI on mobile, zero data loss at the processing layer.

### Why Qdrant for vector storage?

SQLite keyword search is sufficient for Phase 1 — the memory store is small. As memory accumulates over months and years, keyword search degrades. "What did I say about the project last month?" requires semantic retrieval, not a string match.

Qdrant stores embeddings of long-term memory items. Retrieval is by cosine similarity — the model gets the most contextually relevant memories injected into context, not just the most recent ones.

### Adaptive Chunking

Different data types need different chunking strategies:

- **Conversations** → Semantic chunking. Split by topic shift, not token count. A conversation about two different things should produce two chunks, not one large blob.
- **Code** → AST chunking. Split at function and class boundaries. A chunk of code mid-function is useless for retrieval.
- **Facts** → Entity extraction. "My project deadline is June 30" becomes an entity with a date, a topic, and an urgency flag.

The chunking engine runs before vectorization. The quality of the chunk determines the quality of the retrieval.
