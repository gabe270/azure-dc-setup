# Azure Lab Setup Guide

## Setup Resources in Azure

1. **Create the Domain Controller VM (Windows Server 2022) named “DC-1”**:
   - Go to the Azure Portal.
   - Navigate to the Virtual Machines section.
   - Click on "Create" to start the VM creation process.
   - Choose Windows Server 2022 as the OS.
   - Provide a name for the VM (e.g., "DC-1").
   - Configure the necessary settings such as Region, Resource Group, and Virtual Network (Vnet).
   - Set the Domain Controller’s NIC Private IP address to be static during the VM creation process.

2. **Create the Client VM (Windows 10) named “Client-1”**:
   - Follow similar steps as above but choose Windows 10 as the OS.
   - Ensure to use the same Resource Group and Vnet as created for the Domain Controller.
   - Verify that both VMs are in the same Vnet for connectivity.

## Ensure Connectivity between the Client and Domain Controller

1. **Ping Test**:
   - Log into Client-1 with Remote Desktop.
   - Open Command Prompt and ping DC-1’s private IP address with `ping -t <ip address>` for perpetual ping.

2. **Firewall Configuration**:
   - Log into the Domain Controller.
   - Enable ICMPv4 in the local Windows Firewall to allow ping requests.
<img width="1344" height="454" alt="image" src="https://github.com/user-attachments/assets/f3c5b78c-006a-4b58-9cad-9437359b65c8" />


3. **Verification**:
   - Return to Client-1 and check if the ping to DC-1's private IP address succeeds.
<img width="649" height="469" alt="image" src="https://github.com/user-attachments/assets/228035e7-8d1c-4100-864a-ee00c9e291f8" />


## Install Active Directory

1. **Install AD DS**:
   - Log into DC-1.
   - Install Active Directory Domain Services using Server Manager or PowerShell.

2. **Promote as a Domain Controller**:# azure-dc-setup
   - Promote DC-1 as a Domain Controller and set up a new forest as mydomain.com.
   - Restart the server and log back in as user: mydomain.com\labuser.

## Create Admin and Normal User Account in AD

1. **Create Organizational Units**:
   - Open Active Directory Users and Computers (ADUC) on DC-1.
   - Right-click on the domain name and select "New" -> "Organizational Unit".

   - Name the first OU as “_EMPLOYEES”.
   - Similarly, create another OU named “_ADMINS”.

2. **Create User Accounts**:
   - In ADUC, right-click on the “_EMPLOYEES” OU and select "New" -> "User".
   - Enter the user details for a new employee.
   - Set a password for the user account.
   - After creating the user, navigate to the “_ADMINS” OU and add “a-gabe” to the “Domain Admins” Security Group.

   - Log out or close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\a-gabe” to ensure proper access permissions.

## Join Client-1 to your Domain (mydomain.com)

1. **Configure DNS Settings**:
   - Go to the Azure Portal and navigate to Client-1's settings.

   - Set Client-1’s DNS settings to the DC’s Private IP address.!

   - Restart Client-1 to apply the DNS changes.

2. **Join Domain**:
   - Log into Client-1 (Remote Desktop) as the original local admin (labuser).
   - Open the Control Panel, navigate to System and Security -> System -> Change settings -> Change -> Domain and enter the domain name (mydomain.com).

   - Follow the prompts to join the domain. The computer will restart after joining the domain.


3. **Verification**:
   - Log into the Domain Controller (DC-1) via Remote Desktop.
   - Open ADUC and verify if Client-1 shows up in the “Computers” container under the domain.

## Setup Remote Desktop for Non-administrative Users on Client-1

1. **Configure Remote Desktop**:
   - Log into Client-1 as mydomain.com\jane_admin.
   - Open system properties by right-clicking on This PC -> Properties.
   - Click on "Remote settings" and select the "Remote" tab.

   - Check the box labeled "Allow remote connections to this computer".
   - Click "Select Users" and add the “domain users” group to allow access to remote desktop (Normally, we wouild use group policies for this, but this is out of the scope of this lab).


2. **Access as Non-administrative User**:
   - Normal, non-administrative users can now log into Client-1 using Remote Desktop by providing their domain credentials.

## Create Additional Users and Attempt to Log into Client-1

1. **Create Users**:
   - Log into DC-1 as jane_admin.
   - Open PowerShell_ise as an administrator.
   - Run the provided PowerShell script ([Generate-Names-Create-Users.ps1](https://github.com/gabe270/generate-names-create-user-ps1/blob/main/Create-New-Users.ps1)) to create additional user accounts.

   - Review ADUC to confirm the creation of new user accounts in the appropriate OU.
<img width="1541" height="920" alt="image" src="https://github.com/user-attachments/assets/79499f8b-30f7-4155-b6ac-5f3be046e2de" />


2. **Verification**:
   - Attempt to log into Client-1 with one of the newly created accounts to ensure proper user access and authentication.
     <img width="989" height="576" alt="image" src="https://github.com/user-attachments/assets/6078e464-fa59-424a-8076-add72e6eaaa7" />



## Finish

Congratulations! You have successfully set up and configured your Azure lab environment.
