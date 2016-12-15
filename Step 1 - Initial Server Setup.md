#Step 1

	Span a Digital Ocean droplet (or any other of your choice, the guide will assume you have a VPS)

		setup SSH to use with the droplet by adding key during initial setup only

	let the IP address be 132.158.65.24

	login to droplet
		ssh root@132.158.65.24 || if using SSH -> ssh -I " key " root@132.158.65.24   {from linux shell}

	update the sources
		apt-get update	{no need to add sudo, since we are already root}

	update the system
		apt-get dist-upgrade {to update the system with lates patches}

	

	
