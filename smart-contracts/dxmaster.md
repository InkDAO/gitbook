# DXmaster

## Overview

DXmaster is the central protocol contract managing all assets, purchases, comments, and user interactions. It acts as the main entry point for users interacting with the platform.

## Contract Details

- **Type**: Upgradeable (Initializable)
- **Inherits**: PausableUpgradeable, ReentrancyGuardUpgradeable
- **Address**: Stored in DXconfig under `DXMASTER_ADDRESS`

## Key Responsibilities

1. Asset creation and registration
2. Asset purchase handling
3. Comment management
4. User asset tracking
5. Fee distribution
6. Platform administration

## Fee Structure

Platform fee is calculated as:
```
platformFee = totalAmount × platformFeePercentage / 10000
authorFee = totalAmount - platformFee
```

Platform fee percentage is stored in DXconfig.

## Security Considerations

- Reentrancy protection on all state-changing functions
- Validates all user inputs
- Refunds excess payments automatically
- Verifies native transfers succeed
- Access control on admin functions
- Can be paused in emergencies