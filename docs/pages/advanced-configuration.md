---
layout: page
title: Advanced Configuration
permalink: /advanced-configuration/
---

## Running the Server on Default Ports

If you want to run this on the default port, the user running this example will
need permission to connect to that protected port. In our setup, we often have
multiple domains running on the same machine, and we use apache to map to this
setup. This allows you to run the server as a normal user.  Additionally, our
service does not directly support SSL, so you will need to do something similar
to this setup when you move to a production setup.

``` xml
<VirtualHost *:80>
  ServerName dams.mylibrary.org
  ServerAdmin admin@mylibrary.org
  RewriteEngine on
  RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,QSA,R=permanent]
</VirtualHost>

<IfModule mod_ssl.c>
 <VirtualHost *:443>
        ServerName dams.mylibrary.org
        SSLEngine on

        Header set Access-Control-Allow-Origin "*"
#        SSLCertificateFile      /etc/ssl/certs/__name__.cer
#       SSLCertificateKeyFile /etc/ssl/private/__name__.key

       # Required for RIIIF pathnames
       AllowEncodedSlashes On
       ProxyPreserveHost On
       ProxyPass / http://localhost:3000/ nocanon
       ProxyPassReverse / http://localhost:3000/ nocanon
</VirtualHost>
</IfModule>
```
