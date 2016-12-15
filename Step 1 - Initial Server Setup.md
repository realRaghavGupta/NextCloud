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
###Add Public Key Authentication (Recommended)
		
	Generate a Key Pair
			ssh-keygen

		su - xyz
		mkdir ~/.ssh
		chmod 700 ~/.ssh
		nano ~/.ssh/authorized_keys
			Insert public key
		Hit CTRL-x to exit the file, then y to save changes

	Restrict permissions to file
		chmod 600 ~/.ssh/authorized_keys

		####return to root
			exit

###Edit SSh Daemon for security

	nano /etc/ssh/sshd_config

		Change Port 22 to any other port {we use the port 1555 for this guide}
		Change PermitRootLogin to no

		systemctl reload sshd
			OR
		systemctl restart sshd

###Test Log in
		ssh xyz@132.158.65.24

###Setup Basic Firewall
```ShellSession

	sudo ufw allow 1555/tcp	{ssh port}
	sudo ufw allow 80/tcp 		{http port}
	sudo ufw allow 443/tcp 	{https port}
```

###Enable Firewall
		sudo ufw enable

###Configure Timezones
		sudo dpkg-reconfigure tzdata

###Configure NTP Synchronization
		sudo apt-get install ntp


		
