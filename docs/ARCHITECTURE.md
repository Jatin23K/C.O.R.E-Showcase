# Architecture — C.O.R.E.

## Overview

C.O.R.E. is designed as a **personal sovereign AI ecosystem** — a system that belongs entirely to one person, runs on their hardware, communicates over their private network, and stores data in their own vault.

The architecture reflects three core constraints that were non-negotiable from day one:

1. **Privacy** — No data routed through third-party communication layers
2. **Ownership** — No subscriptions, no platform lock-in, no external dependency on the core mesh
3. **Scalability** — The Phase 1 two-client foundation must be extendable to an N-device ecosystem without architectural rewrites

---

## Phase 1 Topology — Live Today

```
┌─────────────────────────────────────────┐
│             DAVID (Desktop)              │
│     React + Python · Windows PC          │
│   Acting as Tier 1 Core (Phase 1)        │
│                                          │
│  Cortex · Files · Tasks · Projects      │
│  Settings · Security · Bridge           │
│  Personas: DAVID · DARA                 │
└──────────────┬──────────────────────────┘
               │
     Tailscale Encrypted Mesh
     (Private VPN · Zero third-party routing)
               │
┌──────────────┴──────────────────────────┐
│              JAMES (Mobile)              │
│        Flutter · Android                 │
│                                          │
│  Chat · Vault · Core Brain · Safe Tab   │
│  Dashboard · Diagnostics                │
│  Personas: JAMES · JANIE                │
└─────────────────────────────────────────┘
               │
       Shared Memory System
  Short-term → CRON Filter → Long-term
```

Both clients share one memory system. JAMES can delegate heavy computation to DAVID over the bridge. The bridge is bidirectional — DAVID can push context to JAMES.

---

## Phase 2 Topology — Designed

```
┌─────────────────────────────────────────┐
│         Tier 1 Core — DGX Spark          │
│    Dedicated AI Hardware · Sovereign     │
│    Full Local Inference · No Cloud       │
│    All Sub-Hub Orchestration             │
└──────────┬──────────────────────────────┘
           │
    Tailscale Encrypted Mesh
           │
    ┌──────┴──────────────────────────┐
    │         Tier 2 Sub-Hubs          │
    ├──────────────────────────────────┤
    │  JAMES (Mobile · Flutter)        │
    │  Home Tablet (Home Control Hub)  │
    │  ∞ PC Nodes (Desktop Clients)    │
    └──────┬──────────────────────────┘
           │
    ┌──────┴──────────────────────────┐
    │         Tier 3 End Nodes         │
    ├──────────────────────────────────┤
    │  Smart Watch · Biometric Input   │
    │  Meta Glasses · AR Context       │
    │  Smart Bulbs · IoT Environment   │
    │  ∞ Edge Devices                  │
    └─────────────────────────────────┘
```

In the final architecture, DAVID/PC steps down from acting Tier 1 Core to a Tier 2 Sub-Hub. The DGX Spark (or equivalent dedicated AI hardware) takes Tier 1. The architecture was designed so this transition requires no client rewrites — only a config change at the bridge layer.

---

## Design Decisions

### Why Tailscale for the bridge?

The requirement was zero third-party involvement in the communication layer between clients. Tailscale runs as a private mesh — traffic flows peer-to-peer, encrypted end-to-end, and never passes through a Tailscale-controlled relay if both nodes are reachable. For nodes that are not directly reachable, the relay is used only for routing, not decryption.

The alternative (a self-hosted VPN server) introduces a single point of failure and maintenance overhead. Tailscale's key exchange is built on WireGuard — the cryptographic model is audited and well-understood.

### Why SQLite for memory, not a database server?

Phase 1 runs on a personal PC. A database server introduces a daemon that must be running, a port that must be open, and a service that can crash. SQLite is a file. It starts instantly, requires no server process, and survives machine reboots without intervention.

The trade-off is write concurrency — SQLite handles one write at a time. For a two-client personal system with a nightly CRON write batch, this is not a constraint. When Phase 2 introduces multiple Sub-Hubs writing simultaneously, Redis handles the queue and Qdrant handles vector storage. SQLite remains the long-term persistence layer.

### Why two personas per client instead of one?

A digital companion with only one personality is a product. Two personalities that behave fundamentally differently — same infrastructure, different brain — is a system. The persona engine was built to prove that the cognitive profile is a parameter, not a hardcoded identity. This matters for the Phase 2 multi-user RBAC design: each Tenant ID can have its own persona configuration.

### Why closed-source?

The system holds personal memory, private conversations, and contextual data about one person's life. Open-sourcing the implementation does not increase trust — it increases attack surface. The engineering decisions are documented publicly (this repo). The implementation stays private for the same reason the vault contents stay private.

---

## Trade-offs Acknowledged

| Decision | Trade-off |
|---|---|
| Tailscale mesh over self-hosted VPN | Dependency on Tailscale key exchange infrastructure |
| SQLite over PostgreSQL | No concurrent writes — acceptable for Phase 1 scale |
| Cloud API primary (Gemini) | External API dependency — acknowledged, hardware-blocked |
| Closed-source | No community contribution — intentional |
| Two clients before ecosystem | Phase 2 requires additional hardware investment |
