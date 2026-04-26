# Multi-Cloud Web Server Deployment (AWS & Azure)

**Author:** Oluwasheyi Ojelade
**Date:** February 4, 2026
**Status:** ✅ Completed

---

## Project Overview

This project focuses on designing and deploying a secure, 3-tier network architecture across two major cloud platforms: **AWS** and **Microsoft Azure**.
The goal was to demonstrate proficiency in:

- **Infrastructure as a Service (IaaS):** Manually configuring VPCs/VNets, Subnets, and Gateways.
- **Network Security:** Implementing stateless (NACLs) and stateful (Security Groups/NSGs) firewalls.
- **Linux Administration:** Provisioning Nginx web servers via CLI and deploying a custom portfolio website.

---

## Architecture Design

**Objective:** Create a logical 3-tier network diagram (Frontend, Backend, Database) to guide the deployment.

- **VPC/VNet CIDR:** `192.168.0.0/16`
- **Subnets:**
    - **Frontend (Public):** `192.168.0.0/24`
    - **Backend (Private):** `192.168.1.0/24`
    - **Database (Private):** `192.168.2.0/24`
- **Connectivity:** Internet Gateway attached to the VPC to allow public traffic.
- **Security:** Subnet-level filtering (NACL) and Instance-level firewalls (Security Groups/NSGs).

*The logical network diagram showing the 3-Tier architecture and traffic flow.*

![WhatsApp Image 2026-02-06 at 11.55.43.jpeg](WhatsApp_Image_2026-02-06_at_11.55.43.jpeg)

---

## ☁️ 3. AWS Implementation

**Objective:** Translate the architectural drawing into AWS resources and deploy the server.

### **Phase 1: Infrastructure Setup**

1. **VPC Creation:** Created `My-Project-VPC` with custom CIDR `192.168.0.0/16`.
2. **Internet Gateway:** Created and attached `My-Project-IGW` to the VPC to enable internet access.
3. **Route Tables:** Configured the public route table to direct traffic `0.0.0.0/0` → **Internet Gateway**.
4. **Subnet Association:** Associated the Frontend subnet with the Public Route Table.

> 
> 
> 
> ![AWS INTERNET GW ROUTE ATTACH.JPG](AWS_INTERNET_GW_ROUTE_ATTACH.jpg)
> 

![AWS ROUTE TABLE.JPG](AWS_ROUTE_TABLE.jpg)

### **Phase 2: Security Configurations**

- **NACL (Subnet Level):** Configured Inbound/Outbound rules.
    - *Troubleshooting Note:* Explicitly allowed **Ephemeral Ports (1024-65535)** to ensure return traffic for `apt update` worked.
- **Security Groups (Instance Level):** Configured Inbound rules to allow:
    - **SSH (Port 22):** From `My IP`.
    - **HTTP (Port 80):** From `Anywhere`.

> 
> 
> 
> ![image.png](image.png)
> 

![image.png](image%201.png)

### **Phase 3: Linux Configuration & Deployment**

**Objective:** SSH into the EC2 instance and configure the Nginx web server.

**1. System Verification & Updates**
Verified user identity and updated package repositories.

Bash

`whoami                  # Checks the current logged-in user (e.g., ubuntu)
who                     # Shows who is logged into the system
sudo apt update         # Updates the local package index
apt list --upgradable   # Lists packages that have newer versions available
sudo apt upgrade        # Upgrades all installed packages`

**2. Web Server Installation (Nginx)**
Installed the web server and confirmed it was active.

Bash

`sudo apt install nginx -y    # Installs Nginx
sudo systemctl status nginx  # Checks if service is 'Active (running)'`

**3. Navigation & File Exploration**
Navigated to the web root directory.

Bash

`cd /var/www/html        # Go into the Web Root folder
ls                      # Lists the default index file`

**4. Deploying Custom Application**
Simulated a real-world deployment by uploading the "Ethos" project zip file.

Bash

