# zkpdf Architecture

This page describes the technical architecture of the zkpdf system and how it integrates with the ZkBNB Layer 2 network.

## System Overview

```
┌─────────────────────────────────────────────────────────┐
│                     Application Layer                    │
│              (Your app using zkpdf + ZkBNB SDK)          │
└───────────────────────────┬─────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────┐
│                    zkpdf Core Library                    │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────┐  │
│  │  ZK Prover  │  │  Doc Hasher  │  │ Proof Verifier │  │
│  └──────┬──────┘  └──────┬───────┘  └───────┬────────┘  │
└─────────┼────────────────┼──────────────────┼───────────┘
          │                │                  │
┌─────────▼────────────────▼──────────────────▼───────────┐
│                  ZkBNB Go SDK Layer                      │
│  ┌────────────────┐  ┌────────────────────────────────┐  │
│  │  L2 Client     │  │      Key Manager / Signer      │  │
│  │  (API calls)   │  │  (accounts + signer packages)  │  │
│  └───────┬────────┘  └───────────────┬────────────────┘  │
└──────────┼────────────────────────────┼──────────────────┘
           │                            │
┌──────────▼────────────────────────────▼──────────────────┐
│                  ZkBNB Layer 2 Network                    │
│             (Transaction processing + ZK proofs)          │
└──────────────────────────┬────────────────────────────────┘
                           │
┌──────────────────────────▼────────────────────────────────┐
│               BNB Smart Chain (Layer 1)                    │
│          (Final settlement + contract verification)        │
└────────────────────────────────────────────────────────────┘
```

## Components

### ZK Prover

The ZK Prover generates zero-knowledge proofs from PDF document hashes. It uses the gnark circuit library (via ZkBNB's cryptographic primitives) to produce proofs that:

- Confirm the prover knows the preimage of a document hash
- Prove set membership (the document belongs to a verified collection)
- Attest to document metadata without revealing the content

### Doc Hasher

The Doc Hasher normalizes and hashes PDF documents into a canonical form suitable for on-chain anchoring:

1. Strips PDF metadata that changes on each save (e.g., timestamps)
2. Computes a SHA-256 or Poseidon hash of the normalized content
3. Returns a 32-byte commitment suitable for the ZK circuit

### Proof Verifier

The Proof Verifier checks proofs on-chain using NFT content stored on ZkBNB L2. It reads the document commitment from an NFT's `NftContent` field and verifies it against a provided proof.

## Data Flow

### Anchoring a Document

```
PDF file → Doc Hasher → SHA-256 hash → MintNft (ZkBNB SDK) → NFT on L2
```

### Verifying a Document

```
PDF file → Doc Hasher → SHA-256 hash
                                     ↘
                   NFT lookup (GetNftByNftIndex) → compare hashes → result
```

### Zero-Knowledge Proof Flow

```
PDF file + secret → ZK Prover → proof + public inputs
                                                      ↘
                                          Proof Verifier → valid/invalid
```

## Key Packages

| Package | Purpose |
|---------|---------|
| `client` | L2 and L1 ZkBNB API client interfaces |
| `accounts` | Key management and seed-based key generation |
| `signer` | L1 transaction signing |
| `txutils` | Transaction construction utilities |
| `types` | Shared type definitions for all ZkBNB data structures |

## Security Considerations

- Private keys are never transmitted to the ZkBNB network; signing is done locally
- PDF content is never sent on-chain; only its hash commitment is stored
- ZK proofs guarantee that only the document owner can produce a valid proof
- All L2 transactions are ultimately verified and settled on BNB Smart Chain (L1)
