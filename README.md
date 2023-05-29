<h1 align="center">Configuring Active Directory on VirtualBox</h1>
<p align="center"><img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/></p>

This tutorial outlines the implementation of Microsoft Active Directory within Oracle VM VirtualBox.

<h3>Environments and Technologies Used</h3>

- Active Directory
- Oracle VM VirtualBox
- Remote Desktop
- PowerShell

<h3>Operating Systems Used</h3>

- Windows Server 2019
- Windows 10 Pro


<h2>Deployment and Configuration Steps</h2>


<p>
In this lab, we will create two virtual machines on VirtualBox. One will be the Domain Controller (DC), while the other will be a Client machine. The Client will connect to the internet through the DC's internal network (172.16.0.1) and out the external network (10.0.2.15). 
</p>

<p>
  <img src="https://i.imgur.com/gY8ouR6.png" height="80%" width="80%" alt="AD lab diagram"/>
</p>

<p>First, let's create the Windows Server 2019 Domain Controller. To start the set up, we will configure two adapters to simulate a corporate network environment.</p>

<p>
  <img src="https://i.imgur.com/Y3khK6x.png" height="50%" width="50%" alt="Two network adapter configuration"/>
  <img src="https://i.imgur.com/AeIUgCO.png" height="40%" width="40%" alt="Server 2019 installation"/>
</p>

<p>After installing Microsoft 2019, let's see what the ipconfig command outputs.</p>
<img src="https://i.imgur.com/ASLOlOl.png" height="80%" width="80%" alt="External static adapter settings"/>
<p>As you can see, there are two ethernet adapters. The bottom result being the <i>internal</i> network because of it assigned APIPA IP address: 169.254.185.206. In turn, this means the top result is the <i>external</i> IP address assigned by the home router DHCP: 10.0.2.15. Because we are configuring the domain controller, it is best to solidify the IP addresses to <i>static</i>. We can do so in the network and internet settings. We will first manually enter the <i>external</i> IPv4 details, copying and pasting the given IPv4 address, subnet mask, and default gateway as seen above. In the DNS server section, we will input a loopback address because the domain controller will serve as the DNS server.</p>

<p>Next, we will manually configure the <i>internal</i> network properties. We will go ahead and change the assigned APIPA address to a private class B IP address: 172.16.0.1. Additionally, we will reduce the size of the subnet mask from 255.255.0.0 to 255.255.255.0. This will provide the <i>internal</i> home lab network with 255 addresses. Similarily to the setup of the <i>external</i> adapter, we will supply a loopback address for the DNS server.</p>
<img src="https://i.imgur.com/3IWMFpP.png" height="80%" width="80%" alt="Internal static adapter settings"/>

<p>Now, let's add the domain service role to this DC in server manager. We will also create a new forest with the domain name being <i>homelab.com</i></p>

<p><img src="https://i.imgur.com/6AG3lFq.png" height="48%" width="48%" alt="Domain Services role"/>
<img src="https://i.imgur.com/O3hCnYh.png" height="48%" width="48%" alt="new forest creation"/></p>

<p>Next, we will add the Remote Access role to enable Network Address Translation (NAT) internet connection from the two adapters.</p>
<p><img src="https://i.imgur.com/q0YTNkY.png" height="48%" width="48%" alt="Remote access"/>
<img src="https://i.imgur.com/NjFl209.png" height="48%" width="48%" alt="NAT setup"/></p>

<p>The last role we will add to this DC is the Dynamic Host Configuration Protocol (DHCP) Server. We will create a new scope using the internal network, ranging from 172.16.0.100 to 172.16.0.200. Furthermore, we will set the internal network's default gateway/router to the DC's IP address: 172.16.0.1</p>
<p><img src="https://i.imgur.com/WZ8hSFG.png" height="38%" width="38%" alt="DHCP"/>
<img src="https://i.imgur.com/TS3bRSN.png" height="30%" width="30%" alt="scope"/>
<img src="https://i.imgur.com/0NsMJu9.png" height="30%" width="30%" alt="default gateway/router"/></p>

<p>Now that we have the DC set up. Let's configure a client machine and connect to the homelab.com domain. Below, you can see screenshots of the Client1 machine being set up in an internal adapter.</p>
<p><img src="https://i.imgur.com/ILT9fVe.png" height="48%" width="48%" alt="Client1 internal adapter"/>
<img src="https://i.imgur.com/EOqecv7.png" height="48%" width="48%" alt="Win10 pro setup"/></p>

<img src="https://i.imgur.com/9RQmvZ7.png" height="70%" width="70%" alt="Client1 ipconfig"/>
<p>Now, after searching Client1's ipconfig information, we can observe the machine recognized homelab.com as the DNS. Also, we see the DC's DCHP role has automatically leased an IP address. Furthermore, the default gateway/router has been set to the domain controller's internal IP address. We can confirm this by nagivating to the DC's server manager's "DHCP" and "Users and Computers" tools.</p>
<p><img src="https://i.imgur.com/63lbpxp.png" height="60%" width="60%" alt="DHCP DC"/>
<img src="https://i.imgur.com/Su8kVYc.png" height="38%" width="38%" alt="Users and computers DC"/></p>

<p>Now, in order to simulate a corporate environment, let's create a handful of users. Rather than creating the domain users manually, we will utilize the automation of powershell. To do so, I drafted a list of users in a text (.txt) file. Next, I created a script in powershell ISE to create the domain users for me.</p>
<p><img src="https://i.imgur.com/lgxHvJs.png" height="29%" width="29%" alt="text file of users"/>
<img src="https://i.imgur.com/Z1McXIi.png" height="40%" width="40%" alt="powershell ise"/>
<img src="https://i.imgur.com/mU86sY2.png" height="29%" width="29%" alt="AD users"/></p>

<p>Now that we have created a few users in the domain. Let's rename the machine to Client1 and join the domain using one of the automated user creations.</p>
<p><img src="https://i.imgur.com/9fKta3W.png" height="49%" width="49%" alt="joining domain"/>
<img src="https://i.imgur.com/1PeD4va.png" height="48%" width="48%" alt="ctioukalov credentials"/></p>
                                                                                                                                              
<p>As you can see with the screenshot below, we can run the whoami command in the command prompt and observe the joined domain: homelab.com.</p>
<img src="https://i.imgur.com/Sv2SBxI.png" height="70%" width="70%" alt="whoami"/>   

<p>Now on to the last section of this lab, Windows Remote Desktop. After enabling remote desktop in the Microsoft settings, let's see if we can remotely connect to Client1's machine from the Domain Controller using Administrator credentials.</p>
<p><img src="https://i.imgur.com/YcYz6L3.png" height="49%" width="49%" alt="connecting to remote desktop"/>
<img src="https://i.imgur.com/6FSdCnD.png" height="48%" width="48%" alt="remote desktop successful"/></p><br><br>

<p>Thank you for your time! I refered to Josh Madakor's Active Directory Home Lab YouTube <a href="https://www.youtube.com/watch?v=MHsI8hJmggI&t=1050s">video</a> for this lab.</p>
                                                                                              
                                                                                               
                                                                                               


