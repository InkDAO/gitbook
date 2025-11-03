# MarketPlace

## Overview

MarketPlace is the core protocol contract implementing the **ERC-6909 multi-token standard** for decentralized content publishing and monetization. Unlike traditional approaches that deploy a separate ERC-20 contract for each post, MarketPlace manages all posts within a single contract using unique token IDs, providing significant gas savings and architectural simplicity.

## Contract Details

- **Type**: Upgradeable (Initializable)
- **Inherits**: 
  - `PausableUpgradeable` - Emergency stop mechanism
  - `ReentrancyGuardUpgradeable` - Protection against reentrancy attacks
  - `ERC6909Upgradeable` - Multi-token standard implementation
  - `ERC6909TokenSupplyUpgradeable` - Token supply tracking
- **Standard**: ERC-6909 Multi-Token
- **Solidity Version**: ^0.8.21

## ERC-6909 Multi-Token Standard

### What is ERC-6909?

ERC-6909 is a minimal multi-token interface that allows a single contract to manage multiple token types. Each token type is identified by a unique `uint256` token ID.

**Key Differences from ERC-20:**

| Feature | ERC-20 | ERC-6909 |
|---------|--------|----------|
| Tokens per Contract | 1 | Unlimited |
| Contract Deployment | Per token | Once |
| Gas Cost | ~2-3M per token | ~200k per token |
| Balance Query | `balanceOf(address)` | `balanceOf(address, uint256)` |
| Transfer | `transfer(to, amount)` | `transfer(to, id, amount)` |

### Implementation in MarketPlace

- Each post is assigned a unique token ID
- Token IDs start from 1 (0 is reserved/invalid)
- Subscribers receive 1 unit of the corresponding token ID
- Tokens are **non-transferable** (soul-bound to subscriber)
- All posts managed in a single contract

---

## Resources

- [ERC-6909 Specification](https://eips.ethereum.org/EIPS/eip-6909)
- [OpenZeppelin ERC-6909 Implementation](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable)
- [InkDAO Platform Documentation](../README.md)

