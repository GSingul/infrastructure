# Setup of Authentication Infrastructure for G Singul

## Pre-Requisite
Install the required applications

```
sudo apt install openjdk-21-jdk
sudo apt install unzip
```

## Download Keycloak
Get and install the most recent version of Keycloak, 
[Keycloak Github](https://github.com/keycloak/keycloak/releases/)

```
sudo wget -O /tmp/keycloak.zip https://github.com/keycloak/keycloak/releases/download/26.1.2/keycloak-26.1.2.zip
sudo unzip /tmp/keycloak.zip -d /opt/
sudo mv /opt/keycloak-* /opt/keycloak
```

## Download additional software

Add the required java connectors in the provider folder

```
sudo wget -P /opt/keycloak/providers https://downloads.mariadb.com/Connectors/java/connector-java-3.5.2/mariadb-java-client-3.5.2.jar
sudo wget -P /opt/keycloak/providers https://download.oracle.com/otn-pub/otn_software/jdbc/237/ojdbc17.jar
```

## Create a certificate with alias
create or copy from the repository the configuration file `san.cnf`.

Replace {CNIP},{DNS},{IP},{INTIP}

Execute the following commands to generate the certificate

```
openssl req -new -newkey rsa:2048 -nodes -keyout keycloak.key -out keycloak.csr -config san.cnf
openssl x509 -req -in keycloak.csr -signkey keycloak.key -out keycloak.crt -days 365 -extensions v3_req -extfile san.cnf
cat keycloak.key keycloak.crt > keycloak.pem
sudo cp keycloak.key /opt/keycloak/conf/
sudo cp keycloak.pem /opt/keycloak/conf/
```



## Setup Keycloak

For a standalone configuration, create or copy from the repository the configuration file `keycloak.conf`.

Replace {DBUSER},{DBPASS},{IP},{CERTPASS},{HOST}

Copy the configuration file `keycloak.conf` in folder /opt/keycloak/conf/

`cp keycloak.conf /opt/keycloak/conf/keycloak.conf`


## First time
The first time you need an administrative user to setup Keycloak

Run the command and define the administrative user and give a password 

`sudo /opt/keycloak/bin/kc.sh bootstrap-admin user`

## Start Keycloak
To run Keycloak, just run

`sudo /opt/keycloak/bin/kc.sh start --optimized &`


## System Service

#### The service
Create or copy the keycloak.service file

`/etc/systemd/system/keycloak.service`

Reload daemon, enable if necessary and start or restart the service

```
sudo systemctl daemon-reload
sudo systemctl enable keycloak
sudo systemctl start keycloak
```



