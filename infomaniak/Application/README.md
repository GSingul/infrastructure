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



