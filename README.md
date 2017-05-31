# Supported tags and respective `Dockerfile` links

-	[`latest` (*xtrabackup/Dockerfile*)](https://github.com/gleez/docker-images/blob/master/xtrabackup/Dockerfile)

# Percona Xtrabackup

Derived from the official Docker Debian Jessie image. The image contains Percona Xtrabackup installed and a simple bash script to run the backup command.

# How to use this image?

To run the backup, link it to the running MySQL container and ensure to map the following volumes correctly:

- MySQL datadir of the running MySQL container: /var/lib/mysql
- Backup destination: /backups

## Example

Suppose you have a MySQL container running named "mysql-server", started with this command:

```bash
$ docker run -d \
--name=mysql-server \
-v /storage/mysql-server/datadir:/var/lib/mysql \
-e MySQL_ROOT_PASSWORD=mypassword \
mysql
```

Then, to perform backup against the above container, the command would be:

```bash
$ docker run -it \
-v /storage/mysql-server/datadir:/var/lib/mysql \
-v /storage/backups:/backups \
--rm=true \
gleez/xtrabackup \
sh -c 'exec /xtrabackup.sh'
```

You should see Xtrabackup output on the screen. Ensure you get the “completed OK” line indicating the backup is successful:

```bash
...
innobackupex: Backup created in directory '/backups/2017-02-02_07-00-28'
170202 17:07:57  innobackupex: Connection to database server closed
170202 17:07:57  innobackupex: completed OK!
```

The container will then exit (the "run" is executed interactively) and automatically removed by Docker since we specified “--rm=true” in the command line. On the machine host, we can see the backups are there:

```bash
$ ls -1 /storage/backups/
2017-02-02_07-00-28
2017-01-17_13-07-28
2017-01-17_14-02-50
```

