---
title: Architektura lambda s Azure Cosmos DB a Apache Spark
description: Tento článek popisuje, jak implementovat architekturu lambda pomocí Azure Cosmos DB, HDInsight a Sparku.
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76719734"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: implementace architektury lambda na platformě Azure 

Architektury lambda umožňují efektivní zpracování dat obrovských datových sad. Architektury lambda využívají dávkové zpracování, zpracování datových proudů a obsluhu vrstvy k minimalizaci latence při dotazování velkých objemů dat. 

K implementaci architektury lambda v Azure můžete kombinovat následující technologie, abyste urychlili analýzu velkých objemů dat v reálném čase:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), první globálně distribuovaná databázová služba pro více modelů. 
* [Apache Spark pro Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), rozhraní pro zpracování, ve kterém běží aplikace pro analýzu velkých objemů dat
* Azure Cosmos DB [změnu kanálu](change-feed.md), který streamuje nová data do dávkové vrstvy pro HDInsight ke zpracování
* [Konektor Spark to Azure Cosmos DB](spark-connector.md)

Tento článek popisuje základy architektury lambda založené na původním designu na více vrstevch a výhodách "přepracované" architektury lambda, která zjednodušuje operace.  

## <a name="what-is-a-lambda-architecture"></a>Co je architektura lambda?
Architektura lambda je obecná a škálovatelná architektura pro zpracování dat odolná proti chybám, která řeší scénáře latence v dávce a rychlosti, jak je popsáno v [Nathan Marz](https://twitter.com/nathanmarz).

![Diagram znázorňující architekturu lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Zdroj: http://lambda-architecture.net/

Základní principy architektury lambda jsou popsány v předchozím diagramu na základě [http://lambda-architecture.net](http://lambda-architecture.net/).

 1. Všechna **data** jsou vložena *do* vrstvy *vrstvy dávky* i *rychlosti*.
 2. **Dávková vrstva** má hlavní datovou sadu (neměnitelnou sadu pouze pro připojení nezpracovaných dat) a předem vypočítá dávková zobrazení.
 3. **Vrstva obsluhující** má pro rychlé dotazy dávkové zobrazení. 
 4. **Vrstva rychlosti** kompenzuje dobu zpracování (do vrstvy obsluhující) a pracuje s nejnovějšími daty.
 5. Všechny dotazy mohou být zodpovězeny sloučením výsledků ze zobrazení Batch a zobrazeními v reálném čase nebo jejich jednorázovým odesláním.

Po dalším čtení budeme moct tuto architekturu implementovat jenom pomocí následujících kroků:

* Cosmos kontejnerů Azure
* Cluster HDInsight (Apache Spark 2,1)
* Konektor Spark [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Rychlost – vrstva

V perspektivě operací údržba dvou datových proudů dat a zajištění správného stavu dat může být složitá Endeavor. Aby bylo možné zjednodušit provoz, využijte [podporu Azure Cosmos DB změn kanálu](change-feed.md) , abyste zachovali stav pro *dávkovou vrstvu* a zároveň Odhalte Azure Cosmos DB protokol změn prostřednictvím *rozhraní API Change feed* pro vaši *rychlost*.  
![Diagram zvýrazňující nová část dat, rychlosti vrstev a hlavní datovou část architektury lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co je důležité v těchto vrstvách:

 1. Všechna **data** jsou vložena *pouze* do Azure Cosmos DB, takže se můžete vyhnout problémům s vícenásobným přetypováním.
 2. **Dávková vrstva** má hlavní datovou sadu (neměnitelnou sadu pouze pro připojení nezpracovaných dat) a předem vypočítá dávková zobrazení.
 3. **Vrstva obsluhující** je popsána v následující části.
 4. **Rychlost** využívá HDInsight (Apache Spark) ke čtení Azure Cosmos DB změn kanálu. Díky tomu můžete uchovávat data i v současném dotazování a zpracování.
 5. Všechny dotazy mohou být zodpovězeny sloučením výsledků ze zobrazení Batch a zobrazeními v reálném čase nebo jejich jednorázovým odesláním.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Příklad kódu: strukturovaný streamování Sparku do Azure Cosmos DBho kanálu změn
Chcete-li spustit rychlý prototyp Azure Cosmos DBho kanálu změn v rámci **vrstvy rychlosti**, můžete ho otestovat pomocí dat Twitteru jako součást [zpracování datových proudů pomocí Azure Cosmos DB změn kanálu a Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) . Pokud chcete přejít na začátek výstupu na Twitteru, přečtěte si ukázku kódu v [kanálu Stream z Twitteru na Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). V předchozím příkladu načítáte data z Twitteru do Azure Cosmos DB a potom můžete nastavit cluster HDInsight (Apache Spark) pro připojení ke kanálu změn. Další informace o nastavení této konfigurace najdete v tématu [Apache Spark nastavení konektoru Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Následující fragment kódu ukazuje, jak nakonfigurovat `spark-shell` tak, aby spouštěl úlohu strukturovaného streamování, která se připojí k informačnímu kanálu Azure Cosmos DB změn, který zkontroluje datový proud na Twitteru v reálném čase a provede průběžný počet intervalů.

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

Kompletní ukázky kódu najdete v tématu [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), včetně:
* [Streamování dotazu z Cosmos DB změny kanálu. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Streamuje dotaz na značky Cosmos DB změny kanálu. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Výstupem tohoto je `spark-shell` konzola, která průběžně spouští strukturované úlohy streamování, která provádí počet intervalů na základě dat Twitteru z Azure Cosmos DBho kanálu změn. Následující obrázek ukazuje výstup úlohy streamu a počty intervalů.

![Výstup streamování ukazující počet intervalů na data Twitteru z kanálu změny Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Další informace o Azure Cosmos DB změně kanálu najdete v tématech:

* [Práce s podporou kanálu změn v Azure Cosmos DB](change-feed.md)
* [Představujeme knihovnu Azure CosmosDB Change feed Processor](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Změny zpracování streamu: Azure CosmosDB Change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Dávkování a obsluha vrstev
Vzhledem k tomu, že jsou nová data načtena do Azure Cosmos DB (kde se pro vrstvu rychlosti používá kanál změn), je v tomto umístění umístěna **hlavní datová sada** (neměnná sada nezpracovaných dat). Od tohoto bodu výše použijte HDInsight (Apache Spark) k provedení předběžného výpočetních funkcí z **vrstvy dávky** do **obsluhy**, jak je znázorněno na následujícím obrázku:

![Diagram zvýrazňující dávkovou vrstvu a obsluhu vrstvy architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co je důležité v těchto vrstvách:

 1. Všechna **data** jsou vložena pouze do Azure Cosmos dB (aby nedocházelo k problémům s vícenásobným přetypováním).
 2. **Dávková vrstva** má hlavní datovou sadu (neměnné, pouze připojovací sadu nezpracovaných dat) uloženou v Azure Cosmos DB. Pomocí HDI Spark můžete předem vypočítat agregace, které se budou ukládat do vypočítaných zobrazení dávek.
 3. **Vrstva obsluhující** je databáze Azure Cosmos s kolekcemi pro hlavní datovou sadu a vypočítané dávkové zobrazení.
 4. **Rychlost** je popsána dále v tomto článku.
 5. Na všechny dotazy se dá odpovědět sloučením výsledků z zobrazení Batch a zobrazení v reálném čase, nebo pomocí jednorázového dotazování.

### <a name="code-example-pre-computing-batch-views"></a>Příklad kódu: předběžné zpracování dávkových zobrazení
Chcete-li předběžně vypočítané zobrazení pro vaši **hlavní datovou sadu** z Apache Spark Azure Cosmos DB, použijte následující fragmenty kódu z poznámkových bloků [lambda](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) s převedením architektury s [architekturou lambda a architekturou lambda znovu navrženou na obsluhu vrstvy](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). V tomto scénáři použijte data Twitteru uložená v Azure Cosmos DB.

Pojďme začít vytvořením konfiguračního připojení k datům Twitteru v Azure Cosmos DB pomocí níže uvedeného kódu PySpark.

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

V dalším kroku spustíte následující příkaz Spark SQL, který určí prvních 10 hashtagů sady tweety. Pro tento dotaz Spark SQL je spuštěný v Jupyter poznámkovém bloku bez výstupního pruhového grafu přímo za tímto fragmentem kódu.

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

![Graf zobrazující počet tweety na hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teď, když máte dotaz, Pojďme ho uložit zpátky do kolekce pomocí konektoru Spark, který uloží výstupní data do jiné kolekce.  V tomto příkladu se pomocí Scala prezentuje připojení. Podobně jako v předchozím příkladu vytvořte připojení konfigurace a uložte Apache Spark dataframe do jiného kontejneru Azure Cosmos.

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

Po zadání příkazu `SaveMode` (s označením `Overwrite` , `Append` zda se má jednat o `tweets_bytags` dokumenty) vytvořte v předchozím příkladu datový rámec podobný dotazu Spark SQL.  `tweets_bytags` Po vytvoření datového rámce jej můžete uložit pomocí `write` metody pomocí dříve zadaného `writeConfig`postupu.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Tento poslední příkaz teď uložil váš datový rámec Spark do nového kontejneru Azure Cosmos; z perspektivy architektury lambda se jedná o **dávkové zobrazení** v rámci **vrstvy obsluhy**.
 
#### <a name="resources"></a>Zdroje a prostředky

Kompletní ukázky kódu najdete v tématu [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) , včetně:
* Rearchitekta architektury lambda – dávková vrstva [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Architektura lambda se znovu[rozipynbá](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) – dávka na obsluhu vrstvy [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | 

## <a name="speed-layer"></a>Rychlost – vrstva
Jak bylo uvedeno výše, umožňuje pomocí knihovny Azure Cosmos DB Change feed Library zjednodušit operace mezi dávkou a rychlostmi vrstev. V této architektuře použijte Apache Spark (přes HDInsight) k provádění *strukturovaných streamických* dotazů na data. Můžete také dočasně zachovat výsledky strukturovaných dotazech streamování, aby k nim měli přístup ostatní systémy.

![Diagram zvýraznění úrovně rychlosti architektury lambda](./media/lambda-architecture/lambda-architecture-speed.png)

Provedete to tak, že vytvoříte samostatný kontejner Azure Cosmos, ve kterém uložíte výsledky strukturovaných streamických dotazů.  Díky tomu budete mít k těmto informacím přístup i další systémy, které nejsou jenom Apache Spark. I když funkce Cosmos DB Time to Live (TTL), můžete nastavit, aby se vaše dokumenty po uplynutí nastavené doby automaticky odstranily.  Další informace o funkci Azure Cosmos DB TTL najdete v tématu [vypršení platnosti dat v kontejnerech Azure Cosmos automaticky s časem až Live](time-to-live.md) .

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

## <a name="lambda-architecture-rearchitected"></a>Architektura lambda: přearchitektný
Jak je uvedeno v předchozích částech, můžete zjednodušit původní architekturu lambda pomocí následujících součástí:
* Azure Cosmos DB
* Knihovna Azure Cosmos DB změn kanálu, aby nedocházelo k vícenásobné přetypování dat mezi dávkou a rychlostmi vrstev
* Apache Spark ve službě HDInsight
* Konektor Spark pro Azure Cosmos DB

![Diagram znázorňující rearchitekturu architektury lambda pomocí Azure Cosmos DB, Sparku a rozhraní API pro změnu kanálu Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

V tomto návrhu potřebujete jenom dvě spravované služby, Azure Cosmos DB a HDInsight. Společně řeší dávku, obsluhu a rychlost vrstev architektury lambda. Tím se zjednoduší nejen operace, ale také tok dat. 
 1. Všechna data jsou vložena do Azure Cosmos DB ke zpracování.
 2. Dávková vrstva má hlavní datovou sadu (neměnné, pouze připojovací sadu nezpracovaných dat) a předběžně vypočítá dávková zobrazení.
 3. Vrstva obsluhující má dávková zobrazení dat pro rychlé dotazy.
 4. Vrstva rychlosti kompenzuje dobu zpracování (do vrstvy obsluhující) a pracuje s nejnovějšími daty.
 5. Všechny dotazy mohou být zodpovězeny sloučením výsledků ze zobrazení Batch a zobrazení v reálném čase.

### <a name="resources"></a>Zdroje a prostředky

* **Nová data**: [datový proud streamování z Twitteru do CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), který je mechanismem pro vložení nových dat do Azure Cosmos DB.
* **Dávková vrstva:** Dávková vrstva se skládá z *hlavní datové sady* (neproměnlivá sada nezpracovaných dat, která je pouze pro připojení) a schopnost předběžně vypočítat dávková zobrazení dat, která jsou vložena do **vrstvy obsluhy**.
   * **Architektura lambda s rearchitektem-Batch** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | Poznámkový blok pro dávkovou *datovou* sadu zobrazení v dávce[se dotazuje](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) .
* **Obsluha vrstvy:** **Vrstva obsluhující** se skládá z předem vypočítaných dat, která mají za následek dávková zobrazení (například agregace, konkrétní průřezy atd.) pro rychlé dotazy.
  * **Architektura lambda znovu navržená – Batch pro obsluhu vrstvy** poznámkového bloku [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) vloží data dávky do vrstvy obsluhy. To znamená, že Spark se dotazuje dávkové kolekce tweety, zpracuje ho a uloží ho do jiné kolekce (vypočítanou dávku).
    * **Rychlost vrstvy:** **Vrstva rychlosti** se skládá ze Sparku, který využívá Azure Cosmos DB změny kanálu ke čtení a jednání okamžitě. Data je také možné uložit do *vypočítaného RT* , aby se ostatní systémy mohly dotazovat na zpracovaná data v reálném čase na rozdíl od provádění samotného dotazu v reálném čase.
  * [Dotaz streamování z skriptu Cosmos DB Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) Scala spustí dotaz streamování z Azure Cosmos DB změn kanálu pro výpočet počtu intervalů z Spark-Shell.
  * [Dotaz na značky streamování ze skriptu Cosmos DB Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) Scala spustí dotaz streamování z Azure Cosmos DB změny kanálu, aby vypočítal počet intervalů značek z Spark-Shell.
  
## <a name="next-steps"></a>Další kroky
Pokud jste to ještě neudělali, Stáhněte si z úložiště GitHubu Azure Cosmos DB pro [Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) konektor Sparku a Prozkoumejte další prostředky v úložišti:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Příklady distribuovaných agregací](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkové bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Ukázky strukturovaného streamování](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Změny ukázek informačních kanálů](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Zpracování datových toků pomocí Azure Cosmos DB změny kanálu a Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Můžete si také projít [průvodce Apache Spark SQL, datovými rámečky a datovými sadami](https://spark.apache.org/docs/latest/sql-programming-guide.html) a článkem [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) .
