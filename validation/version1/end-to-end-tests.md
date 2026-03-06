# End-to-End Tests – Version 1

## Objective
Summarize final pass/fail traffic behavior for the base Project 04 topology.

## Test Matrix

| Source | Destination | Expected | Result | Notes |
|---|---|---|---|---|
| VLAN 10 client | VLAN 10 gateway (`192.168.10.1`) | Pass | Pass | Local gateway reachable (baseline) |
| VLAN 10 client | VLAN 20 host (`192.168.20.21`) | Pass | Pass | IT can reach STAFF |
| VLAN 10 client | VLAN 30 host (`192.168.30.21`) | Fail | Fail | Guest isolation prevents return traffic to internal VLANs |
| VLAN 10 client | DNS-SRV (`192.168.99.10`) | Pass | Pass | IT can reach services subnet |
| VLAN 10 client | Public web server by IP (`198.51.100.10`) | Pass | Pass | NAT/PAT working |
| VLAN 10 client | Public web server by name (`www.branchlab.com`) | Pass | Pass | DNS + NAT working |
| VLAN 20 client | VLAN 20 gateway (`192.168.20.1`) | Pass | Pass | Local gateway reachable (baseline) |
| VLAN 20 client | VLAN 10 host (`192.168.10.22`) | Pass | Pass | STAFF can reach IT (initial loss observed in PT) |
| VLAN 20 client | VLAN 30 host (`192.168.30.22`) | Fail | Fail | Guest isolation prevents VLAN 20 ↔ VLAN 30 reachability |
| VLAN 20 client | DNS-SRV by ICMP (`192.168.99.10`) | Fail | Fail | Blocked by STAFF policy (VLAN 99 restricted) |
| VLAN 20 client | Public web server by name (`www.branchlab.com`) | Pass | Pass | DNS query permitted; public reachability preserved |
| VLAN 30 client | VLAN 30 gateway (`192.168.30.1`) | Pass | Pass | Local gateway reachable (baseline) |
| VLAN 30 client | VLAN 10 host (`192.168.10.21`) | Fail | Fail | Blocked by GUEST policy |
| VLAN 30 client | VLAN 20 host (`192.168.20.21`) | Fail | Fail | Blocked by GUEST policy |
| VLAN 30 client | DNS-SRV by ICMP (`192.168.99.10`) | Fail | Fail | Only DNS queries allowed; ICMP denied by policy |
| VLAN 30 client | Public web server by name (`www.branchlab.com`) | Pass | Pass | DNS query permitted; public reachability preserved |

## Summary
Version 1 testing confirmed that the branch edge services design successfully provided DHCP, DNS-supported name resolution, NAT/PAT internet reachability, and ACL-based access control. IT and STAFF retained required public access, while the guest VLAN was isolated from internal branch networks. Packet Tracer initial packet loss/timeouts were observed in some first attempts, but final reachability outcomes matched the intended policy.
