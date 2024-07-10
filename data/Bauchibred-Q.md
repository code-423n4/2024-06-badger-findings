# QA Report for **Badger**

## Table of Contents

| Issue ID                                                                                                           | Description                                                                                        |
| ------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| [QA-01](#qa-01-consider-using-transfershares-directly-for-steth-transfers-for-better-efficiency-)                  | Consider using `transferShares` directly for stETH transfers for better efficiency                 |
| [QA-02](#qa-02-import-declarations-should-import-specific-identifiers-rather-than-the-whole-file-)                 | Import declarations should import specific identifiers, rather than the whole file                 |
| [QA-03](<#qa-03-ensure-documentation-is-sufficiently-covered-for-zaprouterbase#_transferinitialstethfromcaller()>) | Ensure documentation is sufficiently covered for `ZapRouterBase#_transferInitialStETHFromCaller()` |
| [QA-04](#qa-04-leveragemacrobase.dooperation-should-check-flashloan-return-value-)                                 | `LeverageMacroBase.doOperation` should check flashLoan return value                                |
| [QA-05](#qa-05-whitelisted-tokens-should-not-be-sweepable-)                                                        | Whitelisted tokens should not be sweepable                                                         |
| [QA-06](#qa-06-ebtcleveragezaprouteropencdpwitheth-erroneuosly-claims-that-a-higher-margin-equals-lower-cr)        | `EbtcLeverageZapRouter#openCdpWithEth()` erroneuosly claims that a _higher margin equals lower CR_ |
| [QA-07](#qa-07-open-todos-should-be-sorted-before-live-deployment-)                                                | Open todos should be sorted before live deployment                                                 |
| [QA-08](<#qa-08-leveragemacrobase#_doswap()-might-not-work-for-some-tokens-like-bnb->)                             | `LeverageMacroBase#_doSwap()` might not work for some tokens like `BNB`                            |
| [QA-09](<#qa-09-_opencdpcallback()-does-not-emit-an-event>)                                                        | `_openCdpCallback()` does not emit an event                                                        |
| [QA-10](#qa-10-consider-attaching-error-messages-to-_require_-checks-)                                             | Consider attaching error messages to _require_ checks                                              |

## QA-01 Consider using `transferShares` directly for stETH transfers for better efficiency

### Proof of Concept

Currently, this is how `stETH` is being transferred in the `ZapRouterBase` https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L107-L114

```solidity
    function _transferInitialStETHFromCaller(uint256 _initialStETH) internal returns (uint256) {
        // check before-after balances for 1-wei corner case
        uint256 _balBefore = stEth.balanceOf(address(this));
        stEth.transferFrom(msg.sender, address(this), _initialStETH);
        uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
        return _deposit;
    }

```

Evidently, there is a balance before and after check, which is done so as to ensure [the 1-2 wei corner case](https://docs.lido.fi/guides/lido-tokens-integration-guide/#1-2-wei-corner-case) is sorted out.

This however can be easily mitigated by using the `transferShares()` function from stETH directly.

Quoting lido's official docs from here: https://docs.lido.fi/guides/lido-tokens-integration-guide/#1-2-wei-corner-case
"

> stETH balance calculation includes integer division, and there is a common case when the whole stETH balance can't be transferred from the account while leaving the last 1-2 wei on the sender's account. The same thing can actually happen at any transfer or deposit transaction. In the future, when the stETH/share rate will be greater, the error can become a bit bigger. To avoid it, one can use `transferShares` to be precise.

"

We can see that the recommendation provided by Lido is that, instead of having the balance before and after check, Badger can instead just query the stETH's `transferShares` shares directly and not be susceptible to the 1-2 wei flaw, i.e https://github.com/lidofinance/lido-dao/blob/5fcedc6e9a9f3ec154e69cff47c2b9e25503a78a/contracts/0.4.24/StETH.sol#L330-L335

```rust
    function transferShares(address _recipient, uint256 _sharesAmount) external returns (uint256) {
        _transferShares(msg.sender, _recipient, _sharesAmount);
        uint256 tokensAmount = getPooledEthByShares(_sharesAmount);
        _emitTransferEvents(msg.sender, _recipient, tokensAmount, _sharesAmount);
        return tokensAmount;
    }
```

### Impact

QA, however using this would mean Badger, completely sidesteps the issue with the 1-2 wei corner case.

### Recommended Mitigation Steps

Consider directly using `transferShares` for stETH transfers, like is currently done in [`LeverageZapRouterBase#_sweepStEth()`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L88-L98).

## QA-02 Import declarations should import specific identifiers, rather than the whole file

### Proof of Concept

Multiple instances in scope, for example take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L4-L12

```solidity
import "./Interfaces/IBorrowerOperations.sol";
import "./Interfaces/IERC3156FlashLender.sol";
import "./Interfaces/IEBTCToken.sol";
import "./Interfaces/ICdpManager.sol";
import "./Interfaces/ISortedCdps.sol";
import "./Interfaces/IPriceFeed.sol";
import "./Dependencies/ICollateralToken.sol";
import {ICdpManagerData} from "./Interfaces/ICdpManagerData.sol";
import "./Dependencies/SafeERC20.sol";
```

Evidently, the imports being done is not name specific, but this is not the best implementation cause this could lead to polluting the symbol namespace.

### Impact

QA, albeit this could lead to the potential pollution of the symbol namespace and a slower compilation speed.

### Recommended Mitigation Steps

Consider using import declarations of the form `import {<identifier_name>} from "some/file.sol"` which avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation (but does not save any gas).

This implementation is what's currently done in https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L4-L17

```solidity
import {IERC3156FlashLender} from "@ebtc/contracts/Interfaces/IERC3156FlashLender.sol";
import {LeverageZapRouterBase} from "./LeverageZapRouterBase.sol";
import {ICdpManagerData} from "@ebtc/contracts/Interfaces/ICdpManagerData.sol";
import {ICdpManager} from "@ebtc/contracts/Interfaces/ICdpManager.sol";
import {IBorrowerOperations} from "@ebtc/contracts/Interfaces/IBorrowerOperations.sol";
import {IPositionManagers} from "@ebtc/contracts/Interfaces/IPositionManagers.sol";
import {IERC20} from "@ebtc/contracts/Dependencies/IERC20.sol";
import {SafeERC20} from "@ebtc/contracts/Dependencies/SafeERC20.sol";
import {EbtcBase} from "@ebtc/contracts/Dependencies/EbtcBase.sol";
import {IStETH} from "./interface/IStETH.sol";
import {IWrappedETH} from "./interface/IWrappedETH.sol";
import {IEbtcLeverageZapRouter} from "./interface/IEbtcLeverageZapRouter.sol";
import {IWstETH} from "./interface/IWstETH.sol";

```

## QA-03 Ensure documentation is sufficiently covered for `ZapRouterBase#_transferInitialStETHFromCaller()`

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L107-L114

```solidity
    function _transferInitialStETHFromCaller(uint256 _initialStETH) internal returns (uint256) {
        // check before-after balances for 1-wei corner case
        uint256 _balBefore = stEth.balanceOf(address(this));
        stEth.transferFrom(msg.sender, address(this), _initialStETH);
        uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
        return _deposit;
    }

```

This function transfers the initial stETH from the caller, it includes a [documentation](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L108) that hints that the before and after balance checks are being done so as to mitigate the 1-2 wei corner case.

Issue however is that the comment reads `        // check before-after balances for 1-wei corner case` instead of `        // check before-after balances for 1-2 wei corner case`.

To dive a bit deeper, here is the section of the Lido's offiicial docs that hints this issue: https://docs.lido.fi/guides/lido-tokens-integration-guide/#1-2-wei-corner-case, quoting them:

> stETH balance calculation includes integer division, and there is a common case when the whole stETH balance can't be transferred from the account while leaving the last 1-2 wei on the sender's account. The same thing can actually happen at any transfer or deposit transaction. In the future, when the stETH/share rate will be greater, the error can become a bit bigger. To avoid it, one can use `transferShares` to be precise.

The above quoted statement hints that in the future, this difference could indeed be bigger.

Additionally, from here https://github.com/lidofinance/lido-dao/issues/442#issue-1298099918, we can understand that there is now a possibility to even face a 2 wei corner case, which happens due to the fact that `stETH` balance calculation depends on two integer divisions (each one has 1 wei "loss" at most).

### Impact

QA

### Recommended Mitigation Steps

Consider applying these changes:

```diff
    function _transferInitialStETHFromCaller(uint256 _initialStETH) internal returns (uint256) {
-        // check before-after balances for 1-wei corner case
+        // check before-after balances for 1-2 wei corner case, see https://docs.lido.fi/guides/lido-tokens-integration-guide/#1-2-wei-corner-case
        uint256 _balBefore = stEth.balanceOf(address(this));
        stEth.transferFrom(msg.sender, address(this), _initialStETH);
        uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
        return _deposit;
    }

```

## QA-04 `LeverageMacroBase.doOperation` should check flashLoan return value

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L167-L244

```solidity
    function _doOperation(
        FlashLoanType flType,
        uint256 borrowAmount,
        LeverageMacroOperation memory operation,
        PostOperationCheck postCheckType,
        PostCheckParams memory checkParams,
        bytes32 expectedCdpId
    ) internal {
        // Call FL Here, then the stuff below needs to happen inside the FL
        if (operation.amountToTransferIn > 0) {
            IERC20(operation.tokenToTransferIn).safeTransferFrom(
                msg.sender,
                address(this),
                operation.amountToTransferIn
            );
        }

        // Take eBTC or stETH FlashLoan
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

        /**
         * POST CALL CHECK FOR CREATION
         */
        if (postCheckType == PostOperationCheck.openCdp) {
            // Check for param details
            ICdpManagerData.Cdp memory cdpInfo = cdpManager.Cdps(expectedCdpId);
            _doCheckValueType(checkParams.expectedDebt, cdpInfo.debt);
            _doCheckValueType(checkParams.expectedCollateral, cdpInfo.coll);
            require(
                cdpInfo.status == checkParams.expectedStatus,
                "!LeverageMacroReference: openCDP status check"
            );
        }

        // Update CDP, Ensure the stats are as intended
        if (postCheckType == PostOperationCheck.cdpStats) {
            ICdpManagerData.Cdp memory cdpInfo = cdpManager.Cdps(checkParams.cdpId);

            _doCheckValueType(checkParams.expectedDebt, cdpInfo.debt);
            _doCheckValueType(checkParams.expectedCollateral, cdpInfo.coll);
            require(
                cdpInfo.status == checkParams.expectedStatus,
                "!LeverageMacroReference: adjustCDP status check"
            );
        }

        // Post check type: Close, ensure it has the status we want
        if (postCheckType == PostOperationCheck.isClosed) {
            ICdpManagerData.Cdp memory cdpInfo = cdpManager.Cdps(checkParams.cdpId);

            require(
                cdpInfo.status == checkParams.expectedStatus,
                "!LeverageMacroReference: closeCDP status check"
            );
        }

        // Sweep here if it's Reference, do not if it's delegate
        if (willSweep) {
            sweepToCaller();
        }
    }
```

And all the external instances where it gets called, i.e

```rust

ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol:
  154
  155:         _doOperation(flType, borrowAmount, operation, postCheckType, checkParams, expectedCdpId);
  156      }

  166      /// for all other operations
  167:     function _doOperation(
  168          FlashLoanType flType,

ebtc-zap-router/lib/ebtc/packages/contracts/contracts/LeverageMacroBase.sol:
  154
  155:         _doOperation(flType, borrowAmount, operation, postCheckType, checkParams, expectedCdpId);
  156      }

  166      /// for all other operations
  167:     function _doOperation(
  168          FlashLoanType flType,

ebtc-zap-router/src/LeverageZapRouterBase.sol:
  145
  146:         _doOperation(
  147              _flType,

  179
  180:         _doOperation(
  181              FlashLoanType.stETH,

  218
  219:         _doOperation(
  220              FlashLoanType.eBTC,
```

We can see that the flashloan's return value is not being checked, in any instance.

### Impact

According to IERC3156FlashLender.sol: https://github.com/code-423n4/2023-10-badger/blob/f2f2e2cf9965a1020661d179af46cb49e993cb7e/packages/contracts/contracts/Interfaces/IERC3156FlashLender.sol#L28-L33, `flashLoan()` includes a return value the caller should check.

### Recommended Mitigation Steps

Consider checking this return value.

## QA-05 Whitelisted tokens should not be sweepable

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L267-L272

```solidity
    function sweepToken(address token, uint256 amount) public {
        _assertOwner();

        IERC20(token).safeTransfer(msg.sender, amount);
    }

```

This function is used to transfer arbitrary tokens, however there is no check that the `token` provided here is not `eBTC` or `stETH` or any other whitelisted one, which would mean that the _owner_ can sweep even whitelisted tokens.

### Impact

QA - Centralisation risks.

_Following Code4rena's new QA rules, this should be flagged in an audit_... in retrospect this allows the owner to take any token.

### Recommended Mitigation Steps

Consider protecting whitelisted tokens and/or heavily documenting this functionality to the end users.

## QA-06 `EbtcLeverageZapRouter#openCdpWithEth()` erroneuosly claims that a _higher margin equals lower CR_

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-badger/blob/main/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L34-L66

```solidity
    function openCdpWithEth(
        uint256 _debt,
        bytes32 _upperHint,
        bytes32 _lowerHint,
        uint256 _stEthLoanAmount,
        uint256 _ethMarginBalance,
        uint256 _stEthDepositAmount,
        bytes calldata _positionManagerPermit,
        TradeData calldata _tradeData
    ) external payable returns (bytes32 cdpId) {
        uint256 _collVal = _convertRawEthToStETH(_ethMarginBalance);


        cdpId = _openCdp(
            _debt,
            _upperHint,
            _lowerHint,
            _stEthLoanAmount,
            _collVal,
            _stEthDepositAmount,
            _positionManagerPermit,
            _tradeData
        );


        emit ZapOperationEthVariant(
            cdpId,
            EthVariantZapOperationType.OpenCdp,
            true,
            NATIVE_ETH_ADDRESS,
            _ethMarginBalance,
            _collVal,
            msg.sender
        );
    }

```

This function is used to open a CDP with raw native Ether.

Issue however is that [the natspec/comment from the documentation for this function](https://github.com/code-423n4/2024-06-badger/blob/main/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L30) reads `      /// @param _ethMarginBalance The amount of margin deposit (converted from raw Ether) from the user, higher margin equals lower CR` instead of `         /// @param _ethMarginBalance The amount of margin deposit (converted from raw Ether) from the user, higher margin equals HIGHER CR`.

The above documentation is erroneous in the sense that, naturally and in the case of Badger, higher margin should equal a higher CR.

### Impact

QA, confusing code for all integrating partners (users/devs/auditors).

### Recommended Mitigation Steps

Consider applying these changes:

```diff
    /// @dev Open a CDP with raw native Ether
    /// @param _debt The total expected debt for new CDP
    /// @param _upperHint The expected CdpId of neighboring higher ICR within SortedCdps, could be simply bytes32(0)
    /// @param _lowerHint The expected CdpId of neighboring lower ICR within SortedCdps, could be simply bytes32(0)
    /// @param _stEthLoanAmount The flash loan amount needed to open the leveraged Cdp position
-    /// @param _ethMarginBalance The amount of margin deposit (converted from raw Ether) from the user, higher margin equals lower CR
+    /// @param _ethMarginBalance The amount of margin deposit (converted from raw Ether) from the user, higher margin equals higher CR
    /// @param _stEthDepositAmount The total stETH collateral amount deposited (added) for the specified Cdp
    /// @param _positionManagerPermit PositionPermit required for Zap approved by calling user
    /// @param _tradeData DEX calldata for converting between debt and collateral
    function openCdpWithEth(
        uint256 _debt,
        bytes32 _upperHint,
        bytes32 _lowerHint,
        uint256 _stEthLoanAmount,
        uint256 _ethMarginBalance,
        uint256 _stEthDepositAmount,
        bytes calldata _positionManagerPermit,
        TradeData calldata _tradeData
    ) external payable returns (bytes32 cdpId) {
        uint256 _collVal = _convertRawEthToStETH(_ethMarginBalance);


        cdpId = _openCdp(
            _debt,
            _upperHint,
            _lowerHint,
            _stEthLoanAmount,
            _collVal,
            _stEthDepositAmount,
            _positionManagerPermit,
            _tradeData
        );


        emit ZapOperationEthVariant(
            cdpId,
            EthVariantZapOperationType.OpenCdp,
            true,
            NATIVE_ETH_ADDRESS,
            _ethMarginBalance,
            _collVal,
            msg.sender
        );
    }

```

## QA-07 Open todos should be sorted before live deployment

### Proof of Concept

Multiple instances in scope, for example take a look at take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcZapRouter.sol#L23-L41

```solidity
    constructor(
        IERC20 _wstEth,
        IERC20 _wEth,
        IStETH _stEth,
        IERC20 _ebtc,
        IBorrowerOperations _borrowerOperations,
        ICdpManager _cdpManager,
        address _owner
    ) ZapRouterBase(address(_borrowerOperations), _wstEth, _wEth, _stEth) {
        ebtc = _ebtc;
        borrowerOperations = _borrowerOperations;
        cdpManager = _cdpManager;
        owner = _owner;

        // Infinite Approvals @TODO: do these stay at max for each token?
        stEth.approve(address(borrowerOperations), type(uint256).max);
        stEth.approve(address(wstEth), type(uint256).max);
    }

```

Another one is here https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L34-L41

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

As hinted this is just a few of the multiple instances in scope, use this search command to pinpoint to other instances: [https://github.com/search?q=repo%3Acode-423n4%2F2024-06-badger+todo+language%3A%22Gerber+Image%22+NOT+path%3A%2F%5Eebtc-protocol%5C%2Fpackages%5C%2Fcontracts%5C%2Ffoundry_test%5C%2F%2F+NOT+path%3A%2F%5Eebtc-protocol%5C%2Fpackages%5C%2Fcontracts%5C%2Fcontracts%5C%2FTestContracts%5C%2Finvariants%5C%2F%2F+NOT+path%3A%2F%5Eebtc-protocol%5C%2Fpackages%5C%2Fcontracts%5C%2Flib%5C%2Fforge-std%5C%2Fsrc%5C%2F%2F&type=code&l=Gerber+Image](https://github.com/search?q=repo%3Acode-423n4%2F2024-06-badger+todo+language%3A%22Gerber+Image%22+NOT+path%3A%2F%5Eebtc-protocol%5C%2Fpackages%5C%2Fcontracts%5C%2Ffoundry_test%5C%2F%2F+NOT+path%3A%2F%5Eebtc-protocol%5C%2Fpackages%5C%2Fcontracts%5C%2Fcontracts%5C%2FTestContracts%5C%2Finvariants%5C%2F%2F+NOT+path%3A%2F%5Eebtc-protocol%5C%2Fpackages%5C%2Fcontracts%5C%2Flib%5C%2Fforge-std%5C%2Fsrc%5C%2F%2F&type=code&l=Gerber+Image)

### Impact

QA... Un-ready code is being deployed.

### Recommended Mitigation Steps

Consider sorting out all todos before final deployment.

## QA-08 `LeverageMacroBase#_doSwap()` might not work for some tokens like `BNB`

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L448-L481

```solidity
    function _doSwap(SwapOperation memory swapData) internal {
        // Ensure call is safe
        // Block all system contracts
        _ensureNotSystem(swapData.addressForSwap);

        // Exact approve
        // Approve can be given anywhere because this is a router, and after call we will delete all approvals
        IERC20(swapData.tokenForSwap).safeApprove(
            swapData.addressForApprove,
            swapData.exactApproveAmount
        );

        // Call and perform swap
        // NOTE: Technically approval may be different from target, something to keep in mind
        // Call target are limited
        // But technically you could approve w/e you want here, this is fine because the contract is a router and will not hold user funds
        (bool success, ) = excessivelySafeCall(
            swapData.addressForSwap,
            gasleft(),
            0,
            0,
            swapData.calldataForSwap
        );
        require(success, "Call has failed");

        // Approve back to 0
        // Enforce exact approval
        // Can use max because the tokens are OZ
        // val -> 0 -> 0 -> val means this is safe to repeat since even if full approve is unused, we always go back to 0 after
        IERC20(swapData.tokenForSwap).safeApprove(swapData.addressForApprove, 0);

        // Do the balance checks after the call to the aggregator
        _doSwapChecks(swapData.swapChecks);
    }
```

Evidently, the new update to the `_doSwap()` function now includes it resetting the approval back to zero, issue however is that some tokens are now automatically not supported, considering there is no possibility of approving to zero for these tokens, see https://github.com/d-xo/weird-erc20/tree/main?tab=readme-ov-file#revert-on-approval-to-zero-address

### Impact

QA

### Recommended Mitigation Steps

Consider checking if in any case these types of tokens are to be integrated then reset the approval for these tokens to 1 wei instead, robust method, would be to use OZ's `forceApprove()`.

## QA-09 `_openCdpCallback()` does not emit an event

Take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L510-L522

```solidity
    function _openCdpCallback(bytes memory data) internal virtual {
        OpenCdpOperation memory flData = abi.decode(data, (OpenCdpOperation));

        /**
         * Open CDP and Emit event
         */
        bytes32 _cdpId = borrowerOperations.openCdp(
            flData.eBTCToMint,
            flData._upperHint,
            flData._lowerHint,
            flData.stETHToDeposit
        );
    }
```

According to comment - function `_openCdpCallback()` should emit an event. However, there's no event emitted by `_openCdpCallback()`.

## QA-10 Consider attaching error messages to _require_ checks

### Proof of Concept

Multiple instances in scope, for example take a look at https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L500-L507

```solidity
    function _ensureNotSystem(address addy) internal {
        /// @audit Check and add more if you think it's better
        require(addy != address(borrowerOperations));
        require(addy != address(sortedCdps));
        require(addy != address(activePool));
        require(addy != address(cdpManager));
        require(addy != address(this)); // If it could call this it could fake the forwarded caller
    }
```

This function is used to prevent arbitrary calls on some protected targets, issue however is that this function includes `5` requirement checks but no error message whatsoever.

### Impact

Bad code design, not of the industry standard, stalls smooth user operation.

### Recommended Mitigation Steps

Consider attaching error messages to every of the checks as is done similarly here https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L310-L329

```solidity
    function _requireNonZeroAdjustment(
        uint256 _stEthBalanceIncrease,
        uint256 _debtChange,
        uint256 _stEthBalanceDecrease
    ) internal pure {
        require(
            _stEthBalanceIncrease > 0 || _stEthBalanceDecrease > 0 || _debtChange > 0,
            "BorrowerOperations: There must be either a collateral or debt change"
        );
    }

    function _requireSingularMarginChange(
        uint256 _stEthMarginIncrease,
        uint256 _stEthMarginDecrease
    ) internal pure {
        require(
            _stEthMarginIncrease == 0 || _stEthMarginDecrease == 0,
            "EbtcLeverageZapRouter: Cannot add and withdraw margin in same operation"
        );
    }
```
