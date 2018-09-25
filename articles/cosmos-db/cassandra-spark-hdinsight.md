---
title: Přístup k Azure Cosmos DB Cassandra API z aplikace Spark na základě YARNU s HDInsight
description: Tento článek popisuje, jak pracovat s Azure Cosmos DB Cassandra API z aplikace Spark na základě YARNU s HDInsight
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: f71f5fa71d685af103bd82b3ccd2a910ab81d90f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962909"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Přístup k Azure Cosmos DB Cassandra API z aplikace Spark na základě YARNU s HDInsight

Tento článek popisuje, jak získat přístup k Azure Cosmos DB Cassandra API z aplikace Spark na základě YARNU s HDInsight Spark v prostředí spark. HDInsight je společnosti Microsoft Hortonworks Hadoop PaaS v Azure, která využívá úložiště objektů pro HDFS a je k dispozici ve několik typů, včetně [Spark](../hdinsight/spark/apache-spark-overview.md).  Přitom obsah tohoto dokumentu odkazů na HDInsight Spark, se vztahuje na všechny distribuce Hadoopu.  

## <a name="prerequisites"></a>Požadavky

* [Zřízení služby Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Přečtěte si základní informace o připojení k Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Zřízení clusteru HDInsight Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Projděte si ukázky kódu pro práci s rozhraním Cassandra API](cassandra-spark-generic.md#next-steps)

* [Pokud tedy chcete používat cqlsh pro ověření](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfigurace rozhraní Cassandra API ve Spark2** – The Datastax konektor pro Cassandra vyžaduje, že připojení Cassandra podrobnosti nutné jej inicializovat jako součást kontextu Spark. Při spuštění poznámkového bloku Jupyter, jsou již inicializovány relaci spark a kontext a není vhodné zastavit a znovu inicializovat kontext Spark, dokud neskončí všechny konfigurace, nastavte jako součást spuštění poznámkového bloku Jupyter výchozí HDInsight. Jeden alternativním řešením je přidat podrobnosti o instanci Cassandra Ambari, Spark2 přímo v konfiguraci služby. Toto je jednorázovou aktivitou za cluster, který vyžaduje restartování služby Spark2.
 
  1. Přejděte na Ambari, Spark2 služby a klikněte na konfigurace

  2. Potom přejděte na vlastní spark2 – výchozí hodnoty a přidejte novou vlastnost s tímto a restartujte službu Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Přístup k Azure Cosmos DB Cassandra API z prostředí Sparku

Prostředí Spark se používá pro účely testování a zkoumání.

* Spusťte prostředí sparku s kompatibilní s verzí vašeho clusteru Spark maven požadované závislosti.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Spustit některé operace DDL a jazyk DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //datastax Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Spuštění operace CRUD

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Přístup k Azure Cosmos DB Cassandra API z aplikace Jupyter notebook

HDInsight Spark se dodává se službami Poznámkový blok Zeppelin a Jupyter. Jsou obě webové Poznámkový blok prostředí s podporou jazyků Scala a Python. Poznámkové bloky se skvěle hodí pro interaktivní nahodilé analýzy a spolupráci, ale není určená pro provozní/productionized procesy.

Tyto poznámkové bloky Jupyter se dají nahrát do vašeho clusteru HDInsight Spark a poskytují připravené ukázky pro práci s Azure Cosmos DB Cassandra API. Nezapomeňte si přečíst první Poznámkový blok `1.0-ReadMe.ipynb` ke kontrole konfigurace služby Spark pro připojení k rozhraní Cassandra API služby Azure Cosmos DB.

Stáhněte si tyto poznámkové bloky v části [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) k vašemu počítači.
  
### <a name="how-to-upload"></a>Jak odeslat:
Když spustíte Jupyter, přejděte na Scala. Nejprve vytvořte adresář a potom nahrání poznámkových bloků do adresáře. Tlačítko Nahrát je na horní, pravé straně.  

### <a name="how-to-run"></a>Jak spustit:
Postupně spusťte prostřednictvím poznámkových bloků a každá buňka poznámkového bloku.  Klikněte na tlačítko pro spuštění v horní části každé Poznámkový blok jupyter spustit všechny buňky nebo shift + enter pro každou buňku.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Přístup s Azure Cosmos DB Cassandra API z aplikace Spark Scala

Pro automatizované procesy v produkčním prostředí, odesílají programů Spark na clusteru přes [skriptu spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Další postup

* [Jak vytvořit Spark Scala programovat v rozhraní IDE a odeslat ji na clusteru HDInsight Spark prostřednictvím Livy pro spuštění](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Jak se připojit k Azure Cosmos DB Cassandra API z aplikace Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Úplný seznam všech ukázek kódu pro práci s rozhraním Cassandra API](cassandra-spark-generic.md)
