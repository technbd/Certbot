
## Let’s Encrypt Certbot:



### Install Certbot:

Certbot is available in the EPEL repository. First, enable it and install Certbot:

```
yum install epel-release -y
```


```
yum list certbot
```


```
yum install mod_ssl -y
```



_For Apache:_

```
yum install certbot python2-certbot-apache -y

dnf install certbot python3-certbot-apache -y
```



_For Nginx:_

```
yum install certbot python2-certbot-nginx -y

dnf install certbot python3-certbot-nginx -y
```




_For Ubuntu:_

```
apt install -y certbot python3-certbot-apache 

apt install -y certbot python3-certbot-nginx 
```



```
certbot --version

certbot 1.22.0
```




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

    ssl_certificate     /etc/letsencrypt/live/mon.technbd.net/fullchain.crt;
    ssl_certificate_key /etc/letsencrypt/live/mon.technbd.net/privkey.key;

    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;

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





### Manual renew:

```
certbot certonly --force-renew --cert-name sub.domain.com

certbot certonly --force-renew --cert-name mon.technbd.net
```


### Automate Certificate Renewal:

Let’s Encrypt certificates expire every 90 days, so it’s essential to set up automatic renewal. Certbot installs a cron job by default, but you can verify or manually add one.

To check automatic renewal, you can simulate a dry run with:

```
certbot renew --dry-run
```



### Links:
- [SSL Checker](sslshopper.com)

