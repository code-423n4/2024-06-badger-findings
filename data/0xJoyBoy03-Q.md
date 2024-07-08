# [L-1] The zero debt amount for opening a cdp conflicts with the `borrowerOperation::_openCdp` function

## Description
users might want to provide coll and no debt for opening a cdp and the `EbtcLeverageZapRouter::_openCdp` function won't prevent users to providing the debt as zero amount. but in the `borrowerOperation::_openCdp` function, the debt provided should be at least the minimum and it will revert to the 0 amount because of the `_requireMinDebt(_debt);` line

## Mitigation
since the `EbtcLeverageZapRouter` contract is in scope, prevent users with debt as zero amount