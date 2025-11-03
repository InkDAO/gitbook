# Webhooks

Webhook endpoints receive and process blockchain event notifications from Alchemy and QuickNode when assets are published on-chain.

## Overview

### Purpose

Webhooks provide real-time synchronization between blockchain events and file status in the API. When a user publishes a post to the blockchain via the **MarketPlace** smart contract, the system needs to update the corresponding file's status from `pending` to `onchain`. Webhooks automate this process without requiring polling or manual updates.

### Workflow

1. User publishes post → Calls `MarketPlace.createPost()` with post CID and metadata
2. Smart contract processes transaction → Emits `PostCreated` event on blockchain
3. Blockchain provider detects event → Alchemy or QuickNode captures event in real-time
4. Provider sends webhook → HTTP POST request to configured webhook URL
5. API validates signature → HMAC-SHA256 verification ensures authenticity
6. API decodes event data → Extracts postCid, postId, and author from event logs
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

## POST /webhook/alchemy/publish

Receives blockchain event notifications from Alchemy's Notify API when posts are published.

### Description

This endpoint processes webhook calls from Alchemy when the `PostCreated` event is emitted by the **MarketPlace** smart contract. Alchemy monitors specified contract addresses and sends HTTP POST requests when matching events occur. The endpoint:
1. Verifies the request signature using HMAC-SHA256
2. Decodes the event data to extract postCid, postId, and author
3. Updates the corresponding file's status from `pending` to `onchain`

---

## POST /webhook/quicknode/publish

Receives blockchain event notifications from QuickNode Streams when posts are published.

### Description

This endpoint processes webhook calls from QuickNode Streams service. Similar to Alchemy webhooks, it monitors the **MarketPlace** contract for `PostCreated` events and updates file status accordingly. QuickNode uses a more sophisticated signature verification method that includes nonce and timestamp in addition to the body payload.

---

### Security Benefits

- **Authenticity:** Proves webhook originates from legitimate provider
- **Integrity:** Detects any tampering during transmission
- **Non-repudiation:** Provider cannot deny sending the webhook
- **Replay Protection:** QuickNode's nonce prevents replay attacks