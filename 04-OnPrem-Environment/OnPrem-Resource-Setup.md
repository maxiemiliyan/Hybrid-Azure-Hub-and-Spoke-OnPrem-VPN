# On-Premises Resource Setup

## Overview

This document describes the setup of the **on-premises environment** used in this hybrid architecture. The on-premises environment simulates a traditional data center and hosts an **internal enterprise resource** that must be accessed securely from Azure workloads.

The environment is implemented using **Windows Server 2019 hosted on Hyper-V** and integrated with Azure using **Routing and Remote Access Service (RRAS)**.

---

## On-Premises Infrastructure

### Hyper-V Host

- Acts as the virtualization platform
- Hosts the Windows Server 2019 virtual machine
- Provides a controlled and isolated on-premises environment

---

### Windows Server 2019 Virtual Machine

- Operating System: Windows Server 2019
- Role: On-Premises infrastructure server
- Network Configuration:
  - LAN interface connected to internal network (`10.0.0.0/24`)
  - WAN interface connected to the internet

This virtual machine represents a typical on-premises server deployed in enterprise environments.

---

## Internal On-Premises Resource

To simulate a real enterprise workload, an **internal resource** is hosted on the on-premises server.

### Example Resource

- Internal file storage or service
- Accessible only within the on-premises network
- Used to validate secure access from Azure workloads

This resource is intentionally **not exposed directly to the internet**.

---

## Routing and Remote Access Service (RRAS)

### Purpose of RRAS

RRAS is configured on the Windows Server 2019 VM to:

- Establish Site-to-Site VPN connectivity with Azure
- Route traffic between on-premises and Azure networks
- Act as the VPN termination point for the hybrid connection

---

### RRAS Configuration Summary

- VPN Type: Route-based
- Tunnel Protocol: IPsec (IKEv2)
- Authentication: Pre-shared key
- IP forwarding: Enabled
- NAT: Disabled (required for route-based VPN)

RRAS maintains routing awareness of Azure address spaces to ensure proper traffic forwarding.

---

## Access Control

- Internal resources are accessible only from:
  - Azure App VNet workloads
  - Azure Management VNet workloads
- All access is routed through:
  - Azure Hub VNet
  - Azure Firewall
  - Site-to-Site VPN

Direct access from the internet or bypassing the Hub is not permitted.

---

## Validation

The on-premises setup is validated by:

- Confirming RRAS VPN tunnel connectivity
- Testing access to internal resources from:
  - Azure App VNet
  - Azure Management VNet
- Verifying traffic passes through Azure Firewall and VPN Gateway

---

## Outcome

This on-premises setup provides a **secure and realistic enterprise data center simulation**, enabling controlled hybrid access while maintaining strong security boundaries.
