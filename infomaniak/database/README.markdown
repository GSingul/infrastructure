##  Setup of Database Infrastructure for G Singul

# Guide: https://docs.openstack.org/install-guide/environment-sql-database-ubuntu.html

sudo apt install mariadb-server
sudo apt install python3-pymysql

# copy configuration file in folder /etc/mysql/mariadb.conf.d/

cp 99-openstack.cnf /etc/mysql/mariadb.conf.d/99-openstack.cnf

# restart the database service

sudo service mysql restart

# Secure the database (PROD)

sudo mysql_secure_installation

# From SSH create the service user:

CREATE USER 'srv_ik_mdb'@'%' IDENTIFIED BY 'YOURPASS';

# Enable and assign grant

GRANT ALL PRIVILEGES ON gsingul.* TO "srv_ik_mdb"@"%" IDENTIFIED BY "YOURPASS";
FLUSH PRIVILEGES;

