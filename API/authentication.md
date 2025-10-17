# Authentication

The authentication system uses Ethereum wallet signatures for passwordless authentication, combined with JWT tokens for session management.

## Authentication Flow

1. Client generates a timestamp (salt) representing the current Unix time in seconds
2. Client signs the salt using their Ethereum wallet (EIP-191 personal sign method)
3. Client sends the signature, salt, and Ethereum address to `/auth/login`
4. Server verifies the signature authenticity and recovers the signer's address
5. Server validates that the signature is recent (within 10 seconds) to prevent replay attacks
6. Server issues a JWT token valid for 2 hours
7. Client includes the JWT token in the `Authorization` header for subsequent requests

## Security Mechanisms

### EIP-191 Personal Sign
Uses Ethereum's standard for signing messages with wallet private keys. The message is prefixed with `"\x19Ethereum Signed Message:\n"` to prevent signature reuse on blockchain transactions.

### Time-based Replay Protection
Signatures older than 10 seconds are automatically rejected, preventing attackers from reusing captured signatures.

### Address Verification
The server cryptographically recovers the signer's address from the signature and verifies it matches the claimed address.

### JWT Token Expiration
Tokens automatically expire after 2 hours, limiting the window of exposure if a token is compromised.

---

## POST /auth/login

Authenticates a user using an Ethereum wallet signature and returns a JWT token for subsequent API requests.

### Description

This endpoint provides passwordless authentication by verifying an Ethereum wallet signature. Users sign a timestamp with their wallet, and the API verifies the signature's authenticity. Upon successful verification, a JWT token is issued for accessing protected endpoints.

**Key Features:**
- No passwords required
- Cryptographically secure
- Automatic signature expiration (10 seconds)
- JWT token valid for 2 hours

### Request

**Endpoint:** `POST /auth/login`

**Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "salt": "1697472000",
  "address": "0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb",
  "signature": "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1b"
}
```

### Response

**Success Response (200 OK):**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZGRyZXNzIjoiMHg3NDJkMzVjYzY2MzRjMDUzMjkyNWEzYjg0NGJjOWU3NTk1ZjBiZWIiLCJpYXQiOjE2OTc0NzIwMDAsImV4cCI6MTY5NzQ3OTIwMH0.signature_hash_here",
  "address": "0x742d35cc6634c0532925a3b844bc9e7595f0beb",
  "expiresIn": "2h"
}
```

---

## Using JWT Tokens

After successfully authenticating, include the JWT token in the `Authorization` header for all protected endpoints.

### Header Format

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```