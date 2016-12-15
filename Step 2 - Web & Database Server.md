#Step 2
###Install nginx server
>We will not use default build from repository but build it from source. This is to add nginx-ct module to enable certificate transparency, GeoIP module to restrict use by IP, cache purge module so as to allow control of cache by nginx.
>A nginx build for UBUNTU 16.04 is available in binaries folder.
>It was built from source using nginx-1.11.7, openssl-1.0.2j, pcre-8.39, zlib-1.2.8, ngx_cache_purge-2.3 and nginx-ct.
```ShellSession
apt-get install nginx.deb
```
