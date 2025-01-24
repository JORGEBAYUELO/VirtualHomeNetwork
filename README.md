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

![Screenshot From 2025-01-21 12-26-01](https://github.com/user-attachments/assets/f9fb82bc-5b43-4486-bea4-72107bfa7ad9)

**Open VMware Workstation and create a new Virtual Machine. Go with the recommended configuration.**

![Screenshot From 2025-01-21 12-26-12](https://github.com/user-attachments/assets/ce56b520-ceb0-4a0c-9073-d71b3af6bc9c)

**Choose the option to install the operating system later.**

![Screenshot From 2025-01-21 12-26-22](https://github.com/user-attachments/assets/5e76f5f6-d2fb-4e6b-91b2-d1f7942f2e70)

**For this configuration we are going to choose Linux, and find the Fedora 64-bit version.**

![Screenshot From 2025-01-21 12-26-40](https://github.com/user-attachments/assets/32600297-005e-4cc4-959a-f6becdae53ef)

**Choose a name for your VM, and location to save it.**

![Screenshot From 2025-01-21 12-26-59](https://github.com/user-attachments/assets/a0765594-6c3d-4ef8-b24f-af8219dbccf7)

**Specify the disk capacity. In this case, I'm going with the minimum size recommended.**

![Screenshot From 2025-01-21 12-28-03](https://github.com/user-attachments/assets/01b24cfd-68b1-405b-ad6e-e4e5f80ce8a3)

**This is how the initial configuration will look like for the Router VM.**

![Screenshot From 2025-01-21 12-32-20](https://github.com/user-attachments/assets/02984db2-567f-445e-ace8-e3ba4a94e409)

**After the initial configuration for the Router VM. Go to the Virtual Network Editor in VMware and add a new network (vmnet2).**

![Screenshot From 2025-01-21 12-32-38](https://github.com/user-attachments/assets/cab9b05c-e70a-4d49-b9d8-b9518205f3a1)

**Set this new network adapter as a Host-only.**

![Screenshot From 2025-01-21 12-33-12](https://github.com/user-attachments/assets/79e4313b-eeb2-4834-af84-34459915024b)

**Make sure to disable (Use local DHCP service to distribute IP addresses to VMs), since the Router VM is going to be handling the DHCP.**
**Also make sure to disable (Connect a host virtual adapter vmnet2 to this network), to avoid interferance with the host machine.**

![Screenshot From 2025-01-21 12-34-01](https://github.com/user-attachments/assets/f9f6e7ac-717f-4ffe-9c35-b90cffe7eb1e)

**Since we are in the network editor settings, let's add a third network that is going to be use later as a subnet. Follow the same steps as the previews network we added before.**

![Screenshot From 2025-01-21 12-35-59](https://github.com/user-attachments/assets/562c84ac-d527-441c-ac0e-44f12700dc9e)

**Add a new Network Adapter for the Router VM.**

![Screenshot From 2025-01-21 12-36-40](https://github.com/user-attachments/assets/f4a284fd-2553-49b0-9145-56a85640ba3a)

**Choose the Custon option, and select the first virtual network we created. In this case (vmnet2).**

![Screenshot From 2025-01-21 12-38-50](https://github.com/user-attachments/assets/546fb843-f93c-425c-b268-b5a8a83c8544)

**Power on the Router VM to start with the installation and configuration for Fedora Server.**

![Screenshot From 2025-01-21 12-39-07](https://github.com/user-attachments/assets/5816b995-7d9f-4216-b094-7fd52d6c4fb0)

**Choose your desired Language.**

![Screenshot From 2025-01-21 12-41-44](https://github.com/user-attachments/assets/b8810549-79e1-44db-a8ee-822af8897b9c)

**Set up your Root Account and User Account.**

![Screenshot From 2025-01-21 12-41-48](https://github.com/user-attachments/assets/23edecd5-e52f-4379-8d64-b3455c711486)

**Installation progress.**

![Screenshot From 2025-01-21 12-46-40](https://github.com/user-attachments/assets/3785918b-3ea3-47d3-ae08-fc0aa3491edc)

**Important to note that Fedora Server doesn't come with a GUI pre-installed, but you could choose to install one if you want to do so.**

![Screenshot From 2025-01-21 12-48-10](https://github.com/user-attachments/assets/5c1198a7-0273-4b8f-bd5f-6d0b66b99ba5)

**We are going to proceed updating the system and packages first in the Router VM.**

4. Install necessary packages:

   ![Screenshot From 2025-01-21 12-53-02](https://github.com/user-attachments/assets/1adb4e93-eb82-4fd5-b9ec-03128ca6ff16)

   **Install dhcp server and iptables services.**

   ```bash
   sudo dnf install dhcp-server iptables-services -y
   ```
5. Enable IP forwarding (`/etc/sysctl.conf`):

   ![Screenshot From 2025-01-21 12-54-19](https://github.com/user-attachments/assets/58984bc8-9cb1-4ef6-992b-7c571ab5207d)

   **Enable IP forwarding and load the configuration file.**

   ```bash
   echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p
   ```
6. Configure DHCP (`/etc/dhcp/dhcpd.conf`):

   ![Screenshot From 2025-01-21 13-03-21](https://github.com/user-attachments/assets/8c1b9fc7-c9c9-409a-862f-b052ba99435e)

   **Configure the dhcpd.conf file with your subnet details.**

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

   ![Screenshot From 2025-01-21 13-06-03](https://github.com/user-attachments/assets/943a50b5-da6b-4e6e-90d0-ac2ff310810e)

   **Edit the dhcpd configuration file and specify the interface that is going to handle DHCP**

   ```plaintext
   DHCPDARGS=ens192
   ```
8. Use the following command to assign the static 172.16.206.1 to the vmnet2 interface:
   ```bash
   nmcli connection modify ens192 ipv4.method manual ipv4.addresses 172.16.206.1/24 ipv4.gateway 172.16.206.1 ipv4.dns "8.8.8.8,8.8.4.4" connection.autoconnect yes
   ```   
9. Start and enable DHCP:

   ![Screenshot From 2025-01-21 14-23-14](https://github.com/user-attachments/assets/2ab61ad1-1470-4780-b365-50228b466cbb)

   ```bash
   sudo systemctl enable dhcpd
   sudo systemctl start dhcpd
   sudo systemctl status dhcpd
   ```

10. Configure NAT with iptables:

    ![Screenshot From 2025-01-21 13-08-58](https://github.com/user-attachments/assets/d27904cd-bc70-4186-b242-4f25e9bce252)

    **configure NAT iptables and make sure you anble the iptables services and start it so it can automatically restart if the routerVM is restarted**

    ```bash
    sudo iptables -t nat -A POSTROUTING -o ens160 -j MASQUERADE
    sudo iptables-save | sudo tee /etc/sysconfig/iptables
    sudo systemctl enable iptables
    sudo systemctl start iptables
    ```

---

### **3. Creating and Configuring Client VMs**

![Screenshot From 2025-01-21 12-26-01](https://github.com/user-attachments/assets/ce12c518-1922-499e-8d8c-dad1569ed8d9)

**Follow the same installation wizard to create the Client VMs.**

![Screenshot From 2025-01-21 14-24-20](https://github.com/user-attachments/assets/ddd03fb8-09f2-4d77-a3e3-782c773adf79)

**For each Client VM, make sure to change the network from NAT to the custom network (vmnet2).**

![Screenshot From 2025-01-21 14-26-46](https://github.com/user-attachments/assets/86343513-2fda-427c-9202-63fee996ea57)

**Power on the Client VMs and start the installation process.**

1. Create Fedora Workstation VMs:

   - **ClientVM1** (DHCP):
     
     ![Screenshot From 2025-01-21 15-28-23](https://github.com/user-attachments/assets/1e16e50a-8ad1-454d-b87f-e2a0045eade3)

     **Set up the Client VM 1 to recieve DHCP. Make sure you replace the (ens160) with your interface name.**
     ```bash
     sudo nmcli connection modify ens160 ipv4.method auto connection.autoconnect yes
     sudo systemctl restart NetworkManager
     ```
   - **ClientVM2** (Static IP):
  
     ![Screenshot From 2025-01-21 15-44-50](https://github.com/user-attachments/assets/88bf824b-0506-49d1-a006-7315e9c8dc49)
  
     **Set up static IP for the second Client VM.**
     ```bash
     sudo nmcli connection add type ethernet ifname ens160 con-name static-ip ipv4.method manual ipv4.addresses 172.16.206.10/24 ipv4.gateway 172.16.206.1 ipv4.dns "8.8.8.8,8.8.4.4" connection.autoconnect yes
     sudo nmcli connection up static-ip
     ```

---

### **4. Testing the Setup**
1. Verify IP configuration:

   ![Screenshot From 2025-01-21 15-31-37](https://github.com/user-attachments/assets/976f4e3b-0f0e-4657-8bba-7d720cbd7d08)
   
   **Verify the IP configuration for both Client VMs.**
   ```bash
   ip addr show
   ```
3. Test connectivity:
   - Ping router from clients:
     
     ![Screenshot From 2025-01-21 15-30-36](https://github.com/user-attachments/assets/39453b5e-2445-46df-bea6-f8711f260bb4)
     ![Screenshot From 2025-01-21 15-46-32](https://github.com/user-attachments/assets/69820769-37d9-43e6-a6bb-35fb4c4f270c)

     **Test internet connectivity from both Client VMs, ping the router, also ping the Client VMs to test connectivity between each other.**
     ```bash
     ping -c 4 172.16.206.1
     ```
   - Test internet access:
     ```bash
     ping -c 4 google.com
     ```
---

## **Adding a Third Subnet**
1. Create `VMnet3` with a subnet (e.g., `192.168.5.0/24`).
   
   ![Screenshot From 2025-01-21 16-18-10](https://github.com/user-attachments/assets/0620a20e-9276-4924-a73a-dab35fcac959)

3. Attach it to **RouterVM** and **ClientVM2**.
4. Configure static IPs on RouterVM and ClientVM2 for the new interface.
5. Test routing and connectivity.

---

## **Use Wireshark to monitor traffic**
1. Install Wireshark. Replace dnf with your distro package manager or Install it directly from their official website.

   ![Screenshot From 2025-01-21 17-15-03](https://github.com/user-attachments/assets/45554bad-7121-4d80-8e84-4dbd623d637f)
   ![Screenshot From 2025-01-21 17-15-27](https://github.com/user-attachments/assets/af564ca6-3cab-4548-9218-239aa4b20fab)

   **Update packages and install wireshark from terminal.**
   ```bash
   sudo dnf update -y
   sudo dnf install wireshark -y
   ```
2. Launch Wireshark from the terminal or from the GUI:

   ![Screenshot From 2025-01-21 20-05-28](https://github.com/user-attachments/assets/14e538b7-6dd4-41d6-8184-528536b3719d)

   ```bash
   wireshark &
   ```
3. Select the Network Interface.
4. Choose the interface you want to monitor (e.g.,ens160 for VMnet2 or ens193 for VMnet3).
5. Click Start to begin capturing packets.
6. Perform Network Activity
7. From another VM or the Router VM, generate traffic:
8. Ping the Client VM:
   ```bash
   ping -c 4 172.16.206.101
   ```
   Access a website from the Client VM:
   ```bash
   curl http://example.com
   ```
9. Use Wireshark’s filters to isolate specific types of traffic:
   - **ICMP (Ping):**

     ![Screenshot From 2025-01-21 20-15-25](https://github.com/user-attachments/assets/97a3eaff-a961-4814-8967-88cbb0705ff9)

   - **DNS Queries:**

     ![Screenshot From 2025-01-21 20-15-53](https://github.com/user-attachments/assets/f514dbc5-5b1f-4e89-8bae-d0020673e591)

   - **HTTP Traffic:**

     ![Screenshot From 2025-01-21 20-16-12](https://github.com/user-attachments/assets/40815811-f695-45a6-aedd-313e045b3cde)

     **Here you are going to be able to observe packet details, including source and destination IPs, protocols used and packet size.**
     
---

### **License**
This project is licensed under the MIT License.
