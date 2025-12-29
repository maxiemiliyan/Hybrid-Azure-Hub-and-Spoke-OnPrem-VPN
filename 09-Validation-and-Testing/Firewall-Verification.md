# Firewall Verification

## Overview

This document verifies that **Azure Firewall** is correctly enforcing **centralized security controls** in the hybrid Hub-and-Spoke architecture.

The verification ensures that:
- All permitted traffic is inspected and allowed
- All unauthorized or bypass traffic is blocked
- No workload can communicate without passing through the Hub VNet

This step confirms that the security design is working as intended.

---

## Verification Objectives

The firewall verification process validates:

- Mandatory inspection of all inter-VNet traffic
- Mandatory inspection of all Azure ↔ On-Prem traffic
- Proper enforcement of allow and deny rules
- No possibility of bypassing the Azure Firewall

---

## Firewall Inspection Points

Azure Firewall inspects traffic at the following points:

- App VNet → On-Prem
- Mgmt VNet → App VNet
- Mgmt VNet → On-Prem
- On-Prem → Azure workloads

No direct traffic path exists outside these inspection points.

---

## Verification Methodology

Firewall behavior is verified using:

- Azure Firewall metrics
- Azure Monitor insights
- Controlled positive and negative connectivity tests
- Observation of routing enforcement via UDRs

---

## Verification Checks

### 1. Allowed Traffic Verification

#### Scenario
Verify that permitted traffic flows are allowed and inspected.

**Examples:**
- App VNet → On-Prem resource
- Mgmt VNet → App VNet
- Mgmt VNet → On-Prem resource

**Expected Behavior:**
- Connectivity succeeds
- Traffic is visible in Azure Firewall metrics
- VPN Gateway ingress/egress counters increase (for on-prem flows)

---

### 2. Blocked Traffic Verification

#### Scenario
Verify that unauthorized traffic is blocked.

**Examples:**
- Direct App VNet → Mgmt VNet access
- Direct Spoke → On-Prem access bypassing Hub
- Any traffic not explicitly allowed by firewall policy

**Expected Behavior:**
- Connectivity fails
- No traffic bypasses Azure Firewall
- Deny behavior enforced by default

---

### 3. Hub Bypass Prevention

#### Scenario
Verify that workloads cannot bypass the Hub VNet.

**Validation Steps:**
- Confirm UDRs are applied to App and Mgmt subnets
- Verify default route points to Azure Firewall
- Attempt direct routing changes (negative test)

**Expected Behavior:**
- Traffic is still forced through Azure Firewall
- No direct routing paths are available

---

## Logging and Metrics Validation

Firewall verification includes reviewing:

- Azure Firewall metrics:
  - Allowed traffic counts
  - Denied traffic counts
- Azure Monitor logs:
  - Traffic flow visibility
  - Rule hit confirmation

These logs provide audit evidence of centralized inspection.

---

## Verification Summary

| Verification Area | Result |
|------------------|--------|
| Allowed Traffic Inspection | Verified |
| Unauthorized Traffic Blocking | Verified |
| Hub Bypass Prevention | Verified |
| Centralized Logging | Verified |

---

## Outcome

Firewall verification confirms that **Azure Firewall is correctly enforcing centralized security controls**, preventing unauthorized access, and ensuring all hybrid and inter-VNet traffic is inspected.

This validates that the security architecture meets **enterprise-grade networking and compliance requirements**.
