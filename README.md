# Network-Design
This is ABC Company Ltd Network design and Implementation

**Case Study**

ABC Ltd is a fast-growing company in West Africa, Nigeria with more than 4 million customers globally. The organization deals with the buying and selling of agricultural products which are operated from headquarters. The company is planning to open a branch near the local village of Enungba. Therefore, the company requires New recruited IT engineer to design a network for the branch.  The proposed network is intended to operate separately from the headquarters network.

Being a small network, the company has the following requirements during implementation

1. One router and One switch to be used
2. Three (3) departments are comprised of Admin/IT, Finance/HR and Customer Service/Reception
3. Each department is required to be in a different VLAN
4. Each department is required to have a wireless network for the users
5. Host devices in the network are required to get IPV4 address auto
6. Devices are expected to communicate with each other.

Assuming the ISP gave out a base network address of 192.168.2.0, as the Network Engineer who has been hired, designs and implements the network considering the above requirements.

**Solution**

In addressing the case study given, we first design the topology of the network.

**Network Topology**
Network topology is the arrangement of elements (links, nodes, etc.) of a computer or telecommunications network. It describes how different nodes or devices are connected and how data flows through the network.

The image has answers to questions 1, 2, and 4

![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/4027d208-ad80-4846-a171-f9e4c4c6fb23)

**Subnetting**

Subnetting is the process of dividing a larger network into smaller, more manageable subnetworks, or subnets. It's a technique used to improve network performance, security, and organization. Subnetting involves allocating portions of a network's IP address space to different subnetworks, allowing for more efficient use of IP addresses and better organization of network resources.

As explained, to get the number of networks needed for this project, we look at the case study where departments are required to be created.

No.of subnet: 3( Admin/IT, Finance/HR, and Customer service/Reception)

To find the number of subnets, this formula will be used to determine the outcome.
No.of subnets = 2^n where n represents the number of bits from the host portion.

2^n=3

Remember, you can not use a smaller number or equal to the required subnet needed but can use a higher number to accommodate the subnet required. We cannot use 2^0 =1 and 2^1=2 which are both less than 3, thus we can use 2^2=4 but we will tag it as 3. Without much explanation, lets get the calculation done.

Basic Network: 192.168.2.0/24
Subnet Mask: 255.255.255.0 --- Class C
Binary of SM: 11111111. 11111111.11111111.00000000
Octet:              1st             2nd          3rd           4th 
Network/host: Net         .  Net        .   Net       . Host

To satisfy the network requirements, 2 bits from the host portion will be borrowed.

Therefore, the new binary bits will be:
New Binary: 11111111.11111111.11111111.11000000
CIDR: /26
Decimal Format: 255.255.255.192

I understand you will be confused about how we arrived at the 192.  We will show you how is calculated.
This bit from 11000000 from the new subnet mask is converted to decimal format
Since we are converting from binary to decimal, our figure will be in base 2.

Solving: 1*2^7+1*2^6+0*2^5+0*2^4+0*2^3+0*2^2+0*2^1+0*2^0 = 128+64+0+0+0+0+0+0 = 192.

New Subnet Mask: 255.255.255.192
Block size: 64

After subnetting, we need to assigned the network to each of the required network respectively.

**First subnet (Assigned to VLAN 10)**
Network ID: 192.168.2.0/26
Host range: 192.168.2.1 - 192.168.2.62
Broadcast ID: 192.168.2.63

**Second subnet (Assigned to VLAN 20)**
Network ID: 192.168.2.64/26
Host range: 192.168.2.65 - 192.168.2.126
Broadcast ID: 192.168.2.127

**Third subnet (Assigned to VLAN 30)**
Network ID: 192.168.2.128/26
Host range: 192.168.2.129- 192.168.2.190
Broadcast ID: 192.168.2.191

Note: It is important to remember that, the first subnet will take the base network while the rest network ID will be determined with increments of block size and in this case is 64. as illustrated above.  The broadcast address takes the last IP of the network while the first usage IP is the first IP after the network address which in most cases is being assigned to the default gateway.

