# QA Report    

## [L-1] The `openCdp` function reverts if `_stEthDepositAmount > _collVal + _stEthLoanAmount`

### Impact

Issue requires a user error in function parameters. If the user specifies the value` _stEthDepositAmount` > `_collVal + _stEthLoanAmount` in the incoming parameters of the function `openCdp` [Link to code](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L171-L203), the function will be revert. It will take some time for the user to understand why the revert occurred. 

This issue could impact the protocol's operation by causing transactions to fail unexpectedly, leading to user frustration and potential loss of funds if not handled correctly. Users might repeatedly attempt to execute the transaction without understanding the root cause, wasting resources (gas) and potentially locking themselves out of accessing certain features or services within the protocol until the issue is resolved.

### Proof Of Concept

Let's change input test params in 138 line [Link to code](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/test/LeverageZaps.t.sol#L138) of test file `LeverageZaps.t.sol`.

```diff

FILE: ebtc-zap-router/test/LeverageZaps.t.sol

- 138:   (_flAmount + _marginAmount) * COLLATERAL_BUFFER / SLIPPAGE_PRECISION,
+ 138:   _flAmount + _marginAmount + 1,

```

So, test `test_ZapOpenCdp_WithStEth_LowLeverage()` will be broke:

``` javascript
$ forge test --mt test_ZapOpenCdp_WithStEth_LowLeverage
[⠆] Compiling...
No files changed, compilation skipped

Ran 1 test for test/LeverageZaps.t.sol:LeverageZaps
[FAIL. Reason: revert: ERC20: transfer amount exceeds balance] test_ZapOpenCdp_WithStEth_LowLeverage() (gas: 1100868)
Suite result: FAILED. 0 passed; 1 failed; 0 skipped; finished in 26.62ms (3.44ms CPU time)

Ran 1 test suite in 503.78ms (26.62ms CPU time): 0 tests passed, 1 failed, 0 skipped (1 total tests)

Failing tests:
Encountered 1 failing test in test/LeverageZaps.t.sol:LeverageZaps
[FAIL. Reason: revert: ERC20: transfer amount exceeds balance] test_ZapOpenCdp_WithStEth_LowLeverage() (gas: 1100868)

Encountered a total of 1 failing tests, 0 tests succeeded

```

### Recommendation

Add a check to line 182 [Link to code](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L182)

```diff

FILE: ebtc-zap-router/src/EbtcLeverageZapRouter.sol

181:    uint256 _collVal = _transferInitialStETHFromCaller(_stEthMarginAmount);

+ 182:   require(_stEthDepositAmount <= _collVal + _stEthLoanAmount, "EbtcLeverageZapRouter:_stEthDepositAmount must be less or equal then _collVal + _stEthLoanAmount");

183:    cdpId = _openCdp(
        _debt,
        _upperHint,
        _lowerHint,
        _stEthLoanAmount,
        _collVal,
        _stEthDepositAmount,
        _positionManagerPermit,
        _tradeData
    );

```

### Addition

Similar to the `openCdp` function, it is recommended to add the same check to the `openCdpWithWstEth`, `openCdpWithWrappedEth` and `openCdpWithEth` functions.

## [L-2] If `_debt` is equal to zero, functions `openCdp` and `adjustCdp` will be revert. Setting `_debt` to 0 could cause these functions to fail or behave unexpectedly.

### Impact

Issue in the use of the check function `_requireZeroOrMinAdjustment(_debt)` in lines [216](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L216) and [411](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L411)

If `_debt` is equal to 0, the function will revert later. 


### Proof Of Concept

Let's set `_debt` = 0 in the test in line [80](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/test/LeverageZaps.t.sol#L80). Then tests will fail.

```javascript

Failing tests:
Encountered 9 failing tests in test/LeverageZaps.t.sol:LeverageZaps
[FAIL. Reason: revert: ActivePool: 0 Amount] test_ZapCloseCdpWithDonation_WithStEth_LowLeverage() (gas: 834561)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_ZapCloseCdp_WithStEth_LowLeverage() (gas: 834585)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_ZapCloseCdp_WithWstEth_LowLeverage() (gas: 834605)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_ZapOpenCdp_WithEth_LowLeverage() (gas: 772152)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_ZapOpenCdp_WithStEth_LowLeverage() (gas: 845953)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_ZapOpenCdp_WithWrappedEth_LowLeverage() (gas: 865007)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_ZapOpenCdp_WithWstEth_LowLeverage() (gas: 995949)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_adjustCdp_debtDecrease_stEth() (gas: 834584)
[FAIL. Reason: revert: ActivePool: 0 Amount] test_adjustCdp_debtIncrease_stEth() (gas: 834650)

```

Let's set `_debt` = 0 in the test in line [475](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/test/LeverageZaps.t.sol#L475). Then test `test_adjustCdp_debtIncrease_stEth` will fail.

```javascript
[FAIL. Reason: revert: ERC20: transfer amount exceeds balance] test_adjustCdp_debtIncrease_stEth() (gas: 1544442)
Suite result: FAILED. 8 passed; 1 failed; 0 skipped; finished in 182.47ms (158.96ms CPU time)

```

### Recommendation

Leave the check for the minimum value and exclude equality 0 from the checker.


## [L-3] Incorrect calculation of `PostCheckParams` when opening Cdp


### Impact

At line [188](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L188) we calculate the value of the collateral in shares that we will contribute to the opening of Cdp. But not the whole amount of `_cdp.stETHToDeposit` will be used to open a Cdp position, 0.2 stETH is the gas stipend. This amount is separate from the collateral and is not included in the Individual Collateral Ratio (ICR) calculation.

