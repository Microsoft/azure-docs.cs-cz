---
title: Interakce s Azure Cosmos DB pomocí Apache Spark v propojení Azure synapse
description: Jak pracovat s Azure Cosmos DB pomocí Apache Spark v propojení Azure synapse
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 4a8367ea41ea96d8a412af965346684737d190fe
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627570"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Interakce s Azure Cosmos DB pomocí Apache Spark v propojení Azure synapse

V tomto článku se dozvíte, jak pracovat s Azure Cosmos DB pomocí Apache Spark synapse. Díky jeho plné podpoře pro Scala, Python, SparkSQL a C# synapse Apache Spark je centrální na analýzy, datové strojírenství, datové vědy a scénáře pro zkoumání dat v [Azure synapse pro Azure Cosmos DB](../../cosmos-db/synapse-link.md).

Při interakci s Azure Cosmos DB jsou podporovány následující funkce:
* Synapse Apache Spark umožňuje analyzovat data ve vašich Azure Cosmos DBch kontejnerech, které jsou povolené pomocí Azure synapse linku téměř v reálném čase, aniž by to ovlivnilo výkon transakčních úloh. K dispozici jsou následující dvě možnosti pro dotazování Azure Cosmos DB [analytického úložiště](../../cosmos-db/analytical-store-introduction.md) ze Sparku:
    + Načíst do Spark dataframe
    + Vytvořit tabulku Spark
* Synapse Apache Spark také umožňuje ingestovat data do Azure Cosmos DB. Je důležité si uvědomit, že data se vždycky ingestují do Azure Cosmos DB kontejnerů prostřednictvím transakčního úložiště. Když je povolený odkaz synapse, všechny nové vložení, aktualizace a odstranění se pak automaticky synchronizují do analytického úložiště.
* Synapse Apache Spark také podporuje strukturované streamování Sparku s Azure Cosmos DB jako zdroj a jímku. 

Následující části vás provedou syntaxí výše uvedených možností. Gesta v pracovním prostoru Azure synapse Analytics jsou navržená tak, aby poskytovala snadné prostředí, které vám umožní začít. Gesta se zobrazí po kliknutí pravým tlačítkem na kontejner Azure Cosmos DB na kartě **data** v pracovním prostoru synapse. Pomocí gest můžete rychle vygenerovat kód a přizpůsobit ho vašim potřebám. Gesta jsou také ideální ke zjišťování dat jedním kliknutím.

