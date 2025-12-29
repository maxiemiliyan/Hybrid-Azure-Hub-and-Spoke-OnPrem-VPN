# Traffic Flow Design

## Overview

This document explains how **network traffic flows between workloads** in the hybrid Hub-and-Spoke architecture implemented in this project.

The design ensures that **all communication between on-premises, application, and management workloads is strictly routed through the Azure Hub Virtual Network**, where centralized security and routing controls are applied.

No direct communication is permitted between spoke networks or between spokes and on-prem resources without passing through the Hub.

---

## Traffic Flow Principles

The architecture follows these core principles:

- Centralized routing through the Hub VNet
- Mandatory traffic inspection using Azure Firewall
- Strict isolation between Application and Management networks
- Secure, encrypted hybrid connectivity using Site-to-Site VPN

---

## Traffic Flow Diagram

            ┌─────────────────────────────┐
            │        Management VNet      │
            │     (Admin / Jumpbox VM)    │
            └──────────────┬──────────────┘
                           │
                           │  (UDR forces traffic)
                           ▼
            ┌─────────────────────────────┐
            │        Azure Hub VNet       │
            │                             │
            │  ┌───────────────────────┐  │
            │  │     Azure Firewall    │  │
            │  │  (Traffic Inspection) │  │
            │  └───────────┬───────────┘  │
            │              │              │
            │  ┌───────────▼───────────┐  │
            │  │     VPN Gateway       │  │
            │  │   (Route-based VPN)   │  │
            │  └───────────┬───────────┘  │
            └──────────────┼──────────────┘
                           │
            (IPsec Site-to-Site VPN - IKEv2)
                           │
                           ▼
            ┌─────────────────────────────┐
            │     On-Premises Network     │
            │   (Hyper-V + Windows 2019)  │
            │                             │
            │  ┌───────────────────────┐  │
            │  │         RRAS          │  │
            │  │   (Routing + VPN)     │  │
            │  └───────────┬───────────┘  │
            │              │              │
            │  ┌───────────▼───────────┐  │
            │  │   Internal Resource   │  │
            │  │ (Storage / Service)   │  │
            │  └───────────────────────┘  │
            └─────────────────────────────┘


            ┌─────────────────────────────┐
            │           App VNet          │
            │     (Application Workload)  │
            └──────────────┬──────────────┘
                           │
                           │  (UDR forces traffic)
                           └──────────────► Hub VNet



---

## Allowed Traffic Flows

### 1. App VNet → On-Prem Resource

**Use Case:**  
Application workloads need to access internal on-prem resources such as storage or services.

**Traffic Path:**

App VNet → Hub VNet → Azure Firewall → VPN Gateway → RRAS → On-Prem Resource


**Controls Applied:**
- UDR forces traffic to Azure Firewall
- Firewall policy explicitly allows required ports and protocols
- Traffic is encrypted over IPsec VPN

---

### 2. Management VNet → App VNet

**Use Case:**  
Administrators need to manage and monitor application workloads.

**Traffic Path:**

Mgmt VNet → Hub VNet → Azure Firewall → App VNet


**Controls Applied:**
- No direct peering between spokes
- Firewall policy governs permitted management access

---

### 3. Management VNet → On-Prem Resource

**Use Case:**  
Administrative access to on-prem resources for management and troubleshooting.

**Traffic Path:**

Mgmt VNet → Hub VNet → Azure Firewall → VPN Gateway → RRAS → On-Prem Resource


**Controls Applied:**
- Central inspection via Azure Firewall
- Secure VPN tunnel for hybrid access

---

## Blocked Traffic Flows

The following traffic flows are **explicitly blocked**:

- App VNet → Management VNet (direct)
- Management VNet → App VNet (direct)
- Spoke VNets → On-Prem (bypassing Hub)
- Any traffic not explicitly allowed by firewall rules

---

## Routing Enforcement Mechanism

- **User Defined Routes (UDRs)** are applied to:
  - App VNet subnet
  - Management VNet subnet
- Default route (`0.0.0.0/0`) points to Azure Firewall
- Ensures all outbound traffic passes through the Hub

---

## Security Enforcement

- Azure Firewall enforces:
  - Network-level access control
  - Centralized policy management
- Firewall metrics and logs provide:
  - Visibility into allowed and denied traffic
  - Auditability of hybrid communication

---

## Outcome

This traffic flow design ensures:

- Predictable and secure communication paths
- Complete isolation between workloads
- Centralized security inspection
- Enterprise-grade hybrid connectivity


