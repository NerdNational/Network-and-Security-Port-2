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

4.2. Subnet 01 (Ubuntu Desktop)
Configured Static IP Address:
bash
Copy code
sudo nano /etc/netplan/01-netcfg.yaml
Example configuration:
yaml
Copy code
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      addresses:
        - 192.168.25.10/24
      gateway4: 192.168.25.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
4.3. Subnet 02 (Web Server)
Deployed Bitnami Web Server:
Downloaded a preconfigured VM from Bitnami.
Configured static IP for Subnet 02.
Challenges:
Routing Configuration: Troubleshooting connectivity between Subnet 01 and Subnet 02.
Firewall Rules: Allowing traffic while maintaining security.
5. Testing and Results
5.1. Connectivity Testing
Ping Tests:

Ubuntu Desktop ↔ Gateway Router: ping 192.168.25.1
Web Server ↔ Gateway Router: ping 192.168.125.1
Cross-Subnet Communication: ping 192.168.125.10 from Ubuntu Desktop.
Web Server Access:

Accessed the web application from Ubuntu Desktop using:
arduino
Copy code
http://192.168.125.10
5.2. Functional Tests
Verified internet access for all devices via the Gateway Router.
Used nslookup on the Web Server to resolve domain names:
bash
Copy code
nslookup bbc.co.uk
Challenges:
Latency: Resolved by optimizing VirtualBox network adapter settings.
Firewall Conflicts: Resolved by adjusting ufw rules:
bash
Copy code
sudo ufw allow from 192.168.25.0/24 to any
6. Lessons Learned
Subnetting Mastery: Gained practical experience in dividing networks into subnets.
Routing Skills: Configured and troubleshot complex routing scenarios using iptables.
Troubleshooting: Overcame network delays and adapter conflicts through experimentation.
