---
title: Připojit Apache Spark k Azure Cosmos DB
description: Seznamte se s Azure Cosmos DB konektor Spark, který umožňuje připojení Apache Spark k Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 633baafa7f6cb1bc134f07ce64ba98ebd4cb626c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603046"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Urychlení analýz velkých objemů dat pomocí Apache Spark pro Azure Cosmos DB konektor

Můžete spouštět úlohy [Spark](https://spark.apache.org/) s daty uloženými v Azure Cosmos DB pomocí konektoru Cosmos DB Spark. Cosmos lze použít pro zpracování dávkových a datových proudů a jako vrstvu obsluhy pro přístup s nízkou latencí.

Můžete použít konektor s [Azure Databricks](https://azure.microsoft.com/services/databricks) nebo [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), který poskytuje spravované Clustery Spark v Azure. V následující tabulce jsou uvedeny podporované verze Sparku.

| Součást | Verze |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x a 2.1. x |
| Scala | 2,11 |
| Verze modulu runtime Azure Databricks | > 3,4 |

> [!WARNING]
> Tento konektor podporuje rozhraní API jádra (SQL) Azure Cosmos DB.
> Pro Cosmos DB rozhraní MongoDB API použijte [konektor MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Pro Cosmos DB rozhraní API Cassandra použijte [konektor Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Rychlé zprovoznění

* Postupujte podle kroků v [části Začínáme se sadou Java SDK](sql-api-async-java-get-started.md) a nastavte účet Cosmos DB a naplňte data.
* Postupujte podle kroků v [Azure Databricks Začínáme](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) s nastavením pracovního prostoru Azure Databricks a clusteru.
* Nyní můžete vytvořit nové poznámkové bloky a importovat knihovnu konektoru Cosmos DB. Podrobnosti o tom, jak nastavit pracovní prostor, najdete v článku o [práci s konektorem Cosmos DB](#bk_working_with_connector) .
* Následující část obsahuje fragmenty kódu, jak číst a zapisovat pomocí konektoru.

### <a name="batch-reads-from-cosmos-db"></a>Dávková čtení z Cosmos DB

Následující fragment kódu ukazuje, jak vytvořit datový rámec Spark pro čtení z Cosmos DB v PySpark.

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

### <a name="batch-writes-to-cosmos-db"></a>Dávková zápisy do Cosmos DB

Následující fragment kódu ukazuje, jak napsat datový rámec pro Cosmos DB v PySpark.

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
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
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
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Streamování čtení z Cosmos DB

Následující fragment kódu ukazuje, jak se připojit ke Azure Cosmos DB změnám kanálu a číst z něj.

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

Následující fragment kódu ukazuje, jak napsat datový rámec pro Cosmos DB v PySpark.

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
Další informace o dalších fragmentech a koncových ukázkách najdete v tématu [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Práce s konektorem

Konektor můžete vytvořit ze zdroje na GitHubu nebo stáhnout Uber jar z Maven na odkazech níže.

| Spark | Scala | Nejnovější verze |
|---|---|---|
| 2.4.0 | 2,11 | [Azure – cosmosdb – spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [Azure-cosmosdb-spark_2.3.0 _2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [Azure-cosmosdb-spark_2.2.0 _2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [Azure-cosmosdb-spark_2.1.0 _2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Použití poznámkových bloků datacihly

Vytvořte knihovnu pomocí pracovního prostoru datacihly podle pokynů v příručce Azure Databricks > [použijte konektor Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html) .

> [!NOTE]
> Stránka **použít konektor Azure Cosmos DB Spark** aktuálně není aktuální. Místo stažení šesti samostatných jar do šesti různých knihoven si můžete stáhnout Uber jar z Maven v [Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) a nainstalovat tuto jednu sklenici nebo knihovnu.
> 

### <a name="using-spark-cli"></a>Použití Spark-CLI

Chcete-li pracovat s konektorem pomocí Spark-CLI (tj.,, `spark-shell` `pyspark` `spark-submit` ), můžete použít `--packages` parametr s [souřadnicemi Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)spojnice.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Použití poznámkových bloků Jupyter

Pokud používáte Jupyter Notebook v rámci služby HDInsight, můžete `%%configure` k určení souřadnic Maven spojnice použít buňku Spark-Magic.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Všimněte si, že zahrnutí `spark.jars.excludes` je specifické pro odebrání možných konfliktů mezi konektorem, Apache Spark a Livy.

### <a name="build-the-connector"></a>Sestavení konektoru

V současné době tento projekt konektoru používá `maven` k sestavení bez závislostí, můžete spustit:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Práce s našimi ukázkami

[Úložiště GitHub Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) má následující ukázkové poznámkové bloky a skripty, které můžete vyzkoušet.

* **Čas provozu v čase pomocí Sparku a Cosmos dB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Připojte Spark pro Cosmos DB pomocí služby HDInsight Jupyter notebook, abyste předvedli Spark SQL, GraphFrames a předpověď zpoždění letu pomocí kanálů ml.
* **Zdroj Twitteru s Apache Spark a Azure Cosmos DB změnu kanálu**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Použití Apache Spark k dotazování Cosmos DB grafů**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Připojení Azure Databricks k Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** pomocí `azure-cosmosdb-spark` .  Zde je také Azure Databricks verze [poznámkového bloku on-time Performance letu](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architektura lambda s Azure Cosmos DB a HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: můžete snížit provozní režii při údržbě kanálů s velkými objemy dat pomocí Cosmos DB a Sparku.

## <a name="more-information"></a>Další informace

Na wikiwebu máme další informace, `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) včetně těchto:

* [Uživatelská příručka ke konektoru Spark Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Příklady agregací](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfigurace a nastavení

* [Konfigurace konektoru Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Nastavení konektoru Spark to Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (probíhá)
* [Konfigurace Power BI přímý dotaz na Azure Cosmos DB prostřednictvím Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Řešení potíží

* [Použití agregací Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Známé problémy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Výkon

* [Tipy ke zvýšení výkonu](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Dotazy na testovací běhy](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Zápis testovacích běhů](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Kanál změn

* [Zpracování datových toků pomocí Azure Cosmos DB změny kanálu a Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Změny ukázek informačních kanálů](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Ukázky strukturovaného streamu](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorování

* [Monitorování úloh Spark pomocí Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, Stáhněte si z úložiště GitHub [Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) konektor Sparku do Azure Cosmos DB. Prozkoumejte následující další prostředky v úložišti:

* [Příklady agregací](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkové bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Můžete si také projít [průvodce Apache Spark SQL, datovými rámečky a datovými sadami](https://spark.apache.org/docs/latest/sql-programming-guide.html)a v článku [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) .
