<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we analyze network traffic to and from Azure Virtual Machines using Wireshark and explore Network Security Groups. <br />




<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDP, DHCP, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 22.04

<h2>Actions and Observations</h2>

<p>
</p>
<p>
Welcome to my tutorial on Network Security Groups and Network Protocol Inspection.

To begin, you will need to create two virtual machines on Azure: one running Linux and the other running Windows 10/11. Both machines must be on the same Virtual Network (VNET). Once the VMs are set up, go to the Windows machine and download Wireshark from the official site: [Wireshark Download](https://www.wireshark.org/download.html). After installing Wireshark, open the application and set a filter for ICMP traffic only.

ICMP, or Internet Control Message Protocol, is a network layer protocol used for sending error messages and operational information. The ping command uses ICMP to test connectivity between hosts. By filtering Wireshark to capture only ICMP packets and pinging the private IP address of the Linux machine, you will be able to visually observe the packets in Wireshark in real-time.
</p>
<br />

