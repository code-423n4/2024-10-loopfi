# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings) | 10 |
| [GAS-2](#GAS-2) | Use assembly to check for `address(0)` | 5 |
| [GAS-3](#GAS-3) | Using bools for storage incurs overhead | 2 |
| [GAS-4](#GAS-4) | Cache array length outside of loop | 2 |
| [GAS-5](#GAS-5) | State variables should be cached in stack variables rather than re-reading them from storage | 12 |
| [GAS-6](#GAS-6) | For Operations that will not overflow, you could use unchecked | 270 |
| [GAS-7](#GAS-7) | Avoid contract existence checks by using low level calls | 1 |
| [GAS-8](#GAS-8) | Stack variable used as a cheaper cache for a state variable is only used once | 5 |
| [GAS-9](#GAS-9) | State variables only set in the constructor should be declared `immutable` | 12 |
| [GAS-10](#GAS-10) | Functions guaranteed to revert when called by normal users can be marked `payable` | 6 |
| [GAS-11](#GAS-11) | `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`) | 2 |
| [GAS-12](#GAS-12) | Using `private` rather than `public` for constants, saves gas | 1 |
| [GAS-13](#GAS-13) | Superfluous event fields | 1 |
| [GAS-14](#GAS-14) | Increments/decrements can be unchecked in for-loops | 2 |
| [GAS-15](#GAS-15) | Use != 0 instead of > 0 for unsigned integer comparison | 11 |
| [GAS-16](#GAS-16) | `internal` functions not called by the contract should be removed | 1 |
| [GAS-17](#GAS-17) | WETH address definition can be use directly | 1 |
### <a name="GAS-1"></a>[GAS-1] `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings)
This saves **16 gas per instance.**

*Instances (10)*:
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

### <a name="GAS-2"></a>[GAS-2] Use assembly to check for `address(0)`
*Saves 6 gas per instance*

*Instances (5)*:
```solidity
File: ./src/CDPVault.sol

332:         if (address(rewardManager) != address(0)) {

388:         if (address(rewardController) != address(0)) {

392:         if (address(rewardManager) != address(0)) _handleTokenRewards(owner, collateralBefore, deltaCollateral);

584:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

655:         if (owner == address(0) || repayAmount == 0) revert CDPVault__liquidatePosition_invalidParameters();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="GAS-3"></a>[GAS-3] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (2)*:
```solidity
File: ./src/PoolV3.sol

88:     bool public locked;

113:     mapping(address => bool) internal _allowed;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

### <a name="GAS-4"></a>[GAS-4] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (2)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="GAS-5"></a>[GAS-5] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*Saves 100 gas per instance*

*Instances (12)*:
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

### <a name="GAS-6"></a>[GAS-6] For Operations that will not overflow, you could use unchecked

*Instances (270)*:
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

### <a name="GAS-7"></a>[GAS-7] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*Instances (1)*:
```solidity
File: ./src/PoolV3.sol

202:         return IERC20(underlyingToken).balanceOf(address(this)); // U:[LP-3]

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

### <a name="GAS-8"></a>[GAS-8] Stack variable used as a cheaper cache for a state variable is only used once
If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the **3 gas** the extra stack assignment would spend

*Instances (5)*:
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

### <a name="GAS-9"></a>[GAS-9] State variables only set in the constructor should be declared `immutable`
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around **20 000 gas** per variable) and replace the expensive storage-reading operations (around **2100 gas** per reading) to a less expensive value reading (**3 gas**)

*Instances (12)*:
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

### <a name="GAS-10"></a>[GAS-10] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (6)*:
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

### <a name="GAS-11"></a>[GAS-11] `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)
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

*Instances (2)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="GAS-12"></a>[GAS-12] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (1)*:
```solidity
File: ./src/PoolV3.sol

65:     uint256 public constant override version = 3_00;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

### <a name="GAS-13"></a>[GAS-13] Superfluous event fields
`block.timestamp` and `block.number` are added to event information by default so adding them manually wastes gas

*Instances (1)*:
```solidity
File: ./src/Locking.sol

32:     event CooldownInitiated(address indexed user, uint256 timestamp, uint256 amount);

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/Locking.sol)

### <a name="GAS-14"></a>[GAS-14] Increments/decrements can be unchecked in for-loops
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

*Instances (2)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="GAS-15"></a>[GAS-15] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (11)*:
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

### <a name="GAS-16"></a>[GAS-16] `internal` functions not called by the contract should be removed
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*Instances (1)*:
```solidity
File: ./src/CDPVault.sol

796:     function calcAccruedInterest(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="GAS-17"></a>[GAS-17] WETH address definition can be use directly
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
| [NC-1](#NC-1) | Missing checks for `address(0)` when assigning values to address state variables | 7 |
| [NC-2](#NC-2) | `constant`s should be defined rather than using magic numbers | 2 |
| [NC-3](#NC-3) | Control structures do not follow the Solidity Style Guide | 64 |
| [NC-4](#NC-4) | Default Visibility for constants | 3 |
| [NC-5](#NC-5) | Consider disabling `renounceOwnership()` | 2 |
| [NC-6](#NC-6) | Unused `error` definition | 3 |
| [NC-7](#NC-7) | Event is never emitted | 1 |
| [NC-8](#NC-8) | Event missing indexed field | 2 |
| [NC-9](#NC-9) | Events that mark critical parameter changes should contain both the old and the new value | 9 |
| [NC-10](#NC-10) | Function ordering does not follow the Solidity style guide | 3 |
| [NC-11](#NC-11) | Functions should not be longer than 50 lines | 81 |
| [NC-12](#NC-12) | Interfaces should be defined in separate files from their usage | 2 |
| [NC-13](#NC-13) | Lack of checks in setters | 8 |
| [NC-14](#NC-14) | Missing Event for critical parameters change | 7 |
| [NC-15](#NC-15) | NatSpec is completely non-existent on functions that should have them | 9 |
| [NC-16](#NC-16) | Incomplete NatSpec: `@param` is missing on actually documented functions | 3 |
| [NC-17](#NC-17) | Incomplete NatSpec: `@return` is missing on actually documented functions | 6 |
| [NC-18](#NC-18) | File's first line is not an SPDX Identifier | 1 |
| [NC-19](#NC-19) | Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor | 10 |
| [NC-20](#NC-20) | Consider using named mappings | 5 |
| [NC-21](#NC-21) | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 1 |
| [NC-22](#NC-22) | Adding a `return` statement when the function defines a named return variable, is redundant | 9 |
| [NC-23](#NC-23) | Take advantage of Custom Error's return value property | 45 |
| [NC-24](#NC-24) | Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`) | 1 |
| [NC-25](#NC-25) | Contract does not follow the Solidity style guide's suggested layout ordering | 5 |
| [NC-26](#NC-26) | Internal and private variables and functions names should begin with an underscore | 3 |
| [NC-27](#NC-27) | Event is missing `indexed` fields | 9 |
| [NC-28](#NC-28) | `public` functions not called by the contract should be declared `external` instead | 1 |
| [NC-29](#NC-29) | Variables need not be initialized to zero | 2 |
### <a name="NC-1"></a>[NC-1] Missing checks for `address(0)` when assigning values to address state variables

*Instances (7)*:
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

### <a name="NC-2"></a>[NC-2] `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*Instances (2)*:
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

### <a name="NC-3"></a>[NC-3] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*Instances (64)*:
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

### <a name="NC-4"></a>[NC-4] Default Visibility for constants
Some constants are using the default visibility. For readability, consider explicitly declaring them as `internal`.

*Instances (3)*:
```solidity
File: ./src/CDPVault.sol

45: bytes32 constant VAULT_CONFIG_ROLE = keccak256("VAULT_CONFIG_ROLE");

46: bytes32 constant VAULT_UNWINDER_ROLE = keccak256("VAULT_UNWINDER_ROLE");

71:     uint256 constant INDEX_PRECISION = 10 ** 9;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="NC-5"></a>[NC-5] Consider disabling `renounceOwnership()`
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

### <a name="NC-6"></a>[NC-6] Unused `error` definition
Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.

*Instances (3)*:
```solidity
File: ./src/CDPVault.sol

165:     error CDPVault__modifyCollateralAndDebt_maxUtilizationRatio();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

```solidity
File: ./src/PoolV3.sol

58:     error CallerNotManagerException();

61:     error WethTransferFailed();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

### <a name="NC-7"></a>[NC-7] Event is never emitted
The following are defined but never emitted. They can be removed to make the code cleaner.

*Instances (1)*:
```solidity
File: ./src/CDPVault.sol

150:     event LiquidatePosition(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="NC-8"></a>[NC-8] Event missing indexed field
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be indexed.

*Instances (2)*:
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

### <a name="NC-9"></a>[NC-9] Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*Instances (9)*:
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

### <a name="NC-10"></a>[NC-10] Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*Instances (3)*:
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

### <a name="NC-11"></a>[NC-11] Functions should not be longer than 50 lines
Overly complex code can make understanding functionality more difficult, try to further modularize your code to ensure readability 

*Instances (81)*:
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

### <a name="NC-12"></a>[NC-12] Interfaces should be defined in separate files from their usage
The interfaces below should be defined in separate files, so that it's easier for future projects to import them, and to avoid duplication later on if they need to be used elsewhere in the project

*Instances (2)*:
```solidity
File: ./src/CDPVault.sol

24: interface IPoolV3Loop is IPoolV3 {

34: interface IRewardManager {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="NC-13"></a>[NC-13] Lack of checks in setters
Be it sanity checks (like checks against `0`-values) or initial setting checks: it's best for Setter functions to have them

*Instances (8)*:
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

### <a name="NC-14"></a>[NC-14] Missing Event for critical parameters change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

*Instances (7)*:
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

### <a name="NC-15"></a>[NC-15] NatSpec is completely non-existent on functions that should have them
Public and external functions that aren't view or pure should have NatSpec comments

*Instances (9)*:
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

### <a name="NC-16"></a>[NC-16] Incomplete NatSpec: `@param` is missing on actually documented functions
The following functions are missing `@param` NatSpec comments.

*Instances (3)*:
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

### <a name="NC-17"></a>[NC-17] Incomplete NatSpec: `@return` is missing on actually documented functions
The following functions are missing `@return` NatSpec comments.

*Instances (6)*:
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

### <a name="NC-18"></a>[NC-18] File's first line is not an SPDX Identifier

*Instances (1)*:
```solidity
File: ./src/StakingLPEth.sol

1: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

### <a name="NC-19"></a>[NC-19] Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor
If a function is supposed to be access-controlled, a `modifier` should be used instead of a `require/if` statement for more readability.

*Instances (10)*:
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

### <a name="NC-20"></a>[NC-20] Consider using named mappings
Consider moving to solidity version 0.8.18 or later, and using [named mappings](https://ethereum.stackexchange.com/questions/51629/how-to-name-the-arguments-in-mapping/145555#145555) to make it easier to understand the purpose of each mapping

*Instances (5)*:
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

### <a name="NC-21"></a>[NC-21] Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` *function* should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*Instances (1)*:
```solidity
File: ./src/StakingLPEth.sol

1: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/StakingLPEth.sol)

### <a name="NC-22"></a>[NC-22] Adding a `return` statement when the function defines a named return variable, is redundant

*Instances (9)*:
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

### <a name="NC-23"></a>[NC-23] Take advantage of Custom Error's return value property
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

*Instances (45)*:
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

### <a name="NC-24"></a>[NC-24] Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While this won't save gas in the recent solidity versions, this is shorter and more readable (this is especially true in calculations).

*Instances (1)*:
```solidity
File: ./src/CDPVault.sol

71:     uint256 constant INDEX_PRECISION = 10 ** 9;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="NC-25"></a>[NC-25] Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be:

1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

However, the contract(s) below do not follow this ordering

*Instances (5)*:
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

### <a name="NC-26"></a>[NC-26] Internal and private variables and functions names should begin with an underscore
According to the Solidity Style Guide, Non-`external` variable and function names should begin with an [underscore](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables)

*Instances (3)*:
```solidity
File: ./src/CDPVault.sol

730:     function calcDecrease(

796:     function calcAccruedInterest(

814:     function calcTotalDebt(DebtData memory debtData) internal pure returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="NC-27"></a>[NC-27] Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*Instances (9)*:
```solidity
File: ./src/CDPVault.sol

140:     event ModifyPosition(address indexed position, uint256 debt, uint256 collateral, uint256 totalDebt);

148:     event SetParameter(bytes32 indexed parameter, uint256 data);

149:     event SetParameter(bytes32 indexed parameter, address data);

150:     event LiquidatePosition(

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

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

### <a name="NC-28"></a>[NC-28] `public` functions not called by the contract should be declared `external` instead

*Instances (1)*:
```solidity
File: ./src/PoolV3.sol

259:     function depositETH(address receiver)

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

### <a name="NC-29"></a>[NC-29] Variables need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.

*Instances (2)*:
```solidity
File: ./src/CDPVault.sol

323:             for (uint256 i = 0; i < tokens.length; i++) {

336:             for (uint256 i = 0; i < tokens.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Use a 2-step ownership transfer pattern | 2 |
| [L-2](#L-2) | Some tokens may revert when zero value transfers are made | 18 |
| [L-3](#L-3) | Missing checks for `address(0)` when assigning values to address state variables | 7 |
| [L-4](#L-4) | `decimals()` is not a part of the ERC-20 standard | 2 |
| [L-5](#L-5) | Division by zero not prevented | 2 |
| [L-6](#L-6) | Prevent accidentally burning tokens | 7 |
| [L-7](#L-7) | Loss of precision | 4 |
| [L-8](#L-8) | Solidity version 0.8.20+ may not work on other chains due to `PUSH0` | 4 |
| [L-9](#L-9) | Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership` | 2 |
| [L-10](#L-10) | File allows a version of solidity that is susceptible to an assembly optimizer bug | 2 |
| [L-11](#L-11) | Sweeping may break accounting if tokens with multiple addresses are used | 3 |
| [L-12](#L-12) | Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when downcasting | 9 |
### <a name="L-1"></a>[L-1] Use a 2-step ownership transfer pattern
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

### <a name="L-2"></a>[L-2] Some tokens may revert when zero value transfers are made
Example: https://github.com/d-xo/weird-erc20#revert-on-zero-value-transfers.

In spite of the fact that EIP-20 [states](https://github.com/ethereum/EIPs/blob/46b9b698815abbfa628cd1097311deee77dd45c5/EIPS/eip-20.md?plain=1#L116) that zero-valued transfers must be accepted, some tokens, such as LEND will revert if this is attempted, which may cause transactions that involve other tokens (such as batch operations) to fully revert. Consider skipping the transfer if the amount is zero, which will also save gas.

*Instances (18)*:
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

### <a name="L-3"></a>[L-3] Missing checks for `address(0)` when assigning values to address state variables

*Instances (7)*:
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

### <a name="L-4"></a>[L-4] `decimals()` is not a part of the ERC-20 standard
The `decimals()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*Instances (2)*:
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

### <a name="L-5"></a>[L-5] Division by zero not prevented
The divisions below take an input parameter which does not have any zero-value checks, which may lead to the functions reverting when zero is passed.

*Instances (2)*:
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

### <a name="L-6"></a>[L-6] Prevent accidentally burning tokens
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

### <a name="L-7"></a>[L-7] Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*Instances (4)*:
```solidity
File: ./src/PoolV3.sol

715:         return (_totalDebt.borrowed * baseInterestRate().calcLinearGrowth(timestamp)) / RAY;

720:         return (_baseInterestIndexLU * (RAY + baseInterestRate().calcLinearGrowth(timestamp))) / RAY;

929:         return (amount * PERCENTAGE_FACTOR) / (PERCENTAGE_FACTOR - withdrawFee);

934:         return (amount * (PERCENTAGE_FACTOR - withdrawFee)) / PERCENTAGE_FACTOR;

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/PoolV3.sol)

### <a name="L-8"></a>[L-8] Solidity version 0.8.20+ may not work on other chains due to `PUSH0`
The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new `PUSH0` op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version). While the project itself may or may not compile with 0.8.20, other projects with which it integrates, or which extend this project may, and those projects will have problems deploying these contracts/libraries.

*Instances (4)*:
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

### <a name="L-9"></a>[L-9] Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership`
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

### <a name="L-10"></a>[L-10] File allows a version of solidity that is susceptible to an assembly optimizer bug
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

### <a name="L-11"></a>[L-11] Sweeping may break accounting if tokens with multiple addresses are used
There have been [cases](https://blog.openzeppelin.com/compound-tusd-integration-issue-retrospective/) in the past where a token mistakenly had two addresses that could control its balance, and transfers using one address impacted the balance of the other. To protect against this potential scenario, sweep functions should ensure that the balance of the non-sweepable token does not change after the transfer of the swept tokens.

*Instances (3)*:
```solidity
File: ./src/CDPVault.sol

174:     error CDPVault__recoverERC20_invalidToken();

851:     function recoverERC20(address tokenAddress, address to, uint256 tokenAmount) external onlyRole(DEFAULT_ADMIN_ROLE) {

852:         if (tokenAddress == address(token)) revert CDPVault__recoverERC20_invalidToken();

```
[Link to code](https://github.com/code-423n4/2024-10-loopfi/tree/main/./src/CDPVault.sol)

### <a name="L-12"></a>[L-12] Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when downcasting
Downcasting from `uint256`/`int256` in Solidity does not revert on overflow. This can result in undesired exploitation or bugs, since developers usually assume that overflows raise errors. [OpenZeppelin's SafeCast library](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) restores this intuition by reverting the transaction when such an operation overflows. Using this library eliminates an entire class of bugs, so it's recommended to use it always. Some exceptions are acceptable like with the classic `uint256(uint160(address(variable)))`

*Instances (9)*:
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


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Contracts are vulnerable to fee-on-transfer accounting-related issues | 3 |
| [M-2](#M-2) | Centralization Risk for trusted owners | 8 |
### <a name="M-1"></a>[M-1] Contracts are vulnerable to fee-on-transfer accounting-related issues
Consistently check account balance before and after transfers for Fee-On-Transfer discrepancies. As arbitrary ERC20 tokens can be used, the amount here should be calculated every time to take into consideration a possible fee-on-transfer or deflation.
Also, it's a good practice for the future of the solution.

Use the balance before and after the transfer to calculate the received amount instead of assuming that it would be equal to the amount passed as a parameter. Or explicitly document that such tokens shouldn't be used and won't be supported

*Instances (3)*:
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

### <a name="M-2"></a>[M-2] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (8)*:
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

