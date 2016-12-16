#Step 2
###Install nginx server
We will not use default build from repository but build it from source. This is to add nginx-ct module to enable certificate transparency, GeoIP module to restrict use by IP, cache purge module so as to allow control of cache by nginx.
A nginx build for UBUNTU 16.04 is available in binaries folder.
It was built from source using nginx-1.11.7, openssl-1.0.2j, pcre-8.39, zlib-1.2.8, ngx_cache_purge-2.3 and nginx-ct.
```ShellSession
apt-get install nginx.deb
```
	{we can visit the public IP in browser to check if its working}
	{There will be a separate guide to build nginx from source yourself, or you can google it}
###Edit nginx.conf
		The path to the file is /etc/nginx/nginx.conf
```ShellSession
Change the file to the one in conf folder
```
###Setup web directories
```ShellSession
sudo mkdir -p /var/www/nextcloud
sudo mkdir -p /var/nc_data
```
###Create configuration file for nextcloud
```ShellSession
sudo mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.bak
sudo nano /etc/nginx/conf.d/nextcloud.conf
```
		{The nextcloud.conf is available in conf folder}
###Directories for nginx-cache
```ShellSession
mkdir -p /usr/local/tmp
mkdir -p /usr/local/tmp/cache
```
###Setup certbot (for Let's Encrypt certiicates)
```ShellSession
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
```
	Get certificates
>We will generate CSR and then sign the certificate. So that we can afterwards implemet HPKP for our server

>	Generate an account key
```ShellSession
openssl genrsa 4096 > account.key
```

>	Generate a private key
```ShellSession
openssl genrsa -out domain1.key 4096
```

>	Generate a CSR
```ShellSession
openssl req -new -key domain1.key -sha256 -out domain1.csr
```

>	Get certificate from Letsencrypt
```ShellSession
./certbot certonly -d DOMAIN -d ADDitional_domains --rsa-key-size 4096 --must-staple --hsts --uir --staple-ocsp --csr /home/domain1.csr --key-path account.key
```
	This would generate 3 files in the same folder - 0000_cert.pem 0000_chain.pem 0001_chain.pem

>	Generate DHPARAM
```ShellSession
openssl dhparam -out dhparam.pem 4096 /etc/ssl/
```
		This can take a long time.

###Install PHP
We can install php directly from ubuntu repositories
```ShellSession
sudo apt-get install php7.0 php7.0-common php7.0-curl php7.0-gd php7.0-gmp php7.0-json php7.0-mysql php7.0-tidy php7.0-xml php7.0-bz2 php7.0-fpm php7.0-imap php7.0-intl php7.0-mcrypt php7.0-zip php-apcu php-imagick php7.0-mbstring
```
##Configure PHP
###PHP global
		/etc/php/7.0/fpm/pool.d/www.conf
>Search for
		Pass environment variables like LD_LIBRARY_PATH. ALL $VARIABLES are taken from the current environment

remove the comment

```ShellSession
; Pass environment variables like LD_LIBRARY_PATH. All $VARIABLEs are taken from
 ; the current environment.
 ; Default Value: clean env
env[HOSTNAME] = $HOSTNAME
env[PATH] = /usr/local/bin:/usr/bin:/bin
env[TMP] = /tmp
env[TMPDIR] = /tmp
env[TEMP] = /tmp
```
###Enable APC for PHP Cli
		/etc/php/7.0/cli/php.ini
```ShellSession
...
; should be bit bigger than upload_max_filesize
post_max_size = 510M
...
; cannot be bigger than post_max_size
upload_max_filesize = 500M
...
; on online servers this could require bigger values (my server is at home) 
max_input_time = 3600 
...
; from Nextcloud documentation 
output_buffering = Off 
...
; it seems like Nextcloud needs time to move the file to it's 
; final place after upload and that can take quite some time for big files 
max_execution_time = 300 
...
; Temp-directory for HTTP(s) uploaded files
upload_tmp_dir = /upload_tmp
...
```
>	At the end of file, add __apc.enable_cli=1__ , as shown:
```ShellSession
...
; Local Variables:
; tab-width: 4
apc.enable_cli = 1
; End:
```

###php.ini
		/etc/php/7.0/fpm/php.ini
```ShellSession
...
; should be bit bigger than upload_max_filesize 10250M = (10*1024M) + 10M
post_max_size = 510M
...
; cannot be bigger than post_max_size
upload_max_filesize = 500M
...
; on online servers this could require bigger values (my server is at home) 
max_input_time = 300 
...
; from Nextcloud documentation 
output_buffering = Off 
...
; it seems like Nextcloud needs time to move the file to it's 
; final place after upload and that can take quite some time for big files 
max_execution_time = 1800
...
; Temp-directory for HTTP(s) uploaded files
upload_tmp_dir = /upload_tmp
...
```

>	Create the temp directory and give it persmission
```ShellSession
sudo mkdir /upload_tmp
chown -R www-data:www-data /upload_tmp

###Adjust PHP settings
		/etc/php/7.0/fpm/php-fpm.conf
```ShellSession
...
emergency_restart_threshold = 10
...
emergency_restart_interval = 1m
...
process_control_timeout = 10s
...
```

>	Restart PHP and nginx
```ShellSession
systemctl restart php7.0-fpm && systemctl restart nginx
```
