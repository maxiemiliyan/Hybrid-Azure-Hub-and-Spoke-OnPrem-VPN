# RRAS Configuration (On-Premises)

## Overview

This document describes the configuration of **Routing and Remote Access Service (RRAS)** on the **on-premises Windows Server 2019 virtual machine**.  
RRAS acts as the **VPN endpoint and routing device** for the hybrid connectivity between the on-premises environment and Azure.

The RRAS configuration enables **secure Site-to-Site VPN connectivity**, routing between networks, and controlled access to on-prem resources.

---

## Role of RRAS in the Architecture

RRAS is responsible for:

- Terminating the Site-to-Site VPN from Azure
- Routing traffic between on-prem and Azure networks
- Advertising on-prem address space to Azure
- Forwarding Azure traffic to internal on-prem resources

RRAS represents a **typical enterprise on-prem VPN and routing setup**.

---

## RRAS Prerequisites

Before configuring RRAS:

- Windows Server 2019 installed on Hyper-V
- Two network interfaces configured:
  - **LAN Interface** – Internal network (`10.0.0.0/24`)
  - **WAN Interface** – Internet-facing network
- Static IP assigned to the LAN interface
- Internet connectivity verified

---

## RRAS Installation

1. Open **Server Manager**
2. Add Roles and Features
3. Select **Remote Access**
4. Install:
   - Routing
   - Remote Access Services
5. Complete installation and reboot if required

---

## RRAS Initial Configuration

1. Open **Routing and Remote Access**
2. Right-click the server → **Configure and Enable RRAS**
3. Select **Custom Configuration**
4. Enable:
   - VPN access
   - LAN routing
5. Finish the wizard and start the RRAS service

---

## Demand-Dial Interface Configuration

A demand-dial interface is created to establish the VPN tunnel to Azure.

### Configuration Summary

- Interface Type: VPN
- VPN Type: IKEv2
- Destination: Azure VPN Gateway Public IP
- Authentication: Pre-shared key
- Protocols: IPv4 only

The pre-shared key must match the key configured on the Azure Site-to-Site VPN connection.

---

## Routing Configuration

### Static Routes

Static routes are added to allow on-prem systems to reach Azure networks.

Example Azure address spaces:
- Hub VNet
- App VNet
- Management VNet

These routes ensure that traffic destined for Azure is sent through the VPN tunnel.

---

## IP Forwarding

- IPv4 forwarding is enabled in RRAS
- Allows the server to forward traffic between interfaces
- Required for proper hybrid routing

---

## NAT Configuration

- NAT is **disabled**
- Required for route-based Site-to-Site VPN compatibility
- Ensures Azure can see the original source IP addresses

---

## Security Configuration

- VPN uses IPsec with IKEv2
- Authentication via pre-shared key
- Windows Firewall configured to allow VPN traffic
- On-prem resources are not exposed to the internet

---

## Validation

RRAS configuration is validated by:

- Confirming VPN tunnel status is connected
- Verifying Azure address spaces are reachable from on-prem
- Testing access to on-prem resources from Azure workloads
- Confirming traffic flows through Azure Firewall and VPN Gateway

---

## Outcome

The RRAS configuration provides a **secure and reliable on-premises VPN endpoint**, enabling seamless hybrid connectivity while maintaining enterprise-grade routing and security controls.