So, incorrect calculations can lead to incorrect contract functionality.

### Proof of Concept

We can look at the [line 456](https://github.com/ebtc-protocol/ebtc/blob/2da05f2d1354190eacf64ec256d2c5c5358e5fa2/packages/contracts/contracts/BorrowerOperations.sol#L456) in the contract `BorrowerOperations`. That's where the function [`_calcNetStEthBalance`](https://github.com/ebtc-protocol/ebtc/blob/bfbe735865989df23d840e5df8c57cca88336a37/packages/contracts/contracts/Dependencies/EbtcBase.sol#L62-L64) is called. 

```javascript

FILE: ebtc/packages/contracts/contracts/BorrowerOperations.sol

456:    vars.netStEthBalance = _calcNetStEthBalance(_stEthBalance);

FILE: ebtc/packages/contracts/contracts/Dependencies/EbtcBase.sol

62:     function _calcNetStEthBalance(uint256 _stEthBalance) internal pure returns (uint256) {
63:         return _stEthBalance - LIQUIDATOR_REWARD;
64:     }

```

The following transformation of our variable [there](https://github.com/ebtc-protocol/ebtc/blob/bfbe735865989df23d840e5df8c57cca88336a37/packages/contracts/contracts/BorrowerOperations.sol#L469). 
And after all the checks, we initialize the pool using this variable: [Line of code](https://github.com/ebtc-protocol/ebtc/blob/bfbe735865989df23d840e5df8c57cca88336a37/packages/contracts/contracts/BorrowerOperations.sol#L524)

```javascript

FILE: ebtc/packages/contracts/contracts/BorrowerOperations.sol

469:    uint256 _netCollAsShares = collateral.getSharesByPooledEth(vars.netStEthBalance);
...
524:    cdpManager.initializeCdp(
            _cdpId,
            vars.debt,
            _netCollAsShares,
            _liquidatorRewardShares,
            _borrower
        );

```

### Recommendation

Calculate the [check parameter](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L188) considering the gas stipend.

```diff

FILE: ebtc-zap-router/src/LeverageZapRouterBase.sol

- 188:  stETH.getSharesByPooledEth(_cdp.stETHToDeposit),
+ 188:  stETH.getSharesByPooledEth(_cdp.stETHToDeposit - LIQUIDATOR_REWARD),

```

## [L-4] Value of stETH increase calculate differently when opening a position and when adjust increasing a position

### Impact

When calculating the amount of deposited stETH upon increasing the position, we do not subtract 30 BPS from the marginAmount. [Link to code](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L430C13-L430C58)
This approach creates calculation inconsistencies between `openCdp` and `ajustIncreaseCdp`.


### Proof of Concept

When we open a cdp position the amount of stETH deposit is calculated [as follows](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/test/LeverageZaps.t.sol#L138)

```javascript
FILE: test/LeverageZaps.t.sol

138: ((_flAmount + _marginAmount) * COLLATERAL_BUFFER) / SLIPPAGE_PRECISION,

```

When we adjust increase a cdp position the amount of stETH deposit is calculated [as follows](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L430C13-L430C58) and [follows](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/test/LeverageZaps.t.sol#L477)

```javascript
FILE: test/LeverageZaps.t.sol

477:    uint256 collValue = _debtToCollateral(debtChange) * COLLATERAL_BUFFER / 10000;

FILE: src/EbtcLeverageZapRouter.sol

430:    marginIncrease += _params.stEthMarginBalance;

```

### Recommendation

It is recommended that the calculations be made consistent.

For example, in [Linc to code](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L430C13-L430C58)

```diff

FILE: src/EbtcLeverageZapRouter.sol

- 430:    marginIncrease += _params.stEthMarginBalance;
+ 430:    marginIncrease += _params.stEthMarginBalance * 9970 / 10000;

```

## [L-5] Missing check when decrease Cdp

### Impact

Function [`_adjustCdp`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L403-L468) has no check for the minimum acceptable value after collateral decrease. If the total collateral is less than `MIN_NET_STETH_BALANCE` the transaction will be revert.


### Proof of Concept

[Link to code](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L403-L468)

Let's change the input parameters in the test [`test_adjustCdp_debtDecrease_stEth`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/test/LeverageZaps.t.sol#L507-L534).

```diff

FILE: test/LeverageZaps.t.sol

- 514:    uint256 debtChange = 0.8e18;
- 515:   uint256 marginBalance = 0.5e18;

+ 514:   uint256 debtChange = 0.9e18;
+ 515:   uint256 marginBalance = 4.5e18;

```

At such input parameters the function `adjustCdp` reverts in contract `BorrowerOperations`

```javascript
Failing tests:
Encountered 1 failing test in test/LeverageZaps.t.sol:LeverageZaps
[FAIL. Reason: revert: BorrowerOperations: Cdp's net stEth balance must not fall below minimum] test_adjustCdp_debtDecrease_stEth() (gas: 1394022)

Encountered a total of 1 failing tests, 0 tests succeeded

```

If the check is done earlier, as in the [`openCdp` function](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L217), the user will lose less gas amount.


### Recommendation

Add check to [line](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L427)

```diff
    uint256 marginDecrease = _params.isStEthBalanceIncrease ? 0 : _params.stEthBalanceChange;
    if (!_params.isStEthMarginIncrease && _params.stEthMarginBalance > 0) {
        marginDecrease += _params.stEthMarginBalance;
    }

+   _requireAtLeastMinNetStEthBalance(collateral.getPooledEthByShares(coll) - marginDecrease);   

```

