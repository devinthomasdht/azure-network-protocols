<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />






<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create virtual machines
- Install Remote Desktop
- Install Wireshark
- Setup Network-Security Group
- Filter ICMP, SSH, DHCP, DNS, RDP traffic in Wireshark
- Use Power Shell to assist with observation of Network Traffic.

<h2>Actions and Observations</h2>

<p>
<img width="1913" alt="Linux-vm" src="https://github.com/user-attachments/assets/26d405f7-7323-40d8-a2da-6d721b46bff4" />
<img width="1906" alt="windows-vm" src="https://github.com/user-attachments/assets/a3e59ef6-19c5-4dd6-9036-463a16db9c43" />


</p>
<p>
Created virtual machines for Windows and Linux using Window 10 PRO and Ubuntu 22.04 under the same Resource Group. This is essential to capturing network traffic using Wireshark and Command-Line Tools. Made sure both VM's are on the same virtual network.
</p>
<br />

<p>
<img width="897" alt="Remote Desktop" src="https://github.com/user-attachments/assets/8359a0e6-f223-4d79-82c4-748f9411256f" />
<img width="896" alt="Remote Desktop p2" src="https://github.com/user-attachments/assets/3667d0b0-2fc9-4c10-ae56-a4842828cbf2" />


</p>
<p>
If your computer does not have Remote Desktop, then you can go to the Apple Store or the Microsoft Store and install "Microsoft Remote Desktop" to log into your virtual machines. Once installed to add the VM you need the public IP address of the VM from Azure. Select the add option and under PC name place the IP address, after that you can give your VM a Display name. Once finished you can log in with username and password.
</p>
<br />

<p>
<img width="1910" alt="Install wireshark_packetsniffing tool or protocol_analyzer" src="https://github.com/user-attachments/assets/4901a117-a996-4954-81d3-33a77040a665" />

</p>
<p>
Once logged into Windows-vm, you can install Wireshark from the internet onto the VM. Follow the prompts for installing options selected are enough to capture network traffic in Wireshark. Wireshark is mainly used for packet capture, protocol analysis, filtering, visualization, and exploring data. This tool can seem very helpful when analyzing traffic on a network. Wireshark helps in network troubleshooting, identifying performance bottlenecks, security analysis (like detecting unusual activity or potential attacks), and even protocol development.
</p>
<br />

<p>
<img width="896" alt="Start capture in wireshark" src="https://github.com/user-attachments/assets/70cd7509-4ccd-4712-bf7b-dbda9215a98d" />
<img width="1916" alt="By filtering for icmp in wire shark and pinging linux-vm private ip we can get icmp traffic" src="https://github.com/user-attachments/assets/f2f60a9e-c1f2-4158-9f31-59e9b0879aa8" />

</p>
<p>
To start once Wireshark is open click the ethernet option and then click the blue fin in the top left corner of the screen to start a capture in Wireshark. You should see traffic starting to flow in the packet sniffer and this is traffic happening on the network. Next, we will filter traffic for icmp and we will do this by typing icmp into the filter field and hit enter. To filter traffic for icmp we then open Power Shell and use ping command-line tool to ping the Linux-vm. We type "ping 10.0.0.5" to see if Linux-vm is reachable via icmp. When done we could see that we were able to reach Linux-vm using ping and we could see the traffic for icmp in Wireshark. On Wireshark on the bottom left you can see "Internet Control Message Protocol" and if need be, we can expand to see additional information. All options in that pane show additional information, looking at it from the first option down, it matches the 7 Layers of the OSI model.
</p>
<br />

<p>
<img width="1919" alt="Network Security Group _NSG created custom inbound security rule to block or deny icmpv4 traffic" src="https://github.com/user-attachments/assets/cb299555-22e8-4b4c-9e9d-8f7bed171d89" />
<img width="1629" alt="linux-vm NSG setup to block icmp traffic wireshark and powershell show traffic being blocked per firewall_NSG" src="https://github.com/user-attachments/assets/dd2bc221-3a3b-430d-9ccd-2f3f8bbdcf54" />

