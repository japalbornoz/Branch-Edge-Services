# ACL Validation – Version 1

## Objective
Validate that access-control policy on R1 restricts STAFF and GUEST traffic according to the intended design while preserving required DNS and public access.

## Policy Summary

### VLAN 10 – IT
IT users have broad access to internal resources and public resources.
Note: due to the GUEST isolation policy on VLAN 30, IT-to-GUEST ICMP may fail because return traffic from VLAN 30 to internal VLANs is denied.

### VLAN 20 – STAFF
STAFF users are allowed to:
- access VLAN 10
- resolve hostnames via DNS queries to the DNS server
- access public resources

STAFF users are denied:
- general access to VLAN 99 management/services resources (including ICMP to DNS-SRV)

### VLAN 30 – GUEST
GUEST users are allowed to:
- resolve hostnames via DNS queries to the DNS server
- access public resources

GUEST users are denied:
- access to VLAN 10
- access to VLAN 20
- general access to VLAN 99 management/services resources

## ACL Enforcement Points
- `VLAN20-IN` applied inbound on `GigabitEthernet0/1.20`
- `VLAN30-IN` applied inbound on `GigabitEthernet0/1.30`

## Validation Steps
1. From an IT client (VLAN 10), test reachability to STAFF, GUEST, and DNS-SRV
2. From a STAFF client (VLAN 20), test reachability to IT, GUEST, DNS-SRV by ICMP, and public web by name
3. From a GUEST client (VLAN 30), test reachability to IT, STAFF, DNS-SRV by ICMP, and public web by name

## Expected Result
- VLAN 20 (STAFF) is denied general access to VLAN 99 but retains DNS name resolution and public access
- VLAN 30 (GUEST) is denied access to internal VLANs while retaining DNS name resolution and public access
- VLAN 10 (IT) can reach internal services and STAFF; IT-to-GUEST may fail due to guest return traffic restrictions

## Observed Result
Validation succeeded. STAFF users were restricted from VLAN 99 by ICMP while maintaining public reachability by hostname. GUEST users were isolated from internal branch VLANs while maintaining public reachability by hostname. IT users could reach STAFF and DNS-SRV but could not complete ICMP to GUEST due to the guest isolation policy.

## Technical Note
DNS query permission (UDP/TCP port 53) does not imply ICMP (ping) permission to the DNS server. In this design, STAFF and GUEST VLANs can still resolve www.branchlab.com and reach the public server by name even though direct ICMP to 192.168.99.10 is denied by policy.

---

## Supporting Evidence
### VLAN 10 (IT) – PC1 Tests

#### IT → STAFF (Allowed)
```text
C:\>ping 192.168.20.21

Reply from 192.168.20.21: bytes=32 time=5ms TTL=127
Reply from 192.168.20.21: bytes=32 time<1ms TTL=127
Reply from 192.168.20.21: bytes=32 time<1ms TTL=127
Reply from 192.168.20.21: bytes=32 time<1ms TTL=127

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

#### IT → GUEST (Not reachable due to GUEST return traffic restrictions)
```
C:\>ping 192.168.30.21

Request timed out.
Request timed out.
Request timed out.
Request timed out.

Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```
#### IT → DNS-SRV (Allowed)
```
C:\>ping 192.168.99.10

Reply from 192.168.99.10: bytes=32 time<1ms TTL=127
Reply from 192.168.99.10: bytes=32 time<1ms TTL=127
Reply from 192.168.99.10: bytes=32 time<1ms TTL=127
Reply from 192.168.99.10: bytes=32 time<1ms TTL=127

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

---

### VLAN 20 (STAFF) – PC3 Tests
#### STAFF → IT (Allowed; initial packet loss observed)
```
C:\>ping 192.168.10.22

Request timed out.
Reply from 192.168.10.22: bytes=32 time<1ms TTL=127
Reply from 192.168.10.22: bytes=32 time<1ms TTL=127
Reply from 192.168.10.22: bytes=32 time<1ms TTL=127

Packets: Sent = 4, Received = 3, Lost = 1 (25% loss)
```

