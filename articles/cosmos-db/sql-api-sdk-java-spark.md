---
title: Cosmos DB Apache Spark konektor pro poznámky k verzi a prostředky SQL API
description: Přečtěte si o konektoru Azure Cosmos DB Apache Spark pro rozhraní SQL API, včetně data vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi sady Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4ee7e180094c1584995e5d734adbe589a112754b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477568"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark konektor pro jádro (SQL) API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Sada .NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

Analýzu velkých objemů dat můžete urychlit pomocí konektoru Azure Cosmos DB Apache Spark pro jádro (SQL). Konektor Spark umožňuje spouštět úlohy [Sparku](https://spark.apache.org/) na datech uložených v Azure Cosmos DB. Zpracování dávek a datových proudů je podporováno.

Můžete použít konektor s [Azure Databricks](https://azure.microsoft.com/services/databricks) nebo [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), který poskytuje spravované Clustery Spark v Azure. Podporované verze jsou uvedené v následující tabulce:

| Komponenta | Verze |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x*a 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (verze modulu runtime) | Později než 3,4 |

> [!WARNING]
> Tento konektor podporuje rozhraní API jádra (SQL) Azure Cosmos DB.
> Pro rozhraní Cosmos DB API pro MongoDB použijte [konektor MongoDB pro Spark](https://docs.mongodb.com/spark-connector/master/).
> Pro rozhraní API Cassandra Cosmos DB použijte [konektor Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Zdroje a prostředky

| Prostředek | Odkaz |
|---|---|
| **Stažení sady SDK** | [Stáhnout nejnovější. jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**Dokumentace k rozhraní API** | [Referenční informace pro Spark Connector]() |
|**Přispívání do sady SDK** | [Azure Cosmos DB konektor pro Apache Spark na GitHubu](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Začínáme** | [Urychlení analýz velkých objemů dat pomocí Apache Spark pro Azure Cosmos DB konektor](./spark-connector.md#bk_working_with_connector) <br> [Použití strukturovaného streamování Apache Spark s Apache Kafka a Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Historie verzí

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nové funkce
- Přidá novou možnost konfigurace, `changefeedstartfromdatetime` která se dá použít k určení počátečního času, kdy se má changefeed zpracovat. Další informace najdete v tématu [Možnosti konfigurace](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references).

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
- Opravuje regresi, která způsobila nadměrné využití paměti u prováděcích modulů pro velké sady výsledků dotazu (například s miliony řádků), a to v důsledku chyby `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje hraniční případ kontrolního bodu streamování, ve kterém `ID` obsahuje znak kanálu (|) s `ChangeFeedMaxPagesPerBatch` použitou konfigurací.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nové funkce
* Přidá podporu pro hromadné aktualizace při použití vnořených klíčů oddílů.
* Přidá podporu pro datový typ Decimal a float během zápisu do Azure Cosmos DB.
* Přidá podporu pro typy časových razítek při použití Long (UNIX epocha) jako hodnoty.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje výjimku přetypovat, ke které dojde při `WriteThroughputBudget` použití konfigurace.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nové funkce
* Přidá informace o chybě pro hromadné selhání do výjimky a protokolu.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje problémy s kontrolními body streamování.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Aby se snížila hladina hluku, opravuje úroveň protokolu zprávy bez úmyslného výskytu chyby úrovně.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje chybu ve strukturovaném streamování během rozdělení oddílů. Tato chyba by mohla vést k chybějícím záznamům kanálu změn nebo výjimkám null pro zápis kontrolních bodů.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje chybu, která způsobí ignorování vlastního schématu poskytnutého pro readStream.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje regresi (nestínovaný JAR zahrnuje všechny stínované závislosti), které zvyšují čas sestavení o 50 procent.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje problém závislosti, který způsobí, že přímý přenos přes protokol TCP selže s RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nové funkce
* Vylepšuje správu připojení a sdružování připojení, aby se snížil počet volání metadat.

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Přečtěte si další informace o [Apache Sparku](https://spark.apache.org/).
