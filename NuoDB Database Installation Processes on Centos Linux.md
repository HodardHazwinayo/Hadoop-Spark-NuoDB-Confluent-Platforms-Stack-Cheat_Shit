# Nuodb installations steps.

## Ports required for NuoDB: 

firewall-cmd --zone=public --add-port=8888/tcp --permanent
firewall-cmd --zone=public --add-port=48004/tcp --permanent
firewall-cmd --zone=public --add-port=48005/tcp --permanent
firewall-cmd --zone=public --add-port=48006/tcp --permanent
firewall-cmd --zone=public --add-port=48007/tcp --permanent
firewall-cmd --zone=public --add-port=48008/tcp --permanent
firewall-cmd --zone=public --add-port=10003/tcp --permanent
firewall-cmd --reload

## Create the parent directories and permissions for later use:
mkdir -p /data/nuodb/archives/
mkdir -p /data/nuodb/backups/
mkdir -p /data/nuodb/spill/
mkdir -p /data/nuodb/journals/
mkdir -p /backup/nuodb/backups/


## Get recommended NuoDB version

curl https://download.nuohub.org/ce_releases/nuodb-ce-4.1.1.6.x86_64.rpm -o /data/nuodb/nuodb-ce-4.x.x.x.x86_64.rpm
yum install libncurses* -y
rpm --install /data/nuodb/nuodb-ce-4.x.x.x.x86_64.rpm


## Variables configuration for NuoDB on home directory permanent 
vi ~/.bashrc
export NUODB_HOME=/opt/nuodb
export PATH=$NUODB_HOME/bin:$PATH
source ~/.bashrc 

## update-crypto-policies --set LEGACY
vi /etc/nuodb/nuoadmin.conf
chown nuodb:nuodb /etc/nuodb/nuoadmin.conf

fallocate -l 10G /data/nuodb/balloon.img
fallocate -l 5G /NUO_jrnl/nuodb/balloon.img



## Update-crypto-policies --set LEGACY

vi /etc/nuodb/nuoadmin.conf
chown nuodb:nuodb /etc/nuodb/nuoadmin.conf

cat > /etc/security/limits.d/20-nofiles.conf << EOF
nuodb hard nofile 65536
nuodb soft nofile 65536
EOF

## Or check on the permenant directory to be edited
vi /usr/lib/systemd/system/nuoadmin.service
systemctl daemon-reload

systemctl enable nuoadmin
systemctl start nuoadmin




mkdir /etc/nuodb/keys
chown nuodb:nuodb /etc/nuodb/keys
cd /etc/nuodb/keys
export CA_PASSWORD='replacebyrealpassword'
export TLS_PASSWORD='replacebyrealpassword'

## Begin ---- Configure TLS on NuoDB but if you want to continue with the community skipp this following commands

nuocmd create keypair --keystore /etc/nuodb/keys/ca.p12 --store-password "$CA_PASSWORD" --dname "CN=ca,SERIALNUMBER=1" --validity 36500 --ca

nuocmd import certificate --keystore /etc/nuodb/keys/ca.p12 --store-password "$CA_PASSWORD" --truststore /etc/nuodb/keys/nuoadmin-truststore.p12 --truststore-password "$TLS_PASSWORD"

nuocmd show certificate --keystore /etc/nuodb/keys/ca.p12 --store-password "$CA_PASSWORD" --cert-only > /etc/nuodb/keys/ca.cert

nuocmd create keypair --keystore /etc/nuodb/keys/nuocmd.p12 --store-password "$TLS_PASSWORD" --validity 180 --dname "CN=nuocmd,SERIALNUMBER=1"

nuocmd import certificate --keystore /etc/nuodb/keys/nuocmd.p12 --store-password "$TLS_PASSWORD" --truststore /etc/nuodb/keys/nuoadmin-truststore.p12 --truststore-password "$TLS_PASSWORD"

nuocmd show certificate --keystore /etc/nuodb/keys/nuocmd.p12 --store-password "$TLS_PASSWORD" > /etc/nuodb/keys/nuocmd.pem

nuocmd create keypair --keystore /etc/nuodb/keys/nuoadmin.p12 --store-password "$TLS_PASSWORD" --dname "CN=*.bk.local,SERIALNUMBER=1" --sub-altnames "dns:pdcdessit1.bk.local" --validity 365 --ca

nuocmd sign certificate --keystore /etc/nuodb/keys/nuoadmin.p12 --store-password "$TLS_PASSWORD" --update --ca --ca-keystore /etc/nuodb/keys/ca.p12 --ca-store-password "$CA_PASSWORD"

"ssl": "true",
"keystore": "/etc/nuodb/keys/nuoadmin.p12",
"keystore-type": "PKCS12",
"keystore-password": "replacebyrealpassword",
"truststore": "/etc/nuodb/keys/nuoadmin-truststore.p12",
"truststore-type": "PKCS12",
"truststore-password": "replacebyrealpassword",

export NUOCMD_VERIFY_SERVER=/etc/nuodb/keys/ca.cert
export NUOCMD_CLIENT_KEY=/etc/nuodb/keys/nuocmd.pem
nuocmd --client-key /etc/nuodb/keys/nuocmd.pem --verify-server /etc/nuodb/keys/ca.cert show domain

