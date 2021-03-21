---
title: Integrace Apache Kafka připojení k Azure Event Hubs pomocí Debezium pro zachycení změn dat
description: Tento článek poskytuje informace o tom, jak používat Debezium s Azure Event Hubs pro Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 01/06/2021
ms.openlocfilehash: 0ad1df23e71e652f7d380ffbabb542b81954e038
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935168"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-with-debezium-for-change-data-capture"></a>Integrace podpory Apache Kafka Connect do Azure Event Hubs s Debezium for Change Data Capture

Funkce **Change Data Capture (CDC)** je technika používaná ke sledování změn na úrovni řádků v databázových tabulkách v reakci na operace vytvoření, aktualizace a odstranění. [Debezium](https://debezium.io/) je distribuovaná platforma, která se sestavuje nad funkcemi funkce Change Data Capture, které jsou k dispozici v různých databázích (například [logické dekódování v PostgreSQL](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)). Poskytuje sadu [konektorů Kafka Connect](https://debezium.io/documentation/reference/1.2/connectors/index.html) , které klepnutím na změny na úrovni řádků v databázových tabulkách a převádí je do datových proudů událostí odesílaných do [Apache Kafka](https://kafka.apache.org/).

> [!WARNING]
> Použití architektury Apache Kafka Connect a také Debezium platforma a její konektory **nemají nárok na podporu produktů prostřednictvím Microsoft Azure**.
>
> Apache Kafka Connect předpokládá, že se má jeho dynamická konfigurace uchovávat v kompaktních tématech s jiným neomezeným uchováváním. Azure Event Hubs [neimplementuje komprimaci jako funkci zprostředkovatele](event-hubs-federation-overview.md#log-projections) a vždycky ukládá limit uchovávání dat na zachované události, od principu, kdy je Azure Event Hubs modul pro streamování událostí v reálném čase, a ne Dlouhodobá data nebo úložiště konfigurace.
>
> I když Apache Kafka projekt může být při semíchání těchto rolí pohodlnější, Azure se domnívá, že tyto informace jsou nejlépe spravované ve správné databázi nebo úložišti konfigurace.
>
> Mnoho scénářů Apache Kafka připojení bude funkční, ale tyto koncepční rozdíly mezi modely uchovávání Apache Kafka a Azure Event Hubs mohou způsobit, že některé konfigurace nebudou fungovat podle očekávání. 

V tomto kurzu se dozvíte, jak nastavit systém založený na Change Data Capture v Azure pomocí [azure Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (pro Kafka), [Azure DB pro PostgreSQL](../postgresql/overview.md) a Debezium. Použije [konektor Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) k streamování úprav databáze z PostgreSQL na Kafka témata v Azure Event Hubs

> [!NOTE]
> Tento článek obsahuje odkazy na seznam *povolených* termínů, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Nastavení a konfigurace Azure Database for PostgreSQL
> * Konfigurace a spuštění Kafka Connect s konektorem PostgreSQL Debezium
> * Change Data Capture testu
> * Volitelné Využití událostí změny dat pomocí `FileStreamSink` konektoru

## <a name="pre-requisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:

- Předplatné Azure. Pokud žádné nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).
- Linux/MacOS
- Vydaná verze Kafka (verze 1.1.1, Scala verze 2.11), která je k dispozici na webu [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Přečtěte si úvodní článek [Event Hubs pro Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md).

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
K odesílání do jakékoli služby Event Hubs a příjmu z ní se vyžaduje obor názvů služby Event Hubs. Pokyny k vytvoření oboru názvů a centra událostí najdete v tématu [vytvoření centra událostí](event-hubs-create.md) . Získejte plně kvalifikovaný název domény a připojovací řetězec služby Event Hubs pro pozdější použití. Pokyny najdete v tématu [Získání připojovacího řetězce služby Event Hubs](event-hubs-get-connection-string.md). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Nastavení a konfigurace Azure Database for PostgreSQL
[Azure Database for PostgreSQL](../postgresql/overview.md) je relační databázová služba založená na verzi komunity Open Source PostgreSQL Database Engine a je dostupná ve dvou možnostech nasazení: jeden server a Citus (). [Podle těchto pokynů](../postgresql/quickstart-create-server-database-portal.md) Vytvořte Azure Database for PostgreSQL server pomocí Azure Portal. 

## <a name="setup-and-run-kafka-connect"></a>Nastavení a spuštění Kafka Connect
Tato část se zabývá následujícími tématy:

- Instalace konektoru Debezium
- Konfigurace Kafka Connect pro Event Hubs
- Spuštění Kafka Connect cluster s konektorem Debezium

### <a name="download-and-setup-debezium-connector"></a>Stažení a nastavení konektoru Debezium
Pokud chcete tento konektor stáhnout a nastavit, postupujte prosím podle pokynů v [dokumentaci k Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) .

- Stáhněte si archiv modulu plug-in konektoru. Například pro stažení verze `1.2.0` konektoru použijte tento odkaz- https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Extrahujte soubory JAR a zkopírujte je do [modulu plug-in Kafka Connect. cesta](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurace připojení Kafka pro službu Event Hubs
Přesměrování propustnosti připojení Kafka z Kafka do služby Event Hubs vyžaduje minimální konfiguraci.  Následující ukázka souboru `connect-distributed.properties` znázorňuje, jak pro připojení nakonfigurovat ověřování a komunikaci s koncovým bodem Kafka ve službě Event Hubs:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Nahraďte `{YOUR.EVENTHUBS.CONNECTION.STRING}` připojovacím řetězcem pro váš Event Hubs obor názvů. Pokyny k získání připojovacího řetězce najdete v tématu [získání připojovacího řetězce Event Hubs](event-hubs-get-connection-string.md). Tady je příklad konfigurace: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>Spuštění připojení Kafka
V tomto kroku se místně spustí pracovní proces připojení Kafka v distribuovaném režimu a k zachování stavu clusteru s použije služba Event Hubs.

1. Místně uložte výše uvedený soubor `connect-distributed.properties`.  Nezapomeňte nahradit všechny hodnoty v závorkách.
2. Na svém počítači přejděte do umístění verze Kafka.
3. Spusťte příkaz `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` a počkejte, než se cluster spustí.

> [!NOTE]
> Kafka Connect používá rozhraní Kafka AdminClient API k automatickému vytváření témat s doporučenými konfiguracemi, včetně komprimace. Rychlou kontrolou oboru názvů na webu Azure Portal zjistíte, že se interní témata pracovního procesu připojení vytvořila automaticky.
>
> Interní témata Kafka Connect **musí používat komprimaci**.  Event Hubs tým není zodpovědný za opravu nesprávných konfigurací, pokud jsou témata interního připojení nesprávně nakonfigurovaná.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Konfigurace a spuštění zdrojového konektoru Debezium PostgreSQL

Vytvořte konfigurační soubor ( `pg-source-connector.json` ) pro PostgreSQL source konektor – nahraďte hodnoty podle vaší instance Azure PostgreSQL.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` atribut je logický název, který identifikuje a poskytuje obor názvů pro konkrétní PostgreSQL databázový server nebo cluster, který je monitorovaný. Podrobné informace najdete v [dokumentaci k Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name) .

K vytvoření instance konektoru použijte koncový bod Kafka Connect REST API:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Postup kontroly stavu konektoru:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Change Data Capture testu
Pokud chcete zobrazit Change Data Capture v akci, budete muset vytvořit, aktualizovat nebo odstranit záznamy v databázi Azure PostgreSQL.

Začněte připojením k databázi Azure PostgreSQL (níže uvedený příklad používá [psql](https://www.postgresql.org/docs/12/app-psql.html))

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Vytvoření tabulky a vložení záznamů**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

Spojnice by nyní měla reagovat na akci a odesílat události změny dat do Event Hubsho tématu s následujícím na, a `my-server.public.todos` za předpokladu, že máte `my-server` jako hodnotu pro `database.server.name` a `public.todos` je tabulka, jejíž změny sledujete (podle `table.whitelist` Konfigurace).

**Zjistit Event Hubs téma**

Pojďme introspect obsah tématu, abyste se ujistili, že všechno funguje podle očekávání. Následující příklad používá [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , ale můžete také [vytvořit příjemce pomocí kterékoli z možností uvedených tady](apache-kafka-developer-guide.md) .

Vytvořte soubor s názvem `kafkacat.conf` s následujícím obsahem:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Aktualizace `metadata.broker.list` a `sasl.password` atributy v `kafkacat.conf` rámci podle Event Hubs informací 

V jiném terminálu spusťte příjemce:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

V reakci na řádky, které jste právě přidali do tabulky, byste měli vidět datové části JSON, které představují události změny dat generované v PostgreSQL `todos` . Zde je fragment kódu datové části:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

Událost se skládá z `payload` spolu s jejím `schema` (vynecháno pro zkrácení). V `payload` části si všimněte, jak `"op": "c"` je reprezentovaná operace vytvoření () – znamená, že se jednalo o nově přidaný `"before": null` `INSERT` řádek, `after` poskytuje hodnoty pro sloupce na řádku, `source` poskytuje metadata instance PostgreSQL z místa, kde byla tato událost vyzvednuta atd.

Můžete to zkusit stejně jako operace Update nebo DELETE a introspect události změny dat. Například chcete-li aktualizovat stav úlohy pro `configure and install connector` (za předpokladu, že `id` je `3` ):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Volitelné Nainstalovat konektor FileStreamSink
Teď, když se všechny `todos` změny tabulky zaznamenávají v Event Hubs tématu, použijeme konektor FileStreamSink (který je ve výchozím nastavení k dispozici ve výchozím nastavení v Kafka Connect), aby se tyto události využily.

Vytvořit konfigurační soubor ( `file-sink-connector.json` ) pro konektor – nahraďte atribut na základě `file` vašeho systému souborů.

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Chcete-li vytvořit konektor a ověřit jeho stav:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Vkládat, aktualizovat a odstraňovat záznamy databáze a monitorovat záznamy v nakonfigurovaném výstupním souboru jímky:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Vyčištění
Připojení Kafka vytváří témata centra událostí, do kterých se ukládají konfigurace, posuny a stav a která se zachovají i po zrušení clusteru připojení. Pokud toto zachování nevyžadujete, doporučujeme tato témata odstranit. Můžete také chtít odstranit `my-server.public.todos` centrum událostí, které bylo vytvořeno během tohoto podrobného postupu.

## <a name="next-steps"></a>Další kroky

Další informace o Event Hubs pro Kafka najdete v následujících článcích:  

- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Připojení Akka Streams k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md)