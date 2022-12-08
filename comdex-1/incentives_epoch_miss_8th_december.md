## Incident Report for Failure of farming reward distribution

## Date: December 08, 2022

## Time: 5:13:57 UTC


## Details:

The farming reward distribution was scheduled to take place on December 08, 2022, at 5:13:57 UTC.
The distribution of rewards requires oracle prices to calculate the value of shares for each farmer in the master and child pools.
The band relayer stopped working around December 07, 12:46:50 UTC, resulting in the oracle not being updated with the latest prices.
Due to a recent modification in the infrastructure, the notification server failed to notify the team of the issue with the band relayer.
As a result, the distribution time passed before the team was able to address the issue.

### Risks and Hazards:
The failure of the farming reward distribution resulted in farmers not receiving their rewards for the designated epoch.
The failure of the band relayer and notification server put the integrity of the farming rewards system at risk.

### Mitigation:
The missed epoch will be extended to the next batch, effectively extending the incentive period by one day (from December 21 to December 22).
The team will investigate the root cause of the failure and implement measures to prevent similar incidents from occurring in the future.

### Recommendations:
Regular monitoring and maintenance of the band relayer and notification server should be implemented to ensure their proper functioning.
Back-up systems should be implemented to ensure that farming rewards can be distributed in the event of a failure of the primary systems.
Regular communication and coordination between the relevant teams (oracle, relayer, notification) should be established to prevent future incidents.

