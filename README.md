# ZlatnikContract

Core access control foundation for the **Zlatnik (ZLT)** token ecosystem.

## Overview

`ZlatnikContract.sol` provides the role-based access control (RBAC) module used across all Zlatnik smart contracts. It is based on [OpenZeppelin AccessControl v5.4.0](https://docs.openzeppelin.com/contracts/5.x/access-control) and serves as the shared base for:

- `ZLTToken` — ERC20 token (Zlatnik / ZLT, 1 billion supply)
- `ZLTStaking` — Token staking contract
- `ZLTReferral` — Referral rewards with signature-based claims
- `HousingStaking` — Housing program staking
- `AmbassadorRewards` — Ambassador rewards management

## Contract: `AccessControl`

**License:** MIT  
**Solidity:** `^0.8.20`  
**Inherits:** `Context`, `IAccessControl`, `ERC165`

### How Roles Work

Roles are `bytes32` identifiers, typically defined as `keccak256` hash constants:

```solidity
bytes32 public constant ADMIN_ROLE  = keccak256("ADMIN_ROLE");
bytes32 public constant SIGNER_ROLE = keccak256("SIGNER_ROLE");
bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

Access to a function is restricted using the `onlyRole` modifier:

```solidity
function pause() external onlyRole(PAUSER_ROLE) {
    _pause();
}
```

### Built-in Roles

| Role | Identifier | Description |
|------|-----------|-------------|
| `DEFAULT_ADMIN_ROLE` | `0x00` | Admin of all roles; can grant and revoke any role |

### Key Functions

| Function | Visibility | Description |
|----------|-----------|-------------|
| `hasRole(role, account)` | `public view` | Returns `true` if `account` holds `role` |
| `getRoleAdmin(role)` | `public view` | Returns the admin role that controls `role` |
| `grantRole(role, account)` | `public` | Grants `role` to `account`; caller must have the role's admin |
| `revokeRole(role, account)` | `public` | Revokes `role` from `account`; caller must have the role's admin |
| `renounceRole(role, callerConfirmation)` | `public` | Lets an account voluntarily drop its own role |
| `supportsInterface(interfaceId)` | `public view` | ERC165 interface detection |

### Events

| Event | Emitted When |
|-------|-------------|
| `RoleGranted(role, account, sender)` | A role is granted to an account |
| `RoleRevoked(role, account, sender)` | A role is revoked from an account |
| `RoleAdminChanged(role, previousAdmin, newAdmin)` | A role's admin is changed via `_setRoleAdmin` |

### Security Notes

- `DEFAULT_ADMIN_ROLE` is its own admin — accounts holding it can grant/revoke any role, including itself. Secure admin key management is critical.
- For additional admin safety (e.g., two-step transfers, time delays), consider upgrading to `AccessControlDefaultAdminRules`.
- Role membership is not enumerable on-chain; use event logs for off-chain tracking.

## Usage

Inherit from `AccessControl` and define your roles:

```solidity
pragma solidity ^0.8.20;

import "./ZlatnikContract.sol";

contract MyZlatnikModule is AccessControl {
    bytes32 public constant OPERATOR_ROLE = keccak256("OPERATOR_ROLE");

    constructor(address admin) {
        _grantRole(DEFAULT_ADMIN_ROLE, admin);
    }

    function sensitiveAction() external onlyRole(OPERATOR_ROLE) {
        // ...
    }
}
```

## License

MIT
