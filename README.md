# Virtual Home Network Setup Using VMware Workstation and Fedora 41
![NetworkDesign](https://github.com/user-attachments/assets/0cf95974-d443-4c58-a29b-42695465ca43)

This project demonstrates how to set up a virtual home network using VMware Workstation with Fedora 41 as the operating system. The network simulates a router, DHCP server, multiple clients, and subnets. Wireshark is used for network traffic monitoring.

---

## **Table of Contents**

1. [Project Overview](#project-overview)
2. [System Requirements](#system-requirements)
3. [Network Design](#network-design)
4. [Step-by-Step Configuration](#step-by-step-configuration)
   - [Setting Up Virtual Networks](#setting-up-virtual-networks)
   - [Creating and Configuring the Router VM](#creating-and-configuring-the-router-vm)
   - [Creating and Configuring Client VMs](#creating-and-configuring-client-vms)
   - [Configuring DHCP and NAT](#configuring-dhcp-and-nat)
   - [Testing the Setup](#testing-the-setup)
5. [Adding a Second Subnet](#adding-a-second-subnet)
6. [Using Wireshark to Monitor Traffic](#use-wireshark-to-monitor-traffic)

---

## **Project Overview**
This setup simulates a home network within a virtual environment. A Fedora Server acts as the router, handling DHCP and NAT for client machines, and enabling subnet communication. Wireshark is used to analyze network traffic.

---

## **System Requirements**

- VMware Workstation
- Fedora 41 ISO (Server and Workstation editions)
- Minimum host system specifications:
  - CPU: 4 cores
  - RAM: 16 GB
  - Storage: 100 GB available

---

## **Network Design**


### **Components:**

- **Router VM:**
  - Fedora Server 41
  - Handles DHCP, NAT, and IP routing

- **Client VM 1:**
  - Fedora Workstation 41
  - Dynamic IP via DHCP

- **Client VM 2:**
  - Fedora Workstation 41
  - Static IP configuration

- **Optional:**
  - Additional isolated subnet simulation

### **Network Topology:**

| Network Adapter | Network Type       | Subnet IP       |
|-----------------|--------------------|-----------------|
| Adapter 1       | NAT                | Host Access     |
| Adapter 2       | Custom VMnet2      | 172.16.206.0/24 |
| Adapter 3       | Custon VMnet3      | Custom Subnet   |

---

## **Step-by-Step Configuration**

### **1. Setting Up Virtual Networks**
1. Open VMware **Virtual Network Editor**.
2. Create custom networks:
   - `VMnet2`: Host-only, subnet `172.16.206.0/24`.
   - `VMnet3`: Host-only, custom subnet `192.168.50.0/24`.
3. Ensure DHCP is disabled for these custom networks.
4. Ensure Connect a host virtual adapter to this network is disabled for these customs networks.

---

### **2. Creating and Configuring the Router VM**
1. Create a VM:
   - OS: Fedora Server 41
   - CPU: 4 cores
   - RAM: 4 GB
   - Storage: 20 GB
2. Attach network adapters:
   - Adapter 1: NAT
   - Adapter 2: Custom VMnet2
   - Adapter 3: Custom VMnet3
3. Boot and install Fedora Server.

4. Install necessary packages:
   ```bash
   sudo dnf install -y dhcp-server iptables-services
   ```
5. Enable IP forwarding:
   ```bash
   echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p
   ```
6. Configure DHCP (`/etc/dhcp/dhcpd.conf`):
   ```plaintext
   subnet 172.16.206.0 netmask 255.255.255.0 {
       range 172.16.206.100 172.16.206.200;
       option routers 172.16.206.1;
       option domain-name-servers 8.8.8.8, 8.8.4.4;
       default-lease-time 600;
       max-lease-time 7200;
   }
   ```
7. Specify the interface for DHCP (`/etc/sysconfig/dhcpd`):
   ```plaintext
   DHCPDARGS=ens192
   ```
8. Use the following command to assign the static 172.16.206.1 to the vmnet2 interface:
   ```bash
   nmcli connection modify ens192 ipv4.method manual ipv4.addresses 172.16.206.1/24 ipv4.gateway 172.16.206.1 ipv4.dns "8.8.8.8,8.8.4.4" connection.autoconnect yes
   ```   
9. Start and enable DHCP:
   ```bash
   sudo systemctl enable dhcpd
   sudo systemctl start dhcpd
   sudo systemctl status dhcpd
   ```

10. Configure NAT with iptables:
   ```bash
   sudo iptables -t nat -A POSTROUTING -o ens160 -j MASQUERADE
   sudo iptables-save | sudo tee /etc/sysconfig/iptables
   sudo systemctl enable iptables
   sudo systemctl start iptables
   ```

---

### **3. Creating and Configuring Client VMs**
1. Create Fedora Workstation VMs:
   - **ClientVM1** (DHCP):
     ```bash
     sudo nmcli connection modify ens160 ipv4.method auto connection.autoconnect yes
     sudo systemctl restart NetworkManager
     ```
   - **ClientVM2** (Static IP):
     ```bash
     sudo nmcli connection add type ethernet ifname ens160 con-name static-ip ipv4.method manual ipv4.addresses 172.16.206.10/24 ipv4.gateway 172.16.206.1 ipv4.dns "8.8.8.8,8.8.4.4" connection.autoconnect yes
     sudo nmcli connection up static-ip
     ```

---

### **4. Testing the Setup**
1. Verify IP configuration:
   ```bash
   ip addr show
   ```
2. Test connectivity:
   - Ping router from clients:
     ```bash
     ping -c 4 172.16.206.1
     ```
   - Test internet access:
     ```bash
     ping -c 4 google.com
     ```
---

## **Adding a Second Subnet**
1. Create `VMnet3` with a subnet (e.g., `192.168.50.0/24`).
2. Attach it to **RouterVM** and **ClientVM2**.
3. Configure static IPs on RouterVM and ClientVM2 for the new interface.
4. Test routing and connectivity.

---

## **Use Wireshark to monitor traffic**
1. Install Wireshark if you dont have it already. replace dnf with your distro package manager or Install it directly from their official website.
   ```bash
   sudo dnf update -y
   sudo dnf install wireshark -y
   ```
   ![Screenshot From 2025-01-21 17-15-03](https://github.com/user-attachments/assets/45554bad-7121-4d80-8e84-4dbd623d637f)
   ![Screenshot From 2025-01-21 17-15-27](https://github.com/user-attachments/assets/af564ca6-3cab-4548-9218-239aa4b20fab)
   <p align="center">Update packages and install wireshark from terminal.</p>

3. Launch Wireshark from the terminal or from the GUI
   ```bash
   wireshark &
   ```

---

### **License**
This project is licensed under the MIT License.
