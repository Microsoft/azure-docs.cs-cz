---
title: Azure Cosmos DB poznámky a zdroje informací k verzi Java SDK v4 pro SQL API
description: Přečtěte si všechno o Azure Cosmos DB Java SDK v4 pro SQL API a SDK včetně data vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f88004d219989b06d4bc7e75e76aca1ce1e894fe
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536396"
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
> * [Jarní data v2](sql-api-sdk-java-spring-v2.md)
> * [Jarní data V3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
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

## <a name="helpful-content"></a>Užitečný obsah

| Content | Odkaz |
|---|---|
|**Stažení sady SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Dokumentace k rozhraní API** | [Referenční dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Přispívání do sady SDK** | [Azure SDK pro centrální úložiště Java na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Začínáme** | [Rychlý Start: Vytvoření aplikace Java pro správu Azure Cosmos DB dat rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [Úložiště GitHub s kódem pro rychlý Start](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Ukázky základních kódů** | [Azure Cosmos DB: příklady v Javě pro rozhraní SQL API](sql-api-java-sdk-samples.md) <br> [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolová aplikace se změněným kanálem**| [Ukázka změny kanálu – Java SDK v4](create-sql-api-java-changefeed.md) <br> [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Ukázka webové aplikace**| [Vytvoření webové aplikace pomocí sady Java SDK v4](sql-api-java-application.md) <br> [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tipy pro zvýšení výkonu**| [Tipy ke zvýšení výkonu pro Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Řešení potíží** | [Řešení potíží se sadou Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrace na V4 ze starší sady SDK** | [Migrace na Java V4 SDK](migrate-java-v4-sdk.md) |
| **Minimální podporovaná doba běhu**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB semináře a cvičení** |[Domovská stránka inCosmos DBch Workshops](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Historie verzí

### <a name="440-beta2-unreleased"></a>4.4.0-beta. 2 (neuvolní se)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Pevný RequestTimeoutException při povolení tcnative.

### <a name="440-beta1-2020-08-27"></a>4.4.0-beta. 1 (2020-08-27)
#### <a name="new-features"></a>Nové funkce
* Bylo přidáno nové rozhraní API pro efektivní načtení mnoha dokumentů (prostřednictvím seznamu párů PK/ID nebo všech dokumentů pro sadu hodnot PK).
* Bylo přidáno nové `deleteItem` rozhraní API.
* Ve výchozím nastavení se povolují metriky dotazů.
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Pevný NPE v `GatewayAddressCache` .
* Opravuje se problém metriky dotazu pro odpověď na nulovou položku.
* Vylepšený výkon (snížení využití procesoru) pro analýzu adres a ověřování hlavního klíče.

### <a name="432-beta2-2020-08-17"></a>4.3.2 – Beta. 2 (2020-08-17)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Žádné změny z předchozí verze, vydání pro problémy s kompatibilitou s datovými moduly pružiny.

### <a name="432-beta1-2020-08-14"></a>4.3.2 – Beta. 1 (2020-08-14)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravili jsme problém v RntbdServiceEndpoint, abyste se vyhnuli předčasnému uzavření nepoužívaného připojení TCP.

### <a name="431-2020-08-13"></a>4.3.1 (2020-08-13)
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravili jsme problém s `GROUP BY` dotazem, kde vrátil jenom jednu stránku.
* Pevný formát řetězce uživatelského agenta, který bude vyhovovat pravidlům centrální sady SDK.
* Rozšířené diagnostické informace, které zahrnují diagnostiku plánu dotazů.

### <a name="430-2020-07-29"></a>4.3.0 (2020-07-29)
#### <a name="new-features"></a>Nové funkce
* Verze knihovny Reactor-Core se aktualizovala na `3.3.8.RELEASE` . 
* Verze knihovny Reactor-síťoviny se aktualizovala na `0.9.10.RELEASE` . 
* Aktualizovaná verze knihovny na `4.1.51.Final` . 
* Byla přidána nová rozhraní API přetížení pro `upsertItem` pomocí `partitionKey` . 
* Byla přidána podpora otevřeného trasování telemetrie. 
#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Opravili jsme problém, kdy se SSLException vyvolal v případě zrušení požadavků v režimu brány.
* Pevná zásada opakování omezení prostředků při spuštění uložených procedur.
* Opravili jsme problém, kdy sada SDK zamrzne do režimu ladění na úrovni protokolu. 
* Pevné pravidelné špičky v přímém režimu v latenci. 
* Byl opraven problém s vysokým časem inicializace klienta. 
* Opravená chyba proxy serveru HTTP při přizpůsobování klienta s přímým režimem a režimem brány 
* Opravený potenciální NPE v uživatelích předává možnosti null. 
* Do diagnostického řetězce se přidalo timeUnit `requestLatency` .
* Z diagnostického řetězce byl odebrán duplicitní řetězec identifikátoru URI. 
* Pevný řetězec diagnostiky ve správném formátu JSON pro operace Point.
* Opravili jsme problém s `.single()` operátorem, který způsobí, že se v případě výjimky v případě nenalezeného řetězu Vyvolá příkaz. 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
#### <a name="new-features"></a>Nové funkce
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
* `CosmosClientException` rozšiřuje `AzureException` . 
* Rozhraní API ze systému se odebrala `maxItemCount`  &  `requestContinuationToken` `FeedOptions` místo toho `byPage()` `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Zavedená `CosmosPermissionProperties` na veřejné ploše pro `Permission` rozhraní API.
* Odebraný `SqlParameterList` typ & nahrazen `List`
* V přímém klientovi TCP byla vyřešena nevracení více paměti. 
* Byla přidána podpora pro `DISTINCT` dotazy. 
* Odstraněné externí závislosti `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Přesunuto `CosmosPagedFlux`  &  `CosmosPagedIterable` do `utils` balíčku. 
* V 4.1.45 se aktualizovala síťovina. konečné & reaktoru projektu na verzi 3.3.3.
* Veřejné smlouvy Rest se aktualizovaly na `Final` třídy.
* Přidání podpory pro pokročilou diagnostiku pro operace Point.
* Aktualizovaný balíček na `com.azure.cosmos`
* Přidal se `models` balíček pro kontrakty modelu/REST.
* Byl přidán `utils` balíček pro `CosmosPagedFlux`  &  `CosmosPagedIterable` typy. 
* Byla aktualizována veřejná rozhraní API pro použití `Duration` v rámci sady SDK.
* Do balíčku se přidaly všechny smlouvy REST `models` .
* `RetryOptions` přejmenování na `ThrottlingRetryOptions` .
* Přidání `CosmosPagedFlux`  &  `CosmosPagedIterable` typů stránkování pro rozhraní API pro dotazy. 
* Přidání podpory pro sdílení TransportClient napříč několika instancemi CosmosClients pomocí nového rozhraní API v `CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Optimalizace dotazu odebráním dvojité serializace/deserializace. 
* Optimalizace hlaviček odpovědí odebráním zbytečného kopírování znovu a zpátky. 
* Optimalizované `ByteBuffer` serializace/deserializace odebráním zprostředkujících instancí řetězců.

#### <a name="key-bug-fixes"></a>Opravy chyb klíčů
* Pevná `toString()` výjimka ConnectionPolicy ukazatele na hodnotu null
* Opravili jsme problém s analýzou výsledků dotazu v případě dotazů na hodnotu ORDER by. 
* Pevné problémy s nevracením paměti s přímým klientem TCP.
* Opraveno `orderByQuery` s chybou tokenu pokračování.
* `ChangeFeedProcessor` Oprava chyby pro zpracování děleného oddílu &, když se oddíl nenašel
* `ChangeFeedProcessor` Oprava chyby při synchronizaci aktualizací zapůjčení napříč různými vlákny.
* Pevná podmínka časování způsobila `ArrayIndexOutOfBound` výjimku v StoreReader

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Další kroky
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .