# HacktheBox-machine-Wifinetic
 Exploring Wireless Security with Wifinetic!**   I’ve just published a new article on my latest CTF experience with the Wifinetic machine, diving deep into wireless security and network monitoring. From FTP enumeration and password reuse attacks to cracking WPS PINs for full system access—it's all covered! 📚   In this article, you'll learn: - Enumeration techniques - Exploiting password reuse vulnerabilities 



HacktheBox “machine” Wifinetic
Description
Wifinetic is an easy Linux machine focused on wireless security and network monitoring. An exposed FTP service with anonymous access lets us download an OpenWRT backup, revealing the Access Point password. By analyzing shadow or passwd files, we can identify usernames and perform a password reuse attack on the SSH service, gaining access as the netadmin user. Using standard tools and placing the wireless interface in monitoring mode, we can brute force the WPS PIN to obtain the pre-shared key (PSK) and reuse it to gain root access via SSH.
👉Skills required
1.	Enumeration
2.	Basic Linux Knowledge
3.	Basic knowledge linux
👉Skills learned
1.	Password Resuse
2.	WPS Brute Force Attack

Analysis
Step:-1 This nmap command is to run a scan on the 10.129.229.90 IP address and includes the following options:
-Pn: Bypass the ping scan and do port scanning directly, assuming all hosts are live.
-sC: Run the default scripts, which are standard security tests and other common scripts by nmap.
-sV: Do service version scanning, which will reveal which services and their versions are running on which ports.

 


Step:-2 The system has three open ports: 21 (FTP), 22 (SSH), and 53 (DNS). Nmap reveals that FTP allows anonymous login and contains multiple files. To download all files at once, we can disable interactive mode and log into FTP with the following commands.
ftp 10.129.229.90
prompt
mget *
 


Step:-3 The ProjectGreatMigration.pdf document contains some information like an email, contact number and a domain name
 


Step:-4 ProjectOpenWRT.pdf discloses another email and some information about network infrastructure migration.
 


Step:-5 The Reaver tool can be used to assess and identify potential wireless networks security issues. There is also an OpenWrt backup archive present among the files. Let's extract the files from this backup.
tar -xvf backup-OpenWrt-2023-07-26.tar 
 
The extracted files include critical configurations, settings, and other essential information related to the current network setup. It is likely that the configuration files may contain some sensitive wireless PIN or access point password information about the target network setup.
Step:-6 FOOTHOLD
Let's examine the contents of the passwd file to know more about users present in the system
cat etc/passwd
 


Step:-7 The SSID or Wi-Fi name is set to OpenWrt , and the password for this Wi-Fi network is specified as VeRyUniUqWiFIPasswrd1! .
 


Step:-8 With this information, we can try logging into the SSH (Secure Shell) service using the provided credentials for both the root and netadmin accounts.
 
This was successful, and we were able to retrieve the user flag from /home/netadmin/user.txt.

Step:-9 Privilege escalation
Now that we have a foothold on the system, we can begin enumerating the network information and explore potential methods to gain root access. Let's start by checking for the presence of any wireless interfaces.

 



Step:-10 Next, we will shift our focus to the custom services running on the system.

 


Step:-11 The systemctl status wpa_supplicant.service command is used to check the current status of the WPA supplicant service, which manages wireless network connections. It shows whether the service is:
•	Active (running) or inactive (dead).
•	Loaded and if it is enabled to start at boot.
•	Any errors or issues preventing it from running.

 


Step:-12 The systemctl status hostapd.service command is used to check the status of the hostapd service, which is responsible for turning a system into a wireless access point. It provides details such as:
•	Loaded: Whether the service is loaded and enabled for startup at boot.
•	Active: If the service is currently running, inactive, or has encountered an error.
•	Logs: Recent log entries for troubleshooting.
 


Step:-13 The iwconfig command is used to configure and display information about wireless network interfaces in Linux. It shows the status of wireless connections and allows for manual configuration of parameters like SSID, frequency, mode, and encryption.
 


Step:-14 The iw dev command is used in Linux to display information about wireless network interfaces. It provides details such as:
•	List of wireless interfaces: Shows all wireless devices (e.g., wlan0).
•	Current status: Indicates if interfaces are connected or disconnected.
•	SSID and channel: Displays the network name and channel being used.
•	Operating mode: Shows the mode of the interface (e.g., managed, monitor).
 


Step:-15 Attempting to brute force the WPS PIN could potentially lead to obtaining the actual Wi-Fi password. Let's check if we've any pre installed tools that are having capabilities set to perform network related activities.
 


Step:-16 We discovered the reaver utility in the output, which allows us to potentially perform a WPS PIN attack. To proceed, we'll need the BSSID (Basic Service Set Identifier) of the Access Point (AP). The BSSID uniquely identifies the AP, enabling us to target it specifically for the attack.
To obtain the BSSID, we can use either the wash tool or the output from the iw command.

 


Step:-17 The command reaver -i mon0 -b 02:00:00:00:00 -vv -c 1 is used to initiate a WPS PIN attack on a specific Access Point (AP). Here's a brief breakdown of the options:
•	-i mon0: Specifies the wireless interface in monitor mode (e.g., mon0).
•	-b 02:00:00:00:00:00: Sets the BSSID of the target AP.
•	-vv: Enables verbose output for more detailed information during the attack.
•	-c 1: Specifies the channel number (1 in this case) on which the target AP is operating.

 


Step:-18 This was successful, and we obtained the WPA PSK: WhatIsRealAnDWhAtIsNot51121!. Based on previous enumeration, it's likely that the user is reusing passwords across accounts. We can attempt to use this password for SSH as the root user to see if it works.
su root
 
Both flags have been successfully found.
Thankyou for Visiting 

