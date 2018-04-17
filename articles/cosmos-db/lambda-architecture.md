---
title: Architektura lambda s Azure Cosmos DB a HDInsight (Apache Spark) | Microsoft Docs
description: Tento článek popisuje, jak implementace architektury lambda pomocí Azure Cosmos DB, HDInsight a Spark
keywords: Lambda – architektura
services: cosmos-db
documentationcenter: ''
author: dennyglee
manager: kfile
editor: ''
ms.assetid: 273aeae9-e31c-4a43-b216-5751c46f212e
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: 9c1a8c212fbacde9fbf69fdf9bf72ed2393786db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementace architektury lambda na platformě Azure 

Lambda architektury povolit efektivní zpracování dat velkých datových sad. Lambda architektury použít dávkové zpracování, zpracování datového proudu a vrstva slouží k zajištění minimální latence zahrnutých v dotazování velkých objemů dat. 

Implementace architektury lambda v Azure, můžete kombinovat následující technologie pro urychlení analýzy velkých objemů dat v reálném čase:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), první službě globálně distribuované a více modelech databáze odvětví. 
* [Apache Spark pro Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), zpracování rozhraní, které běží ve velkém rozsahu dat analýza aplikace
* Azure Cosmos DB [změnit informačního kanálu](change-feed.md), které datové proudy nová data do vrstvy batch pro HDInsight ke zpracování
* [Spark pro konektor Azure Cosmos DB](spark-connector.md)

Tento článek popisuje základní informace o architektuře lambda na základě původní vícevrstvých návrhu a výhody architektury "rearchitected" lambda, který zjednodušuje operace.  

