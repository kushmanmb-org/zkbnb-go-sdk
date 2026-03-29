# Getting Started with zkpdf

This guide walks you through setting up and using zkpdf with the ZkBNB Go SDK.

## Prerequisites

- Go 1.18 or later
- A ZkBNB account with a registered account name
- Access to a ZkBNB API endpoint

## Installation

Add the ZkBNB Go SDK to your project:

```bash
go get github.com/kushmanmb-org/zkbnb-go-sdk
```

## Setting Up the Client

### 1. Initialize the ZkBNB Client

```go
import (
    "github.com/kushmanmb-org/zkbnb-go-sdk/client"
)

zkbnbClient, err := client.NewZkBNBClientWithPrivateKey(
    "https://your-zkbnb-endpoint",
    "your-private-key",
    97, // BSC testnet chain ID
)
if err != nil {
    panic(err)
}
```

### 2. Configure the Key Manager

```go
import "github.com/kushmanmb-org/zkbnb-go-sdk/accounts"

keyManager, err := accounts.NewSeedKeyManager("your-private-seed")
if err != nil {
    panic(err)
}
```

## Anchoring a PDF Document

### Step 1: Hash the Document

```go
import (
    "crypto/sha256"
    "os"
)

data, err := os.ReadFile("document.pdf")
if err != nil {
    panic(err)
}
hash := sha256.Sum256(data)
```

### Step 2: Mint a Document NFT on ZkBNB

Use the ZkBNB Go SDK to mint an NFT that represents the document hash:

```go
import "github.com/kushmanmb-org/zkbnb-go-sdk/types"

mintTx := &types.MintNftTxReq{
    CreatorAccountIndex: accountIndex,
    ToAccountIndex:      accountIndex,
    ToAccountName:       "your-account-name",
    NftContentType:      0,
    NftContent:          fmt.Sprintf("%x", hash),
    CollectionId:        0,
    CreatorTreasuryRate: 0,
    GasAccountIndex:     1,
    GasAssetId:          0,
    ExpiredAt:           time.Now().Add(24 * time.Hour).UnixMilli(),
    Nonce:               nonce,
}

txHash, err := zkbnbClient.MintNft(mintTx, nil)
if err != nil {
    panic(err)
}
fmt.Printf("Document anchored with tx hash: %s\n", txHash)
```

### Step 3: Verify a Document

To verify a document later, retrieve the NFT and compare the stored hash:

```go
nft, err := zkbnbClient.GetNftByNftIndex(nftIndex)
if err != nil {
    panic(err)
}

currentHash := fmt.Sprintf("%x", sha256.Sum256(currentDocData))
if nft.NftContent == currentHash {
    fmt.Println("Document integrity verified!")
} else {
    fmt.Println("Document has been modified!")
}
```

## Next Steps

- [Architecture Overview](Architecture.md)
- [Full API Reference](API-Reference.md)
- [zkpdf Integration Guide](zkpdf-Integration.md)
