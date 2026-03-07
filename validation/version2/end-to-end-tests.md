# End-to-End Tests – Version 2

## Objective
Summarize final pass/fail traffic behavior for the Project 04 Version 2 topology (remote guest subnet with DHCP relay).

## Test Matrix

| Source | Destination | Expected | Result | Notes |
|---|---|---|---|---|
| VLAN 30 client | DHCP lease via relay | Pass | Pass | Client received `192.168.30.21/24` with GW `192.168.30.1` |
| VLAN 30 client | Public web server by name (`www.branchlab.com`) | Pass | Pass | DNS resolution + NAT working (warm-up timeouts observed initially) |
| VLAN 30 client | Internal VLAN 20 host (`192.168.20.21`) | Fail | Fail | Blocked by guest policy on R2 |
| R1 | DHCP binding for R2 WAN (`203.0.114.2`) | Pass | Pass | Confirms R2 received upstream DHCP lease |
| R1 | STAFF ACL applied inbound on `g0/1.20` | Pass | Pass | `VLAN20-IN` present and applied as intended |
| R2 | GUEST ACL applied inbound on `g0/1` | Pass | Pass | `VLAN30-IN` present and applied as intended |

## Summary
Version 2 testing confirmed that VLAN 30 could be relocated behind R2 while still receiving centralized DHCP from R1 through DHCP relay. Guest users retained DNS-supported public access while being denied access to internal branch VLANs through policy enforcement at the remote gateway (R2).
