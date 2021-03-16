---
title: Jarní data Azure Cosmos DB v3 pro poznámky k verzi a prostředky SQL API
description: Přečtěte si o jarních datech Azure Cosmos DB v3 pro SQL API, včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/15/2021
ms.author: kuthapar
ms.custom: devx-track-java
ms.openlocfilehash: 3c740aa00b158c7ddbca3e4f61d79e37978223c2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493607"
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

> [!IMPORTANT]
> Pružinová data Azure Cosmos DB mají závislost na architektuře pro jarní data.
> 
> verze Azure-jaře-data-Cosmos z 3.0.0 do 3.4.0 podporují verze jarních dat 2,2 a 2,3.
> 
> Azure-jaře-data-Cosmos verze 3.5.0 a vyšší podporují data pružiny verze 2.4.3 a vyšší.
>

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

## <a name="get-started-fast"></a>Začínáme rychle

  Využijte [průvodce počátečním startem pružiny](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db), abyste mohli začít pracovat s jarními daty Azure Cosmos DB. Přístup k jaře Boot Starter je doporučeným způsobem, jak začít s používáním konektoru jarních dat Azure Cosmos DB.

  Alternativně můžete přidat Azure Cosmos DB závislost k `pom.xml` souboru, jak je znázorněno níže:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Užitečný obsah

| Content | Odkaz |
|---|---|
|**Stažení sady SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**Dokumentace k rozhraní API** | [Referenční dokumentace k rozhraní Java API](/java/api/com.azure.spring.data.cosmos) |
|**Přispívání do sady SDK** | [Azure SDK pro centrální úložiště Java na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Začínáme** | [Rychlý Start: Vytvoření aplikace Azure Cosmos DBch pružinových dat pro správu Azure Cosmos DB dat rozhraní SQL API](./create-sql-api-spring-data.md) <br> [Úložiště GitHub s kódem pro rychlý Start](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Ukázky základních kódů** | [Azure Cosmos DB: jarní data Azure Cosmos DB příklady pro rozhraní SQL API](sql-api-spring-data-sdk-samples.md) <br> [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Tipy pro zvýšení výkonu**| [Tipy ke zvýšení výkonu pro Java SDK v4 (platí pro pružinová data)](performance-tips-java-sdk-v4-sql.md)| 
| **Řešení potíží** | [Řešení potíží s Java SDK v4 (platí pro pružinová data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB semináře a cvičení** |[Domovská stránka inCosmos DBch Workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Další poznámky

* Jarní data Azure Cosmos DB podporují jazyky Java JDK 8 a Java JDK 11.
* Jarní data 2,3 se momentálně podporují, v současné době se nepodporují jarní data 2,4.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Přečtěte si další informace o [architektuře pružiny](https://spring.io/projects/spring-framework).

Přečtěte si další informace o [pružinovém spuštění](https://spring.io/projects/spring-boot).

Přečtěte si další informace o [jarních datech](https://spring.io/projects/spring-data).