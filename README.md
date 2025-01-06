# Building a Virtual Sandboxed Network Using VirtualBox 

#### Table of Contents
- [Introduction](#introduction)
- [What is a Sandboxed Network and Why is it Needed?](#what-is-a-sandboxed-network-and-why-is-it-needed)
- [Prerequisites](#prerequisites)
- [Network Setup](#network-setup)
- [Network Diagram](#network-diagram)
- [IP Tables](#ip-tables)
- [Configuration Process](#configuration-process)
- [Challenges and Lessons Learned](#challenges-and-lessons-learned)
- [Conclusion](#conclusion)

## **Introduction**
A sandboxed network is a secure, isolated environment designed to simulate real-world network scenarios without posing any risks to live systems. It allows professionals and learners to experiment, test configurations, and resolve networking challenges in a controlled setting. 

## **What is a Sandboxed Network and Why is it Needed?**

A sandboxed network is a secure, isolated environment that simulates real-world network scenarios without impacting live systems. It enables professionals and learners to experiment, test configurations, and troubleshoot networking issues in a controlled setup. 

**Key Applications of a Sandboxed Network:**
- **Learning and Development:** Explore networking concepts in a risk-free environment.
- **Testing and Experimentation:** Deploy and test configurations safely.
- **Security Training:** Simulate attack and defence scenarios to improve cybersecurity preparedness.
---
## Prerequisites

1. Knowledge of operating systems, e.g., Windows and Linux.
2. Knowledge of networking basics, IP addresses and subnetting.
3. **VirtualBox:** For creating and managing virtual machines.

---
## **Network Setup**

This sandboxed network consists of three VMs configured to simulate a real-world scenario:
1. **Desktop VM:** Configured to manage other VMs, connected to Subnet 1 on Ubuntu Desktop.
2. **Gateway VM:** powered by the Ubuntu Server and acts as a router with three interfaces:
   - **Interface 1 (enp0s3):** Subnet 1 (Desktop VM connection).
   - **Interface 2 (enp0s8):** Subnet 2 (Application Server connection).
   - **Interface 3 (enp0s9):** Internet access via NAT.
3. **Application Server VM:** Hosted Subnet 2 on Bitnami Mastodon and connected to the Gateway VM.

---
## Network Diagram
![Network Diagram](https://github.com/bintMA/building_a_sandboxed_network/blob/main/Sandbox%20Network%20Diagram%20-%20Adeola.png)

---
## IP Tables
| **DEVICE** | **ROLE**  | **IP ADDRESS**  | **SUBNET MASK**  |
|------------|-----------|-----------------|------------------|
|Desktop VM | Management  | 192.168.1.2 | 255.255.255.0 |
|Gateway Router VM (enp0s3) | Subnet 01 - Internal Network   | 192.168.1.1 | 255.255.255.0 |
|Gateway Router VM (enp0s8) | Subnet 02 - Internal Network   | 192.168.101.1 | 255.255.255.0 |
|Gateway Router VM (enp0s9) | NAT/Internet Access   | 10.0.2.15 | 255.255.255.0 |
|Application Setver | Server   | 192.168.101.2 | 255.255.255.0 |

---

## **Configuration Process**

1. **Set Up Virtual Machines:**
   - Install Ubuntu Desktop, Ubuntu Server, and Bitnami Mastodon on VirtualBox.
   - Configure network interfaces for each VM as per the IP Address Table.

2. **Subnet Configuration:**
   - Assign private IP ranges to Subnet 1 and Subnet 2 to ensure isolation.
   - Use `sudo nano /etc/systemd/network/*.network` to set static IPs.

3. **Gateway Configuration:**
   - Enable IP forwarding on the Ubuntu Server gateway using `sysctl.conf`.
   - Configure routing with `iptables` to allow communication between subnets.
      ```bash
     sudo iptables -A FORWARD -i enp0s3 -o enp0s8 -j ACCEPT
     sudo iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT
     sudo iptables -A FORWARD -i enp0s9 -o enp0s3 -j ACCEPT
     sudo iptables -A FORWARD -i enp0s3 -o enp0s9 -j ACCEPT
     ```
4. **Test Connectivity:**
   - Verify that the Desktop VM can communicate with the Application Server VM via the Gateway VM.
   - Ensure internet access from the Desktop VM through the Gateway.

---
## **Challenges and Lessons Learned**

### **Key Challenges**
1. **Multi-NIC Configuration Complexity**
   - Balancing traffic across three interfaces was trickier than expected.
   - **Reflection:** I realised how critical it is to map out routing logic before diving into configurations.

2. **Firewall Rules**
   - Initial firewall settings blocked inter-subnet traffic.
   - **Resolution:** Thoroughly tested `iptables` rules, ensuring security and functionality.
  

### **Lessons Learned**
- **The Power of Planning:** Every successful configuration began with a well-thought-out plan.
- **Patience Pays:** Debugging routing issues taught me the importance of systematic troubleshooting.
- **Documentation is Key:** Keeping detailed records saved time and helped me reflect on what went wrong and how to fix it.

---

## **Conclusion**

This project allowed me to apply theoretical knowledge to practical scenarios. I gained valuable experience in network design, configuration, and troubleshooting by creating a sandboxed network. 

---
[Link to Functionality Test Video](https://www.loom.com/share/7dbb6c7ee4aa49e3bb6a8ca3a5500342?sid=f773fa6d-f06a-417b-b182-f6a11ee06d1b)
