# Enterprise Active Directory & Identity Management in Microsoft Azure

[![Azure](https://img.shields.io/badge/Platform-Microsoft%20Azure-0078D4?logo=microsoftazure&logoColor=white)](#)
[![Windows Server](https://img.shields.io/badge/OS-Windows%20Server%202022-0078D6?logo=windows&logoColor=white)](#)
[![Active Directory](https://img.shields.io/badge/Service-AD%20DS%20%7C%20DNS-blue)](#)
[![Security](https://img.shields.io/badge/SOC%20Analysis-Windows%20Event%20Logs-green)](#)

## 📌 Executive Summary
This project demonstrates the end-to-end design, provisioning, and administration of an on-premises style enterprise identity environment hosted natively within **Microsoft Azure**. 

The implementation models a standard corporate IT topology: a dedicated **Windows Server 2022 Domain Controller (`corp.local`)** providing identity, authentication, and DNS services to domain-joined workstations. Beyond core systems administration, the environment was leveraged to validate Role-Based Access Control (RBAC), Group Policy scoping, and Windows Security Event auditing for ingestion into downstream SIEM platforms like Splunk.

---

## 🛠️ Architecture & Specifications

### Network Topology & Compute Specifications
* **Cloud Platform:** Microsoft Azure
* **Virtual Network (VNet):** `AD-VNet` (Address Space: `10.0.0.0/16`, Subnet: `10.0.0.0/24`)
* **Domain Controller (`DC-01`):**
  * **OS:** Windows Server 2022 Datacenter x64 Gen2
  * **Roles:** Active Directory Domain Services (AD DS), DNS Server
  * **IP Configuration:** Static private IP (`10.0.0.4`), DNS loopback pointing to localhost
* **Client Workstation (`Client-01`):**
  * **OS:** Windows 11 Enterprise
  * **Role:** Domain-joined managed client endpoint
  * **DNS:** Configured statically to point to `DC-01` (`10.0.0.4`)

```text
               +---------------------------------------------------+
               |               Azure Virtual Network               |
               |                     (AD-VNet)                     |
               |                                                   |
               |  +--------------------+   +--------------------+  |
               |  |     DC-01 (VM)     |   |   Client-01 (VM)   |  |
               |  |  Windows Svr 2022  |   |     Windows 11     |  |
               |  |  AD DS & DNS Role  |<--|    Domain Joined   |  |
               |  |    10.0.0.4        |   |      10.0.0.5      |  |
               |  +--------------------+   +--------------------+  |
               +---------------------------------------------------+
