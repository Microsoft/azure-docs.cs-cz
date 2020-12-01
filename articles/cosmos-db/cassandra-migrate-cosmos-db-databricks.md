---
title: Migrace dat z Apache Cassandra na Azure Cosmos DB rozhraní API Cassandra pomocí datacihlů (Spark)
description: Naučte se migrovat data z databáze Apache Cassandra do Azure Cosmos DB rozhraní API Cassandra pomocí Azure Databricks a Sparku.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 827abcdb9eb2eb8b7328eb3e3a78f49d51e87e6b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349176"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Migrace dat z Cassandra do Azure Cosmos DB rozhraní API Cassandra účtu pomocí Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Rozhraní API Cassandra v Azure Cosmos DB se stal skvělou volbou pro podniková zatížení, která běží na Apache Cassandra, z nejrůznějších důvodů: 

* **Žádná režie správy a monitorování:** Eliminuje režijní náklady na správu a monitorování nesčetných nastavení napříč systémy souborů OS, JVM a YAML a jejich interakcemi.

* **Významné úspory nákladů:** Náklady můžete ušetřit pomocí Azure Cosmos DB, což zahrnuje náklady na virtuální počítač, šířku pásma a příslušné licence. Navíc nemusíte spravovat datové centra, servery, úložiště SSD, sítě a náklady na elektřinu. 

* **Možnost použít existující kód a nástroje:** Azure Cosmos DB poskytuje kompatibilitu na úrovni přenosů protokolů se stávajícími sadami SDK a nástroji Cassandra. Tato kompatibilita zajišťuje, že můžete použít stávající základ kódu s rozhraním API Cassandra v Azure Cosmos DB s nepatrnými změnami.

Existují různé způsoby, jak migrovat databázové úlohy z jedné platformy do jiné. [Azure Databricks](https://azure.microsoft.com/services/databricks/) je platforma jako nabídka služeb pro [Apache Spark](https://spark.apache.org/) , která nabízí způsob, jak provádět offline migrace ve velkém měřítku. Tento článek popisuje kroky, které jsou potřebné k migraci dat z nativních Cassandra a tabulek pro Apache, do Azure Cosmos DB rozhraní API Cassandra pomocí Azure Databricks.

## <a name="prerequisites"></a>Požadavky

* [Zřídit účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Projděte si základy připojení k Azure Cosmos DB rozhraní API Cassandra](cassandra-spark-generic.md)

* Zkontrolujte [podporované funkce v Azure Cosmos DB rozhraní API Cassandra](cassandra-support.md) , aby se zajistila kompatibilita.

* Ujistěte se, že jste už vytvořili prázdné místo a tabulky v cílovém Azure Cosmos DB rozhraní API Cassandram účtu.

* [Použít cqlsh nebo hostované prostředí pro ověření](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Zřízení clusteru Azure Databricks

Podle pokynů můžete [zřídit cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Upozorňujeme však, že Apache Spark 3. x se v současnosti pro konektor Apache Cassandra nepodporuje. Modul runtime datacihly bude potřeba zřídit s podporovanou verzí v2. x Apache Spark. Doporučujeme verzi 6,6 modulu runtime datacihly:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Runtime datacihly":::


## <a name="add-dependencies"></a>Přidat závislosti

Abyste se mohli připojit k nativním i Cosmos DB koncovým bodům Cassandra, budete muset do svého clusteru přidat knihovnu Apache Spark konektoru Cassandra. V clusteru vyberte knihovny – > nainstalovat balíčky New-> Maven-> Search:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Balíčky pro vyhledávání datacihlů":::

`Cassandra`Do vyhledávacího pole zadejte a vyberte nejnovější `spark-cassandra-connector` dostupné úložiště Maven a pak vyberte instalovat:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Datacihly – výběr balíčků":::

> [!NOTE]
> Po nainstalování knihovny konektoru Cassandra se ujistěte, že restartujete cluster datacihly.

## <a name="create-scala-notebook-for-migration"></a>Vytvoření poznámkového bloku Scala pro migraci

V datacihlách vytvořte Scala Poznámkový blok s následujícím kódem. Nahraďte své zdrojové a cílové konfigurace Cassandra odpovídajícími přihlašovacími údaji a zdrojovými a cílovými prostory a tabulkami a pak spusťte:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> `spark.cassandra.output.concurrent.writes`Konfigurace a `connections_per_executor_max` jsou důležité pro zamezení [Omezení četnosti](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/), která se stane, když požadavky na Cosmos DB překračují zřízenou propustnost ([jednotky žádostí](./request-units.md)). Tato nastavení může být nutné upravit v závislosti na počtu prováděcích modulů v clusteru Spark a potenciálně velikosti (a tedy i nákladů na RU) každého záznamu zapsaného do cílových tabulek.

## <a name="next-steps"></a>Další kroky

* [Zřízení propustnosti u kontejnerů a databází](set-throughput.md) 
* [Doporučené postupy pro klíč oddílu](partitioning-overview.md#choose-partitionkey)
* [Odhad ru/s pomocí článků Azure Cosmos DB Capacity Planneru](estimate-ru-with-capacity-planner.md)
* [Elastické škálování v Azure Cosmos DB rozhraní API Cassandra](manage-scale-cassandra.md)