# LoopFi audit details
- Total Prize Pool: $25,000 in USDC
  - HM awards: $21,000 in USDC
  - Judge awards: $2,300 in USDC
  - Validator awards: $1,200 USDC
  - Scout awards: $500 in USDC
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts October 10, 2024 20:00 UTC
- Ends October 17, 2024 20:00 UTC

ℹ️ While there are no QA awards, QA reports are encouraged as a fallback in the event of no valid HMs.

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-10-loopfi/blob/main/4naly3er-report.md).



_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._

# Overview

**Loop** is a decentralized finance (DeFi) platform designed to optimize yield distribution among participants through innovative lending, borrowing, and staking mechanisms.

This is a flow diagram of the Loop DeFi system:

![Loop DeFi Diagram](assets/diagram.png)



The platform's core concept revolves around leveraging ETH-based yields, utilizing receipt tokens, and staking mechanisms to align incentives between various actors:

- **Lenders:** Receive `lpETH` tokens in exchange for ETH deposits, which can be further staked to earn passive yield in ETH.
- **Borrowers (Loopers):** Can borrow ETH against yielding LRT derivatives to engage in carry trades and optimize returns on ETH-based yields.
- **dLP Lockers:** Lock the governance token `LOOP` into a Dynamic Liquidity Position (`dLP`) to earn protocol revenue.
- **Interest Rebates:** Loopers can offset borrow costs by locking 5% of their Total Looped Position Size in the `dLP`, effectively receiving rebates on interest paid.

## Core Smart Contracts

### 1. CDPVault

The `CDPVault` contract manages the core logic of the borrowing and lending functionalities. It allows users to deposit collateral, borrow against it, and handle repayments.

**Public Interface:**
- `deposit(address to, uint256 amount)`: Deposits collateral tokens into the vault.
- `withdraw(address to, uint256 amount)`: Withdraws collateral from the vault.
- `borrow(address borrower, address position, uint256 amount)`: Borrows ETH against collateral.
- `repay(address borrower, address position, uint256 amount)`: Repays borrowed ETH.

**Liquidity Management:**
The `CDPVault` utilizes `PoolV3` for liquidity management. The liquidity pool facilitates lending and borrowing activities, managing deposits, withdrawals, and interest accruals.

#### Interaction with PoolV3:
- **Borrowing:** The `CDPVault` borrows from `PoolV3` against the deposited collateral and the available liquidity.
- **Repayments:** Repayments to the vault are forwarded to `PoolV3`, updating the total debt and interest accordingly.

### 2. PoolV3

`PoolV3` is a contract that provides a liquidity pool for the `CDPVault`. It handles the aggregation of funds, debt management, and distribution of interest among liquidity providers.

**Key Functions:**
- `deposit(uint256 assets, address receiver)`: Allows underlying asset deposits into the pool.
- `withdraw(uint256 assets, address receiver, address owner)`: Withdraws the specified amount from the pool.
- `lendCreditAccount(uint256 borrowedAmount, address creditAccount)`: Facilitates lending to credit accounts.
- `repayCreditAccount(uint256 repaidAmount, uint256 profit, uint256 loss)`: Handles repayments and adjusts the pool's state accordingly.

### 3. Treasury 

The `Treasury` contract manages protocol revenues, distributing fees generated by the `CDPVault` and `PoolV3` among stakeholders, including dLP lockers and ETH stakers.

### 4. StakingLPEth

The `StakingLPEth` contract allows `lpETH` holders to stake their tokens and earn passive yield in ETH.

### 5. FlashLender

The `FlashLender` contract enables leveraging positions and executing flash loans.

## Auxiliary Contracts

The Loop protocol provides a set of auxiliary contracts to enhance user experience and flexibility. These contracts work in conjunction with the core contracts, enabling complex interactions and optimizations.

### Action Contracts

Action contracts facilitate the bundling of multiple transactions into a single contract interaction, allowing users to efficiently manage their positions and leverage within the protocol.

