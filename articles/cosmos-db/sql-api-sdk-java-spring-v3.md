---
title: Jarní data Azure Cosmos DB v3 pro poznámky k verzi a prostředky SQL API
description: Přečtěte si o jarních datech Azure Cosmos DB v3 pro SQL API, včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 64054a2bb5c1f7e17eef87c3babb28137b6c912a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097120"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Jarní data Azure Cosmos DB v3 pro Core (SQL) API: poznámky k verzi a prostředky
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
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

Jarní data Azure Cosmos DB verze 3 pro jádro (SQL) umožňuje vývojářům používat Azure Cosmos DB v aplikacích pružiny. Jarní data Azure Cosmos DB zpřístupňuje rozhraní jarních dat pro práci s databázemi a kolekcemi, práci s dokumenty a vydávání dotazů. Synchronní i asynchronní (opětovně aktivní) rozhraní API se podporují ve stejném artefaktu Maven. 

Pružinová data Azure Cosmos DB mají závislost na architektuře pro jarní data. Tým Azure Cosmos DB SDK vydává artefakty Maven pro data jarní verze 2,2 a 2,3.

[Jarní architektura](https://spring.io/projects/spring-framework) je model programování a konfigurace, který zjednodušuje vývoj aplikací Java. Pružina zjednodušuje "domovníing" aplikací pomocí injektáže závislostí. Mnoho vývojářů, jako je pružina, protože usnadňuje sestavování a testování aplikací. [Pružinové spouštění](https://spring.io/projects/spring-boot) rozšiřuje toto zpracování instalace s využitím očí pro vývoj webových aplikací a mikroslužeb. [Pružinová data](https://spring.io/projects/spring-data) jsou programovací model a architektura pro přístup k datovým úložišti, jako je Azure Cosmos DB z kontextu aplikace pružinové nebo jarního spuštění. 

Ve [jarních cloudových aplikacích Azure](https://azure.microsoft.com/services/spring-cloud/) můžete použít jarní data Azure Cosmos DB.

> [!IMPORTANT]  
> Tyto poznámky k verzi jsou pro Azure Cosmos DB verze 3 jarních dat. [Poznámky k verzi 2 najdete tady](sql-api-sdk-java-spring-v2.md). 
>
> Jarní data Azure Cosmos DB podporují pouze rozhraní SQL API.
>
> V těchto článcích najdete informace o jarních datech v jiných Azure Cosmos DB rozhraních API:
> * [Jarní data pro Apache Cassandra s Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Jarní MongoDBy dat pomocí Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Jarní Gremliny dat pomocí Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Začněte tady

# <a name="explore"></a>[Zkoumání](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Tyto karty obsahují základní vzorky jarních dat Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurace závislostí

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Připojit](#tab/connect)

### <a name="connect"></a>Připojit

Zadejte Azure Cosmos DB účet a podrobnosti kontejneru. Jarní data Azure Cosmos DB automaticky vytvoří klienta a připojí se ke kontejneru.

[aplikace. vlastnosti](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Operace dokumentu doc](#tab/docs)

### <a name="document-operations"></a>Operace dokumentů

---

## <a name="resources"></a>Zdroje a prostředky

* **Přispívání do sady SDK** : [jarní data Azure Cosmos DB úložiště na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Kurz** : [Azure Cosmos DB kurz pro jarní data na GitHubu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Přečtěte si další informace o [architektuře pružiny](https://spring.io/projects/spring-framework).

Přečtěte si další informace o [pružinovém spuštění](https://spring.io/projects/spring-boot).

Přečtěte si další informace o [jarních datech](https://spring.io/projects/spring-data).