# Setup of Database Infrastructure for G Singul

## Guide: [See documentation](https://www.postgresql.org/download/linux/ubuntu/)
Install the required applications:

```
sudo apt update
sudo apt upgrade -y
sudo apt install postgresql postgresql-contrib -y
```

## Create database

At first installation, you must create the database and the user for Keycloak:

```
sudo -u postgres psql
CREATE USER {username} WITH PASSWORD '{password}';
create database keycloak with owner {username};
GRANT ALL PRIVILEGES ON DATABASE keycloak TO {username};

```

## Configuration file
Configura le connessioni remote (opzionale)
Se desideri consentire connessioni remote (da una macchina diversa), devi configurare due file di PostgreSQL:

#### Modifica postgresql.conf

Permetti a PostgreSQL di ascoltare su tutte le interfacce di rete. Modifica il file di configurazione postgresql.conf:

```
sudo nano /etc/postgresql/16/main/postgresql.conf
```

Cerca la riga listen_addresses e modificala come segue:

```
listen_addresses = '*'
```

Questo permetterà al server di ascoltare su tutte le interfacce di rete.

#### Modifica pg_hba.conf

Per consentire connessioni remote, devi configurare il file pg_hba.conf:

```
sudo nano /etc/postgresql/16/main/pg_hba.conf
```

Aggiungi una riga come questa per consentire a tutti i client di connettersi con una password (modifica l'IP in base alle tue esigenze):

```
host    all             all             0.0.0.0/0            md5
```

Nota: Sostituisci 0.0.0.0/0 con l'indirizzo IP o il range di indirizzi IP da cui desideri consentire le connessioni.


## Restart the database service
`sudo service mysql restart`

## Secure the database (PROD)
`sudo mysql_secure_installation`


