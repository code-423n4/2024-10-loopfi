# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings) | 24 |
| [GAS-2](#GAS-2) | Use assembly to check for `address(0)` | 30 |
| [GAS-3](#GAS-3) | Using bools for storage incurs overhead | 7 |
| [GAS-4](#GAS-4) | Cache array length outside of loop | 9 |
| [GAS-5](#GAS-5) | State variables should be cached in stack variables rather than re-reading them from storage | 28 |
| [GAS-6](#GAS-6) | Use calldata instead of memory for function arguments that do not get mutated | 19 |
| [GAS-7](#GAS-7) | For Operations that will not overflow, you could use unchecked | 674 |
| [GAS-8](#GAS-8) | Avoid contract existence checks by using low level calls | 10 |
| [GAS-9](#GAS-9) | Stack variable used as a cheaper cache for a state variable is only used once | 6 |
| [GAS-10](#GAS-10) | State variables only set in the constructor should be declared `immutable` | 42 |
| [GAS-11](#GAS-11) | Functions guaranteed to revert when called by normal users can be marked `payable` | 20 |
| [GAS-12](#GAS-12) | `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`) | 8 |
| [GAS-13](#GAS-13) | Using `private` rather than `public` for constants, saves gas | 12 |
| [GAS-14](#GAS-14) | Superfluous event fields | 1 |
| [GAS-15](#GAS-15) | Increments/decrements can be unchecked in for-loops | 10 |
| [GAS-16](#GAS-16) | Use != 0 instead of > 0 for unsigned integer comparison | 27 |
| [GAS-17](#GAS-17) | `internal` functions not called by the contract should be removed | 8 |
| [GAS-18](#GAS-18) | WETH address definition can be use directly | 1 |
### <a name="GAS-1"></a>[GAS-1] `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings)
This saves **16 gas per instance.**

*Instances (24)*:
```solidity
File: ./src/CDPVault.sol

549:         cdd.cumulativeQuotaInterest += position.cumulativeQuotaInterest;

553:         cdd.accruedInterest += cdd.cumulativeQuotaInterest;

748:                 profit += cumulativeQuotaInterest; // U:[CL-3]

754:                 profit += amountToRepay; // U:[CL-3]

773:                 profit += interestAccrued;

778:                 profit += amountToRepay; // U:[CL-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Locking.sol

50:         deposits[msg.sender].amount += _amount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

772:             _expectedLiquidityLU += _calcQuotaRevenueAccrued(timestampLU).toUint128(); // U:[LP-20]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

110:         cooldowns[msg.sender].underlyingAmount += uint152(assets);

123:         cooldowns[msg.sender].underlyingAmount += uint152(assets);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/VaultRegistry.sol

70:             totalNormalDebt += debt;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

74:                 if (totalShares != 0) index += accrued.divDown(totalShares);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

203:                 i += 1;

262:                 i += 1;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/proxy/PoolAction.sol

210:             joinAmount += upfrontAmount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction4626.sol

97:             addCollateralAmount += upFrontAmount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction4626.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

153:             qp.totalVotesLpSide += votes; // U:[GA-12]

154:             uv.votesLpSide += votes; // U:[GA-12]

156:             qp.totalVotesCaSide += votes; // U:[GA-12]

157:             uv.votesCaSide += votes; // U:[GA-12]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

147:             quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR;

211:             quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR; // U:[PQK-7]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Math.sol

358:         r += 16597577552685614221487285958193947469193820559219878177908093499208371 * (159 - t);

360:         r += 600920179829731861736702779321621459595472258049074101567377883020018308;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

### <a name="GAS-2"></a>[GAS-2] Use assembly to check for `address(0)`
*Saves 6 gas per instance*

*Instances (30)*:
```solidity
File: ./src/CDPVault.sol

332:         if (address(rewardManager) != address(0)) {

388:         if (address(rewardController) != address(0)) {

392:         if (address(rewardManager) != address(0)) _handleTokenRewards(owner, collateralBefore, deltaCollateral);

584:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

655:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

47:         if (user1 != address(0) && user1 != address(this))

58:         assert(user != address(0) && user != address(this));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

102:     ) external view returns (bool permission) {

112:         permission = _permissions[proxy.owner()][envoy][target];

162:     ) external override onlyNonProxyOwner(msg.sender) returns (IPRBProxy proxy) {

273: 

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/proxy/PoolAction.sol

106:                 if (input.tokenIn != address(0)) {

170:         if (input.tokenIn != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

118:             flashlender_ == address(0) ||

119:             swapAction_ == address(0) ||

120:             poolAction_ == address(0) ||

121:             vaultRegistry_ == address(0)

332:             leverParams.auxSwap.assetIn != address(0) &&

380:             if (residualRecipient == address(0)) {

416:         if (leverParams.auxSwap.assetIn != address(0)) {

514:                 if (leverParams.auxSwap.assetIn != address(0) && leverParams.auxSwap.swapType == SwapType.EXACT_IN) {

547:         if (collateralParams.auxSwap.assetIn != address(0)) {

578:         if (collateralParams.auxSwap.assetIn != address(0)) {

604:         if (creditParams.auxSwap.assetIn == address(0)) {

628:         if (creditParams.auxSwap.assetIn != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/PositionAction4626.sol

92:         if (leverParams.collateralToken == upFrontToken && leverParams.auxSwap.assetIn == address(0)) {

106:             if (leverParams.auxSwap.assetIn != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction4626.sol)

```solidity
File: ./src/proxy/PositionActionPendle.sol

64:         if (dst != collateralToken && dst != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionActionPendle.sol)

```solidity
File: ./src/proxy/SwapAction.sol

151:             if (swapParams.residualRecipient != address(0)) {

389:         if (recipient != address(this)) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

### <a name="GAS-3"></a>[GAS-3] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (7)*:
```solidity
File: ./src/PoolV3.sol

88:     bool public locked;

113:     mapping(address => bool) internal _allowed;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/VaultRegistry.sol

17:     mapping(ICDPVault => bool) private registeredVaults;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

50:     mapping(address owner => mapping(address envoy => mapping(address target => bool permission)))

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

47:     bool public override epochFrozen;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/utils/Permission.sol

26:     mapping(address owner => mapping(address caller => bool permitted)) private _permitted;

29:     mapping(address owner => mapping(address manager => bool permitted)) private _permittedAgents;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

### <a name="GAS-4"></a>[GAS-4] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (9)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

46:         for (uint256 i = 0; i < _tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

63:             for (uint256 i = 0; i < tokens.length; ++i) {

84:             for (uint256 i = 0; i < tokens.length; i++) {

97:         for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

62:         for (uint256 i = 0; i < tokens.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/proxy/PoolAction.sol

91:                 for (uint256 i = 0; i < assets.length; ) {

135:         for (uint256 i = 0; i < assets.length; ) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

### <a name="GAS-5"></a>[GAS-5] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*Saves 100 gas per instance*

*Instances (28)*:
```solidity
File: ./src/CDPVault.sol

478:             uint256 scaledDebtDecrease = wmul(debtToDecrease, poolUnderlyingScale);

499:             uint256 scaledRemainingDebt = wmul(debtData.debt - newDebt, poolUnderlyingScale);

513:             uint256 amount = wmul(abs(deltaCollateral), tokenScale);

529:             int256 scaledQuotaRevenueChange = wmul(poolUnderlyingScale, quotaRevenueChange);

530:             IPoolV3(pool).updateQuotaRevenue(scaledQuotaRevenueChange);

642:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), penalty);

643:         IPoolV3Loop(address(pool)).mintProfit(penalty);

646:             IPoolV3(pool).updateQuotaRevenue(_calcQuotaRevenueChange(-int(debtData.debt - newDebt))); // U:[PQK-15]

708:             IPoolV3(pool).updateQuotaRevenue(quotaRevenueChange); // U:[PQK-15]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

464:                 IERC20(underlyingToken).safeTransfer({to: treasury, value: assetsSent - amountToUser}); // U:[LP-8,9]

594:             address treasury_ = treasury;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

137:         emit CooldownDurationUpdated(previousDuration, cooldownDuration);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

71:                 uint256 accrued = IERC20(tokens[i]).balanceOf(vault) - lastBalance;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

40:         else _totalShares = _totalShares - (-deltaCollateral).toUint256();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/proxy/PositionAction.sol

343:                 _transferFrom(upFrontToken, collateralizer, self, upFrontAmount, permitParams);

348:         IPermission(leverParams.vault).modifyPermission(leverParams.position, self, true);

350:             IERC3156FlashBorrower(self),

351:             address(underlyingToken),

355:         IPermission(leverParams.vault).modifyPermission(leverParams.position, self, false);

387:             ICreditFlashBorrower(self),

392:         IPermission(leverParams.vault).modifyPermission(leverParams.position, self, false);

426:             address(swapAction),

446:         underlyingToken.forceApprove(address(flashlender), addDebt);

503:                 address(swapAction),

517:                         address(swapAction),

527:         underlyingToken.forceApprove(address(flashlender), subDebt + fee);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

361:     function pendleJoin(address recipient, uint256 minOut, bytes memory data) internal returns (uint256 netLpOut) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

220:         IPoolV3(pool).setQuotaRevenue(quotaRevenue); // U:[PQK-7]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="GAS-6"></a>[GAS-6] Use calldata instead of memory for function arguments that do not get mutated
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly bypasses this loop. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gas-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one. 

 *Saves 60 gas per instance*

*Instances (19)*:
```solidity
File: ./src/proxy/PoolAction.sol

117:     function join(PoolActionParams memory poolActionParams) public {

191:     PoolActionParams memory poolActionParams,

244:     function exit(PoolActionParams memory poolActionParams) public returns (uint256 retAmount) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

366:         LeverParams memory leverParams,

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

113:     function swap(SwapParams memory swapParams) public returns (uint256 retAmount) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/vendor/IBalancerVault.sol

127:         SingleSwap memory singleSwap,

128:         FundManagement memory funds,

164:         BatchSwapStep[] memory swaps,

165:         address[] memory assets,

166:         FundManagement memory funds,

167:         int256[] memory limits,

207:         JoinPoolRequest memory request

249:         ExitPoolRequest memory request

287:         BatchSwapStep[] memory swaps,

288:         address[] memory assets,

289:         FundManagement memory funds

299:     function manageUserBalance(UserBalanceOp[] memory ops) external payable;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IBalancerVault.sol)

```solidity
File: ./src/vendor/IPriceOracle.sol

51:         OracleAverageQuery[] memory queries

85:         OracleAccumulatorQuery[] memory queries

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IPriceOracle.sol)

### <a name="GAS-7"></a>[GAS-7] For Operations that will not overflow, you could use unchecked

*Instances (674)*:
```solidity
File: ./src/CDPVault.sol

4: import {AccessControl} from "@openzeppelin/contracts/access/AccessControl.sol";

5: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import {IERC20Metadata} from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

7: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

9: import {ICDPVaultBase, CDPVaultConstants, CDPVaultConfig} from "./interfaces/ICDPVault.sol";

10: import {IOracle} from "./interfaces/IOracle.sol";

12: import {WAD, toInt256, toUint64, max, min, add, sub, wmul, wdiv, wmulUp, abs} from "./utils/Math.sol";

13: import {Permission} from "./utils/Permission.sol";

14: import {Pause, PAUSER_ROLE} from "./utils/Pause.sol";

15: import {IPoolV3} from "./interfaces/IPoolV3.sol";

17: import {IChefIncentivesController} from "./reward/interfaces/IChefIncentivesController.sol";

19: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

20: import {CreditLogic} from "@gearbox-protocol/core-v3/contracts/libraries/CreditLogic.sol";

21: import {QuotasLogic} from "@gearbox-protocol/core-v3/contracts/libraries/QuotasLogic.sol";

22: import {IPoolQuotaKeeperV3} from "@gearbox-protocol/core-v3/contracts/interfaces/IPoolQuotaKeeperV3.sol";

71:     uint256 constant INDEX_PRECISION = 10 ** 9;

110:         uint256 collateral; // [wad]

111:         uint256 debt; // [wad]

112:         uint256 lastDebtUpdate; // [timestamp]

186:         poolUnderlyingScale = 10 ** IERC20Metadata(address(poolUnderlying)).decimals();

258:         int256 deltaCollateral = -toInt256(tokenAmount);

294:         deltaDebt = -toInt256(scaledAmount);

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

369:         position.debt = newDebt; // U:[CM-10,11]

370:         position.cumulativeIndexLastUpdate = newCumulativeIndex; // U:[CM-10,11]

371:         position.lastDebtUpdate = block.timestamp; // U:[CM-10,11]

382:             totalDebt_ = totalDebt_ + (newDebt - currentDebt);

384:             totalDebt_ = totalDebt_ - (currentDebt - newDebt);

475:                 deltaDebt = -toInt256(debtToDecrease);

497:             quotaRevenueChange = _calcQuotaRevenueChange(-int(debtData.debt - newDebt));

499:             uint256 scaledRemainingDebt = wmul(debtData.debt - newDebt, poolUnderlyingScale);

549:         cdd.cumulativeQuotaInterest += position.cumulativeQuotaInterest;

553:         cdd.accruedInterest += cdd.cumulativeQuotaInterest;

567:         outstandingQuotaInterest = outstandingInterestDelta; // U:[CM-24]

605:         uint256 penalty = wmul(repayAmount, WAD - liqConfig_.liquidationPenalty);

613:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount - penalty);

626:                 deltaDebt, // delta debt

628:                 debtData.cumulativeIndexNow, // current cumulative base interest index in Ray

635:         position = _modifyPosition(owner, position, newDebt, newCumulativeIndex, -toInt256(takeCollateral), totalDebt);

637:         pool.repayCreditAccount(debtData.debt - newDebt, profit, 0); // U:[CM-11]

645:         if (debtData.debt - newDebt != 0) {

646:             IPoolV3(pool).updateQuotaRevenue(_calcQuotaRevenueChange(-int(debtData.debt - newDebt))); // U:[PQK-15]

681:         uint256 loss = calcTotalDebt(debtData) - repayAmount;

684:             profit = repayAmount - debtData.debt;

698:             -toInt256(takeCollateral),

702:         pool.repayCreditAccount(debtData.debt, profit, loss); // U:[CM-11]

706:         int256 quotaRevenueChange = _calcQuotaRevenueChange(-int(debtData.debt));

708:             IPoolV3(pool).updateQuotaRevenue(quotaRevenueChange); // U:[PQK-15]

747:                 amountToRepay -= cumulativeQuotaInterest; // U:[CL-3]

748:                 profit += cumulativeQuotaInterest; // U:[CL-3]

750:                 newCumulativeQuotaInterest = 0; // U:[CL-3]

753:                 uint256 quotaInterestPaid = amountToRepay; // U:[CL-3]

754:                 profit += amountToRepay; // U:[CL-3]

755:                 amountToRepay = 0; // U:[CL-3]

757:                 newCumulativeQuotaInterest = uint128(cumulativeQuotaInterest - quotaInterestPaid); // U:[CL-3]

771:                 amountToRepay -= interestAccrued;

773:                 profit += interestAccrued;

778:                 profit += amountToRepay; // U:[CL-3]

781:                     (INDEX_PRECISION * cumulativeIndexNow * cumulativeIndexLastUpdate) /

782:                     (INDEX_PRECISION *

783:                         cumulativeIndexNow -

784:                         (INDEX_PRECISION * amountToRepay * cumulativeIndexLastUpdate) /

785:                         debt); // U:[CL-3]

787:                 amountToRepay = 0; // U:[CL-3]

792:         newDebt = debt - amountToRepay;

802:         return (amount * cumulativeIndexNow) / cumulativeIndexLastUpdate - amount;

815:         return debtData.debt + debtData.accruedInterest; //+ debtData.accruedFees;

820:         return IPoolV3(pool).poolQuotaKeeper(); // U:[CM-47]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

4: import {ReentrancyGuard} from "@openzeppelin/contracts/security/ReentrancyGuard.sol";

5: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import {IFlashlender, IERC3156FlashBorrower, ICreditFlashBorrower} from "./interfaces/IFlashlender.sol";

7: import {IPoolV3} from "./interfaces/IPoolV3.sol";

8: import {wmul, wdiv, WAD} from "./utils/Math.sol";

66:             max = wdiv(pool.creditManagerBorrowable(address(this)), WAD + protocolFee);

95:         uint256 total = amount + fee;

106:         pool.repayCreditAccount(total - fee, 0, 0);

124:         uint256 total = amount + fee;

135:         pool.repayCreditAccount(total - fee, 0, 0);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

6: import "@openzeppelin/contracts/access/Ownable.sol";

50:         deposits[msg.sender].amount += _amount;

74:             if (block.timestamp < deposits[msg.sender].cooldownStart + cooldownPeriod) revert CooldownPeriodNotPassed();

80:         deposits[msg.sender].amount -= _amount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

7: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

9: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

10: import {IERC4626} from "@openzeppelin/contracts/interfaces/IERC4626.sol";

11: import {IERC20Metadata} from "@openzeppelin/contracts/interfaces/IERC20Metadata.sol";

12: import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";

13: import {ERC4626} from "@openzeppelin/contracts/token/ERC20/extensions/ERC4626.sol";

14: import {ERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";

16: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

17: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

18: import {EnumerableSet} from "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

21: import {IAddressProviderV3, AP_TREASURY, NO_VERSION_CONTROL} from "@gearbox-protocol/core-v3/contracts/interfaces/IAddressProviderV3.sol";

22: import {ICreditManagerV3} from "@gearbox-protocol/core-v3/contracts/interfaces/ICreditManagerV3.sol";

23: import {ILinearInterestRateModelV3} from "@gearbox-protocol/core-v3/contracts/interfaces/ILinearInterestRateModelV3.sol";

24: import {IPoolQuotaKeeperV3} from "@gearbox-protocol/core-v3/contracts/interfaces/IPoolQuotaKeeperV3.sol";

25: import {IPoolV3} from "./interfaces/IPoolV3.sol";

26: import {IWETH} from "./reward/interfaces/IWETH.sol";

29: import {CreditLogic} from "@gearbox-protocol/core-v3/contracts/libraries/CreditLogic.sol";

30: import {ACLNonReentrantTrait} from "@gearbox-protocol/core-v3/contracts/traits/ACLNonReentrantTrait.sol";

31: import {ContractsRegisterTrait} from "@gearbox-protocol/core-v3/contracts/traits/ContractsRegisterTrait.sol";

34: import {RAY, MAX_WITHDRAW_FEE, SECONDS_PER_YEAR, PERCENTAGE_FACTOR} from "@gearbox-protocol/core-v2/contracts/libraries/Constants.sol";

36: import {ICDM} from "./interfaces/ICDM.sol";

39: import "@gearbox-protocol/core-v3/contracts/interfaces/IExceptions.sol";

137:         if (msg.sender != poolQuotaKeeper) revert CallerNotPoolQuotaKeeperException(); // U:[LP-2C]

143:             revert CallerNotCreditManagerException(); // U:[PQK-4]

148:         if (locked) revert PoolV3LockedException(); // U:[LP-2C]

167:         ACLNonReentrantTrait(addressProvider_) // U:[LP-1A]

169:         ERC4626(IERC20(underlyingToken_)) // U:[LP-1B]

170:         ERC20(name_, symbol_) // U:[LP-1B]

171:         ERC20Permit(name_) // U:[LP-1B]

172:         nonZeroAddress(underlyingToken_) // U:[LP-1A]

173:         nonZeroAddress(interestRateModel_) // U:[LP-1A]

176:         addressProvider = addressProvider_; // U:[LP-1B]

177:         underlyingToken = underlyingToken_; // U:[LP-1B]

179:         lastBaseInterestUpdate = uint40(block.timestamp); // U:[LP-1B]

180:         _baseInterestIndexLU = uint128(RAY); // U:[LP-1B]

182:         interestRateModel = interestRateModel_; // U:[LP-1B]

183:         emit SetInterestRateModel(interestRateModel_); // U:[LP-1B]

187:         _setTotalDebtLimit(totalDebtLimit_); // U:[LP-1B]

202:         return IERC20(underlyingToken).balanceOf(address(this)); // U:[LP-3]

208:         return _expectedLiquidityLU + _calcBaseInterestAccrued() + _calcQuotaRevenueAccrued(); // U:[LP-4]

236:         whenNotPaused // U:[LP-2A]

237:         nonReentrant // U:[LP-2B]

238:         nonZeroAddress(receiver) // U:[LP-5]

241:         uint256 assetsReceived = _amountMinusFee(assets); // U:[LP-6]

242:         shares = _convertToShares(assetsReceived); // U:[LP-6]

243:         _deposit(receiver, assets, assetsReceived, shares); // U:[LP-6]

252:         shares = deposit(assets, receiver); // U:[LP-2A,2B,5,6]

253:         emit Refer(receiver, referralCode, assets); // U:[LP-6]

262:         whenNotPaused // U:[LP-2A]

263:         nonReentrant // U:[LP-2B]

264:         nonZeroAddress(receiver) // U:[LP-5]

280:         uint256 assetsReceived = _amountMinusFee(msg.value); // U:[LP-6]

281:         shares = _convertToShares(assetsReceived); // U:[LP-6]

284:         _registerDeposit(receiver, msg.value, assetsReceived, shares); // U:[LP-6]

297:         whenNotPaused // U:[LP-2A]

298:         nonReentrant // U:[LP-2B]

299:         nonZeroAddress(receiver) // U:[LP-5]

302:         uint256 assetsReceived = _convertToAssets(shares); // U:[LP-7]

303:         assets = _amountWithFee(assetsReceived); // U:[LP-7]

304:         _deposit(receiver, assets, assetsReceived, shares); // U:[LP-7]

313:         assets = mint(shares, receiver); // U:[LP-2A,2B,5,7]

314:         emit Refer(receiver, referralCode, assets); // U:[LP-7]

329:         whenNotPaused // U:[LP-2A]

331:         nonReentrant // U:[LP-2B]

332:         nonZeroAddress(receiver) // U:[LP-5]

336:         uint256 assetsSent = _amountWithWithdrawalFee(assetsToUser); // U:[LP-8]

337:         shares = _convertToShares(assetsSent); // U:[LP-8]

338:         _withdraw(receiver, owner, assetsSent, assets, assetsToUser, shares); // U:[LP-8]

353:         whenNotPaused // U:[LP-2A]

355:         nonReentrant // U:[LP-2B]

356:         nonZeroAddress(receiver) // U:[LP-5]

359:         uint256 assetsSent = _convertToAssets(shares); // U:[LP-9]

361:         assets = _amountMinusFee(assetsToUser); // U:[LP-9]

362:         _withdraw(receiver, owner, assetsSent, assets, assetsToUser, shares); // U:[LP-9]

367:         shares = _convertToShares(_amountMinusFee(assets)); // U:[LP-10]

372:         return _amountWithFee(_convertToAssets(shares)); // U:[LP-10]

377:         return _convertToShares(_amountWithWithdrawalFee(_amountWithFee(assets))); // U:[LP-10]

382:         return _amountMinusFee(_amountMinusWithdrawalFee(_convertToAssets(shares))); // U:[LP-10]

387:         return paused() ? 0 : type(uint256).max; // U:[LP-11]

392:         return paused() ? 0 : type(uint256).max; // U:[LP-11]

402:                 ); // U:[LP-11]

407:         return paused() ? 0 : Math.min(balanceOf(owner), _convertToShares(availableLiquidity())); // U:[LP-11]

415:         IERC20(underlyingToken).safeTransferFrom({from: msg.sender, to: address(this), value: assetsSent}); // U:[LP-6,7]

421:         }); // U:[LP-6,7]

423:         _mint(receiver, shares); // U:[LP-6,7]

424:         emit Deposit(msg.sender, receiver, assetsSent, shares); // U:[LP-6,7]

434:         }); // U:[LP-6,7]

436:         _mint(receiver, shares); // U:[LP-6,7]

437:         emit Deposit(msg.sender, receiver, assetsSent, shares); // U:[LP-6,7]

452:         if (msg.sender != owner) _spendAllowance({owner: owner, spender: msg.sender, amount: shares}); // U:[LP-8,9]

453:         _burn(owner, shares); // U:[LP-8,9]

456:             expectedLiquidityDelta: -assetsSent.toInt256(),

457:             availableLiquidityDelta: -assetsSent.toInt256(),

459:         }); // U:[LP-8,9]

461:         IERC20(underlyingToken).safeTransfer({to: receiver, value: amountToUser}); // U:[LP-8,9]

464:                 IERC20(underlyingToken).safeTransfer({to: treasury, value: assetsSent - amountToUser}); // U:[LP-8,9]

467:         emit Withdraw(msg.sender, receiver, owner, assetsReceived, shares); // U:[LP-8,9]

474:         return assets; //(assets == 0 || supply == 0) ? assets : assets.mulDiv(supply, totalAssets(), rounding);

481:         return shares; //(supply == 0) ? shares : shares.mulDiv(totalAssets(), supply, rounding);

510:         borrowable = _borrowable(_totalDebt); // U:[LP-12]

511:         if (borrowable == 0) return 0; // U:[LP-12]

513:         borrowable = Math.min(borrowable, _borrowable(_creditManagerDebt[creditManager])); // U:[LP-12]

514:         if (borrowable == 0) return 0; // U:[LP-12]

519:         }); // U:[LP-12]

521:         borrowable = Math.min(borrowable, available); // U:[LP-12]

533:         creditManagerOnly // U:[LP-2C]

534:         whenNotPaused // U:[LP-2A]

535:         nonReentrant // U:[LP-2B]

540:         uint128 totalBorrowed_ = _totalDebt.borrowed + borrowedAmountU128;

541:         uint128 cmBorrowed_ = cmDebt.borrowed + borrowedAmountU128;

543:             revert CreditManagerCantBorrowException(); // U:[LP-2C,13A]

548:             availableLiquidityDelta: -borrowedAmount.toInt256(),

550:         }); // U:[LP-13B]

552:         cmDebt.borrowed = cmBorrowed_; // U:[LP-13B]

553:         _totalDebt.borrowed = totalBorrowed_; // U:[LP-13B]

555:         IERC20(underlyingToken).safeTransfer({to: creditAccount, value: borrowedAmount}); // U:[LP-13B]

556:         emit Borrow(msg.sender, creditAccount, borrowedAmount); // U:[LP-13B]

579:         creditManagerOnly // U:[LP-2C]

580:         whenNotPaused // U:[LP-2A]

581:         nonReentrant // U:[LP-2B]

588:             revert CallerNotCreditManagerException(); // U:[LP-2C,14A]

592:             _mint(treasury, _convertToShares(profit)); // U:[LP-14B]

601:                         loss: _convertToAssets(sharesToBurn - sharesInTreasury)

602:                     }); // U:[LP-14D]

606:             _burn(treasury_, sharesToBurn); // U:[LP-14C,14D]

610:             expectedLiquidityDelta: -loss.toInt256(),

613:         }); // U:[LP-14B,14C,14D]

615:         _totalDebt.borrowed -= repaidAmountU128; // U:[LP-14B,14C,14D]

616:         cmDebt.borrowed = cmBorrowed - repaidAmountU128; // U:[LP-14B,14C,14D]

618:         emit Repay(msg.sender, repaidAmount, profit, loss); // U:[LP-14B,14C,14D]

630:             return limit - borrowed;

651:             ((baseInterestRate_ * _totalDebt.borrowed) * (PERCENTAGE_FACTOR - withdrawFee)) /

652:             PERCENTAGE_FACTOR /

653:             assets; // U:[LP-15]

659:         if (block.timestamp == timestampLU) return _baseInterestIndexLU; // U:[LP-16]

660:         return _calcBaseInterestIndex(timestampLU); // U:[LP-16]

671:         if (block.timestamp == timestampLU) return 0; // U:[LP-17]

672:         return _calcBaseInterestAccrued(timestampLU); // U:[LP-17]

690:         uint256 expectedLiquidity_ = (expectedLiquidity().toInt256() + expectedLiquidityDelta).toUint256();

691:         uint256 availableLiquidity_ = (availableLiquidity().toInt256() + availableLiquidityDelta).toUint256();

695:             _baseInterestIndexLU = _calcBaseInterestIndex(lastBaseInterestUpdate_).toUint128(); // U:[LP-18]

700:             lastQuotaRevenueUpdate = uint40(block.timestamp); // U:[LP-18]

703:         _expectedLiquidityLU = expectedLiquidity_.toUint128(); // U:[LP-18]

710:             .toUint128(); // U:[LP-18]

715:         return (_totalDebt.borrowed * baseInterestRate().calcLinearGrowth(timestamp)) / RAY;

720:         return (_baseInterestIndexLU * (RAY + baseInterestRate().calcLinearGrowth(timestamp))) / RAY;

739:         nonReentrant // U:[LP-2B]

743:         _setQuotaRevenue((quotaRevenue().toInt256() + quotaRevenueDelta).toUint256());

753:         nonReentrant // U:[LP-2B]

754:         poolQuotaKeeperOnly // U:[LP-2C]

756:         _setQuotaRevenue(newQuotaRevenue); // U:[LP-20]

762:         if (block.timestamp == timestampLU) return 0; // U:[LP-21]

763:         return _calcQuotaRevenueAccrued(timestampLU); // U:[LP-21]

772:             _expectedLiquidityLU += _calcQuotaRevenueAccrued(timestampLU).toUint128(); // U:[LP-20]

773:             lastQuotaRevenueUpdate = uint40(block.timestamp); // U:[LP-20]

775:         _quotaRevenue = newQuotaRevenue.toUint96(); // U:[LP-20]

794:         configuratorOnly // U:[LP-2C]

795:         nonZeroAddress(newInterestRateModel) // U:[LP-22A]

797:         interestRateModel = newInterestRateModel; // U:[LP-22B]

798:         _updateBaseInterest(0, 0, false); // U:[LP-22B]

799:         emit SetInterestRateModel(newInterestRateModel); // U:[LP-22B]

809:         configuratorOnly // U:[LP-2C]

810:         nonZeroAddress(newPoolQuotaKeeper) // U:[LP-23A]

813:             revert IncompatiblePoolQuotaKeeperException(); // U:[LP-23C]

816:         poolQuotaKeeper = newPoolQuotaKeeper; // U:[LP-23D]

819:         _setQuotaRevenue(newQuotaRevenue); // U:[LP-23D]

821:         emit SetPoolQuotaKeeper(newPoolQuotaKeeper); // U:[LP-23D]

836:         controllerOnly // U:[LP-2C]

838:         _setTotalDebtLimit(newLimit); // U:[LP-24]

851:         controllerOnly // U:[LP-2C]

852:         nonZeroAddress(creditManager) // U:[LP-25A]

856:                 revert IncompatibleCreditManagerException(); // U:[LP-25C]

858:             _creditManagerSet.add(creditManager); // U:[LP-25D]

859:             emit AddCreditManager(creditManager); // U:[LP-25D]

861:         _creditManagerDebt[creditManager].limit = _convertToU128(newLimit); // U:[LP-25D]

862:         emit SetCreditManagerDebtLimit(creditManager, newLimit); // U:[LP-25D]

872:         controllerOnly // U:[LP-2C]

875:             revert IncorrectParameterException(); // U:[LP-26A]

879:         withdrawFee = newWithdrawFee.toUint16(); // U:[LP-26B]

880:         emit SetWithdrawFee(newWithdrawFee); // U:[LP-26B]

907:         _totalDebt.limit = newLimit; // U:[LP-1B,24]

908:         emit SetTotalDebtLimit(limit); // U:[LP-1B,24]

929:         return (amount * PERCENTAGE_FACTOR) / (PERCENTAGE_FACTOR - withdrawFee);

934:         return (amount * (PERCENTAGE_FACTOR - withdrawFee)) / PERCENTAGE_FACTOR;

954:         }); // U:[LP-14B,14C,14D]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

3: import "@openzeppelin/contracts/token/ERC20/extensions/ERC4626.sol";

4: import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

5: import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";

6: import "src/Silo.sol";

109:         cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;

110:         cooldowns[msg.sender].underlyingAmount += uint152(assets);

122:         cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;

123:         cooldowns[msg.sender].underlyingAmount += uint152(assets);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

3: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

4: import {Address} from "@openzeppelin/contracts/utils/Address.sol";

5: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

6: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

7: import {PaymentSplitter} from "@openzeppelin/contracts/finance/PaymentSplitter.sol";

8: import {AccessControl} from "@openzeppelin/contracts/access/AccessControl.sol";

57:         for (uint256 i = 0; i < len; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

4: import {AccessControl} from "@openzeppelin/contracts/access/AccessControl.sol";

5: import {ICDPVault} from "./interfaces/ICDPVault.sol";

6: import {Permission} from "./utils/Permission.sol";

7: import {Pause, PAUSER_ROLE} from "./utils/Pause.sol";

8: import {IVaultRegistry} from "./interfaces/IVaultRegistry.sol";

70:             totalNormalDebt += debt;

73:                 ++i;

84:                 vaultList[i] = vaultList[vaultLen - 1];

90:                 ++i;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/interfaces/IBuffer.sol

4: import {ICDM} from "./ICDM.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IBuffer.sol)

```solidity
File: ./src/interfaces/ICDM.sol

4: import {IPermission} from "./IPermission.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDM.sol)

```solidity
File: ./src/interfaces/ICDPVault.sol

4: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import {IAccessControl} from "@openzeppelin/contracts/access/IAccessControl.sol";

7: import {ICDM} from "./ICDM.sol";

8: import {IOracle} from "./IOracle.sol";

9: import {IBuffer} from "./IBuffer.sol";

10: import {IPause} from "./IPause.sol";

11: import {IPermission} from "./IPermission.sol";

12: import {IInterestRateModel} from "./IInterestRateModel.sol";

13: import {IPoolV3} from "./IPoolV3.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDPVault.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

4: import {IPoolV3} from "./IPoolV3.sol";

5: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/interfaces/IMinter.sol

4: import {ICDM} from "./ICDM.sol";

5: import {IStablecoin} from "./IStablecoin.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IMinter.sol)

```solidity
File: ./src/interfaces/IPoolQuotaKeeperV3.sol

6: import {IVersion} from "@gearbox-protocol/core-v2/contracts/interfaces/IVersion.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolQuotaKeeperV3.sol)

```solidity
File: ./src/interfaces/IPoolV3.sol

7: import {IERC4626} from "@openzeppelin/contracts/interfaces/IERC4626.sol";

8: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Permit.sol";

9: import {IVersion} from "@gearbox-protocol/core-v2/contracts/interfaces/IVersion.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolV3.sol)

```solidity
File: ./src/interfaces/IStablecoin.sol

4: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Permit.sol";

5: import {IERC20Metadata} from "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IStablecoin.sol)

```solidity
File: ./src/interfaces/IVaultRegistry.sol

4: import {ICDPVault} from "./ICDPVault.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IVaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

7: import {AggregatorV3Interface} from "../vendor/AggregatorV3Interface.sol";

9: import {wdiv} from "../utils/Math.sol";

10: import {IOracle, MANAGER_ROLE} from "../interfaces/IOracle.sol";

46:         for (uint256 i = 0; i < _tokens.length; i++) {

70:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {}

99:             uint80 /*roundId*/,

101:             uint256 /*startedAt*/,

103:             uint80 /*answeredInRound*/

105:             isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

6: import "pendle/oracles/PendleLpOracleLib.sol";

8: import {AggregatorV3Interface} from "../vendor/AggregatorV3Interface.sol";

10: import {wdiv, wmul} from "../utils/Math.sol";

11: import {IOracle, MANAGER_ROLE} from "../interfaces/IOracle.sol";

12: import {IPMarket} from "pendle/interfaces/IPMarket.sol";

13: import {PendleLpOracleLib} from "pendle/oracles/PendleLpOracleLib.sol";

14: import {IPPtOracle} from "pendle/interfaces/IPPtOracle.sol";

65:         aggregatorScale = 10 ** uint256(aggregator.decimals());

90:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {

101:     function getStatus(address /*token*/) public view virtual override returns (bool status) {

109:     function spot(address /* token */) external view virtual override returns (uint256 price) {

126:             uint256 /*startedAt*/,

130:             isValid = (answer > 0 && block.timestamp - updatedAt <= stalePeriod);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

4: import "./RewardManagerAbstract.sol";

5: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import {IPRBProxy, IPRBProxyRegistry} from "../prb-proxy/interfaces/IPRBProxyRegistry.sol";

7: import {console} from "forge-std/console.sol";

63:             for (uint256 i = 0; i < tokens.length; ++i) {

71:                 uint256 accrued = IERC20(tokens[i]).balanceOf(vault) - lastBalance;

74:                 if (totalShares != 0) index += accrued.divDown(totalShares);

78:                     lastBalance: (lastBalance + accrued).Uint128()

84:             for (uint256 i = 0; i < tokens.length; i++) {

97:         for (uint256 i = 0; i < tokens.length; i++) {

101:                 rewardState[tokens[i]].lastBalance -= rewardAmounts[i].Uint128();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

4: import "pendle/interfaces/IRewardManager.sol";

6: import "pendle/core/libraries/ArrayLib.sol";

7: import "pendle/core/libraries/TokenHelper.sol";

8: import "pendle/core/libraries/math/PMath.sol";

9: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

10: import "./RewardManagerAbstract.sol";

11: import {console} from "forge-std/console.sol";

39:         if (deltaCollateral > 0) _totalShares = _totalShares + deltaCollateral.toUint256();

40:         else _totalShares = _totalShares - (-deltaCollateral).toUint256();

62:         for (uint256 i = 0; i < tokens.length; ++i) {

73:             uint256 deltaIndex = index - userIndex;

76:             uint256 rewardAccrued = userReward[token][user].accrued + rewardDelta;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

4: import {IPRBProxy} from "prb-proxy/interfaces/IPRBProxy.sol";

5: import {IPRBProxyPlugin} from "prb-proxy/interfaces/IPRBProxyPlugin.sol";

6: import {IPRBProxyRegistry} from "./interfaces/IPRBProxyRegistry.sol";

7: import {PRBProxy} from "prb-proxy/PRBProxy.sol";

38:                                 USER-FACING STORAGE

81:                            USER-FACING CONSTANT FUNCTIONS

136:                          USER-FACING NON-CONSTANT FUNCTIONS

203:                 i += 1;

215:                           INTERNAL NON-CONSTANT FUNCTIONS

262:                 i += 1;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/prb-proxy/interfaces/IPRBProxyRegistry.sol

4: import {IPRBProxy} from "prb-proxy/interfaces/IPRBProxy.sol";

5: import {IPRBProxyPlugin} from "prb-proxy/interfaces/IPRBProxyPlugin.sol";

145:                                NON-CONSTANT FUNCTIONS

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/interfaces/IPRBProxyRegistry.sol)

```solidity
File: ./src/proxy/ERC165Plugin.sol

4: import {ERC1155Holder} from "@openzeppelin/contracts/token/ERC1155/utils/ERC1155Holder.sol";

5: import {ERC721Holder} from "@openzeppelin/contracts/token/ERC721/utils/ERC721Holder.sol";

7: import {IPRBProxyPlugin} from "prb-proxy/interfaces/IPRBProxyPlugin.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/ERC165Plugin.sol)

```solidity
File: ./src/proxy/PoolAction.sol

4: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

5: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import {TransferAction, PermitParams} from "./TransferAction.sol";

8: import {IVault, JoinKind, JoinPoolRequest, ExitKind, ExitPoolRequest} from "../vendor/IBalancerVault.sol";

9: import {IPActionAddRemoveLiqV3} from "pendle/interfaces/IPActionAddRemoveLiqV3.sol";

10: import {TokenInput, LimitOrderData} from "pendle/interfaces/IPAllActionTypeV3.sol";

11: import {ApproxParams} from "pendle/router/base/MarketApproxLib.sol";

12: import {IPPrincipalToken} from "pendle/interfaces/IPPrincipalToken.sol";

13: import {IStandardizedYield} from "pendle/interfaces/IStandardizedYield.sol";

14: import {IPYieldToken} from "pendle/interfaces/IPYieldToken.sol";

15: import {IPMarket} from "pendle/interfaces/IPMarket.sol";

97:                         ++i;

141:                 ++i;

210:             joinAmount += upfrontAmount;

215:             uint256 assetIndex = i - (skipIndex ? 1 : 0);

229:                 i++;

265:         for (uint256 i = 0; i <= tmpOutIndex; i++) if (assets[i] == bpt) tmpOutIndex++;

280:         return IERC20(assets[tmpOutIndex]).balanceOf(poolActionParams.recipient) - balanceBefore;

302:             netSyToRedeem = netSyRemoved + netSyFromPt;

307:             netSyToRedeem = netSyRemoved + netSySwappedOut;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

4: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

6: import {IPoolV3} from "../interfaces/IPoolV3.sol";

7: import {IPermission} from "../interfaces/IPermission.sol";

8: import {ICDPVault} from "../interfaces/ICDPVault.sol";

9: import {toInt256, wmul, min} from "../utils/Math.sol";

10: import {TransferAction, PermitParams} from "./TransferAction.sol";

11: import {BaseAction} from "./BaseAction.sol";

12: import {SwapAction, SwapParams, SwapType} from "./SwapAction.sol";

13: import {PoolAction, PoolActionParams} from "./PoolAction.sol";

14: import {IVaultRegistry} from "../interfaces/IVaultRegistry.sol";

16: import {IFlashlender, IERC3156FlashBorrower, ICreditFlashBorrower} from "../interfaces/IFlashlender.sol";

303:                 ++i;

341:                 IERC20(upFrontToken).safeTransfer(self, upFrontAmount); // if tokens are on the proxy then just transfer

402:         address /*initiator*/,

403:         address /*token*/,

435:         uint256 addDebt = amount + fee;

454:         address /*initiator*/,

455:         uint256 /*amount*/,

466:         underlyingToken.forceApprove(address(leverParams.vault), subDebt + fee);

473:             -toInt256(subDebt)

488:             uint256 residualAmount = swapAmountOut - subDebt;

498:                     -toInt256(residualAmount)

509:             uint256 residualAmount = withdrawnCollateral - swapAmountIn;

527:         underlyingToken.forceApprove(address(flashlender), subDebt + fee);

651:             -toInt256(amount)

669:             uint256 remainder = swapParams.limit - retAmount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/PositionAction20.sol

4: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

7: import {ICDPVault} from "../interfaces/ICDPVault.sol";

9: import {PositionAction, LeverParams} from "./PositionAction.sol";

42:         address /*src*/,

58:         address /*dst*/,

71:         address /*upFrontToken*/,

76:         uint256 addCollateralAmount = swapAmountOut + upFrontAmount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction20.sol)

```solidity
File: ./src/proxy/PositionAction4626.sol

4: import {IERC20} from "@openzeppelin/contracts/interfaces/IERC20.sol";

5: import {IERC4626} from "@openzeppelin/contracts/interfaces/IERC4626.sol";

6: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

8: import {ICDPVault} from "../interfaces/ICDPVault.sol";

10: import {PositionAction, LeverParams, PoolActionParams} from "./PositionAction.sol";

63:         address /*position*/,

97:             addCollateralAmount += upFrontAmount;

128:             IERC4626(leverParams.collateralToken).deposit(addCollateralAmount, address(this)) +

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction4626.sol)

```solidity
File: ./src/proxy/PositionActionPendle.sol

4: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

7: import {ICDPVault} from "../interfaces/ICDPVault.sol";

9: import {PositionAction, LeverParams} from "./PositionAction.sol";

10: import {PoolActionParams, Protocol} from "./PoolAction.sol";

43:         address /*src*/,

94:         address /*upFrontToken*/,

95:         uint256 /*upFrontAmount*/,

96:         uint256 /*swapAmountOut*/

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionActionPendle.sol)

```solidity
File: ./src/proxy/SwapAction.sol

4: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

5: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import {IUniswapV3Router, ExactInputParams, ExactOutputParams, decodeLastToken} from "../vendor/IUniswapV3Router.sol";

8: import {IVault, SwapKind, BatchSwapStep, FundManagement} from "../vendor/IBalancerVault.sol";

9: import {TokenInput, LimitOrderData} from "pendle/interfaces/IPAllActionTypeV3.sol";

10: import {ApproxParams} from "pendle/router/base/MarketApproxLib.sol";

11: import {IPActionAddRemoveLiqV3} from "pendle/interfaces/IPActionAddRemoveLiqV3.sol";

12: import {IPPrincipalToken} from "pendle/interfaces/IPPrincipalToken.sol";

13: import {IStandardizedYield} from "pendle/interfaces/IStandardizedYield.sol";

14: import {IPYieldToken} from "pendle/interfaces/IPYieldToken.sol";

15: import {IPMarket} from "pendle/interfaces/IPMarket.sol";

16: import {toInt256, abs} from "../utils/Math.sol";

17: import {console} from "forge-std/console.sol";

18: import {TransferAction, PermitParams} from "./TransferAction.sol";

40:     uint256 amount; // Exact amount in or exact amount out depending on swapType

41:     uint256 limit; // Min amount out or max amount in depending on swapType

43:     address residualRecipient; // Address to send any residual tokens to

152:                 IERC20(swapParams.assetIn).safeTransfer(swapParams.residualRecipient, swapParams.limit - retAmount);

154:                 IERC20(swapParams.assetIn).safeTransfer(swapParams.recipient, swapParams.limit - retAmount);

197:         int256[] memory limits = new int256[](pathLength + 1); // limit for each asset, leave as 0 to autocalculate

236:             bytes memory userData; // empty bytes, not used

246:                         assetInIndex: i + inIncrement,

247:                         assetOutIndex: i + outIncrement,

248:                         amount: 0, // 0 to autocalculate

251:                     ++i;

254:             swaps[0].amount = amount; // amount always pertains to the first swap

263:             limits[0] = toInt256(amount); // positive signifies tokens going into the vault from the caller

264:             limits[pathLength] = -toInt256(limit); // negative signifies tokens going out of the vault to the caller

268:             limits[0] = -toInt256(amount);

400:             netSyToRedeem = netSyRemoved + netSyFromPt;

405:             netSyToRedeem = netSyRemoved + netSySwappedOut;

423:                 token = primarySwapPath[primarySwapPath.length - 1];

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/proxy/TransferAction.sol

4: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";

6: import {SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

8: import {ISignatureTransfer} from "permit2/interfaces/ISignatureTransfer.sol";

63:                 bytes.concat(params.r, params.s, bytes1(params.v)) // Construct signature

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/TransferAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

8: import {IGaugeV3, QuotaRateParams, UserVotes} from "@gearbox-protocol/core-v3/contracts/interfaces/IGaugeV3.sol";

9: import {IGearStakingV3} from "@gearbox-protocol/core-v3/contracts/interfaces/IGearStakingV3.sol";

10: import {IPoolQuotaKeeperV3} from "src/interfaces/IPoolQuotaKeeperV3.sol";

11: import {IPoolV3} from "@gearbox-protocol/core-v3/contracts/interfaces/IPoolV3.sol";

14: import {ACLNonReentrantTrait} from "@gearbox-protocol/core-v3/contracts/traits/ACLNonReentrantTrait.sol";

17: import {CallerNotVoterException, IncorrectParameterException, TokenNotAllowedException, InsufficientVotesException} from "@gearbox-protocol/core-v3/contracts/interfaces/IExceptions.sol";

57:         nonZeroAddress(_voter) // U:[GA-01]

59:         pool = _pool; // U:[GA-01]

60:         voter = _voter; // U:[GA-01]

61:         epochLastUpdate = IGearStakingV3(_voter).getCurrentEpoch(); // U:[GA-01]

62:         epochFrozen = true; // U:[GA-01]

63:         emit SetFrozenEpoch(true); // U:[GA-01]

68:         _revertIfCallerNotVoter(); // U:[GA-02]

74:         _checkAndUpdateEpoch(); // U:[GA-14]

79:         uint16 epochNow = IGearStakingV3(voter).getCurrentEpoch(); // U:[GA-14]

82:             epochLastUpdate = epochNow; // U:[GA-14]

86:                 _poolQuotaKeeper().updateRates(); // U:[GA-14]

89:             emit UpdateEpoch(epochNow); // U:[GA-14]

98:         uint256 len = tokens.length; // U:[GA-15]

99:         rates = new uint16[](len); // U:[GA-15]

102:             for (uint256 i; i < len; ++i) {

103:                 address token = tokens[i]; // U:[GA-15]

105:                 if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-15]

107:                 QuotaRateParams memory qrp = quotaRateParams[token]; // U:[GA-15]

109:                 uint96 votesLpSide = qrp.totalVotesLpSide; // U:[GA-15]

110:                 uint96 votesCaSide = qrp.totalVotesCaSide; // U:[GA-15]

111:                 uint256 totalVotes = votesLpSide + votesCaSide; // U:[GA-15]

115:                     : uint16((uint256(qrp.minRate) * votesCaSide + uint256(qrp.maxRate) * votesLpSide) / totalVotes); // U:[GA-15]

133:         onlyVoter // U:[GA-02]

135:         (address token, bool lpSide) = abi.decode(extraData, (address, bool)); // U:[GA-10,11,12]

136:         _vote({user: user, token: token, votes: votes, lpSide: lpSide}); // U:[GA-10,11,12]

145:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-10]

147:         _checkAndUpdateEpoch(); // U:[GA-11]

149:         QuotaRateParams storage qp = quotaRateParams[token]; // U:[GA-12]

153:             qp.totalVotesLpSide += votes; // U:[GA-12]

154:             uv.votesLpSide += votes; // U:[GA-12]

156:             qp.totalVotesCaSide += votes; // U:[GA-12]

157:             uv.votesCaSide += votes; // U:[GA-12]

160:         emit Vote({user: user, token: token, votes: votes, lpSide: lpSide}); // U:[GA-12]

176:         onlyVoter // U:[GA-02]

178:         (address token, bool lpSide) = abi.decode(extraData, (address, bool)); // U:[GA-10,11,13]

179:         _unvote({user: user, token: token, votes: votes, lpSide: lpSide}); // U:[GA-10,11,13]

188:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-10]

190:         _checkAndUpdateEpoch(); // U:[GA-11]

192:         QuotaRateParams storage qp = quotaRateParams[token]; // U:[GA-13]

193:         UserVotes storage uv = userTokenVotes[user][token]; // U:[GA-13]

198:                 qp.totalVotesLpSide -= votes; // U:[GA-13]

199:                 uv.votesLpSide -= votes; // U:[GA-13]

204:                 qp.totalVotesCaSide -= votes; // U:[GA-13]

205:                 uv.votesCaSide -= votes; // U:[GA-13]

209:         emit Unvote({user: user, token: token, votes: votes, lpSide: lpSide}); // U:[GA-13]

239:         nonZeroAddress(token) // U:[GA-04]

240:         configuratorOnly // U:[GA-03]

243:             revert TokenNotAllowedException(); // U:[GA-04]

245:         _checkParams({minRate: minRate, maxRate: maxRate}); // U:[GA-04]

252:         }); // U:[GA-05]

256:             quotaKeeper.addQuotaToken({token: token, rate: minRate}); // U:[GA-05]

259:         emit AddQuotaToken({token: token, minRate: minRate, maxRate: maxRate}); // U:[GA-05]

270:         nonZeroAddress(token) // U: [GA-04]

271:         controllerOnly // U: [GA-03]

284:         nonZeroAddress(token) // U: [GA-04]

285:         controllerOnly // U: [GA-03]

292:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-06A, GA-06B]

294:         _checkParams(minRate, maxRate); // U:[GA-04]

296:         QuotaRateParams storage qrp = quotaRateParams[token]; // U:[GA-06A, GA-06B]

298:         qrp.minRate = minRate; // U:[GA-06A, GA-06B]

299:         qrp.maxRate = maxRate; // U:[GA-06A, GA-06B]

301:         emit SetQuotaTokenParams({token: token, minRate: minRate, maxRate: maxRate}); // U:[GA-06A, GA-06B]

307:             revert IncorrectParameterException(); // U:[GA-04]

313:         return quotaRateParams[token].maxRate != 0; // U:[GA-08]

324:             revert CallerNotVoterException(); // U:[GA-02]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

7: import {EnumerableSet} from "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

10: import {ACLNonReentrantTrait} from "@gearbox-protocol/core-v3/contracts/traits/ACLNonReentrantTrait.sol";

11: import {ContractsRegisterTrait} from "@gearbox-protocol/core-v3/contracts/traits/ContractsRegisterTrait.sol";

12: import {QuotasLogic} from "@gearbox-protocol/core-v3/contracts/libraries/QuotasLogic.sol";

14: import {IPoolV3} from "@gearbox-protocol/core-v3/contracts/interfaces/IPoolV3.sol";

15: import {IPoolQuotaKeeperV3, TokenQuotaParams, AccountQuota} from "src/interfaces/IPoolQuotaKeeperV3.sol";

16: import {IGaugeV3} from "@gearbox-protocol/core-v3/contracts/interfaces/IGaugeV3.sol";

17: import {ICreditManagerV3} from "@gearbox-protocol/core-v3/contracts/interfaces/ICreditManagerV3.sol";

19: import {PERCENTAGE_FACTOR, RAY} from "@gearbox-protocol/core-v2/contracts/libraries/Constants.sol";

22: import "@gearbox-protocol/core-v3/contracts/interfaces/IExceptions.sol";

82:         pool = _pool; // U:[PQK-1]

83:         underlying = IPoolV3(_pool).asset(); // U:[PQK-1]

147:             quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR;

150:                 ++i;

167:         gaugeOnly // U:[PQK-3]

170:             revert TokenAlreadyAddedException(); // U:[PQK-6]

174:         quotaTokensSet.add(token); // U:[PQK-5]

175:         totalQuotaParams[token].cumulativeIndexLU = 1; // U:[PQK-5]

177:         emit AddQuotaToken(token); // U:[PQK-5]

187:         gaugeOnly // U:[PQK-3]

190:         uint16[] memory rates = IGaugeV3(gauge).getRates(tokens); // U:[PQK-7]

192:         uint256 quotaRevenue; // U:[PQK-7]

200:             TokenQuotaParams storage tokenQuotaParams = totalQuotaParams[token]; // U:[PQK-7]

207:             ); // U:[PQK-7]

209:             tokenQuotaParams.rate = rate; // U:[PQK-7]

211:             quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR; // U:[PQK-7]

213:             emit UpdateTokenQuotaRate(token, rate); // U:[PQK-7]

216:                 ++i;

220:         IPoolV3(pool).setQuotaRevenue(quotaRevenue); // U:[PQK-7]

221:         lastQuotaRateUpdate = uint40(block.timestamp); // U:[PQK-7]

231:         configuratorOnly // U:[PQK-2]

234:             gauge = _gauge; // U:[PQK-8]

235:             emit SetGauge(_gauge); // U:[PQK-8]

245:         configuratorOnly // U:[PQK-2]

274:             revert TokenIsNotQuotedException(); // U:[PQK-14]

280:         if (msg.sender != gauge) revert CallerNotGaugeException(); // U:[PQK-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

6: import {SafeCast} from "@openzeppelin/contracts/utils/math/SafeCast.sol";

7: import {RAY, SECONDS_PER_YEAR, PERCENTAGE_FACTOR} from "@gearbox-protocol/core-v2/contracts/libraries/Constants.sol";

9: uint192 constant RAY_DIVIDED_BY_PERCENTAGE = uint192(RAY / PERCENTAGE_FACTOR);

24:                 uint256(cumulativeIndexLU) +

25:                     (RAY_DIVIDED_BY_PERCENTAGE * (block.timestamp - lastQuotaRateUpdate) * rate) /

27:             ); // U:[QL-1]

37:         return uint128((uint256(quoted) * (cumulativeIndexNow - cumulativeIndexLU)) / RAY); // U:[QL-2]

42:         return (change * int256(uint256(rate))) / int16(PERCENTAGE_FACTOR);

56:             uint96 maxQuotaCapacity = limit - totalQuoted;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

30:     assembly ("memory-safe") {

38:     assembly ("memory-safe") {

45:     assembly ("memory-safe") {

52:     assembly ("memory-safe") {

59:     assembly ("memory-safe") {

67:     assembly ("memory-safe") {

76:         z = int256(x) * y;

77:         if (int256(x) < 0 || (y != 0 && z / y != int256(x))) revert Math__mul_overflow_signed();

84:     assembly ("memory-safe") {

99:         z = mul(x, y) / int256(WAD);

122:     assembly ("memory-safe") {

154:         assembly ("memory-safe") {

172:                     let half := div(b, 2) // for rounding.

210:     return wexp((wln(x) * y) / int256(WAD));

219:         if (x <= -42139678854452767551) return r;

226:                 mstore(0x00, 0xa37bfec9) // `ExpOverflow()`.

234:         x = (x << 78) / 5 ** 18;

239:         int256 k = ((x << 96) / 54916777467707473351141471128 + 2 ** 95) >> 96;

240:         x = x - k * 54916777467707473351141471128;

246:         int256 y = x + 1346386616545796478920950773328;

247:         y = ((y * x) >> 96) + 57155421227552351082224309758442;

248:         int256 p = y + x - 94201549194550492254356042504812;

249:         p = ((p * y) >> 96) + 28719021644029726153956944680412240;

250:         p = p * x + (4385272521454847904659076985693276 << 96);

253:         int256 q = x - 2855989394907223263936484059900;

254:         q = ((q * x) >> 96) + 50020603652535783019961831881945;

255:         q = ((q * x) >> 96) - 533845033583426703283633433725380;

256:         q = ((q * x) >> 96) + 3604857256930695427073651918091429;

257:         q = ((q * x) >> 96) - 14423608567350463180887372962807573;

258:         q = ((q * x) >> 96) + 26449188498355588339934803723976023;

276:         r = int256((uint256(r) * 3822833074963236453042738258902158003155416615667) >> uint256(195 - k));

287:                 mstore(0x00, 0x1615e638) // `LnWadUndefined()`.

322:         int256 p = x + 3273285459638523848632254066296;

323:         p = ((p * x) >> 96) + 24828157081833163892658089445524;

324:         p = ((p * x) >> 96) + 43456485725739037958740375743393;

325:         p = ((p * x) >> 96) - 11111509109440967052023855526967;

326:         p = ((p * x) >> 96) - 45023709667254063763336534515857;

327:         p = ((p * x) >> 96) - 14706773417378608786704636184526;

328:         p = p * x - (795164235651350426258249787498 << 96);

332:         int256 q = x + 5573035233440673466300451813936;

333:         q = ((q * x) >> 96) + 71694874799317883764090561454958;

334:         q = ((q * x) >> 96) + 283447036172924575727196451306956;

335:         q = ((q * x) >> 96) + 401686690394027663651624208769553;

336:         q = ((q * x) >> 96) + 204048457590392012362485061816622;

337:         q = ((q * x) >> 96) + 31853899698501571402653359427138;

338:         q = ((q * x) >> 96) + 909429971244387300277376558375;

356:         r *= 1677202110996718588342820967067443963516166;

358:         r += 16597577552685614221487285958193947469193820559219878177908093499208371 * (159 - t);

360:         r += 600920179829731861736702779321621459595472258049074101567377883020018308;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/utils/Pause.sol

4: import {AccessControl} from "@openzeppelin/contracts/access/AccessControl.sol";

5: import {Pausable} from "@openzeppelin/contracts/security/Pausable.sol";

7: import {IPause} from "../interfaces/IPause.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Pause.sol)

```solidity
File: ./src/utils/Permission.sol

4: import "../interfaces/IPermission.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

41:     _revert(errorCode, 0x42414c); // This is the raw byte representation of "BAL"

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

```solidity
File: ./src/vendor/IBalancerVault.sol

3: import {IAsset} from "src/vendor/IAsset.sol";

98:     MANAGEMENT_FEE_TOKENS_OUT // for InvestmentPool

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IBalancerVault.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

25:     if (_start + 20 < _start) revert UniswapV3Router_toAddress_overflow();

26:     if (_bytes.length < _start + 20) revert UniswapV3Router_toAddress_outOfBounds();

41:     token = toAddress(path, path.length - 20);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

```solidity
File: ./src/vendor/Imports.sol

4: import {PRBProxyRegistry} from "../prb-proxy/PRBProxyRegistry.sol";

6: import {TransparentUpgradeableProxy} from "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

7: import {ProxyAdmin} from "@openzeppelin/contracts/proxy/transparent/ProxyAdmin.sol";

9: import {IAllowanceTransfer} from "permit2/interfaces/IAllowanceTransfer.sol";

10: import {PendleMarket} from "pendle/core/Market/PendleMarket.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/Imports.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

17: import "src/vendor/BalancerErrors.sol";

18: import {IVault} from "src/vendor/IBalancerVault.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="GAS-8"></a>[GAS-8] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*Instances (10)*:
```solidity
File: ./src/PoolV3.sol

202:         return IERC20(underlyingToken).balanceOf(address(this)); // U:[LP-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Treasury.sol

69:         uint256 amount = token.balanceOf(address(this));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

71:                 uint256 accrued = IERC20(tokens[i]).balanceOf(vault) - lastBalance;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/BaseAction.sol

21:         (bool success, bytes memory returnData) = to.delegatecall(data);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/BaseAction.sol)

```solidity
File: ./src/proxy/PoolAction.sol

266:         uint256 balanceBefore = IERC20(assets[tmpOutIndex]).balanceOf(poolActionParams.recipient);

280:         return IERC20(assets[tmpOutIndex]).balanceOf(poolActionParams.recipient) - balanceBefore;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction4626.sol

122:             addCollateralAmount = IERC20(underlyingToken).balanceOf(address(this));

156:             tokenOut = IERC20(IERC4626(leverParams.collateralToken).asset()).balanceOf(address(this));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction4626.sol)

```solidity
File: ./src/proxy/PositionActionPendle.sol

101:         addCollateralAmount = ICDPVault(leverParams.vault).token().balanceOf(address(this));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionActionPendle.sol)

```solidity
File: ./src/proxy/SwapAction.sol

370:             input.netTokenIn = IERC20(input.tokenIn).balanceOf(address(this));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

### <a name="GAS-9"></a>[GAS-9] Stack variable used as a cheaper cache for a state variable is only used once
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the **3 gas** the extra stack assignment would spend

*Instances (6)*:
```solidity
File: ./src/CDPVault.sol

519:         VaultConfig memory config = vaultConfig;

587:         VaultConfig memory config = vaultConfig;

658:         VaultConfig memory config = vaultConfig;

659:         LiquidationConfig memory liqConfig_ = liquidationConfig;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/StakingLPEth.sol

135:         uint24 previousDuration = cooldownDuration;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

193:         uint256 timestampLU = lastQuotaRateUpdate;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="GAS-10"></a>[GAS-10] State variables only set in the constructor should be declared `immutable`
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around **20 000 gas** per variable) and replace the expensive storage-reading operations (around **2100 gas** per reading) to a less expensive value reading (**3 gas**)

*Instances (42)*:
```solidity
File: ./src/CDPVault.sol

180:         pool = constants.pool;

181:         oracle = constants.oracle;

183:         tokenScale = constants.tokenScale;

185:         poolUnderlying = IERC20(pool.underlyingToken());

186:         poolUnderlyingScale = 10 ** IERC20Metadata(address(poolUnderlying)).decimals();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

51:         pool = pool_;

52:         underlyingToken = IERC20(pool.underlyingToken());

53:         protocolFee = protocolFee_;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

35:         token = IERC20(_token);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

175:         WETH = IWETH(weth_);

176:         addressProvider = addressProvider_; // U:[LP-1B]

177:         underlyingToken = underlyingToken_; // U:[LP-1B]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

19:         STAKING_VAULT = _stakingVault;

20:         lpETH = IERC20(_lpEth);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

59:         silo = new Silo(address(this), _liquidityPool);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

110:         flashlender = IFlashlender(flashlender_);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

63:         aggregator = aggregator_;

64:         stalePeriod = stalePeriod_;

65:         aggregatorScale = 10 ** uint256(aggregator.decimals());

66:         market = IPMarket(market_);

67:         twapWindow = twap_;

68:         ptOracle = IPPtOracle(ptOracle_);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

39:         market = IPendleMarketV3(_market);

40:         vault = _vault;

41:         proxyRegistry = IPRBProxyRegistry(_proxyRegistry);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/PoolAction.sol

62:         balancerVault = IVault(balancerVault_);

63:         pendleRouter = IPActionAddRemoveLiqV3(_pendleRouter);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

124:         flashlender = IFlashlender(flashlender_);

125:         pool = flashlender.pool();

126:         vaultRegistry = IVaultRegistry(vaultRegistry_);

127:         underlyingToken = IERC20(pool.underlyingToken());

128:         self = address(this);

129:         swapAction = SwapAction(swapAction_);

130:         poolAction = PoolAction(poolAction_);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

83:         balancerVault = balancerVault_;

84:         uniRouter = uniRouter_;

85:         pendleRouter = pendleRouter_;

86:         kyberRouter = kyberRouter_;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

59:         pool = _pool; // U:[GA-01]

60:         voter = _voter; // U:[GA-01]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

82:         pool = _pool; // U:[PQK-1]

83:         underlying = IPoolV3(_pool).asset(); // U:[PQK-1]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="GAS-11"></a>[GAS-11] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (20)*:
```solidity
File: ./src/CDPVault.sol

211:     function setParameter(bytes32 parameter, uint256 data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {

224:     function setParameter(bytes32 parameter, address data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {

851:     function recoverERC20(address tokenAddress, address to, uint256 tokenAmount) external onlyRole(DEFAULT_ADMIN_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Locking.sol

40:     function setCooldownPeriod(uint256 _cooldownPeriod) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/Silo.sol

28:     function withdraw(address to, uint256 amount) external onlyStakingVault {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

130:     function setCooldownDuration(uint24 duration) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

45:     function moveFunds(address treasury, IERC20 token) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

55:     function moveFunds(address treasury, IERC20[] calldata tokens) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

39:     function addVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

49:     function removeVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {

70:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {}

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

90:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

140:     function deploy() external override onlyNonProxyOwner(msg.sender) returns (IPRBProxy proxy) {

153:     function deployFor(address user) external override onlyNonProxyOwner(user) returns (IPRBProxy proxy) {

176:     function installPlugin(IPRBProxyPlugin plugin) external override onlyCallerWithProxy {

181:     function setPermission(address envoy, address target, bool permission) external override onlyCallerWithProxy {

188:     function uninstallPlugin(IPRBProxyPlugin plugin) external override onlyCallerWithProxy {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/utils/Pause.sol

30:     function pause() external onlyRole(PAUSER_ROLE) {

36:     function unpause() external onlyRole(PAUSER_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Pause.sol)

### <a name="GAS-12"></a>[GAS-12] `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)
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

*Instances (8)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Treasury.sol

57:         for (uint256 i = 0; i < len; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

46:         for (uint256 i = 0; i < _tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

84:             for (uint256 i = 0; i < tokens.length; i++) {

97:         for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/PoolAction.sol

229:                 i++;

265:         for (uint256 i = 0; i <= tmpOutIndex; i++) if (assets[i] == bpt) tmpOutIndex++;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

### <a name="GAS-13"></a>[GAS-13] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (12)*:
```solidity
File: ./src/Flashlender.sol

19:     bytes32 public constant CALLBACK_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");

20:     bytes32 public constant CALLBACK_SUCCESS_CREDIT = keccak256("CreditFlashBorrower.onCreditFlashLoan");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/PoolV3.sol

65:     uint256 public constant override version = 3_00;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/VaultRegistry.sol

14:     bytes32 public constant VAULT_MANAGER_ROLE = keccak256("VAULT_MANAGER_ROLE");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

106:     bytes32 public constant CALLBACK_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");

107:     bytes32 public constant CALLBACK_SUCCESS_CREDIT = keccak256("CreditFlashBorrower.onCreditFlashLoan");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

35:     string public constant override VERSION = "4.0.1";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/proxy/PositionAction.sol

73:     bytes32 public constant CALLBACK_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");

74:     bytes32 public constant CALLBACK_SUCCESS_CREDIT = keccak256("CreditFlashBorrower.onCreditFlashLoan");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/TransferAction.sol

39:     address public constant permit2 = 0x000000000022D473030F116dDEE9F6B43aC78BA3;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/TransferAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

29:     uint256 public constant override version = 3_00;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

37:     uint256 public constant override version = 3_00;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="GAS-14"></a>[GAS-14] Superfluous event fields
`block.timestamp` and `block.number` are added to event information by default so adding them manually wastes gas

*Instances (1)*:
```solidity
File: ./src/Locking.sol

32:     event CooldownInitiated(address indexed user, uint256 timestamp, uint256 amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

### <a name="GAS-15"></a>[GAS-15] Increments/decrements can be unchecked in for-loops
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

*Instances (10)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Treasury.sol

57:         for (uint256 i = 0; i < len; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

46:         for (uint256 i = 0; i < _tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

63:             for (uint256 i = 0; i < tokens.length; ++i) {

84:             for (uint256 i = 0; i < tokens.length; i++) {

97:         for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

62:         for (uint256 i = 0; i < tokens.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/proxy/PoolAction.sol

265:         for (uint256 i = 0; i <= tmpOutIndex; i++) if (assets[i] == bpt) tmpOutIndex++;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

102:             for (uint256 i; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

### <a name="GAS-16"></a>[GAS-16] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (27)*:
```solidity
File: ./src/CDPVault.sol

317:         if (deltaCollateral > 0) {

431:             ((deltaDebt > 0 || deltaCollateral < 0) && !hasPermission(owner, msg.sender)) ||

433:             (deltaCollateral > 0 && !hasPermission(collateralizer, msg.sender)) ||

439:         if (deltaDebt > 0 || deltaCollateral != 0){

451:         if (deltaDebt > 0) {

509:         if (deltaCollateral > 0) {

524:             (deltaDebt > 0 || deltaCollateral < 0) &&

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Locking.sol

72:         if (cooldownPeriod > 0) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

591:         if (profit > 0) {

593:         } else if (loss > 0) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

143:         if (_totalSupply > 0 && _totalSupply < MIN_SHARES) revert MinSharesViolation();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

70:         if (amount > 0) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

105:             isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

130:             isValid = (answer > 0 && block.timestamp - updatedAt <= stalePeriod);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

39:         if (deltaCollateral > 0) _totalShares = _totalShares + deltaCollateral.toUint256();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

2: pragma solidity >=0.8.18;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/prb-proxy/interfaces/IPRBProxyRegistry.sol

2: pragma solidity >=0.8.4;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/interfaces/IPRBProxyRegistry.sol)

```solidity
File: ./src/proxy/PositionAction.sol

339:         if (upFrontAmount > 0) {

491:             if (residualAmount > 0) {

512:             if (residualAmount > 0) {

670:             if (remainder > 0) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/utils/Math.sol

62:     if ((y > 0 && z < x) || (y < 0 && z > x)) revert Math__add_overflow_signed();

70:     if ((y > 0 && z > x) || (y < 0 && z < x)) revert Math__sub_overflow_signed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

15: pragma solidity >=0.7.1 <0.9.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

```solidity
File: ./src/vendor/IAsset.sol

15: pragma solidity >=0.7.0 <0.9.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IAsset.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

25:     if (_start + 20 < _start) revert UniswapV3Router_toAddress_overflow();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

15: pragma solidity >=0.7.0 <0.9.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="GAS-17"></a>[GAS-17] `internal` functions not called by the contract should be removed
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*Instances (8)*:
```solidity
File: ./src/CDPVault.sol

796:     function calcAccruedInterest(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

113:     function approvePayback(uint256 amount) internal {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

255:     function isInitialised(TokenQuotaParams storage tokenQuotaParams) internal view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

17:     function cumulativeIndexSince(

31:     function calcAccruedQuotaInterest(

41:     function calcQuotaRevenueChange(uint16 rate, int256 change) internal pure returns (int256) {

46:     function calcActualQuotaChange(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

37:     function ensureNotInVaultContext(IVault vault) internal view {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="GAS-18"></a>[GAS-18] WETH address definition can be use directly
WETH is a wrap Ether contract with a specific address in the Ethereum network, giving the option to define it may cause false recognition, it is healthier to define it directly.

    Advantages of defining a specific contract directly:
    
    It saves gas,
    Prevents incorrect argument definition,
    Prevents execution on a different chain and re-signature issues,
    WETH Address : 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2

*Instances (1)*:
```solidity
File: ./src/PoolV3.sol

68:     IWETH public immutable WETH;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Replace `abi.encodeWithSignature` and `abi.encodeWithSelector` with `abi.encodeCall` which keeps the code typo/type safe | 14 |
| [NC-2](#NC-2) | Missing checks for `address(0)` when assigning values to address state variables | 12 |
| [NC-3](#NC-3) | Array indices should be referenced via `enum`s rather than via numeric literals | 8 |
| [NC-4](#NC-4) | `require()` should be used instead of `assert()` | 1 |
| [NC-5](#NC-5) | Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked` | 1 |
| [NC-6](#NC-6) | Constants should be in CONSTANT_CASE | 1 |
| [NC-7](#NC-7) | `constant`s should be defined rather than using magic numbers | 57 |
| [NC-8](#NC-8) | Control structures do not follow the Solidity Style Guide | 152 |
| [NC-9](#NC-9) | Critical Changes Should Use Two-step Procedure | 1 |
| [NC-10](#NC-10) | Default Visibility for constants | 10 |
| [NC-11](#NC-11) | Delete rogue `console.log` imports | 3 |
| [NC-12](#NC-12) | Consider disabling `renounceOwnership()` | 2 |
| [NC-13](#NC-13) | Draft Dependencies | 1 |
| [NC-14](#NC-14) | Unused `error` definition | 5 |
| [NC-15](#NC-15) | Event is never emitted | 1 |
| [NC-16](#NC-16) | Event missing indexed field | 7 |
| [NC-17](#NC-17) | Events that mark critical parameter changes should contain both the old and the new value | 14 |
| [NC-18](#NC-18) | Function ordering does not follow the Solidity style guide | 15 |
| [NC-19](#NC-19) | Functions should not be longer than 50 lines | 298 |
| [NC-20](#NC-20) | Change uint to uint256 | 3 |
| [NC-21](#NC-21) | Interfaces should be defined in separate files from their usage | 12 |
| [NC-22](#NC-22) | Lack of checks in setters | 14 |
| [NC-23](#NC-23) | Lines are too long | 1 |
| [NC-24](#NC-24) | Missing Event for critical parameters change | 10 |
| [NC-25](#NC-25) | NatSpec is completely non-existent on functions that should have them | 14 |
| [NC-26](#NC-26) | Incomplete NatSpec: `@param` is missing on actually documented functions | 10 |
| [NC-27](#NC-27) | Incomplete NatSpec: `@return` is missing on actually documented functions | 10 |
| [NC-28](#NC-28) | File's first line is not an SPDX Identifier | 1 |
| [NC-29](#NC-29) | Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor | 21 |
| [NC-30](#NC-30) | Constant state variables defined more than once | 9 |
| [NC-31](#NC-31) | Consider using named mappings | 13 |
| [NC-32](#NC-32) | `address`s shouldn't be hard-coded | 1 |
| [NC-33](#NC-33) | Numeric values having to do with time should use time units for readability | 4 |
| [NC-34](#NC-34) | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 1 |
| [NC-35](#NC-35) | Adding a `return` statement when the function defines a named return variable, is redundant | 25 |
| [NC-36](#NC-36) | Take advantage of Custom Error's return value property | 103 |
| [NC-37](#NC-37) | Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`) | 1 |
| [NC-38](#NC-38) | Contract does not follow the Solidity style guide's suggested layout ordering | 17 |
| [NC-39](#NC-39) | Use Underscores for Number Literals (add an underscore every 3 digits) | 30 |
| [NC-40](#NC-40) | Internal and private variables and functions names should begin with an underscore | 39 |
| [NC-41](#NC-41) | Event is missing `indexed` fields | 26 |
| [NC-42](#NC-42) | Constants should be defined rather than using magic numbers | 8 |
| [NC-43](#NC-43) | `public` functions not called by the contract should be declared `external` instead | 4 |
| [NC-44](#NC-44) | Variables need not be initialized to zero | 16 |
### <a name="NC-1"></a>[NC-1] Replace `abi.encodeWithSignature` and `abi.encodeWithSelector` with `abi.encodeCall` which keeps the code typo/type safe
When using `abi.encodeWithSignature`, it is possible to include a typo for the correct function signature.
When using `abi.encodeWithSignature` or `abi.encodeWithSelector`, it is also possible to provide parameters that are not of the correct type for the function.

To avoid these pitfalls, it would be best to use [`abi.encodeCall`](https://solidity-by-example.org/abi-encode/) instead.

*Instances (14)*:
```solidity
File: ./src/proxy/PositionAction.sol

419:                 abi.encodeWithSelector(swapAction.swap.selector, leverParams.auxSwap)

427:             abi.encodeWithSelector(swapAction.swap.selector, leverParams.primarySwap)

484:                 abi.encodeWithSelector(swapAction.swap.selector, leverParams.primarySwap)

504:                 abi.encodeWithSelector(swapAction.swap.selector, leverParams.primarySwap)

518:                         abi.encodeWithSelector(swapAction.swap.selector, leverParams.auxSwap)

583:                 abi.encodeWithSelector(swapAction.swap.selector, auxSwap)

612:             _delegateCall(address(swapAction), abi.encodeWithSelector(swapAction.swap.selector, creditParams.auxSwap));

663:             abi.encodeWithSelector(swapAction.transferAndSwap.selector, sender, permitParams, swapParams)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/PositionAction4626.sol

119:             _delegateCall(address(poolAction), abi.encodeWithSelector(poolAction.join.selector, poolActionParams));

153:                 abi.encodeWithSelector(poolAction.exit.selector, leverParams.auxAction)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction4626.sol)

```solidity
File: ./src/proxy/PositionActionPendle.sol

78:                 abi.encodeWithSelector(poolAction.exit.selector, poolActionParams)

99:             _delegateCall(address(poolAction), abi.encodeWithSelector(poolAction.join.selector, leverParams.auxAction));

120:                 abi.encodeWithSelector(poolAction.exit.selector, leverParams.auxAction)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionActionPendle.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

79:             abi.encodeWithSelector(vault.manageUserBalance.selector, 0)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="NC-2"></a>[NC-2] Missing checks for `address(0)` when assigning values to address state variables

*Instances (12)*:
```solidity
File: ./src/PoolV3.sol

176:         addressProvider = addressProvider_; // U:[LP-1B]

177:         underlyingToken = underlyingToken_; // U:[LP-1B]

182:         interestRateModel = interestRateModel_; // U:[LP-1B]

797:         interestRateModel = newInterestRateModel; // U:[LP-22B]

816:         poolQuotaKeeper = newPoolQuotaKeeper; // U:[LP-23D]

826:         treasury = treasury_;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

19:         STAKING_VAULT = _stakingVault;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

40:         vault = _vault;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/SwapAction.sol

86:         kyberRouter = kyberRouter_;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

59:         pool = _pool; // U:[GA-01]

60:         voter = _voter; // U:[GA-01]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

82:         pool = _pool; // U:[PQK-1]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="NC-3"></a>[NC-3] Array indices should be referenced via `enum`s rather than via numeric literals

*Instances (8)*:
```solidity
File: ./src/proxy/ERC165Plugin.sol

20:         methods[0] = this.onERC1155Received.selector;

21:         methods[1] = this.onERC1155BatchReceived.selector;

22:         methods[2] = this.onERC721Received.selector;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/ERC165Plugin.sol)

```solidity
File: ./src/proxy/PoolAction.sol

107:                     _transferFrom(input.tokenIn, from, address(this), input.netTokenIn, permitParams[0]);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

272:         IERC20(assetIn).forceApprove(address(balancerVault), amountToApprove);

281:                     FundManagement({

289:                 )[pathLength]

439:                 revert(add(32, errMsg), mload(errMsg))

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

### <a name="NC-4"></a>[NC-4] `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Additionally, a require statement (or a custom error) are more friendly in terms of understanding what happened."

*Instances (1)*:
```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

58:         assert(user != address(0) && user != address(this));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

### <a name="NC-5"></a>[NC-5] Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked`
Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

Solidity version 0.8.12 introduces `string.concat()` (vs `abi.encodePacked(<str>,<str>), which catches concatenation errors (in the event of a `bytes` data mixed in the concatenation)`)

*Instances (1)*:
```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

221:         bytes32 salt = bytes32(abi.encodePacked(owner));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

### <a name="NC-6"></a>[NC-6] Constants should be in CONSTANT_CASE
For `constant` variable names, each word should use all capital letters, with underscores separating each word (CONSTANT_CASE)

*Instances (1)*:
```solidity
File: ./src/proxy/TransferAction.sol

39:     address public constant permit2 = 0x000000000022D473030F116dDEE9F6B43aC78BA3;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/TransferAction.sol)

### <a name="NC-7"></a>[NC-7] `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*Instances (57)*:
```solidity
File: ./src/CDPVault.sol

186:         poolUnderlyingScale = 10 ** IERC20Metadata(address(poolUnderlying)).decimals();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/StakingLPEth.sol

34:     uint24 public MAX_COOLDOWN_DURATION = 30 days;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

65:         aggregatorScale = 10 ** uint256(aggregator.decimals());

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/utils/Math.sol

18:     if (x >= 1 << 255) revert Math__toInt256_overflow();

24:     if (x >= 1 << 64) revert Math__toUint64_overflow();

165:                     switch mod(n, 2)

172:                     let half := div(b, 2) // for rounding.

174:                         n := div(n, 2)

176:                         n := div(n, 2)

187:                         if mod(n, 2) {

225:             if iszero(slt(x, 135305999368893231589)) {

234:         x = (x << 78) / 5 ** 18;

239:         int256 k = ((x << 96) / 54916777467707473351141471128 + 2 ** 95) >> 96;

240:         x = x - k * 54916777467707473351141471128;

246:         int256 y = x + 1346386616545796478920950773328;

247:         y = ((y * x) >> 96) + 57155421227552351082224309758442;

248:         int256 p = y + x - 94201549194550492254356042504812;

249:         p = ((p * y) >> 96) + 28719021644029726153956944680412240;

250:         p = p * x + (4385272521454847904659076985693276 << 96);

253:         int256 q = x - 2855989394907223263936484059900;

254:         q = ((q * x) >> 96) + 50020603652535783019961831881945;

255:         q = ((q * x) >> 96) - 533845033583426703283633433725380;

256:         q = ((q * x) >> 96) + 3604857256930695427073651918091429;

257:         q = ((q * x) >> 96) - 14423608567350463180887372962807573;

258:         q = ((q * x) >> 96) + 26449188498355588339934803723976023;

276:         r = int256((uint256(r) * 3822833074963236453042738258902158003155416615667) >> uint256(195 - k));

318:         x = int256(uint256(x << uint256(t)) >> 159);

322:         int256 p = x + 3273285459638523848632254066296;

323:         p = ((p * x) >> 96) + 24828157081833163892658089445524;

324:         p = ((p * x) >> 96) + 43456485725739037958740375743393;

325:         p = ((p * x) >> 96) - 11111509109440967052023855526967;

326:         p = ((p * x) >> 96) - 45023709667254063763336534515857;

327:         p = ((p * x) >> 96) - 14706773417378608786704636184526;

328:         p = p * x - (795164235651350426258249787498 << 96);

332:         int256 q = x + 5573035233440673466300451813936;

333:         q = ((q * x) >> 96) + 71694874799317883764090561454958;

334:         q = ((q * x) >> 96) + 283447036172924575727196451306956;

335:         q = ((q * x) >> 96) + 401686690394027663651624208769553;

336:         q = ((q * x) >> 96) + 204048457590392012362485061816622;

337:         q = ((q * x) >> 96) + 31853899698501571402653359427138;

338:         q = ((q * x) >> 96) + 909429971244387300277376558375;

356:         r *= 1677202110996718588342820967067443963516166;

358:         r += 16597577552685614221487285958193947469193820559219878177908093499208371 * (159 - t);

360:         r += 600920179829731861736702779321621459595472258049074101567377883020018308;

362:         r >>= 174;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

64:         let units := add(mod(errorCode, 10), 0x30)

66:         errorCode := div(errorCode, 10)

67:         let tenths := add(mod(errorCode, 10), 0x30)

69:         errorCode := div(errorCode, 10)

70:         let hundreds := add(mod(errorCode, 10), 0x30)

93:         mstore(0x24, 7)

99:         revert(0, 100)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

25:     if (_start + 20 < _start) revert UniswapV3Router_toAddress_overflow();

26:     if (_bytes.length < _start + 20) revert UniswapV3Router_toAddress_outOfBounds();

40:     if (path.length < 20) revert UniswapV3Router_decodeLastToken_invalidPath();

41:     token = toAddress(path, path.length - 20);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

78:         (, bytes memory revertData) = address(vault).staticcall{gas: 10_000}(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="NC-8"></a>[NC-8] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*Instances (152)*:
```solidity
File: ./src/CDPVault.sol

140:     event ModifyPosition(address indexed position, uint256 debt, uint256 collateral, uint256 totalDebt);

141:     event ModifyCollateralAndDebt(

162:     error CDPVault__modifyPosition_debtFloor();

163:     error CDPVault__modifyCollateralAndDebt_notSafe();

164:     error CDPVault__modifyCollateralAndDebt_noPermission();

165:     error CDPVault__modifyCollateralAndDebt_maxUtilizationRatio();

212:         if (parameter == "debtFloor") vaultConfig.debtFloor = uint128(data);

213:         else if (parameter == "liquidationRatio") vaultConfig.liquidationRatio = uint64(data);

214:         else if (parameter == "liquidationPenalty") liquidationConfig.liquidationPenalty = uint64(data);

215:         else if (parameter == "liquidationDiscount") liquidationConfig.liquidationDiscount = uint64(data);

225:         if (parameter == "rewardController") rewardController = IChefIncentivesController(data);

226:         else if (parameter == "rewardManager") rewardManager = IRewardManager(data);

356:     function _modifyPosition(

374:         if (position.debt != 0 && position.debt < uint256(vaultConfig.debtFloor))

375:             revert CDPVault__modifyPosition_debtFloor();

392:         if (address(rewardManager) != address(0)) _handleTokenRewards(owner, collateralBefore, deltaCollateral);

394:         emit ModifyPosition(owner, position.debt, position.collateral, totalDebt_);

422:     function modifyCollateralAndDebt(

429:         if (

436:         ) revert CDPVault__modifyCollateralAndDebt_noPermission();

439:         if (deltaDebt > 0 || deltaCollateral != 0){

517:         position = _modifyPosition(owner, position, newDebt, newCumulativeIndex, deltaCollateral, totalDebt);

523:         if (

526:         ) revert CDPVault__modifyCollateralAndDebt_notSafe();

532:         emit ModifyCollateralAndDebt(owner, collateralizer, creditor, deltaCollateral, deltaDebt);

584:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

597:         if (spotPrice_ == 0) revert CDPVault__liquidatePosition_invalidSpotPrice();

600:         if (calcTotalDebt(debtData) > wmul(position.collateral, discountedPrice)) revert CDPVault__BadDebt();

606:         if (takeCollateral > position.collateral) revert CDPVault__tooHighRepayAmount();

609:         if (_isCollateralized(calcTotalDebt(debtData), wmul(position.collateral, spotPrice_), config.liquidationRatio))

635:         position = _modifyPosition(owner, position, newDebt, newCumulativeIndex, -toInt256(takeCollateral), totalDebt);

655:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

665:         if (spotPrice_ == 0) revert CDPVault__liquidatePosition_invalidSpotPrice();

667:         if (_isCollateralized(calcTotalDebt(debtData), wmul(position.collateral, spotPrice_), config.liquidationRatio))

673:         if (calcTotalDebt(debtData) <= wmul(position.collateral, discountedPrice)) revert CDPVault__noBadDebt();

676:         if (takeCollateral < position.collateral) revert CDPVault__repayAmountNotEnough();

693:         position = _modifyPosition(

801:         if (amount == 0) return 0;

852:         if (tokenAddress == address(token)) revert CDPVault__recoverERC20_invalidToken();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

6: import {IFlashlender, IERC3156FlashBorrower, ICreditFlashBorrower} from "./interfaces/IFlashlender.sol";

76:         if (token != address(underlyingToken)) revert Flash__flashFee_unsupportedToken();

93:         if (token != address(underlyingToken)) revert Flash__flashLoan_unsupportedToken();

101:         if (receiver.onFlashLoan(msg.sender, token, amount, fee, data) != CALLBACK_SUCCESS)

130:         if (receiver.onCreditFlashLoan(msg.sender, amount, fee, data) != CALLBACK_SUCCESS_CREDIT)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

48:         if (_amount == 0) revert AmountIsZero();

60:         if (_amount == 0) revert AmountIsZero();

61:         if (deposits[msg.sender].amount < _amount) revert InsufficientBalanceForCooldown();

73:             if (deposits[msg.sender].cooldownAmount == 0) revert NoTokensInCooldown();

74:             if (block.timestamp < deposits[msg.sender].cooldownStart + cooldownPeriod) revert CooldownPeriodNotPassed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

117:         _revertIfCallerIsNotPoolQuotaKeeper();

123:         _revertIfCallerNotCreditManager();

131:             _revertIfLocked();

137:         if (msg.sender != poolQuotaKeeper) revert CallerNotPoolQuotaKeeperException(); // U:[LP-2C]

148:         if (locked) revert PoolV3LockedException(); // U:[LP-2C]

511:         if (borrowable == 0) return 0; // U:[LP-12]

514:         if (borrowable == 0) return 0; // U:[LP-12]

628:         if (borrowed >= limit) return 0;

649:         if (assets == 0) return baseInterestRate_;

659:         if (block.timestamp == timestampLU) return _baseInterestIndexLU; // U:[LP-16]

671:         if (block.timestamp == timestampLU) return 0; // U:[LP-17]

762:         if (block.timestamp == timestampLU) return 0; // U:[LP-21]

877:         if (newWithdrawFee == withdrawFee) return;

905:         if (newLimit == _totalDebt.limit) return;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

24:         if (msg.sender != STAKING_VAULT) revert OnlyStakingVault();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

44:         if (cooldownDuration != 0) revert OperationNotAllowed();

50:         if (cooldownDuration == 0) revert OperationNotAllowed();

105:         if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

118:         if (shares > maxRedeem(msg.sender)) revert ExcessiveRedeemAmount();

143:         if (_totalSupply > 0 && _totalSupply < MIN_SHARES) revert MinSharesViolation();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/VaultRegistry.sol

40:         if (registeredVaults[vault]) revert VaultRegistry__addVault_vaultAlreadyRegistered();

50:         if (!registeredVaults[vault]) revert VaultRegistry__removeVault_vaultNotFound();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/interfaces/ICDM.sol

19:     function modifyBalance(address from, address to, uint256 amount) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDM.sol)

```solidity
File: ./src/interfaces/ICDPVault.sol

68:     function modifyCollateralAndDebt(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDPVault.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

104:     IFlashlender public immutable flashlender;

110:         flashlender = IFlashlender(flashlender_);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/interfaces/IPermission.sol

7:     function modifyPermission(address caller, bool allowed) external;

9:     function modifyPermission(address owner, address caller, bool allowed) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPermission.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

90:         if (!isValid) revert ChainlinkOracle__spot_invalidValue();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

91:         if (_getStatus()) revert PendleLPOracle__authorizeUpgrade_validStatus();

112:         if (!isValid) revert PendleLPOracle__spot_invalidValue();

114:         if (!isValidPtOracle) revert PendleLPOracle__validatePtOracle_invalidValue();

142:         if (status) return _validatePtOracle();

153:             if (!increaseCardinalityRequired && oldestObservationSatisfied) return true;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

34:         if (msg.sender != vault) revert OnlyVault();

53:         if (tokens.length == 0) return (tokens, indexes);

73:                 if (index == 0) index = INITIAL_REWARD_INDEX;

74:                 if (totalShares != 0) index += accrued.divDown(totalShares);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

39:         if (deltaCollateral > 0) _totalShares = _totalShares + deltaCollateral.toUint256();

45:         if (tokens.length == 0) return;

47:         if (user1 != address(0) && user1 != address(this))

71:             if (userIndex == index) continue;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

60:                                      MODIFIERS

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/proxy/BaseAction.sol

22:         if (!success) _revertBytes(returnData);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/BaseAction.sol)

```solidity
File: ./src/proxy/PoolAction.sol

262:         if (bptAmount != 0) IERC20(bpt).forceApprove(address(balancerVault), bptAmount);

265:         for (uint256 i = 0; i <= tmpOutIndex; i++) if (assets[i] == bpt) tmpOutIndex++;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

16: import {IFlashlender, IERC3156FlashBorrower, ICreditFlashBorrower} from "../interfaces/IFlashlender.sol";

80:     IFlashlender public immutable flashlender;

117:         if (

124:         flashlender = IFlashlender(flashlender_);

134:                                 MODIFIERS

139:         if (address(this) == self) revert PositionAction__onlyDelegatecall();

144:         if (!vaultRegistry.isVaultRegistered(vault)) revert PositionAction__unregisteredVault();

300:                 if (!success) _revertBytes(response);

324:         if (

331:         if (

341:                 IERC20(upFrontToken).safeTransfer(self, upFrontAmount); // if tokens are on the proxy then just transfer

348:         IPermission(leverParams.vault).modifyPermission(leverParams.position, self, true);

355:         IPermission(leverParams.vault).modifyPermission(leverParams.position, self, false);

371:         if (leverParams.primarySwap.recipient != self) revert PositionAction__decreaseLever_invalidPrimarySwap();

373:         IPermission(leverParams.vault).modifyPermission(leverParams.position, self, true);

392:         IPermission(leverParams.vault).modifyPermission(leverParams.position, self, false);

408:         if (msg.sender != address(flashlender)) revert PositionAction__onFlashLoan__invalidSender();

438:         ICDPVault(leverParams.vault).modifyCollateralAndDebt(

459:         if (msg.sender != address(flashlender)) revert PositionAction__onCreditFlashLoan__invalidSender();

468:         ICDPVault(leverParams.vault).modifyCollateralAndDebt(

493:                 ICDPVault(leverParams.vault).modifyCollateralAndDebt(

548:             if (

597:         ICDPVault(vault).modifyCollateralAndDebt(

629:             if (creditParams.auxSwap.recipient != address(this)) revert PositionAction__repay_InvalidAuxSwap();

646:         ICDPVault(vault).modifyCollateralAndDebt(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

239:             if (swapType == SwapType.EXACT_IN) outIncrement = 1;

263:             limits[0] = toInt256(amount); // positive signifies tokens going into the vault from the caller

264:             limits[pathLength] = -toInt256(limit); // negative signifies tokens going out of the vault to the caller

302:         if (!success) _revertBytes(result);

418:             if (swapParams.swapType == SwapType.EXACT_OUT){ 

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

68:         _revertIfCallerNotVoter(); // U:[GA-02]

105:                 if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-15]

145:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-10]

188:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-10]

196:             if (uv.votesLpSide < votes) revert InsufficientVotesException();

202:             if (uv.votesCaSide < votes) revert InsufficientVotesException();

292:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-06A, GA-06B]

297:         if (minRate == qrp.minRate && maxRate == qrp.maxRate) return;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

67:         _revertIfCallerNotGauge();

280:         if (msg.sender != gauge) revert CallerNotGaugeException(); // U:[PQK-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Math.sol

18:     if (x >= 1 << 255) revert Math__toInt256_overflow();

24:     if (x >= 1 << 64) revert Math__toUint64_overflow();

62:     if ((y > 0 && z < x) || (y < 0 && z > x)) revert Math__add_overflow_signed();

70:     if ((y > 0 && z > x) || (y < 0 && z < x)) revert Math__sub_overflow_signed();

77:         if (int256(x) < 0 || (y != 0 && z / y != int256(x))) revert Math__mul_overflow_signed();

219:         if (x <= -42139678854452767551) return r;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/utils/Permission.sol

11:     event ModifyPermission(address authorizer, address owner, address caller, bool grant);

18:     error Permission__modifyPermission_notPermitted();

40:         emit ModifyPermission(msg.sender, msg.sender, caller, permitted);

48:         if (owner != msg.sender && !_permittedAgents[owner][msg.sender])

49:             revert Permission__modifyPermission_notPermitted();

51:         emit ModifyPermission(msg.sender, owner, caller, permitted);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

25:     if (!condition) _revert(errorCode);

33:     if (!condition) _revert(errorCode, prefix);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

25:     if (_start + 20 < _start) revert UniswapV3Router_toAddress_overflow();

26:     if (_bytes.length < _start + 20) revert UniswapV3Router_toAddress_outOfBounds();

40:     if (path.length < 20) revert UniswapV3Router_decodeLastToken_invalidPath();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

### <a name="NC-9"></a>[NC-9] Critical Changes Should Use Two-step Procedure
The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference: <https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure>

**Recommended Mitigation Steps**

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

*Instances (1)*:
```solidity
File: ./src/oracle/ChainlinkOracle.sol

45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

### <a name="NC-10"></a>[NC-10] Default Visibility for constants
Some constants are using the default visibility. For readability, consider explicitly declaring them as `internal`.

*Instances (10)*:
```solidity
File: ./src/CDPVault.sol

45: bytes32 constant VAULT_CONFIG_ROLE = keccak256("VAULT_CONFIG_ROLE");

46: bytes32 constant VAULT_UNWINDER_ROLE = keccak256("VAULT_UNWINDER_ROLE");

71:     uint256 constant INDEX_PRECISION = 10 ** 9;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Treasury.sol

10: bytes32 constant FUNDS_ADMINISTRATOR_ROLE = keccak256("FUNDS_ADMINISTRATOR_ROLE");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/interfaces/IOracle.sol

5: bytes32 constant MANAGER_ROLE = keccak256("MANAGER_ROLE");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IOracle.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

81:                            USER-FACING CONSTANT FUNCTIONS

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/prb-proxy/interfaces/IPRBProxyRegistry.sol

82:                                  CONSTANT FUNCTIONS

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/interfaces/IPRBProxyRegistry.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

9: uint192 constant RAY_DIVIDED_BY_PERCENTAGE = uint192(RAY / PERCENTAGE_FACTOR);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

14: uint256 constant WAD = 1e18;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/utils/Pause.sol

10: bytes32 constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Pause.sol)

### <a name="NC-11"></a>[NC-11] Delete rogue `console.log` imports
These shouldn't be deployed in production

*Instances (3)*:
```solidity
File: ./src/pendle-rewards/RewardManager.sol

7: import {console} from "forge-std/console.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

11: import {console} from "forge-std/console.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/proxy/SwapAction.sol

17: import {console} from "forge-std/console.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

### <a name="NC-12"></a>[NC-12] Consider disabling `renounceOwnership()`
If the plan for your project does not include eventually giving up all ownership control, consider overwriting OpenZeppelin's `Ownable`'s `renounceOwnership()` function in order to disable it.

*Instances (2)*:
```solidity
File: ./src/Locking.sol

10: contract Locking is Ownable {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/StakingLPEth.sol

8: contract StakingLPEth is ERC4626, Ownable, ReentrancyGuard {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

### <a name="NC-13"></a>[NC-13] Draft Dependencies
Draft contracts have not received adequate security auditing or are liable to change with future developments.

*Instances (1)*:
```solidity
File: ./src/proxy/TransferAction.sol

5: import {IERC20Permit} from "@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/TransferAction.sol)

### <a name="NC-14"></a>[NC-14] Unused `error` definition
Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.

*Instances (5)*:
```solidity
File: ./src/CDPVault.sol

165:     error CDPVault__modifyCollateralAndDebt_maxUtilizationRatio();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

44:     error Flash__creditFlashLoan_unsupportedToken();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/PoolV3.sol

58:     error CallerNotManagerException();

61:     error WethTransferFailed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

39:     error ChainlinkOracle__authorizeUpgrade_validStatus();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

### <a name="NC-15"></a>[NC-15] Event is never emitted
The following are defined but never emitted. They can be removed to make the code cleaner.

*Instances (1)*:
```solidity
File: ./src/CDPVault.sol

150:     event LiquidatePosition(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="NC-16"></a>[NC-16] Event missing indexed field
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be indexed.

*Instances (7)*:
```solidity
File: ./src/Locking.sol

31:     event CooldownPeriodSet(uint256 cooldownPeriod);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/StakingLPEth.sol

40:     event CooldownDurationUpdated(uint24 previousDuration, uint24 newDuration);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

24:     event Treasury__moveFunds(address treasury);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/interfaces/IPoolV3.sol

31:     event SetTotalDebtLimit(uint256 limit);

40:     event SetWithdrawFee(uint256 fee);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolV3.sol)

```solidity
File: ./src/utils/Permission.sol

11:     event ModifyPermission(address authorizer, address owner, address caller, bool grant);

12:     event SetPermittedAgent(address owner, address agent, bool grant);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

### <a name="NC-17"></a>[NC-17] Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*Instances (14)*:
```solidity
File: ./src/CDPVault.sol

211:     function setParameter(bytes32 parameter, uint256 data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {
             if (parameter == "debtFloor") vaultConfig.debtFloor = uint128(data);
             else if (parameter == "liquidationRatio") vaultConfig.liquidationRatio = uint64(data);
             else if (parameter == "liquidationPenalty") liquidationConfig.liquidationPenalty = uint64(data);
             else if (parameter == "liquidationDiscount") liquidationConfig.liquidationDiscount = uint64(data);
             else revert CDPVault__setParameter_unrecognizedParameter();
             emit SetParameter(parameter, data);

224:     function setParameter(bytes32 parameter, address data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {
             if (parameter == "rewardController") rewardController = IChefIncentivesController(data);
             else if (parameter == "rewardManager") rewardManager = IRewardManager(data);
             else revert CDPVault__setParameter_unrecognizedParameter();
             emit SetParameter(parameter, data);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Locking.sol

40:     function setCooldownPeriod(uint256 _cooldownPeriod) external onlyOwner {
            cooldownPeriod = _cooldownPeriod;
            emit CooldownPeriodSet(_cooldownPeriod);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

789:     function setInterestRateModel(
             address newInterestRateModel
         )
             external
             override
             configuratorOnly // U:[LP-2C]
             nonZeroAddress(newInterestRateModel) // U:[LP-22A]
         {
             interestRateModel = newInterestRateModel; // U:[LP-22B]
             _updateBaseInterest(0, 0, false); // U:[LP-22B]
             emit SetInterestRateModel(newInterestRateModel); // U:[LP-22B]

804:     function setPoolQuotaKeeper(
             address newPoolQuotaKeeper
         )
             external
             override
             configuratorOnly // U:[LP-2C]
             nonZeroAddress(newPoolQuotaKeeper) // U:[LP-23A]
         {
             if (IPoolQuotaKeeperV3(newPoolQuotaKeeper).pool() != address(this)) {
                 revert IncompatiblePoolQuotaKeeperException(); // U:[LP-23C]
             }
     
             poolQuotaKeeper = newPoolQuotaKeeper; // U:[LP-23D]
     
             uint256 newQuotaRevenue = IPoolQuotaKeeperV3(poolQuotaKeeper).poolQuotaRevenue();
             _setQuotaRevenue(newQuotaRevenue); // U:[LP-23D]
     
             emit SetPoolQuotaKeeper(newPoolQuotaKeeper); // U:[LP-23D]

845:     function setCreditManagerDebtLimit(
             address creditManager,
             uint256 newLimit
         )
             external
             override
             controllerOnly // U:[LP-2C]
             nonZeroAddress(creditManager) // U:[LP-25A]
         {
             if (!_creditManagerSet.contains(creditManager)) {
                 if (address(this) != ICreditManagerV3(creditManager).pool()) {
                     revert IncompatibleCreditManagerException(); // U:[LP-25C]
                 }
                 _creditManagerSet.add(creditManager); // U:[LP-25D]
                 emit AddCreditManager(creditManager); // U:[LP-25D]
             }
             _creditManagerDebt[creditManager].limit = _convertToU128(newLimit); // U:[LP-25D]
             emit SetCreditManagerDebtLimit(creditManager, newLimit); // U:[LP-25D]

845:     function setCreditManagerDebtLimit(
             address creditManager,
             uint256 newLimit
         )
             external
             override
             controllerOnly // U:[LP-2C]
             nonZeroAddress(creditManager) // U:[LP-25A]
         {
             if (!_creditManagerSet.contains(creditManager)) {
                 if (address(this) != ICreditManagerV3(creditManager).pool()) {
                     revert IncompatibleCreditManagerException(); // U:[LP-25C]
                 }
                 _creditManagerSet.add(creditManager); // U:[LP-25D]
                 emit AddCreditManager(creditManager); // U:[LP-25D]

867:     function setWithdrawFee(
             uint256 newWithdrawFee
         )
             external
             override
             controllerOnly // U:[LP-2C]
         {
             if (newWithdrawFee > MAX_WITHDRAW_FEE) {
                 revert IncorrectParameterException(); // U:[LP-26A]
             }
             if (newWithdrawFee == withdrawFee) return;
     
             withdrawFee = newWithdrawFee.toUint16(); // U:[LP-26B]
             emit SetWithdrawFee(newWithdrawFee); // U:[LP-26B]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

130:     function setCooldownDuration(uint24 duration) external onlyOwner {
             if (duration > MAX_COOLDOWN_DURATION) {
                 revert InvalidCooldown();
             }
     
             uint24 previousDuration = cooldownDuration;
             cooldownDuration = duration;
             emit CooldownDurationUpdated(previousDuration, cooldownDuration);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

214:     /*//////////////////////////////////////////////////////////////////////////
                               INTERNAL NON-CONSTANT FUNCTIONS
         //////////////////////////////////////////////////////////////////////////*/
     
         /// @dev See the documentation for the user-facing functions that call this internal function.

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

219:     function setFrozenEpoch(bool status) external override configuratorOnly {
             if (status != epochFrozen) {
                 epochFrozen = status;
     
                 emit SetFrozenEpoch(status);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

184:     function updateRates()
             external
             override
             gaugeOnly // U:[PQK-3]
         {
             address[] memory tokens = quotaTokensSet.values();
             uint16[] memory rates = IGaugeV3(gauge).getRates(tokens); // U:[PQK-7]
     
             uint256 quotaRevenue; // U:[PQK-7]
             uint256 timestampLU = lastQuotaRateUpdate;
             uint256 len = tokens.length;
     
             for (uint256 i; i < len; ) {
                 address token = tokens[i];
                 uint16 rate = rates[i];
     
                 TokenQuotaParams storage tokenQuotaParams = totalQuotaParams[token]; // U:[PQK-7]
                 (uint16 prevRate, uint192 tqCumulativeIndexLU, ) = _getTokenQuotaParamsOrRevert(tokenQuotaParams);
     
                 tokenQuotaParams.cumulativeIndexLU = QuotasLogic.cumulativeIndexSince(
                     tqCumulativeIndexLU,
                     prevRate,
                     timestampLU
                 ); // U:[PQK-7]
     
                 tokenQuotaParams.rate = rate; // U:[PQK-7]
     
                 quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR; // U:[PQK-7]
     
                 emit UpdateTokenQuotaRate(token, rate); // U:[PQK-7]

226:     function setGauge(
             address _gauge
         )
             external
             override
             configuratorOnly // U:[PQK-2]
         {
             if (gauge != _gauge) {
                 gauge = _gauge; // U:[PQK-8]
                 emit SetGauge(_gauge); // U:[PQK-8]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Permission.sol

57:     function setPermissionAgent(address agent, bool permitted) external {
            _permittedAgents[msg.sender][agent] = permitted;
            emit SetPermittedAgent(msg.sender, agent, permitted);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

### <a name="NC-18"></a>[NC-18] Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*Instances (15)*:
```solidity
File: ./src/CDPVault.sol

1: 
   Current order:
   external mintProfit
   external enter
   external exit
   external addAvailable
   external handleRewardsOnDeposit
   external handleRewardsOnWithdraw
   external setParameter
   external setParameter
   external deposit
   external withdraw
   external borrow
   external repay
   public spotPrice
   internal _handleTokenRewards
   external getRewards
   internal _modifyPosition
   internal _isCollateralized
   public modifyCollateralAndDebt
   internal _calcQuotaRevenueChange
   internal _calcDebt
   internal _getQuotedTokensData
   external liquidatePosition
   external liquidatePositionBadDebt
   internal calcDecrease
   internal calcAccruedInterest
   external virtualDebt
   internal calcTotalDebt
   public poolQuotaKeeper
   external quotasInterest
   external getDebtData
   external getDebtInfo
   external recoverERC20
   
   Suggested order:
   external mintProfit
   external enter
   external exit
   external addAvailable
   external handleRewardsOnDeposit
   external handleRewardsOnWithdraw
   external setParameter
   external setParameter
   external deposit
   external withdraw
   external borrow
   external repay
   external getRewards
   external liquidatePosition
   external liquidatePositionBadDebt
   external virtualDebt
   external quotasInterest
   external getDebtData
   external getDebtInfo
   external recoverERC20
   public spotPrice
   public modifyCollateralAndDebt
   public poolQuotaKeeper
   internal _handleTokenRewards
   internal _modifyPosition
   internal _isCollateralized
   internal _calcQuotaRevenueChange
   internal _calcDebt
   internal _getQuotedTokensData
   internal calcDecrease
   internal calcAccruedInterest
   internal calcTotalDebt

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

1: 
   Current order:
   internal _revertIfCallerIsNotPoolQuotaKeeper
   internal _revertIfCallerNotCreditManager
   internal _revertIfLocked
   public decimals
   external creditManagers
   public availableLiquidity
   public expectedLiquidity
   public expectedLiquidityLU
   public totalAssets
   public deposit
   external depositWithReferral
   public depositETH
   public mint
   external mintWithReferral
   public withdraw
   public redeem
   public previewDeposit
   public previewMint
   public previewWithdraw
   public previewRedeem
   public maxDeposit
   public maxMint
   public maxWithdraw
   public maxRedeem
   internal _deposit
   internal _registerDeposit
   internal _withdraw
   internal _convertToShares
   internal _convertToAssets
   external totalBorrowed
   external totalDebtLimit
   external creditManagerBorrowed
   external creditManagerDebtLimit
   external creditManagerBorrowable
   external lendCreditAccount
   external repayCreditAccount
   internal _borrowable
   public baseInterestRate
   external supplyRate
   public baseInterestIndex
   external baseInterestIndexLU
   internal _calcBaseInterestAccrued
   external calcAccruedQuotaInterest
   internal _updateBaseInterest
   private _calcBaseInterestAccrued
   private _calcBaseInterestIndex
   public quotaRevenue
   external updateQuotaRevenue
   external setQuotaRevenue
   internal _calcQuotaRevenueAccrued
   internal _setQuotaRevenue
   private _calcQuotaRevenueAccrued
   external setInterestRateModel
   external setPoolQuotaKeeper
   external setTreasury
   external setTotalDebtLimit
   external setCreditManagerDebtLimit
   external setWithdrawFee
   external setAllowed
   external setLock
   external isAllowed
   internal _setTotalDebtLimit
   internal _amountWithFee
   internal _amountMinusFee
   internal _amountWithWithdrawalFee
   internal _amountMinusWithdrawalFee
   internal _convertToU256
   internal _convertToU128
   external mintProfit
   
   Suggested order:
   external creditManagers
   external depositWithReferral
   external mintWithReferral
   external totalBorrowed
   external totalDebtLimit
   external creditManagerBorrowed
   external creditManagerDebtLimit
   external creditManagerBorrowable
   external lendCreditAccount
   external repayCreditAccount
   external supplyRate
   external baseInterestIndexLU
   external calcAccruedQuotaInterest
   external updateQuotaRevenue
   external setQuotaRevenue
   external setInterestRateModel
   external setPoolQuotaKeeper
   external setTreasury
   external setTotalDebtLimit
   external setCreditManagerDebtLimit
   external setWithdrawFee
   external setAllowed
   external setLock
   external isAllowed
   external mintProfit
   public decimals
   public availableLiquidity
   public expectedLiquidity
   public expectedLiquidityLU
   public totalAssets
   public deposit
   public depositETH
   public mint
   public withdraw
   public redeem
   public previewDeposit
   public previewMint
   public previewWithdraw
   public previewRedeem
   public maxDeposit
   public maxMint
   public maxWithdraw
   public maxRedeem
   public baseInterestRate
   public baseInterestIndex
   public quotaRevenue
   internal _revertIfCallerIsNotPoolQuotaKeeper
   internal _revertIfCallerNotCreditManager
   internal _revertIfLocked
   internal _deposit
   internal _registerDeposit
   internal _withdraw
   internal _convertToShares
   internal _convertToAssets
   internal _borrowable
   internal _calcBaseInterestAccrued
   internal _updateBaseInterest
   internal _calcQuotaRevenueAccrued
   internal _setQuotaRevenue
   internal _setTotalDebtLimit
   internal _amountWithFee
   internal _amountMinusFee
   internal _amountWithWithdrawalFee
   internal _amountMinusWithdrawalFee
   internal _convertToU256
   internal _convertToU128
   private _calcBaseInterestAccrued
   private _calcBaseInterestIndex
   private _calcQuotaRevenueAccrued

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

1: 
   Current order:
   public withdraw
   public redeem
   external unstake
   external cooldownAssets
   external cooldownShares
   external setCooldownDuration
   internal _checkMinShares
   internal _deposit
   internal _withdraw
   
   Suggested order:
   external unstake
   external cooldownAssets
   external cooldownShares
   external setCooldownDuration
   public withdraw
   public redeem
   internal _checkMinShares
   internal _deposit
   internal _withdraw

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/VaultRegistry.sol

1: 
   Current order:
   external addVault
   external removeVault
   external getVaults
   external getUserTotalDebt
   private _removeVaultFromList
   external isVaultRegistered
   
   Suggested order:
   external addVault
   external removeVault
   external getVaults
   external getUserTotalDebt
   external isVaultRegistered
   private _removeVaultFromList

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

1: 
   Current order:
   external setOracles
   external initialize
   internal _authorizeUpgrade
   public getStatus
   external spot
   internal _fetchAndValidate
   private _getStatus
   
   Suggested order:
   external setOracles
   external initialize
   external spot
   public getStatus
   internal _authorizeUpgrade
   internal _fetchAndValidate
   private _getStatus

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

1: 
   Current order:
   external initialize
   internal _authorizeUpgrade
   public getStatus
   external spot
   internal _fetchAndValidate
   private _getStatus
   internal _validatePtOracle
   
   Suggested order:
   external initialize
   external spot
   public getStatus
   internal _authorizeUpgrade
   internal _fetchAndValidate
   internal _validatePtOracle
   private _getStatus

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

1: 
   Current order:
   external redeemRewards
   external getRewardTokens
   external balanceOf
   internal _updateRewardIndex
   internal _doTransferOutRewards
   internal _rewardSharesTotal
   external handleRewardsOnDeposit
   external handleRewardsOnWithdraw
   
   Suggested order:
   external redeemRewards
   external getRewardTokens
   external balanceOf
   external handleRewardsOnDeposit
   external handleRewardsOnWithdraw
   internal _updateRewardIndex
   internal _doTransferOutRewards
   internal _rewardSharesTotal

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

1: 
   Current order:
   internal _updateAndDistributeRewards
   internal _updateAndDistributeRewardsForTwo
   private _distributeRewardsPrivate
   internal _updateRewardIndex
   internal _doTransferOutRewards
   
   Suggested order:
   internal _updateAndDistributeRewards
   internal _updateAndDistributeRewardsForTwo
   internal _updateRewardIndex
   internal _doTransferOutRewards
   private _distributeRewardsPrivate

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/proxy/PoolAction.sol

1: 
   Current order:
   external transferAndJoin
   public join
   internal _balancerJoin
   internal _pendleJoin
   external updateLeverJoin
   public exit
   internal _balancerExit
   internal _pendleExit
   
   Suggested order:
   external transferAndJoin
   external updateLeverJoin
   public join
   public exit
   internal _balancerJoin
   internal _pendleJoin
   internal _balancerExit
   internal _pendleExit

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

1: 
   Current order:
   internal _onDeposit
   internal _onWithdraw
   internal _onIncreaseLever
   internal _onDecreaseLever
   external deposit
   external withdraw
   external borrow
   external repay
   external depositAndBorrow
   external withdrawAndRepay
   external multisend
   external increaseLever
   external decreaseLever
   external onFlashLoan
   external onCreditFlashLoan
   internal _deposit
   internal _withdraw
   internal _borrow
   internal _repay
   internal _transferAndSwap
   
   Suggested order:
   external deposit
   external withdraw
   external borrow
   external repay
   external depositAndBorrow
   external withdrawAndRepay
   external multisend
   external increaseLever
   external decreaseLever
   external onFlashLoan
   external onCreditFlashLoan
   internal _onDeposit
   internal _onWithdraw
   internal _onIncreaseLever
   internal _onDecreaseLever
   internal _deposit
   internal _withdraw
   internal _borrow
   internal _repay
   internal _transferAndSwap

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

1: 
   Current order:
   external transferAndSwap
   public swap
   internal balancerSwap
   internal kyberSwap
   internal uniV3Swap
   internal pendleJoin
   internal pendleExit
   public getSwapToken
   internal _revertBytes
   
   Suggested order:
   external transferAndSwap
   public swap
   public getSwapToken
   internal balancerSwap
   internal kyberSwap
   internal uniV3Swap
   internal pendleJoin
   internal pendleExit
   internal _revertBytes

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

1: 
   Current order:
   external updateEpoch
   internal _checkAndUpdateEpoch
   external getRates
   external vote
   internal _vote
   external unvote
   internal _unvote
   external setFrozenEpoch
   external addQuotaToken
   external changeQuotaMinRate
   external changeQuotaMaxRate
   internal _changeQuotaTokenRateParams
   internal _checkParams
   public isTokenAdded
   internal _poolQuotaKeeper
   internal _revertIfCallerNotVoter
   
   Suggested order:
   external updateEpoch
   external getRates
   external vote
   external unvote
   external setFrozenEpoch
   external addQuotaToken
   external changeQuotaMinRate
   external changeQuotaMaxRate
   public isTokenAdded
   internal _checkAndUpdateEpoch
   internal _vote
   internal _unvote
   internal _changeQuotaTokenRateParams
   internal _checkParams
   internal _poolQuotaKeeper
   internal _revertIfCallerNotVoter

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

1: 
   Current order:
   public cumulativeIndex
   external getQuotaRate
   external quotedTokens
   external isQuotedToken
   external getTokenQuotaParams
   external poolQuotaRevenue
   external addQuotaToken
   external updateRates
   external setGauge
   external setCreditManager
   internal isInitialised
   internal _getTokenQuotaParamsOrRevert
   internal _revertIfCallerNotGauge
   
   Suggested order:
   external getQuotaRate
   external quotedTokens
   external isQuotedToken
   external getTokenQuotaParams
   external poolQuotaRevenue
   external addQuotaToken
   external updateRates
   external setGauge
   external setCreditManager
   public cumulativeIndex
   internal isInitialised
   internal _getTokenQuotaParamsOrRevert
   internal _revertIfCallerNotGauge

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Pause.sol

1: 
   Current order:
   internal _pause
   external pause
   external unpause
   
   Suggested order:
   external pause
   external unpause
   internal _pause

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Pause.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

1: 
   Current order:
   internal toAddress
   internal decodeLastToken
   external exactInput
   external exactOutput
   
   Suggested order:
   external exactInput
   external exactOutput
   internal toAddress
   internal decodeLastToken

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

### <a name="NC-19"></a>[NC-19] Functions should not be longer than 50 lines
Overly complex code can make understanding functionality more difficult, try to further modularize your code to ensure readability 

*Instances (298)*:
```solidity
File: ./src/CDPVault.sol

27:     function enter(address user, uint256 amount) external;

29:     function exit(address user, uint256 amount) external;

31:     function addAvailable(address user, int256 amount) external;

35:     function handleRewardsOnDeposit(address user, uint256 amount, int256 deltaCollateral) external;

211:     function setParameter(bytes32 parameter, uint256 data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {

224:     function setParameter(bytes32 parameter, address data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {

240:     function deposit(address to, uint256 amount) external returns (uint256 tokenAmount) {

256:     function withdraw(address to, uint256 amount) external returns (uint256 tokenAmount) {

273:     function borrow(address borrower, address position, uint256 amount) external returns (int256 deltaDebt) {

292:     function repay(address borrower, address position, uint256 amount) external returns (int256 deltaDebt){

312:     function spotPrice() public view returns (uint256) {

316:     function _handleTokenRewards(address owner, uint256 collateralAmountBefore, int256 deltaCollateral) internal {

535:     function _calcQuotaRevenueChange(int256 deltaDebt) internal view returns (int256 quotaRevenueChange) {

540:     function _calcDebt(Position memory position) internal view returns (DebtData memory cdd) {

582:     function liquidatePosition(address owner, uint256 repayAmount) external whenNotPaused {

653:     function liquidatePositionBadDebt(address owner, uint256 repayAmount) external whenNotPaused {

808:     function virtualDebt(address position) external view returns (uint256) {

814:     function calcTotalDebt(DebtData memory debtData) internal pure returns (uint256) {

819:     function poolQuotaKeeper() public view returns (address) {

824:     function quotasInterest(address position) external view returns (uint256) {

830:     function getDebtData(address position) external view returns (DebtData memory) {

851:     function recoverERC20(address tokenAddress, address to, uint256 tokenAmount) external onlyRole(DEFAULT_ADMIN_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

64:     function maxFlashLoan(address token) external view override returns (uint256 max) {

75:     function flashFee(address token, uint256 amount) external view override returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

40:     function setCooldownPeriod(uint256 _cooldownPeriod) external onlyOwner {

59:     function initiateCooldown(uint256 _amount) external {

91:     function getBalance(address _user) external view returns (uint256) {

98:     function getCoolDownInfo(address _user) external view returns (uint256, uint256, uint256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

136:     function _revertIfCallerIsNotPoolQuotaKeeper() internal view {

141:     function _revertIfCallerNotCreditManager() internal view {

191:     function decimals() public view override(ERC20, ERC4626, IERC20Metadata) returns (uint8) {

196:     function creditManagers() external view override returns (address[] memory) {

201:     function availableLiquidity() public view override returns (uint256) {

207:     function expectedLiquidity() public view override returns (uint256) {

212:     function expectedLiquidityLU() public view override returns (uint256) {

222:     function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {

366:     function previewDeposit(uint256 assets) public view override(ERC4626, IERC4626) returns (uint256 shares) {

371:     function previewMint(uint256 shares) public view override(ERC4626, IERC4626) returns (uint256) {

376:     function previewWithdraw(uint256 assets) public view override(ERC4626, IERC4626) returns (uint256) {

381:     function previewRedeem(uint256 shares) public view override(ERC4626, IERC4626) returns (uint256) {

386:     function maxDeposit(address) public view override(ERC4626, IERC4626) returns (uint256) {

391:     function maxMint(address) public view override(ERC4626, IERC4626) returns (uint256) {

396:     function maxWithdraw(address owner) public view override(ERC4626, IERC4626) returns (uint256) {

406:     function maxRedeem(address owner) public view override(ERC4626, IERC4626) returns (uint256) {

414:     function _deposit(address receiver, uint256 assetsSent, uint256 assetsReceived, uint256 shares) internal {

429:     function _registerDeposit(address receiver, uint256 assetsSent, uint256 assetsReceived, uint256 shares) internal {

472:     function _convertToShares(uint256 assets) internal pure returns (uint256 shares) {

479:     function _convertToAssets(uint256 shares) internal pure returns (uint256 assets) {

489:     function totalBorrowed() external view override returns (uint256) {

494:     function totalDebtLimit() external view override returns (uint256) {

499:     function creditManagerBorrowed(address creditManager) external view override returns (uint256) {

504:     function creditManagerDebtLimit(address creditManager) external view override returns (uint256) {

509:     function creditManagerBorrowable(address creditManager) external view override returns (uint256 borrowable) {

622:     function _borrowable(DebtParams storage debt) internal view returns (uint256) {

639:     function baseInterestRate() public view override returns (uint256) {

646:     function supplyRate() external view override returns (uint256) {

657:     function baseInterestIndex() public view override returns (uint256) {

664:     function baseInterestIndexLU() external view override returns (uint256) {

669:     function _calcBaseInterestAccrued() internal view returns (uint256) {

675:     function calcAccruedQuotaInterest() external view returns (uint256) {

714:     function _calcBaseInterestAccrued(uint256 timestamp) private view returns (uint256) {

719:     function _calcBaseInterestIndex(uint256 timestamp) private view returns (uint256) {

728:     function quotaRevenue() public view override returns (uint256) {

760:     function _calcQuotaRevenueAccrued() internal view returns (uint256) {

769:     function _setQuotaRevenue(uint256 newQuotaRevenue) internal {

779:     function _calcQuotaRevenueAccrued(uint256 timestamp) private view returns (uint256) {

825:     function setTreasury(address treasury_) external configuratorOnly nonZeroAddress(treasury_) {

886:     function setAllowed(address account, bool status) external controllerOnly {

892:     function setLock(bool status) external controllerOnly {

898:     function isAllowed(address account) external view returns (bool) {

903:     function _setTotalDebtLimit(uint256 limit) internal {

917:     function _amountWithFee(uint256 amount) internal view virtual returns (uint256) {

923:     function _amountMinusFee(uint256 amount) internal view virtual returns (uint256) {

928:     function _amountWithWithdrawalFee(uint256 amount) internal view returns (uint256) {

933:     function _amountMinusWithdrawalFee(uint256 amount) internal view returns (uint256) {

938:     function _convertToU256(uint128 limit) internal pure returns (uint256) {

943:     function _convertToU128(uint256 limit) internal pure returns (uint128) {

947:     function mintProfit(uint256 amount) external creditManagerOnly {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

28:     function withdraw(address to, uint256 amount) external onlyStakingVault {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

104:     function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {

117:     function cooldownShares(uint256 shares) external ensureCooldownOn returns (uint256 assets) {

130:     function setCooldownDuration(uint24 duration) external onlyOwner {

153:     function _deposit(address caller, address receiver, uint256 assets, uint256 shares) internal override nonReentrant {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

34:     function moveFunds(address payable treasury) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

45:     function moveFunds(address treasury, IERC20 token) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

55:     function moveFunds(address treasury, IERC20[] calldata tokens) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

68:     function _moveFunds(address treasury, IERC20 token) internal {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

39:     function addVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

49:     function removeVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

59:     function getVaults() external view override(IVaultRegistry) returns (ICDPVault[] memory) {

65:     function getUserTotalDebt(address user) external view override(IVaultRegistry) returns (uint256 totalNormalDebt) {

80:     function _removeVaultFromList(ICDPVault vault) private {

95:     function isVaultRegistered(address vault) external view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/interfaces/IBuffer.sol

9:     function withdrawCredit(address to, uint256 amount) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IBuffer.sol)

```solidity
File: ./src/interfaces/ICDM.sol

7:     function globalDebt() external view returns (uint256);

9:     function globalDebtCeiling() external view returns (uint256);

11:     function accounts(address account) external view returns (int256 balance, uint256 debtCeiling);

13:     function setParameter(bytes32 parameter, uint256 data) external;

15:     function setParameter(address debtor, bytes32 parameter, uint256 data) external;

17:     function creditLine(address account) external view returns (uint256);

19:     function modifyBalance(address from, address to, uint256 amount) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDM.sol)

```solidity
File: ./src/interfaces/ICDPVault.sol

38:     function oracle() external view returns (IOracle);

42:     function tokenScale() external view returns (uint256);

44:     function vaultConfig() external view returns (uint128 debtFloor, uint64 liquidationRatio);

46:     function totalDebt() external view returns (uint256);

62:     function deposit(address to, uint256 amount) external returns (uint256);

64:     function withdraw(address to, uint256 amount) external returns (uint256);

82:     function virtualDebt(address position) external view returns (uint256);

84:     function getAccruedInterest(address position) external view returns (uint256 accruedInterest);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDPVault.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

28:     function maxFlashLoan(address token) external view returns (uint256);

34:     function flashFee(address token, uint256 amount) external view returns (uint256);

80:     function underlyingToken() external view returns (IERC20);

82:     function CALLBACK_SUCCESS() external view returns (bytes32);

84:     function CALLBACK_SUCCESS_CREDIT() external view returns (bytes32);

86:     function maxFlashLoan(address token) external view override returns (uint256);

88:     function flashFee(address token, uint256 amount) external view override returns (uint256);

113:     function approvePayback(uint256 amount) internal {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/interfaces/IInterestRateModel.sol

5:     function getIRS() external view returns (int64, uint64, uint64, uint64, uint256);

7:     function getAccruedInterest() external view returns (uint256 accruedInterest);

9:     function virtualRateAccumulator() external view returns (uint64 rateAccumulator);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IInterestRateModel.sol)

```solidity
File: ./src/interfaces/IMinter.sol

10:     function stablecoin() external view returns (IStablecoin);

12:     function enter(address user, uint256 amount) external;

14:     function exit(address user, uint256 amount) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IMinter.sol)

```solidity
File: ./src/interfaces/IOracle.sol

8:     function spot(address token) external view returns (uint256);

9:     function getStatus(address token) external view returns (bool);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IOracle.sol)

```solidity
File: ./src/interfaces/IPause.sol

5:     function pausedAt() external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPause.sol)

```solidity
File: ./src/interfaces/IPermission.sol

5:     function hasPermission(address owner, address caller) external view returns (bool);

7:     function modifyPermission(address caller, bool allowed) external;

9:     function modifyPermission(address owner, address caller, bool allowed) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPermission.sol)

```solidity
File: ./src/interfaces/IPoolQuotaKeeperV3.sol

48:     function underlying() external view returns (address);

66:     function getQuotaRate(address) external view returns (uint16);

68:     function cumulativeIndex(address token) external view returns (uint192);

70:     function isQuotedToken(address token) external view returns (bool);

96:     function poolQuotaRevenue() external view returns (uint256);

98:     function lastQuotaRateUpdate() external view returns (uint40);

111:     function setCreditManager(address token, address vault) external;

113:     function quotedTokens() external view returns (address[] memory);

115:     function addQuotaToken(address token, uint16 rate) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolQuotaKeeperV3.sol)

```solidity
File: ./src/interfaces/IPoolV3.sol

45:     function addressProvider() external view returns (address);

47:     function underlyingToken() external view returns (address);

49:     function treasury() external view returns (address);

51:     function withdrawFee() external view returns (uint16);

53:     function creditManagers() external view returns (address[] memory);

55:     function availableLiquidity() external view returns (uint256);

57:     function expectedLiquidity() external view returns (uint256);

59:     function expectedLiquidityLU() external view returns (uint256);

71:     function mintWithReferral(uint256 shares, address receiver, uint256 referralCode) external returns (uint256 assets);

77:     function totalBorrowed() external view returns (uint256);

79:     function totalDebtLimit() external view returns (uint256);

81:     function creditManagerBorrowed(address creditManager) external view returns (uint256);

83:     function creditManagerDebtLimit(address creditManager) external view returns (uint256);

85:     function creditManagerBorrowable(address creditManager) external view returns (uint256 borrowable);

87:     function lendCreditAccount(uint256 borrowedAmount, address creditAccount) external;

89:     function repayCreditAccount(uint256 repaidAmount, uint256 profit, uint256 loss) external;

95:     function interestRateModel() external view returns (address);

97:     function baseInterestRate() external view returns (uint256);

99:     function supplyRate() external view returns (uint256);

101:     function baseInterestIndex() external view returns (uint256);

103:     function baseInterestIndexLU() external view returns (uint256);

105:     function lastBaseInterestUpdate() external view returns (uint40);

111:     function poolQuotaKeeper() external view returns (address);

113:     function quotaRevenue() external view returns (uint256);

115:     function lastQuotaRevenueUpdate() external view returns (uint40);

117:     function updateQuotaRevenue(int256 quotaRevenueDelta) external;

119:     function setQuotaRevenue(uint256 newQuotaRevenue) external;

125:     function setInterestRateModel(address newInterestRateModel) external;

127:     function setPoolQuotaKeeper(address newPoolQuotaKeeper) external;

131:     function setTotalDebtLimit(uint256 newLimit) external;

133:     function setCreditManagerDebtLimit(address creditManager, uint256 newLimit) external;

135:     function setWithdrawFee(uint256 newWithdrawFee) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolV3.sol)

```solidity
File: ./src/interfaces/IStablecoin.sol

8:     function mint(address to, uint256 amount) external;

10:     function burn(address from, uint256 amount) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IStablecoin.sol)

```solidity
File: ./src/interfaces/IVaultRegistry.sol

19:     function getVaults() external view returns (ICDPVault[] memory);

23:     function getUserTotalDebt(address user) external view returns (uint256 totalNormalDebt);

27:     function isVaultRegistered(address vault) external view returns (bool);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IVaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {

58:     function initialize(address admin, address manager) external initializer {

70:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {}

79:     function getStatus(address token) public view virtual override returns (bool status) {

87:     function spot(address token) external view virtual override returns (uint256 price) {

96:     function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {

115:     function _getStatus(address token) private view returns (bool status) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

78:     function initialize(address admin, address manager) external initializer {

90:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {

101:     function getStatus(address /*token*/) public view virtual override returns (bool status) {

109:     function spot(address /* token */) external view virtual override returns (uint256 price) {

122:     function _fetchAndValidate() internal view returns (bool isValid, uint256 price) {

140:     function _getStatus() private view returns (bool status) {

147:     function _validatePtOracle() internal view returns (bool isValid) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

10:     function redeemRewards(address user) external returns (uint256[] memory rewardsOut);

12:     function getRewardTokens() external view returns (address[] memory);

14:     function balanceOf(address account) external view returns (uint256);

114:     function _rewardSharesTotal() internal view virtual returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

43:     function _updateAndDistributeRewardsForTwo(address user1, uint256 collateralAmountBefore) internal virtual {

81:     function _updateRewardIndex() internal virtual returns (address[] memory tokens, uint256[] memory indexes);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

85:     function getMethodsByOwner(address owner, IPRBProxyPlugin plugin) external view returns (bytes4[] memory methods) {

116:     function getPluginByOwner(address owner, bytes4 method) external view returns (IPRBProxyPlugin plugin) {

121:     function getPluginByProxy(IPRBProxy proxy, bytes4 method) external view returns (IPRBProxyPlugin plugin) {

126:     function getProxy(address user) external view returns (IPRBProxy proxy) {

131:     function isProxy(address user) external view override returns (bool) {

140:     function deploy() external override onlyNonProxyOwner(msg.sender) returns (IPRBProxy proxy) {

153:     function deployFor(address user) external override onlyNonProxyOwner(user) returns (IPRBProxy proxy) {

176:     function installPlugin(IPRBProxyPlugin plugin) external override onlyCallerWithProxy {

181:     function setPermission(address envoy, address target, bool permission) external override onlyCallerWithProxy {

188:     function uninstallPlugin(IPRBProxyPlugin plugin) external override onlyCallerWithProxy {

219:     function _deploy(address owner, address target, bytes memory data) internal returns (IPRBProxy proxy) {

238:     function _installPlugin(IPRBProxyPlugin plugin) internal {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/prb-proxy/interfaces/IPRBProxyRegistry.sol

87:     function VERSION() external view returns (string memory);

92:     function constructorParams() external view returns (address owner, address target, bytes memory data);

98:     function getMethodsByOwner(address owner, IPRBProxyPlugin plugin) external view returns (bytes4[] memory methods);

104:     function getMethodsByProxy(IPRBProxy proxy, IPRBProxyPlugin plugin) external view returns (bytes4[] memory methods);

111:     function getPermissionByOwner(address owner, address envoy, address target) external view returns (bool permission);

128:     function getPluginByOwner(address owner, bytes4 method) external view returns (IPRBProxyPlugin plugin);

134:     function getPluginByProxy(IPRBProxy proxy, bytes4 method) external view returns (IPRBProxyPlugin plugin);

138:     function getProxy(address user) external view returns (IPRBProxy proxy);

142:     function isProxy(address proxy) external view returns (bool);

156:     function deploy() external returns (IPRBProxy proxy);

171:     function deployAndExecute(address target, bytes calldata data) external returns (IPRBProxy proxy);

207:     function deployAndInstallPlugin(IPRBProxyPlugin plugin) external returns (IPRBProxy proxy);

218:     function deployFor(address user) external returns (IPRBProxy proxy);

236:     function installPlugin(IPRBProxyPlugin plugin) external;

252:     function setPermission(address envoy, address target, bool permission) external;

264:     function uninstallPlugin(IPRBProxyPlugin plugin) external;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/interfaces/IPRBProxyRegistry.sol)

```solidity
File: ./src/proxy/BaseAction.sol

20:     function _delegateCall(address to, bytes memory data) internal returns (bytes memory) {

29:     function _revertBytes(bytes memory errMsg) internal pure {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/BaseAction.sol)

```solidity
File: ./src/proxy/ERC165Plugin.sol

18:     function getMethods() external pure returns (bytes4[] memory methods) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/ERC165Plugin.sol)

```solidity
File: ./src/proxy/PoolAction.sol

117:     function join(PoolActionParams memory poolActionParams) public {

131:     function _balancerJoin(PoolActionParams memory poolActionParams) internal {

162:     function _pendleJoin(PoolActionParams memory poolActionParams) internal {

244:     function exit(PoolActionParams memory poolActionParams) public returns (uint256 retAmount) {

252:     function _balancerExit(PoolActionParams memory poolActionParams) internal returns (uint256 retAmount) {

283:     function _pendleExit(PoolActionParams memory poolActionParams) internal returns (uint256 retAmount) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

195:     function _onDecreaseLever(LeverParams memory leverParams, uint256 subCollateral) internal virtual returns (uint256);

596:     function _borrow(address vault, address position, CreditParams calldata creditParams) internal {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/PositionAction4626.sol

41:     function _onDeposit(address vault, address position, address src, uint256 amount) internal override returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction4626.sol)

```solidity
File: ./src/proxy/SwapAction.sol

113:     function swap(SwapParams memory swapParams) public returns (uint256 retAmount) {

299:     function kyberSwap(address assetIn, uint256 amountIn, bytes memory payload) internal returns (uint256) {

361:     function pendleJoin(address recipient, uint256 minOut, bytes memory data) internal returns (uint256 netLpOut) {

384:     function pendleExit(address recipient, uint256 minOut, bytes memory data) internal returns (uint256 retAmount) {

414:     function getSwapToken(SwapParams calldata swapParams) public pure returns (address token) {

436:     function _revertBytes(bytes memory errMsg) internal pure {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

97:     function getRates(address[] calldata tokens) external view override returns (uint16[] memory rates) {

144:     function _vote(address user, uint96 votes, address token, bool lpSide) internal {

187:     function _unvote(address user, uint96 votes, address token, bool lpSide) internal {

219:     function setFrozenEpoch(bool status) external override configuratorOnly {

291:     function _changeQuotaTokenRateParams(address token, uint16 minRate, uint16 maxRate) internal {

305:     function _checkParams(uint16 minRate, uint16 maxRate) internal pure {

312:     function isTokenAdded(address token) public view override returns (bool) {

317:     function _poolQuotaKeeper() internal view returns (IPoolQuotaKeeperV3) {

322:     function _revertIfCallerNotVoter() internal view {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

87:     function cumulativeIndex(address token) public view override returns (uint192) {

95:     function getQuotaRate(address token) external view override returns (uint16) {

100:     function quotedTokens() external view override returns (address[] memory) {

105:     function isQuotedToken(address token) external view override returns (bool) {

135:     function poolQuotaRevenue() external view virtual override returns (uint256 quotaRevenue) {

255:     function isInitialised(TokenQuotaParams storage tokenQuotaParams) internal view returns (bool) {

279:     function _revertIfCallerNotGauge() internal view {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

41:     function calcQuotaRevenueChange(uint16 rate, int256 change) internal pure returns (int256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

17: function toInt256(uint256 x) pure returns (int256) {

23: function toUint64(uint256 x) pure returns (uint64) {

37: function min(uint256 x, uint256 y) pure returns (uint256 z) {

44: function min(int256 x, int256 y) pure returns (int256 z) {

51: function max(uint256 x, uint256 y) pure returns (uint256 z) {

58: function add(uint256 x, int256 y) pure returns (uint256 z) {

66: function sub(uint256 x, int256 y) pure returns (uint256 z) {

74: function mul(uint256 x, int256 y) pure returns (int256 z) {

83: function wmul(uint256 x, uint256 y) pure returns (uint256 z) {

97: function wmul(uint256 x, int256 y) pure returns (int256 z) {

105: function wmulUp(uint256 x, uint256 y) pure returns (uint256 z) {

121: function wdiv(uint256 x, uint256 y) pure returns (uint256 z) {

137: function wdivUp(uint256 x, uint256 y) pure returns (uint256 z) {

152: function wpow(uint256 x, uint256 n, uint256 b) pure returns (uint256 z) {

208: function wpow(int256 x, int256 y) pure returns (int256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/utils/Pause.sol

36:     function unpause() external onlyRole(PAUSER_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Pause.sol)

```solidity
File: ./src/utils/Permission.sol

38:     function modifyPermission(address caller, bool permitted) external {

47:     function modifyPermission(address owner, address caller, bool permitted) external {

57:     function setPermissionAgent(address agent, bool permitted) external {

66:     function hasPermission(address owner, address caller) public view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

```solidity
File: ./src/vendor/AggregatorV3Interface.sol

8:     function decimals() external view returns (uint8);

10:     function description() external view returns (string memory);

12:     function version() external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/AggregatorV3Interface.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

24: function _require(bool condition, uint256 errorCode) pure {

32: function _require(bool condition, uint256 errorCode, bytes3 prefix) pure {

47: function _revert(uint256 errorCode, bytes3 prefix) pure {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

```solidity
File: ./src/vendor/IBalancerVault.sol

299:     function manageUserBalance(UserBalanceOp[] memory ops) external payable;

340:     function getPool(bytes32 poolId) external view returns (address, PoolSpecialization);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IBalancerVault.sol)

```solidity
File: ./src/vendor/ICurvePool.sol

6:     function get_virtual_price() external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/ICurvePool.sol)

```solidity
File: ./src/vendor/IPriceOracle.sol

57:     function getLatest(Variable variable) external view returns (uint256);

79:     function getLargestSafeQueryWindow() external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IPriceOracle.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

24: function toAddress(bytes memory _bytes, uint256 _start) pure returns (address) {

39: function decodeLastToken(bytes memory path) pure returns (address token) {

50:     function exactInput(ExactInputParams calldata params) external payable returns (uint256 amountOut);

55:     function exactOutput(ExactOutputParams calldata params) external payable returns (uint256 amountIn);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

```solidity
File: ./src/vendor/IWeightedPool.sol

5:     function getNormalizedWeights() external view returns (uint256[] memory);

7:     function totalSupply() external view returns (uint256);

9:     function getPoolId() external view returns (bytes32);

11:     function getInvariant() external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IWeightedPool.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

37:     function ensureNotInVaultContext(IVault vault) internal view {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="NC-20"></a>[NC-20] Change uint to uint256
Throughout the code base, some variables are declared as `uint`. To favor explicitness, consider changing all instances of `uint` to `uint256`

*Instances (3)*:
```solidity
File: ./src/pendle-rewards/RewardManager.sol

120:         uint collateralAmountBefore,

128:         uint collateralAmountBefore,

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

48:     uint256 prefixUint = uint256(uint24(prefix));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

### <a name="NC-21"></a>[NC-21] Interfaces should be defined in separate files from their usage
The interfaces below should be defined in separate files, so that it's easier for future projects to import them, and to avoid duplication later on if they need to be used elsewhere in the project

*Instances (12)*:
```solidity
File: ./src/CDPVault.sol

24: interface IPoolV3Loop is IPoolV3 {

34: interface IRewardManager {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/interfaces/ICDPVault.sol

35: interface ICDPVaultBase is IAccessControl, IPause, IPermission {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDPVault.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

7: interface IERC3156FlashBorrower {

24: interface IERC3156FlashLender {

49: interface ICreditFlashBorrower {

64: interface ICreditFlashLender {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/interfaces/IPoolQuotaKeeperV3.sol

21: interface IPoolQuotaKeeperV3Events {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolQuotaKeeperV3.sol)

```solidity
File: ./src/interfaces/IPoolV3.sol

11: interface IPoolV3Events {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolV3.sol)

```solidity
File: ./src/interfaces/IStablecoin.sol

7: interface IERC20Mintable {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IStablecoin.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

9: interface IPendleMarketV3 {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/vendor/IBalancerVault.sol

112: interface IVault {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IBalancerVault.sol)

### <a name="NC-22"></a>[NC-22] Lack of checks in setters
Be it sanity checks (like checks against `0`-values) or initial setting checks: it's best for Setter functions to have them

*Instances (14)*:
```solidity
File: ./src/Locking.sol

40:     function setCooldownPeriod(uint256 _cooldownPeriod) external onlyOwner {
            cooldownPeriod = _cooldownPeriod;
            emit CooldownPeriodSet(_cooldownPeriod);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

734:     function updateQuotaRevenue(
             int256 quotaRevenueDelta
         )
             external
             override
             nonReentrant // U:[LP-2B]
             //poolQuotaKeeperOnly // U:[LP-2C]
             creditManagerOnly
         {
             _setQuotaRevenue((quotaRevenue().toInt256() + quotaRevenueDelta).toUint256());

748:     function setQuotaRevenue(
             uint256 newQuotaRevenue
         )
             external
             override
             nonReentrant // U:[LP-2B]
             poolQuotaKeeperOnly // U:[LP-2C]
         {
             _setQuotaRevenue(newQuotaRevenue); // U:[LP-20]

789:     function setInterestRateModel(
             address newInterestRateModel
         )
             external
             override
             configuratorOnly // U:[LP-2C]
             nonZeroAddress(newInterestRateModel) // U:[LP-22A]
         {
             interestRateModel = newInterestRateModel; // U:[LP-22B]
             _updateBaseInterest(0, 0, false); // U:[LP-22B]
             emit SetInterestRateModel(newInterestRateModel); // U:[LP-22B]

825:     function setTreasury(address treasury_) external configuratorOnly nonZeroAddress(treasury_) {
             treasury = treasury_;

831:     function setTotalDebtLimit(
             uint256 newLimit
         )
             external
             override
             controllerOnly // U:[LP-2C]
         {
             _setTotalDebtLimit(newLimit); // U:[LP-24]

886:     function setAllowed(address account, bool status) external controllerOnly {
             _allowed[account] = status;

892:     function setLock(bool status) external controllerOnly {
             locked = status;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {
            for (uint256 i = 0; i < _tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

214:     /*//////////////////////////////////////////////////////////////////////////
                               INTERNAL NON-CONSTANT FUNCTIONS
         //////////////////////////////////////////////////////////////////////////*/
     
         /// @dev See the documentation for the user-facing functions that call this internal function.

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

73:     function updateEpoch() external override {
            _checkAndUpdateEpoch(); // U:[GA-14]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

184:     function updateRates()
             external
             override
             gaugeOnly // U:[PQK-3]
         {
             address[] memory tokens = quotaTokensSet.values();
             uint16[] memory rates = IGaugeV3(gauge).getRates(tokens); // U:[PQK-7]
     
             uint256 quotaRevenue; // U:[PQK-7]
             uint256 timestampLU = lastQuotaRateUpdate;
             uint256 len = tokens.length;
     
             for (uint256 i; i < len; ) {
                 address token = tokens[i];
                 uint16 rate = rates[i];
     
                 TokenQuotaParams storage tokenQuotaParams = totalQuotaParams[token]; // U:[PQK-7]
                 (uint16 prevRate, uint192 tqCumulativeIndexLU, ) = _getTokenQuotaParamsOrRevert(tokenQuotaParams);
     
                 tokenQuotaParams.cumulativeIndexLU = QuotasLogic.cumulativeIndexSince(
                     tqCumulativeIndexLU,
                     prevRate,
                     timestampLU
                 ); // U:[PQK-7]
     
                 tokenQuotaParams.rate = rate; // U:[PQK-7]
     
                 quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR; // U:[PQK-7]
     
                 emit UpdateTokenQuotaRate(token, rate); // U:[PQK-7]
     
                 unchecked {
                     ++i;
                 }
             }
     
             IPoolV3(pool).setQuotaRevenue(quotaRevenue); // U:[PQK-7]
             lastQuotaRateUpdate = uint40(block.timestamp); // U:[PQK-7]

239:     function setCreditManager(
             address token,
             address vault
         )
             external
             override
             configuratorOnly // U:[PQK-2]
         {
             creditManagers[token] = vault;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Permission.sol

57:     function setPermissionAgent(address agent, bool permitted) external {
            _permittedAgents[msg.sender][agent] = permitted;
            emit SetPermittedAgent(msg.sender, agent, permitted);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

### <a name="NC-23"></a>[NC-23] Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*Instances (1)*:
```solidity
File: ./src/quotas/GaugeV3.sol

17: import {CallerNotVoterException, IncorrectParameterException, TokenNotAllowedException, InsufficientVotesException} from "@gearbox-protocol/core-v3/contracts/interfaces/IExceptions.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

### <a name="NC-24"></a>[NC-24] Missing Event for critical parameters change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

*Instances (10)*:
```solidity
File: ./src/PoolV3.sol

734:     function updateQuotaRevenue(
             int256 quotaRevenueDelta
         )
             external
             override
             nonReentrant // U:[LP-2B]
             //poolQuotaKeeperOnly // U:[LP-2C]
             creditManagerOnly
         {
             _setQuotaRevenue((quotaRevenue().toInt256() + quotaRevenueDelta).toUint256());

748:     function setQuotaRevenue(
             uint256 newQuotaRevenue
         )
             external
             override
             nonReentrant // U:[LP-2B]
             poolQuotaKeeperOnly // U:[LP-2C]
         {
             _setQuotaRevenue(newQuotaRevenue); // U:[LP-20]

825:     function setTreasury(address treasury_) external configuratorOnly nonZeroAddress(treasury_) {
             treasury = treasury_;

831:     function setTotalDebtLimit(
             uint256 newLimit
         )
             external
             override
             controllerOnly // U:[LP-2C]
         {
             _setTotalDebtLimit(newLimit); // U:[LP-24]

886:     function setAllowed(address account, bool status) external controllerOnly {
             _allowed[account] = status;

892:     function setLock(bool status) external controllerOnly {
             locked = status;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

104:     function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
             if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();
     
             shares = previewWithdraw(assets);
     
             cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
             cooldowns[msg.sender].underlyingAmount += uint152(assets);
     
             _withdraw(msg.sender, address(silo), msg.sender, assets, shares);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {
            for (uint256 i = 0; i < _tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

73:     function updateEpoch() external override {
            _checkAndUpdateEpoch(); // U:[GA-14]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

239:     function setCreditManager(
             address token,
             address vault
         )
             external
             override
             configuratorOnly // U:[PQK-2]
         {
             creditManagers[token] = vault;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="NC-25"></a>[NC-25] NatSpec is completely non-existent on functions that should have them
Public and external functions that aren't view or pure should have NatSpec comments

*Instances (14)*:
```solidity
File: ./src/CDPVault.sol

25:     function mintProfit(uint256 profit) external;

27:     function enter(address user, uint256 amount) external;

29:     function exit(address user, uint256 amount) external;

31:     function addAvailable(address user, int256 amount) external;

35:     function handleRewardsOnDeposit(address user, uint256 amount, int256 deltaCollateral) external;

37:     function handleRewardsOnWithdraw(

331:     function getRewards(address owner) external {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

947:     function mintProfit(uint256 amount) external creditManagerOnly {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

28:     function withdraw(address to, uint256 amount) external onlyStakingVault {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

90:     function flashLoan(

96:     function creditFlashLoan(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

10:     function redeemRewards(address user) external returns (uint256[] memory rewardsOut);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

239:     function setCreditManager(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="NC-26"></a>[NC-26] Incomplete NatSpec: `@param` is missing on actually documented functions
The following functions are missing `@param` NatSpec comments.

*Instances (10)*:
```solidity
File: ./src/PoolV3.sol

246:     /// @dev Same as `deposit`, but allows to specify the referral code
         function depositWithReferral(
             uint256 assets,
             address receiver,
             uint256 referralCode

307:     /// @dev Same as `mint`, but allows to specify the referral code
         function mintWithReferral(
             uint256 shares,
             address receiver,
             uint256 referralCode

824:     /// @notice Sets new treasury address, can only be called by configurator
         function setTreasury(address treasury_) external configuratorOnly nonZeroAddress(treasury_) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/proxy/PositionAction.sol

201:     /// @notice Adds collateral to a CDP Vault
         /// @param position The CDP Vault position
         /// @param vault The CDP Vault
         /// @param collateralParams The collateral parameters
         function deposit(
             address position,
             address vault,
             CollateralParams calldata collateralParams,
             PermitParams calldata permitParams

252:     /// @notice Adds collateral and debt to a CDP Vault
         /// @param position The CDP Vault position
         /// @param vault The CDP Vault
         /// @param collateralParams The collateral parameters
         /// @param creditParams The credit parameters
         function depositAndBorrow(
             address position,
             address vault,
             CollateralParams calldata collateralParams,
             CreditParams calldata creditParams,
             PermitParams calldata permitParams

399:     /// @notice Callback function for the flash loan taken out in increaseLever
         /// @param data The encoded bytes that were passed into the flash loan
         function onFlashLoan(
             address /*initiator*/,
             address /*token*/,
             uint256 amount,
             uint256 fee,
             bytes calldata data

451:     /// @notice Callback function for the credit flash loan taken out in decreaseLever
         /// @param data The encoded bytes that were passed into the credit flash loan
         function onCreditFlashLoan(
             address /*initiator*/,
             uint256 /*amount*/,
             uint256 fee,
             bytes calldata data

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

262:     /// @dev Changes the min rate for a quoted token
         /// @param minRate The minimal interest rate paid on token's quotas
         function changeQuotaMinRate(
             address token,
             uint16 minRate

276:     /// @dev Changes the max rate for a quoted token
         /// @param maxRate The maximal interest rate paid on token's quotas
         function changeQuotaMaxRate(
             address token,
             uint16 maxRate

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

159:     /// @notice Adds a new quota token
         /// @param token Address of the token
         function addQuotaToken(
             address token,
             uint16 rate

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="NC-27"></a>[NC-27] Incomplete NatSpec: `@return` is missing on actually documented functions
The following functions are missing `@return` NatSpec comments.

*Instances (10)*:
```solidity
File: ./src/CDPVault.sol

268:     /// @notice Borrows credit against collateral
         /// @param borrower Address of the borrower
         /// @param position Address of the position
         /// @param amount Amount of debt to generate [Underlying token scale]
         /// @dev The borrower will receive the amount of credit in the underlying token
         function borrow(address borrower, address position, uint256 amount) external returns (int256 deltaDebt) {

287:     /// @notice Repays credit against collateral
         /// @param borrower Address of the borrower
         /// @param position Address of the position
         /// @param amount Amount of debt to repay [Underlying token scale]
         /// @dev The borrower will repay the amount of credit in the underlying token
         function repay(address borrower, address position, uint256 amount) external returns (int256 deltaDebt){

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

246:     /// @dev Same as `deposit`, but allows to specify the referral code
         function depositWithReferral(
             uint256 assets,
             address receiver,
             uint256 referralCode
         ) external override returns (uint256 shares) {

307:     /// @dev Same as `mint`, but allows to specify the referral code
         function mintWithReferral(
             uint256 shares,
             address receiver,
             uint256 referralCode
         ) external override returns (uint256 assets) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

102:     /// @notice redeem assets and starts a cooldown to claim the converted underlying asset
         /// @param assets assets to redeem
         function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {

115:     /// @notice redeem shares into assets and starts a cooldown to claim the converted underlying asset
         /// @param shares shares to redeem
         function cooldownShares(uint256 shares) external ensureCooldownOn returns (uint256 assets) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

36:     /// @dev Initiate a flash loan
        /// @param receiver The receiver of the tokens in the loan, and the receiver of the callback
        /// @param token The loan currency
        /// @param amount The amount of tokens lent
        /// @param data Arbitrary data structure, intended to contain user-defined parameters
        function flashLoan(
            IERC3156FlashBorrower receiver,
            address token,
            uint256 amount,
            bytes calldata data
        ) external returns (bool);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/proxy/PoolAction.sol

242:     /// @notice Exit a protocol specific pool
         /// @param poolActionParams The parameters for the exit
         function exit(PoolActionParams memory poolActionParams) public returns (uint256 retAmount) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

399:     /// @notice Callback function for the flash loan taken out in increaseLever
         /// @param data The encoded bytes that were passed into the flash loan
         function onFlashLoan(
             address /*initiator*/,
             address /*token*/,
             uint256 amount,
             uint256 fee,
             bytes calldata data
         ) external returns (bytes32) {

451:     /// @notice Callback function for the credit flash loan taken out in decreaseLever
         /// @param data The encoded bytes that were passed into the credit flash loan
         function onCreditFlashLoan(
             address /*initiator*/,
             uint256 /*amount*/,
             uint256 fee,
             bytes calldata data
         ) external returns (bytes32) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

### <a name="NC-28"></a>[NC-28] File's first line is not an SPDX Identifier

*Instances (1)*:
```solidity
File: ./src/StakingLPEth.sol

1: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

### <a name="NC-29"></a>[NC-29] Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor
If a function is supposed to be access-controlled, a `modifier` should be used instead of a `require/if` statement for more readability.

*Instances (21)*:
```solidity
File: ./src/Flashlender.sol

101:         if (receiver.onFlashLoan(msg.sender, token, amount, fee, data) != CALLBACK_SUCCESS)

130:         if (receiver.onCreditFlashLoan(msg.sender, amount, fee, data) != CALLBACK_SUCCESS_CREDIT)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

61:         if (deposits[msg.sender].amount < _amount) revert InsufficientBalanceForCooldown();

73:             if (deposits[msg.sender].cooldownAmount == 0) revert NoTokensInCooldown();

74:             if (block.timestamp < deposits[msg.sender].cooldownStart + cooldownPeriod) revert CooldownPeriodNotPassed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

128:         if (_allowed[msg.sender]) {

137:         if (msg.sender != poolQuotaKeeper) revert CallerNotPoolQuotaKeeperException(); // U:[LP-2C]

142:         if (!_creditManagerSet.contains(msg.sender)) {

452:         if (msg.sender != owner) _spendAllowance({owner: owner, spender: msg.sender, amount: shares}); // U:[LP-8,9]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

24:         if (msg.sender != STAKING_VAULT) revert OnlyStakingVault();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

105:         if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

118:         if (shares > maxRedeem(msg.sender)) revert ExcessiveRedeemAmount();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

34:         if (msg.sender != vault) revert OnlyVault();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

65:         if (address(_proxies[msg.sender]) == address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/proxy/PositionAction.sol

408:         if (msg.sender != address(flashlender)) revert PositionAction__onFlashLoan__invalidSender();

459:         if (msg.sender != address(flashlender)) revert PositionAction__onCreditFlashLoan__invalidSender();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

323:         if (msg.sender != voter) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

280:         if (msg.sender != gauge) revert CallerNotGaugeException(); // U:[PQK-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Permission.sol

40:         emit ModifyPermission(msg.sender, msg.sender, caller, permitted);

48:         if (owner != msg.sender && !_permittedAgents[owner][msg.sender])

51:         emit ModifyPermission(msg.sender, owner, caller, permitted);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

### <a name="NC-30"></a>[NC-30] Constant state variables defined more than once
Rather than redefining state variable constant, consider using a library to store all constants as this will prevent data redundancy

*Instances (9)*:
```solidity
File: ./src/Flashlender.sol

19:     bytes32 public constant CALLBACK_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");

20:     bytes32 public constant CALLBACK_SUCCESS_CREDIT = keccak256("CreditFlashBorrower.onCreditFlashLoan");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/PoolV3.sol

65:     uint256 public constant override version = 3_00;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

106:     bytes32 public constant CALLBACK_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");

107:     bytes32 public constant CALLBACK_SUCCESS_CREDIT = keccak256("CreditFlashBorrower.onCreditFlashLoan");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/proxy/PositionAction.sol

73:     bytes32 public constant CALLBACK_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");

74:     bytes32 public constant CALLBACK_SUCCESS_CREDIT = keccak256("CreditFlashBorrower.onCreditFlashLoan");

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

29:     uint256 public constant override version = 3_00;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

37:     uint256 public constant override version = 3_00;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="NC-31"></a>[NC-31] Consider using named mappings
Consider moving to solidity version 0.8.18 or later, and using [named mappings](https://ethereum.stackexchange.com/questions/51629/how-to-name-the-arguments-in-mapping/145555#145555) to make it easier to understand the purpose of each mapping

*Instances (13)*:
```solidity
File: ./src/CDPVault.sol

119:     mapping(address => Position) public positions;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Locking.sol

27:     mapping(address => Deposit) public deposits;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

107:     mapping(address => DebtParams) internal _creditManagerDebt;

113:     mapping(address => bool) internal _allowed;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

32:     mapping(address => UserCooldown) public cooldowns;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/VaultRegistry.sol

17:     mapping(ICDPVault => bool) private registeredVaults;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

27:     mapping(address => Oracle) public oracles;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

27:     mapping(address => RewardState) public rewardState;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

31:     mapping(address => mapping(address => UserReward)) public userReward;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

35:     mapping(address => QuotaRateParams) public override quotaRateParams;

38:     mapping(address => mapping(address => UserVotes)) public override userTokenVotes;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

52:     mapping(address => TokenQuotaParams) internal totalQuotaParams;

63:     mapping(address => address) public creditManagers;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="NC-32"></a>[NC-32] `address`s shouldn't be hard-coded
It is often better to declare `address`es as `immutable`, and assign them via constructor arguments. This allows the code to remain the same across deployments on different networks, and avoids recompilation when addresses need to change.

*Instances (1)*:
```solidity
File: ./src/proxy/TransferAction.sol

39:     address public constant permit2 = 0x000000000022D473030F116dDEE9F6B43aC78BA3;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/TransferAction.sol)

### <a name="NC-33"></a>[NC-33] Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*Instances (4)*:
```solidity
File: ./src/vendor/BalancerErrors.sol

156:     uint256 internal constant AMP_END_TIME_TOO_CLOSE = 317;

165:     uint256 internal constant GRADUAL_UPDATE_TIME_TRAVEL = 326;

205:     uint256 internal constant MAX_BUFFER_PERIOD_DURATION = 405;

225:     uint256 internal constant BUFFER_PERIOD_EXPIRED = 425;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

### <a name="NC-34"></a>[NC-34] Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` *function* should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*Instances (1)*:
```solidity
File: ./src/StakingLPEth.sol

1: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

### <a name="NC-35"></a>[NC-35] Adding a `return` statement when the function defines a named return variable, is redundant

*Instances (25)*:
```solidity
File: ./src/CDPVault.sol

268:     /// @notice Borrows credit against collateral
         /// @param borrower Address of the borrower
         /// @param position Address of the position
         /// @param amount Amount of debt to generate [Underlying token scale]
         /// @dev The borrower will receive the amount of credit in the underlying token
         function borrow(address borrower, address position, uint256 amount) external returns (int256 deltaDebt) {
             uint256 scaledAmount = wdiv(amount, poolUnderlyingScale);
             deltaDebt = toInt256(scaledAmount);
             modifyCollateralAndDebt({
                 owner: position,
                 collateralizer: position,
                 creditor: borrower,
                 deltaCollateral: 0,
                 deltaDebt: deltaDebt
             });
     
             return deltaDebt;

287:     /// @notice Repays credit against collateral
         /// @param borrower Address of the borrower
         /// @param position Address of the position
         /// @param amount Amount of debt to repay [Underlying token scale]
         /// @dev The borrower will repay the amount of credit in the underlying token
         function repay(address borrower, address position, uint256 amount) external returns (int256 deltaDebt){
             uint256 scaledAmount = wdiv(amount, poolUnderlyingScale);
             deltaDebt = -toInt256(scaledAmount);
             modifyCollateralAndDebt({
                 owner: position,
                 collateralizer: position,
                 creditor: borrower,
                 deltaCollateral: 0,
                 deltaDebt: deltaDebt
             });
     
             return deltaDebt;

535:     function _calcQuotaRevenueChange(int256 deltaDebt) internal view returns (int256 quotaRevenueChange) {
             uint16 rate = IPoolQuotaKeeperV3(poolQuotaKeeper()).getQuotaRate(address(token));
             return QuotasLogic.calcQuotaRevenueChange(rate, deltaDebt);

834:     /// @notice Returns debt data for a position
         function getDebtInfo(
             address position
         ) external view returns (uint256 debt, uint256 accruedInterest, uint256 cumulativeQuotaInterest) {
             DebtData memory debtData = _calcDebt(positions[position]);
             return (debtData.debt, debtData.accruedInterest, debtData.cumulativeQuotaInterest);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

220:     /// @notice Total amount of underlying tokens managed by the pool, same as `expectedLiquidity`
         /// @dev Since `totalAssets` doesn't depend on underlying balance, pool is not vulnerable to the inflation attack
         function totalAssets() public view override(ERC4626, IERC4626) returns (uint256 assets) {
             return expectedLiquidity();

470:     /// @dev Internal conversion function (from assets to shares) with support for rounding direction
         /// @dev Pool is not vulnerable to the inflation attack, so the simplified implementation w/o virtual shares is used
         function _convertToShares(uint256 assets) internal pure returns (uint256 shares) {
             // uint256 supply = totalSupply();
             return assets; //(assets == 0 || supply == 0) ? assets : assets.mulDiv(supply, totalAssets(), rounding);

477:     /// @dev Internal conversion function (from shares to assets) with support for rounding direction
         /// @dev Pool is not vulnerable to the inflation attack, so the simplified implementation w/o virtual shares is used
         function _convertToAssets(uint256 shares) internal pure returns (uint256 assets) {
             //uint256 supply = totalSupply();
             return shares; //(supply == 0) ? shares : shares.mulDiv(totalAssets(), supply, rounding);

508:     /// @notice Amount available to borrow for a given credit manager
         function creditManagerBorrowable(address creditManager) external view override returns (uint256 borrowable) {
             borrowable = _borrowable(_totalDebt); // U:[LP-12]
             if (borrowable == 0) return 0; // U:[LP-12]

508:     /// @notice Amount available to borrow for a given credit manager
         function creditManagerBorrowable(address creditManager) external view override returns (uint256 borrowable) {
             borrowable = _borrowable(_totalDebt); // U:[LP-12]
             if (borrowable == 0) return 0; // U:[LP-12]
     
             borrowable = Math.min(borrowable, _borrowable(_creditManagerDebt[creditManager])); // U:[LP-12]
             if (borrowable == 0) return 0; // U:[LP-12]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

76:     /// @notice Returns the status of the oracle
        /// @param token Token address
        /// @dev The status is valid if the price is validated and not stale
        function getStatus(address token) public view virtual override returns (bool status) {
            return _getStatus(token);

93:     /// @notice Fetches and validates the latest price from Chainlink
        /// @return isValid Whether the price is valid based on the value range and staleness
        /// @return price Asset price [WAD]
        function _fetchAndValidate(address token) internal view returns (bool isValid, uint256 price) {
            Oracle memory oracle = oracles[token];
            try AggregatorV3Interface(oracle.aggregator).latestRoundData() returns (
                uint80 /*roundId*/,
                int256 answer,
                uint256 /*startedAt*/,
                uint256 updatedAt,
                uint80 /*answeredInRound*/
            ) {
                isValid = (answer > 0 && block.timestamp - updatedAt <= oracle.stalePeriod);
                return (isValid, wdiv(uint256(answer), oracle.aggregatorScale));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

98:     /// @notice Returns the status of the oracle
        /// @param /*token*/ Token address, ignored for this oracle
        /// @dev The status is valid if the price is validated and not stale
        function getStatus(address /*token*/) public view virtual override returns (bool status) {
            return _getStatus();

119:     /// @notice Fetches and validates the latest price from Chainlink
         /// @return isValid Whether the price is valid based on the value range and staleness
         /// @return price Asset price [WAD]
         function _fetchAndValidate() internal view returns (bool isValid, uint256 price) {
             try AggregatorV3Interface(aggregator).latestRoundData() returns (
                 uint80,
                 int256 answer,
                 uint256 /*startedAt*/,
                 uint256 updatedAt,
                 uint80
             ) {
                 isValid = (answer > 0 && block.timestamp - updatedAt <= stalePeriod);
                 return (isValid, wdiv(uint256(answer), aggregatorScale));

137:     /// @notice Returns the status of the oracle
         /// @return status Whether the oracle is valid
         /// @dev The status is valid if the price is validated and not stale
         function _getStatus() private view returns (bool status) {
             (status, ) = _fetchAndValidate();
             if (status) return _validatePtOracle();

145:     /// @notice Validates the PT oracle
         /// @return isValid Whether the PT oracle is valid for this market and twap window
         function _validatePtOracle() internal view returns (bool isValid) {
             try ptOracle.getOracleState(address(market), twapWindow) returns (
                 bool increaseCardinalityRequired,
                 uint16,
                 bool oldestObservationSatisfied
             ) {
                 if (!increaseCardinalityRequired && oldestObservationSatisfied) return true;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

44:     function _updateRewardIndex()
            internal
            virtual
            override
            returns (address[] memory tokens, uint256[] memory indexes)
        {
            tokens = market.getRewardTokens();
            indexes = new uint256[](tokens.length);
    
            if (tokens.length == 0) return (tokens, indexes);

90:     /// @dev this function doesn't need redeemExternal since redeemExternal is bundled in updateRewardIndex
        /// @dev this function also has to update rewardState.lastBalance
        function _doTransferOutRewards(
            address user
        ) internal virtual override returns (address[] memory tokens, uint256[] memory rewardAmounts, address to) {
            tokens = market.getRewardTokens();
            rewardAmounts = new uint256[](tokens.length);
            for (uint256 i = 0; i < tokens.length; i++) {
                rewardAmounts[i] = userReward[tokens[i]][user].accrued;
                if (rewardAmounts[i] != 0) {
                    userReward[tokens[i]][user].accrued = 0;
                    rewardState[tokens[i]].lastBalance -= rewardAmounts[i].Uint128();
                    //_transferOut(tokens[i], receiver, rewardAmounts[i]);
                }
            }
    
            if (proxyRegistry.isProxy(user)) {
                to = IPRBProxy(user).owner();
            } else {
                to = user;
            }
            return (tokens, rewardAmounts, to);

126:     function handleRewardsOnWithdraw(
             address user,
             uint collateralAmountBefore,
             int256 deltaCollateral
         ) external virtual onlyVault returns (address[] memory tokens, uint256[] memory amounts, address to) {
             _updateAndDistributeRewards(user, collateralAmountBefore, deltaCollateral);
             return _doTransferOutRewards(user);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/PoolAction.sol

252:     function _balancerExit(PoolActionParams memory poolActionParams) internal returns (uint256 retAmount) {
             (
                 bytes32 poolId,
                 address bpt,
                 uint256 bptAmount,
                 uint256 outIndex,
                 address[] memory assets,
                 uint256[] memory minAmountsOut
             ) = abi.decode(poolActionParams.args, (bytes32, address, uint256, uint256, address[], uint256[]));
     
             if (bptAmount != 0) IERC20(bpt).forceApprove(address(balancerVault), bptAmount);
     
             uint256 tmpOutIndex = outIndex;
             for (uint256 i = 0; i <= tmpOutIndex; i++) if (assets[i] == bpt) tmpOutIndex++;
             uint256 balanceBefore = IERC20(assets[tmpOutIndex]).balanceOf(poolActionParams.recipient);
     
             balancerVault.exitPool(
                 poolId,
                 address(this),
                 payable(poolActionParams.recipient),
                 ExitPoolRequest({
                     assets: assets,
                     minAmountsOut: minAmountsOut,
                     userData: abi.encode(ExitKind.EXACT_BPT_IN_FOR_ONE_TOKEN_OUT, bptAmount, outIndex),
                     toInternalBalance: false
                 })
             );
     
             return IERC20(assets[tmpOutIndex]).balanceOf(poolActionParams.recipient) - balanceBefore;

283:     function _pendleExit(PoolActionParams memory poolActionParams) internal returns (uint256 retAmount) {
             (address market, uint256 netLpIn, address tokenOut) = abi.decode(
                 poolActionParams.args,
                 (address, uint256, address)
             );
     
             (IStandardizedYield SY, IPPrincipalToken PT, IPYieldToken YT) = IPMarket(market).readTokens();
     
             if (poolActionParams.recipient != address(this)) {
                 IPMarket(market).transferFrom(poolActionParams.recipient, market, netLpIn);
             } else {
                 IPMarket(market).transfer(market, netLpIn);
             }
     
             uint256 netSyToRedeem;
     
             if (PT.isExpired()) {
                 (uint256 netSyRemoved, ) = IPMarket(market).burn(address(SY), address(YT), netLpIn);
                 uint256 netSyFromPt = YT.redeemPY(address(SY));
                 netSyToRedeem = netSyRemoved + netSyFromPt;
             } else {
                 (uint256 netSyRemoved, uint256 netPtRemoved) = IPMarket(market).burn(address(SY), market, netLpIn);
                 bytes memory empty;
                 (uint256 netSySwappedOut, ) = IPMarket(market).swapExactPtForSy(address(SY), netPtRemoved, empty);
                 netSyToRedeem = netSyRemoved + netSySwappedOut;
             }
     
             return SY.redeem(poolActionParams.recipient, netSyToRedeem, tokenOut, poolActionParams.minOut, true);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionActionPendle.sol

86:     /// @notice Hook to increase lever by depositing collateral into the CDPVault
        /// @param leverParams LeverParams struct
        /// @param /*upFrontToken*/ the address of the token passed up front
        /// @param /*upFrontAmount*/ the amount of tokens passed up front [CDPVault.tokenScale()]
        /// @param /*swapAmountOut*/ the amount of tokens received from the stablecoin flash loan swap [CDPVault.tokenScale()]
        /// @return addCollateralAmount Amount of collateral added to CDPVault position [wad]
        function _onIncreaseLever(
            LeverParams memory leverParams,
            address /*upFrontToken*/,
            uint256 /*upFrontAmount*/,
            uint256 /*swapAmountOut*/
        ) internal override returns (uint256 addCollateralAmount) {
            if (leverParams.auxAction.args.length != 0) {
                _delegateCall(address(poolAction), abi.encodeWithSelector(poolAction.join.selector, leverParams.auxAction));
            }
            addCollateralAmount = ICDPVault(leverParams.vault).token().balanceOf(address(this));
            IERC20(leverParams.collateralToken).forceApprove(leverParams.vault, addCollateralAmount);
            // deposit into the CDP Vault
            return addCollateralAmount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionActionPendle.sol)

```solidity
File: ./src/proxy/SwapAction.sol

400:             netSyToRedeem = netSyRemoved + netSyFromPt;
             } else {
                 (uint256 netSyRemoved, uint256 netPtRemoved) = IPMarket(market).burn(address(SY), market, netLpIn);
                 bytes memory empty;
                 (uint256 netSySwappedOut, ) = IPMarket(market).swapExactPtForSy(address(SY), netPtRemoved, empty);
                 netSyToRedeem = netSyRemoved + netSySwappedOut;
             }
     
             return SY.redeem(recipient, netSyToRedeem, tokenOut, minOut, true);
         }
     
         /// @notice Helper function that decodes the swap params and returns the token that will be swapped into
         /// @param swapParams The parameters for the swap
         /// @return token The token that will be swapped into
         function getSwapToken(SwapParams calldata swapParams) public pure returns (address token) {
             if (swapParams.swapProtocol == SwapProtocol.BALANCER) {
                 (, address[] memory primarySwapPath) = abi.decode(swapParams.args, (bytes32[], address[]));
                 
                 if (swapParams.swapType == SwapType.EXACT_OUT){ 
                     // For EXACT_OUT, the token that will be swapped into is the first token in the path
                     token = primarySwapPath[0];

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

45:     /// @dev Upper-bounds requested quota increase such that the resulting total quota doesn't exceed the limit
        function calcActualQuotaChange(
            uint96 totalQuoted,
            uint96 limit,
            int96 requestedChange
        ) internal pure returns (int96 quotaChange) {
            if (totalQuoted >= limit) {
                return 0;
            }
    
            unchecked {
                uint96 maxQuotaCapacity = limit - totalQuoted;
                // The function is never called with `requestedChange < 0`, so casting it to `uint96` is safe
                // With correct configuration, `limit < type(int96).max`, so casting `maxQuotaCapacity` to `int96` is safe
                return uint96(requestedChange) > maxQuotaCapacity ? int96(maxQuotaCapacity) : requestedChange;

45:     /// @dev Upper-bounds requested quota increase such that the resulting total quota doesn't exceed the limit
        function calcActualQuotaChange(
            uint96 totalQuoted,
            uint96 limit,
            int96 requestedChange
        ) internal pure returns (int96 quotaChange) {
            if (totalQuoted >= limit) {
                return 0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

213: /// @dev Taken from https://github.com/Vectorized/solady/blob/cde0a5fb594da8655ba6bfcdc2e40a7c870c0cc0/src/utils/FixedPointMathLib.sol#L116
     /// @dev Returns `exp(x)`, denominated in `WAD`.
     function wexp(int256 x) pure returns (int256 r) {
         unchecked {
             // When the result is < 0.5 we return zero. This happens when
             // x <= floor(log(0.5e18) * 1e18) ~ -42e18
             if (x <= -42139678854452767551) return r;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

### <a name="NC-36"></a>[NC-36] Take advantage of Custom Error's return value property
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

*Instances (103)*:
```solidity
File: ./src/CDPVault.sol

216:         else revert CDPVault__setParameter_unrecognizedParameter();

227:         else revert CDPVault__setParameter_unrecognizedParameter();

375:             revert CDPVault__modifyPosition_debtFloor();

436:         ) revert CDPVault__modifyCollateralAndDebt_noPermission();

526:         ) revert CDPVault__modifyCollateralAndDebt_notSafe();

584:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

597:         if (spotPrice_ == 0) revert CDPVault__liquidatePosition_invalidSpotPrice();

600:         if (calcTotalDebt(debtData) > wmul(position.collateral, discountedPrice)) revert CDPVault__BadDebt();

606:         if (takeCollateral > position.collateral) revert CDPVault__tooHighRepayAmount();

610:             revert CDPVault__liquidatePosition_notUnsafe();

655:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

665:         if (spotPrice_ == 0) revert CDPVault__liquidatePosition_invalidSpotPrice();

668:             revert CDPVault__liquidatePosition_notUnsafe();

673:         if (calcTotalDebt(debtData) <= wmul(position.collateral, discountedPrice)) revert CDPVault__noBadDebt();

676:         if (takeCollateral < position.collateral) revert CDPVault__repayAmountNotEnough();

852:         if (tokenAddress == address(token)) revert CDPVault__recoverERC20_invalidToken();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

76:         if (token != address(underlyingToken)) revert Flash__flashFee_unsupportedToken();

93:         if (token != address(underlyingToken)) revert Flash__flashLoan_unsupportedToken();

102:             revert Flash__flashLoan_callbackFailed();

131:             revert Flash__creditFlashLoan_callbackFailed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

48:         if (_amount == 0) revert AmountIsZero();

60:         if (_amount == 0) revert AmountIsZero();

61:         if (deposits[msg.sender].amount < _amount) revert InsufficientBalanceForCooldown();

73:             if (deposits[msg.sender].cooldownAmount == 0) revert NoTokensInCooldown();

74:             if (block.timestamp < deposits[msg.sender].cooldownStart + cooldownPeriod) revert CooldownPeriodNotPassed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

117:         _revertIfCallerIsNotPoolQuotaKeeper();

123:         _revertIfCallerNotCreditManager();

131:             _revertIfLocked();

136:     function _revertIfCallerIsNotPoolQuotaKeeper() internal view {

137:         if (msg.sender != poolQuotaKeeper) revert CallerNotPoolQuotaKeeperException(); // U:[LP-2C]

141:     function _revertIfCallerNotCreditManager() internal view {

143:             revert CallerNotCreditManagerException(); // U:[PQK-4]

147:     function _revertIfLocked() internal view {

148:         if (locked) revert PoolV3LockedException(); // U:[LP-2C]

268:             revert NoEthSent();

273:             revert WrongUnderlying();

543:             revert CreditManagerCantBorrowException(); // U:[LP-2C,13A]

588:             revert CallerNotCreditManagerException(); // U:[LP-2C,14A]

813:             revert IncompatiblePoolQuotaKeeperException(); // U:[LP-23C]

856:                 revert IncompatibleCreditManagerException(); // U:[LP-25C]

875:             revert IncorrectParameterException(); // U:[LP-26A]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

24:         if (msg.sender != STAKING_VAULT) revert OnlyStakingVault();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

44:         if (cooldownDuration != 0) revert OperationNotAllowed();

50:         if (cooldownDuration == 0) revert OperationNotAllowed();

98:             revert InvalidCooldown();

105:         if (assets > maxWithdraw(msg.sender)) revert ExcessiveWithdrawAmount();

118:         if (shares > maxRedeem(msg.sender)) revert ExcessiveRedeemAmount();

132:             revert InvalidCooldown();

143:         if (_totalSupply > 0 && _totalSupply < MIN_SHARES) revert MinSharesViolation();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/VaultRegistry.sol

40:         if (registeredVaults[vault]) revert VaultRegistry__addVault_vaultAlreadyRegistered();

50:         if (!registeredVaults[vault]) revert VaultRegistry__removeVault_vaultNotFound();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

90:         if (!isValid) revert ChainlinkOracle__spot_invalidValue();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

91:         if (_getStatus()) revert PendleLPOracle__authorizeUpgrade_validStatus();

112:         if (!isValid) revert PendleLPOracle__spot_invalidValue();

114:         if (!isValidPtOracle) revert PendleLPOracle__validatePtOracle_invalidValue();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

34:         if (msg.sender != vault) revert OnlyVault();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/BaseAction.sol

9:     error Action__revertBytes_emptyRevertBytes();

35:         revert Action__revertBytes_emptyRevertBytes();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/BaseAction.sol)

```solidity
File: ./src/proxy/PoolAction.sol

88:                     revert PoolAction__transferAndJoin_invalidPermitParams();

109:             } else revert PoolAction__transferAndJoin_unsupportedProtocol();

123:             revert PoolAction__join_unsupportedProtocol();

249:         } else revert PoolAction__exit_unsupportedProtocol();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction.sol

122:         ) revert PositionAction__constructor_InvalidParam();

139:         if (address(this) == self) revert PositionAction__onlyDelegatecall();

144:         if (!vaultRegistry.isVaultRegistered(vault)) revert PositionAction__unregisteredVault();

328:         ) revert PositionAction__increaseLever_invalidPrimarySwap();

336:         ) revert PositionAction__increaseLever_invalidAuxSwap();

371:         if (leverParams.primarySwap.recipient != self) revert PositionAction__decreaseLever_invalidPrimarySwap();

381:                 revert PositionAction__decreaseLever_invalidResidualRecipient();

408:         if (msg.sender != address(flashlender)) revert PositionAction__onFlashLoan__invalidSender();

459:         if (msg.sender != address(flashlender)) revert PositionAction__onCreditFlashLoan__invalidSender();

551:             ) revert PositionAction__deposit_InvalidAuxSwap();

610:                 revert PositionAction__borrow_InvalidAuxSwap();

629:             if (creditParams.auxSwap.recipient != address(this)) revert PositionAction__repay_InvalidAuxSwap();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

76:     error SwapAction__revertBytes_emptyRevertBytes();

148:         } else revert SwapAction__swap_notSupported();

429:             revert SwapAction__swap_notSupported();

442:         revert SwapAction__revertBytes_emptyRevertBytes();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

68:         _revertIfCallerNotVoter(); // U:[GA-02]

105:                 if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-15]

145:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-10]

188:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-10]

196:             if (uv.votesLpSide < votes) revert InsufficientVotesException();

202:             if (uv.votesCaSide < votes) revert InsufficientVotesException();

243:             revert TokenNotAllowedException(); // U:[GA-04]

292:         if (!isTokenAdded(token)) revert TokenNotAllowedException(); // U:[GA-06A, GA-06B]

307:             revert IncorrectParameterException(); // U:[GA-04]

322:     function _revertIfCallerNotVoter() internal view {

324:             revert CallerNotVoterException(); // U:[GA-02]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

67:         _revertIfCallerNotGauge();

170:             revert TokenAlreadyAddedException(); // U:[PQK-6]

274:             revert TokenIsNotQuotedException(); // U:[PQK-14]

279:     function _revertIfCallerNotGauge() internal view {

280:         if (msg.sender != gauge) revert CallerNotGaugeException(); // U:[PQK-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Math.sol

18:     if (x >= 1 << 255) revert Math__toInt256_overflow();

24:     if (x >= 1 << 64) revert Math__toUint64_overflow();

62:     if ((y > 0 && z < x) || (y < 0 && z > x)) revert Math__add_overflow_signed();

70:     if ((y > 0 && z > x) || (y < 0 && z < x)) revert Math__sub_overflow_signed();

77:         if (int256(x) < 0 || (y != 0 && z / y != int256(x))) revert Math__mul_overflow_signed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/utils/Permission.sol

49:             revert Permission__modifyPermission_notPermitted();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

25:     if (_start + 20 < _start) revert UniswapV3Router_toAddress_overflow();

26:     if (_bytes.length < _start + 20) revert UniswapV3Router_toAddress_outOfBounds();

40:     if (path.length < 20) revert UniswapV3Router_decodeLastToken_invalidPath();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

### <a name="NC-37"></a>[NC-37] Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While this won't save gas in the recent solidity versions, this is shorter and more readable (this is especially true in calculations).

*Instances (1)*:
```solidity
File: ./src/CDPVault.sol

71:     uint256 constant INDEX_PRECISION = 10 ** 9;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="NC-38"></a>[NC-38] Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be:

1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

However, the contract(s) below do not follow this ordering

*Instances (17)*:
```solidity
File: ./src/CDPVault.sol

1: 
   Current order:
   FunctionDefinition.mintProfit
   FunctionDefinition.enter
   FunctionDefinition.exit
   FunctionDefinition.addAvailable
   FunctionDefinition.handleRewardsOnDeposit
   FunctionDefinition.handleRewardsOnWithdraw
   UsingForDirective.IERC20
   UsingForDirective.SafeCast
   VariableDeclaration.oracle
   VariableDeclaration.token
   VariableDeclaration.tokenScale
   VariableDeclaration.INDEX_PRECISION
   VariableDeclaration.pool
   VariableDeclaration.poolUnderlying
   VariableDeclaration.poolUnderlyingScale
   StructDefinition.VaultConfig
   VariableDeclaration.vaultConfig
   VariableDeclaration.totalDebt
   StructDefinition.DebtData
   StructDefinition.Position
   VariableDeclaration.positions
   StructDefinition.LiquidationConfig
   VariableDeclaration.liquidationConfig
   VariableDeclaration.rewardController
   VariableDeclaration.rewardManager
   EventDefinition.ModifyPosition
   EventDefinition.ModifyCollateralAndDebt
   EventDefinition.SetParameter
   EventDefinition.SetParameter
   EventDefinition.LiquidatePosition
   EventDefinition.VaultCreated
   ErrorDefinition.CDPVault__modifyPosition_debtFloor
   ErrorDefinition.CDPVault__modifyCollateralAndDebt_notSafe
   ErrorDefinition.CDPVault__modifyCollateralAndDebt_noPermission
   ErrorDefinition.CDPVault__modifyCollateralAndDebt_maxUtilizationRatio
   ErrorDefinition.CDPVault__setParameter_unrecognizedParameter
   ErrorDefinition.CDPVault__liquidatePosition_notUnsafe
   ErrorDefinition.CDPVault__liquidatePosition_invalidSpotPrice
   ErrorDefinition.CDPVault__liquidatePosition_invalidParameters
   ErrorDefinition.CDPVault__noBadDebt
   ErrorDefinition.CDPVault__BadDebt
   ErrorDefinition.CDPVault__repayAmountNotEnough
   ErrorDefinition.CDPVault__tooHighRepayAmount
   ErrorDefinition.CDPVault__recoverERC20_invalidToken
   FunctionDefinition.constructor
   FunctionDefinition.setParameter
   FunctionDefinition.setParameter
   FunctionDefinition.deposit
   FunctionDefinition.withdraw
   FunctionDefinition.borrow
   FunctionDefinition.repay
   FunctionDefinition.spotPrice
   FunctionDefinition._handleTokenRewards
   FunctionDefinition.getRewards
   FunctionDefinition._modifyPosition
   FunctionDefinition._isCollateralized
   FunctionDefinition.modifyCollateralAndDebt
   FunctionDefinition._calcQuotaRevenueChange
   FunctionDefinition._calcDebt
   FunctionDefinition._getQuotedTokensData
   FunctionDefinition.liquidatePosition
   FunctionDefinition.liquidatePositionBadDebt
   FunctionDefinition.calcDecrease
   FunctionDefinition.calcAccruedInterest
   FunctionDefinition.virtualDebt
   FunctionDefinition.calcTotalDebt
   FunctionDefinition.poolQuotaKeeper
   FunctionDefinition.quotasInterest
   FunctionDefinition.getDebtData
   FunctionDefinition.getDebtInfo
   FunctionDefinition.recoverERC20
   
   Suggested order:
   UsingForDirective.IERC20
   UsingForDirective.SafeCast
   VariableDeclaration.oracle
   VariableDeclaration.token
   VariableDeclaration.tokenScale
   VariableDeclaration.INDEX_PRECISION
   VariableDeclaration.pool
   VariableDeclaration.poolUnderlying
   VariableDeclaration.poolUnderlyingScale
   VariableDeclaration.vaultConfig
   VariableDeclaration.totalDebt
   VariableDeclaration.positions
   VariableDeclaration.liquidationConfig
   VariableDeclaration.rewardController
   VariableDeclaration.rewardManager
   StructDefinition.VaultConfig
   StructDefinition.DebtData
   StructDefinition.Position
   StructDefinition.LiquidationConfig
   ErrorDefinition.CDPVault__modifyPosition_debtFloor
   ErrorDefinition.CDPVault__modifyCollateralAndDebt_notSafe
   ErrorDefinition.CDPVault__modifyCollateralAndDebt_noPermission
   ErrorDefinition.CDPVault__modifyCollateralAndDebt_maxUtilizationRatio
   ErrorDefinition.CDPVault__setParameter_unrecognizedParameter
   ErrorDefinition.CDPVault__liquidatePosition_notUnsafe
   ErrorDefinition.CDPVault__liquidatePosition_invalidSpotPrice
   ErrorDefinition.CDPVault__liquidatePosition_invalidParameters
   ErrorDefinition.CDPVault__noBadDebt
   ErrorDefinition.CDPVault__BadDebt
   ErrorDefinition.CDPVault__repayAmountNotEnough
   ErrorDefinition.CDPVault__tooHighRepayAmount
   ErrorDefinition.CDPVault__recoverERC20_invalidToken
   EventDefinition.ModifyPosition
   EventDefinition.ModifyCollateralAndDebt
   EventDefinition.SetParameter
   EventDefinition.SetParameter
   EventDefinition.LiquidatePosition
   EventDefinition.VaultCreated
   FunctionDefinition.mintProfit
   FunctionDefinition.enter
   FunctionDefinition.exit
   FunctionDefinition.addAvailable
   FunctionDefinition.handleRewardsOnDeposit
   FunctionDefinition.handleRewardsOnWithdraw
   FunctionDefinition.constructor
   FunctionDefinition.setParameter
   FunctionDefinition.setParameter
   FunctionDefinition.deposit
   FunctionDefinition.withdraw
   FunctionDefinition.borrow
   FunctionDefinition.repay
   FunctionDefinition.spotPrice
   FunctionDefinition._handleTokenRewards
   FunctionDefinition.getRewards
   FunctionDefinition._modifyPosition
   FunctionDefinition._isCollateralized
   FunctionDefinition.modifyCollateralAndDebt
   FunctionDefinition._calcQuotaRevenueChange
   FunctionDefinition._calcDebt
   FunctionDefinition._getQuotedTokensData
   FunctionDefinition.liquidatePosition
   FunctionDefinition.liquidatePositionBadDebt
   FunctionDefinition.calcDecrease
   FunctionDefinition.calcAccruedInterest
   FunctionDefinition.virtualDebt
   FunctionDefinition.calcTotalDebt
   FunctionDefinition.poolQuotaKeeper
   FunctionDefinition.quotasInterest
   FunctionDefinition.getDebtData
   FunctionDefinition.getDebtInfo
   FunctionDefinition.recoverERC20

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

1: 
   Current order:
   VariableDeclaration.CALLBACK_SUCCESS
   VariableDeclaration.CALLBACK_SUCCESS_CREDIT
   VariableDeclaration.pool
   VariableDeclaration.protocolFee
   VariableDeclaration.underlyingToken
   EventDefinition.FlashLoan
   EventDefinition.CreditFlashLoan
   ErrorDefinition.Flash__flashFee_unsupportedToken
   ErrorDefinition.Flash__flashLoan_unsupportedToken
   ErrorDefinition.Flash__flashLoan_callbackFailed
   ErrorDefinition.Flash__creditFlashLoan_callbackFailed
   ErrorDefinition.Flash__creditFlashLoan_unsupportedToken
   FunctionDefinition.constructor
   FunctionDefinition.maxFlashLoan
   FunctionDefinition.flashFee
   FunctionDefinition.flashLoan
   FunctionDefinition.creditFlashLoan
   
   Suggested order:
   VariableDeclaration.CALLBACK_SUCCESS
   VariableDeclaration.CALLBACK_SUCCESS_CREDIT
   VariableDeclaration.pool
   VariableDeclaration.protocolFee
   VariableDeclaration.underlyingToken
   ErrorDefinition.Flash__flashFee_unsupportedToken
   ErrorDefinition.Flash__flashLoan_unsupportedToken
   ErrorDefinition.Flash__flashLoan_callbackFailed
   ErrorDefinition.Flash__creditFlashLoan_callbackFailed
   ErrorDefinition.Flash__creditFlashLoan_unsupportedToken
   EventDefinition.FlashLoan
   EventDefinition.CreditFlashLoan
   FunctionDefinition.constructor
   FunctionDefinition.maxFlashLoan
   FunctionDefinition.flashFee
   FunctionDefinition.flashLoan
   FunctionDefinition.creditFlashLoan

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

1: 
   Current order:
   UsingForDirective.IERC20
   ErrorDefinition.AmountIsZero
   ErrorDefinition.InsufficientBalanceForCooldown
   ErrorDefinition.NoTokensInCooldown
   ErrorDefinition.CooldownPeriodNotPassed
   VariableDeclaration.cooldownPeriod
   VariableDeclaration.token
   StructDefinition.Deposit
   VariableDeclaration.deposits
   EventDefinition.Deposited
   EventDefinition.Withdrawn
   EventDefinition.CooldownPeriodSet
   EventDefinition.CooldownInitiated
   FunctionDefinition.constructor
   FunctionDefinition.setCooldownPeriod
   FunctionDefinition.deposit
   FunctionDefinition.initiateCooldown
   FunctionDefinition.withdraw
   FunctionDefinition.getBalance
   FunctionDefinition.getCoolDownInfo
   
   Suggested order:
   UsingForDirective.IERC20
   VariableDeclaration.cooldownPeriod
   VariableDeclaration.token
   VariableDeclaration.deposits
   StructDefinition.Deposit
   ErrorDefinition.AmountIsZero
   ErrorDefinition.InsufficientBalanceForCooldown
   ErrorDefinition.NoTokensInCooldown
   ErrorDefinition.CooldownPeriodNotPassed
   EventDefinition.Deposited
   EventDefinition.Withdrawn
   EventDefinition.CooldownPeriodSet
   EventDefinition.CooldownInitiated
   FunctionDefinition.constructor
   FunctionDefinition.setCooldownPeriod
   FunctionDefinition.deposit
   FunctionDefinition.initiateCooldown
   FunctionDefinition.withdraw
   FunctionDefinition.getBalance
   FunctionDefinition.getCoolDownInfo

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

1: 
   Current order:
   UsingForDirective.Math
   UsingForDirective.SafeCast
   UsingForDirective.SafeCast
   UsingForDirective.CreditLogic
   UsingForDirective.EnumerableSet.AddressSet
   UsingForDirective.IERC20
   ErrorDefinition.CallerNotManagerException
   ErrorDefinition.PoolV3LockedException
   ErrorDefinition.NoEthSent
   ErrorDefinition.WethTransferFailed
   ErrorDefinition.WrongUnderlying
   VariableDeclaration.version
   VariableDeclaration.WETH
   VariableDeclaration.addressProvider
   VariableDeclaration.underlyingToken
   VariableDeclaration.treasury
   VariableDeclaration.interestRateModel
   VariableDeclaration.lastBaseInterestUpdate
   VariableDeclaration.lastQuotaRevenueUpdate
   VariableDeclaration.withdrawFee
   VariableDeclaration.locked
   VariableDeclaration.poolQuotaKeeper
   VariableDeclaration._quotaRevenue
   VariableDeclaration._baseInterestRate
   VariableDeclaration._baseInterestIndexLU
   VariableDeclaration._expectedLiquidityLU
   VariableDeclaration._totalDebt
   VariableDeclaration._creditManagerDebt
   VariableDeclaration._creditManagerSet
   VariableDeclaration._allowed
   ModifierDefinition.poolQuotaKeeperOnly
   ModifierDefinition.creditManagerOnly
   ModifierDefinition.whenNotLocked
   FunctionDefinition._revertIfCallerIsNotPoolQuotaKeeper
   FunctionDefinition._revertIfCallerNotCreditManager
   FunctionDefinition._revertIfLocked
   FunctionDefinition.constructor
   FunctionDefinition.decimals
   FunctionDefinition.creditManagers
   FunctionDefinition.availableLiquidity
   FunctionDefinition.expectedLiquidity
   FunctionDefinition.expectedLiquidityLU
   FunctionDefinition.totalAssets
   FunctionDefinition.deposit
   FunctionDefinition.depositWithReferral
   FunctionDefinition.depositETH
   FunctionDefinition.mint
   FunctionDefinition.mintWithReferral
   FunctionDefinition.withdraw
   FunctionDefinition.redeem
   FunctionDefinition.previewDeposit
   FunctionDefinition.previewMint
   FunctionDefinition.previewWithdraw
   FunctionDefinition.previewRedeem
   FunctionDefinition.maxDeposit
   FunctionDefinition.maxMint
   FunctionDefinition.maxWithdraw
   FunctionDefinition.maxRedeem
   FunctionDefinition._deposit
   FunctionDefinition._registerDeposit
   FunctionDefinition._withdraw
   FunctionDefinition._convertToShares
   FunctionDefinition._convertToAssets
   FunctionDefinition.totalBorrowed
   FunctionDefinition.totalDebtLimit
   FunctionDefinition.creditManagerBorrowed
   FunctionDefinition.creditManagerDebtLimit
   FunctionDefinition.creditManagerBorrowable
   FunctionDefinition.lendCreditAccount
   FunctionDefinition.repayCreditAccount
   FunctionDefinition._borrowable
   FunctionDefinition.baseInterestRate
   FunctionDefinition.supplyRate
   FunctionDefinition.baseInterestIndex
   FunctionDefinition.baseInterestIndexLU
   FunctionDefinition._calcBaseInterestAccrued
   FunctionDefinition.calcAccruedQuotaInterest
   FunctionDefinition._updateBaseInterest
   FunctionDefinition._calcBaseInterestAccrued
   FunctionDefinition._calcBaseInterestIndex
   FunctionDefinition.quotaRevenue
   FunctionDefinition.updateQuotaRevenue
   FunctionDefinition.setQuotaRevenue
   FunctionDefinition._calcQuotaRevenueAccrued
   FunctionDefinition._setQuotaRevenue
   FunctionDefinition._calcQuotaRevenueAccrued
   FunctionDefinition.setInterestRateModel
   FunctionDefinition.setPoolQuotaKeeper
   FunctionDefinition.setTreasury
   FunctionDefinition.setTotalDebtLimit
   FunctionDefinition.setCreditManagerDebtLimit
   FunctionDefinition.setWithdrawFee
   FunctionDefinition.setAllowed
   FunctionDefinition.setLock
   FunctionDefinition.isAllowed
   FunctionDefinition._setTotalDebtLimit
   FunctionDefinition._amountWithFee
   FunctionDefinition._amountMinusFee
   FunctionDefinition._amountWithWithdrawalFee
   FunctionDefinition._amountMinusWithdrawalFee
   FunctionDefinition._convertToU256
   FunctionDefinition._convertToU128
   FunctionDefinition.mintProfit
   
   Suggested order:
   UsingForDirective.Math
   UsingForDirective.SafeCast
   UsingForDirective.SafeCast
   UsingForDirective.CreditLogic
   UsingForDirective.EnumerableSet.AddressSet
   UsingForDirective.IERC20
   VariableDeclaration.version
   VariableDeclaration.WETH
   VariableDeclaration.addressProvider
   VariableDeclaration.underlyingToken
   VariableDeclaration.treasury
   VariableDeclaration.interestRateModel
   VariableDeclaration.lastBaseInterestUpdate
   VariableDeclaration.lastQuotaRevenueUpdate
   VariableDeclaration.withdrawFee
   VariableDeclaration.locked
   VariableDeclaration.poolQuotaKeeper
   VariableDeclaration._quotaRevenue
   VariableDeclaration._baseInterestRate
   VariableDeclaration._baseInterestIndexLU
   VariableDeclaration._expectedLiquidityLU
   VariableDeclaration._totalDebt
   VariableDeclaration._creditManagerDebt
   VariableDeclaration._creditManagerSet
   VariableDeclaration._allowed
   ErrorDefinition.CallerNotManagerException
   ErrorDefinition.PoolV3LockedException
   ErrorDefinition.NoEthSent
   ErrorDefinition.WethTransferFailed
   ErrorDefinition.WrongUnderlying
   ModifierDefinition.poolQuotaKeeperOnly
   ModifierDefinition.creditManagerOnly
   ModifierDefinition.whenNotLocked
   FunctionDefinition._revertIfCallerIsNotPoolQuotaKeeper
   FunctionDefinition._revertIfCallerNotCreditManager
   FunctionDefinition._revertIfLocked
   FunctionDefinition.constructor
   FunctionDefinition.decimals
   FunctionDefinition.creditManagers
   FunctionDefinition.availableLiquidity
   FunctionDefinition.expectedLiquidity
   FunctionDefinition.expectedLiquidityLU
   FunctionDefinition.totalAssets
   FunctionDefinition.deposit
   FunctionDefinition.depositWithReferral
   FunctionDefinition.depositETH
   FunctionDefinition.mint
   FunctionDefinition.mintWithReferral
   FunctionDefinition.withdraw
   FunctionDefinition.redeem
   FunctionDefinition.previewDeposit
   FunctionDefinition.previewMint
   FunctionDefinition.previewWithdraw
   FunctionDefinition.previewRedeem
   FunctionDefinition.maxDeposit
   FunctionDefinition.maxMint
   FunctionDefinition.maxWithdraw
   FunctionDefinition.maxRedeem
   FunctionDefinition._deposit
   FunctionDefinition._registerDeposit
   FunctionDefinition._withdraw
   FunctionDefinition._convertToShares
   FunctionDefinition._convertToAssets
   FunctionDefinition.totalBorrowed
   FunctionDefinition.totalDebtLimit
   FunctionDefinition.creditManagerBorrowed
   FunctionDefinition.creditManagerDebtLimit
   FunctionDefinition.creditManagerBorrowable
   FunctionDefinition.lendCreditAccount
   FunctionDefinition.repayCreditAccount
   FunctionDefinition._borrowable
   FunctionDefinition.baseInterestRate
   FunctionDefinition.supplyRate
   FunctionDefinition.baseInterestIndex
   FunctionDefinition.baseInterestIndexLU
   FunctionDefinition._calcBaseInterestAccrued
   FunctionDefinition.calcAccruedQuotaInterest
   FunctionDefinition._updateBaseInterest
   FunctionDefinition._calcBaseInterestAccrued
   FunctionDefinition._calcBaseInterestIndex
   FunctionDefinition.quotaRevenue
   FunctionDefinition.updateQuotaRevenue
   FunctionDefinition.setQuotaRevenue
   FunctionDefinition._calcQuotaRevenueAccrued
   FunctionDefinition._setQuotaRevenue
   FunctionDefinition._calcQuotaRevenueAccrued
   FunctionDefinition.setInterestRateModel
   FunctionDefinition.setPoolQuotaKeeper
   FunctionDefinition.setTreasury
   FunctionDefinition.setTotalDebtLimit
   FunctionDefinition.setCreditManagerDebtLimit
   FunctionDefinition.setWithdrawFee
   FunctionDefinition.setAllowed
   FunctionDefinition.setLock
   FunctionDefinition.isAllowed
   FunctionDefinition._setTotalDebtLimit
   FunctionDefinition._amountWithFee
   FunctionDefinition._amountMinusFee
   FunctionDefinition._amountWithWithdrawalFee
   FunctionDefinition._amountMinusWithdrawalFee
   FunctionDefinition._convertToU256
   FunctionDefinition._convertToU128
   FunctionDefinition.mintProfit

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

1: 
   Current order:
   UsingForDirective.IERC20
   ErrorDefinition.OnlyStakingVault
   VariableDeclaration.STAKING_VAULT
   VariableDeclaration.lpETH
   FunctionDefinition.constructor
   ModifierDefinition.onlyStakingVault
   FunctionDefinition.withdraw
   
   Suggested order:
   UsingForDirective.IERC20
   VariableDeclaration.STAKING_VAULT
   VariableDeclaration.lpETH
   ErrorDefinition.OnlyStakingVault
   ModifierDefinition.onlyStakingVault
   FunctionDefinition.constructor
   FunctionDefinition.withdraw

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

1: 
   Current order:
   UsingForDirective.IERC20
   ErrorDefinition.ExcessiveRedeemAmount
   ErrorDefinition.ExcessiveWithdrawAmount
   ErrorDefinition.InvalidCooldown
   ErrorDefinition.OperationNotAllowed
   ErrorDefinition.MinSharesViolation
   StructDefinition.UserCooldown
   VariableDeclaration.MIN_SHARES
   VariableDeclaration.silo
   VariableDeclaration.cooldowns
   VariableDeclaration.MAX_COOLDOWN_DURATION
   VariableDeclaration.cooldownDuration
   EventDefinition.CooldownDurationUpdated
   ModifierDefinition.ensureCooldownOff
   ModifierDefinition.ensureCooldownOn
   FunctionDefinition.constructor
   FunctionDefinition.withdraw
   FunctionDefinition.redeem
   FunctionDefinition.unstake
   FunctionDefinition.cooldownAssets
   FunctionDefinition.cooldownShares
   FunctionDefinition.setCooldownDuration
   FunctionDefinition._checkMinShares
   FunctionDefinition._deposit
   FunctionDefinition._withdraw
   
   Suggested order:
   UsingForDirective.IERC20
   VariableDeclaration.MIN_SHARES
   VariableDeclaration.silo
   VariableDeclaration.cooldowns
   VariableDeclaration.MAX_COOLDOWN_DURATION
   VariableDeclaration.cooldownDuration
   StructDefinition.UserCooldown
   ErrorDefinition.ExcessiveRedeemAmount
   ErrorDefinition.ExcessiveWithdrawAmount
   ErrorDefinition.InvalidCooldown
   ErrorDefinition.OperationNotAllowed
   ErrorDefinition.MinSharesViolation
   EventDefinition.CooldownDurationUpdated
   ModifierDefinition.ensureCooldownOff
   ModifierDefinition.ensureCooldownOn
   FunctionDefinition.constructor
   FunctionDefinition.withdraw
   FunctionDefinition.redeem
   FunctionDefinition.unstake
   FunctionDefinition.cooldownAssets
   FunctionDefinition.cooldownShares
   FunctionDefinition.setCooldownDuration
   FunctionDefinition._checkMinShares
   FunctionDefinition._deposit
   FunctionDefinition._withdraw

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/VaultRegistry.sol

1: 
   Current order:
   VariableDeclaration.VAULT_MANAGER_ROLE
   VariableDeclaration.registeredVaults
   VariableDeclaration.vaultList
   EventDefinition.VaultAdded
   EventDefinition.VaultRemoved
   ErrorDefinition.VaultRegistry__removeVault_vaultNotFound
   ErrorDefinition.VaultRegistry__addVault_vaultAlreadyRegistered
   FunctionDefinition.constructor
   FunctionDefinition.addVault
   FunctionDefinition.removeVault
   FunctionDefinition.getVaults
   FunctionDefinition.getUserTotalDebt
   FunctionDefinition._removeVaultFromList
   FunctionDefinition.isVaultRegistered
   
   Suggested order:
   VariableDeclaration.VAULT_MANAGER_ROLE
   VariableDeclaration.registeredVaults
   VariableDeclaration.vaultList
   ErrorDefinition.VaultRegistry__removeVault_vaultNotFound
   ErrorDefinition.VaultRegistry__addVault_vaultAlreadyRegistered
   EventDefinition.VaultAdded
   EventDefinition.VaultRemoved
   FunctionDefinition.constructor
   FunctionDefinition.addVault
   FunctionDefinition.removeVault
   FunctionDefinition.getVaults
   FunctionDefinition.getUserTotalDebt
   FunctionDefinition._removeVaultFromList
   FunctionDefinition.isVaultRegistered

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

1: 
   Current order:
   FunctionDefinition.onFlashLoan
   FunctionDefinition.maxFlashLoan
   FunctionDefinition.flashFee
   FunctionDefinition.flashLoan
   FunctionDefinition.onCreditFlashLoan
   FunctionDefinition.creditFlashLoan
   FunctionDefinition.pool
   FunctionDefinition.underlyingToken
   FunctionDefinition.CALLBACK_SUCCESS
   FunctionDefinition.CALLBACK_SUCCESS_CREDIT
   FunctionDefinition.maxFlashLoan
   FunctionDefinition.flashFee
   FunctionDefinition.flashLoan
   FunctionDefinition.creditFlashLoan
   VariableDeclaration.flashlender
   VariableDeclaration.CALLBACK_SUCCESS
   VariableDeclaration.CALLBACK_SUCCESS_CREDIT
   FunctionDefinition.constructor
   FunctionDefinition.approvePayback
   
   Suggested order:
   VariableDeclaration.flashlender
   VariableDeclaration.CALLBACK_SUCCESS
   VariableDeclaration.CALLBACK_SUCCESS_CREDIT
   FunctionDefinition.onFlashLoan
   FunctionDefinition.maxFlashLoan
   FunctionDefinition.flashFee
   FunctionDefinition.flashLoan
   FunctionDefinition.onCreditFlashLoan
   FunctionDefinition.creditFlashLoan
   FunctionDefinition.pool
   FunctionDefinition.underlyingToken
   FunctionDefinition.CALLBACK_SUCCESS
   FunctionDefinition.CALLBACK_SUCCESS_CREDIT
   FunctionDefinition.maxFlashLoan
   FunctionDefinition.flashFee
   FunctionDefinition.flashLoan
   FunctionDefinition.creditFlashLoan
   FunctionDefinition.constructor
   FunctionDefinition.approvePayback

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

1: 
   Current order:
   StructDefinition.Oracle
   VariableDeclaration.oracles
   VariableDeclaration.__gap
   ErrorDefinition.ChainlinkOracle__spot_invalidValue
   ErrorDefinition.ChainlinkOracle__authorizeUpgrade_validStatus
   FunctionDefinition.setOracles
   FunctionDefinition.initialize
   FunctionDefinition._authorizeUpgrade
   FunctionDefinition.getStatus
   FunctionDefinition.spot
   FunctionDefinition._fetchAndValidate
   FunctionDefinition._getStatus
   
   Suggested order:
   VariableDeclaration.oracles
   VariableDeclaration.__gap
   StructDefinition.Oracle
   ErrorDefinition.ChainlinkOracle__spot_invalidValue
   ErrorDefinition.ChainlinkOracle__authorizeUpgrade_validStatus
   FunctionDefinition.setOracles
   FunctionDefinition.initialize
   FunctionDefinition._authorizeUpgrade
   FunctionDefinition.getStatus
   FunctionDefinition.spot
   FunctionDefinition._fetchAndValidate
   FunctionDefinition._getStatus

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

1: 
   Current order:
   FunctionDefinition.redeemRewards
   FunctionDefinition.getRewardTokens
   FunctionDefinition.balanceOf
   UsingForDirective.PMath
   UsingForDirective.ArrayLib
   ErrorDefinition.OnlyVault
   VariableDeclaration.lastRewardBlock
   VariableDeclaration.rewardState
   VariableDeclaration.market
   VariableDeclaration.vault
   VariableDeclaration.proxyRegistry
   ModifierDefinition.onlyVault
   FunctionDefinition.constructor
   FunctionDefinition._updateRewardIndex
   FunctionDefinition._doTransferOutRewards
   FunctionDefinition._rewardSharesTotal
   FunctionDefinition.handleRewardsOnDeposit
   FunctionDefinition.handleRewardsOnWithdraw
   
   Suggested order:
   UsingForDirective.PMath
   UsingForDirective.ArrayLib
   VariableDeclaration.lastRewardBlock
   VariableDeclaration.rewardState
   VariableDeclaration.market
   VariableDeclaration.vault
   VariableDeclaration.proxyRegistry
   ErrorDefinition.OnlyVault
   ModifierDefinition.onlyVault
   FunctionDefinition.redeemRewards
   FunctionDefinition.getRewardTokens
   FunctionDefinition.balanceOf
   FunctionDefinition.constructor
   FunctionDefinition._updateRewardIndex
   FunctionDefinition._doTransferOutRewards
   FunctionDefinition._rewardSharesTotal
   FunctionDefinition.handleRewardsOnDeposit
   FunctionDefinition.handleRewardsOnWithdraw

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

1: 
   Current order:
   UsingForDirective.PMath
   UsingForDirective.SafeCast
   VariableDeclaration.INITIAL_REWARD_INDEX
   StructDefinition.RewardState
   StructDefinition.UserReward
   VariableDeclaration._totalShares
   VariableDeclaration.userReward
   FunctionDefinition._updateAndDistributeRewards
   FunctionDefinition._updateAndDistributeRewardsForTwo
   FunctionDefinition._distributeRewardsPrivate
   FunctionDefinition._updateRewardIndex
   FunctionDefinition._doTransferOutRewards
   
   Suggested order:
   UsingForDirective.PMath
   UsingForDirective.SafeCast
   VariableDeclaration.INITIAL_REWARD_INDEX
   VariableDeclaration._totalShares
   VariableDeclaration.userReward
   StructDefinition.RewardState
   StructDefinition.UserReward
   FunctionDefinition._updateAndDistributeRewards
   FunctionDefinition._updateAndDistributeRewardsForTwo
   FunctionDefinition._distributeRewardsPrivate
   FunctionDefinition._updateRewardIndex
   FunctionDefinition._doTransferOutRewards

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/prb-proxy/interfaces/IPRBProxyRegistry.sol

1: 
   Current order:
   ErrorDefinition.PRBProxyRegistry_PluginMethodCollision
   ErrorDefinition.PRBProxyRegistry_PluginUnknown
   ErrorDefinition.PRBProxyRegistry_PluginWithZeroMethods
   ErrorDefinition.PRBProxyRegistry_UserDoesNotHaveProxy
   ErrorDefinition.PRBProxyRegistry_UserHasProxy
   EventDefinition.DeployProxy
   EventDefinition.InstallPlugin
   EventDefinition.SetPermission
   EventDefinition.UninstallPlugin
   StructDefinition.ConstructorParams
   FunctionDefinition.VERSION
   FunctionDefinition.constructorParams
   FunctionDefinition.getMethodsByOwner
   FunctionDefinition.getMethodsByProxy
   FunctionDefinition.getPermissionByOwner
   FunctionDefinition.getPermissionByProxy
   FunctionDefinition.getPluginByOwner
   FunctionDefinition.getPluginByProxy
   FunctionDefinition.getProxy
   FunctionDefinition.isProxy
   FunctionDefinition.deploy
   FunctionDefinition.deployAndExecute
   FunctionDefinition.deployAndExecuteAndInstallPlugin
   FunctionDefinition.deployAndInstallPlugin
   FunctionDefinition.deployFor
   FunctionDefinition.installPlugin
   FunctionDefinition.setPermission
   FunctionDefinition.uninstallPlugin
   
   Suggested order:
   StructDefinition.ConstructorParams
   ErrorDefinition.PRBProxyRegistry_PluginMethodCollision
   ErrorDefinition.PRBProxyRegistry_PluginUnknown
   ErrorDefinition.PRBProxyRegistry_PluginWithZeroMethods
   ErrorDefinition.PRBProxyRegistry_UserDoesNotHaveProxy
   ErrorDefinition.PRBProxyRegistry_UserHasProxy
   EventDefinition.DeployProxy
   EventDefinition.InstallPlugin
   EventDefinition.SetPermission
   EventDefinition.UninstallPlugin
   FunctionDefinition.VERSION
   FunctionDefinition.constructorParams
   FunctionDefinition.getMethodsByOwner
   FunctionDefinition.getMethodsByProxy
   FunctionDefinition.getPermissionByOwner
   FunctionDefinition.getPermissionByProxy
   FunctionDefinition.getPluginByOwner
   FunctionDefinition.getPluginByProxy
   FunctionDefinition.getProxy
   FunctionDefinition.isProxy
   FunctionDefinition.deploy
   FunctionDefinition.deployAndExecute
   FunctionDefinition.deployAndExecuteAndInstallPlugin
   FunctionDefinition.deployAndInstallPlugin
   FunctionDefinition.deployFor
   FunctionDefinition.installPlugin
   FunctionDefinition.setPermission
   FunctionDefinition.uninstallPlugin

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/interfaces/IPRBProxyRegistry.sol)

```solidity
File: ./src/proxy/PositionAction.sol

1: 
   Current order:
   UsingForDirective.IERC20
   VariableDeclaration.CALLBACK_SUCCESS
   VariableDeclaration.CALLBACK_SUCCESS_CREDIT
   VariableDeclaration.vaultRegistry
   VariableDeclaration.flashlender
   VariableDeclaration.pool
   VariableDeclaration.underlyingToken
   VariableDeclaration.self
   VariableDeclaration.swapAction
   VariableDeclaration.poolAction
   ErrorDefinition.PositionAction__constructor_InvalidParam
   ErrorDefinition.PositionAction__deposit_InvalidAuxSwap
   ErrorDefinition.PositionAction__borrow_InvalidAuxSwap
   ErrorDefinition.PositionAction__repay_InvalidAuxSwap
   ErrorDefinition.PositionAction__increaseLever_invalidPrimarySwap
   ErrorDefinition.PositionAction__increaseLever_invalidAuxSwap
   ErrorDefinition.PositionAction__decreaseLever_invalidPrimarySwap
   ErrorDefinition.PositionAction__decreaseLever_invalidAuxSwap
   ErrorDefinition.PositionAction__decreaseLever_invalidClosePositionPrimarySwap
   ErrorDefinition.PositionAction__decreaseLever_invalidResidualRecipient
   ErrorDefinition.PositionAction__onFlashLoan__invalidSender
   ErrorDefinition.PositionAction__onFlashLoan__invalidInitiator
   ErrorDefinition.PositionAction__onCreditFlashLoan__invalidSender
   ErrorDefinition.PositionAction__onlyDelegatecall
   ErrorDefinition.PositionAction__unregisteredVault
   FunctionDefinition.constructor
   ModifierDefinition.onlyDelegatecall
   ModifierDefinition.onlyRegisteredVault
   FunctionDefinition._onDeposit
   FunctionDefinition._onWithdraw
   FunctionDefinition._onIncreaseLever
   FunctionDefinition._onDecreaseLever
   FunctionDefinition.deposit
   FunctionDefinition.withdraw
   FunctionDefinition.borrow
   FunctionDefinition.repay
   FunctionDefinition.depositAndBorrow
   FunctionDefinition.withdrawAndRepay
   FunctionDefinition.multisend
   FunctionDefinition.increaseLever
   FunctionDefinition.decreaseLever
   FunctionDefinition.onFlashLoan
   FunctionDefinition.onCreditFlashLoan
   FunctionDefinition._deposit
   FunctionDefinition._withdraw
   FunctionDefinition._borrow
   FunctionDefinition._repay
   FunctionDefinition._transferAndSwap
   
   Suggested order:
   UsingForDirective.IERC20
   VariableDeclaration.CALLBACK_SUCCESS
   VariableDeclaration.CALLBACK_SUCCESS_CREDIT
   VariableDeclaration.vaultRegistry
   VariableDeclaration.flashlender
   VariableDeclaration.pool
   VariableDeclaration.underlyingToken
   VariableDeclaration.self
   VariableDeclaration.swapAction
   VariableDeclaration.poolAction
   ErrorDefinition.PositionAction__constructor_InvalidParam
   ErrorDefinition.PositionAction__deposit_InvalidAuxSwap
   ErrorDefinition.PositionAction__borrow_InvalidAuxSwap
   ErrorDefinition.PositionAction__repay_InvalidAuxSwap
   ErrorDefinition.PositionAction__increaseLever_invalidPrimarySwap
   ErrorDefinition.PositionAction__increaseLever_invalidAuxSwap
   ErrorDefinition.PositionAction__decreaseLever_invalidPrimarySwap
   ErrorDefinition.PositionAction__decreaseLever_invalidAuxSwap
   ErrorDefinition.PositionAction__decreaseLever_invalidClosePositionPrimarySwap
   ErrorDefinition.PositionAction__decreaseLever_invalidResidualRecipient
   ErrorDefinition.PositionAction__onFlashLoan__invalidSender
   ErrorDefinition.PositionAction__onFlashLoan__invalidInitiator
   ErrorDefinition.PositionAction__onCreditFlashLoan__invalidSender
   ErrorDefinition.PositionAction__onlyDelegatecall
   ErrorDefinition.PositionAction__unregisteredVault
   ModifierDefinition.onlyDelegatecall
   ModifierDefinition.onlyRegisteredVault
   FunctionDefinition.constructor
   FunctionDefinition._onDeposit
   FunctionDefinition._onWithdraw
   FunctionDefinition._onIncreaseLever
   FunctionDefinition._onDecreaseLever
   FunctionDefinition.deposit
   FunctionDefinition.withdraw
   FunctionDefinition.borrow
   FunctionDefinition.repay
   FunctionDefinition.depositAndBorrow
   FunctionDefinition.withdrawAndRepay
   FunctionDefinition.multisend
   FunctionDefinition.increaseLever
   FunctionDefinition.decreaseLever
   FunctionDefinition.onFlashLoan
   FunctionDefinition.onCreditFlashLoan
   FunctionDefinition._deposit
   FunctionDefinition._withdraw
   FunctionDefinition._borrow
   FunctionDefinition._repay
   FunctionDefinition._transferAndSwap

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

1: 
   Current order:
   VariableDeclaration.version
   VariableDeclaration.pool
   VariableDeclaration.quotaRateParams
   VariableDeclaration.userTokenVotes
   VariableDeclaration.voter
   VariableDeclaration.epochLastUpdate
   VariableDeclaration.epochFrozen
   FunctionDefinition.constructor
   ModifierDefinition.onlyVoter
   FunctionDefinition.updateEpoch
   FunctionDefinition._checkAndUpdateEpoch
   FunctionDefinition.getRates
   FunctionDefinition.vote
   FunctionDefinition._vote
   FunctionDefinition.unvote
   FunctionDefinition._unvote
   FunctionDefinition.setFrozenEpoch
   FunctionDefinition.addQuotaToken
   FunctionDefinition.changeQuotaMinRate
   FunctionDefinition.changeQuotaMaxRate
   FunctionDefinition._changeQuotaTokenRateParams
   FunctionDefinition._checkParams
   FunctionDefinition.isTokenAdded
   FunctionDefinition._poolQuotaKeeper
   FunctionDefinition._revertIfCallerNotVoter
   
   Suggested order:
   VariableDeclaration.version
   VariableDeclaration.pool
   VariableDeclaration.quotaRateParams
   VariableDeclaration.userTokenVotes
   VariableDeclaration.voter
   VariableDeclaration.epochLastUpdate
   VariableDeclaration.epochFrozen
   ModifierDefinition.onlyVoter
   FunctionDefinition.constructor
   FunctionDefinition.updateEpoch
   FunctionDefinition._checkAndUpdateEpoch
   FunctionDefinition.getRates
   FunctionDefinition.vote
   FunctionDefinition._vote
   FunctionDefinition.unvote
   FunctionDefinition._unvote
   FunctionDefinition.setFrozenEpoch
   FunctionDefinition.addQuotaToken
   FunctionDefinition.changeQuotaMinRate
   FunctionDefinition.changeQuotaMaxRate
   FunctionDefinition._changeQuotaTokenRateParams
   FunctionDefinition._checkParams
   FunctionDefinition.isTokenAdded
   FunctionDefinition._poolQuotaKeeper
   FunctionDefinition._revertIfCallerNotVoter

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/utils/Permission.sol

1: 
   Current order:
   EventDefinition.ModifyPermission
   EventDefinition.SetPermittedAgent
   ErrorDefinition.Permission__modifyPermission_notPermitted
   VariableDeclaration._permitted
   VariableDeclaration._permittedAgents
   FunctionDefinition.modifyPermission
   FunctionDefinition.modifyPermission
   FunctionDefinition.setPermissionAgent
   FunctionDefinition.hasPermission
   
   Suggested order:
   VariableDeclaration._permitted
   VariableDeclaration._permittedAgents
   ErrorDefinition.Permission__modifyPermission_notPermitted
   EventDefinition.ModifyPermission
   EventDefinition.SetPermittedAgent
   FunctionDefinition.modifyPermission
   FunctionDefinition.modifyPermission
   FunctionDefinition.setPermissionAgent
   FunctionDefinition.hasPermission

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

```solidity
File: ./src/vendor/IBalancerVault.sol

1: 
   Current order:
   FunctionDefinition.swap
   FunctionDefinition.batchSwap
   FunctionDefinition.joinPool
   FunctionDefinition.exitPool
   FunctionDefinition.getPoolTokens
   FunctionDefinition.queryBatchSwap
   FunctionDefinition.manageUserBalance
   StructDefinition.UserBalanceOp
   EnumDefinition.UserBalanceOpKind
   EnumDefinition.PoolSpecialization
   FunctionDefinition.getPool
   
   Suggested order:
   EnumDefinition.UserBalanceOpKind
   EnumDefinition.PoolSpecialization
   StructDefinition.UserBalanceOp
   FunctionDefinition.swap
   FunctionDefinition.batchSwap
   FunctionDefinition.joinPool
   FunctionDefinition.exitPool
   FunctionDefinition.getPoolTokens
   FunctionDefinition.queryBatchSwap
   FunctionDefinition.manageUserBalance
   FunctionDefinition.getPool

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IBalancerVault.sol)

```solidity
File: ./src/vendor/IPriceOracle.sol

1: 
   Current order:
   EnumDefinition.Variable
   FunctionDefinition.getTimeWeightedAverage
   FunctionDefinition.getLatest
   StructDefinition.OracleAverageQuery
   FunctionDefinition.getLargestSafeQueryWindow
   FunctionDefinition.getPastAccumulators
   StructDefinition.OracleAccumulatorQuery
   
   Suggested order:
   EnumDefinition.Variable
   StructDefinition.OracleAverageQuery
   StructDefinition.OracleAccumulatorQuery
   FunctionDefinition.getTimeWeightedAverage
   FunctionDefinition.getLatest
   FunctionDefinition.getLargestSafeQueryWindow
   FunctionDefinition.getPastAccumulators

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IPriceOracle.sol)

### <a name="NC-39"></a>[NC-39] Use Underscores for Number Literals (add an underscore every 3 digits)

*Instances (30)*:
```solidity
File: ./src/utils/Math.sol

225:             if iszero(slt(x, 135305999368893231589)) {

239:         int256 k = ((x << 96) / 54916777467707473351141471128 + 2 ** 95) >> 96;

240:         x = x - k * 54916777467707473351141471128;

246:         int256 y = x + 1346386616545796478920950773328;

247:         y = ((y * x) >> 96) + 57155421227552351082224309758442;

248:         int256 p = y + x - 94201549194550492254356042504812;

249:         p = ((p * y) >> 96) + 28719021644029726153956944680412240;

253:         int256 q = x - 2855989394907223263936484059900;

254:         q = ((q * x) >> 96) + 50020603652535783019961831881945;

255:         q = ((q * x) >> 96) - 533845033583426703283633433725380;

256:         q = ((q * x) >> 96) + 3604857256930695427073651918091429;

257:         q = ((q * x) >> 96) - 14423608567350463180887372962807573;

258:         q = ((q * x) >> 96) + 26449188498355588339934803723976023;

276:         r = int256((uint256(r) * 3822833074963236453042738258902158003155416615667) >> uint256(195 - k));

322:         int256 p = x + 3273285459638523848632254066296;

323:         p = ((p * x) >> 96) + 24828157081833163892658089445524;

324:         p = ((p * x) >> 96) + 43456485725739037958740375743393;

325:         p = ((p * x) >> 96) - 11111509109440967052023855526967;

326:         p = ((p * x) >> 96) - 45023709667254063763336534515857;

327:         p = ((p * x) >> 96) - 14706773417378608786704636184526;

332:         int256 q = x + 5573035233440673466300451813936;

333:         q = ((q * x) >> 96) + 71694874799317883764090561454958;

334:         q = ((q * x) >> 96) + 283447036172924575727196451306956;

335:         q = ((q * x) >> 96) + 401686690394027663651624208769553;

336:         q = ((q * x) >> 96) + 204048457590392012362485061816622;

337:         q = ((q * x) >> 96) + 31853899698501571402653359427138;

338:         q = ((q * x) >> 96) + 909429971244387300277376558375;

356:         r *= 1677202110996718588342820967067443963516166;

358:         r += 16597577552685614221487285958193947469193820559219878177908093499208371 * (159 - t);

360:         r += 600920179829731861736702779321621459595472258049074101567377883020018308;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

### <a name="NC-40"></a>[NC-40] Internal and private variables and functions names should begin with an underscore
According to the Solidity Style Guide, Non-`external` variable and function names should begin with an [underscore](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables)

*Instances (39)*:
```solidity
File: ./src/CDPVault.sol

730:     function calcDecrease(

796:     function calcAccruedInterest(

814:     function calcTotalDebt(DebtData memory debtData) internal pure returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/VaultRegistry.sol

17:     mapping(ICDPVault => bool) private registeredVaults;

20:     ICDPVault[] private vaultList;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

113:     function approvePayback(uint256 amount) internal {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/proxy/SwapAction.sol

186:     function balancerSwap(

311:     /// @param limit EXACT_IN:  `limit` is the minimum acceptable amount to receive from the swap

341:             IERC20(assetIn).forceApprove(address(uniRouter), limit);

380:             limit

400:             netSyToRedeem = netSyRemoved + netSyFromPt;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

49:     EnumerableSet.AddressSet internal quotaTokensSet;

52:     mapping(address => TokenQuotaParams) internal totalQuotaParams;

255:     function isInitialised(TokenQuotaParams storage tokenQuotaParams) internal view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

17:     function cumulativeIndexSince(

31:     function calcAccruedQuotaInterest(

41:     function calcQuotaRevenueChange(uint16 rate, int256 change) internal pure returns (int256) {

46:     function calcActualQuotaChange(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

17: function toInt256(uint256 x) pure returns (int256) {

23: function toUint64(uint256 x) pure returns (uint64) {

29: function abs(int256 x) pure returns (uint256 z) {

37: function min(uint256 x, uint256 y) pure returns (uint256 z) {

44: function min(int256 x, int256 y) pure returns (int256 z) {

51: function max(uint256 x, uint256 y) pure returns (uint256 z) {

58: function add(uint256 x, int256 y) pure returns (uint256 z) {

66: function sub(uint256 x, int256 y) pure returns (uint256 z) {

74: function mul(uint256 x, int256 y) pure returns (int256 z) {

83: function wmul(uint256 x, uint256 y) pure returns (uint256 z) {

97: function wmul(uint256 x, int256 y) pure returns (int256 z) {

105: function wmulUp(uint256 x, uint256 y) pure returns (uint256 z) {

121: function wdiv(uint256 x, uint256 y) pure returns (uint256 z) {

137: function wdivUp(uint256 x, uint256 y) pure returns (uint256 z) {

152: function wpow(uint256 x, uint256 n, uint256 b) pure returns (uint256 z) {

208: function wpow(int256 x, int256 y) pure returns (int256) {

215: function wexp(int256 x) pure returns (int256 r) {

282: function wln(int256 x) pure returns (int256 r) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/vendor/IUniswapV3Router.sol

24: function toAddress(bytes memory _bytes, uint256 _start) pure returns (address) {

39: function decodeLastToken(bytes memory path) pure returns (address token) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IUniswapV3Router.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

37:     function ensureNotInVaultContext(IVault vault) internal view {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="NC-41"></a>[NC-41] Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*Instances (26)*:
```solidity
File: ./src/CDPVault.sol

140:     event ModifyPosition(address indexed position, uint256 debt, uint256 collateral, uint256 totalDebt);

148:     event SetParameter(bytes32 indexed parameter, uint256 data);

149:     event SetParameter(bytes32 indexed parameter, address data);

150:     event LiquidatePosition(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

33:     event FlashLoan(address indexed receiver, address token, uint256 amount, uint256 fee);

34:     event CreditFlashLoan(address indexed receiver, uint256 amount, uint256 fee);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

29:     event Deposited(address indexed user, uint256 amount);

30:     event Withdrawn(address indexed user, uint256 amount);

31:     event CooldownPeriodSet(uint256 cooldownPeriod);

32:     event CooldownInitiated(address indexed user, uint256 timestamp, uint256 amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/StakingLPEth.sol

40:     event CooldownDurationUpdated(uint24 previousDuration, uint24 newDuration);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

24:     event Treasury__moveFunds(address treasury);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/interfaces/IPoolQuotaKeeperV3.sol

23:     event UpdateQuota(address indexed creditAccount, address indexed token, int96 quotaChange);

26:     event UpdateTokenQuotaRate(address indexed token, uint16 rate);

38:     event SetTokenLimit(address indexed token, uint96 limit);

41:     event SetQuotaIncreaseFee(address indexed token, uint16 fee);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolQuotaKeeperV3.sol)

```solidity
File: ./src/interfaces/IPoolV3.sol

13:     event Refer(address indexed onBehalfOf, uint256 indexed referralCode, uint256 amount);

16:     event Borrow(address indexed creditManager, address indexed creditAccount, uint256 amount);

19:     event Repay(address indexed creditManager, uint256 borrowedAmount, uint256 profit, uint256 loss);

22:     event IncurUncoveredLoss(address indexed creditManager, uint256 loss);

31:     event SetTotalDebtLimit(uint256 limit);

37:     event SetCreditManagerDebtLimit(address indexed creditManager, uint256 newLimit);

40:     event SetWithdrawFee(uint256 fee);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IPoolV3.sol)

```solidity
File: ./src/prb-proxy/interfaces/IPRBProxyRegistry.sol

41:     event DeployProxy(address indexed operator, address indexed owner, IPRBProxy proxy);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/interfaces/IPRBProxyRegistry.sol)

```solidity
File: ./src/utils/Permission.sol

11:     event ModifyPermission(address authorizer, address owner, address caller, bool grant);

12:     event SetPermittedAgent(address owner, address agent, bool grant);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

### <a name="NC-42"></a>[NC-42] Constants should be defined rather than using magic numbers

*Instances (8)*:
```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

269:             cumulativeIndexLU := and(shr(16, data), 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF)

270:             quotaIncreaseFee := shr(208, data)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/utils/Math.sol

31:         let mask := sub(0, shr(255, x))

250:         p = p * x + (4385272521454847904659076985693276 << 96);

276:         r = int256((uint256(r) * 3822833074963236453042738258902158003155416615667) >> uint256(195 - k));

328:         p = p * x - (795164235651350426258249787498 << 96);

358:         r += 16597577552685614221487285958193947469193820559219878177908093499208371 * (159 - t);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

81:         let revertReason := shl(200, add(formattedPrefix, add(add(units, shl(8, tenths)), shl(16, hundreds))))

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

### <a name="NC-43"></a>[NC-43] `public` functions not called by the contract should be declared `external` instead

*Instances (4)*:
```solidity
File: ./src/PoolV3.sol

259:     function depositETH(address receiver)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/proxy/PoolAction.sol

244:     function exit(PoolActionParams memory poolActionParams) public returns (uint256 retAmount) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

427:             token = decodeLastToken(swapParams.args);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/utils/Permission.sol

66:     function hasPermission(address owner, address caller) public view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Permission.sol)

### <a name="NC-44"></a>[NC-44] Variables need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.

*Instances (16)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Treasury.sol

57:         for (uint256 i = 0; i < len; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

67:         for (uint256 i = 0; i < vaultLen; ) {

82:         for (uint256 i = 0; i < vaultLen; ) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

46:         for (uint256 i = 0; i < _tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

63:             for (uint256 i = 0; i < tokens.length; ++i) {

84:             for (uint256 i = 0; i < tokens.length; i++) {

97:         for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/pendle-rewards/RewardManagerAbstract.sol

62:         for (uint256 i = 0; i < tokens.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManagerAbstract.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

200:         for (uint256 i = 0; i < length; ) {

250:         for (uint256 i = 0; i < length; ) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/proxy/PoolAction.sol

91:                 for (uint256 i = 0; i < assets.length; ) {

135:         for (uint256 i = 0; i < assets.length; ) {

214:         for (uint256 i = 0; i < len; ) {

265:         for (uint256 i = 0; i <= tmpOutIndex; i++) if (assets[i] == bpt) tmpOutIndex++;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | `approve()`/`safeApprove()` may revert if the current approval is not zero | 1 |
| [L-2](#L-2) | Use a 2-step ownership transfer pattern | 2 |
| [L-3](#L-3) | Some tokens may revert when zero value transfers are made | 30 |
| [L-4](#L-4) | Missing checks for `address(0)` when assigning values to address state variables | 12 |
| [L-5](#L-5) | `decimals()` is not a part of the ERC-20 standard | 3 |
| [L-6](#L-6) | Deprecated approve() function | 1 |
| [L-7](#L-7) | Do not use deprecated library functions | 4 |
| [L-8](#L-8) | Deprecated _setupRole() function | 4 |
| [L-9](#L-9) | Do not leave an implementation contract uninitialized | 1 |
| [L-10](#L-10) | Division by zero not prevented | 7 |
| [L-11](#L-11) | Duplicate import statements | 2 |
| [L-12](#L-12) | External calls in an un-bounded `for-`loop may result in a DOS | 2 |
| [L-13](#L-13) | External call recipient may consume all transaction gas | 2 |
| [L-14](#L-14) | Initializers could be front-run | 5 |
| [L-15](#L-15) | Prevent accidentally burning tokens | 7 |
| [L-16](#L-16) | Possible rounding issue | 1 |
| [L-17](#L-17) | Loss of precision | 12 |
| [L-18](#L-18) | Solidity version 0.8.20+ may not work on other chains due to `PUSH0` | 21 |
| [L-19](#L-19) | Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership` | 2 |
| [L-20](#L-20) | File allows a version of solidity that is susceptible to an assembly optimizer bug | 2 |
| [L-21](#L-21) | Sweeping may break accounting if tokens with multiple addresses are used | 3 |
| [L-22](#L-22) | Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when downcasting | 12 |
| [L-23](#L-23) | Unsafe ERC20 operation(s) | 7 |
| [L-24](#L-24) | Unsafe solidity low-level call can cause gas grief attack | 2 |
| [L-25](#L-25) | Unspecific compiler version pragma | 5 |
| [L-26](#L-26) | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 7 |
| [L-27](#L-27) | Upgradeable contract not initialized | 15 |
### <a name="L-1"></a>[L-1] `approve()`/`safeApprove()` may revert if the current approval is not zero
- Some tokens (like the *very popular* USDT) do not work when changing the allowance from an existing non-zero allowance value (it will revert if the current approval is not zero to protect against front-running changes of approvals). These tokens must first be approved for zero and then the actual allowance can be approved.
- Furthermore, OZ's implementation of safeApprove would throw an error if an approve is attempted from a non-zero value (`"SafeERC20: approve from non-zero to non-zero allowance"`)

Set the allowance to zero immediately before each of the existing allowance calls

*Instances (1)*:
```solidity
File: ./src/interfaces/IFlashlender.sol

115:         flashlender.underlyingToken().approve(address(flashlender), amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

### <a name="L-2"></a>[L-2] Use a 2-step ownership transfer pattern
Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an `acceptOwnership()` function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account. Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

*Instances (2)*:
```solidity
File: ./src/Locking.sol

10: contract Locking is Ownable {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/StakingLPEth.sol

8: contract StakingLPEth is ERC4626, Ownable, ReentrancyGuard {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

### <a name="L-3"></a>[L-3] Some tokens may revert when zero value transfers are made
Example: https://github.com/d-xo/weird-erc20#revert-on-zero-value-transfers.

In spite of the fact that EIP-20 [states](https://github.com/ethereum/EIPs/blob/46b9b698815abbfa628cd1097311deee77dd45c5/EIPS/eip-20.md?plain=1#L116) that zero-valued transfers must be accepted, some tokens, such as LEND will revert if this is attempted, which may cause transactions that involve other tokens (such as batch operations) to fully revert. Consider skipping the transfer if the amount is zero, which will also save gas.

*Instances (30)*:
```solidity
File: ./src/CDPVault.sol

325:                     IERC20(tokens[i]).safeTransfer(to, rewardAmounts[i]);

338:                     IERC20(tokens[i]).safeTransfer(to, rewardAmounts[i]);

479:             poolUnderlying.safeTransferFrom(creditor, address(pool), scaledDebtDecrease);

511:             token.safeTransferFrom(collateralizer, address(this), amount);

514:             token.safeTransfer(collateralizer, amount);

613:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount - penalty);

639:         token.safeTransfer(msg.sender, takeCollateral);

642:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), penalty);

688:         poolUnderlying.safeTransferFrom(msg.sender, address(pool), repayAmount);

704:         token.safeTransfer(msg.sender, takeCollateral);

853:         IERC20(tokenAddress).safeTransfer(to, tokenAmount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Flashlender.sol

105:         underlyingToken.transferFrom(address(receiver), address(pool), total);

134:         underlyingToken.transferFrom(address(receiver), address(pool), total);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

51:         token.safeTransferFrom(msg.sender, address(this), _amount);

83:         token.safeTransfer(msg.sender, _amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

415:         IERC20(underlyingToken).safeTransferFrom({from: msg.sender, to: address(this), value: assetsSent}); // U:[LP-6,7]

461:         IERC20(underlyingToken).safeTransfer({to: receiver, value: amountToUser}); // U:[LP-8,9]

464:                 IERC20(underlyingToken).safeTransfer({to: treasury, value: assetsSent - amountToUser}); // U:[LP-8,9]

555:         IERC20(underlyingToken).safeTransfer({to: creditAccount, value: borrowedAmount}); // U:[LP-13B]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

29:         lpETH.safeTransfer(to, amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/Treasury.sol

71:             SafeERC20.safeTransfer(token, treasury, amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/proxy/PositionAction.sol

341:                 IERC20(upFrontToken).safeTransfer(self, upFrontAmount); // if tokens are on the proxy then just transfer

522:                     IERC20(leverParams.primarySwap.assetIn).safeTransfer(residualRecipient, residualAmount);

587:             IERC20(collateralParams.targetToken).safeTransfer(collateralParams.collateralizer, collateral);

606:             underlyingToken.safeTransferFrom(address(this), creditParams.creditor, creditParams.amount);

671:                 IERC20(swapParams.assetIn).safeTransfer(sender, remainder);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

152:                 IERC20(swapParams.assetIn).safeTransfer(swapParams.residualRecipient, swapParams.limit - retAmount);

154:                 IERC20(swapParams.assetIn).safeTransfer(swapParams.recipient, swapParams.limit - retAmount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/proxy/TransferAction.sol

76:             IERC20(token).safeTransferFrom(from, to, amount);

79:             IERC20(token).safeTransferFrom(from, to, amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/TransferAction.sol)

### <a name="L-4"></a>[L-4] Missing checks for `address(0)` when assigning values to address state variables

*Instances (12)*:
```solidity
File: ./src/PoolV3.sol

176:         addressProvider = addressProvider_; // U:[LP-1B]

177:         underlyingToken = underlyingToken_; // U:[LP-1B]

182:         interestRateModel = interestRateModel_; // U:[LP-1B]

797:         interestRateModel = newInterestRateModel; // U:[LP-22B]

816:         poolQuotaKeeper = newPoolQuotaKeeper; // U:[LP-23D]

826:         treasury = treasury_;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

19:         STAKING_VAULT = _stakingVault;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/pendle-rewards/RewardManager.sol

40:         vault = _vault;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/SwapAction.sol

86:         kyberRouter = kyberRouter_;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

59:         pool = _pool; // U:[GA-01]

60:         voter = _voter; // U:[GA-01]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

82:         pool = _pool; // U:[PQK-1]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

### <a name="L-5"></a>[L-5] `decimals()` is not a part of the ERC-20 standard
The `decimals()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*Instances (3)*:
```solidity
File: ./src/CDPVault.sol

186:         poolUnderlyingScale = 10 ** IERC20Metadata(address(poolUnderlying)).decimals();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

192:         return ERC4626.decimals();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

65:         aggregatorScale = 10 ** uint256(aggregator.decimals());

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

### <a name="L-6"></a>[L-6] Deprecated approve() function
Due to the inheritance of ERC20's approve function, there's a vulnerability to the ERC20 approve and double spend front running attack. Briefly, an authorized spender could spend both allowances by front running an allowance-changing transaction. Consider implementing OpenZeppelin's `.safeApprove()` function to help mitigate this.

*Instances (1)*:
```solidity
File: ./src/interfaces/IFlashlender.sol

115:         flashlender.underlyingToken().approve(address(flashlender), amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

### <a name="L-7"></a>[L-7] Do not use deprecated library functions

*Instances (4)*:
```solidity
File: ./src/Treasury.sol

27:         _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);

28:         _setupRole(FUNDS_ADMINISTRATOR_ROLE, fundsAdmin);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

33:         _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);

34:         _setupRole(VAULT_MANAGER_ROLE, msg.sender);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

### <a name="L-8"></a>[L-8] Deprecated _setupRole() function

*Instances (4)*:
```solidity
File: ./src/Treasury.sol

27:         _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);

28:         _setupRole(FUNDS_ADMINISTRATOR_ROLE, fundsAdmin);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

33:         _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);

34:         _setupRole(VAULT_MANAGER_ROLE, msg.sender);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

### <a name="L-9"></a>[L-9] Do not leave an implementation contract uninitialized
An uninitialized implementation contract can be taken over by an attacker, which may impact the proxy. To prevent the implementation contract from being used, it's advisable to invoke the `_disableInitializers` function in the constructor to automatically lock it when it is deployed. This should look similar to this:
```solidity
  /// @custom:oz-upgrades-unsafe-allow constructor
  constructor() {
      _disableInitializers();
  }
```

Sources:
- https://docs.openzeppelin.com/contracts/4.x/api/proxy#Initializable-_disableInitializers--
- https://twitter.com/0xCygaar/status/1621417995905167360?s=20

*Instances (1)*:
```solidity
File: ./src/oracle/PendleLPOracle.sol

56:     constructor(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

### <a name="L-10"></a>[L-10] Division by zero not prevented
The divisions below take an input parameter which does not have any zero-value checks, which may lead to the functions reverting when zero is passed.

*Instances (7)*:
```solidity
File: ./src/CDPVault.sol

802:         return (amount * cumulativeIndexNow) / cumulativeIndexLastUpdate - amount;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

929:         return (amount * PERCENTAGE_FACTOR) / (PERCENTAGE_FACTOR - withdrawFee);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

115:                     : uint16((uint256(qrp.minRate) * votesCaSide + uint256(qrp.maxRate) * votesLpSide) / totalVotes); // U:[GA-15]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

42:         return (change * int256(uint256(rate))) / int16(PERCENTAGE_FACTOR);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

77:         if (int256(x) < 0 || (y != 0 && z / y != int256(x))) revert Math__mul_overflow_signed();

99:         z = mul(x, y) / int256(WAD);

210:     return wexp((wln(x) * y) / int256(WAD));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

### <a name="L-11"></a>[L-11] Duplicate import statements

*Instances (2)*:
```solidity
File: ./src/oracle/PendleLPOracle.sol

6: import "pendle/oracles/PendleLpOracleLib.sol";

13: import {PendleLpOracleLib} from "pendle/oracles/PendleLpOracleLib.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

### <a name="L-12"></a>[L-12] External calls in an un-bounded `for-`loop may result in a DOS
Consider limiting the number of iterations in for-loops that make external calls

*Instances (2)*:
```solidity
File: ./src/pendle-rewards/RewardManager.sol

101:                 rewardState[tokens[i]].lastBalance -= rewardAmounts[i].Uint128();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

```solidity
File: ./src/proxy/PositionAction.sol

299:                 (bool success, bytes memory response) = targets[i].call(data[i]);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

### <a name="L-13"></a>[L-13] External call recipient may consume all transaction gas
There is no limit specified on the amount of gas used, so the recipient can use up all of the transaction's gas, causing it to revert. Use `addr.call{gas: <amount>}("")` or [this](https://github.com/nomad-xyz/ExcessivelySafeCall) library instead.

*Instances (2)*:
```solidity
File: ./src/proxy/PositionAction.sol

299:                 (bool success, bytes memory response) = targets[i].call(data[i]);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

301:         (bool success, bytes memory result) = kyberRouter.call(payload);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

### <a name="L-14"></a>[L-14] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (5)*:
```solidity
File: ./src/oracle/ChainlinkOracle.sol

58:     function initialize(address admin, address manager) external initializer {

60:         __AccessControl_init();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

62:     ) initializer {

78:     function initialize(address admin, address manager) external initializer {

80:         __AccessControl_init();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

### <a name="L-15"></a>[L-15] Prevent accidentally burning tokens
Minting and burning tokens to address(0) prevention

*Instances (7)*:
```solidity
File: ./src/PoolV3.sol

313:         assets = mint(shares, receiver); // U:[LP-2A,2B,5,7]

423:         _mint(receiver, shares); // U:[LP-6,7]

436:         _mint(receiver, shares); // U:[LP-6,7]

453:         _burn(owner, shares); // U:[LP-8,9]

592:             _mint(treasury, _convertToShares(profit)); // U:[LP-14B]

606:             _burn(treasury_, sharesToBurn); // U:[LP-14C,14D]

948:         _mint(treasury, amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

### <a name="L-16"></a>[L-16] Possible rounding issue
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator. Also, there is indication of multiplication and division without the use of parenthesis which could result in issues.

*Instances (1)*:
```solidity
File: ./src/quotas/GaugeV3.sol

115:                     : uint16((uint256(qrp.minRate) * votesCaSide + uint256(qrp.maxRate) * votesLpSide) / totalVotes); // U:[GA-15]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

### <a name="L-17"></a>[L-17] Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*Instances (12)*:
```solidity
File: ./src/PoolV3.sol

715:         return (_totalDebt.borrowed * baseInterestRate().calcLinearGrowth(timestamp)) / RAY;

720:         return (_baseInterestIndexLU * (RAY + baseInterestRate().calcLinearGrowth(timestamp))) / RAY;

929:         return (amount * PERCENTAGE_FACTOR) / (PERCENTAGE_FACTOR - withdrawFee);

934:         return (amount * (PERCENTAGE_FACTOR - withdrawFee)) / PERCENTAGE_FACTOR;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

115:                     : uint16((uint256(qrp.minRate) * votesCaSide + uint256(qrp.maxRate) * votesLpSide) / totalVotes); // U:[GA-15]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

147:             quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR;

211:             quotaRevenue += (IPoolV3(pool).creditManagerBorrowed(creditManagers[token]) * rate) / PERCENTAGE_FACTOR; // U:[PQK-7]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

9: uint192 constant RAY_DIVIDED_BY_PERCENTAGE = uint192(RAY / PERCENTAGE_FACTOR);

37:         return uint128((uint256(quoted) * (cumulativeIndexNow - cumulativeIndexLU)) / RAY); // U:[QL-2]

42:         return (change * int256(uint256(rate))) / int16(PERCENTAGE_FACTOR);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

99:         z = mul(x, y) / int256(WAD);

210:     return wexp((wln(x) * y) / int256(WAD));

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

### <a name="L-18"></a>[L-18] Solidity version 0.8.20+ may not work on other chains due to `PUSH0`
The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new `PUSH0` op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version). While the project itself may or may not compile with 0.8.20, other projects with which it integrates, or which extend this project may, and those projects will have problems deploying these contracts/libraries.

*Instances (21)*:
```solidity
File: ./src/Flashlender.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/Locking.sol

2: pragma solidity ^0.8.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

4: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/Silo.sol

2: pragma solidity ^0.8.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

```solidity
File: ./src/StakingLPEth.sol

1: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

2: pragma solidity >=0.8.18;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/proxy/ERC165Plugin.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/ERC165Plugin.sol)

```solidity
File: ./src/proxy/PoolAction.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/PositionAction20.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction20.sol)

```solidity
File: ./src/proxy/PositionAction4626.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction4626.sol)

```solidity
File: ./src/proxy/PositionActionPendle.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionActionPendle.sol)

```solidity
File: ./src/proxy/SwapAction.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

```solidity
File: ./src/quotas/GaugeV3.sol

4: pragma solidity ^0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/GaugeV3.sol)

```solidity
File: ./src/quotas/PoolQuotaKeeperV3.sol

4: pragma solidity ^0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/PoolQuotaKeeperV3.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

4: pragma solidity ^0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

```solidity
File: ./src/vendor/Imports.sol

2: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/Imports.sol)

### <a name="L-19"></a>[L-19] Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership`
Use [Ownable2Step.transferOwnership](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) which is safer. Use it as it is more secure due to 2-stage ownership transfer.

**Recommended Mitigation Steps**

Use <a href="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol">Ownable2Step.sol</a>
  
  ```solidity
      function acceptOwnership() external {
          address sender = _msgSender();
          require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
          _transferOwnership(sender);
      }
```

*Instances (2)*:
```solidity
File: ./src/Locking.sol

6: import "@openzeppelin/contracts/access/Ownable.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/StakingLPEth.sol

5: import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

### <a name="L-20"></a>[L-20] File allows a version of solidity that is susceptible to an assembly optimizer bug
In solidity versions 0.8.13 and 0.8.14, there is an [optimizer bug](https://github.com/ethereum/solidity-blog/blob/499ab8abc19391be7b7b34f88953a067029a5b45/_posts/2022-06-15-inline-assembly-memory-side-effects-bug.md) where, if the use of a variable is in a separate `assembly` block from the block in which it was stored, the `mstore` operation is optimized out, leading to uninitialized memory. The code currently does not have such a pattern of execution, but it does use `mstore`s in `assembly` blocks, so it is a risk for future changes. The affected solidity versions should be avoided if at all possible.

*Instances (2)*:
```solidity
File: ./src/Locking.sol

2: pragma solidity ^0.8.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/Silo.sol

2: pragma solidity ^0.8.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Silo.sol)

### <a name="L-21"></a>[L-21] Sweeping may break accounting if tokens with multiple addresses are used
There have been [cases](https://blog.openzeppelin.com/compound-tusd-integration-issue-retrospective/) in the past where a token mistakenly had two addresses that could control its balance, and transfers using one address impacted the balance of the other. To protect against this potential scenario, sweep functions should ensure that the balance of the non-sweepable token does not change after the transfer of the swept tokens.

*Instances (3)*:
```solidity
File: ./src/CDPVault.sol

174:     error CDPVault__recoverERC20_invalidToken();

851:     function recoverERC20(address tokenAddress, address to, uint256 tokenAmount) external onlyRole(DEFAULT_ADMIN_ROLE) {

852:         if (tokenAddress == address(token)) revert CDPVault__recoverERC20_invalidToken();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="L-22"></a>[L-22] Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when downcasting
Downcasting from `uint256`/`int256` in Solidity does not revert on overflow. This can result in undesired exploitation or bugs, since developers usually assume that overflows raise errors. [OpenZeppelin's SafeCast library](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) restores this intuition by reverting the transaction when such an operation overflows. Using this library eliminates an entire class of bugs, so it's recommended to use it always. Some exceptions are acceptable like with the classic `uint256(uint160(address(variable)))`

*Instances (12)*:
```solidity
File: ./src/CDPVault.sol

212:         if (parameter == "debtFloor") vaultConfig.debtFloor = uint128(data);

213:         else if (parameter == "liquidationRatio") vaultConfig.liquidationRatio = uint64(data);

214:         else if (parameter == "liquidationPenalty") liquidationConfig.liquidationPenalty = uint64(data);

215:         else if (parameter == "liquidationDiscount") liquidationConfig.liquidationDiscount = uint64(data);

562:             uint96(cdd.debt),

757:                 newCumulativeQuotaInterest = uint128(cumulativeQuotaInterest - quotaInterestPaid); // U:[CL-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

180:         _baseInterestIndexLU = uint128(RAY); // U:[LP-1B]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/StakingLPEth.sol

110:         cooldowns[msg.sender].underlyingAmount += uint152(assets);

123:         cooldowns[msg.sender].underlyingAmount += uint152(assets);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/quotas/QuotasLogic.sol

9: uint192 constant RAY_DIVIDED_BY_PERCENTAGE = uint192(RAY / PERCENTAGE_FACTOR);

42:         return (change * int256(uint256(rate))) / int16(PERCENTAGE_FACTOR);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/quotas/QuotasLogic.sol)

```solidity
File: ./src/utils/Math.sol

25:     return uint64(x);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Math.sol)

### <a name="L-23"></a>[L-23] Unsafe ERC20 operation(s)

*Instances (7)*:
```solidity
File: ./src/Flashlender.sol

105:         underlyingToken.transferFrom(address(receiver), address(pool), total);

134:         underlyingToken.transferFrom(address(receiver), address(pool), total);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

```solidity
File: ./src/interfaces/IFlashlender.sol

115:         flashlender.underlyingToken().approve(address(flashlender), amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/IFlashlender.sol)

```solidity
File: ./src/proxy/PoolAction.sol

292:             IPMarket(market).transferFrom(poolActionParams.recipient, market, netLpIn);

294:             IPMarket(market).transfer(market, netLpIn);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PoolAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

390:             IPMarket(market).transferFrom(recipient, market, netLpIn);

392:             IPMarket(market).transfer(market, netLpIn);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

### <a name="L-24"></a>[L-24] Unsafe solidity low-level call can cause gas grief attack
Using the low-level calls of a solidity address can leave the contract open to gas grief attacks. These attacks occur when the called contract returns a large amount of data.

So when calling an external contract, it is necessary to check the length of the return data before reading/copying it (using `returndatasize()`).

*Instances (2)*:
```solidity
File: ./src/proxy/PositionAction.sol

299:                 (bool success, bytes memory response) = targets[i].call(data[i]);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

```solidity
File: ./src/proxy/SwapAction.sol

301:         (bool success, bytes memory result) = kyberRouter.call(payload);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/SwapAction.sol)

### <a name="L-25"></a>[L-25] Unspecific compiler version pragma

*Instances (5)*:
```solidity
File: ./src/prb-proxy/PRBProxyRegistry.sol

2: pragma solidity >=0.8.18;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/PRBProxyRegistry.sol)

```solidity
File: ./src/prb-proxy/interfaces/IPRBProxyRegistry.sol

2: pragma solidity >=0.8.4;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/prb-proxy/interfaces/IPRBProxyRegistry.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

15: pragma solidity >=0.7.1 <0.9.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

```solidity
File: ./src/vendor/IAsset.sol

15: pragma solidity >=0.7.0 <0.9.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/IAsset.sol)

```solidity
File: ./src/vendor/VaultReentrancyLib.sol

15: pragma solidity >=0.7.0 <0.9.0;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/VaultReentrancyLib.sol)

### <a name="L-26"></a>[L-26] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*Instances (7)*:
```solidity
File: ./src/oracle/ChainlinkOracle.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

15: contract ChainlinkOracle is IOracle, AccessControlUpgradeable, UUPSUpgradeable {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

17: contract PendleLPOracle is IOracle, AccessControlUpgradeable, UUPSUpgradeable {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/vendor/Imports.sol

6: import {TransparentUpgradeableProxy} from "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/Imports.sol)

### <a name="L-27"></a>[L-27] Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*Instances (15)*:
```solidity
File: ./src/oracle/ChainlinkOracle.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

15: contract ChainlinkOracle is IOracle, AccessControlUpgradeable, UUPSUpgradeable {

58:     function initialize(address admin, address manager) external initializer {

60:         __AccessControl_init();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

4: import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

5: import {AccessControlUpgradeable} from "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

17: contract PendleLPOracle is IOracle, AccessControlUpgradeable, UUPSUpgradeable {

62:     ) initializer {

78:     function initialize(address admin, address manager) external initializer {

80:         __AccessControl_init();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/vendor/BalancerErrors.sol

131:     uint256 internal constant UNINITIALIZED = 206;

152:     uint256 internal constant ORACLE_NOT_INITIALIZED = 313;

184:     uint256 internal constant UNINITIALIZED_POOL_CONTROLLER = 345;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/BalancerErrors.sol)

```solidity
File: ./src/vendor/Imports.sol

6: import {TransparentUpgradeableProxy} from "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/vendor/Imports.sol)


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Contracts are vulnerable to fee-on-transfer accounting-related issues | 4 |
| [M-2](#M-2) | `block.number` means different things on different L2s | 2 |
| [M-3](#M-3) | Centralization Risk for trusted owners | 22 |
| [M-4](#M-4) | Return values of `transfer()`/`transferFrom()` not checked | 2 |
| [M-5](#M-5) | Unsafe use of `transfer()`/`transferFrom()` with `IERC20` | 2 |
### <a name="M-1"></a>[M-1] Contracts are vulnerable to fee-on-transfer accounting-related issues
Consistently check account balance before and after transfers for Fee-On-Transfer discrepancies. As arbitrary ERC20 tokens can be used, the amount here should be calculated every time to take into consideration a possible fee-on-transfer or deflation.
Also, it's a good practice for the future of the solution.

Use the balance before and after the transfer to calculate the received amount instead of assuming that it would be equal to the amount passed as a parameter. Or explicitly document that such tokens shouldn't be used and won't be supported

*Instances (4)*:
```solidity
File: ./src/CDPVault.sol

511:             token.safeTransferFrom(collateralizer, address(this), amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Locking.sol

51:         token.safeTransferFrom(msg.sender, address(this), _amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/PoolV3.sol

415:         IERC20(underlyingToken).safeTransferFrom({from: msg.sender, to: address(this), value: assetsSent}); // U:[LP-6,7]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

```solidity
File: ./src/proxy/PositionAction.sol

606:             underlyingToken.safeTransferFrom(address(this), creditParams.creditor, creditParams.amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/proxy/PositionAction.sol)

### <a name="M-2"></a>[M-2] `block.number` means different things on different L2s
On Optimism, `block.number` is the L2 block number, but on Arbitrum, it's the L1 block number, and `ArbSys(address(100)).arbBlockNumber()` must be used. Furthermore, L2 block numbers often occur much more frequently than L1 block numbers (any may even occur on a per-transaction basis), so using block numbers for timing results in inconsistencies, especially when voting is involved across multiple chains. As of version 4.9, OpenZeppelin has [modified](https://blog.openzeppelin.com/introducing-openzeppelin-contracts-v4.9#governor) their governor code to use a clock rather than block numbers, to avoid these sorts of issues, but this still requires that the project [implement](https://docs.openzeppelin.com/contracts/4.x/governance#token_2) a [clock](https://eips.ethereum.org/EIPS/eip-6372) for each L2.

*Instances (2)*:
```solidity
File: ./src/pendle-rewards/RewardManager.sol

55:         if (lastRewardBlock != block.number) {

57:             lastRewardBlock = block.number;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/pendle-rewards/RewardManager.sol)

### <a name="M-3"></a>[M-3] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (22)*:
```solidity
File: ./src/CDPVault.sol

51: contract CDPVault is AccessControl, Pause, Permission, ICDPVaultBase {

211:     function setParameter(bytes32 parameter, uint256 data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {

224:     function setParameter(bytes32 parameter, address data) external whenNotPaused onlyRole(VAULT_CONFIG_ROLE) {

851:     function recoverERC20(address tokenAddress, address to, uint256 tokenAmount) external onlyRole(DEFAULT_ADMIN_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/Locking.sol

10: contract Locking is Ownable {

40:     function setCooldownPeriod(uint256 _cooldownPeriod) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

```solidity
File: ./src/StakingLPEth.sol

8: contract StakingLPEth is ERC4626, Ownable, ReentrancyGuard {

130:     function setCooldownDuration(uint24 duration) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

```solidity
File: ./src/Treasury.sol

12: contract Treasury is AccessControl, PaymentSplitter {

34:     function moveFunds(address payable treasury) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

45:     function moveFunds(address treasury, IERC20 token) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

55:     function moveFunds(address treasury, IERC20[] calldata tokens) external onlyRole(FUNDS_ADMINISTRATOR_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Treasury.sol)

```solidity
File: ./src/VaultRegistry.sol

12: contract VaultRegistry is AccessControl, IVaultRegistry {

39:     function addVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

49:     function removeVault(ICDPVault vault) external override(IVaultRegistry) onlyRole(VAULT_MANAGER_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/VaultRegistry.sol)

```solidity
File: ./src/interfaces/ICDPVault.sol

35: interface ICDPVaultBase is IAccessControl, IPause, IPermission {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/interfaces/ICDPVault.sol)

```solidity
File: ./src/oracle/ChainlinkOracle.sol

45:     function setOracles(address[] calldata _tokens, Oracle[] calldata _oracles) external onlyRole(DEFAULT_ADMIN_ROLE) {

70:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {}

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/ChainlinkOracle.sol)

```solidity
File: ./src/oracle/PendleLPOracle.sol

90:     function _authorizeUpgrade(address /*implementation*/) internal virtual override onlyRole(MANAGER_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/oracle/PendleLPOracle.sol)

```solidity
File: ./src/utils/Pause.sol

12: abstract contract Pause is AccessControl, Pausable, IPause {

30:     function pause() external onlyRole(PAUSER_ROLE) {

36:     function unpause() external onlyRole(PAUSER_ROLE) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/utils/Pause.sol)

### <a name="M-4"></a>[M-4] Return values of `transfer()`/`transferFrom()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `transfer()`/`transferFrom()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

*Instances (2)*:
```solidity
File: ./src/Flashlender.sol

105:         underlyingToken.transferFrom(address(receiver), address(pool), total);

134:         underlyingToken.transferFrom(address(receiver), address(pool), total);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

### <a name="M-5"></a>[M-5] Unsafe use of `transfer()`/`transferFrom()` with `IERC20`
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens.  For example Tether (USDT)'s `transfer()` and `transferFrom()` functions on L1 do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to `IERC20`, their [function signatures](https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca) do not match and therefore the calls made, revert (see [this](https://gist.github.com/IllIllI000/2b00a32e8f0559e8f386ea4f1800abc5) link for a test case). Use OpenZeppelin's `SafeERC20`'s `safeTransfer()`/`safeTransferFrom()` instead

*Instances (2)*:
```solidity
File: ./src/Flashlender.sol

105:         underlyingToken.transferFrom(address(receiver), address(pool), total);

134:         underlyingToken.transferFrom(address(receiver), address(pool), total);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Flashlender.sol)

