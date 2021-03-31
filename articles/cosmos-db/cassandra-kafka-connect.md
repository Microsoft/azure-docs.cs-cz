---
title: Integrace Apache Kafka a Azure Cosmos DB rozhraní API Cassandra pomocí Kafka Connect
description: Naučte se, jak ingestovat data z Kafka do Azure Cosmos DB rozhraní API Cassandra pomocí konektoru Apache Kafka DataStax.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803625"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Ingestování dat z Apache Kafka do Azure Cosmos DB rozhraní API Cassandra pomocí Kafka Connect
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Stávající aplikace Cassandra můžou snadno pracovat s [Azure Cosmos DB rozhraní API Cassandra](cassandra-introduction.md) kvůli [kompatibilitě ovladačů CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). Tuto možnost můžete využít k integraci s platformami streamování, jako je [Apache Kafka](https://kafka.apache.org/) a přenos dat do Azure Cosmos DB.

Data v Apache Kafka (témata) jsou užitečná jenom v případě, že jsou využívány jinými aplikacemi nebo ingestují do jiných systémů. Je možné vytvořit řešení s využitím rozhraní API pro [Kafka nebo spotřebitele](https://kafka.apache.org/documentation/#api) [pomocí jazyka a klientské sady SDK podle vašeho výběru](https://cwiki.apache.org/confluence/display/KAFKA/Clients). Kafka Connect nabízí alternativní řešení. Je to platforma pro streamování dat mezi Apache Kafkami a dalšími systémy, a to škálovatelným a spolehlivým způsobem. Vzhledem k tomu, že Kafka Connect podporuje mimo konektory na police, které zahrnují Cassandra, nemusíte psát vlastní kód pro integraci Kafka s Azure Cosmos DB rozhraní API Cassandra. 

V tomto článku budeme používat open source [konektor DataStax Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html), který funguje na Kafka Connect Framework k ingestování záznamů z tématu Kafka do řádků jedné nebo více tabulek Cassandra. Příklad poskytuje opakovaně použitelná nastavení pomocí Docker Compose. To je poměrně pohodlné, protože umožňuje spustit všechny požadované součásti místně pomocí jediného příkazu. Mezi tyto komponenty patří Kafka, Zookeeper, Kafka Connect Worker a aplikace generátoru ukázkových dat.

Tady je rozpis komponent a jejich definic služeb – můžete se podívat na kompletní `docker-compose` soubor [v úložišti GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka a Zookeeper používají obrázky [debezium](https://hub.docker.com/r/debezium/kafka/) .
- Aby bylo možné spustit jako kontejner Docker, konektor DataStax Apache Kafka je vloženými nad existujícím rozhraním Docker image – [debezium/Connect-Base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Tato bitová kopie zahrnuje instalaci Kafka a jeho knihoven Kafka Connect, takže by bylo vhodné přidat vlastní konektory. Můžete se podívat na [souboru Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- `data-generator`Data o vygenerování semen služby (JSON) se náhodně generují do `weather-data` tématu Kafka. Můžete odkazovat na kód a `Dockerfile` v [úložišti GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/) .

## <a name="prerequisites"></a>Požadavky

* [Zřídit účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Použít cqlsh nebo hostované prostředí pro ověření](cassandra-support.md#hosted-cql-shell-preview)

* Instalace [Docker](https://docs.docker.com/get-docker/) a [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Vytvoření prostoru klíčů, tabulek a spuštění kanálu integrace

Pomocí Azure Portal vytvořte prostor Cassandra a tabulky požadované pro ukázkovou aplikaci.

> [!NOTE]
> Použijte stejný prostor pro názvy klíčů a tabulek, jak je uvedeno níže.

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Naklonujte úložiště GitHub:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Spusťte všechny služby:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Stažení a spuštění kontejnerů může chvíli trvat: Jedná se pouze o jednorázový proces.

Potvrzení, zda byly všechny kontejnery spuštěny:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

Aplikace generátor dat spustí pumpu dat do `weather-data` tématu v Kafka. Můžete také provést rychlou správnosti kontrolu. Prohlédněte si kontejner Docker, na kterém běží Kafka Connect Worker:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Po přetažení do prostředí kontejneru stačí spustit obvyklý proces příjemce konzoly Kafka a ve službě by se měla zobrazit data o počasí (ve formátu JSON).

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Nastavení konektoru jímky Cassandra

Zkopírujte obsah JSON níže do souboru (můžete ho pojmenovat `cassandra-sink-config.json` ). Budete ho muset aktualizovat podle vašeho nastavení. zbývající část této části vám poskytne pokyny pro toto téma.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Tady je souhrn atributů:

**Základní konektivita**

- `contactPoints`: Zadejte kontaktní bod pro Cosmos DB Cassandra
- `loadBalancing.localDc`: zadejte oblast pro Cosmos DB účet, např. jihovýchodní Asie
- `auth.username`: zadejte uživatelské jméno.
- `auth.password`: zadejte heslo.
- `port`: zadejte hodnotu portu (to znamená `10350` , ne `9042` . ponechat tak, jak je.)

**Konfigurace protokolu SSL**

Azure Cosmos DB vynutila [zabezpečené připojení přes SSL](database-security.md) a konektor Kafka Connect podporuje také SSL.

- `ssl.keystore.path`: cesta k JDK úložiště klíčů v kontejneru – `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: Heslo JDK úložiště klíčů (výchozí)
- `ssl.hostnameValidation`: Zapíná se vlastní ověření názvu hostitele uzlu.
- `ssl.provider`: `JDK` používá se jako poskytovatel SSL.

**Obecné parametry**

- `key.converter`: Používáme převaděč řetězců. `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: vzhledem k tomu, že data v Kafka témata jsou JSON, používáme `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Vzhledem k tomu, že k naší datové části JSON nejsou přidružené žádné schéma (pro účely ukázkové aplikace), musíme pokyn Kafka připojit, aby nevypadal na schématu nastavením tohoto atributu na `false` . V důsledku toho nedojde k selhání.

### <a name="install-the-connector"></a>Instalace konektoru

Nainstalujte konektor pomocí koncového bodu REST služby Kafka Connect:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Chcete-li zjistit stav:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Pokud vše napadne správně, konektor by měl začít tkaní jeho Magic. Mělo by se ověřit pro Azure Cosmos DB a zahájení ingestování dat z tématu Kafka ( `weather-data` ) do tabulek Cassandra – `weather.data_by_state` a `weather.data_by_station`

Nyní můžete zadávat dotazy na data v tabulkách. Přejděte na Azure Portal, zaveďte hostované prostředí CQL pro váš účet Azure Cosmos DB.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Otevřít CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Dotazování dat z Azure Cosmos DB

Ověřte `data_by_state` tabulky a `data_by_station` . Tady je několik ukázkových dotazů, které vám pomohou začít:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md) 
* [Doporučené postupy pro klíč oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad ru/s pomocí článků Azure Cosmos DB Capacity Planneru](estimate-ru-with-capacity-planner.md)
