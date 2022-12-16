## Incident Report for Failure of Remaining Collateral not being distributed back to the Liquidated Vault owners in the Collateral Auction mechanism 
## Date: December 15, 2022
## Time: 00:50:10 UTC
## Details:

Due to the vaults being undercollateralized i.e. the vault had less collateral than the minimum required Collateralization ratio, the liquidation of the vault was triggered. As a result, the collateral was sold in the auction using a Dutch-style liquidation mechanism, in which the price starts at a 20% premium and gradually decreases. The remaining collateral, if any, is returned to the vault owner if the protocol reaches the Target CMST that was borrowed from the protocol (including the liquidation penalty). But this wasn't the case, and the remaining amount was being sent to a random, invalid address. The team discovered that five users were impacted by this occurrence, and they will receive compensation for their losses.

## Risks and Hazards:
The failure of the remaining collateral not going back to the vault owner results in a potential loss to the vault owner as he loses the amount which should have essentially been added to their wallet. This puts the integrity of the entire Harbor protocol at risk as auctions are the essential part of the system.

## Mitigation:
The five vault owners who had been liquidated and did not receive the remaining collateral will be compensated. The team will implement measures to prevent similar incidents from occurring in the future. The below table contains the wallet addresses of the vault owners along with the amount to be compensated.


## Recommendations:
The team has rectified the issue and will implement more test cases to find such bugs in initial stages to prevent such incidents occurring in the future on the Mainnet.
