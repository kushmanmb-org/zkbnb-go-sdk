# zkpdf Integration Guide

This guide provides a detailed walkthrough for integrating zkpdf document anchoring and verification into your application using the ZkBNB Go SDK.

## Overview

The zkpdf integration enables:
- **Document Anchoring**: Commit a PDF hash to ZkBNB L2 as an NFT
- **Ownership Transfer**: Transfer document NFTs between accounts
- **Integrity Verification**: Confirm that a document has not been modified since anchoring
- **Batch Operations**: Anchor or verify multiple documents efficiently

## Step-by-Step Integration

### 1. Initialize the Client

```go
package main

import (
    "fmt"
    "log"

    "github.com/kushmanmb-org/zkbnb-go-sdk/client"
)

func main() {
    zkClient, err := client.NewZkBNBClientWithPrivateKey(
        "https://api.zkbnb.finance",
        "0xYOUR_PRIVATE_KEY",
        56, // BSC mainnet chain ID (use 97 for testnet)
    )
    if err != nil {
        log.Fatalf("failed to create ZkBNB client: %v", err)
    }
    _ = zkClient
}
```

### 2. Look Up Your Account

```go
account, err := zkClient.GetAccountByL1Address("0xYourEthAddress")
if err != nil {
    log.Fatalf("failed to get account: %v", err)
}
fmt.Printf("Account index: %d, Nonce: %d\n", account.Index, account.Nonce)
```

### 3. Hash the PDF Document

```go
import (
    "crypto/sha256"
    "encoding/hex"
    "os"
)

func hashPDF(filePath string) (string, error) {
    data, err := os.ReadFile(filePath)
    if err != nil {
        return "", fmt.Errorf("failed to read file: %w", err)
    }
    hash := sha256.Sum256(data)
    return hex.EncodeToString(hash[:]), nil
}
```

### 4. Anchor the Document

```go
import (
    "time"
    "github.com/kushmanmb-org/zkbnb-go-sdk/types"
)

func anchorDocument(zkClient client.ZkBNBClient, account *types.Account, docHash string) (string, error) {
    nonce, err := zkClient.GetNextNonce(account.Index)
    if err != nil {
        return "", fmt.Errorf("failed to get nonce: %w", err)
    }

    gasFee, err := zkClient.GetGasFee(0, int(types.TxTypeMintNft))
    if err != nil {
        return "", fmt.Errorf("failed to get gas fee: %w", err)
    }

    mintTx := &types.MintNftTxReq{
        CreatorAccountIndex: account.Index,
        ToAccountIndex:      account.Index,
        ToAccountName:       account.Name,
        NftContentType:      0,
        NftContent:          docHash,
        CollectionId:        0,
        CreatorTreasuryRate: 0,
        GasAccountIndex:     1,
        GasAssetId:          0,
        GasAssetAmount:      gasFee.String(),
        ExpiredAt:           time.Now().Add(24 * time.Hour).UnixMilli(),
        Nonce:               nonce,
    }

    txHash, err := zkClient.MintNft(mintTx, nil)
    if err != nil {
        return "", fmt.Errorf("failed to anchor document: %w", err)
    }
    return txHash, nil
}
```

### 5. Verify Document Integrity

```go
func verifyDocument(zkClient client.ZkBNBClient, nftIndex int64, filePath string) (bool, error) {
    nft, err := zkClient.GetNftByNftIndex(nftIndex)
    if err != nil {
        return false, fmt.Errorf("failed to get NFT: %w", err)
    }

    currentHash, err := hashPDF(filePath)
    if err != nil {
        return false, err
    }

    return nft.NftContent == currentHash, nil
}
```

### 6. List All Anchored Documents

```go
func listAnchoredDocuments(zkClient client.ZkBNBClient, accountIndex int64) ([]*types.Nft, error) {
    nfts, err := zkClient.GetNftsByAccountIndex(accountIndex, 0, 100)
    if err != nil {
        return nil, fmt.Errorf("failed to list NFTs: %w", err)
    }
    return nfts.Nfts, nil
}
```

## Complete Example

```go
package main

import (
    "crypto/sha256"
    "encoding/hex"
    "fmt"
    "log"
    "os"
    "time"

    "github.com/kushmanmb-org/zkbnb-go-sdk/client"
    "github.com/kushmanmb-org/zkbnb-go-sdk/types"
)

const (
    endpoint   = "https://api.zkbnb.finance"
    privateKey = "0xYOUR_PRIVATE_KEY"
    chainID    = uint64(56)
    pdfPath    = "./document.pdf"
)

func main() {
    // Initialize client
    zkClient, err := client.NewZkBNBClientWithPrivateKey(endpoint, privateKey, chainID)
    if err != nil {
        log.Fatalf("client init: %v", err)
    }

    // Get account
    account, err := zkClient.GetAccountByL1Address("0xYourAddress")
    if err != nil {
        log.Fatalf("account lookup: %v", err)
    }

    // Hash PDF
    data, _ := os.ReadFile(pdfPath)
    hash := sha256.Sum256(data)
    docHash := hex.EncodeToString(hash[:])
    fmt.Printf("Document hash: %s\n", docHash)

    // Anchor document
    nonce, _ := zkClient.GetNextNonce(account.Index)
    gasFee, _ := zkClient.GetGasFee(0, int(types.TxTypeMintNft))

    txHash, err := zkClient.MintNft(&types.MintNftTxReq{
        CreatorAccountIndex: account.Index,
        ToAccountIndex:      account.Index,
        ToAccountName:       account.Name,
        NftContent:          docHash,
        GasAccountIndex:     1,
        GasAssetId:          0,
        GasAssetAmount:      gasFee.String(),
        ExpiredAt:           time.Now().Add(24 * time.Hour).UnixMilli(),
        Nonce:               nonce,
    }, nil)
    if err != nil {
        log.Fatalf("anchor document: %v", err)
    }
    fmt.Printf("Document anchored! TX hash: %s\n", txHash)
}
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `nonce mismatch` | Always call `GetNextNonce` immediately before submitting a transaction |
| `insufficient balance` | Ensure your ZkBNB account has enough BNB for gas fees |
| `expired tx` | Increase the `ExpiredAt` duration (default 24 hours) |
| `account not found` | Register your account on ZkBNB using `RegisterZNS` via the L1 client |
| Hash mismatch on verify | Ensure the PDF has not been re-saved; use the same normalization step |
