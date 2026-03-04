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
Validation succeeded. Internal clients in the permitted VLANs were able to reach the public server, and NAT translations were created on R1 as expected.

## Supporting Evidence
- successful client ping to `198.51.100.10`
- successful client ping to `www.branchlab.com`
- `show ip nat translations`
- `show ip nat statistics`
