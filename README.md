# Cisco VLAN & Trunk Security Troubleshooting Lab

## Project Overview

This project demonstrates the design, troubleshooting, and security validation of a small-office switched network using Cisco Packet Tracer.

The network uses three VLANs to logically segment Sales, IT, and Management devices:

- VLAN 10 — Sales
- VLAN 20 — IT
- VLAN 30 — Management

Two Cisco switches, SW1 and SW2, are connected through an IEEE 802.1Q trunk. Each VLAN has a host connected to both switches, allowing same-VLAN connectivity to be tested across the inter-switch link.

A controlled trunk misconfiguration was introduced by removing VLAN 10 from the trunk's allowed VLAN list. This caused communication between the two Sales PCs to fail even though both PCs remained correctly assigned to VLAN 10.

Cisco IOS verification commands were then used to identify the fault, restore VLAN 10 to the trunk, and verify that connectivity was successfully restored.

The project also validates Layer 2 segmentation by demonstrating that Sales devices cannot directly communicate with IT or Management devices in the absence of Layer 3 routing.

## Objectives

The objectives of this lab were to:

1. Configure VLAN-based network segmentation for Sales, IT, and Management.
2. Configure an IEEE 802.1Q trunk between two Cisco switches.
3. Verify same-VLAN communication across the inter-switch trunk.
4. Introduce a controlled trunk configuration fault affecting VLAN 10.
5. Use Cisco IOS troubleshooting commands to identify the cause of the connectivity failure.
6. Restore the correct trunk configuration and verify connectivity.
7. Validate Layer 2 separation between the configured VLANs.
   
## Network Architecture

The lab uses two Cisco 2960 switches connected through a GigabitEthernet trunk link.

- SW1 connects the first Sales, IT, and Management PCs.
- SW2 connects the second Sales, IT, and Management PCs.
- GigabitEthernet0/1 on both switches is configured as an IEEE 802.1Q trunk.
- The trunk carries VLANs 10, 20, and 30 between the switches.

**VLAN Segmentation**

```bash
VLAN| Name| Purpose
10| SALES| Sales devices
20| IT| IT devices
30| MANAGEMENT| Management devices
```
The topology is intentionally Layer 2 only. No router or Layer 3 switch is used, allowing the lab to demonstrate same-VLAN connectivity and Layer 2 separation without inter-VLAN routing.

## VLAN & IP Addressing

The network uses three VLANs to separate Sales, IT, and Management devices. Each VLAN uses its own /24 IP subnet.

```bash
Device| Switch| Port| VLAN| IP Address
Sales-PC1| SW1| Fa0/1| 10| 192.168.10.11/24
IT-PC1| SW1| Fa0/2| 20| 192.168.20.11/24
Management-PC1| SW1| Fa0/3| 30| 192.168.30.11/24
Sales-PC2| SW2| Fa0/1| 10| 192.168.10.12/24
IT-PC2| SW2| Fa0/2| 20| 192.168.20.12/24
Management-PC2| SW2| Fa0/3| 30| 192.168.30.12/24
```
The inter-switch connection uses GigabitEthernet0/1 on both switches as an 802.1Q trunk. The trunk is configured to carry VLANs 10, 20, and 30.

No default gateway is configured because the lab focuses on Layer 2 connectivity and VLAN segmentation rather than inter-VLAN routing.

## Initial Configuration

The switches were configured with three VLANs and the appropriate access-port assignments.

**VLAN Configuration**

The following VLANs were created on both switches:

```text
VLAN 10 — SALES
VLAN 20 — IT
VLAN 30 — MANAGEMENT
```
**Access Port Configuration**

The end-device ports were assigned to their respective VLANs:

```text
SW1:
Fa0/1 → VLAN 10
Fa0/2 → VLAN 20
Fa0/3 → VLAN 30

SW2:
Fa0/1 → VLAN 10
Fa0/2 → VLAN 20
Fa0/3 → VLAN 30
```

**Trunk Configuration**

The GigabitEthernet0/1 interface on both switches was configured as an IEEE 802.1Q trunk:

```text
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 no shutdown
```

The initial configuration was verified using:

```text
show vlan brief
show interfaces trunk
```
These commands confirmed that the VLANs were active, the access ports were correctly assigned, and the inter-switch trunk was carrying VLANs 10, 20, and 30.

## Baseline Connectivity

Before introducing the troubleshooting scenario, same-VLAN connectivity was tested between devices connected to different switches.

The following tests were successful:

```bash
Source| Destination| VLAN| Result
Sales-PC1| Sales-PC2| 10| Successful
IT-PC1| IT-PC2| 20| Successful
Management-PC1| Management-PC2| 30| Successful
```

These successful tests confirmed that the VLAN assignments and inter-switch trunk were functioning correctly before the fault was introduced.

## Troubleshooting Scenario

A controlled trunk misconfiguration was introduced to simulate a real-world network connectivity issue.

VLAN 10 was deliberately removed from the allowed VLAN list on the inter-switch trunk of both SW1 and SW2.

The trunk configuration was changed from:

`switchport trunk allowed vlan 10,20,30`

to:

`switchport trunk allowed vlan 20,30`

This prevented VLAN 10 traffic from crossing the trunk between SW1 and SW2.

As a result, Sales-PC1 could no longer communicate with Sales-PC2, while the VLAN 10 access-port assignments on the switches remained unchanged.

The fault was verified using:

`show interfaces trunk`

The command showed that VLAN 10 was no longer included in the trunk's allowed VLAN list.

## Fault Identification

The troubleshooting process used Cisco IOS verification commands to determine whether the problem was caused by the VLAN configuration or the inter-switch trunk.

Step 1 — Verify VLAN Configuration

The following command was used on both switches:

`show vlan brief`

The output confirmed that:

- VLAN 10 (SALES) was active.
- VLAN 10 was assigned to FastEthernet0/1.
- VLAN 20 (IT) and VLAN 30 (MANAGEMENT) were also active and correctly assigned.

This ruled out a missing VLAN or incorrect access-port assignment as the cause of the failure.

Step 2 — Verify the Trunk

The following command was then used:

`show interfaces trunk`

The trunk was operational and using 802.1Q, but the allowed VLAN list showed:

`20,30`

VLAN 10 was missing from the allowed list.

**Root Cause**

The root cause was a trunk allowed-VLAN misconfiguration. VLAN 10 was active on the switches and correctly assigned to the Sales access ports, but it was not permitted to cross the inter-switch trunk.

Therefore, Sales-PC1 and Sales-PC2 could not communicate even though both devices belonged to VLAN 10.

This demonstrates the importance of verifying both VLAN membership and trunk configuration when troubleshooting VLAN connectivity.


## Remediation

The trunk configuration was corrected on both SW1 and SW2 by restoring VLAN 10 to the allowed VLAN list.

The following configuration was applied to GigabitEthernet0/1:

```text
interface gigabitEthernet 0/1
 switchport trunk allowed vlan 10,20,30
```

The trunk was then verified using:

`show interfaces trunk`

The verification confirmed that VLANs 10, 20, and 30 were allowed, active, and forwarding across the trunk.

**Connectivity Verification**

After the configuration was corrected, the Sales devices were tested again.

**Sales-PC1 → Sales-PC2**

`ping 192.168.10.12`

The ping was successful, confirming that VLAN 10 connectivity had been restored.

The IT and Management same-VLAN connectivity tests were also successful.

## Security Validation

## Evidence

## Key Commands

## Lessons Learned

## Technologies used
