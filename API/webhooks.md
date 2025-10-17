# Webhooks

Webhook endpoints receive and process blockchain event notifications from Alchemy and QuickNode when assets are published on-chain.

## Overview

### Purpose

Webhooks provide real-time synchronization between blockchain events and file status in the API. When a user publishes an asset to the blockchain via the dXmaster smart contract, the system needs to update the corresponding file's status from `pending` to `onchain`. Webhooks automate this process without requiring polling or manual updates.

### Workflow

1. User publishes asset → Calls `dXmaster.addAsset()` with file CID and metadata
2. Smart contract processes transaction → Emits `AssetAdded` event on blockchain
3. Blockchain provider detects event → Alchemy or QuickNode captures event in real-time
4. Provider sends webhook → HTTP POST request to configured webhook URL
5. API validates signature → HMAC-SHA256 verification ensures authenticity
6. API decodes event data → Extracts asset CID and author from event logs
7. API updates file status → Changes file status from `pending` to `onchain`

### Benefits

**Real-time Synchronization:**
- No polling required - push-based architecture
- Immediate status updates (1-2 seconds after blockchain confirmation)
- Automatic process without user intervention

**Security:**
- HMAC-SHA256 signature verification prevents spoofing
- Cryptographic validation of webhook authenticity
- Protection against man-in-the-middle attacks

**Reliability:**
- Both Alchemy and QuickNode supported for redundancy
- Automatic retry mechanisms built into providers
- Webhook delivery guarantees from infrastructure providers

### Security Mechanism

Both webhook endpoints implement HMAC-SHA256 signature verification to ensure requests originate from legitimate blockchain providers and haven't been tampered with during transmission.

---

## POST /alchemy/webhook

Receives blockchain event notifications from Alchemy's Notify API when assets are published.

### Description

This endpoint processes webhook calls from Alchemy when the `AssetAdded` event is emitted by the dXmaster smart contract. Alchemy monitors specified contract addresses and sends HTTP POST requests when matching events occur. The endpoint verifies the request signature using HMAC-SHA256, decodes the event data, and updates the corresponding file's status to `onchain`.

**Authentication:**
- HMAC-SHA256 signature in `X-Alchemy-Signature` header
- Signature computed over raw request body
- Uses Alchemy signing key for verification

### Request

**Endpoint:** `POST /alchemy/webhook`

**Headers:**
```
Content-Type: application/json
X-Alchemy-Signature: 3a7bd3e2360a3d29eea436fcfb7e44c735d117c42d1c1835420b6b9942dd4f1b
```

**Request Body:**
```json
{
  "webhookId": "wh_abc123xyz789",
  "id": "whevt_def456uvw012",
  "createdAt": "2024-10-16T12:00:00.000Z",
  "type": "ADDRESS_ACTIVITY",
  "event": {
    "network": "ETH_SEPOLIA",
    "activity": [
      {
        "fromAddress": "0x742d35cc6634c0532925a3b844bc9e7595f0beb",
        "toAddress": "0x1234567890123456789012345678901234567890",
        "blockNum": "0x4b7e23",
        "hash": "0xabcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890",
        "value": 0,
        "asset": null,
        "category": "external"
      }
    ],
    "data": {
      "block": {
        "logs": [
          {
            "address": "0x1234567890123456789012345678901234567890",
            "topics": [
              "0xd5e1a6e2b7c4f8a9d3e5c8b7a4f2e1d9c8b7a6f5e4d3c2b1a0987654321fed",
              "0x0000000000000000000000001234567890123456789012345678901234567890"
            ],
            "data": "0x00000000000000000000000000000000000000000000000000000000000000a0",
            "blockNumber": "0x4b7e23",
            "transactionHash": "0xabcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890",
            "transactionIndex": "0x0",
            "blockHash": "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
            "logIndex": "0x0",
            "removed": false
          }
        ]
      }
    }
  }
}
```

### Response

**Success Response (200 OK):**

