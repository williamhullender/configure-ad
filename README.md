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
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Domain Controller VM and Client Computer VM in Azure
- Install Active Directory and Configure the Domain
- Configure Remote Desktop for Users and Create Users
- Configure and Observe Group Policies

<h2>Deployment and Configuration Steps</h2>

</p>
<br />

First, we must create a resource group and a virtual network in https://portal.azure.com/. Go to the Resource Groups tab and create a new resource group named "Active-Directory". Now, go to the Virtual Networks tab, and create a new vnet named "active-directory-vnet". Ensure it is in the Resource Group we created. 

Ensure everything we create is in the same region as your resource group. 

</p>
<br />

![image](https://github.com/user-attachments/assets/e36380b1-59c4-4beb-9b87-7e270d02eaa5)

</p>
<br />

Now, we can create the Domain Controller by creating a new VM. Then ensure it is in the Active-Directory resource group, then we can name it "dc-1". Next, select Windows Server 2022, then select the size of at least 2 vcpus and 8 GB. Then click next until you reach Networking. 

</p>
<br />

![image](https://github.com/user-attachments/assets/ab95f741-3111-42b7-8e9b-5b196ce2a3e8)

</p>
<br />

Then ensure the VM is on the active-directory-vnet we created before. Then we can review and create the VM.

</p>
<br />

![image](https://github.com/user-attachments/assets/696a416e-391a-40c3-88fc-3870bebaddf2)

</p>
<br />

Now, we can create the computer client VM. Create another VM with the same settings except it is running Windows 10 Pro and is named "client-1".

Next, we will configure the Domain Controller's IP address and change it to static. This way the IP address does not change.

Go to Virtual Machines in Azure and click dc-1. Then, click Networking -> Network Settings -> Network interface / IP configuration.

</p>
<br />

![image](https://github.com/user-attachments/assets/f52266b2-1fb7-4f3b-82ad-e8d2772bdeec)

</p>
<br />

Then click ipconfig 1.

</p>
<br />

![image](https://github.com/user-attachments/assets/1a1688cb-7a6f-42e7-a9db-87293e4cc15d)

</p>
<br />

Then click static and make sure to remember the new static IP address. Then click save.

</p>
<br />

![image](https://github.com/user-attachments/assets/571bc2a5-aea5-4bf7-bcf2-14cd997d2363)

</p>
<br />

Next, we need to configure the DNS settings for client-1 and set dc-1 as the main DNS server.

Go to Virtual Machines in Azure and click client-1. Then, click Networking -> Network Settings -> Network interface / IP configuration.

Instead of selecting ipconfig 1, we are going to select DNS servers.

</p>
<br />

![image](https://github.com/user-attachments/assets/81e89e51-dbdc-4a06-b79f-fc2f1e9bd63d)

</p>
<br />

Then click Custom. Then for "Add DNS server", input the private IP address of dc-1. Then click save.

</p>
<br />

![image](https://github.com/user-attachments/assets/c272b3e9-498d-43ca-aa2a-1bcc03ad87d2)

</p>
<br />

Here you can see the applied DNS servers.

</p>
<br />

![image](https://github.com/user-attachments/assets/ad199c66-0c37-4023-915f-342e73acebf0)

</p>
<br />

Now restart the client-1 VM.

Now log in to both dc-1 and client-1 with Remote Desktop.

When logged in, go on dc-1 and we need to disable the Windows Firewall (This is only testing in this lab, not recommended for real-world deployment). Open "run" by pressing the start key or typing in the search bar, "r" then click run. Now input "wf.msc" and hit enter.

</p>
<br />

![image](https://github.com/user-attachments/assets/ffb1b0fc-d50c-472e-b407-149432b00730)

</p>
<br />

Then click on "Windows Defender Firewall Properties"

</p>
<br />

![image](https://github.com/user-attachments/assets/333f6a5f-203f-44e8-a3fc-34135cde71da)

</p>
<br />

Now for Domain Profile, Private Profile, and Public Profile, we are going to click on each one and hit the "o" key to turn them off. Then hit apply and Ok.

</p>
<br />

![image](https://github.com/user-attachments/assets/4a7e573d-4232-4bcd-94b7-75860bc2e6f5)

</p>
<br />

![image](https://github.com/user-attachments/assets/13e1ef2a-6273-41ac-9727-9dff95552edb)

</p>
<br />

Next, we are gonna go into client-1 and try to ping and ensure that dc-1 is set as the DNS server. 

Now, attempt to ping dc-1 with its private IP addresses in Powershell. Ensure the ping succeeds.

Next, type in ipconfig /all to ensure the DNS server is dc-1.

</p>
<br />

![image](https://github.com/user-attachments/assets/f785abbf-42c5-47e2-b61c-f0f43c510742)

</p>
<br />

Here we can see the ping succeeded, and the DNS server is the private IP address for dc-1.

Next, we are going to install Active Directory Domain Services.

Click start and then go to server manager.

![image](https://github.com/user-attachments/assets/8acaa679-a2b0-4fc4-b1bf-e5a325584896)

Then click, Add Roles and features

![image](https://github.com/user-attachments/assets/46eb03fe-1c9b-43a5-8d5d-f1fa4b3fcb8e)

Then click Next -> Next until you get to server selection. Verify there is only one entry and it should say dc-1.

![image](https://github.com/user-attachments/assets/5419b74c-1988-484d-ab38-112cad668b31)

Then click next to get to Server Roles and check the box beside Active Directory Domain Service.

![image](https://github.com/user-attachments/assets/e9599b6a-45aa-4cca-a844-416b01a2c986)

Next, click Add Features.

![image](https://github.com/user-attachments/assets/27e80987-ab45-4f8d-814a-407477b9a55c)

Then click next (x3). Then click the box next to "Restart the following roles, role services, or features on selected server, click Install." Then click yes on the Add Roles and Features Wizard. Afterwards, click Install.

![image](https://github.com/user-attachments/assets/15487689-95cc-451d-bd0e-771be569727e)

This will start Installing the Active Directory services. When it is done click close.

Next, we must configure dc-1 to become a Domain Controller and create a new forest.

In Server Manager, go to the top right corner and click the flag icon. Next, click Promote this server to a domain controller.

![image](https://github.com/user-attachments/assets/1415a577-09b3-42dc-9fb3-d27cd3a4c9c8)

Next, click Add a new forest. Then input the name "mydomain.com". Afterward, click next.

![image](https://github.com/user-attachments/assets/a12d09a1-29b6-4b69-97cf-3145a08a010a)

Then for the password, input "Password1", Ensure to write this down or remember it. Then click next.

![image](https://github.com/user-attachments/assets/e8cb801e-f34f-456f-ac16-bbbeaeedb17a)

Then ensure that DNS delegation is unchecked.

![image](https://github.com/user-attachments/assets/37c1bc68-db33-4869-af1f-230af59371e5)

Then click next (x4), until you get to Prerequisite check, then click Install.

![image](https://github.com/user-attachments/assets/71f73684-7514-43e1-9456-c76e3a603443)

After this, dc-1 should automatically restart.

Next were going to log back on dc-1, however, the normal login username will not work. You will need to use the new username "mydomain.com\labuser". Ensure you use a backslash or it will not work. The password stays the same. The reason for the username being different is because dc-1 is acting as a domain controller now, to log into it, we must specify which domain we want to log into, and then specify which user is logging in.

Next were going to create a domain admin user. Click start, then Windows Administrative Tools, then Active Directory Users and Computers.

![image](https://github.com/user-attachments/assets/c627d193-6836-463a-b082-b6f8503bbcaa)

Next were going top create an Organizational Unit for our users and admins to be in. Click the mydomain.com dropdown. Then right click mydomain.com, click new, then click Organizational Unit

![image](https://github.com/user-attachments/assets/bf3679af-9287-4e40-9d2b-ed9d8c874881)

Were going to create two organizational units, the first being "_EMPLOYEES" the second being "_ADMINS". Ensure it is exactly the same as what is in the quotation marks. Then click OK.

![image](https://github.com/user-attachments/assets/c26bc6be-3f8b-4ab1-bad5-9260e40127e8)

![image](https://github.com/user-attachments/assets/c046998b-aafc-4f15-940f-571ebac7c6f1)

Next we can create our Admin User. Click on _ADMINS, then right click _ADMINS, then click New, thenm click User.

![image](https://github.com/user-attachments/assets/827c9d05-3e66-4400-9407-f3c9bc82c596)

Then input the Information. Then click Next.

![image](https://github.com/user-attachments/assets/f209467b-3484-41e8-aaf7-8b64d3a9fb37)

For password you can use "Password1" or another password just ensure to remember it. Then, uncheck "User must chnage password at next login", and check "Password never expires". You never want to do this in real life, this is just to make the lab more simple. Then click Next.

![image](https://github.com/user-attachments/assets/afcc0d2d-d325-424c-b40c-48a133a9cd1b)

Then click Finish.

![image](https://github.com/user-attachments/assets/33259168-43b2-48a8-b079-3459ef2e74b5)

Now we can see our User is created.

![image](https://github.com/user-attachments/assets/107fa050-4aa5-4b16-b696-ee780d1cb6fc)

Next were gonna add Jane Doe to the "Domain Admins" Security Group.

Right click on Jane Doe, then click Properties.

![image](https://github.com/user-attachments/assets/5359da3b-88bc-4656-b7b8-a91f26d9d46c)

Then click Member Of -> Add.

![image](https://github.com/user-attachments/assets/8b76ada8-fff9-4b22-9fe6-c1dc7fbd04e7)

Then in text box input Domain Admins, then click Check Name. Then click OK.

![image](https://github.com/user-attachments/assets/526e05ce-605b-410d-9050-1e705ef786e1)

Now you can see Jane Doe being a member of Domian Admins.

![image](https://github.com/user-attachments/assets/12bf0c9f-9d5c-4fcf-9348-ca68eec0221b)

Click Apply, then OK.

Now we can logout of dc-1 and log back in as Jane Doe. Were going to use Jane Doe account as the admin account from now on.















