**VLAN Configuration**

The third stage Vlan configuration which will be done on the switch.

**1st VLAN**
Switch>enable ----  To enable the switch 
Switch#configure terminal -----Enter global configuration
Switch(config)# int range fa0/2-4 --- interface range on fastethernet
Switch(config-if)# switchport mode access --- switch the port to access mode
Switch(config-if)# switchport access vlan 10 ---- creating of vlan and assign value

**2nd VLAN**
Switch>enable ----  To enable the switch 
Switch#configure terminal -----Enter global configuration
Switch(config)# int range fa0/5-7 --- interface range on FastEthernet
Switch(config-if)# switchport mode access --- switch the port to access mode
Switch(config-if)# switchport access vlan 20 ---- creating of vlan and assign value

**3rd VLAN**
Switch>enable ----  To enable the switch 
Switch#configure terminal -----Enter global configuration
Switch(config)# int range fa0/8-10 --- interface range on FastEthernet
Switch(config-if)# switchport mode access --- switch the port to access mode
Switch(config-if)# switchport access vlan 30 ---- creating of vlan and assign value
Switch(config-if)# exit ---- exit the interface
Switch(config)# do write --- To save the configuration.

Note: VLANs help segment network traffic, but if there are too many VLANs, it can lead to increased broadcast and multicast traffic within each VLAN. This can consume network bandwidth and processing resources on network devices, potentially slowing down network performance. To address this issue, we implement a trunk on the gateway interface. 

In networking, a trunk refers to a communication channel or link that carries multiple VLANs between switches, routers, or other network devices. Trunks are essential for enabling communication between devices in different VLANs and are commonly used in larger networks where VLAN segmentation is implemented.

**Trunk Configuration**
switch>en or enable --- Both can work perfectly well
switch# configure terminal or config t ---  The two phrases perform the same functions.
switch(config) #int fa0/1 ---- initiate the interface FasthEthenet which is the gateway interface
switch(config-if)# switchport mode trunk --- trunk mode is activated and assigned to Fa0/1
switch(config-if)# ex or exit
switch(config)# do wr or do write

**Sub Interface Creation (Inter VLAN Routing)**

We implement inter-VLAN routing to allow communication between each of the devices. This configuration will be carried on the router.

Firstly, we need to enable the router and also the interface G0/0.

Router>enable or en
Router# configure terminal or config t
Router(config)# interface g0/0 or int g0/0 ---- To initiate the face and port number
Router(config-if)# no shut or shutdown --- To power on the interface.
Router(config-if)# do wr
Router(config-if)# exit

Remember: By default, router interface power is off for security reasons so you need to power during configuration.


**Inter-VLAN routing configuration**

This process involves assigning VLAN to the default gateway (10,20, and 30)
Router(config) int g0/0.10
Router(config-subif) # encapsulation dot1q 10
Router(config-subif) # ip add 192.168.2.1 255.255.255.192 --- the first ip address of first subnet will be assigned to interface
Router(config-subif) #exit

Router(config) int g0/0.20
Router(config-subif) # encapsulation dot1q 20
Router(config-subif) # ip add 192.168.2.65 255.255.255.192 --- the first ip address of second subnet will be assigned to this interface
Router(config-subif) #exit

Router(config) int g0/0.30
Router(config-subif) # encapsulation dot1q 30
Router(config-subif) # ip add 192.168.2.129 255.255.255.192 --- the first IP address of the third subnet will be assigned to this interface
Router(config-subif) #exit
Router(config) # do wr

**DHCP Server Configuration**
As requested in the case study, the device will obtain IP automatically, and DHCP service must be enabled and configured on the router.

