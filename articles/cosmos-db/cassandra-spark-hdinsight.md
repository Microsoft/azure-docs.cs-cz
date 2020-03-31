---
title: Přístup k rozhraní API Azure Cosmos DB Cassandra ze Spark u YARN s HDInsight
description: Tento článek popisuje, jak pracovat s rozhraním API Azure Cosmos DB Cassandra ze Spark u YARN s HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bef4ee14cb4a7d64d80dc5776d8ecea0f831881a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887628"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Přístup k rozhraní API Azure Cosmos DB Cassandra ze Spark u YARN s HDInsight

Tento článek popisuje, jak získat přístup k rozhraní API Azure Cosmos DB Cassandra ze Spark na YARN s HDInsight-Spark z jiskry. HDInsight je Hortonworks Hadoop PaaS společnosti Microsoft v Azure, který využívá úložiště objektů pro HDFS a přichází v několika příchutích, včetně [Spark](../hdinsight/spark/apache-spark-overview.md).  Zatímco obsah v tomto dokumentu odkazuje na HDInsight-Spark, je použitelný pro všechny distribuce Hadoop.  

## <a name="prerequisites"></a>Požadavky

* [Zřízení rozhraní API Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Projděte si základy připojení k rozhraní API Azure Cosmos DB Cassandra](cassandra-spark-generic.md)

* [Zřízení clusteru HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Zkontrolujte ukázky kódu pro práci s rozhraním CASSANDRA API](cassandra-spark-generic.md#next-steps)

* [Použijte cqlsh pro ověření, pokud dáváte přednost](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfigurace rozhraní API Cassandra v Spark2** – konektor Spark pro Cassandra vyžaduje, aby podrobnosti o připojení Cassandra byly inicializovány jako součást kontextu Spark. Když spustíte poznámkový blok Jupyter, relace jiskry a kontext jsou již inicializovány a není vhodné zastavit a znovu inicializovat kontext Spark, pokud není kompletní s každou konfigurační sadou jako součást výchozího spuštění notebooku Jupyter HDInsight. Jedním z řešení je přidání podrobností instance Cassandra přímo do konfigurace služby Ambari, Spark2. Jedná se o jednorázovou aktivitu na cluster, která vyžaduje restartování služby Spark2.
 
  1. Přejděte na službu Ambari, Spark2 a vyberte konfigurace.

  2. Pak přejděte na vlastní výchozí hodnoty spark2 a přidejte novou vlastnost s následujícími vlastnostmi a restartujte službu Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Přístup k rozhraní API Azure Cosmos DB Cassandra z prostředí Spark

Spark shell se používá pro účely testování / průzkumu.

* Spusťte spark-shell s požadovanými maven závislostmi kompatibilními s verzí Spark vašeho clusteru.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Provést některé operace DDL a DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
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

* Spuštění operací CRUD

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Přístup k rozhraní API Azure Cosmos DB Cassandra z poznámkových bloků Jupyter

HDInsight-Spark je dodáván se službami Notebook Zeppelin a Jupyter. Obě jsou webová prostředí pro poznámkové bloky, která podporují Scala a Python. Poznámkové bloky jsou skvělé pro interaktivní průzkumné analýzy a spolupráci, ale nejsou určeny pro provozní/produkční procesy.

Následující poznámkové bloky Jupyter umožni it nahrát do clusteru HDInsight Spark a poskytnout připravené ukázky pro práci s Azure Cosmos DB Cassandra API. Zkontrolujte první poznámkový `1.0-ReadMe.ipynb` blok a zkontrolujte konfiguraci služby Spark pro připojení k rozhraní API Azure Cosmos DB Cassandra.

Stáhněte si tyto poznámkové bloky pod [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) do svého počítače.
  
### <a name="how-to-upload"></a>Jak nahrát:
Když spustíte Jupyter, přejděte na Scalu. Nejprve vytvořte adresář a potom poznámkové bloky nahrajte do adresáře. Tlačítko nahrávání je na horní, pravé straně.  

### <a name="how-to-run"></a>Jak spustit:
Projděte poznámkové bloky a každou buňku poznámkového bloku postupně.  Kliknutím na tlačítko spustit v horní části každého poznámkového bloku spusťte všechny buňky nebo shift+enter pro každou buňku.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Přístup k rozhraní AZURE Cosmos DB Cassandra api z vašeho programu Spark Scala

Pro automatizované procesy ve výrobě jsou programy Spark odesílány do clusteru prostřednictvím [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Další kroky

* [Jak vytvořit program Spark Scala v ide a odeslat jej do clusteru HDInsight Spark prostřednictvím Livy k provedení](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Jak se připojit k rozhraní AZURE Cosmos DB Cassandra API z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Kompletní seznam ukázek kódu pro práci s rozhraním CASSANDRA API](cassandra-spark-generic.md)
