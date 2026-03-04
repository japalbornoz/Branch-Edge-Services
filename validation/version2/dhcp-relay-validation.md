# DHCP Relay Validation – Version 2

## Objective
Validate that VLAN 30 clients behind R2 successfully receive DHCP configuration from the centralized DHCP server on R1 using DHCP relay.

## Scope
This validation confirms:

- R2 acts as the default gateway for VLAN 30
- R2 relays DHCP requests to R1 using `ip helper-address`
- R1 provides DHCP leases for the `192.168.30.0/24` subnet
- VLAN 30 clients receive correct gateway and DNS settings

## Topology Context
In Version 2, VLAN 30 was moved behind R2. R2 connects upstream to R1 using a routed link and forwards DHCP requests to R1.

## Key Configuration Elements
- R2 guest-facing interface: `192.168.30.1/24`
- R2 relay target: `ip helper-address 203.0.114.1`
- R1 DHCP pool: `VLAN30_GUEST`
- R1 route to `192.168.30.0/24` via R2

## Validation Steps
1. Set VLAN 30 clients to DHCP
2. Confirm clients receive addresses in `192.168.30.0/24`
3. Confirm default gateway is `192.168.30.1`
4. Confirm DNS server is `192.168.99.10`
5. Verify DHCP lease visibility on R1

## Expected Results
- VLAN 30 clients receive valid DHCP leases
- gateway is assigned as `192.168.30.1`
- DNS server is assigned as `192.168.99.10`
- lease appears in R1 DHCP bindings

## Observed Result
Validation succeeded. VLAN 30 clients received DHCP addressing through R2 relay and were able to use the assigned default gateway and DNS server values successfully.

## Supporting Evidence
Suggested evidence to include:
- VLAN 30 PC `ipconfig`
- R1 `show ip dhcp binding`
- R2 `show running-config`
