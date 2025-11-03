# DXconfig

## Overview

DXconfig is the configuration management and access control contract for the InkDAO platform. It maintains role-based permissions and stores critical protocol parameters in a centralized, upgradeable manner. The contract acts as a registry for both addresses and numeric values used across the platform.

## Contract Details

- **Type**: Upgradeable (Initializable)
- **Inherits**: `AccessControlUpgradeable`
- **Role Management**: Uses OpenZeppelin's AccessControl
- **Solidity Version**: ^0.8.20

## Key Responsibilities

1. **Registry**: Store contract addresses for MarketPlace and other contracts
2. **Parameters**: Store protocol configuration values (fees, limits)
3. **Access Control**: Manage admin roles and permissions
4. **Lookup Service**: Provide configuration access to other contracts

## Roles

### DEFAULT_ADMIN_ROLE (`0x0`)

The admin role has full control over the platform:

**Privileges:**
- Grant and revoke roles
- Update contract addresses
- Update protocol parameters
- Pause/unpause contracts

## Security Considerations

- Centralized configuration requires trust in admin
- Admin can update critical parameters
- Role management follows OpenZeppelin best practices
- Zero address checks on initialization
- Immutable after initialization (upgradeable pattern)
- Key validation prevents accidental overwrites