#### STAFF → GUEST (Not reachable due to GUEST isolation policy)
```
C:\>ping 192.168.30.22

Request timed out.
Request timed out.
Request timed out.
Request timed out.

Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

#### STAFF → DNS-SRV by ICMP (Denied; policy blocks VLAN 99)
```
C:\>ping 192.168.99.10

Reply from 192.168.20.1: Destination host unreachable.
Reply from 192.168.20.1: Destination host unreachable.
Reply from 192.168.20.1: Destination host unreachable.
Reply from 192.168.20.1: Destination host unreachable.

Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

#### STAFF → Public by hostname (Allowed; DNS queries permitted)
```
C:\>ping www.branchlab.com

Reply from 198.51.100.10: bytes=32 time<1ms TTL=126
Reply from 198.51.100.10: bytes=32 time<1ms TTL=126
Reply from 198.51.100.10: bytes=32 time<1ms TTL=126
Reply from 198.51.100.10: bytes=32 time<1ms TTL=126

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

---

### VLAN 30 (GUEST) – PC5 Tests
#### GUEST → IT (Denied)
```
C:\>ping 192.168.10.21

Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.

Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

#### GUEST → STAFF (Denied)
```
C:\>ping 192.168.20.21

Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.

Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

#### GUEST → DNS-SRV by ICMP (Denied; only DNS queries are permitted)
```
C:\>ping 192.168.99.10

Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.

Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```


#### GUEST → Public by hostname (Allowed; DNS queries permitted)
```
C:\>ping www.branchlab.com

Reply from 198.51.100.10: bytes=32 time<1ms TTL=126
Reply from 198.51.100.10: bytes=32 time<1ms TTL=126
Reply from 198.51.100.10: bytes=32 time<1ms TTL=126
Reply from 198.51.100.10: bytes=32 time<1ms TTL=126

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

---

### Router Evidence (R1)
#### ACL Definitions and Match Counters
```
R1#sh access-lists 
Standard IP access list 1
    10 permit 192.168.10.0 0.0.0.255 (32 match(es))
    20 permit 192.168.20.0 0.0.0.255 (24 match(es))
    30 permit 192.168.30.0 0.0.0.255 (16 match(es))
    40 permit 192.168.99.0 0.0.0.255
Extended IP access list VLAN20-IN
    10 permit udp 192.168.20.0 0.0.0.255 host 192.168.99.10 eq domain (1 match(es))
    20 permit tcp 192.168.20.0 0.0.0.255 host 192.168.99.10 eq domain
    30 deny ip 192.168.20.0 0.0.0.255 192.168.99.0 0.0.0.255 (4 match(es))
    40 permit ip 192.168.20.0 0.0.0.255 any (16 match(es))
Extended IP access list VLAN30-IN
    10 permit udp 192.168.30.0 0.0.0.255 host 192.168.99.10 eq domain (1 match(es))
    20 permit tcp 192.168.30.0 0.0.0.255 host 192.168.99.10 eq domain
    30 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255 (8 match(es))
    40 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255 (7 match(es))
    50 deny ip 192.168.30.0 0.0.0.255 192.168.99.0 0.0.0.255 (4 match(es))
    60 permit ip 192.168.30.0 0.0.0.255 any (4 match(es))
```

#### ACL Application on Subinterfaces (Inbound)

##### VLAN 20 ACL applied on G0/1.20
```
R1#sh ip interface g0/1.20 | include access list
  Outgoing access list is not set
  Inbound  access list is VLAN20-IN
```

##### VLAN 30 ACL applied on G0/1.30
```
R1#sh ip interface g0/1.30 | include access list
  Outgoing access list is not set
  Inbound  access list is VLAN30-IN
```
