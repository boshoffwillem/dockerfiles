# How to use

There are two ways of spinning up the container:

- Clean container with no existing data
- Import existing data into a container

Three environment variables are needed when creating the container.

```txt
ACCEPT_EULA=Y
SA_PASSWORD=<your_strong_password>
MSSQL_PID=<edition_name> (default: Developer)
```

- Developer: This will run the container using the Developer Edition (this is the default if no MSSQL_PID environment variable is supplied)
- Express: This will run the container using the Express Edition
- Standard: This will run the container using the Standard Edition
- Enterprise: This will run the container using the Enterprise Edition
- EnterpriseCore: This will run the container using the Enterprise edition Core

## 1. Spinning up a clean container with no existing data

```bash
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<password>" -e "MSSQL_PID=Developer" -p <port>:1433 boshoffwillem/sql-server:latest
```

You'll probably want to persist the SQL data of the container in case the container crashes. Just start the container and bind the data to a volume (docker will create the volume if it doesn't exist).

```bash
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<password>" -e "MSSQL_PID=Developer" -p <port>:1433 -v <volume-name>:/var/opt/mssql boshoffwillem/sql-server:latest
```

## 2. If you have existing SQL data

If you want to import existing SQL data you have into the container you're going to spin up follow these steps:

### 2.1 Create backup files of your existing SQL data

At the least, you need the .bak file of each DB you want to import into the container.

### 2.2 Create the container

```bash
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<password>" -e "MSSQL_PID=Developer" -p <port>:1433 -v <volume-name>:/var/opt/mssql boshoffwillem/sql-server:latest
```

### 2.3 Copy backup files into container

```bask
docker cp <db-backup>.bak <volume-name>:/var/opt/mssql/backup
```

### 2.4 Execute query to restore the backup files

You'll need to execute the following query to tell SQL Server to import the backup files. The easiest way to do it is to connect to the server with a DB program like Azure Data Studio. The connection string will be **hostname,port**

For example

localhost,11433

```sql
RESTORE FILELISTONLY 
FROM DISK = '/var/opt/mssql/backup/<db-backup>.bak'
```

### 2.5 Execute query to restore the DB

The following query will create a DB from the backup file.

```sql
RESTORE FILELISTONLY 
FROM DISK = '/var/opt/mssql/backup/<db-backup>.bak'

RESTORE DATABASE <DB_NAME>
FROM DISK = '/var/opt/mssql/backup/<db-backup>.bak'
WITH 
    MOVE '<DB_NAME>' TO '/var/opt/mssql/data/<DB_NAME>.mdf',
    MOVE '<DB_NAME>_Log' TO '/var/opt/mssql/data/<DB_NAME>.ldf'
```

## 3. Backup SQL data in the container

If you want to create backup files for the SQL data in your container, execute the following query.

```sql
BACKUP DATABASE [<DB_NAME>] 
TO DISK = N'/var/opt/mssql/backup/<db-backup>.bak' 
WITH NOFORMAT, 
NOINIT, 
NAME = '<DB_NAME>', SKIP, NOREWIND, NOUNLOAD, STATS = 10
```

### 3.1 Copy DB backup out of the container

```bash
docker cp <volume-name>:/var/opt/mssql/backup/<db-backup>.bak <db-backup>.bak
```
