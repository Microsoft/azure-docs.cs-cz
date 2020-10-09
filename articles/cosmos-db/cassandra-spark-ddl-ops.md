---
title: Operace DDL v Azure Cosmos DB rozhraní API Cassandra ze Sparku
description: Tento článek podrobně popisuje operace v oblasti klíčů a v tabulce DDL pro Azure Cosmos DB rozhraní API Cassandra ze Sparku.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 589114fa004c8b4479e1a14c5a99161dd972c5bf
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841097"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operace DDL v Azure Cosmos DB rozhraní API Cassandra ze Sparku

Tento článek podrobně popisuje operace v oblasti klíčů a v tabulce DDL pro Azure Cosmos DB rozhraní API Cassandra ze Sparku.

## <a name="cassandra-api-related-configuration"></a>Konfigurace související s rozhraní API Cassandra 

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

## <a name="keyspace-ddl-operations"></a>Operace DDL prostoru klíčů

### <a name="create-a-keyspace"></a>Vytvoření prostoru klíčů

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

Spusťte následující příkaz v cqlsh a měli byste vidět místo na disku, které jste vytvořili dříve.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Vyřazení prostoru klíčů

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operace DDL tabulky

**Odůvodněn**  

- Propustnost lze přiřadit na úrovni tabulky pomocí příkazu CREATE TABLE.  
- Jeden klíč oddílu může ukládat 20 GB dat.  
- Jeden záznam může obsahovat maximálně 2 MB dat.  
- Jeden rozsah klíčů oddílu může ukládat několik klíčů oddílů.

### <a name="create-a-table"></a>Vytvoření tabulky

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks1.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

V cqlsh spusťte následující příkaz a měli byste vidět tabulku s názvem Books: 

```bash
USE books_ks;
DESCRIBE books;
```

Zřízené propustnost a výchozí hodnoty TTL nejsou zobrazeny ve výstupu předchozího příkazu. Tyto hodnoty můžete získat z portálu.

### <a name="alter-table"></a>Změnit tabulku

Následující hodnoty můžete změnit pomocí příkazu ALTER TABLE:

* zřízená propustnost 
* Hodnota TTL (Time to Live)
<br>Změny sloupců nejsou aktuálně podporovány.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Odkládací tabulka

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

Spusťte následující příkaz v cqlsh a měli byste vidět, že tabulka Books už není dostupná:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Další kroky

Po vytvoření prostoru klíčů a tabulky pokračujte v následujících článcích pro operace CRUD a další:
 
* [Operace vytvoření/vložení](cassandra-spark-create-ops.md)  
* [Operace čtení](cassandra-spark-read-ops.md)  
* [Operace Upsert](cassandra-spark-upsert-ops.md)  
* [Operace Delete](cassandra-spark-delete-ops.md)  
* [Agregační operace](cassandra-spark-aggregation-ops.md)  
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)  
