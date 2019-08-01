---
title: 'Azure Cosmos DB: SQL .NET Standard API, prostředky & SDK'
description: Seznamte se se všemi informacemi o rozhraních SQL API a .NET SDK, včetně dat vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB .NET SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663809"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET Standard SDK pro SQL API: Poznámky ke stažení a verzi
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Ukázky**|[Ukázky kódu .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Začínáme**|[Začínáme s Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Kurz vývoje webové aplikace**|[Vývoj webových aplikací pomocí služby Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuální podporované architektury**|[Microsoft .NET Standard 2,0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Poznámky k verzi
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>Přidáno
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) Přidání úrovně konzistence na klienta a možnosti dotazu
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) Přidání podpory tokenu pokračování pro LINQ
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) Přidání možností triggeru do možností žádosti o položku
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) Přidání výchozího serializátoru JSON.net s volitelným nastavením
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) Přidání ověření klíče oddílu na CreateContainerIfNotExistsAsync
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) Přidání rozhraní API LINQ to QueryDefinition
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) Přidání CreateIfNotExistsAsync do Tvůrce kontejnerů
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) Do ResponseMessage se přidala vlastnost tokenu pokračování.
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) Přidání metody rozšíření LINQ ToStreamIterator

#### <a name="fixed"></a>Pevné
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) Opravená zpráva typu MIS v CosmosException. ToString ();
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) Oprava kolizí LocationCache ConcurrentDict Lock
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) GetItemLinqQueryable nyní funguje s nulovým dotazem
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) Dotaz správně zpracovává různé jazykové jazykové verze
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) Pevná prázdná chybová zpráva, pokud se analýza dotazu nezdařila z neočekávané výjimky
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) Dotaz správně serializace vstup do datového proudu.

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Obecná dostupnost [verze 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) sady .NET SDK
* Cíle .NET Standard 2,0, které podporují rozhraní .NET Framework 4.6.1 + a .NET Core 2.0 +
* Nový objektový model s CosmosClient a metodami na nejvyšší úrovni mezi relevantními třídami databáze a kontejnerů
* Nová rozhraní API pro vysoce výkonné streamování
* Integrovaná podpora rozhraní API pro změnu kanálu procesoru
* Rozhraní API služby Fluent Builder pro CosmosClient, kontejner a Change feed Processor
* Rozhraní API pro správu propustnosti idiomatickou
* Podrobné RequestOptions a ResponseTypes pro požadavky na databázi, kontejner, položku, dotazy a propustnost
* Možnost škálování kontejnerů, které nejsou rozdělené na oddíly 
* Rozšiřitelný a přizpůsobitelný serializátor
* Rozšiřitelný kanál žádostí s podporou pro vlastní obslužné rutiny


## <a name="release--retirement-dates"></a>Data vyřazení & vydání
Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože se tak doporučuje kdykoli nejdříve upgradovat na nejnovější verzi sady SDK. 

Všechny požadavky na Azure Cosmos DB s využitím vyřazené sady SDK jsou službou odmítnuty.

<br/>

| Version | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [3.1.0](#3.1.0) |29. července 2019 |--- |
| [3.0.0](#3.0.0) |15. července 2019 |--- |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

