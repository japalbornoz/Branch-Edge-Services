# CCNA Project 04 – Branch Edge Services: DHCP, NAT, ACL, and Internet Access Control

## Overview
This project simulates a small branch office edge network in Cisco Packet Tracer. The lab demonstrates how a branch router can provide core user services such as DHCP, inter-VLAN routing, NAT/PAT for outbound internet access, ACL-based traffic restrictions, and DNS-supported name resolution.

The topology includes multiple user VLANs connected to a branch switch, a branch router acting as the default gateway and policy enforcement point, an ISP router simulating upstream internet connectivity, an internal DNS server, and a public web server used to validate internet reachability.

---

## Objective
The objective of this project is to build and validate a branch edge design that provides:

- automatic IP addressing for user endpoints through DHCP
- inter-VLAN routing for segmented internal networks
- internal DNS service for name resolution
- PAT/NAT for outbound internet access
- ACL-based traffic control between internal users, management resources, and public destinations

---

## Scope
This project includes the following:

- VLAN-based segmentation for branch users
- router-on-a-stick inter-VLAN routing on the branch router
- DHCP pools for client VLANs
- internal DNS service for client name resolution
- NAT overload (PAT) for inside users reaching external networks
- standard and extended ACLs for internet access control
- validation of end-to-end connectivity and policy enforcement
- troubleshooting scenarios for DHCP, DNS, NAT, and ACL issues

This project does not include:

- dynamic routing protocols
- firewall appliances
- VPN tunnels
- high availability edge devices
- production internet connectivity

---

## Topology Summary
The branch LAN consists of a Layer 2 access switch connected to a branch router using an 802.1Q trunk. Multiple PCs are distributed across separate VLANs representing IT, STAFF, and GUEST users. An internal DNS server is connected to the branch switch in the management/services VLAN.

The branch router connects to an ISP router through a point-to-point WAN segment. A public web server is connected on the ISP side to simulate an internet-hosted resource. Internal clients resolve the web server name through DNS and reach it using NAT/PAT through the branch edge router.

---

## Devices Used
- 1 Cisco 2911 Branch Router (R1)
- 1 Cisco 2911 ISP Router (ISP)
- 1 Cisco 2960 Switch (SW1)
- 6 PCs
- 1 Internal DNS Server
- 1 Public Web Server

---

## VLAN Plan
- VLAN 10 – IT
- VLAN 20 – STAFF
- VLAN 30 – GUEST
- VLAN 99 – MGMT_SERVICES
- VLAN 999 – NATIVE_BLACKHOLE

---

## IP Addressing Plan

### Internal VLAN Gateways
- VLAN 10: `192.168.10.1/24`
- VLAN 20: `192.168.20.1/24`
- VLAN 30: `192.168.30.1/24`
- VLAN 99: `192.168.99.1/24`

### Internal Services
- DNS-SRV: `192.168.99.10/24`
- SW1 Management IP: `192.168.99.2/24`

### WAN Link
- R1 G0/0: `203.0.113.2/30`
- ISP G0/0: `203.0.113.1/30`

### Public Server Network
- ISP G0/1: `198.51.100.1/24`
- WEB-SRV: `198.51.100.10/24`

---

## Service Design

### DHCP
The branch router provides DHCP services for user VLANs. Each DHCP pool assigns:

- IP address
- subnet mask
- default gateway
- DNS server address

### DNS
The internal DNS server provides name resolution for branch clients. A DNS A record is created so internal users can resolve the public web server by name.

Example:
- `www.branchlab.com` → `198.51.100.10`

### NAT/PAT
The branch router performs NAT overload so multiple inside clients can share a single public-facing interface when accessing external networks.

### ACLs
ACLs are used to enforce traffic policy between internal VLANs and internet destinations.

Policy examples:
- IT users are allowed broader access
- STAFF users are denied access to management resources but allowed internet access
- GUEST users are denied access to internal branch networks and allowed internet-only access

---

## Access Policy Summary

### VLAN 10 – IT
Permitted:
- internal branch resources
- management/services VLAN
- external/public resources

### VLAN 20 – STAFF
Permitted:
- DNS service
- external/public resources

Denied:
- management/services VLAN access

### VLAN 30 – GUEST
Permitted:
- DNS service
- external/public resources

Denied:
- access to internal branch VLANs

---

## Validation Goals
This project validates the following:

- clients receive correct IP settings via DHCP
- user VLANs can reach their default gateways
- DNS name resolution works correctly
- clients can reach the public web server by IP address
- clients can reach the public web server by DNS name
- NAT translations are created for outbound sessions
- ACL restrictions block unauthorized traffic while allowing approved traffic

---

## Validation Artifacts
Validation output for this project is documented in the `/validation` folder, including:

- DHCP lease verification
- DNS resolution testing
- NAT translation verification
- ACL enforcement testing
- end-to-end connectivity tests

---

## Troubleshooting Scenarios
Troubleshooting cases for this project are documented in the `/troubleshooting` folder and include examples such as:

- incorrect DHCP pool configuration
- missing or incorrect DNS server assignment
- NAT inside/outside misconfiguration
- ACL applied on the wrong interface or in the wrong direction
- missing route to the ISP or public server network

---

## Key Commands Used

### Switch Validation
- `show vlan brief`
- `show interfaces trunk`
- `show ip interface brief`
- `show running-config`

### Router Validation
- `show ip interface brief`
- `show ip route`
- `show ip dhcp binding`
- `show ip dhcp pool`
- `show access-lists`
- `show ip nat translations`
- `show ip nat statistics`
- `show running-config`

---

## What I Learned
Through this project, I practiced how branch edge services work together to support endpoint connectivity and policy enforcement. I reinforced the relationship between VLAN segmentation, DHCP address assignment, DNS dependency, NAT/PAT operation, and ACL-based traffic filtering. I also improved my ability to validate network services step by step and troubleshoot configuration errors affecting user access.

---

## Tools Used
- Cisco Packet Tracer
- CLI verification using show commands
- Markdown documentation
- topology screenshots and configuration exports

---

## Folder Structure
- `/topology` – topology image and addressing plan
- `/configs` – device configurations
- `/validation` – command outputs and test results
- `/troubleshooting` – failure scenarios and fixes
- `/diagrams` – logical diagram files

---

## Portfolio Positioning
This is a lab project built in Cisco Packet Tracer for skills demonstration and portfolio use. It is not based on production administration experience. The project is intended to show practical understanding of branch routing, user services, traffic control, validation methodology, and troubleshooting workflow relevant to entry-level networking roles.
