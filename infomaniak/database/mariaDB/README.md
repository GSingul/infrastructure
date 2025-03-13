# Setup of Database Infrastructure for G Singul

## Guide: [See documentation](https://docs.openstack.org/install-guide/environment-sql-database-ubuntu.html)
Install the required applications:

`sudo apt install mariadb-server`

`sudo apt install python3-pymysql`

## Configuration file
Create or copy from the repository the configuration file `99-openstack.cnf`.

Copy the configuration file `99-openstack.cnf` in folder /etc/mysql/mariadb.conf.d/


`cp 99-openstack.cnf /etc/mysql/mariadb.conf.d/99-openstack.cnf`


## Database initialization
Create or copy from the repository the configuration file `db.sql`.

Execute the SQL scripts

`mysql db.sql`


NOTE: If not already done, connect on the server via SSH and create the service user, replace YOURPASS with your secure password

`CREATE USER 'srv_ik_mdb'@'%' IDENTIFIED BY 'YOURPASS';`

Enable and assign grant

`GRANT ALL PRIVILEGES ON gsingul.* TO "srv_ik_mdb"@"%" IDENTIFIED BY "YOURPASS";`

`FLUSH PRIVILEGES;`


## Restart the database service
`sudo service mysql restart`

## Secure the database (PROD)
`sudo mysql_secure_installation`

