---
title: 'Azure Cosmos DB: SQL .NET Core API, sadu SDK a prostředky | Microsoft Docs'
description: Další informace o SQL .NET Core API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi Cosmos DB .NET SDK služby Azure jádra.
services: cosmos-db
author: rnagpal
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c62869d1ec0dca1859972e817fe1e5e2c1f4c1eb
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797858"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK pro rozhraní API pro SQL: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál změnu rozhraní .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - rozhraní .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Stažení sady SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Dokumentaci k rozhraní API**</td><td>[Referenční dokumentace rozhraní API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Ukázky**</td><td>[Ukázky kódu rozhraní .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s Azure Cosmos DB .NET Core SDK](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Kurz vývoje webové aplikace**</td><td>[Vývoj webových aplikací s Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Aktuální podporovaných prostředí**</td><td>[Rozhraní .NET 1.6 standardní a .NET standardní 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

Cosmos DB .NET SDK služby Azure základní má parity funkcí s nejnovější verzi [.NET SDK služby Azure Cosmos DB](sql-api-sdk-dotnet.md).

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Přidané vlastnosti ConsistencyLevel k FeedOptions.
* Přidání JsonSerializerSettings RequestOptions a FeedOptions.
* Přidání EnableReadRequestsFallback k ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Mezi oddílu pořadí opraven KeyNotFoundException pro dotazy v nestandardních případech.
* Opravené chyby, kde nebyl se dodržení JsonPropery atribut v klauzuli select pro dotazy LINQ.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Opravené chybu, která je přístupů za určitých podmínek soupeření, jejímž výsledkem přerušované "Microsoft.Azure.Documents.NotFoundException: čtení relace není k dispozici pro vstupní relace token" chyby při použití úrovně konzistence relace.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Pevné regrese kde FeedOptions.MaxItemCount = -1 došlo System.ArithmeticException: velikost stránky je záporná.
* Přidat novou funkci ToString() do QueryMetrics.
* Zveřejněné statistiky oddílu na čtení kolekce.
* Přidané vlastnosti PartitionKey k ChangeFeedOptions.
* Menšími opravami chyb.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Přidá možnost zadat jedinečný indexů pro dokumenty pomocí vlastnosti UniqueKeyPolicy na DocumentCollection.
 * Opravit chyby, ve kterém nebyly se dodržení vlastní nastavení serializátor JsonSerializer pro některé dotazy a spuštění uložené procedury.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Změna z Azure DocumentDB k databázi Azure Cosmos v referenční dokumentace rozhraní API Branding dokumentaci, informace o metadatech v sestavení a balíček NuGet. 
 * Vystavení diagnostické informace a latence z odpovědi požadavky odeslané s režimem přímé připojení. Názvy vlastností, které jsou RequestDiagnosticsString a RequestLatency u ResourceResponse třídy.
 * Tato verze sady SDK vyžaduje nejnovější verze emulátoru DB Cosmos Azure k dispozici ke stažení https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Přidat několik oprav spolehlivost a vylepšení.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Interní změny související podpora [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Přidaná podpora pro PartitionKeyRangeId jako FeedOption pro vymezení výsledky dotazu a hodnotu klíče rozsahu konkrétního oddílu. 
* Přidaná podpora pro StartTime jako ChangeFeedOption zahájíte hledá změny po uplynutí této doby. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Byl opraven problém ve třídě JsonSerializable, která může způsobit výjimce přetečení zásobníku.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Přidaná podpora pro zadání vlastní JsonSerializerSettings při vytváření instancí [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) instance.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Podpora rozhraní .NET standardní 1.5 jako jeden z cílové architektury.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Byl opraven problém ovlivňující x64 počítače, které nemají podporu SSE4 instrukce a throw SEHException – při spuštění dotazů Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Přidaná podpora pro novou úroveň konzistence volá ConsistentPrefix.
*   Přidaná podpora pro dotaz metriky pro jednotlivé oddíly.
*   Přidaná podpora pro omezení velikosti token pokračování pro dotazy.
*   Přidaná podpora pro podrobnější trasování pro chybné žádosti.
*   Provedli jsme některé vylepšení výkonu v sadě SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Opravit problém, který ignoruje PartitionKey hodnota zadaná v FeedOptions pro agregační dotazy.
* Byl opraven problém v transparentní zpracování oddílu správy během letu střední cross-partition Order By dotazu provádění.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Byl opraven problém, který chybu způsobil zablokování v některých asynchronní rozhraní API, pokud se používá v kontextu ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Opravy aby SDK pružnější automatické převzetí služeb při selhání za určitých podmínek.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Opravte pro problém způsobující příležitostně o výjimku WebException: vzdálený název nelze rozpoznat.
* Přidaná podpora pro přímo čtení typu dokumentu přidáním nové přetížení ReadDocumentAsync rozhraní API.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Byla přidána podpora LINQ pro dotazy agregace (COUNT, MIN, MAX, součet a průměr).
* Opravte pro problém nevracení paměti pro objekt ConnectionPolicy způsobené použití obslužné rutiny události.
* Oprava problému, ve kterém nebyl UpsertAttachmentAsync pracovat, když byla použita značka ETag.
* Oprava problému, ve kterém nebyl křížové oddílu klauzule order by dotazu pokračování práce při řazení na pole řetězce.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Přidaná podpora pro dotazy agregace (COUNT, MIN, MAX, součet a průměr). V tématu [podporu agregace](sql-api-sql-query.md#Aggregates).
* Snížena minimální propustnosti na dělené kolekce z 10,100 RU/s na 2 500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK umožňuje vytvářet rychlé a napříč platformami [ASP.NET Core](https://www.asp.net/core) a [.NET Core](https://www.microsoft.com/net/core#windows) aplikace běžely na Windows, Mac a Linux. Nejnovější verzi Azure Cosmos DB .NET Core SDK je plně [Xamarin](https://www.xamarin.com) kompatibilní a umožňuje vytvářet aplikace, které cílí na iOS, Android a Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

Azure Cosmos DB .NET Core Preview SDK umožňuje vytvářet rychlé a napříč platformami [ASP.NET Core](https://www.asp.net/core) a [.NET Core](https://www.microsoft.com/net/core#windows) aplikace běžely na Windows, Mac a Linux.

Cosmos DB .NET SDK služby Azure základní Preview má parity funkcí s nejnovější verzi [.NET SDK služby Azure Cosmos DB](sql-api-sdk-dotnet.md) a podporuje následující:
* Všechny [připojení režimy](performance-tips.md#networking): režim brány, přímé TCP a přímé HTTPs. 
* Všechny [úrovně konzistence](consistency-levels.md): silným, relace, typu s ohraničenou Prošlostí a Eventual.
* [Oddíly kolekce](partition-data.md). 
* [Účty databáze více oblasti a geografická replikace](distribute-data-globally.md).

Pokud máte otázky související s touto sadou SDK, odeslání na [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb), nebo problém v souboru [úložiště github](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Verze & vyřazení kalendářních dat

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.9.1](#1.9.1) |09 března 2018 |--- |
| [1.8.2](#1.8.2) |21. února 2018 |--- |
| [1.8.1](#1.8.1) |05 února 2018 |--- |
| [1.7.1](#1.7.1) |16. listopadu 2017 |--- |
| [1.7.0](#1.7.0) |10 listopadu 2017 |--- |
| [1.6.0](#1.6.0) |17 říjen 2017 |--- |
| [1.5.1](#1.5.1) |02 říjen 2017 |--- |
| [1.5.0](#1.5.0) |10. srpnu 2017 |--- | 
| [1.4.1](#1.4.1) |07 srpen 2017 |--- |
| [1.4.0](#1.4.0) |02 srpen 2017 |--- |
| [1.3.2](#1.3.2) |12. června 2017 |--- |
| [1.3.1](#1.3.1) |23 může 2017 |--- |
| [1.3.0](#1.3.0) |10. května 2017 |--- |
| [1.2.2](#1.2.2) |19. dubna 2017 |--- |
| [1.2.1](#1.2.1) |29. března 2017 |--- |
| [1.2.0](#1.2.0) |25. března 2017 |--- |
| [1.1.2](#1.1.2) |20. března 2017 |--- |
| [1.1.1](#1.1.1) |14. března 2017 |--- |
| [1.1.0](#1.1.0) |16 února 2017 |--- |
| [1.0.0](#1.0.0) |21 prosince 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15. listopadu 2016 |31. prosinci 2016 |

## <a name="see-also"></a>Viz také
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

