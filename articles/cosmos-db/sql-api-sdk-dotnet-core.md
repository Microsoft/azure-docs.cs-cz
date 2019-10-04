---
title: 'Azure Cosmos DB: rozhraní SQL .NET Core API, sady SDK & prostředky'
description: Přečtěte si všechno o rozhraních API SQL .NET Core a sadě SDK včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 325e84ec4489eb2f1f530585af7d3c4bc4b201fc
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949575"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>.NET Core SDK Azure Cosmos DB pro SQL API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Asynchronní Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Stažení sady SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Ukázky**|[Ukázky kódu .NET](sql-api-dotnet-samples.md)|
|**Začínáme**|[Začínáme s rozhraním Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Kurz webové aplikace**|[Vývoj webových aplikací pomocí Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuální podporovaná architektura**|[.NET Standard 1,6 a .NET Standard 1,5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Poznámky k vydané verzi

> [!NOTE]
> Pokud používáte .NET Core, přečtěte si prosím nejnovější verzi 3. x sady [.NET SDK](sql-api-sdk-dotnet-standard.md), která cílí na .NET Standard. 

### <a name="a-name270270"></a>@no__t – 02.7.0

* Přidání podpory pro pole a objekty v pořadí podle dotazů
* Zpracování efektivních kolizí klíčů oddílu
* Přidání podpory LINQ pro více operátorů OrderBy s operátorem ThenBy
* Vlastní nastavení serializace se teď aplikují na všechny operace Upsert a nahrazování.
* Opravený problém se zablokováním AysncCache tak, aby fungoval s jedním vláknovým plánovačem úloh

### <a name="a-name260260"></a>@no__t – 02.6.0

* Přidání PortReusePolicy k ConnectionPolicy
* Opraveno Ntdll! RtlGetVersion TypeLoadException problém, když se v aplikaci pro UWP používá sada SDK

### <a name="a-name251251"></a>@no__t – 02.5.1

* Verze System .NET. http sady SDK se nyní shoduje s tím, co je definováno v balíčku NuGet.
* Povolí použití požadavků na zápis do jiné oblasti v případě, že se původní chyba nezdařila.
* Přidejte zásady opakování relace pro požadavek na zápis.

### <a name="a-name241241"></a>@no__t – 02.4.1

* Opravuje podmínku sledování časování pro dotazy, které způsobily prázdné stránky.

### <a name="a-name240240"></a>@no__t – 02.4.0

* Zvýšená velikost desetinné přesnosti pro dotazy LINQ.
* Přidání nových tříd CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType a PartitionKeyDefinitionVersion
* Přidání TimeToLivePropertyPath do souboru DocumentCollection
* Přidání CompositeIndexes a SpatialIndexes do IndexPolicy
* Přidání verze do PartitionKeyDefinition
* Přidáno None do PartitionKey

### <a name="a-name230230"></a>@no__t – 02.3.0

 * Do ConnectionPolicy přidejte IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection a MaxTcpConnectionsPerEndpoint.
 
### <a name="a-name223223"></a>@no__t – 02.2.3

* Vylepšení diagnostiky

### <a name="a-name222222"></a>@no__t – 02.2.2

* Bylo přidáno nastavení proměnné prostředí POCOSerializationOnly.

* Odebrali jsme DocumentDB. prostor. SQL. dll a teď je součástí Microsoft. Azure. Documents. ServiceInterop. dll.

### <a name="a-name221221"></a>@no__t – 02.2.1

* Vylepšení logiky opakování při převzetí služeb při selhání pro StoredProcedure volání.

* Byl vytvořen DocumentClientEventSource typu singleton. 

* Oprava GatewayAddressCache timeout nedodržuje ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a>@no__t – 02.2.0

* Pro diagnostiku přímých/TCP přenosů přidejte TransportException, typ interní výjimky sady SDK. V případě zpráv výjimek tento typ tiskne Další informace pro řešení potíží s připojením klientů.

* Bylo přidáno nové přetížení konstruktoru, které přijímá HttpMessageHandler, zásobník obslužných rutin HTTP, který se má použít pro odesílání požadavků HttpClient (např. HttpClientHandler).

* Oprava chyby, kdy hlavička s hodnotami null nebyla správně zpracována

* Vylepšené ověřování mezipaměti kolekce

### <a name="a-name213213"></a>@no__t – 02.1.3

* Systém .NET. Security byl aktualizován na 4.3.2.

### <a name="a-name212212"></a>@no__t – 02.1.2

* Vylepšení trasování diagnostiky.

### <a name="a-name211211"></a>@no__t – 02.1.1

* Přidání větší odolnosti k přechodným selháním žádostí na více oblastí.

### <a name="a-name210210"></a>@no__t – 02.1.0

* Přidání podpory pro zápis ve více oblastech.
* Zvýšení výkonu dotazů mezi oddíly s HORNÍmi a MaxBufferedItemCountmi.

### <a name="a-name200200"></a>@no__t – 02.0.0

* Přidání podpory zrušení žádosti
* Přidání SetCurrentLocation do ConnectionPolicy, které automaticky naplní upřednostňovaná umístění na základě oblasti.
* Opravila se chyba v dotazech pro různé oddíly s min/max a filtrem, který se neshoduje s žádnými dokumenty v jednotlivých oddílech.
* Metody DocumentClient nyní mají paritu s IDocumentClient.
* Byl aktualizován přímý přenosový zásobník TCP, aby se snížil počet navázaných připojení.
* Přidání podpory pro přímý režim TCP pro klienty s jiným systémem než Windows.

### <a name="a-name200-preview2200-preview2"></a>@no__t – 02.0.0 – preview2

* Přidání podpory zrušení žádosti
* Přidání SetCurrentLocation do ConnectionPolicy, které automaticky naplní upřednostňovaná umístění na základě oblasti.
* Opravila se chyba v dotazech pro různé oddíly s min/max a filtrem, který se neshoduje s žádnými dokumenty v jednotlivých oddílech.

### <a name="a-name200-preview200-preview"></a>@no__t – 02.0.0 – Preview

* Metody DocumentClient nyní mají paritu s IDocumentClient.
* Byl aktualizován přímý přenosový zásobník TCP, aby se snížil počet navázaných připojení.
* Přidání podpory pro přímý režim TCP pro klienty s jiným systémem než Windows.

### <a name="a-name11001100"></a>@no__t – 01.10.0

* Do FeedOptions se přidala vlastnost ConsistencyLevel.
* Přidání JsonSerializerSettings k RequestOptions a FeedOptions.
* Přidání EnableReadRequestsFallback k ConnectionPolicy.

### <a name="a-name191191"></a>@no__t – 01.9.1

* Pevná KeyNotFoundException pro řazení v různých oddílech podle dotazů v rohových případech.
* Byla opravena chyba, kdy atribut JsonProperty v klauzuli SELECT pro dotazy LINQ nebyl dodržen.

### <a name="a-name182182"></a>@no__t – 01.8.2

* Opravená chyba, ke které dochází za určitých podmínek časování, což má za následek občasné "Microsoft. Azure. Documents. NotFoundException: relace čtení není k dispozici pro chyby tokenu vstupní relace při použití úrovně konzistence relace.

### <a name="a-name181181"></a>@no__t – 01.8.1

* Pevná regrese, kde FeedOptions. MaxItemCount =-1 vyvolala hodnotu System. ArithmeticException –: velikost stránky je záporná.
* Do QueryMetrics se přidala nová funkce ToString ().
* Statistika vystavených oddílů při čtení kolekcí
* Do ChangeFeedOptions se přidala vlastnost PartitionKey.
* Drobné opravy chyb.

### <a name="a-name171171"></a>@no__t – 01.7.1
 
 * Přidá do třídy DocumentCollection možnost zadat jedinečné indexy pro dokumenty pomocí vlastnosti UniqueKeyPolicy.
 * Opravili jsme chybu, ve které se vlastní nastavení JsonSerializer neuplatňují pro některé dotazy a spuštění uložených procedur.

### <a name="a-name170170"></a>@no__t – 01.7.0
 
 * Změna brandingu z Azure DocumentDB na Azure Cosmos DB v referenční dokumentaci k rozhraní API, informace o metadatech v sestaveních a balíčku NuGet.
 * Vystavit diagnostické informace a latenci z odpovědi na požadavky odeslané v režimu přímého připojení. Názvy vlastností jsou RequestDiagnosticsString a RequestLatency na třídě ResourceResponse.
 * Tato verze sady SDK vyžaduje k dispozici nejnovější verzi Azure Cosmos DB emulátoru pro stažení z https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a>@no__t – 01.6.0

* Bylo přidáno několik oprav spolehlivosti a vylepšení.

### <a name="a-name151151"></a>@no__t – 01.5.1 

* Vnitřní změny související s podporou [Microsoft. Azure.](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet) Graphs

### <a name="a-name150150"></a>@no__t – 01.5.0 

* Přidání podpory pro PartitionKeyRangeId jako FeedOption pro rozsah výsledků dotazu na určitou hodnotu rozsahu klíčů oddílu
* Přidání podpory pro Čas_spuštění jako ChangeFeedOption pro zahájení hledání změn po uplynutí této doby.

### <a name="a-name141141"></a>@no__t – 01.4.1

*   Opravili jsme problém ve třídě JsonSerializable, která může způsobit výjimku přetečení zásobníku.

### <a name="a-name140140"></a>@no__t – 01.4.0

*   Přidání podpory pro zadání vlastních JsonSerializerSettings při vytváření instancí instance [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet)

### <a name="a-name132132"></a>@no__t – 01.3.2

*   Podpora .NET Standard 1,5 jako jedné z cílových rozhraní.

### <a name="a-name131131"></a>@no__t – 01.3.1

*   Opravili jsme problém, který ovlivnil počítače x64, které nepodporují instrukci SSE4 a vyvolávají SEHException – při spuštění dotazů Azure Cosmos DB.

### <a name="a-name130130"></a>@no__t – 01.3.0

*   Přidání podpory pro novou úroveň konzistence s názvem ConsistentPrefix.
*   Přidání podpory pro metriky dotazů pro jednotlivé oddíly.
*   Přidání podpory pro omezení velikosti tokenu pokračování pro dotazy.
*   Přidání podpory pro podrobnější trasování pro neúspěšné požadavky.
*   Provedli jsme některá vylepšení výkonu v sadě SDK.

### <a name="a-name122122"></a>@no__t – 01.2.2

* Opravili jsme problém, který ignoroval hodnotu PartitionKey poskytnutou v FeedOptions pro agregované dotazy.
* Opravili jsme problém transparentním zpracováním správy oddílů během zpracování dotazů mezi oddíly středních let.

### <a name="a-name121121"></a>@no__t – 01.2.1

* Opravili jsme problém, který způsobil zablokování v některých asynchronních rozhraních API při použití uvnitř ASP.NET kontextu.

### <a name="a-name120120"></a>@no__t – 01.2.0

* Opravuje, aby sada SDK byla za určitých podmínek pružnější pro automatické převzetí služeb při selhání.

### <a name="a-name112112"></a>@no__t – 01.1.2

* Oprava problému, který občas způsobí WebException: vzdálený název se nedal přeložit.
* Přidání podpory pro přímý čtení typového dokumentu přidáním nových přetížení do rozhraní Readdocumentasync přečteme API.

### <a name="a-name111111"></a>@no__t – 01.1.1

* Přidání podpory LINQ pro agregační dotazy (COUNT, MIN, MAX, SUM a AVG).
* Opravte problém nevracení paměti pro objekt ConnectionPolicy, který je způsoben použitím obslužné rutiny události.
* Oprava problému, ve kterém UpsertAttachmentAsync nefungovala při použití ETag.
* Oprava problému s řazením mezi oddíly – při pokračování dotazu nefungoval při řazení pole řetězce.

### <a name="a-name110110"></a>@no__t – 01.1.0

* Přidání podpory agregačních dotazů (COUNT, MIN, MAX, SUM a AVG). Viz [Podpora agregace](sql-query-aggregates.md).
* Byla snížena minimální propustnost u dělených kolekcí z 10 100 RU/s na 2500 RU/s.

### <a name="a-name100100"></a>@no__t – 01.0.0

Azure Cosmos DB .NET Core SDK vám umožní vytvářet rychlé aplikace [ASP.NET Core](https://www.asp.net/core) pro různé platformy a aplikace [.NET Core](https://www.microsoft.com/net/core#windows) pro spouštění v systémech Windows, Mac a Linux. Nejnovější vydaná verze Azure Cosmos DB .NET Core SDK je plně kompatibilní s [Xamarin](https://www.xamarin.com) a používá se k sestavování aplikací určených pro iOS, Android a Mono (Linux).  

### <a name="a-name010-preview010-preview"></a>@no__t – 00.1.0 – Preview

Sada Azure Cosmos DB .NET Core Preview SDK umožňuje sestavovat rychlé aplikace [ASP.NET Core](https://www.asp.net/core) pro různé platformy a aplikace [.NET Core](https://www.microsoft.com/net/core#windows) pro spouštění v systémech Windows, Mac a Linux.

Sada Azure Cosmos DB .NET Core Preview SDK má paritu funkcí s nejnovější verzí sady [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) a podporuje následující:
* Všechny [režimy připojení](performance-tips.md#networking): režim brány, přímý protokol TCP a přímý https.
* Všechny [úrovně konzistence](consistency-levels.md): silný, relace, ohraničená neaktuálnost a případné.
* [Dělené kolekce](partition-data.md).
* [Účty databáze ve více oblastech a geografická replikace](distribute-data-globally.md).

Pokud máte dotazy související s touto sadou SDK, odešlete příspěvek do [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb)nebo zadáte problém v [úložišti GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Data vyřazení & vydání
Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože se tak doporučuje kdykoli nejdříve upgradovat na nejnovější verzi sady SDK. 

Všechny požadavky na Azure Cosmos DB s využitím vyřazené sady SDK jsou službou odmítnuty.

> [!WARNING]
> Všechny verze **1. x** .NET Core SDK pro rozhraní SQL API budou vyřazení od **30. srpna 2020**.
> 
>
<br/>


| Version | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [2.7.0](#2.7.0) |23. září 2019 |--- |
| [2.6.0](#2.6.0) |30. srpna 2019 |--- |
| [2.5.1](#2.5.1) |2\. července 2019 |--- |
| [2.4.1](#2.4.1) |20. června 2019 |--- |
| [2.4.0](#2.4.0) |05. května 2019 |--- |
| [2.3.0](#2.3.0) |04. dubna 2019 |--- |
| [2.2.3](#2.2.3) |11. března 2019 |--- |
| [bodě](#2.2.2) |06 6. února 2019 |--- |
| [2.2.1](#2.2.1) |24. prosince 2018 |--- |
| [2.2.0](#2.2.0) |7\. prosince 2018 |--- |
| [2.1.3](#2.1.3) |15. října 2018 |--- |
| [2.1.2](#2.1.2) |4\. října 2018 |--- |
| [bodů](#2.1.1) |27. září 2018 |--- |
| [2.1.0](#2.1.0) |21. září 2018 |--- |
| [2.0.0](#2.0.0) |7\. září 2018 |--- |
| [1.9.1](#1.9.1) |9\. března 2018 |30. srpna 2020 |
| [1.8.2](#1.8.2) |21. února 2018 |30. srpna 2020 |
| [1.8.1](#1.8.1) |05. února 2018 |30. srpna 2020 |
| [1.7.1](#1.7.1) |16. listopadu 2017 |30. srpna 2020 |
| [1.7.0](#1.7.0) |10. listopadu 2017 |30. srpna 2020 |
| [1.6.0](#1.6.0) |17. října 2017 |30. srpna 2020 |
| [1.5.1](#1.5.1) |2\. října 2017 |30. srpna 2020 |
| [1.5.0](#1.5.0) |10. srpna 2017 |30. srpna 2020 | 
| [bodů](#1.4.1) |7\. srpna 2017 |30. srpna 2020 |
| [1.4.0](#1.4.0) |2\. srpna 2017 |30. srpna 2020 |
| [1.3.2](#1.3.2) |12. června 2017 |30. srpna 2020 |
| [1.3.1](#1.3.1) |23. května 2017 |30. srpna 2020 |
| [1.3.0](#1.3.0) |10. května 2017 |30. srpna 2020 |
| [1.2.2](#1.2.2) |19. dubna 2017 |30. srpna 2020 |
| [1.2.1](#1.2.1) |29. března 2017 |30. srpna 2020 |
| [1.2.0](#1.2.0) |25. března 2017 |30. srpna 2020 |
| [bodu](#1.1.2) |20. března 2017 |30. srpna 2020 |
| [1.1.1](#1.1.1) |14. března 2017 |30. srpna 2020 |
| [1.1.0](#1.1.0) |16. února 2017 |30. srpna 2020 |
| [1.0.0](#1.0.0) |21. prosince 2016 |30. srpna 2020 |
| [0.1.0 – Preview](#0.1.0-preview) |15. listopadu 2016 |31. prosince 2016 |

## <a name="see-also"></a>Viz také
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

