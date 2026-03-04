# CCNA Project 04 – Branch Edge Services: DHCP, NAT, ACL, and Internet Access Control

## Overview
This project simulates a small branch office edge network in Cisco Packet Tracer. The lab demonstrates how a branch router can provide core user services such as DHCP, inter-VLAN routing, NAT/PAT for outbound internet access, ACL-based traffic restrictions, and DNS-supported name resolution.

The topology includes multiple user VLANs connected to a branch switch, a branch router acting as the default gateway and policy enforcement point, an ISP router simulating upstream internet connectivity, an internal DNS server, and a public web server used to validate internet reachability.

---

## Versions Implemented

This project was built in two versions:

- **Version 1** – Base branch edge services design using router-on-a-stick on R1 for VLAN 10, VLAN 20, VLAN 30, and VLAN 99
- **Version 2** – Extended design where VLAN 30 was moved behind R2 and supported through DHCP relay back to R1

Version 1 is the primary implementation for this project. Version 2 is included as an advanced extension showing centralized DHCP with a remote guest subnet.

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
- extended ACLs for internal access restriction and controlled public reachability
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
ACLs are used to enforce traffic policy between internal VLANs and public destinations.

Policy examples:
- IT users are allowed broad access to internal and public resources
- STAFF users are allowed DNS and public access, but denied general access to management/services resources
- GUEST users are allowed DNS and public access, but denied access to internal branch subnets

---

## Access Policy Summary

### VLAN 10 – IT
Permitted:
- internal branch resources
- management/services VLAN
- external/public resources

### VLAN 20 – STAFF
Permitted:
- DNS queries to the internal DNS server
- VLAN 10 access
- external/public resources

Denied:
- general access to management/services VLAN resources

### VLAN 30 – GUEST
Permitted:
- DNS queries to the internal DNS server
- external/public resources

Denied:
- access to internal branch VLANs
- general access to management/services VLAN resources

---

## Validation Artifacts
Validation output for this project is documented in the `/validation` folder and is separated into:

- **Version 1** validation for the base branch edge services topology
- **Version 2** validation for the remote guest segment and DHCP relay extension

Included validation areas:
- DHCP lease verification
- DHCP relay verification
- DNS resolution testing
- NAT translation verification
- ACL and guest-policy enforcement testing
- end-to-end connectivity tests

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

- Packet Tracer DHCP endpoint behavior during early Version 1 testing
- DNS reachability versus DNS application traffic under ACL restrictions
- switchport mode mismatch affecting Version 2 DHCP relay
- missing route conditions affecting return reachability to the remote guest subnet
- ACL placement and policy validation on the correct gateway interface

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

## Skills Demonstrated
This project demonstrates practical skills in:

- VLAN segmentation and inter-VLAN routing
- DHCP server configuration
- DNS-supported client name resolution
- NAT/PAT for outbound internet simulation
- ACL-based traffic control
- end-to-end validation using CLI and host testing
- troubleshooting Layer 2, Layer 3, and service dependency issues
- extending a base design using DHCP relay and a remote routed subnet

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

---

## Version 2 Extension – Remote Guest Segment with DHCP Relay

As an extension of the base Project 04 design, VLAN 30 (GUEST) was moved off the main branch router and placed behind a second router (R2). In this version, R2 acts as the default gateway for the guest subnet and relays DHCP requests to the centralized DHCP server on R1 using `ip helper-address`.

This design variation demonstrates how centralized branch services can still support a remote subnet while keeping local policy enforcement at the remote gateway.

### Version 2 Design Changes
Compared with Version 1, the following changes were introduced:

- VLAN 30 was removed from the router-on-a-stick design on R1
- R2 was added as the default gateway for the guest subnet
- R2 received upstream connectivity from R1 over a routed point-to-point link
- DHCP for VLAN 30 remained centralized on R1 and was delivered through DHCP relay
- guest ACL policy enforcement moved from R1 to R2
- R1 continued to provide DNS reachability, NAT/PAT, and simulated internet access

### Version 2 Objective
The objective of Version 2 was to extend the base branch edge services lab by introducing a remote guest segment while preserving centralized DHCP, DNS, and internet access services.

### Version 2 Key Configurations
Version 2 introduced the following technical changes:

- `ip helper-address` configured on R2 for DHCP relay
- static route on R1 toward `192.168.30.0/24` via R2
- default route on R2 toward R1
- NAT inside role added on the R1 interface facing R2
- guest access-control policy applied inbound on the R2 guest-facing interface

### Version 2 Validation Summary
Validation confirmed that:

- VLAN 30 clients successfully received DHCP addressing through relay
- VLAN 30 clients resolved `www.branchlab.com` using the internal DNS server
- VLAN 30 clients successfully reached the public web server
- VLAN 30 clients were denied access to internal branch subnets such as VLAN 10 and VLAN 20
- centralized branch services remained functional while the guest subnet was moved behind a separate router

### Version 2 Skills Demonstrated
This extension demonstrates:

- DHCP relay operation
- routed subnet extension
- centralized service design
- distributed policy enforcement
- guest network isolation
- troubleshooting of switchport mode mismatch and missing route conditions
