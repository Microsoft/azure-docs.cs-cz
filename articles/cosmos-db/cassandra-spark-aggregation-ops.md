---
title: Agregační operace pro tabulky Azure Cosmos DB Cassandra API z aplikace Spark
description: V tomto článku najdete základní agregační operace na tabulky Azure Cosmos DB Cassandra API z aplikace Spark
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.custom: basics, DDL, DML
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: ac150b72e94b010457731616784b2a8381c9080c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969323"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Agregační operace pro tabulky Azure Cosmos DB Cassandra API z aplikace Spark 

Tento článek popisuje základní agregační operace na tabulky Azure Cosmos DB Cassandra API z aplikace Spark. 

> [!NOTE]
> Filtrování na straně serveru a na straně serveru agregace se aktuálně nepodporuje v Azure Cosmos DB Cassandra API.

## <a name="cassandra-api-configuration"></a>Konfigurace rozhraní Cassandra API

```scala
import org.apache.spark.sql.cassandra._
//datastax Spark connector
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
## <a name="sample-data-generator"></a>Generátor dat vzorku

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

## <a name="count-operation"></a>Počet operací


### <a name="rdd-api"></a>ROZHRANÍ API RDD

```scala
sc.cassandraTable("books_ks", "books").count
```

**Výstup:**
```
res48: Long = 5
```

### <a name="dataframe-api"></a>Datový rámec rozhraní API

Počet proti datových rámců není aktuálně podporováno.  Následující ukázka ukazuje, jak provést počet datový rámec po trvalém ukládání datového rámce do paměti jako alternativní řešení.

Zvolte [možnost úložiště]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) z těchto možností k dispozici, aby se zabránilo nezobrazovalo "nedostatek paměti" problémy:

* MEMORY_ONLY: Toto je výchozí možnost úložiště. Úložiště RDD jako deserializovaný objekty Java v JVM. Pokud RDD se nevejde do paměti, nebudou zapisována do mezipaměti několik oddílů a jsou přepočítány průběžně pokaždé, když je budete potřebovat.

* MEMORY_AND_DISK: Úložiště RDD jako deserializovaný objekty Java v JVM. Pokud RDD se nevejde do paměti, uložte oddíly, které nemáte vejít na disku a pokaždé, když se vyžaduje, přečtěte si je z umístění, které jsou pak uloženy.

* MEMORY_ONLY_SER (Java/Scala): RDD úložiště jako serializovat objekty jeden bajtové pole Java na oddíl. Tato možnost je prostor efektivní ve srovnání s objekty deserializovat, zvláště při použití rychlého serializátor, ale více náročnou na procesor ke čtení.

* MEMORY_AND_DISK_SER (Java/Scala): Tato možnost úložiště je třeba MEMORY_ONLY_SER, jediným rozdílem je, že ho rozlití kapaliny oddíly, které nevyhovují uvedeným disk paměti namísto recomputing je v případě potřeby.

* DISK_ONLY: Ukládá RDD oddílů na disku pouze.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: Stejné jako úrovně výše, ale replikuje každý oddíl na dva uzly clusteru.

* (Experimentální) OFF_HEAP: podobný MEMORY_ONLY_SER, ale jeho ukládá data v vypnout haldy paměti a vyžaduje vypnutí haldy paměti povolení předem. 

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

## <a name="average-operation"></a>Operace průměru

### <a name="rdd-api"></a>ROZHRANÍ API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Výstup:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Datový rámec rozhraní API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Výstup:**
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
**Výstup:**
```
16.016000175476073
```

## <a name="min-operation"></a>Operace min

### <a name="rdd-api"></a>ROZHRANÍ API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Výstup:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Datový rámec rozhraní API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Výstup:**
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

**Výstup:**
```
11.33
```

## <a name="max-operation"></a>Operaci Max

### <a name="rdd-api"></a>ROZHRANÍ API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Datový rámec rozhraní API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Výstup:**
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
**Výstup:**
```22.45 ```

## <a name="sum-operation"></a>Operace součtu

### <a name="rdd-api"></a>ROZHRANÍ API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Výstup:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Datový rámec rozhraní API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Výstup:**
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

**Výstup:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Horní nebo srovnatelný operace

### <a name="rdd-api"></a>ROZHRANÍ API RDD

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Výstup:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Datový rámec rozhraní API

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

**Výstup:**
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

## <a name="next-steps"></a>Další postup

K provedení operace kopírování tabulky, naleznete v tématu:

* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)