Router>enable or en
Router#config t or configure terminal
Router(config)# service dhcp ---- Enable DHCP service
Router(dhcp-config)# ip dhcp pool Admin-IT --- This command create a pool for Admin/IT department where an IP address can be assigned to a device automatic once connected.
Router(dhcp-config)# network 192.168.2.0 255.255.255.192 ---- The network ID of VLAN 10
Router(dhcp-config)# default-router 192.168.2.1 ---- Default gateway address of VLAN 10
Router(dhcp-config)# dns-server 192.168.2.1 --- any IP can be assigned on a dns server.
Router(dhcp-config)# domain-name admin.com ---- assigning a domain name (Not compulsory)
Router(dhcp-config)# exit

Router(dhcp-config)# ip dhcp pool Finance --- This command create a pool for Finance department where an IP address can be assigned to a device automatic once connected.
Router(dhcp-config)# network 192.168.2.64 255.255.255.192 ---- The network ID of VLAN 20
Router(dhcp-config)# default-router 192.168.2.65 ---- Default gateway address of VLAN 10
Router(dhcp-config)# dns-server 192.168.2.65 --- any IP can be assigned on a dns server.
Router(dhcp-config)# domain-name finance.com ---- assigning a domain name (Not compulsory)
Router(dhcp-config)# exit

Router(dhcp-config)# ip dhcp pool customer --- This command create a pool for Admin/IT department where an IP address can be assigned to a device automatic once connected.
Router(dhcp-config)# network 192.168.2.128 255.255.255.192 ---- The network ID of VLAN 30
Router(dhcp-config)# default-router 192.168.2.129 ---- Default gateway address of VLAN 30
Router(dhcp-config)# dns-server 192.168.2.129 --- any IP can be assigned on a dns server.
Router(dhcp-config)# domain-name csr.com ---- assigning a domain name (Not compulsory)
Router(dhcp-config)# exit
Router(config)# do wr

**Setting up access point Device**

Step 1: Click on the device
Step 2: Once the open, click on the config box
Step 3: Choose the appropriate port 1
Step 4: Enter the SSID and choose the authentication mechanism
   SSID: Admin-IT Wifi ---- Any name of your choice can be used
   Channel: Choose from drop-down
   Coverage range: depends on preference on length coverage
   Authentication options: WEP, WPA-PSK, WPA2-PSK. For this project, WPA2-PSK.
   PSK pass phrase: admin@123
Step 5: Close

**How Device Obtained IP Automatic**

To help understand the process, We document the steps to get IP assigned to the device through image illustration.

Step 1: Click on the PC

Step 2: Click Desktop once open as shown in the image


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/d0fd0e78-9187-4ae5-8178-e9b32a8775c5)

Step 3: Click on IP Configuration


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/e9d6f49d-7028-416d-8438-162c2b2400a7)

Step 4: Click on DHCP to request for IP


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/8bb89e12-b956-47de-8941-e4a77f506ef4)

Step 5: IP Obtained Successfully


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/8bb54532-b851-4b3b-b67c-647fd8614b40)

**How Laptop Obtained IP**

Step 1 : Click on the Labtop

Step 2:  Change the interface to a wireless (WPC300N) interface as shown in the image below


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/b782404f-8970-4a51-9ceb-435feea8da49)

Step 3: Click on PC Wireless


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/0dce2ca9-1b20-47fe-a112-8ddd8607ba4f)

Step 4: Search for available wireless adapter


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/4587dd35-5962-4ff3-a6c4-9a1e0d7e9f60)

Step 5:  Select from the wireless adapter


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/30f166fa-2f9f-4363-96c8-a80b774ac867)

Step 6: Enter the passcode and connect


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/84f3e245-13f3-4482-8899-fe193feb425f)


**How Smathphone obtained IP**

Step 1: Click on the device
Step 2: Complete set is depict on the image below


![Image](https://github.com/users/Saniabuh/projects/2/assets/101403891/31ad95ef-874e-43be-9a8d-4595984ccc12)


**Software use** 
Packet tracer : Can be downloaded here: https://www.netacad.com/courses/packet-tracer
