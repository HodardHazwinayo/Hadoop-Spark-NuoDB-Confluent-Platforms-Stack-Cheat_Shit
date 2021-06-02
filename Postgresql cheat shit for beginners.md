# Postgresql cheat shit for beginners

## Moving data from one server to another 
1. Take the backup of the database either all or just db itself.
	1. **pg_dumpall -d databasename -f filename.sql**
	2. **pg_dump -d databasename -f filename.sql**
	3. **Eg: pg_dump -d testdb -f testdb.sql**

2. Lets move the data to destination server
	1. **scp filename.sql username@hostIP:directory**
	2. **Eg: scp testdb.sql root@10.24.12.138:~/testdb.sql**

3. Lets login to the server
**ssh root@10.24.12.138**
4. Run sql against database
**psql testdb < testdb.sql**
5. Login to the database called testdb as we're using it as example.
**psql testdb**
6. Describe tables of the database
**\dt**
## A simple backup of the database
1. Check the file if exist and how big is 
**ls -la**
2. Lets take the backup and zip it for compressing the size.
**pg_dump -d testdb | gzip > back.gz**
3. Second option to take backup is to format a file in custom mode
**pg_dump -Fc testdb > back.bk**
4. How to restore it accordingly 
**pg_restore -d testdb back.bk**
## How to setup a backup using a simple cron jobs,
1. Make backup directory where you want to store a certain period of backups
**mkdir backup**
2. On Linux, there is built-in option to schedule a jobs
**crontab -e**
3. Take daily backup at mid night, please check on the following scripts and use yours accordingly
![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/readme.png)
	1. **# m h dom mon dow  command**
	2. **0 0 * * * root pg_dump -Fc testdb > backup/back.bk** 
4. Automated Backup on Linux 
[Automated Backup on Linux Reference link for more details to customize it](https://wiki.postgresql.org/wiki/Automated_Backup_on_Linux)
	1. crontab scripts
	![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/crontab.png)
	2. backup.sh scripts
	![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/backupshellscript.png)
	3. How to add backup.sh scripts under terminal
	![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/addbackupscript.png)
	4. Run the following commands **vi /backup/backup.sh** and **source /backup/backup.sh** 
	![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/scripts.png)
	5. To test if the backup is going well
	![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/backuptest.png)
	6. Edit crontab for taking daily and weekly backup on sunday at mid night: dow means day of week.
	![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/crontabfordailyandweekly.png)
	7. References for the backup scripts in details
		1. [Automatic cron backup of PostgreSQL database](https://txcowboycoder.wordpress.com/2011/06/03/automatic-cron-backup-of-postgresql-database/)
		2. [Automated Backup on Linux](https://wiki.postgresql.org/wiki/Automated_Backup_on_Linux)
		
## Understanding your server
### Using pgbench
1. Create pgbench db and initiaze it using the followings commands:
	1. **createdb bench**
	2. **pgbench -i bench**
	3. ![]()
	4. To run pgbench database, lets use our usual example:
		1. **pgbench bench**
		2. Initialize the number of scale factor of approximately 10 thousands users or connections for only reading.
			1. **pgbench -i -S 100 bench**
			2. **pgbench -T 120 -j 2 -c 20 -S bench**
			3. **pgbench -j 4 -T 120  -c 20 bench**
2. How to display config file and maximum connections etc ... of postgresql on Linux
	1. **psql dbname -c "show config_file"**
	2. **psql testdb -c "show config_file"**
	3. **psql testdb -c "show max_connections"**
	4. **psql testdb -c "show shared_buffers"**
	
## Configuration Tweaks 
### Configuration calculator for PostgreSQL
1. [Parameters of your system](https://pgtune.leopard.in.ua/#/)
2. edit the following path where postgresql.conf is
		**vi /var/lib/pgsql/13/data/postgresql.conf**
3. Add the customized postgresql.local.conf with the following screenshot.
		![](https://github.com/HodardHazwinayo/Hadoop-Spark-NuoDB-Confluent-Platforms-Stack-Cheat_Shit/blob/master/Images/postgresqllocalconf.png)
4. To add customized postgresql.local.conf, we have to open the file and paste specifics configs from pgtune URL:
		**vi /var/lib/pgsql/13/data/postgresql.local.conf**
5. **pgbench -j 4 -T 120  -c 20 bench**
6. **systemctl restart postgresql-13**
	
### Installing pg_stat_statements
1. The most useful Postgres extension: pg_stat_statements
1. **The module must be loaded by adding pg_stat_statements to shared_preload_libraries in postgresql.conf**
2. **psql databasename**
3. **CREATE EXTENSION pg_stat_statements;**
4. ![]()
5. **systemctl restart postgresql-13**
6. **SELECT * FROM pg_stat_statements;**
7. **SHOW config_file;**





