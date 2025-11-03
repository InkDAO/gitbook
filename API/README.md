# API Documentation

The InkDAO API works in conjunction with smart contracts and IPFS to enable a complete Web3 content publishing and monetization platform. It provides the necessary backend infrastructure for content storage, authentication, and access control while maintaining decentralization principles.

### Architecture

This API is powered by **Edge Functions**, a serverless computing architecture that runs code on globally distributed edge nodes close to end users. By deploying on Netlify's Edge Functions with the Deno runtime, the platform delivers ultra-low latency responses (typically under 50ms), automatic scaling to handle traffic spikes, and zero cold-start delays.

**Technology Stack:**
- **Netlify Edge Functions**: Serverless deployment with global edge network distribution
- **Hono Framework**: Ultrafast web framework optimized for edge computing environments
- **Pinata**: Enterprise-grade IPFS pinning service for decentralized file storage
- **Ethers.js v6**: Industry-standard library for Ethereum blockchain interaction

### Key Features & Benefits

#### 1. Passwordless Authentication
**Technique**: Ethereum wallet signature-based authentication using EIP-4361 (Sign-In with Ethereum) standard

**Benefits:**
- No passwords to remember or manage
- Cryptographically secure authentication using SIWE (Sign-In with Ethereum) protocol
- Standardized authentication format with human-readable messages
- Users maintain full control of their identity
- Seamless Web3 integration with widely adopted standard

#### 2. Decentralized File Storage
**Technique**: IPFS (InterPlanetary File System) with Pinata pinning service

**Benefits:**
- Content-addressed storage (files identified by content hash)
- Censorship-resistant and tamper-proof
- No single point of failure
- Global content distribution

#### 3. JWT Token Session Management
**Technique**: JSON Web Tokens with 2-hour expiration

**Benefits:**
- Stateless authentication (no server-side session storage)
- Scalable across distributed systems
- Automatic token expiration for enhanced security
- Easy to implement client-side token management

#### 4. Blockchain-based Access Control
**Technique**: ERC20 token ownership verification via smart contracts

**Benefits:**
- Transparent and auditable access rules
- Programmable access control logic
- Transferable access rights
- No centralized access control server
- Native cryptocurrency payment integration

#### 5. Webhook Event System
**Technique**: HMAC-SHA256 signed webhooks from Alchemy and QuickNode

**Benefits:**
- Real-time blockchain event notifications
- Automatic status synchronization
- Cryptographically verified event authenticity
- Reliable delivery with built-in retry mechanisms

#### 6. Two-tier File System
**Technique**: Status-based file lifecycle (pending → onchain)

**Benefits:**
- Draft files can be modified before publishing
- Published files are immutable and blockchain-backed
- Cost-effective (only publish finalized content on-chain)

#### 7. Private IPFS Gateway
**Technique**: Pinata's private gateway with access control

**Benefits:**
- Content remains on IPFS but access is controlled
- Fast content delivery via CDN
- Compatible with standard IPFS ecosystem

### Security Features

The API implements multiple layers of security to ensure data integrity and user protection:

1. **EIP-4361 (SIWE) Authentication**: Login uses Sign-In with Ethereum standard for secure, human-readable authentication
2. **EIP-712 Typed Signatures**: All write operations use typed structured data signing for enhanced security and UX
3. **HMAC Webhook Verification**: Webhooks are validated using SHA-256 cryptographic signatures
4. **Ownership Verification**: All file operations verify ownership through smart contracts or metadata
5. **Automatic Token Expiration**: JWT tokens expire after 2 hours to limit exposure window
6. **Time-based Replay Protection**: All signatures include timestamps and are validated within 60 seconds
7. **Domain Separation**: EIP-712 signatures are bound to specific application domains preventing cross-app attacks

### Base URL

```
https://api.inkdao.tech/
```

## API Categories

### [1. Authentication](./authentication.md)
Signature-based authentication and JWT token management

**Endpoint:**
- `POST /auth/login`

### [2. Read APIs](./read-apis.md)
Retrieve files and asset content with proper access control

**Endpoints:**
- `GET /filesByTags` - Get files filtered by hashtags (public)
- `GET /fileByCid` - Get draft file content (owner only)
- `GET /pendingFilesByOwner` - Get user's draft files
- `GET /filesByNextPageToken` - Paginated file retrieval (public)
- `GET /fileByPostId` - Get published post content (requires token ownership)
- `GET /freeFileByPostId` - Get free published post (public)
- `GET /filesMetaData` - Get file metadata (public)

### [3. Write APIs](./write-apis.md)
Create, update, publish, and delete files

**Endpoints:**
- `POST /create/group` - Create initial draft
- `POST /update/file` - Update existing draft
- `POST /publish/file` - Upload thumbnail for publishing
- `POST /delete/file` - Delete draft file

### [4. Webhooks](./webhooks.md)
Blockchain event handlers for automated status updates

**Endpoints:**
- `POST /webhook/alchemy/publish` - Alchemy blockchain event receiver
- `POST /webhook/quicknode/publish` - QuickNode blockchain event receiver

## Support

For technical support, bug reports, or feature requests, please refer to the main repository documentation or contact through official channels.
