# Advanced Postgres Configurations and allow Remote Access
By default PostgreSQL access is bound to localhost only. 
To make the PostgreSQL accept outside connection, you will need to make following changes in the configuration files:
1. sudo vi /var/lib/pgsql/13/data/postgresql.conf
#### Locate, uncomment port number and change listen_addresses = 'localhost' to
2. listen_address ='*'
3. port = 5432
#### Save and close the editor when you are finished. 
#### Next, edit pg_hba.conf file to make the changes:
3. sudo vi /var/lib/pgsql/13/data/pg_hba.conf
#### Accept from anywhere 
4. host    all             all             0.0.0.0/0               md5
#### You can also limit PostgreSQL to accept connection only from specified IPs:
5. host    all             all             your_client_machine1_ip/32               md5
6. host    all             all             your_client_machine2_ip/32               md5
7. host    all             all             your_client_machine3_ip/32               md5

#### Or Accept from trusted subnet
1. host all all x.x.0.0/32 md5 [Less number of connected machines](#)
2. host all all x.x.0.0/24 md5 [Medium number of connected machines](#)
3. host all all x.x.0.0/16 md5 [Recommended and High number of connected machines](#)


#### Update Firewall Rules
sudo firewall-cmd --add-service=postgresql --permanent
sudo firewall-cmd --reload

#### Once the file is modified,kindly restart the pgsql service with the below command:
sudo systemctl restart postgresql-13
#### You can check the status of the service with the below command:
sudo systemctl status postgresql-13

## Tools Needed to access Remote PostgreSQL database
1. [DBeaver](https://dbeaver.io/download/)
2. [PgAdmin4](https://www.pgadmin.org/download/pgadmin-4-windows/) 
3. [DbVisualizer](https://www.dbvis.com/download/12.0)

## Troubleshoots Cheat shit
1. [Installing the PL/pgSQL Debugger Extension pldbgapi for pgAdmin 3/4 on PostgreSQL 9.4 plus and Linux](https://gist.github.com/rdrey/37bc41a2876b2be103768f5812d80048)
2. [Install pldebugger for postgres](https://centos.pkgs.org/7/postgresql-10-x86_64/pldebugger10-1.1-1.rhel7.x86_64.rpm.html)
3. Add PostgreSQL 10 repository as described on its homepage:
https://yum.postgresql.org/repopackages.php
4. Install pldebugger10 rpm package if postgres is 10:
yum install pldebugger10
#### Or if postgres13 use the below command:
1. yum install pldebugger13
2. sudo systemctl restart postgresql
3. sudo systemctl restart postgresql-13
## To finish pldebugger configuration on db level but not recommended on Prod env, you should follow: 
1. Once its done we need to restart the server; then only it will imply the changes and its mandatory
2. After that we have to execute a command on database to create the plugin for the db this command: 
##### CREATE EXTENSION pldbgapi;
### Either we can do the above script from pgadmin or from backend itself
3. sudo systemctl restart postgresql-13
### How to change default port of postgresq-9.2.x or postgresql-13.x
#### A. lets start with postgresq-9.2.x
1. To check the listening port of postgresql **netstat -tulpn | grep LISTEN**
2. Open the desired port under firewall-cmd and add the service of postgres on firewall-cmd
	1. **systemctl start firewalld** and **systemctl status firewalld**
	2. **firewall-cmd --zone=public --add-port=portnumber/tcp --permanent**
	3. **firewall-cmd --zone=public --add-service=postgres --permanent**
	4. **firewall-cmd --reload**
3. Uncomment and change the port number under this path: **vi /var/lib/pgsql/data/postgresql.conf**
	1. **listen_address ='*'**
	2. **port = desiredportnumber**
4. Change environment running assigned default port: **vi /lib/systemd/system/postgresql.service**
 [# Port number for server to listen on] **Environment=PGPORT=portnumber**
5. Reload the systemd by the following cmd: **systemctl daemon-reload**
6. Then restart the postgresql using: 
	1. **systemctl restart postgresql** 
	2. **systemctl status postgresql**

#### B. lets continue with postgresq-13.x
1. To check the listening port of postgresql **netstat -tulpn | grep LISTEN**
2. Open the desired port under firewall-cmd and add the service of postgres on firewall-cmd
	1. **systemctl start firewalld** and **systemctl status firewalld**
	2. **firewall-cmd --zone=public --add-port=portnumber/tcp --permanent**
	3. **firewall-cmd --zone=public --add-service=postgres --permanent**
	4. **firewall-cmd --reload**
3. Uncomment and change the port number under this path: **vi /var/lib/pgsql/13/data/postgresql.conf**
	1. **listen_address ='*'**
	2. **port = desiredportnumber**
4. Change environment running assigned default port: **vi /lib/systemd/system/postgresql-13.service**
 [# Port number for server to listen on, check if the following file is available, if yes then edit it according if not skip it.] **Environment=PGPORT=portnumber**
5. Reload the systemd by the following cmd: **systemctl daemon-reload**
6. Then restart the postgresql using: 
	1. **systemctl restart postgresql-13** 
	2. **systemctl status postgresql-13**

## Reference
[Need help to change postgresql port on CentOS 7](https://stackoverflow.com/questions/25148693/need-help-to-change-postgresql-port-on-centos-7/25152682)