# Read APIs

Read APIs allow retrieval of file content and metadata with appropriate access control. These endpoints support fetching draft files, and accessing published assets.

---

## GET /filesByTags

Retrieves files filtered by hashtags with pagination support. This endpoint allows public access to discover content by categories.

### Description

This endpoint returns a list of files that match the specified hashtags. Multiple hashtags can be provided as a comma-separated list. Results are paginated with a maximum of 9 files per page. Use the returned `next_page_token` with `/filesByNextPageToken` to fetch subsequent pages.

**Access Control:**
- Public access (no authentication required)
- Returns metadata for files tagged with the specified hashtags

### Request

**Endpoint:** `GET /filesByTags?tags={tag1,tag2,tag3}`

**Query Parameters:**
- **tags**: Comma-separated list of hashtags to filter by (e.g., "web3,crypto,tutorial")

**Example Request:**
```
GET /filesByTags?tags=web3,crypto,defi
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
        "web3": "web3",
        "crypto": "crypto",
        "defi": "defi",
        "publishedAt": "2024-10-16T12:00:00.000Z"
      }
    }
  ],
  "count": 1,
  "tags": ["web3", "crypto", "defi"],
  "next_page_token": "eyJvZmZzZXQiOiI5In0"
}
```

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

## GET /filesByNextPageToken

Retrieves the next page of files using a pagination token from a previous request.

### Description

This endpoint continues pagination from any previous list request by using the `next_page_token` returned in the previous response. Each page returns up to 9 files. This endpoint is generic and works with any pagination token.

**Access Control:**
- No authentication required (public access)
- Must provide valid pagination token from previous request

### Request

**Endpoint:** `GET /filesByNextPageToken?next_page_token={token}`

**Example Request:**
```
GET /filesByNextPageToken?next_page_token=eyJvZmZzZXQiOiIxMiJ9
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

## GET /fileByPostId

Retrieves published asset content based on ERC-6909 token ownership. Access is granted to token holders and the post author.

### Description

This endpoint provides access to published posts (files with `onchain` status). Access is controlled through blockchain smart contracts - users must either own the post tokens (ERC-6909) or be the post author. The endpoint verifies ownership on-chain before returning content.

**Access Control:**
- Requires JWT authentication
- User must own post tokens (ERC-6909 balance > 0) OR be the post author
- Verifies ownership via blockchain smart contract (MarketPlace contract)
- Free posts (price = 0) still require authentication but no token ownership

### Request

**Endpoint:** `GET /fileByPostId?user={address}&postId={tokenId}`

**Headers:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Query Parameters:**
- **user**: The Ethereum address requesting access
- **postId**: The token ID of the post (from the MarketPlace contract)

**Example Request:**
```
GET /fileByPostId?user=0x742d35cc6634c0532925a3b844bc9e7595f0beb&postId=1
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

## GET /freeFileByPostId

Retrieves content for posts with zero price (free posts). No authentication or token ownership required.

### Description

This endpoint provides public access to free posts (posts with price set to 0). It first verifies the post price on-chain, and only returns content if the price is zero. This allows creators to share content publicly without requiring users to purchase tokens.

**Access Control:**
- No authentication required
- Post price must be 0 (verified on-chain via MarketPlace contract)
- Public access for everyone

### Request

**Endpoint:** `GET /freeFileByPostId?postId={tokenId}`

**Query Parameters:**
- **postId**: The token ID of the post (from the MarketPlace contract)

**Example Request:**
```
GET /freeFileByPostId?postId=1
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

## GET /filesMetaData

Retrieves metadata for files, optionally filtered by CID. Returns file information without content.

### Description

This endpoint returns metadata for files stored in IPFS via Pinata. If no CID is provided, it returns up to 9 recent files. If a CID is provided, it returns metadata for that specific file. This is useful for getting file information without downloading the actual content.

**Access Control:**
- Public access (no authentication required)
- Returns file metadata only (no content)

### Request

**Endpoint:** `GET /filesMetaData` or `GET /filesMetaData?cid={fileCid}`

**Query Parameters (Optional):**
- **cid**: The IPFS CID of a specific file to query

**Example Requests:**
```
GET /filesMetaData
GET /filesMetaData?cid=QmX1234567890abcdefghijklmnopqrstuvwxyz
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
        "status": "pending",
        "web3": "web3"
      }
    }
  ],
  "next_page_token": "eyJvZmZzZXQiOiI5In0"
}
```

---