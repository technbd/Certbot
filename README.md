
## Let’s Encrypt Certbot:



## Install Certbot:

```
yum install epel-release -y
```


### Method-1: Snap (Recommended)


```
snap install --classic certbot
```



```
ln -s /snap/bin/certbot /usr/bin/certbot
```


#### For Apache:

_Get and install your certificates:_

```
certbot --apache
```


_Just get a certificate:_

```
certbot certonly --apache
```



#### For Nginx:



_Get and install your certificates:_

```
certbot --nginx
```


_Just get a certificate:_

```
certbot certonly --nginx
```




#### Test automatic renewal: 

The Certbot packages on your system come with a cron job or systemd timer that will renew your certificates automatically before they expire. You will not need to run Certbot again, unless you change your configuration. 

The command to renew certbot is installed in one of the following locations:

```
/etc/crontab/

/etc/cron.*/*

systemctl list-timers
```


_You can test automatic renewal for your certificates by running this command:_

```
certbot renew --dry-run
```




#### Lists all active systemd timers:

```
systemctl list-timers


NEXT                         LEFT          LAST                         PASSED    UNIT                         ACTIVATES

Tue 2025-02-18 01:17:23 +06  3h 11min left Mon 2025-02-17 12:47:12 +06  9h ago    certbot-renew.timer          certbot-renew.service
```



```
find / -name certbot-renew*

/etc/systemd/system/timers.target.wants/certbot-renew.timer
/usr/lib/systemd/system/certbot-renew.timer
/usr/lib/systemd/system/certbot-renew.service
```



---
---




### Method-2: 


#### For Apache:

```
yum install mod_ssl -y
```


```
yum install certbot python2-certbot-apache -y

dnf install certbot python3-certbot-apache -y
```



#### For Nginx:

```
yum install certbot python2-certbot-nginx -y

dnf install certbot python3-certbot-nginx -y
```




#### For Ubuntu:

```
apt install -y certbot python3-certbot-apache 

apt install -y certbot python3-certbot-nginx 
```



---
---



### Method-3: Alternative: Pip

_Remove certbot-auto and any Certbot OS packages:_

```
apt remove certbot

dnf remove certbot
```


_For RPM-based distributions:_
```
dnf install python3 augeas-libs
```


_For APT-based distributions:_
```
apt update
apt install python3 python3-venv libaugeas0
```


_Set up a Python virtual environment:_
```
python3 -m venv /opt/certbot/

/opt/certbot/bin/pip install --upgrade pip
```



#### For Apache:


_To install Certbot:_
```
/opt/certbot/bin/pip install certbot certbot-apache
```


```
ln -s /opt/certbot/bin/certbot /usr/bin/certbot
```


```
certbot --apache

certbot certonly --apache
```



#### For Nginx:


_To install Certbot:_
```
/opt/certbot/bin/pip install certbot certbot-nginx
```


```
ln -s /opt/certbot/bin/certbot /usr/bin/certbot
```


```
certbot --nginx

certbot certonly --nginx
```




```
certbot --version

certbot 1.22.0
```




### Deleting certificates:

`Note`: Do not manually delete certificate files from inside `/etc/letsencrypt/`. Always use the `delete` subcommand: 



```
ll /etc/letsencrypt/live/
```


```
certbot certificates
```


```
certbot delete --cert-name example.com

## or, to choose from a list:

certbot delete
```




---
---



## Obtain and Install SSL Certificate: 


### For Apache: 

#### Certfifcate and automatically configure it on Apache (recommended):

```
certbot --apache
certbot --apache -d example.com
certbot --apache -d example.com -d www.example.com
```


#### Request a certificate without configuring Apache:

```
certbot certonly
certbot certonly --apache 
certbot certonly --apache -d www.example.com
```





```
ll /etc/letsencrypt/live/www.example.com


lrwxrwxrwx 1 root root   39 Feb 12 22:58 cert.pem -> ../../archive/mon.technbd.net/cert1.pem
lrwxrwxrwx 1 root root   40 Feb 12 22:58 chain.pem -> ../../archive/mon.technbd.net/chain1.pem
lrwxrwxrwx 1 root root   44 Feb 12 22:58 fullchain.pem -> ../../archive/mon.technbd.net/fullchain1.pem
lrwxrwxrwx 1 root root   42 Feb 12 22:58 privkey.pem -> ../../archive/mon.technbd.net/privkey1.pem
-rw-r--r-- 1 root root  692 Feb 12 22:58 README
```


#### Each File Does:

- `cert.pem` → The server certificate for your domain.
- `chain.pem` → Intermediate certificate(s) from Let's Encrypt.
- `fullchain.pem` → cert.pem + chain.pem combined (used for Nginx/Apache).
- `privkey.pem` → The private key (used for SSL decryption).




