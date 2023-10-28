

# setup on ubuntu
```mongodb
sudo apt update
sudo apt install gnupg -y
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
sudo apt update
sudo apt install mongodb-org -y

cat /etc/mongod.conf

```



# connect to server
```mongodb
mongosh admin
```

# server status
```mongodb
db.serverStatus()
db.serverStatus().storageEngine.name
```

# dump data and restore
`mongodump` and `mongorestore` by default use ./dump directory
```mongodb
mongodump
mongodump -d login -c myData
mongorestore

mongodump --host mongodb.example.com --port 27017 -u <mongouser> --authenticationDatabase <authdb> --db rocketchat --out /opt/backups/mongodb/
```



# explain queries
```mongodb
db.myData.find({$and:[{x:{$in:[25,2,7,10]}}]}).explain()
```

# index
```mongodb
db.myData.createIndex({x:1})
db.myData.getIndexes()
```


# users
```mongodb
use admin
db.createUser({user: "moj1", pwd: "mojpass", roles: [{role: "read", db: "login"}]})
db.createUser({user: "root", pwd: "rootpass", roles:[{role: "root", db: "admin"}]})

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