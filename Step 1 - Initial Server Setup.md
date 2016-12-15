#Step 1
###Root Login
	To log into your server, you will need to know your server's public IP address and the password for the "root" user's account.
```ShellSession
ssh root@SERVER_IP_ADDRESS
```
```ShellSession
apt-get update
```
			{no need to add sudo, since we are already root}
```ShellSession
apt-get dist-upgrade
```
			{to update the system with lates patches}
###Add non root user
```ShellSession
adduser xyz
```
			{xyz can be any user}
###Add the user to sudo group
```ShellSession
usermod -aG sudo xyz
```
###Edit SSh Daemon for security
```ShellSession
nano /etc/ssh/sshd_config
```
			Change Port 22 to any other port {we use the port 1555 for this guide}
```ShellSession
Port 1555
```
			Change PermitRootLogin to no
```ShellSession
PermitRootLogin no
```
			Reload SSH Daemon
```ShellSession
systemctl reload sshd
```
###Setup Basic Firewall
```ShellSession
sudo ufw allow 1555/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```
###Enable Firewall
```ShellSession
sudo ufw enable
```
###Configure Timezones
```ShellSession
sudo dpkg-reconfigure tzdata
```
###Configure NTP Synchronization
```ShellSession
sudo apt-get install ntp
```
