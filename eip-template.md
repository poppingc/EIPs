---
title: Time-lock Extension for ERC20
description: An extension to the ERC20 standard with methods for allocating tokens and locking tokens based on time.
author: Mike <mike@sss.org>
discussions-to: <https://ethereum-magicians.org/t/eip-5489-nft-hyperlink-extension/10431>
status: Draft
type: Standards Track
category: ERC
created: 2023-03-08
requires: 20
---

## Abstract

This EIP introduces general methods for allocating tokens and automatically time-locking tokens according to the release schedule within a contract to extend the ERC20 standard, without the need of transferring tokens to an external escrow smart contract.
It also provides an interface for all third parties to query the lock-up balance of each allocation, token release schedule, and calculate the real circulation of tokens.

## Motivation

Tokens typically need to be time-locked against transfers to avoid malicious dumping when TGE. Besides, the token issuer was usually required to announce to the community / stakeholders the lock-up status of each allocated token, the time-based release rules and the real circulation of the token according to its token vesting schedules.

Time-locking of tokens can also be achieved via transferring tokens to an escrow smart contract, which requires additional trust on the escrow contract and additional approval process for token transfer, resulting in increased operations costs.

Therefore, creating general methods for allocating tokens and automatically time-locking tokens within a contract to extend the ERC20 can simplify implementations and improve efficiency.

Here are some use cases that can benefit from this standard:
- Simply publicize the allocation of tokens 
- Clearly publicize the token release schedule of each allocation 
- Conveniently query the overall lock-up balance to calculate the real circulation 
- Avoid rug pull and improve token transparency


## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

Interface

## IERC9999

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IERC9999 {

    /**
     * @dev The `ReleaseRule` struct is used to store the every release rule of the specific token allocation part.
     * @param `releaseTime` of the time of token release
     * @param `releasePercentage` of the percentage of token release
     */
    struct ReleaseRule {
        uint256 releaseTime;
        uint256 releasePercentage;
    }

    /**
     * @dev The `Allocation` struct is used to store the allocation detail of each token part.
     * @param `allocationTitle` of the title of the allocation part.
     * @param `allocationPercentage` of the percentage of the allocation part.
     * @param `nextClaimIndex` of current index of token claim.
     * @param `releaseRules` of the array of release rules in this allocation part.
     */
    struct Allocation {
        string allocationTitle;
        uint256 allocationPercentage;
        uint16 nextClaimIndex;
        ReleaseRule[] releaseRules;
    }
    /**
     * @dev This event emits when claiming token.
     * @param `to` of the address you want to claim.
     * @param `value` of the token amount you want to claim.
     */
    event Claim(address indexed to, uint256 indexed value);

    /**
     * @dev Claim all the tokens that can be claimed in an allocated part by `allocationTitle` to address `to`.
     * @param `allocationTitle` of title of allocation which you want to claim.
     * @param `to` of the address you want to claim.
     * This method will emit `Claim` event.
     */
    function claim(string memory allocationTitle, address to) external;

    /**
     * @dev Query the allocation detail by `allocationTitle`.
     * @param `allocationTitle` of the title of the allocation part.
     */
    function allocationByTitle(string memory allocationTitle) external view returns (Allocation memory);

    /**
     * @dev Query all the allocation titles.
     */
    function allAllocationTitles() external view returns (string[] memory);
}

```

## Rationale

<!--
  The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages.

  The current placeholder is acceptable for a draft.

  TODO: Remove this comment before submitting
-->

TBD

## Test Cases

<!--
  This section is optional for non-Core EIPs.

  The Test Cases section should include expected input/output pairs, but may include a succinct set of executable tests. It should not include project build files. No new requirements may be be introduced here (meaning an implementation following only the Specification section should pass all tests here.)
  If the test suite is too large to reasonably be included inline, then consider adding it as one or more files in `../assets/eip-####/`. External links will not be allowed

  TODO: Remove this comment before submitting
-->

## Backwards Compatibility

As mentioned in the specifications section, this standard can be fully EIP-721 compatible by adding an extension function set.

In addition, new functions introduced in this standard have many similarities with the existing functions in EIP-721. This allows developers to easily adopt the standard quickly.

## Reference Implementation

You can find an implementation of this standard in [`ERC5489.sol`](../assets/eip-5489/contracts/ERC5489.sol).

## Security Considerations

No security considerations were found.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).
