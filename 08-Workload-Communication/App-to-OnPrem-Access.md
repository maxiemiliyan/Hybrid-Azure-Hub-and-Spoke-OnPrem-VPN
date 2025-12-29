# Application VNet to On-Premises Resource Access

## Overview

This document explains how **application workloads deployed in the Azure App VNet** securely access **on-premises internal resources** using the hybrid Hub-and-Spoke architecture.

The access is intentionally designed to be **controlled, inspected, and auditable**, ensuring that application traffic can reach on-prem resources **only through the Azure Hub VNet**.

---

## Business Requirement

Application workloads running in Azure require access to internal on-premises resources such as:

- File storage
- Internal services
- Legacy applications

Direct exposure of on-prem resources to the internet or direct connectivity from application networks is **not permitted**.

---

## Access Design

The access model follows these rules:

- Application workloads **cannot** access on-prem resources directly
- All traffic must pass through:
  - Azure Hub VNet
  - Azure Firewall
  - Site-to-Site VPN
- Traffic must be encrypted and inspected

This ensures compliance with enterprise security standards.

---

## Traffic Flow

### Application VNet → On-Prem Resource

Application VM (App VNet)
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

---

## Routing Enforcement

Routing is enforced using:

- User Defined Routes (UDRs) on the App subnet
- Default route (`0.0.0.0/0`) pointing to Azure Firewall
- No direct routing to on-prem address space

This prevents traffic from bypassing centralized security controls.

---

## Security Controls

The following controls are applied:

- Azure Firewall network rules explicitly allow required traffic
- All other traffic is denied by default
- IPsec encryption protects data in transit
- On-prem resources remain non-public

---

## Validation

Access is validated by:

- Successful connectivity from App VM to on-prem resource
- Azure Firewall metrics showing inspected traffic
- VPN Gateway ingress and egress traffic increase
- Failed attempts when bypassing the Hub

---

## Outcome

This design enables **secure and controlled application access** to on-premises resources while maintaining strict security boundaries and centralized traffic inspection.

The implementation reflects **real-world enterprise hybrid workload access patterns**.
