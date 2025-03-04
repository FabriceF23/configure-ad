<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Setting Up Active Directory in Azure</title>
</head>
<body>
    <h1 align="center">Setting Up Active Directory (On-Premises) in Azure</h1>
    <p>This guide shows you how to set up Active Directory (AD) on Azure Virtual Machines (VMs). Active Directory helps manage users and computers in a network, and we’ll be setting it up in the cloud using Azure.</p>

    <h2>Tools and Technologies You'll Need</h2>
    <ul>
        <li><strong>Microsoft Azure:</strong> This is where we’ll create the virtual machines.</li>
        <li><strong>Remote Desktop:</strong> Used to connect to the VMs from your computer.</li>
        <li><strong>Active Directory Domain Services:</strong> This is the service that lets you manage users and computers.</li>
        <li><strong>PowerShell:</strong> A tool that lets you automate tasks on your computer or server.</li>
    </ul>

    <h2>Operating Systems Used</h2>
    <ul>
        <li><strong>Windows Server 2022:</strong> This will be the Domain Controller (the server managing everything).</li>
        <li><strong>Windows 10:</strong> This will be the client machine, where users log in.</li>
    </ul>

    <h2>High-Level Steps</h2>
    <ol>
        <li>Set up the resources (virtual machines).</li>
        <li>Make sure the client machine and Domain Controller can communicate.</li>
        <li>Install Active Directory on the server.</li>
        <li>Create admin and regular users in Active Directory.</li>
        <li>Join the client machine to the domain.</li>
        <li>Set up Remote Desktop for non-admin users.</li>
        <li>Create more users and test logging in.</li>
    </ol>

    <h2>Step 1: Set Up Resources in Azure</h2>
    <h3>Create the Domain Controller VM (Windows Server 2022):</h3>
    <p>This machine will manage everything, so name it <strong>DC-1</strong>.</p>
    <img src="https://i.imgur.com/gaAzjvb.png" height="75%" width="100%" alt="Create DC-1" />
    
    <h3>Create the Client VM (Windows 10):</h3>
    <p>This is the machine that users will log into, so name it <strong>Client-1</strong>.</p>
    <img src="https://i.imgur.com/XyEmv8f.png" height="75%" width="100%" alt="Create Client-1" />

    <h3>Set a Static IP for the Domain Controller:</h3>
    <p>Set the Domain Controller’s IP to be static so it doesn't change.</p>
    <img src="https://i.imgur.com/KHU9kC4.png" height="75%" width="100%" alt="Static IP" />

    <h3>Check the Network:</h3>
    <p>Ensure both VMs are in the same network. You can do this using Azure’s Network Watcher.</p>
    <img src="https://i.imgur.com/rFpHLdQ.png" height="75%" width="100%" alt="Network Topology" />

    <h2>Step 2: Connect Client-1 to the Domain Controller</h2>
    <h3>Ping the Domain Controller:</h3>
    <p>Log in to <strong>Client-1</strong> and open the command prompt. Type the command <code>ping -t &lt;IP of DC-1&gt;</code> to test if it can reach the Domain Controller.</p>
    <img src="https://i.imgur.com/bnPM9tX.png" height="75%" width="100%" alt="Ping Test" />

    <h3>Enable ICMP on DC-1:</h3>
    <p>Log in to <strong>DC-1</strong>, go to the firewall settings, and allow ICMPv4 (this lets the ping work).</p>
    <img src="https://i.imgur.com/ZpPyEkt.png" height="75%" width="100%" alt="Enable ICMP" />

    <h3>Check Ping Success:</h3>
    <p>After enabling ICMP on <strong>DC-1</strong>, check the ping again from <strong>Client-1</strong>. It should succeed now!</p>
    <img src="https://i.imgur.com/8o3OfjY.png" height="75%" width="100%" alt="Ping Success" />

    <h2>Step 3: Install Active Directory on DC-1</h2>
    <h3>Install AD Domain Services:</h3>
    <p>On <strong>DC-1</strong>, open the server manager, add the <strong>Active Directory Domain Services</strong> role, and follow the prompts to install it.</p>
    <img src="https://i.imgur.com/A1V9XJ5.png" height="75%" width="100%" alt="Install AD" />

    <h3>Promote to Domain Controller:</h3>
    <p>After installing AD, promote the server to be the Domain Controller, and create a new domain (for example, <strong>myadproject.com</strong>).</p>
    <img src="https://i.imgur.com/zi15fw4.png" height="75%" width="100%" alt="Domain Controller Promotion" />

    <h3>Restart and Log In:</h3>
    <p>Once <strong>DC-1</strong> restarts, log back in using your domain credentials.</p>
    <img src="https://i.imgur.com/7UakWMQ.png" height="75%" width="100%" alt="Login" />

    <h2>Step 4: Create Admin and Regular User Accounts in AD</h2>
    <h3>Create Organizational Units (OUs):</h3>
    <p>In Active Directory, create two Organizational Units (OUs): one for <strong>_EMPLOYEES</strong> and another for <strong>_ADMINS</strong>.</p>
    <img src="https://i.imgur.com/cYmv0r7.png" height="75%" width="100%" alt="Create OUs" />

    <h3>Create the Admin User:</h3>
    <p>Create an admin user like <strong>Jane Doe</strong> with the username <strong>jane_admin</strong>.</p>
    <img src="https://i.imgur.com/h546E6L.png" height="75%" width="100%" alt="Create Admin" />

    <h3>Add User to Domain Admins Group:</h3>
    <p>Add <strong>jane_admin</strong> to the <strong>Domain Admins</strong> group to give them admin access.</p>
    <img src="https://i.imgur.com/mnLwTgq.png" height="75%" width="100%" alt="Add to Admin Group" />

    <h3>Log In as Admin:</h3>
    <p>Log out of <strong>DC-1</strong>, then log in again as <strong>jane_admin</strong>.</p>
    <img src="https://i.imgur.com/xWZ4Kol.png" height="75%" width="100%" alt="Admin Login" />

    <h2>Step 5: Join Client-1 to the Domain</h2>
    <h3>Set DNS on Client-1:</h3>
    <p>On <strong>Client-1</strong>, change the DNS settings to use <strong>DC-1’s</strong> private IP.</p>
    <img src="https://i.imgur.com/1KRsjI6.png" height="75%" width="100%" alt="Client DNS" />

    <h3>Join the Domain:</h3>
    <p>Log in to <strong>Client-1</strong> as the local admin and join it to the domain. The machine will restart after this.</p>
    <img src="https://i.imgur.com/50wszcP.png" height="75%" width="100%" alt="Domain Join" />

    <h3>Verify in Active Directory:</h3>
    <p>Go to <strong>DC-1</strong>, open Active Directory Users and Computers, and check that <strong>Client-1</strong> shows up under the <strong>Computers</strong> section.</p>
    <img src="https://i.imgur.com/vB1n9m0.png" height="75%" width="100%" alt="Verify in AD" />

    <h2>Step 6: Set Up Remote Desktop for Non-Admin Users</h2>
    <h3>Allow Domain Users to Access Remote Desktop:</h3>
    <p>On <strong>Client-1</strong>, go to the system properties, and allow <strong>domain users</strong> to connect via Remote Desktop.</p>
    <img src="https://i.imgur.com/8BfpT3s.png" height="75%" width="100%" alt="Allow Remote Desktop" />

    <h2>Step 7: Create More Users and Test Logins</h2>
    <h3>Create Users Using PowerShell:</h3>
    <p>Create several users with a script. Here’s a link to the script you can use: <a href="https://github.com/Xinloiazn/configure-ad/blob/main/adscript.ps1">adscript.ps1</a>.</p>
    <img src="https://i.imgur.com/0i8uApf.png" height="75%" width="100%" alt="Create Users Script" />

    <h3>Test Logging In:</h3>
    <p>After running the script, check Active Directory for the new users and test logging in to <strong>Client-1</strong> with one of the new user accounts.</p>
    <img src="https://i.imgur.com/ZZCfiCp.png" height="75%" width="100%" alt="Test User Login" />

    <h2>Final Step: Clean Up Your Environment</h2>
    <p>Once you're done, make sure to delete the Azure resources you created to avoid unnecessary charges!</p>
</body>
</html>
