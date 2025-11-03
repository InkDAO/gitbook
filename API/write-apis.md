# Write APIs

Write APIs allow creating, updating, publishing, and deleting files. These endpoints use **EIP-712 typed structured data signature-based authentication** for security without requiring JWT tokens. Each operation has its own typed data structure ensuring type-safe and wallet-friendly authentication.

---

## POST /create/group

Creates a new Pinata group and uploads the initial draft file with `pending` status.

### Description

This endpoint initializes a new file group and creates the first draft file. Each group is uniquely named using a nonce to prevent conflicts. The file is uploaded to IPFS via Pinata and marked as `pending` status, indicating it's a draft that can be modified.

**Authentication:**
- Uses **EIP-712 typed data signature** (no JWT required)
- Signature must be fresh (within 60 seconds based on timestamp)
- Unique nonce prevents replay attacks

### Request

**Endpoint:** `POST /create/group`

**Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "salt": {
    "domain": {
      "name": "InkDAO",
      "version": "1",
      "chainId": 1
    },
    "types": {
      "CreateFile": [
        { "name": "action", "type": "string" },
        { "name": "nonce", "type": "string" },
        { "name": "timestamp", "type": "string" }
      ]
    },
    "message": {
      "action": "create",
      "nonce": "742d35cc6634c0532925a3b844bc9e7595f0beb_1697472000",
      "timestamp": "1697472000"
    }
  },
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
- Uses **EIP-712 typed data signature** (no JWT required)
- File must be owned by the signer
- File must have `pending` status
- Signature must be fresh (within 60 seconds)

### Request

**Endpoint:** `POST /update/file?cid={existingCid}`

**Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "salt": {
    "domain": {
      "name": "InkDAO",
      "version": "1",
      "chainId": 1
    },
    "types": {
      "UpdateFile": [
        { "name": "action", "type": "string" },
        { "name": "nonce", "type": "string" },
        { "name": "timestamp", "type": "string" }
      ]
    },
    "message": {
      "action": "update",
      "nonce": "742d35cc6634c0532925a3b844bc9e7595f0beb_1697472100",
      "timestamp": "1697472100"
    }
  },
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

This endpoint uploads a thumbnail image (preview image) that will be publicly accessible via IPFS. The thumbnail CID is returned and should be included when calling the blockchain smart contract to publish the asset. Unlike draft files, thumbnails are uploaded to public IPFS storage. The endpoint also accepts hashtags for categorizing the published content.

**Authentication:**
- Uses **EIP-712 typed data signature** (no JWT required)
- File must be owned by the signer
- File must have `pending` status
- Signature must be fresh (within 60 seconds)

### Request

**Endpoint:** `POST /publish/file?cid={fileCid}`

**Headers:**
```
Content-Type: multipart/form-data
```

**Example FormData:**
```
file: [Binary PNG/JPG data for thumbnail]
salt: {
  "domain": {
    "name": "InkDAO",
    "version": "1",
    "chainId": 1
  },
  "types": {
    "PublishFile": [
      { "name": "action", "type": "string" },
      { "name": "nonce", "type": "string" },
      { "name": "timestamp", "type": "string" }
    ]
  },
  "message": {
    "action": "publish",
    "nonce": "742d35cc6634c0532925a3b844bc9e7595f0beb_1697472200",
    "timestamp": "1697472200"
  }
}
address: "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb"
signature: "0x1234567890abcdef..."
hashtags: "web3,crypto,tutorial"
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
2. Call blockchain smart contract's **MarketPlace** `createPost()` function with:
   - `postCid` (original file CID)
   - `thumbnailCid` (from this endpoint)
   - Other metadata (title, description, price, hashtags)
3. Smart contract emits `PostCreated` event
4. Webhook automatically updates file status from `pending` to `onchain`

---

## POST /delete/file

Deletes a draft file from IPFS. Only files with `pending` status can be deleted.

### Description

This endpoint permanently deletes a draft file from Pinata/IPFS. The file is unpinned and becomes inaccessible. Published files cannot be deleted as they are immutably referenced on the blockchain. The endpoint verifies that the file is not already published on-chain before allowing deletion.

**Authentication:**
- Uses **EIP-712 typed data signature** (no JWT required)
- File must be owned by the signer
- File must have `pending` status (not published on-chain)
- Signature must be fresh (within 60 seconds)

### Request

**Endpoint:** `POST /delete/file?cid={fileCid}`

**Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "salt": {
    "domain": {
      "name": "InkDAO",
      "version": "1",
      "chainId": 1
    },
    "types": {
      "DeleteFile": [
        { "name": "action", "type": "string" },
        { "name": "nonce", "type": "string" },
        { "name": "timestamp", "type": "string" }
      ]
    },
    "message": {
      "action": "delete",
      "nonce": "742d35cc6634c0532925a3b844bc9e7595f0beb_1697472300",
      "timestamp": "1697472300"
    }
  },
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

All write APIs use **EIP-712 typed structured data signature-based authentication** instead of JWT tokens. This provides transaction-level security with enhanced type safety and wallet user experience.

### EIP-712 Signature Generation Process

1. **Construct typed data structure**: Create a structured object containing:
   - **Domain**: Application-specific information (name, version, chainId)
   - **Types**: Type definitions for the specific operation (CreateFile, UpdateFile, PublishFile, DeleteFile)
   - **PrimaryType**: The primary type being signed
   - **Message**: The actual data with action, unique nonce, and timestamp

2. **Sign with wallet**: Use EIP-712 signing (e.g., `eth_signTypedData_v4`) with your Ethereum wallet
   - Modern wallets display the structured data clearly to users
   - Users can see exactly what action they're authorizing

3. **Submit request**: Include the typed data structure (as `salt`), `address`, and `signature` in request body

4. **Server verification**: Server validates:
   - Signature authenticity using EIP-712 verification
   - Timestamp is fresh (within 60 seconds)
   - Nonce uniqueness (prevents replay attacks)

### Why EIP-712 for Write Operations?

- **Type Safety**: Strongly typed message structures prevent errors
- **Better UX**: Wallets display structured, human-readable data instead of hex strings
- **Domain Separation**: Signatures are bound to specific application domains
- **Transaction Security**: Each write operation is individually authenticated with its own type
- **No Session Required**: No need to maintain JWT tokens for writes
- **Replay Protection**: Timestamp and nonce combination prevents signature reuse
- **Atomic Operations**: Authentication tied directly to the specific operation type
- **Standard Compliance**: EIP-712 is a widely adopted Ethereum standard

### Supported Operation Types

Each write operation has its own EIP-712 type definition:

- **CreateFile**: For creating new draft files (`POST /create/group`)
- **UpdateFile**: For updating existing draft files (`POST /update/file`)
- **PublishFile**: For publishing files and uploading thumbnails (`POST /publish/file`)
- **DeleteFile**: For deleting draft files (`POST /delete/file`)

All types include:
- `action` (string): The operation being performed
- `nonce` (string): Unique identifier (typically address_timestamp)
- `timestamp` (string): Unix timestamp in seconds for replay protection

---