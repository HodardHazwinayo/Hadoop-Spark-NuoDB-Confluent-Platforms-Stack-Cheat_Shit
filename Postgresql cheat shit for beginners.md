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
	![]()
	6. Edit crontab for taking daily and weekly backup on sunday at mid night: dow means day of week.
	![]()



