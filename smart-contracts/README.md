# Smart Contracts

## Overview

The dX platform smart contracts enable a decentralized marketplace for digital assets. Users can create, buy, sell, and comment on digital assets stored on IPFS, with ownership tracked on-chain through ERC20 tokens.

## Architecture

The platform consists of four core contracts working together:

### Core Contracts

1. **DXmaster** - Central hub managing assets, purchases, and comments
2. **DXconfig** - Configuration management and access control
3. **DXasset** - ERC20 token representing ownership of digital assets
4. **DXassetFactory** - Factory for deploying new asset tokens

### Contract Interaction Flow

```
User → DXmaster → DXassetFactory → DXasset
              ↓
          DXconfig (access control & settings)
```

## Deployed Contracts

The following contracts are currently deployed on the network:

| Contract | Address |
|----------|---------|
| **DXconfig** | `0x072Fdc9C5C8f3e2f36c776136f849F53C6c7e702` |
| **DXmaster** | `0x332Eac90e80dd8A56eAf8FD156000087Ce08D01C` |
| **DXassetFactory** | `0x9F0445c898DB65CFBAE461116D4B86B17323f4d0` |

> **Note**: DXasset contracts are deployed dynamically when users create new assets on the platform.

## Key Features

- **Asset Creation**: Create tokenized digital assets with IPFS storage
- **Asset Trading**: Buy and sell assets with native cryptocurrency
- **Comments**: Community engagement through asset comments
- **Access Control**: Role-based permissions (Admin, Bot)
- **Upgradeable**: Uses proxy pattern for future improvements
- **Pausable**: Emergency stop mechanism
- **Fee Management**: Platform fee collection from sales

## Technology Stack

- **Solidity**: ^0.8.20
- **OpenZeppelin**: Upgradeable contracts, ERC20, Access Control
- **Foundry**: Development and testing framework
- **Create2**: Deterministic asset deployment

## Configuration Parameters

- **Platform Fee**: Percentage taken from each sale (denominator: 10,000)
- **Max Comment Length**: Character limit for comments
- **Max Asset Title Length**: Character limit for asset titles
- **Max Description Length**: Character limit for descriptions

## Security Features

- Reentrancy protection on critical functions
- Access control for administrative functions
- Input validation and sanity checks
- Pausable functionality for emergencies
- Zero address checks
- Native transfer validation

## Getting Started

See individual contract documentation:

- [DXmaster](dxmaster.md) - Core protocol logic
- [DXconfig](dxconfig.md) - Configuration management
- [DXasset](dxasset.md) - Asset token implementation
- [DXassetFactory](dxassetfactory.md) - Factory contract
- [Utilities](utilities.md) - Helper contracts and libraries

