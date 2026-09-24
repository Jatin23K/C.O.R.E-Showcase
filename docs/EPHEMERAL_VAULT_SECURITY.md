# Ephemeral Vault Security Architecture — C.O.R.E. Design Specification

## 1. The Security Challenge

A sovereign AI ecosystem that stores persistent memory, credentials, and private context has one critical vulnerability: **physical or remote machine compromise.** This document specifies a proposed mitigation model for the public C.O.R.E. architecture; it is not a completed security audit or production guarantee.

Standard whole-disk encryption (e.g. BitLocker or LUKS) protects data when the computer is turned off. However, while a device is running, decrypted files in storage remain vulnerable to local inspection or forensics.

C.O.R.E. proposes an **Ephemeral Key Security Model** intended to reduce the impact of physical possession of a running satellite device. Its effectiveness depends on implementation details, platform behavior, threat modeling, and adversarial testing.

---

## 2. The Core Principle: Mesh-Conditional Decryption

A C.O.R.E. satellite node (JAMES mobile or DAVID desktop) is not a standalone storage silo. Its ability to decrypt local personal memory is conditional on its continuous, verified presence in the encrypted **Tailscale mesh**.

```
Satellite Node Starts
     │
     ▼
Tailscale Mesh Connection Verified
     │
     ▼
Ephemeral Decryption Key Derived (RAM Only)
     │
     ▼
Local Memory Vault Accessible (Encrypted SQLite Readable)

──────────────────────────────────────────────────────────

Tailscale Disconnection / Network Severed / Theft Event
     │
     ▼
Designed Key Zeroization Trigger
     │
     ▼
RAM Cleared — Decryption Key Destroyed
     │
     ▼
Vault Inaccessible (Encrypted Database Remains Locked)
```

---

## 3. RAM-Only Key Lifecycle

The target design keeps decryption keys exclusively in volatile memory (RAM) during operation. This must be verified against process dumps, swap/hibernation behavior, crash reporting, logs, backups, and platform-specific memory handling.

### Why This Matters:
1. **Disk Forensics Defense:** If an attacker extracts or images the physical SSD, they find only an encrypted SQLite database. Zero key remnants exist on disk.
2. **Power-Loss Zeroization:** If a device is seized or loses power, volatile RAM discharges instantly, permanently destroying the ephemeral key.
3. **Hibernation Protection:** Hibernation states trigger key zeroization before memory pages can be dumped to disk.

---

## 4. Degraded Offline Autonomy & Reconnection

When a satellite node loses connection to the Central Node:
1. **Designed Key Zeroization:** Access to long-term memory is intended to be locked when the trusted heartbeat fails.
2. **Degraded Local Autonomy:** The local node switches to standalone mode, allowing basic tasks and local notes via lightweight local inference without crashing.
3. **Proposed Re-sync:** When the heartbeat is re-established, the key may be re-derived and offline work reconciled after device authentication, authorization, idempotency, and conflict checks.

---

## 5. Security & Attack Vector Matrix

| Attack Vector | Defense Mechanism | Outcome |
|---|---|---|
| **Stolen Device (Powered Off)** | Disk encryption and no persistent key storage are intended controls | Requires forensic and recovery testing; no system is automatically “100% secure” |
| **Stolen Device (Powered On)** | Heartbeat failure is intended to trigger RAM key zeroization | Requires timing, race-condition, memory-dump, and platform testing |
| **Remote Node Breach** | Volatile-key design limits static key exposure | Does not remove runtime compromise, token theft, or privileged-process risk |
| **Prompt Injection / Unauthorized Remote Action** | Risk-gated dispatcher and primary-device approval are intended controls | Requires authorization, replay, injection, and action-boundary testing |
