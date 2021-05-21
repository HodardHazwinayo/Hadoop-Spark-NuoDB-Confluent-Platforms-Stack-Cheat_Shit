# Advanced Postgres Configurations and llow Remote Access
By default PostgreSQL access is bound to localhost only. 
To make the PostgreSQL accept outside connection, you will need to make following changes in the configuration files:
sudo vi /var/lib/pgsql/13/data/postgresql.conf
#### Locate, uncomment and change listen_addresses = 'localhost' to
listen_address ='*'
#### Save and close the editor when you are finished. 
#### Next, edit pg_hba.conf file to make the changes:
sudo vi /var/lib/pgsql/13/data/pg_hba.conf
###### Accept from anywhere 
host    all             all             0.0.0.0/0               md5
###### You can also limit PostgreSQL to accept connection only from specified IPs:
host    all             all             your_client_machine1_ip/32               md5
host    all             all             your_client_machine2_ip/32               md5
host    all             all             your_client_machine3_ip/32               md5

