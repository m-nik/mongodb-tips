# Install mongodb-exporter
https://github.com/percona/mongodb_exporter
```sh
wget https://github.com/percona/mongodb_exporter/releases/download/v0.47.2/mongodb_exporter-0.47.2.linux-amd64.tar.gz
tar xf mongodb_exporter-0.47.2.linux-amd64.tar.gz
mv mongodb_exporter-0.47.2.linux-amd64/mongodb_exporter /usr/local/bin/
mongodb_exporter --version
```

# Create user
```json
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

