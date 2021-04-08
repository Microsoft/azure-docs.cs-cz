---
title: Čtení dat rozhraní API Cassandra tabulky pomocí Sparku
titleSufix: Azure Cosmos DB
description: Tento článek popisuje, jak číst data z rozhraní API Cassandra tabulky v Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073492"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Čtení dat z Azure Cosmos DB rozhraní API Cassandra tabulek pomocí Sparku
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 Tento článek popisuje, jak číst data uložená v Azure Cosmos DB rozhraní API Cassandra ze Sparku.

## <a name="cassandra-api-configuration"></a>Konfigurace rozhraní API Cassandra
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
## <a name="dataframe-api"></a>Rozhraní API pro dataframe

### <a name="read-table-using-sessionreadformat-command"></a>Čtení tabulky pomocí příkazu Session. Read. Format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Čtení tabulky pomocí Spark. Read. cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Čtení určitých sloupců v tabulce

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Použití filtrů

K zajištění lepších optimalizovaných dotazů Spark můžete do databáze přejít predikáty. Predikát je podmínka pro dotaz, který vrací hodnotu true nebo false, která se obvykle nachází v klauzuli WHERE. Predikát push dolů filtruje data v databázovém dotazu, snižuje počet položek načtených z databáze a zvyšuje výkon dotazů. Rozhraní API datové sady Spark standardně automaticky přeskočí platné klauzule WHERE do databáze. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

Část PushedFilters fyzického plánu zahrnuje filtr push GreaterThan. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="oddíly":::

## <a name="rdd-api"></a>ROZHRANÍ API PRO RDD

### <a name="read-table"></a>Přečíst tabulku
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Čtení určitých sloupců v tabulce

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Zobrazení SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Vytvořit dočasné zobrazení z datového rámce

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Spouštění dotazů pro zobrazení

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Další kroky

Níže najdete další články o práci s Azure Cosmos DB rozhraní API Cassandra ze Sparku:
 
 * [Operace Upsert](cassandra-spark-upsert-ops.md)
 * [Operace Delete](cassandra-spark-delete-ops.md)
 * [Operace agregace](cassandra-spark-aggregation-ops.md)
 * [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)

