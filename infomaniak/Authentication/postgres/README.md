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

None for Postgres



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

### The service
Create or copy the keycloak.service file

`/etc/systemd/system/keycloak.service`

Reload daemon, enable if necessary and start or restart the service

```
sudo systemctl daemon-reload
sudo systemctl enable keycloak
sudo systemctl start keycloak
```


## Realm

### Create a new realm
Signin on kaycloak with admin (IP:8443).

On menu Keycloak click and on the submenu click on "Create realm"

On field Realm name enter: gsingul

### Create a new client
On realm gsingul, clik on menu item "Clients" and then the button "Create client"

#### General Setting
Field Client ID: gsingul-id

Field Name: GSingul client

#### Capability config
Enable Client Authentication

Enable Authorization

#### Login settings
Field Valid redirect URIs: https://gsingul.ch/*

Field Valid post logout redirect URIs: https://gsingul.ch/

Field Web origins: https://gsingul.ch/

#### Create the roles
Once the client is saved, moves on Roles tab and add the roles

Create role: admin

Create role: teacher

Create role: manager

### Update the environemnt variables
GS_OAUTH2_KC_CLIENT_ID=gsingul-id

GS_OAUTH2_KC_CLIENT_SECRET={Credential - Client secret

GS_OAUTH2_KC_REDIRECT_URI=https://gsingul.ch

GS_KC_CLIENT_UUID={Client UUID - read from url of client}

### Create a second client for application access
On realm gsingul, clik on menu item "Clients" and then the button "Create client"

#### General Setting
Field Client ID: service-account-client

Field Name: Application service client

Enable Client authentication

Select option Service accounts roles

#### Service Account roles
Assign roles for:

- view-clients
- query-users
- query-clients
- view-users

### Client scopes
Search the client scope "roles" and click it

#### Setting
Enable Include in token scope

#### Mappers

Select the "client roles"

Select Client ID with the gsingul-id client

Enable Add to ID token

#### Scope
Add following roles
- query-users
- query-clients
- view-users
- view-clients
- manage-users

### Realm setting
On realm gsingul, goes in menu Realm settings, in Login tab:

#### Login screen customization
Enable User registration

Enable Forgot password 

#### Email settings
Enable Email as username

Enable Login with email

#### User registration
Add following roles
- view-users