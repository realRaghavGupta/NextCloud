#Step 1

	Span a Digital Ocean droplet (or any other of your choice, the guide will assume you have a VPS)

		setup SSH key to use with the droplet by adding key during initial setup only

	let the IP address be 132.158.65.24

	login to droplet
		ssh root@132.158.65.24 
		if using with key: ssh -I " key " root@132.158.65.24   {from linux shell}

	update the sources
		apt-get update	{no need to add sudo, since we are already root}

	update the system
		apt-get dist-upgrade {to update the system with lates patches}

###Add non root user

		adduser xyz {xyz can be any user}

###Add the user to sudo group
		
		usermod -aG sudo xyz

			####add details for the user

###Add Public Key Authentication (Recommended)
		
		####Generate a Key Pair
			ssh-keygen

		su - xyz
		mkdir ~/.ssh
		chmod 700 ~/.ssh
		nano ~/.ssh/authorized_keys
			Insert public key
		Hit CTRL-x to exit the file, then y to save changes

		####Restrict permissions to file
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
	>sudo ufw allow 1555/tcp	{ssh port}
	>sudo ufw allow 80/tcp 		{http port}
	>sudo ufw allow 443/tcp 	{https port}

###Enable Firewall
		sudo ufw enable

###Configure Timezones
		sudo dpkg-reconfigure tzdata

###Configure NTP Synchronization
		sudo apt-get install ntp


		