Přehled architektury lambda a prostředky, které jsou k dispozici ve vzorku, architektura lambda najdete v následujícím videu:

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>Co je architektura lambda?
Architektura lambda je obecný, škálovatelná a odolný proti chybám data architekturu adresu zpracování dávky a rychlost scénáře s latencí podle [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram zobrazující architektura lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Zdroj: http://lambda-architecture.net/

Základní zásady architektury lambda jsou popsané v předchozím obrázku jako za [ https://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Všechny **data** je vložena do *i* *vrstvy batch* a *rychlost vrstvy*.
 2. **Batch vrstvy** má hlavní datovou sadu (neměnné, připojovacího sadu dat ve formátu raw) a předem vypočítá zobrazení dávky.
 3. **Slouží vrstvy** obsahuje zobrazení dávky pro rychlé dotazy. 
 4. **Rychlost vrstvy** kompenzuje doba zpracování (do vrstvy slouží) a se zabývá pouze nejnovější data.
 5. Všechny dotazy může odpovědět slučovat výsledky z dávky zobrazení a zobrazení v reálném čase nebo je otestováním jednotlivě.

Při další čtení, jsme bude moct implementace této architektury pomocí jenom následující:

* Azure Cosmos DB kolekcí
* Cluster HDInsight (Apache Spark 2.1)
* Spark konektor [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Rychlost vrstvy

Z hlediska operations zachování dvě datových proudů a zajistit správný stav dat může být složité omezené úsilí. Pro zjednodušení operací, využívat [Azure Cosmos DB změnu kanálu podporu](change-feed.md) chcete zachovat stav *vrstvy batch* při odhalil protokol změn Azure Cosmos DB prostřednictvím *změnit kanálu rozhraní API* pro vaše *rychlost vrstvy*.  
![Diagram zvýraznění nová data, rychlost vrstvy a hlavní datovou sadu část architektury lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co je důležité v těchto úrovní:

 1. Všechny **data** bude *pouze* do Azure Cosmos DB, proto se můžete vyhnout problémům s více přetypování.
 2. **Batch vrstvy** má hlavní datovou sadu (neměnné, připojovacího sadu dat ve formátu raw) a předem vypočítá zobrazení dávky.
 3. **Slouží vrstvy** popsané v další části.
 4. **Rychlost vrstvy** využívá HDInsight (Apache Spark) číst informační kanál změnu Azure Cosmos DB. To umožňuje zachovat data i, dotazování a současně zpracovávat.
 5. Všechny dotazy může odpovědět slučovat výsledky z dávky zobrazení a zobrazení v reálném čase nebo je otestováním jednotlivě.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Příklad kódu: Spark strukturovaná streamování o změnu Azure Cosmos DB kanálu
Spustit Rychlé prototyp Azure Cosmos DB změnu kanálu jako součást **rychlost vrstvy**, můžete otestovat pomocí dat služby Twitter jako součást [datového proudu zpracování změny pomocí Azure Cosmos DB změnit kanálu a Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)příklad. Chcete-li rychle začít s prací výstupu Twitter, přečtěte si téma ukázka kódu v [datového proudu kanálu z Twitteru do databáze Cosmos](https://github.com/tknandu/TwitterCosmosDBFeed). Předchozí příklad načítání dat Twitteru do Azure Cosmos DB a vašemu clusteru HDInsight (Apache Spark) pak můžete nastavit pro připojení k změnu informačního kanálu. Další informace o tom, jak nastavit tuto konfiguraci najdete v tématu [Apache Spark pro instalaci konektoru DB Cosmos Azure](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Následující fragment kódu ukazuje, jak nakonfigurovat `spark-shell` ke spuštění strukturovaných streamování úlohy pro připojení databázi Azure Cosmos změňte kanálu, který kontroluje v reálném čase datový proud Twitter, k provedení průběžný počet intervalu.

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

Ukázky kódu dokončení, najdete v části [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), včetně:
* [Streamování dotaz z Feed.scala změnu DB Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streamování značky dotaz z Feed.scala změnu DB Cosmos](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Výstup tohoto objektu je `spark-shell` konzoly, která je neustále spuštěna strukturovaných streamování úlohu, která provádí počet intervalu s daty služby Twitter z Azure Cosmos DB změnu informačního kanálu. Následující obrázek ukazuje výstup úlohy datového proudu a počty interval.

![Streamování výstup zobrazuje počet intervalu pro Twitter data z informačního kanálu změnu Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Další informace o Azure Cosmos DB změnit informačního kanálu najdete v tématu:

* [Práce se změnami kanálu podpory v Azure Cosmos DB](change-feed.md)
* [Představení Azure změnu CosmosDB kanálu procesoru knihovny](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Změn zpracování datového proudu: Informační kanál + Apache Spark změnit Azure CosmosDB](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch a obsluhuje vrstev.
Vzhledem k tomu, že nová data se je načten do Azure Cosmos databáze (kde změnu kanálu se používá pro vrstvu rychlost), je tam, kde **hlavní datovou sadu** nachází (neměnné, připojovacího sadu nezpracovaných dat). Z tohoto bodu dále pomocí HDInsight (Apache Spark) provádět předběžné výpočetních funkcí z **vrstvy batch** k **slouží vrstvy**, jak je znázorněno na následujícím obrázku:

![Diagram zvýraznění vrstvě batch a obsluhující vrstvy architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co je důležité v těchto úrovní:

 1. Všechny **data** je vložena pouze do Azure Cosmos DB (abyste předešli problémům s vícesměrového vysílání).
 2. **Batch vrstvy** má hlavní datovou sadu (neměnné, připojovacího sadu dat ve formátu raw) uložené v Azure Cosmos DB. Pomocí HDI Spark, můžete předem vypočítat vaší agregace se neukládají v zobrazení počítaný batch.
 3. **Slouží vrstvy** je databáze Azure Cosmos DB s kolekcí pro hlavní datovou sadu a počítaný batch zobrazení.
 4. **Rychlost vrstvy** je popsána dále v tomto článku.
 5. Všechny dotazy může odpovědět slučování výsledky z dávky zobrazení a zobrazení v reálném čase nebo je otestováním jednotlivě.

### <a name="code-example-pre-computing-batch-views"></a>Příklad kódu: předem computing batch zobrazení
K předvádí provést předem vypočtené zobrazení na vaše **hlavní datovou sadu** z Apache Spark k databázi Cosmos Azure, použijte následující fragmenty kódu z poznámkových bloků [Lambda architektura Rearchitected - Batch Layer ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) a [Lambda architektura Rearchitected - dávky na obsluhující vrstvy](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). V tomto scénáři použijte Twitter data uložená v Azure Cosmos DB.

Začněme vytvořením konfigurace připojení k Twitteru dat v rámci Azure DB Cosmos pomocí PySpark kód níže.

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

Další, můžeme spustíte následující příkaz Spark SQL k určení prvních 10 hashtags sadu tweetů. Pro tento dotaz Spark SQL jsme používáte to v poznámkového bloku Jupyter bez pruhový graf výstup přímo následující tento fragment kódu.

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

![Graf zobrazující počet tweetů za hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teď, když máte dotaz, umožňuje uložit zpět do kolekce pomocí konektoru Spark uložit výstupní data do jiné kolekce.  V tomto příkladu pomocí Scala prezentují připojení. Podobá se na předchozí příklad, vytvořit konfiguraci připojení k uložit Apache Spark DataFrame do jiné kolekce Azure Cosmos DB.

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

Po zadání `SaveMode` (která udává, jestli se má `Overwrite` nebo `Append` dokumenty), vytvořit `tweets_bytags` DataFrame podobná dotazů Spark SQL v předchozím příkladu.  S `tweets_bytags` DataFrame vytvořen, můžete pomocí `write` metodu pomocí dříve zadaný `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Tento příkaz poslední má uložené vaše Spark DataFrame do nové kolekce Azure Cosmos DB; z hlediska architektury lambda Toto je vaše **dávky zobrazení** v rámci **slouží vrstvy**.
 
#### <a name="resources"></a>Zdroje a prostředky

Ukázky kódu dokončení, najdete v části [azure-cosmosdb-spark/lambda/samples](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) včetně:
* Lambda architektura Rearchitected - Batch Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda architektura Rearchitected - Batch obsluhující vrstvy [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Rychlost vrstvy
Jako výše uvedené, pomocí Cosmos DB změnu kanálu knihovny Azure umožňuje zjednodušují provoz mezi vrstvami batch a rychlost. V této architektuře pomocí Apache Spark (prostřednictvím HDInsight) provádět *strukturovaná streamování* dotazy na data. Můžete také dočasně zachovat výsledky vaší strukturovaného streamované dotazy, aby ostatní systémy můžete přístup k těmto datům.

![Diagram zvýraznění rychlost vrstvy architektury lambda](./media/lambda-architecture/lambda-architecture-speed.png)

K tomuto účelu vytvořte kolekci Azure Cosmos DB samostatné uložte výsledky vaší strukturovaného streamované dotazy.  To umožňuje, abyste měli přístup ostatní systémy tyto informace nejen Apache Spark. Funkce Time-to-Live (TTL) Cosmos DB s také můžete nakonfigurovat vaše dokumenty, které se budou automaticky odstraněna po nastavte dobu trvání.  Další informace o funkci Azure Cosmos DB TTL najdete v tématu [vyprší platnost dat v kolekcích Azure Cosmos DB automaticky s Hodnota time to live](time-to-live.md)

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
Jak jsme uvedli v předchozí části, můžete zjednodušit původní architektura lambda pomocí následující součásti:
* Azure Cosmos DB
* Azure Cosmos DB změnu kanálu knihovnu, která se nemuseli vícesměrovým vysíláním data mezi vrstvami batch a rychlosti
* Apache Spark v HDInsight
* Konektor Spark pro Azure Cosmos DB

![Diagram zobrazující rearchitecture architektury lambda pomocí Azure Cosmos DB, Spark a Cosmos DB změnu kanálu rozhraní API služby Azure](./media/lambda-architecture/lambda-architecture-re-architected.png)

V tomto návrhu stačí pouze dvě spravované služby, databázi Cosmos Azure a prostředí HDInsight. Společně se adres batch, slouží a rychlost vrstvy architektury lambda. Tato funkce zjednodušuje pouze operace, ale také datový tok. 
 1. Všechna data, je vložena do Azure Cosmos DB pro zpracování
 2. Vrstva batch má hlavní datovou sadu (neměnné, připojovacího sadu dat ve formátu raw) a předem vypočítá zobrazení dávky
 3. Vrstva slouží má batch zobrazení dat pro rychlé dotazy.
 4. Vrstva rychlost kompenzuje doba zpracování (do vrstvy slouží) a se zabývá pouze nejnovější data.
 5. Všechny dotazy můžete odpovědi sloučením výsledky z batch a v reálném čase zobrazení.

### <a name="resources"></a>Zdroje a prostředky

 * **Nová data**: [datového proudu informační kanál z Twitteru CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), což je mechanizmus pro vkládání nová data do Azure Cosmos DB.
 * **Vrstva batch:** vrstvě batch se skládá z *hlavní datovou sadu* (neměnné, připojovacího sadu nezpracovaných dat) a schopnost předem výpočetní batch zobrazení dat, která se instaluje do **slouží vrstvy** .
    * **Lambda architektura Rearchitected - Batch Layer** poznámkového bloku [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) dotazy *hlavní datovou sadu* sada zobrazení dávky.
 * **Vrstva slouží:** **slouží vrstvy** se skládá z předvypočítaných dat, což vede k zobrazení batch (např. agregace, konkrétní průřezy atd.) pro rychlé dotazy.
    * **Lambda architektura Rearchitected - Batch obsluhující vrstvy** poznámkového bloku [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) nabízených oznámení data dávky k vrstvě slouží; to znamená, Spark. dotazuje batch kolekce tweetů, procesy a ukládá je do jiné kolekce (počítaný batch).
* **Rychlost vrstvy:** **rychlost vrstvy** se skládá z Spark využitím Azure Cosmos DB změnu informační kanál čtení a provádění akcí na okamžitě. Data můžete uložit také *počítaný RT* tak, aby ostatní systémy můžete dotazovat zpracování dat v reálném čase a spuštěna v reálném čase dotaz sami.
    * [Streamování dotazu z Cosmos DB změnit kanálu](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala skript provede streamování dotaz z Azure Cosmos DB změnu kanálu vypočítat počet intervalu z prostředí spark.
    * [Streamování značky dotazu z Cosmos DB změnit kanálu](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala skript provede streamování dotaz z Azure Cosmos DB změnu kanálu vypočítat počet intervalu značky z prostředí spark.
  
## <a name="next-steps"></a>Další postup
Pokud jste tak ještě neučinili, stáhněte Spark pro Azure Cosmos DB konektor z [azure. cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) úložiště GitHub a seznamte se s další prostředky v úložišti:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Příklady distribuované agregace](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkových bloků](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturované streamování ukázky](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Změna kanálu ukázky](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Zpracování změny pomocí Azure Cosmos DB změnu kanálu a Apache Spark datového proudu](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Můžete také zkontrolovat [Apache Spark SQL, průvodce datové sady a DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) a [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
