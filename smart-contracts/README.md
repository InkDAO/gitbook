# Smart Contracts

## Overview

The InkDAO platform smart contracts enable a decentralized content publishing and monetization platform. Creators publish content to IPFS with access controlled through blockchain-verified ownership. Each post becomes a unique ERC20 token that readers purchase for lifetime access.

## Architecture

The platform consists of four core contracts working together to create a seamless content monetization system:

### Core Contracts

1. **DXmaster** - Central hub for asset creation, purchases, and platform logic
2. **DXconfig** - Configuration management and access control
3. **DXasset** - ERC20 token representing access rights to content
4. **DXassetFactory** - Factory for deploying new asset token contracts

### How They Work Together

```
Creator → DXmaster.addAsset() → DXassetFactory.createAsset() → New DXasset Contract
                                                                        ↓
Reader → DXmaster.buyAsset() → Payment to Creator + DXasset tokens minted to Reader
                                    ↓
                              DXconfig (stores configuration)
```

**Publishing Flow**:
1. Creator uploads content to IPFS (private)
2. Creator calls `addAsset()` with metadata and price
3. DXassetFactory deploys new ERC20 contract for the post
4. Metadata stored on-chain, CID recorded
5. Post visible on platform

**Subscription Flow**:
1. Reader finds post on platform
2. Reader calls `buyAsset()` with payment
3. Payment split: 95% to creator's wallet, 5% to platform
4. DXasset tokens minted to reader
5. Backend verifies token ownership
6. Full content served from IPFS

## Deployed Contracts (Sepolia Testnet)

The following contracts are currently deployed on the Sepolia testnet:

| Contract | Address |
|----------|---------|
| **DXconfig** | `0x072Fdc9C5C8f3e2f36c776136f849F53C6c7e702` |
| **DXmaster** | `0x332Eac90e80dd8A56eAf8FD156000087Ce08D01C` |
| **DXassetFactory** | `0x9F0445c898DB65CFBAE461116D4B86B17323f4d0` |

> **Note**: Each published post creates a new DXasset contract with a unique address. These are deployed dynamically using CREATE2 for deterministic addresses.

## Key Features

### Content Monetization
- **Creator-Set Pricing**: Each post has individual pricing in ETH
- **Direct Payments**: 95% to creator's wallet, 5% to platform infrastructure
- **Lifetime Access**: One-time purchase for permanent access rights
- **Tokenized Ownership**: Access proven through ERC20 token ownership

### Technical Features
- **ERC20 Standard**: Compatible with existing wallets and tools
- **Upgradeable**: Uses proxy pattern for future improvements
- **Pausable**: Emergency stop mechanism for security
- **Access Control**: Role-based permissions (Admin)
- **Deterministic Deployment**: CREATE2 for predictable addresses
- **Event Logging**: Comprehensive event emission for indexing

### Security Features
- Reentrancy protection on all state-changing functions
- Input validation and sanity checks
- Pausable functionality for emergencies
- Zero address checks
- Native transfer validation with revert on failure
- Platform fee (5%) for infrastructure sustainability

## Technology Stack

- **Solidity**: ^0.8.20
- **OpenZeppelin**: Upgradeable contracts, ERC20, Access Control
- **Foundry**: Development and testing framework
- **Create2**: Deterministic asset deployment

## Configuration Parameters

- **Platform Fee**: 5% (500/10,000) for infrastructure costs (IPFS, servers, deployment)
- **Max Comment Length**: Character limit for comments
- **Max Asset Title Length**: Character limit for asset titles
- **Max Description Length**: Character limit for descriptions

See individual contract documentation:

- [DXmaster](dxmaster.md) - Core protocol logic
- [DXconfig](dxconfig.md) - Configuration management
- [DXasset](dxasset.md) - Asset token implementation
- [DXassetFactory](dxassetfactory.md) - Factory contract
- [Utilities](utilities.md) - Helper contracts and libraries