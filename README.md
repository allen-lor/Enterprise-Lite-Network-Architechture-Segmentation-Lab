# 🏢 Enterprise-Lite Network Architecture: Departmental VLANs & DHCP

> **📋 Project Summary:** This lab demonstrates the configuration of a scalable, enterprise-lite network architecture using Cisco Packet Tracer. The project focuses on deploying isolated broadcast domains for distinct corporate departments and automating IP address provisioning. Key implementations include 802.1Q trunking, inter-VLAN routing, and layer 2 loop prevention, reflecting standard practices for a stable and secure corporate infrastructure.

## 🛠️ Technologies & Protocols Used
* **Cisco IOS:** Device configuration and network management.
* **VLANs (802.1Q):** Logical network segmentation and broadcast domain isolation. 
* **Spanning Tree Protocol (STP):** Layer 2 loop prevention mechanism ensuring a stable, redundant, and loop-free logical topology across interconnected switches. 
* **DHCP:** Automated, dynamic IPv4 address allocation.
* **Trunking:** Passing multiple VLANs across single switch-to-switch and switch-to-router links.
* **Inter-VLAN Routing:** Enabling communication between isolated departmental subnets.

## 🌐 Network Topology
The architecture represents a multi-departmental corporate network, featuring segmented access switches and routed inter-VLAN traffic to allow controlled communication between departments.

<img width="738" height="792" alt="Screenshot 2026-02-19 184940" src="https://github.com/user-attachments/assets/72eb155a-3b93-485d-983c-f8b87f76a0bf" />

## 🏆 Important Feats & Achievements

### 1. Departmental VLAN Segmentation 🚧
Successfully partitioned the physical network into multiple, isolated broadcast domains. Each department (including a dedicated VLAN 30 for Guest access) was assigned a specific VLAN to enforce logical separation, improve network performance, and lay the groundwork for strict access control across the enterprise network.

<img width="1398" height="1412" alt="VLAN names" src="https://github.com/user-attachments/assets/c58094e5-5b50-4789-b225-afa1256742ae" />

### 2. Automated IP Addressing & STP Integration ⚡
Configured centralized DHCP pools to dynamically assign IP addresses, subnet masks, and default gateways to end devices. Because the initial DHCP DISCOVER process relies heavily on network broadcasts, Spanning Tree Protocol (STP) was critical in this setup. STP maintained a loop-free topology to prevent broadcast storms from crippling the network during IP allocation. Furthermore, managing STP port states (such as enabling PortFast on access ports) was necessary to bypass the standard 30-second listening and learning delays, ensuring end devices didn't time out and successfully received their leased IP addresses the moment they connected.

<img width="1336" height="1106" alt="Screenshot 2026-02-19 190739" src="https://github.com/user-attachments/assets/daf18e08-6d28-4b6c-a55b-b56cbb146dda" />

### 3. DHCP Binding Verification 🔍
Audited the routing device's active DHCP bindings to verify the integrity of the IP allocation. This confirmed that addresses were accurately mapped to the MAC addresses of authorized departmental devices with zero IP conflicts.

<img width="1334" height="794" alt="Screenshot 2026-02-19 191838" src="https://github.com/user-attachments/assets/941cfd1d-18b1-4775-8dbd-a730a5c25fd2" />

## ⚠️ Notable Failures & Troubleshooting

### Inter-VLAN DHCP Request Failures ❌
During the initial deployment, client devices situated in specific department VLANs (specifically the Guest network on VLAN 30) failed to receive DHCP offers, resulting in those devices being isolated from the network with APIPA addresses. 

<img width="1266" height="1058" alt="Screenshot 2026-02-19 144407" src="https://github.com/user-attachments/assets/14700bcb-4db9-4ba4-8532-c9a6a58a0de3" />

<img width="1370" height="1244" alt="Screenshot 2026-02-19 113254" src="https://github.com/user-attachments/assets/9a3653f3-575e-453b-afdc-d55650c26c5e" />

**🔧 Root Cause & Resolution:** The failure was traced back to an 802.1Q trunking and VLAN mismatch between the interconnected switches. Because the trunk link was not properly configured to allow the necessary VLAN tags to pass between the switches, the DHCP DISCOVER broadcast messages from VLAN 30 were being dropped before they could reach the DHCP pool. 

To resolve the issue, the trunking status on the uplink interfaces was audited. The trunking encapsulation was corrected, and the allowed VLAN lists on both sides of the switch-to-switch links were synchronized. Once the trunk was properly passing tagged traffic for all departmental VLANs, the DHCP requests were successfully routed, and the end devices received their IP configurations.

## 💻 Configuration Snippets

### DHCP Pool Setup (VLAN 30 Guest)
```text
Router(config)# ip dhcp pool GUEST_VLAN30
Router(dhcp-config)# network 192.168.30.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.30.1
```

### STP PortFast Configuration (Access Ports)
```text
Switch(config)# interface range fa0/1 - 10
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# spanning-tree portfast
Switch(config-if-range)# exit
```

### Trunking Mismatch Resolution (802.1Q & Allowed VLANs)
```text
Switch(config)# interface gig0/1
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30,99
