# Guest Policy Validation – Version 2

## Objective
Validate that guest users in VLAN 30 can access approved services (DNS + public internet) while being denied access to internal branch subnets.

## Policy Summary
Guest users (VLAN 30) are allowed to:
- resolve `www.branchlab.com` via the internal DNS server
- access public resources (simulated internet)

Guest users are denied access to:
- access to internal branch VLANs (VLAN 10, VLAN 20)
- general access to management/services resources

## Enforcement Point
In Version 2, guest policy is enforced on **R2**, applied inbound on the VLAN 30 gateway interface.

## Verification Commands (VLAN 30 PC5)
```text
ping www.branchlab.com
ping 192.168.20.21
```

## Supporting Evidence
### VLAN 30 PC5 → Public by Hostname (Allowed)
```
C:\>ping www.branchlab.com

Pinging 198.51.100.10 with 32 bytes of data:

Request timed out.
Request timed out.
Reply from 198.51.100.10: bytes=32 time<1ms TTL=125
Reply from 198.51.100.10: bytes=32 time<1ms TTL=125

Ping statistics for 198.51.100.10:
    Packets: Sent = 4, Received = 2, Lost = 2 (50% loss)
```
```
C:\>ping www.branchlab.com

Pinging 198.51.100.10 with 32 bytes of data:

Reply from 198.51.100.10: bytes=32 time<1ms TTL=125
Reply from 198.51.100.10: bytes=32 time<1ms TTL=125
Reply from 198.51.100.10: bytes=32 time<1ms TTL=125
Reply from 198.51.100.10: bytes=32 time<1ms TTL=125

Ping statistics for 198.51.100.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

### VLAN 30 PC5 → Internal VLAN 20 Host (Denied)
```
C:\>ping 192.168.20.21

Pinging 192.168.20.21 with 32 bytes of data:

Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.
Reply from 192.168.30.1: Destination host unreachable.

Ping statistics for 192.168.20.21:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```

### ACL Application Evidence
```
R1#sh ip interface g0/1.20 | include access list
  Outgoing access list is not set
  Inbound  access list is VLAN20-IN
```
```
R2#show ip interface g0/1 | include access list
  Outgoing access list is not set
  Inbound  access list is VLAN30-IN
```

## Observed Result
Validation succeeded. VLAN 30 clients were able to reach the public server by hostname while being denied access to internal branch VLAN resources. Guest restrictions were enforced at the correct policy point (R2, inbound on the VLAN 30 gateway interface).

## Technical Note
Initial timeouts are common in Packet Tracer due to ARP/NAT/DNS warm-up. Subsequent successful replies confirm correct name resolution and reachability.
