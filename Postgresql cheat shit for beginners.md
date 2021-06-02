# Postgresql cheat shit for beginners

## Moving data from one server to another 
1. Take the backup of the database either all or just db itself.
**pg_dumpall -d databasename -f filename.sql**
**pg_dump -d databasename -f filename.sql**
*Eg: pg_dump -d testdb -f testdb.sql*

2. Lets move the data to destination server
**scp filename.sql username@hostIP:directory
*Eg: scp testdb.sql root@10.24.12.138:~/testdb.sql*

3. Lets login to the server
**ssh root@10.24.12.138**
4. Run sql against database
**psql testdb < testdb.sql**
5. Login to the database called testdb as we're using it as example.
**psql testdb**
6. Describe tables of the database
**\dt**
