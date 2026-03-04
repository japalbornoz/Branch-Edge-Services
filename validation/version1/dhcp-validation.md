# DHCP Validation – Version 1

## Objective
Validate that R1 successfully provides DHCP services to VLAN 10, VLAN 20, and VLAN 30 clients in the base branch edge services design.

## Scope
This validation confirms that:

- clients in VLAN 10, VLAN 20, and VLAN 30 receive IP addresses from the correct subnet
- clients receive the correct default gateway
- clients receive the correct DNS server address
- DHCP pools on R1 operate as intended

## DHCP Server Role
In Version 1, R1 acts as the DHCP server for directly connected user VLANs:

- VLAN 10 – IT
- VLAN 20 – STAFF
- VLAN 30 – GUEST

## Expected DHCP Assignments

| VLAN | Subnet | Default Gateway | DNS Server |
|---|---|---|---|
| VLAN 10 | 192.168.10.0/24 | 192.168.10.1 | 192.168.99.10 |
| VLAN 20 | 192.168.20.0/24 | 192.168.20.1 | 192.168.99.10 |
| VLAN 30 | 192.168.30.0/24 | 192.168.30.1 | 192.168.99.10 |

## Validation Steps
1. Set one or more clients in each VLAN to DHCP
2. Verify assigned IP settings on the client
3. Confirm leases are visible on R1
4. Confirm the assigned default gateway matches the client VLAN
5. Confirm the assigned DNS server is `192.168.99.10`

## Verification Commands

### On R1
```bash
show ip dhcp binding
show ip dhcp pool
```
### On Clients
```bash
ipconfig
```
## Observed Result
Validation succeeded. Clients in VLAN 10, VLAN 20, and VLAN 30 successfully received DHCP addresses from the correct subnet. Each client also received the expected default gateway and DNS server values.

## Notes
During early Packet Tracer testing, endpoint DHCP behavior was inconsistent until the router was rebuilt cleanly. After rebuilding R1 and reapplying the configuration step by step, DHCP behavior became stable and leases were correctly recorded.

## Supporting Evidence
- client ipconfig output for one host in each VLAN
- R1 `show ip dhcp binding`
- R1 `show ip dhcp pool`
