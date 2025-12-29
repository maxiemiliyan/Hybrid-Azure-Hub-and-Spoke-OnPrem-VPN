# Management VNet to On-Premises Resource Access

## Overview

This document explains how **management workloads deployed in the Azure Management VNet** securely access **on-premises internal resources** as part of the hybrid Hub-and-Spoke architecture.

The access path is strictly controlled to ensure that **all management traffic is routed through the Azure Hub VNet**, inspected by Azure Firewall, and securely transported to the on-premises environment using a **Site-to-Site IPsec VPN**.

---

## Business Requirement

Administrators require access to on-premises resources for:

- System administration
- Monitoring and maintenance
- Troubleshooting hybrid workloads
- Managing internal services and storage

Direct access to on-premises resources from the Management VNet is **not permitted**. All access must follow a **centralized and auditable path**.

---

## Access Design

The management access model enforces the following rules:

- Management workloads cannot access on-prem resources directly
- All traffic must pass through:
  - Azure Hub VNet
  - Azure Firewall
  - Azure VPN Gateway
- Traffic must be encrypted and centrally inspected

This ensures strong security controls and visibility over administrative access.

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
        +-- VPN Gateway
                |
                |  IPsec Site-to-Site VPN (IKEv2)
                v
On-Premises Network
        |
        +-- RRAS
        |
        +-- Internal Resource (Storage / Service)

## Routing Enforcement

Routing is enforced using:

- User Defined Routes (UDRs) applied to the Mgmt subnet
- Default route (0.0.0.0/0) pointing to Azure Firewall
- No direct routing from Management VNet to on-prem networks

This prevents management traffic from bypassing the Hub and firewall controls.

## Security Controls

The following security mechanisms are applied:

- Azure Firewall network rules explicitly allow required management traffic
- Deny-by-default policy blocks all other access
- IPsec encryption protects data in transit
- On-prem resources are not exposed to the internet

## Validation

Management access to on-prem resources is validated by:

- Successful connectivity from Mgmt VM to on-prem resource
- Azure Firewall metrics showing inspected management traffic
- VPN Gateway ingress and egress traffic confirming tunnel usage
- Failed access attempts when trying to bypass the Hub

## Outcome

This design provides secure, centralized, and auditable management access to on-premises resources while maintaining strict network isolation and enterprise-grade security controls.