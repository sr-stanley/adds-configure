<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Installing and Configuring Active Directory on the Domain Controller</h1>

This tutorial outlines the installation and configuration of **Active Directory Domain Services (AD DS)** on the Domain Controller. After installation, we will create an administrative user account, join the Client VM to the domain, and verify the Client VM is connected to the Domain.

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>Prerequisite</h2>

Open and log in to Azure: [https://azure.microsoft.com/](https://azure.microsoft.com/)

## Table of Contents
- [Step 1 - Install Active Directory Domain Services](#step-1---install-active-directory-domain-services)
- [Step 2 - Configure Active Directory to become a Domain Controller](#step-2---configure-active-directory-to-become-a-domain-controller)
- [Step 3 - Create a Domain Admin User](#step-3---create-a-domain-admin-user)
- [Step 4 - Join Client VM to the Domain](#step-4---join-client-vm-to-the-domain)
- [Step 5 - Verify Client VM is connected to the Domain](#step-5---verify-client-vm-is-connected-to-the-domain)
- [Step 6 - Setup Remote Desktop Access for Users on Client VM](#step-6---setup-remote-desktop-access-for-users-on-client-vm)
- [Step 7 - Create Employee (Client) Users in Active Directory](#step-7---create-employee-client-users-in-active-directory)

---

### Step 1 - Install Active Directory Domain Services
Active Directory is a directory service developed by Microsoft for Windows domain networks. It is used to store information about network objects, such as users, computers, and resources, in a structured format. It allows administrators to manage permissions and access to network resources efficiently. Active Directory Domain Services (AD DS) is the core component of Active Directory, responsible for handling authentication and authorization processes.

Using the Public IP Address of your Domain Controller VM, remote desktop into your Domain Controller VM. Server Manager should boot once you log in. If not, you can find it within the start menu.

- Click `Add roles and features` → From the left hand menu select `Server Roles` and click the box for `Active Directory Domain Services` → Click `Add Features`.

[pic 2.1] 

- From the left hand menu select `Confirmation` and click the box for `Restart the destination server automatically if required` → Click `Yes` → Click `Install`.
- After installation click `Close`.

[pic 2.2]

---

### Step 2 - Configure Active Directory to become a Domain Controller
Technically, up to this point we’ve only created a Windows Server. By promoting this server to a Domain Controller, we enable it to fully utilize Active Directory Domain Services (AD DS). This allows centralized management of users, computers, and security policies within the domain.

Inside the Server Manager click the flag icon at the top of the screen and select `Promote this server to a domain controller`.
- Click `Add a new forest`.
- Root domain name: `mydomain.com` (could be anything) → Click `Next`.
- Create a Directory Services Restore Mode (DSRM) password.
- Click `Next` until you get to the option to Install → Click `Install`.

[pic 2.3]

After installation the VM will restart and you will need to reconnect via Remote Desktop Connection using **domain credentials** instead of local credentials..
- Use DC-1's VM Public IP Address.
- Username: `mydomain.com\labuser`.
- Password: Same as before.
**Note: The account created when the Azure VM was first deployed (labuser in my case) functions as a local administrator before installing AD DS.
Once the server is promoted to a Domain Controller, local accounts are no longer used. The built-in Administrator account becomes the Domain Administrator, responsible for managing users, computers, and security within the domain.**


[pic 2.4]

**Under Roles and Server Groups you should now see Active Directory Domain Services along with other Roles.**

[pic 2.5]

---

### Step 3 - Create a Domain Admin User
A Domain Administrator (or Domain Admin user) is a user account in Active Directory that has full administrative privileges over the domain.  This account differs from the built-in Domain Administrator account. This one represents an actual Administrative employee.

From the start menu click the folder for `Windows Administrative Tools` and select `Active Directory Users and Computers`. 

[pic 2.6]

Create two **Organizational Units** (OUs) one for Employees and one for Admins.
First click the down arrow for `mydomain.com` to expand the Containers and OUs.

1. Right click on `mydomain.com` → Select `New` → Click `Organizational Unit` → Name `_EMPLOYEES` → Click `OK`. 

2. Right click on `mydomain.com` → Select `New` → Click `Organizational Unit` → Name `_ADMINS` → Click `OK`.

***Normally you wouldn't use an underscore but for the mock environment it makes the folder easier to find.***

**Create an Admin User.**

- Right click on the `_ADMINS OU` → Select `New` → Click `User`.
- Create a Name, user logon name and password for your user.
	- Uncheck change password at next logon.
	- Check Password never expires.
***In a realistic environment you would want the user to change their password on first logon for security reasons and have a password expiration date. Since we are playing the role of the user this is not necessary. Also, Remote Desktop Connection doesn't allow for this.***

[pic 2.7]

**Set the User as an Admin.**

- Open the `_ADMIN OU` and right click on the Admin User's name → Select `Properties`.
- Click the tab `Member Of` → Select `Add`.
- Type `Domain Admins` → Click `Check Names`.
- Click `OK` → Click `Apply` → Click `OK`.

[pic 2.8}

---

### Step 4 - Join Client VM to the Domain
Joining the Client VM to the domain connects it to the Active Directory environment managed by the Domain Controller. This allows the Client VM to use domain credentials, receive Group Policies, and access shared resources within the domain.

- Log in to the Client VM via Remote Desktop Connection using the original user name and password you created when first setting up the VM.
- Right click the start menu and select `System`.
- Click `Rename this PC (advanced)`.
- Under the `Computer Name` tab click `Change`.
- Select `Domain` → Enter `mydomain.com` → Click `OK`.
- Enter the Domain User Name and Password for you Domain Admin account.
- Restart the VM to apply changes.

[pic 2.9]

[pic 3.0]

---

### Step 5 - Verify Client VM is connected to the Domain

- Log in to the DC-1 VM via Remote Desktop Connection as the Admin User we created in Step 3.
- Use DC-1's VM Public IP Address.
- Username: `mydomain.com\[Admin User logon name you created]`.
- Password: `[Password you created for the Admin User]`.

From the start menu open `Active Directory Users and Computers`.
- Click the dropdown for `mydomain.com` to expand the folders
- Click on the `Computers` container and verify the Client VM shows up.

[pic 3.1]

---
### Step 6 - Setup Remote Desktop Access for Users on Client VM
Setting up Remote Desktop Access allows authorized domain users (employees) to connect remotely to the Client VM over the network.

- Right click the start menu and select `System`.
- Click on `Remote desktop` on the right hand menu.
- Under User accounts click on `Select user that can remotely access this PC`.
- Click `Add` → Type `domain users` in the object names box and click `Check Names` → Click `OK`.

[pic 3.2]

---

### Step 7 - Create Employee (Client) Users in Active Directory
These will be the domain user accounts that will represent employees. These accounts will be used to test domain logons, Group Policy, and access control in later steps.

- From the start menu open `Active Directory Users and Computers`.
- Click on `mydomain.com` → Right click on the OU `_EMPLOYEES` → Click `New` → Click `User`.
- Create 3-5 different users; Custom name & Password. **Be sure to uncheck the box "User must change password at next logon. Remote Desktop logon does not allow this to happen.**
- Log out of the Client VM as the admin user and log in using one of the client users you just created.

---

## This concludes Part 2 - Installing and configuring Active Directory on the Domain Controller. <br>
**In the next part we will:**
- Configure Group Policy.
- Replicate account lockouts and unlocking user accounts within Active Directory.
- Disabling user accounts within Active Directory.
