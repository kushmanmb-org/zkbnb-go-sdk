# Project Roadmap

This document outlines the planned features, milestones, and improvements for the ZkBNB Go SDK and the zkpdf integration.

## Current Status

The ZkBNB Go SDK provides a full-featured Layer 2 client for ZkBNB, supporting queries, transaction signing, NFT operations, and L1 interactions. The zkpdf integration is under active development.

---

## Milestones

### ✅ v1.0 — Core SDK (Completed)

- [x] L2 client with full querier and transaction sender interfaces
- [x] Seed-based key manager for local transaction signing
- [x] L1 client for BNB Smart Chain contract interactions (deposits, withdrawals, NFT exits)
- [x] Transaction construction utilities (`txutils`)
- [x] Transaction signature verification
- [x] Support for BEP-20 deposits and withdrawals
- [x] NFT minting, transfer, withdrawal

---

### 🔄 v1.1 — zkpdf Foundation (In Progress)

- [x] Wiki documentation for zkpdf integration
- [x] Architecture documentation
- [x] Getting Started guide
- [x] API Reference documentation
- [x] FAQ documentation
- [ ] PDF normalization utility (strip metadata before hashing)
- [ ] SHA-256 and Poseidon hash adapters for document commitments
- [ ] CLI tool: `zkpdf anchor <file>` — anchor a PDF to ZkBNB
- [ ] CLI tool: `zkpdf verify <file> <nft-index>` — verify a PDF against its on-chain commitment

---

### 🔜 v1.2 — Batch Operations

- [ ] Batch document anchoring (multiple PDFs in a single L2 transaction)
- [ ] Batch verification API
- [ ] Collection-based document organization
- [ ] Pagination helpers for listing anchored documents

---

### 🔜 v1.3 — Zero-Knowledge Proofs

- [ ] ZK circuit for document ownership proofs (gnark-based)
- [ ] Proof generation API: `GenerateOwnershipProof(docHash, privateKey)`
- [ ] Proof verification API: `VerifyOwnershipProof(proof, publicInputs)`
- [ ] Integration with ZkBNB's on-chain proof verifier

---

### 🔜 v2.0 — Advanced Features

- [ ] Selective disclosure: prove specific PDF fields without revealing the full document
- [ ] Multi-party document signing with aggregated ZK proofs
- [ ] Document revocation support (mark an anchored document as revoked)
- [ ] IPFS integration for decentralized document storage with on-chain hash anchoring
- [ ] WebAssembly (WASM) build for browser-based zkpdf operations
- [ ] REST API server mode for server-side document anchoring services

---

## Known Limitations

| Limitation | Planned Fix | Target |
|-----------|-------------|--------|
| No PDF metadata normalization | Add `pdf.Normalize()` utility | v1.1 |
| No batch transaction support | Batch anchoring API | v1.2 |
| ZK proof generation not yet available | ZK circuit implementation | v1.3 |
| No document revocation | Revocation list on L2 | v2.0 |

---

## Contributing

Contributions are welcome! Please see the [README](readme.md) for setup instructions and submit a pull request using the [PR template](.github/PULL_REQUEST_TEMPLATE.md).

For roadmap discussions, open a GitHub Issue with the label `roadmap`.
