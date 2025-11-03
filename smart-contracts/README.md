# Smart Contracts

## Overview

The InkDAO platform smart contracts enable a decentralized content publishing and monetization platform. Creators publish content to IPFS with access controlled through blockchain-verified ownership. The platform uses **ERC-6909 multi-token standard** to manage all posts within a single contract, eliminating the need for deploying individual token contracts per post.

## Architecture

The platform consists of two core contracts working together to create a seamless content monetization system:

### Core Contracts

1. **MarketPlace** - Central hub using ERC-6909 for post creation, subscriptions, and platform logic
2. **DXconfig** - Configuration management and access control

### ERC-6909 Multi-Token Standard

**Why ERC-6909?**
- **Gas Efficiency**: No contract deployment per post (massive gas savings)
- **Simplified Architecture**: Single contract manages all posts
- **Scalability**: Unlimited posts without deploying new contracts
- **Token IDs**: Each post is identified by a unique token ID
- **Non-Transferable**: Tokens are soul-bound to subscribers (access rights cannot be traded)

### How They Work Together

```
Creator → MarketPlace.createPost() → Token ID assigned + Metadata stored
                                              ↓
Reader → MarketPlace.subscribePost() → Payment to Creator + Token minted to Reader
                                              ↓
                                        DXconfig (stores configuration)
```

**Publishing Flow**:
1. Creator uploads content to IPFS (private)
2. Creator calls `createPost()` with metadata and price
3. MarketPlace assigns unique token ID and stores metadata on-chain
4. PostCreated event emitted with token ID and CID
5. Post visible on platform

**Subscription Flow**:
1. Reader finds post on platform
2. Reader calls `subscribePost(tokenId)` with payment
3. Payment split: ~95% to creator's wallet, ~5% to platform
4. ERC-6909 token minted to reader (1 unit of tokenId)
5. Backend verifies token balance via `balanceOf(reader, tokenId)`
6. Full content served from IPFS

## Deployed Contracts

The following contracts are currently deployed:

| Contract | Network | Address |
|----------|---------|---------|
| **DXconfig** | Sepolia | TBD |
| **MarketPlace** | Sepolia | TBD |

> **Note**: With ERC-6909, all posts are managed within the single MarketPlace contract. Each post is identified by a unique token ID rather than having its own contract address.

## Key Features

### Content Monetization
- **Creator-Set Pricing**: Each post has individual pricing in native currency (ETH/MATIC)
- **Direct Payments**: ~95% to creator's wallet, ~5% to platform infrastructure
- **Lifetime Access**: One-time subscription for permanent access rights
- **Tokenized Ownership**: Access proven through ERC-6909 token balance
- **Non-Transferable**: Soul-bound tokens prevent resale of access rights

### Technical Features
- **ERC-6909 Multi-Token**: All posts managed in one contract with unique token IDs
- **Gas Efficient**: Eliminates costly contract deployment per post
- **Upgradeable**: Uses proxy pattern for future improvements
- **Pausable**: Emergency stop mechanism for security
- **Access Control**: Role-based permissions (Admin)
- **Event Logging**: Comprehensive event emission for indexing
- **Token Supply Tracking**: Built-in supply management per token ID

### Security Features
- Reentrancy protection on all state-changing functions
- Input validation and sanity checks (title length, description, price limits)
- Pausable functionality for emergencies
- Zero address checks
- Native transfer validation with revert on failure
- Platform fee (~5%) for infrastructure sustainability
- Non-transferable tokens prevent unauthorized access trading
- Duplicate subscription prevention

## Technology Stack

- **Solidity**: ^0.8.21
- **OpenZeppelin**: Upgradeable contracts, ERC-6909, Access Control
- **Foundry**: Development and testing framework
- **ERC-6909**: Multi-token standard for efficient token management

## Configuration Parameters

- **Platform Fee**: ~5% (500/10,000) for infrastructure costs (IPFS, servers, deployment)
- **Max Post Title Length**: Character limit for post titles
- **Max Description Length**: Character limit for descriptions  
- **Max Price In Native**: Maximum allowed price per post

## Token ID System

Each post is assigned a unique token ID starting from 1:
- Token IDs are sequential and auto-incrementing
- Token ID 0 is reserved/invalid
- Mapping from CID to Token ID for lookups
- Each subscriber receives 1 unit of the token ID

See individual contract documentation:

- [MarketPlace](marketplace.md) - Core ERC-6909 marketplace contract
- [DXconfig](dxconfig.md) - Configuration management