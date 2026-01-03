# Network Operations, Monitoring, and Troubleshooting Lab

![Network Topology Status](https://img.shields.io/badge/Status-Complete-success) ![Platform](https://img.shields.io/badge/Platform-Cisco_Packet_Tracer-blue)

## 📖 Project Overview
The primary objective of this project is to master incident management lifecycles by **intentionally introducing faults** into a Campus-style **Collapsed Core** network topology and systematically resolving them.

The lab focuses on the detection, diagnosis, and resolution of connectivity issues using standard industry protocols and Cisco IOS–style diagnostic commands (implemented in Packet Tracer).

## 🏗️ Network Topology

![Network Topology Diagram](./img/topology.png)

**Architecture Summary:**
* **Network & End Devices:** * PCs 
  * Servers
  * 2 Cisco 2960 switches
  * 2 Cisco 3560-24PS multilayer switches
* **Collapsed Core (Distribution) Layer:** Two Layer 3 switches performing inter-VLAN routing via SVIs and providing specific First Hop Redundancy (HSRP).
* **Access Layer:** Layer 2 switches connecting end hosts using VLAN segmentation and trunk uplinks.
* **Endpoints:** PCs and servers simulating traffic sources across multiple VLANs.

> **Note:** Before going further, please try to do the lab (`lab.pkt`) to practice troubleshooting.

## ⚡ Simulated Fault Scenarios
To replicate production incidents, the following faults were injected into the network components:

![Fault Scenarios Diagram](./img/fault_scenarios.png)

### Layer 1: Physical Layer
* **Interface Shutdowns:** Administratively down interfaces on uplinks to simulate cable breaks.
* **Speed and Duplex Mismatch:** Inconsistent speed/duplex settings between Access and Core switches.

### Layer 2: Data Link Layer
* **VLAN Mismatches:** Access ports assigned to incorrect VLANs, isolating hosts from their gateway.
* **Trunking Errors:** Native VLAN mismatches on uplinks and missing "Allowed VLANs" preventing traffic flow to the Core.

### Layer 3: Network Layer
* **Gateway Errors:** End hosts configured with incorrect default gateways (pointing to wrong SVI IP).
* **SVI Misconfiguration:** Switched Virtual Interface (SVI) configured with incorrect IP/HSRP settings.

## 🔍 Troubleshooting Methodology

1. **Symptom Identification:**
    * Used `ping` to test connectivity from Host to Gateway (SVI) and Host to Host.
    * Used `tracert` to identify where packets stopped routing.
2. **Information Gathering:**
    * **Layer 1 Check:** `show ip interface brief` (Status/Protocol), `show interfaces [int] status`.
    * **Layer 2 Check:** `show vlan`, `show interfaces trunk`, `show mac address-table`.
    * **Layer 3 Check:** `show standby brief`, `show ip interface brief` (verifying SVI status).
3. **Resolution & Validation:**
    * Corrected configuration using CLI.
    * Verified fix with repeated `ping` tests.

## 📝 Incident Log & Resolution
| Fault Type | Symptom | Command Used | Cause | Corrective Action |
| :--- | :--- | :--- | :--- | :--- |
| **L1** | Uplink Down (SW1's F0/23 & MSW1's F0/23) | `show interfaces F0/23 status` | Interface F0/23 speed and duplex mismatch | Configured `speed auto` and `duplex auto` on interfaces on both switches |
| **L1** | Uplink Down (SW1's F0/24 & MSW2's F0/24)| `show ip int brief` | MSW2's F0/24 administratively down | Issued `no shutdown` on interface |
| **L2** | Hosts on VLAN 20 cannot reach each other | `show vlan` | Port assigned to Default VLAN 1 instead of VLAN 20 | Configured `switchport access vlan 20` on the interfaces |
| **L2** | Hosts on VLAN 20 cannot reach hosts on VLAN 30 | `show int trunk` | Native VLAN mismatch (1 vs 3333) and VLAN 30 not allowed on F0/23-24 of SW2 | Configured `switchport trunk native vlan 3333` on both ends and added VLAN 30 to allowed list |
| **L3** | Server 2 cannot reach hosts on other subnets | `ipconfig` | Server 2's default gateway IP is incorrect | Change gateway to SVI of VLAN 30 (172.16.30.254) |
| **L3** | Hosts on VLAN 10 cannot reach hosts on other subnets | `show standby brief` | VLAN 10 SVI HSRP mismatch | Configured `standby 1 ip 172.16.10.254` |