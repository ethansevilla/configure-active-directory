# configure-active-directory
<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" height="40%" width="70%"alt="Microsoft Active Directory Logo"/>
</p>

<h1>Configuring Active Directory (On-Premises) Within Azure</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell ise (admin)

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create Resources
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain (myadproject.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create a manu additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>
<br />
<br />
<h3 align="center">Setup Resources in Azure</h3>
<br />
<p>
  Create the Resoruce Group and Region
</p>  

<p> 
       <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/2d8aacd5-073b-462d-9c0e-eb531488cfda"/>
</p> 
<p> 
  Create the Domain Controller VM (Windows Server 2022) named “DC-1”:
</p> 
<p>

  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/c495e496-db07-4098-8d4a-df9d77429f45"/>
</p>
<p>
  Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in previous step:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/e701fdd0-bfcd-421d-9ab9-9b7082bdcc3d"/>
</p>
<p>
  Set Domain Controller’s NIC Private IP address to be static:
</p>
<p>
  <img src="https://i.imgur.com/KHU9kC4.png" height="75%" width="100%" alt="static ip"/>
</p>
<p>
  Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher):
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/2082d5bf-c4f3-4101-907b-1c82982989e5"/>
</p>
<br />
<br />
<h3 align="center">Ensure Connectivity between the Client and Domain Controller</h3>
<br />
<p>
  Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping):
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/db01ee99-3a13-4625-8d86-4bae58953eca"/>
</p>
<p>
  Login to the Domain Controller and enable ICMPv4 on the local Windows firewall:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/addd5d59-2734-4286-891e-e30b3e62992d"/>
</p>
<p>
  Check back at Client-1 to see the ping succeed which should reply: 
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/b1c7ac0f-f621-44ea-957d-07031b3a585"/>   #check this
</p>
<br />
<br />
<h3 align="center">Install Active Directory</h3>
<br />
<p>
  Login to DC-1 and install Active Directory Domain Services through Add Roles And Features Wizard 
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/9aa19734-bd69-4ea7-bc97-142cea3b1790"/>
</p>
<p>
  Promote as a Domain Controller: Press the flag with the warning sign above 
</p>
<p>
  <img src="https://i.imgur.com/zi15fw4.png" height="75%" width="100%" alt="domain controller promotion"/>   
</p>
<p>
  Setup a new forest as myactivedirectory.com (or it can be anything, just remember what it is):
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/897d6f5d-dd7a-48da-a5ee-70cf0334f941"/>
</p>
<p>
  Restart and then log back into DC-1 as user: myactivedirectory.com\labuser007:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/a31f15df-d1b6-4a42-b39d-c47b3e2c9547"/>
</p>
<br />
<br />
<h3 align="center">Create an Admin and Normal User Account in AD</h3>
<br />
<p>
  In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES” and another one called "_ADMINS":
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/a95d7585-557f-4de1-b2a3-cb3f904f7b5d"/>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/3a4a6e43-5545-4d41-9a9a-554d6e1e7650"/> 
  <img scr= "https://github.com/ethansevilla/configure-active-directory/assets/170621372/52c9fda0-2bd5-454f-89be-f58d98bdf804"/>
</p>
<p>
  Create a new employee named “Jane Doe” with the username of “jane_admin”:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/ddb3a50f-b442-43af-ae10-447e008840ac"/>
</p>
<p>
  Add jane_admin to the “Domain Admins” Security Group:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/9440901f-2349-44aa-8df2-3d192f00eac8"/>
</p>
<p>  
  Log out/close the Remote Desktop connection to DC-1 and log back in as “myactivedirectory.com\jane_admin”.I use jane_admin as my admin account from now on:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/73771ed4-cfe1-41d1-8f4e-e2a5ce3d27cb"/>
</p>
<br />
<br />
<h3 align="center">Join Client-1 to your domain (myactivedirectory.com)</h3>
<br />
<p>
  From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/ba805a56-6c74-485d-adc0-07b292291ad5"/>
</p>
<p>
  From the Azure Portal, restart Client-1.
</p>
<p>
  Login to Client-1 (Remote Desktop) as the original local admin (labuser007) and join it to the domain (the computer will restart):
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/4797c4d4-6367-446b-b7d3-b440ca2fd37e"/>
</p>
<p>
  Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain.
</p> 
<p>
    <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/5531e729-a700-4eb9-9082-e53069461f49" />
</p>
<p>
  Create a new OU named “_CLIENTS” and drag Client-1 into there:
</p>
<p>

  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/165d5491-28f2-4d9d-9e59-343a1ec47a14"/>

</p>
<br />
<br />
<h3 align="center">Setup Remote Desktop for non-administrative users on Client-1</h3>
<br />
<p>
  Log into Client-1 as myactivedirectory.com\jane_admin and open system properties.
</p>
<p>
  Click “Remote Desktop”.
</p>
<p>
  Allow “domain users” access to remote desktop.
</p>
<p>
  You can now log into Client-1 as a normal, non-administrative user now.
</p>
<p>
  Doing this action with Group Policy that allows you to change MANY systems at once:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/43497f40-c7e4-4a91-9618-31e63660a784"/>
</p>
<br />
<br />
<h3 align="center">Create additional users and attempt to log into client-1 with one of the users username and password</h3>
<br />
<p>
  Login to DC-1 as jane_admin for example
</p>
<p>
  Open PowerShell_ise as an administrator.
</p> 
<p>  
  Create a new File and paste the contents of this script (https://github.com/agruezo/configure-active-directory/blob/script/createUsers.ps1) into it:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/0c071413-b563-4b03-b3e6-91113f336c5b"/>
</p>
<p>
  Run the script and observe the accounts being created:
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/029b9a1f-d9e2-4718-ae14-8099fd8cab88"/>
</p>
<p>
  When finished, open Active Directory Users and Computers and observe the accounts in the appropriate OU, and attempt to log into Client-1 with one of the accounts (take note of the password in the script):
</p>
<p>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/4170ceae-48a0-4f5b-81c9-abe391830d5c"/>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/cada02fb-fcc9-41aa-b605-637a1310c83b"/>
  <img src="https://github.com/ethansevilla/configure-active-directory/assets/170621372/b07fc3b6-6833-48fc-9ab5-abf9472b0a7d"/>
</p>
<br />
<br />
<p>
  I hope this tutorial helped you learn a little about network security protocols, Active Directory, and observing traffic between virtual machines. 
</p>
<p>
   DON'T FORGET TO CLEAN UP YOUR AZURE ENVIRONMENT so that you don't incur unnecessary charges.
</p>
<p>
  Close your Remote Desktop connection, delete the Resource Group(s) created at the beginning of this tutorial, and verify Resource Group deletion.
</p>
