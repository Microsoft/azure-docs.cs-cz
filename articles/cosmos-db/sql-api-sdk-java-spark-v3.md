---
title: Azure Cosmos DB Apache Spark 3 OLTP konektor pro SQL API (Preview) poznámky k verzi a prostředky
description: Přečtěte si o Azure Cosmos DB konektoru Apache Spark 3 OLTP pro rozhraní SQL API (Preview), včetně dat o verzích, data odchodu a změn provedených mezi jednotlivými verzemi sady Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: e7d206b63d6e1bf741a5dc298dd5bbc2d48ab11b
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368591"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-preview-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 3 OLTP konektor pro jádro (SQL) API (Preview): poznámky k verzi a prostředky
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
> * [Konektor Spark 3 OLTP](sql-api-sdk-java-spark-v3.md)
> * [Konektor Spark 2 OLTP](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

**Azure Cosmos DB konektor OLTP Spark 3 (Preview)** poskytuje podporu Apache Spark v3 pro Azure Cosmos DB s použitím rozhraní SQL API.
[Azure Cosmos DB](introduction.md) je globálně distribuovaná databázová služba, která vývojářům umožňuje pracovat s daty pomocí různých standardních rozhraní API, jako jsou SQL, MongoDB, Cassandra, Graph a Table.

> [!Note]
> Tato verze Azure Cosmos DB konektoru Spark 3 OLTP je sestavení verze Preview.
> Toto sestavení nebylo načteno nebo bylo testováno z hlediska výkonu.
> Toto sestavení se nedoporučuje používat v produkčních scénářích.
>

## <a name="documentation"></a>Dokumentace

- [Začínáme](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [Rozhraní API pro katalog](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [Odkaz na konfigurační parametr](https://github.com/Azure/azure-sdk-for-java/blob/feature/cosmos/spark30/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>Kompatibilita verzí

| Konektor     | Spark         | Minimální verze Java | Podporované verze Scala |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0-beta. 1  | 3.1.1         |        8             | 2,12                     |

## <a name="download"></a>Stáhnout

K automatické instalaci konektoru Sparku do Databricks Runtime 8 z Maven můžete použít souřadnici Mavenu jar. `com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.0.0-beta.1`

V projektu SBT můžete také integrovat do konektoru Spark Cosmos DB:
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.0.0-beta.1"
```

Konektor Cosmos DB Spark je k dispozici v [centrálním úložišti Maven](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/4.0.0-beta.1/jar).

### <a name="general"></a>Obecné

Pokud narazíte na chybu, uveďte prosím problém [tady](https://github.com/Azure/azure-sdk-for-java/issues/new).

Chcete-li navrhnout novou funkci nebo změny, které by mohly být provedeny, vydejte problém stejným způsobem jako u chyby.

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>Další kroky

Projděte si náš [Průvodce rychlým startem pro práci s Azure Cosmos DB konektor OLTP Spark 3](create-sql-api-spark.md).
