# Solution Overview

## Overview

To address the challenges outlined in the problem statement, this project implements a **secure hybrid Hub-and-Spoke architecture** that enables controlled communication between **on-premises and Azure workloads**.

The solution integrates an **on-premises Windows Server 2019 environment hosted on Hyper-V** with **Microsoft Azure**, using a **route-based Site-to-Site IPsec VPN** and centralized routing and security enforcement through an **Azure Hub Virtual Network**.

---

## Proposed Solution

The solution is built around the following core components:

- **On-Premises Environment**
  - Hosts internal enterprise resources
  - Acts as a secure endpoint for hybrid connectivity

- **Azure Hub Virtual Network**
  - Serves as the central routing and security control point
  - Hosts shared connectivity and security services

- **Azure Spoke Virtual Networks**
  - Separate networks for application and management workloads
  - Fully isolated from each other

All communication between workloads is **forced through the Hub VNet**, ensuring consistent security inspection and controlled traffic flow.

---

## Key Architectural Decisions

### 1. Hub-and-Spoke Network Model

A Hub-and-Spoke design was chosen to:
- Centralize routing and security controls
- Prevent direct communication between isolated workloads
- Simplify future scalability and expansion
- Align with enterprise networking best practices

---

### 2. Secure Hybrid Connectivity

- Implemented a **route-based Site-to-Site VPN** using IPsec and IKEv2
- VPN tunnel established between:
  - On-premises RRAS server
  - Azure VPN Gateway in the Hub VNet
- Ensures encrypted and reliable communication between environments

---

### 3. Centralized Security Enforcement

- Deployed **Azure Firewall** in the Hub VNet
- Configured **Firewall Policies** to explicitly allow required traffic flows
- Denied all unauthorized or unintended traffic by default

This ensures:
- Consistent inspection of hybrid and inter-VNet traffic
- Improved visibility and auditability
- Reduced security misconfiguration risks

---

### 4. Workload Isolation

- Application workloads deployed in a dedicated **App VNet**
- Administrative workloads deployed in a dedicated **Management VNet**
- No direct peering or routing between spokes

Isolation reduces:
- Attack surface
- Risk of lateral movement
- Operational complexity

---

## End-to-End Traffic Flow

The solution enforces the following communication patterns:

- App workloads access on-prem resources **via the Hub**
- Management workloads access both App and On-Prem environments **via the Hub**
- All traffic is inspected by Azure Firewall before reaching its destination

---

## Benefits of the Solution

- Secure hybrid connectivity
- Centralized routing and security management
- Clear separation of duties between application and management workloads
- Scalable and extensible network design
- Enterprise-aligned architecture suitable for real-world deployments

---

## Outcome

This solution provides a **robust, secure, and scalable hybrid infrastructure** that enables seamless workload communication while maintaining strict security and isolation requirements. It serves as a practical demonstration of enterprise-grade hybrid cloud networking using Microsoft Azure and Windows Server technologies.
