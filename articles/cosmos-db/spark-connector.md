---
title: Apache Spark se připojit ke službě Azure Cosmos DB
description: Další informace o konektoru Azure Cosmos DB Spark, který vám umožní připojit ke službě Azure Cosmos DB Apache Spark.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: c39614a05db6553102e74ddbc3838d8c8f812640
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190488"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Urychlení analýzy velkých objemů dat pomocí Apache Sparku do konektoru služby Azure Cosmos DB

Můžete spustit [Spark](https://spark.apache.org/) úlohy s daty uloženými ve službě Azure Cosmos DB pomocí konektoru Cosmos DB Spark. Cosmos můžete použít pro dávkové zpracování datových proudů a jako obslužné vrstvy pro přístup s nízkou latencí.

Můžete použít konektor s [Azure Databricks](https://azure.microsoft.com/services/databricks) nebo [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), které poskytují spravované clustery Spark v Azure. Následující tabulka uvádí podporované Spark verze.

| Komponenta | Verze |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x a 2.1.x |
| Scala | 2.11 |
| Verze modulu runtime služby Azure Databricks | > 3.4 |

> [!WARNING]
> Tento konektor podporuje rozhraní API core (SQL) ze služby Azure Cosmos DB.
> Pro Cosmos DB pro rozhraní MongoDB API, použijte [konektor MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Pro Cosmos DB Cassandra API, použijte [konektor Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Rychlý start

* Postupujte podle kroků uvedených v [Začínáme se sadou Java SDK](sql-api-async-java-get-started.md) nastavení účtu služby Cosmos DB a naplňte jimi nějaká data.
* Postupujte podle kroků uvedených v [Začínáme se službou Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) nastavení pracovního prostoru Azure Databricks a cluster služby.
* Nyní můžete vytvořit nové poznámkových bloků a importovat knihovna konektorů služby Cosmos DB. Přejít na [práce s konektorem služby Cosmos DB](#bk_working_with_connector) podrobnosti o tom, jak nastavit váš pracovní prostor.
* Následující část obsahuje fragmenty o tom, jak číst a zapisovat pomocí konektoru.

### <a name="batch-reads-from-cosmos-db"></a>Dávkové operace čtení ze služby Cosmos DB

Následující fragment kódu ukazuje, jak vytvořit Spark DataFrame ke čtení ze služby Cosmos DB v PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

A stejném fragmentu kódu v jazyce Scala:

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

### <a name="batch-writes-to-cosmos-db"></a>Batch zapisuje do služby Cosmos DB

Následující fragment kódu ukazuje, jak zapsat datového rámce do Cosmos DB v PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

A stejném fragmentu kódu v jazyce Scala:

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

### <a name="streaming-reads-from-cosmos-db"></a>Datový proud čtení ze služby Cosmos DB

Následující fragment kódu ukazuje, jak se připojit k a číst z Azure Cosmos DB Change Feed.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "ReadChangeFeed" : "true",
  "ChangeFeedQueryName" : "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" : "false",
  "InferStreamSchema" : "true",
  "ChangeFeedCheckpointLocation" : "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
.readStream
.format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
.options(**readConfig)
.load())
```
A stejném fragmentu kódu v jazyce Scala:

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

### <a name="streaming-writes-to-cosmos-db"></a>Streamování zápisy do služby Cosmos DB

Následující fragment kódu ukazuje, jak zapsat datového rámce do Cosmos DB v PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true",
 "WritingBatchSize" : "500",
 "CheckpointLocation" : "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
 .writeStream
 .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
 .outputMode("append")
 .options(**writeconfig)
 .start())
```

A stejném fragmentu kódu v jazyce Scala:

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
Další fragmenty kódu a koncové ukázky, najdete v části [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Práce s konektorem

Můžete vytvořit konektor ze zdroje na Githubu nebo stáhnout uber JAR z Mavenu ve níže uvedených odkazů.

| Spark | Scala | Nejnovější verze |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Použití poznámkových bloků Databricks

Vytvoření knihovny pomocí pracovního prostoru Databricks. podle pokynů v příručce k Azure Databricks > [použití konektoru Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Poznámka: **použití konektoru Spark Azure Cosmos DB** stránka momentálně není aktuální. Místo stažení šest samostatných JAR do šesti různých knihoven, si můžete stáhnout soubor jar uber z mavenu na https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) a nainstalovat jeden soubor jar /.
> 

### <a name="using-spark-cli"></a>Použití sparku – rozhraní příkazového řádku

Pro práci s konektorem pomocí spark – rozhraní příkazového řádku (to znamená `spark-shell`, `pyspark`, `spark-submit`), můžete použít `--packages` parametr s konektoru [souřadnice maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Pomocí poznámkových bloků Jupyter

Pokud používáte poznámkové bloky Jupyter v HDInsight, můžete použít spark magic `%%configure` buňky k zadání souřadnic maven pro tento konektor.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Všimněte si, zařazení `spark.jars.excludes` je specifická pro odebrání potenciální konflikty mezi konektorem, Apache Spark a Livy.

### <a name="build-the-connector"></a>Vytvoření konektoru

V současné době tento konektor projekt používá `maven` sestavit bez závislostí, abyste mohli spustit:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Práce s našimi ukázkami

[Úložiště Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) má následující ukázkové poznámkové bloky a skripty, které můžete vyzkoušet.

* **O včasných Odletech letu pomocí Sparku a Cosmos DB (Praha)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Připojení ke službě Cosmos DB pomocí služby Poznámkový blok HDInsight Jupyter k prezentaci Spark SQL, GraphFrames a předpověď zpoždění letů pomocí kanálů ML Spark.
* **Twitter zdroj s Apache Spark a Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Použití Apache Spark k dotazu Cosmos DB grafy**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Připojení Azure Databricks ke službě Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  pomocí `azure-cosmosdb-spark`.  Propojené zde je také verze Azure Databricks [Poznámkový blok o včasných Odletech letu](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architektura lambda pomocí služby Azure Cosmos DB a HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** : Můžete snížit provozní nároky na údržbu kanály velkých objemů dat pomocí služby Cosmos DB a Spark.

## <a name="more-information"></a>Další informace

Další informace o máme v `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) včetně:

* [Uživatelská příručka k Azure Cosmos DB Spark konektoru](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Příklady agregace](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfigurace a nastavení

* [Konfigurace konektoru Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark je instalace konektoru služby Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Probíhá)
* [Konfigurace dotazů Power BI přímo do služby Azure Cosmos DB pomocí Apache Sparku (Hdinsight)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Řešení potíží

* [Pomocí agregace Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Známé problémy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Výkon

* [Tipy ke zvýšení výkonu](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Dotaz testovacích běhů](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Zápis testovací běhy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Kanál změn

* [Stream zpracování změn pomocí Azure Cosmos DB Change Feed a Apache Sparku](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Ukázky informačního kanálu změn](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Ukázky strukturovaný Stream](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorování

* [Monitorování Sparkových úloh pomocí služby application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Další postup

Pokud jste tak dosud neučinili, stáhněte si Sparku do konektoru služby Azure Cosmos DB z [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark) úložiště GitHub. Prozkoumejte tyto další prostředky v úložišti:

* [Příklady agregace](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkovými bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Můžete také zkontrolovat [Apache Spark SQL, datových rámců a datových sad průvodce](https://spark.apache.org/docs/latest/sql-programming-guide.html)a [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
