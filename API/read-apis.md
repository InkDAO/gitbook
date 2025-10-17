# Read APIs

Read APIs allow retrieval of file content and metadata with appropriate access control. These endpoints support fetching draft files, and accessing published assets.

---

## GET /fileByCid

Retrieves the content of a draft file by its IPFS Content Identifier (CID). This endpoint is restricted to file owners only.

### Description

This endpoint fetches file content from IPFS for files in `pending` status (drafts). Only the file owner can access their draft files. Published files (status `onchain`) cannot be accessed through this endpoint.

**Access Control:**
- Requires JWT authentication
- File must be owned by authenticated user
- File must have `pending` status

### Request

**Endpoint:** `GET /fileByCid?cid={cid}`

**Headers:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Example Request:**
```
GET /fileByCid?cid=QmX1234567890abcdefghijklmnopqrstuvwxyz
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Response

**Success Response (200 OK):**

```json
{
  "content": "Your file content here",
  "lang": "ts"
}
```

## GET /pendingFilesByOwner

Retrieves all pending (draft) files owned by a specific Ethereum address, with pagination support.

### Description

This endpoint returns a list of files with `pending` status owned by the specified address. Results are paginated with a maximum of 12 files per page. Use the returned `next_page_token` to fetch subsequent pages.

**Access Control:**
- Requires JWT authentication
- Users can query their own files
- Returns metadata and identifiers for draft files

### Request

**Endpoint:** `GET /pendingFilesByOwner?owner={address}`

**Headers:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Example Request:**
```
GET /pendingFilesByOwner?owner=0x742d35cc6634c0532925a3b844bc9e7595f0beb
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Response

**Success Response (200 OK):**

```json
{
  "files": [
    {
      "id": "01234567-89ab-cdef-0123-456789abcdef",
      "name": "742d35cc6634c0532925a3b844bc9e7595f0beb_7472000000",
      "cid": "QmX1234567890abcdefghijklmnopqrstuvwxyz",
      "size": 2048,
      "created_at": "2024-10-16T12:00:00.000Z",
      "group_id": "01234567-89ab-cdef-0123-456789abcdef",
      "keyvalues": {
        "owner": "0x742d35cc6634c0532925a3b844bc9e7595f0beb",
        "status": "pending"
      }
    }
  ],
  "next_page_token": "eyJvZmZzZXQiOiIxMiJ9"
}
```

---

## GET /filesByOwnerByNextPageToken

Retrieves the next page of files using a pagination token from a previous request.

### Description

This endpoint continues pagination from `/pendingFilesByOwner` by using the `next_page_token` returned in the previous response. Each page returns up to 9 files.

**Access Control:**
- Requires JWT authentication
- Must provide valid pagination token from previous request

### Request

**Endpoint:** `GET /filesByOwnerByNextPageToken?owner={address}&next_page_token={token}`

**Headers:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Example Request:**
```
GET /filesByOwnerByNextPageToken?owner=0x742d35cc6634c0532925a3b844bc9e7595f0beb&next_page_token=eyJvZmZzZXQiOiIxMiJ9
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Response

**Success Response (200 OK):**

```json
{
  "files": [
    {
      "id": "01234567-89ab-cdef-0123-456789abcdef",
      "name": "742d35cc6634c0532925a3b844bc9e7595f0beb_7472000001",
      "cid": "QmY5678901234567890abcdefghijklmnopqrstuvwxyz",
      "size": 3072,
      "created_at": "2024-10-16T12:05:00.000Z",
      "group_id": "01234567-89ab-cdef-0123-456789abcdef",
      "keyvalues": {
        "owner": "0x742d35cc6634c0532925a3b844bc9e7595f0beb",
        "status": "pending"
      }
    }
  ],
  "next_page_token": "eyJvZmZzZXQiOiIyMSJ9"
}
```

---

## GET /fileByAssetAddress

Retrieves published asset content based on ERC20 token ownership. Access is granted to token holders and the asset author.

### Description

This endpoint provides access to published assets (files with `onchain` status). Access is controlled through blockchain smart contracts - users must either own the dXasset ERC20 tokens or be the asset author. The endpoint verifies ownership on-chain before returning content.

**Access Control:**
- Requires JWT authentication
- User must own dXasset tokens OR be the asset author
- Verifies ownership via blockchain smart contract
- Free assets (price = 0) still require authentication but no token ownership

### Request

**Endpoint:** `GET /fileByAssetAddress?user={address}&assetAddress={contractAddress}`

**Headers:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Example Request:**
```
GET /fileByAssetAddress?user=0x742d35cc6634c0532925a3b844bc9e7595f0beb&assetAddress=0x1234567890123456789012345678901234567890
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Response

**Success Response (200 OK):**

```json
{
  "content": "Published asset content here",
  "lang": "ts"
}
```

---

## GET /freeFileByAddress

Retrieves content for assets with zero price (free assets). No authentication or token ownership required.

### Description

This endpoint provides public access to free assets (assets with price set to 0 ETH). It first verifies the asset price on-chain, and only returns content if the price is zero. This allows creators to share content publicly without requiring users to purchase tokens.

**Access Control:**
- No authentication required
- Asset price must be 0 (verified on-chain)
- Public access for everyone

### Request

**Endpoint:** `GET /freeFileByAddress?assetAddress={contractAddress}`

**Example Request:**
```
GET /freeFileByAddress?assetAddress=0x1234567890123456789012345678901234567890
```

### Response

**Success Response (200 OK):**

```json
{
  "content": "Free asset content available to everyone",
  "lang": "ts"
}
```

---