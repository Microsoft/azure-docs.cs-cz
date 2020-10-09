---
title: Přístup k Azure Cosmos DB rozhraní API Cassandra ze Sparku na PŘÍZi pomocí HDInsight
description: Tento článek popisuje, jak pracovat s Azure Cosmos DB rozhraní API Cassandra ze Sparku na PŘÍZi pomocí HDInsight.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 4848bdceef084d587c839f2af93d1ff5a43c850b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260565"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Přístup k Azure Cosmos DB rozhraní API Cassandra ze Sparku na PŘÍZi pomocí HDInsight

Tento článek popisuje, jak získat přístup k Azure Cosmos DB rozhraní API Cassandra ze Sparku na PŘÍZi pomocí HDInsight-Spark z Spark-Shell. HDInsight je Hortonworks Hadoop PaaS v Azure, který využívá úložiště objektů pro HDFS a je v několika různých charakterech, jako je [Spark](../hdinsight/spark/apache-spark-overview.md).  Obsah tohoto dokumentu odkazuje na HDInsight-Spark, ale vztahuje se na všechny distribuce Hadoop.  

## <a name="prerequisites"></a>Požadavky

* [Zřídit Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Projděte si základy připojení k Azure Cosmos DB rozhraní API Cassandra](cassandra-spark-generic.md)

* [Zřízení clusteru HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Přečtěte si ukázky kódu pro práci s rozhraní API Cassandra](cassandra-spark-generic.md#next-steps)

* [Pro ověření použijte cqlsh, pokud to dáváte přednost](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfigurace rozhraní API Cassandra v Spark2** – konektor Spark pro Cassandra vyžaduje, aby byly podrobnosti připojení Cassandra inicializované jako součást kontextu Spark. Když spustíte Poznámkový blok Jupyter, relace a kontext Sparku jsou už inicializované a není vhodné zastavit a znovu inicializovat kontext Spark, pokud se neprovádí se všemi konfiguračními sadou v rámci výchozího spuštění poznámkového bloku HDInsight Jupyter. Jedním z alternativních řešení je přidat podrobnosti instance Cassandra přímo do konfigurace služby Ambari, Spark2. Jedná se o jednorázovou aktivitu na cluster, která vyžaduje restart služby Spark2.
 
  1. Přejít na Ambari, služba Spark2 a vyberte konfigurace.

  2. Pak klikněte na Custom spark2-Defaults a přidejte novou vlastnost s následujícími vlastnostmi a restartujte službu Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Přístup k Azure Cosmos DB rozhraní API Cassandra z prostředí Spark

Prostředí Spark se používá pro účely testování/průzkumu.

* Spusťte Spark-shell s požadovanými závislostmi Maven, které jsou kompatibilní s verzí Spark vašeho clusteru.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Provádění některých operací DDL a DML

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

* Spustit operace CRUD

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Přístup k Azure Cosmos DB rozhraní API Cassandra z poznámkových bloků Jupyter

HDInsight-Spark se dodává se službami poznámkových bloků Zeppelin a Jupyter. Jsou to webová poznámková bloková prostředí, která podporují Scala a Python. Poznámkové bloky jsou skvělé pro interaktivní průzkumné analýzy a spolupráci, ale nejsou určeny pro provozní a produkční procesy.

Následující poznámkové bloky Jupyter se dají nahrát do clusteru HDInsight Spark a poskytnou připravené ukázky pro práci s Azure Cosmos DB rozhraní API Cassandra. Nezapomeňte si projít první Poznámkový blok `1.0-ReadMe.ipynb` a zkontrolovat konfiguraci služby Spark pro připojení k Azure Cosmos DB rozhraní API Cassandra.

Stáhněte si tyto poznámkové bloky v části [Azure-Cosmos-DB-Cassandra-API-Spark-poznámkové bloky – Jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) do počítače.
  
### <a name="how-to-upload"></a>Jak nahrát:
Když spustíte Jupyter, přejděte na Scala. Nejprve vytvořte adresář a pak poznámkové bloky nahrajte do tohoto adresáře. Tlačítko pro nahrání je v horní části, pravé na straně.  

### <a name="how-to-run"></a>Jak spustit:
Spusťte prostřednictvím poznámkových bloků a každou buňku poznámkového bloku postupně.  Kliknutím na tlačítko spustit v horní části každého poznámkového bloku spustíte všechny buňky nebo SHIFT + ENTER pro každou buňku.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Přístup k Azure Cosmos DB rozhraní API Cassandra z programu Spark Scala

Pro automatizované procesy v produkčním prostředí se programy Spark odesílají do clusteru prostřednictvím [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Další kroky

* [Jak vytvořit program Spark Scala v integrovaném vývojovém prostředí a odeslat ho do clusteru HDInsight Spark prostřednictvím Livy ke spuštění](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Jak se připojit k Azure Cosmos DB rozhraní API Cassandra z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Úplný seznam ukázek kódu pro práci s rozhraní API Cassandra](cassandra-spark-generic.md)
