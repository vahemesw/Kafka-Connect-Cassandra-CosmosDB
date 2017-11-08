# Kafka-Connect-CosmosDB
Quick start to use Cosmos DB Cassandra as source through Kafka connect

## pre-requisites
1. Cosmos DB account
2. Confluent setup
3. Data Mountaineer Stream Reactor

## How to use Cosmos DB Cassandra as Kafka Connect Source 

1. Setup Confluent and stream reactor . See http://docs.datamountaineer.com/en/latest/install.html#install for details.

2. Use cqlsh to create the table and insert data
To connect to Cosmos DB Cassandra, you can launch cqlsh with following command

```
cqlsh <Cassandra end point provided by Cosmos DB> <Cassandra port provided by Cosmos DB> --connect-timeout=600 --request-timeout=600 -u <COSMOS DB ACCOUNT NAME> -p <COSMOS DB ACCOUNT PASSWORD>
```

3. Create a keyspace, table and insert sample data as follows

```
create keyspace source with replication = {'class' : 'SimpleStrategy', 'replication_factor' : 1};
        
create table IF NOT EXISTS source.orders (id int, user text, product text, qty int, price float, PRIMARY KEY (id, user))
        WITH CLUSTERING ORDER BY (user asc);
        
INSERT INTO source.orders (id, user, product, qty, price) VALUES (1, 'user1', 'product1', 500, 77.2);
```

4. Create a kafka-topic which reads data from cassandra
```
kafka-topics --zookeeper 127.0.0.1:2181 --topic cassandra-source --partitions 1 --replication-factor 1 --create
```

5. You can start connect with the following command.
```
bin/connect-distributed etc/schema-registry/connect-avro-distributed.properties
```

6. To fetch data from the created source table to Kafka topic, modify the following properties in the Cassandra configuration file with the Cosmos DB account details.

```
"connect.cassandra.contact.points": "<Cassandra end point provided by Cosmos DB>",
"connect.cassandra.port": "<Cassandra port provided by Cosmos DB>",
"connect.cassandra.username": "<COSMOS DB ACCOUNT NAME>",
"connect.cassandra.password": "<COSMOS DB ACCOUNT PASSWORD>",
```

7. Once the Connect is up we use the kafka-connect-tools cli to post the distributed properties file for Cassandra.
Get the sample file from this repository
```
 java -jar kafka-connect-cli-1.0.4-all.jar create cassandra-source  < conf/cassandra-source-cosmosdb.properties
```

8. verify the data being fetched using the following command
```
 $CONFLUENT_HOME/bin/kafka-avro-console-consumer --zookeeper localhost:2181 --topic cassandra-source --from-beginning
```

## More information

- [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)
- [Data Mountaineer kafka-connect](http://docs.datamountaineer.com/en/latest/cassandra-source.html)
- [Confluent](https://www.confluent.io/blog/kafka-connect-cassandra-sink-the-perfect-match/)
