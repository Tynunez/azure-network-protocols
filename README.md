<p align="center">
<img src="https://i.imgur.com/Ua6li3W.png" width=500px alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>

<p>
In this tutorial, we analyze network traffic to and from Azure Virtual Machines using Wireshark and explore Network Security Groups.
</p> 
<br/>

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
Welcome to my tutorial on Network Security Groups and Network Protocol Inspection.

To begin, you will need to create two virtual machines on Azure: one running Ubuntu Linux and the other running Windows 10/11. Both machines must be on the same Virtual Network (VNET). Once the VMs are set up, go to the Windows machine and download Wireshark from the official site: [Wireshark Download](https://www.wireshark.org/download.html). After installing Wireshark, open the application and set a filter for ICMP traffic only.

ICMP, or Internet Control Message Protocol, is a network layer protocol used for sending error messages and operational information. The ping command uses ICMP to test connectivity between hosts. By filtering Wireshark to capture only ICMP packets and pinging the private IP address of the Linux machine, you will be able to visually observe the packets in Wireshark in real-time.
</p>
<img src="https://i.imgur.com/GVeffnX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
We can analyze each packet to view the exact data transmitted with each ping, as shown in the image below.
</p>
<img src="https://i.imgur.com/WH5jwrH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
Now, let’s observe how a Network Security Group (NSG) affects ICMP traffic by configuring a firewall rule.
Start by initiating a continuous ping from your Windows 10 VM to your Ubuntu VM. Open PowerShell or Command Prompt on the Windows VM and enter the following command, replacing <private IP> with the actual private IP address of the Ubuntu VM: ping <private IP> -t

This command will send ICMP Echo Requests indefinitely until manually stopped.
Next, open the Azure Portal and navigate to the Network Security Group (NSG) assigned to your Ubuntu VM. Locate the Inbound Security Rules section and find the rule allowing ICMP traffic. Disable or delete this rule to block incoming ICMP packets.
Back on the Windows 10 VM, observe the behavior in Wireshark and the ping command output. You should notice that the ICMP requests are still being sent from the Windows VM, but no replies are being received. Wireshark will capture the outgoing ICMP Echo Requests, but the responses from the Ubuntu VM will be missing. The command line will display "Request timed out" messages, indicating that the packets are being blocked.
Now, re-enable ICMP traffic by adding a rule in the Network Security Group to allow inbound ICMP packets again. Once this rule is applied, go back to the Windows 10 VM and check Wireshark and the ping output. You should now see ICMP replies being received again, confirming that connectivity has been restored.
Finally, stop the ping activity by pressing Ctrl + C in the command line. This experiment demonstrates how Network Security Groups control inbound traffic and how firewall rules can affect network communication in real time.
</p>

<img src="https://i.imgur.com/u7iEUzJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<img src="https://i.imgur.com/2Lny9yV.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
Now that you have observed ICMP traffic, let's move on to inspecting SSH traffic. Begin by logging back into the Windows VM and open Wireshark to start a packet capture. This time, you’ll need to filter for SSH traffic only. In Wireshark, you can do this by applying the filter tcp.port == 22 (or ‘ssh’), which will capture only the packets related to the SSH protocol.
Next, from your Windows 10 VM, you will SSH into your Ubuntu Linux machine using its private IP address. Open PowerShell and type the following command, replacing <private IP address> with the actual private IP address of your Linux machine: ssh labuser@<private IP address>

After entering the command, you’ll be prompted to provide the password for the labuser account on your Linux VM. Type the password and press Enter. As you interact with the SSH session by typing commands (such as username, pwd, etc.), you will start seeing a significant amount of SSH traffic appearing in Wireshark. This traffic includes encrypted packets exchanged between the Windows and Linux machines as part of the SSH protocol.
To end the SSH session, simply type exit and press Enter. You’ll notice that once the connection is terminated, the SSH traffic in Wireshark will stop, and the captured packets will no longer be updated.
This step allows you to analyze how SSH operates on a network, including the connection establishment and encrypted data transmission. It's an essential part of understanding secure remote communications between systems.
</p>
<img src="https://i.imgur.com/WQGKCRa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
Now, let's analyze DHCP traffic. Open Wireshark on your Windows VM and filter for DHCP traffic by using the filter dhcp. This will capture all packets related to the Dynamic Host Configuration Protocol, which is responsible for assigning IP addresses to devices on a network.
Next, open PowerShell as an administrator and attempt to issue your Windows VM a new IP address by running the following command: ifconfig  /renew

This command requests a new IP lease from the DHCP server. As soon as you execute it, you will see DHCP traffic appearing in Wireshark. The DHCP process consists of four key steps: Discover, Offer, Request, and Acknowledge(DORA). First, your VM sends a DHCP Discover packet, searching for an available DHCP server. The server responds with a DHCP Offer, suggesting an available IP address. Your VM then sends a DHCP Request, formally requesting the offered IP, and finally, the DHCP server confirms the assignment with a DHCP Acknowledge packet.
By monitoring the packet exchange in Wireshark, you can observe this entire negotiation in real time. This is a fundamental process in networking, ensuring devices on a network receive valid IP addresses dynamically without manual configuration.
</p>
<img src="https://i.imgur.com/DzuzIiI.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
Now, let’s observe DNS traffic. Open Wireshark on your Windows VM and apply a filter for DNS traffic using dns. This will capture packets related to Domain Name System (DNS) queries and responses, which are used to resolve domain names into IP addresses.
Next, open the Command Prompt or PowerShell on your Windows VM and use the nslookup command to find the IP addresses of different websites. Start by typing the following commands:
<p>nslookup google.com</p>
<p>nslookup disney.com</p>
After running these commands, you will see DNS request and response packets appearing in Wireshark. Your Windows VM first sends a DNS Query asking for the IP address of the domain. In response, a DNS Reply is received, providing the corresponding IP address.
DNS traffic is an essential part of network communication, as it allows human-readable domain names to be converted into numerical IP addresses that computers use to route traffic. By capturing this exchange in Wireshark, you gain insight into how your machine communicates with DNS servers to resolve web addresses.
</p>
<img src="https://i.imgur.com/TzEALYX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
Now, let’s observe RDP traffic. Open Wireshark on your Windows VM and apply a filter for RDP traffic using tcp.port == 3389. This will capture packets related to the Remote Desktop Protocol (RDP), which allows users to remotely control another computer over the network.
As soon as you apply the filter, you will notice a continuous stream of RDP packets appearing in Wireshark, even if you are not actively interacting with the remote machine. Unlike protocols such as SSH, which primarily send data when commands are entered, RDP maintains a constant flow of traffic.
This happens because RDP functions as a live video stream of the remote machine’s display. The protocol continuously transmits screen updates, cursor movements, and other visual changes, ensuring a smooth and responsive experience. Since the remote session must reflect every detail in real-time, the traffic never stops, even when idle.
By analyzing this in Wireshark, you can see how RDP differs from other remote access protocols and understand why it generates consistent network traffic.
</p>
<img src="https://i.imgur.com/kNckWTn.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<p>
Now that you have completed the lab, it is important to clean up the resources to avoid unnecessary charges and maintain a tidy Azure environment.
Start by closing your Remote Desktop connection to the Windows VM. Once disconnected, navigate to the Azure Portal and locate the Resource Group(s) you created at the beginning of this lab. Deleting the Resource Group will remove all associated resources, including the virtual machines, virtual network, and any storage or network components created during the session.

To delete a Resource Group, go to the Azure Portal, find the Resource Groups section, select the group you want to delete, and click Delete Resource Group. Azure will prompt you to confirm the deletion by typing the Resource Group’s name. Once confirmed, the deletion process will begin.
After a few minutes, verify that the Resource Group has been completely removed by refreshing the Resource Groups page in the Azure Portal. Ensuring proper cleanup prevents unnecessary resource consumption and helps keep your Azure environment organized.
</p>
