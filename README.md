# Hybrid Azure Hub-and-Spoke Architecture with On-Premises Workload Integration

## Project Overview

This project demonstrates a **real-world hybrid cloud architecture** where **on-premises and Azure workloads actively communicate with each other** using a **Hub-and-Spoke network design**.

An **on-premises Windows Server 2019 virtual machine hosted on Hyper-V** acts as the internal data center environment. Azure hosts two isolated workloads:
- An **Application VNet** for business applications
- A **Management VNet** for administration and monitoring

All communication between **On-Prem**, **App VNet**, and **Management VNet** is **strictly forced through a central Hub VNet**, which contains the **VPN Gateway and Azure Firewall**.  
There is **no direct connectivity** between spokes or to on-prem resources.

This project represents how enterprises securely expose on-prem resources (such as storage or internal services) to cloud workloads while maintaining **centralized security, visibility, and control**.

---

## Business Scenario

An organization has:

- **On-Premises Data Center**
  - Hosts internal resources such as storage and core services
- **Azure App Environment**
  - Runs application workloads that need access to on-prem data
- **Azure Management Environment**
  - Used by administrators to manage and monitor both on-prem and application resources

### Key Requirements

- App workloads must access on-prem resources securely
- Management workloads must access both App and On-Prem environments
- App and Management networks must **not communicate directly**
- All traffic must be inspected and controlled centrally
- Hybrid connectivity must be secure and scalable

---

## High-Level Architecture

On-Premises (Hyper-V)
    └─ Windows Server 2019
        └─ Internal Resource (Example: Storage / Service)
            └─ RRAS (Routing + VPN)
                │
                │ Site-to-Site IPsec VPN (IKEv2)
                │
Azure Hub VNet
├─ VPN Gateway (Route-based)
├─ Azure Firewall
├─ Firewall Policy
└─ Central Routing Control
    │
    ├─ App VNet (Application Resource)
    └─ Management VNet (Admin Resource)



---

## Defined Workloads

### On-Premises
- Windows Server 2019 VM (Hyper-V)
- Internal resource (simulated storage/service)
- RRAS configured for Site-to-Site VPN and routing

### Azure App VNet
- Application workload VM
- Requires access to on-prem resources only via Hub

### Azure Management VNet
- Management / jumpbox VM
- Requires access to both App and On-Prem resources via Hub

---

## Network Design

### Address Space

| Environment       | IP Range          |
|------------       |----------         |
| On-Prem LAN       | `10.0.0.0/24`     |
| Azure Hub VNet    | `10.1.0.0/16`     |
| Azure App VNet    | Isolated subnet   | 
| Azure Mgmt VNet   | Isolated subnet   |

---

## Traffic Flow Rules (Core of This Project)

### Enforced Communication Paths

| Source        | Destination           | Path                                      |
|------         |---------------------- |-------------------------------------------|
| App VNet      | On-Prem Resource      | App → Hub → Firewall → VPN → On-Prem      |
| Mgmt VNet     | App VNet              | Mgmt → Hub → Firewall → App               |
| Mgmt VNet     | On-Prem Resource      | Mgmt → Hub → Firewall → VPN → On-Prem     |

### Explicitly Blocked Paths

- ❌ App VNet → Mgmt VNet (direct)
- ❌ Mgmt VNet → App VNet (direct)
- ❌ App VNet → On-Prem (bypassing Hub)
- ❌ Any spoke-to-spoke communication without Hub transit

---

## Hybrid Connectivity Implementation

- Azure **Route-based VPN Gateway** deployed in Hub VNet
- **Local Network Gateway** represents on-prem environment
- **Site-to-Site IPsec VPN** established between Azure and RRAS
- RRAS configuration includes:
  - Demand-dial VPN interface (IKEv2)
  - Static routing for Azure networks
  - IP forwarding enabled
  - NAT disabled for route-based VPN compatibility

---

## Security and Routing Enforcement

- **Azure Firewall** deployed in Hub VNet
- Firewall Policy controls:
  - App ↔ On-Prem access
  - Mgmt ↔ App access
  - Mgmt ↔ On-Prem access
- **User Defined Routes (UDRs)** applied to App and Mgmt subnets:
  - Force all outbound traffic to Azure Firewall
- Centralized inspection and logging enabled

---

## Validation and Testing

- Verified VPN tunnel establishment
- Tested connectivity:
  - App VM → On-Prem resource
  - Mgmt VM → App VM
  - Mgmt VM → On-Prem resource
- Confirmed:
  - No direct spoke-to-spoke routing
  - Firewall rule hit counts increasing
  - VPN gateway ingress/egress traffic

---

## Key Learnings

- Designed a **workload-driven hybrid network**, not just connectivity
- Implemented strict hub-and-spoke routing enforcement
- Gained hands-on experience with:
  - RRAS and Site-to-Site VPN
  - Azure Firewall and UDR-based traffic control
  - Hybrid workload communication patterns
- Understood real enterprise security and routing dependencies

---

## Resume-Ready Summary

Designed and implemented a workload-driven hybrid Azure hub-and-spoke architecture integrating an on-premises Windows Server 2019 VM hosted on Hyper-V. Enabled secure communication between on-prem, application, and management workloads using RRAS-based Site-to-Site VPN, Azure Firewall, and forced tunneling via UDRs, ensuring centralized security and controlled traffic flow.

---

## Repository Structure

Hybrid-Azure-Hub-Spoke-Hybrid-Workloads/
├── README.md
├── 01-Business-Scenario/
├── 02-Architecture/
├── 03-Network-Design/
├── 04-OnPrem-Environment/
├── 05-Azure-Environments/
├── 06-Hybrid-Connectivity/
├── 07-Security-and-Routing/
├── 08-Workload-Communication/
├── 09-Validation-and-Testing/
├── 10-Operational-Insights/
├── 11-Future-Enhancements/
└── screenshots/

---

## Future Enhancements

- Azure Bastion for secure management access
- Azure Monitor and Log Analytics integration
- Active-Active VPN Gateway
- Azure Site Recovery (ASR)
- Infrastructure as Code (Terraform/Bicep)

---

## Notes

This project is a hands-on simulation of a real enterprise hybrid infrastructure where **all workload communication is intentionally forced through a centralized hub for security, control, and auditability**.
