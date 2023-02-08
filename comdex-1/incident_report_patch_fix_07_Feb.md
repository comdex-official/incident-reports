## Incident Report for Increase in Stability fees on the Harbor Protocol during the v8.1.0 Software Upgrade.
## Date: February 06, 2023
## Time: 15:30:00 UTC
## Details:

The Stability Fee of Vaults A, B and C (ATOM & OSMO ) were supposed to be reduced to 1%, 0.5%, and 0.25%, respectively. However, the Stability fee was changed by mistake to 100%, 50%, and 25% through upgrade code. The team identified this issue and immediately started working on the fix once the upgrade was done. 

The interest on CMST loan started accruing on all active vaults (ATOM, OSMO vaults) at a high stability fees until this issue got resolved(~20 hrs). Also, one vault got liquidated during this period while the fix was being implemented.

## Risks and Hazards:
The high stability fees did not accrue much interest by the time the fix was implemented. (100% annual rate is equivalent to ~0.23%/day). As the issue was resolved within a day, its impact was minimized.

## Mitigation:
The impacted users will receive compensation for their losses based on the following list-
[Click to view list](https://docs.google.com/spreadsheets/d/1ERxr6b0pmNHwJUIYcm-72zS3X4Qi37JJo7axo7SAyww/edit#gid=0)


## Recommendations:
The team has rectified the issue and will prevent such incidents occurring in the future.


