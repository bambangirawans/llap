# Lightweight Ledger Attestation Protocol (LLAP)

**Version:** 0.1 (Draft)

**Status:** Research Proposal

**License:** Apache 2.0

---

# Abstract

The Lightweight Ledger Attestation Protocol (LLAP) is an application-layer protocol for cryptographically attesting offline-generated transaction history before synchronization.

Unlike transport security protocols such as TLS, which protect data during transmission, LLAP enables a trusted verifier to validate the integrity and continuity of historical ledger state generated while a device operated offline.

LLAP composes existing cryptographic primitives—including Merkle trees, historical hash chaining, canonical serialization, digital signatures, append-only archives, idempotency keys, and signed receipts—into a deterministic synchronization workflow.

The protocol complements rather than replaces transport security or distributed consensus.

---

# 1. Introduction

Offline-first systems increasingly operate in environments with intermittent connectivity.

Examples include:

- Retail Point-of-Sale
- Industrial IoT
- Manufacturing
- Logistics
- Maritime Systems
- Edge Computing

These systems continue processing transactions while disconnected from central infrastructure.

When connectivity returns, synchronization typically relies on HTTPS and digital signatures.

Although these mechanisms secure communication, they cannot prove that locally stored transaction history remained unmodified before synchronization.

LLAP addresses this gap.

---

# 2. Design Goals

LLAP aims to provide:

- Historical Integrity
- Tamper Detection
- Replay Protection
- Cryptographic Receipts
- Deterministic Synchronization
- Application-layer Attestation

---

# 3. Non-Goals

LLAP intentionally does not attempt to solve:

- Endpoint compromise
- Malware detection
- Physical device attacks
- Distributed consensus
- Confidentiality without TLS

These concerns require complementary security mechanisms.

---

# 4. Terminology

| Term | Description |
|------|-------------|
| Edge Device | Offline system producing transactions |
| Ledger | Ordered collection of transactions |
| Ledger Evidence | Cryptographic evidence generated from the ledger |
| Trust Anchor | Central verifier |
| Receipt | Signed acknowledgment from Trust Anchor |
| Epoch | Independent historical segment |
| Idempotency Key | Unique synchronization identifier |

---

# 5. Cryptographic Components

LLAP uses existing cryptographic primitives.

## 5.1 Canonical Serialization

CBOR (RFC 8949)

Purpose:

- Deterministic encoding
- Platform independence

---

## 5.2 Historical Hash Chain

Each ledger entry references the previous hash.

```
H0 = SHA256(genesis)

H1 = SHA256(H0 || Tx1)

H2 = SHA256(H1 || Tx2)

H3 = SHA256(H2 || Tx3)
```

---

## 5.3 Merkle Tree

Transactions within one synchronization batch are organized into a Merkle Tree.

Purpose:

- Efficient verification
- Partial proofs
- Batch integrity

---

## 5.4 Digital Signatures

Supported algorithms:

- Ed25519
- ECDSA P-256

Purpose:

- Authentication
- Non-repudiation

---

## 5.5 Signed Receipt

The Trust Anchor returns a digitally signed receipt containing:

- Ledger Root
- Epoch
- Timestamp
- Receipt ID
- Signature

---

# 6. Protocol Workflow

LLAP consists of six deterministic phases.

## Phase 1

Generate Ledger Evidence

Inputs:

- Transactions

Outputs:

- Ledger Root
- Historical Hash
- Metadata

---

## Phase 2

Sign Evidence

The device signs generated evidence using its private key.

---

## Phase 3

Synchronize

The client transmits:

- Ledger Evidence
- Metadata
- Signature
- Idempotency Key

Transport SHOULD use TLS.

---

## Phase 4

Verification

The Trust Anchor verifies:

- Signature
- Hash Chain
- Merkle Root
- Epoch Continuity
- Replay Status

---

## Phase 5

Receipt Generation

Upon successful verification the Trust Anchor generates a signed receipt.

---

## Phase 6

Attestation

The client verifies the receipt.

If verification succeeds:

```
State = ATTESTED
```

---

# 7. State Machine

```
OFFLINE

↓

EVIDENCE_GENERATED

↓

SIGNED

↓

SYNCHRONIZING

↓

VERIFIED

↓

RECEIPT_RECEIVED

↓

ATTESTED
```

---

# 8. Message Flow

```
Edge Device                 Trust Anchor

Generate Ledger

Sign Evidence

──────── Evidence ────────►

Verify

Store Archive

Generate Receipt

◄──── Signed Receipt ──────

Verify Receipt

ATTESTED
```

---

# 9. Idempotent Recovery

If synchronization fails:

Client sends:

```
ReceiptRequest

IdempotencyKey
```

Trust Anchor responds:

```
Receipt Exists

or

Receipt Missing
```

This avoids duplicate synchronization.

---

# 10. Security Properties

LLAP provides:

✔ Historical Integrity

✔ Replay Protection

✔ Cryptographic Receipts

✔ Tamper Detection

✔ Verifiable Synchronization

LLAP assumes:

- Private keys remain secure.
- Trust Anchor is trusted.

---

# 11. Threat Model

Out of scope:

- Private key theft
- Hardware compromise
- Memory attacks
- Physical attacks
- Supply-chain compromise

Future versions may incorporate hardware-backed key protection.

---

# 12. Performance Considerations

The protocol introduces:

- Hash computation
- Merkle construction
- Signature generation
- Signature verification

These costs are expected to remain practical for edge devices.

Formal benchmarking is planned.

---

# 13. Future Work

Planned work includes:

- Formal verification
- Threat modeling
- TPM integration
- Secure Enclave integration
- HSM integration
- Transparency logs
- Test vectors
- SDK implementations

---

# 14. IANA Considerations

This document requires no IANA actions.

---

# 15. References

Normative References

- RFC 8949 — CBOR
- RFC 8032 — Ed25519

Informative References

- RFC 9162 — Certificate Transparency
- NIST FIPS 180-4
- Google Android Offline-first Architecture
- Microsoft Azure IoT Edge

---

# Appendix A

Example Hash Chain

```
Genesis

↓

Tx1

↓

Tx2

↓

Tx3

↓

Tx4
```

---

# Appendix B

Example Receipt

```
Receipt ID

Ledger Root

Epoch

Timestamp

Device ID

Signature
```

---

# Appendix C

Version History

| Version | Status |
|----------|--------|
| 0.1 | Initial Draft |
