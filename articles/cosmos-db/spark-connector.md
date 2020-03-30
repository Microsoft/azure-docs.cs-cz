---
title: Připojení Apache Spark k Azure Cosmos DB
description: Přečtěte si o konektoru Azure Cosmos DB Spark, který umožňuje připojit Apache Spark k Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bbc97489f0c7045040dd0189b97946c2bd8fb3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481632"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Zrychlete analýzu velkých objemů dat pomocí konektoru Apache Spark to Azure Cosmos DB

Úlohy [Spark](https://spark.apache.org/) můžete spouštět s daty uloženými v Azure Cosmos DB pomocí konektoru Cosmos DB Spark. Cosmos lze použít pro dávkové a datové proudové zpracování a jako vrstvu obsluhy pro přístup s nízkou latencí.

Konektor můžete použít s [Azure Databricks](https://azure.microsoft.com/services/databricks) nebo [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), které poskytují spravované clustery Spark v Azure. V následující tabulce jsou uvedeny podporované verze Spark.

| Komponenta | Version |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x a 2.1.x |
| Scala | 2.11 |
| Runtime verze Azure Databricks | > 3.4 |

> [!WARNING]
> Tento konektor podporuje základní (SQL) rozhraní API Azure Cosmos DB.
> Pro Cosmos DB pro rozhraní API MongoDB použijte [konektor MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Pro rozhraní API Cosmos DB Cassandra použijte [konektor Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Rychlý start

* Postupujte podle pokynů na [Začínáme s Java SDK](sql-api-async-java-get-started.md) nastavit účet Cosmos DB a naplnit některá data.
* Postupujte podle pokynů na [Azure Databricks začínáme](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) nastavit pracovní prostor Azure Databricks a clusteru.
* Teď můžete vytvořit nové poznámkové bloky a importovat knihovnu konektorů Cosmos DB. Přejděte na [Pracovat s konektorem Cosmos DB](#bk_working_with_connector) a najdete podrobnosti o tom, jak nastavit pracovní prostor.
* V následující části jsou úryvky o tom, jak číst a psát pomocí konektoru.

### <a name="batch-reads-from-cosmos-db"></a>Dávková čtení z Cosmos DB

Následující úryvek ukazuje, jak vytvořit datový rámec Spark pro čtení z Cosmos DB v PySparku.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

A stejný fragment kódu v Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Dávkové zápisy do Cosmos DB

Následující úryvek ukazuje, jak zapisovat datový rámec do Cosmos DB v PySparku.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

A stejný fragment kódu v Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streamování čtení z Cosmos DB

Následující úryvek ukazuje, jak se připojit a číst z kanálu změn Azure Cosmos DB.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
A stejný fragment kódu v Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streamování zápisů do Cosmos DB

Následující úryvek ukazuje, jak zapisovat datový rámec do Cosmos DB v PySparku.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

A stejný fragment kódu v Scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Další úryvky a vzorky od konce do konce viz [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Práce s konektorem

Můžete vytvořit konektor ze zdroje v GitHubu, nebo stáhnout uber sklenice z Maven v níže uvedených odkazech.

| Spark | Scala | Nejnovější verze |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Používání poznámkových bloků Databricks

Vytvořte knihovnu pomocí pracovního prostoru Databricks podle pokynů v průvodci Azure Databricks guide > [použijte konektor Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Všimněte si, že **použití Azure Cosmos DB Spark konektor** stránky není aktuálně aktuální. Namísto stahování šesti samostatných sklenic do šesti různých knihoven si můžete stáhnout https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) uber jar z maven at a nainstalovat tuto jednu sklenici / knihovnu.
> 

### <a name="using-spark-cli"></a>Použití jiskry-cli

Chcete-li pracovat s konektorem pomocí zapalovacího cli (tj. `spark-shell` `pyspark` `spark-submit`, ), můžete použít `--packages` parametr s [maven souřadnicemi](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)konektoru .

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Používání poznámkových bloků Jupyter

Pokud používáte poznámkové bloky Jupyter v HDInsight, `%%configure` můžete použít buňku magie jiskry k určení souřadnic maven konektoru.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Všimněte si, `spark.jars.excludes` že zahrnutí je specifické pro odstranění potenciální konflikty mezi konektorem Apache Spark a Livy.

### <a name="build-the-connector"></a>Sestavení konektoru

V současné době tento `maven` projekt konektoru používá tak, aby sestavení bez závislostí, můžete spustit:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Práce s našimi vzorky

[Úložiště GitHub Cosmos DB Spark má](https://github.com/Azure/azure-cosmosdb-spark) následující ukázkové poznámkové bloky a skripty, které můžete vyzkoušet.

* Výkon v yimnbu a **cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html:](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)Připojte Spark k Cosmos DB pomocí služby notebooku HDInsight Jupyter k prezentaci Spark SQL, GraphFrames a předpovídání zpoždění letů pomocí kanálů ML.
* **Twitter Zdroj s Apache Spark a Azure Cosmos DB Change Feed:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Použití Apache Spark k dotazování Cosmos DB Grafy:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Připojení Datových cihel Azure k Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** pomocí . `azure-cosmosdb-spark`  Tady je propojená také verze [notebooku Výkon letu V době, kdy je propojena.](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* **[Architektura Lambda s Azure Cosmos DB a HDInsight (Apache Spark):](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** Můžete snížit provozní režii údržby kanálů velkých objemů dat pomocí Cosmos DB a Spark.

## <a name="more-information"></a>Další informace

Máme více informací `azure-cosmosdb-spark` na [wiki,](https://github.com/Azure/azure-cosmosdb-spark/wiki) včetně:

* [Uživatelská příručka konektoru Azure Cosmos DB Spark Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Příklady agregací](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfigurace a nastavení

* [Konfigurace konektoru spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Instalace konektoru Spark to Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (probíhá)
* [Konfigurace přímého dotazu Power BI do Azure Cosmos DB přes Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Řešení potíží

* [Použití agregací Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Známé problémy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Výkon

* [Tipy pro výkon](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Spuštění testu dotazu](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Zápis testovacích běhů](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Kanál změn

* [Streamování změn zpracování pomocí kanálu změn Azure Cosmos DB a Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Ukázky kanálu změn](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Ukázky strukturovaného datového proudu](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorování

* [Monitorování úloh Spark pomocí přehledů aplikací](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, stáhněte si konektor Spark do Azure Cosmos DB z úložiště [GitHub azure-cosmosdb-spark.](https://github.com/Azure/azure-cosmosdb-spark) Prozkoumejte následující další zdroje v repo:

* [Příklady agregací](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkové bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Můžete také zkontrolovat [Apache Spark SQL, DataFrames a Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)a Apache Spark na Azure [HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
