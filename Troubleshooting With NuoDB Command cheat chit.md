# Troubleshooting With NuoDB Command

## Get diagnose-info
### nuocmd get diagnose-info collects admin logs, admin configuration, system binaries, and cores in a single zip file.

nuocmd get diagnose-info --output-dir /tmp/nuodiag --include-cores

## Get server-logs
### nuocmd get server-logs creates a zip file containing the Admin Service log files in the location specified.

nuocmd get server-logs --output /tmp

## Get log-messages
### nuocmd get log-messages streams log messages for the specified process, database, or domain.

nuocmd get log-messages --log-options msgs

## Get core-file
### nuocmd get core-file downloads the core file for a running database process.

nuocmd get core-file --start-id 1 --output-dir /tmp/

## Get database-connectivity
### nuocmd get database-connectivity provides connectivity information for the specified database in JSON format.

nuocmd get database-connectivity --db-name test

## show database-connectivity
### nuocmd show database-connectivity displays a connectivity graph of the specified database.

nuocmd show database-connectivity --db-name test --with-node-ids

[Reference](https://doc.nuodb.com/nuodb/latest/troubleshooting/troubleshooting-with-nuodb-command/).


# Variables configuration for NuoDB on home directory permanent 
vi ~/.bashrc
export NUODB_HOME=/opt/nuodb
export PATH=$NUODB_HOME/bin:$PATH
source ~/.bashrc 

# update-crypto-policies --set LEGACY
vi /etc/nuodb/nuoadmin.conf
chown nuodb:nuodb /etc/nuodb/nuoadmin.conf

# Setting up the limits on both hard and soft files
cat > /etc/security/limits.d/20-nofiles.conf << EOF
nuodb hard nofile 65536
nuodb soft nofile 65536
EOF

# Edit the file
vi /usr/lib/systemd/system/nuoadmin.service

systemctl daemon-reload

[Extending the Database Across Multiple Hosts (Scaling Out)]
(https://doc.nuodb.com/nuodb/latest/deployment-models/physical-or-vmware-environments-with-nuodb-admin/database-operations/extending-the-database-across-a-second-host-scaling-out/)

# Managing TLS Security

## Checking the State of TLS Support
NUODB_HOME/etc/nuoadmin tls status

## Enabling TLS Support
$NUODB_HOME/etc/nuoadmin tls enable

# Displaying Domain, Database, and Archive Status
nuocmd show domain
nuocmd show database --db-name <DB_NAME>
nuocmd show archives

# Displaying Durable Domain Configuration Information
nuocmd get server-config --server-id nuoadmin-1

# Displaying Database Process Information
nuocmd show database --db-name <name> ++[++--all-incarnations++]++
nuocmd show database --db-name test
nuocmd show database --db-name test --all-incarnations

# Performing Health Checks on Domain and Database Status
nuocmd check database --db-name <database name>
nuocmd check servers
nuocmd check process --start-id <start_id>

## Example 1: Using the --check-running subcommand without timing out
### To check whether a database named test is running, and wait until it is running, specify the following:
nuocmd check database --db-name test --check-running --wait-forever

## Example 2: Using the --check-running subcommand with the timeout option
### To check, for a period of 10 seconds before timing out, whether a database named test is running, specify the following:
nuocmd check database --db-name test --check-running --timeout 10

## Example 3: Using the --num-processes subcommand with the timeout option
### To check for a period of 10 seconds before timing out, whether a database named test has 
### four processes ( say two TE processes and two SM processes), specify the following:
nuocmd check database --db-name test --num-processes 4 --timeout 10

## Example 4: Using the --check-running subcommand without timing out
### To check whether a database process with a specific start ID is running, and 
### Wait until it is running, specify the following:
nuocmd check process --start-id 1 --check-running --wait-forever

## Example 5: Using the --check-exited subcommand without timing out
### A shutdown process command was issued on start ID 1. To check whether the database process with 
### start ID 1 has exited, and wait until it has, specify the following:
nuocmd check process --start-id 1 --check-exited --wait-forever

## Example 6: Using the nuocmd check command in a Bash if statement
### Check if database test is running and has three database processes.
if nuocmd check database --db-name test --check-running --num-processes 3; then
    echo "Database test is RUNNING with 3 processes"
fi

