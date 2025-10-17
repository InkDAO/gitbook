# Write APIs

Write APIs allow creating, updating, publishing, and deleting files. These endpoints use signature-based authentication for security without requiring JWT tokens.

---

## POST /create/group

Creates a new Pinata group and uploads the initial draft file with `pending` status.

### Description

This endpoint initializes a new file group and creates the first draft file. Each group is uniquely named using the owner's address and timestamp, preventing conflicts. The file is uploaded to IPFS via Pinata and marked as `pending` status, indicating it's a draft that can be modified.

**Authentication:**
- Uses signature-based authentication (no JWT required)
- Signature must be fresh (within 10 seconds)
- Group name prevents replay attacks

### Request

**Endpoint:** `POST /create/group`

**Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "salt": "1697472000",
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "signature": "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1b",
  "content": "Initial file content here"
}
```

### Response

**Success Response (200 OK):**

```json
{
  "updatedUpload": {
    "id": "01234567-89ab-cdef-0123-456789abcdef",
    "name": "742d35cc6634c0532925a3b844bc9e7595f0beb_7472000000",
    "cid": "QmX1234567890abcdefghijklmnopqrstuvwxyz",
    "size": 1024,
    "created_at": "2024-10-16T12:00:00.000Z",
    "group_id": "01234567-89ab-cdef-0123-456789abcdef",
    "keyvalues": {
      "owner": "0x742d35cc6634c0532925a3b844bc9e7595f0beb",
      "status": "pending"
    }
  }
}
```

---

## POST /update/file

Updates an existing draft file by deleting the old version and uploading a new one with updated content.

### Description

This endpoint replaces an existing draft file with new content. The old file is deleted from IPFS (unpinned), and a new version is uploaded with a fresh CID. This operation is only allowed on files with `pending` status - published files cannot be modified.

**Authentication:**
- Uses signature-based authentication (no JWT required)
- File must be owned by the signer
- File must have `pending` status

### Request

**Endpoint:** `POST /update/file?cid={existingCid}`

**Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "salt": "1697472100",
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "signature": "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1b",
  "content": "Updated file content"
}
```

### Response

**Success Response (200 OK):**

```json
{
  "upload": {
    "id": "98765432-10fe-dcba-9876-543210fedcba",
    "name": "742d35cc6634c0532925a3b844bc9e7595f0beb_7472000100",
    "cid": "QmY5678901234567890abcdefghijklmnopqrstuvwxyz",
    "size": 2048,
    "created_at": "2024-10-16T12:01:40.000Z",
    "group_id": "01234567-89ab-cdef-0123-456789abcdef",
    "keyvalues": {
      "owner": "0x742d35cc6634c0532925a3b844bc9e7595f0beb",
      "status": "pending"
    }
  }
}
```

---

## POST /publish/file

Uploads a thumbnail image to prepare for publishing an asset to the blockchain.

### Description

This endpoint uploads a thumbnail image (preview image) that will be publicly accessible via IPFS. The thumbnail CID is returned and should be included when calling the blockchain smart contract to publish the asset. Unlike draft files, thumbnails are uploaded to public IPFS storage.

**Authentication:**
- Uses signature-based authentication (no JWT required)
- File must be owned by the signer
- File must have `pending` status

### Request

**Endpoint:** `POST /publish/file?cid={fileCid}`

**Headers:**
```
Content-Type: multipart/form-data
```

**Example FormData:**
```
file: [Binary PNG data]
salt: "1697472200"
address: "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb"
signature: "0x1234567890abcdef..."
```

### Response

**Success Response (200 OK):**

```json
{
  "thumbnailCid": "QmT9876543210fedcba9876543210fedcba987654"
}
```

### Publishing Workflow

1. Call `POST /publish/file` to upload thumbnail → Get `thumbnailCid`
2. Call blockchain smart contract's `addAsset()` function with:
   - `assetCid` (original file CID)
   - `thumbnailCid` (from this endpoint)
   - Other metadata (title, description, price)
3. Webhook automatically updates file status to `onchain`

---

## POST /delete/file

Deletes a draft file from IPFS. Only files with `pending` status can be deleted.

### Description

This endpoint permanently deletes a draft file from Pinata/IPFS. The file is unpinned and becomes inaccessible. Published files (status `onchain`) cannot be deleted as they are immutably referenced on the blockchain.

**Authentication:**
- Uses signature-based authentication (no JWT required)
- File must be owned by the signer
- File must have `pending` status

### Request

**Endpoint:** `POST /delete/file?cid={fileCid}`

**Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "salt": "1697472300",
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "signature": "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1b"
}
```

### Response

**Success Response (200 OK):**

```json
{
  "deletedFile": {
    "id": "01234567-89ab-cdef-0123-456789abcdef",
    "status": "deleted"
  }
}
```

---

## Authentication Requirements

All write APIs use signature-based authentication instead of JWT tokens. This provides transaction-level security without requiring prior login.

### Signature Generation Process

1. Generate `salt`: Current Unix timestamp in seconds
2. Sign the salt using Ethereum wallet (EIP-191 personal sign)
3. Include `salt`, `address`, and `signature` in request body
4. Server validates signature is fresh (<10 seconds) and authentic

### Why Signature-Based?

- **Transaction Security**: Each write operation is individually authenticated
- **No Session Required**: No need to maintain JWT tokens for writes
- **Replay Protection**: Time-limited signatures prevent reuse
- **Atomic Operations**: Authentication tied directly to the operation

---