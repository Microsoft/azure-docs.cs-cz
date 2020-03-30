---
title: Operace DDL v rozhraní Azure Cosmos DB Cassandra API od Spark
description: Tento článek podrobně popisuje keyspace a tabulka DDL operace proti Azure Cosmos DB Cassandra API ze Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c0df05eff5dc84ef24e1ed5afcaf705d99f447ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622578"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operace DDL v rozhraní Azure Cosmos DB Cassandra API od Spark

Tento článek podrobně popisuje keyspace a tabulka DDL operace proti Azure Cosmos DB Cassandra API ze Spark.

## <a name="cassandra-api-related-configuration"></a>Konfigurace související s rozhraním CASSANDRA API 

```scala
import org.apache.spark.sql.cassandra._

//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="keyspace-ddl-operations"></a>Operace DDL klíčového prostoru

### <a name="create-a-keyspace"></a>Vytvoření klíčového prostoru

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

Spusťte následující příkaz v cqlsh a měli byste vidět keyspace, které jste vytvořili dříve.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Přetažení klíčového prostoru

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operace DDL tabulky

**Úvahy:**  

- Propustnost lze přiřadit na úrovni tabulky pomocí příkazu vytvořit tabulku.  
- Jeden klíč oddílu může uložit 20 GB dat.  
- Jeden záznam může uložit maximálně 2 MB dat.  
- Jeden rozsah klíčů oddílu může uložit více klíčů oddílu.

### <a name="create-a-table"></a>Vytvoření tabulky

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

Spusťte následující příkaz v cqlsh a měli byste vidět tabulku s názvem "knihy: 

```bash
USE books_ks;
DESCRIBE books;
```

Zřízená propustnost a výchozí hodnoty TTL nejsou zobrazeny ve výstupu předchozího příkazu, můžete tyto hodnoty získat z portálu.

### <a name="alter-table"></a>Změnit tabulku

Pomocí příkazu alter table můžete změnit následující hodnoty:

* zřízená propustnost 
* hodnota time-to-live
<br>Změny sloupců nejsou aktuálně podporovány.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Drop tabulka

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

Spusťte následující příkaz v cqlsh a měli byste vidět, že tabulka "knihy" již není k dispozici:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Další kroky

Po vytvoření keyspace a tabulky, přejděte k následujícím článkům pro operace CRUD a další:
 
* [Vytvořit nebo vložit operace](cassandra-spark-create-ops.md)  
* [Operace čtení](cassandra-spark-read-ops.md)  
* [Upsert operace](cassandra-spark-upsert-ops.md)  
* [Odstranit operace](cassandra-spark-delete-ops.md)  
* [Agregační operace](cassandra-spark-aggregation-ops.md)  
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)  
