# MongoDB Cluster Setup with Docker

This project sets up a **MongoDB Cluster** using **Docker Compose**, including:

- 3 Config Servers (for sharding metadata)
- 3 Shard Servers (for storing data)
- 1 MongoS Router (for query routing)

## Setting Up the Cluster

Run the following command to start the cluster in detached mode:

```bash
docker-compose up -d
```

## Configure the Replica Set

```bash
docker exec -it config1 mongosh --port 28017
```

```js
rs.initiate({
    _id: "configReplSet",
    configsvr: true,
    members: [
        { _id: 0, host: "config1:28017" },
        { _id: 1, host: "config2:28018" },
        { _id: 2, host: "config3:28019" }
    ]
    });
```

## Configure the Sharding

```bash
docker exec -it shard1 mongosh --port 27021
```

```js
rs.initiate({
    _id: "shardReplSet",
    members: [
        { _id: 0, host: "shard1:27021" },
        { _id: 1, host: "shard2:27022" },
        { _id: 2, host: "shard3:27023" }
    ]
    });
```

## Access the Mongos

```bash
docker exec -it mongos mongosh --port 27020
```

```js
sh.addShard("shardReplSet/shard1:27021,shard2:27022,shard3:27023");
```

```js
sh.status();
```

## Test the Cluster

Run the following commands in the mongos shell:

```js
use testDB;
```

```js
sh.enableSharding("testDB");
```

```js
sh.shardCollection("testDB.collection", { shardKey: 1 });
```

## License

This project is open-source and provided for educational purposes.
