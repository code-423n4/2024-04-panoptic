# Panoptic audit details
- Total Prize Pool: $120,000 in USDC
  - HM awards: $104,500 in USDC
  - QA awards: $4180 in USDC
  - Judge awards: $9,000 USDC
  - Lookout awards: $6,000 USDC
  - Scout awards: $500 in USDC
 
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-04-panoptic/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts April 1, 2024 20:00 UTC
- Ends April 22, 2024 20:00 UTC

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-04-panoptic/blob/main/4naly3er-report.md).



_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._



- Transfers of ERC1155 SFPM tokens are very limited by design. It is expected that certain accounts will be unable to transfer or receive tokens. 
Some tokens may not be transferable at all.
- Construction helper functions (prefixed with add) in the TokenId library and other types do not perform extensive input validation. Passing invalid or nonsensical inputs into these functions or attempting to overwrite already filled slots may yield unexpected or invalid results. This is by design, so it is expected that users
of these functions will validate the inputs beforehand. 
- Very large quantities of tokens are not supported. It should be assumed that for any given pool, the cumulative amount of tokens that enter the system (associated with that pool, through adding liquidity, collection, etc.) will not exceed 2^127 - 1. Note that this is only a per-pool assumption, so if it is broken on one pool it should not affect the operation of any other pools, only the pool in question.
- If one token on a pool is broken/does not meet listed criteria/is malicious there are no guarantees as to the security of the other token in that pool, as long as other pools with two legitimate and compliant tokens are not affected.
- Price/oracle manipulation that is not atomic or requires attackers to hold a price across more than one block (i.e., to manipulate a Uniswap observation, you need to set the manipulated price at the end of one block, and then keep it there until the next block) is not in scope
- Attacks that stem from the TWAP being extremely stale compared to the market price within its period (currently 10 minutes) 
- As a general rule, only price manipulation issues that can be triggered by manipulating the price atomically from a normal pool/oracle state are valid
- The constants VEGOID, TWAP_WINDOW, SLOW_ORACLE_UNISWAP_MODE, MEDIAN_PERIOD, FAST_ORACLE_CARDINALITY, SLOW_ORACLE_CARDINALITY, SLOW_ORACLE_PERIOD, FAST_ORACLE_PERIOD, MAX_TWAP_DELTA_LIQUIDATION, MAX_SLOW_FAST_DELTA, MAX_SPREAD, MAX_POSITIONS, BP_DECREASE_BUFFER, and NO_BUFFER are all parameters and subject to change, but within reasonable levels. Cardinalities should be odd, and we're aware that the oracle/pool deployment will fail if the period is too large for the pool. It's important that the SLOW_ORACLE_UNISWAP_MODE flag works as intended, but otherwise not looking for anything other than a super glaring issue with the implementation of those parameters. Playing with the parameters to find an extreme example that technically won't work isn't going to be very useful to us.
 - Given a small enough pool and low seller diversity, premium manipulation by swapping back and forth in Uniswap is a known risk. As long as it's not possible to do it between two of your own accounts *profitably* and doesn't cause protocol loss, that's acceptable
 - Front-running via insufficient slippage specification is not in scope
 - It's known that liquidators sometimes have a limited capacity to force liquidations to execute at a less favorable price and extract some additional profit from that. This is acceptable even if it causes some amount of unnecessary protocol loss.
 - It's possible to leverage the rounding direction to artificially inflate the total gross premium and significantly decrease the rate of premium option sellers earn/are able to withdraw (but not the premium buyers pay) in the future (only significant for very-low-decimal pools, since this must be done one token at a time).
- Premium accumulation can become permanently capped if the accumulator exceeds the maximum value; this can happen if a low amount of liquidity earns a large amount of (token) fees
- The liquidator may not be able to execute a liquidation if MAX_POSITIONS is too high for the deployed chain due to an insufficient gas limit. This parameter is not final and will be adjusted by deployed chain such that the most expensive liquidation is well within a safe margin of the gas limit.
- It's expected that liquidators may have to sell options, perform force exercises, and deposit collateral to perform some liquidations. In some situations, the liquidation may not be profitable.
- In some situations (stale TWAP tick), force exercised users will be worse off than if they had burnt their position.
 - For the purposes of this competition, assume the constructor arguments to the CollateralTracker are: `10, 2_000, 1_000, -128, 5_000, 9_000, 20_000`
- Depending on the token, the amount of funds required for the initial factory deployment may be high or unrealistic



# Overview

[ ⭐️ SPONSORS: add info here ]

## Links

- **Previous audits:**  None public
- **Documentation:** https://docs.panoptic.xyz/ 
- **Website:** https://panoptic.xyz/
- **Twitter:** https://twitter.com/Panoptic_xyz
- **Discord:** https://discord.com/invite/9aGgM4B4MZ

---

# Scope


### Files in scope

