# Frequently Asked Questions

## General

### What is zkpdf?

zkpdf is a zero-knowledge document anchoring system built on top of the ZkBNB Layer 2 network. It allows you to prove ownership and integrity of PDF documents without revealing their contents.

### What blockchain does zkpdf use?

zkpdf anchors document commitments on the ZkBNB L2 network, which settles on BNB Smart Chain (L1).

### Is my document content stored on-chain?

No. Only a SHA-256 hash of your document is stored on-chain as an NFT's `NftContent` field. The actual PDF content never leaves your device.

---

## Technical

### How does document hashing work?

zkpdf computes a SHA-256 hash of the raw PDF binary content and stores the hex-encoded result in the ZkBNB NFT. When verifying, the same hash is recomputed and compared to the stored value.

### What happens if I re-save the PDF?

Most PDF editors modify metadata (timestamps, creator info) even if the visible content is unchanged. This will change the hash. For production use, you should normalize the PDF before hashing (strip metadata fields).

### Can I anchor multiple documents?

Yes. Each document requires a separate `MintNft` transaction, each creating a unique NFT. You can query all of them via `GetNftsByAccountIndex`.

### How do I transfer a document to another person?

Use the `TransferNft` transaction to move the document NFT to another ZkBNB L2 account. The recipient must have a registered ZkBNB account name.

---

## Costs & Performance

### What are the gas fees for anchoring a document?

Gas fees depend on the current ZkBNB network state. Call `GetGasFee(assetId, TxTypeMintNft)` to get the current fee before submitting a transaction.

### How long does anchoring take?

Transactions are typically confirmed on L2 within seconds. Final settlement on BNB Smart Chain (L1) takes longer and depends on the L2 batch submission schedule.

---

## Security

### Is my private key safe?

Yes. The ZkBNB Go SDK signs transactions locally. Your private key is never transmitted to any server.

### Can someone forge a document proof?

No. The SHA-256 hash is a one-way function. Without the original document, no one can produce a matching hash.

### What if I lose my private key?

If you lose your private key, you lose access to your ZkBNB account and the NFTs it holds. There is no recovery mechanism. Always back up your private key securely.
