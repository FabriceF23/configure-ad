<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Domain Controller and Client-1 in Azure
- Install Active Directory
- Create a Domain Admin user within the domain
- Join Client-1 to your domain (mydomain.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create a bunch of additional users and attempt to log into client-1 with one of the users
- Dealing with Account Lockouts
- Enabling and Disabling Accounts
- Observing Logs


<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/Z6rVp93.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
The first step I did was creating a Resource Group in Azure and titled it "Active-Directory-Lab".
</p>
<br />

<p>
<img src="https://i.imgur.com/cRjq0oK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
The next step I did was creating a virtual network and subnet for the virtual machines to run on. I placed the virtual network in the the Resource Group I created.
</p>
<br />

<p>
<img src="https://i.imgur.com/R3cztcO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
I then created a virtual machine for the domain controller and titled it "dc-1". I set its operating system to Windows Server 2022 with the username "chlabuser". I also set it's size to 2 vcpus with a 16 GiB memory to be certain the server can handle all that I will be doing going forward.
</p>
<br />

<p>
<img src="https://i.imgur.com/0gLg1pf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/EVM8NNu.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/zohJrYl.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
I created another virtul machine for the client titled "client-1". I gave it the same username and password as "dc-1" for lab purposes. I attached it to the same virtual network and resource group as "dc-1" and used Windows 10 (22H2) for the image.
</p>
<br />

<p>
<img src="https://i.imgur.com/W34r5bK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
I set Domain Controller’s NIC Private IP address to be static so that when I set Client-1's DNS settings to DC-1's private IP address, there wouldn't be any change which would cause confusion later navigating client-1's server.
</p>
<br />

<p>
<img src="https://i.imgur.com/Z1xJJM1.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/UUsdkQC.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/SFb3rhZ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
The next step was to copy DC-1's public IP address and log into the server with remote desktop (Microsoft Remote Desktop).
</p>
<br />

<p>
<img src="https://i.imgur.com/AeTxEVf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/8a9RxPi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the DC-1 server I disabled the Windows Firewall so that I can test connectivity betwwen the two virtual machines.
</p>
<br />

<p>
<img src="https://i.imgur.com/QNE7zoD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/n6zA9LD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/9Yyheue.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/cejX7jx.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
The next step was to add new inbound and outbound security rules to the Network security group of both Virtual Machines so that I will be able to override the deafult security rules with a higher priority one. This will enable me to set Client-1's DNS settings to DC-1's Private IP. I then restarted DC-1 and Client-1 virtual machines in the Azure Portal.
</p>
<br />

<p>
<img src="https://i.imgur.com/EnEBImq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the DNS settings of CLient-1's VM on the Azure portal, I set it to DC-1's Private IP address. I then restarted the Client-1 virtual machine in the Azure Portal.
</p>
<br />

<p>
<img src="https://i.imgur.com/2IY0xfZ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/2nAeW6w.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/RHBIXbF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next I logged into the Client-1 virtual machine and attempted to ping DC-1's private IP address in PowerShell. After ensuring the ping succeeded I ran "ipconfig /all" to see if the output for the DNS settings would show DC-1's private IP.
</p>
<br />

<p>
<img src="https://i.imgur.com/TEvZy0T.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/YYyDlZp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/FvxORVA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
I then logged into DC-1 virtual machine and installed Active Directory Domain Services.
</p>
<br />


</p>
<p>
I stayed in the Active Directory and disabled the same account. I attempted to login and I saw the error message. I then re-enabled the account in the Active Directory and successfully logged in.
</p>
<br />

<p>
<img src="https://i.imgur.com/KPIGulK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
I reset the password of the same user within the Active Directory.
</p>
<br />

<p>
<img src="https://i.imgur.com/WXrNyse.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Finally I used the Event Viewer to observe the logs in the Domain Controller and client machine. 
</p>
<br />
