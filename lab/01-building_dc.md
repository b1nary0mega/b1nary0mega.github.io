# Building out a lab - DC Edition

☣️CAUTION: **Do NOT use in production!** In no way should *any* of the below content be utilized for or in a production environment. Additionally, **All** machines should be segmented off from everything you love and hold dear (or even slightly care about), within your network. You have been warned.

🛑IMPORTANT: This exercise utilizes [VirtualBox](https://www.virtualbox.org/). Make sure you understand [how VirtualBox networking works](https://www.youtube.com/watch?v=qasi0j_tgsg).

💡NOTE: Unless explicity called out, use default options.

## Building the VM

![](img/Pasted%20image%2020220508122723.png)

_Figure 01 - General Settings_

![](img/Pasted%20image%2020220508122827.png)

_Figure 02 - System Settings_

![](img/Pasted%20image%2020220508122842.png)

_Figure 03 - Storage Settings_

![](img/Pasted%20image%2020220508122853.png)

_Figure 04- Network Settings_

## Installing Windows Server

![](img/Pasted%20image%2020220508122906.png)

_Figure 05 - Installation Settings_

As we are performing a clean install, select "Custom Install" and make sure to create a new partion for the installation.

![](img/Pasted%20image%2020220508123213.png)

_Figure 06 - General Settings_

![](img/Pasted%20image%2020220508124049.png)

_Figure 07 - Set an Administrator password_

## Configuring Services

💡Note: At this point, you could install VM tools if desired.

![](img/Pasted%20image%2020220508125855.png)

_Figure 08 - Update the machine's name_

### Active Directory Domain Services
From Server Manager, select "Manage / Add Roles and Features". 
Click next until you are at the "Server Role", and select "Active Directory Domain Services". 
Click continue until the feature is installed then click close.

![](img/Pasted%20image%2020220508130315.png)

_Figure 09 - Install Active Directory Domain Services_

![](img/Pasted%20image%2020220508130647.png)

_Figure 10 - Promote the machine to a DC_

![](img/Pasted%20image%2020220508130859.png)

_Figure 11 - Create a new forest_

![](img/Pasted%20image%2020220508131103.png)

_Figure 12 - Use the same password for DSRM as was originally set_

You will most likely see an alert relating the the delegation for this DNS server lacking due to an authoritative parent zone not being found, go ahead and click next.
After a few moments, the NetBIOS domain name should populate with what was previously entered; in my case "WASTELAND". 
Click next.

Leave the default options for paths and click next until you are at the "Prerequisites Check" screen. 
Click "install".

Once everything has completed the machine will reboot.

![](img/Pasted%20image%2020220508131742.png)

_Figure 13 - Completion of AD service installation_

### Active Directory Certificate Services
#### Installation
From Server Manager, select "Manage / Add Roles and Features". 
Click next until you are reach "Server Role".
Select "Active Directory Certificate Services". 
Click on next until you reach "Role Services".
Select the following:

-   Certification Authority
-   Certificate Enrollment Policy Web Service
-   Certificate Enrollment Web Service
-   Certification Authority Web Enrollment

💡Note: Include any additional services that are required to support the above selections. 

Click next until you reach the "Confirmation" section.
Check the box for "Restart the destination server automatically if required". 
Click the "Install" button.

![](img/Pasted%20image%2020220508134617.png)

_Figure 14 - Adding in AD CS Service Roles_

#### Configuration 
Now we need to configure AD CS.

![](img/Pasted%20image%2020220508135653.png)

_Figure 15 - Configure AD CS_

![](img/Pasted%20image%2020220508140009.png)

_Figure 16 - Selecting the appropriate credentials to configure AD CS_

![](img/Pasted%20image%2020220508140125.png)

_Figure 17 - Select the first two services **Certification Authority** and **Certification Authority Web Enrollment**_

![](img/Pasted%20image%2020220508140305.png)

_Figure 18 - Select **Enterprise CA** for the setup type_

![](img/Pasted%20image%2020220508140436.png)

_Figure 19 - Select **Root CA** for the CA type_

![](img/Pasted%20image%2020220508140605.png)

_Figure 20 - Generate a new private key_

![](img/Pasted%20image%2020220508140706.png)

_Figure 21 - The default options provided are fine for our pusposes._

![](img/Pasted%20image%2020220508140927.png)

_Figure 22 - Update the **CA Common Name** if desired, but do **NOT** touch the **Distinguished Name Suffix**._

Click next until you are at the confirmation screen. 
Click configure.

![](img/Pasted%20image%2020220508141305.png)

_Figure 23 - Confirm Configuration for AD CS_

Choose to configure additional "Role Services" when prompted.

![](img/Pasted%20image%2020220508141656.png)

_Figure 24 - Select "Certificate Enrollment Web Services" to configure_

![](img/Pasted%20image%2020220508141846.png)

_Figure 25 - Choose the previously created CA for use_

Click next and on the following screen choose "Windows integrated authentication" as the authentication type. 
Click next.

![](img/Pasted%20image%2020220508142108.png)

_Figure 26 - Choose the "Use the built-in application pool identity" to communicate with the CA._

![](img/Pasted%20image%2020220508142255.png)

_Figure 27 - Choose the previously created certificate to encrypt traffic_

![](img/Pasted%20image%2020220508142356.png)

_Figure 28 - Make sure that the settings are as desired, and click "Configure"._

Choose to configure additional **Role Services** when prompted.

![](img/Pasted%20image%2020220508143808.png)

_Figure 29 - Select "Certificate Enrollment Policy Web Service" to configure_

![](img/Pasted%20image%2020220508143949.png)

_Figure 30 - Accept default settings until you reach the "Configure" screen and accept the settings_

#### Confirmation

![](img/Pasted%20image%2020220508142956.png)

_Figure 31 - Verify the server using *certutil.exe*._

💡Note: Copy the "Server" value found under "Description".

![](img/Pasted%20image%2020220508143144.png)

_Figure 32 - Confirm web services using the previously identified "Server" by appending "/certsrv" to the end._

💡Note: Use the same account to login.

![](img/Pasted%20image%2020220508143408.png)

_Figure 33 - Successfully logged into AD CS Web Services._

#### Conclusion
At this point you have a fully functional DC with AD CS web services setup. 
It is **HIGHLY** encouraged you export an OVA for backup purposes.
I would also recommend you get in the habit of creating a snapshot before doinging any additional work. Once the work has been performed, and changes confirmed, merge the snapshot back to a base image.