```
cp privkey.pem privkey.key
cp fullchain.pem fullchain.crt
```



_Check Certificate:_

```
certbot certificates


Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Found the following certs:
  Certificate Name: mon.technbd.net
    Serial Number: 420fabcf67a9eeffc20b439f222b662d1a5
    Key Type: RSA
    Domains: mon.technbd.net
    Expiry Date: 2025-05-13 10:00:21+00:00 (VALID: 89 days)
    Certificate Path: /etc/letsencrypt/live/mon.technbd.net/fullchain.pem
    Private Key Path: /etc/letsencrypt/live/mon.technbd.net/privkey.pem
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```





```
openssl s_client -connect mon.technbd.net:443
```





#### Apache HTTPS:

```
<VirtualHost *:443>
    DocumentRoot /opt/observium/html/
    ServerName mon.technbd.net

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/mon.technbd.net/fullchain.crt
    SSLCertificateKeyFile /etc/letsencrypt/live/mon.technbd.net/privkey.key


   <Directory "/opt/observium/html/">
     AllowOverride All
     Options FollowSymLinks MultiViews
     Require all granted
   </Directory>

    #ErrorLog /var/log/httpd/observium_error.log
    #CustomLog /var/log/httpd/observium_access.log combined

</VirtualHost>
```




### For Nginx: 

#### Certfifcate and automatically configure it on NGINX (recommended):

```
certbot --nginx
certbot --nginx -d example.com
certbot --nginx -d example.com -d www.example.com
```


#### Request a certificate without configuring NGINX:

```
certbot certonly
certbot certonly --nginx
certbot certonly --nginx -d www.example.com
```



#### Nginx HTTPS:


```
vim /usr/share/nginx/html/index.php

<?php
        phpinfo();
?>
```


```
grep www.sock /etc/php-fpm.d/www.conf
```



```
vim /etc/nginx/conf.d/technbd.net.conf


server {
    listen 443 ssl;

    server_name technbd.net mon.technbd.net;

    root /usr/share/nginx/html;
    index index.html index.htm index.php;

    ssl_certificate     /etc/letsencrypt/live/mon.technbd.net/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/mon.technbd.net/privkey.pem;
    #ssl_trusted_certificate /etc/letsencrypt/live/technbd.net/chain.pem;

    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/run/php-fpm/www.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

}
```



---
---




### Standalone certificate:

1. Stop your webserver, then run this command to get a certificate. Certbot will temporarily spin up a webserver on your machine.


#### Yes, my web server is not currently running on this machine.

```
certbot certonly --standalone
```


```
### Output:

Saving debug log to /var/log/letsencrypt/letsencrypt.log
Please enter the domain name(s) you would like on your certificate (comma and/or

space separated) (Enter 'c' to cancel): technbd.net
Requesting a certificate for technbd.net

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/technbd.net/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/technbd.net/privkey.pem
This certificate expires on 2025-05-18.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```



2. If you have a webserver that's already using port 80 and don't want to stop it while Certbot runs, run this command and follow the instructions in the terminal.


#### No, I need to keep my web server running.

```
certbot certonly --webroot
```


`Important Note:`  
To use the webroot plugin, your server must be configured to serve files from hidden directories. If `/.well-known` is treated specially by your webserver configuration, you might need to modify the configuration to ensure that files inside `/.well-known/acme-challenge` are served by the webserver.





---
---





### Manual Renew:

```
certbot certonly --force-renew --cert-name sub.domain.com

certbot certonly --force-renew --cert-name mon.technbd.net
```


### Automate Certificate Renewal:

Let’s Encrypt certificates expire every 90 days, so it’s essential to set up automatic renewal. Certbot installs a cron job by default, but you can verify or manually add one.

To check automatic renewal, you can test that the renewal works using the `--dry-run` parameter to simulate the process:

```
certbot renew --dry-run
```


#### Cron Job:

Add the `certbot` command to run daily. In this example, we run the command every day at noon. The command checks to see if the certificate on the server will expire within the next 30 days, and renews it if so. The `--quiet` directive tells `certbot` not to generate output.


```
crontab -e
```


```
## Runs the command every day at 12:00 PM (noon):
0 12 * * * /usr/bin/certbot renew --quiet
```







### Links:
- [SSL Checker](sslshopper.com)
- [Certbot installation](https://eff-certbot.readthedocs.io/en/latest/install.html#installation)
- [Certbot install Instructions](https://certbot.eff.org/instructions)
- [Certbot User Guide](https://eff-certbot.readthedocs.io/en/latest/using.html)
- [Encrypt SSL/TLS Certificates with NGINX](https://www.nginx.com/blog/using-free-ssltls-certificates-from-lets-encrypt-with-nginx/)

