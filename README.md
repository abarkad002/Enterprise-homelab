# enterprise-homelab
Build a enterprise-homelab 

# Enterprise Homelab - Cisco ASA 5505, Dell PowerEdge R320 & Proxmox

## Project Overview

This project documents the design, deployment, and troubleshooting of my enterprise-style homelab. The objective is to gain practical experience with networking, virtualization, routing, firewalls, and system administration using enterprise hardware.

## Hardware

* Dell PowerEdge R320
* Cisco ASA 5505 Firewall
* Layer 3 Switch
* Bell Giga Hub (700 Mbps Internet)
* Desktop Computer
* 2 × 1 TB HDD (RAID 1)
* Proxmox VE Hypervisor

---

# Initial Network Topology

```
               enterprise-homelab/
│
├── README.md
├── diagrams/
│   ├── physical-topology.md
│   ├── logical-topology.md
│   ├── vlan-design.md
│   └── firewall-topology.md
│
├── proxmox/
│   ├── installation.md
│   ├── storage.md
│   ├── networking.md
│   └── screenshots/
│
├── cisco-asa/
│   ├── interface-configuration.md
│   ├── nat.md
│   ├── acl.md
│   └── vpn.md
│
├── windows-server/
├── active-directory/
├── kali-linux/
├── security-onion/
├── writeups/
├── python-tools/
└── screenshots/

---

# ASA Default Configuration

Factory Default:

Outside

* VLAN 2
* Ethernet0/0
* Security Level 0

Inside

* VLAN 1
* Ethernet0/1 through Ethernet0/7
* Security Level 100

Outside Interface

* Obtained IP automatically from Bell modem via DHCP

```
192.168.2.29
```

Inside Interface

```
192.168.1.1/24
```

---

# Proxmox Installation

Originally planned to deploy VMware ESXi.

Due to download availability and compatibility considerations, Proxmox VE was selected as the virtualization platform.

Storage Configuration

* RAID 1
* 2 × 1 TB HDD
* Approximately 1 TB usable storage

---

# Troubleshooting Process

## Issue 1

### Problem

Unable to access the Proxmox Web Interface.

```
https://<server-ip>:8006
```

### Investigation

Checked interfaces

```
ip addr
```

Result

* Loopback interface present
* Physical NICs DOWN
* vmbr0 DOWN

### Root Cause

The Ethernet cable was not connected to the server.

### Resolution

The RJ45 cable was connected.

Physical interfaces became operational.

---

## Issue 2

### Problem

Could not reach the Proxmox Web Interface.

### Investigation

Verified:

* Server could not communicate with the network.
* Reviewed physical topology

Discovered:

Desktop PC was not connected behind the firewall.

Original topology prevented management traffic from reaching the Proxmox server.

### Resolution

Connected the Desktop PC to the internal network through the Layer 3 switch.

---

## Issue 3

### Firewall Understanding

Initially suspected the ASA firewall was blocking traffic.

After investigation:

Outside Interface

```
192.168.2.29
```

Received dynamically from Bell Giga Hub.

Inside Interface

```
192.168.1.1
```

Serves as the default gateway for the internal network.

Realization:

Traffic between internal devices should communicate with the ASA Inside Interface, not the Outside Interface.

---

## Issue 4

### Proxmox Management IP

Modified the management IP.

Observed:

```
ip addr show vmbr0
```

Still displayed the old address.

Cause:

The running configuration had not yet reflected the intended configuration.

After correcting the configuration and applying the changes, the management interface became reachable.

---

## Connectivity Verification

Successfully verified:

✓ PC → ASA

```
ping 192.168.1.1
```

✓ Proxmox → ASA

```
ping 192.168.1.1
```

✓ PC → Proxmox

```
ping <Proxmox-IP>
```

✓ Accessed

```
https://<Proxmox-IP>:8006
```

---

# Lessons Learned

* Always verify physical connectivity before troubleshooting software.
* Understand the difference between the ASA Inside and Outside interfaces.
* Verify interface status before troubleshooting applications.
* Test connectivity one hop at a time.
* Separate routing problems from application problems.
* Validate Layer 1 before moving to Layers 2, 3, and 7.
* Enterprise troubleshooting follows a structured methodology rather than guessing.

---

# Skills Demonstrated

Networking

* Network topology design
* Layer 2 switching
* Layer 3 concepts
* Default gateways
* DHCP
* IP addressing
* Routing fundamentals
* Network troubleshooting

Firewall

* Cisco ASA 5505
* Inside/Outside interfaces
* Security zones
* NAT planning
* Enterprise edge design

Virtualization

* Proxmox VE deployment
* Hypervisor management
* Linux networking
* RAID storage planning

System Administration

* Interface configuration
* IP troubleshooting
* Connectivity testing
* Server deployment
* Infrastructure validation

---

# Next Steps

* Configure PAT/NAT on Cisco ASA
* Configure Internet access for internal network
* Configure VLAN segmentation
* Configure Layer 3 routing
* Deploy Windows Server
* Deploy Active Directory
* Configure DNS
* Configure DHCP
* Deploy Kali Linux
* Deploy Security Onion
* Build enterprise security lab.
* Document firewall policies
* Implement VPN access

---

## Project Status

✔ Physical infrastructure deployed

✔ Firewall operational

✔ Proxmox deployed

✔ Internal connectivity verified

🔄 Next milestone: Configure Internet access through the Cisco ASA using NAT/PAT.
