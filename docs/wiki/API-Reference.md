# zkpdf API Reference

This page documents the main interfaces and types used in the zkpdf integration with the ZkBNB Go SDK.

## ZkBNBClient Interface

The primary interface for interacting with the ZkBNB L2 network.

### Query Methods

#### GetNftByNftIndex

```go
GetNftByNftIndex(nftIndex int64) (*types.Nft, error)
```

Returns the NFT at the given index. Used to retrieve anchored document commitments.

**Parameters:**
- `nftIndex` — The L2 NFT index

**Returns:** `*types.Nft` containing the document commitment in the `NftContent` field.

---

#### GetNftsByAccountIndex

```go
GetNftsByAccountIndex(accountIndex, offset, limit int64) (*types.Nfts, error)
```

Returns a paginated list of NFTs owned by the given account. Use this to list all documents anchored by an account.

**Parameters:**
- `accountIndex` — The L2 account index
- `offset` — Pagination offset
- `limit` — Maximum number of results

---

#### GetAccountByL1Address

```go
GetAccountByL1Address(l1Address string) (*types.Account, error)
```

Looks up a ZkBNB L2 account by its L1 (BNB Chain) address.

---

#### GetLayer2BasicInfo

```go
GetLayer2BasicInfo() (*types.Layer2BasicInfo, error)
```

Returns basic information about the L2 network, including supported assets and gas fees.

---

### Transaction Methods

#### MintNft

```go
MintNft(tx *types.MintNftTxReq, ops *types.TransactOpts, signatureList ...string) (string, error)
```

Mints a new NFT representing an anchored document. The document commitment is stored in the `NftContent` field.

**Parameters:**
- `tx` — NFT minting request (see `types.MintNftTxReq`)
- `ops` — Optional transaction options
- `signatureList` — Optional pre-computed signatures

**Returns:** The transaction hash as a hex string.

---

#### TransferNft

```go
TransferNft(tx *types.TransferNftTxReq, ops *types.TransactOpts, signatureList ...string) (string, error)
```

Transfers ownership of a document NFT to another ZkBNB account.

---

#### WithdrawNft

```go
WithdrawNft(tx *types.WithdrawNftTxReq, ops *types.TransactOpts, signatureList ...string) (string, error)
```

Withdraws a document NFT back to the L1 BNB Smart Chain.

---

## Key Types

### types.Nft

```go
type Nft struct {
    Index               int64  `json:"index"`
    CreatorAccountIndex int64  `json:"creator_account_index"`
    OwnerAccountIndex   int64  `json:"owner_account_index"`
    ContentHash         string `json:"content_hash"`
    NftContent          string `json:"nft_content"`   // document commitment stored here
    CollectionId        int64  `json:"collection_id"`
    CreatorTreasuryRate int64  `json:"creator_treasury_rate"`
    Status              int64  `json:"status"`
}
```

### types.MintNftTxReq

```go
type MintNftTxReq struct {
    CreatorAccountIndex int64  `json:"creator_account_index"`
    ToAccountIndex      int64  `json:"to_account_index"`
    ToAccountName       string `json:"to_account_name"`
    NftContentType      int64  `json:"nft_content_type"`
    NftContent          string `json:"nft_content"`   // set to hex-encoded document hash
    CollectionId        int64  `json:"collection_id"`
    CreatorTreasuryRate int64  `json:"creator_treasury_rate"`
    GasAccountIndex     int64  `json:"gas_account_index"`
    GasAssetId          int64  `json:"gas_asset_id"`
    GasAssetAmount      string `json:"gas_asset_amount"`
    ExpiredAt           int64  `json:"expired_at"`
    Nonce               int64  `json:"nonce"`
    Sig                 []byte `json:"sig,omitempty"`
}
```

### types.Account

```go
type Account struct {
    Status          uint32          `json:"status"`
    Index           int64           `json:"index"`
    L1Address       string          `json:"l1_address"`
    Name            string          `json:"name"`
    Pk              string          `json:"pk"`
    Nonce           int64           `json:"nonce"`
    Assets          []*AccountAsset `json:"assets"`
    TotalAssetValue string          `json:"total_asset_value"`
}
```

## Key Manager

### accounts.NewSeedKeyManager

```go
func NewSeedKeyManager(seed string) (KeyManager, error)
```

Creates a new key manager from a seed string. The seed is typically derived from an Ethereum private key.

### accounts.GenerateSeed

```go
func GenerateSeed(privateKey string, chainId uint64) (string, error)
```

Derives a ZkBNB-compatible seed from an Ethereum private key and chain ID.

## Error Handling

All methods return an `error` as the last return value. Common errors include:

| Error | Cause |
|-------|-------|
| `invalid signature` | Key manager not set or private key mismatch |
| `account not found` | L1 address not registered on ZkBNB |
| `nft not found` | Requested NFT index does not exist |
| `insufficient gas` | Not enough gas asset balance for transaction |
| `nonce mismatch` | Stale nonce; call `GetNextNonce` before each transaction |