```json
{
  "success": true,
  "message": "Webhook received successfully",
  "assetCid": "QmX1234567890abcdefghijklmnopqrstuvwxyz",
  "timestamp": "2024-10-16T12:00:01.234Z"
}
```

---

## POST /quicknode/webhook

Receives blockchain event notifications from QuickNode Streams when assets are published.

### Description

This endpoint processes webhook calls from QuickNode Streams service. Similar to Alchemy webhooks, it monitors the dXmaster contract for `AssetAdded` events and updates file status accordingly. QuickNode uses a different signature verification method that includes nonce and timestamp in addition to the body.

**Authentication:**
- HMAC-SHA256 signature in `X-QN-Signature` header
- Signature computed over `nonce + timestamp + body`
- Requires three headers: nonce, timestamp, and signature

### Request

**Endpoint:** `POST /quicknode/webhook`

**Headers:**
```
Content-Type: application/json
X-QN-Nonce: abc123xyz789
X-QN-Timestamp: 1697472000123
X-QN-Signature: 7f3d8e9a2b1c5f4e8d7a3b6c9e2f1d8c7b6a5f4e3d2c1b0a987654321fedcba
```

**Request Body:**
```json
{
  "streamId": "stream_abc123xyz789",
  "timestamp": 1697472000123,
  "network": "ethereum-sepolia",
  "matchingReceipts": [
    {
      "transactionHash": "0xabcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890",
      "blockNumber": "0x4b7e23",
      "blockHash": "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
      "from": "0x742d35cc6634c0532925a3b844bc9e7595f0beb",
      "to": "0x1234567890123456789012345678901234567890",
      "logs": [
        {
          "address": "0x1234567890123456789012345678901234567890",
          "topics": [
            "0xd5e1a6e2b7c4f8a9d3e5c8b7a4f2e1d9c8b7a6f5e4d3c2b1a0987654321fed",
            "0x0000000000000000000000001234567890123456789012345678901234567890"
          ],
          "data": "0x00000000000000000000000000000000000000000000000000000000000000a0",
          "blockNumber": "0x4b7e23",
          "transactionHash": "0xabcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890",
          "transactionIndex": "0x0",
          "blockHash": "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
          "logIndex": "0x0",
          "removed": false
        }
      ],
      "status": "0x1",
      "contractAddress": null,
      "cumulativeGasUsed": "0x5208",
      "gasUsed": "0x5208",
      "effectiveGasPrice": "0x12a05f200"
    }
  ]
}
```

### Response

**Success Response (200 OK):**

```json
{
  "success": true,
  "message": "Webhook received successfully",
  "assetCid": "QmX1234567890abcdefghijklmnopqrstuvwxyz",
  "timestamp": "2024-10-16T12:00:01.234Z"
}
```

---

## Event Data Structure

Both webhooks process the `AssetAdded` event emitted by the dXmaster smart contract:

```solidity
event AssetAdded(
    string _assetTitle,
    string _assetCid,
    string _thumbnailCid,
    address _assetAddress,
    address _author,
    uint256 _costInNativeInWei
);
```

### Decoded Event Fields

| Field | Type | Description |
|-------|------|-------------|
| `_assetTitle` | string | Human-readable title of the asset |
| `_assetCid` | string | IPFS CID of the asset content |
| `_thumbnailCid` | string | IPFS CID of the thumbnail image |
| `_assetAddress` | address | Address of the created dXasset ERC20 contract |
| `_author` | address | Ethereum address of the asset creator |
| `_costInNativeInWei` | uint256 | Price of the asset in wei |

**Webhook Processing:**
- Extracts `_assetCid` and `_author` from event
- Locates file with matching CID and owner
- Updates file status from `pending` to `onchain`

---

### Security Benefits

- **Authenticity:** Proves webhook originates from legitimate provider
- **Integrity:** Detects any tampering during transmission
- **Non-repudiation:** Provider cannot deny sending the webhook
- **Replay Protection:** QuickNode's nonce prevents replay attacks