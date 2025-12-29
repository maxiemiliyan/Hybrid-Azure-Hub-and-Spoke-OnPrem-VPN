# Management VNet to Application VNet Access

## Overview

This document explains how **management workloads deployed in the Azure Management VNet** securely access **application workloads deployed in the Azure Application VNet**.

The design enforces **strict network isolation** between the two VNets while allowing controlled management access through the **Azure Hub Virtual Network**, where centralized routing and security inspection are applied.

---

## Business Requirement

Administrators require controlled access to application workloads for:

- System administration
- Monitoring and troubleshooting
- Application maintenance

Direct connectivity between Management and Application VNets is **not permitted** to reduce security risk and prevent lateral movement.

---

## Access Design

The access model follows these rules:

- Management workloads cannot access application workloads directly
- All traffic must pass through the Azure Hub VNet
- Traffic must be inspected by Azure Firewall
- Only explicitly allowed management traffic is permitted

This approach aligns with enterprise security best practices.

---

## Traffic Flow

text
Management VM (Mgmt VNet)
        |
        |  (UDR forces traffic)
        v
Azure Hub VNet
        |
        +-- Azure Firewall (Inspection)
        |
        +-- Routing Control
                |
                v
Application VM (App VNet)


## Routing Enforcement

Routing is enforced using:

- User Defined Routes (UDRs) on the Mgmt subnet
- Default route (0.0.0.0/0) pointing to Azure Firewall
- No direct VNet peering between Mgmt and App VNets

This ensures management traffic always passes through centralized inspection.

## Security Controls

The following security controls are applied:

- Azure Firewall rules allow only required management protocols
- Deny-by-default policy blocks all other traffic
- Centralized logging and monitoring enabled

## Validation

Management access is validated by:

- Successful connectivity from Mgmt VM to App VM
- Azure Firewall metrics showing inspected management traffic
- Failed connectivity when attempting direct access without Hub transit

## Outcome

This design provides secure, controlled management access to application workloads while preserving strict network isolation and centralized security enforcement.

