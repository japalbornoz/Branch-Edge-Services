# ACL Validation – Version 1

## Objective
Validate that access-control policy on R1 restricts STAFF and GUEST traffic according to the intended design while preserving required DNS and public access.

## Policy Summary

### VLAN 10 – IT
IT users are allowed broad access to internal services and public resources.

### VLAN 20 – STAFF
STAFF users are allowed to:

- use DNS
- access VLAN 10
- access public resources

STAFF users are denied:

- general access to VLAN 99 management/services resources

### VLAN 30 – GUEST
GUEST users are allowed to:

- send DNS queries to `192.168.99.10`
- access public resources

GUEST users are denied:

- access to VLAN 10
- access to VLAN 20
- general access to VLAN 99

## ACL Enforcement Points
- `VLAN20-IN` applied inbound on `GigabitEthernet0/1.20`
- `VLAN30-IN` applied inbound on `GigabitEthernet0/1.30`

## Validation Steps

### VLAN 20 – STAFF Tests
Allowed:
- public web server by IP
- public web server by name
- VLAN 10 host

Denied:
- DNS server by direct ICMP
- switch management SVI in VLAN 99

### VLAN 30 – GUEST Tests
Allowed:
- public web server by IP
- public web server by name

Denied:
- VLAN 10 host
- VLAN 20 host
- direct management subnet access

## Expected Result
- STAFF is restricted from general VLAN 99 access but retains DNS and internet access
- GUEST is restricted from internal branch networks while retaining DNS and internet access

## Observed Result
Validation succeeded. STAFF users were denied management/services subnet access while maintaining required DNS and public reachability. GUEST users were denied access to internal branch networks and allowed only DNS query traffic plus public internet access.

## Technical Note
Direct ICMP to the DNS server is not the same as permitting DNS application traffic. In this project, ACLs allowed DNS queries while still denying general ping access to restricted resources.

## Supporting Evidence
Suggested evidence to include:
- successful and failed client ping outputs
- R1 `show access-lists`
- R1 interface configuration showing ACL application
