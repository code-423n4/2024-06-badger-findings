---
sponsor: "BadgerDAO"
slug: "2024-06-badger"
date: "2024-08-05"
title: "eBTC Zap Router"
findings: "https://github.com/code-423n4/2024-06-badger-findings/issues"
contest: 401
---

# Overview

## About C4

Code4rena (C4) is an open organization consisting of security researchers, auditors, developers, and individuals with domain expertise in smart contracts.

A C4 audit is an event in which community participants, referred to as Wardens, review, audit, or analyze smart contract logic in exchange for a bounty provided by sponsoring projects.

During the audit outlined in this document, C4 conducted an analysis of the Badger eBTC Zap Router smart contract system written in Solidity. The audit took place between June 24 — July 8, 2024.

## Wardens

16 Wardens contributed reports to Badger eBTC Zap Router:

  1. [0xBinChook](https://code4rena.com/@0xBinChook)
  2. [jesjupyter](https://code4rena.com/@jesjupyter)
  3. [Nexarion](https://code4rena.com/@Nexarion)
  4. [debo](https://code4rena.com/@debo)
  5. [pep7siup](https://code4rena.com/@pep7siup)
  6. [DemoreX](https://code4rena.com/@DemoreX)
  7. [Greed](https://code4rena.com/@Greed)
  8. [alix40](https://code4rena.com/@alix40)
  9. [Chad0](https://code4rena.com/@Chad0)
  10. [Rhaydden](https://code4rena.com/@Rhaydden)
  11. [chaduke](https://code4rena.com/@chaduke)
  12. [SBSecurity](https://code4rena.com/@SBSecurity) ([Slavcheww](https://code4rena.com/@Slavcheww) and [Blckhv](https://code4rena.com/@Blckhv))
  13. [lian886](https://code4rena.com/@lian886)
  14. [Drynooo](https://code4rena.com/@Drynooo)
  15. [stacey](https://code4rena.com/@stacey)

This audit was judged by [0xsomeone](https://code4rena.com/@0xsomeone).

Final report assembled by [thebrittfactor](https://twitter.com/brittfactorC4).

# Summary

The C4 analysis yielded an aggregated total of 2 unique vulnerabilities, receiving a risk rating in the category of MEDIUM severity.

Additionally, C4 analysis included 2 reports detailing issues with a risk rating of LOW severity or non-critical.

All of the issues presented here are linked back to their original finding.

# Scope

The code under review can be found within the [C4 Badger eBTC Zap Router repository](https://github.com/code-423n4/2024-06-badger), and is composed of 5 smart contracts written in the Solidity programming language and includes 1006 lines of Solidity code.

# Severity Criteria

C4 assesses the severity of disclosed vulnerabilities based on three primary risk categories: high, medium, and low/non-critical.

High-level considerations for vulnerabilities span the following key areas when conducting assessments:

- Malicious Input Handling
- Escalation of privileges
- Arithmetic
- Gas use

For more information regarding the severity criteria referenced throughout the submission review process, please refer to the documentation provided on [the C4 website](https://code4rena.com), specifically our section on [Severity Categorization](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization).

# Medium Risk Findings (2)
## [[M-01] Incorrect comparison logic in post-operation checks](https://github.com/code-423n4/2024-06-badger-findings/issues/31)
*Submitted by [Nexarion](https://github.com/code-423n4/2024-06-badger-findings/issues/31), also found by [chaduke](https://github.com/code-423n4/2024-06-badger-findings/issues/42), [SBSecurity](https://github.com/code-423n4/2024-06-badger-findings/issues/41), [lian886](https://github.com/code-423n4/2024-06-badger-findings/issues/27), [Drynooo](https://github.com/code-423n4/2024-06-badger-findings/issues/26), [stacey](https://github.com/code-423n4/2024-06-badger-findings/issues/21), [jesjupyter](https://github.com/code-423n4/2024-06-badger-findings/issues/18), and [Rhaydden](https://github.com/code-423n4/2024-06-badger-findings/issues/50)*

The `_doCheckValueType` function, which is crucial for post-operation validation, contains reversed comparison logic for the `gte` (greater than or equal to) and `lte` (less than or equal to) operators. This reversal causes the function to perform the opposite checks than intended, potentially allowing operations to pass validation when they should fail, or fail when they should pass.

This issue could lead to:
1. Incorrect validation of CDP (Collateralized Debt Position) states after operations.
2. Potential manipulation of the system by exploiting these misaligned checks.

### Proof of Concept

The issue is located in the `_doCheckValueType` function of the LeverageMacroBase contract:

<https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L277-L289>

Specifically, the problematic comparisons are:

```solidity
} else if (check.operator == Operator.gte) {
    require(check.value >= valueToCheck, "!LeverageMacroReference: gte post check");
} else if (check.operator == Operator.lte) {
    require(check.value <= valueToCheck, "!LeverageMacroReference: let post check");
}
```

These comparisons are reversed and should be:

```solidity
} else if (check.operator == Operator.gte) {
    require(valueToCheck >= check.value, "!LeverageMacroReference: gte post check");
} else if (check.operator == Operator.lte) {
    require(valueToCheck <= check.value, "!LeverageMacroReference: lte post check");
}
```

### Recommended Mitigation Steps

To fix this issue:
1. Reverse the comparisons in the `_doCheckValueType` function for the `gte` and `lte` operators.
2. Update the function as follows:

```solidity
function _doCheckValueType(CheckValueAndType memory check, uint256 valueToCheck) internal {
    if (check.operator == Operator.skip) {
        // Early return
        return;
    } else if (check.operator == Operator.gte) {
        require(valueToCheck >= check.value, "!LeverageMacroReference: gte post check");
    } else if (check.operator == Operator.lte) {
        require(valueToCheck <= check.value, "!LeverageMacroReference: lte post check");
    } else if (check.operator == Operator.equal) {
        require(check.value == valueToCheck, "!LeverageMacroReference: equal post check");
    } else {
        revert("Operator not found");
    }
}
```

### Assessed type

Context

**[wtj2021 (Badger) acknowledged via duplicate Issue #18](https://github.com/code-423n4/2024-06-badger-findings/issues/18#event-13539025504)**

**[0xsomeone (judge) increased severity to High](https://github.com/code-423n4/2024-06-badger-findings/issues/31#issuecomment-2244049094)**

**[GalloDaSballo (Badger) commented](https://github.com/code-423n4/2024-06-badger-findings/issues/31#issuecomment-2259923000):**
 > We disagree that the finding is of High Severity and believe it's logically equivalent to a lack of a slippage check, more commonly classified as Medium Severity.
> 
> Additionally a slippage check is implicitly present when using 0x for swaps.
> 
> The check is a important check to have but a lack of it working well doesn't open up to meaningful losses due to other safeguards.

**[0xsomeone (judge) commented](https://github.com/code-423n4/2024-06-badger-findings/issues/31#issuecomment-2261023660):**
 > @GalloDaSballo - My initial judgment of this submission as `high` was based on the fact that the code performs an invalid operation and is a sensitive dependency with a high likelihood of vulnerability re-surfacing if left unpatched due to its presence in a utility library. In detail, the impact would be considered medium but the incidence itself merits a "high" rating as it is incorrectly executed every time the function is invoked.
> 
> Given that the function is present in a generic library, its impact would vary depending on the library's usage and it is not unreasonable to assume that, if left unpatched, it would lead to a higher severity vulnerability surfacing. 
> 
> I believe a high-severity rating is justified based on the above analysis, however, I am keen to hear more feedback especially if you believe that any of my statements are incorrect.

**[GalloDaSballo (Badger) commented](https://github.com/code-423n4/2024-06-badger-findings/issues/31#issuecomment-2261069153):**
 > From our perspective the check is tightly coupled with [these parameters](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L207-L229).
> 
> And was meant to be a generic way to perform checks on those values not to be re-used in other contracts.

**[0xsomeone (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2024-06-badger-findings/issues/31#issuecomment-2261542998):**
 > After closely reviewing the codebase, the function does not appear to be utilized anywhere else except for the `LeverageMacroBase` contract itself. Based on the fact that the demonstratable vulnerability would be capped at medium severity and the prospective vulnerabilities that can arise from this code flaw are not readily apparent or guaranteed, I will proceed with downgrading this submission to medium risk. 
> 
> This decision is final and no further feedback is expected in relation to this submission.

***

## [[M-02] Staking ETH incorrectly assumes revert bubbling](https://github.com/code-423n4/2024-06-badger-findings/issues/14)
*Submitted by [0xBinChook](https://github.com/code-423n4/2024-06-badger-findings/issues/14), also found by [debo](https://github.com/code-423n4/2024-06-badger-findings/issues/44), [pep7siup](https://github.com/code-423n4/2024-06-badger-findings/issues/37), [DemoreX](https://github.com/code-423n4/2024-06-badger-findings/issues/35), [Greed](https://github.com/code-423n4/2024-06-badger-findings/issues/28), [alix40](https://github.com/code-423n4/2024-06-badger-findings/issues/25), and [Chad0](https://github.com/code-423n4/2024-06-badger-findings/issues/22)*

When `EbtcLeverageZapRouter::openCdpWithEth` and `EbtcLeverageZapRouter::adjustCdpWithEth` stake ETH with Lido, the underlying function performing the call assumes that any exceptions (from Lido) will be bubbled up, but with call being a low level rather than a Solidity function, it is not the case.

Lido stETH has two cases where a revert on depositing ETH for staking may be encountered, when deposits are paused or when staking limits are enabled and it is exceeded.

If Lido returns a revert it is ignored and the entire CDP flow continues (permits, flash loans, account synching, CDP initialization, eBTC minting), with a revert only being cause only by one of the last step of the flow, moving the collateral to the active pool.

### Proof of Concept

The two parts to the issue:
1. Any possibility of reverts during staking ETH with Lido.
2. Incorrect assumption that revert would bubble.

**1. Lido deposit could revert:**

Lido has pausable staking on [Ethereum stETH](<https://etherscan.io/address/0x17144556fd3424edc8fc8a4c940b2d04936d17eb#code#F1#L324>)

```solidity
    /**
     * @notice Stops accepting new Ether to the protocol
     *
     * @dev While accepting new Ether is stopped, calls to the `submit` function,
     * as well as to the default payable function, will revert.
     *
     * Emits `StakingPaused` event.
     */
    function pauseStaking() external {
```

Lido can impose a per block stake limit on [Ethereum stETH](https://etherscan.io/address/0x17144556fd3424edc8fc8a4c940b2d04936d17eb#code#F1#L368)

```solidity
     * @dev Reverts if:
     * - `_maxStakeLimit` == 0
     * - `_maxStakeLimit` >= 2^96
     * - `_maxStakeLimit` < `_stakeLimitIncreasePerBlock`
     * - `_maxStakeLimit` / `_stakeLimitIncreasePerBlock` >= 2^32 (only if `_stakeLimitIncreasePerBlock` != 0)
     *
     * Emits `StakingLimitSet` event
     *
     * @param _maxStakeLimit max stake limit value
     * @param _stakeLimitIncreasePerBlock stake limit increase per single block
     */
    function setStakingLimit(uint256 _maxStakeLimit, uint256 _stakeLimitIncreasePerBlock) external {
```

Both these cause the same effect; a possible revert during the submit of ETH for staking with [Ethereum stETH](https://etherscan.io/address/0x17144556fd3424edc8fc8a4c940b2d04936d17eb#code#F1#L922):

```solidity
    function _submit(address _referral) internal returns (uint256) {
        require(msg.value != 0, "ZERO_DEPOSIT");

        StakeLimitState.Data memory stakeLimitData = STAKING_STATE_POSITION.getStorageStakeLimitStruct();
        // There is an invariant that protocol pause also implies staking pause.
        // Thus, no need to check protocol pause explicitly.
@>      require(!stakeLimitData.isStakingPaused(), "STAKING_PAUSED");

        if (stakeLimitData.isStakingLimitSet()) {
            uint256 currentStakeLimit = stakeLimitData.calculateCurrentStakeLimit();

@>          require(msg.value <= currentStakeLimit, "STAKE_LIMIT");
```

**2. Incorrect assumption that revert would bubble:**

`EbtcLeverageZapRouter` and `EbtcZapRouter` both use the same ancestor function to stake ETH with Lido,
that performs the `call` operation on a `address` in [`ZapRouterBase::_depositRawEthIntoLido`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L34-L41)

```solidity
    function _depositRawEthIntoLido(uint256 _initialETH) internal returns (uint256) {
        // check before-after balances for 1-wei corner case
        uint256 _balBefore = stEth.balanceOf(address(this));
        // TODO call submit() with a referral?
@>      payable(address(stEth)).call{value: _initialETH}("");
        uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
        return _deposit;
    }
```

As outlined in [Solidity by Example - call](https://solidity-by-example.org/call/)

```text
call is a low level function to interact with other contracts.

This is the recommended method to use when you're just sending Ether via calling the fallback function.

However, it is not the recommended way to call existing functions.
Few reasons why low-level call is not recommended

    Reverts are not bubbled up
    Type checks are bypassed
    Function existence checks are omitted
```

`EbtcZapRouter` does check the collateral balance in the function that follows the staking of ETH with Lido in [`EbtcZapRouter::_openCdpWithPermit()`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcZapRouter.sol#L444-L447)

```require
        require(
            stEth.balanceOf(address(this)) >= _stEthBalance,
            "EbtcZapRouter: not enough collateral for open!"
        );
```

`EbtcLeverageZapRouter` performs no similar checks on the collateral balance after the staking, rather failing on a ERC20 transfer after another \~320K gas of other operations.

### Test Case

Adds a pause to the `CollateralTokenTester` and demonstrates the non-propagation of the initial revert, instead relying on the later ERC20 revert.

The `CollateralTokenTester` mocks `Lido stETH`, add the following to the version used by the `ebtc-zap-router` tests (should be at `/lib/ebtc/packages/contracts/TestContracts/CollateralTokenTester.sol`):

```diff 
-    function deposit() public payable {
+    bool private depositsArePaused;
+
+    function pauseDeposits() external {
+        depositsArePaused = true;
+    }
+
+    function deposit() public payable {
+        require(!depositsArePaused, "STAKING_PAUSED");
```

Add to [LeverageZaps](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/test/LeverageZaps.t.sol#L534):

```solidity
    function test_ZapOpenCdp_WithEth_LidoRReverts() external {
        seedActivePool();

        // Pausing deposits to mimic Lido's pauseStaking() or stakeLimit being set & exceeded
        CollateralTokenTester(collateral).pauseDeposits();

        // Logic equivalent to that from createLeveragedPosition(MarginType.ETH)
        // Extracted to test correctly using expectRevert()
        address user = vm.addr(userPrivateKey);
        uint256 _debt = 1e18;
        uint256 flAmount = _debtToCollateral(_debt);
        uint256 marginAmount = 5 ether;
        vm.deal(user, type(uint96).max);
        IEbtcZapRouter.PositionManagerPermit memory pmPermit = createPermit(user);
        vm.prank(user);

        // Fails on the last step of BorrowerOperations::_openCdp(); transfer of collateral to the active pool
        vm.expectRevert("ERC20: transfer amount exceeds balance");
        _openTestCdp(MarginType.ETH, _debt, flAmount, marginAmount, pmPermit);
    }
```

Run with `forge test --match-contract LeverageZaps --match-test test_ZapOpenCdp_WithEth_LidoRReverts`.

### Tools Used

Foundry

### Recommended Mitigation Steps

Use submit instead, as that will propagate any revert.

In [`ZapRouterBase::\_depositRawEthIntoLido()`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L34-L41):

```diff
    function _depositRawEthIntoLido(uint256 _initialETH) internal returns (uint256) {
        // check before-after balances for 1-wei corner case
        uint256 _balBefore = stEth.balanceOf(address(this));
        // TODO call submit() with a referral?
-       payable(address(stEth)).call{value: _initialETH}("");
+       stETH.submit{value: _initialETH}(address(0));
        uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
        return _deposit;
    }
```

The `msg.sender` could also be included for the referral, as that will be included by Lido their emitted event.

```diff
    function _depositRawEthIntoLido(uint256 _initialETH, address _referral) internal returns (uint256) {
        // check before-after balances for 1-wei corner case
        uint256 _balBefore = stEth.balanceOf(address(this));
        // TODO call submit() with a referral?
-       payable(address(stEth)).call{value: _initialETH}("");
+       stETH.submit{value: _initialETH}(_referral);
        uint256 _deposit = stEth.balanceOf(address(this)) - _balBefore;
        return _deposit;
    }
```

**[GalloDaSballo (Badger) confirmed and commented](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2221207728):**
 > We agree that the finding is valid, but believe it should be downgraded to QA. The check is missing and will cause the delta returned to be 0.
> 
> The stETH amount is checked a few lines after, and a 0 amount, or a 0 change will cause every operation to revert due to insufficient change amount:
> 
> Open Cdp would revert [here](https://github.com/ebtc-protocol/ebtc-zap-router/blob/4b37cce03c16b5e5caf5a9aab0bab5d344a587d2/src/EbtcLeverageZapRouter.sol#L217-L218):
> 
> ```solidity
>         _requireAtLeastMinNetStEthBalance(_stEthDepositAmount - LIQUIDATOR_REWARD);
> ```
> 
> 
> On [`adjustCDP`](https://github.com/ebtc-protocol/ebtc-zap-router/blob/4b37cce03c16b5e5caf5a9aab0bab5d344a587d2/src/EbtcLeverageZapRouter.sol#L411-L414):
> 
> ```solidity
>         _requireZeroOrMinAdjustment(_params.debtChange);
>         _requireZeroOrMinAdjustment(_params.stEthBalanceChange);
>         _requireZeroOrMinAdjustment(_params.stEthMarginBalance);
> ```
> 
> The operation will also revert later once it tries to send an insufficient amount of tokens to the protocol.
> 
> For these reasons, we will fix the bug, but believe it's QA.

**[0xsomeone (judge) increased severity to High and commented](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2244047190):**
 > The Warden has demonstrated how a low-level interaction with the `stETH` system has not adequately handled the `bool` flag yielded which would indicate whether the call was successful or not, causing `revert` errors to be ignored.
> 
> I am not sure I fully agree with the Sponsor, as I believe certain code paths and CDP operations would result in the vulnerability regardless of the additional security checks imposed in the routers.
> 
> Specifically, a healthy CDP that has its debt increased and collateral added to it simultaneously via the ETH or WETH to stETH deposit flows would satisfy all conditions and be executed. Here's a quick PoC that can be added to the `LeverageZaps.t.sol` file:
> 
> ```sol
> function test_marginErrorSilent() public {
>     seedActivePool();
> 
>     (address user, bytes32 cdpId) = createLeveragedPosition(MarginType.stETH);
> 
>     IEbtcZapRouter.PositionManagerPermit memory pmPermit = createPermit(user);
> 
>     uint256 debtChange = 1e18;
>     uint256 marginIncrease = 0.5e18;
>     uint256 collValue = _debtToCollateral(debtChange) * COLLATERAL_BUFFER / 10000;
>     uint256 flAmount = _debtToCollateral(debtChange);
> 
>     _before();
>     vm.startPrank(user);
>     leverageZapRouter.adjustCdpWithEth{value: marginIncrease}(
>         cdpId, 
>         _getAdjustCdpParams(flAmount, int256(debtChange), int256(collValue), int256(marginIncrease), false),
>         abi.encode(pmPermit), 
>         _getExactInDebtToCollateralTradeData(debtChange)
>     );
>     vm.stopPrank();
>     _after();
> 
>     // Test zap fee
>     assertEq(eBTCToken.balanceOf(testFeeReceiver), (1e18 + debtChange) * defaultZapFee / 10000); 
> 
>     // Demonstrate a non-zero balance remains
>     assertEq(address(leverageZapRouter).balance, 0);
> 
>     _checkZapStatusAfterOperation(user);
> }
> ```
> 
> I believe that the above PoC sufficiently justifies a high severity risk rating as the funds transmitted alongside the call are lost and a CDP adjustment can leave the position in a worse-off state than the caller expected due to the unprocessed margin. To note, more PoCs can be observed in [#37](https://github.com/code-423n4/2024-06-badger-findings/issues/37) and [#22](https://github.com/code-423n4/2024-06-badger-findings/issues/22) that demonstrate the flaw and the possibility that funds will indeed be locked in the router.
> 
> A subset of submissions has been marked as no-reward due to lacking sufficient justification as to why the low-level interaction must be validated and how it can be exploited. Simply pointing out that the low-level call remains unchecked is insufficient for this particular vulnerability as it is identical to re-hashing static analysis output without properly understanding its ramifications.
> 
> To note, the primary submission of this duplicate set might change after PJQA as this submission details the vulnerability nicely but fails to demonstrate a valid exploitation path in its PoC.

**[Slavcheww (warden) commented](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2245594254):**
 > @0xsomeone - I believe that the above PoC sufficiently justifies a high severity risk rating as the funds transmitted alongside the call are lost and a CDP adjustment can leave the position in a worse-off state than the caller expected due to the unprocessed margin.
> 
> It's not valid, as like @GalloDaSballo stated and it's just visible, if you adjust the position by 0 nothing worse can happen. In addition, there is a slippage ensuring that the position is as the user wants it, and a worse condition as you stated will be impossible.
> 
> What should the provided test show? This perhaps: `demonstrate the flaw and the possibility that funds will indeed be locked in the router.`
> 
> This is also an invalid assumption because the test will fail if the balance is not 0, since the assertion is for equality.
> `assertEq(address(leverageZapRouter).balance, 0);`
>
> *Note: to view the provided image, please see the original comment [here](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2245594254).*
> 
> Also, the probability of a low-level stETH call revert due to a pause or deposit limit is very unlikely. The current staking limit is set at 150,000 ETH, which makes it even less likely to happen. Reference [this doc](https://docs.lido.fi/guides/lido-tokens-integration-guide#staking-rate-limits).

**[0xsomeone (judge) commented](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2245713409):**
 > @Slavcheww, thank you for your feedback! The screenshot shared does not properly apply the PoC described as you did not perform the necessary adjustments in the `CollateralTokenTester` (i.e., `stETH`) contract for it to `revert`. As it is misleading, I strongly advise revising your latest feedback.
> 
> The PoC shared alongside the `CollateralTokenTester` adjustment demonstrates that the funds transmitted alongside the call were lost. As such, the vulnerability can lead to direct fund loss.
> 
> The likelihood of a `stETH` call reverting was deemed as medium-risk given that it has historically been breached ([example](https://dailycoin.com/lido-activates-staking-rate-limit-as-eth-deposits-spike/)) and is set in place for a reason. 
> 
> While I am more than happy to hear opinions on and debate whether this vulnerability should be deemed medium-risk or high-risk, it is clear that the vulnerability exists. 

**[Slavcheww (warden) commented](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2248715312):**
 > @0xsomeone - I rechecked the whole flow and yes, the problem is there indeed.
> 
> > The likelihood of a stETH call reverting was deemed as medium-risk given that it has historically been breached ([example](https://dailycoin.com/lido-activates-staking-rate-limit-as-eth-deposits-spike/)) and is set in place for a reason.
> 
> This article only announces that the stETH `depositLimit` is enabled at 150,000 ETH per day, which I already said. The article also clarifies that this 150 000 ETH has been set, because in the past one address deposited over 150,000 in a day and it is highly unlikely to happen again. So it only remains to happen if stETH is paused. With the user also providing a slippage for what they want to deposit, the severity of this should be Medium at maximum, in my opinion.

**[0xsomeone (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2259392756):**
 > @Slavcheww - The slippage the user provides when it comes to leverage is not sufficient in protecting against this attack as evidenced by the PoCs.
> 
> I do understand the concerns around the likelihood of the vulnerability manifesting, however, the result of its manifestation is a direct and complete loss of native funds. Additionally, `stETH` is upgradeable and we cannot be sure of what the future holds with deposit limitations as it is an external project and not related to Badger. 
> 
> Nevertheless, after consulting with a fellow judge I have decided to indeed reduce the severity of this submission to medium-risk. The low likelihood of the vulnerability manifesting would necessitate a critical impact (i.e. total loss of funds) which is not demonstrated here as we have a high impact (i.e. loss of funds for the caller at that particular time).

**[GalloDaSballo (Badger) commented](https://github.com/code-423n4/2024-06-badger-findings/issues/14#issuecomment-2259923823):**
 > We agree with the decision above, going through various scenarios, not all scenarios would cause a loss of funds; however, some would and all are conditional on stETH being paused.

***

# Low Risk and Non-Critical Issues

For this audit, 2 reports were submitted by wardens detailing low risk and non-critical issues. The [report highlighted below](https://github.com/code-423n4/2024-06-badger-findings/issues/48) by **Rhaydden** received the top score from the judge.

*The following wardens also submitted reports: [jesjupyter](https://github.com/code-423n4/2024-06-badger-findings/issues/47).*

## [01] ZapRouterBase contract won't be eligible for referral rewards from Lido for depositing ETH

*We communicated with the sponsors to confirm if the protocol plans to accrue referral rewards from LIDO but they replied "not at the moment" and also went ahead to state that any issues that could affect future implementation (although no immediate plan) could be reported in QA.*

### Impact

The ZapRouterBase contract won't be eligible for referral rewards from Lido when depositing ETH, potentially leading to a significant loss of value for the protocol. Lido has a reward program. 

Read more about the Lido's reward programs using this links below:
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

## [02] Missing flash loan return value check in `doOperation` function

The `doOperation` function does not check the return value of the `flashLoan` function, which can cause undetected failures in the flash loan process. 

### Proof of Concept

According to `IERC3156FlashLender.sol` (out of scope for this audit), the `flashLoan` function contains a return value that the caller should check. However, the `_doOperation` function initiates the flash loan but does not check the return value:

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

### Recommended Mitigation Steps

Modify the `_doOperation` function to check the return value of the `flashLoan` function and ensure it matches `FLASH_LOAN_SUCCESS`.

## [03] Reentrancy could occur in flash loan callback functions

The lack of reentrancy protection in the flash loan callback functions (`_openCdpCallback`, `_adjustCdpCallback`, `_closeCdpCallback`) can allow an attacker to reenter the contract during execution, potentially leading to multiple CDP openings, adjustments, closures or other malicious acts.

### Proof of Concept

Look at these part of the `LeverageMacroBase.sol` contract `_openCdpCallback`:

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

## [04] Fee calculation in `LeverageZapRouterBase` could lead to excessive fees being charged and inaccurate CDP debt

This can result in users paying higher fees than intended and having their CDPs reflect a higher debt than the actual eBTC received. 

### Proof of Concept

The problem is present in both the `_openCdpForCallback` and `_adjustCdpCallback` functions. Looking at the `_openCdpForCallback` function: 

https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L276-L292

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
- The user actually receives `flData.eBTCToMint - (flData.eBTCToMint * zapFeeBPS / BPS)`.

For example, if a user wants to mint 100 eBTC and the `zapFeeBPS` is 50 (0.5%):
1. The CDP is opened with a debt of 100 eBTC.
2. 0.5 eBTC is sent to the `zapFeeReceiver`.
3. The user only receives 99.5 eBTC.

This could be costly in scenarios where the user is close to the liquidation threshold. They might believe they have more buffer than they actually do, potentially leading to unexpected liquidations as the case may be.

### Recommended Mitigation Steps

Recalculate the eBTC amount to mint or adjust before performing the CDP operation, subtracting the fee amount first. Then, open or adjust the CDP with this reduced amount, ensuring the CDP's debt accurately reflects the eBTC the user receives. Finally, transfer the calculated fee to the `zapFeeReceiver`.

## [05] Lack of slippage protection in swap operations exposes users to excessive slippage

The current implementation of swap operations in the `LeverageZapRouterBase` contract lacks proper slippage protection, potentially exposing users to significant financial losses due to unfavorable trade execution.

### Proof of Concept

Look at the `_getSwapOperations` function:

https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L232-L247

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

Always enforce slippage protection for swap operations by removing the `performSwapChecks` condition. Implement on-chain logic to calculate a reasonable `minOut` value based on current market conditions and a configurable maximum allowed slippage percentage. Consider using a price oracle to validate that the executed swap price is within acceptable bounds.

## [06] Potential overflow in `_getPostCheckParams` function 

If `_totalCollateral` and `_collValidationBuffer` are large enough, their multiplication could overflow before the division by `BPS`, resulting in an incorrect `expectedCollateral` value. This could cause the function to return invalid post-check parameters.

### Proof of Concept

Look at the [`_getPostCheckParams`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L257-L274) function:

```solidity
expectedCollateral: CheckValueAndType({
    value:  _totalCollateral * _collValidationBuffer / BPS,
    operator: Operator.gte
}),
```

Looking at the order of operations, if `_totalCollateral` and `_collValidationBuffer` are large enough, their multiplication could exceed the maximum value represented by `uint256`, causing an overflow. This overflow would occur before the division by `BPS`, leading to an incorrect `expectedCollateral` value.

### Recommended Mitigation Steps

To prevent the potential overflow, divide `_collValidationBuffer` by `BPS` before multiplying it with `_totalCollateral`.

## [07] Potential rebasing token transfer inconsistency in ZapRouterBase

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

## [08] An invariant is broken which relates to Position Manager Permit not being revoked after operations, allowing unauthorized access to user CDPs

According to the [ReadMe](https://github.com/code-423n4/2024-06-badger#main-invariants), 

> Main invariants:<br>
> Position manager permit needs to be revoked after each operation

The failure to revoke the position manager permit after each operation leaves users' CDPs vulnerable to unauthorized access. This breaks the main security invariant and could potentially allow the `ZapRouter` contract or malicious actors to manipulate users' positions without their consent in subsequent transactions.

### Proof of Concept

In the ZapRouterBase.sol contract, there's a function to grant the position manager permit:

https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/ZapRouterBase.sol#L115-L133

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

In `LeverageZapRouterBase.sol`, operations like [`_adjustCdpOperation`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L132-L163), [`_openCdpOperation`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L165-L198), and [`_closeCdpOperation`](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L200-L230) use these permissions but do not revoke them afterward:

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

## [09] Incorrect NatSpec comment in `adjustCdpWithWstEth` function leading to potential misunderstanding

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

## [10] Fix typos

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

## [11] Incorrect error message in `_doCheckValueType` for `lte` operator

The incorrect error message in the `_doCheckValueType` function for the `lte` operator can lead to confusion when debugging or handling errors. The message currently reads `"let post check"` instead of `"lte post check"`.

### Proof of Concept

The `_doCheckValueType` function is intended to check if a value meets certain conditions based on the operator provided. However, there is a typo in the error message for the `lte` operator.

Look at [this part](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L284) of the code:

```solidity
        require(check.value <= valueToCheck, "!LeverageMacroReference: let post check");
```

The problem here is with the error message for the `lte` check. The message currently reads `"let post check"` instead of `"lte post check"`.

### Recommended Mitigation Steps

```diff
-        require(valueToCheck <= check.value, "!LeverageMacroReference: let post check");
+        require(valueToCheck <= check.value, "!LeverageMacroReference: lte post check");
```

***

# Disclosures

C4 is an open organization governed by participants in the community.

C4 audits incentivize the discovery of exploits, vulnerabilities, and bugs in smart contracts. Security researchers are rewarded at an increasing rate for finding higher-risk issues. Audit submissions are judged by a knowledgeable security researcher and solidity developer and disclosed to sponsoring developers. C4 does not conduct formal verification regarding the provided code but instead provides final verification.

C4 does not provide any guarantee or warranty regarding the security of this project. All smart contract software should be used at the sole risk and responsibility of users.
