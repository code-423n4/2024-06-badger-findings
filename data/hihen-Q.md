# QA Report

Issues not included in the 4naly3er-report.

## Summary

### Low Issues

Total **16 instances** over **6 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[L-01]](#l-01-unchecked-return-value-of-low-level-call) | Unchecked return value of low-level call | 1 |
| [[L-02]](#l-02-downcasting-other-types-to-an-address-can-cause-collisions) | Downcasting other types to an address can cause collisions | 1 |
| [[L-03]](#l-03-constructor--initialization-function-lacks-parameter-validation) | Constructor / initialization function lacks parameter validation | 1 |
| [[L-04]](#l-04-unsafe-solidity-low-level-call-can-cause-gas-grief-attack) | Unsafe solidity low-level call can cause gas grief attack | 1 |
| [[L-05]](#l-05-functions-calling-contractsaddresses-with-transfer-hooks-should-be-protected-by-reentrancy-guard) | Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard | 7 |
| [[L-06]](#l-06-code-does-not-follow-the-best-practice-of-check-effects-interaction) | Code does not follow the best practice of check-effects-interaction | 5 |

### Non Critical Issues

Total **123 instances** over **40 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[N-01]](#n-01-import-declarations-should-import-specific-identifiers-rather-than-the-whole-file) | Import declarations should import specific identifiers, rather than the whole file | 8 |
| [[N-02]](#n-02-visibility-of-state-variables-is-not-explicitly-defined) | Visibility of state variables is not explicitly defined | 1 |
| [[N-03]](#n-03-names-of-privateinternal-functions-should-be-prefixed-with-an-underscore) | Names of `private`/`internal` functions should be prefixed with an underscore | 1 |
| [[N-04]](#n-04-names-of-privateinternal-state-variables-should-be-prefixed-with-an-underscore) | Names of `private`/`internal` state variables should be prefixed with an underscore | 4 |
| [[N-05]](#n-05-use-of-override-is-unnecessary) | Use of `override` is unnecessary | 4 |
| [[N-06]](#n-06-add-inline-comments-for-unnamed-parameters) | Add inline comments for unnamed parameters | 1 |
| [[N-07]](#n-07-consider-splitting-complex-checks-into-multiple-steps) | Consider splitting complex checks into multiple steps | 1 |
| [[N-08]](#n-08-consider-adding-a-blockdeny-list) | Consider adding a block/deny-list | 3 |
| [[N-09]](#n-09-convert-simple-if-statements-to-ternary-expressions) | Convert simple `if`-statements to ternary expressions | 1 |
| [[N-10]](#n-10-contracts-should-each-be-defined-in-separate-files) | Contracts should each be defined in separate files | 2 |
| [[N-11]](#n-11-consider-emitting-an-event-at-the-end-of-the-constructor) | Consider emitting an event at the end of the constructor | 4 |
| [[N-12]](#n-12-empty-function-body-without-comments) | Empty function body without comments | 1 |
| [[N-13]](#n-13-function-state-mutability-can-be-restricted-to-pure) | Function state mutability can be restricted to pure | 5 |
| [[N-14]](#n-14-imports-could-be-organized-more-systematically) | Imports could be organized more systematically | 5 |
| [[N-15]](#n-15-expressions-for-constant-values-should-use-immutable-rather-than-constant) | Expressions for constant values should use `immutable` rather than `constant` | 1 |
| [[N-16]](#n-16-functions-should-be-named-in-mixedcase-style) | Functions should be named in mixedCase style | 3 |
| [[N-17]](#n-17-variable-names-for-immutables-should-use-upper_case_with_underscores) | Variable names for `immutable`s should use UPPER_CASE_WITH_UNDERSCORES | 13 |
| [[N-18]](#n-18-constants-should-be-put-on-the-left-side-of-comparisons) | Constants should be put on the left side of comparisons | 5 |
| [[N-19]](#n-19-else-block-not-required) | `else`-block not required | 1 |
| [[N-20]](#n-20-large-multiples-of-ten-should-use-scientific-notation) | Large multiples of ten should use scientific notation | 1 |
| [[N-21]](#n-21-todos-left-in-the-code) | `TODO`s left in the code | 2 |
| [[N-22]](#n-22-high-cyclomatic-complexity) | High cyclomatic complexity | 1 |
| [[N-23]](#n-23-consider-bounding-input-array-length) | Consider bounding input array length | 2 |
| [[N-24]](#n-24-unused-contract-variables) | Unused contract variables | 1 |
| [[N-25]](#n-25-unused-function-parameters) | Unused function parameters | 7 |
| [[N-26]](#n-26-unused-local-variables) | Unused local variables | 3 |
| [[N-27]](#n-27-consider-using-delete-rather-than-assigning-zero-to-clear-values) | Consider using `delete` rather than assigning zero to clear values | 2 |
| [[N-28]](#n-28-use-the-latest-solidity-version) | Use the latest Solidity version | 4 |
| [[N-29]](#n-29-use-transfer-libraries-instead-of-low-level-calls-to-transfer-native-tokens) | Use transfer libraries instead of low level calls to transfer native tokens | 1 |
| [[N-30]](#n-30-use-a-struct-to-encapsulate-multiple-function-parameters) | Use a struct to encapsulate multiple function parameters | 8 |
| [[N-31]](#n-31-returning-a-struct-instead-of-a-bunch-of-variables-is-better) | Returning a struct instead of a bunch of variables is better | 1 |
| [[N-32]](#n-32-contract-variables-should-have-comments) | Contract variables should have comments | 1 |
| [[N-33]](#n-33-empty-bytes-check-is-missing) | Empty bytes check is missing | 11 |
| [[N-34]](#n-34-function-state-mutability-can-be-restricted-to-view) | Function state mutability can be restricted to view | 2 |
| [[N-35]](#n-35-missing-event-for-critical-changes) | Missing event for critical changes | 3 |
| [[N-36]](#n-36-non-assembly-method-available) | Non-assembly method available | 4 |
| [[N-37]](#n-37-consider-adding-emergency-stop-functionality) | Consider adding emergency-stop functionality | 1 |
| [[N-38]](#n-38-use-the-modern-upgradeable-contract-paradigm) | Use the Modern Upgradeable Contract Paradigm | 1 |
| [[N-39]](#n-39-large-or-complicated-code-bases-should-implement-invariant-tests) | Large or complicated code bases should implement invariant tests | 1 |
| [[N-40]](#n-40-contracts-should-have-all-publicexternal-functions-exposed-by-interfaces) | Contracts should have all `public`/`external` functions exposed by `interface`s | 2 |

## Low Issues

### [L-01] Unchecked return value of low-level call

The function being called may revert, which will be indicated by the return value to `call()`/`delegatecall()`. If the return value is not checked, the code will continue on as if there was no error, rather than reverting with the error encountered.

There is 1 instance:

- *ZapRouterBase.sol* ( [38-38](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L38-L38) ):

```solidity
38:         payable(address(stEth)).call{value: _initialETH}("");
```

### [L-02] Downcasting other types to an address can cause collisions

Downcasting other types to an address will truncates the upper bytes, which means that multiple values can be mapped to an address, i.e. address collisions can occur.

There is 1 instance:

- *ZapRouterBase.sol* ( [151-151](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L151-L151) ):

```solidity
151:         return address(uint160(_tmp));
```

### [L-03] Constructor / initialization function lacks parameter validation

Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

There is 1 instance:

- *LeverageMacroBase.sol* ( [51-59](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L51-L59) ):

```solidity
/// @audit `_sweepToCaller`
51:     constructor(
52:         address _borrowerOperationsAddress,
53:         address _activePool,
54:         address _cdpManager,
55:         address _ebtc,
56:         address _coll,
57:         address _sortedCdps,
58:         bool _sweepToCaller
59:     ) {
```

### [L-04] Unsafe solidity low-level call can cause gas grief attack

Using the low-level calls of a solidity address can leave the contract open to gas grief attacks. These attacks occur when the called contract returns a large amount of data.
So when calling an external contract, it is necessary to check the length of the return data before reading/copying it (using `returndatasize()`).

There is 1 instance:

- *ZapRouterBase.sol* ( [38-38](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L38-L38) ):

```solidity
38:         payable(address(stEth)).call{value: _initialETH}("");
```

### [L-05] Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks opens the users of this protocol up to [read-only reentrancy vulnerability](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect them except by block-listing the entire protocol.

There are 7 instances:

- *LeverageMacroBase.sol* ( [177-181](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L177-L181), [270-270](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L270-L270) ):

```solidity
177:             IERC20(operation.tokenToTransferIn).safeTransferFrom(
178:                 msg.sender,
179:                 address(this),
180:                 operation.amountToTransferIn
181:             );

270:         IERC20(token).safeTransfer(msg.sender, amount);
```

- *ZapRouterBase.sol* ( [45-45](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L45-L45), [61-61](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L61-L61), [91-91](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L91-L91), [103-103](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L103-L103), [110-110](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L110-L110) ):

```solidity
45:         wrappedEth.transferFrom(msg.sender, address(this), _initialWETH);

61:             wstEth.transferFrom(msg.sender, address(this), _initialWstETH),

91:             wstEth.transfer(msg.sender, _wstETHVal);

103:             stEth.transfer(msg.sender, _stEthVal);

110:         stEth.transferFrom(msg.sender, address(this), _initialStETH);
```

### [L-06] Code does not follow the best practice of check-effects-interaction

Code should follow the best-practice of [check-effects-interaction](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

<details>
<summary>There are 5 instances (click to show):</summary>

- *LeverageMacroBase.sol* ( [438-438](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L438-L438) ):

```solidity
/// @audit `_doSwap()` is called on line 436, triggering an external call - `safeApprove()`
438:                 ++i;
```

- *EbtcLeverageZapRouter.sol* ( [46-55](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L46-L55), [89-98](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L89-L98), [140-149](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L140-L149), [183-192](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L183-L192) ):

```solidity
/// @audit `_convertRawEthToStETH()` is called on line 44, triggering an external call - `balanceOf()`
46:         cdpId = _openCdp(
47:             _debt,
48:             _upperHint,
49:             _lowerHint,
50:             _stEthLoanAmount,
51:             _collVal,
52:             _stEthDepositAmount,
53:             _positionManagerPermit,
54:             _tradeData
55:         );

/// @audit `_convertWstEthToStETH()` is called on line 87, triggering an external call - `balanceOf()`
89:         cdpId = _openCdp(
90:             _debt,
91:             _upperHint,
92:             _lowerHint,
93:             _stEthLoanAmount,
94:             _collVal,
95:             _stEthDepositAmount,
96:             _positionManagerPermit,
97:             _tradeData
98:         );

/// @audit `_convertWrappedEthToStETH()` is called on line 138, triggering an external call - `balanceOf()`
140:         cdpId = _openCdp(
141:             _debt,
142:             _upperHint,
143:             _lowerHint,
144:             _stEthLoanAmount,
145:             _collVal,
146:             _stEthDepositAmount,
147:             _positionManagerPermit,
148:             _tradeData
149:         );

/// @audit `_transferInitialStETHFromCaller()` is called on line 181, triggering an external call - `balanceOf()`
183:         cdpId = _openCdp(
184:             _debt,
185:             _upperHint,
186:             _lowerHint,
187:             _stEthLoanAmount,
188:             _collVal,
189:             _stEthDepositAmount,
190:             _positionManagerPermit,
191:             _tradeData
192:         );
```

</details>

## Non Critical Issues

### [N-01] Import declarations should import specific identifiers, rather than the whole file

Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation (but does not save any gas).

There are 8 instances:

- *LeverageMacroBase.sol* ( [4](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L4), [5](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L5), [6](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L6), [7](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L7), [8](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L8), [9](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L9), [10](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L10), [12](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L12) ):

```solidity
4: import "./Interfaces/IBorrowerOperations.sol";

5: import "./Interfaces/IERC3156FlashLender.sol";

6: import "./Interfaces/IEBTCToken.sol";

7: import "./Interfaces/ICdpManager.sol";

8: import "./Interfaces/ISortedCdps.sol";

9: import "./Interfaces/IPriceFeed.sol";

10: import "./Dependencies/ICollateralToken.sol";

12: import "./Dependencies/SafeERC20.sol";
```

### [N-02] Visibility of state variables is not explicitly defined

To avoid misunderstandings and unexpected state accesses, it is recommended to explicitly define the visibility of each state variable.

There is 1 instance:

- *LeverageMacroBase.sol* ( [37-37](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L37-L37) ):

```solidity
37:     bytes32 constant FLASH_LOAN_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");
```

### [N-03] Names of `private`/`internal` functions should be prefixed with an underscore

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#underscore-prefix-for-non-external-functions-and-variables).

There is 1 instance:

- *LeverageMacroBase.sol* ( [568-574](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L568-L574) ):

```solidity
568:     function excessivelySafeCall(
569:         address _target,
570:         uint256 _gas,
571:         uint256 _value,
572:         uint16 _maxCopy,
573:         bytes memory _calldata
574:     ) internal returns (bool, bytes memory) {
```

### [N-04] Names of `private`/`internal` state variables should be prefixed with an underscore

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#underscore-prefix-for-non-external-functions-and-variables).

There are 4 instances:

- *LeverageMacroBase.sol* ( [35-35](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L35-L35), [37-37](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L37-L37) ):

```solidity
35:     bool internal immutable willSweep;

37:     bytes32 constant FLASH_LOAN_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");
```

- *LeverageZapRouterBase.sol* ( [18-18](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L18-L18), [19-19](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L19-L19) ):

```solidity
18:     uint256 internal constant PRECISION = 1e18;

19:     uint256 internal constant BPS = 10000;
```

### [N-05] Use of `override` is unnecessary

[Starting from Solidity 0.8.8](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding), the override keyword is not required when overriding an interface function, except for the case where the function is defined in multiple bases.

There are 4 instances:

- *LeverageZapRouterBase.sol* ( [61-61](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L61-L61), [65-71](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L65-L71), [276-276](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L276-L276), [294-294](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L294-L294) ):

```solidity
61:     function owner() public override returns (address) {

65:     function doOperation(
66:         FlashLoanType flType,
67:         uint256 borrowAmount,
68:         LeverageMacroOperation calldata operation,
69:         PostOperationCheck postCheckType,
70:         PostCheckParams calldata checkParams
71:     ) external override {

276:     function _openCdpForCallback(bytes memory data) internal override {

294:     function _adjustCdpCallback(bytes memory data) internal override {
```

### [N-06] Add inline comments for unnamed parameters

`function func(address a, address)` -> `function func(address a, address /* b */)`

There is 1 instance:

- *LeverageMacroBase.sol* ( [15-15](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L15-L15) ):

```solidity
15:     function Cdps(bytes32) external view returns (ICdpManagerData.Cdp memory);
```

### [N-07] Consider splitting complex checks into multiple steps

Assign the expression's parts to intermediate local variables, and check against those instead.

There is 1 instance:

- *EbtcLeverageZapRouter.sol* ( [316-316](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L316-L316) ):

```solidity
316:             _stEthBalanceIncrease > 0 || _stEthBalanceDecrease > 0 || _debtChange > 0,
```

### [N-08] Consider adding a block/deny-list

Doing so will significantly increase centralization, but will help to prevent hackers from using stolen tokens

There are 3 instances:

- *LeverageMacroBase.sol* ( [26](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L26) ):

```solidity
26: abstract contract LeverageMacroBase {
```

- *LeverageZapRouterBase.sol* ( [15](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L15) ):

```solidity
15: abstract contract LeverageZapRouterBase is ZapRouterBase, LeverageMacroBase, ReentrancyGuard, IEbtcLeverageZapRouter {
```

- *ZapRouterBase.sol* ( [17](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L17) ):

```solidity
17: abstract contract ZapRouterBase is IEbtcZapRouterBase {
```

### [N-09] Convert simple `if`-statements to ternary expressions

Converting some if statements to ternaries (such as `z = (a < b) ? x : y`) can make the code more concise and easier to read.

There is 1 instance:

- *LeverageZapRouterBase.sol* ( [113-129](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L113-L129) ):

```solidity
113:         if (_cdp._isDebtIncrease) {
114:             // for debt increases, we flash borrow stETH
115:             // trade eBTC -> stETH for repayment
116:             op.swapsAfter = _getSwapOperations(
117:                 address(ebtcToken), 
118:                 address(stETH),
119:                 _tradeData
120:             );
121:         } else {
122:             // for debt decreases (unwinding), we flash borrow eBTC
123:             // trade stETH -> eBTC for repayment
124:             op.swapsAfter = _getSwapOperations(
125:                 address(stETH),
126:                 address(ebtcToken),
127:                 _tradeData
128:             );
129:         }
```

### [N-10] Contracts should each be defined in separate files

Keeping each contract in a separate file makes it easier to work with multiple people, makes the code easier to maintain, and is a common practice on most projects. The following files each contains more than one contract/library/interface.

There are 2 instances:

- [*LeverageMacroBase.sol*](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol)

- [*ZapRouterBase.sol*](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol)

### [N-11] Consider emitting an event at the end of the constructor

This will allow users to easily exactly pinpoint when and by whom a contract was constructed.

<details>
<summary>There are 4 instances (click to show):</summary>

- *LeverageMacroBase.sol* ( [51-59](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L51-L59) ):

```solidity
51:     constructor(
52:         address _borrowerOperationsAddress,
53:         address _activePool,
54:         address _cdpManager,
55:         address _ebtc,
56:         address _coll,
57:         address _sortedCdps,
58:         bool _sweepToCaller
59:     ) {
```

- *EbtcLeverageZapRouter.sol* ( [21-23](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L21-L23) ):

```solidity
21:     constructor(
22:         IEbtcLeverageZapRouter.DeploymentParams memory params
23:     ) LeverageZapRouterBase(params) { }
```

- *LeverageZapRouterBase.sol* ( [26-44](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L26-L44) ):

```solidity
26:     constructor(
27:         IEbtcLeverageZapRouter.DeploymentParams memory params
28:     )
29:         ZapRouterBase(
30:             params.borrowerOperations, 
31:             IERC20(params.wstEth), 
32:             IERC20(params.weth), 
33:             IStETH(params.stEth)
34:         )
35:         LeverageMacroBase(
36:             params.borrowerOperations,
37:             params.activePool,
38:             params.cdpManager,
39:             params.ebtc,
40:             params.stEth,
41:             params.sortedCdps,
42:             false // Do not sweep
43:         )
44:     {
```

- *ZapRouterBase.sol* ( [27-27](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L27-L27) ):

```solidity
27:     constructor(address _borrowerOperations, IERC20 _wstEth, IERC20 _wEth, IStETH _stEth) {
```

</details>

### [N-12] Empty function body without comments

Empty function body in solidity is not recommended, consider adding some comments to the body.

There is 1 instance:

- *EbtcLeverageZapRouter.sol* ( [21-23](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L21-L23) ):

```solidity
21:     constructor(
22:         IEbtcLeverageZapRouter.DeploymentParams memory params
23:     ) LeverageZapRouterBase(params) { }
```

### [N-13] Function state mutability can be restricted to pure

Function state mutability can be restricted to pure

There are 5 instances:

- *LeverageMacroBase.sol* ( [39-39](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L39-L39), [277-277](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L277-L277), [388-388](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L388-L388) ):

```solidity
39:     function owner() public virtual returns (address) {

277:     function _doCheckValueType(CheckValueAndType memory check, uint256 valueToCheck) internal {

388:     function decodeFLData(bytes calldata data) public view returns (LeverageMacroOperation memory) {
```

- *LeverageZapRouterBase.sol* ( [65-71](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L65-L71), [249-250](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L249-L250) ):

```solidity
65:     function doOperation(
66:         FlashLoanType flType,
67:         uint256 borrowAmount,
68:         LeverageMacroOperation calldata operation,
69:         PostOperationCheck postCheckType,
70:         PostCheckParams calldata checkParams
71:     ) external override {

249:     function _getSwapChecks(address tokenToCheck, uint256 expectedMinOut) 
250:         internal view returns (SwapCheck[] memory checks) {
```

### [N-14] Imports could be organized more systematically

The contract's interface should be imported first, followed by each of the interfaces it uses, followed by all other files. The examples below do not follow this layout.

There are 5 instances:

- *EbtcLeverageZapRouter.sol* ( [6-6](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L6-L6), [13-13](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L13-L13) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
6: import {ICdpManagerData} from "@ebtc/contracts/Interfaces/ICdpManagerData.sol";

/// @audit Out of order with the prev import️ ⬆
13: import {IStETH} from "./interface/IStETH.sol";
```

- *LeverageZapRouterBase.sol* ( [10-10](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L10-L10), [12-12](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L12-L12) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
10: import {IEbtcLeverageZapRouter} from "./interface/IEbtcLeverageZapRouter.sol";

/// @audit Out of order with the prev import️ ⬆
12: import {IStETH} from "./interface/IStETH.sol";
```

- *ZapRouterBase.sol* ( [7-7](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L7-L7) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
7: import {IERC20} from "@ebtc/contracts/Dependencies/IERC20.sol";
```

### [N-15] Expressions for constant values should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

There is 1 instance:

- *LeverageMacroBase.sol* ( [37-37](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L37-L37) ):

```solidity
37:     bytes32 constant FLASH_LOAN_SUCCESS = keccak256("ERC3156FlashBorrower.onFlashLoan");
```

### [N-16] Functions should be named in mixedCase style

As the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.21/style-guide.html#function-names) suggests: functions should be named in mixedCase style.

There are 3 instances:

- *LeverageMacroBase.sol* ( [15](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L15), [388](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L388) ):

```solidity
15:     function Cdps(bytes32) external view returns (ICdpManagerData.Cdp memory);

388:     function decodeFLData(bytes calldata data) public view returns (LeverageMacroOperation memory) {
```

- *ZapRouterBase.sol* ( [14](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L14) ):

```solidity
14:     function MIN_CHANGE() external view returns (uint256);
```

### [N-17] Variable names for `immutable`s should use UPPER_CASE_WITH_UNDERSCORES

For `immutable` variable names, each word should use all capital letters, with underscores separating each word (UPPER_CASE_WITH_UNDERSCORES)

<details>
<summary>There are 13 instances (click to show):</summary>

- *LeverageMacroBase.sol* ( [29-29](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L29-L29), [30-30](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L30-L30), [31-31](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L31-L31), [32-32](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L32-L32), [33-33](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L33-L33), [34-34](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L34-L34), [35-35](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L35-L35) ):

```solidity
29:     IBorrowerOperations public immutable borrowerOperations;

30:     IActivePool public immutable activePool;

31:     ICdpCdps public immutable cdpManager;

32:     IEBTCToken public immutable ebtcToken;

33:     ISortedCdps public immutable sortedCdps;

34:     ICollateralToken public immutable stETH;

35:     bool internal immutable willSweep;
```

- *LeverageZapRouterBase.sol* ( [21-21](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L21-L21), [23-23](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L23-L23), [24-24](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L24-L24) ):

```solidity
21:     address public immutable theOwner;

23:     uint256 public immutable zapFeeBPS;

24:     address public immutable zapFeeReceiver;
```

- *ZapRouterBase.sol* ( [23-23](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L23-L23), [24-24](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L24-L24), [25-25](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L25-L25) ):

```solidity
23:     IERC20 public immutable wstEth;

24:     IStETH public immutable stEth;

25:     IERC20 public immutable wrappedEth;
```

</details>

### [N-18] Constants should be put on the left side of comparisons

Putting constants on the left side of comparison statements is a best practice known as [Yoda conditions](https://en.wikipedia.org/wiki/Yoda_conditions).
Although solidity's static typing system prevents accidental assignments within conditionals, adopting this practice can improve code readability and consistency, especially when working across multiple languages.

There are 5 instances:

- *EbtcLeverageZapRouter.sol* ( [326](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L326), [326](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L326) ):

```solidity
/// @audit put `0` on the left
326:             _stEthMarginIncrease == 0 || _stEthMarginDecrease == 0,

/// @audit put `0` on the left
326:             _stEthMarginIncrease == 0 || _stEthMarginDecrease == 0,
```

- *LeverageZapRouterBase.sol* ( [46](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L46) ):

```solidity
/// @audit put `address(0)` on the left
46:             require(params.zapFeeReceiver != address(0));
```

- *ZapRouterBase.sol* ( [137](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L137), [144](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L144) ):

```solidity
/// @audit put `0` on the left
137:             _change == 0 || _change >= MIN_CHANGE,

/// @audit put `MIN_NET_STETH_BALANCE` on the left
144:             _stEthBalance >= MIN_NET_STETH_BALANCE,
```

### [N-19] `else`-block not required

One level of nesting can be removed by not having an `else` block when the `if`-block always jumps at the end. For example:
```solidity
if (condition) {
    body1...
    return x;
} else {
    body2...
}
```
can be changed to:
```solidity
if (condition) {
    body1...
    return x;
}
body2...
```

There is 1 instance:

- *LeverageMacroBase.sol* ( [278-281](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L278-L281) ):

```solidity
278:         if (check.operator == Operator.skip) {
279:             // Early return
280:             return;
281:         } else if (check.operator == Operator.gte) {
```

### [N-20] Large multiples of ten should use scientific notation

Use a scientific notation rather than decimal literals (e.g. `1e6` instead of `1000000`), for better code readability.

There is 1 instance:

- *LeverageZapRouterBase.sol* ( [19-19](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L19-L19) ):

```solidity
/// @audit 10000 -> 1e4
19:     uint256 internal constant BPS = 10000;
```

### [N-21] `TODO`s left in the code

TODOs may signal that a feature is missing or not ready for audit, consider resolving the issue and removing the TODO comment

There are 2 instances:

- *LeverageZapRouterBase.sol* ( [54](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L54) ):

```solidity
54:         // Infinite Approvals @TODO: do these stay at max for each token?
```

- *ZapRouterBase.sol* ( [37](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L37) ):

```solidity
37:         // TODO call submit() with a referral?
```

### [N-22] High cyclomatic complexity

Consider breaking down these blocks into more manageable units, by splitting things into utility functions, by reducing nesting, and by using early returns.

<details>
<summary>There is 1 instance (click to show):</summary>

- *LeverageMacroBase.sol* ( [167-244](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L167-L244) ):

```solidity
167:     function _doOperation(
168:         FlashLoanType flType,
169:         uint256 borrowAmount,
170:         LeverageMacroOperation memory operation,
171:         PostOperationCheck postCheckType,
172:         PostCheckParams memory checkParams,
173:         bytes32 expectedCdpId
174:     ) internal {
175:         // Call FL Here, then the stuff below needs to happen inside the FL
176:         if (operation.amountToTransferIn > 0) {
177:             IERC20(operation.tokenToTransferIn).safeTransferFrom(
178:                 msg.sender,
179:                 address(this),
180:                 operation.amountToTransferIn
181:             );
182:         }
183: 
184:         // Take eBTC or stETH FlashLoan
185:         if (flType == FlashLoanType.eBTC) {
186:             IERC3156FlashLender(address(borrowerOperations)).flashLoan(
187:                 IERC3156FlashBorrower(address(this)),
188:                 address(ebtcToken),
189:                 borrowAmount,
190:                 abi.encode(operation)
191:             );
...... OMITTED ......
220:             ICdpManagerData.Cdp memory cdpInfo = cdpManager.Cdps(checkParams.cdpId);
221: 
222:             _doCheckValueType(checkParams.expectedDebt, cdpInfo.debt);
223:             _doCheckValueType(checkParams.expectedCollateral, cdpInfo.coll);
224:             require(
225:                 cdpInfo.status == checkParams.expectedStatus,
226:                 "!LeverageMacroReference: adjustCDP status check"
227:             );
228:         }
229: 
230:         // Post check type: Close, ensure it has the status we want
231:         if (postCheckType == PostOperationCheck.isClosed) {
232:             ICdpManagerData.Cdp memory cdpInfo = cdpManager.Cdps(checkParams.cdpId);
233: 
234:             require(
235:                 cdpInfo.status == checkParams.expectedStatus,
236:                 "!LeverageMacroReference: closeCDP status check"
237:             );
238:         }
239: 
240:         // Sweep here if it's Reference, do not if it's delegate
241:         if (willSweep) {
242:             sweepToCaller();
243:         }
244:     }
```

</details>

### [N-23] Consider bounding input array length

The functions below take in an unbounded array, and make function calls for entries in the array. While the function will revert if it eventually runs out of gas, it may be a nicer user experience to require() that the length of the array is below some reasonable maximum, so that the user doesn't have to use up a full transaction's gas only to see that the transaction reverts.

There are 2 instances:

- *LeverageMacroBase.sol* ( [435](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L435), [488](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L488) ):

```solidity
435:         for (uint256 i; i < swapLength; ) {

488:             for (uint256 i; i < length; ++i) {
```

### [N-24] Unused contract variables

The following state variables are defined but not used.
It is recommended to check the code for logical omissions that cause them not to be used. If it's determined that they are not needed anywhere, it's best to remove them from the codebase to improve code clarity and minimize confusion.

There is 1 instance:

- *LeverageZapRouterBase.sol* ( [18-18](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L18-L18) ):

```solidity
18:     uint256 internal constant PRECISION = 1e18;
```

### [N-25] Unused function parameters

Function parameters that are defined but not used may be logical errors and need to be checked to see if any logic is missing.
If the parameter is not really needed, it should be removed, otherwise it will repeatedly cause confusion and make code maintenance difficult.
If the parameter cannot be removed directly (for example, if it is an override function), its name should be removed and some comment can be appended.

There are 7 instances:

- *LeverageMacroBase.sol* ( [394-400](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L394-L400) ):

```solidity
/// @audit amount
/// @audit fee
394:     function onFlashLoan(
395:         address initiator,
396:         address token,
397:         uint256 amount,
398:         uint256 fee,
399:         bytes calldata data
400:     ) external returns (bytes32) {
```

- *LeverageZapRouterBase.sol* ( [65-71](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L65-L71) ):

```solidity
/// @audit flType
/// @audit borrowAmount
/// @audit operation
/// @audit postCheckType
/// @audit checkParams
65:     function doOperation(
66:         FlashLoanType flType,
67:         uint256 borrowAmount,
68:         LeverageMacroOperation calldata operation,
69:         PostOperationCheck postCheckType,
70:         PostCheckParams calldata checkParams
71:     ) external override {
```

### [N-26] Unused local variables

The following local variables are not used. It is recommended to check the code for logical omissions that cause them not to be used. If it's determined that they are not needed anywhere, it's best to remove them from the codebase to improve code clarity and minimize confusion.

There are 3 instances:

- *LeverageMacroBase.sol* ( [516-516](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L516-L516), [530-530](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L530-L530) ):

```solidity
516:         bytes32 _cdpId = borrowerOperations.openCdp(

530:         bytes32 _cdpId = borrowerOperations.openCdpFor(
```

- *LeverageZapRouterBase.sol* ( [280-280](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L280-L280) ):

```solidity
280:             bytes32 _cdpId = borrowerOperations.openCdpFor(
```

### [N-27] Consider using `delete` rather than assigning zero to clear values

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

There are 2 instances:

- *LeverageZapRouterBase.sol* ( [106-106](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L106-L106), [175-175](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L175-L175) ):

```solidity
106:         op.amountToTransferIn = 0;

175:         op.amountToTransferIn = 0;
```

### [N-28] Use the latest Solidity version

Upgrading to the [latest solidity version](https://github.com/ethereum/solc-js/tags) (0.8.19 for L2s) can optimize gas usage, take advantage of new features and improve overall contract efficiency. Where possible, based on compatibility requirements, it is recommended to use newer/latest solidity version to take advantage of the latest optimizations and features.

There are 4 instances:

- *LeverageMacroBase.sol* ( [2](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L2) ):

```solidity
2: pragma solidity 0.8.17;
```

- *EbtcLeverageZapRouter.sol* ( [2](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L2) ):

```solidity
2: pragma solidity ^0.8.17;
```

- *LeverageZapRouterBase.sol* ( [2](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L2) ):

```solidity
2: pragma solidity 0.8.17;
```

- *ZapRouterBase.sol* ( [2](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L2) ):

```solidity
2: pragma solidity 0.8.17;
```

### [N-29] Use transfer libraries instead of low level calls to transfer native tokens

Consider using [`SafeTransferLib.safeTransferETH`](https://github.com/transmissions11/solmate/blob/fadb2e2778adbf01c80275bfb99e5c14969d964b/src/utils/SafeTransferLib.sol#L15) or [`Address.sendValue`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/utils/Address.sol#L41) for clearer semantic meaning instead of using a low level `call`.

There is 1 instance:

- *ZapRouterBase.sol* ( [38-38](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L38-L38) ):

```solidity
38:         payable(address(stEth)).call{value: _initialETH}("");
```

### [N-30] Use a struct to encapsulate multiple function parameters

If a function has too many parameters, replacing them with a struct can improve code readability and maintainability, increase reusability, and reduce the likelihood of errors when passing the parameters.

<details>
<summary>There are 8 instances (click to show):</summary>

- *EbtcLeverageZapRouter.sol* ( [34-43](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L34-L43), [77-86](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L77-L86), [128-137](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L128-L137), [171-180](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L171-L180), [205-214](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L205-L214), [403-409](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L403-L409) ):

```solidity
34:     function openCdpWithEth(
35:         uint256 _debt,
36:         bytes32 _upperHint,
37:         bytes32 _lowerHint,
38:         uint256 _stEthLoanAmount,
39:         uint256 _ethMarginBalance,
40:         uint256 _stEthDepositAmount,
41:         bytes calldata _positionManagerPermit,
42:         TradeData calldata _tradeData
43:     ) external payable returns (bytes32 cdpId) {

77:     function openCdpWithWstEth(
78:         uint256 _debt,
79:         bytes32 _upperHint,
80:         bytes32 _lowerHint,
81:         uint256 _stEthLoanAmount,
82:         uint256 _wstEthMarginBalance,
83:         uint256 _stEthDepositAmount,
84:         bytes calldata _positionManagerPermit,
85:         TradeData calldata _tradeData
86:     ) external returns (bytes32 cdpId) {

128:     function openCdpWithWrappedEth(
129:         uint256 _debt,
130:         bytes32 _upperHint,
131:         bytes32 _lowerHint,
132:         uint256 _stEthLoanAmount,
133:         uint256 _wethMarginBalance,
134:         uint256 _stEthDepositAmount,
135:         bytes calldata _positionManagerPermit,
136:         TradeData calldata _tradeData
137:     ) external returns (bytes32 cdpId) {

171:     function openCdp(
172:         uint256 _debt,
173:         bytes32 _upperHint,
174:         bytes32 _lowerHint,
175:         uint256 _stEthLoanAmount,
176:         uint256 _stEthMarginAmount,
177:         uint256 _stEthDepositAmount,
178:         bytes calldata _positionManagerPermit,
179:         TradeData calldata _tradeData
180:     ) external returns (bytes32 cdpId) {

205:     function _openCdp(
206:         uint256 _debt,
207:         bytes32 _upperHint,
208:         bytes32 _lowerHint,
209:         uint256 _stEthLoanAmount,
210:         uint256 _stEthMarginAmount,
211:         uint256 _stEthDepositAmount,
212:         bytes calldata _positionManagerPermit,
213:         TradeData calldata _tradeData
214:     ) internal nonReentrant returns (bytes32 cdpId) {

403:     function _adjustCdp(
404:         bytes32 _cdpId,
405:         AdjustCdpParams memory _params,
406:         bytes calldata _positionManagerPermit,
407:         TradeData calldata _tradeData,
408:         uint256 _zapStEthBalanceBefore
409:     ) internal nonReentrant {
```

- *LeverageZapRouterBase.sol* ( [65-71](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L65-L71), [132-140](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L132-L140) ):

```solidity
65:     function doOperation(
66:         FlashLoanType flType,
67:         uint256 borrowAmount,
68:         LeverageMacroOperation calldata operation,
69:         PostOperationCheck postCheckType,
70:         PostCheckParams calldata checkParams
71:     ) external override {

132:     function _adjustCdpOperation(
133:         bytes32 _cdpId,
134:         FlashLoanType _flType,
135:         uint256 _flAmount,
136:         AdjustCdpOperation memory _cdp,
137:         uint256 debt,
138:         uint256 coll,
139:         TradeData calldata _tradeData
140:     ) internal {
```

</details>

### [N-31] Returning a struct instead of a bunch of variables is better

If a function returns [too many variables](https://docs.soliditylang.org/en/v0.8.21/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

There is 1 instance:

- *LeverageMacroBase.sol* ( [568-574](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L568-L574) ):

```solidity
568:     function excessivelySafeCall(
569:         address _target,
570:         uint256 _gas,
571:         uint256 _value,
572:         uint16 _maxCopy,
573:         bytes memory _calldata
574:     ) internal returns (bool, bytes memory) {
```

### [N-32] Contract variables should have comments

Consider adding some comments on non-public contract variables to explain what they are supposed to do. This will help for future code reviews.

There is 1 instance:

- *LeverageMacroBase.sol* ( [35-35](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L35-L35) ):

```solidity
35:     bool internal immutable willSweep;
```

### [N-33] Empty bytes check is missing

Passing empty bytes to a function can cause unexpected behavior, such as certain operations failing, producing incorrect results, or wasting gas. It is recommended to check that all byte parameters are not empty.

<details>
<summary>There are 11 instances (click to show):</summary>

- *LeverageMacroBase.sol* ( [394-400](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L394-L400) ):

```solidity
/// @audit data
394:     function onFlashLoan(
395:         address initiator,
396:         address token,
397:         uint256 amount,
398:         uint256 fee,
399:         bytes calldata data
400:     ) external returns (bytes32) {
```

- *EbtcLeverageZapRouter.sol* ( [34-43](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L34-L43), [77-86](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L77-L86), [128-137](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L128-L137), [171-180](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L171-L180), [255-259](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L255-L259), [268-272](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L268-L272), [336-341](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L336-L341), [354-359](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L354-L359), [372-377](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L372-L377), [390-395](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L390-L395) ):

```solidity
/// @audit _positionManagerPermit
34:     function openCdpWithEth(
35:         uint256 _debt,
36:         bytes32 _upperHint,
37:         bytes32 _lowerHint,
38:         uint256 _stEthLoanAmount,
39:         uint256 _ethMarginBalance,
40:         uint256 _stEthDepositAmount,
41:         bytes calldata _positionManagerPermit,
42:         TradeData calldata _tradeData
43:     ) external payable returns (bytes32 cdpId) {

/// @audit _positionManagerPermit
77:     function openCdpWithWstEth(
78:         uint256 _debt,
79:         bytes32 _upperHint,
80:         bytes32 _lowerHint,
81:         uint256 _stEthLoanAmount,
82:         uint256 _wstEthMarginBalance,
83:         uint256 _stEthDepositAmount,
84:         bytes calldata _positionManagerPermit,
85:         TradeData calldata _tradeData
86:     ) external returns (bytes32 cdpId) {

/// @audit _positionManagerPermit
128:     function openCdpWithWrappedEth(
129:         uint256 _debt,
130:         bytes32 _upperHint,
131:         bytes32 _lowerHint,
132:         uint256 _stEthLoanAmount,
133:         uint256 _wethMarginBalance,
134:         uint256 _stEthDepositAmount,
135:         bytes calldata _positionManagerPermit,
136:         TradeData calldata _tradeData
137:     ) external returns (bytes32 cdpId) {

/// @audit _positionManagerPermit
171:     function openCdp(
172:         uint256 _debt,
173:         bytes32 _upperHint,
174:         bytes32 _lowerHint,
175:         uint256 _stEthLoanAmount,
176:         uint256 _stEthMarginAmount,
177:         uint256 _stEthDepositAmount,
178:         bytes calldata _positionManagerPermit,
179:         TradeData calldata _tradeData
180:     ) external returns (bytes32 cdpId) {

/// @audit _positionManagerPermit
255:     function closeCdp(
256:         bytes32 _cdpId,
257:         bytes calldata _positionManagerPermit,
258:         TradeData calldata _tradeData
259:     ) external {

/// @audit _positionManagerPermit
268:     function closeCdpForWstETH(
269:         bytes32 _cdpId,
270:         bytes calldata _positionManagerPermit,
271:         TradeData calldata _tradeData
272:     ) external {

/// @audit _positionManagerPermit
336:     function adjustCdpWithEth(
337:         bytes32 _cdpId,
338:         AdjustCdpParams memory _params,
339:         bytes calldata _positionManagerPermit,
340:         TradeData calldata _tradeData
341:     ) external payable {

/// @audit _positionManagerPermit
354:     function adjustCdpWithWstEth(
355:         bytes32 _cdpId,
356:         AdjustCdpParams memory _params,
357:         bytes calldata _positionManagerPermit,
358:         TradeData calldata _tradeData
359:     ) external {

/// @audit _positionManagerPermit
372:     function adjustCdpWithWrappedEth(
373:         bytes32 _cdpId,
374:         AdjustCdpParams memory _params,
375:         bytes calldata _positionManagerPermit,
376:         TradeData calldata _tradeData
377:     ) external {

/// @audit _positionManagerPermit
390:     function adjustCdp(
391:         bytes32 _cdpId,
392:         AdjustCdpParams memory _params,
393:         bytes calldata _positionManagerPermit,
394:         TradeData calldata _tradeData
395:     ) external {
```

</details>

### [N-34] Function state mutability can be restricted to view

Function state mutability can be restricted to view

There are 2 instances:

- *LeverageMacroBase.sol* ( [39-39](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L39-L39), [277-277](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L277-L277) ):

```solidity
39:     function owner() public virtual returns (address) {

277:     function _doCheckValueType(CheckValueAndType memory check, uint256 valueToCheck) internal {
```

### [N-35] Missing event for critical changes

Events should be emitted when critical changes are made to the contracts.

<details>
<summary>There are 3 instances (click to show):</summary>

- *EbtcLeverageZapRouter.sol* ( [205-214](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L205-L214) ):

```solidity
205:     function _openCdp(
206:         uint256 _debt,
207:         bytes32 _upperHint,
208:         bytes32 _lowerHint,
209:         uint256 _stEthLoanAmount,
210:         uint256 _stEthMarginAmount,
211:         uint256 _stEthDepositAmount,
212:         bytes calldata _positionManagerPermit,
213:         TradeData calldata _tradeData
214:     ) internal nonReentrant returns (bytes32 cdpId) {
```

- *LeverageZapRouterBase.sol* ( [165-170](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L165-L170), [200-204](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L200-L204) ):

```solidity
165:     function _openCdpOperation(
166:         bytes32 _cdpId,
167:         OpenCdpForOperation memory _cdp,
168:         uint256 _flAmount,
169:         TradeData calldata _tradeData
170:     ) internal {

200:     function _closeCdpOperation(
201:         bytes32 _cdpId,
202:         uint256 _debt,
203:         TradeData calldata _tradeData
204:     ) internal {
```

</details>

### [N-36] Non-assembly method available

There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary. In addition, most assembly methods can be replaced by non-assembly methods, for example:
- `assembly{ g := gas() }` => `uint256 g = gasleft()`
- `assembly{ id := chainid() }` => `uint256 id = block.chainid`
- `assembly { r := mulmod(a, b, d) }` => `uint256 m = mulmod(x, y, k)`
- `assembly { size := extcodesize() }` => `uint256 size = address(a).code.length`
- etc.

There are 4 instances:

- *LeverageMacroBase.sol* ( [584-592](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L584-L592), [594-594](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L594-L594), [599-599](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L599-L599), [601-601](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L601-L601) ):

```solidity
584:             _success := call(
585:                 _gas, // gas
586:                 _target, // recipient
587:                 _value, // ether value
588:                 add(_calldata, 0x20), // inloc
589:                 mload(_calldata), // inlen
590:                 0, // outloc
591:                 0 // outlen
592:             )

594:             _toCopy := returndatasize()

599:             mstore(_returnData, _toCopy)

601:             returndatacopy(add(_returnData, 0x20), 0, _toCopy)
```

### [N-37] Consider adding emergency-stop functionality

Adding a way to quickly halt protocol functionality in an emergency, rather than having to pause individual contracts one-by-one, will make in-progress hack mitigation faster and much less stressful.

There is 1 instance:

- *EbtcLeverageZapRouter.sol* ( [18-18](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L18-L18) ):

```solidity
18: contract EbtcLeverageZapRouter is LeverageZapRouterBase {
```

### [N-38] Use the Modern Upgradeable Contract Paradigm

Modern smart contract development often employs upgradeable contract structures, utilizing proxy patterns like OpenZeppelin’s Upgradeable Contracts. This paradigm separates logic and state, allowing developers to amend and enhance the contract's functionality without altering its state or the deployed contract address. Transitioning to this approach enhances long-term maintainability.
Resolution: Adopt a well-established proxy pattern for upgradeability, ensuring proper initialization and employing transparent proxies to mitigate potential risks. Embrace comprehensive testing and audit practices, particularly when updating contract logic, to ensure state consistency and security are preserved across upgrades. This ensures your contract remains robust and adaptable to future requirements.

There is 1 instance:

- *EbtcLeverageZapRouter.sol* ( [18](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L18) ):

```solidity
18: contract EbtcLeverageZapRouter is LeverageZapRouterBase {
```

### [N-39] Large or complicated code bases should implement invariant tests

This includes: large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts.
Invariant fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold.
Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and invariant fuzzers may help significantly.

There is 1 instance:

- Global finding

### [N-40] Contracts should have all `public`/`external` functions exposed by `interface`s

All `external`/`public` functions should extend an `interface`. This is useful to ensure that the whole API is extracted and can be more easily integrated by other projects.

There are 2 instances:

- *LeverageMacroBase.sol* ( [26](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L26) ):

```solidity
26: abstract contract LeverageMacroBase {
```

- *LeverageZapRouterBase.sol* ( [15](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L15) ):

```solidity
15: abstract contract LeverageZapRouterBase is ZapRouterBase, LeverageMacroBase, ReentrancyGuard, IEbtcLeverageZapRouter {
```