|                      File                      | Logic Contracts | Interfaces | SLOC |
|:----------------------------------------------:|:---------------:|:----------:|:----:|
| [contracts/CollateralTracker.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/CollateralTracker.sol)               | 1               | ****       | 792  |
| [contracts/PanopticFactory.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/PanopticFactory.sol)                 | 1               | ****       | 249  |
| [contracts/PanopticPool.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/PanopticPool.sol)                    | 1               | ****       | 1162 |
| [contracts/SemiFungiblePositionManager.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/SemiFungiblePositionManager.sol)     | 1               | ****       | 724  |
| [contracts/libraries/CallbackLib.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/CallbackLib.sol)           | 1               | ****       | 22   |
| [contracts/libraries/Constants.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/Constants.sol)             | 1               | ****       | 9    |
| [contracts/libraries/Errors.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/Errors.sol)                | 1               | ****       | 35   |
| [contracts/libraries/FeesCalc.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/FeesCalc.sol)              | 1               | ****       | 86   |
| [contracts/libraries/InteractionHelper.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/InteractionHelper.sol)     | 1               | ****       | 72   |
| [contracts/libraries/Math.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/Math.sol)                  | 1               | ****       | 417  |
| [contracts/libraries/PanopticMath.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/PanopticMath.sol)          | 1               | ****       | 573  |
| [contracts/libraries/SafeTransferLib.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/libraries/SafeTransferLib.sol)       | 1               | ****       | 33   |
| [contracts/multicall/Multicall.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/multicall/Multicall.sol)             | 1               | ****       | 18   |
| [contracts/tokens/ERC1155Minimal.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/tokens/ERC1155Minimal.sol)           | 1               | ****       | 115  |
| [contracts/tokens/ERC20Minimal.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/tokens/ERC20Minimal.sol)             | 1               | ****       | 52   |
| [contracts/tokens/interfaces/IERC20Partial.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/tokens/interfaces/IERC20Partial.sol) | ****            | 1          | 6    |
| [contracts/types/LeftRight.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/types/LeftRight.sol)                 | 1               | ****       | 156  |
| [contracts/types/LiquidityChunk.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/types/LiquidityChunk.sol)            | 1               | ****       | 91   |
| [contracts/types/TokenId.sol](https://github.com/code-423n4/2024-04-panoptic/tree/main/contracts/types/TokenId.sol)                   | 1               | ****       | 305  |
| Totals                                         | 18              | 1          | 4917 |


### Files out of scope

|                         Contract                         |
|:--------------------------------------------------------:|
| deploy/DeployProtocol.s.sol                              |
| periphery/PanopticHelper.sol                             |
| scripts/DeployTestPool.s.sol                             |
| scripts/tokens/ERC20S.sol                                |
| test/foundry/core/CollateralTracker.t.sol                |
| test/foundry/core/Misc.t.sol                             |
| test/foundry/core/PanopticFactory.t.sol                  |
| test/foundry/core/PanopticPool.t.sol                     |
| test/foundry/core/SemiFungiblePositionManager.t.sol      |
| test/foundry/libraries/CallbackLib.t.sol                 |
| test/foundry/libraries/FeesCalc.t.sol                    |
| test/foundry/libraries/Math.t.sol                        |
| test/foundry/libraries/PanopticMath.t.sol                |
| test/foundry/libraries/PositionAmountsTest.sol           |
| test/foundry/libraries/SafeTransferLib.t.sol             |
| test/foundry/libraries/harnesses/CallbackLibHarness.sol  |
| test/foundry/libraries/harnesses/FeesCalcHarness.sol     |
| test/foundry/libraries/harnesses/MathHarness.sol         |
| test/foundry/libraries/harnesses/PanopticMathHarness.sol |
| test/foundry/periphery/PanopticHelper.t.sol              |
| test/foundry/testUtils/PositionUtils.sol                 |
| test/foundry/testUtils/PriceMocks.sol                    |
| test/foundry/testUtils/ReentrancyMocks.sol               |
| test/foundry/tokens/ERC1155Minimal.t.sol                 |
| test/foundry/types/LeftRight.t.sol                       |
| test/foundry/types/LiquidityChunk.t.sol                  |
| test/foundry/types/TokenId.t.sol                         |
| test/foundry/types/harnesses/LeftRightHarness.sol        |
| test/foundry/types/harnesses/LiquidityChunkHarness.sol   |
| test/foundry/types/harnesses/TokenIdHarness.sol          |

## Scoping Q &amp; A

### General questions


| Question                                | Answer                       |
| --------------------------------------- | ---------------------------- |
| Test coverage                           | -                          |
| ERC20 used by the protocol              |       any             |
| ERC721 used  by the protocol            |            any              |
| ERC777 used by the protocol             |           no                |
| ERC1155 used by the protocol            |              SFPM and factory ERC1155 tokens            |
| Chains the protocol will be deployed on | Ethereum, Arbitrum, Avax, Base, BSC, Optimism ,Polygon |

### ERC20 token behaviors in scope

| Question                                                                                                                                                   | Answer |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| [Missing return values](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#missing-return-values)                                                      | ✅ Yes  |
| [Fee on transfer](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#fee-on-transfer)                                                                  | ❌ No |
| [Balance changes outside of transfers](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#balance-modifications-outside-of-transfers-rebasingairdrops) | ❌ No   |
| [Upgradeability](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#upgradable-tokens)                                                                 | ❌ No |
| [Flash minting](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#flash-mintable-tokens)                                                              | ✅ Yes   |
| [Pausability](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#pausable-tokens)                                                                      | ❌ No   |
| [Approval race protections](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#approval-race-protections)                                              | ✅ Yes   |
| [Revert on approval to zero address](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-approval-to-zero-address)                            | ✅ Yes   |
| [Revert on zero value approvals](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-zero-value-approvals)                                    | ✅ Yes   |
| [Revert on zero value transfers](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-zero-value-transfers)                                    | ✅ Yes   |
| [Revert on transfer to the zero address](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-transfer-to-the-zero-address)                    | ✅ Yes   |
| [Revert on large approvals and/or transfers](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-large-approvals--transfers)                  | ❌ No   |
| [Doesn't revert on failure](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#no-revert-on-failure)                                                   | ❌ No  |
| [Multiple token addresses](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-zero-value-transfers)                                          | ❌ No   |
| [Low decimals ( < 6)](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#low-decimals)                                                                 | ✅ Yes |
| [High decimals ( > 18)](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#high-decimals)                                                              | ✅ Yes   |
| [Blocklists](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#tokens-with-blocklists)                                                                | ❌ No  |

### External integrations (e.g., Uniswap) behavior in scope:


| Question                                                  | Answer |
| --------------------------------------------------------- | ------ |
| Enabling/disabling fees (e.g. Blur disables/enables fees) | No   |
| Pausability (e.g. Uniswap pool gets paused)               |  No   |
| Upgradeability (e.g. Uniswap gets upgraded)               |   No  |


### EIP compliance checklist
N/A


# Additional context

## Main invariants

SFPM:
- Users of the SFPM can only remove liquidity (via isLong==1 or burning positions) that they have previously added under the same (tickLower, tickUpper, tokenType) key. They cannot remove liquidity owned by other users.
- Fees collected from Uniswap during any given operation should not exceed the amount of fees earned by the liquidity owned by the user performing the operation.
- Fees paid to a given user should not exceed the amount of fees earned by the liquidity owned by that user.
Panoptic:
- Commission paid by an account should be greater than or equal to 1 (of either) token
- Option sellers must pay back the exact amounts of tokens that were borrowed (the "shortAmounts") when their positions are burned
- Option buyers must add back the same amount of liquidity that was borrowed when their positions are burned
- Users should not be allowed to mint/burn options or pay premium if their end state is insolvent (at the fast oracle tick, or both ticks if the fast and slow oracle ticks are further away than the threshold)
- Users should not be allowed to withdraw collateral if they have open positions
- Users should not be allowed to mint more positions than the limit
- Option sellers should not receive premium that has not settled (by collection from Uniswap or payments from long option holders) yet
- Option sellers in a given chunk should not receive premium, as a fraction of the premium owed to them , that is greater than the ratio of total owed premium to total settled premium in that chunk
- Liquidations must only occur if the liquidated account is insolvent at the TWAP tick and  the TWAP tick is not above the threshold for distance to the current tick
- Note that some executable liquidations may not be profitable due to the required actions to execute the liquidation (depositing collateral, force exercises) or the price the token conversions occur at
- Note that it is acceptable for an account to cause protocol loss (shares minted to liquidator) during a liquidation even if they have a residual token balance in their account, this may happen if the value of tokens remaining correspond to less than 1 of the other token
- If, at the final state after a liquidation, any premium paid to sellers DURING the liquidation has not been revoked, there must not be any protocol loss (shares minted to liquidator). In other words, this means that premium cannot be paid over the protocol loss threshold.
- The value of the bonus paid to the liquidator must not exceed the liquidated user's collateral balance at the beginning of the call


## Attack ideas (where to focus for bugs)
The factory contract and usage of libraries by external integrators is relatively unimportant -- wardens should focus their efforts on the security of the SFPM (previously audited by C4, code has changed since that audit however), PanopticPool, and CollateralTracker


## All trusted roles in the protocol

N/A
There is a factory owner but they do not have any permissions over the contracts in scope.


## Running tests

⚠️**Note**: You will need to provide your own Ethereum Mainnet eth_rpc_url (Works best with a local archives node) in the Foundry.toml.

```bash
git clone --recurse-submodules https://github.com/code-423n4/2023-04-panoptic.git
cd 2023-04-panoptic
export FOUNDRY_PROFILE=ci_test  # (tests WILL fail without this because of a Foundry bug)
forge build
forge test
```

To run gas benchmarks:
```bash
forge test --gas-report
```

#### Gas snapshot
See [gas-snapshot.txt](https://github.com/code-423n4/2024-04-panoptic/tree/main/assets/gas-snapshot.txt)


## Miscellaneous
Employees of Panoptic and employees' family members are ineligible to participate in this audit.
