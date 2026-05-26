# OnlineMigration

#### Introduction
OnlineMigration is an online migration tool that uses [Debezium](https://debezium.io/). It currently supports migration from Oracle to openGauss only.

#### Compilation
```
mvn compile
```

#### Packaging
```
mvn package
```

The generated JAR package is stored in **target/**.

#### Preparations
For details about how to configure Oracle and start Kafka, see [PREPARATION](instruction/PREPARATION.md) in the `instruction` directory.

#### How to Run
Parameters:
```
--write-scn: write scn captured in debezium snapshot mode.
--schema: the schema want to migrate
--from-beginning: consume the messages from beginning.
--consumer-file-path: the application property file
--help: print the help message
```

Read the SCN during snapshot creation and write it to the `scn.txt` file.
```shell
java -jar OnlineMigration-1.0-SNAPSHOT.jar --write-scn
```

Consume messages from the start of the topic.
```shell
java -jar OnlineMigration-1.0-SNAPSHOT.jar --schema schema_name --from-beginning
```

Resume consumption from the last consumed topic offset.
```shell
java -jar OnlineMigration-1.0-SNAPSHOT.jar --schema schema_name
```

Use intelligent object name conversion:
```shell
java -jar OnlineMigration-1.0-SNAPSHOT.jar --smartConversionOfObjectNames
```
Add `INDEX_NAME_PREFIX` to avoid `INDEX_NAME` matching `TABLE_NAME` during index migration.
```shell
java -jar OnlineMigration-1.0-SNAPSHOT.jar --indexPrefix idx_
```

After intelligent object escaping is enabled, onlineMigration processes object names as follows:

| oracle   | openGauss |
|----------|-----------|
| Object_a | Object_a  |
| OBJECT_A | object_a  |
| object_a | object_a  |

Modify parameters in the `resources/consummer_setting.properties` file and recompile the file, or create a new properties file and pass it to the program as a command line parameter.
```shell
cp src/main/resources/consumer_setting.properties ./my_consumer_setting.properties
# Modify parameters in the `my_consumer_setting.properties` file and start the tool.
java -jar OnlineMigration-1.0-SNAPSHOT.jar --schema schema_name --from-beginning --consumer-file-path my_consumer_setting.properties
```

#### How to End
Press `Ctrl+C` to stop the tool.
