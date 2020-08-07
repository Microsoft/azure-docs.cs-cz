---
title: Azure Cosmos DB Apache Spark konektor pro poznámky k verzi a prostředky SQL API
description: Přečtěte si všechny informace o konektoru Azure Cosmos DB Apache Spark pro rozhraní SQL API včetně data vydání, data o vyřazení a změn provedených mezi jednotlivými verzemi sady Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 72b3b190492be5cec9986729875c5b09e2559ae5
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854214"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark konektor pro jádro (SQL) API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Rozhraní .NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

Zrychlete analýzu velkých objemů dat pomocí konektoru Azure Cosmos DB Apache Spark pro jádro (SQL). Konektor Spark umožňuje spouštět úlohy [Sparku](https://spark.apache.org/) na datech uložených v Azure Cosmos DB. Zpracování dávek a datových proudů je podporováno.

Můžete použít konektor s [Azure Databricks](https://azure.microsoft.com/services/databricks) nebo [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), který poskytuje spravované Clustery Spark v Azure. V následující tabulce jsou uvedeny podporované verze Sparku.

| Komponenta | Verze |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x a 2.1. x |
| Scala | 2,11 |
| Verze modulu runtime Azure Databricks | > 3,4 |

> [!WARNING]
> Tento konektor podporuje rozhraní API jádra (SQL) Azure Cosmos DB.
> Pro Cosmos DB rozhraní MongoDB API použijte [konektor MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Pro Cosmos DB rozhraní API Cassandra použijte [konektor Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Užitečný obsah

| Obsah | Odkaz |
|---|---|
| **Stažení sady SDK** | [Stáhnout z Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Dokumentace k rozhraní API** | [Referenční informace pro Spark Connector]() |
|**Přispívání do sady SDK** | [Azure Cosmos DB konektor pro Apache Spark na GitHubu](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Začínáme** | [Urychlení analýz velkých objemů dat pomocí Apache Spark pro Azure Cosmos DB konektor](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Použití strukturovaného streamování Apache Spark s Apache Kafka a Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Historie verzí

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje hraniční případ kontrolního bodu streamování, kde v "ID" obsahuje znak "|" s použitou konfigurací "ChangeFeedMaxPagesPerBatch".

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nové funkce
* Přidá podporu pro hromadné aktualizace při použití vnořených klíčů oddílů.
* Přidá podporu pro datový typ Decimal a float během zápisu do Cosmos DB.
* Přidá podporu pro typy časových razítek při použití dlouhého (UNIX epocha) jako hodnoty.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje výjimku přetypování typů při použití konfigurace "WriteThroughputBudget".

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nové funkce
* Přidá informace o chybě pro hromadné selhání do výjimky a protokolu.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje problémy s kontrolními body streamování.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje úroveň protokolu zprávy bez úmyslného výskytu chyby úrovně pro snížení šumu.

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje chybu ve strukturovaném streamování během rozdělení oddílů – možná by to vedlo k chybějícím záznamům změn kanálu nebo zobrazení výjimek null pro zápisy kontrolního bodu.

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje chybu, při které se vlastní schéma zadané pro readStream ignoruje.

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opraví regrese (nestínovaný JAR zahrnuje všechny stínované závislosti), které zvyšují čas sestavení o 50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje problém závislosti, který způsobuje přímé přenosu přes protokol TCP, s RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nové funkce
* Vylepšuje správu připojení a sdružování připojení, aby se snížil počet volání metadat.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky

Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

Další informace o Apache Spark najdete [na domovské stránce](https://spark.apache.org/).