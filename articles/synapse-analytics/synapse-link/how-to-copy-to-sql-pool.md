---
title: Kopírovat synapse odkaz na data Azure Cosmos DB do fondu SQL pomocí Apache Spark
description: Načtěte data do datového rámce Spark, nahrajte data a načtěte je do tabulky fondu SQL.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 409f1ecee5ccf42a0168d500b40337366e07bfc0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287846"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-sql-pool-using-apache-spark"></a>Kopírování dat z Azure Cosmos DB do fondu SQL pomocí Apache Spark

Odkaz na Azure synapse pro Azure Cosmos DB umožňuje uživatelům spouštět analýzy v reálném čase nad provozními daty v Azure Cosmos DB. Existují však situace, kdy je potřeba agregovat a rozšířit některá data pro poskytování uživatelů datového skladu. Data odkazů synapse se dají dělat v poznámkovém bloku a exportovat je jenom v několika buňkách.

## <a name="prerequisites"></a>Požadavky
* [Zřídit pracovní prostor synapse](../quickstart-create-workspace.md) s:
    * [Fond Spark](../quickstart-create-apache-spark-pool-studio.md)
    * [Fond SQL](../quickstart-create-sql-pool-studio.md)
* [Zřízení účtu Cosmos DB s využitím kontejneru HTAP s daty](../../cosmos-db/configure-synapse-link.md)
* [Připojení kontejneru Azure Cosmos DB HTAP k pracovnímu prostoru](./how-to-connect-synapse-link-cosmos-db.md)
* [Správné nastavení pro import dat do fondu SQL ze Sparku](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Postup
V tomto kurzu se připojíte ke službě analytické úložiště, takže nebude mít žádný dopad na transakční úložiště (nespotřebovává žádné jednotky žádostí). Projděte si následující kroky:
1. Načtení kontejneru Cosmos DB HTAP do datového rámce Spark
2. Agregace výsledků v novém dataframe
3. Ingestování dat do fondu SQL

[![Kroky pro Spark na SQL 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Data
V tomto příkladu používáme kontejner HTAP s názvem **RetailSales**. Je součástí propojené služby s názvem **ConnectedData**a má následující schéma:
* _rid: řetězec (Nullable = true)
* _ts: Long (Nullable = true)
* logQuantity: Double (Nullable = true)
* productCode: řetězec (Nullable = true)
* množství: Long (Nullable = true)
* Price: Long (Nullable = true)
* ID: řetězec (Nullable = true)
* inzerce: Long (Nullable = true)
* storeId: Long (Nullable = true)
* weekStarting: Long (Nullable = true)
* _etag: řetězec (Nullable = true)

Pro účely generování sestav agregujeme prodej (*množství*, *výnosy* (cena × množství) podle hodnoty *productCode* a *weekStarting* . Nakonec tato data exportujeme do tabulky fondu SQL s názvem **dbo. ProductSales**.

## <a name="configure-a-spark-notebook"></a>Konfigurace poznámkového bloku Spark
Vytvořte notebook Spark s Scala jako Spark (Scala) jako hlavní jazyk. Pro relaci používáme výchozí nastavení poznámkového bloku.

## <a name="read-the-data-in-spark"></a>Čtení dat ve Sparku
Přečtěte si kontejner Cosmos DB HTAP pomocí Sparku do datového rámce v první buňce.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Agregace výsledků v novém dataframe

Ve druhé buňce spustíme transformaci a agregace potřebné pro nový datový rámec předtím, než je načtete do databáze fondu SQL.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-sql-pool"></a>Načtení výsledků do fondu SQL

Do třetí buňky načteme data do fondu SQL. Automaticky se vytvoří dočasná externí tabulka, externí zdroj dat a formát externího souboru, který se po dokončení úlohy odstraní.

```java
df_olap_aggr.write.sqlanalytics("arnaudpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Dotazování výsledků pomocí SQL

Dotaz na výsledek můžete zadat pomocí jednoduchého dotazu SQL, jako je například následující skript SQL:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

Dotaz bude v režimu grafu prezentovat následující výsledky: [ ![ Spark to SQL Steps 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Další kroky
* [Dotaz Azure Cosmos DB analytické úložiště s Apache Spark](./how-to-query-analytical-store-spark.md)