# Hub-and-Spoke Routing Design

## Overview

This document explains how **routing is designed and enforced** in the hybrid Hub-and-Spoke architecture to ensure that **all traffic flows through the Azure Hub Virtual Network**.

The routing design prevents direct communication between spoke networks and enforces centralized inspection and control using **Azure Firewall** and **Site-to-Site VPN connectivity**.

---

## Routing Objectives

The routing configuration is designed to achieve the following:

- Enforce mandatory transit through the Hub VNet
- Prevent direct spoke-to-spoke communication
- Enable secure hybrid connectivity with on-premises
- Centralize security inspection and routing decisions
- Maintain predictable and auditable traffic paths

---

## Hub-and-Spoke Routing Model

### Hub Virtual Network

The Hub VNet acts as the **central routing point** and contains:

- Azure VPN Gateway for on-prem connectivity
- Azure Firewall for traffic inspection
- Routing logic for all inter-network traffic

No application workloads are deployed in the Hub.

---

### Spoke Virtual Networks

Two spoke VNets are used:

- **App VNet** – hosts application workloads
- **Management VNet** – hosts administrative and management workloads

The spokes:
- Do not have direct peering with each other
- Rely on the Hub for all outbound and inter-VNet traffic

---

## Routing Enforcement Using UDRs

### User Defined Routes (UDRs)

User Defined Routes are applied at the **subnet level** in both spoke VNets.

Each spoke subnet has a route table configured with:

Destination: 0.0.0.0/0
Next Hop: Virtual Appliance (Azure Firewall private IP)


This configuration ensures:
- All outbound traffic from spokes is forced to the Hub
- No direct internet or lateral traffic bypasses the firewall

---

## Hybrid Routing (Azure ↔ On-Prem)

### Azure to On-Prem Routing

- Traffic destined for on-prem address space (`10.0.0.0/24`) is routed:
  - From spokes → Hub → Azure Firewall → VPN Gateway → On-Prem
- Azure VPN Gateway advertises routes learned from the Local Network Gateway

---

### On-Prem to Azure Routing

- RRAS on the on-prem Windows Server maintains static routes for:
  - Azure Hub VNet
  - Azure App VNet
  - Azure Management VNet
- Traffic from on-prem is routed through the VPN tunnel into the Hub

---

## Preventing Direct Spoke Communication

Direct spoke-to-spoke traffic is prevented by:

- No direct VNet peering between App and Management VNets
- UDRs forcing traffic to Azure Firewall
- Firewall policies controlling permitted flows

Even if peering exists with the Hub, traffic cannot bypass centralized inspection.

---

## Routing Validation

Routing behavior is validated by:

- Verifying UDR association on spoke subnets
- Testing connectivity paths:
  - App → On-Prem
  - Mgmt → App
  - Mgmt → On-Prem
- Confirming Azure Firewall metrics show traffic inspection
- Confirming VPN Gateway ingress and egress counters increase

---

## Design Outcome

This routing design ensures:

- Centralized control of all traffic flows
- Strong network isolation between workloads
- Secure and predictable hybrid connectivity
- Alignment with enterprise Hub-and-Spoke best practices

The routing model supports scalability and future expansion without architectural changes.
