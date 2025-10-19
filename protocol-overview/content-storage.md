# Content Storage

Protocol uses a hybrid storage approach, combining IPFS for content storage with Ethereum blockchain for metadata and access control. This architecture provides the benefits of decentralization while maintaining performance and privacy.

## Storage Architecture

### Hybrid Model

**IPFS (InterPlanetary File System)**:
- Stores full post content
- Stores thumbnail images
- Content-addressed storage (CIDs)
- Distributed and censorship-resistant
- Private access control via Pinata groups

**Ethereum Blockchain**:
- Stores post metadata
- Stores ownership records
- Stores access control logic
- Immutable transaction history
- Transparent and auditable

## IPFS Implementation

### Pinata Service

dX uses Pinata for managed IPFS infrastructure:
- Reliable pinning service
- High availability gateways
- Private groups for access control
- API for content management
- SDKs for easy integration

## Data Integrity

### Content Immutability

**IPFS Guarantees**:
- CID derived from content hash
- Any change creates new CID
- Original content unchangeable
- Verifiable by anyone

**Blockchain Guarantees**:
- Transaction history immutable
- Ownership records permanent
- Timestamps verified by miners
- No central authority can alter

## Privacy & Security

### Content Privacy

**Private Content**:
- Stored in Pinata private groups
- Not accessible via public gateways
- Requires backend authentication
- Backend verifies blockchain ownership

**Public Metadata**:
- Title, description, thumbnail public
- Author address public
- Price public
- Asset address public

### Security Measures

**Access Control**:
- JWT authentication for API
- Wallet signature verification
- Blockchain ownership checks
- No credentials stored

**Key Management**:
- Pinata keys server-side only
- Never exposed to client
- Regularly rotated (TODO: verify)
- Environment variable storage

**Attack Vectors**:
- ❌ Brute-force CIDs (infeasible)
- ❌ Replay attacks (nonce in signatures)
- ❌ Unauthorized access (ownership verified)
- ✅ DDoS (rate limiting needed)