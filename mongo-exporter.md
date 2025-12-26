# Install mongodb-exporter
https://github.com/percona/mongodb_exporter
```sh
wget https://github.com/percona/mongodb_exporter/releases/download/v0.47.2/mongodb_exporter-0.47.2.linux-64-bit.deb
apt install ./mongodb_exporter-0.47.2.linux-64-bit.deb
echo "MONGODB_URI=\"mongodb://exporter:password@127.0.0.1:27017/admin\"
OPTIONS=--collect-all" > vi /etc/default/mongodb_exporter

sudo systemctl start --now mongodb_exporter.service 
systemctl status mongodb_exporter.service
```

# Create user
```mongodb
use admin
db.createUser({
  user: "exporter",
  pwd: "password",
  roles: [
    { role: "clusterMonitor", db: "admin" },
    { role: "read", db: "local" }
  ]
})
```

# Test
```
mongodb_exporter --mongodb.uri="mongodb://exporter:password@127.0.0.1:27017/admin" --web.listen-address="0.0.0.0:9216" &
curl 127.0.0.1:9216/metrics
```
