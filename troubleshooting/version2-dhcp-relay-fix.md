# Version 2 Troubleshooting – DHCP Relay Fix

## Issue
VLAN 30 clients behind R2 were not receiving DHCP configuration from R1 even though R2 itself successfully received an upstream IP address.

## Symptoms
- VLAN 30 clients did not obtain leases
- guest subnet users could not complete end-to-end testing
- R2 upstream connectivity appeared partially functional

## Root Cause
Two main issues were identified:

1. The SW2 uplink toward R2 was configured as a trunk while R2 G0/1 was operating as a plain Layer 3 interface, not an 802.1Q subinterface.
2. R1 did not initially have a route back to `192.168.30.0/24` via R2.

## Fix Implemented
- SW2 uplink toward R2 was changed from trunk mode to access mode in VLAN 30
- a static route to `192.168.30.0/24` via R2 was added on R1
- a default route toward R1 was added on R2
- NAT inside role was applied on the R1 interface facing R2 so guest traffic could still be translated toward the ISP

## Result
After the corrections, VLAN 30 clients successfully received DHCP leases through relay, resolved public DNS names, reached the simulated public web server, and were correctly blocked from internal branch subnets by the guest ACL on R2.

## Lessons Learned
This issue reinforced the importance of:

- matching switchport mode to the router interface design
- validating bidirectional routing for relayed subnets
- separating DHCP relay logic from access-control policy troubleshooting
