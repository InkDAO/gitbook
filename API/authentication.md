# Authentication

The authentication system uses Ethereum wallet signatures for passwordless authentication, combined with JWT tokens for session management. The platform implements **EIP-4361 (Sign-In with Ethereum)** for login and **EIP-712 (Typed Structured Data)** for write operations.

## Authentication Flow

### Login Authentication (EIP-4361)

1. Client generates a SIWE (Sign-In with Ethereum) compliant message containing:
   - Domain (URI)
   - Address (user's Ethereum address)
   - Statement (optional human-readable description)
   - URI (optional application-specific identifier)
   - Version (SIWE version, typically "1")
   - Chain ID (blockchain network identifier)
   - Nonce (unique random string for each request)
   - Issued At (ISO 8601 datetime string)
2. Client signs the SIWE message using their Ethereum wallet
3. Client sends the signature, SIWE message (as salt), and Ethereum address to `/auth/login`
4. Server verifies the signature authenticity and recovers the signer's address
5. Server validates that the signature is recent (within 60 seconds based on "Issued At" timestamp) to prevent replay attacks
6. Server issues a JWT token valid for 2 hours
7. Client includes the JWT token in the `Authorization` header for subsequent read requests

### Write Operations Authentication (EIP-712)

For all write operations (create, update, publish, delete), the system uses **EIP-712 typed structured data signing** instead of JWT tokens:

1. Client constructs a typed data structure with:
   - **Domain**: Contains contract/app-specific information
   - **Types**: Defines the structure of the message (e.g., CreateFile, UpdateFile, PublishFile, DeleteFile)
   - **PrimaryType**: Specifies which type to use
   - **Message**: Contains the actual data including timestamp and nonce
2. Client signs the typed data using their Ethereum wallet (EIP-712 signature)
3. Client sends the signature along with the typed data structure
4. Server verifies the typed signature and validates timestamp (within 60 seconds)

## Security Mechanisms

### EIP-4361 (Sign-In with Ethereum)
Uses the standardized SIWE protocol for secure, human-readable authentication messages. This provides:
- **Human-readable messages**: Users can see exactly what they're signing
- **Domain binding**: Prevents phishing attacks by binding signatures to specific domains
- **Standardization**: Compatible with major wallets and dApps
- **Nonce protection**: Prevents replay attacks with unique nonces

### Time-based Replay Protection
Signatures older than 60 seconds are automatically rejected, preventing attackers from reusing captured signatures.

### Address Verification
The server cryptographically recovers the signer's address from the signature and verifies it matches the claimed address.

### JWT Token Expiration
Tokens automatically expire after 2 hours, limiting the window of exposure if a token is compromised.

---

## POST /auth/login

Authenticates a user using an EIP-4361 (Sign-In with Ethereum) signature and returns a JWT token for subsequent API requests.

### Description

This endpoint provides passwordless authentication by verifying an EIP-4361 compliant SIWE (Sign-In with Ethereum) message signature. Users sign a human-readable SIWE message with their wallet, and the API verifies the signature's authenticity. Upon successful verification, a JWT token is issued for accessing protected read endpoints.

**Key Features:**
- No passwords required
- EIP-4361 (SIWE) standard compliance
- Human-readable authentication messages
- Automatic signature expiration (60 seconds based on "Issued At" timestamp)
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
  "salt": "example.com wants you to sign in with your Ethereum account:\n0x742d35Cc6634C0532925a3b844Bc9e7595f0bEb\n\nWelcome to InkDAO!\n\nURI: https://inkdao.tech\nVersion: 1\nChain ID: 1\nNonce: abc123xyz789\nIssued At: 2024-10-16T12:00:00.000Z",
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