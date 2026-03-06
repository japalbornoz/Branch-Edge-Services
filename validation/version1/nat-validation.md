# NAT Validation – Version 1

## Objective
Validate that R1 performs NAT overload (PAT) for internal clients accessing the simulated internet/public server network.

## Scope
This validation confirms that:

- inside client traffic is translated when exiting toward the ISP
- multiple internal VLANs can share the public-facing interface on R1
- internal clients can reach the public web server by IP and by name
- NAT statistics and translation entries are visible on R1

## NAT Design
R1 performs NAT overload for internal branch networks using its WAN interface.

### NAT Outside
- `GigabitEthernet0/0`

### NAT Inside
- `GigabitEthernet0/1.10`
- `GigabitEthernet0/1.20`
- `GigabitEthernet0/1.30`
- `GigabitEthernet0/1.99`

### Inside Networks Matched by ACL 1
- `192.168.10.0/24`
- `192.168.20.0/24`
- `192.168.30.0/24`
- `192.168.99.0/24`

## Validation Steps
1. Generate traffic from internal clients toward `198.51.100.10`
2. Verify successful public reachability
3. Check NAT translation entries on R1
4. Check NAT statistics on R1

## Verification Commands

### On R1
```bash
show ip nat translations
show ip nat statistics
```

### On Clients
```bash
ping 198.51.100.10
ping www.branchlab.com
```

## Expected Result
- internal clients successfully reach the public web server
- NAT translations appear on R1
- inside local addresses are translated through the WAN interface

## Observed Result
Validation succeeded. Internal clients in the permitted VLANs was translated using NAT overload on R1, and dynamic translations were visible during active testing.

## Supporting Evidence
### R1 NAT Translations
```
R1#show ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
icmp 203.0.113.2:13    192.168.10.21:13   198.51.100.10:13   198.51.100.10:13
icmp 203.0.113.2:14    192.168.10.21:14   198.51.100.10:14   198.51.100.10:14
icmp 203.0.113.2:15    192.168.10.21:15   198.51.100.10:15   198.51.100.10:15
icmp 203.0.113.2:16    192.168.10.21:16   198.51.100.10:16   198.51.100.10:16
```
```
R1#show ip nat statistics
Total translations: 4 (0 static, 4 dynamic, 4 extended)
Outside Interfaces: GigabitEthernet0/0
Inside Interfaces: GigabitEthernet0/1.10 , GigabitEthernet0/1.20 , GigabitEthernet0/1.30 , GigabitEthernet0/1.99
Hits: 26  Misses: 28
Expired translations: 24
Dynamic mappings:
```

### Client Traffic Generation (Example)

Traffic was generated from an internal client (example shown in NAT table: `192.168.10.21`) by pinging the public web server 198.51.100.10 and hostname `www.branchlab.com`.

### Additional Validation Note

NAT/PAT behavior was also tested from other internal VLANs (VLAN 20 and VLAN 30). Results were consistent: public reachability succeeded and translations were created on R1. Only one example translation table is included here to avoid repeating identical outputs.
