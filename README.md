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

Next, we are going to create a domain admin user. Click Start, then Windows Administrative Tools, then Active Directory Users and Computers.

![image](https://github.com/user-attachments/assets/c627d193-6836-463a-b082-b6f8503bbcaa)

Next, we are going to create an Organizational Unit for our users and admins to be in. Click the mydomain.com dropdown. Then right-click mydomain.com, click new, then click Organizational Unit

![image](https://github.com/user-attachments/assets/bf3679af-9287-4e40-9d2b-ed9d8c874881)

Were going to create two organizational units, the first being "_EMPLOYEES" and the second being "_ADMINS". Ensure it is the same as what is in the quotation marks. Then click OK.

![image](https://github.com/user-attachments/assets/c26bc6be-3f8b-4ab1-bad5-9260e40127e8)

![image](https://github.com/user-attachments/assets/c046998b-aafc-4f15-940f-571ebac7c6f1)

Next, we can create our Admin User. Click on _ADMINS, then right-click _ADMINS, then click New, then click User.

![image](https://github.com/user-attachments/assets/827c9d05-3e66-4400-9407-f3c9bc82c596)

Then input the Information. Then click Next.

![image](https://github.com/user-attachments/assets/f209467b-3484-41e8-aaf7-8b64d3a9fb37)

For password, you can use "Password1" or another password just ensure to remember it. Then, uncheck "User must change password at next login", and check "Password never expires". You never want to do this in real life, this is just to make the lab more simple. Then click Next.

![image](https://github.com/user-attachments/assets/afcc0d2d-d325-424c-b40c-48a133a9cd1b)

Then click Finish.

![image](https://github.com/user-attachments/assets/33259168-43b2-48a8-b079-3459ef2e74b5)

Now we can see our User is created.

![image](https://github.com/user-attachments/assets/107fa050-4aa5-4b16-b696-ee780d1cb6fc)

Next, we're gonna add Jane Doe to the "Domain Admins" Security Group.

Right-click on Jane Doe, then click Properties.

![image](https://github.com/user-attachments/assets/5359da3b-88bc-4656-b7b8-a91f26d9d46c)

Then click Member Of -> Add.

![image](https://github.com/user-attachments/assets/8b76ada8-fff9-4b22-9fe6-c1dc7fbd04e7)

Then in the text box input Domain Admins, and click Check Name. Then click OK.

![image](https://github.com/user-attachments/assets/526e05ce-605b-410d-9050-1e705ef786e1)

Now you can see Jane Doe is a member of Domain Admins.

![image](https://github.com/user-attachments/assets/12bf0c9f-9d5c-4fcf-9348-ca68eec0221b)

Click Apply, then OK.

Now we can log out of dc-1 and log back in as Jane Doe. Were going to use Jane Doe account as the admin account from now on. Using Jane Doe login to dc-1.

![image](https://github.com/user-attachments/assets/4fc7fe2d-6ac3-48df-8e31-84d333237e89)

Next were going to go back into Client-1 to join it to the domain.

In client 1, rightclick the start button, then click System.

![image](https://github.com/user-attachments/assets/cfbd9a84-928f-4039-8f2a-354e9368d9b8)

Then scroll down and click Rename this PC (Advanced).

![image](https://github.com/user-attachments/assets/09f90113-e5a1-4b59-87ff-5abc0e070a18)

Then click Chnage.

![image](https://github.com/user-attachments/assets/d3e38290-c4cd-4d97-9f45-fa323c58e0e7)

Then click Domain under Member Of, then input "mydomain.com". Then click OK.

![image](https://github.com/user-attachments/assets/d4144e06-2bdb-430b-9164-f11d215a7613)

It was able to find mydomain.com because we put client-1's Primary DNS server as our Domain Controller.

Now we can input Jane Doe's admin account username and password here to make the changes.

![image](https://github.com/user-attachments/assets/ec46325d-0a05-4ebc-a09f-f90796608db0)

Then click OK. It will say Welcome to the mydomain.com domain. Click OK (x2).

![image](https://github.com/user-attachments/assets/a92afdf7-9745-40a2-993b-377cffa041ed)

Then in the Systems Properties click Close, then click Restart Now. This will restart client-1.

Next in dc-1, we can check to ensure client-1now shows up in Active Directory Users and Computers.

In dc-1 go to Active Directory Users and Computers. Then click the mydomain.com dropdown, then click Computers. You can see client-1 now shows up. If you double click client-1 you can see all the information about that computer.

![image](https://github.com/user-attachments/assets/c0d0bd64-2376-4185-9773-f4f6c920446f)

![image](https://github.com/user-attachments/assets/9bfd9253-2585-405e-b07a-deb9a0e654f4)

Now create a new Organizational Unit called "_CLIENTS".

Next xlixk hold and drag client-1 into _CLIENTS. Then click Yes.

![image](https://github.com/user-attachments/assets/e4028b41-618c-49ce-90af-e2d61bed3671)

Now when you click on _clients, you can see client-1 has now moved.

![image](https://github.com/user-attachments/assets/ab6888e3-4e33-4c58-953d-8de39ccfe98c)

Now we need to set up Remote Desktop on client-1 for non0admin users.

In client-1 login as Jane Doe admin account. 

Right click Start and click system to go back into System Properties.

Scroll down and click Remote Desktop.

![image](https://github.com/user-attachments/assets/93f00385-5934-4012-b379-235f21fcfcfd)

Then click Select users thta can remotely access this PC.

![image](https://github.com/user-attachments/assets/5e7c2af9-577b-4ef8-a1db-aff9167dffdd)

Then click Add.

![image](https://github.com/user-attachments/assets/f281197a-9b8d-4b44-99e7-aac979fdfde7)

Then in the text box input "domain users", click check name. Click OK.

![image](https://github.com/user-attachments/assets/ac490d33-33f5-46f7-b8dc-52ed02ce03fb)

You can see that Domain Users was added, click OK.

![image](https://github.com/user-attachments/assets/36bc01eb-8263-4ba6-9be0-dc92b630aae3)

Now you can login to client-1 as a normal non-admin user.

Next we can create addittional non-admin users.

In dc-1, ensure youre logged in as Jane Doe admin account, and open Powershell as a administrator.

In the search bar type in PowerShell, then right-click Powershell ISE and click run as administrator. Then click Yes.

![image](https://github.com/user-attachments/assets/9f8b9330-ce31-46ec-9439-187d6f27f037)

![image](https://github.com/user-attachments/assets/2d9de501-c126-4c9c-b724-11363d837d50)

Click New Script.

![image](https://github.com/user-attachments/assets/1cf781d6-f306-49fa-bc2c-da9dd1d6dfc5)

Then copy the contents that are in this [Script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

Click the copy raw file button.

![image](https://github.com/user-attachments/assets/45e609f7-5416-4ddd-ac5f-da62c0115203)

Then paste the script into the New Script in Powershell ISE on dc-1.

![image](https://github.com/user-attachments/assets/4a5174e2-133a-40ea-8497-a54c44bef0f0)

While clicked onto the New Script hit ctrl+s to save it to the desktop.

![image](https://github.com/user-attachments/assets/85bf8109-37df-4b38-b274-7ecc2e041265)

Next xlixk run script uing the green play icon.

![image](https://github.com/user-attachments/assets/03651145-4346-421b-bc8e-234bc420dc6d)

Now you can see all the users being created.

If you go into Active Directory Users and Computers, and click on _EMPLOYEES, you can see all the users being created.

When looking at the scrtipt we can see thta the password for all these new users is "Password1".

Now we can try to log in as a random user, pick a random user's username.

![image](https://github.com/user-attachments/assets/d6cef8be-ad4c-4f32-9e78-6ab08c553829)

Then go to client-1 and sign out of jane_admin, and sign in using the new username and password.

You can see that you were able to login now as the new user.

Finally we can look at Group Policies and Managing Accounts. First we will configure the Group Policy.

In dc-1, rightclick start and click run. Input "gpmc.msc" and hit Enter.

![image](https://github.com/user-attachments/assets/f6581254-4782-4e47-acd8-c7a9d8b063a5)

![image](https://github.com/user-attachments/assets/34ac0a76-da7d-4627-8eca-486703bfc08d)

This will load the Group Policy Management Console.

Next click the dropdown arrow for Forest: mydomain.com -> Domains -> mydomain.com

![image](https://github.com/user-attachments/assets/365b609b-428a-409d-ae77-5f026786a367)

Then rightclick Default Domian Policy and click Edit.

Next click the dropdown arrow for Computer Configuration -> Policies -> Window Settings -> Security Settings -> Account Policies, then click on Account Lockout Policy.

![image](https://github.com/user-attachments/assets/23aa4452-895a-4620-83ae-76a72ff16e0d)

Now you can see the settings for Account Lockout Settings.

- Account Lockout Duration is the time in minutes a account remains locked before automatically being unlocked.
- Account Lockout Threshold is the number of failed logon attempts that will trigger an account lockout.
- Reset Account Lockout Counter After is the time after which the failed logon atempts counter is reset to 0, if there are no additional failed logon attempts.

Double click Account lockout duration and check the box, then input "30" for 30 minutes. Click Apply -> OK (x2).

![image](https://github.com/user-attachments/assets/72f30b6e-54d2-412a-a84f-084eee93241e)

Now double click Account lockout threshold and ensure it is checked and says 5. Click OK.

![image](https://github.com/user-attachments/assets/90e477cf-8b41-4a5e-adde-b5536a2d60cc)

Next Double click Allow Administrators account lockout, ensure the box is checked and Enabled is picked. Click OK.

![image](https://github.com/user-attachments/assets/903c2d65-6660-4f79-81b6-018d8beeead2)

Lastly, double click Reset account lockout counter after, and ensure box is checked and it syas 10, then click OK.

![image](https://github.com/user-attachments/assets/7cdc5f6d-b2e9-43d6-800e-b70357758ccb)

Now that we configured the GRoup Policy settings we need to login to client-1 as jane_admin to force update client-1.














