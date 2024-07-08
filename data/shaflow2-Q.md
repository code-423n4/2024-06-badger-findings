# [L-1] Malicious attackers steal the owner's transaction and prevent the owner from rescuing tokens

## impact
Owner can rescue tokens stored in router due to unexpected situations, using the sweepToCaller() function
github:[https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L246C1-L263C6](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-protocol/packages/contracts/contracts/LeverageMacroBase.sol#L246C1-L263C6)
```solidity
    /// @notice Sweep away tokens if they are stuck here
    function sweepToCaller() public {
        _assertOwner();
        /**
         * SWEEP TO CALLER *
         */
        // Safe unchecked because known tokens
        uint256 ebtcBal = ebtcToken.balanceOf(address(this));
        uint256 collateralBal = stETH.sharesOf(address(this));

        if (ebtcBal > 0) {
            ebtcToken.transfer(msg.sender, ebtcBal);
        }

        if (collateralBal > 0) {
            stETH.transferShares(msg.sender, collateralBal);
        }
    }
```
Malicious individuals can monitor and preempt the owner's transaction by opening a CDP position to obtain tokens in the contract, as all tokens will be forwarded to the caller before the transaction ends
github:[https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L196](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/LeverageZapRouterBase.sol#L196)
```solidity
    function _openCdpOperation(
        bytes32 _cdpId,
        OpenCdpForOperation memory _cdp,
        uint256 _flAmount,
        TradeData calldata _tradeData
    ) internal {

        ...

        _sweepEbtc();
        _sweepStEth();
    }
```
For example, Alice mistakenly sent 1000 stETH to the router contract. The owner of the router contract called sweepToCaller() to prepare for the rescue token and then sent it to Alice. Bob monitored the owner's transaction and obtained Alice's token through openCdp

## Recommended Mitigation Steps
Use emergency pause mode, or compare the changes in stETH and eBTC before and after the transaction ends. If it is greater than 0, send the excess to the caller

# [L-2] The contract cannot rescue the remaining ETH in the router
## impact
Although the contract imposes strict limitations on accepting ether, there are still two ways in which the router contract can retain ether
github:[https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L112](https://github.com/code-423n4/2024-06-badger/blob/9173558ee1ac8a78a7ae0a39b97b50ff0dd9e0f8/ebtc-zap-router/src/EbtcLeverageZapRouter.sol#L112)
```solidity
    receive() external payable {
        require(
            msg.sender == address(wrappedEth),
            "EbtcLeverageZapRouter: only allow Wrapped ETH to send Ether!"
        );
    }
```
The first one is through 0X interaction  

The second one is to use selfdestruct to force the sending of eth

No one can retrieve these ETH locked in the contract
## Recommended Mitigation Steps
Add a function to retrieve the locked ETH in the contract
```solidity
    function sweepETH(address token, uint256 amount) public {
        _assertOwner();
        msg.sender.transfer(address(this).balance);
    }
```