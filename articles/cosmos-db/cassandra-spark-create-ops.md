---
title: Vytvořit/vložení dat do Azure Cosmos DB Cassandra API z aplikace Spark
description: Tento článek podrobně popisuje, jak vložit ukázková data do tabulek Azure Cosmos DB Cassandra API
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 66d41c519f6d6681f2e2644f718f241f6a4d5da2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989410"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Vytvořit/vložení dat do Azure Cosmos DB Cassandra API z aplikace Spark
 
Tento článek popisuje, jak vložit ukázková data do tabulky v Azure Cosmos DB Cassandra API z aplikace Spark.

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
## <a name="dataframe-api"></a>Datový rámec rozhraní API

### <a name="create-a-dataframe-with-sample-data"></a>Vytvořte datový rámec s ukázkovými daty

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> "Vytvořit, pokud neexistuje" funkce na úrovni řádku, se ještě nepodporuje.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Zachovat do služby Azure Cosmos DB Cassandra API

Při ukládání dat, můžete také nastavit time-to-live a konzistence nastavení zásad, jak je znázorněno v následujícím příkladu:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> Hodnota TTL úrovni sloupce se ještě nepodporuje.

#### <a name="validate-in-cqlsh"></a>Ověření v cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Rozhraní API pružné distribuovanou databázi (RDD)

### <a name="create-a-rdd-with-sample-data"></a>Vytvoření RDD s ukázkovými daty
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Vytvořit, pokud neexistuje funkce není dosud podporována.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Zachovat do služby Azure Cosmos DB Cassandra API

Při ukládání dat do rozhraní Cassandra API, můžete také nastavit time-to-live a konzistence nastavení zásad, jak je znázorněno v následujícím příkladu:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Ověření v cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Další postup

Po vložení dat do tabulky Azure Cosmos DB Cassandra API, pokračujte v následujících článcích pro provádění jiných operací s daty uloženými v Cosmos DB Cassandra API:
 
* [operace čtení](cassandra-spark-read-ops.md)
* [Operace Upsert](cassandra-spark-upsert-ops.md)
* [Operace odstranění](cassandra-spark-delete-ops.md)
* [Agregační operace](cassandra-spark-aggregation-ops.md)
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)

