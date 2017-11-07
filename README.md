# Kafka-Connect-CosmosDB-Samples
The repository contains sample files to use Cosmos DB Cassandra through Kafka connect

We will be using the environment setup here https://github.com/Landoop/kafka-connectors-tests and configure the Source to use the Cosmos DB Cassandra using these samples.

# pre-requisites
1. Environment setup specified here - https://github.com/Landoop/kafka-connectors-tests
2. Cosmos DB account

# How to run the samples against Cosmos DB Cassandra

1. Replace cotote.yml contents in the kafka-connect-cassandra tests to one of the samples here to use Cosmos DB Cassandra with Kafka Connect.

To connect to Cosmos DB Cassandra, you will need to make the following changes

1. In create Cassandra Source Table and Data section, provide values for Cassandra endpoint,port, username and password.
This will be the account which hosts your Cassandra source table data.
```
docker run --rm --network=kafkaconnectcassandra_default -i landoop/cassandra
          cqlsh <Cassandra end point provided by Cosmos DB> <Cassandra port provided by Cosmos DB> --connect-timeout=600 --request-timeout=600 -u <COSMOS DB ACCOUNT NAME> -p <COSMOS DB ACCOUNT PASSWORD>
```

2. To fetch data from the created source table to Kafka topic, modify the cassandra-source-incr.properties to have the Cosmos DB account details.
Sample properties posted to cassandra-source-incr.properties file

```
{
          "name": "cassandra-source",
          "config": {
            "connector.class": "com.datamountaineer.streamreactor.connect.cassandra.source.CassandraSourceConnector",
            "tasks.max": "1",
            "connect.cassandra.key.space": "source",
            "connect.cassandra.source.kcql": "INSERT INTO cassandra-source SELECT * FROM orders PK creator",
            "connect.cassandra.kcql": "INSERT INTO cassandra-source SELECT * FROM orders PK creator",
            "connect.cassandra.import.mode": "incremental",
            "connect.cassandra.contact.points": "<Cassandra end point provided by Cosmos DB>",
            "connect.cassandra.port": "<Cassandra port provided by Cosmos DB>",
            "connect.cassandra.username": "<COSMOS DB ACCOUNT NAME>",
            "connect.cassandra.password": "<COSMOS DB ACCOUNT PASSWORD>",
            "connect.deprecated.fields.to.remove": "connect.cassandra.source.kcql"
}
```