- **PoolAction:** Facilitates interactions with the pool, such as deposits and withdrawals.
- **PositionAction:** Bundles transactions related to the `CDPVault`, allowing users to leverage positions by performing multiple actions in a single transaction.
- **Swap and Transfer Actions:** Provide wrappers over swapping and transferring functionalities, enabling users to perform these actions seamlessly within the protocol.

### Oracle Contracts

The protocol uses robust and trustworthy price feeds powered by Chainlink. These Oracle contracts ensure reliable and accurate asset pricing, which is critical for maintaining the integrity of the system.

- **Price Feeds:** The Oracle contracts fetch asset prices using Chainlink, providing secure and decentralized data feeds for the protocol.

## Links

- **Previous audits:**
  - [Report #1](https://notes.watchpug.com/p/18ea3089e2esgBHp) 
  - [Report #2](https://notes.watchpug.com/p/1909aa8a565HVvGe) 
  - [Report #3](https://notes.watchpug.com/p/190becc04cemgrXz) 
  - [Report #4](https://notes.watchpug.com/p/190c8fbf44ek5zZ4) 
  - [Report #5](https://notes.watchpug.com/p/190dd9d39acrEJAv) 
- [**Documentation**](https://docs.loopfi.xyz/) 
- [**Website**](https://www.loopfi.xyz/) 
- [**X/Twitter**](https://twitter.com/loopfixyz) 
- [**Discord**](https://discord.com/invite/mVqf2Q5Whg)

---

# Scope


*See [scope.txt](https://github.com/code-423n4/2024-10-loopfi/blob/main/scope.txt)*

### Files in scope


| File   | Logic Contracts | Interfaces | nSLOC | Purpose | Libraries used |
| ------ | --------------- | ---------- | ----- | -----   | ------------ |
| /src/PoolV3.sol | 1| **** | 488 | |@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/interfaces/IERC4626.sol<br>@openzeppelin/contracts/interfaces/IERC20Metadata.sol<br>@openzeppelin/contracts/token/ERC20/ERC20.sol<br>@openzeppelin/contracts/token/ERC20/extensions/ERC4626.sol<br>@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol<br>@openzeppelin/contracts/utils/math/Math.sol<br>@openzeppelin/contracts/utils/math/SafeCast.sol<br>@openzeppelin/contracts/utils/structs/EnumerableSet.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IAddressProviderV3.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/ICreditManagerV3.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/ILinearInterestRateModelV3.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IPoolQuotaKeeperV3.sol<br>@gearbox-protocol/core-v3/contracts/libraries/CreditLogic.sol<br>@gearbox-protocol/core-v3/contracts/traits/ACLNonReentrantTrait.sol<br>@gearbox-protocol/core-v3/contracts/traits/ContractsRegisterTrait.sol<br>@gearbox-protocol/core-v2/contracts/libraries/Constants.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IExceptions.sol|
| /src/Silo.sol | 1| **** | 20 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol|
| /src/CDPVault.sol | 1| 2 | 479 | |@openzeppelin/contracts/access/AccessControl.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/utils/math/SafeCast.sol<br>@gearbox-protocol/core-v3/contracts/libraries/CreditLogic.sol<br>@gearbox-protocol/core-v3/contracts/libraries/QuotasLogic.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IPoolQuotaKeeperV3.sol|
| /src/StakingLPEth.sol | 1| **** | 90 | |@openzeppelin/contracts/token/ERC20/extensions/ERC4626.sol<br>@openzeppelin/contracts/security/ReentrancyGuard.sol<br>@openzeppelin/contracts/access/Ownable.sol<br>src/Silo.sol|
| /src/Locking.sol | 1| **** | 64 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/access/Ownable.sol|
| /src/Flashlender.sol | 1| **** | 59 | |@openzeppelin/contracts/security/ReentrancyGuard.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /src/VaultRegistry.sol | 1| **** | 60 | |@openzeppelin/contracts/access/AccessControl.sol|
| /src/Treasury.sol | 1| **** | 39 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/utils/Address.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/utils/math/SafeCast.sol<br>@openzeppelin/contracts/finance/PaymentSplitter.sol<br>@openzeppelin/contracts/access/AccessControl.sol|
| /src/proxy/PositionActionPendle.sol | 1| **** | 60 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol|
| /src/proxy/TransferAction.sol | 1| **** | 51 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/token/ERC20/extensions/draft-IERC20Permit.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>permit2/interfaces/ISignatureTransfer.sol|
| /src/proxy/PositionAction.sol | 1| **** | 339 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol|
| /src/proxy/PositionAction20.sol | 1| **** | 30 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol|
| /src/proxy/PoolAction.sol | 1| **** | 207 | |@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol<br>pendle/interfaces/IPActionAddRemoveLiqV3.sol<br>pendle/interfaces/IPAllActionTypeV3.sol<br>pendle/router/base/MarketApproxLib.sol<br>pendle/interfaces/IPPrincipalToken.sol<br>pendle/interfaces/IStandardizedYield.sol<br>pendle/interfaces/IPYieldToken.sol<br>pendle/interfaces/IPMarket.sol|
| /src/proxy/PositionAction4626.sol | 1| **** | 76 | |@openzeppelin/contracts/interfaces/IERC20.sol<br>@openzeppelin/contracts/interfaces/IERC4626.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol|
| /src/proxy/BaseAction.sol | 1| **** | 17 | ||
| /src/proxy/SwapAction.sol | 1| **** | 254 | |@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol<br>pendle/interfaces/IPAllActionTypeV3.sol<br>pendle/router/base/MarketApproxLib.sol<br>pendle/interfaces/IPActionAddRemoveLiqV3.sol<br>pendle/interfaces/IPPrincipalToken.sol<br>pendle/interfaces/IStandardizedYield.sol<br>pendle/interfaces/IPYieldToken.sol<br>pendle/interfaces/IPMarket.sol<br>forge-std/console.sol|
| /src/proxy/ERC165Plugin.sol | 1| **** | 12 | |@openzeppelin/contracts/token/ERC1155/utils/ERC1155Holder.sol<br>@openzeppelin/contracts/token/ERC721/utils/ERC721Holder.sol<br>prb-proxy/interfaces/IPRBProxyPlugin.sol|
| /src/quotas/GaugeV3.sol | 1| **** | 160 | |@gearbox-protocol/core-v3/contracts/interfaces/IGaugeV3.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IGearStakingV3.sol<br>src/interfaces/IPoolQuotaKeeperV3.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IPoolV3.sol<br>@gearbox-protocol/core-v3/contracts/traits/ACLNonReentrantTrait.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IExceptions.sol|
| /src/quotas/PoolQuotaKeeperV3.sol | 1| **** | 131 | |@openzeppelin/contracts/utils/structs/EnumerableSet.sol<br>@gearbox-protocol/core-v3/contracts/traits/ACLNonReentrantTrait.sol<br>@gearbox-protocol/core-v3/contracts/traits/ContractsRegisterTrait.sol<br>@gearbox-protocol/core-v3/contracts/libraries/QuotasLogic.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IPoolV3.sol<br>src/interfaces/IPoolQuotaKeeperV3.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IGaugeV3.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/ICreditManagerV3.sol<br>@gearbox-protocol/core-v2/contracts/libraries/Constants.sol<br>@gearbox-protocol/core-v3/contracts/interfaces/IExceptions.sol|
| /src/quotas/QuotasLogic.sol | 1| **** | 30 | |@openzeppelin/contracts/utils/math/SafeCast.sol<br>@gearbox-protocol/core-v2/contracts/libraries/Constants.sol|
| /src/oracle/BalancerOracle.sol | 1| **** | 93 | |@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol<br>@openzeppelin/contracts/token/ERC20/ERC20.sol<br>src/vendor/VaultReentrancyLib.sol|
| /src/oracle/ChainlinkOracle.sol | 1| **** | 53 | |@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol|
| /src/oracle/PendleLPOracle.sol | 1| **** | 84 | |@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol<br>pendle/oracles/PendleLpOracleLib.sol<br>pendle/interfaces/IPMarket.sol<br>pendle/interfaces/IPPtOracle.sol|
| /src/utils/Math.sol | ****| **** | 228 | ||
| /src/utils/Pause.sol | 1| **** | 19 | |@openzeppelin/contracts/access/AccessControl.sol<br>@openzeppelin/contracts/security/Pausable.sol|
| /src/utils/Permission.sol | 1| **** | 26 | ||
| /src/pendle-rewards/RewardManager.sol | 1| 1 | 79 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>forge-std/console.sol|
| /src/pendle-rewards/RewardManagerAbstract.sol | 1| **** | 51 | |pendle/interfaces/IRewardManager.sol<br>pendle/core/libraries/ArrayLib.sol<br>pendle/core/libraries/TokenHelper.sol<br>pendle/core/libraries/math/PMath.sol<br>@openzeppelin/contracts/utils/math/SafeCast.sol<br>forge-std/console.sol|
| /src/prb-proxy/PRBProxyRegistry.sol | 1| **** | 126 | |prb-proxy/interfaces/IPRBProxy.sol<br>prb-proxy/interfaces/IPRBProxyPlugin.sol<br>prb-proxy/PRBProxy.sol|
| /src/prb-proxy/interfaces/IPRBProxyRegistry.sol | ****| 1 | 47 | |prb-proxy/interfaces/IPRBProxy.sol<br>prb-proxy/interfaces/IPRBProxyPlugin.sol|
| /src/vendor/IUniswapV3Router.sol | ****| 1 | 33 | ||
| /src/vendor/IPriceOracle.sol | ****| 1 | 17 | ||
| /src/vendor/ICurvePool.sol | ****| 1 | 3 | ||
| /src/vendor/AggregatorV3Interface.sol | ****| 1 | 3 | ||
| /src/vendor/Imports.sol | ****| **** | 6 | |@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol<br>@openzeppelin/contracts/proxy/transparent/ProxyAdmin.sol<br>permit2/interfaces/IAllowanceTransfer.sol<br>pendle/core/Market/PendleMarket.sol|
| /src/vendor/IBalancerVault.sol | ****| 1 | 67 | |src/vendor/IAsset.sol|
| /src/vendor/VaultReentrancyLib.sol | 1| **** | 11 | |src/vendor/BalancerErrors.sol<br>src/vendor/IBalancerVault.sol|
| /src/vendor/IWeightedPool.sol | ****| 1 | 3 | ||
| /src/vendor/BalancerErrors.sol | 1| **** | 197 | ||
| /src/vendor/IAsset.sol | ****| 1 | 3 | ||
| /src/interfaces/IInterestRateModel.sol | ****| 1 | 3 | ||
| /src/interfaces/IPermission.sol | ****| 1 | 3 | ||
| /src/interfaces/IPoolV3.sol | ****| 2 | 19 | |@openzeppelin/contracts/interfaces/IERC4626.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Permit.sol<br>@gearbox-protocol/core-v2/contracts/interfaces/IVersion.sol|
| /src/interfaces/ICDM.sol | ****| 1 | 4 | ||
| /src/interfaces/IStablecoin.sol | ****| 2 | 5 | |@openzeppelin/contracts/token/ERC20/extensions/IERC20Permit.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol|
| /src/interfaces/IOracle.sol | ****| 1 | 4 | ||
| /src/interfaces/IVaultRegistry.sol | ****| 1 | 4 | ||
| /src/interfaces/IBuffer.sol | ****| 1 | 4 | ||
| /src/interfaces/IPause.sol | ****| 1 | 3 | ||
| /src/interfaces/IFlashlender.sol | 1| 5 | 19 | |@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /src/interfaces/IMinter.sol | ****| 1 | 5 | ||
| /src/interfaces/ICDPVault_Deployer.sol | ****| 1 | 3 | ||
| /src/interfaces/IPoolQuotaKeeperV3.sol | ****| 2 | 24 | |@gearbox-protocol/core-v2/contracts/interfaces/IVersion.sol|
| /src/interfaces/ICDPVault.sol | ****| 2 | 28 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/access/IAccessControl.sol|
| **Totals** | **31** | **33** | **3943** | | |

### Files out of scope

*See [out_of_scope.txt](https://github.com/code-423n4/2024-10-loopfi/blob/main/out_of_scope.txt)*

## Scoping Q &amp; A

### General questions

| Question                                | Answer                       |
| --------------------------------------- | ---------------------------- |
| ERC20 used by the protocol              |       WETH, PendleLPs             |
| Test coverage                           | 80.13% (1339/1671 statements)       |
| ERC721 used  by the protocol            |            None              |
| ERC777 used by the protocol             |           None             |
| ERC1155 used by the protocol            |              None            |
| Chains the protocol will be deployed on | Ethereum, Arbitrum           |

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

Debt >= Borrowed


## Attack ideas (where to focus for bugs)
Verify fixes from the first contest and audit the newly added contracts:
* src/Locking.sol
* src/Treasury.sol
* src/pendle-rewards/RewardManager.sol
* src/pendle-rewards/RewardManagerAbstract.sol

## All trusted roles in the protocol


| Role                                | Description                       |
| --------------------------------------- | ---------------------------- |
| Admin                                  |  can run permitted transactions like paused the contracts, add or remove vaults, add or remove configurators                 |
| Configurator                             |     can parameterize the contracts                        |

## Describe any novel or unique curve logic or mathematical models implemented in the contracts:

N/A


## Running tests

```bash
git clone --recurse https://github.com/code-423n4/2024-10-loopfi.git
cd 2024-10-loopfi
cp example.env .env

# Fill the MAINNET_RPC_URL field with your own Alchemy RPC URL in the .env file (or try to use a public one like https://eth.llamarpc.com)

forge install
forge test
forge coverage
```

## coverage

| File                                         | % Lines            | % Statements       | % Branches       | % Funcs          |
|----------------------------------------------|--------------------|--------------------|------------------|------------------|
| src/CDPVault.sol                             | 93.62% (220/235)   | 92.09% (291/316)   | 73.77% (45/61)   | 88.89% (24/27)   |
| src/Flashlender.sol                          | 96.43% (27/28)     | 97.30% (36/37)     | 80.00% (4/5)     | 100.00% (5/5)    |
| src/Locking.sol                              | 96.00% (24/25)     | 93.55% (29/31)     | 85.71% (6/7)     | 85.71% (6/7)     |
| src/PoolV3.sol                               | 66.84% (131/196)   | 65.48% (165/252)   | 47.83% (11/23)   | 52.05% (38/73)   |
| src/Silo.sol                                 | 100.00% (4/4)      | 80.00% (4/5)       | 0.00% (0/1)      | 100.00% (3/3)    |
| src/StakingLPEth.sol                         | 91.18% (31/34)     | 81.25% (39/48)     | 25.00% (2/8)     | 83.33% (10/12)   |
| src/Treasury.sol                             | 100.00% (14/14)    | 100.00% (17/17)    | 100.00% (1/1)    | 100.00% (5/5)    |
| src/VaultRegistry.sol                        | 45.83% (11/24)     | 46.43% (13/28)     | 0.00% (0/3)      | 57.14% (4/7)     |
| src/interfaces/IFlashlender.sol              | 100.00% (2/2)      | 100.00% (2/2)      | 100.00% (0/0)    | 100.00% (2/2)    |
| src/oracle/BalancerOracle.sol                | 97.50% (39/40)     | 96.36% (53/55)     | 88.89% (8/9)     | 87.50% (7/8)     |
| src/oracle/ChainlinkOracle.sol               | 81.25% (13/16)     | 80.95% (17/21)     | 100.00% (1/1)    | 57.14% (4/7)     |
| src/oracle/PendleLPOracle.sol                | 92.86% (26/28)     | 91.89% (34/37)     | 66.67% (2/3)     | 100.00% (8/8)    |
| src/pendle-rewards/RewardManager.sol         | 97.30% (36/37)     | 96.00% (48/50)     | 87.50% (7/8)     | 100.00% (7/7)    |
| src/pendle-rewards/RewardManagerAbstract.sol | 100.00% (20/20)    | 100.00% (29/29)    | 83.33% (5/6)     | 100.00% (3/3)    |
| src/prb-proxy/PRBProxyRegistry.sol           | 43.24% (16/37)     | 53.45% (31/58)     | 20.00% (1/5)     | 45.00% (9/20)    |
| src/proxy/BaseAction.sol                     | 83.33% (5/6)       | 75.00% (6/8)       | 0.00% (0/2)      | 100.00% (2/2)    |
| src/proxy/ERC165Plugin.sol                   | 100.00% (4/4)      | 100.00% (4/4)      | 100.00% (0/0)    | 100.00% (1/1)    |
| src/proxy/PoolAction.sol                     | 87.95% (73/83)     | 88.07% (96/109)    | 74.19% (23/31)   | 100.00% (9/9)    |
| src/proxy/PositionAction.sol                 | 86.51% (109/126)   | 84.66% (138/163)   | 71.79% (28/39)   | 100.00% (19/19)  |
| src/proxy/PositionAction20.sol               | 100.00% (8/8)      | 100.00% (12/12)    | 100.00% (0/0)    | 100.00% (5/5)    |
| src/proxy/PositionAction4626.sol             | 71.43% (25/35)     | 70.45% (31/44)     | 42.86% (3/7)     | 80.00% (4/5)     |
| src/proxy/PositionActionPendle.sol           | 63.16% (12/19)     | 64.00% (16/25)     | 66.67% (2/3)     | 80.00% (4/5)     |
| src/proxy/SwapAction.sol                     | 87.36% (76/87)     | 79.49% (93/117)    | 55.88% (19/34)   | 80.00% (8/10)    |
| src/proxy/TransferAction.sol                 | 100.00% (6/6)      | 100.00% (6/6)      | 100.00% (4/4)    | 100.00% (1/1)    |
| src/quotas/GaugeV3.sol                       | 60.00% (45/75)     | 59.57% (56/94)     | 29.41% (5/17)    | 61.11% (11/18)   |
| src/quotas/PoolQuotaKeeperV3.sol             | 72.88% (43/59)     | 72.22% (52/72)     | 25.00% (1/4)     | 80.00% (12/15)   |
| src/quotas/QuotasLogic.sol                   | 0.00% (0/7)        | 0.00% (0/10)       | 0.00% (0/1)      | 0.00% (0/4)      |
| src/utils/Pause.sol                          | 100.00% (5/5)      | 100.00% (5/5)      | 100.00% (0/0)    | 100.00% (3/3)    |
| src/utils/Permission.sol                     | 100.00% (9/9)      | 100.00% (13/13)    | 100.00% (1/1)    | 100.00% (4/4)    |
| src/vendor/VaultReentrancyLib.sol            | 100.00% (2/2)      | 100.00% (3/3)      | 100.00% (0/0)    | 100.00% (1/1)    |
| Total                                       | 81.51% (1036/1271) | 80.13% (1339/1671) | 63.03% (179/284) | 73.99% (219/296) |

## Miscellaneous
Employees of LoopFi and employees' family members are ineligible to participate in this audit.

Code4rena's rules cannot be overridden by the contents of this README. In case of doubt, please check with C4 staff.