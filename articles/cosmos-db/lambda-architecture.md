---
title: Architektury lambda s využitím služby Azure Cosmos DB a HDInsight (Apache Spark)
description: Tento článek popisuje, jak implementace architektury lambda pomocí služby Azure Cosmos DB, HDInsight a Spark
keywords: Architektura lambda
services: cosmos-db
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: b6831e9c6b679d2fd4fa585331213290d67068c2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084007"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementace architektury lambda na platformě Azure 

Architektury lambda umožňují efektivní zpracování dat z obrovských datových sad. Architektury lambda minimalizovat latenci při dotazování velkých objemů dat pomocí dávkové zpracování, zpracování datových proudů a obslužné vrstvy. 

Implementace architektury lambda v Azure, můžete kombinovat následující technologie k posílení analýzy velkých objemů dat v reálném čase:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), první globálně distribuovaná a vícemodelová databázová služba v oboru. 
* [Apache Spark pro Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), platformu, na kterém běží velkých objemů dat analytických aplikací
* Azure Cosmos DB [kanálu změn](change-feed.md), která nová data k vrstvě služby batch pro HDInsight ke zpracování datových proudů
* [Spark je konektor služby Azure Cosmos DB](spark-connector.md)

Tento článek popisuje základní informace o architektury lambda podle původního návrhu více vrstev a výhody architektury lambda "rearchitected", která zjednodušuje operace.  

