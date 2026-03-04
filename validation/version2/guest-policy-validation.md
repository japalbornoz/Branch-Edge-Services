# Guest Policy Validation – Version 2

## Objective
Validate that guest users in VLAN 30 can access approved services while being denied access to internal branch subnets.

## Policy Summary
Guest users are allowed to:

- query the DNS server on `192.168.99.10`
- access public/internet resources

Guest users are denied access to:

- VLAN 10
- VLAN 20
- general access to VLAN 99

## ACL Enforcement Point
In Version 2, guest policy is enforced on R2 inbound on the VLAN 30 gateway interface.

## Validation Tests

### Allowed Tests
- ping public web server by IP: `198.51.100.10`
- ping public web server by name: `www.branchlab.com`

### Denied Tests
- ping VLAN 10 host
- ping VLAN 20 host
- ping management subnet resources directly

## Expected Results
- public web access succeeds
- name resolution works
- internal subnet reachability fails

## Observed Result
Validation succeeded. VLAN 30 users were able to reach the public web server and resolve its hostname, while traffic to internal branch subnets was denied by the guest access-control policy.

## Technical Note
DNS query permission does not imply ICMP permission to the DNS server itself. If DNS resolution works while direct ping to the DNS server fails, that behavior is consistent with the ACL design.

## Supporting Evidence
Suggested evidence to include:
- guest client ping to `198.51.100.10`
- guest client ping to `www.branchlab.com`
- failed guest ping to `192.168.20.21`
- R2 ACL configuration
