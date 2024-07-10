# Quality Assurance for eBTC Zap Router

| Issue ID | Description |
| -------- | ----------- |
| [QA-01](#qa-01-zaprouterbase-contract-wont-be-eligible-for-referral-rewards-from-lido-for-depositing-eth) | ZapRouterBase contract won't be eligible for referral rewards from Lido for depositing ETH |
| [QA-02](#qa-02-false-comparison-logic-in-_docheckvaluetype-leading-to-incorrect-post-operation-checks) | False comparison logic in `_doCheckValueType` leading to incorrect post-operation checks |
| [QA-03](#qa-03-missing-flash-loan-return-value-check-in-dooperation-function) | Missing Flash Loan Return Value Check in `doOperation` Function |
| [QA-04](#qa-04-reentrancy-could-occur-in-flash-loan-callback-functions) | Reentrancy could occur in flash loan callback functions |
| [QA-05](#qa-05-fee-calculation-in-leveragezaprouterbase-could-lead-to-excessive-fees-being-charged-and-inaccurate-cdp-debt) | Fee calculation in `LeverageZapRouterBase` could lead to excessive fees being charged and inaccurate CDP debt |
| [QA-06](#qa-06-lack-of-slippage-protection-in-swap-operations-exposes-users-to-excessive-slippage) | Lack of Slippage Protection in Swap Operations Exposes Users to Excessive Slippage |
| [QA-07](#qa-07-potential-overflow-in-_getpostcheckparams-function) | Potential Overflow in `_getPostCheckParams` Function |
| [QA-08](#qa-08-potential-rebasing-token-transfer-inconsistency-in-zaprouterbase) | Potential Rebasing Token Transfer Inconsistency in ZapRouterBase |
| [QA-09](#qa-09-an-invariant-is-broken-which-relates-to-position-manager-permit-not-being-revoked-after-operations-allowing-unauthorized-access-to-user-cdps) | An invariant is broken which relates to Position Manager Permit not being revoked after operations, allowing unauthorized access to user CDPs |
| [QA-10](#qa-10-incorrect-natspec-comment-in-adjustcdpwithwsteth-function-leading-to-potential-misunderstanding) | Incorrect NatSpec Comment in `adjustCdpWithWstEth` Function Leading to Potential Misunderstanding |
| [QA-11](#qa-11-fix-typos) | Fix typos |
| [QA-12](#qa-12-incorrect-error-message-in-_docheckvaluetype-for-lte-operator) | Incorrect error message in `_doCheckValueType` for `lte` operator |
| [QA-13](#qa-13-potential-eth-lock-up-and-abuse-in-receive-function) | Potential ETH Lock-up and Abuse in `receive()` Function |




## [QA-01] ZapRouterBase contract won't be eligible for referral rewards from Lido for depositing ETH


>We communicated with the sponsors to confirm if the protocol plans to accrue referral rewards from LIDO but they replied "not at the moment" and also went ahead to state that any issues that could affect future implementation(although no immediate plan) could be reported in QA.

### Impact
The ZapRouterBase contract won't be eligible for referral rewards from Lido when depositing ETH, potentially leading to a significant loss of value for the protocol.
Lido has a reward program. read more about the Lido's reward programs using this links below:
- https://docs.lido.fi/multisigs/committees#22-rewards-share-committee-prev-referral-program-committee
- https://research.lido.fi/t/tiered-rewards-share-program-a-sustainable-approach-to-steth-growth/4851


### Proof of Concept
In the `_depositRawEthIntoLido` function of the ZapRouterBase contract, the contract directly calls the `submit` function on the Lido stETH contract without passing any referral address:

https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L34-L41

```solidity
function _depositRawEthIntoLido(uint256 _initialETH) internal returns (uint256) {
    // check before-after balances for 1-wei corner case
    uint256 _balBefore = stEth.balanceOf(address(this));
    // TODO call submit() with a referral?
    payable(address(stEth)).call{value: _initialETH}("");
    uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
    return _deposit;
}
```

The Lido stETH contract allows the caller to pass a referral address when depositing ETH using the `submit` function. If a valid referral address is provided, the referral account can be eligible for referral rewards. However, in the current implementation, the ZapRouterBase contract is not passing any referral address, effectively making the referral the same as the caller contract itself. This is invalid according to Lido's referral program, and no rewards will be granted to the contract for depositing ETH.

### Recommended Mitigation Steps
The contract should pass a valid referral address (e.g., the owner's address) when calling the `submit` function:

```diff
function _depositRawEthIntoLido(uint256 _initialETH) internal returns (uint256) {
    // check before-after balances for 1-wei corner case
    uint256 _balBefore = stEth.balanceOf(address(this));
-   // TODO call submit() with a referral?
-   payable(address(stEth)).call{value: _initialETH}("");
+   IstETH(address(stEth)).submit{value: _initialETH}(owner());
    uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
    return _deposit;
}
```










## [QA-02] False comparison logic in `_doCheckValueType` leading to incorrect post-operation checks

### Impact
The incorrect comparison logic in the `_doCheckValueType` function can lead to incorrect validation of post-operation checks. This will result in the ptotocol not properly verifying the expected conditions, thereby, allowing operations to proceed when they should not, or failing valid operations. 

### Proof of Concept
The `_doCheckValueType` function is intended to check if a value meets certain conditions based on the operator provided. However, there is a mistake in the comparison logic for the `gte` and `lte` operators.

Look at this part of the code: https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L277-L290

```solidity
function _doCheckValueType(CheckValueAndType memory check, uint256 valueToCheck) internal {
    if (check.operator == Operator.skip) {
        // Early return
        return;
    } else if (check.operator == Operator.gte) {
        require(check.value >= valueToCheck, "!LeverageMacroReference: gte post check");
    } else if (check.operator == Operator.lte) {
        require(check.value <= valueToCheck, "!LeverageMacroReference: let post check");
    } else if (check.operator == Operator.equal) {
        require(check.value == valueToCheck, "!LeverageMacroReference: equal post check");
    } else {
        revert("Operator not found");
    }
}
```

The problem here is with the `gte` and `lte` checks. The `gte` (greater than or equal to) check should ensure that `valueToCheck` is greater than or equal to `check.value`, and the `lte` (less than or equal to) check should ensure that `valueToCheck` is less than or equal to `check.value`. However, the current implementation does the opposite.

### Recommended Mitigation Steps
The comparison logic should be corrected to ensure that the `gte` and `lte` checks are performed correctly:

```diff
function _doCheckValueType(CheckValueAndType memory check, uint256 valueToCheck) internal {
    if (check.operator == Operator.skip) {
        // Early return
        return;
    } else if (check.operator == Operator.gte) {
-        require(check.value >= valueToCheck, "!LeverageMacroReference: gte post check");
+        require(valueToCheck >= check.value, "!LeverageMacroReference: gte post check");
    } else if (check.operator == Operator.lte) {
-        require(check.value <= valueToCheck, "!LeverageMacroReference: let post check");
+        require(valueToCheck <= check.value, "!LeverageMacroReference: lte post check");
    } else if (check.operator == Operator.equal) {
        require(valueToCheck == check.value, "!LeverageMacroReference: equal post check");
    } else {
        revert("Operator not found");
    }
}
```






## [QA-03] Missing Flash Loan Return Value Check in `doOperation` Function

## Impact
The `doOperation` function does not check the return value of the `flashLoan` function, which can cause undetected failures in the flash loan process. 

## Proof of Concept
According to `IERC3156FlashLender.sol`_(Out of scope for this contest)_, the `flashLoan` function contains a return value that the caller should check. However, the `_doOperation` function initiates the flash loan but does not check the return value:

https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L185-L202

```solidity
if (flType == FlashLoanType.eBTC) {
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
} else {
    // No leverage, just do the operation
    _handleOperation(operation);
}
```

## Recommended Mitigation Steps
Modify the `_doOperation` function to check the return value of the `flashLoan` function and ensure it matches `FLASH_LOAN_SUCCESS`.













## [QA-04] Reentrancy could occur in flash loan callback functions

### Impact
The lack of reentrancy protection in the flash loan callback functions (`_openCdpCallback`, `_adjustCdpCallback`, `_closeCdpCallback`) can allow an attacker to reenter the contract during execution, potentially leading to multiple CDP openings, adjustments, closures or other malicious acts.

### Proof of Concept
Look at these part of the `LeverageMacroBase.sol` contract: _openCdpCallback

```solidity
// LeverageMacroBase.sol
function _openCdpCallback(bytes memory data) internal virtual {
    OpenCdpOperation memory flData = abi.decode(data, (OpenCdpOperation));
    bytes32 _cdpId = borrowerOperations.openCdp(
        flData.eBTCToMint,
        flData._upperHint,
        flData._lowerHint,
        flData.stETHToDeposit
    );
}

function _adjustCdpCallback(bytes memory data) internal virtual {
    AdjustCdpOperation memory flData = abi.decode(data, (AdjustCdpOperation));
    borrowerOperations.adjustCdpWithColl(
        flData._cdpId,
        flData._stEthBalanceDecrease,
        flData._EBTCChange,
        flData._isDebtIncrease,
        flData._upperHint,
        flData._lowerHint,
        flData._stEthBalanceIncrease
    );
}

function _closeCdpCallback(bytes memory data) internal virtual {
    CloseCdpOperation memory flData = abi.decode(data, (CloseCdpOperation));
    borrowerOperations.closeCdp(flData._cdpId);
}
```

The `onFlashLoan` function in `LeverageMacroBase.sol` handles the flash loan callback and calls `_handleOperation` to perform the necessary operations. The `_handleOperation` function then calls the appropriate callback function (`_openCdpCallback`, `_adjustCdpCallback`, or `_closeCdpCallback`) based on the operation type.
However, the callback functions (`_openCdpCallback`, `_adjustCdpCallback`, `_closeCdpCallback`) do not have explicit reentrancy protection mechanisms such as the `nonReentrant` modifier. This could potentially allow an attacker to reenter the contract during the execution of these functions and manipulate the state in an unintended way.

### Recommended Mitigation Steps
Add the `nonReentrant` modifier to the `_openCdpCallback`, `_adjustCdpCallback`, and `_closeCdpCallback` functions to prevent reentrancy.





## [QA-05] Fee calculation in `LeverageZapRouterBase` could lead to excessive fees being charged and inaccurate CDP debt

### Impact

This can result in users paying higher fees than intended and having their CDPs reflect a higher debt than the actual eBTC received. 

### Proof of Concept

The problem is present in both the `_openCdpForCallback` and `_adjustCdpCallback` functions. Lookking at the `_openCdpForCallback` function: https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L276-L292

```solidity
function _openCdpForCallback(bytes memory data) internal override {
    if (zapFeeBPS > 0) {
        OpenCdpForOperation memory flData = abi.decode(data, (OpenCdpForOperation));

        bytes32 _cdpId = borrowerOperations.openCdpFor(
            flData.eBTCToMint,
            flData._upperHint,
            flData._lowerHint,
            flData.stETHToDeposit,
            flData.borrower
        );

        IERC20(address(ebtcToken)).safeTransfer(zapFeeReceiver, flData.eBTCToMint * zapFeeBPS / BPS);
    } else {
        super._openCdpForCallback(data);
    }
}
```

CDP is opened for the full `flData.eBTCToMint` amount, but then a fee is deducted from this amount and sent to the `zapFeeReceiver`. This means:

- The user's CDP reflects a debt of `flData.eBTCToMint`.
-  The user actually receives `flData.eBTCToMint - (flData.eBTCToMint * zapFeeBPS / BPS)`.

For exxample, if a user wants to mint 100 eBTC and the `zapFeeBPS` is 50 (0.5%):
1. The CDP is opened with a debt of 100 eBTC.
2. 0.5 eBTC is sent to the `zapFeeReceiver`.
3. The user only receives 99.5 eBTC.

This could be costly in scenarios where the user is close to the liquidation threshold. They might believe they have more buffer than they actually do, potentially leading to unexpected liquidations as the case may be.

### Recommended Mitigation Steps

Recalculate the eBTC amount to mint or adjust before performing the CDP operation, subtracting the fee amount first. Then, open or adjust the CDP with this reduced amount, ensuring the CDP's debt accurately reflects the eBTC the user receives. Finally, transfer the calculated fee to the `zapFeeReceiver`.





## [QA-06] Lack of Slippage Protection in Swap Operations Exposes Users to Excessive Slippage

### Impact

The current implementation of swap operations in the `LeverageZapRouterBase` contract lacks proper slippage protection, potentially exposing users to significant financial losses due to unfavorable trade execution.

### Proof of Concept

Look at the `_getSwapOperations` function: https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L232-L247

```solidity
function _getSwapOperations(
    address _tokenIn,
    address _tokenOut,
    TradeData calldata _tradeData
) internal view returns (SwapOperation[] memory swaps) {
    swaps = new SwapOperation[](1);

    swaps[0].tokenForSwap = _tokenIn;
    swaps[0].addressForApprove = DEX;
    swaps[0].exactApproveAmount = _tradeData.approvalAmount;
    swaps[0].addressForSwap = DEX;
    swaps[0].calldataForSwap = _tradeData.exchangeData;
    if (_tradeData.performSwapChecks) {
        swaps[0].swapChecks = _getSwapChecks(_tokenOut, _tradeData.expectedMinOut);            
    }
}
```

The function creates a `SwapOperation`, but the slippage protection (via `swapChecks`) is only applied if `_tradeData.performSwapChecks` is true. This boolean flag allows the possibility of swaps to be executed without any slippage protection.

Even when slippage checks are performed, they rely entirely on the `_tradeData.expectedMinOut` value provided by the caller, without any on-chain validation of this value's reasonableness.

1. A user initiates a large swap operation.
2. The transaction is pending in the mempool.
3. The market moves unfavorably.
4. A MEV bot or malicious miner could execute the transaction at a much worse rate than the user expected.
5. If `performSwapChecks` is false or if `expectedMinOut` is set too low, the user could suffer significant losses due to excessive slippage.

### Recommended Mitigation Steps

Always enforce slippage protection for swap operations by removing the `performSwapChecks` condition. Implement on-chain logic to calculate a reasonable `minOut` value based on current market conditions and a configurable maximum allowed slippage percentage. Consider using a price oracle to validate that the executed swap price is within acceptable bounds."






## [QA-07] Potential Overflow in `_getPostCheckParams` Function 

### Impact
If `_totalCollateral` and `_collValidationBuffer` are large enough, their multiplication could overflow before the division by `BPS`, resulting in an incorrect `expectedCollateral` value. This could cause the function to return invalid post-check parameters.

### Proof of Concept
Look at the [`_getPostCheckParams`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L257-L274) function:

```solidity
expectedCollateral: CheckValueAndType({
    value:  _totalCollateral * _collValidationBuffer / BPS,
    operator: Operator.gte
}),
```

Looking at the order of operations, if `_totalCollateral` and `_collValidationBuffer` are large enough, their multiplication could exceed the maximum value representable by `uint256`, causing an overflow. This overflow would occur before the division by `BPS`, leading to an incorrect `expectedCollateral` value.

### Recommended Mitigation Steps
To prevent the potential overflow, divide `_collValidationBuffer` by `BPS` before multiplying it with `_totalCollateral`.








## [QA-08] Potential Rebasing Token Transfer Inconsistency in ZapRouterBase

### Proof of concept
In the `ZapRouterBase.sol`, the `_transferStEthToCaller` function uses two different methods for transferring stETH depending on whether it's wrapped or not:

1. For wrapped stETH (wstETH), it uses the wrap and transfer functions:
```solidity
uint256 _wstETHVal = IWstETH(address(wstEth)).wrap(_stEthVal);
wstEth.transfer(msg.sender, _wstETHVal);
```

2. For unwrapped stETH, it uses a direct transfer:
```solidity
stEth.transfer(msg.sender, _stEthVal);
```

While this implementation isn't necessarily incorrect, it doesn't consistently use the share-based transfer method for `stETH`, which could potentially lead to slight discrepancies if a rebasing event occurs between the calculation of `_stEthVal` and the actual transfer.

### Recommendation
To ensure consistent and rebasing-resistant behavior, modify the `_transferStEthToCaller` function to use `transferShares` for unwrapped `stETH` transfers. 







## [QA-09] An invariant is broken which relates to Position Manager Permit not being revoked after operations, allowing unauthorized access to user CDPs

### Impact

According to the [ReadMe](https://github.com/code-423n4/2024-06-badger#main-invariants), 

```
Main invariants:
Position manager permit needs to be revoked after each operation
```

The failure to revoke the position manager permit after each operation leaves users' CDPs vulnerable to unauthorized access. This breaks the main security invariant and could potentially allow the `ZapRouter` contract or malicious actors to manipulate users' positions without their consent in subsequent transactions.

### Proof of Concept
In the ZapRouterBase.sol contract, there's a function to grant the position manager permit: https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L115-L133

```solidity
function _permitPositionManagerApproval(
    IBorrowerOperations borrowerOperations,
    PositionManagerPermit memory _positionManagerPermit
) internal {
    try
        borrowerOperations.permitPositionManagerApproval(
            msg.sender,
            address(this),
            IPositionManagers.PositionManagerApproval.OneTime,
            _positionManagerPermit.deadline,
            _positionManagerPermit.v,
            _positionManagerPermit.r,
            _positionManagerPermit.s
        )
    {} catch {
        /// @notice adding try...catch around to mitigate potential permit front-running
        /// see: https://www.trust-security.xyz/post/permission-denied
    }
}
```

However, there is no corresponding function or mechanism to revoke this permit after the operation is completed. This means that once granted, the `ZapRouter` contract still retains the ability to manage the user's CDP beyond the intended single operation.

In `LeverageZapRouterBase.sol`, operations like [`_adjustCdpOperation`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L132-L163), [`_openCdpOperation`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L165-L198), and [`_closeCdpOperation`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L200-L230) use these permissions but do not revoke them afterwards:

```solidity
function _adjustCdpOperation(
    bytes32 _cdpId,
    FlashLoanType _flType,
    uint256 _flAmount,
    AdjustCdpOperation memory _cdp,
    uint256 debt,
    uint256 coll,
    TradeData calldata _tradeData
) internal {
    // ... operation logic ...

    _sweepEbtc();
    // sweepStEth happens outside of this call
    // No revocation of position manager permit
}
```

As we observe, this pattern is consistent across all CDP operations, leaving the permit active after each transaction.

### Recommended Mitigation Steps
Implement a function to revoke the position manager permit immediately after each CDP operation is completed, and ensure it's called at the end of every relevant function.








## [QA-10] Incorrect NatSpec Comment in `adjustCdpWithWstEth` Function Leading to Potential Misunderstanding

### Proof of Concept
The NatSpec comment for the `adjustCdpWithWstEth` function incorrectly mentions "wrapped Ether" instead of "wrapped staked Ether". This could cause confusion for auditors and developers.

https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L349
```solidity
/// @notice Function that allows various operations which might change both collateral (increase collateral with wrapped Ether) and debt of a Cdp

```

The function name `adjustCdpWithWstEth` and the use of `_convertWstEthToStETH` clearly indicate that the function deals with wrapped staked Ether (WstETH).
The current NatSpec comment incorrectly states that the function deals with "wrapped Ether".

### Recommended Mitigation Steps
Update the NatSpec comment to accurately reflect the function's implementation. The corrected comment should be:

```solidity
/// @notice Function that allows various operations which might change both collateral (increase collateral with wrapped staked Ether) and debt of a Cdp
```





## [QA-11] Fix typos

### Proof of Concept
The word "received" is misspelled as "reiceived" in multiple instances:
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L43-L51

```solidity
function _convertWrappedEthToStETH(uint256 _initialWETH) internal returns (uint256) {
    ...
    uint256 _wETHReiceived = wrappedEth.balanceOf(address(this)) - _wETHBalBefore;
    ...
    IWrappedETH(address(wrappedEth)).withdraw(_wETHReiceived);
    ...
}
```

https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L59-L70

```solidity
function _convertWstEthToStETH(uint256 _initialWstETH) internal returns (uint256) {
    ...
    uint256 _stETHReiceived = stEth.balanceOf(address(this)) - _stETHBalBefore;
    ...
    return _stETHReiceived;
}
```

### Recommended Mitigation Steps
Correct the spelling of "reiceived" to "received" in the function `_convertWrappedEthToStETH` and `_convertWstEthToStETH` as shown below:

```diff
function _convertWrappedEthToStETH(uint256 _initialWETH) internal returns (uint256) {
    ...
-    uint256 _wETHReiceived = wrappedEth.balanceOf(address(this)) - _wETHBalBefore;
+    uint256 _wETHReceived = wrappedEth.balanceOf(address(this)) - _wETHBalBefore;
    ...
-    IWrappedETH(address(wrappedEth)).withdraw(_wETHReiceived);
+    IWrappedETH(address(wrappedEth)).withdraw(_wETHReceived);
    ...
}
```

```diff
function _convertWstEthToStETH(uint256 _initialWstETH) internal returns (uint256) {
    ...
-    uint256 _stETHReiceived = stEth.balanceOf(address(this)) - _stETHBalBefore;
+    uint256 _stETHReceived = stEth.balanceOf(address(this)) - _stETHBalBefore;
    ...
-    return _stETHReiceived;
+    return _stETHReceived;
}
```




## [QA-12] Incorrect error message in `_doCheckValueType` for `lte` operator

### Impact
The incorrect error message in the `_doCheckValueType` function for the `lte` operator can lead to confusion when debugging or handling errors. The message currently reads `"let post check"` instead of `"lte post check"`.

### Proof of Concept
The `_doCheckValueType` function is intended to check if a value meets certain conditions based on the operator provided. However, there is a typo in the error message for the `lte` operator.

Look at this part of the code: https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L284

```solidity
        require(check.value <= valueToCheck, "!LeverageMacroReference: let post check");
```

The problem here is with the error message for the `lte` check. The message currently reads `"let post check"` instead of `"lte post check"`.

### Recommended Mitigation Steps
```diff
-        require(valueToCheck <= check.value, "!LeverageMacroReference: let post check");
+        require(valueToCheck <= check.value, "!LeverageMacroReference: lte post check");
```




## [QA-13] Potential ETH Lock-up and Abuse in `receive()` Function

### Impact
The `receive()` function in the contract allows the contract to receive ETH only from the `wrappedEth` address. However, there is no mechanism to handle or withdraw this ETH, which could lead to ETH being permanently locked in the contract. Additionally, if the `wrappedEth` address is compromised or manipulated, an attacker could send ETH to the contract, resulting in stuck funds.

### Proof of Concept
https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L112-L117

```solidity
/// @dev This is to allow wrapped ETH related Zap
receive() external payable {
    require(
        msg.sender == address(wrappedEth),
        "EbtcLeverageZapRouter: only allow Wrapped ETH to send Ether!"
    );
}
```

 If ETH is sent to this contract from the `wrappedEth` address, it could become permanently locked in the contract, as there is no function to withdraw or use this ETH. Also iff the `wrappedEth` address is compromised or can be manipulated, an attacker could send ETH to this contract, which would then be stuck.

### Recommended Mitigation Steps
Implement a mechanism to handle and properly account for received ETH, such as adding a function to withdraw ETH. 