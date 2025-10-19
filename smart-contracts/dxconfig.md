# DXconfig

## Overview

DXconfig is the configuration management and access control contract for the dX platform. It maintains role-based permissions and stores critical protocol parameters.

## Contract Details

- **Type**: Upgradeable (Initializable)
- **Inherits**: AccessControlUpgradeable
- **Role Management**: Uses OpenZeppelin's AccessControl

## Key Responsibilities

1. Store contract addresses (registry)
2. Store protocol parameters
3. Manage access control roles
4. Provide configuration lookup

## Roles

### DEFAULT_ADMIN_ROLE (`0x0`)
- Full administrative privileges
- Can grant/revoke roles
- Update configurations
- Pause/unpause contracts

## Security Considerations

- Centralized configuration requires trust in admin
- Admin can update critical parameters
- Role management follows OpenZeppelin best practices
- Zero address checks on initialization
- Immutable after initialization (upgradeable pattern)
- Key validation prevents accidental overwrites


