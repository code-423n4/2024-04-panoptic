# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Don't use `_msgSender()` if not supporting EIP-2771 | 9 |
| [GAS-2](#GAS-2) | `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings) | 41 |
| [GAS-3](#GAS-3) | Using bools for storage incurs overhead | 12 |
| [GAS-4](#GAS-4) | Cache array length outside of loop | 8 |
| [GAS-5](#GAS-5) | For Operations that will not overflow, you could use unchecked | 606 |
| [GAS-6](#GAS-6) | Avoid contract existence checks by using low level calls | 3 |
| [GAS-7](#GAS-7) | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 |
| [GAS-8](#GAS-8) | `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`) | 24 |
| [GAS-9](#GAS-9) | Use shift right/left instead of division/multiplication if possible | 29 |
| [GAS-10](#GAS-10) | Increments/decrements can be unchecked in for-loops | 16 |
| [GAS-11](#GAS-11) | Use != 0 instead of > 0 for unsigned integer comparison | 29 |
| [GAS-12](#GAS-12) | WETH address definition can be use directly | 2 |
### <a name="GAS-1"></a>[GAS-1] Don't use `_msgSender()` if not supporting EIP-2771
Use `msg.sender` if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support

*Instances (9)*:
```solidity
File: contracts/PanopticFactory.sol

133:         s_factoryOwner = _msgSender();

147:         emit OwnerChanged(address(0), _msgSender());

156:         if (_msgSender() != s_factoryOwner) revert Errors.NotOwner();

202:                 _msgSender(),

209:                 _msgSender(),

252:             POOL_REFERENCE.cloneDeterministic(_getSalt(address(v3Pool), _msgSender(), salt))

380:         if (deployer == address(0)) deployer = _msgSender();

444:                     payer: _msgSender()

507:         _mint(_msgSender(), tokenId, 1, "");

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

### <a name="GAS-2"></a>[GAS-2] `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings)
This saves **16 gas per instance.**

*Instances (41)*:
```solidity
File: contracts/CollateralTracker.sol

436:             s_poolAssets += uint128(assets);

496:             s_poolAssets += uint128(assets);

895:         balanceOf[delegatee] += convertToShares(assets);

1064:                 tokenToPay += intrinsicValue;

1119:             exchangedAmount += int256(

1175:                     tokenRequired += uint128(-premiumAllPositions);

1184:                 netBalance += uint256(uint128(premiumAllPositions));

1228:                 tokenRequired += _tokenRequired;

1259:                 tokenRequired += _getRequiredCollateralSingleLeg(

1401:                         required += Math.mulDiv96RoundingUp(amountMoved, c2);

1417:                         required += c3;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

1134:                 liquidationBonus0 += deltaBonus0;

1135:                 liquidationBonus1 += deltaBonus1;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

228:               s_accountPremiumOwed += feeGrowthX128 * R * (1 + ν*R/N) / R

229:                                    += feeGrowthX128 * (T - R + ν*R)/N

230:                                    += feeGrowthX128 * T/N * (1 - R/T + ν*R/T)

244:              s_accountPremiumOwed += feesCollected * T/N^2 * (1 - R/T + ν*R/T)          (Eqn 3)     

264:             s_accountPremiumGross += feesCollected * T/N^2 * (1 - R/T + ν*R^2/T^2)       (Eqn 4) 

922:                     amount0 += Math.getAmount0ForLiquidity(liquidityChunk);

924:                     amount1 += Math.getAmount1ForLiquidity(liquidityChunk);

1032:                         removedLiquidity += chunkLiquidity;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/FeesCalc.sol

69:                         value0 += int256(amount0);

70:                         value1 += int256(amount1);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/FeesCalc.sol)

```solidity
File: contracts/libraries/Math.sol

95:                 r += 32;

99:                 r += 16;

103:                 r += 8;

107:                 r += 4;

111:                 r += 2;

114:                 r += 1;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

51:             poolId += uint64(uint24(tickSpacing)) << 48;

219:                         shift += 1;

715:                     bonus1 += Math.min(

733:                     bonus0 += Math.min(

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

107:             balanceOf[to][id] += amount;

151:                 balanceOf[to][id] += amount;

217:             balanceOf[to][id] += amount;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/tokens/ERC20Minimal.sol

67:             balanceOf[to] += amount;

91:             balanceOf[to] += amount;

109:             balanceOf[to] += amount;

126:             balanceOf[to] += amount;

128:         totalSupply += amount;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC20Minimal.sol)

### <a name="GAS-3"></a>[GAS-3] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (12)*:
```solidity
File: contracts/CollateralTracker.sol

93:     bool internal s_initialized;

102:     bool internal s_underlyingIsToken0;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

109:     bool internal constant COMPUTE_ALL_PREMIA = true;

111:     bool internal constant COMPUTE_LONG_PREMIA = false;

114:     bool internal constant ONLY_AVAILABLE_PREMIUM = false;

119:     bool internal constant COMMIT_LONG_SETTLED = true;

120:     bool internal constant DONOT_COMMIT_LONG_SETTLED = false;

123:     bool internal constant ADD = true;

133:     bool internal constant SLOW_ORACLE_UNISWAP_MODE = false;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

125:     bool internal constant MINT = false;

126:     bool internal constant BURN = true;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

71:     mapping(address owner => mapping(address operator => bool approvedForAll))

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

### <a name="GAS-4"></a>[GAS-4] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (8)*:
```solidity
File: contracts/PanopticPool.sol

802:         for (uint256 i = 0; i < positionIdList.length; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

575:         for (uint256 i = 0; i < ids.length; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/FeesCalc.sol

51:         for (uint256 k = 0; k < positionIdList.length; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/FeesCalc.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

781:             for (uint256 i = 0; i < positionIdList.length; ++i) {

860:             for (uint256 i = 0; i < positionIdList.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/multicall/Multicall.sol

14:         for (uint256 i = 0; i < data.length; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/multicall/Multicall.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

143:         for (uint256 i = 0; i < ids.length; ) {

187:             for (uint256 i = 0; i < owners.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

### <a name="GAS-5"></a>[GAS-5] For Operations that will not overflow, you could use unchecked

*Instances (606)*:
```solidity
File: contracts/CollateralTracker.sol

73:     string internal constant NAME_PREFIX = "POPT-V1";

200:             int256 ratioTick = (int256(_sellerCollateralRatio) - 2000);

202:                 2230 +

203:                     (12500 * ratioTick) /

204:                     10_000 +

205:                     (7812 * ratioTick ** 2) /

206:                     10_000 ** 2 +

207:                     (6510 * ratioTick ** 3) /

208:                     10_000 ** 3

234:         totalSupply = 10 ** 6;

249:             _poolFee = fee / 100;

262:             s_ITMSpreadFee = uint128((ITM_SPREAD_MULTIPLIER * _poolFee) / DECIMALS);

372:             return s_poolAssets + s_inAMM;

403:                 assets * (DECIMALS - COMMISSION_FEE),

405:                 totalAssets() * DECIMALS

436:             s_poolAssets += uint128(assets);

446:             return (convertToShares(type(uint104).max) * DECIMALS) / (DECIMALS + COMMISSION_FEE);

463:                 shares * DECIMALS,

465:                 totalSupply * (DECIMALS - COMMISSION_FEE)

496:             s_poolAssets += uint128(assets);

519:         uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.

542:             uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.

544:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

552:             s_poolAssets -= uint128(assets);

600:             uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.

602:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

612:             s_poolAssets -= uint128(assets);

659:         uint256 maxNumRangesFromStrike = 1; // technically "maxNum(Half)RangesFromStrike" but the name is long

662:             for (uint256 leg = 0; leg < positionId.countLegs(); ++leg) {

670:                                 uint24(positionId.width(leg) * positionId.tickSpacing()),

677:                         uint256(Math.abs(currentTick - positionId.strike(leg)) / range)

715:                                 int128(uint128(oracleValue0)) - int128(uint128(currentValue0))

718:                                 int128(uint128(oracleValue1)) - int128(uint128(currentValue1))

727:             int256 fee = (FORCE_EXERCISE_COST >> (maxNumRangesFromStrike - 1)); // exponential decay of fee based on number of half ranges away from the price

731:                 .toRightSlot(int128((longAmounts.rightSlot() * fee) / DECIMALS_128))

732:                 .toLeftSlot(int128((longAmounts.leftSlot() * fee) / DECIMALS_128));

743:             return int256((s_inAMM * DECIMALS) / totalAssets());

764:                    100% - |                _------

765:                           |             _-¯

766:                           |          _-¯

767:                     20% - |---------¯

769:                           +---------+-------+-+--->   POOL_

778:                 min_sell_ratio /= 2;

779:                 utilization = -utilization;

796:                 min_sell_ratio +

797:                 ((DECIMALS - min_sell_ratio) * (uint256(utilization) - TARGET_POOL_UTIL)) /

798:                 (SATURATED_POOL_UTIL - TARGET_POOL_UTIL);

827:            10% - |----------__       min_ratio = 5%

828:            5%  - | . . . . .  ¯¯¯--______

830:                  +---------+-------+-+--->   POOL_

843:                 return BUYER_COLLATERAL_RATIO / 2;

849:                 (BUYER_COLLATERAL_RATIO +

850:                     (BUYER_COLLATERAL_RATIO * (SATURATED_POOL_UTIL - utilization)) /

851:                     (SATURATED_POOL_UTIL - TARGET_POOL_UTIL)) / 2; // do the division by 2 at the end after all addition and multiplication; b/c y1 = buyCollateralRatio / 2

856:           LIFECYCLE OF A COLLATERAL TOKEN AND DELEGATE/REVOKE LOGIC

895:         balanceOf[delegatee] += convertToShares(assets);

904:         balanceOf[delegatee] -= convertToShares(assets);

958:                     totalSupply - delegateeBalance,

959:                     uint256(Math.max(1, int256(totalAssets()) - int256(assets)))

960:                 ) - delegateeBalance

980:                 _transferFrom(refundee, refunder, convertToShares(uint256(-assets)));

1003:             int256 updatedAssets = int256(uint256(s_poolAssets)) - swappedAmount;

1020:                 uint256 sharesToMint = convertToShares(uint256(-tokenToPay));

1029:             s_inAMM = uint128(uint256(int256(uint256(s_inAMM)) + (shortAmount - longAmount)));

1052:             int256 updatedAssets = int256(uint256(s_poolAssets)) - swappedAmount;

1055:             int256 tokenToPay = -realizedPremium;

1058:             int256 intrinsicValue = swappedAmount - (longAmount - shortAmount);

1064:                 tokenToPay += intrinsicValue;

1077:                 uint256 sharesToMint = convertToShares(uint256(-tokenToPay));

1084:             s_poolAssets = uint128(uint256(updatedAssets + realizedPremium));

1085:             s_inAMM = uint128(uint256(int256(uint256(s_inAMM)) - (shortAmount - longAmount)));

1105:             int256 intrinsicValue = swappedAmount - (shortAmount - longAmount);

1110:                     s_ITMSpreadFee * uint256(Math.abs(intrinsicValue)),

1115:                 exchangedAmount = intrinsicValue + int256(swapCommission);

1119:             exchangedAmount += int256(

1121:                     uint256(uint128(shortAmount + longAmount)) * COMMISSION_FEE,

1175:                     tokenRequired += uint128(-premiumAllPositions);

1184:                 netBalance += uint256(uint128(premiumAllPositions));

1228:                 tokenRequired += _tokenRequired;

1231:                 ++i;

1255:             for (uint256 index = 0; index < numLegs; ++index) {

1259:                 tokenRequired += _getRequiredCollateralSingleLeg(

1286:             tokenId.riskPartner(index) == index // does this leg have a risk partner? Affects required collateral

1346:                     ((atTick >= tickUpper) && (tokenType == 1)) || // strike OTM when price >= upperTick for tokenType=1

1347:                     ((atTick < tickLower) && (tokenType == 0)) // strike OTM when price < lowerTick for tokenType=0

1362:                     uint160 ratio = tokenType == 1 // tokenType

1364:                             Math.max24(2 * (atTick - strike), Constants.MIN_V3POOL_TICK)

1365:                         ) // puts ->  price/strike

1367:                             Math.max24(2 * (strike - atTick), Constants.MIN_V3POOL_TICK)

1368:                         ); // calls -> strike/price

1374:                         ((atTick < tickLower) && (tokenType == 1)) || // strike ITM but out of range price < lowerTick for tokenType=1

1375:                         ((atTick >= tickUpper) && (tokenType == 0)) // strike ITM but out of range when price >= upperTick for tokenType=0

1378:                                     Short put BPR = 100% - (price/strike) + SCR

1385:                                          |        <- ITM . <-ATM-> . OTM ->

1386:                            100% + SCR% - |--__           .    .    .

1387:                                   100% - | . .¯¯--__     .    .    .

1388:                                          |    .     ¯¯--__    .    .

1389:                                    SCR - |    .          .¯¯--__________

1391:                                          +----+----------+----+----+--->   current

1392:                                          0   Liqui-     Pa  strike Pb       price

1394:                                              price = SCR*strike                                         

1397:                         uint256 c2 = Constants.FP96 - ratio;

1401:                         required += Math.mulDiv96RoundingUp(amountMoved, c2);

1409:                             (tickUpper - strike) + (strike - tickLower)

1413:                             scaleFactor - ratio,

1414:                             scaleFactor + Constants.FP96

1417:                         required += c3;

1486:                 required = Math.unsafeDivRoundingUp(amount * sellCollateral, DECIMALS);

1496:                 required = Math.unsafeDivRoundingUp(amount * buyCollateral, DECIMALS);

1546:                         ? movedPartnerRight - movedRight

1547:                         : movedRight - movedPartnerRight;

1550:                         ? movedPartnerLeft - movedLeft

1551:                         : movedLeft - movedPartnerLeft;

1571:                     ? Math.unsafeDivRoundingUp((notionalP - notional) * contracts, notional)

1572:                     : Math.unsafeDivRoundingUp((notional - notionalP) * contracts, notionalP);

1614:                     Put side of a short strangle, BPR = 100% - (100% - SCR/2)*(price/strike)

1619:                          |           <- ITM   .  OTM ->

1620:                   100% - |--__                .

1621:                          |    ¯¯--__          .

1622:                          |          ¯¯--__    .

1623:                  SCR/2 - |                ¯¯--______ <------ base collateral is half that of a single-leg

1624:                          +--------------------+--->   current

1637:                 uint128(uint64(-int64(poolUtilization0 == 0 ? 1 : poolUtilization0))) +

1638:                 (uint128(uint64(-int64(poolUtilization1 == 0 ? 1 : poolUtilization1))) << 64);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

239:         if (address(v3Pool) == address(0)) revert Errors.UniswapPoolNotInitialized(); // the uniswap pool needs to exist

270:          FULL-RANGE LIQUIDITY DEPLOYMENT, INITIAL COLLATERAL DEPOSITS, AND NFT LOGIC

300:         6  : super-bull

303:         9  : super-bear

336:         20+: BASIQUE

391:         uint256 maxLoops = salt + loops;

412:                 ++nonce;

438:             CallbackLib.CallbackData({ // compute by reading values from univ3pool every time

489:                 (Constants.MIN_V3POOL_TICK / tickSpacing) * tickSpacing,

490:                 (Constants.MAX_V3POOL_TICK / tickSpacing) * tickSpacing,

503:         tokenId = s_nextId++;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

103:     int24 internal constant MIN_SWAP_TICK = Constants.MIN_V3POOL_TICK + 1;

105:     int24 internal constant MAX_SWAP_TICK = Constants.MAX_V3POOL_TICK - 1;

165:     uint64 internal constant MAX_SPREAD = 9 * (2 ** 32);

309:                 (uint256(block.timestamp) << 216) +

312:                 (uint256(0xF590A6F276170D89E9F276170D89E9F276170D89E9000000000000)) +

313:                 (uint256(uint24(currentTick)) << 24) + // add to slot 4

314:                 (uint256(uint24(currentTick))); // add to slot 3

483:                     ++leg;

488:                 ++k;

537:                           MINT/BURN INTERFACE

624:             tokenId = positionIdList[positionIdList.length - 1];

730:             return uint128(uint256(utilization0) + uint128(uint256(utilization1) << 64));

779:                 ++leg;

813:                 ++i;

872:                 ++leg;

943:         if (Math.abs(int256(fastOracleTick) - slowOracleTick) > MAX_SLOW_FAST_DELTA)

1035:             if (Math.abs(currentTick - twapTick) > MAX_TWAP_DELTA_LIQUIDATION)

1134:                 liquidationBonus0 += deltaBonus0;

1135:                 liquidationBonus1 += deltaBonus1;

1144:             uint256(int256(uint256(_delegations.rightSlot())) + liquidationBonus0)

1149:             uint256(int256(uint256(_delegations.leftSlot())) + liquidationBonus1)

1255:                 refundAmounts.rightSlot() - delegatedAmounts.rightSlot()

1260:                 refundAmounts.leftSlot() - delegatedAmounts.leftSlot()

1329:             return balanceCross >= Math.unsafeDivRoundingUp(thresholdCross * buffer, 10_000);

1348:                 Math.mulDiv(uint256(tokenData1.rightSlot()), 2 ** 96, sqrtPriceX96) +

1353:                 Math.mulDivRoundingUp(uint256(tokenData1.leftSlot()), 2 ** 96, sqrtPriceX96) +

1376:             pLength = positionIdList.length - offset;

1389:                 ++i;

1487:             effectiveLiquidityFactorX32 = (uint256(totalLiquidity) * 2 ** 32) / netLiquidity;

1550:                                     ((premiumAccumulatorsByLeg[leg][0] -

1551:                                         premiumAccumulatorLast.rightSlot()) *

1552:                                         (liquidityChunk.liquidity())) / 2 ** 64

1559:                                     ((premiumAccumulatorsByLeg[leg][1] -

1560:                                         premiumAccumulatorLast.leftSlot()) *

1561:                                         (liquidityChunk.liquidity())) / 2 ** 64

1572:                 ++leg;

1594:         TokenId tokenId = positionIdList[positionIdList.length - 1];

1635:                 .toRightSlot(int128(int256((accumulatedPremium.rightSlot() * liquidity) / 2 ** 64)))

1636:                 .toLeftSlot(int128(int256((accumulatedPremium.leftSlot() * liquidity) / 2 ** 64)));

1672:         for (uint256 leg = 0; leg < numLegs; ++leg) {

1723:                     uint256 totalLiquidityBefore = totalLiquidity - positionLiquidity;

1729:                                 (grossCurrent[0] *

1730:                                     positionLiquidity +

1731:                                     grossPremiumLast.rightSlot() *

1732:                                     totalLiquidityBefore) / (totalLiquidity)

1737:                                 (grossCurrent[1] *

1738:                                     positionLiquidity +

1739:                                     grossPremiumLast.leftSlot() *

1740:                                     totalLiquidityBefore) / (totalLiquidity)

1768:             uint256 accumulated0 = ((premiumAccumulators[0] - grossPremiumLast.rightSlot()) *

1769:                 totalLiquidity) / 2 ** 64;

1770:             uint256 accumulated1 = ((premiumAccumulators[1] - grossPremiumLast.leftSlot()) *

1771:                 totalLiquidity) / 2 ** 64;

1779:                                 (uint256(premiumOwed.rightSlot()) * settledTokens.rightSlot()) /

1788:                                 (uint256(premiumOwed.leftSlot()) * settledTokens.leftSlot()) /

1820:             totalLiquidity = accountLiquidities.rightSlot() + accountLiquidities.leftSlot();

1884:                     uint256 totalLiquidityBefore = totalLiquidity + positionLiquidity;

1889:                         totalLiquidity + positionLiquidity,

1936:                                                     grossPremiumLast.rightSlot() *

1938:                                                 ) -

1940:                                                         _premiumAccumulatorsByLeg[_leg][0] *

1942:                                                     )) + int256(legPremia.rightSlot() * 2 ** 64),

1945:                                         ) / totalLiquidity

1953:                                                     grossPremiumLast.leftSlot() *

1955:                                                 ) -

1957:                                                         _premiumAccumulatorsByLeg[_leg][1] *

1959:                                                     )) + int256(legPremia.leftSlot()) * 2 ** 64,

1962:                                         ) / totalLiquidity

1977:                 ++leg;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

159:           │  ┌────┐-T      due isLong=1   in the UniswapV3Pool 

161:           │  │    │                        ┌────┐-(T-R)  

162:           │  │    │         ┌────┐-R       │    │          

165:              total=T       removed=R      net=(T-R)

183:         keep track of the amount of fees that *would have been collected*, we call this the owed

191:         same tick using a tokenId with a isLong=1 parameter. Because the netLiquidity is only (T-R),

194:               net_feesCollectedX128 = feeGrowthX128 * (T - R)

195:                                     = feeGrowthX128 * N                                     

197:         where N = netLiquidity = T-R. Had that liquidity never been removed, we want the gross

200:               gross_feesCollectedX128 = feeGrowthX128 * T

208:               gross_feesCollectedX128 = net_feesCollectedX128 + owed_feesCollectedX128

212:               owed_feesCollectedX128 = feeGrowthX128 * R * (1 + spread)                      (Eqn 1)

216:               spread = ν*(liquidity removed from that strike)/(netLiquidity remaining at that strike)

217:                      = ν*R/N

219:         For an arbitrary parameter 0 <= ν <= 1 (ν = 1/2^VEGOID). This way, the gross_feesCollectedX128 will be given by: 

221:               gross_feesCollectedX128 = feeGrowthX128 * N + feeGrowthX128*R*(1 + ν*R/N) 

222:                                       = feeGrowthX128 * T + feesGrowthX128*ν*R^2/N         

223:                                       = feeGrowthX128 * T * (1 + ν*R^2/(N*T))                (Eqn 2)

225:         The s_accountPremiumOwed accumulator tracks the feeGrowthX128 * R * (1 + spread) term

228:               s_accountPremiumOwed += feeGrowthX128 * R * (1 + ν*R/N) / R

229:                                    += feeGrowthX128 * (T - R + ν*R)/N

230:                                    += feeGrowthX128 * T/N * (1 - R/T + ν*R/T)

236:              feesCollected = feesGrowthX128 * (T-R)

240:              feesGrowthX128 = feesCollected/N

244:              s_accountPremiumOwed += feesCollected * T/N^2 * (1 - R/T + ν*R/T)          (Eqn 3)     

249:              owedPremia(t1, t2) = (s_accountPremiumOwed_t2-s_accountPremiumOwed_t1) * r

250:                                 = ∆feesGrowthX128 * r * T/N * (1 - R/T + ν*R/T)

251:                                 = ∆feesGrowthX128 * r * (T - R + ν*R)/N

252:                                 = ∆feesGrowthX128 * r * (N + ν*R)/N

253:                                 = ∆feesGrowthX128 * r * (1 + ν*R/N)             (same as Eqn 1)

260:         However, since we require that Eqn 2 holds up-- ie. the gross fees collected should be equal

264:             s_accountPremiumGross += feesCollected * T/N^2 * (1 - R/T + ν*R^2/T^2)       (Eqn 4) 

269:             grossPremia(t1, t2) = ∆(s_accountPremiumGross) * t

270:                                 = ∆feeGrowthX128 * t * T/N * (1 - R/T + ν*R^2/T^2) 

271:                                 = ∆feeGrowthX128 * t * (T - R + ν*R^2/T) / N 

272:                                 = ∆feeGrowthX128 * t * (N + ν*R^2/T) / N

273:                                 = ∆feeGrowthX128 * t * (1  + ν*R^2/(N*T))   (same as Eqn 2)

278:         long+short liquidity to guarantee that liquidity deposited always receives the correct

388:             s_AddrToPoolIdData[univ3pool] = uint256(poolId) + 2 ** 255;

460:                        PUBLIC MINT/BURN FUNCTIONS

579:                 ++i;

650:                 ++leg;

761:         bool zeroForOne; // The direction of the swap, true for token0 to token1, false for token1 to token0

762:         int256 swapAmount; // The amount of token0 or token1 to swap

817:                 int256 net0 = itm0 - PanopticMath.convert1to0(itm1, sqrtPriceX96);

822:                 swapAmount = -net0;

825:                 swapAmount = -itm0;

828:                 swapAmount = -itm1;

842:                     ? Constants.MIN_V3POOL_SQRT_RATIO + 1

843:                     : Constants.MAX_V3POOL_SQRT_RATIO - 1,

899:                     _leg = _isBurn ? numLegs - leg - 1 : leg;

922:                     amount0 += Math.getAmount0ForLiquidity(liquidityChunk);

924:                     amount1 += Math.getAmount1ForLiquidity(liquidityChunk);

932:                 ++leg;

988:         LeftRightUnsigned currentLiquidity = s_accountLiquidity[positionKey]; //cache

1002:                 updatedLiquidity = startingLiquidity + chunkLiquidity;

1007:                     removedLiquidity -= chunkLiquidity;

1023:                         updatedLiquidity = startingLiquidity - chunkLiquidity;

1032:                         removedLiquidity += chunkLiquidity;

1068:                 : _burnLiquidity(liquidityChunk, univ3pool); // from msg.sender to Uniswap

1191:             CallbackLib.CallbackData({ // compute by reading values from univ3pool every time

1241:             movedAmounts = LeftRightSigned.wrap(0).toRightSlot(-int128(int256(amount0))).toLeftSlot(

1242:                 -int128(int256(amount1))

1297:                     ? receivedAmount0 - uint128(-movedInLeg.rightSlot())

1300:                     ? receivedAmount1 - uint128(-movedInLeg.leftSlot())

1340:             uint256 totalLiquidity = netLiquidity + removedLiquidity;

1352:                     totalLiquidity * 2 ** 64,

1353:                     netLiquidity ** 2

1357:                     totalLiquidity * 2 ** 64,

1358:                     netLiquidity ** 2

1367:                     uint256 numerator = netLiquidity + (removedLiquidity / 2 ** VEGOID);

1388:                     uint256 numerator = totalLiquidity ** 2 -

1389:                         totalLiquidity *

1390:                         removedLiquidity +

1391:                         ((removedLiquidity ** 2) / 2 ** (VEGOID));

1394:                         .mulDiv(premium0X64_base, numerator, totalLiquidity ** 2)

1397:                         .mulDiv(premium1X64_base, numerator, totalLiquidity ** 2)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Constants.sol

12:     int24 internal constant MIN_V3POOL_TICK = -887272;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Constants.sol)

```solidity
File: contracts/libraries/FeesCalc.sol

69:                         value0 += int256(amount0);

70:                         value1 += int256(amount1);

74:                         value0 -= int256(amount0);

75:                         value1 -= int256(amount1);

80:                     ++leg;

84:                 ++k;

166:                 feeGrowthInside0X128 = lowerOut0 - upperOut0; // fee growth inside the chunk

167:                 feeGrowthInside1X128 = lowerOut1 - upperOut1;

183:                 feeGrowthInside0X128 = upperOut0 - lowerOut0;

184:                 feeGrowthInside1X128 = upperOut1 - lowerOut1;

204:                 feeGrowthInside0X128 = univ3pool.feeGrowthGlobal0X128() - lowerOut0 - upperOut0;

205:                 feeGrowthInside1X128 = univ3pool.feeGrowthGlobal1X128() - lowerOut1 - upperOut1;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/FeesCalc.sol)

```solidity
File: contracts/libraries/InteractionHelper.sol

78:                     "/",

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/InteractionHelper.sol)

```solidity
File: contracts/libraries/Math.sol

15:     uint256 internal constant MAX_UINT256 = 2 ** 256 - 1;

74:         return x > 0 ? x : -x;

83:             return x > 0 ? uint256(x) : uint256(-x);

95:                 r += 32;

99:                 r += 16;

103:                 r += 8;

107:                 r += 4;

111:                 r += 2;

114:                 r += 1;

130:             uint256 absTick = tick < 0 ? uint256(-int256(tick)) : uint256(int256(tick));

137:             if (absTick & 0x2 != 0) sqrtR = (sqrtR * 0xfff97272373d413259a46990580e213a) >> 128;

139:             if (absTick & 0x4 != 0) sqrtR = (sqrtR * 0xfff2e50f5f656932ef12357cf3c7fdcc) >> 128;

141:             if (absTick & 0x8 != 0) sqrtR = (sqrtR * 0xffe5caca7e10e4e61c3624eaa0941cd0) >> 128;

143:             if (absTick & 0x10 != 0) sqrtR = (sqrtR * 0xffcb9843d60f6159c9db58835c926644) >> 128;

145:             if (absTick & 0x20 != 0) sqrtR = (sqrtR * 0xff973b41fa98c081472e6896dfb254c0) >> 128;

147:             if (absTick & 0x40 != 0) sqrtR = (sqrtR * 0xff2ea16466c96a3843ec78b326b52861) >> 128;

149:             if (absTick & 0x80 != 0) sqrtR = (sqrtR * 0xfe5dee046a99a2a811c461f1969c3053) >> 128;

151:             if (absTick & 0x100 != 0) sqrtR = (sqrtR * 0xfcbe86c7900a88aedcffc83b479aa3a4) >> 128;

153:             if (absTick & 0x200 != 0) sqrtR = (sqrtR * 0xf987a7253ac413176f2b074cf7815e54) >> 128;

155:             if (absTick & 0x400 != 0) sqrtR = (sqrtR * 0xf3392b0822b70005940c7a398e4b70f3) >> 128;

157:             if (absTick & 0x800 != 0) sqrtR = (sqrtR * 0xe7159475a2c29b7443b29c7fa6e889d9) >> 128;

159:             if (absTick & 0x1000 != 0) sqrtR = (sqrtR * 0xd097f3bdfd2022b8845ad8f792aa5825) >> 128;

161:             if (absTick & 0x2000 != 0) sqrtR = (sqrtR * 0xa9f746462d870fdf8a65dc1f90e061e5) >> 128;

163:             if (absTick & 0x4000 != 0) sqrtR = (sqrtR * 0x70d869a156d2a1b890bb3df62baf32f7) >> 128;

165:             if (absTick & 0x8000 != 0) sqrtR = (sqrtR * 0x31be135f97d08fd981231505542fcfa6) >> 128;

167:             if (absTick & 0x10000 != 0) sqrtR = (sqrtR * 0x9aa508b5b7a84e1c677de54f3e99bc9) >> 128;

169:             if (absTick & 0x20000 != 0) sqrtR = (sqrtR * 0x5d6af8dedb81196699c329225ee604) >> 128;

171:             if (absTick & 0x40000 != 0) sqrtR = (sqrtR * 0x2216e584f5fa1ea926041bedfe98) >> 128;

173:             if (absTick & 0x80000 != 0) sqrtR = (sqrtR * 0x48a170391f7dc42444e8fa2) >> 128;

176:             if (tick > 0) sqrtR = type(uint256).max / sqrtR;

179:             return uint160((sqrtR >> 32) + (sqrtR % (1 << 32) == 0 ? 0 : 1));

184:                     LIQUIDITY AMOUNTS (STRIKE+WIDTH)

198:                     highPriceX96 - lowPriceX96,

200:                 ) / lowPriceX96;

212:             return mulDiv96(liquidityChunk.liquidity(), highPriceX96 - lowPriceX96);

258:                             highPriceX96 - lowPriceX96

284:                     toUint128(mulDiv(amount1, Constants.FP96, highPriceX96 - lowPriceX96))

351:             uint256 prod0; // Least significant 256 bits of the product

352:             uint256 prod1; // Most significant 256 bits of the product

353:             assembly ("memory-safe") {

362:                 assembly ("memory-safe") {

379:             assembly ("memory-safe") {

383:             assembly ("memory-safe") {

391:             uint256 twos = (0 - denominator) & denominator;

393:             assembly ("memory-safe") {

398:             assembly ("memory-safe") {

404:             assembly ("memory-safe") {

407:             prod0 |= prod1 * twos;

414:             uint256 inv = (3 * denominator) ^ 2;

418:             inv *= 2 - denominator * inv; // inverse mod 2**8

419:             inv *= 2 - denominator * inv; // inverse mod 2**16

420:             inv *= 2 - denominator * inv; // inverse mod 2**32

421:             inv *= 2 - denominator * inv; // inverse mod 2**64

422:             inv *= 2 - denominator * inv; // inverse mod 2**128

423:             inv *= 2 - denominator * inv; // inverse mod 2**256

431:             result = prod0 * inv;

449:                 result++;

465:             uint256 prod0; // Least significant 256 bits of the product

466:             uint256 prod1; // Most significant 256 bits of the product

467:             assembly ("memory-safe") {

476:                 assembly ("memory-safe") {

484:             require(2 ** 64 > prod1);

493:             assembly ("memory-safe") {

497:             assembly ("memory-safe") {

503:             assembly ("memory-safe") {

511:             prod0 |= prod1 * 2 ** 192;

528:             uint256 prod0; // Least significant 256 bits of the product

529:             uint256 prod1; // Most significant 256 bits of the product

530:             assembly ("memory-safe") {

539:                 assembly ("memory-safe") {

547:             require(2 ** 96 > prod1);

556:             assembly ("memory-safe") {

560:             assembly ("memory-safe") {

566:             assembly ("memory-safe") {

574:             prod0 |= prod1 * 2 ** 160;

587:             if (mulmod(a, b, 2 ** 96) > 0) {

589:                 result++;

605:             uint256 prod0; // Least significant 256 bits of the product

606:             uint256 prod1; // Most significant 256 bits of the product

607:             assembly ("memory-safe") {

616:                 assembly ("memory-safe") {

624:             require(2 ** 128 > prod1);

633:             assembly ("memory-safe") {

637:             assembly ("memory-safe") {

643:             assembly ("memory-safe") {

651:             prod0 |= prod1 * 2 ** 128;

664:             if (mulmod(a, b, 2 ** 128) > 0) {

666:                 result++;

682:             uint256 prod0; // Least significant 256 bits of the product

683:             uint256 prod1; // Most significant 256 bits of the product

684:             assembly ("memory-safe") {

693:                 assembly ("memory-safe") {

701:             require(2 ** 192 > prod1);

710:             assembly ("memory-safe") {

714:             assembly ("memory-safe") {

720:             assembly ("memory-safe") {

728:             prod0 |= prod1 * 2 ** 64;

739:         assembly ("memory-safe") {

758:             int256 pivot = arr[uint256(left + (right - left) / 2)];

760:                 while (arr[uint256(i)] < pivot) i++;

761:                 while (pivot < arr[uint256(j)]) j--;

764:                     i++;

765:                     j--;

778:             quickSort(data, int256(0), int256(data.length - 1));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

23:     uint256 internal constant MAX_UINT256 = 2 ** 256 - 1;

51:             poolId += uint64(uint24(tickSpacing)) << 48;

62:             return (poolId & TICKSPACING_MASK) + (uint48(poolId) + 1);

77:             return addr == address(0) ? 40 : 39 - Math.mostSignificantNibble(uint160(addr));

107:                     ? uint256(updatedHash) + (((existingHash >> 248) + 1) << 248)

108:                     : uint256(updatedHash) + (((existingHash >> 248) - 1) << 248);

133:             int256[] memory tickCumulatives = new int256[](cardinality + 1);

135:             uint256[] memory timestamps = new uint256[](cardinality + 1);

137:             for (uint256 i = 0; i < cardinality + 1; ++i) {

140:                         (int256(observationIndex) - int256(i * period)) +

148:             for (uint256 i = 0; i < cardinality; ++i) {

150:                     (tickCumulatives[i] - tickCumulatives[i + 1]) /

151:                     int256(timestamps[i] - timestamps[i + 1]);

155:             return int24(Math.sort(ticks)[cardinality / 2]);

178:                 (int24(uint24(medianData >> ((uint24(medianData >> (192 + 3 * 3)) % 8) * 24))) +

179:                     int24(uint24(medianData >> ((uint24(medianData >> (192 + 3 * 4)) % 8) * 24)))) /

183:             if (block.timestamp >= uint256(uint40(medianData >> 216)) + period) {

188:                             int256(observationIndex) - int256(1) + int256(observationCardinality)

195:                         (tickCumulative_last - tickCumulative_old) /

196:                             int256(timestamp_last - timestamp_old)

207:                 for (uint8 i; i < 8; ++i) {

209:                     rank = (orderMap >> (3 * i)) % 8;

212:                         shift -= 1;

217:                     entry = int24(uint24(medianData >> (rank * 24)));

219:                         shift += 1;

223:                     newOrderMap = newOrderMap + ((rank + 1) << (3 * (i + shift - 1)));

227:                     (block.timestamp << 216) +

228:                     (uint256(newOrderMap) << 192) +

229:                     uint256(uint192(medianData << 24)) +

248:             for (uint256 i = 0; i < 20; ++i) {

249:                 secondsAgos[i] = uint32(((i + 1) * twapWindow) / 20);

256:             for (uint256 i = 0; i < 19; ++i) {

258:                     (tickCumulatives[i] - tickCumulatives[i + 1]) / int56(uint56(twapWindow / 20))

324:         uint256 amount = uint256(positionSize) * tokenId.optionRatio(legIndex);

346:             int24 minTick = (Constants.MIN_V3POOL_TICK / tickSpacing) * tickSpacing;

347:             int24 maxTick = (Constants.MAX_V3POOL_TICK / tickSpacing) * tickSpacing;

351:             (tickLower, tickUpper) = (strike - rangeDown, strike + rangeUp);

376:             (width * tickSpacing) / 2,

377:             int24(int256(Math.unsafeDivRoundingUp(uint24(width) * uint24(tickSpacing), 2)))

407:                 ++leg;

427:                 tokenData0.rightSlot() + convert1to0(tokenData1.rightSlot(), sqrtPriceX96),

428:                 tokenData0.leftSlot() + convert1to0(tokenData1.leftSlot(), sqrtPriceX96)

432:                 tokenData1.rightSlot() + convert0to1(tokenData0.rightSlot(), sqrtPriceX96),

433:                 tokenData1.leftSlot() + convert0to1(tokenData0.leftSlot(), sqrtPriceX96)

476:                 ? convert0to1(contractSize, Math.getSqrtRatioAtTick((tickUpper + tickLower) / 2))

477:                 : convert1to0(contractSize, Math.getSqrtRatioAtTick((tickUpper + tickLower) / 2));

495:                 return Math.mulDiv192(amount, uint256(sqrtPriceX96) ** 2);

512:                 return Math.mulDiv(amount, 2 ** 192, uint256(sqrtPriceX96) ** 2);

514:                 return Math.mulDiv(amount, 2 ** 128, Math.mulDiv64(sqrtPriceX96, sqrtPriceX96));

530:                     .mulDiv192(Math.absUint(amount), uint256(sqrtPriceX96) ** 2)

532:                 return amount < 0 ? -absResult : absResult;

537:                 return amount < 0 ? -absResult : absResult;

553:                     .mulDiv(Math.absUint(amount), 2 ** 192, uint256(sqrtPriceX96) ** 2)

555:                 return amount < 0 ? -absResult : absResult;

560:                         2 ** 128,

564:                 return amount < 0 ? -absResult : absResult;

585:             amount0 = positionSize * uint128(tokenId.optionRatio(legIndex));

591:             amount1 = positionSize * uint128(tokenId.optionRatio(legIndex));

638:                        REVOKE/REFUND COMPUTATIONS

669:                 uint256 requiredRatioX128 = (required0 << 128) / (required0 + required1);

678:                 uint256 bonusCross = Math.min(balanceCross / 2, thresholdCross - balanceCross);

685:                         Math.mulDiv128(bonusCross, 2 ** 128 - requiredRatioX128),

693:             int256 balance0 = int256(uint256(tokenData0.rightSlot())) -

695:             int256 balance1 = int256(uint256(tokenData1.rightSlot())) -

698:             int256 paid0 = bonus0 + int256(netExchanged.rightSlot());

699:             int256 paid1 = bonus1 + int256(netExchanged.leftSlot());

715:                     bonus1 += Math.min(

716:                         balance1 - paid1,

717:                         PanopticMath.convert0to1(paid0 - balance0, sqrtPriceX96Final)

719:                     bonus0 -= Math.min(

720:                         PanopticMath.convert1to0(balance1 - paid1, sqrtPriceX96Final),

721:                         paid0 - balance0

733:                     bonus0 += Math.min(

734:                         balance0 - paid0,

735:                         PanopticMath.convert1to0(paid1 - balance1, sqrtPriceX96Final)

737:                     bonus1 -= Math.min(

738:                         PanopticMath.convert0to1(balance0 - paid0, sqrtPriceX96Final),

739:                         paid1 - balance1

744:             paid0 = bonus0 + int256(netExchanged.rightSlot());

745:             paid1 = bonus1 + int256(netExchanged.leftSlot());

749:                 LeftRightSigned.wrap(0).toRightSlot(int128(balance0 - paid0)).toLeftSlot(

750:                     int128(balance1 - paid1)

781:             for (uint256 i = 0; i < positionIdList.length; ++i) {

784:                 for (uint256 leg = 0; leg < numLegs; ++leg) {

791:             int256 collateralDelta0 = -Math.min(collateralRemaining.rightSlot(), 0);

792:             int256 collateralDelta1 = -Math.min(collateralRemaining.leftSlot(), 0);

803:                     -Math.min(

804:                         collateralDelta0 - longPremium.rightSlot(),

806:                             longPremium.leftSlot() - collateralDelta1,

811:                         longPremium.leftSlot() - collateralDelta1,

813:                             collateralDelta0 - longPremium.rightSlot(),

820:                 haircut1 = protocolLoss1 + collateralDelta1;

828:                         longPremium.rightSlot() - collateralDelta0,

830:                             collateralDelta1 - longPremium.leftSlot(),

834:                     -Math.min(

835:                         collateralDelta1 - longPremium.leftSlot(),

837:                             longPremium.rightSlot() - collateralDelta0,

843:                 haircut0 = collateralDelta0 + protocolLoss0;

860:             for (uint256 i = 0; i < positionIdList.length; i++) {

863:                 for (uint256 leg = 0; leg < tokenId.countLegs(); ++leg) {

870:                             uint128(-_premiasByLeg[i][leg].rightSlot()) * uint256(haircut0),

874:                             uint128(-_premiasByLeg[i][leg].leftSlot()) * uint256(haircut1),

890:                             uint128(-_premiasByLeg[i][leg].rightSlot()) - settled0

894:                             uint128(-_premiasByLeg[i][leg].leftSlot()) - settled1

928:             int256 balanceShortage = refundValues.rightSlot() -

935:                         .toRightSlot(int128(refundValues.rightSlot() - balanceShortage))

940:                                 ) + refundValues.leftSlot()

946:                 refundValues.leftSlot() -

953:                         .toLeftSlot(int128(refundValues.leftSlot() - balanceShortage))

958:                                 ) + refundValues.rightSlot()

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/libraries/SafeTransferLib.sol

24:         assembly ("memory-safe") {

30:             mstore(add(4, p), from) // Append the "from" argument.

31:             mstore(add(36, p), to) // Append the "to" argument.

32:             mstore(add(68, p), amount) // Append the "amount" argument.

55:         assembly ("memory-safe") {

61:             mstore(add(4, p), to) // Append the "to" argument.

62:             mstore(add(36, p), amount) // Append the "amount" argument.

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/SafeTransferLib.sol)

```solidity
File: contracts/multicall/Multicall.sol

25:                 assembly ("memory-safe") {

33:                 ++i;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/multicall/Multicall.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

103:         balanceOf[from][id] -= amount;

107:             balanceOf[to][id] += amount;

147:             balanceOf[from][id] -= amount;

151:                 balanceOf[to][id] += amount;

157:                 ++i;

187:             for (uint256 i = 0; i < owners.length; ++i) {

202:             interfaceId == 0x01ffc9a7 || // ERC165 Interface ID for ERC165

203:             interfaceId == 0xd9b67a26; // ERC165 Interface ID for ERC1155

207:                         INTERNAL MINT/BURN LOGIC

217:             balanceOf[to][id] += amount;

237:         balanceOf[from][id] -= amount;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/tokens/ERC20Minimal.sol

62:         balanceOf[msg.sender] -= amount;

67:             balanceOf[to] += amount;

82:         uint256 allowed = allowance[from][msg.sender]; // Saves gas for limited approvals.

84:         if (allowed != type(uint256).max) allowance[from][msg.sender] = allowed - amount;

86:         balanceOf[from] -= amount;

91:             balanceOf[to] += amount;

104:         balanceOf[from] -= amount;

109:             balanceOf[to] += amount;

116:                         INTERNAL MINT/BURN LOGIC

126:             balanceOf[to] += amount;

128:         totalSupply += amount;

137:         balanceOf[from] -= amount;

142:             totalSupply -= amount;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC20Minimal.sol)

```solidity
File: contracts/types/LeftRight.sol

68:                     (LeftRightUnsigned.unwrap(self) & LEFT_HALF_BIT_MASK) +

69:                         uint256(uint128(LeftRightUnsigned.unwrap(self)) + right)

88:                     (LeftRightSigned.unwrap(self) & LEFT_HALF_BIT_MASK_INT) +

89:                         (int256(int128(LeftRightSigned.unwrap(self)) + right) & RIGHT_HALF_BIT_MASK)

126:             return LeftRightUnsigned.wrap(LeftRightUnsigned.unwrap(self) + (uint256(left) << 128));

136:             return LeftRightSigned.wrap(LeftRightSigned.unwrap(self) + (int256(left) << 128));

155:             z = LeftRightUnsigned.wrap(LeftRightUnsigned.unwrap(x) + LeftRightUnsigned.unwrap(y));

178:             z = LeftRightUnsigned.wrap(LeftRightUnsigned.unwrap(x) - LeftRightUnsigned.unwrap(y));

196:             int256 left = int256(uint256(x.leftSlot())) + y.leftSlot();

201:             int256 right = int256(uint256(x.rightSlot())) + y.rightSlot();

216:             int256 left256 = int256(x.leftSlot()) + y.leftSlot();

219:             int256 right256 = int256(x.rightSlot()) + y.rightSlot();

234:             int256 left256 = int256(x.leftSlot()) - y.leftSlot();

237:             int256 right256 = int256(x.rightSlot()) - y.rightSlot();

256:             int256 left256 = int256(x.leftSlot()) - y.leftSlot();

259:             int256 right256 = int256(x.rightSlot()) - y.rightSlot();

285:         uint128 z_xR = (uint256(x.rightSlot()) + dx.rightSlot()).toUint128Capped();

286:         uint128 z_xL = (uint256(x.leftSlot()) + dx.leftSlot()).toUint128Capped();

287:         uint128 z_yR = (uint256(y.rightSlot()) + dy.rightSlot()).toUint128Capped();

288:         uint128 z_yL = (uint256(y.leftSlot()) + dy.leftSlot()).toUint128Capped();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LeftRight.sol)

```solidity
File: contracts/types/LiquidityChunk.sol

78:                     (uint256(uint24(_tickLower)) << 232) +

79:                         (uint256(uint24(_tickUpper)) << 208) +

94:             return LiquidityChunk.wrap(LiquidityChunk.unwrap(self) + amount);

109:                     LiquidityChunk.unwrap(self) + (uint256(uint24(_tickLower)) << 232)

126:                     LiquidityChunk.unwrap(self) + ((uint256(uint24(_tickUpper))) << 208)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LiquidityChunk.sol)

```solidity
File: contracts/types/TokenId.sol

98:             return int24(uint24((TokenId.unwrap(self) >> 48) % 2 ** 16));

110:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48)) % 2);

120:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 1)) % 128);

130:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 8)) % 2);

140:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 9)) % 2);

150:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 10)) % 4);

160:             return int24(int256(TokenId.unwrap(self) >> (64 + legIndex * 48 + 12)));

171:             return int24(int256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 36)) % 4096));

172:         } // "% 4096" = take last (2 ** 12 = 4096) 12 bits

185:             return TokenId.wrap(TokenId.unwrap(self) + _poolId);

195:             return TokenId.wrap(TokenId.unwrap(self) + (uint256(uint24(_tickSpacing)) << 48));

212:                 TokenId.wrap(TokenId.unwrap(self) + (uint256(_asset % 2) << (64 + legIndex * 48)));

229:                     TokenId.unwrap(self) + (uint256(_optionRatio % 128) << (64 + legIndex * 48 + 1))

246:             return TokenId.wrap(TokenId.unwrap(self) + ((_isLong % 2) << (64 + legIndex * 48 + 8)));

263:                     TokenId.unwrap(self) + (uint256(_tokenType % 2) << (64 + legIndex * 48 + 9))

281:                     TokenId.unwrap(self) + (uint256(_riskPartner % 4) << (64 + legIndex * 48 + 10))

299:                     TokenId.unwrap(self) +

300:                         uint256((int256(_strike) & BITMASK_INT24) << (64 + legIndex * 48 + 12))

319:                     TokenId.unwrap(self) +

320:                         (uint256(uint24(_width) % 4096) << (64 + legIndex * 48 + 36))

376:             if (optionRatios < 2 ** 64) {

378:             } else if (optionRatios < 2 ** 112) {

380:             } else if (optionRatios < 2 ** 160) {

382:             } else if (optionRatios < 2 ** 208) {

395:                         ((LONG_MASK >> (48 * (4 - optionRatios))) & CLEAR_POOLID_MASK)

406:             return self.isLong(0) + self.isLong(1) + self.isLong(2) + self.isLong(3);

439:         if (optionRatios < 2 ** 64) {

441:         } else if (optionRatios < 2 ** 112) {

443:         } else if (optionRatios < 2 ** 160) {

445:         } else if (optionRatios < 2 ** 208) {

507:             for (uint256 i = 0; i < 4; ++i) {

512:                     if ((TokenId.unwrap(self) >> (64 + 48 * i)) != 0)

515:                     break; // we are done iterating over potential legs

520:                 for (uint256 j = i + 1; j < numLegs; ++j) {

521:                     if (uint48(chunkData >> (48 * i)) == uint48(chunkData >> (48 * j))) {

569:             } // end for loop over legs

581:             for (uint256 i = 0; i < numLegs; ++i) {

589:                 if ((currentTick >= _strike + rangeUp) || (currentTick < _strike - rangeDown)) {

592:                     if (self.isLong(i) == 1) return; // validated

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="GAS-6"></a>[GAS-6] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*Instances (3)*:
```solidity
File: contracts/libraries/PanopticMath.sol

929:                 int256(collateral0.convertToAssets(collateral0.balanceOf(refunder)));

947:                 int256(collateral1.convertToAssets(collateral1.balanceOf(refunder)));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/multicall/Multicall.sol

15:             (bool success, bytes memory result) = address(this).delegatecall(data[i]);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/multicall/Multicall.sol)

### <a name="GAS-7"></a>[GAS-7] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (4)*:
```solidity
File: contracts/CollateralTracker.sol

894:     function delegate(address delegatee, uint256 assets) external onlyPanopticPool {

903:     function refund(address delegatee, uint256 assets) external onlyPanopticPool {

975:     function refund(address refunder, address refundee, int256 assets) external onlyPanopticPool {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

163:     function setOwner(address newOwner) external nonReentrant onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

### <a name="GAS-8"></a>[GAS-8] `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)
Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
  
However, pre-increments (or pre-decrements) return the new value:
  
```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```

In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

*Saves 5 gas per instance*

*Instances (24)*:
```solidity
File: contracts/CollateralTracker.sol

764:                    100% - |                _------

767:                     20% - |---------¯

769:                           +---------+-------+-+--->   POOL_

827:            10% - |----------__       min_ratio = 5%

828:            5%  - | . . . . .  ¯¯¯--______

830:                  +---------+-------+-+--->   POOL_

1386:                            100% + SCR% - |--__           .    .    .

1387:                                   100% - | . .¯¯--__     .    .    .

1388:                                          |    .     ¯¯--__    .    .

1389:                                    SCR - |    .          .¯¯--__________

1391:                                          +----+----------+----+----+--->   current

1620:                   100% - |--__                .

1621:                          |    ¯¯--__          .

1622:                          |          ¯¯--__    .

1623:                  SCR/2 - |                ¯¯--______ <------ base collateral is half that of a single-leg

1624:                          +--------------------+--->   current

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

503:         tokenId = s_nextId++;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

260:         However, since we require that Eqn 2 holds up-- ie. the gross fees collected should be equal

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Math.sol

449:                 result++;

589:                 result++;

666:                 result++;

764:                     i++;

765:                     j--;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

860:             for (uint256 i = 0; i < positionIdList.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="GAS-9"></a>[GAS-9] Use shift right/left instead of division/multiplication if possible
While the `DIV` / `MUL` opcode uses 5 gas, the `SHR` / `SHL` opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version `0.8+`
- Use `>> 1` instead of `/ 2`
- Use `>> 2` instead of `/ 4`
- Use `<< 3` instead of `* 8`
- ...
- Use `>> 5` instead of `/ 2^5 == / 32`
- Use `<< 6` instead of `* 2^6 == * 64`

TL;DR:
- Shifting left by N is like multiplying by 2^N (Each bits to the left is an increased power of 2)
- Shifting right by N is like dividing by 2^N (Each bits to the right is a decreased power of 2)

*Saves around 2 gas + 20 for unchecked per instance*

*Instances (29)*:
```solidity
File: contracts/CollateralTracker.sol

843:                 return BUYER_COLLATERAL_RATIO / 2;

851:                     (SATURATED_POOL_UTIL - TARGET_POOL_UTIL)) / 2; // do the division by 2 at the end after all addition and multiplication; b/c y1 = buyCollateralRatio / 2

1614:                     Put side of a short strangle, BPR = 100% - (100% - SCR/2)*(price/strike)

1623:                  SCR/2 - |                ¯¯--______ <------ base collateral is half that of a single-leg

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

1487:             effectiveLiquidityFactorX32 = (uint256(totalLiquidity) * 2 ** 32) / netLiquidity;

1552:                                         (liquidityChunk.liquidity())) / 2 ** 64

1561:                                         (liquidityChunk.liquidity())) / 2 ** 64

1635:                 .toRightSlot(int128(int256((accumulatedPremium.rightSlot() * liquidity) / 2 ** 64)))

1636:                 .toLeftSlot(int128(int256((accumulatedPremium.leftSlot() * liquidity) / 2 ** 64)));

1769:                 totalLiquidity) / 2 ** 64;

1771:                 totalLiquidity) / 2 ** 64;

1942:                                                     )) + int256(legPremia.rightSlot() * 2 ** 64),

1959:                                                     )) + int256(legPremia.leftSlot()) * 2 ** 64,

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

219:         For an arbitrary parameter 0 <= ν <= 1 (ν = 1/2^VEGOID). This way, the gross_feesCollectedX128 will be given by: 

1352:                     totalLiquidity * 2 ** 64,

1357:                     totalLiquidity * 2 ** 64,

1367:                     uint256 numerator = netLiquidity + (removedLiquidity / 2 ** VEGOID);

1391:                         ((removedLiquidity ** 2) / 2 ** (VEGOID));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Math.sol

511:             prod0 |= prod1 * 2 ** 192;

574:             prod0 |= prod1 * 2 ** 160;

651:             prod0 |= prod1 * 2 ** 128;

728:             prod0 |= prod1 * 2 ** 64;

758:             int256 pivot = arr[uint256(left + (right - left) / 2)];

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

155:             return int24(Math.sort(ticks)[cardinality / 2]);

179:                     int24(uint24(medianData >> ((uint24(medianData >> (192 + 3 * 4)) % 8) * 24)))) /

376:             (width * tickSpacing) / 2,

476:                 ? convert0to1(contractSize, Math.getSqrtRatioAtTick((tickUpper + tickLower) / 2))

477:                 : convert1to0(contractSize, Math.getSqrtRatioAtTick((tickUpper + tickLower) / 2));

678:                 uint256 bonusCross = Math.min(balanceCross / 2, thresholdCross - balanceCross);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="GAS-10"></a>[GAS-10] Increments/decrements can be unchecked in for-loops
In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

[ethereum/solidity#10695](https://github.com/ethereum/solidity/issues/10695)

The change would be:

```diff
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

These save around **25 gas saved** per instance.

The same can be applied with decrements (which should use `break` when `i == 0`).

The risk of overflow is non-existent for `uint256`.

*Instances (16)*:
```solidity
File: contracts/CollateralTracker.sol

662:             for (uint256 leg = 0; leg < positionId.countLegs(); ++leg) {

1255:             for (uint256 index = 0; index < numLegs; ++index) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

1672:         for (uint256 leg = 0; leg < numLegs; ++leg) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

137:             for (uint256 i = 0; i < cardinality + 1; ++i) {

148:             for (uint256 i = 0; i < cardinality; ++i) {

207:                 for (uint8 i; i < 8; ++i) {

248:             for (uint256 i = 0; i < 20; ++i) {

256:             for (uint256 i = 0; i < 19; ++i) {

781:             for (uint256 i = 0; i < positionIdList.length; ++i) {

784:                 for (uint256 leg = 0; leg < numLegs; ++leg) {

860:             for (uint256 i = 0; i < positionIdList.length; i++) {

863:                 for (uint256 leg = 0; leg < tokenId.countLegs(); ++leg) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

187:             for (uint256 i = 0; i < owners.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/types/TokenId.sol

507:             for (uint256 i = 0; i < 4; ++i) {

520:                 for (uint256 j = i + 1; j < numLegs; ++j) {

581:             for (uint256 i = 0; i < numLegs; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="GAS-11"></a>[GAS-11] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (29)*:
```solidity
File: contracts/CollateralTracker.sol

709:                     (tokenType == 1 && currentValue0 < oracleValue0)

976:         if (assets > 0) {

1010:             if (tokenToPay > 0) {

1067:             if (tokenToPay > 0) {

1168:         if (positionBalanceArray.length > 0) {

1182:         if (premiumAllPositions > 0) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

198:         if (amount0Owed > 0)

205:         if (amount1Owed > 0)

235:         (token0, token1) = token0 < token1 ? (token0, token1) : (token1, token0);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

1274:         if (positionIdListExercisor.length > 0)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

219:         For an arbitrary parameter 0 <= ν <= 1 (ν = 1/2^VEGOID). This way, the gross_feesCollectedX128 will be given by: 

412:         if (amount0Owed > 0)

419:         if (amount1Owed > 0)

446:         address token = amount0Delta > 0

453:         uint256 amountToPay = amount0Delta > 0 ? uint256(amount0Delta) : uint256(amount1Delta);

819:                 zeroForOne = net0 < 0;

824:                 zeroForOne = itm0 < 0;

827:                 zeroForOne = itm1 > 0;

1085:         if (currentLiquidity.rightSlot() > 0) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Math.sol

74:         return x > 0 ? x : -x;

83:             return x > 0 ? uint256(x) : uint256(-x);

176:             if (tick > 0) sqrtR = type(uint256).max / sqrtR;

361:                 require(denominator > 0);

447:             if (mulmod(a, b, denominator) > 0) {

587:             if (mulmod(a, b, 2 ** 96) > 0) {

664:             if (mulmod(a, b, 2 ** 128) > 0) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

669:                 uint256 requiredRatioX128 = (required0 << 128) / (required0 + required1);

931:             if (balanceShortage > 0) {

949:             if (balanceShortage > 0) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="GAS-12"></a>[GAS-12] WETH address definition can be use directly
WETH is a wrap Ether contract with a specific address in the Ethereum network, giving the option to define it may cause false recognition, it is healthier to define it directly.

    Advantages of defining a specific contract directly:
    
    It saves gas,
    Prevents incorrect argument definition,
    Prevents execution on a different chain and re-signature issues,
    WETH Address : 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

*Instances (2)*:
```solidity
File: contracts/PanopticFactory.sol

82:     address internal immutable WETH;

86:     uint256 internal constant FULL_RANGE_LIQUIDITY_AMOUNT_WETH = 0.1 ether;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked` | 13 |
| [NC-2](#NC-2) | `constant`s should be defined rather than using magic numbers | 227 |
| [NC-3](#NC-3) | Control structures do not follow the Solidity Style Guide | 142 |
| [NC-4](#NC-4) | Critical Changes Should Use Two-step Procedure | 1 |
| [NC-5](#NC-5) | Functions should not be longer than 50 lines | 119 |
| [NC-6](#NC-6) | Change int to int256 | 11 |
| [NC-7](#NC-7) | Lines are too long | 1 |
| [NC-8](#NC-8) | `type(uint256).max` should be used instead of `2 ** 256 - 1` | 2 |
| [NC-9](#NC-9) | Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor | 10 |
| [NC-10](#NC-10) | Take advantage of Custom Error's return value property | 58 |
| [NC-11](#NC-11) | Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`) | 1 |
| [NC-12](#NC-12) | Strings should use double quotes rather than single quotes | 2 |
| [NC-13](#NC-13) | Use Underscores for Number Literals (add an underscore every 3 digits) | 9 |
| [NC-14](#NC-14) | Constants should be defined rather than using magic numbers | 33 |
| [NC-15](#NC-15) | Variables need not be initialized to zero | 31 |
### <a name="NC-1"></a>[NC-1] Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked`
Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

Solidity version 0.8.12 introduces `string.concat()` (vs `abi.encodePacked(<str>,<str>), which catches concatenation errors (in the event of a `bytes` data mixed in the concatenation)`)

*Instances (13)*:
```solidity
File: contracts/PanopticFactory.sol

534:             bytes32(abi.encodePacked(uint80(uint160(_v3Pool)), uint80(uint160(_deployer)), _nonce));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

462:                         abi.encodePacked(

1643:                 abi.encodePacked(

1674:                 abi.encodePacked(tokenId.strike(leg), tokenId.width(leg), tokenId.tokenType(leg))

1856:                 abi.encodePacked(tokenId.strike(leg), tokenId.width(leg), tokenId.tokenType(leg))

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

612:                 abi.encodePacked(

621:                 abi.encodePacked(

975:             abi.encodePacked(

1151:                     abi.encodePacked(

1431:             keccak256(abi.encodePacked(univ3pool, owner, tokenType, tickLower, tickUpper))

1459:             abi.encodePacked(univ3pool, owner, tokenType, tickLower, tickUpper)

1544:             keccak256(abi.encodePacked(univ3pool, owner, tokenType, tickLower, tickUpper))

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

879:                             abi.encodePacked(

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="NC-2"></a>[NC-2] `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*Instances (227)*:
```solidity
File: contracts/CollateralTracker.sol

200:             int256 ratioTick = (int256(_sellerCollateralRatio) - 2000);

202:                 2230 +

204:                     10_000 +

205:                     (7812 * ratioTick ** 2) /

206:                     10_000 ** 2 +

207:                     (6510 * ratioTick ** 3) /

208:                     10_000 ** 3

234:         totalSupply = 10 ** 6;

249:             _poolFee = fee / 100;

671:                                 2

763:                           |                  max ratio = 100%

764:                    100% - |                _------

767:                     20% - |---------¯

770:                                    50%    90% 100%     UTILIZATION

778:                 min_sell_ratio /= 2;

826:                  |   buy_ratio = 10%

827:            10% - |----------__       min_ratio = 5%

828:            5%  - | . . . . .  ¯¯¯--______

831:                           50%    90% 100%      UTILIZATION

843:                 return BUYER_COLLATERAL_RATIO / 2;

851:                     (SATURATED_POOL_UTIL - TARGET_POOL_UTIL)) / 2; // do the division by 2 at the end after all addition and multiplication; b/c y1 = buyCollateralRatio / 2

1330:             : int64(uint64(poolUtilization >> 64));

1378:                                     Short put BPR = 100% - (price/strike) + SCR

1386:                            100% + SCR% - |--__           .    .    .

1387:                                   100% - | . .¯¯--__     .    .    .

1586:                     : int64(uint64(poolUtilization >> 64))

1614:                     Put side of a short strangle, BPR = 100% - (100% - SCR/2)*(price/strike)

1620:                   100% - |--__                .

1632:             uint64 poolUtilization1 = uint64(poolUtilization >> 64);

1638:                 (uint128(uint64(-int64(poolUtilization1 == 0 ? 1 : poolUtilization1))) << 64);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

296:         2  : jade lizard

297:         3  : straddle

298:         4  : iron butterfly

299:         5  : iron condor

300:         6  : super-bull

301:         7  : calendar

302:         8  : covered position

303:         9  : super-bear

318:         2  : EPIQUE

319:         3  : MYTHIQUE

320:         4  : LEGENDAIRE

321:         5  : FANTASMAGORIQUE

322:         6  : AGATHIQUE

323:         7  : QUIXOTIQUE

324:         8  : UTOPIQUE

325:         9  : VITALIQUE

326:         10 : ETHERONIQUE

327:         11 : PROMETHEIQUE

328:         12 : ATOMIQUE

329:         13 : QUANTIQUE

330:         14 : LEPTONIQUE

331:         15 : QUARKTIQUE

332:         16 : BRANIQUE

333:         17 : CONIQUE

334:         18 : COMIQUE

335:         19 : STEREOTYPIQUE

336:         20+: BASIQUE

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

309:                 (uint256(block.timestamp) << 216) +

313:                 (uint256(uint24(currentTick)) << 24) + // add to slot 4

370:         poolUtilization1 = uint64(balanceData.leftSlot() >> 64);

730:             return uint128(uint256(utilization0) + uint128(uint256(utilization1) << 64));

1329:             return balanceCross >= Math.unsafeDivRoundingUp(thresholdCross * buffer, 10_000);

1348:                 Math.mulDiv(uint256(tokenData1.rightSlot()), 2 ** 96, sqrtPriceX96) +

1353:                 Math.mulDivRoundingUp(uint256(tokenData1.leftSlot()), 2 ** 96, sqrtPriceX96) +

1415:         if ((newHash >> 248) > MAX_POSITIONS) revert Errors.TooManyPositionsOpen();

1445:         _numberOfPositions = (s_positionsHash[user] >> 248);

1487:             effectiveLiquidityFactorX32 = (uint256(totalLiquidity) * 2 ** 32) / netLiquidity;

1552:                                         (liquidityChunk.liquidity())) / 2 ** 64

1561:                                         (liquidityChunk.liquidity())) / 2 ** 64

1596:         if (tokenId.isLong(legIndex) == 0 || legIndex > 3) revert Errors.NotALongLeg();

1635:                 .toRightSlot(int128(int256((accumulatedPremium.rightSlot() * liquidity) / 2 ** 64)))

1636:                 .toLeftSlot(int128(int256((accumulatedPremium.leftSlot() * liquidity) / 2 ** 64)));

1769:                 totalLiquidity) / 2 ** 64;

1771:                 totalLiquidity) / 2 ** 64;

1942:                                                     )) + int256(legPremia.rightSlot() * 2 ** 64),

1959:                                                     )) + int256(legPremia.leftSlot()) * 2 ** 64,

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

223:                                       = feeGrowthX128 * T * (1 + ν*R^2/(N*T))                (Eqn 2)

244:              s_accountPremiumOwed += feesCollected * T/N^2 * (1 - R/T + ν*R/T)          (Eqn 3)     

260:         However, since we require that Eqn 2 holds up-- ie. the gross fees collected should be equal

264:             s_accountPremiumGross += feesCollected * T/N^2 * (1 - R/T + ν*R^2/T^2)       (Eqn 4) 

273:                                 = ∆feeGrowthX128 * t * (1  + ν*R^2/(N*T))   (same as Eqn 2)

275:         where the last expression matches Eqn 2 exactly.

388:             s_AddrToPoolIdData[univ3pool] = uint256(poolId) + 2 ** 255;

1352:                     totalLiquidity * 2 ** 64,

1353:                     netLiquidity ** 2

1357:                     totalLiquidity * 2 ** 64,

1358:                     netLiquidity ** 2

1367:                     uint256 numerator = netLiquidity + (removedLiquidity / 2 ** VEGOID);

1388:                     uint256 numerator = totalLiquidity ** 2 -

1391:                         ((removedLiquidity ** 2) / 2 ** (VEGOID));

1394:                         .mulDiv(premium0X64_base, numerator, totalLiquidity ** 2)

1397:                         .mulDiv(premium1X64_base, numerator, totalLiquidity ** 2)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Constants.sol

22:         1461446703485210103287273052203988822378723970342;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Constants.sol)

```solidity
File: contracts/libraries/Math.sol

94:                 x >>= 128;

95:                 r += 32;

98:                 x >>= 64;

99:                 r += 16;

102:                 x >>= 32;

103:                 r += 8;

106:                 x >>= 16;

107:                 r += 4;

110:                 x >>= 8;

111:                 r += 2;

137:             if (absTick & 0x2 != 0) sqrtR = (sqrtR * 0xfff97272373d413259a46990580e213a) >> 128;

139:             if (absTick & 0x4 != 0) sqrtR = (sqrtR * 0xfff2e50f5f656932ef12357cf3c7fdcc) >> 128;

141:             if (absTick & 0x8 != 0) sqrtR = (sqrtR * 0xffe5caca7e10e4e61c3624eaa0941cd0) >> 128;

143:             if (absTick & 0x10 != 0) sqrtR = (sqrtR * 0xffcb9843d60f6159c9db58835c926644) >> 128;

145:             if (absTick & 0x20 != 0) sqrtR = (sqrtR * 0xff973b41fa98c081472e6896dfb254c0) >> 128;

147:             if (absTick & 0x40 != 0) sqrtR = (sqrtR * 0xff2ea16466c96a3843ec78b326b52861) >> 128;

149:             if (absTick & 0x80 != 0) sqrtR = (sqrtR * 0xfe5dee046a99a2a811c461f1969c3053) >> 128;

151:             if (absTick & 0x100 != 0) sqrtR = (sqrtR * 0xfcbe86c7900a88aedcffc83b479aa3a4) >> 128;

153:             if (absTick & 0x200 != 0) sqrtR = (sqrtR * 0xf987a7253ac413176f2b074cf7815e54) >> 128;

155:             if (absTick & 0x400 != 0) sqrtR = (sqrtR * 0xf3392b0822b70005940c7a398e4b70f3) >> 128;

157:             if (absTick & 0x800 != 0) sqrtR = (sqrtR * 0xe7159475a2c29b7443b29c7fa6e889d9) >> 128;

159:             if (absTick & 0x1000 != 0) sqrtR = (sqrtR * 0xd097f3bdfd2022b8845ad8f792aa5825) >> 128;

161:             if (absTick & 0x2000 != 0) sqrtR = (sqrtR * 0xa9f746462d870fdf8a65dc1f90e061e5) >> 128;

163:             if (absTick & 0x4000 != 0) sqrtR = (sqrtR * 0x70d869a156d2a1b890bb3df62baf32f7) >> 128;

165:             if (absTick & 0x8000 != 0) sqrtR = (sqrtR * 0x31be135f97d08fd981231505542fcfa6) >> 128;

167:             if (absTick & 0x10000 != 0) sqrtR = (sqrtR * 0x9aa508b5b7a84e1c677de54f3e99bc9) >> 128;

169:             if (absTick & 0x20000 != 0) sqrtR = (sqrtR * 0x5d6af8dedb81196699c329225ee604) >> 128;

171:             if (absTick & 0x40000 != 0) sqrtR = (sqrtR * 0x2216e584f5fa1ea926041bedfe98) >> 128;

173:             if (absTick & 0x80000 != 0) sqrtR = (sqrtR * 0x48a170391f7dc42444e8fa2) >> 128;

179:             return uint160((sqrtR >> 32) + (sqrtR % (1 << 32) == 0 ? 0 : 1));

197:                     uint256(liquidityChunk.liquidity()) << 96,

414:             uint256 inv = (3 * denominator) ^ 2;

418:             inv *= 2 - denominator * inv; // inverse mod 2**8

419:             inv *= 2 - denominator * inv; // inverse mod 2**16

420:             inv *= 2 - denominator * inv; // inverse mod 2**32

421:             inv *= 2 - denominator * inv; // inverse mod 2**64

422:             inv *= 2 - denominator * inv; // inverse mod 2**128

423:             inv *= 2 - denominator * inv; // inverse mod 2**256

484:             require(2 ** 64 > prod1);

511:             prod0 |= prod1 * 2 ** 192;

547:             require(2 ** 96 > prod1);

574:             prod0 |= prod1 * 2 ** 160;

587:             if (mulmod(a, b, 2 ** 96) > 0) {

624:             require(2 ** 128 > prod1);

651:             prod0 |= prod1 * 2 ** 128;

664:             if (mulmod(a, b, 2 ** 128) > 0) {

701:             require(2 ** 192 > prod1);

728:             prod0 |= prod1 * 2 ** 64;

758:             int256 pivot = arr[uint256(left + (right - left) / 2)];

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

50:             uint64 poolId = uint64(uint160(univ3pool) >> 112);

51:             poolId += uint64(uint24(tickSpacing)) << 48;

77:             return addr == address(0) ? 40 : 39 - Math.mostSignificantNibble(uint160(addr));

107:                     ? uint256(updatedHash) + (((existingHash >> 248) + 1) << 248)

108:                     : uint256(updatedHash) + (((existingHash >> 248) - 1) << 248);

155:             return int24(Math.sort(ticks)[cardinality / 2]);

178:                 (int24(uint24(medianData >> ((uint24(medianData >> (192 + 3 * 3)) % 8) * 24))) +

179:                     int24(uint24(medianData >> ((uint24(medianData >> (192 + 3 * 4)) % 8) * 24)))) /

180:                 2;

183:             if (block.timestamp >= uint256(uint40(medianData >> 216)) + period) {

200:                 uint24 orderMap = uint24(medianData >> 192);

207:                 for (uint8 i; i < 8; ++i) {

209:                     rank = (orderMap >> (3 * i)) % 8;

211:                     if (rank == 7) {

217:                     entry = int24(uint24(medianData >> (rank * 24)));

227:                     (block.timestamp << 216) +

228:                     (uint256(newOrderMap) << 192) +

229:                     uint256(uint192(medianData << 24)) +

248:             for (uint256 i = 0; i < 20; ++i) {

249:                 secondsAgos[i] = uint32(((i + 1) * twapWindow) / 20);

256:             for (uint256 i = 0; i < 19; ++i) {

258:                     (tickCumulatives[i] - tickCumulatives[i + 1]) / int56(uint56(twapWindow / 20))

376:             (width * tickSpacing) / 2,

377:             int24(int256(Math.unsafeDivRoundingUp(uint24(width) * uint24(tickSpacing), 2)))

476:                 ? convert0to1(contractSize, Math.getSqrtRatioAtTick((tickUpper + tickLower) / 2))

477:                 : convert1to0(contractSize, Math.getSqrtRatioAtTick((tickUpper + tickLower) / 2));

495:                 return Math.mulDiv192(amount, uint256(sqrtPriceX96) ** 2);

512:                 return Math.mulDiv(amount, 2 ** 192, uint256(sqrtPriceX96) ** 2);

514:                 return Math.mulDiv(amount, 2 ** 128, Math.mulDiv64(sqrtPriceX96, sqrtPriceX96));

530:                     .mulDiv192(Math.absUint(amount), uint256(sqrtPriceX96) ** 2)

553:                     .mulDiv(Math.absUint(amount), 2 ** 192, uint256(sqrtPriceX96) ** 2)

560:                         2 ** 128,

669:                 uint256 requiredRatioX128 = (required0 << 128) / (required0 + required1);

678:                 uint256 bonusCross = Math.min(balanceCross / 2, thresholdCross - balanceCross);

685:                         Math.mulDiv128(bonusCross, 2 ** 128 - requiredRatioX128),

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/libraries/SafeTransferLib.sol

37:                 or(and(eq(mload(0), 1), gt(returndatasize(), 31)), iszero(returndatasize())),

41:                 call(gas(), token, 0, p, 100, 0, 32)

67:                 or(and(eq(mload(0), 1), gt(returndatasize(), 31)), iszero(returndatasize())),

71:                 call(gas(), token, 0, p, 68, 0, 32)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/SafeTransferLib.sol)

```solidity
File: contracts/multicall/Multicall.sol

26:                     revert(add(result, 32), mload(result))

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/multicall/Multicall.sol)

```solidity
File: contracts/types/LeftRight.sol

102:         return uint128(LeftRightUnsigned.unwrap(self) >> 128);

109:         return int128(LeftRightSigned.unwrap(self) >> 128);

126:             return LeftRightUnsigned.wrap(LeftRightUnsigned.unwrap(self) + (uint256(left) << 128));

136:             return LeftRightSigned.wrap(LeftRightSigned.unwrap(self) + (int256(left) << 128));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LeftRight.sol)

```solidity
File: contracts/types/LiquidityChunk.sol

78:                     (uint256(uint24(_tickLower)) << 232) +

79:                         (uint256(uint24(_tickUpper)) << 208) +

109:                     LiquidityChunk.unwrap(self) + (uint256(uint24(_tickLower)) << 232)

126:                     LiquidityChunk.unwrap(self) + ((uint256(uint24(_tickUpper))) << 208)

173:             return int24(int256(LiquidityChunk.unwrap(self) >> 232));

182:             return int24(int256(LiquidityChunk.unwrap(self) >> 208));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LiquidityChunk.sol)

```solidity
File: contracts/types/TokenId.sol

98:             return int24(uint24((TokenId.unwrap(self) >> 48) % 2 ** 16));

110:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48)) % 2);

120:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 1)) % 128);

130:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 8)) % 2);

140:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 9)) % 2);

150:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 10)) % 4);

160:             return int24(int256(TokenId.unwrap(self) >> (64 + legIndex * 48 + 12)));

171:             return int24(int256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 36)) % 4096));

195:             return TokenId.wrap(TokenId.unwrap(self) + (uint256(uint24(_tickSpacing)) << 48));

212:                 TokenId.wrap(TokenId.unwrap(self) + (uint256(_asset % 2) << (64 + legIndex * 48)));

229:                     TokenId.unwrap(self) + (uint256(_optionRatio % 128) << (64 + legIndex * 48 + 1))

246:             return TokenId.wrap(TokenId.unwrap(self) + ((_isLong % 2) << (64 + legIndex * 48 + 8)));

263:                     TokenId.unwrap(self) + (uint256(_tokenType % 2) << (64 + legIndex * 48 + 9))

281:                     TokenId.unwrap(self) + (uint256(_riskPartner % 4) << (64 + legIndex * 48 + 10))

300:                         uint256((int256(_strike) & BITMASK_INT24) << (64 + legIndex * 48 + 12))

320:                         (uint256(uint24(_width) % 4096) << (64 + legIndex * 48 + 36))

376:             if (optionRatios < 2 ** 64) {

378:             } else if (optionRatios < 2 ** 112) {

380:             } else if (optionRatios < 2 ** 160) {

381:                 optionRatios = 2;

382:             } else if (optionRatios < 2 ** 208) {

383:                 optionRatios = 3;

385:                 optionRatios = 4;

439:         if (optionRatios < 2 ** 64) {

441:         } else if (optionRatios < 2 ** 112) {

443:         } else if (optionRatios < 2 ** 160) {

444:             return 2;

445:         } else if (optionRatios < 2 ** 208) {

446:             return 3;

448:         return 4;

477:         if (i == 2)

483:         if (i == 3)

506:             uint256 chunkData = (TokenId.unwrap(self) & CHUNK_MASK) >> 64;

507:             for (uint256 i = 0; i < 4; ++i) {

512:                     if ((TokenId.unwrap(self) >> (64 + 48 * i)) != 0)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="NC-3"></a>[NC-3] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*Instances (142)*:
```solidity
File: contracts/CollateralTracker.sol

170:         if (msg.sender != address(s_panopticPool)) revert Errors.NotPanopticPool();

229:         if (s_initialized) revert Errors.CollateralTokenAlreadyInitialized();

331:         if (s_panopticPool.numberOfPositions(msg.sender) != 0) revert Errors.PositionCountNotZero();

350:         if (s_panopticPool.numberOfPositions(from) != 0) revert Errors.PositionCountNotZero();

418:         if (assets > type(uint104).max) revert Errors.DepositTooLarge();

480:         if (assets > type(uint104).max) revert Errors.DepositTooLarge();

519:         uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.

536:         if (assets > maxWithdraw(owner)) revert Errors.ExceedsMaximumRedemption();

544:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

596:         if (shares > maxRedeem(owner)) revert Errors.ExceedsMaximumRedemption();

602:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

664:                 if (positionId.isLong(leg) == 0) continue;

707:                 if (

856:           LIFECYCLE OF A COLLATERAL TOKEN AND DELEGATE/REVOKE LOGIC

1257:                 if (tokenId.tokenType(index) != (underlyingIsToken0 ? 0 : 1)) continue;

1345:                 if (

1373:                     if (

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

73:     SemiFungiblePositionManager internal immutable SFPM;

124:         SemiFungiblePositionManager _SFPM,

156:         if (_msgSender() != s_factoryOwner) revert Errors.NotOwner();

198:         if (amount0Owed > 0)

205:         if (amount1Owed > 0)

239:         if (address(v3Pool) == address(0)) revert Errors.UniswapPoolNotInitialized(); // the uniswap pool needs to exist

241:         if (address(s_getPanopticPool[v3Pool]) != address(0))

380:         if (deployer == address(0)) deployer = _msgSender();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

179:     SemiFungiblePositionManager internal immutable SFPM;

299:         if (address(s_univ3pool) != address(0)) revert Errors.PoolAlreadyInitialized();

533:         if (medianData != 0) s_miniMedian = medianData;

633:         if (tokenId.poolId() != SFPM.getPoolId(address(s_univ3pool)))

638:         if (LeftRightUnsigned.unwrap(s_positionBalance[msg.sender][tokenId]) != 0)

664:         if (medianData != 0) s_miniMedian = medianData;

940:         if (!solventAtFast) revert Errors.NotEnoughCollateral();

943:         if (Math.abs(int256(fastOracleTick) - slowOracleTick) > MAX_SLOW_FAST_DELTA)

944:             if (!_checkSolvencyAtTick(user, positionIdList, currentTick, slowOracleTick, buffer))

1035:             if (Math.abs(currentTick - twapTick) > MAX_TWAP_DELTA_LIQUIDATION)

1066:             if (balanceCross >= thresholdCross) revert Errors.NotMarginCalled();

1155:         if (

1188:         if (touchedId.length != 1) revert Errors.InputListFail();

1274:         if (positionIdListExercisor.length > 0)

1394:         if (fingerprintIncomingList != currentHash) revert Errors.InputListFail();

1415:         if ((newHash >> 248) > MAX_POSITIONS) revert Errors.TooManyPositionsOpen();

1483:         if (netLiquidity == 0) return;

1492:         if (effectiveLiquidityFactorX32 > uint256(effectiveLiquidityLimitX32))

1596:         if (tokenId.isLong(legIndex) == 0 || legIndex > 3) revert Errors.NotALongLeg();

1865:                     if (commitLongSettled)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

153:         We're tracking the amount of net and removed liquidity for the specific region:

182:         time, we call this the gross premia. If that liquidity has been removed, we also need to

205:         In addition to tracking, we also want to track those fees plus a small spread. Specifically,

282:         specific risk management profile of every smart contract. And simply setting the ν parameter

322:         if (s_poolContext[poolId].locked) revert Errors.ReentrantCall();

355:         if (univ3pool == address(0)) revert Errors.UniswapPoolNotInitialized();

362:         if (s_AddrToPoolIdData[univ3pool] != 0) return;

412:         if (amount0Owed > 0)

419:         if (amount1Owed > 0)

549:         if (s_poolContext[TokenId.wrap(id).poolId()].locked) revert Errors.ReentrantCall();

576:             if (s_poolContext[TokenId.wrap(ids[i]).poolId()].locked) revert Errors.ReentrantCall();

631:             if (

638:             if (LeftRightUnsigned.unwrap(fromLiq) != liquidityChunk.liquidity())

688:         if (positionSize == 0) revert Errors.OptionsBalanceZero();

702:         if (univ3pool == IUniswapV3Pool(address(0))) revert Errors.UniswapPoolNotInitialized();

727:         if ((currentTick >= tickLimitHigh) || (currentTick <= tickLimitLow))

833:             if (swapAmount == 0) return LeftRightSigned.wrap(0);

939:         if (amount0 > uint128(type(int128).max) || amount1 > uint128(type(int128).max))

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/CallbackLib.sol

36:         if (factory.getPool(features.token0, features.token1, features.fee) != sender)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/CallbackLib.sol)

```solidity
File: contracts/libraries/InteractionHelper.sol

25:         SemiFungiblePositionManager sfpm,

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/InteractionHelper.sol)

```solidity
File: contracts/libraries/Math.sol

131:             if (absTick > uint256(int256(Constants.MAX_V3POOL_TICK))) revert Errors.InvalidTick();

137:             if (absTick & 0x2 != 0) sqrtR = (sqrtR * 0xfff97272373d413259a46990580e213a) >> 128;

139:             if (absTick & 0x4 != 0) sqrtR = (sqrtR * 0xfff2e50f5f656932ef12357cf3c7fdcc) >> 128;

141:             if (absTick & 0x8 != 0) sqrtR = (sqrtR * 0xffe5caca7e10e4e61c3624eaa0941cd0) >> 128;

143:             if (absTick & 0x10 != 0) sqrtR = (sqrtR * 0xffcb9843d60f6159c9db58835c926644) >> 128;

145:             if (absTick & 0x20 != 0) sqrtR = (sqrtR * 0xff973b41fa98c081472e6896dfb254c0) >> 128;

147:             if (absTick & 0x40 != 0) sqrtR = (sqrtR * 0xff2ea16466c96a3843ec78b326b52861) >> 128;

149:             if (absTick & 0x80 != 0) sqrtR = (sqrtR * 0xfe5dee046a99a2a811c461f1969c3053) >> 128;

151:             if (absTick & 0x100 != 0) sqrtR = (sqrtR * 0xfcbe86c7900a88aedcffc83b479aa3a4) >> 128;

153:             if (absTick & 0x200 != 0) sqrtR = (sqrtR * 0xf987a7253ac413176f2b074cf7815e54) >> 128;

155:             if (absTick & 0x400 != 0) sqrtR = (sqrtR * 0xf3392b0822b70005940c7a398e4b70f3) >> 128;

157:             if (absTick & 0x800 != 0) sqrtR = (sqrtR * 0xe7159475a2c29b7443b29c7fa6e889d9) >> 128;

159:             if (absTick & 0x1000 != 0) sqrtR = (sqrtR * 0xd097f3bdfd2022b8845ad8f792aa5825) >> 128;

161:             if (absTick & 0x2000 != 0) sqrtR = (sqrtR * 0xa9f746462d870fdf8a65dc1f90e061e5) >> 128;

163:             if (absTick & 0x4000 != 0) sqrtR = (sqrtR * 0x70d869a156d2a1b890bb3df62baf32f7) >> 128;

165:             if (absTick & 0x8000 != 0) sqrtR = (sqrtR * 0x31be135f97d08fd981231505542fcfa6) >> 128;

167:             if (absTick & 0x10000 != 0) sqrtR = (sqrtR * 0x9aa508b5b7a84e1c677de54f3e99bc9) >> 128;

169:             if (absTick & 0x20000 != 0) sqrtR = (sqrtR * 0x5d6af8dedb81196699c329225ee604) >> 128;

171:             if (absTick & 0x40000 != 0) sqrtR = (sqrtR * 0x2216e584f5fa1ea926041bedfe98) >> 128;

173:             if (absTick & 0x80000 != 0) sqrtR = (sqrtR * 0x48a170391f7dc42444e8fa2) >> 128;

176:             if (tick > 0) sqrtR = type(uint256).max / sqrtR;

297:         if ((downcastedInt = uint128(toDowncast)) != toDowncast) revert Errors.CastingError();

312:         if ((downcastedInt = int128(toCast)) < 0) revert Errors.CastingError();

319:         if (!((downcastedInt = int128(toCast)) == toCast)) revert Errors.CastingError();

326:         if (toCast > uint256(type(int256).max)) revert Errors.CastingError();

351:             uint256 prod0; // Least significant 256 bits of the product

352:             uint256 prod1; // Most significant 256 bits of the product

465:             uint256 prod0; // Least significant 256 bits of the product

466:             uint256 prod1; // Most significant 256 bits of the product

528:             uint256 prod0; // Least significant 256 bits of the product

529:             uint256 prod1; // Most significant 256 bits of the product

605:             uint256 prod0; // Least significant 256 bits of the product

606:             uint256 prod1; // Most significant 256 bits of the product

682:             uint256 prod0; // Least significant 256 bits of the product

683:             uint256 prod1; // Most significant 256 bits of the product

757:             if (i == j) return;

768:             if (left < j) quickSort(arr, left, j);

769:             if (i < right) quickSort(arr, i, right);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

77:             return addr == address(0) ? 40 : 39 - Math.mostSignificantNibble(uint160(addr));

203:                 uint24 shift = 1;

212:                         shift -= 1;

219:                         shift += 1;

223:                     newOrderMap = newOrderMap + ((rank + 1) << (3 * (i + shift - 1)));

356:             if (

479:             if (notional == 0 || notional > type(uint128).max) revert Errors.InvalidNotionalValue();

797:             if (

821:             } else if (

856:                 if (haircut0 != 0) collateral0.exercise(_liquidatee, 0, 0, 0, int128(haircut0));

857:                 if (haircut1 != 0) collateral1.exercise(_liquidatee, 0, 0, 0, int128(haircut1));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/libraries/SafeTransferLib.sol

45:         if (!success) revert Errors.TransferFailed();

75:         if (!success) revert Errors.TransferFailed();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/SafeTransferLib.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

101:         if (!(msg.sender == from || isApprovedForAll[from][msg.sender])) revert NotAuthorized();

113:             if (

137:         if (!(msg.sender == from || isApprovedForAll[from][msg.sender])) revert NotAuthorized();

164:             if (

223:             if (

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/tokens/ERC20Minimal.sol

84:         if (allowed != type(uint256).max) allowance[from][msg.sender] = allowed - amount;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC20Minimal.sol)

```solidity
File: contracts/types/LeftRight.sol

160:             if (

183:             if (

199:             if (left128 != left) revert Errors.UnderOverFlow();

204:             if (right128 != right) revert Errors.UnderOverFlow();

222:             if (left128 != left256 || right128 != right256) revert Errors.UnderOverFlow();

240:             if (left128 != left256 || right128 != right256) revert Errors.UnderOverFlow();

262:             if (left128 != left256 || right128 != right256) revert Errors.UnderOverFlow();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LeftRight.sol)

```solidity
File: contracts/types/TokenId.sol

465:         if (i == 0)

471:         if (i == 1)

477:         if (i == 2)

483:         if (i == 3)

501:         if (self.optionRatio(0) == 0) revert Errors.InvalidTokenIdParameter(1);

512:                     if ((TokenId.unwrap(self) >> (64 + 48 * i)) != 0)

528:                 if ((self.width(i) == 0)) revert Errors.InvalidTokenIdParameter(5);

530:                 if (

541:                     if (self.riskPartner(riskPartnerIndex) != i)

545:                     if (

560:                     if ((_isLong == isLongP) && (_tokenType == tokenTypeP))

566:                     if (((_isLong != isLongP) || _isLong == 1) && (_tokenType != tokenTypeP))

592:                     if (self.isLong(i) == 1) return; // validated

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="NC-4"></a>[NC-4] Critical Changes Should Use Two-step Procedure
The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference: <https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure>

**Recommended Mitigation Steps**

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

*Instances (1)*:
```solidity
File: contracts/PanopticFactory.sol

163:     function setOwner(address newOwner) external nonReentrant onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

### <a name="NC-5"></a>[NC-5] Functions should not be longer than 50 lines
Overly complex code can make understanding functionality more difficult, try to further modularize your code to ensure readability 

*Instances (119)*:
```solidity
File: contracts/CollateralTracker.sol

289:     function name() external view returns (string memory) {

303:     function symbol() external view returns (string memory) {

310:     function decimals() external view returns (uint8) {

361:     function asset() external view returns (address assetTokenAddress) {

370:     function totalAssets() public view returns (uint256 totalManagedAssets) {

379:     function convertToShares(uint256 assets) public view returns (uint256 shares) {

386:     function convertToAssets(uint256 shares) public view returns (uint256 assets) {

392:     function maxDeposit(address) external pure returns (uint256 maxAssets) {

399:     function previewDeposit(uint256 assets) public view returns (uint256 shares) {

417:     function deposit(uint256 assets, address receiver) external returns (uint256 shares) {

444:     function maxMint(address) external view returns (uint256 maxShares) {

453:     function previewMint(uint256 shares) public view returns (uint256 assets) {

477:     function mint(uint256 shares, address receiver) external returns (uint256 assets) {

507:     function maxWithdraw(address owner) public view returns (uint256 maxAssets) {

518:     function previewWithdraw(uint256 assets) public view returns (uint256 shares) {

572:     function maxRedeem(address owner) public view returns (uint256 maxShares) {

581:     function previewRedeem(uint256 shares) public view returns (uint256 assets) {

741:     function _poolUtilization() internal view returns (int256 poolUtilization) {

894:     function delegate(address delegatee, uint256 assets) external onlyPanopticPool {

903:     function refund(address delegatee, uint256 assets) external onlyPanopticPool {

975:     function refund(address refunder, address refundee, int256 assets) external onlyPanopticPool {

1245:     function _getRequiredCollateralAtTickSinglePosition(

1311:     function _getRequiredCollateralSingleLegNoPartner(

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

163:     function setOwner(address newOwner) external nonReentrant onlyOwner {

174:     function factoryOwner() external view returns (address) {

498:     function _issueNFTToDonor() internal returns (uint256 tokenId) {

544:     function getPanopticPool(IUniswapV3Pool univ3pool) external view returns (PanopticPool) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

338:     function assertPriceWithinBounds(uint160 sqrtLowerBound, uint160 sqrtUpperBound) external view {

739:     function _addUserOption(TokenId tokenId, uint64 effectiveLiquidityLimitX32) internal {

859:     function _updatePositionDataBurn(address owner, TokenId tokenId) internal {

1405:     function _updatePositionsHash(address account, TokenId tokenId, bool addFlag) internal {

1425:     function univ3pool() external view returns (IUniswapV3Pool) {

1431:     function collateralToken0() external view returns (CollateralTracker collateralToken) {

1437:     function collateralToken1() external view returns (CollateralTracker) {

1444:     function numberOfPositions(address user) public view returns (uint256 _numberOfPositions) {

1450:     function getUniV3TWAP() internal view returns (int24 twapTick) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

320:     function beginReentrancyLock(uint64 poolId) internal {

330:     function endReentrancyLock(uint64 poolId) internal {

350:     function initializeAMMPool(address token0, address token1, uint24 fee) external {

593:     function registerTokenTransfer(address from, address to, TokenId id, uint256 amount) internal {

1566:     function getPoolId(address univ3pool) external view returns (uint64 poolId) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/InteractionHelper.sol

107:     function computeDecimals(address token) external view returns (uint8) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/InteractionHelper.sol)

```solidity
File: contracts/libraries/Math.sol

25:     function min24(int24 a, int24 b) internal pure returns (int24) {

33:     function max24(int24 a, int24 b) internal pure returns (int24) {

41:     function min(uint256 a, uint256 b) internal pure returns (uint256) {

49:     function min(int256 a, int256 b) internal pure returns (int256) {

57:     function max(uint256 a, uint256 b) internal pure returns (uint256) {

65:     function max(int256 a, int256 b) internal pure returns (int256) {

73:     function abs(int256 x) internal pure returns (int256) {

81:     function absUint(int256 x) internal pure returns (uint256) {

91:     function mostSignificantNibble(uint160 x) internal pure returns (uint256 r) {

128:     function getSqrtRatioAtTick(int24 tick) internal pure returns (uint160) {

191:     function getAmount0ForLiquidity(LiquidityChunk liquidityChunk) internal pure returns (uint256) {

207:     function getAmount1ForLiquidity(LiquidityChunk liquidityChunk) internal pure returns (uint256) {

296:     function toUint128(uint256 toDowncast) internal pure returns (uint128 downcastedInt) {

302:     function toUint128Capped(uint256 toDowncast) internal pure returns (uint128 downcastedInt) {

311:     function toInt128(uint128 toCast) internal pure returns (int128 downcastedInt) {

318:     function toInt128(int256 toCast) internal pure returns (int128 downcastedInt) {

325:     function toInt256(uint256 toCast) internal pure returns (int256) {

458:     function mulDiv64(uint256 a, uint256 b) internal pure returns (uint256) {

521:     function mulDiv96(uint256 a, uint256 b) internal pure returns (uint256) {

584:     function mulDiv96RoundingUp(uint256 a, uint256 b) internal pure returns (uint256 result) {

598:     function mulDiv128(uint256 a, uint256 b) internal pure returns (uint256) {

661:     function mulDiv128RoundingUp(uint256 a, uint256 b) internal pure returns (uint256 result) {

675:     function mulDiv192(uint256 a, uint256 b) internal pure returns (uint256) {

738:     function unsafeDivRoundingUp(uint256 a, uint256 b) internal pure returns (uint256 result) {

753:     function quickSort(int256[] memory arr, int256 left, int256 right) internal pure {

776:     function sort(int256[] memory data) internal pure returns (int256[] memory) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

47:     function getPoolId(address univ3pool) internal view returns (uint64) {

59:     function incrementPoolPattern(uint64 poolId) internal pure returns (uint64) {

75:     function numberOfLeadingHexZeros(address addr) external pure returns (uint256) {

241:     function twapFilter(IUniswapV3Pool univ3pool, uint32 twapWindow) external view returns (int24) {

490:     function convert0to1(uint256 amount, uint160 sqrtPriceX96) internal pure returns (uint256) {

507:     function convert1to0(uint256 amount, uint160 sqrtPriceX96) internal pure returns (uint256) {

524:     function convert0to1(int256 amount, uint160 sqrtPriceX96) internal pure returns (int256) {

547:     function convert1to0(int256 amount, uint160 sqrtPriceX96) internal pure returns (int256) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/libraries/SafeTransferLib.sol

21:     function safeTransferFrom(address token, address from, address to, uint256 amount) internal {

52:     function safeTransfer(address token, address to, uint256 amount) internal {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/SafeTransferLib.sol)

```solidity
File: contracts/multicall/Multicall.sol

12:     function multicall(bytes[] calldata data) public payable returns (bytes[] memory results) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/multicall/Multicall.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

81:     function setApprovalForAll(address operator, bool approved) public {

200:     function supportsInterface(bytes4 interfaceId) public pure returns (bool) {

214:     function _mint(address to, uint256 id, uint256 amount) internal {

236:     function _burn(address from, uint256 id, uint256 amount) internal {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/tokens/ERC20Minimal.sol

49:     function approve(address spender, uint256 amount) public returns (bool) {

61:     function transfer(address to, uint256 amount) public virtual returns (bool) {

81:     function transferFrom(address from, address to, uint256 amount) public virtual returns (bool) {

103:     function _transferFrom(address from, address to, uint256 amount) internal {

122:     function _mint(address to, uint256 amount) internal {

136:     function _burn(address from, uint256 amount) internal {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC20Minimal.sol)

```solidity
File: contracts/tokens/interfaces/IERC20Partial.sol

16:     function balanceOf(address account) external view returns (uint256);

22:     function approve(address spender, uint256 amount) external;

27:     function transfer(address to, uint256 amount) external;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/interfaces/IERC20Partial.sol)

```solidity
File: contracts/types/LeftRight.sol

39:     function rightSlot(LeftRightUnsigned self) internal pure returns (uint128) {

46:     function rightSlot(LeftRightSigned self) internal pure returns (int128) {

101:     function leftSlot(LeftRightUnsigned self) internal pure returns (uint128) {

108:     function leftSlot(LeftRightSigned self) internal pure returns (int128) {

134:     function toLeftSlot(LeftRightSigned self, int128 left) internal pure returns (LeftRightSigned) {

194:     function add(LeftRightUnsigned x, LeftRightSigned y) internal pure returns (LeftRightSigned z) {

214:     function add(LeftRightSigned x, LeftRightSigned y) internal pure returns (LeftRightSigned z) {

232:     function sub(LeftRightSigned x, LeftRightSigned y) internal pure returns (LeftRightSigned z) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LeftRight.sol)

```solidity
File: contracts/types/LiquidityChunk.sol

171:     function tickLower(LiquidityChunk self) internal pure returns (int24) {

180:     function tickUpper(LiquidityChunk self) internal pure returns (int24) {

189:     function liquidity(LiquidityChunk self) internal pure returns (uint128) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LiquidityChunk.sol)

```solidity
File: contracts/types/TokenId.sol

87:     function poolId(TokenId self) internal pure returns (uint64) {

96:     function tickSpacing(TokenId self) internal pure returns (int24) {

108:     function asset(TokenId self, uint256 legIndex) internal pure returns (uint256) {

118:     function optionRatio(TokenId self, uint256 legIndex) internal pure returns (uint256) {

128:     function isLong(TokenId self, uint256 legIndex) internal pure returns (uint256) {

138:     function tokenType(TokenId self, uint256 legIndex) internal pure returns (uint256) {

148:     function riskPartner(TokenId self, uint256 legIndex) internal pure returns (uint256) {

158:     function strike(TokenId self, uint256 legIndex) internal pure returns (int24) {

169:     function width(TokenId self, uint256 legIndex) internal pure returns (int24) {

183:     function addPoolId(TokenId self, uint64 _poolId) internal pure returns (TokenId) {

193:     function addTickSpacing(TokenId self, int24 _tickSpacing) internal pure returns (TokenId) {

366:     function flipToBurnToken(TokenId self) internal pure returns (TokenId) {

404:     function countLongs(TokenId self) internal pure returns (uint256) {

432:     function countLegs(TokenId self) internal pure returns (uint256) {

464:     function clearLeg(TokenId self, uint256 i) internal pure returns (TokenId) {

578:     function validateIsExercisable(TokenId self, int24 currentTick) internal pure {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="NC-6"></a>[NC-6] Change int to int256
Throughout the code base, some variables are declared as `int`. To favor explicitness, consider changing all instances of `int` to `int256`

*Instances (11)*:
```solidity
File: contracts/CollateralTracker.sol

1020:                 uint256 sharesToMint = convertToShares(uint256(-tokenToPay));

1077:                 uint256 sharesToMint = convertToShares(uint256(-tokenToPay));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

311:         Mint NFT according to its rarity.

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

125:     bool internal constant MINT = false;

1049:                 Selling(isLong=0): Mint chunk of liquidity in Uniswap (defined by upper tick, lower tick, and amount)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Math.sol

297:         if ((downcastedInt = uint128(toDowncast)) != toDowncast) revert Errors.CastingError();

303:         if ((downcastedInt = uint128(toDowncast)) != toDowncast) {

304:             downcastedInt = type(uint128).max;

312:         if ((downcastedInt = int128(toCast)) < 0) revert Errors.CastingError();

319:         if (!((downcastedInt = int128(toCast)) == toCast)) revert Errors.CastingError();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/types/LeftRight.sol

26:     int256 internal constant LEFT_HALF_BIT_MASK_INT =

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LeftRight.sol)

### <a name="NC-7"></a>[NC-7] Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*Instances (1)*:
```solidity
File: contracts/CollateralTracker.sol

851:                     (SATURATED_POOL_UTIL - TARGET_POOL_UTIL)) / 2; // do the division by 2 at the end after all addition and multiplication; b/c y1 = buyCollateralRatio / 2

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

### <a name="NC-8"></a>[NC-8] `type(uint256).max` should be used instead of `2 ** 256 - 1`

*Instances (2)*:
```solidity
File: contracts/libraries/Math.sol

15:     uint256 internal constant MAX_UINT256 = 2 ** 256 - 1;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

23:     uint256 internal constant MAX_UINT256 = 2 ** 256 - 1;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="NC-9"></a>[NC-9] Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor
If a function is supposed to be access-controlled, a `modifier` should be used instead of a `require/if` statement for more readability.

*Instances (10)*:
```solidity
File: contracts/CollateralTracker.sol

170:         if (msg.sender != address(s_panopticPool)) revert Errors.NotPanopticPool();

331:         if (s_panopticPool.numberOfPositions(msg.sender) != 0) revert Errors.PositionCountNotZero();

541:         if (msg.sender != owner) {

544:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

599:         if (msg.sender != owner) {

602:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

638:         if (LeftRightUnsigned.unwrap(s_positionBalance[msg.sender][tokenId]) != 0)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

101:         if (!(msg.sender == from || isApprovedForAll[from][msg.sender])) revert NotAuthorized();

137:         if (!(msg.sender == from || isApprovedForAll[from][msg.sender])) revert NotAuthorized();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/tokens/ERC20Minimal.sol

84:         if (allowed != type(uint256).max) allowance[from][msg.sender] = allowed - amount;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC20Minimal.sol)

### <a name="NC-10"></a>[NC-10] Take advantage of Custom Error's return value property
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

*Instances (58)*:
```solidity
File: contracts/CollateralTracker.sol

170:         if (msg.sender != address(s_panopticPool)) revert Errors.NotPanopticPool();

229:         if (s_initialized) revert Errors.CollateralTokenAlreadyInitialized();

331:         if (s_panopticPool.numberOfPositions(msg.sender) != 0) revert Errors.PositionCountNotZero();

350:         if (s_panopticPool.numberOfPositions(from) != 0) revert Errors.PositionCountNotZero();

418:         if (assets > type(uint104).max) revert Errors.DepositTooLarge();

480:         if (assets > type(uint104).max) revert Errors.DepositTooLarge();

536:         if (assets > maxWithdraw(owner)) revert Errors.ExceedsMaximumRedemption();

596:         if (shares > maxRedeem(owner)) revert Errors.ExceedsMaximumRedemption();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

156:         if (_msgSender() != s_factoryOwner) revert Errors.NotOwner();

239:         if (address(v3Pool) == address(0)) revert Errors.UniswapPoolNotInitialized(); // the uniswap pool needs to exist

242:             revert Errors.PoolAlreadyInitialized();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

299:         if (address(s_univ3pool) != address(0)) revert Errors.PoolAlreadyInitialized();

342:             revert Errors.PriceBoundFail();

639:             revert Errors.PositionAlreadyMinted();

940:         if (!solventAtFast) revert Errors.NotEnoughCollateral();

945:                 revert Errors.NotEnoughCollateral();

1036:                 revert Errors.StaleTWAP();

1066:             if (balanceCross >= thresholdCross) revert Errors.NotMarginCalled();

1163:         ) revert Errors.NotEnoughCollateral();

1188:         if (touchedId.length != 1) revert Errors.InputListFail();

1394:         if (fingerprintIncomingList != currentHash) revert Errors.InputListFail();

1415:         if ((newHash >> 248) > MAX_POSITIONS) revert Errors.TooManyPositionsOpen();

1493:             revert Errors.EffectiveLiquidityAboveThreshold();

1596:         if (tokenId.isLong(legIndex) == 0 || legIndex > 3) revert Errors.NotALongLeg();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

322:         if (s_poolContext[poolId].locked) revert Errors.ReentrantCall();

355:         if (univ3pool == address(0)) revert Errors.UniswapPoolNotInitialized();

549:         if (s_poolContext[TokenId.wrap(id).poolId()].locked) revert Errors.ReentrantCall();

576:             if (s_poolContext[TokenId.wrap(ids[i]).poolId()].locked) revert Errors.ReentrantCall();

634:             ) revert Errors.TransferFailed();

639:                 revert Errors.TransferFailed();

688:         if (positionSize == 0) revert Errors.OptionsBalanceZero();

702:         if (univ3pool == IUniswapV3Pool(address(0))) revert Errors.UniswapPoolNotInitialized();

728:             revert Errors.PriceBoundFail();

940:             revert Errors.PositionTooLarge();

1018:                     revert Errors.NotEnoughLiquidity();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/CallbackLib.sol

37:             revert Errors.InvalidUniswapCallback();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/CallbackLib.sol)

```solidity
File: contracts/libraries/Math.sol

131:             if (absTick > uint256(int256(Constants.MAX_V3POOL_TICK))) revert Errors.InvalidTick();

297:         if ((downcastedInt = uint128(toDowncast)) != toDowncast) revert Errors.CastingError();

312:         if ((downcastedInt = int128(toCast)) < 0) revert Errors.CastingError();

319:         if (!((downcastedInt = int128(toCast)) == toCast)) revert Errors.CastingError();

326:         if (toCast > uint256(type(int256).max)) revert Errors.CastingError();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

361:             ) revert Errors.TicksNotInitializable();

479:             if (notional == 0 || notional > type(uint128).max) revert Errors.InvalidNotionalValue();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/libraries/SafeTransferLib.sol

45:         if (!success) revert Errors.TransferFailed();

75:         if (!success) revert Errors.TransferFailed();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/SafeTransferLib.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

101:         if (!(msg.sender == from || isApprovedForAll[from][msg.sender])) revert NotAuthorized();

117:                 revert UnsafeRecipient();

137:         if (!(msg.sender == from || isApprovedForAll[from][msg.sender])) revert NotAuthorized();

168:                 revert UnsafeRecipient();

227:                 revert UnsafeRecipient();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/types/LeftRight.sol

163:             ) revert Errors.UnderOverFlow();

186:             ) revert Errors.UnderOverFlow();

199:             if (left128 != left) revert Errors.UnderOverFlow();

204:             if (right128 != right) revert Errors.UnderOverFlow();

222:             if (left128 != left256 || right128 != right256) revert Errors.UnderOverFlow();

240:             if (left128 != left256 || right128 != right256) revert Errors.UnderOverFlow();

262:             if (left128 != left256 || right128 != right256) revert Errors.UnderOverFlow();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/LeftRight.sol)

```solidity
File: contracts/types/TokenId.sol

598:         revert Errors.NoLegsExercisable();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="NC-11"></a>[NC-11] Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While this won't save gas in the recent solidity versions, this is shorter and more readable (this is especially true in calculations).

*Instances (1)*:
```solidity
File: contracts/CollateralTracker.sol

234:         totalSupply = 10 ** 6;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

### <a name="NC-12"></a>[NC-12] Strings should use double quotes rather than single quotes
See the Solidity Style Guide: https://docs.soliditylang.org/en/v0.8.20/style-guide.html#other-recommendations

*Instances (2)*:
```solidity
File: contracts/CollateralTracker.sol

874:                      │(1) convert 'assets' to shares (this ERC20 contract)

921:                      │(1) convert 'assets' to shares (this ERC20 contract)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

### <a name="NC-13"></a>[NC-13] Use Underscores for Number Literals (add an underscore every 3 digits)

*Instances (9)*:
```solidity
File: contracts/CollateralTracker.sol

200:             int256 ratioTick = (int256(_sellerCollateralRatio) - 2000);

202:                 2230 +

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

160:     int256 internal constant MAX_SLOW_FAST_DELTA = 1800;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/libraries/Constants.sol

15:     int24 internal constant MAX_V3POOL_TICK = 887272;

18:     uint160 internal constant MIN_V3POOL_SQRT_RATIO = 4295128739;

22:         1461446703485210103287273052203988822378723970342;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Constants.sol)

```solidity
File: contracts/types/TokenId.sol

171:             return int24(int256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 36)) % 4096));

172:         } // "% 4096" = take last (2 ** 12 = 4096) 12 bits

320:                         (uint256(uint24(_width) % 4096) << (64 + legIndex * 48 + 36))

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="NC-14"></a>[NC-14] Constants should be defined rather than using magic numbers

*Instances (33)*:
```solidity
File: contracts/CollateralTracker.sol

203:                     (12500 * ratioTick) /

205:                     (7812 * ratioTick ** 2) /

207:                     (6510 * ratioTick ** 3) /

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/libraries/Math.sol

478:                     res := shr(64, prod0)

505:                 prod0 := shr(64, prod0)

541:                     res := shr(96, prod0)

568:                 prod0 := shr(96, prod0)

695:                     res := shr(192, prod0)

722:                 prod0 := shr(192, prod0)

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

178:                 (int24(uint24(medianData >> ((uint24(medianData >> (192 + 3 * 3)) % 8) * 24))) +

179:                     int24(uint24(medianData >> ((uint24(medianData >> (192 + 3 * 4)) % 8) * 24)))) /

242:         uint32[] memory secondsAgos = new uint32[](20);

244:         int256[] memory twapMeasurement = new int256[](19);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/libraries/SafeTransferLib.sol

31:             mstore(add(36, p), to) // Append the "to" argument.

32:             mstore(add(68, p), amount) // Append the "amount" argument.

62:             mstore(add(36, p), amount) // Append the "amount" argument.

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/SafeTransferLib.sol)

```solidity
File: contracts/types/TokenId.sol

110:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48)) % 2);

120:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 1)) % 128);

130:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 8)) % 2);

140:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 9)) % 2);

150:             return uint256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 10)) % 4);

160:             return int24(int256(TokenId.unwrap(self) >> (64 + legIndex * 48 + 12)));

171:             return int24(int256((TokenId.unwrap(self) >> (64 + legIndex * 48 + 36)) % 4096));

212:                 TokenId.wrap(TokenId.unwrap(self) + (uint256(_asset % 2) << (64 + legIndex * 48)));

229:                     TokenId.unwrap(self) + (uint256(_optionRatio % 128) << (64 + legIndex * 48 + 1))

246:             return TokenId.wrap(TokenId.unwrap(self) + ((_isLong % 2) << (64 + legIndex * 48 + 8)));

263:                     TokenId.unwrap(self) + (uint256(_tokenType % 2) << (64 + legIndex * 48 + 9))

281:                     TokenId.unwrap(self) + (uint256(_riskPartner % 4) << (64 + legIndex * 48 + 10))

300:                         uint256((int256(_strike) & BITMASK_INT24) << (64 + legIndex * 48 + 12))

320:                         (uint256(uint24(_width) % 4096) << (64 + legIndex * 48 + 36))

395:                         ((LONG_MASK >> (48 * (4 - optionRatios))) & CLEAR_POOLID_MASK)

512:                     if ((TokenId.unwrap(self) >> (64 + 48 * i)) != 0)

521:                     if (uint48(chunkData >> (48 * i)) == uint48(chunkData >> (48 * j))) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)

### <a name="NC-15"></a>[NC-15] Variables need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.

*Instances (31)*:
```solidity
File: contracts/CollateralTracker.sol

662:             for (uint256 leg = 0; leg < positionId.countLegs(); ++leg) {

1208:         for (uint256 i = 0; i < totalIterations; ) {

1255:             for (uint256 index = 0; index < numLegs; ++index) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

441:         for (uint256 k = 0; k < pLength; ) {

459:             for (uint256 leg = 0; leg < numLegs; ) {

745:         for (uint256 leg = 0; leg < numLegs; ) {

802:         for (uint256 i = 0; i < positionIdList.length; ) {

864:         for (uint256 leg = 0; leg < numLegs; ) {

1382:         for (uint256 i = 0; i < pLength; ) {

1518:         for (uint256 leg = 0; leg < numLegs; ) {

1672:         for (uint256 leg = 0; leg < numLegs; ++leg) {

1852:         for (uint256 leg = 0; leg < numLegs; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

575:         for (uint256 i = 0; i < ids.length; ) {

601:         for (uint256 leg = 0; leg < numLegs; ) {

882:         for (uint256 leg = 0; leg < numLegs; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/FeesCalc.sol

51:         for (uint256 k = 0; k < positionIdList.length; ) {

55:             for (uint256 leg = 0; leg < numLegs; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/FeesCalc.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

137:             for (uint256 i = 0; i < cardinality + 1; ++i) {

148:             for (uint256 i = 0; i < cardinality; ++i) {

248:             for (uint256 i = 0; i < 20; ++i) {

256:             for (uint256 i = 0; i < 19; ++i) {

395:         for (uint256 leg = 0; leg < numLegs; ) {

781:             for (uint256 i = 0; i < positionIdList.length; ++i) {

784:                 for (uint256 leg = 0; leg < numLegs; ++leg) {

860:             for (uint256 i = 0; i < positionIdList.length; i++) {

863:                 for (uint256 leg = 0; leg < tokenId.countLegs(); ++leg) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

```solidity
File: contracts/multicall/Multicall.sol

14:         for (uint256 i = 0; i < data.length; ) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/multicall/Multicall.sol)

```solidity
File: contracts/tokens/ERC1155Minimal.sol

143:         for (uint256 i = 0; i < ids.length; ) {

187:             for (uint256 i = 0; i < owners.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/tokens/ERC1155Minimal.sol)

```solidity
File: contracts/types/TokenId.sol

507:             for (uint256 i = 0; i < 4; ++i) {

581:             for (uint256 i = 0; i < numLegs; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/types/TokenId.sol)


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | `approve()`/`safeApprove()` may revert if the current approval is not zero | 4 |
| [L-2](#L-2) | `decimals()` is not a part of the ERC-20 standard | 1 |
| [L-3](#L-3) | Division by zero not prevented | 19 |
| [L-4](#L-4) | Possible rounding issue | 6 |
| [L-5](#L-5) | Loss of precision | 12 |
| [L-6](#L-6) | `symbol()` is not a part of the ERC-20 standard | 3 |
| [L-7](#L-7) | Unsafe ERC20 operation(s) | 6 |
| [L-8](#L-8) | Upgradeable contract not initialized | 15 |
### <a name="L-1"></a>[L-1] `approve()`/`safeApprove()` may revert if the current approval is not zero
- Some tokens (like the *very popular* USDT) do not work when changing the allowance from an existing non-zero allowance value (it will revert if the current approval is not zero to protect against front-running changes of approvals). These tokens must first be approved for zero and then the actual allowance can be approved.
- Furthermore, OZ's implementation of safeApprove would throw an error if an approve is attempted from a non-zero value (`"SafeERC20: approve from non-zero to non-zero allowance"`)

Set the allowance to zero immediately before each of the existing allowance calls

*Instances (4)*:
```solidity
File: contracts/libraries/InteractionHelper.sol

32:         IERC20Partial(token0).approve(address(sfpm), type(uint256).max);

33:         IERC20Partial(token1).approve(address(sfpm), type(uint256).max);

36:         IERC20Partial(token0).approve(address(ct0), type(uint256).max);

37:         IERC20Partial(token1).approve(address(ct1), type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/InteractionHelper.sol)

### <a name="L-2"></a>[L-2] `decimals()` is not a part of the ERC-20 standard
The `decimals()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*Instances (1)*:
```solidity
File: contracts/libraries/InteractionHelper.sol

110:         try IERC20Metadata(token).decimals() returns (uint8 _decimals) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/InteractionHelper.sol)

### <a name="L-3"></a>[L-3] Division by zero not prevented
The divisions below take an input parameter which does not have any zero-value checks, which may lead to the functions reverting when zero is passed.

*Instances (19)*:
```solidity
File: contracts/CollateralTracker.sol

446:             return (convertToShares(type(uint104).max) * DECIMALS) / (DECIMALS + COMMISSION_FEE);

677:                         uint256(Math.abs(currentTick - positionId.strike(leg)) / range)

743:             return int256((s_inAMM * DECIMALS) / totalAssets());

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

489:                 (Constants.MIN_V3POOL_TICK / tickSpacing) * tickSpacing,

490:                 (Constants.MAX_V3POOL_TICK / tickSpacing) * tickSpacing,

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

1487:             effectiveLiquidityFactorX32 = (uint256(totalLiquidity) * 2 ** 32) / netLiquidity;

1732:                                     totalLiquidityBefore) / (totalLiquidity)

1740:                                     totalLiquidityBefore) / (totalLiquidity)

1945:                                         ) / totalLiquidity

1962:                                         ) / totalLiquidity

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

228:               s_accountPremiumOwed += feeGrowthX128 * R * (1 + ν*R/N) / R

271:                                 = ∆feeGrowthX128 * t * (T - R + ν*R^2/T) / N 

272:                                 = ∆feeGrowthX128 * t * (N + ν*R^2/T) / N

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Math.sol

176:             if (tick > 0) sqrtR = type(uint256).max / sqrtR;

200:                 ) / lowPriceX96;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Math.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

258:                     (tickCumulatives[i] - tickCumulatives[i + 1]) / int56(uint56(twapWindow / 20))

346:             int24 minTick = (Constants.MIN_V3POOL_TICK / tickSpacing) * tickSpacing;

347:             int24 maxTick = (Constants.MAX_V3POOL_TICK / tickSpacing) * tickSpacing;

669:                 uint256 requiredRatioX128 = (required0 << 128) / (required0 + required1);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="L-4"></a>[L-4] Possible rounding issue
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator. Also, there is indication of multiplication and division without the use of parenthesis which could result in issues.

*Instances (6)*:
```solidity
File: contracts/CollateralTracker.sol

743:             return int256((s_inAMM * DECIMALS) / totalAssets());

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

1732:                                     totalLiquidityBefore) / (totalLiquidity)

1740:                                     totalLiquidityBefore) / (totalLiquidity)

1945:                                         ) / totalLiquidity

1962:                                         ) / totalLiquidity

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

678:                 uint256 bonusCross = Math.min(balanceCross / 2, thresholdCross - balanceCross);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="L-5"></a>[L-5] Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*Instances (12)*:
```solidity
File: contracts/CollateralTracker.sol

262:             s_ITMSpreadFee = uint128((ITM_SPREAD_MULTIPLIER * _poolFee) / DECIMALS);

446:             return (convertToShares(type(uint104).max) * DECIMALS) / (DECIMALS + COMMISSION_FEE);

731:                 .toRightSlot(int128((longAmounts.rightSlot() * fee) / DECIMALS_128))

732:                 .toLeftSlot(int128((longAmounts.leftSlot() * fee) / DECIMALS_128));

743:             return int256((s_inAMM * DECIMALS) / totalAssets());

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticPool.sol

1732:                                     totalLiquidityBefore) / (totalLiquidity)

1740:                                     totalLiquidityBefore) / (totalLiquidity)

1945:                                         ) / totalLiquidity

1962:                                         ) / totalLiquidity

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

1367:                     uint256 numerator = netLiquidity + (removedLiquidity / 2 ** VEGOID);

1391:                         ((removedLiquidity ** 2) / 2 ** (VEGOID));

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/PanopticMath.sol

678:                 uint256 bonusCross = Math.min(balanceCross / 2, thresholdCross - balanceCross);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/PanopticMath.sol)

### <a name="L-6"></a>[L-6] `symbol()` is not a part of the ERC-20 standard
The `symbol()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*Instances (3)*:
```solidity
File: contracts/libraries/InteractionHelper.sol

60:         try IERC20Metadata(token0).symbol() returns (string memory _symbol) {

65:         try IERC20Metadata(token1).symbol() returns (string memory _symbol) {

97:         try IERC20Metadata(token).symbol() returns (string memory tokenSymbol) {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/InteractionHelper.sol)

### <a name="L-7"></a>[L-7] Unsafe ERC20 operation(s)

*Instances (6)*:
```solidity
File: contracts/CollateralTracker.sol

333:         return ERC20Minimal.transfer(recipient, amount);

352:         return ERC20Minimal.transferFrom(from, to, amount);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/libraries/InteractionHelper.sol

32:         IERC20Partial(token0).approve(address(sfpm), type(uint256).max);

33:         IERC20Partial(token1).approve(address(sfpm), type(uint256).max);

36:         IERC20Partial(token0).approve(address(ct0), type(uint256).max);

37:         IERC20Partial(token1).approve(address(ct1), type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/InteractionHelper.sol)

### <a name="L-8"></a>[L-8] Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*Instances (15)*:
```solidity
File: contracts/CollateralTracker.sol

93:     bool internal s_initialized;

229:         if (s_initialized) revert Errors.CollateralTokenAlreadyInitialized();

230:         s_initialized = true;

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/CollateralTracker.sol)

```solidity
File: contracts/PanopticFactory.sol

239:         if (address(v3Pool) == address(0)) revert Errors.UniswapPoolNotInitialized(); // the uniswap pool needs to exist

242:             revert Errors.PoolAlreadyInitialized();

278:         SFPM.initializeAMMPool(token0, token1, fee);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

```solidity
File: contracts/PanopticPool.sol

299:         if (address(s_univ3pool) != address(0)) revert Errors.PoolAlreadyInitialized();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticPool.sol)

```solidity
File: contracts/SemiFungiblePositionManager.sol

80:     event PoolInitialized(address indexed uniswapPool, uint64 poolId);

350:     function initializeAMMPool(address token0, address token1, uint24 fee) external {

355:         if (univ3pool == address(0)) revert Errors.UniswapPoolNotInitialized();

390:         emit PoolInitialized(univ3pool, poolId);

702:         if (univ3pool == IUniswapV3Pool(address(0))) revert Errors.UniswapPoolNotInitialized();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)

```solidity
File: contracts/libraries/Errors.sol

13:     error CollateralTokenAlreadyInitialized();

76:     error PoolAlreadyInitialized();

108:     error UniswapPoolNotInitialized();

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/libraries/Errors.sol)


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Centralization Risk for trusted owners | 1 |
| [M-2](#M-2) | `_safeMint()` should be used rather than `_mint()` wherever possible | 1 |
### <a name="M-1"></a>[M-1] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (1)*:
```solidity
File: contracts/PanopticFactory.sol

163:     function setOwner(address newOwner) external nonReentrant onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/PanopticFactory.sol)

### <a name="M-2"></a>[M-2] `_safeMint()` should be used rather than `_mint()` wherever possible
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both open [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/Rari-Capital/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function so that NFTs aren't lost if they're minted to contracts that cannot transfer them back out.

Be careful however to respect the CEI pattern or add a re-entrancy guard as `_safeMint` adds a callback-check (`_checkOnERC721Received`) and a malicious `onERC721Received` could be exploited if not careful.

Reading material:

- <https://blocksecteam.medium.com/when-safemint-becomes-unsafe-lessons-from-the-hypebears-security-incident-2965209bda2a>
- <https://samczsun.com/the-dangers-of-surprising-code/>
- <https://github.com/KadenZipfel/smart-contract-attack-vectors/blob/master/vulnerabilities/unprotected-callback.md>

*Instances (1)*:
```solidity
File: contracts/SemiFungiblePositionManager.sol

515:         _mint(msg.sender, TokenId.unwrap(tokenId), positionSize);

```
[Link to code](https://github.com/code-423n4/2024-04-panoptic/blob/main/contracts/SemiFungiblePositionManager.sol)