> [!IMPORTANT]
> Měli byste si uvědomit některá omezení v analytickém schématu, která by mohla vést k neočekávanému chování při operacích načítání dat.
> Jako příklad jsou v analytickém schématu k dispozici pouze první 1000 vlastnosti z transakčního schématu, nejsou k dispozici vlastnosti s mezerami atd. Pokud máte nějaké neočekávané výsledky, podívejte se na další podrobnosti v části [omezení schématu analytického úložiště](../../cosmos-db/analytical-store-introduction.md#schema-constraints) .

## <a name="query-azure-cosmos-db-analytical-store"></a>Dotaz Azure Cosmos DB analytického úložiště

Než se dozvíte o dvou možných možnostech pro dotazování Azure Cosmos DB analytického úložiště, nasazování do Spark dataframe a vytvoření tabulky Spark, je vhodné prozkoumat rozdíly ve zkušenostech, abyste si mohli vybrat možnost, která vyhovuje vašim potřebám.

Rozdíl v prostředí je okolo toho, zda se změny podkladových dat v kontejneru Azure Cosmos DB musí automaticky projevit v analýze provedené ve Sparku. Když je zaregistrován datový rámec Spark nebo je vytvořena tabulka Sparku proti analytickému úložišti kontejneru, metadata kolem aktuálního snímku dat v analytickém úložišti se načítají do Sparku pro efektivní přenos po následné analýze. Je důležité si uvědomit, že od Sparku následuje zásada opožděného vyhodnocení, pokud se v datovém rámci Sparku nevyvolá akce, nebo se SparkSQL dotaz na tabulku Spark, skutečná data se nenačte z analytického úložiště podkladového kontejneru.

Při **načítání do datového rámce Sparku** jsou načtená metadata uložená v mezipaměti po celou dobu relace Sparku. To znamená, že k vyhodnocení následných akcí prováděných s datovým rámcem se použije snímek analytického úložiště v okamžiku vytvoření datového rámce.

V případě **vytvoření tabulky Sparku** se metadata o stavu analytického úložiště neukládají do mezipaměti Sparku, ale znovu se načtou při každém spuštění dotazu SparkSQL do tabulky Sparku.

Můžete si tedy vybrat, jestli načtete datový rámec Sparku, nebo vytvoříte tabulku Sparku podle toho, jestli chcete k vyhodnocení analýzy Sparku použít pevně daný snímek analytického úložiště nebo jeho nejnovější snímek.

> [!NOTE]
> Pokud chcete zadat dotaz na rozhraní Azure Cosmos DB API účtů Mongo DB, přečtěte si další informace o [úplném vyjádření schématu přesnosti](../../cosmos-db/analytical-store-introduction.md#analytical-schema) v analytickém úložišti a o rozšířených názvech vlastností, které se mají použít.

### <a name="load-to-spark-dataframe"></a>Načíst do Spark dataframe

V tomto příkladu vytvoříte datový rámec Spark, který odkazuje na Azure Cosmos DB analytické úložiště. Pak můžete provést další analýzu vyvoláním akcí Sparku na datový rámec. Tato operace nemá vliv na transakční úložiště.

Syntaxe v **Pythonu** by byla následující:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

Ekvivalentní syntaxe v **Scala** by byla následující:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Vytvořit tabulku Spark

V tomto příkladu vytvoříte tabulku Spark, která odkazuje na Azure Cosmos DB analytické úložiště. Pak můžete provést další analýzu vyvoláním SparkSQL dotazů na tabulku. Tato operace nemá žádný vliv na transakční úložiště ani nepřenese žádné přesuny dat. Pokud se rozhodnete tuto tabulku Spark odstranit, nebude to mít vliv na příslušný kontejner Azure Cosmos DB ani na příslušné analytické úložiště. 

Tento scénář je vhodný k opakovanému použití tabulek Spark prostřednictvím nástrojů třetích stran a zajištění dostupnosti podkladových dat za běhu.

Syntaxe pro vytvoření tabulky Spark je následující:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Pokud máte scénáře, ve kterých se postupně mění schéma základního kontejneru s Azure Cosmos DB, a chcete, aby se aktualizované schéma automaticky promítlo v dotazech do tabulky Sparku, dosáhnete toho tak, že v možnostech tabulky Sparku nastavíte možnost `spark.cosmos.autoSchemaMerge` na `true`.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Zápis Spark dataframe do kontejneru Azure Cosmos DB

V tomto příkladu napíšete Spark dataframe do kontejneru Azure Cosmos DB. Tato operace bude mít vliv na výkon transakčních úloh a spotřebovávat jednotky požadavků zřízené v kontejneru Azure Cosmos DB nebo ve sdílené databázi.

Syntaxe v **Pythonu** by byla následující:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Ekvivalentní syntaxe v **Scala** by byla následující:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Načíst datový proud streamování z kontejneru
V tomto gestu použijete schopnost streamování Spark k načtení dat z kontejneru do datového rámce. Data se uloží do primárního účtu Data Lake (a systému souborů), který jste připojili k pracovnímu prostoru. 
> [!NOTE]
> Pokud chcete odkazovat na externí knihovny v synapse Apache Spark, přečtěte si další informace [tady](#external-library-management). Pokud třeba chcete ingestovat Spark dataframe do kontejneru Cosmos DB API pro Mongo DB, můžete využít konektor Mongo DB pro Spark [zde](https://docs.mongodb.com/spark-connector/master/).

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Načtení datového proudu streamování z kontejneru Azure Cosmos DB
V tomto příkladu použijete strukturované streamování Sparku k načtení dat z kontejneru Azure Cosmos DB do datového rámce Spark streamování pomocí funkce Change feed v Azure Cosmos DB. Data kontrolního bodu, která používá Spark, se uloží do primárního účtu Data Lake (a systému souborů), který jste připojili k pracovnímu prostoru.

Pokud složka */localReadCheckpointFolder* není vytvořená (v následujícím příkladu), vytvoří se automaticky. Tato operace bude mít vliv na výkon transakčních úloh a spotřebovávat jednotky požadavků zřízené v kontejneru Azure Cosmos DB nebo ve sdílené databázi.

Syntaxe v **Pythonu** by byla následující:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Ekvivalentní syntaxe v **Scala** by byla následující:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Zápis datového proudu streamování do kontejneru Azure Cosmos DB
V tomto příkladu napíšete datový proud streamování do kontejneru Azure Cosmos DB. Tato operace bude mít vliv na výkon transakčních úloh a spotřebovávat jednotky požadavků zřízené v kontejneru Azure Cosmos DB nebo ve sdílené databázi. Pokud složka */localWriteCheckpointFolder* není vytvořená (v následujícím příkladu), vytvoří se automaticky. 

Syntaxe v **Pythonu** by byla následující:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Ekvivalentní syntaxe v **Scala** by byla následující:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Správa externích knihoven

V tomto příkladu se dozvíte, jak odkazovat na externí knihovny ze souborů JAR při použití notebooků Spark v Synpase Apache Spark pracovních prostorech. Soubory JAR můžete umístit do kontejneru v primárním účtu Data Lake, který jste připojili k pracovnímu prostoru, a potom `%configure` do svého poznámkového bloku Spark přidat následující příkaz:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Pokud chcete odesílat definice vzdálených úloh Sparku do fondu Apache Spark bez serveru, můžete se dozvědět, jak odkazovat na externí knihovny pomocí tohoto [kurzu](../spark/apache-spark-job-definitions.md).

## <a name="next-steps"></a>Další kroky

* [Ukázky, které vám pomohou začít s odkazem na Azure synapse na GitHubu](https://aka.ms/cosmosdb-synapselink-samples)
* [Informace o tom, co je podporováno v Azure synapse s odkazem na Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Připojit k synapse odkazu pro Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
