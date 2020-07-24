---
title: Dotaz Azure Cosmos DB analytického úložiště (Preview) s Apache Spark
description: Postup dotazování Azure Cosmos DB analýzy pomocí Apache Spark pro Azure synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a7ee04c922e4373414dc27ed2b7c98be605280e5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089103"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Dotaz Azure Cosmos DB analytické úložiště (Preview) s Apache Spark pro Azure synapse Analytics

Tento článek obsahuje příklady, jak můžete pracovat s analytickým úložištěm z gest synapse. Gesta se zobrazí po kliknutí pravým tlačítkem na kontejner. Pomocí gest můžete rychle vygenerovat kód a přizpůsobit ho vašim potřebám. Gesta jsou také ideální ke zjišťování dat jedním kliknutím.

## <a name="load-to-dataframe"></a>Načíst do datového rámce

V tomto kroku načtete data z Azure Cosmos DB analytického úložiště v datovém rámci Sparku. Zobrazí se 10 řádků z datového rámce s názvem ***DF***. Jakmile jsou data do datového rámce, můžete provést další analýzu.

Tato operace nemá vliv na transakční úložiště.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

Ekvivalentní gesto kódu v **Scala** by byl následující kód:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Vytvořit tabulku Spark

V tomto gestu vytvoříte tabulku Spark ukazující na vybraný kontejner. Tato operace se neúčtují při přesunu dat. Pokud se rozhodnete tuto tabulku odstranit, nebude to mít vliv na příslušný kontejner (a odpovídající analytické úložiště). 

Tento scénář je vhodný pro opětovné použití tabulek prostřednictvím nástrojů třetích stran a poskytuje přístup k datům za běhu.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Zapsat datový rámec do kontejneru

V tomto gestu zapíšete do kontejneru datový rámec. Tato operace ovlivní transakční výkon a využití jednotek žádosti. Použití Azure Cosmos DB transakčního výkonu je ideální pro transakce zápisu. Ujistěte se, že nahradíte **YOURDATAFRAME** datovým rámcem, na který chcete zapisovat.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Ekvivalentní gesto kódu v **Scala** by byl následující kód:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Načíst datový proud streamování z kontejneru
V tomto gestu použijete schopnost streamování Spark k načtení dat z kontejneru do datového rámce. Data budou uložena v primárním účtu Data Lake (a systému souborů), který jste připojili k pracovnímu prostoru. 

Pokud se složka */localReadCheckpointFolder* nevytvoří, vytvoří se automaticky. Tato operace ovlivní transakční výkon Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Ekvivalentní gesto kódu v **Scala** by byl následující kód:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Zápis datového proudu streamování do kontejneru
V tomto gestu zapíšete datový proud streamování do Azure Cosmos DB kontejneru, který jste vybrali. Pokud se složka */localReadCheckpointFolder* nevytvoří, vytvoří se automaticky. Tato operace ovlivní transakční výkon Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Ekvivalentní gesto kódu v **Scala** by byl následující kód:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Další kroky

* [Přečtěte si, co je podporováno mezi synapse a Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Připojit k synapse odkazu pro Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
