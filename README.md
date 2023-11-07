# zabbix_googlechat
Installation of zabbix with integration of google_chat

- Install and configure Zabbix for your platform
- I have used Ubuntu 22.04

sudo wget https://repo.zabbix.com/zabbix/6.3/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.3-3%2Bubuntu22.04_all.deb                                                                                            
sudo dpkg -i zabbix-release_6.3-3+ubuntu22.04_all.deb

sudo apt update 

sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent

- Install sql server

sudo apt-get install mysql-server

sudo systemctl start mysql

- Create initial database

sudo mysql
create database zabbix character set utf8mb4 collate utf8mb4_bin;

create user zabbix@localhost identified by 'password';

grant all privileges on zabbix.* to zabbix@localhost;

set global log_bin_trust_function_creators = 1;

quit;

- On Zabbix server host import initial schema and data. You will be prompted to enter your newly created password.

sudo zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix

- Disable log_bin_trust_function_creators option after importing database schema.

sudo mysql
set global log_bin_trust_function_creators = 0;
quit;

- Configure the database for Zabbix server

Edit file /etc/zabbix/zabbix_server.conf and set the DB password with ->

sudo vim /etc/zabbix/zabbix_server.conf

- Start Zabbix server and agent processes

systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2

- Open Zabbix UI web page and proceed with web ui config (should be self explanatory)

The default URL for Zabbix UI when using Apache web server is http://host/zabbix



Integration google chats with zabbix

Create a workspace, go to space settings --> apps & integration --> add webhooks --> add name & save (URL would be created automatically)

Go to zabbix --> Administration --> Media types --> create media type(webhook)
add parameters like google_endpoint --> Paste URL of that google workspace
		    zabbix_url --> http://ip_of_server:8080/zabbix
set timeout 30s