## <a name="what-is-a-lambda-architecture"></a>Co je architektura lambda?
Architektury lambda je obecný, škálovatelná, a odolné proti chybám data architekturu adresu zpracování služby batch a rychlosti latence scénáře, jak je popsáno v [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram znázorňující architektury lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Zdroj: http://lambda-architecture.net/

Základní principy architektury lambda jsou popsané na předchozím obrázku jako za [ http://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Všechny **data** odesílají do *obě* *dávková vrstva* a *rychlostní vrstva*.
 2. **Dávková vrstva** má hlavní datové sady (set neměnné a jen pro připojení nezpracovaných dat) a předem vypočítá dávkové zobrazení.
 3. **Obslužné vrstvy** má dávkové zobrazení pro rychlé zpracování dotazů. 
 4. **Rychlostní vrstva** kompenzuje doba zpracování (do vrstvy obsluhující) a se zabývá jenom nejnovější data.
 5. Všechny dotazy můžete zodpoví sloučení výsledků z dávkové zobrazení v reálném čase a zobrazení příkazu ping je jednotlivě.

Na další čtení jsme bude možné implementaci této architektury pomocí jenom následující:

* Azure Cosmos DB kolekcí
* Cluster HDInsight (Apache Sparku 2.1)
* Konektor Spark [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Rychlostní vrstva

Z hlediska operace udržování dva proudy dat při zajištění správných stavů data může být složité zařízeními. Pro zjednodušení operací, využití [podpora kanálu změn databáze Azure Cosmos DB](change-feed.md) zachovat stav *dávková vrstva* při odhalení protokol změn služby Azure Cosmos DB prostřednictvím *změnit informační kanál rozhraní API* pro vaše *rychlostní vrstva*.  
![Diagram zvýraznění nová data, rychlostní vrstva a datová sada hlavní část architektury lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co je důležité pro tyto vrstvy:

 1. Všechny **data** je vloženo *pouze* do služby Azure Cosmos DB, proto se můžete vyhnout více přetypování problémy.
 2. **Dávková vrstva** má hlavní datové sady (set neměnné a jen pro připojení nezpracovaných dat) a předem vypočítá dávkové zobrazení.
 3. **Obslužné vrstvy** je popsáno v další části.
 4. **Rychlostní vrstva** využívá HDInsight (Apache Spark) ke čtení kanálu změn služby Azure Cosmos DB. Umožňuje k uchování vašich dat. jde o dotazování a zpracovat souběžně.
 5. Všechny dotazy můžete zodpoví sloučení výsledků z dávkové zobrazení v reálném čase a zobrazení příkazu ping je jednotlivě.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Příklad: Spark strukturované streamování do kanálu změn Azure Cosmos DB
Spuštění rychlého prototypu kanálu jako součást změn služby Azure Cosmos DB **rychlostní vrstva**, můžete otestovat horizontálně s využitím dat Twitteru jako součást [Stream zpracování změn pomocí Azure Cosmos DB změnit informační kanál a Apache Sparku](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)příklad. K nastartování výstupu Twitter, najdete v ukázce kódu v [Stream kanálu z Twitteru do služby Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). Z předchozího příkladu načítání dat Twitteru do služby Azure Cosmos DB a váš cluster HDInsight (Apache Spark) pak můžete nastavit pro připojení k kanálu změn. Další informace o tom, jak nastavit tuto konfiguraci najdete v tématu [Apache Spark pro Azure Cosmos DB konektor nastavení](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Následující fragment kódu ukazuje, jak nakonfigurovat `spark-shell` ke spuštění strukturované streamování úlohy k připojení do Azure Cosmos DB změnit informační kanál, který kontroly v reálném čase datový proud Twitter, provádět počty interval.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Kompletní příklady najdete v části [azure cosmos DB – spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), včetně:
* [Streamování dotazu z Feed.scala změn služby Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streamování značky dotaz z Feed.scala změn služby Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Výstup tohoto objektu je `spark-shell` konzolu, která je neustále spuštěna strukturované streamování úlohu, která provede interval započítávají dat Twitteru z kanálu změn služby Azure Cosmos DB. Následující obrázek ukazuje výstup úlohy služby stream a interval se počítá.

![Streamování výstup zobrazuje počet intervalu proti dat Twitteru z kanálu změn služby Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Další informace o službě Azure Cosmos DB kanálu změn naleznete v tématu:

* [Práce s změnu podpora kanálu ve službě Azure Cosmos DB](change-feed.md)
* [Představujeme Azure cosmos DB změnit informační kanál knihovny procesoru](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Stream zpracování změn: Kanál změn služby Azure cosmos DB a Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch a obsluhující vrstvy
Protože nová data se načtou do služby Azure Cosmos DB (kde kanál změn se používá pro rychlostní vrstva), to je tam, kde **hlavní datovou sadu** nachází (neměnné a jen pro připojení sady nezpracovaných dat). Od této chvíle a vyšší pomocí HDInsight (Apache Spark) k provedení před výpočetních funkcí **dávková vrstva** k **obslužné vrstvy**, jak je znázorněno na následujícím obrázku:

![Diagram zvýraznění dávková vrstva a obslužné vrstvy architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co je důležité pro tyto vrstvy:

 1. Všechny **data** odesílají pouze do služby Azure Cosmos DB (abyste předešli problémům vícesměrového vysílání s).
 2. **Dávková vrstva** má hlavní datové sady (set neměnné a jen pro připojení nezpracovaných dat) uložené ve službě Azure Cosmos DB. Pomocí Hdinsight Spark, můžete předem výpočetní agregace v zobrazení počítané služby batch.
 3. **Obslužné vrstvy** je databáze Azure Cosmos DB s kolekcemi hlavní datové sady a vypočítat dávkové zobrazení.
 4. **Rychlostní vrstva** je popsána dále v tomto článku.
 5. Všechny dotazy můžete zodpoví sloučení výsledků služby batch a v reálném čase zobrazení, nebo je jednotlivě příkazu ping.

### <a name="code-example-pre-computing-batch-views"></a>Příklad: předem computingu dávkové zobrazení
K tom, jak spustit předem vypočítané zobrazení na prezentaci vaší **hlavní datovou sadu** z Apache Spark pro Azure Cosmos DB, použijte následující fragmenty kódu z poznámkových bloků [Rearchitected architektury Lambda - dávková vrstva ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) a [Rearchitected architektury Lambda - Batch se, že vrstvy](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). V tomto scénáři použijte Twitteru data uložená ve službě Azure Cosmos DB.

Začněme vytvořením konfigurace připojení k Twitteru datům ve službě Azure Cosmos DB pomocí níže uvedeného kódu PySpark.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Další, můžeme spustit následující příkaz k určení hashtagy prvních 10 sadu tweetů Spark SQL. Pro tento dotaz Spark SQL máme spuštěnou v poznámkovém bloku Jupyter bez pruhový graf výstup přímo po tento fragment kódu.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Graf zobrazující počet tweetů, které za hashtagem](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teď, když máte dotaz, uložte ji zpět do kolekce pomocí konektoru Spark uložení výstupní data do jiné kolekce.  V tomto příkladu pomocí Scala a představte připojení. Podobně jako v předchozím příkladu, vytvořit konfiguraci připojení k Apache Spark DataFrame uložit do jiné kolekce Azure Cosmos DB.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Po zadání `SaveMode` (označující, jestli se má `Overwrite` nebo `Append` dokumenty), vytvořit `tweets_bytags` DataFrame podobný dotaz Spark SQL v předchozím příkladu.  S `tweets_bytags` vytvořen datový rámec, můžete ji pomocí Uložit `write` metodu pomocí dříve zadaná `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Tento příkaz poslední uchránila Spark DataFrame do nové kolekce Azure Cosmos DB; z pohledu architektury lambda, toto je vaše **dávkové zobrazení** v rámci **obslužné vrstvy**.
 
#### <a name="resources"></a>Zdroje a prostředky

Kompletní příklady najdete v části [azure cosmos DB – spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) včetně:
* Rearchitected architektury lambda - dávková vrstva [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Rearchitected architektury lambda - dávkové vrstvě obsluhující [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Rychlostní vrstva
Výše uvedené, pomocí Azure Cosmos DB změnit informační kanál Library umožňuje zjednodušují provoz mezi vrstvami služby batch a rychlost. V této architektuře pomocí Apache Sparku (prostřednictvím HDInsight) provádět *strukturované streamování* dotazy na data. Můžete také dočasně zachovat výsledky vašich dotazů strukturované streamování, tak jinými systémy můžete přístup k těmto datům.

![Diagram zvýraznění rychlostní vrstva architektury lambda](./media/lambda-architecture/lambda-architecture-speed.png)

K tomuto účelu vytvořte samostatné kolekce Azure Cosmos DB k ukládání výsledků strukturované streamování dotazy.  To umožňuje, abyste měli přístup ostatní systémy tyto informace nejen Apache Spark. Také pomocí služby Cosmos DB Time-to-Live (TTL) funkce, můžete nakonfigurovat dokumentů, které mají být automaticky odstraněna po nastavenou dobu trvání.  Další informace o funkci Azure Cosmos DB TTL najdete v tématu [vypršení platnosti dat v kolekcích Azure Cosmos DB automaticky s časem TTL](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Architektura lambda: Rearchitected
Jak je uvedeno v předchozí části, můžete zjednodušit původní architektury lambda pomocí následující komponenty:
* Azure Cosmos DB
* Azure Cosmos DB změnit informační kanál knihovně nemuseli všesměrového vysílání dat mezi vrstvami služby batch a rychlost
* Apache Spark v HDInsight
* Konektor Spark pro Azure Cosmos DB

![Diagram znázorňující rearchitecture architektury lambda pomocí služby Azure Cosmos DB, Spark a Azure Cosmos DB změnit informační kanál API](./media/lambda-architecture/lambda-architecture-re-architected.png)

S tímto návrhem potřebujete jenom dvě spravované služby Azure Cosmos DB a HDInsight. Společně se týkají služby batch, poskytování obsahu a rychlost vrstvy architektury lambda. To zjednodušuje pouze operace, ale také datový tok. 
 1. Všechna data odesílají do služby Azure Cosmos DB pro zpracování
 2. Dávková vrstva obsahuje hlavní datové sady (set neměnné a jen pro připojení nezpracovaných dat) a předem vypočítá dávkové zobrazení
 3. Přidává do obslužné vrstvy obsahuje dávkové zobrazení dat pro rychlé zpracování dotazů.
 4. Rychlostní vrstva kompenzuje doba zpracování (do vrstvy obsluhující) a se zabývá jenom nejnovější data.
 5. Všechny dotazy můžete zodpoví sloučení výsledků ze služby batch a v reálném čase zobrazení.

### <a name="resources"></a>Zdroje a prostředky

 * **Nová data**: [datového proudu kanál z Twitteru do služby cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed), což je mechanismus pro zápis nových dat do služby Azure Cosmos DB.
 * **Dávková vrstva:** dávková vrstva se skládá z *hlavní datovou sadu* (neměnné a jen pro připojení sady nezpracovaných dat) a schopnost předem compute dávkové zobrazení dat, které jsou vloženy do **obslužné vrstvy** .
    * **Rearchitected architektury Lambda - dávková vrstva** Poznámkový blok [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) dotazy *hlavní datovou sadu* sadu dávkové zobrazení.
 * **Obslužné vrstvy:** **obslužné vrstvy** se skládá z předvypočítaných dat, což vede k zobrazení služby batch (např. agregace, konkrétní průřezy atd.) pro zajištění rychlých dotazů.
    * **Rearchitected architektury Lambda - dávkové vrstvě obsluhující** Poznámkový blok [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) odesílá batch data do obslužné vrstvy; to znamená ve Sparku dotazuje služby batch kolekci tweetů, procesy a uloží je do jiné kolekce (vypočítané batch).
* **Rychlostní vrstva:** **rychlostní vrstva** se skládá z Sparku s využitím kanálu ke čtení a okamžitě reagovat na změn služby Azure Cosmos DB. Data můžou být uložené taky na *vypočítané RT* tak, aby ostatní systémy můžete dotazovat zpracovaných dat v reálném čase na rozdíl od spuštění v reálném čase dotazování sami.
    * [Streamování dotazu z Cosmos DB změnit kanálu](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala skript provede streamování dotaz z kanálu pro výpočet počet intervalu z prostředí sparku změn služby Azure Cosmos DB.
    * [Streamování dotazu značky z Cosmos DB změnit kanálu](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala skript provede streamování dotaz z kanálu pro výpočet intervalu počet značek z prostředí sparku změn služby Azure Cosmos DB.
  
## <a name="next-steps"></a>Další postup
Pokud jste tak dosud neučinili, stáhněte si Sparku do konektoru služby Azure Cosmos DB z [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark) úložiště GitHub a další materiály v úložišti:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Příklady distribuované agregace](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkovými bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturované streamování ukázky](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Ukázky kanálu změn](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Stream zpracovávat změny pomocí Azure Cosmos DB Change Feed a Apache Sparku](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Můžete také zkontrolovat [Apache Spark SQL, datových rámců a datových sad průvodce](https://spark.apache.org/docs/latest/sql-programming-guide.html) a [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
