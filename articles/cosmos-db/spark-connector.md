---
title: Připojení k Azure Cosmos DB Apache Spark | Microsoft Docs
description: Pomocí tohoto kurzu se dozvíte o konektoru Azure Cosmos DB Spark, který vám umožní připojit Apache Spark pro Azure DB Cosmos k provedení distribuované věd agregacemi a daty na více klientů globálně distribuované databázový systém od společnosti Microsoft to je navržené pro cloud.
keywords: Apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: ramkris
ms.openlocfilehash: 47abd2f3534a0f3418bc09e19a61e222093e7099
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960470"
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Urychlit analýzy velkých objemů dat v reálném čase s Spark pro konektor Azure Cosmos DB

Spark pro Azure Cosmos DB konektor umožňuje Azure DB Cosmos tak, aby fungoval jako vstupní zdroj nebo výstupní jímku pro úlohy Apache Spark. Připojení [Spark](http://spark.apache.org/) k [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) zrychluje schopnost řešení fast přesouvání dat vědecké účely problémů, kde můžete použít Azure Cosmos DB k rychlému zachovat a zadávat dotazy na data. Spark pro konektor Azure Cosmos DB efektivně využívá nativní indexů Azure DB Cosmos spravované. Indexy povolit aktualizovatelné sloupce, pokud provádět analýzy a scénáře vědy a analýzy dat nabízená dolů predikátem filtrování fast změna globálně distribuují data, která v rozsahu od Internetu věcí (IoT).

Další informace v tomto videu se Denny Lee Cosmos DB hlavní manažer programu Azure. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

Práce s Spark, GraphX a graf Gremlin rozhraní API databázi Cosmos Azure, najdete v části [provádět analýzy grafu pomocí Spark a Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Ke stažení

Abyste mohli začít, stáhněte Spark do Azure Cosmos DB konektor z [azure. cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark/) úložišti na Githubu.

## <a name="connector-components"></a>Konektor součásti

Konektor používá následující součásti:

* [Azure Cosmos DB](http://documentdb.com) umožňuje zákazníkům zřídit a Elasticky škálovat propustnost a úložiště napříč libovolný počet zeměpisné oblasti. Nabízí služba:
   * Aktivujte klíč [globální distribuční](distribute-data-globally.md) a horizontální škálování
   * Zaručit latence jediná číslice v 99th percentilu
   * [Více dobře definovaný konzistence modelů](consistency-levels.md)
   * Zaručit vysoká dostupnost s více funkci Možnosti
   * Všechny funkce jsou zajišťované špičkový, komplexní [smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA).

* [Apache Spark](http://spark.apache.org/) modul zpracování výkonné s otevřeným zdrojem, který je vytvořena na rychlost, snadné použití a sofistikované analytics.

* [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) tak, aby Apache Spark v cloudu pro kritické nasazení můžete nasadit pomocí [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Oficiálně podporované verze:

| Komponenta | Verze |
|---------|-------|
|Apache Spark|2.0.2, 2.1.0, 2.2.0|
| Scala| 2.10, 2.11|
| Azure Cosmos DB SQL Java SDK | 1.14.0, 1.15.0 |

Tento článek vám pomůže spustit některé jednoduché ukázky pomocí Python (prostřednictvím pydocumentdb v tuto) a rozhraní Scala.

Existují dva přístupy k připojení Apache Spark a Cosmos databázi Azure:
- Použít pydocumentdb v tuto prostřednictvím [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python).
- Vytvoření Spark založené na jazyce Java do Azure Cosmos DB konektoru s využitím [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>pydocumentdb v tuto implementaci
Aktuální [pydocumentdb v tuto sadu SDK](https://github.com/Azure/azure-documentdb-python) umožňuje připojení k databázi Cosmos Azure Spark, jak je znázorněno v následujícím diagramu:

![Spark pro Azure Cosmos DB tok dat prostřednictvím pydocumentdb v tuto DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Tok dat pydocumentdb v tuto implementaci

Tok dat je následující:

1. Připojí se ke uzel brány Azure Cosmos DB prostřednictvím pydocumentdb v tuto hlavní uzel Spark. Uživatel Určuje pouze připojení Spark a Azure Cosmos DB. Připojení k příslušnými uzly hlavní a brány jsou transparentní pro uživatele.
2. Uzel brány vytvoří dotaz na databázi Cosmos Azure, kde dotaz následně spouští kolekce oddílů v datové uzly. Odpovědi na tyto dotazy budou odeslána zpět do uzlu brány, a že sadu výsledků dotazu je vrácen do hlavní uzel Spark.
3. Následující dotazy (například pro Spark DataFrame) odešlou k pracovním uzlům Spark pro zpracování.

Komunikace mezi Spark a Azure Cosmos DB je omezený na hlavní uzel Spark a uzly brány Azure Cosmos DB.  Dotazy přejděte tak rychle, jak umožňuje transportní vrstva mezi těmito dvěma uzly.

### <a name="install-pydocumentdb"></a>Nainstalujte pydocumentdb v tuto
Pydocumentdb v tuto vašeho uzlu ovladačů můžete nainstalovat pomocí **pip**, například:

```bash
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Připojení k databázi Cosmos Azure Spark prostřednictvím pydocumentdb v tuto
Jednoduchost přenosu komunikace provede spuštění dotazu z Spark Azure Cosmos DB pomocí poměrně jednoduché pydocumentdb v tuto.

Následující fragment kódu ukazuje, jak používat pydocumentdb v tuto v kontextu Spark.

```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Jak jsme uvedli v fragment kódu:

* Azure Python Cosmos DB SDK (`pyDocumentDB`) obsahuje všechny potřebné parametry připojení. Například parametr upřednostňované umístění zvolí čtení pořadí repliky a s prioritou.
*  Potřebné knihovny pro import a nakonfigurování vaše **masterKey** a **hostitele** k vytvoření Azure Cosmos DB *klienta* (**pydocumentdb.document_client** ).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Spuštění dotazů Spark prostřednictvím pydocumentdb v tuto
Následující příklady použití Azure Cosmos DB instanci, která byla vytvořena v předchozím fragmentu kódu pomocí zadaného klíče jen pro čtení. Následující fragment kódu se připojuje k **airports.codes** kolekce v účtu DoctorWho jako dříve zadaný a spustí dotaz k extrakci města letiště ve státě Washington.

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Po provedení dotazu prostřednictvím **dotazu**, výsledkem je, **query_iterable. QueryIterable** , jsou převedeny na seznam Python. Seznam Python lze snadno převést na Spark DataFrame pomocí následujícího kódu:

```python
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Proč používat pydocumentdb v tuto pro připojení k databázi Cosmos Azure Spark?
Spark se připojit k databázi Cosmos Azure pomocí pydocumentdb v tuto obvykle pro scénáře, kde:

* Chcete používat jazyk Python.
* Jsou vrácení výsledku poměrně malý, nastavte databázi Cosmos Azure Spark. Všimněte si, že může mít poměrně značnou podkladové datové sady v Azure Cosmos DB. Jsou použití filtrů, tedy spuštěným filtry predikátů pro váš zdroj Azure Cosmos DB.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Spark pro konektor Azure Cosmos DB

Spark pro konektor Azure Cosmos DB využívá [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java) a přesouvá data mezi uzly pracovního procesu Spark a Azure Cosmos DB, jak je znázorněno v následujícím diagramu:

![Tok dat v Spark pro konektor Azure Cosmos DB](./media/spark-connector/spark-connector.png)

Tok dat je následující:

1. Hlavní uzel Spark se připojí k uzel brány Azure Cosmos DB získat mapa oddílu. Uživatel Určuje pouze připojení Spark a Azure Cosmos DB. Připojení k příslušnými uzly hlavní a brány jsou transparentní pro uživatele.
2. Tyto informace jsou poskytovány zpět do hlavního uzlu Spark.  V tomto okamžiku nyní byste měli mít analyzovat dotaz a zjistit oddíly a jejich umístění v Azure DB Cosmos, které potřebujete získat přístup.
3. Tyto informace se přenesou do uzlů pracovního procesu Spark.
4. Pracovní uzly Spark se připojit k Azure Cosmos DB oddíly, které přímo mají extrahovat data a vrátit data do oddílů Spark v pracovním uzlům Spark.

Komunikace mezi Spark a Azure Cosmos DB je výrazně rychlejší, protože je přesun dat mezi uzly pracovního procesu Spark a datové uzly Azure Cosmos DB (oddíly).

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Sestavení Spark pro konektor Azure Cosmos DB
V současné době používá konektor projekt maven. Pokud chcete vytvořit konektor bez závislosti, můžete spustit:
```
mvn clean package
```
Můžete si taky stáhnout nejnovější verze JAR z [ *uvolní* složky](https://github.com/Azure/azure-cosmosdb-spark/releases).

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Zahrnout Spark DB Azure Cosmos JAR
Před spuštěním žádný kód, musíte zahrnout JAR Azure Cosmos pro Spark DB.  Pokud používáte **spark prostředí**, potom můžete zahrnout JAR pomocí **– JAR** možnost.  

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0-uber.jar
```

Pokud chcete provést JAR bez závislostí, použijte následující kód:

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar,/$location/azure-documentdb-1.14.0.jar,/$location/azure-documentdb-rx-0.9.0-rc2.jar,/$location/json-20140107.jar,/$location/rxjava-1.3.0.jar,/$location/rxnetty-0.4.20.jar 
```

Pokud používáte služby Poznámkový blok, jako je služba poznámkového bloku Azure HDInsight Jupyter, můžete použít **spark magic** příkazy:

```
%%configure
{ "name":"Spark-to-Cosmos_DB_Connector", 
  "jars": ["wasb:///example/jars/1.0.0/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-1.14.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-rx-0.9.0-rc2.jar", "wasb:///example/jars/1.0.0/json-20140107.jar", "wasb:///example/jars/1.0.0/rxjava-1.3.0.jar", "wasb:///example/jars/1.0.0/rxnetty-0.4.20.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

**JAR** příkaz umožňuje zahrnout dva JAR, které jsou potřebné pro **azure. cosmosdb spark** (samostatně a sada SDK SQL Azure Cosmos DB Java) a vyloučit **scala-odráží**tak, že nebudou v konfliktu s Livy volání (Poznámkový blok Jupyter > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Spark připojit k databázi Cosmos Azure pomocí služby connector
I když přenos komunikace je trochu složitější, provádění dotazu z Spark pro Azure Cosmos DB pomocí konektoru je výrazně rychlejší.

Následující fragment kódu ukazuje, jak k používání konektoru v relaci Spark; naleznete `azure-cosmosdb-spark` [úložiště GitHub](https://github.com/Azure/azure-cosmosdb-spark) pro Python ukázky.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val baseConfig = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(baseConfig)
coll.createOrReplaceTempView("c")
```

Jak jsme uvedli v fragment kódu:

- **Azure-cosmosdb-spark** obsahuje všechny potřebné parametry připojení, mezi které patří upřednostňované umístění. Například můžete čtení pořadí repliky a s prioritou.
- Stačí importovat potřebné knihovny a nakonfigurujte masterKey a hostitele pro vytvoření klienta Azure Cosmos DB.

### <a name="execute-spark-queries-via-the-connector"></a>Spuštění dotazů Spark prostřednictvím konektoru

Následující příklad používá Azure Cosmos DB instanci, která byla vytvořena v předchozím fragmentu kódu pomocí zadaného klíče jen pro čtení. Následující fragment kódu připojí ke kolekci DepartureDelays.flights_pcoll (v účtu DoctorWho uvedeného výše) a spustí dotaz extrahovat informace o letu zpoždění letů, které jsou jednotlivé Praha.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Proč používat Spark pro Azure Cosmos DB konektor implementaci?

Spark se připojit k databázi Cosmos Azure pomocí konektoru obvykle pro scénáře, kde:

* Chcete použít, Python nebo Scala.
* Máte velký objem dat pro přenos mezi Apache Spark a Azure Cosmos DB.

Poskytnout základní přehled rozdíl výkonu dotazu, najdete v článku [dotazu testovací spouští wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Příklad distribuované agregace
Tato část obsahuje příklady jak to distribuované agregací a analýzy pomocí Apache Spark a Azure Cosmos DB společně. Azure Cosmos DB již podporuje agregace, která je popsána v [planetu agregace škálování s Azure Cosmos DB blog](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). Zde je, jak může trvat na další úroveň s Apache Spark.

Všimněte si, že tyto agregace jsou reference [Spark do poznámkového bloku konektoru DB Cosmos Azure](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Připojení k lety ukázková data
Tyto příklady agregace přístup k cestě výkonu data, která je uložena v našem **DoctorWho** databáze Azure Cosmos DB. Chcete-li se k němu připojit, je potřeba využívat následující fragment kódu:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Tato fragmentem také přidáme ke spuštění základní dotazu, který převádí filtrovanou sadu dat z Azure Cosmos DB Spark kde k tomu můžete provést distribuované agregace. V takovém případě vás žádáme pro lety, které se liší od Praha (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Následující výsledky byly vygenerovány spuštěním dotazy ze služby poznámkového bloku Jupyter.  Všimněte si, že všechny fragmenty kódu jsou obecné a není specifická pro libovolnou službu.

### <a name="running-limit-and-count-queries"></a>LIMIT spuštěné a počet dotazů
Podobně jako jste zvyklí v SQL nebo Spark SQL, můžeme začít s **LIMIT** dotazu:

![Spark LIMIT dotazu](./media/spark-connector/spark-sql-query.png)

Další dotaz je jednoduchý a rychlé **počet** dotazu:

![Dotazu Spark COUNT](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>GROUP BY dotazu
V této sadě další můžeme jednoduše spustit **GROUP BY** dotazy pro Azure Cosmos DB databáze:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Spark GROUP BY dotazu grafu.](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>JEDINEČNÉ, ORDER BY dotazu
A tady je **DISTINCT, ORDER BY** dotazu:

![Spark GROUP BY dotazu grafu.](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Analýza dat letu pokračovat
Následující příklady dotazů můžete pokračovat analýzu dat cestě:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>TOP 5 Zpožděné cíle (města) jednotlivé Praha
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Spark nejvyšší zpoždění grafu](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Vypočítat střední zpoždění podle měst cílové jednotlivé Praha
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Spark střední zpoždění grafu](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Další postup

Pokud jste tak ještě neučinili, stáhněte Spark pro Azure Cosmos DB konektor z [azure. cosmosdb spark](https://github.com/Azure/azure-cosmosdb-spark) úložiště GitHub a seznamte se s další prostředky v úložišti:

* [Příklady distribuované agregace](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkových bloků](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Můžete také zkontrolovat [Apache Spark SQL, průvodce datové sady a DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) a [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
