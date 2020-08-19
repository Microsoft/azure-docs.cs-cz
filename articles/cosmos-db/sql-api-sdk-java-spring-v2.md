---
title: Jarní data Azure Cosmos DB v2 pro poznámky k verzi a prostředky SQL API
description: Přečtěte si všechno o jarních datech Azure Cosmos DB v2 pro SQL API včetně data vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590645"
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

Pružinová data Azure Cosmos DB v2 pro Core (SQL) umožňují vývojářům využít Azure Cosmos DB v aplikacích pružiny. Jarní data Azure Cosmos DB zpřístupňuje rozhraní jarních dat pro práci s databázemi a kolekcemi, práci s dokumenty a vydávání dotazů. Synchronní i asynchronní (opětovně aktivní) rozhraní API se podporují ve stejném artefaktu Maven. 

[Jarní architektura](https://spring.io/projects/spring-framework) je model programování a konfigurace, který zjednodušuje vývoj aplikací Java. Aby se na webu organizace mohla zacitovat, pružina zjednodušuje "domovníing" aplikací pomocí injektáže závislostí. Mnoho vývojářů, jako je pružina, protože sestavování a testování aplikací se bude mnohem jednodušší. [Pružinové spouštění](https://spring.io/projects/spring-boot) rozšiřuje tuto představu o tom, jak se tato instalace prokládá s ohledem na vývoj webových aplikací a mikroslužeb. [Pružinová data](https://spring.io/projects/spring-data) jsou programovací model pro přístup k úložišti dat, jako je například Azure Cosmos DB z kontextu aplikace pružinové nebo jarního spuštění. 

Ve [jarních cloudových aplikacích Azure](https://azure.microsoft.com/services/spring-cloud/) můžete použít jarní data Azure Cosmos DB.

> [!IMPORTANT]  
> Tyto poznámky k verzi jsou k disAzure Cosmos DB pro 2 – jarní data. [Zde](sql-api-sdk-java-spring-v3.md)najdete poznámky k verzi v3. 
>
> Jarní data Azure Cosmos DB podporují jenom rozhraní SQL API.
>
> Následující příručky podporují jarní data u jiných Azure Cosmos DB rozhraní API:
> * [Jarní data pro Apache Cassandra s Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Jarní MongoDBy dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Jarní Gremliny dat pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Chcete se rychle dostat do budoucna?
> 1. Nainstalujte [Minimální podporovaný běhový modul Java, JDK 8,](/java/azure/jdk/?view=azure-java-stable) abyste mohli používat sadu SDK.
> 2. Vytvoření aplikace Azure Cosmos DBovou datovou pružinou pomocí Starter – [je to snadné](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. Pracujte prostřednictvím [Azure Cosmos DB příručka pro vývojáře](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) , která vás provede základními požadavky Azure Cosmos DB.
>
> Pomocí [jarních Initializr](https://start.spring.io/)můžete rychle aktivovat aplikace na jaře Boot Starter.
>

## <a name="helpful-content"></a>Užitečný obsah

| Obsah | Odkaz |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Dokumentace k rozhraní API** | [Data pružiny Azure Cosmos DB Referenční dokumentace]() |
|**Přispívání do sady SDK** | [Jarní data Azure Cosmos DB úložiště na GitHubu](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Jaře Boot Starter**| [Azure Cosmos DB klientskou knihovnu Starter Boot Boot Library pro Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Ukázka aplikace pružiny TODO pomocí Azure Cosmos DB**| [Komplexní prostředí Java v systému App Service Linux (část 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Příručka pro vývojáře** | [Příručka pro vývojáře na jaře data Azure Cosmos DB Developers](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Příručka k používání Starter** | [Používání úvodní sady Spring Boot s využitím rozhraní SQL API služby Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Úložiště GitHub pro Starter Cosmos DB ve jarním startu Azure](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Ukázka pomocí App Services** | [Jak používat Spring a Cosmos DB s App Service v Linuxu](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Ukázka aplikace TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Historie verzí

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Nové funkce
* Aktualizovat verzi na jaře pro spuštění na 2.3.0 
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Nové funkce
* Aktualizace verze Azure Cosmos DB na verzi v 3.7.3
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Obsahuje opravy nevracení paměti a upgrady verzí v Cosmos SDK v 3.7.3. 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Pevný příznak allowTelemetry, který se má vzít v úvahu z CosmosDbConfig
* Pevná vlastnost TTL na kontejneru

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Nové funkce
* Přidání nového findAll pomocí rozhraní API pro klíč oddílu
* Verze Azure-Cosmos se aktualizovala na 3.7.0.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Pevný CollectionName-> ContainerName
* Pevné entityClass & doménová třída-> domainType
* Opravená "návratová kolekce entit uložená podle úložiště namísto vstupní entity"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Oprava nevyřešené pro "návratovou kolekci entit uloženou úložištěm namísto vstupní entity"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Nové funkce
* Aktualizace verze Azure-Cosmos na verzi v 3.6.0
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Nové funkce
* Aktualizace verze Cosmos DB SDK na 3.5.0
* Přidání pole poznámky pro povolení nebo zakázání kolekce auto Create
* Lepší zpracování výjimek, vystavené CosmosClientException prostřednictvím CosmosDBAccessException
* Vystavené requestCharge a activityId prostřednictvím ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* 3.5.0 opravy aktualizací sady SDK "výjimka, když Cosmos DB hlavička odpovědi HTTP je větší než 8192 bajtů", "ConsistencyPolicy. defaultConsistencyLevel () se nezdařila v ohraničené neaktuálnosti a předponě konzistence"
* Opravené chování rozhraní API findById, vrácení prázdné hodnoty v nenalezené situaci namísto vyvolání výjimky
* Opravili jsme chybu, při které se při použití CosmosPageRequest nepoužilo řazení na další stránce.

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Nové funkce
* Přidání pole poznámky pro povolení nebo zakázání kolekce auto Create
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravené chování rozhraní API findById, vrácení prázdné hodnoty v nenalezené situaci namísto vyvolání výjimky

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Nové funkce
* Dokončit reaktivní podporu úložiště Cosmos
* Podpora metriky a řetězce diagnostiky žádostí Cosmos DB
* Aktualizace verze Cosmos DB SDK na 3.3.1
* Upgrade verze architektury pružiny na 5.2.0. RELEASE
* Verze navýšení dat na jaře se upgraduje na 2.2.0. RELEASE.
* Přidání findByIdAndPartitionKey, rozhraní deleteByIdAndPartitionKey API
* Odebrání závislosti z Azure – doumentdb
* Přepálená DocumentDb na Cosmos
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Pevné "řazení vyvolá výjimku, pokud je hodnota pageSize menší než celkový počet položek v úložišti"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Nové funkce
* Zastaralá rozhraní API databáze dokumentů
* Přidání findByIdAndPartitionKey, deleteByIdAndPartitionKey rozhraní API.
* Přidání optimistického blokování na základě _etag
* Povolený výraz SPeL pro název kolekce dokumentů
* ObjectMapper vylepšení.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Nové funkce
* Přidání závislosti sady Cosmos SDK V3
* Přidání reaktivního úložiště Cosmos
* Byla aktualizována implementace DocumentDbTemplate pro použití sady Cosmos SDK v3.
* Další změny konfigurace pro reaktivní úložiště Cosmos support.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Odebrat závislost applicationInsights pro
    * Potenciální riziko znečišťujících závislostí
    * Nekompatibilita Java 11
    * Vyloučení potenciálního dopadu na výkon procesoru nebo paměti.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Nové funkce
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Backport odebere závislost applicationInsights pro
    * Potenciální riziko znečišťujících závislostí
    * Nekompatibilita Java 11
    * Vyloučení potenciálního dopadu na výkon procesoru nebo paměti.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Nové funkce
* Aktualizace hlavní verze na verzi 2.1.1
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Nové funkce
* Ignorovat všechny výjimky z telemetrie
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Nové funkce
* Aktualizace verze na 2.1.0 k vyřešení problému
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Nové funkce
* Existuje klíčové slovo Add, startsWith
* Aktualizovat soubor Readme
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Oprava "nemůžu zavolat přímý odkaz přímo pro entitu"
* Oprava "findAll selže, pokud není vytvořena kolekce"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (Předběžná verze) (2018-08-23)
#### <a name="new-features"></a>Nové funkce
* Přejmenování balíčku z DocumentDB na cosmosdb,
* Přidání nové funkce klíčového slova metody dotazu, 16 klíčových slov z podporovaného rozhraní SQL API.
* Přidejte novou funkci dotazu se stránkováním a řazením.
* Zjednodušte konfiguraci jaře-data-cosmosdb.
* Přidejte deletecollection a rozhraní deleteAll API.

#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Oprava chyb a vylepšení závad.

## <a name="faq"></a>Časté otázky
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

Další informace o architektuře pružiny najdete na [domovské stránce projektu](https://spring.io/projects/spring-framework).

Další informace o pružinovém spuštění najdete na [domovské stránce projektu](https://spring.io/projects/spring-boot).

Další informace o jarních datech najdete na [domovské stránce projektu](https://spring.io/projects/spring-data).