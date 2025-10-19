# DXasset

## Overview

DXasset is an ERC20 token contract representing access of a single digital asset. Each post on the platform has its own DXasset token, allowing access.

## Contract Details

- **Type**: Standard (non-upgradeable)
- **Inherits**: ERC20, Ownable
- **Standard**: ERC20 compliant token
- **Deployment**: Via DXassetFactory using Create2

## Key Features

1. **ERC20 Compliance**: Fully transferable tokens
2. **Asset Metadata**: Stores IPFS references and pricing
3. **Access Tracking**: Author receives royalties
4. **Dynamic Pricing**: Owner can update price
5. **Mintable**: New tokens can be minted on purchase
6. **Burnable**: Tokens can be burned by holders

## Token Metadata

### ERC20 Standard Functions

- `name()`: Returns token name
- `symbol()`: Returns token symbol
- `decimals()`: Returns 18 (default)
- `totalSupply()`: Returns total tokens minted
- `balanceOf(address)`: Returns balance of address
- `transfer(address, uint256)`: Transfer tokens
- `approve(address, uint256)`: Approve spending
- `transferFrom(address, address, uint256)`: Transfer from approved address

### Custom Metadata

- `assetCid()`: IPFS content identifier
- `assetTitle()`: Human-readable title
- `thumbnailCid()`: IPFS thumbnail
- `description()`: Asset description
- `costInNativeInWei()`: Current price per token

## Token Economics

### Unlimited Supply
- No maximum supply cap
- Tokens minted on each purchase
- Supply grows with demand

### Pricing
- Price set by asset author
- Can be updated anytime by owner
- Price in wei (18 decimals)

### Ownership
- Initial owner is asset creator (author)
- Ownership transferable
- Owner receives payment on sales (minus platform fee)

## Integration with DXmaster

### Purchase Flow
1. User calls `DXmaster.buyAsset()`
2. DXmaster validates payment
3. DXmaster calls `DXasset.mint()`
4. Tokens transferred to buyer
5. Payment distributed to author and platform

### Transfer Hook
1. User initiates token transfer
2. `_update()` called automatically
3. DXmaster's `beforeTokenTransfer()` invoked
4. User asset tracking updated
5. Transfer completes

## Security Considerations

- Standard ERC20 security properties
- Owner privileges limited to price updates
- Minting restricted to DXmaster and owner
- Transfer hook maintains consistency with DXmaster state
- No emergency pause (managed by DXmaster)
- Immutable metadata (except price)


