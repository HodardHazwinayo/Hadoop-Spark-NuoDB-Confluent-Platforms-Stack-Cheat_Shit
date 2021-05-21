# Postgres Installation process in Centos7/8

## Prerequisite
You will need one (physical or virtual) machine with CentOS 7 or 8 minimal installed having sudo non-root user privileges. 
You must set correct timezone on your server before proceeding with the installation:

sudo timedatectl set-timezone Africa/Kigali

## Install EPEL Repository
	*For CentOS 7, type below command to install epel repository:
	sudo yum install epel-release
	*For CentOS 8, type below command to install epel repository:
	sudo dnf install epel-release
	sudo dnf config-manager --set-enabled PowerTools
## Install PostgreSQL
#### For CentOS 7, type below command to install PostgreSQL release 13:
	sudo yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
	sudo yum -y install postgresql13 postgresql13-server
#### For CentOS 8, type below command to install PostgreSQL release 13:
	sudo dnf install https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
	sudo dnf -y install yum-utils
	sudo yum-config-manager --enable pgdg13
	sudo dnf -qy module disable postgresql
	sudo dnf -y install postgresql13 postgresql13-server
## Initialize PostgreSQL Cluster
A PostgreSQL database cluster is a collection of databases that are managed by a single server instance. 
Creating a database cluster consists of creating the directories in which the database data will be placed, generating the shared catalog tables, and creating the template and postgres databases.

## Type below command to initialize PostgreSQL database cluster:
1. sudo /usr/bin/postgresql-13-setup initdb
2. Initializing database ... OK

## Type below command to start PostgreSQL server:
1. sudo systemctl start postgresql-13
2. sudo systemctl enable postgresql-13
3. sudo systemctl status postgresql-13

## Creating PostgreSQL Roles and Databases
#### Let's begin with the switching over to the postgres account:
sudo -i -u postgres
#### Type below to access a postgres prompt:
psql
#### This will log you into the PostgreSQL prompt
postgres=#
#### Type below to list default databases
postgres=# \list
#### Type below to logout from the postgres prompt:
postgres=# \q
#### Type below to exit from the postgres user shell
-bash-4.2$ exit
## Access PostgreSQL without Switching the Account
sudo -u postgres psql
#### Type below to logout from postgres prompt:
postgres=# \q
#### Creating or Removing a Role
1. sudo -u postgres createuser --interactive
2. Enter name of role to add: dboperator
3. Shall the new role be a superuser? (y/n) y
#### You can also interactively remove a role from the default database:




