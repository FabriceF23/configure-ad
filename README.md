<p align="center"> <img src="https://i.imgur.com/pU5A58S.png" height="40%" width="70%"alt="Microsoft Active Directory Logo"/> </p> <h1>Configuring Active Directory (On-Premises) Within Azure</h1> In this tutorial, we’ll walk through how to set up on-premises Active Directory inside Azure Virtual Machines.<br /> <h2>Environments and Technologies Used</h2>
Microsoft Azure (Virtual Machines/Compute)
Remote Desktop
Active Directory Domain Services
PowerShell
<h2>Operating Systems Used </h2>
Windows Server 2022
Windows 10 (21H2)
<h2>High-Level Deployment and Configuration Steps</h2>
Create Resources
Ensure Connectivity between the client and Domain Controller
Install Active Directory
Create an Admin and Normal User Account in AD
Join Client-1 to your domain (myadproject.com)
Setup Remote Desktop for non-administrative users on Client-1
Create additional users and attempt to log into client-1 with one of the users
<h2>Deployment and Configuration Steps</h2> <br /> <br /> <h3 align="center">Setup Resources in Azure</h3> <br /> <p> First, we create the Domain Controller VM (Windows Server 2022) named “DC-1”: </p> <p> <img src="https://i.imgur.com/gaAzjvb.png" height="75%" width="100%" alt="resource group"/> <img src="https://i.imgur.com/hubTfey.png" height="75%" width="100%" alt="vm ms server"/> </p> <p> Then, we create the Client VM (Windows 10) named “Client-1” and use the same Resource Group and VNet we created earlier: </p> <p> <img src="https://i.imgur.com/XyEmv8f.png" height="75%" width="100%" alt="vm windows"/> </p> <p> Now, we make sure the Domain Controller’s NIC Private IP address is set to static: </p> <p> <img src="https://i.imgur.com/KHU9kC4.png" height="75%" width="100%" alt="static ip"/> </p> <p> Make sure both VMs are in the same VNet (you can double-check the topology using Network Watcher): </p> <p> <img src="https://i.imgur.com/rFpHLdQ.png" height="75%" width="100%" alt="topology"/> </p> <br /> <br /> <h3 align="center">Ensure Connectivity between the client and Domain Controller</h3> <br /> <p> Log into Client-1 using Remote Desktop and ping the private IP address of DC-1 with the command: ping -t <ip address> (this will keep pinging continuously): </p> <p> <img src="https://i.imgur.com/bnPM9tX.png" height="75%" width="100%" alt="perpetual ping"/> </p> <p> On the Domain Controller, enable ICMPv4 on the Windows firewall to allow ping responses: </p> <p> <img src="https://i.imgur.com/ZpPyEkt.png" height="75%" width="100%" alt="enable ICMPv4"/> </p> <p> Check back at Client-1, and you should see the ping successful: </p> <p> <img src="https://i.imgur.com/8o3OfjY.png" height="75%" width="100%" alt="ping success"/> </p> <br /> <br /> <h3 align="center">Install Active Directory</h3> <br /> <p> Log into DC-1 and install Active Directory Domain Services: </p> <p> <img src="https://i.imgur.com/A1V9XJ5.png" height="75%" width="100%" alt="active directory install"/> </p> <p> After that, promote DC-1 as a Domain Controller: </p> <p> <img src="https://i.imgur.com/zi15fw4.png" height="75%" width="100%" alt="domain controller promotion"/> </p> <p> Set up a new forest with the name "myactivedirectory.com" (this can be anything, but remember the name! I used "myadproject.com" for this tutorial): </p> <p> <img src="https://i.imgur.com/DCFUVrM.png" height="75%" width="100%" alt="set new forest"/> </p> <p> After restarting, log back into DC-1 using the user: myadproject.com\labuser: </p> <p> <img src="https://i.imgur.com/7UakWMQ.png" height="75%" width="100%" alt="fqdn login"/> </p> <br /> <br /> <h3 align="center">Create an Admin and Normal User Account in AD</h3> <br /> <p> In Active Directory Users and Computers (ADUC), create two Organizational Units (OUs): one called “_EMPLOYEES” and the other called “_ADMINS”: </p> <p> <img src="https://i.imgur.com/cYmv0r7.png" height="75%" width="100%" alt="organizational unit"/> <img src="https://i.imgur.com/v02CBPI.png" height="75%" width="100%" alt="organizational unit"/> </p> <p> Create a new user called “Jane Doe” with the username “jane_admin”: </p> <p> <img src="https://i.imgur.com/h546E6L.png" height="75%" width="100%" alt="admin creation"/> </p> <p> Add jane_admin to the “Domain Admins” Security Group: </p> <p> <img src="https://i.imgur.com/mnLwTgq.png" height="75%" width="100%" alt="security group"/> </p> <p> Log out of DC-1 and log back in as “myadproject.com\jane_admin”. From now on, use this admin account: </p> <p> <img src="https://i.imgur.com/xWZ4Kol.png" height="75%" width="100%" alt="admin login"/> </p> <br /> <br /> <h3 align="center">Join Client-1 to your domain (myadproject.com)</h3> <br /> <p> Go to the Azure Portal and change Client-1’s DNS settings to the private IP address of DC-1: </p> <p> <img src="https://i.imgur.com/1KRsjI6.png" height="75%" width="100%" alt="client dns settings"/> </p> <p> Then restart Client-1 from the Azure Portal. </p> <p> Log into Client-1 (using Remote Desktop) as the original local admin (labuser) and join it to the domain (the computer will restart): </p> <p> <img src="https://i.imgur.com/50wszcP.png" height="75%" width="100%" alt="domain joining"/> </p> <p> Log into the Domain Controller and check if Client-1 appears in Active Directory Users and Computers (ADUC) under the “Computers” container: </p> <p> Create a new OU called “_CLIENTS” and move Client-1 into it: </p> <p> <img src="https://i.imgur.com/vB1n9m0.png" height="75%" width="100%" alt="active directory client verification"/> </p> <br /> <br /> <h3 align="center">Setup Remote Desktop for non-administrative users on Client-1</h3> <br /> <p> Log into Client-1 as mydomain.com\jane_admin and go to system properties. </p> <p> Click on “Remote Desktop” and enable access for “domain users”: </p> <p> Now, you can log into Client-1 as a normal, non-administrative user. </p> <p> Usually, you’d want to use Group Policy to manage multiple systems at once (maybe we’ll cover that in another lab): </p> <p> <img src="https://i.imgur.com/8BfpT3s.png" height="75%" width="100%" alt="remote desktop setup"/> </p> <br /> <br /> <h3 align="center">Create a bunch of additional users and attempt to log into client-1 with one of the users</h3> <br /> <p> Log into DC-1 as jane_admin and open PowerShell_ise as an administrator. </p> <p> Create a new file, and paste the script from [this link](https://github.com/Xinloiazn/configure-ad/blob/main/adscript.ps1) into it: </p> <p> <img src="https://i.imgur.com/0i8uApf.png" height="75%" width="100%" alt="create users script"/> </p> <p> Run the script and watch the new accounts get created: </p> <p> <img src="https://i.imgur.com/6QOGzs6.png" height="75%" width="100%" alt="observe create users script"/> </p> <p> After it's done, go to ADUC and check the new accounts. Then, try logging into Client-1 with one of those accounts (make sure you use the correct password from the script): </p> <p> <img src="https://i.imgur.com/ZZCfiCp.png" height="75%" width="100%" alt="employee user accounts"/> <img src="https://i.imgur.com/7gBpNzN.png" height="75%" width="100%" alt="employee user selection"/> <img src="https://i.imgur.com/cqsddjn.png" height="75%" width="100%" alt="employee user login"/> </p> <br /> <br /> <p> I hope this tutorial helped you understand network security protocols and how traffic works between virtual machines. You can try this on a PC or Mac (you just need to download the Remote Desktop App on Mac). </p> <p> Now that we're done, don't forget to clean up your Azure environment to avoid extra charges. </p> <p> Close the Remote Desktop connection, delete the Resource Group(s) you created at the start, and confirm they’ve been deleted. </p>
