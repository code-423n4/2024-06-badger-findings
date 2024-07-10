## QA REPORT

|      | Issue                                                       |
| ---- | :---------------------------------------------------------- |
| [01] | `LiquidationReward` should be obtained instead of hardcoded |
| [02] | The protocol is missing referral rewards from Lido          |
| [03] | The error message in `_requireZeroOrMinAdjustment` is inaccurate |
| [04] | `LeverageMacroBase` should inherit `IERC3156FlashBorrower` |
| [05] | `owner()` function should be `view` and unimplemented |
| [06] | Potential revert caused by strict `>` |
| [07] | Fee-related params can not be changed |
| [08] | User can monitor on the price change of `wstETH` |
| [09] | No deadline check for the transactions |
| [10] | The owner of the `cdp` can not be changed |
| [11] | No Fees charged during CDP de-lever and closure|
| [12] | The input mechanism for `adjustCdp` and `openCdp` is inconsistent|
| [13] | User's action may revert unexpected due to conditions in `ActivePool` or `BorrowerOperations` |

## [01] `LiquidationReward` should be obtained instead of hardcoded value

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L19

### Description
In `ZapRouter`, the `LIQUIDATOR_REWARD` is hardcoded to `2e17`. This is designed to be consistent with the value assigned in `EBtcBase` which is later inherited by `BorrowerOperations`.

```solidity
uint256 public constant LIQUIDATOR_REWARD = 2e17;
```

However, it would be better to query `LIQUIDATOR_REWARD` from `BorrowerOperations` instead of hardcoding it. In this way, the value would always be consistent (This is exactly the way `MIN_CHANGE` has been dealt with).

Note: This is the same case with `MIN_NET_STETH_BALANCE`.

### Recommendation

Use the following pattern:
```solidity
uint256 public immutable LIQUIDATOR_REWARD;

construcotr(...) {
	...
	LIQUIDATOR_REWARD = INTERFACE(_borrowerOperations).LIQUIDATOR_REWARD;
	...
}
```

## [02] The protocol is missing referral rewards from Lido


### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L37-L38

### Description

Currently, the protocol interacts with the `Lido` via its fallback function.

```solidity
        // TODO call submit() with a referral?
        payable(address(stEth)).call{value: _initialETH}("");
```

