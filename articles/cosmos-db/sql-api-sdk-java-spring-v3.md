---
title: Jarní data Azure Cosmos DB v3 pro poznámky k verzi a prostředky SQL API
description: Přečtěte si všechno o jarních datech Azure Cosmos DB v3 pro SQL API, včetně data vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 04a50d37606754ff4540d1056e378d46388e2592
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590636"
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

Pružinová data Azure Cosmos DB v3 pro Core (SQL) umožňují vývojářům využít Azure Cosmos DB v aplikacích pružiny. Jarní data Azure Cosmos DB zpřístupňuje rozhraní jarních dat pro práci s databázemi a kolekcemi, práci s dokumenty a vydávání dotazů. Synchronní i asynchronní (opětovně aktivní) rozhraní API se podporují ve stejném artefaktu Maven. 

Pružinová data Azure Cosmos DB přebírají závislost na architektuře jarních dat. Azure Cosmos DB tým SDK vydává artefakty Maven pro jarní data v 2.2 a v 2.3.

[Jarní architektura](https://spring.io/projects/spring-framework) je model programování a konfigurace, který zjednodušuje vývoj aplikací Java. Aby se na webu organizace mohla zacitovat, pružina zjednodušuje "domovníing" aplikací pomocí injektáže závislostí. Mnoho vývojářů, jako je pružina, protože sestavování a testování aplikací se bude mnohem jednodušší. [Pružinové spouštění](https://spring.io/projects/spring-boot) rozšiřuje tuto představu o tom, jak se tato instalace prokládá s ohledem na vývoj webových aplikací a mikroslužeb. [Pružinová data](https://spring.io/projects/spring-data) jsou programovací model a architektura pro přístup k úložišti dat, jako je například Azure Cosmos DB z kontextu aplikace pružinové nebo jarního spuštění. 

Ve [jarních cloudových aplikacích Azure](https://azure.microsoft.com/services/spring-cloud/) můžete použít jarní data Azure Cosmos DB.

> [!IMPORTANT]  
> Tyto poznámky k verzi jsou k disAzure Cosmos DB pro 3 – Pružinová data. Poznámky k verzi v2 najdete [tady](sql-api-sdk-java-spring-v2.md). 
>
> Jarní data Azure Cosmos DB podporují jenom rozhraní SQL API.
>
> Následující příručky podporují jarní data u jiných Azure Cosmos DB rozhraní API:
> * [Jarní data pro Apache Cassandra s Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Jarní MongoDBy dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Jarní Gremliny dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Začněte tady

# <a name="explore"></a>[Zkoumání](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>Přejděte na karty výše pro základní Azure Cosmos DB ukázek dat na jaře.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurace závislostí

K dispozici jsou dvě Mavená Azure Cosmos DB data pružiny v3.

Artefakt, který závisí na jaře data Framework v 2.2:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

Artefakt, který závisí na jaře data Framework v 2.3:
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

### <a name="query"></a>Dotaz

[Dotaz](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Užitečný obsah

| Obsah | Jarní data Framework v 2.2 | Jarní data Framework v 2.3 |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Přispívání do sady SDK** | [Jarní data Azure Cosmos DB úložiště na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Jarní data Azure Cosmos DB úložiště na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Kurz**| [Kurz pro jarní data Azure Cosmos DB na GitHubu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Kurz pro jarní data Azure Cosmos DB na GitHubu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Historie verzí

### <a name="300-beta1-2020-08-17"></a>3.0.0-beta. 1 (2020-08-17)
#### <a name="new-features"></a>Nové funkce
* ID skupiny bylo aktualizováno na `com.azure` .
* ID artefaktu se aktualizovalo na `azure-spring-data-2-3-cosmos` .
* Aktualizace závislosti sady Azure-Cosmos SDK na `4.3.2-beta.2` .
* Podpora pro entity auditování – Automatická správa polí s poznámkami createdBy, createdDate, lastModifiedBy a lastModifiedDate.
* `@GeneratedValue` Podpora poznámek pro automatické generování ID pro pole ID `String` typu
* Podpora konfigurace více databází pro jeden účet Cosmos s více databázemi a více účtů Cosmos s více databázemi.
* Podpora pro `@Version` anotaci v libovolném poli řetězce
* Z rozhraní API pro synchronizaci se vracely návratové typy `Iterable` namísto `List` .
* Zpřístupnění `CosmosClientBuilder` ze sady Cosmos SDK jako jarní Bob do `@Configuration` třídy.
* Aktualizováno `CosmosConfig` tak, aby obsahovalo metriky dotazů a implementaci procesoru diagnostiky odpovědí.
* Podpora vrácení `Optional` datového typu pro dotazy s jedním výsledkem.
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
* Opravilo se optimistické zamykání při operaci DELETE.
* Opraven problém s dotazy na uvozovací znaky `IN` klauzule for
* Opravili jsme problémy tím, že povolíte `long` datový typ pro `@Id` .
* Opravili jsme problém tím, že povolíte `boolean` , `long` `int` `double` jako datové typy pro `@PartitionKey` anotaci.
* Pevná `IgnoreCase`  &  `AllIgnoreCase` klíčová slova pro dotazy na ignorované případy
* Byla odstraněna výchozí hodnota jednotky žádosti 4000 při automatickém vytváření kontejnerů.

## <a name="faq"></a>Časté otázky
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

Další informace o architektuře pružiny najdete na [domovské stránce projektu](https://spring.io/projects/spring-framework).

Další informace o pružinovém spuštění najdete na [domovské stránce projektu](https://spring.io/projects/spring-boot).

Další informace o jarních datech najdete na [domovské stránce projektu](https://spring.io/projects/spring-data).