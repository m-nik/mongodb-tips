

# setup on ubuntu
```sh
sudo apt update
sudo apt install gnupg -y
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
sudo apt update
sudo apt install mongodb-org -y

cat /etc/mongod.conf


# Install from urls
```sh
wget https://repo.mongodb.org/apt/ubuntu/dists/noble/mongodb-org/8.2/multiverse/binary-amd64/mongodb-org-server_8.2.2_amd64.deb
wget https://downloads.mongodb.com/compass/mongodb-mongosh_2.5.10_amd64.deb
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-ubuntu2404-x86_64-100.13.0.deb
sudo apt install ./*.deb
```



# connect to server
```js
mongosh admin
```

# server status
```js
db.serverStatus()
db.serverStatus().storageEngine.name
```

# dump data and restore
`mongodump` and `mongorestore` by default use ./dump directory
```js
mongodump
mongodump -d login -c myData
mongorestore

mongodump --host mongodb.example.com --port 27017 -u <mongouser> --authenticationDatabase <authdb> --db rocketchat --out /opt/backups/mongodb/
```



# explain queries
```js
db.myData.find({$and:[{x:{$in:[25,2,7,10]}}]}).explain()
```

# index
```js
db.myData.createIndex({x:1})
db.myData.getIndexes()
```


# users
```js
use admin

show users
db.getUsers()
db.dropAllUsers()
db.dropUser("username")


db.createUser({user: "moj1", pwd: "mojpass", roles: [{role: "read", db: "login"}]})
db.createUser({user: "root", pwd: "rootpass", roles:[{role: "root", db: "admin"}]})

// full access user
db.createUser({
  user: "admin",
  pwd: "StrongPasswordHere",
  roles: [
    { role: "userAdminAnyDatabase", db: "admin" },
    { role: "dbAdminAnyDatabase", db: "admin" },
    { role: "readWriteAnyDatabase", db: "admin" }
  ]
})

// readonly user on some dbs
db.createUser({
  user: "user-svc",
  pwd: "StrongPasswordHere",
  roles: [
    { role: "read", db: "db1" },
    { role: "read", db: "db2" }
  ]
})

// roles
db.createRole({ role: "myReadOnlyRole", privileges: [{ resource: { db: "mytest", collection: "col2"}, actions: ["find"]}], roles: []})
// grant role to existing user
db.grantRolesToUser("anotheruser", [ { role: "myReadOnlyRole", db: "mytest" } ])
// create user and grant role 
db.createUser({ user: "reader", pwd: "secretpass", roles: [{ role: "myReadOnlyRole", db: "mytest"}]})
// assign permissions to roles
db.grantPrivilegesToRole("myReadOnlyRole", [{ resource: { db : "mytest", collection : "col1"}, actions : ["find"] }])
// get user roles
db.getRole("myReadOnlyRole", { showPrivileges : true })


```

/etc/mongod.conf
```yml
security:
  authorization: enabled
```
```sh
mongod 
```




# dockerized mongo
```sh
docker run -v /my/custom:/etc/mongo -d mongo --config /etc/mongo/mongod.conf
```

```sh
docker run -v /my/custom:/etc/mongo -d \
-e MONGO_INITDB_ROOT_USERNAME=mongoadmin \
-e MONGO_INITDB_ROOT_PASSWORD=secret \
mongo --auth
```
