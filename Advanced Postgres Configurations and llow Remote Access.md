# Advanced Postgres Configurations and llow Remote Access
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

#### Update Firewall Rules
sudo firewall-cmd --add-service=postgresql --permanent
sudo firewall-cmd --reload

#### Once the file is modified,kindly restart the pgsql service with the below command:
sudo systemctl restart postgresql-13
#### You can check the status of the service with the below command:
sudo systemctl status postgresql-13

## Tools Needed to access Remote PostgreSQL database
[DBeaver](https://dbeaver.io/download/)
[PgAdmin4](https://www.pgadmin.org/download/pgadmin-4-windows/) 
[DbVisualizer](https://www.dbvis.com/download/12.0)

## Troubleshoots Cheat shit
[Installing the PL/pgSQL Debugger Extension (pldbgapi) for pgAdmin 3/4 on PostgreSQL 9.4 plus and Linux](https://gist.github.com/rdrey/37bc41a2876b2be103768f5812d80048)
[Install pldebugger for postgres](https://centos.pkgs.org/7/postgresql-10-x86_64/pldebugger10-1.1-1.rhel7.x86_64.rpm.html)
1. Add PostgreSQL 10 repository as described on its homepage:
https://yum.postgresql.org/repopackages.php
2. Install pldebugger10 rpm package if postres is 10:
yum install pldebugger10
#### Or if postgres13 use the below command:
yum install pldebugger13
sudo systemctl restart postgresql
sudo systemctl restart postgresql-13