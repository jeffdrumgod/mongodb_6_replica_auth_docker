## Mongodb replicaset deployment with SCRAM authentication and keyfile authentication

Replace `mongodbreplicasetauth/.docker/mongodb/sample.file.key` with your own keyfile
This is used by mongodb replicaset members to autheticate themselves using self signed certs. Also this file should only have read access by mongod group
```bash
openssl rand -base64 700 > file.key
chmod 400 file.key  # read access
sudo chown 999:999 file.key
```

`Note: 999 → System users. These are users that do not map to actual “human” users, but are used as security identities for system daemons, to implement privilege separation and run system daemons with minimal privileges.`

### Add an entry to `/etc/hosts` of your home machine for mongodb to resolve connections to remote host
![Alt text](./mongodbreplicasetauth/screenshots/Screenshot 2022-06-10 at 6.12.36 AM.png?raw=true "/etc/hosts entry")

example
150.230.52.139  mongo1 mongo2 mongo3

Mongorestore
```bash
mongorestore --host rs0/mongo2:30002,mongo1:30001,mongo3:30003 --db penny_stc_test dump/penny_stc_test -u <dbusername> -p <dbusernamepassword>
```
where rs0 is the replicaset name we used, ** Make sure to use the db credentials and not the admin **

### Remote machine
![Alt text](./mongodbreplicasetauth/screenshots/Screenshot 2022-06-10 at 6.16.46 AM.png "Docker containers running on cloud instance")


### Connecting to remote machine
![Alt text](./mongodbreplicasetauth/screenshots/Screenshot 2022-06-10 at 6.18.38 AM.png "Connecting with a gui tool")

```bash
mongodb://<admin_username>:<admin_password>@mongo2:30002,mongo1:30001,mongo3:30003/<db_name>?replicaSet=rs0&authSource=admin
```
^^ Use admin credentials