This makes the protocol missing the referral rewards from Lido. More details [here](https://snapshot.org/#/lido-snapshot.eth/proposal/0x9279cd4addefdd9185d024f471f1a29561f61556ae209cdda5dffb1fd73b181e)


### Recommendation
It would be a good idea to use `submit` just as pointed out in the comment.

## [03] The error message in `_requireZeroOrMinAdjustment` is inaccurate

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L135-L140

### Description

In the function `_requireZeroOrMinAdjustment`, the returned error message is incorrect. As the `_change>= MIN_CHANGE` is interpreted as `above min`. This is somehow inaccurate and misleading.

```solidity
    function _requireZeroOrMinAdjustment(uint256 _change) internal view {
        require(
            _change == 0 || _change >= MIN_CHANGE,
            "ZapRouterBase: Debt or collateral change must be zero or above min"
        );
    }
```


### Recommendation
To mitigate this issue, change the `above min` to `gte min`.

## [04] `LeverageMacroBase` should inherit `IERC3156FlashBorrower`

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L26
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/05f218fb6617932e56bf5388c3b389c3028a7b73/contracts/interfaces/IERC3156FlashBorrower.sol#L10C11-L10C32

### Description

The contract `LeverageMacroBase` interacts with Flashloan Lender and implements the function `onFlashLoan`. 

```solidity
    function onFlashLoan(
        address initiator,
        address token,
        uint256 amount,
        uint256 fee,
        bytes calldata data
    ) external returns (bytes32) {
```

In fact, the contract should inherit `IERC3156FlashBorrower` to make its usage more clear.

### Recommendation
Change `abstract contract LeverageMacroBase` to `abstract contract LeverageMacroBase is  IERC3156FlashBorrower`.

## [05] `owner()` function should be `view` and unimplemented

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L39-L41

### Description

In the contract `LeverageMacroBase`, the function `owner` will intentionally revert to alarm that `the contract must be overridden`.

```solidity
    function owner() public virtual returns (address) {
        revert("Must be overridden");
    }
```

Firstly, the `owner` should be marked as `view` since no state changes are made. Also, the `abstract contract` could leave `owner` unimplemented which is apparently a better way to enforce future overridden.

### Recommendation
Change the function to `function owner() public view virtual returns (address);` 

## [06] Potential Revert caused by strict `>`

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L490-L494

### Description

In the contract `LeverageMacroBase`, the function `_doSwapChecks` will revert when `IERC20(swapChecks[i].tokenToCheck).balanceOf(address(this)) <= swapChecks[i].expectedMinOut`.

When `expectedMinOut` is reach, the function would still revert. This is not the common practice when dealing with sllipage.

```solidity
	require(
		IERC20(swapChecks[i].tokenToCheck).balanceOf(address(this)) >	swapChecks[i].expectedMinOut, "LeverageMacroReference: swap check failure!"
	);
```



### Recommendation
Change `>` to `>=` so that `expectedMinOut` could also pass the check.

## [07] Fee-related params can not be changed

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L23-L24

### Description

In the contract `LeverageZapRouterBase`, the fees are charged and fee-related params like `zapFeeBPS` and `zapFeeReceiver` are set.

```solidity
        zapFeeBPS = params.zapFeeBPS;
        zapFeeReceiver = params.zapFeeReceiver;
```

However, these params are declared as `immutable` and can't be changed in market conditions.  As a result:
- Fee can't be adjusted according to marketing conditions. 
- If the `FeeReceiver` is no longer in use, the address can't be adjusted.

```solidity
    uint256 public immutable zapFeeBPS;
    address public immutable zapFeeReceiver;
```



### Recommendation
Redefine the `zapFeeBPS` and `zapFeeReceiver` to make them adjustable.

## [08] User can monitor on the price change of `wstETH`

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L268

### Description

In the contract `EbtcLeverageZapRouter`, user could close his position for `wstETH` or `stETH`. 
```solidity
    function closeCdpForWstETH(
        bytes32 _cdpId,
        bytes calldata _positionManagerPermit,
        TradeData calldata _tradeData
    ) external {
        _closeCdp(_cdpId, _positionManagerPermit, true, _tradeData);
    }
```
The ratio between `wstETH-stETH` is changed when the `Lido` validators choose to reflect the rewards. 

As a result, the user could monitor the distribution of rewards, and front-run this transaction with to claim `wstETH` to gain from the reward distribution.

### Recommendation

Consider reviewing the design pattern.


## [09] No deadline check for the transactions

### Description

In the current contract, all user's operations have no deadline check. If the user's transaction is pending in the mempool for a few hours/days before get processed, this could lead to sub-optimal and unexpected situations.

Especially during the extreme marketing conditions, this could get user under a sub-optimal situation.

### Recommendation

Add a `dealine` param from the user and compare it with current `block.timestamp` to ensure the effectiveness of the transaction.

## [10] The owner of the `cdp` can not be changed

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L149-L152

### Description

In the `ZapRouterBase`, the owner of the `cdp` is returned from the `cdpId`.

```solidity
    function _getOwnerAddress(bytes32 cdpId) internal pure returns (address) {
        uint256 _tmp = uint256(cdpId) >> 96;
        return address(uint160(_tmp));
    }
```

This means that the `cdp` could not be changed. This might be inconvenient for users in several cases, as it always require the `cdp owner` to close the `cdp`.

### Recommendation

A suggestion is to look over the design to find if this aligns with the team's design.

## [11] No Fees charged during CDP de-lever and closure

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L276-L292
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L308-L310

### Description

In the `LeverageZapRouterBase`, not all callbacks are overriden to introduce fee-mechanism.

```solidity
    function _openCdpForCallback(bytes memory data) internal override {...}
    function _adjustCdpCallback(bytes memory data) internal override {...}
```

In fact, the `_closeCdpCallback` is not overriden, meaning no fees will be charged when an user tries to close his Cdp.

Alos, in `_adjustCdpCallback`,  fee is charged only when `flData._isDebtIncrease`, meaning no fees will be changerd when a cdp is de-levered.

```solidity
	if (flData._isDebtIncrease) {
	
	IERC20(address(ebtcToken)).safeTransfer(zapFeeReceiver, flData._EBTCChange * zapFeeBPS / BPS);

	}
```

Failing to charge during CDP de-lever and closure will lead to decrease of protocol revenue.

### Recommendation

A suggestion is to confirm with the team if this is intended design.

## [12] The input mechanism for `adjustCdp` and `openCdp` is inconsistent

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L34-L55
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L423-L431

### Description

In the `LeverageZapRouterBase`, the `openCdp` function and `adjustCdp` function are inconsistent with each other on the way how the deal with `stEthBalance` and `stEthMargin`.

For `openCdp`, the `_stEthMarginAmount` in `_openCdp` is not used, since the `_stEthMarginAmount` is already counted in `_stEthDepositAmount`.

```solidity
    function _openCdp(
        uint256 _debt,
        bytes32 _upperHint,
        bytes32 _lowerHint,
        uint256 _stEthLoanAmount,
 @=>    uint256 _stEthMarginAmount,
        uint256 _stEthDepositAmount,
        bytes calldata _positionManagerPermit,
        TradeData calldata _tradeData
    ) internal nonReentrant returns (bytes32 cdpId) {
        
        _requireZeroOrMinAdjustment(_debt);
        _requireAtLeastMinNetStEthBalance(_stEthDepositAmount - LIQUIDATOR_REWARD);

        // _positionManagerPermit is only required if called directly
        // for 3rd party integrations (i.e. DeFi saver, instadapp), setPositionManagerApproval
        // can be used before and after each operation
        if (_positionManagerPermit.length > 0) {
            PositionManagerPermit memory approval = abi.decode(_positionManagerPermit, (PositionManagerPermit));
            _permitPositionManagerApproval(borrowerOperations, approval);
        }

        // pre-compute cdpId for post checks
        cdpId = sortedCdps.toCdpId(msg.sender, block.number, sortedCdps.nextCdpNonce());

        OpenCdpForOperation memory cdp;

        cdp.eBTCToMint = _debt;
        cdp._upperHint = _upperHint;
        cdp._lowerHint = _lowerHint;
        cdp.stETHToDeposit = _stEthDepositAmount;
        cdp.borrower = msg.sender;

        _openCdpOperation({
            _cdpId: cdpId,
            _cdp: cdp,
            _flAmount: _stEthLoanAmount,
            _tradeData: _tradeData
        });

        if (_positionManagerPermit.length > 0) {
            borrowerOperations.renouncePositionManagerApproval(msg.sender);
        }
    }

```

However, this is not the case for `_adjustCdp`, where the `_params.stEthBalanceChange` does not cover `_params.stEthMarginBalance`. **At first glance, this is a double counted issue if `_params.stEthBalanceChange` already covers `_params.stEthMarginBalance` **, but later in private thread, the team suggests that **in adjustCdp the front end passes in the value without the margin balance**.

```solidity
        uint256 marginDecrease = _params.isStEthBalanceIncrease ? 0 : _params.stEthBalanceChange;
        if (!_params.isStEthMarginIncrease && _params.stEthMarginBalance > 0) {
            marginDecrease += _params.stEthMarginBalance;
        }

        uint256 marginIncrease = _params.isStEthBalanceIncrease ? _params.stEthBalanceChange : 0;
        if (_params.isStEthMarginIncrease && _params.stEthMarginBalance > 0) {
            marginIncrease += _params.stEthMarginBalance;
        }
```

We both agrees that it's a bit inconsistent compared to `openCdp`.

### Recommendation

To mitigate this issue:
1. Confirm if this is intended design or a double-counted issue.
2. Make the doc and comment more clear with concrete examples.

## [13] User's action may revert unexpected due to conditions in `ActivePool` or `BorrowerOperations` 

### Link
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L35
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L185-L198

### Description

User's actions involves borrowing flashloan from `BorrowerOperations` or `ActivePool`. However, their transaction may get reverted unexpectedly.

```solidity
    f        if (flType == FlashLoanType.eBTC) {
            IERC3156FlashLender(address(borrowerOperations)).flashLoan(
                IERC3156FlashBorrower(address(this)),
                address(ebtcToken),
                borrowAmount,
                abi.encode(operation)
            );
        } else if (flType == FlashLoanType.stETH) {
            IERC3156FlashLender(address(activePool)).flashLoan(
                IERC3156FlashBorrower(address(this)),
                address(stETH),
                borrowAmount,
                abi.encode(operation)
            );

```

If the `flashLoansPaused` is suddenly applied, the `maxFlashLoan` would return 0, and user would be unable to open/close/adjust their cdp during extreme marketing conditions.

This would bring uncertainty to users, if they can not close/adjust their cdp in extreme marketing conditions, they may be posed under liquidation line.


### Recommendation

To mitigate this issue, add a buffer time for the close/adjust when `flashLoansPaused` is applied and resumed.