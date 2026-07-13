# Lightweight Ledger Attestation Protocol (LLAP)

**Version:** Draft 0.1

**Status:** Research Proposal

**Author:** Bambang Irawan

---

# 1. Introduction

The Lightweight Ledger Attestation Protocol (LLAP) is a protocol proposal for cryptographically attesting the integrity of offline-generated transaction history before synchronization.

Unlike traditional synchronization protocols that primarily rely on transport security (e.g., TLS), LLAP focuses on generating verifiable evidence that transaction history has not been rewritten during offline operation.

LLAP does **not** replace TLS or distributed consensus protocols. Instead, it complements them by introducing an application-layer attestation mechanism.

---

# 2. Motivation

Offline-first systems are increasingly common in:

- Retail Point-of-Sale
- Manufacturing
- Industrial IoT
- Logistics
- Mining
- Maritime Operations
- Edge Computing
- Remote Field Systems

These environments continue operating while disconnected from central infrastructure.

Although TLS protects data during transmission, it provides no guarantee that locally stored history remained intact before synchronization.

LLAP addresses this gap.

---

# 3. Design Goals

LLAP is designed to provide:

- Historical Integrity
- Tamper Detection
- Cryptographic Attestation
- Replay Protection
- Deterministic Synchronization
- Signed Acknowledgements

LLAP intentionally avoids introducing new cryptographic primitives.

---

# 4. Non-Goals

LLAP does **not** attempt to solve:

- Endpoint malware
- Private key theft
- Physical device destruction
- Consensus between multiple independent authorities
- Byzantine Fault Tolerance

These concerns require complementary security mechanisms.

---

# 5. Threat Model

LLAP assumes that attackers may:

- Modify local databases
- Replay synchronization requests
- Restore outdated backups
- Delete transaction history
- Manipulate transaction ordering

LLAP assumes:

- Device private keys remain secure.
- Trust Anchor is trusted.
- Cryptographic algorithms remain secure.

---

# 6. Architecture

```
Edge Device
     │
Generate Ledger Evidence
     │
Synchronize
     │
Trust Anchor
     │
Signed Receipt
     │
ATTESTED
```

---

# 7. Protocol Overview

Each synchronization consists of six phases.

## Phase 1

Generate deterministic ledger evidence.

Includes:

- Canonical serialization
- Merkle tree
- Historical hash chain

---

## Phase 2

Digitally sign ledger evidence.

Recommended algorithms:

- Ed25519
- ECDSA P-256

---

## Phase 3

Synchronize evidence over an untrusted network.

Recommended transport:

- HTTPS
- TLS 1.3

---

## Phase 4

Trust Anchor verifies:

- Signature
- Merkle Root
- Historical Hash Chain
- Previous Receipt

---

## Phase 5

Trust Anchor stores evidence inside an append-only archive.

---

## Phase 6

Trust Anchor returns a signed cryptographic receipt.

Client verifies receipt.

Client state changes:

```
SYNCED
↓

ATTESTED
```

---

# 8. Protocol Components

## Ledger

Append-only transaction history.

---

## Ledger Evidence

Evidence generated from:

- Canonical CBOR serialization
- Merkle Root
- Historical Hash Chain

---

## Trust Anchor

Central authority responsible for:

- Validation
- Archiving
- Receipt issuance

---

## Signed Receipt

Contains:

- Ledger ID
- Merkle Root
- Previous Hash
- Timestamp
- Signature

---

# 9. Security Properties

LLAP provides:

✓ Integrity

✓ Authenticity

✓ Non-repudiation

✓ Replay Protection

✓ Historical Continuity

LLAP does not provide:

✗ Endpoint Security

✗ Confidentiality without TLS

✗ Consensus

---

# 10. State Machine

```
NEW

↓

GENERATED

↓

SIGNED

↓

SYNCHRONIZED

↓

RECEIPT VERIFIED

↓

ATTESTED
```

---

# 11. Recommended Cryptography

Canonical Encoding

- CBOR (RFC 8949)

Hash Function

- SHA-256
- SHA-512/256

Digital Signature

- Ed25519
- ECDSA P-256

Merkle Tree

- Binary Merkle Tree

---

# 12. Design Principles

LLAP follows several principles.

1. Deterministic

Evidence generation must produce identical outputs for identical ledgers.

2. Append-only

Historical evidence must never be modified.

3. Idempotent

Synchronization must safely tolerate retries.

4. Verifiable

Every synchronization produces independently verifiable evidence.

---

# 13. Future Work

Future research includes:

- Formal verification
- Tamarin proofs
- ProVerif models
- Transparency logs
- TPM integration
- Secure Enclave integration
- HSM integration
- Benchmarking
- Reference implementation

---

# 14. Status

LLAP is currently a research proposal.

The protocol is undergoing community review and is expected to evolve based on implementation experience and peer feedback.

---

# References

- RFC 8949 — CBOR
- RFC 8032 — EdDSA
- RFC 6962 / RFC 9162 — Certificate Transparency
- NIST FIPS 180-4
- Google Android Offline-First Architecture
- Microsoft Azure IoT Edge Documentation
