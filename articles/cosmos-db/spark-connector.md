---
title: Apache Spark se připojit ke službě Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o konektoru Azure Cosmos DB Spark, který vám umožní připojit ke službě Azure Cosmos DB Apache Spark. Můžete provést distribuované agregací v systému více tenantů, globálně distribuovaná databáze Microsoftu.
keywords: Apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: 62395ff5370a057ca1888dd135b83be9d6f34f8d
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707176"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Urychlení analýzy velkých objemů dat pomocí Apache Sparku do konektoru služby Azure Cosmos DB
 
Konektor Apache Spark pro Azure Cosmos DB umožňuje službě Azure Cosmos DB být vstupem nebo výstupem pro úlohy Apache Spark. Připojení [Spark](http://spark.apache.org/) k [služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) se řešení rychlé tempo data science problémů. Služby Azure Cosmos DB můžete použít k rychlému zachovat a dotazování na data. Tento konektor efektivně využívá nativní spravované indexy služby Azure Cosmos DB. Tyto indexy umožňují využít aktualizovatelné sloupce při provádění analýz a důkladné prediktivní filtrování rychle se měnících globálně distribuovaných dat. Tento typ dat musí být v rozsahu Internet of Things (IoT) pro datové vědy a analýzy scénáře.

## <a name="connector-components"></a>Součásti konektoru

Spark pro konektor služby Azure Cosmos DB má následující komponenty:

* [Azure Cosmos DB](http://documentdb.com) umožňuje zřizovat a Elasticky škálovat propustnost a úložiště napříč libovolným počtem geografických oblastí.  

* [Apache Spark](http://spark.apache.org/) je modul pro zpracování výkonné opensourcových, postavené na rychlost, snadné použití a sofistikované analýzy.  

* [Cluster Apache Spark v Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) umožňuje spouštět úlohy Spark v clusteru Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Apache Spark se připojit ke službě Azure Cosmos DB

Existují dva přístupy k připojení Apache Spark a Azure Cosmos DB:

- [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), konektor založenou na jazyce Python, který se také označuje jako *pyDocumentDB*.  

- [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java), konektor založené na jazyce Java.


**Podporované verze**

| Komponenta | Verze |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Verze modulu runtime služby Azure Databricks | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Připojení s použitím Pythonu nebo pyDocumentDB SDK

Následující diagram znázorňuje architekturu implementace pyDocumentDB SDK:

![Diagram Spark pro Azure Cosmos DB tok dat prostřednictvím pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Tok dat

Tok dat provádění pyDocumentDB vypadá takto:

* Hlavní uzel Spark se připojuje k uzlu brány služby Azure Cosmos DB prostřednictvím pyDocumentDB. Můžete zadat pouze připojení Spark a Azure Cosmos DB. Připojení k příslušné uzly hlavní server a brána je transparentní.  

* Uzel brány zadá dotaz na službu Azure Cosmos DB, ve kterém následně spuštění dotazu oddílům kolekce v datových uzlů. Odpovědi na tyto dotazy budou odeslána zpět do uzlu brány a tuto sadu výsledků se vrátí k hlavnímu uzlu Spark.  

* Následující dotazy (například na blok dat Spark) odesílají k pracovním uzlům Spark pro zpracování.  

Komunikace mezi Sparkem a Azure Cosmos DB je omezená na Spark hlavní uzly a uzly brány služby Azure Cosmos DB. Dotazy, přejděte tak rychle, jak umožňuje přenosové vrstvy mezi těmito dvěma uzly.

Provedením následujících kroků pro připojení Spark ke službě Azure Cosmos DB s použitím pyDocumentDB SDK:

1. Vytvoření [pracovního prostoru Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) a [clusteru Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Verze 4.0 modulu runtime v Azure Databricks zahrnuje Apache Spark 2.3.0 a Scala 2.11 v daném pracovním prostoru.  

2. Pokud cluster se vytvoří a je spuštěná, přejděte do **pracovní prostor** > **vytvořit** > **knihovny**.  
3. V dialogovém okně Nová knihovna zvolte **nahrát Egg Python nebo PyPi** jako zdroj. Zadejte **pydocumentdb** jako název a vyberte **nainstalujte knihovnu**. pyDocumentdb SDK je už publikovaný na balíčky pip, aby mohla najít a nainstalovat ho. 

   ![Snímek obrazovky dialogového okna Nová knihovna](./media/spark-connector/create-library.png)

4. Po dokončení instalace knihovny ji připojte ke clusteru, který jste vytvořili dříve.  

5. Přejděte na **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

6. V **vytvořit poznámkový blok** dialogové okno, zadejte popisný název a zvolte **Python** jako jazyk. Z rozevíracího seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

7. Spustit pár dotazů Spark pomocí letů ukázkových dat hostované v účtu Azure Cosmos DB "doctorwho". Tento účet je veřejně dostupná. [Azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hostitelů úložiště GitHub [Read_Batch_PyDocumentDB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_PyDocumentDB.ipynb) poznámkového bloku. Můžete importovat Poznámkový blok ke svému účtu Azure Databricks a spustíme ji. Následující oddíl popisuje funkce bloků kódu podrobně.

Následující fragment kódu ukazuje, jak importovat pyDocumentDB sady SDK a spuštění dotazu v kontextu Spark. Jak je uvedeno ve fragmentu kódu, pyDocumentDB SDK obsahuje parametry připojení potřebné pro připojení k účtu Azure Cosmos DB. Importuje požadované knihovny a nakonfiguruje hlavního klíče a hostitele, jak vytvořit klienta služby Azure Cosmos DB (pydocumentdb.document_client).


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

V dalším kroku můžete spouštět dotazy. Následující fragment kódu připojí ke kolekci airports.codes doctorwho účtu a spustí dotaz k extrakci měst letiště ve státě Washington. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

Po spuštění dotazu výsledek je "query_iterable. QueryIterable", který je převeden na seznam Python. Tento seznam, pak je převedena na Spark datového rámce. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>Informace týkající se použití pyDocumentDB SDK
Spark připojování ke službě Azure Cosmos DB s použitím pyDocumentDB, které sada SDK se doporučuje v následujících scénářích:

* Chcete použít Python.  

* Vrací relativně malou sadu výsledků ze služby Azure Cosmos DB pro Spark. Všimněte si, že podkladové datové sadě ve službě Azure Cosmos DB může mít poměrně značnou a jsou použití filtrů nebo spuštění filtrů predikátů zdroji služby Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Připojení pomocí sady Java SDK

Následující diagram znázorňuje architekturu provádění SQL Java SDK pro Azure Cosmos DB a data se přesouvají mezi uzly pracovního procesu Spark:

![Diagram toku dat ve Sparku do konektoru služby Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Tok dat

Tok dat provádění sady Java SDK je následujícím způsobem:

* Hlavní uzel Spark se připojuje k uzlu brány služby Azure Cosmos DB k získání mapa oddílu. Můžete zadat pouze připojení Spark a Azure Cosmos DB. Připojení k příslušné uzly hlavní server a brána je transparentní.  

* Tyto informace jsou poskytovány zpět do hlavního uzlu Spark. V tomto okamžiku by měl být schopen analyzovat dotazu k určení oddíly a jejich umístění ve službě Azure Cosmos DB, které potřebujete pro přístup k.  

* Tyto informace se přenášejí do Spark pracovních uzlů.  

* Pracovní uzly Spark připojení k oddílů služby Azure Cosmos DB přímo, pro extrakci a vrátit data do oddílů Sparku v pracovních uzlech.  

Komunikace mezi Sparkem a Azure Cosmos DB je výrazně rychlejší, protože je přesouvání dat mezi uzly pracovního procesu Spark a Azure Cosmos DB datové uzly (oddíly). V tomto příkladu odeslat ukázková data Twitteru do účtu Azure Cosmos DB a spuštění dotazů Spark pomocí dat. Následující postup slouží k zápisu dat Twitteru ukázky ke službě Azure Cosmos DB:

1. Vytvoření [účtu rozhraní SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) a [přidat kolekci](create-sql-api-dotnet.md#add-a-collection) k účtu.  

2. Stáhněte si [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) ukázky z Githubu. Pomocí tohoto informačního kanálu pro zápis ukázkových dat Twitteru do služby Azure Cosmos DB.  

3. Otevřete příkazový řádek a nainstalujte Tweepy a pyDocumentdb moduly spuštěním následujících příkazů:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extrahujte obsah ukázkové informační kanál Twitteru a otevřete soubor config.py. Aktualizujte hodnoty hlavního klíče, hostitele, ID databáze, collectionId a seznamu preferredLocations.  

5. Přejděte na `http://apps.twitter.com/`a zaregistrujte twitteru přes aplikaci. Po výběru název vaší aplikace, budete mít k dispozici **uživatelský klíč, uživatelský tajný klíč, přístupový a tajný klíč přístupového tokenu**. Zkopírujte tyto hodnoty a jejich aktualizaci v souboru config.py poskytují že programový přístup k aplikaci pro Twitter informační kanál Twitteru.   

6. Uložte soubor config.py. Otevřete příkazový řádek a spusťte aplikaci v Pythonu pomocí následujícího příkazu:

   ```bash
   Python driver.py
   ```

7. Přejděte ke kolekci Azure Cosmos DB na portálu a ověřte, že je do kolekce zapsána data Twitteru.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Najít a připojit ke clusteru Spark sady Java SDK

1. Vytvoření [pracovního prostoru Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) a [clusteru Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Verze 4.0 modulu runtime v Azure Databricks zahrnuje Apache Spark 2.3.0 a Scala 2.11 v daném pracovním prostoru.  

2. Pokud cluster se vytvoří a je spuštěná, přejděte do **pracovní prostor** > **vytvořit** > **knihovny**.  

3. Z **nová knihovna** dialogového okna zvolte **souřadnice Maven** jako zdroj. Zadejte hodnotu souřadnice **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**a vyberte **vytvořit knihovnu**. Maven závislosti se vyřešila a balíček je přidán do pracovního prostoru. V předchozím Maven souřadnic formátu představuje verzi Spark 2.3.0, představuje verzi Scala 2.11 a 1.2.0 představuje verzi konektoru služby Azure Cosmos DB. 

4. Po dokončení instalace knihovny ji připojte ke clusteru, který jste vytvořili dříve. 

Tento článek ukazuje použití konektoru Spark sady Java SDK v následujících scénářích:

* Čtení dat Twitteru ze služby Azure Cosmos DB.  

* Čtení Twitteru data, která je streamování do služby Azure Cosmos DB.  

* Zápis dat Twitteru do služby Azure Cosmos DB. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Twitter pro čtení dat ze služby Azure Cosmos DB
 
V této části můžete spouštět Spark dotazy ke čtení dávky Twitteru dat ze služby Azure Cosmos DB. [Azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hostitelů úložiště GitHub [Read_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_Twitter_Data.ipynb) poznámkového bloku. Můžete importovat do účtu Azure Databricks Poznámkový blok a aktualizovat účet identifikátor URI, hlavního klíče, databáze a názvy kolekcí. Můžete spustit Poznámkový blok nebo vytvořte ho následujícím způsobem:

1. Přejděte ke svému účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**. 

2. V **vytvořit poznámkový blok** dialogové okno, zadejte popisný název a zvolte **Python** jako jazyk. Z rozevíracího seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavního klíče, databáze a kolekce hodnoty pro připojení k účtu. Čtení tweetů spark.read.format() příkazem.

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Spusťte dotaz pro získání počet tweetů, které se podle různých hashtagy z dat uložených v mezipaměti. 

   ```python
   %sql
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

Sada Java SDK podporuje následující hodnoty pro konfiguraci mapování: 

|Nastavení  |Popis  |
|---------|---------|
|query_maxdegreeofparallelism  | Nastaví počet souběžných operací běhu na straně klienta během paralelního provádění dotazů. Pokud je nastavena na hodnotu, která je větší než 0, omezuje počet souběžných operací přiřazené hodnotě. Pokud je nastavena na hodnotu menší než 0, systém automaticky určuje počet souběžných operací pro spuštění. Protože konektor mapuje každý oddíl kolekci s vykonavatele, tato hodnota nebude mít žádný vliv na operace čtení.        |
|query_maxbuffereditemcount     |    Nastaví maximální počet položek, které můžete do vyrovnávací paměti během paralelního provádění dotazů na straně klienta. Pokud je nastavena na hodnotu, která je větší než 0, omezí počet položek ve vyrovnávací paměti přiřazené hodnotě. Pokud je nastavena na hodnotu menší než 0, systém automaticky určuje počet položek do vyrovnávací paměti.     |
|query_enablescan    |   Nastavuje se možnost povolit kontroly na dotazy, které nelze poskytováni, protože indexování vyjádřil se výslovný nesouhlas na požadované cesty.       |
|query_disableruperminuteusage  |  Zakáže jednotky (ru) žádosti / vyčerpání minuta kapacitu a zvládnete obsloužit dotazu, pokud pravidelně zřízené ru/s.       |
|query_emitverbosetraces   |   Nastaví možnost povolit dotazy a vygenerovat si podrobné trasování pro šetření.      |
|query_pagesize  |   Nastaví velikost stránky výsledek dotazu pro každý požadavek dotazu. Umožňuje optimalizovat propustnost, použijte velikosti stránky a snížit počet zpátečních cest k načtení výsledků.      |
|query_custom  |  Nastaví služby Azure Cosmos DB dotaz přepsat výchozí dotaz při načítání dat ze služby Azure Cosmos DB. Všimněte si, že pokud zadáte tuto hodnotu, použijí se místo dotaz s posunutý dolů také predikáty.     |

V závislosti na scénáři měli byste použít hodnoty jinou konfiguraci pro optimalizaci výkonu a propustnost. Mějte na paměti, že konfigurační klíč je aktuálně velkých a malých písmen a hodnota konfigurace je vždy řetězec.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Čtení Twitteru data, která je streamování do služby Azure Cosmos DB

V této části můžete spouštět Spark dotazy ke čtení kanálu změn streamovaných dat Twitteru. Při spuštění dotazů v této části, ujistěte se, že je spuštěná a dat do služby Azure Cosmos DB – čerpání kanálu aplikaci na Twitteru. [Azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hostitelů úložiště GitHub [Read_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Stream_Twitter_Data.scala) poznámkového bloku. Můžete importovat do účtu Azure Databricks Poznámkový blok a aktualizovat účet identifikátor URI, hlavního klíče, databáze a názvy kolekcí. Můžete spustit Poznámkový blok nebo vytvořte ho následujícím způsobem:

1. Přejděte ke svému účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

2. V **vytvořit poznámkový blok** dialogové okno, zadejte popisný název a zvolte **Scala** jako jazyk. Z rozevíracího seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavního klíče, databáze a kolekce hodnoty pro připojení k účtu.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Spustit změnu čtení informačního kanálu jako datový proud spark.readStream.format() příkazem:

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Spusťte streamování dotazu do konzoly:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Sada Java SDK podporuje následující hodnoty pro konfiguraci mapování:

|Nastavení  |Popis  |
|---------|---------|
|readchangefeed   |  Označuje, že je obsah kolekce načtených z informačního kanálu změn služby cosmos DB. Výchozí hodnota je false.       |
|changefeedqueryname |   Vlastní řetězec k identifikaci dotazu. Kolekce tokenů pokračování pro kanál dotazy samostatně různých změn uchovává informace o konektoru. Pokud readchangefeed má hodnotu true, je to požadované konfigurace, který nemůže nemůže být prázdná hodnota.      |
|changefeedcheckpointlocation  |   Cesta k místní file storage pro uchovávání tokeny pokračování v případě selhání uzlů.      |
|changefeedstartfromthebeginning  |  Nastaví, zda kanál změn byste začít od začátku (pravda) nebo z aktuálního místa (false). Ve výchozím nastavení spustí z aktuální (false).       |
|rollingchangefeed  |   Logickou hodnotu označující, zda změna kanálu by měl být od posledního dotazu. Výchozí hodnota je false, což znamená, že změny se počítají od první čtení kolekce.      |
|changefeedusenexttoken  |   Logická hodnota, pro zajištění podpory scénářů zpracování selhání. Znamená to, že aktuální kanálu batch změn byla zpracována řádně. Resiliant Distributed Dataset musí používat další tokeny pokračování získat následných batch změny.      |
| InferStreamSchema | Logická hodnota, která určuje, zda schéma streamovaných dat vzorkování na začátku streamování. Ve výchozím nastavení, tato hodnota nastavena na hodnotu true. Pokud tento parametr je nastaven na hodnotu true a změny schématu streamovaná data po Vzorkovaná data, nově přidané vlastnosti se zahodí v rámci streamovaná data. <br/><br/> Pokud chcete streamování datového rámce být nezávislá na schématu, nastavte tento parametr na hodnotu false. V tomto režimu textu v dokumentech čtení z kanálu změn služby Azure Cosmos DB jsou zabaleny do vlastnosti body. Tato vlastnost je v výsledné datové proudy datového rámce, kromě hodnoty vlastností systému.
 |

### <a name="connection-settings"></a>Nastavení připojení

Sada Java SDK podporuje následující nastavení připojení:

|Nastavení  |Popis  |
|---------|---------|
|connectionmode   |  Nastaví režim připojení, který interní DocumentClient měli používat ke komunikaci s Azure Cosmos DB. Povolené hodnoty jsou **DirectHttps** (výchozí hodnota) a **brány**. Režim připojení DirectHttps směruje požadavky přímo do oddílů služby cosmos DB a poskytuje některé výhody latence.       |
|connectionmaxpoolsize   |  Nastaví hodnotu velikosti fondu připojení, který používá interní DocumentClient. Výchozí hodnota je 100.       |
|connectionidletimeout  |  Nastaví hodnotu časového limitu v sekundách pro nečinné připojení. Výchozí hodnota je 60.       |
|query_maxretryattemptsonthrottledrequests    |  Nastaví maximální počet opakování. Použijte tuto hodnotu v případě selhání požadavku z důvodu tady na straně klienta. Pokud se nezadá, výchozí hodnota je 1000 opakovaných pokusů.       |
|query_maxretrywaittimeinseconds   |  Nastaví maximální doba opakování v sekundách. Ve výchozím nastavení je 1 000 sekund.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Zápis dat Twitteru do služby Azure Cosmos DB 

V této části můžete spouštět Spark dotazy pro zápis dávky dat Twitteru do nové kolekce ve stejné databázi. [Azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hostitelů úložiště GitHub [Write_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Batch_Twitter_Data.ipynb) poznámkového bloku. Můžete importovat do účtu Azure Databricks Poznámkový blok a aktualizovat účet identifikátor URI, hlavního klíče, databáze a názvy kolekcí. Můžete spustit Poznámkový blok nebo vytvořte ho následujícím způsobem:

1. Přejděte ke svému účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

2. V **vytvořit poznámkový blok** dialogové okno, zadejte popisný název a zvolte **Scala** jako jazyk. Z rozevíracího seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavního klíče, databáze a kolekce hodnoty pro připojení k databázi kolekce ke čtení a zápisu dat Twitteru.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Spusťte dotaz pro získání počet tweetů, které se podle různých hashtagy z dat uložených v mezipaměti. 

   ```scala
   %sql
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

5. Vytvořit nový datový rámec obsahující značky tweety a uložit data do nové kolekce. Po spuštění následujícího kódu se můžete přejít zpět na portál a ověřte, že data se zapisují do kolekce. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Sada Java SDK podporuje následující hodnoty pro konfiguraci mapování:

|Nastavení  |Popis  |
|---------|---------|
| Hromadný import | Logická hodnota, která určuje, zda by měly být naimportovány dat pomocí knihovny BulkExecutor. Ve výchozím nastavení, tato hodnota nastavena na hodnotu true. |
|WritingBatchSize  |   Určuje velikost dávky pro použití při psaní data do kolekce Azure Cosmos DB. <br/><br/> Pokud parametr hromadný import je nastaven na hodnotu true, pak parametr WritingBatchSize označuje velikost dávky zadaný jako vstup na hodnotu importAll API BulkExecutor knihovny dokumentů. Ve výchozím nastavení je tato hodnota nastavena na 100 kB. <br/><br/> Hromadný import parametr je nastavený na hodnotu false, WritingBatchSize parametr označuje velikost dávky pro použití při psaní do kolekce Azure Cosmos DB. Konektor asynchronně odešle createDocument a upsertDocument požadavků ve službě batch. Čím větší velikost dávky, větší propustnost, které můžete dosáhnout, za předpokladu, jsou k dispozici prostředky clusteru. Na druhé straně zadejte menší počet velikosti dávky k omezení frekvence a spotřebu RU. Ve výchozím nastavení velikost dávky zápisu nastavená na 500.  |
|Upsertovat   |  Řetězec logickou hodnotu označující, jestli se má používat upsertDocument místo CreateDocument při psaní do kolekce Azure Cosmos DB.   |
| WriteThroughputBudget |  Celé číslo řetězec, který představuje počet RU\s, kterou chcete přidělit k hromadné ingestování Spark úlohy z celkové propustnosti přidělené do kolekce. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Zápis dat Twitteru, který je streamování do služby Azure Cosmos DB 

V této části můžete spouštět Spark dotazy pro zápis informačního kanálu změn streamovaných dat Twitteru do nové kolekce ve stejné databázi. [Azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hostitelů úložiště GitHub [Write_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Stream_Data.scala) poznámkového bloku. Můžete importovat do účtu Azure Databricks Poznámkový blok a aktualizovat účet identifikátor URI, hlavního klíče, databáze a názvy kolekcí. Můžete spustit Poznámkový blok nebo vytvořte ho následujícím způsobem:

1. Přejděte ke svému účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

2. V **vytvořit poznámkový blok** dialogové okno, zadejte popisný název a zvolte **Scala** jako jazyk. Z rozevíracího seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavního klíče, databáze a kolekce hodnoty pro připojení k databázi kolekce ke čtení a zápisu dat Twitteru.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Spustit změnu čtení informačního kanálu jako datový proud spark.readStream.format() příkazem:
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Definování konfigurace cílové kolekci a spuštění úlohy streamování pomocí writeStream.format() metody:

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

Sada Java SDK podporuje následující hodnoty pro konfiguraci mapování:

|Nastavení  |Popis  |
|---------|---------|
|Upsertovat   |  Řetězec logickou hodnotu označující, jestli se má používat upsertDocument místo CreateDocument při psaní do kolekce Azure Cosmos DB.   |
|checkpointlocation  |   Cesta k místní file storage pro uchovávání tokeny pokračování v případě selhání uzlů.   |
|WritingBatchSize  |  Určuje velikost dávky pro použití při zápisu dat do kolekce Azure Cosmos DB. Konektor asynchronně odešle createDocument a upsertDocument požadavků ve službě batch. Čím větší velikost dávky, větší propustnost, které můžete dosáhnout, za předpokladu, jsou k dispozici prostředky clusteru. Na druhé straně zadejte menší počet velikosti dávky k omezení frekvence a spotřebu RU. Ve výchozím nastavení velikost dávky zápisu nastavená na 500.  |


### <a name="considerations-when-using-java-sdk"></a>Informace týkající se použití sady Java SDK

Připojení Spark ke službě Azure Cosmos DB pomocí sady Java SDK se doporučuje v následujících scénářích:

* Chcete použít Python nebo Scala.  

* Máte velký objem dat pro přenos mezi Apache Spark a Azure Cosmos DB. Sada Java SDK vrací lepší výsledky než pyDocumentDB. Další informace o rozdílu výkon dotazů, najdete v článku [dotazu testovací běhy wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Další postup

Pokud jste tak dosud neučinili, stáhněte si Sparku do konektoru služby Azure Cosmos DB z [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark) úložiště GitHub. Prozkoumejte tyto další prostředky v úložišti:

* [Příklady agregace](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkovými bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Můžete také zkontrolovat [Apache Spark SQL, datových rámců a datových sad průvodce](http://spark.apache.org/docs/latest/sql-programming-guide.html)a [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
