---
title: Jarní data Azure Cosmos DB v2 pro poznámky k verzi a prostředky SQL API
description: Přečtěte si o jarních datech Azure Cosmos DB v2 pro rozhraní SQL API, včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 8d795624097877d20f98a6fd205fb7136cf38007
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069120"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Jarní data Azure Cosmos DB v2 pro Core (SQL) API: poznámky k verzi a prostředky
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

 Jarní data Azure Cosmos DB verze 2 pro jádro (SQL) umožňuje vývojářům používat Azure Cosmos DB v aplikacích pružiny. Jarní data Azure Cosmos DB zpřístupňuje rozhraní jarních dat pro práci s databázemi a kolekcemi, práci s dokumenty a vydávání dotazů. Synchronní i asynchronní (opětovně aktivní) rozhraní API se podporují ve stejném artefaktu Maven. 

[Jarní architektura](https://spring.io/projects/spring-framework) je model programování a konfigurace, který zjednodušuje vývoj aplikací Java. Pružina zjednodušuje "domovníing" aplikací pomocí injektáže závislostí. Mnoho vývojářů, jako je pružina, protože usnadňuje sestavování a testování aplikací. [Pružinové spouštění](https://spring.io/projects/spring-boot) rozšiřuje toto zpracování instalace s využitím očí pro vývoj webových aplikací a mikroslužeb. [Pružinová data](https://spring.io/projects/spring-data) jsou programovací model pro přístup k datovým úložišti, jako je Azure Cosmos DB z kontextu aplikace pružinové nebo jarního spuštění. 

Ve [jarních cloudových aplikacích Azure](https://azure.microsoft.com/services/spring-cloud/) můžete použít jarní data Azure Cosmos DB.

> [!IMPORTANT]  
> Tyto poznámky k verzi jsou k disAzure Cosmos DB ve verzi 2 jarních dat. [Poznámky k verzi pro verzi 3 najdete tady](sql-api-sdk-java-spring-v3.md). 
>
> Jarní data Azure Cosmos DB podporují pouze rozhraní SQL API.
>
> Informace o jarních datech v jiných Azure Cosmos DB rozhraních API najdete v následujících článcích:
> * [Jarní data pro Apache Cassandra s Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Jarní MongoDBy dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Jarní Gremliny dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Chcete se rychle dostat do budoucna?
> 1. Nainstalujte [Minimální podporovaný běhový modul Java, JDK 8](/java/azure/jdk/?view=azure-java-stable), abyste mohli použít sadu SDK.
> 2. Pomocí [počátečního startu](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)Vytvořte Azure Cosmos DBovou datovou aplikaci. Je to snadné!
> 3. Projděte si [příručku pro vývojáře Azure Cosmos DB na jaře](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb), která vás provede základními požadavky Azure Cosmos DB.
>
> Pomocí [jarních Initializr](https://start.spring.io/)můžete rychle aktivovat aplikace na jaře Boot Starter.
>

## <a name="resources"></a>Zdroje a prostředky

| Prostředek | Odkaz |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Dokumentace k rozhraní API** | [Data pružiny Azure Cosmos DB Referenční dokumentace]() |
|**Přispívání do sady SDK** | [Jarní data Azure Cosmos DB úložiště na GitHubu](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Jaře Boot Starter**| [Azure Cosmos DB klientskou knihovnu Starter Boot Boot Library pro Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Ukázka aplikace pružiny TODO pomocí Azure Cosmos DB**| [Komplexní prostředí Java v systému App Service Linux (část 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Příručka pro vývojáře** | [Příručka pro vývojáře Spring Data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Pomocí Starter** | [Jak používat jaře Boot Starter s Azure Cosmos DB SQL API](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Úložiště GitHub pro Azure Cosmos DB jaře Boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Ukázka pomocí Azure App Service** | [Jak používat Spring a Cosmos DB s App Service v Linuxu](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Ukázka aplikace TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Historie verzí

### <a name="230-may-21-2020"></a>2.3.0 (květen 21. května 2020)
#### <a name="new-features"></a>Nové funkce
* Aktualizuje verzi na jaře pro spuštění na 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (Květen 19. května 2020)
#### <a name="new-features"></a>Nové funkce
* Aktualizuje Azure Cosmos DB verze na 3.7.3.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Obsahuje opravy nevracení paměti a upgrady verze z Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6. dubna 2020)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje `allowTelemetry` příznak, ze kterého se má brát v úvahu `CosmosDbConfig` .
* Opravuje `TTL` vlastnost v kontejneru.

### <a name="223-february-25-2020"></a>2.2.3 (25. února 2020)
#### <a name="new-features"></a>Nové funkce
* Přidá nové `findAll` pomocí klíčového rozhraní API pro oddíl.
* Aktualizuje Azure Cosmos DB verze na 3.7.0.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravuje `collectionName`  ->  `containerName` .
* Opravy `entityClass` a `domainClass`  ->  `domainType` .
* Opravuje "návratovou kolekci entit uloženou úložištěm namísto vstupní entity."

### <a name="2110-february-25-2020"></a>2.1.10 (25. února 2020)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Náprava – oprava pro "vrácenou kolekci entit uloženou úložištěm namísto vstupní entity"

### <a name="222-january-15-2020"></a>2.2.2 (15. ledna 2020)
#### <a name="new-features"></a>Nové funkce
* Aktualizuje Azure Cosmos DB verze na 3.6.0.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="221-december-31-2019"></a>2.2.1 (31. prosince 2019)
#### <a name="new-features"></a>Nové funkce
* Aktualizuje Azure Cosmos DB verze sady SDK na 3.5.0.
* Přidá pole Anotace pro povolení nebo zakázání automatického vytváření kolekcí.
* Vylepšuje zpracování výjimek. Zveřejňuje `CosmosClientException` `CosmosDBAccessException` .
* Zpřístupňuje `requestCharge` a `activityId` prostřednictvím `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* 3.5.0 opravy aktualizací sady SDK "výjimka, když Cosmos DB hlavička odpovědi HTTP je větší než 8192 bajtů, ConsistencyPolicy. defaultConsistencyLevel () se nezdařila v ohraničené neaktuálnosti a předponě konzistence.
* Opravuje `findById` chování metody. Tato metoda dříve vrátila prázdnou, pokud nebyla nalezena entita namísto vyvolání výjimky.
* Opravuje chybu, ve které bylo řazení nebylo použito při použití na další stránce `CosmosPageRequest` .

### <a name="219-december-26-2019"></a>2.1.9 (26. prosince 2019)
#### <a name="new-features"></a>Nové funkce
* Přidá pole Anotace pro povolení nebo zakázání automatického vytváření kolekcí.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
*  Opravuje `findById` chování metody. Tato metoda dříve vrátila prázdnou, pokud nebyla nalezena entita namísto vyvolání výjimky.

### <a name="220-october-21-2019"></a>2.2.0 (21. října 2019)
#### <a name="new-features"></a>Nové funkce
* Dokončete podporu reaktivního úložiště Cosmos.
* Azure Cosmos DB řetězec diagnostiky žádosti a podporu metrik dotazů.
* Aktualizace verze Azure Cosmos DB SDK na 3.3.1.
* Upgrade verze architektury pružiny na 5.2.0. RELEASE.
* Napřed upgrade verze na jaře data na 2.2.0. RELEASE.
* Přidá `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` rozhraní API a.
* Odebere závislost z Azure-DocumentDB.
* Přejmenuje DocumentDB na Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Oprava "řazení vyvolá výjimku, pokud je hodnota pageSize menší než celkový počet položek v úložišti."

### <a name="218-october-18-2019"></a>2.1.8 (18. října 2019)
#### <a name="new-features"></a>Nové funkce
* Zastaralá rozhraní API DocumentDB.
* Přidá `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` rozhraní API a.
* Přidá optimistické zamykání na základě `_etag` .
* Povolí SpEL výraz pro název kolekce dokumentů.
* Přidá `ObjectMapper` vylepšení.

### <a name="217-october-18-2019"></a>2.1.7 (18. října 2019)
#### <a name="new-features"></a>Nové funkce
* Přidá závislost Azure Cosmos DB SDK verze 3.
* Přidá reaktivní úložiště Cosmos.
* Implementace aktualizací `DocumentDbTemplate` pro se používá Azure Cosmos DB SDK verze 3.
* Přidá další změny konfigurace pro reaktivní podporu úložiště Cosmos.

### <a name="212-march-19-2019"></a>2.1.2 (19. března 2019)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Odebere `applicationInsights` závislost pro:
    * Potenciální riziko znečišťujících závislostí.
    * Nekompatibilita Java 11.
    * Vyloučení potenciálního dopadu na výkon procesoru nebo paměti.

### <a name="207-march-20-2019"></a>2.0.7 (20. března 2019)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Backport odebere `applicationInsights` závislost pro:
    * Potenciální riziko znečišťujících závislostí.
    * Nekompatibilita Java 11.
    * Vyloučení potenciálního dopadu na výkon procesoru nebo paměti.

### <a name="211-march-7-2019"></a>2.1.1 (7. března 2019)
#### <a name="new-features"></a>Nové funkce
* Aktualizuje hlavní verzi na 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7. března 2019)
#### <a name="new-features"></a>Nové funkce
* Ignorovat všechny výjimky z telemetrie.

### <a name="210-december-17-2018"></a>2.1.0 (17. prosince 2018)
#### <a name="new-features"></a>Nové funkce
* Aktualizuje verzi na 2.1.0 k vyřešení problému.

### <a name="205-september-13-2018"></a>2.0.5 (13. září 2018)
#### <a name="new-features"></a>Nové funkce
* Přidá klíčová slova `exists` a `startsWith` .
* Aktualizuje soubor Readme.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravy "nelze volat přímý odkaz href přímo pro entitu".
* Oprava "Pokud není vytvořena kolekce," findAll selže. "

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (Předběžná verze) (23. srpna 2018)
#### <a name="new-features"></a>Nové funkce
* Přejmenuje balíček z DocumentDB na cosmosdb.
* Přidá novou funkci klíčového slova metody dotazu. 16 klíčových slov z rozhraní SQL API se teď podporuje.
* Přidá novou funkci dotazu se stránkováním a řazením.
* Zjednodušuje konfiguraci jaře-data-cosmosdb.
* Přidá `deleteCollection` `deleteAll` rozhraní API a.

#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Oprava chyby a zmírnění závad.

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Přečtěte si další informace o [architektuře pružiny](https://spring.io/projects/spring-framework).

Přečtěte si další informace o [pružinovém spuštění](https://spring.io/projects/spring-boot).

Přečtěte si další informace o [jarních datech](https://spring.io/projects/spring-data).