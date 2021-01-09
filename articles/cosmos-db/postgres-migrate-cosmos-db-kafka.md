---
title: Migrace dat z PostgreSQL do Azure Cosmos DB rozhraní API Cassandra účtu pomocí Apache Kafka
description: Naučte se, jak pomocí Kafka Connect synchronizovat data z PostgreSQL do Azure Cosmos DB rozhraní API Cassandra v reálném čase.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 24714b185b0f666770b306a7e80a97a3f8f868a3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052621"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migrace dat z PostgreSQL do Azure Cosmos DB rozhraní API Cassandra účtu pomocí Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v Azure Cosmos DB se stal skvělou volbou pro podniková zatížení, která běží na Apache Cassandra, a to z různých důvodů:

* **Významné úspory nákladů:** Náklady můžete ušetřit pomocí Azure Cosmos DB, což zahrnuje náklady na virtuální počítač, šířku pásma a všechny příslušné licence Oracle. Navíc nemusíte spravovat datové centra, servery, úložiště SSD, sítě a náklady na elektřinu.

* **Lepší škálovatelnost a dostupnost:** Eliminuje jednotlivé body selhání, lepší škálovatelnost a dostupnost vašich aplikací.

* **Žádná režie správy a monitorování:** Jako plně spravovaná cloudová služba Azure Cosmos DB odstraňuje režijní náklady na správu a monitorování nesčetných nastavení.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) je platforma pro streamování dat mezi [Apache Kafkami](https://kafka.apache.org/) a dalšími systémy, a to škálovatelným a spolehlivým způsobem. Podporuje několik z konektorů na police, což znamená, že nepotřebujete vlastní kód pro integraci externích systémů s Apache Kafka.

V tomto článku se dozvíte, jak používat kombinaci konektorů Kafka k nastavení datového kanálu pro průběžnou synchronizaci záznamů z relační databáze, jako je například [PostgreSQL](https://www.postgresql.org/) , do [Azure Cosmos DB rozhraní API Cassandra](cassandra-introduction.md).

## <a name="overview"></a>Přehled

Tady je podrobný přehled koncového toku, který je uvedený v tomto článku.

Data v tabulce PostgreSQL budou vložena do Apache Kafka pomocí [konektoru PostgreSQL Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), což je konektor Kafka Connect **source** Connector. Vložení, aktualizace nebo odstranění záznamů v tabulce PostgreSQL bude zachyceno jako `change data` události a odesláno do Kafkach témat. [Konektor DataStax Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (konektor **jímky** Kafka Connect) tvoří druhou část kanálu. Synchronizace událostí Change data z Kafka se synchronizuje s Azure Cosmos DB rozhraní API Cassandra tabulek.

> [!NOTE]
> Použití specifických funkcí konektoru DataStax Apache Kafka umožňuje odeslat data do několika tabulek. V tomto příkladu konektor pomůže zachovat záznamy změn dat změny ve dvou Cassandra tabulkách, které mohou podporovat různé požadavky na dotazy.

## <a name="prerequisites"></a>Požadavky

* [Zřídit účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account)
* [Použít cqlsh nebo hostované prostředí pro ověření](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 nebo novější
* [Docker](https://www.docker.com/) (volitelné)

## <a name="base-setup"></a>Základní nastavení

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Nastavte databázi PostgreSQL, pokud jste to ještě neudělali. 

Může se jednat o existující místní databázi nebo si ji můžete [Stáhnout a nainstalovat](https://www.postgresql.org/download/) na svém místním počítači. Je také možné použít [kontejner Docker](https://hub.docker.com/_/postgres).

Spuštění kontejneru:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Připojte se k instanci PostgreSQL pomocí [`psql`](https://www.postgresql.org/docs/current/app-psql.html) klienta:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Vytvořte tabulku, do které se mají ukládat informace o vzorovém pořadí:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Pomocí Azure Portal vytvořte prostor Cassandra a tabulky požadované pro ukázkovou aplikaci.

> [!NOTE]
> Použijte stejný prostor pro názvy klíčů a tabulek, jak je uvedeno níže.

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Instalační Apache Kafka 

Tento článek používá místní cluster, ale můžete zvolit jakoukoli jinou možnost. [Stáhněte si Kafka](https://kafka.apache.org/downloads), rozbalte ho, spusťte cluster Zookeeper a Kafka.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Instalační konektory

Nainstalujte konektor Debezium PostgreSQL a DataStax Apache Kafka. Stáhněte si archiv modulu plug-in konektoru Debezium PostgreSQL. Chcete-li například stáhnout 1.3.0 verze konektoru (nejnovější v době psaní), použijte [Tento odkaz](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz). Stáhněte si konektor DataStax Apache Kafka z [tohoto odkazu](https://downloads.datastax.com/#akc).

Rozbalte archivy konektorů a zkopírujte soubory JAR do [modulu plug-in Kafka Connect. cesta](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Podrobnosti najdete v dokumentaci k [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) a [DataStax](https://docs.datastax.com/en/kafka/doc/) .

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Konfigurace Kafka připojení a spuštění datového kanálu

### <a name="start-kafka-connect-cluster"></a>Spustit Kafka připojit cluster

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Spustit instanci konektoru PostgreSQL

Uložení konfigurace konektoru (JSON) do souboru jako příklad `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Spuštění instance konektoru PostgreSQL:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> K odstranění můžete použít: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Vložení dat

`orders_info`Tabulka obsahuje podrobnosti o objednávkách, jako je ID objednávky, ID zákazníka, město atd. Naplňte tabulku náhodnými daty pomocí níže uvedeného kódu SQL.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Do tabulky by měla být vložena 10 záznamů. Nezapomeňte aktualizovat počet záznamů `generate_series(1, 10)` níže podle vašich požadavků, aby bylo možné vkládat `100` záznamy, použijte `generate_series(1, 100)`

Pro potvrzení:

```bash
select * from retail.orders_info;
```

Podívejte se na události Change Data Capture v tématu Kafka.

> [!NOTE]
> Všimněte si, že název tématu je dle `myserver.retail.orders_info` [konvence konektoru](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names) .

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Měli byste vidět události změny dat ve formátu JSON.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Spustit DataStax Apache Kafka instance konektoru

Uložte konfiguraci konektoru (JSON) do souboru jako příklad `cassandra-sink-config.json` a aktualizujte vlastnosti podle vašeho prostředí.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Spuštění instance konektoru:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Konektor by měl reagovat na akci a koncového kanálu od PostgreSQL po Azure Cosmos DB bude funkční.

### <a name="query-azure-cosmos-db"></a>Dotazování na Azure Cosmos DB

Ověřte tabulky Cassandra v Azure Cosmos DB. Tady jsou některé z dotazů, které můžete vyzkoušet:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Do PostgreSQL můžete pokračovat v vkládání dalších dat a potvrzení, že jsou záznamy synchronizované Azure Cosmos DB.

## <a name="next-steps"></a>Další kroky

* [Integrace Apache Kafka a Azure Cosmos DB rozhraní API Cassandra pomocí Kafka Connect](cassandra-kafka-connect.md)
* [Integrace Apache Kafka připojení k Azure Event Hubs (Preview) pomocí Debezium pro zachycení změn dat](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Migrace dat ze Oracle do Azure Cosmos DB rozhraní API Cassandra pomocí Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md) 
* [Doporučené postupy pro klíč oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad ru/s pomocí článků Azure Cosmos DB Capacity Planneru](estimate-ru-with-capacity-planner.md)

