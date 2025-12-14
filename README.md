# Network Operations, Monitoring, and Troubleshooting Lab

![Network Status](https://img.shields.io/badge/Status-work_in_progress-yellow) ![Platform](https://img.shields.io/badge/Platform-Cisco_IOS-blue)

## 📖 Project Overview
The primary objective of this project is to master incident management lifecycles by **intentionally introducing faults** into a functional Layer 1–3 network topology and systematically resolving them. 

The lab focuses on the detection, diagnosis, and resolution of connectivity issues using standard industry protocols and Cisco IOS diagnostic tools.

## 🏗️ Network Topology
*(topology diagram here)*

**Architecture Summary:**
* **Core/Distribution:** Cisco Routers and L3 Switches handling Inter-VLAN routing.
* **Access Layer:** Cisco Switches managing VLAN segmentation and trunking.
* **Endpoints:** PCs and Servers simulating traffic sources.

## ⚡ Simulated Fault Scenarios
To replicate production incidents, the following faults were injected into the network:

### Layer 1: Physical Layer
* **Interface Shutdowns:** Administratively down interfaces to simulate cable breaks.
* **Speed and duplex mismatch:** Inconsistent speed/duplex settings between connected interfaces.

### Layer 2: Data Link Layer
* **VLAN Mismatches:** Ports assigned to incorrect VLANs, isolating hosts.
* **Trunking Errors:** Native VLAN mismatches and missing "Allowed VLANs" on trunk links.
* **Port Security:** Violation modes triggered to block MAC addresses.

### Layer 3: Network Layer
* **Gateway Errors:** Incorrect Default Gateway configuration on end hosts.
* **Routing Logic:** Missing static routes and incorrect next-hop addresses.
* **Subnetting:** Mask mismatches causing IP overlap or unreachable subnets.

## 🔍 Troubleshooting Methodology
The resolution process followed a strict 4-step diagnostic workflow:

1.  **Symptom Identification:**
    * Used `ping` to test end-to-end connectivity.
    * Used `traceroute` to identify the specific hop where packet loss occurred.
2.  **Information Gathering:**
    * **Layer 1 Check:** `show ip interface brief` (Status/Protocol).
    * **Layer 2 Check:** `show vlan`, `show interfaces trunk`, `show mac address-table`.
    * **Layer 3 Check:** `show ip route`, `show ip protocols`.
3.  **Root Cause Analysis (RCA):**
    * Correlated command output with the topology design to pinpoint configuration drift.
4.  **Resolution & Validation:**
    * Corrected configuration using CLI.
    * Verified fix with repeated `ping` tests and documented results.

## 📝 Incident Log & Resolution
| Fault Type | Symptom | Command Used | Root Cause | Corrective Action |
| :--- | :--- | :--- | :--- | :--- |
| **L1** | Link Down | `show ip int brief` | Interface Gi0/1 administratively down. | Issued `no shutdown` on interface. |
| **L2** | Intra-VLAN fail | `show vlan` | Port assigned to Default VLAN 1 instead of VLAN 10. | `switchport access vlan 10`. |
| **L2** | Inter-switch fail | `show int trunk` | Native VLAN mismatch (1 vs 99). | Configured `switchport trunk native vlan 99` on both ends. |
| **L3** | Destination Unreachable | `show ip route` | Missing static route to 192.168.20.0/24. | Added `ip route 192.168.20.0 255.255.255.0 [Next-Hop]`. |

## 📂 Repository Structure
```text
├── configs/
│   ├── initial_faulty_config/   # Configurations with injected errors
│   └── final_resolved_config/   # Clean, working configurations
├── logs/
│   └── troubleshooting_logs.txt # Capture of CLI sessions and show commands
├── images/
│   └── topology_diagram.png     # Visual map of the network
└── README.md