## End ---- Configure TLS on NuoDB but if you want to continue with the community skipp this following commands

mkdir /data/nuodb/archives/DATABASE-NAME
mkdir /data/nuodb/backups/DATABASE-NAME
mkdir /data/nuodb/spill/DATABASE-NAME
mkdir /data/nuodb/journals/DATABASE-NAME
mkdir /backup/nuodb/backups/DATABASE-NAME

chown -R nuodb:nuodb /data/nuodb/
chown -R nuodb:nuodb /backup/nuodb/

mkdir /data/nuodb/backups/DATABASE-NAME/DATABASE-NAME-BACKUPSET

tar -xzf /backup/nuodb/backups/DATABASE-NAME/T24-MASTER.tar.gz --directory=/data/nuodb/backups/DATABASE-NAME/DATABASE-NAME-BACKUPSET

## Restore DB from archive-path
nuoarchive restore --restore-dir /data/nuodb/archives/DATABASE-NAME /data/nuodb/backups/DATABASE-NAME/DATABASE-NAME-BACKUPSET/*

mkdir /data/nuodb/backups/hotcopy-backup
tar -xzf /data/nuodb/backups/hotcopy-backup.tar.gz --directory=/data/nuodb/backups/hotcopy-backup

nuoarchive restore --restore-dir /data/nuodb/archives/T24-DEV /data/nuodb/backups/hotcopy-backup/*

chown -R nuodb:nuodb /data/nuodb/archives/T24-DEV

## First option to create archives and database
nuocmd create archive \
--db-name T24-DEV \
--server-id nuoadmin-0 \
--archive-path /data/nuodb/archives/DATABASE-NAME \
--restored

nuocmd create database \
--db-name DATABASE-NAME \
--dba-user dba \
--dba-password dba \
--default-options mem 12G
--te-server-ids nuoadmin-0

## Second option to create archives and database
nuocmd create archive --db-name DATABASE-NAME --server-id nuoadmin-0 --archive-path /data/nuodb/archives/DATABASE-NAME --journal-path /data/nuodb/journals/DATABASE-NAME --restored

nuocmd create database --db-name DATABASE-NAME --dba-user username --dba-password password --default-options verbose error,flush,warn,login-audit,security,hotcopy mem 8G max-lost-archives 1 ping-timeout 60 scratch-dir /data/nuodb/spill/DATABASE-NAME journal-hot-copy enable max-client-connections 65250

nuocmd start process --db-name DATABASE-NAME --server-id nuoadmin-0 --engine-type TE --options mem 8G

nuocmd start database --db-name DATABASE-NAME --te-server-ids nuoadmin-0

nuocmd create archive --db-name DATABASE-NAME --server-id nuoadmin-0 --archive-path /data/nuodb/archives/DATABASE-NAME --journal-path /NUO_jrnl/nuodb/journals/DATABASE-NAME 

chown -R nuodb:nuodb /data/nuodb/archives/DATABASE-NAME

nuocmd update database-options --db-name DATABASE-NAME --default-options verbose error,flush,warn,login-audit,security,hotcopy men 8G ping-timeout 60 journal-hot-copy enable --te-server-ids nuoadmin-0

nuocmd create database --db-name DATABASE-NAME --dba-user username --dba-password password --default-options verbose error,flush,warn,login-audit,security,hotcopy ping-timeout 60 journal-hot-copy enable --te-server-ids nuoadmin-0 max-client-connections 65536

## Additonal troubleshoots commands if the TE and SM are not running well.
nuocmd start process --db-name DATABASE-NAME --server-id nuoadmin-0 --engine-type TE 
nuocmd start process --db-name DATABASE-NAME --server-id nuoadmin-0 --engine-type TE --options mem 8G max-client-connections 65000
nuocmd start process --db-name DATABASE-NAME --engine-type TE --server-id nuoadmin-0 
nuocmd shutdown database --db-name DATABASE-NAME
nuocmd start database --db-name DATABASE-NAME --te-server-ids nuoadmin-0
nuocmd show database --db-name DATABASE-NAME --all-incarnations
nuocmd start process --engine-type TE --db-name DATABASE-NAME --server-id nuoadmin-0

nuosql DATABASE-NAME --user username --password password

rm -rf /var/opt/nuodb/raftlog
https://www.unixmen.com/setting-dns-server-centos-7/

nuosql DATABASE-NAME --user username --password password
nuocmd get processes
nuocmd update database --db-name DATABASE-NAME  --dba-user username --dba-password password --default-options 8G
 
 
vi /etc/nuodb/nuodb.lic
chown nuodb:nuodb /etc/nuodb/nuodb.lic
nuocmd set license --license-file /etc/nuodb/nuodb.lic

## How to create Schema

CREATE SCHEMA SCHEMA-NAME;

## If it failes that requesting you to add more SM storage and it's on one host, please use the following command below:
### Proposal solution : 
You have started the database with the setting max-lost-archives 1 but have only started one SM.
You would need at least 2.  If this is a single host dev environment o would suggest changing the setting to 0 and restarting

### Final Solution: 
nuocmd update database-options --database-name DATABASE-NAME --default-options max-lost-archives 0

