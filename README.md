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

































































































































