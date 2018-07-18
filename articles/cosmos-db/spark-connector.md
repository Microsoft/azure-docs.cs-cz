---
title: Apache Spark připojení ke službě Azure Cosmos DB | Dokumentace Microsoftu
description: Pomocí tohoto kurzu se dozvíte o konektoru Azure Cosmos DB Spark, který vám umožní připojit Apache Spark pro Azure Cosmos DB provedla distribuované agregacemi a daty věd na více tenantů globálně distribuovaný databázový systém od Microsoftu, která navržené pro cloud.
keywords: Apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: cae66a40882231f7762af29cdeaaf658dd2aa968
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113943"
---
# <a name="accelerate-real-time-big-data-analytics-by-using-the-spark-to-azure-cosmos-db-connector"></a>Zrychlení v reálném čase analýzy velkých objemů dat pomocí Sparku do konektoru služby Azure Cosmos DB
 
Spark pro konektor služby Azure Cosmos DB umožňuje službě Azure Cosmos DB fungovat jako vstup nebo výstup úlohy Apache Spark. Připojení [Spark](http://spark.apache.org/) k [služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) zrychluje možnost k řešení problémů, rychlé tempo datové vědy, kde můžete použít službu Azure Cosmos DB rychle zachovat a dotazování dat. Spark pro konektor služby Azure Cosmos DB efektivně využívá nativní indexy spravované služby Azure Cosmos DB. Indexy, které povolí aktualizovatelné sloupce při provádění analýz a důkladné prediktivní filtrování rychle se měnících globálně distribuovaných dat, která v rozsahu od Internetu věcí (IoT) pro datové vědy a analýzy scénáře.

Další informace o Sparku do konektoru služby Azure Cosmos DB v tomto videu:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Součásti konektoru

Spark pro konektor služby Azure Cosmos DB využívá následující komponenty:

* [Azure Cosmos DB](http://documentdb.com) umožňuje zákazníkům zřizovat a Elasticky škálovat propustnost a úložiště napříč libovolným počtem geografických oblastí.  

* [Apache Spark](http://spark.apache.org/) je modul pro zpracování výkonné otevřít zdroj, který je postavené na rychlost, snadné použití a sofistikované analýzy.  

* [Cluster Apache Spark v Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) můžete spouštět úlohy spark na clusteru spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Apache Spark se připojit ke službě Azure Cosmos DB

Existují dva přístupy k připojení Apache Spark a Azure Cosmos DB:

1. S použitím [SQL Python SDK pro Azure Cosmos DB](https://github.com/Azure/azure-documentdb-python), spark založenou na jazyce Python pro konektor služby Cosmos DB, který se také označuje jako "pyDocumentDB".  

2. S použitím [SQL Java SDK pro Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java) spark založené na jazyce Java do konektoru služby Cosmos DB.


**Podporované verze**

| Komponenta | Verze |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Verze modulu runtime Databricks | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Připojení s použitím Pythonu nebo pyDocumentDB SDK

Následující obrázek ukazuje architekturu pyDocumentDB SDK implementace:

![Spark pro Azure Cosmos DB tok dat prostřednictvím pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Tok dat

Tok dat provádění pyDocumentDB vypadá takto:

* Hlavní uzel spark se připojuje k uzlu brány služby Azure Cosmos DB prostřednictvím pyDocumentDB. Uživatel Určuje pouze připojení služby Azure Cosmos DB a spark. Připojení k příslušné uzly master a brány jsou pro uživatele transparentní.  

* Uzel brány zadá dotaz službou Azure Cosmos DB, ve kterém následně spuštění dotazu oddílům kolekce v datových uzlů. Odpovědi na tyto dotazy budou odeslána zpět do uzlu brány a tuto sadu výsledků se vrátí k hlavnímu uzlu spark.  

* Následující dotazy (například na blok dat spark) odesílají k pracovním uzlům Spark pro zpracování.  

Komunikace mezi sparkem a Azure Cosmos DB je omezená na spark hlavní uzly a uzly brány služby Azure Cosmos DB. Dotazy, přejděte tak rychle, jak umožňuje přenosové vrstvy mezi těmito dvěma uzly.

Provedením následujících kroků pro připojení spark ke službě Azure Cosmos DB s použitím pyDocumentDB SDK:

1. Vytvoření [pracovního prostoru Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) a [clusteru spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks modul runtime verze 4.0 (zahrnuje Apache Spark 2.3.0, Scala 2.11) v rámci pracovního prostoru.  

2. Jakmile je cluster se vytvoří a je spuštěná, přejděte do **pracovní prostor** > **vytvořit** > **knihovny**.  
3. V dialogovém okně Nová knihovna zvolte **nahrát Egg Python nebo PyPi** jako zdroj, zadejte **pydocumentdb** jako název a vyberte **nainstalujte knihovnu**. Sada SDK PyDocumentdb je už publikovaný na balíčky pip, aby mohla najít a nainstalovat. 

   ![Vytvoření a připojení knihoven](./media/spark-connector/create-library.png)

4. Po dokončení instalace knihovny ji připojte ke clusteru, který jste vytvořili dříve.  

5. Dále přejděte **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

6. V **vytvořit poznámkový blok** dialogové okno pole, zadejte popisný název, vyberte **Python** jako jazyk. V rozevíracím seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

7. Zjednodušení komunikace přenosu díky spuštění dotazu z spark ke službě Azure Cosmos DB s použitím pyDocumentDB poměrně jednoduché. Potom spustíte pár dotazů spark pomocí ukázkových dat lety konání "doctorwho" účet služby Cosmos DB, která je veřejně přístupná. Verze HTML poznámkového bloku je hostován v [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) úložiště GitHub. By měl stahovat soubory úložiště a přejděte do `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html` můžete importovat Poznámkový blok ke svému účtu Azure Databricks a spustíme ji. Následující oddíl popisuje funkce bloků kódu v podrobné.

Následující fragment kódu ukazuje, jak importovat pyDocumentDB sady SDK a spuštění dotazu v kontextu spark. Jak je uvedeno ve fragmentu kódu, pyDocumentDB SDK obsahuje parametry připojení potřebné pro připojení k účtu Azure Cosmos DB. Importuje požadované knihovny, nastaví hlavní klíč a hostitele k vytvoření klienta služby Azure Cosmos DB (pydocumentdb.document_client).


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

Následně můžete spouštět dotazy, následující fragment kódu připojí ke kolekci airports.codes DoctorWho účtu a spustí dotaz k extrakci měst letiště ve státě Washington. 

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

Po provedení dotazu, výsledek je "query_iterable. QueryIterable", který je převeden do seznamu Python, který je pak převedeno na spark datového rámce. 

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

## <a name="considerations-when-using-pydocumentdb-sdk"></a>Informace týkající se použití pyDocumentDB SDK
Spark připojování ke službě Azure Cosmos DB s použitím pyDocumentDB, které sada SDK se doporučuje v následujících scénářích:

* Chcete použít Python.  

* Vrací relativně malou sadu výsledků ze služby Azure Cosmos DB pro spark. Všimněte si, že podkladové datové sadě ve službě Azure Cosmos DB může mít poměrně značnou a jsou použití filtrů nebo spuštění filtrů predikátů zdroji služby Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Připojení pomocí sady Java SDK

Následující obrázek ukazuje architekturu provádění SQL Java SDK pro Azure Cosmos DB a data se přesouvají mezi uzly pracovního procesu spark:

![Tok dat ve Sparku do konektoru služby Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Tok dat

Tok dat provádění sady Java SDK je následujícím způsobem:

* Hlavní uzel spark se připojuje k uzlu brány služby Azure Cosmos DB k získání mapa oddílu. Uživatel Určuje spark a připojení služby Azure Cosmos DB. Připojení k příslušné uzly master a brány jsou pro uživatele transparentní.  

* Tyto informace jsou poskytovány zpět do hlavního uzlu spark. V tomto okamžiku by měl být schopen analyzovat dotazu k určení oddíly a jejich umístění ve službě Azure Cosmos DB, které potřebujete pro přístup k.  

* Tyto informace se přenášejí do spark pracovních uzlů.  

* Pracovní uzly spark připojení do služby Azure Cosmos DB oddílů přímo k extrahování dat a vrátit data do oddílů sparku v pracovních uzlech.  

Komunikace mezi sparkem a Azure Cosmos DB je výrazně rychlejší, protože data je pohyb mezi uzly pracovního procesu spark a Azure Cosmos DB datové uzly (oddíly). V tomto dokumentu se odeslat ukázková data twitteru k účtu Azure Cosmos DB a spuštění dotazů spark pomocí dat. Následující postup slouží k zápisu dat Twitteru ukázky ke službě Azure Cosmos DB:

1. Vytvoření [účtu rozhraní SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) a [přidat kolekci](create-sql-api-dotnet.md#add-a-collection) k účtu.  

2. Stáhněte si [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) ukázky z Githubu, který se používá k zápisu dat Twitteru ukázky ke službě Azure Cosmos DB.  

3. Otevřete příkazový řádek a nainstalujte Tweepy a pyDocumentdb moduly spuštěním následujících příkazů:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extrakce obsahu informační kanál sítě Twitter, ukázky a otevřete soubor config.py. Aktualizujte hodnoty hlavního klíče, hostitele, ID databáze, collectionId a seznamu preferredLocations.  

5. Přejděte na `http://apps.twitter.com/` a zaregistrujte twitteru přes skript jako novou aplikaci. Po zvolení názvu a aplikace pro vaši aplikaci, budete mít k dispozici **uživatelský klíč, uživatelský tajný klíč, přístupový a tajný klíč přístupového tokenu**. Zkopírujte tyto hodnoty a jejich aktualizaci v soubor config.py poskytuje programový přístup k aplikaci na Twitteru.   

6. Uložte soubor config.py. Otevřít příkazový řádek a spusťte aplikaci v pythonu pomocí následujícího příkazu:

   ```bash
   Python driver.py
   ```

7. Přejděte ke kolekci Azure Cosmos DB na portálu a ověřte, že je do kolekce zapsána data twitteru.

### <a name="find-and-attach-java-sdk-to-the-spark-cluster"></a>Najít a připojit ke clusteru spark sady Java SDK

1. Vytvoření [pracovního prostoru Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) a [clusteru spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks modul runtime verze 4.0 (zahrnuje Apache Spark 2.3.0, Scala 2.11) v rámci pracovního prostoru.  

2. Jakmile je cluster se vytvoří a je spuštěná, přejděte do **pracovní prostor** > **vytvořit** > **knihovny**.  

3. V dialogovém okně Nová knihovna zvolte **souřadnice Maven** jako zdroj, zadejte hodnotu souřadnice **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**a vyberte  **Vytvoření knihovny**. Maven závislosti se vyřešila a balíček je přidán do pracovního prostoru. Ve výše uvedené maven souřadnic formátu představuje verzi spark 2.3.0, představuje verzi scala 2.11 a 1.2.0 představuje verzi konektoru služby Azure Cosmos DB. 

4. Po dokončení instalace knihovny ji připojte ke clusteru, který jste vytvořili dříve. 

Tento článek ukazuje použití konektoru spark sady Java SDK v následujících scénářích:

* Twitter číst data ze služby Azure Cosmos DB  

* Čtení dat twitteru, která je streamování do služby Azure Cosmos DB  

* Zápis dat twitteru do služby Azure Cosmos DB 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Twitter číst data ze služby Azure Cosmos DB
 
V této části je spustit dotazy spark ke čtení dávky Twitteru dat ze služby Azure Cosmos DB. Verze HTML poznámkového bloku je hostován v [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) úložiště GitHub. By měl stahovat soubory úložiště a přejděte do `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html` můžete importovat Poznámkový blok ke svému účtu Azure Databricks, aktualizujte účet identifikátor URI, hlavní klíč databáze, názvy kolekcí a spusťte ho, nebo můžete vytvořit poznámkový blok takto:

1. Přejděte do svého účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**. 

2. V **vytvořit poznámkový blok** dialogové okno pole, zadejte popisný název, vyberte **Python** jako jazyk, v rozevíracím seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavní klíč, databázi a kolekci hodnot pro připojení k účtu a čtení tweetů spark.read.format() příkazem.

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
|query_disableruperminuteusage  |  Zakáže jednotek žádosti (ru) za minutu kapacitu a zvládnete obsloužit dotazu, pokud byl vyčerpán běžné zřízené ru/s.       |
|query_emitverbosetraces   |   Nastaví možnost povolit dotazy a vygenerovat si podrobné trasování pro šetření.      |
|query_pagesize  |   Nastaví velikost stránky výsledek dotazu pro každý požadavek dotazu. Pokud chcete optimalizovat propustnost, použijte velikosti stránky a snížit počet zpátečních cest k načtení výsledků dotazů.      |
|query_custom  |  Nastaví služby Azure Cosmos DB dotaz přepsat výchozí dotaz při načítání dat ze služby Azure Cosmos DB. Všimněte si, že je-li tuto hodnotu, použije se místo dotaz s posunutý dolů také predikáty.     |

V závislosti na scénáři jiné konfigurační hodnoty by měla sloužit k optimalizaci výkonu a propustnost. Mějte na paměti, že konfigurační klíč je aktuálně velkých a malých písmen a hodnota konfigurace je vždy řetězec.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Čtení dat twitteru, která je streamování do služby Azure Cosmos DB

V této části je spustit dotazy spark ke čtení kanálu změn streamovaných dat twitteru. Při spuštění dotazů v této části, ujistěte se, že je spuštěná a dat do služby Azure Cosmos DB – čerpání kanálu aplikaci na Twitteru. Verze HTML poznámkového bloku je hostován v [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) úložiště GitHub. By měl stahovat soubory úložiště a přejděte do `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html` můžete importovat Poznámkový blok ke svému účtu Azure Databricks, aktualizujte účet identifikátor URI, hlavní klíč databáze, názvy kolekcí a spusťte ho, nebo můžete vytvořit poznámkový blok takto:

1. Přejděte do svého účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

2. V **vytvořit poznámkový blok** dialogové okno pole, zadejte popisný název, vyberte **Scala** jako jazyk, v rozevíracím seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavní klíč, databázi a kolekci hodnot pro připojení k účtu.

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
4. Spuštění kanálu jako datový proud pomocí příkazu spark.readStream.format() čtení změn:

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
|changefeedqueryname |   Vlastní řetězec k identifikaci dotazu. Kolekce tokenů pokračování pro kanál dotazy samostatně různých změn uchovává informace o konektoru. Pokud readchangefeedis true, to je požadované konfigurace, která nemůže nemůže být prázdná hodnota.      |
|changefeedcheckpointlocation  |   Cesta k místní file storage pro uchovávání tokeny pokračování v případě selhání uzlů.      |
|changefeedstartfromthebeginning  |  Nastaví, zda kanál změn byste začít od začátku (pravda) nebo z aktuálního místa (false). Ve výchozím nastavení spustí z aktuální (false).       |
|rollingchangefeed  |   Logickou hodnotu označující, zda změna kanálu by měl být od posledního dotazu. Výchozí hodnota je false, což znamená, že změny se bude počítat od první čtení kolekce.      |
|changefeedusenexttoken  |   Logická hodnota, pro zajištění podpory scénářů zpracování selhání. Používá se k označení, že aktuální kanálu batch změn byla zpracována řádně a RDD by měl používat další tokeny pokračování získat následných batch změny.      |
| InferStreamSchema | Logická hodnota, která uvedeno, zda schéma streamovaných dat vzorkování na začátku streamování. Ve výchozím nastavení, tato hodnota nastavena na hodnotu true. Pokud tento parametr je nastaven na hodnotu true a změny schématu streamovaná data po Vzorkovaná data, nově přidané vlastnosti se zahodí v rámci streamovaná data. <br/><br/> Pokud chcete streamování datového rámce být nezávislá na schématu, nastavte tento parametr na hodnotu false. V tomto režimu textu v dokumentech čtení z kanálu změn služby Azure Cosmos DB jsou zabaleny do vlastnosti 'text' v rámci datových proudů Výsledná data kromě hodnoty vlastností systému.
 |

### <a name="connection-settings"></a>Nastavení připojení

Sada Java SDK podporuje následující nastavení připojení:

|Nastavení  |Popis  |
|---------|---------|
|connectionmode   |  Nastaví režim připojení, který interní DocumentClient měli používat ke komunikaci s Azure Cosmos DB. Povolené hodnoty jsou **DirectHttps** (výchozí hodnota) a **brány**. Režim připojení DirectHttps směruje požadavky přímo do oddílů služby cosmos DB a poskytuje některé výhody latence.       |
|connectionmaxpoolsize   |  Nastaví hodnotu velikosti fondu připojení, který používá interní DocumentClient. Výchozí hodnota je 100.       |
|connectionidletimeout  |  Nastaví hodnotu časového limitu nečinného připojení v sekundách. Výchozí hodnota je 60.       |
|query_maxretryattemptsonthrottledrequests    |  Nastaví maximální počet opakování. Tato hodnota se používá v případě selhání požadavku z důvodu tady na straně klienta. Pokud se nezadá, výchozí hodnota je 1000 opakovaných pokusů.       |
|query_maxretrywaittimeinseconds   |  Nastaví maximální doba opakování v sekundách. Ve výchozím nastavení je 1 000 sekund.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Zápis dat twitteru do služby Azure Cosmos DB 

V této části je spustit dotazy spark pro zápis dávky dat twitteru do nové kolekce ve stejné databázi. Verze HTML poznámkového bloku je hostován v [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) úložiště GitHub. By měl stahovat soubory úložiště a přejděte do `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html` můžete importovat Poznámkový blok ke svému účtu Azure Databricks, aktualizujte účet identifikátor URI, hlavní klíč databáze, názvy kolekcí a spusťte ho, nebo můžete vytvořit poznámkový blok takto:

1. Přejděte do svého účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

2. V **vytvořit poznámkový blok** dialogové okno pole, zadejte popisný název, vyberte **Scala** jako jazyk, v rozevíracím seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavní klíč, databázi a kolekci hodnot pro připojení k databázi kolekce ke čtení a zápisu dat twitteru.

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
|WritingBatchSize  |   Určuje velikost dávky pro použití při zápisu dat do kolekce Azure Cosmos DB. <br/><br/> Pokud parametr hromadný import je nastaven na hodnotu true, pak parametr WritingBatchSize označuje velikost dávky zadaný jako vstup na hodnotu importAll API BulkExecutor knihovny dokumentů. Ve výchozím nastavení je tato hodnota nastavena na 100 kB. <br/><br/> Hromadný import parametr je nastavený na hodnotu false, WritingBatchSize parametr označuje velikost dávky pro použití při zápisu do kolekce Azure Cosmos DB. Konektor asynchronně odešle createDocument/upsertDocument požadavků ve službě batch. Čím větší velikost dávky větší propustnost, kterou jsme dosáhnout za předpokladu, jsou k dispozici prostředky clusteru. Na druhé straně zadejte menší počet velikosti dávky k omezení frekvence a spotřebu RU. Ve výchozím nastavení velikost dávky zápisu nastavená na 500.  |
|Upsertovat   |  Řetězec logickou hodnotu označující, zda upsertDocument má být použit místo CreateDocument při zápisu do CosmosDB collection.   |
| WriteThroughputBudget |  Řetězec celé číslo, který představuje číslo RU\s, kterou chcete přidělit k hromadné úloha sparku příjem z celkové propustnosti přidělené do kolekce. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Zápis dat twitteru, který je streamování do služby Azure Cosmos DB 

V této části spustit dotazy spark pro zápis informačního kanálu změn streamovaných dat twitteru do nové kolekce ve stejné databázi. Verze HTML poznámkového bloku je hostován v [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) úložiště GitHub. By měl stahovat soubory úložiště a přejděte do `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html` můžete importovat Poznámkový blok ke svému účtu Azure Databricks, aktualizujte účet identifikátor URI, hlavní klíč databáze, názvy kolekcí a spusťte ho, nebo můžete vytvořit poznámkový blok takto:

1. Přejděte do svého účtu Azure Databricks a vyberte **pracovní prostor** > **vytvořit** > **Poznámkový blok**.  

2. V **vytvořit poznámkový blok** dialogové okno pole, zadejte popisný název, vyberte **Scala** jako jazyk, v rozevíracím seznamu vyberte cluster, který jste vytvořili dříve a vyberte **vytvořit**.  

3. Aktualizujte koncový bod, hlavní klíč, databázi a kolekci hodnot pro připojení k databázi kolekce ke čtení a zápisu dat twitteru.

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
4. Spuštění kanálu jako datový proud pomocí příkazu spark.readStream.format() čtení změn:
 
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
|Upsertovat   |  Řetězec logickou hodnotu označující, zda upsertDocument má být použit místo CreateDocument při zápisu do CosmosDB collection.   |
|checkpointlocation  |   Cesta k místní file storage pro uchovávání tokeny pokračování v případě selhání uzlů.   |
|WritingBatchSize  |  Určuje velikost dávky pro použití při zápisu dat do kolekce Azure Cosmos DB. Konektor asynchronně odešle createDocument/upsertDocument požadavků ve službě batch. Čím větší velikost dávky větší propustnost, kterou jsme dosáhnout za předpokladu, jsou k dispozici prostředky clusteru. Na druhé straně zadejte menší počet velikosti dávky k omezení frekvence a spotřebu RU. Ve výchozím nastavení velikost dávky zápisu nastavená na 500.  |


## <a name="considerations-when-using-java-sdk"></a>Informace týkající se použití sady Java SDK

Spark připojení ke službě Azure Cosmos DB pomocí sady Java SDK se doporučuje v následujících scénářích:

* Chcete použít Python nebo Scala.  

* Máte velký objem dat pro přenos mezi Apache Spark a Azure Cosmos DB, sady Java SDK má vyšší výkon ve srovnání s pyDocumentDB. Abyste získali představu o rozdíl výkon dotazů, najdete v článku [dotaz testovacích běhů wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Další postup

Pokud jste tak dosud neučinili, stáhněte si Sparku do konektoru služby Azure Cosmos DB z [azure-cosmos DB – spark](https://github.com/Azure/azure-cosmosdb-spark) úložiště GitHub a další materiály v úložišti:

* [Příklady distribuované agregace](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Ukázkové skripty a poznámkovými bloky](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Můžete také zkontrolovat [Apache Spark SQL, datových rámců a datových sad průvodce](http://spark.apache.org/docs/latest/sql-programming-guide.html) a [Apache Spark v Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) článku.
