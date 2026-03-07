# DHCP Relay Validation – Version 2

## Objective
Validate that VLAN 30 clients behind R2 successfully receive DHCP configuration from the centralized DHCP server on R1 using DHCP relay.

## Scope
This validation confirms:

- VLAN 30 clients receive IP addresses in `192.168.30.0/24`
- the default gateway for VLAN 30 clients is `192.168.30.1` (R2)
- R1 records DHCP leases for VLAN 30 clients
- R2 obtains upstream connectivity via DHCP on the routed link to R1 (`203.0.114.0/30`)

## Topology Context
In Version 2, VLAN 30 (GUEST) is moved behind R2. R2 acts as the default gateway for VLAN 30 and relays DHCP requests to R1 using `ip helper-address`. R1 remains the centralized DHCP server.

## Verification Commands

### On VLAN 30 PC5
```text
ipconfig
```

### On R1
```
show ip dhcp binding
```

### Supporting Evidence
### VLAN 30 PC5 DHCP Lease (Example)
```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   IPv4 Address....................: 192.168.30.21
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: 192.168.30.1
```

### R1 DHCP Bindings (VLAN 30 and R2 WAN Lease)
```
R1#show ip dhcp binding
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.10.21    0000.0010.AAAA           --                     Automatic
192.168.10.22    0000.0010.BBBB           --                     Automatic
192.168.20.22    0000.0020.CCCC           --                     Automatic
192.168.20.23    0000.0020.DDDD           --                     Automatic
192.168.30.21    0000.0030.FFFF           --                     Automatic
192.168.30.22    0000.0030.EEEE           --                     Automatic
203.0.114.2      0002.1602.B703           --                     Automatic
```

## Observed Result
Validation succeeded. VLAN 30 clients received DHCP leases in `192.168.30.0/24` with default gateway `192.168.30.1` (R2). R1 recorded VLAN 30 client leases and also issued an upstream DHCP lease (`203.0.114.2`) for R2 on the routed link.
