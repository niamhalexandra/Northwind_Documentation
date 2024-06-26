## Set-up script

This can run on a fresh Ubuntu 20.04 server to install MySQL Server, configure it for remote connections, clone the repository containing the application, import the database schema, and create a new MySQL user 'group_2' with the necessary privileges.

```bash
#!/bin/bash

# Update and upgrade packages
sudo apt update -y
sudo DEBIAN_FRONTEND=noninteractive apt-get upgrade -y

# Install MySQL Server
sudo apt-get install -y mysql-server

# Configure MySQL Server for non-interactive secure installation
sudo mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';"
sudo mysql -e "FLUSH PRIVILEGES"

# Enable and restart MySQL service
sudo systemctl enable mysql
sudo systemctl restart mysql

# Configure MySQL to allow remote connections
MYSQL_CONF="/etc/mysql/mysql.conf.d/mysqld.cnf"
sudo sed -i 's/^bind-address\s*=.*/bind-address = 0.0.0.0/' $MYSQL_CONF
sudo systemctl restart mysql

# Clone the repository containing the application
mkdir -p ~/repo && cd $_
git clone https://github.com/followcrom/northwind_python_app.git

# Import the database schema
sudo mysql -u root -proot < ~/repo/northwind_python_app/northwind_sql.sql

# Create a new MySQL user 'group_2' and grant privileges
sudo mysql -u root -proot -e "CREATE USER IF NOT EXISTS 'group_2'@'%' IDENTIFIED BY 'password';"
sudo mysql -u root -proot -e "GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'group_2'@'%'"
sudo mysql -u root -proot -e "FLUSH PRIVILEGES;"
```
