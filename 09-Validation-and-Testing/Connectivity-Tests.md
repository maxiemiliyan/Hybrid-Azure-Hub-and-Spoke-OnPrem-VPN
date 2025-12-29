# Connectivity Tests

## Overview

This document outlines the **connectivity validation tests** performed to verify that the hybrid Hub-and-Spoke architecture functions as designed.

The tests confirm:
- Secure hybrid connectivity
- Correct routing enforcement
- Proper workload communication paths
- Absence of unauthorized or direct network access

All tests are aligned with real enterprise validation practices.

---

## Test Objectives

The connectivity tests are designed to validate that:

- The Site-to-Site VPN tunnel is operational
- Application workloads can access on-prem resources securely
- Management workloads can access both application and on-prem resources
- No direct spoke-to-spoke or bypass traffic is possible
- All traffic is routed through the Azure Hub VNet

---

## Test Environment

| Component     | Description                                   |
|---------      |-------------                                  |
| On-Prem VM    | Windows Server 2019 (Hyper-V)                 |
| App VM        | Application workload in App VNet              |
| Mgmt VM       | Management / Jumpbox VM in Mgmt VNet          |
| Hub           | Azure Hub VNet with Firewall and VPN Gateway  |

---

## Test Case 1: App VNet to On-Prem Resource

### Objective
Verify that application workloads can access on-prem resources via the Hub.

### Test Steps
1. Log in to the Application VM (App VNet)
2. Attempt to connect to the on-prem internal resource
3. Observe connectivity behavior

### Expected Result
- Connection is successful
- Traffic passes through Azure Firewall and VPN Gateway

### Validation
- Azure Firewall metrics show traffic inspection
- VPN Gateway ingress/egress counters increase

---

## Test Case 2: Mgmt VNet to App VNet

### Objective
Verify that management workloads can access application workloads via the Hub.

### Test Steps
1. Log in to the Management VM
2. Attempt to access the Application VM
3. Observe connectivity behavior

### Expected Result
- Connection is successful
- Traffic is inspected by Azure Firewall

### Validation
- Azure Firewall logs show management traffic
- No direct peering-based routing observed

---

## Test Case 3: Mgmt VNet to On-Prem Resource

### Objective
Verify that management workloads can access on-prem resources securely.

### Test Steps
1. Log in to the Management VM
2. Attempt to access the on-prem internal resource
3. Observe connectivity behavior

### Expected Result
- Connection is successful
- Traffic flows through Hub and VPN

### Validation
- Azure Firewall metrics show inspected traffic
- VPN Gateway traffic counters confirm tunnel usage

---

## Test Case 4: Direct Spoke-to-Spoke Access (Negative Test)

### Objective
Ensure direct communication between App and Mgmt VNets is blocked.

### Test Steps
1. Attempt direct connectivity from App VM to Mgmt VM without Hub routing
2. Observe connectivity behavior

### Expected Result
- Connection fails
- No bypass of Azure Firewall

### Validation
- No successful traffic without firewall inspection
- Firewall deny behavior confirmed

---

## Test Case 5: Bypass Hub Attempt (Negative Test)

### Objective
Ensure traffic cannot bypass the Hub VNet.

### Test Steps
1. Attempt routing changes to bypass UDRs
2. Attempt direct access to on-prem resources

### Expected Result
- Connection fails
- Routing enforcement remains intact

---

## Test Summary

| Test Case                 | Result    |
|---------                  |--------   |
| App → On-Prem             | Passed    |
| Mgmt → App                | Passed    |
| Mgmt → On-Prem            | Passed    |
| Spoke → Spoke (Direct)    | Blocked   |
| Hub Bypass Attempt        | Blocked   |

---

## Outcome

The connectivity tests confirm that:

- Hybrid connectivity is functioning as designed
- Routing and security controls are correctly enforced
- Workloads communicate only through approved paths
- The architecture meets enterprise security and design requirements
