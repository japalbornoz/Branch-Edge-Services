# Validation Index

This folder contains validation evidence for both Project 04 Version 1 and Version 2.

## Version 1 Validation
Version 1 validates the base branch edge services design where R1 provides:

- router-on-a-stick inter-VLAN routing
- DHCP
- DNS reachability
- NAT/PAT for internet access
- ACL-based user restrictions

Files:
- `v1/dhcp-validation.md`
- `v1/dns-validation.md`
- `v1/nat-validation.md`
- `v1/acl-validation.md`
- `v1/end-to-end-tests.md`

## Version 2 Validation
Version 2 validates the extended design where VLAN 30 is moved behind R2 and uses DHCP relay back to R1.

Files:
- `v2/dhcp-relay-validation.md`
- `v2/guest-policy-validation.md`
- `v2/end-to-end-tests.md`