</p>
<p>
To understand how networks communicate and how Wireshark captures packets, we will use Network Security Group (NSG) or known as a firewall in Azure (Cloud) to deny or block icmp traffic. To do this we would go back to Azure then select Linux-vm, after that select network settings and then settings. Next click inbound security rules and select add. In the options we want to select an (*) in the destination port ranges, protocol select icmpv4, priority 290 and then select add to create the custom rule. Once that is complete you can go back to Windows-vm and in Power Shell type "ping 10.0.0.5 -t". This will create a perpetual/non-stop ping from Windows-vm to Linux-vm. Observing you can see that Linux-vm is no longer reachable due to the inbound security rule put in place. Looking at Wireshark you can also see the "no response found!" in the top pane. If you look at the source 10.0.0.4 is the Windows-vm trying to reach the destination 10.0.0.5 the Linux-vm. The protocol also states icmp which again is blocked via NSG.
</p>
<br />

<p>
<img width="1913" alt="Capture ssh traffic in powershell ssh_private ip of linux-vm and signin " src="https://github.com/user-attachments/assets/3aa8dcc3-4507-47d2-8b7d-5c018becf348" />
<img width="1660" alt="ssh captured on port 22 destination source random port number" src="https://github.com/user-attachments/assets/73666a16-b4ba-4b78-9aa5-e9c90c4716ce" />
<img width="1679" alt="closure of ssh" src="https://github.com/user-attachments/assets/578b2e38-dd4a-4400-a5a7-a65a61736f0f" />

</p>
<p>
Next, we can filter traffic for Secure Shell (SSH). SSH is used when remotely connecting from one computer to another. The computer being connected to is typically "listening" for a connection on TCP port 22. We start the capture by clicking the blue fin and then we filter for SSH traffic. With the Power Shell open we type "ssh labuser@10.0.0.5" to start a connection. Type yes when prompted and then type username and password for Linux-vm to connect. Once connected you should start seeing traffic on Wireshark. SSH traffic is encrypted and with every keystroke new traffic will show. Once closed in Power Shell, you can see Wireshark reflects (RST, ACK) which means the session has ended.
</p>
<br />

<p>
<img width="1684" alt="filter dhcp traffic p2" src="https://github.com/user-attachments/assets/88a2af85-f9f8-41a8-8092-fe89ab871016" />

</p>
<p>
In this example we filter for DHCP traffic. DHCP is a protocol used to assign an IP address to devices when they are first connected to the network. To filter traffic, we would start the capture in Wireshark and enter DHCP in the filter field. With Power Shell open we type "ipconfig /renew" to attempt getting a new IP address. When successful, information regarding the new IP address to generate. Looking back in Wireshark we can see that traffic has been captured. Observing this information we can see that DHCP runs UDP (User Datagram Protocol) on ports 68 and 67.
</p>
<br />

<p>
<img width="1706" alt="filter dns traffic nslookup" src="https://github.com/user-attachments/assets/5392a4c8-194c-4833-bc04-bbbe863dcabb" />

</p>
<p>
Let's look at DNS traffic. DNS stands for "Domain Name System" or (Domain Naming Server). And its goal is to convert human readable domain names (such as www.google.com) to IP addresses, which computers can use and understand. In this example we start a capture in Wireshark and enter DNS in the filter field. To produce results, we open Power Shell and type "nslookup google.com" or "nslookup disney.com" in the command-line. Nslookup (Name Server Lookup) is a command-line tool used for querying DNS servers to obtain information about domain names, IP addresses, and other DNS records. It helps users troubleshoot DNS issues and gather DNS-related information. In this case we see an IP address for Google and Disney. We can observe that DNS runs on TCP and UDP port 53.
</p>
<br />

<p>
<img width="1654" alt="filtering for RDP tcp_port3389 constant traffic because this vm is live while im using it so much traffic will show in wireshark" src="https://github.com/user-attachments/assets/8219ef79-e7cb-4fd5-bdbe-7a8064c1d3db" />

</p>
<p>
Using the virtual machines created we will now look at filtering Remote Desktop Protocol (RDP). RDP is used when remotely connecting from one computer to another to gain a remote desktop "GUI" (Graphical User Interface). The computer being connected to is typically "listening" for a connection on TCP (Transmission Control Protocol) port 3389. We can observe after filtering for "tcp.port==3389" that the traffic is continuous and will not stop. The reason is because RDP is constantly showing a live stream from one computer to another, therefore traffic is always being transmitted. While in use of the VM which is through Remote Desktop, even filtering for RDP will automatically generate network traffic.
</p>
