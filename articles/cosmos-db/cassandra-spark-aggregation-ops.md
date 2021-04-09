---
title: Agregační operace s tabulkami rozhraní API Cassandra služby Azure Cosmos DB ze Sparku
description: Tento článek popisuje základní operace agregace proti Azure Cosmos DB rozhraní API Cassandra tabulek ze Sparku.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5939690d3f2c0bb9affa3e2fb425b909b96cf002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087617"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Agregační operace s tabulkami rozhraní API Cassandra služby Azure Cosmos DB ze Sparku 
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Tento článek popisuje provádění základních agregačních operací s tabulkami rozhraní API Cassandra služby Azure Cosmos DB ze Sparku. 

> [!NOTE]
> Filtrování na straně serveru a agregace na straně serveru se v Azure Cosmos DB rozhraní API Cassandra aktuálně nepodporuje.

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
## <a name="sample-data-generator"></a>Generátor ukázkových dat

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Operace Count


### <a name="rdd-api"></a>ROZHRANÍ API PRO RDD

```scala
sc.cassandraTable("books_ks", "books").count
```

**Výkonem**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Rozhraní API pro dataframe

Počet pro datový rámec není v současné době podporován.  Následující ukázka ukazuje, jak spustit počet datového rámce po trvalém uložení datového rámce do paměti jako alternativní řešení.

Z následujících dostupných možností vyberte [možnost úložiště]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) , aby nedocházelo k problémům s nedostatkem paměti:

* MEMORY_ONLY: Toto je výchozí možnost úložiště. Ukládá RDD jako deserializovatelné objekty Java v JVM. Pokud se RDD nevejde do paměti, některé oddíly nebudou ukládány do mezipaměti a při každém jejich vypočítávání se budou přepočítat průběžně.

* MEMORY_AND_DISK: ukládá RDD jako deserializovatelné objekty Java v JVM. Pokud se RDD nevejde do paměti, ukládejte oddíly, které se na disk nevejdou, a kdykoli je to potřeba, přečtěte si je z umístění, které jsou uložené.

* MEMORY_ONLY_SER (Java/Scala): ukládá RDD jako serializovaných objektů Java – jedno bajtové pole na oddíl. Tato možnost je při porovnání s deserializovanými objekty velmi efektivní, zejména při použití rychlého serializátoru, ale čtení je náročné na procesor.

* MEMORY_AND_DISK_SER (Java/Scala): Tato možnost úložiště je jako MEMORY_ONLY_SER, jediným rozdílem je to, že při jejich nutnosti přechází oddíly, které se nevejdou do paměti disku, místo jejich zpracování.

* DISK_ONLY: ukládá oddíly RDD jenom na disku.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: stejné jako úrovně výše, ale replikuje každý oddíl na dva uzly clusteru.

* OFF_HEAP (experimentální): podobná MEMORY_ONLY_SER, ale ukládá data do paměti mimo haldu a vyžaduje, aby byla paměť mimo haldu povolená před časem. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Průměrná operace

### <a name="rdd-api"></a>ROZHRANÍ API PRO RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Výkonem**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Rozhraní API pro dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Výkonem**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Výkonem**
```
16.016000175476073
```

## <a name="min-operation"></a>Minimální operace

### <a name="rdd-api"></a>ROZHRANÍ API PRO RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Výkonem**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Rozhraní API pro dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Výkonem**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Výkonem**
```
11.33
```

## <a name="max-operation"></a>Maximální operace

### <a name="rdd-api"></a>ROZHRANÍ API PRO RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Rozhraní API pro dataframe

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Výkonem**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Výkonem**
```
22.45
```

## <a name="sum-operation"></a>Operace Sum

### <a name="rdd-api"></a>ROZHRANÍ API PRO RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Výkonem**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Rozhraní API pro dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Výkonem**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Výkonem**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Horní nebo porovnatelné operace

### <a name="rdd-api"></a>ROZHRANÍ API PRO RDD

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Výkonem**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Rozhraní API pro dataframe

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Výkonem**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Další kroky

Chcete-li provést operace kopírování tabulky, přečtěte si:

* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)
