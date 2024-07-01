# [L-01] The `ZapRouterBase::_permitPositionManagerApproval` will not revert with appropriate Errors in case deadline is passed or signature is invalid
## Impact
Since the `_permitPositionManagerApproval` wraps the `borrowerOperations.permitPositionManagerApproval` inside a catch try scheme to prevent the frontrunning issues it will always revert with wrong error. this will make debugging really hard and confusing for users.
```text
BorrowerOperations: Only borrower account or approved position manager can OpenCdp on borrower's behalf
```
## Proof of Concept
Here is the proof in this test the reason to revert should be deadline has passed but instead we are reverting with wrong error message.
```javascript
    function test_WrongErrorDueToTryCatch() public {
        address user = TEST_FIXED_USER;

        // open a CDP
        test_ZapOpenCdp_WithRawEth_NoLeverage_NoFlippening();

        bytes32 _cdpIdToClose = sortedCdps.cdpOfOwnerByIndex(user, 0);

        vm.startPrank(user);
        IERC20(address(eBTCToken)).approve(address(zapRouter), type(uint256).max);

        // Generate signature to one-time approve zap
        IEbtcZapRouter.PositionManagerPermit
            memory pmPermit = _generateOneTimePermitFromFixedTestUser();

        vm.roll(2);
        vm.warp(1810); // deadline is passed

        vm.expectRevert(
            "BorrowerOperations: Only borrower account or approved position manager can OpenCdp on borrower's behalf"
        );
        zapRouter.closeCdp(_cdpIdToClose, pmPermit);
    }
```
## Tools Used
Manual Review

## Recommended Mitigation Steps
check if the position approval is made for Zap Router in catch and if its not approved revert with a Permit failure error message instead of the other one.

```diff
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
        {
+           return;
        } catch {
            /// @notice adding try...catch around to mitigate potential permit front-running
            /// see: https://www.trust-security.xyz/post/permission-denied
+           if (borrowerOperations.getPositionManagerApproval(msg.sender, address(this)) != 0) {
+               return;
+           }
        }
+       revert("Permit failure Due to Deadline or incorrect Signiture");
    }
```

# [NC-01] Lack of Zero Address Check for Constructors 

## Impact
The constructors for `ZapRouterBase` and `EbtcZapRouter` lack zero address checks for passed addresses for immutable `_borrowerOperations` contract and needed token contracts.

## Proof of Concept
`ZapRouterBase` constructor:
```javascript
constructor(address _borrowerOperations, IERC20 _wstEth, IERC20 _wEth, IStETH _stEth) {
        MIN_CHANGE = IMinChangeGetter(_borrowerOperations).MIN_CHANGE();
@>      wstEth = _wstEth;
@>      wrappedEth = _wEth;
@>      stEth = _stEth;
    }
```
`EbtcZapRouter` constructor:
```javascript
    constructor(
        IERC20 _wstEth,
        IERC20 _wEth,
        IStETH _stEth,
        IERC20 _ebtc,
        IBorrowerOperations _borrowerOperations,
        ICdpManager _cdpManager,
        address _owner
    ) ZapRouterBase(address(_borrowerOperations), _wstEth, _wEth, _stEth) {
@>      ebtc = _ebtc;
@>      borrowerOperations = _borrowerOperations;
@>      cdpManager = _cdpManager;
@>      owner = _owner;

        // Infinite Approvals @TODO: do these stay at max for each token?
        stEth.approve(address(borrowerOperations), type(uint256).max);
        stEth.approve(address(wstEth), type(uint256).max);
    }
```
## Tools Used
Manual Review

## Recommended Mitigation Steps
Add require condition to check for zero address

