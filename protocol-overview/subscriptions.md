# Subscriptions

Subscriptions are the core monetization mechanism on dX. Unlike traditional subscription models with recurring payments, dX implements a one-time payment model where users gain lifetime access to content through blockchain-verified ownership.

## How Subscriptions Work

### The Purchase Process

1. **Discovery**: User browses posts on the homepage and finds interesting content
2. **Preview**: User views post metadata (title, description, thumbnail, author, price)
3. **Purchase Decision**: User decides to purchase access to the full content
4. **Payment**: User initiates a purchase transaction from the post preview page
5. **Token Minting**: DXmaster contract mints DXasset tokens to the buyer
6. **Verification**: Backend verifies ownership by checking token balance
7. **Access Granted**: Full content is served from IPFS to the verified owner

### Technical Implementation

When a user purchases access:
- They send ETH to the DXmaster contract
- The contract verifies the payment matches the asset price × amount
- DXasset tokens are minted to the buyer's address
- Payment is forwarded to the content creator
- Ownership is permanently recorded on blockchain

#### Access Verification
The backend verifies ownership before serving content:

1. **Check Token Balance**: Query DXasset contract for buyer's token balance
2. **Validate Ownership**: Balance > 0 means user has access
3. **Serve Content**: Fetch content from IPFS and return to user
4. **Cache Decision**: No caching of ownership checks for security

## Access Rights

### What Subscribers Get

**Lifetime Access**:
- One-time payment, permanent access
- No expiration date
- No recurring fees
- Access survives wallet transfers (via token transfer)

**Full Content**:
- Complete post content
- All embedded media
- Images, videos, code snippets

**Token Ownership**:
- ERC20 tokens (DXasset)
- Transferable to other wallets
- Can be sold or gifted
- Verified on blockchain

## Free Content

### Zero-Cost Access

Posts with price = 0 are considered "free":
- No purchase required
- Accessible to anyone
- Still stored on IPFS (same infrastructure)
- No authentication needed