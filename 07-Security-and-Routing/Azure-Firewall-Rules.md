# Azure Firewall Rules and Security Enforcement

## Overview

This document describes how **Azure Firewall** is used to enforce **centralized security and traffic control** in the hybrid Hub-and-Spoke architecture.

Azure Firewall is deployed in the **Hub Virtual Network** and acts as the **mandatory inspection point** for all traffic between:
- Azure App VNet
- Azure Management VNet
- On-Premises environment

No traffic is allowed to bypass the firewall.

---

## Role of Azure Firewall

Azure Firewall is responsible for:

- Inspecting all inter-VNet traffic
- Inspecting all hybrid (Azure ↔ On-Prem) traffic
- Enforcing allow/deny rules centrally
- Providing visibility and logging for network traffic
- Preventing unauthorized lateral movement

This design ensures **consistent and auditable security enforcement**.

---

## Firewall Deployment Model

- Azure Firewall is deployed in the **Azure Hub VNet**
- Uses a dedicated **AzureFirewallSubnet**
- Integrated with **User Defined Routes (UDRs)** to force traffic inspection
- Governed by a centralized **Azure Firewall Policy**

---

## Firewall Policy Design

Firewall rules are defined using a **deny-by-default** approach:

- Only explicitly allowed traffic is permitted
- All other traffic is implicitly denied

Rules are grouped based on **traffic purpose**, not individual workloads.

---

## Allowed Traffic Rules

### 1. App VNet → On-Prem Resource

**Purpose:**  
Allow application workloads to access internal on-premises resources.

**Rule Type:** Network Rule

**Rule Definition (Example):**
- Source: App VNet address range (`10.2.0.0/16`)
- Destination: On-Prem network (`10.0.0.0/24`)
- Protocol: Any (or specific protocols as required)
- Action: Allow

**Traffic Path:**
App VNet → Azure Firewall → VPN Gateway → RRAS → On-Prem Resource


---

### 2. Management VNet → App VNet

**Purpose:**  
Allow administrators to manage and monitor application workloads.

**Rule Type:** Network Rule

**Rule Definition (Example):**
- Source: Management VNet (`10.3.0.0/16`)
- Destination: App VNet (`10.2.0.0/16`)
- Protocol: Any (or restricted management protocols)
- Action: Allow

---

### 3. Management VNet → On-Prem Resource

**Purpose:**  
Allow administrative access to on-premises resources.

**Rule Type:** Network Rule

**Rule Definition (Example):**
- Source: Management VNet (`10.3.0.0/16`)
- Destination: On-Prem network (`10.0.0.0/24`)
- Protocol: Any (or management-specific protocols)
- Action: Allow

---

## Explicitly Blocked Traffic

The following traffic flows are **not allowed**:

- App VNet → Management VNet (direct)
- Management VNet → App VNet (direct, bypassing firewall)
- Any Spoke → On-Prem traffic bypassing Hub
- Any traffic not explicitly defined in firewall rules

Blocking is achieved through:
- Lack of firewall allow rules
- Mandatory routing via UDRs

---

## Integration with Routing

Azure Firewall enforcement is combined with routing controls:

- User Defined Routes in App and Mgmt VNets
- Default route (`0.0.0.0/0`) pointing to Azure Firewall
- No direct internet or lateral routing from spokes

This ensures firewall rules cannot be bypassed.

---

## Logging and Monitoring

Azure Firewall provides visibility through:

- Firewall metrics (allowed and denied traffic)
- Azure Monitor integration
- Traffic flow analysis for troubleshooting
- Auditability of hybrid communication

These logs help validate that all traffic is inspected as designed.

---

## Validation

Firewall rules are validated by:

- Successful connectivity for allowed traffic flows
- Failed connectivity attempts for blocked paths
- Azure Firewall metrics showing rule hit counts
- Confirmation that no traffic bypasses the Hub

---

## Outcome

Azure Firewall enforces a **centralized, consistent, and secure traffic control model** across the hybrid environment. This design minimizes risk, prevents unauthorized access, and aligns with enterprise security best practices.
