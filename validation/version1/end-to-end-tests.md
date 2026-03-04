# End-to-End Tests – Version 1

## Objective
Summarize final pass/fail traffic behavior for the base Project 04 topology.

## Test Matrix

| Source | Destination | Expected | Result | Notes |
|---|---|---|---|---|
| VLAN 10 client | VLAN 10 gateway | Pass | Pass | Local gateway reachable |
| VLAN 10 client | VLAN 20 host | Pass | Pass | IT allowed to reach STAFF |
| VLAN 10 client | DNS-SRV | Pass | Pass | IT allowed to reach services subnet |
| VLAN 10 client | Public web server by IP | Pass | Pass | NAT/PAT working |
| VLAN 10 client | Public web server by name | Pass | Pass | DNS + NAT working |
| VLAN 20 client | VLAN 99 management/services by ICMP | Fail | Fail | Blocked by STAFF ACL |
| VLAN 20 client | Public web server by IP | Pass | Pass | Public access preserved |
| VLAN 20 client | Public web server by name | Pass | Pass | DNS query permitted |
| VLAN 30 client | VLAN 10 host | Fail | Fail | Blocked by GUEST ACL |
| VLAN 30 client | VLAN 20 host | Fail | Fail | Blocked by GUEST ACL |
| VLAN 30 client | VLAN 99 management/services by ICMP | Fail | Fail | Restricted by GUEST policy |
| VLAN 30 client | Public web server by IP | Pass | Pass | Public access preserved |
| VLAN 30 client | Public web server by name | Pass | Pass | DNS query permitted |

## Summary
Version 1 testing confirmed that the branch edge services design successfully provided DHCP, DNS, NAT/PAT, and access-control enforcement across segmented VLANs. Approved traffic was preserved while restricted internal traffic was denied according to policy.
