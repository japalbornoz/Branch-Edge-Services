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

### On Clients
```text
ipconfig
```

### On R1
```text
show ip dhcp binding
show ip dhcp pool
```

## Supporting Evidence
- client ipconfig output for one host in each VLAN
- R1 `show ip dhcp binding`
- R1 `show ip dhcp pool`

### Client DHCP Evidence (ipconfig)

#### VLAN 10 Client (example output)
```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..:
   Link-local IPv6 Address.........: FE80::201:96FF:FEBB:A2EB
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.10.21
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     192.168.10.1
```

#### R1 DHCP Bindings
```
R1#sh ip dhcp binding 
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.10.21    0000.0010.AAAA           --                     Automatic
192.168.10.22    0000.0010.BBBB           --                     Automatic
192.168.20.21    0000.0020.CCCC           --                     Automatic
192.168.20.22    0000.0020.DDDD           --                     Automatic
192.168.30.21    0000.0030.EEEE           --                     Automatic
192.168.30.22    0000.0030.FFFF           --                     Automatic
```

#### R1 DHCP Pool Status
```
R1#show ip dhcp pool

Pool VLAN10_IT :
 Total addresses                : 254
 Leased addresses               : 2
 Excluded addresses             : 4

Pool VLAN20_STAFF :
 Total addresses                : 254
 Leased addresses               : 2
 Excluded addresses             : 4

Pool VLAN30_GUEST :
 Total addresses                : 254
 Leased addresses               : 2
 Excluded addresses             : 4
```

## Observed Result

Validation succeeded. Clients in VLAN 10, VLAN 20, and VLAN 30 received DHCP addresses from the correct subnet with the expected default gateways. R1 recorded leases in `show ip dhcp binding`, and each DHCP pool showed the correct lease count in `show ip dhcp pool`.
