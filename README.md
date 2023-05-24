<h1 align="center">Configuring Active Directory on VirtualBox</h1>
<p align="center"><img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/></p>

This tutorial outlines the implementation of Microsoft Active Directory within Oracle VM VirtualBox.

<h3>Environments and Technologies Used</h3>

- Active Directory Domain Services
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

<p></p>




