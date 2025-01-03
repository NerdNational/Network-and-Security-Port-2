# Portfolio 02: Building a Virtual Sandboxed Network

This repository documents the process, configurations, and outcomes of the **Portfolio 02: Building a Virtual Sandboxed Network** project. Below, you will find a detailed explanation of each stage, along with challenges encountered and solutions implemented.

---

## 1. Overview of the Network Design
This project involves creating a sandboxed virtual network with three key components:

- **Ubuntu Desktop (Subnet 01)**: Used as a management interface to configure other network components.
- **Gateway Router**: Serves as the primary router for the network, connecting Subnet 01, Subnet 02, and the internet.
- **Web Server (Subnet 02)**: Hosts applications and services for testing network functionality.

The primary objective is to simulate a real-world network environment using **VirtualBox**, IP subnetting, and open-source software. The sandboxed network ensures a safe space to experiment with networking concepts and tools.

---

## 2. Network Diagram

![Network Diagram](network-diagram.png)

The network diagram illustrates the topology:
- **Internet** connects to the **Gateway Router**.
- **Subnet 01** connects to the **Ubuntu Desktop**.
- **Subnet 02** connects to the **Web Server**.

### Challenges:
- **Tool Selection**: Deciding between GNS3 and Packet Tracer for creating a clear, professional-looking diagram.
- **Subnet Design**: Determining IP ranges and ensuring they don't overlap or conflict.

---

## 3. IP Address Table

| Device               | Role                   | IP Address        | Subnet Mask      |
|----------------------|------------------------|-------------------|------------------|
| Ubuntu Desktop       | Management Interface   | 192.168.25.10     | 255.255.255.0    |
| Gateway Router (LAN) | Subnet 01 Interface    | 192.168.25.1      | 255.255.255.0    |
| Gateway Router (LAN) | Subnet 02 Interface    | 192.168.125.1     | 255.255.255.0    |
| Gateway Router (NAT) | Internet Interface     | Dynamic (NAT)     | N/A              |
| Web Server           | Application Server     | 192.168.125.10    | 255.255.255.0    |

### Challenges:
- **IP Allocation**: Ensuring unique addresses for each device.
- **Subnet Masks**: Choosing appropriate masks for segmentation and connectivity.

---

## 4. Configuration Steps

### 4.1. Gateway Router Setup
1. Created a virtual machine for the **Gateway Router** and installed Ubuntu Server.
2. Configured **three network adapters** in VirtualBox:
   - Adapter 1: **NAT** for internet access.
   - Adapter 2: **Internal Network** for Subnet 01.
   - Adapter 3: **Internal Network** for Subnet 02.
3. Installed and configured `iptables` for routing:
   ```bash
   sudo sysctl -w net.ipv4.ip_forward=1
   sudo iptables -A FORWARD -i enp0s3 -o enp0s8 -j ACCEPT
   sudo iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT
   sudo iptables -A POSTROUTING -t nat -j MASQUERADE

# Lab Report: Network Configuration and Troubleshooting

## Introduction

This lab report details the process of configuring static IP addresses for two subnets within a virtual network environment. The lab involved deploying a pre-configured web server from Bitnami and establishing communication between the subnets.

## Subnet Configuration

### 4.2. Subnet 01 (Ubuntu Desktop)

1. **Configured Static IP Address:**
    * Used `sudo nano /etc/netplan/01-netcfg.yaml` to edit the network configuration file.
    * Implemented the following configuration (replace with your specific IP address and gateway):

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      addresses:
        - 192.168.25.10/24  # Replace with your desired IP address
      gateway4: 192.168.25.1   # Replace with your gateway IP address
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
4.3. Subnet 02 (Web Server)
Deployed Bitnami Web Server:
Downloaded and deployed a pre-configured virtual machine from Bitnami for the web server.
Configured Static IP Address:
Assigned a static IP address within Subnet 02 (e.g., 192.168.125.10) to the web server.
Challenges
4.3.1. Routing Configuration
Issue: Establishing communication between devices on Subnet 01 and Subnet 02.
Resolution: Configured routing rules (e.g., using iptables) to direct traffic between the subnets.
4.3.2. Firewall Rules
Issue: Balancing security and allowing necessary traffic through the firewall.
Resolution: Implemented firewall rules (e.g., using ufw) to allow traffic while maintaining security.
Testing and Results
5.1. Connectivity Testing
Ping Tests:
Verified connectivity between devices within each subnet and with the gateway router using ping commands.
Example: ping 192.168.25.1 (from Ubuntu Desktop to gateway router).
Cross-Subnet Communication:
Confirmed communication between Subnet 01 and Subnet 02 using ping commands (e.g., pinging the web server IP from the Ubuntu Desktop).
Web Server Access:
Accessed the web application from the Ubuntu Desktop using the web server's IP address in a web browser (e.g., http://192.168.125.10).
5.2. Functional Tests
Internet Access:
Verified internet access for all devices on both subnets through the gateway router.
DNS Resolution:
Used nslookup on the web server to confirm successful domain name resolution (e.g., nslookup bbc.co.uk).
5.2.1. Challenges
Latency:
Encountered latency issues, which were resolved by optimizing the VirtualBox network adapter settings.
Firewall Conflicts:
Adjusted firewall rules (e.g., using ufw commands) to address conflicts and allow necessary traffic.
Lessons Learned
Subnetting: Gained practical experience in configuring subnets for network segmentation.
Routing: Enhanced skills in configuring and troubleshooting routing between subnets using tools like iptables.
Troubleshooting: Developed problem-solving techniques to overcome network delays and adapter configuration issues.
Conclusion
This lab successfully configured static IP addresses for two subnets, established communication between them, and ensured internet access for devices. The troubleshooting process provided valuable insights into routing, firewall rules, and network optimization techniques.
