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
8. Document the troubleshooting process and security implications as a practical network-security portfolio project.

## Network Architecture

## VLAN & IP Addressing

## Initial Configuration

## Troubleshooting Scenario

Fault Identification

## Remediation

## Security Validation

## Evidence

## Key Commands

## Lessons Learned

## Technologies used
