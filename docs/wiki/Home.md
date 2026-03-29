# zkpdf Wiki

Welcome to the **kushmanmb/zkpdf** wiki. This documentation covers the design, architecture, usage, and integration of the zkpdf project — a zero-knowledge proof system for verifiable PDF document operations on ZkBNB.

## Table of Contents

- [Overview](#overview)
- [Getting Started](Getting-Started.md)
- [Architecture](Architecture.md)
- [API Reference](API-Reference.md)
- [zkpdf Integration](zkpdf-Integration.md)
- [FAQ](FAQ.md)

## Overview

**zkpdf** is a zero-knowledge proof library that enables privacy-preserving verification of PDF documents on the ZkBNB Layer 2 network. It allows users to:

- Prove ownership of a PDF document without revealing its contents
- Verify the integrity of a document without exposing sensitive data
- Anchor document hashes to the ZkBNB blockchain via NFT minting
- Perform batch verifications of multiple document proofs in a single transaction

## Key Features

| Feature | Description |
|---------|-------------|
| Document Ownership Proofs | Generate ZK proofs that attest to PDF ownership |
| Content Integrity Verification | Verify that a document has not been tampered with |
| On-chain Anchoring | Mint NFTs representing verified PDF documents on ZkBNB L2 |
| Batch Processing | Verify multiple documents in a single L2 transaction |
| Privacy Preservation | Document contents are never revealed on-chain |

## Quick Links

- [ZkBNB Go SDK](../../readme.md)
- [Project Roadmap](../../ROADMAP.md)
- [GitHub Repository](https://github.com/kushmanmb-org/zkbnb-go-sdk)
