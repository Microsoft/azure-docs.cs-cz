---
title: Azure Cosmos DB poznámky a zdroje informací k verzi Java SDK v4 pro SQL API
description: Přečtěte si všechno o Azure Cosmos DB Java SDK v4 pro SQL API a SDK včetně data vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6cd4d64361e1975a6752ff3a15600923ce3fdf98
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322886"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB rozhraní Java SDK v4 pro Core (SQL) API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Rozhraní .NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core (SQL) kombinuje asynchronní rozhraní API a synchronizační rozhraní API do jednoho artefaktu Maven. Sada v4 SDK přináší Vylepšený výkon, nové funkce rozhraní API a asynchronní podporu založenou na reaktoru projektů a v [knihovně sítí](https://netty.io/). Uživatelé můžou očekávat vyšší výkon pomocí Azure Cosmos DB Java SDK v4 a [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) a [Azure Cosmos DB synchronizaci Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Tyto poznámky k verzi platí jenom pro Azure Cosmos DB Java SDK v4. Pokud aktuálně používáte starší verzi než v4, přečtěte si článek průvodce [migrací do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , který vám pomůže s upgradem na V4.
>
> Tady jsou tři kroky pro rychlé zprovoznění.
> 1. Nainstalujte [Minimální podporovaný běhový modul Java, JDK 8,](/java/azure/jdk/?view=azure-java-stable) abyste mohli používat sadu SDK.
> 2. Projděte si [příručku pro rychlý Start pro Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) , která vám umožní přístup k artefaktu Maven a provede základní žádosti o Azure Cosmos DB.
> 3. Pokud chcete optimalizovat sadu SDK pro vaši aplikaci, přečtěte si [tipy k výkonu](performance-tips-java-sdk-v4-sql.md) Azure Cosmos DB Java SDK v4 a průvodci [odstraňováním potíží](troubleshoot-java-sdk-v4-sql.md) .
>
> [Azure Cosmos DB semináře a cvičení](https://aka.ms/cosmosworkshop) jsou dalším skvělým prostředkem, který se naučí používat Azure Cosmos DB Java SDK v4!
>

| |  |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Dokumentace k rozhraní API** | [Referenční dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Přispívání do sady SDK** | [Azure SDK pro centrální úložiště Java na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Začínáme** | [Rychlý Start: Vytvoření aplikace Java pro správu Azure Cosmos DB dat rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Úložiště GitHub s kódem pro rychlý Start](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Ukázky základních kódů** | [Azure Cosmos DB: Příklady jazyka Java pro rozhraní SQL API](sql-api-java-sdk-samples.md) · [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolová aplikace se změněným kanálem**| [Změna kanálu – ukázka pro Java SDK v4](create-sql-api-java-changefeed.md) · [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Ukázka webové aplikace**| [Vytvoření webové aplikace pomocí Java SDK v4](sql-api-java-application.md) · [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tipy ke zvýšení výkonu**| [Tipy ke zvýšení výkonu pro Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Řešení potíží** | [Řešení potíží se sadou Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrace na V4 ze starší sady SDK** | [Migrace na Java V4 SDK](migrate-java-v4-sdk.md) |
| **Minimální podporovaná doba běhu**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB semináře a cvičení** |[Domovská stránka inCosmos DBch Workshops](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Historie verzí

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
* Bylo přidáno rozhraní API s povoleným protokolováním skriptů do `CosmosStoredProcedureRequestOptions` .
* Aktualizace `DirectConnectionConfig` Default `idleEndpointTimeout` na 1H a Default `connectTimeout` na 5.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravili jsme problém, kdy `GatewayConnectionConfig` `idleConnectionTimeout` došlo k přepsání `DirectConnectionConfig` `idleConnectionTimeout` .
* Pevná `responseContinuationTokenLimitInKb` rozhraní API pro načtení a nastavení v `CosmosQueryRequestOptions` .
* Opravili jsme problém v dotazech a změnili kanál při opětovném vytváření kolekce se stejným názvem.
* Vyřešil se problém s ClassCastExceptionem horního vyvolání dotazu.
* Vyřešil se problém s vyvoláním NullPointerException dotazu ORDER by.
* Opravili jsme problémy při zpracování zrušených žádostí v přímém režimu, který způsobuje volání třídy Actor `onErrorDropped` . 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Nové funkce
* Přidání podpory pro `GROUP BY` dotaz
* Zvýšila se výchozí hodnota maxConnectionsPerEndpoint na 130 v DirectConnectionConfig.
* Zvýšila se výchozí hodnota maxRequestsPerConnection na 30 v DirectConnectionConfig.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravili jsme problémy v pořadí podle dotazů, které vrací duplicitní výsledky při obnovení pomocí tokenu pro pokračování. 
* Opravené problémy s dotazem na hodnotu vrací hodnoty null pro vnořený objekt.
* Pevná výjimka nenulového ukazatele u správce požadavků v RntbdClientChannelPool.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Nové funkce
* Přejmenování `QueryRequestOptions` na `CosmosQueryRequestOptions` .
* Aktualizováno `ChangeFeedProcessorBuilder` na vzor tvůrce.
* Aktualizováno `CosmosPermissionProperties` pomocí nového názvu kontejneru a rozhraní API podřízených prostředků.
* Do se přidaly další ukázky & obohacených dokumentů `CosmosClientBuilder` . 
* Aktualizovala `CosmosDatabase`  &  `CosmosContainer` se rozhraní API s throughputProperties pro podporu automatického škálování/autopilotu. 
* Přejmenování `CosmosClientException` na `CosmosException` . 
* Nahrazeno `AccessCondition`  &  `AccessConditionType` `ifMatchETag()`  &  `ifNoneMatchETag()` rozhraními API. 
* Sloučí všechny `Cosmos*AsyncResponse`  &  `CosmosResponse` typy do jednoho `CosmosResponse` typu.
* Přejmenování `CosmosResponseDiagnostics` na `CosmosDiagnostics` .  
* Zabaleno `FeedResponseDiagnostics` v `CosmosDiagnostics` . 
* Odebrala se `jackson` závislost z Azure – cosmos & spoléhá na Azure-Core. 
* Nahrazeno `CosmosKeyCredential` `AzureKeyCredential` typem. 
* `ProxyOptions`Do se přidala rozhraní API `GatewayConnectionConfig` . 
* Sada SDK se aktualizovala na použití `Instant` typu místo `OffsetDateTime` . 
* Byl přidán nový typ výčtu `OperationKind` . 
* Přejmenování `FeedOptions` na `QueryRequestOptions` . 
* `getETag()`  &  `getTimestamp()` Do typů se přidala rozhraní API `Cosmos*Properties` . 
* Přidané `userAgent` informace v `CosmosException`  &  `CosmosDiagnostics` . 
* Byl aktualizován znak nového řádku v `Diagnostics` systému na znak nového řádku systému. 
* Odebraná `readAll*` rozhraní API, použijte místo toho možnost dotaz vybrat všechna rozhraní API.
* Bylo přidáno `ChangeFeedProcessor` rozhraní API pro prodlevu odhadu.   
* Přidání podpory automatického škálování/pilotního zřizování propustnosti do sady SDK.  
* Nahrazené `ConnectionPolicy` novými konfiguracemi připojení. Vystavená `DirectConnectionConfig`  &  `GatewayConnectionConfig` rozhraní API `CosmosClientBuilder` pro přímé konfigurace připojení režimu & brány
* Přesunuté `JsonSerializable`  &  `Resource` do implementačního balíčku 
* `contentResponseOnWriteEnabled`Do CosmosClientBuilder bylo přidáno rozhraní API, které zakazuje úplný obsah odpovědi při operacích zápisu.
* Vystavená `getETag()` rozhraní API na typech odpovědí.
* Přesunuté `CosmosAuthorizationTokenResolver` do implementace. 
* Přejmenováno `preferredLocations`  &  `multipleWriteLocations` rozhraní API na `preferredRegions`  &  `multipleWriteRegions` . 
* Aktualizováno `reactor-core` na 3.3.5. Release, `reactor-netty` na 0.9.7. Release & `netty` do 4.1.49. finální verze. 
* Přidání podpory pro `analyticalStoreTimeToLive` sadu SDK.     
* `CosmosClientException`rozšiřuje `AzureException` . 
* Rozhraní API ze systému se odebrala `maxItemCount`  &  `requestContinuationToken` `FeedOptions` místo toho `byPage()` `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Zavedená `CosmosPermissionProperties` na veřejné ploše pro `Permission` rozhraní API.
* Odebraný `SqlParameterList` typ & nahrazen`List`
* V přímém klientovi TCP byla vyřešena nevracení více paměti. 
* Byla přidána podpora pro `DISTINCT` dotazy. 
* Odstraněné externí závislosti `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Přesunuto `CosmosPagedFlux`  &  `CosmosPagedIterable` do `utils` balíčku. 
* V 4.1.45 se aktualizovala síťovina. konečné & reaktoru projektu na verzi 3.3.3.
* Veřejné smlouvy Rest se aktualizovaly na `Final` třídy.
* Přidání podpory pro pokročilou diagnostiku pro operace Point.
* Aktualizovaný balíček na`com.azure.cosmos`
* Přidal se `models` balíček pro kontrakty modelu/REST.
* Byl přidán `utils` balíček pro `CosmosPagedFlux`  &  `CosmosPagedIterable` typy. 
* Byla aktualizována veřejná rozhraní API pro použití `Duration` v rámci sady SDK.
* Do balíčku se přidaly všechny smlouvy REST `models` .
* `RetryOptions`přejmenování na `ThrottlingRetryOptions` .
* Přidání `CosmosPagedFlux`  &  `CosmosPagedIterable` typů stránkování pro rozhraní API pro dotazy. 
* Přidání podpory pro sdílení TransportClient napříč několika instancemi CosmosClients pomocí nového rozhraní API v`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Optimalizace dotazu odebráním dvojité serializace/deserializace. 
* Optimalizace hlaviček odpovědí odebráním zbytečného kopírování znovu a zpátky. 
* Optimalizované `ByteBuffer` serializace/deserializace odebráním zprostředkujících instancí řetězců.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Pevná `toString()` výjimka ConnectionPolicy ukazatele na hodnotu null
* Opravili jsme problém s analýzou výsledků dotazu v případě dotazů na hodnotu ORDER by. 
* Pevné problémy s nevracením paměti s přímým klientem TCP.
* Opraveno `orderByQuery` s chybou tokenu pokračování.
* `ChangeFeedProcessor`Oprava chyby pro zpracování děleného oddílu &, když se oddíl nenašel
* `ChangeFeedProcessor`Oprava chyby při synchronizaci aktualizací zapůjčení napříč různými vlákny.
* Pevná podmínka časování způsobila `ArrayIndexOutOfBound` výjimku v StoreReader

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také:
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .