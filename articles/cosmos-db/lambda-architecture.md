---
title: Architektura Lambda s Azure Cosmos DB a Apache Spark
description: Tento článek popisuje, jak implementovat architekturu lambda pomocí Azure Cosmos DB, HDInsight a Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719734"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementace architektury lambda na platformě Azure 

Architektury Lambda umožňují efektivní zpracování dat rozsáhlých datových sad. Architektury Lambda používají dávkové zpracování, zpracování datových proudů a vrstvu obsluhy k minimalizaci latence při dotazování velkých objemů dat. 

Chcete-li implementovat architekturu lambda v Azure, můžete kombinovat následující technologie a urychlit analýzu velkých objemů dat v reálném čase:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), první globálně distribuovaná databázová služba s více modely v oboru. 
* [Apache Spark pro Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), architektura zpracování, která spouští rozsáhlé aplikace pro analýzu dat
* [Kanál změn](change-feed.md)Azure Cosmos DB , který streamuje nová data do dávkové vrstvy pro HDInsight ke zpracování
* Konektor [Spark to Azure Cosmos DB](spark-connector.md)

Tento článek popisuje základy architektury lambda založené na původním vícevrstvém návrhu a výhodách architektury "rearchitected" lambda, která zjednodušuje operace.  

## <a name="what-is-a-lambda-architecture"></a>Co je architektura lambda?
Architektura lambda je obecná, škálovatelná a chybově odolná architektura zpracování dat, která řeší scénáře dávkové a latence rychlosti, jak je popsáno [Nathanem Marzem](https://twitter.com/nathanmarz).

![Diagram znázorňující architekturu lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Zdroj: http://lambda-architecture.net/

Základní principy architektury lambda jsou popsány v předchozím [http://lambda-architecture.net](http://lambda-architecture.net/)diagramu podle .

 1. Všechna **data** jsou zasunuta *jak* do *dávkové vrstvy,* tak *do vrstvy rychlosti*.
 2. **Dávková vrstva** má hlavní datovou sadu (neměnná, pouze připojitelná sada nezpracovaných dat) a předem vypočítá zobrazení dávky.
 3. **Servírovací vrstva** má dávková zobrazení pro rychlé dotazy. 
 4. **Rychlostní vrstva** kompenzuje dobu zpracování (do servírovací vrstvy) a zabývá se pouze nedávnými daty.
 5. Na všechny dotazy lze odpovědět sloučením výsledků z dávkových zobrazení a zobrazení v reálném čase nebo jejich individuálním dotazem.

Po dalším čtení budeme schopni implementovat tuto architekturu pouze pomocí následujících možností:

* Kontejnery Azure Cosmos
* Cluster HDInsight (Apache Spark 2.1)
* Konektor pro jiskru [1.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Rychlostní vrstva

Z hlediska operací může být udržování dvou datových proudů dat při zajištění správného stavu dat složité. Chcete-li zjednodušit operace, využijte [podporu kanálu změn Azure Cosmos DB](change-feed.md) k zachování stavu *dávkové vrstvy* a zároveň odhalte protokol změn Azure Cosmos DB prostřednictvím *rozhraní API kanálu změn* pro vaši *vrstvu rychlosti*.  
![Diagram zvýrazňující novou datovou vrstvu, vrstvu rychlosti a hlavní datovou sadu architektury lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co je důležité v těchto vrstvách:

 1. Všechna **data** se zasouvá *jenom* do Azure Cosmos DB, takže se můžete vyhnout problémům s více castingy.
 2. **Dávková vrstva** má hlavní datovou sadu (neměnná, pouze připojitelná sada nezpracovaných dat) a předem vypočítá zobrazení dávky.
 3. **Vrstva porce** je popsána v další části.
 4. **Rychlostní vrstva** využívá HDInsight (Apache Spark) ke čtení kanálu změn Azure Cosmos DB. To umožňuje zachovat data, stejně jako dotaz a zpracování současně.
 5. Na všechny dotazy lze odpovědět sloučením výsledků z dávkových zobrazení a zobrazení v reálném čase nebo jejich individuálním dotazem.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Příklad kódu: Strukturované streamování Spark do kanálu změn Azure Cosmos DB
Chcete-li spustit rychlý prototyp zdroje změn Azure Cosmos DB jako součást **vrstvy rychlosti**, můžete otestovat pomocí dat Twitter jako součást [změny zpracování datového proudu pomocí Azure Cosmos DB Change Feed a Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) příklad. Chcete-li nastartovat výstup Twitteru, podívejte se na ukázku kódu v [kanálu Stream z Twitteru do Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). S předchozím příkladem načítáte data z Twitteru do Azure Cosmos DB a pak můžete nastavit cluster HDInsight (Apache Spark) pro připojení k kanálu změn. Další informace o nastavení této konfigurace najdete v [tématu Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Následující fragment kódu ukazuje, jak `spark-shell` nakonfigurovat spuštění strukturované úlohy streamování pro připojení k kanálu změn Azure Cosmos DB, který kontroluje datový proud Twitter v reálném čase, aby provedl počet spuštěných intervalů.

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

Úplné ukázky kódu naleznete [v tématu azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), včetně:
* [Streamování dotazu z Kanálu změn Cosmos DB.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Dotaz na změnu datových proudů značek z kanálu změn Cosmos DB.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Výstupem tohoto je `spark-shell` konzola, která nepřetržitě spouští strukturované streamování úlohy, která provádí interval počítat s daty Twitter z kanálu změn Azure Cosmos DB. Následující obrázek znázorňuje výstup úlohy datového proudu a počítá interval.

![Výstup streamování zobrazující počet intervalů s daty twitteru z informačního kanálu změn Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Další informace o kanálu změn Azure Cosmos DB najdete v tématu:

* [Práce s podporou kanálu změn v Azure Cosmos DB](change-feed.md)
* [Představujeme knihovnu procesorů kanálu změn Azure CosmosDB](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Změny zpracování datového proudu: Kanál změn Azure CosmosDB + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Dávkové a servírovací vrstvy
Vzhledem k tomu, že nová data se načtou do Azure Cosmos DB (kde se kanál změn používá pro vrstvu rychlosti), je to místo, kde se nachází **hlavní datová sada** (neměnná, pouze připojená sada nezpracovaných dat). Od tohoto okamžiku použijte HDInsight (Apache Spark) k provádění předvýpočetních funkcí z **dávkové vrstvy** do **servírovací vrstvy**, jak je znázorněno na následujícím obrázku:

![Diagram zvýrazňující dávkovou vrstvu a servírovací vrstvu architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co je důležité v těchto vrstvách:

 1. Všechna **data** se zasouvá jenom do Azure Cosmos DB (aby se zabránilo problémům s více datovými vrstvami).
 2. **Dávková vrstva** má hlavní datovou sadu (neměnná, jenom jenom nezpracovaná data) uloženou v Azure Cosmos DB. Pomocí HDI Spark můžete předem vypočítat agregace, které mají být uloženy ve vypočítaných dávkových zobrazeních.
 3. **Vrstva obsluhy** je databáze Azure Cosmos s kolekcemi pro hlavní datovou sadu a vypočítané dávkové zobrazení.
 4. **Úroveň rychlosti** je popsána dále v tomto článku.
 5. Všechny dotazy mohou být zodpovězeny sloučením výsledků z dávkových zobrazení a zobrazení v reálném čase nebo jejich individuálním dotazem.

### <a name="code-example-pre-computing-batch-views"></a>Příklad kódu: Dávková zobrazení před výpočtem
Chcete-li předvést, jak spustit předem vypočítaná zobrazení oproti **vaší hlavní datové sadě** z Apache Spark do Azure Cosmos DB, použijte následující fragmenty kódu z rekonstruovaných architektur [Lambda Architecture – batch layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) a [lambda architecture rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). V tomto scénáři použijte twitterová data uložená v Azure Cosmos DB.

Začněme vytvořením konfiguračního připojení k datům Twitteru v rámci Azure Cosmos DB pomocí kódu PySpark níže.

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

Dále spusťme následující příkaz Spark SQL, abyste určili prvních 10 hashtagů sady tweetů. Pro tento dotaz Spark SQL jsme spuštění tohoto v poznámkovém bloku Jupyter bez výstupní pruhový graf přímo po tento fragment kódu.

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

![Graf znázorňující počet tweetů na hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teď, když máte dotaz, uložte ho zpět do kolekce pomocí konektoru Spark k uložení výstupních dat do jiné kolekce.  V tomto příkladu použijte Scala k prezentaci připojení. Podobně jako v předchozím příkladu vytvořte konfigurační připojení pro uložení datového rámce Apache Spark do jiného kontejneru Azure Cosmos.

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

Po zadání `SaveMode` (označující, `Overwrite` zda `Append` nebo dokumenty), vytvořte `tweets_bytags` datový rámec podobný dotazu Spark SQL v předchozím příkladu.  Po `tweets_bytags` vytvoření datového rámce jej můžete `write` uložit pomocí `writeConfig`metody pomocí dříve zadaného .

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Tento poslední příkaz teď uložil váš Datový rámec Spark do nového kontejneru Azure Cosmos; z hlediska architektury lambda je toto **dávkové zobrazení** v rámci **servírovací vrstvy**.
 
#### <a name="resources"></a>Prostředky

Úplné ukázky kódu najdete [v tématu azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) včetně:
* Lambda Architektura Rearchitected - Dávková vrstva [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architektura Rearchitected - Dávka do porce vrstvy [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Rychlostní vrstva
Jak již bylo uvedeno, pomocí knihovny zdrojů změn DB Azure Cosmos umožňuje zjednodušit operace mezi dávkovými a rychlostními vrstvami. V této architektuře použijte Apache Spark (přes HDInsight) k provádění *strukturovaných dotazů streamování* proti datům. Můžete také chtít dočasně zachovat výsledky strukturovaných dotazů streamování, aby k těmto datům měly přístup jiné systémy.

![Diagram zvýrazňující rychlostní vrstvu architektury lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Chcete-li to provést, vytvořte samostatný kontejner Azure Cosmos pro uložení výsledků strukturovaných dotazů streamování.  To vám umožní mít jiné systémy přístup k těmto informacím nejen Apache Spark. Stejně tak s funkcí Cosmos DB Time-to-Live (TTL) můžete nakonfigurovat dokumenty tak, aby byly automaticky odstraněny po nastavené době trvání.  Další informace o funkci Azure Cosmos DB TTL najdete v [tématu Vypršení platnosti dat v kontejnerech Azure Cosmos automaticky s časem na život.](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Lambda architektura: Rearchitected
Jak je uvedeno v předchozích částech, můžete zjednodušit původní architekturu lambda pomocí následujících komponent:
* Azure Cosmos DB
* Knihovna zdrojů změn Azure Cosmos DB, abyste se vyhnuli nutnosti vícenásobného přetypovat data mezi dávkovými a rychlostními vrstvami
* Apache Spark na HDInsight
* Konektor Spark pro Azure Cosmos DB

![Diagram znázorňující přearchitekturu architektury lambda pomocí Azure Cosmos DB, Spark a rozhraní API kanálu změn DB Azure Cosmos](./media/lambda-architecture/lambda-architecture-re-architected.png)

S tímto návrhem potřebujete jenom dvě spravované služby, Azure Cosmos DB a HDInsight. Společně řeší dávkové, servírovací a rychlostní vrstvy architektury lambda. To zjednodušuje nejen operace, ale také tok dat. 
 1. Všechna data se zasouvá do Azure Cosmos DB pro zpracování
 2. Dávková vrstva má hlavní datovou sadu (neměnná, pouze připojitelná sada nezpracovaných dat) a předem vypočítá zobrazení dávky.
 3. Servírovací vrstva má dávková zobrazení dat pro rychlé dotazy.
 4. Rychlostní vrstva kompenzuje dobu zpracování (do servírovací vrstvy) a zabývá se pouze nedávnými daty.
 5. Všechny dotazy mohou být zodpovězeny sloučením výsledků z dávkových zobrazení a zobrazení v reálném čase.

### <a name="resources"></a>Prostředky

* **Nová data**: Datový [proud kanálu z Twitteru do CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), což je mechanismus pro nabízení nových dat do Azure Cosmos DB.
* **Dávková vrstva:** Dávková vrstva se skládá z *hlavní datové sady* (neměnná, pouze připojitelná sada nezpracovaných dat) a schopnostpředem vypočítat dávková zobrazení dat, která jsou posunuta do **servírovací vrstvy**.
   * **Lambda Architektura Rearchitected - Batch Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) dotazy hlavní *datové sady* dávkových zobrazení.
* **Servírovací vrstva:** **Vrstva obsluhy** se skládá z předem vypočítaných dat, která vedou k dávkovým zobrazením (například agregace, konkrétní průřezy atd.) pro rychlé dotazy.
  * **Lambda Architektura Rearchitected - Batch to Serving Layer** notebook ipynb | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) tlačí dávková data do servírovací [vrstvy;](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) To znamená, že Spark dotazuje dávkovou kolekci tweetů, zpracovává ji a ukládá do jiné kolekce (vypočítaná dávka).
    * **Rychlostní vrstva:** **Vrstva rychlosti** se skládá ze Spark s využitím zdroje změn Azure Cosmos DB ke čtení a okamžitému zasuňování. Data lze také uložit do *vypočítané RT* tak, aby ostatní systémy můžete dotaz na zpracovaná data v reálném čase na rozdíl od spuštění dotazu v reálném čase sami.
  * [Streaming dotaz z Cosmos DB Změna Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala skript spustí streamování dotazz kanálu změny Azure Cosmos DB vypočítat počet intervalů z jiskry-prostředí.
  * [Streaming Tagy dotaz z Cosmos DB Změna Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala skript spustí streamování dotaz z kanálu změny Azure Cosmos DB vypočítat interval počet značek z jiskry-prostředí.
  
## <a name="next-steps"></a>Další kroky
Pokud jste to ještě neudělali, stáhněte si konektor Spark do Azure Cosmos DB z úložiště GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) a prozkoumejte další prostředky v úložišti:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Příklady distribuovaných agregací](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkové bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Ukázky strukturovaného streamování](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Změna ukázky informačního kanálu](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Streamování změn zpracování pomocí kanálu změn Azure Cosmos DB a Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Můžete také zkontrolovat [Apache Spark SQL, DataFrames a Datasets Guide a](https://spark.apache.org/docs/latest/sql-programming-guide.html) Apache Spark na Azure [HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
