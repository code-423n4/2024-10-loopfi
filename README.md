

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

| File         |
| ------------ |
| ./src/reward/ChefIncentivesController.sol |
| ./src/reward/EligibilityDataProvider.sol |
| ./src/reward/MultiFeeDistribution.sol |
| ./src/reward/RecoverERC20.sol |
| ./src/reward/interfaces/IBountyManager.sol |
| ./src/reward/interfaces/IChefIncentivesController.sol |
| ./src/reward/interfaces/IEligibilityDataProvider.sol |
| ./src/reward/interfaces/IFeeDistribution.sol |
| ./src/reward/interfaces/IMintableToken.sol |
| ./src/reward/interfaces/IMultiFeeDistribution.sol |
| ./src/reward/interfaces/IPriceProvider.sol |
| ./src/reward/interfaces/IWETH.sol |
| ./src/reward/interfaces/LockedBalance.sol |
| ./src/reward/interfaces/balancer/IWeightedPoolFactory.sol |
| ./src/reward/libraries/RecoverERC20.sol |
| ./src/test/MockOracle.sol |
| ./src/test/MockVoter.sol |
| ./src/test/PoolQuotaKeeperMock.sol |
| ./src/test/TestBase.sol |
| ./src/test/integration/IntegrationTestBase.sol |
| ./src/test/integration/PendleLPOracle.t.sol |
| ./src/test/integration/PoolAction.t.sol |
| ./src/test/integration/PoolAndSwapActionPendle.t.sol |
| ./src/test/integration/PoolV3.t.sol |
| ./src/test/integration/PositionAction20.lever.t.sol |
| ./src/test/integration/PositionAction20.t.sol |
| ./src/test/integration/PositionAction20Pendle.lever.t.sol |
| ./src/test/integration/PositionAction20Pendle.t.sol |
| ./src/test/integration/PositionAction4626.lever.t.sol |
| ./src/test/integration/RewardManagerPendle.t.sol |
| ./src/test/integration/SwapAction.t.sol |
| ./src/test/integration/Tokenomics.t.sol |
| ./src/test/integration/TransferAction.t.sol |
| ./src/test/unit/BalancerOracle.t.sol |
| ./src/test/unit/CDPVault.t.sol |
| ./src/test/unit/ChainlinkOracle.t.sol |
| ./src/test/unit/ChefIncentivesController.t.sol |
| ./src/test/unit/EligibilityDataProvider.t.sol |
| ./src/test/unit/Flashlender.t.sol |
| ./src/test/unit/Locking.t.sol |
| ./src/test/unit/Math.t.sol |
| ./src/test/unit/MultiFeeDistribution.t.sol |
| ./src/test/unit/Pause.t.sol |
| ./src/test/unit/Permission.t.sol |
| ./src/test/unit/Proxy.t.sol |
| ./src/test/unit/StakingLP.t.sol |
| ./src/test/unit/Treasury.t.sol |
| ./src/test/utils/ChainIds.sol |
| ./src/test/utils/PatchedDeal.sol |
| ./src/test/utils/PermitMaker.sol |
| ./src/vendor/AuraVault.sol |
| ./src/vendor/IAuraPool.sol |
| Totals: 52 |

