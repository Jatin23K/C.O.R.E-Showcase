# Zero-Trust DRM Architecture — C.O.R.E.

## The Problem

A personal AI system that stores private memory, conversations, and personal context has one critical vulnerability: physical access to the hardware.

If someone gets the machine, they get the vault. Standard disk encryption solves this partially — but only when the machine is off. If the system is running and connected, the SQLite vault is readable.

C.O.R.E. needed a security model where physical possession of the hardware — even a running machine — does not grant access to the vault.

---

## The Model: Zero-Trust at the Node Level

The core principle: **no node trusts itself in isolation**.

A C.O.R.E. node (JAMES or DAVID) is not a self-contained system. It is a node in a mesh. Its ability to decrypt the local vault is conditional on its position in that mesh. Remove it from the mesh, and the vault becomes cryptographically inaccessible — regardless of what else the attacker controls.

---

## Tailscale Heartbeat Validation

Each node maintains a continuous heartbeat with the Tailscale mesh. This is not just a connectivity check — it is the authorization signal.

```
Node starts
     │
     ▼
Tailscale heartbeat established
     │
     ▼
Heartbeat signal → authorization event
     │
     ▼
Decryption key derived (in RAM only)
     │
     ▼
Vault accessible (SQLite readable)

─────────────────────────────────────

Heartbeat lost / mesh disconnected
     │
     ▼
Key destruction triggered
     │
     ▼
RAM cleared — key no longer exists
     │
     ▼
Vault inaccessible (SQLite encrypted, key gone)
```

The heartbeat runs on a short interval. If the node is disconnected from the mesh — by network failure, VPN termination, physical isolation, or an attacker cutting the connection — the key is destroyed immediately.

---

## RAM-Only Decryption Keys

The decryption key for the SQLite vault is never written to disk. At any point in time, it exists only in RAM.

**Why this matters:**

- **Disk forensics** — If an attacker images the disk, they find an encrypted SQLite file and no key anywhere on the disk. The key was never written.
- **Hibernation / sleep** — If the machine hibernates, the RAM contents may be written to the hibernation file. The security model accounts for this: hibernation triggers the same key destruction as mesh disconnection.
- **Power loss** — If the machine loses power while running, RAM is cleared by physics. The key is gone. The vault remains encrypted.

**Key derivation:**

The key is derived at runtime from a combination of factors that require the mesh to be active. It is not stored, cached, or checkpointed. Each session start requires re-derivation from a fresh heartbeat confirmation.

---

## Offline Isolation Behavior

When a node loses mesh connectivity:

1. **Immediate** — Key destruction triggered
2. **Within one heartbeat interval** — Vault locked, all pending writes flushed to encrypted store before lock
3. **After isolation** — Node enters degraded mode: UI accessible, no memory reads, no memory writes, no bridge communication
4. **On reconnection** — Heartbeat re-established, key re-derived, vault re-opened, missed context synced from bridge

The system does not crash on isolation. It degrades gracefully and recovers automatically on reconnection.

---

## What This Protects Against

| Attack vector | Protection |
|---|---|
| Physical machine theft (powered off) | Disk encryption + no key on disk |
| Physical machine theft (powered on) | Mesh disconnection triggers key destruction |
| Remote compromise (attacker on machine) | Key in RAM only — not extractable from disk |
| Network isolation (cut the connection) | Same as physical disconnection — key destroyed |
| Hibernation image analysis | Hibernation triggers key destruction before sleep |

---

## What This Does Not Protect Against

Full transparency on the threat model:

| Limitation | Why |
|---|---|
| RAM extraction while running and connected | Cold boot attack while mesh is live — key in RAM is readable with physical memory access |
| Compromised Tailscale infrastructure | If Tailscale's key exchange is compromised, the heartbeat signal can be spoofed |
| Attacker with mesh access | If an attacker gains legitimate mesh access, they appear as a connected node |

These are known limitations. The model is not perfect. It is significantly better than standard disk encryption for a system that runs continuously on personal hardware.

---

## Why Not Standard Solutions?

**BitLocker / FileVault** — Encrypts at rest. Does nothing while the system is running.

**Database-level encryption** — Protects against disk access but the key must be stored somewhere to function. We move the key to RAM and tie its existence to the mesh.

**Hardware Security Module (HSM)** — Correct direction, significant hardware cost. Phase 2 considers HSM integration as the DGX Spark becomes the Tier 1 Core. An HSM on dedicated AI hardware is a different cost equation than on a personal PC.

The Zero-Trust DRM architecture is the Phase 1 answer to a problem that has no cheap hardware solution. It is not perfect — it is honest about what it protects and what it does not.