`sudo apt install unzip -y           # Install the 'unzip' utility
unzip Oluwasheyi.zip                # Extract the uploaded project file
sudo cp -r Ethos-1.0.0/* /var/www/html/  # Copy files to web root
sudo rm Oluwasheyi.zip              # Cleanup`

> 
> 
> 
> ![image.png](image%202.png)
> 

---

![image.png](image%203.png)

## 🔷Microsoft Azure Implementation

**Objective:** Replicate the setup on Azure using a Virtual Network (VNet) and Linux VM.

### **Phase 1: Infrastructure Setup**

1. **Resource Group:** Created `Project-ResourceGroup` to hold all assets.
2. **Virtual Network (VNet):** Created `Project-VNet` with address space `10.0.0.0/16`.
3. **Subnetting:** Created a `Frontend-Subnet` (`10.0.0.0/24`) inside the VNet.

> 
> 
> 
> ![Create VM Dep 1.JPG](Create_VM_Dep_1.jpg)
> 

### **Phase 2: Virtual Machine Deployment**

1. **Provisioning:** Launched a **Linux (Ubuntu 20.04)** Virtual Machine named `KlipNaija-VM`.
2. **Networking:** Attached the VM to the `Frontend-Subnet`.
3. **Network Security Group (NSG):**
    - Added an Inbound Rule to allow **Port 80 (HTTP)**.
    - Added an Inbound Rule to allow **Port 22 (SSH)**.

> 
> 
> 
> ![Create VM 2.JPG](Create_VM_2.jpg)
> 

![Create VM 3.JPG](Create_VM_3.jpg)

### **Phase 3: Deployment**

1. Connected via SSH using Termius.
2. Installed Nginx (`sudo apt install nginx`).
3. Uploaded the portfolio website files via SFTP to `/var/www/html`.
4. Restarted Nginx to apply changes.

> 
> 
> 
> ![Nginx installed.JPG](Nginx_installed.jpg)
> 

---

![Powershell commands running 1.JPG](Powershell_commands_running_1.jpg)

## **Troubleshooting Log**

During the AWS deployment, I encountered specific networking challenges:

**Issue 1: Connection Timed Out during `apt update`**

- **Cause:** The NACL was blocking return traffic from the internet because NACLs are stateless.
- **Fix:** Added an **Inbound Rule** to the NACL allowing **All TCP** (covering ephemeral ports 1024-65535).

**Issue 2: Site Not Loading in Browser**

- **Cause:** The Security Group was blocking Port 80 by default.
- **Fix:** Added an **Inbound Rule** to the Frontend Security Group allowing **HTTP (Port 80)** from `0.0.0.0/0`.

---

## 🚀 6. Final Result

Successfully accessed the web server on both clouds via their respective Public IPs. The custom HTML content matches the deployed files.

![Website up and running PB IP.JPG](Website_up_and_running_PB_IP.jpg)

![Website finally running on oluwasheyi.virusinferno.JPG](Website_finally_running_on_oluwasheyi.virusinferno.jpg)

## 🎯 Project Summary & Conclusion

This project successfully demonstrated the end-to-end deployment of a **3-Tier Network Architecture** across both **AWS** and **Azure** cloud environments. By manually configuring the underlying network infrastructure, including VPCs, Subnets, Route Tables, and Firewalls. I established a secure and scalable foundation for hosting web applications.

**Key Achievements:**

- **Cross-Cloud Competency:** Successfully replicated the architecture on both AWS and Azure, proving versatility in handling different cloud providers.
- **Security-First Approach:** Implemented a "Defense in Depth" strategy using both Network ACLs (subnet level) and Security Groups (instance level).
- **Troubleshooting:** Diagnosed and resolved real-world connectivity issues related to stateless firewalls and port blocking.
- **Linux Administration:** Demonstrated proficiency in managing Linux servers via CLI, including package management, service configuration, and file permissions.

This project serves as a practical proof of concept for deploying static websites in a secure, enterprise-grade cloud environment.

### 🌐 Live Demo

The final portfolio website is currently live and accessible via the custom domain configured below:

Final Portfolio Website URL: oluwasheyi-portfolio.virusinferno.xyz

![image.png](image%204.png)