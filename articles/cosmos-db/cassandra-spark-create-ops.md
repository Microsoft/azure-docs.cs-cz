---
title: Vytvoření nebo vložení dat do Azure Cosmos DB rozhraní API Cassandra ze Sparku
description: Tento článek popisuje, jak vložit ukázková data do Azure Cosmos DB rozhraní API Cassandra tabulky.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 3ec44d20b763a98683d9b947c94ad6be75180113
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93092232"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Vytvoření nebo vložení dat do Azure Cosmos DB rozhraní API Cassandra ze Sparku
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]
 
Tento článek popisuje, jak vložit ukázková data do tabulky v Azure Cosmos DB rozhraní API Cassandra ze Sparku.

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

### <a name="create-a-dataframe-with-sample-data"></a>Vytvoření datového rámce s ukázkovými daty

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
> Funkce "vytvořit, pokud neexistují", na úrovni řádku, zatím není podporována.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Zachovat Azure Cosmos DB rozhraní API Cassandra

Při ukládání dat můžete také nastavit nastavení zásad pro čas na živé a konzistenci, jak je znázorněno v následujícím příkladu:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> Hodnota TTL na úrovni sloupce se ještě nepodporuje.

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Rozhraní API odolné distribuované databáze (RDD)

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
> Vytvořit, pokud funkce NOT EXISTS ještě není podporována.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Zachovat Azure Cosmos DB rozhraní API Cassandra

Když ukládáte data do rozhraní API Cassandra, můžete také nastavit nastavení zásad pro čas na živý a konzistenci, jak je znázorněno v následujícím příkladu:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Ověřit v cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Další kroky

Po vložení dat do tabulky Azure Cosmos DB rozhraní API Cassandra proveďte další operace s daty uloženými v Cosmos DB rozhraní API Cassandra pomocí následujících článků:
 
* [Operace čtení](cassandra-spark-read-ops.md)
* [Operace Upsert](cassandra-spark-upsert-ops.md)
* [Operace Delete](cassandra-spark-delete-ops.md)
* [Operace agregace](cassandra-spark-aggregation-ops.md)
* [Operace kopírování tabulky](cassandra-spark-table-copy-ops.md)

