# End-to-End Tests – Version 2

## Objective
Summarize the final pass/fail traffic outcomes for the Version 2 topology.

## Test Matrix

| Source | Destination | Expected | Result | Notes |
|---|---|---|---|---|
| VLAN 30 client | DHCP lease from R1 via R2 relay | Pass | Pass | Client received correct subnet, gateway, and DNS settings |
| VLAN 30 client | `198.51.100.10` | Pass | Pass | Public web server reachable |
| VLAN 30 client | `www.branchlab.com` | Pass | Pass | DNS resolution and public reachability both succeeded |
| VLAN 30 client | VLAN 20 host | Fail | Fail | Blocked by guest ACL on R2 |
| VLAN 30 client | VLAN 10 host | Fail | Fail | Blocked by guest ACL on R2 |
| VLAN 30 client | Internal management access | Fail | Fail | Restricted by guest policy |

## Summary
Version 2 testing confirmed that the remote guest subnet remained fully functional for DHCP, DNS, and internet access while staying isolated from internal branch resources.
