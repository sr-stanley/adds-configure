# adds-configure


Part 2 - Installing and configuring Active Directory on the Domain Controller. 


Step 1 - Install Active Directory Domain Services

Using the Public IP Address, remote desktop into your Domain Controller VM. Server Manager should boot once you log in, if not find it via the start menu.

Click "Add roles and features" 
	- From the left hand menu select "Server Roles" and click the box for "Active Directory Domain Services" → Click "Add Features".

[pic 2.1] 

	- From the left hand menu select "Confirmation" and click the box for "Restart the destination server automatically if required" → Click "Yes" → Click "Install".
	- After installation click "Close"

[pic 2.2]

--------------------------------------------------

Step 2 - Configure Active Directory to become a Domain Controller

Inside the Server Manager click the flag icon at the top of the screen and select "Promote this server to a domain controller".
- Click "Add a new forest".
- Root domain name: "mydomain.com" (could be anything) → Click "Next".
- Create a Directory Services Restore Mode (DSRM) password.
- Click "Next" until you get to the option to Install.

[pic 2.3]

After instillation the VM will restart and you will need to reconnect via Remote Desktop Connection as a domain user instead of a normal user.
- Use DC-1's VM Public IP Address.
- Username: "mydomain.com\labuser".
- Password: Same as before.

[pic 2.4]

Under Roles and Server Groups you should now see the Active Directory Domain Services along with other Roles.

[pic 2.5]

--------------------------------------------------

Step 3 - Create a Domain Admin User

From the start menu click the folder for "Windows Administrative Tools" and select "active Directory Users and Computers". 

[pic 2.6]

Create two Organizational Units (OU's) one for Employees and one for Admins.
First click the down arrow for mydomain.com to expand the Containers and OU's.

1. Right click on mydomain.com → Select "New" → Click "Organizational Unit" → Name "_EMPLOYEES" → Click "OK". 

2. Right click on mydomain.com → Select "New" → Click "Organizational Unit" → Name "_ADMINS" → Click "OK".

(Normally you wouldn't use an underscore but for the mock environment it makes the folder easier to find)

Create an Admin User.

- Right click on the _ADMINS OU → Select "New" → Click "User".
- Create a Name, user logon name and password for your user.
	- Uncheck change password at next logon.
	- Check Password never expires.
(In a realistic environment you would want the user to change their password on first logon for security reasons and have a password expiration date. Since we are playing the role of the user this is not necessary.)

[pic 2.7]

Set the User as an Admin

- Open the _ADMIN OU and right click on the Admin User's name → Select "Properties".
- Click the tab "Member Of" → Select "Add".
- Type "Domain Admins" → Click "Check Names"
- Click "OK" → Click "Apply" → Click "OK".

[pic 2.8}

--------------------------------------------------

Step 4 - Join Client VM to the Domain

- Logon to the Client VM via Remote Desktop Connection using the original user name and password you created when setting up the VM.
- Right click the start menu and select "System".
- Click "Rename this PC (advanced).
- Under the "Computer Name" tab click "Change".
- Select "Domain" → Enter "mydomain.com" → Click "OK".
- Enter the Domain User Name and Password for you Domain Admin account.
- Restart the VM to apply changes.

[pic 2.9]

[pic 3.0]

--------------------------------------------------

Step 5 - Verify Client VM is connected to the Domain

- Logon to the DC-1 VM via Remote Desktop Connection as the Admin User we created in Step 3.
- Use DC-1's VM Public IP Address.
- Username: "mydomain.com\[Admin User logon name you created]".
- Password: [Password you created for the Admin User]

From the start menu open "Active Directory Users and Computers".
- Click the dropdown for mydomain.com to expand the folders
- Click on the Computers container and verify the Client VM shows up.

[pic 3.1]
