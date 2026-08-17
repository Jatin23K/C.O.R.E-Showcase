# Ephemeral Vault Security Architecture — C.O.R.E.

## 1. The Security Challenge

A sovereign AI ecosystem that stores persistent memory, credentials, and private context has one critical vulnerability: **physical or remote machine compromise.**

Standard whole-disk encryption (e.g. BitLocker or LUKS) protects data when the computer is turned off. However, while a device is running, decrypted files in storage remain vulnerable to local inspection or forensics.

C.O.R.E. implements an **Ephemeral Key Security Model** where physical possession of a running satellite device does not compromise the private vault.

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
Instant Key Zeroization Triggered
     │
     ▼
RAM Cleared — Decryption Key Destroyed
     │
     ▼
Vault Inaccessible (Encrypted Database Remains Locked)
```

---

## 3. RAM-Only Key Lifecycle

The decryption keys for local memory databases are **never written to persistent disk**. At all times during operation, keys exist exclusively in volatile memory (RAM).

### Why This Matters:
1. **Disk Forensics Defense:** If an attacker extracts or images the physical SSD, they find only an encrypted SQLite database. Zero key remnants exist on disk.
2. **Power-Loss Zeroization:** If a device is seized or loses power, volatile RAM discharges instantly, permanently destroying the ephemeral key.
3. **Hibernation Protection:** Hibernation states trigger key zeroization before memory pages can be dumped to disk.

---

## 4. Degraded Offline Autonomy & Reconnection

When a satellite node loses connection to the Central Node:
1. **Immediate Key Zeroization:** Access to long-term memory is securely locked.
2. **Degraded Local Autonomy:** The local node switches to standalone mode, allowing basic tasks and local notes via lightweight local inference without crashing.
3. **Automatic Re-sync:** When Tailscale heartbeat is re-established, the key is securely re-derived, the local vault unlocks, and offline queues flush automatically.

---

## 5. Security & Attack Vector Matrix

| Attack Vector | Defense Mechanism | Outcome |
|---|---|---|
| **Stolen Device (Powered Off)** | Disk Encryption + Zero Keys on Disk | **100% Secure** (No key extractable) |
| **Stolen Device (Powered On)** | Tailscale Network Severance triggers RAM Zeroization | **100% Secure** (Key destroyed within 1 heartbeat) |
| **Remote Node Breach** | Keys exist in volatile memory only | **100% Secure** (No static key files on filesystem) |
| **Prompt Injection / Unauthorized Remote Action** | Risk-Gated Dispatcher blocks high-risk IoT/actions | **100% Secure** (Requires physical primary device MFA) |
