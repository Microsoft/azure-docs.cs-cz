---
title: Operace DDL v Azure Cosmos DB Cassandra API z aplikace Spark
description: Tento článek podrobně popisuje operace DDL prostoru klíčů a tabulky Azure Cosmos DB Cassandra API z aplikace Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5c12787cd6e0df19fd842dd44da49aa5ea97aa05
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036660"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operace DDL v Azure Cosmos DB Cassandra API z aplikace Spark

Tento článek podrobně popisuje operace DDL prostoru klíčů a tabulky Azure Cosmos DB Cassandra API z aplikace Spark.

## <a name="cassandra-api-related-configuration"></a>Konfigurace rozhraní API související Cassandra 

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

## <a name="keyspace-ddl-operations"></a>Operace DDL prostor klíčů

### <a name="create-a-keyspace"></a>Vytvoření prostor klíčů

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Ověření v cqlsh

Spuštěním následujícího příkazu v cqlsh a měli byste vidět prostor klíčů, který jste vytvořili dříve.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Vyřadit prostor klíčů

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Ověření v cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operace DDL tabulky

**Důležité informace:**  

- Propustnost je možné přiřadit na úrovni tabulky pomocí příkazu create table.  
- 10 GB dat můžete ukládat jeden klíč oddílu.  
- Jeden záznam může ukládat maximálně 2 MB na data.  
- Jeden rozsah klíče oddílu můžete uložit více klíčů oddílů.

### <a name="create-a-table"></a>Vytvoření tabulky

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Ověření v cqlsh

Spuštěním následujícího příkazu v cqlsh a měli byste vidět v tabulce s názvem "knih: 

```bash
USE books_ks;
DESCRIBE books;
```

Zřízená propustnost a výchozí hodnoty TTL se nezobrazují ve výstupu předchozího příkazu, můžete tyto hodnoty získat z portálu.

### <a name="alter-table"></a>Příkaz alter table

Pomocí příkazu alter table je možné změnit následující hodnoty:

* zřízená propustnost 
* Hodnota Time to live
<br>Změny ve sloupcích se momentálně nepodporují.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Odstranit tabulku

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Ověření v cqlsh

Spuštěním následujícího příkazu v cqlsh a byste měli vidět, že tabulka "knihy" již není k dispozici:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Další postup

Po vytvoření prostor klíčů a tabulky, pokračujte v následujících článcích pro operace CRUD a další:
 
* [Vytvořit/vložit operací](cassandra-spark-create-ops.md)  
* [operace čtení](cassandra-spark-read-ops.md)  
* [Operace Upsert](cassandra-spark-upsert-ops.md)  
* [Operace odstranění](cassandra-spark-delete-ops.md)  
* [Agregační operace](cassandra-spark-aggregation-ops.md)  
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)  
