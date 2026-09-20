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
---

## 🚀 Deployment & Implementation

### 1. Cloud Infrastructure & Virtual Network Provisioning
1. Provisioned a dedicated resource group (`AD-Lab`) in `US-West 2` to isolate lab resources.
2. Established an Azure Virtual Network (`AD-VNet`) with a default subnet to facilitate flat Layer 3 communication between compute nodes.
3. Deployed two VMs (`DC-01` and `Client-01`) within the subnet, attaching Network Security Groups (NSGs) allowing controlled inbound RDP access (Port 3389).

<p align="center">
  <img width="850" alt="Resource Group Deployment" src="https://github.com/user-attachments/assets/43e3c00d-b3db-4ef7-89bf-cbd00e3c9a1c" />
</p>

---

### 2. Domain Controller Promotion (`DC-01`)
1. Connected via RDP to `DC-01` using initial local administrator credentials.
2. Installed **Active Directory Domain Services (AD DS)** and the **DNS Server** role via Server Manager.
3. Promoted the server to a Domain Controller:
   * **Deployment Operation:** Add a new forest
   * **Root Domain Name:** `corp.local`
   * **Forest & Domain Functional Level:** Windows Server 2022
   * Configured Directory Services Restore Mode (DSRM) credentials and rebooted the system to apply directory partitions.

<p align="center">
  <img width="850" alt="AD DS Role Promotion" src="https://github.com/user-attachments/assets/85b36e85-a67a-4af1-a5f4-fcfb375cca85" />
</p>

---

### 3. DNS Redirection & Domain Join (`Client-01`)
For successful domain resolution, the client machine must resolve internal DNS queries against the domain controller rather than Azure's public virtual IP resolver.

1. Accessed `Client-01` via RDP and navigated to the network adapter properties (`IPv4`).
2. Pointed **Preferred DNS Server** directly to `DC-01`'s private internal IP (`10.0.0.4`).
3. Appended the client machine to the `corp.local` domain via **System Properties > Computer Name / Domain Changes**, providing Domain Admin credentials (`corp\adminuser`) to authorize the machine account creation in AD.
4. Rebooted the endpoint and verified membership via the command line:
   ```cmd
   whoami
