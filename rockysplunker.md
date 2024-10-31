### Install Splunk Server on Rocky Linux
1) Install `wget`, Rocky Linux minimal install does not have `wget` installed.

   `sudo dnf install wget`
   
2) Download latest Splunk Enterprise RPM and its SHA512 hashsum. You will have to login to grab latest RPM package and its hashsum. Thankfully Splunk even generates a prepared `wget` command for the latest package.![[Pasted image 20241031024945.png]]
   
3) Generate SHA512 hashsum to verify file integrity by comparing it to the hashsum you downloaded. 

   `sha512sum [Splunk RPM Filename]`

   and

   `cat [Splunk RPM Filename].sha512`
   
   If hashsums do not match, your file integrity is compromised!

4)  Install RPM package.

   `sudo rpm -iv [Splunk RPM Filename]`
	   `-i : Install`
	   `-v : verbose`

5) Start Splunk using the following command and accept the EULA.

   `sudo /opt/splunk/bin/start`
   
6) Create dashboard administrator credentials.
   
7) Add firewall rule to add the web dashboard, API socket, and to send logs from Forwarders.

   `sudo firewall-cmd --add-port=8000/tcp --permanent` 
   `sudo firewall-cmd --add-port=8089/tcp --permanent`
   `sudo firewall-cmd --add-port=9997/tcp --permanent`
   
   Confirm added ports to firewall rules:

   `sudo firewall-cmd --list-all | grep -m 1 ports`
   
   Reload firewall rules

   `sudo firewall-cmd --reload`

8) We are going to automate updating the system, and starting the Splunk server after the OS loads.

   `mkdir ~/scripts`

   Create the following `~/scripts/start.sh` script:   

```
dnf update -y
dnf upgrade -y
/opt/splunk/bin/start
```

Create `/etc/systemd/system/splunker.service` a systemd service to have it run this script at start up. 

```
[Unit]
Description=Start Splunk at boot
After=network.target

[Service]
ExecStart=/home/USERNAME/scripts/start.sh
User=root
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
