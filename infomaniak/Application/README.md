# Setup of Front-End Infrastructure for G Singul

## Pre-Requisite
Install the required applications

```
sudo apt install openjdk-21-jdk
sudo apt install git
sudo apt install maven
sudo apt install unzip
sudo apt install nginx -y
```

## Nginx configuration

#### Short intro

You should look at the following URL's in order to grasp a solid understanding
of Nginx configuration files in order to fully unleash the power of Nginx.

[wiki start](https://www.nginx.com/resources/wiki/start/)

[config pitfalls](https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/)

[directory structure](https://wiki.debian.org/Nginx/DirectoryStructure)

In most cases, administrators will remove this file from sites-enabled/ and
leave it as reference inside of sites-available where it will continue to be
updated by the nginx packaging team.

This file will automatically load configuration files provided by other
applications, such as Drupal or Wordpress. These applications will be made
available underneath a path with that package name, such as /drupal8.

Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.

#### Config SSL

Create the private key:

`openssl genpkey -algorithm RSA -out privkey.pem`

Create the certificate signed request (CSR):

`openssl req -new -key privkey.pem -out cert.csr -config openssl.cnf`

Check the certificate:

`openssl x509 -req -in cert.csr -signkey privkey.pem -out cert.pem -days 365 -extfile openssl.cnf -extensions req_ext`



#### Spring Boot
Spring boot normally uses a PKCS12 certificate, you can convert with:

`openssl pkcs12 -export -in cert.pem -inkey privkey.pem -out certificato.p12 -name mycert`




Create a folder ssl and add the files

```
sudo mkdir -p /etc/nginx/ssl
sudo cp /percorso/del/certificato.pem /etc/nginx/ssl/cert.pem
sudo cp /percorso/della/chiave.pem /etc/nginx/ssl/privkey.pem
sudo cp /percorso/della/catena.pem /etc/nginx/ssl/chain.pem
```


#### Setup nginx
Create or copy the file `default` in the folder `/etc/nginx/sites-available`

Replace the {IP}

`cp default /etc/nginx/sites-available/default `


## System Service
#### Environment file.
Create or copy the web-portal.env file and fill the values

`web-portal.env`

#### JAR package
Build the application from application folder

`mvn clean package -DskipTests`

Or just copy the Jar file in target folder

#### The service
Create or copy the web-portal.service file

`/etc/systemd/system/web-portal.service`

Reload daemon, enable if necessary and start or restart the service

```
sudo systemctl daemon-reload
sudo systemctl enable web-portal
sudo systemctl start web-portal
```

## Certificati per Keycloak
Per accedere a keycloak in https va registrato nella JVM il certificato di Keycloak generato sul server di authentication.

Recuperare il certificato e poi salvarlo nel keystore della JVM, attenzione ad usare un nuovo alia oppure rimuovere quello precedente!

```
echo | openssl s_client -connect 37.156.43.106:8443  2>/dev/null | openssl x509 > keycloak.crt
sudo keytool -import -trustcacerts -keystore /usr/lib/jvm/java-21-openjdk-amd64/lib/security/cacerts -storepass changeit -noprompt -alias keycloakpsql -file keycloak.crt
```

