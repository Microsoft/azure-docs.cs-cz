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
ms.openlocfilehash: 4b2d474f25209034034db092ca971bff6b78d73a
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068728"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Jarní data Azure Cosmos DB v3 pro Core (SQL) API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Rozhraní .NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Jarní data v2](sql-api-sdk-java-spring-v2.md)
> * [Jarní data V3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

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
> * [Jarní data pro Apache Cassandra s Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Jarní MongoDBy dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Jarní Gremliny dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Začněte tady

# <a name="explore"></a>[Zkoumání](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Tyto karty obsahují základní vzorky jarních dat Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurace závislostí

K dispozici jsou dvě Mavená Azure Cosmos DB data pružiny verze 3.

- Artefakt, který závisí na jaře data Framework verze 2,2:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-2-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

- Artefakt, který závisí na jaře data Framework verze 2,3:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-3-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Připojit](#tab/connect)

### <a name="connect"></a>Připojit

Zadejte Azure Cosmos DB účet a podrobnosti kontejneru. Jarní data Azure Cosmos DB automaticky vytvoří klienta a připojí se ke kontejneru.

[aplikace. vlastnosti](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
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

[Vytvořit](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Odstranit](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Dotaz](#tab/queries)

[Dotaz](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="resources"></a>Zdroje a prostředky

| Prostředek | Jarní data Framework 2,2 | Jarní data Framework 2,3 |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Přispívání do sady SDK** | [Jarní data Azure Cosmos DB úložiště na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Jarní data Azure Cosmos DB úložiště na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Kurz**| [Kurz pro jarní data Azure Cosmos DB na GitHubu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Kurz pro jarní data Azure Cosmos DB na GitHubu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Historie verzí

### <a name="300-beta2-unreleased"></a>3.0.0-beta. 2 (neuvolní se)

### <a name="300-beta1-august-17-2020"></a>3.0.0-beta. 1 (17. srpna 2020)
#### <a name="new-features"></a>Nové funkce
* Aktualizuje ID skupiny na `com.azure` .
* Aktualizuje ID artefaktu na `azure-spring-data-2-3-cosmos` .
* Aktualizuje závislost Azure-Cosmos SDK na `4.3.2-beta.2` .
* Přidává podporu pro entity auditování: automatickou správu `createdBy` polí s `createdDate` `lastModifiedBy` poznámkami,, a `lastModifiedDate` .
* Přidá `@GeneratedValue` podporu poznámek pro automatické generování ID pro pole ID `String` typu.
* Přidá podporu konfigurace více databází pro jeden Azure Cosmos DB účet s více databázemi a více Azure Cosmos DBch účtů s více databázemi.
* Přidá podporu pro `@Version` anotaci v libovolném poli řetězce.
* Aktualizuje návratové typy rozhraní API pro synchronizaci na `Iterable` typy namísto `List` .
* Zpřístupňuje `CosmosClientBuilder` z Azure Cosmos DB SDK jako jarní Bob do `@Configuration` třídy.
* Aktualizace `CosmosConfig` obsahují metriky dotazů a implementaci procesoru pro diagnostiku odpovědí.
* Přidá podporu pro vrácení `Optional` datového typu pro dotazy s jedním výsledkem.
#### <a name="renames"></a>Přejmenuje
* `CosmosDbFactory` na `CosmosFactory` .
* `CosmosDBConfig` na `CosmosConfig` .
* `CosmosDBAccessException` na `CosmosAccessException` .
* `Document` Anotace `Container` .
* `DocumentIndexingPolicy` Anotace `CosmosIndexingPolicy` .
* `DocumentQuery` na `CosmosQuery` .
* příznak Application. Properties `populateQueryMetrics` `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Plánování úlohy protokolování diagnostiky na `Parallel` vlákna, aby se zabránilo zablokování vstupně-výstupních vláken v síťovině.
* Opravuje optimistické zamykání při operaci DELETE.
* Opravuje problém s dotazy na uvozovací znaky `IN` klauzule for.
* Opraví problém tím, že povoluje `long` datový typ pro `@Id` .
* Opravuje problém tím, že umožňuje `boolean` , `long` , `int` a `double` jako datové typy pro `@PartitionKey` anotaci.
* Opravy `IgnoreCase` a `AllIgnoreCase` klíčová slova pro dotazy na ignorovat velikost písmen
* Odebere výchozí hodnotu jednotky žádosti 4 000 při automatickém vytvoření kontejnerů.

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Přečtěte si další informace o [architektuře pružiny](https://spring.io/projects/spring-framework).

Přečtěte si další informace o [pružinovém spuštění](https://spring.io/projects/spring-boot).

Přečtěte si další informace o [jarních datech](https://spring.io/projects/spring-data).