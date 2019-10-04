---
title: 'Azure Cosmos DB: rozhraní SQL .NET API, sady SDK & prostředků'
description: Přečtěte si všechno o rozhraních API a sadě SDK pro SQL .NET, včetně dat vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: f8c38c46bd60834c166721f62088d8edb2c722a9
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949578"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK pro SQL API: stažení a poznámky k verzi
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
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
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Ukázky**|[Ukázky kódu .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Začínáme**|[Začínáme s Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Kurz webové aplikace**|[Vývoj webových aplikací pomocí Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuální podporovaná architektura**|[Microsoft .NET Framework 4,5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Zpráva k vydání verze

> [!NOTE]
> Pokud používáte .NET Framework, přečtěte si nejnovější verzi 3. x sady [.NET SDK](sql-api-sdk-dotnet-standard.md), která cílí na .NET Standard. 

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

* Vylepšení trasování diagnostiky

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

### <a name="a-name12201220"></a>@no__t – 01.22.0

* Do FeedOptions se přidala vlastnost ConsistencyLevel.
* Přidání JsonSerializerSettings k RequestOptions a FeedOptions.
* Přidání EnableReadRequestsFallback k ConnectionPolicy.

### <a name="a-name12111211"></a>@no__t – 01.21.1

* Pevná KeyNotFoundException pro řazení v různých oddílech podle dotazů v rohových případech.
* Byla opravena chyba, kdy atribut JsonProperty v klauzuli SELECT pro dotazy LINQ nebyl dodržen.

### <a name="a-name12021202"></a>@no__t – 01.20.2

* Opravená chyba, ke které dochází za určitých podmínek časování, což má za následek občasné "Microsoft. Azure. Documents. NotFoundException: relace čtení není k dispozici pro chyby tokenu vstupní relace při použití úrovně konzistence relace.

### <a name="a-name12011201"></a>@no__t – 01.20.1

* Pevná regrese, kde FeedOptions. MaxItemCount =-1 vyvolala hodnotu System. ArithmeticException –: velikost stránky je záporná.
* Do QueryMetrics se přidala nová funkce ToString ().
* Statistika vystavených oddílů při čtení kolekcí
* Do ChangeFeedOptions se přidala vlastnost PartitionKey.
* Drobné opravy chyb.

### <a name="a-name11911191"></a>@no__t – 01.19.1

* Přidá do třídy DocumentCollection možnost zadat jedinečné indexy pro dokumenty pomocí vlastnosti UniqueKeyPolicy.
* Opravili jsme chybu, ve které se vlastní nastavení JsonSerializer neuplatňují pro některé dotazy a spuštění uložených procedur.

### <a name="a-name11901190"></a>@no__t – 01.19.0

* Změna brandingu z Azure DocumentDB na Azure Cosmos DB v referenční dokumentaci k rozhraní API, informace o metadatech v sestaveních a balíčku NuGet. 
* Vystavit diagnostické informace a latenci z odpovědi na požadavky odeslané v režimu přímého připojení. Názvy vlastností jsou RequestDiagnosticsString a RequestLatency na třídě ResourceResponse.
* Tato verze sady SDK vyžaduje k dispozici nejnovější verzi Azure Cosmos DB emulátoru pro stažení z https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a>@no__t – 01.18.1 

* Interní změny pro sestavení přátel Microsoftu.

### <a name="a-name11801180"></a>@no__t – 01.18.0 

* Bylo přidáno několik oprav spolehlivosti a vylepšení.

### <a name="a-name11701170"></a>@no__t – 01.17.0 

* Přidání podpory pro PartitionKeyRangeId jako FeedOption pro rozsah výsledků dotazu na určitou hodnotu rozsahu klíčů oddílu 
* Přidání podpory pro Čas_spuštění jako ChangeFeedOption pro zahájení hledání změn po uplynutí této doby.

### <a name="a-name11611161"></a>@no__t – 01.16.1
* Opravili jsme problém ve třídě JsonSerializable, která může způsobit výjimku přetečení zásobníku.

### <a name="a-name11601160"></a>@no__t – 01.16.0
*   Opravili jsme problém, který vyžadoval opětovné kompilování aplikace z důvodu zavedení JsonSerializerSettings jako volitelného parametru v konstruktoru DocumentClient.
* Byl označen konstruktor DocumentClient zastaralý, který vyžadoval JsonSerializerSettings jako poslední parametr pro povolení výchozích hodnot parametrů ConnectionPolicy a ConsistencyLevel při předávání parametru JsonSerializerSettings.

### <a name="a-name11501150"></a>@no__t – 01.15.0
*   Přidání podpory pro zadání vlastních JsonSerializerSettings při vytváření instancí [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a>@no__t – 01.14.1
*   Opravili jsme problém, který ovlivnil počítače x64, které nepodporují instrukci SSE4, a vyvolávají SEHException – při spuštění dotazů SQL Azure Cosmos DB.

### <a name="a-name11401140"></a>@no__t – 01.14.0
*   Přidání podpory pro novou úroveň konzistence s názvem ConsistentPrefix.
*   Přidání podpory pro metriky dotazů pro jednotlivé oddíly.
*   Přidání podpory pro omezení velikosti tokenu pokračování pro dotazy.
*   Přidání podpory pro podrobnější trasování pro neúspěšné požadavky.
*   Provedli jsme některá vylepšení výkonu v sadě SDK.

### <a name="a-name11341134"></a>@no__t – 01.13.4
* Funkčně stejné jako 1.13.3. Provedli jsme některé interní změny.

### <a name="a-name11331133"></a>@no__t – 01.13.3
* Funkčně stejné jako 1.13.2. Provedli jsme některé interní změny.

### <a name="a-name11321132"></a>@no__t – 01.13.2
* Opravili jsme problém, který ignoroval hodnotu PartitionKey poskytnutou v FeedOptions pro agregované dotazy.
* Opravili jsme problém transparentním zpracováním správy oddílů během zpracování dotazů mezi oddíly středních let.

### <a name="a-name11311131"></a>@no__t – 01.13.1
* Opravili jsme problém, který způsobil zablokování v některých asynchronních rozhraních API při použití uvnitř ASP.NET kontextu.

### <a name="a-name11301130"></a>@no__t – 01.13.0
* Opravuje, aby sada SDK byla za určitých podmínek pružnější pro automatické převzetí služeb při selhání.

### <a name="a-name11221122"></a>@no__t – 01.12.2
* Oprava problému, který občas způsobí WebException: vzdálený název se nedal přeložit.
* Přidání podpory pro přímý čtení typového dokumentu přidáním nových přetížení do rozhraní Readdocumentasync přečteme API.

### <a name="a-name11211121"></a>@no__t – 01.12.1
* Přidání podpory LINQ pro agregační dotazy (COUNT, MIN, MAX, SUM a AVG).
* Opravte problém nevracení paměti pro objekt ConnectionPolicy, který je způsoben použitím obslužné rutiny události.
* Oprava problému, ve kterém UpsertAttachmentAsync nefungovala při použití ETag.
* Oprava problému s řazením mezi oddíly – při pokračování dotazu nefungoval při řazení pole řetězce.

### <a name="a-name11201120"></a>@no__t – 01.12.0
* Přidání podpory agregačních dotazů (COUNT, MIN, MAX, SUM a AVG). Viz [Podpora agregace](sql-query-aggregates.md).
* Byla snížena minimální propustnost u dělených kolekcí z 10 100 RU/s na 2500 RU/s.

### <a name="a-name11141114"></a>@no__t – 01.11.4
* Oprava problému, ve kterém některé z mezioddílových dotazů selžou v procesu 32 hostitele.
* Opravte problém, ve kterém se kontejner relace neaktualizoval s tokenem pro neúspěšné požadavky v režimu brány.
* Oprava problému, který zastavuje dotaz s voláními UDF v projekci, se v některých případech nezdařil.
* Opravy výkonu na straně klienta pro zvýšení propustnosti čtení a zápisu požadavků.

### <a name="a-name11131113"></a>@no__t – 01.11.3
* Oprava problému, ve kterém se kontejner relace neaktualizoval s tokenem pro neúspěšné žádosti.
* Přidání podpory pro sadu SDK pro práci v procesu 32 hostitele. Všimněte si, že pokud používáte Mezioddílové dotazy, doporučujeme pro zlepšení výkonu použití 64 bitového zpracování hostitele.
* Vylepšený výkon pro scénáře zahrnující dotazy s velkým počtem hodnot klíče oddílu ve výrazu IN.
* Vyplněné různé statistiky kvót prostředků v ResourceResponse pro žádosti o čtení kolekce dokumentů, pokud je nastavená možnost PopulateQuotaInfo žádosti

### <a name="a-name11111111"></a>@no__t – 01.11.1
* Menší oprava výkonu pro rozhraní API CreateDocumentCollectionIfNotExistsAsync představené v 1.11.0.
* Oprava výkonu v sadě SDK pro scénáře, které zahrnují vysoký stupeň souběžných požadavků.

### <a name="a-name11101110"></a>@no__t – 01.11.0
* Podpora nových tříd a metod pro zpracování [kanálu změn](change-feed.md) dokumentů v rámci kolekce.
* Podpora pro pokračování v dotazech mezi oddíly a některá vylepšení výkonu pro dotazy na více oddílů.
* Přidání metod CreateDatabaseIfNotExistsAsync a CreateDocumentCollectionIfNotExistsAsync.
* Podpora LINQ pro systémové funkce: je definována, IsNull a primitivum.
* Oprava pro automatické binplacing Microsoft. Azure. Documents. ServiceInterop. dll a DocumentDB. prostor. SQL. dll do složky bin aplikace při použití balíčku NuGet s projekty, které mají nástroje Project. JSON.
* Podpora pro vygenerování trasování ETW na straně klienta, která by mohla být užitečná ve scénářích ladění.

### <a name="a-name11001100"></a>@no__t – 01.10.0
* Přidání podpory přímého připojení pro dělené kolekce.
* Vylepšený výkon pro úroveň konzistence s ohraničenou kostarou.
* Přidání mnohoúhelníku a LineString DataTypes při určování zásad indexování kolekce pro prostorové vytváření geografických zón.
* Při překladu predikátů byla přidána podpora LINQ pro StringEnumConverter, IsoDateTimeConverter a UnixDateTimeConverter.
* Různé opravy chyb sady SDK.

### <a name="a-name195195"></a>@no__t – 01.9.5
* Opravili jsme problém, který způsobil následující NotFoundException: relace čtení není k dispozici pro token vstupní relace. K této výjimce došlo v některých případech při dotazování pro oblast pro čtení geograficky distribuovaného účtu.
* Vlastnost ResponseStream byla vystavena ve třídě ResourceResponse, která umožňuje přímý přístup k podkladovým datovým proudům z odpovědi.

### <a name="a-name194194"></a>@no__t – 01.9.4
* Třídy ResourceResponse, FeedResponse, StoredProcedureResponse a MediaResponse byly upraveny tak, aby se implementovaly odpovídající veřejné rozhraní tak, aby bylo možné je nasazovat pro nasazení řízené testem (TDD).
* Opravili jsme problém, který způsobil nesprávně vytvořenou hlavičku klíče oddílu při použití vlastního objektu JsonSerializerSettings k serializaci dat.

### <a name="a-name193193"></a>@no__t – 01.9.3
* Opravili jsme problém, který způsobil, že dlouho běžící dotazy selžou s chybou: autorizační token není v aktuálním čase platný.
* Opravili jsme problém, který odebral původní SqlParameterCollection z různých dotazů mezi jednotlivými oddíly.

### <a name="a-name192192"></a>@no__t – 01.9.2
* Přidání podpory pro paralelní dotazy pro dělené kolekce.
* Přidání podpory pro řazení mezi oddíly a nejčastější dotazy pro dělené kolekce.
* Opravili jsme chybějící odkazy na DocumentDB. prostor. SQL. dll a Microsoft. Azure. Documents. ServiceInterop. dll, které jsou požadovány při odkazování na Azure Cosmos DB projekt s odkazem na Azure Cosmos DB balíček NuGet.
* Při použití uživatelsky definovaných funkcí v technologii LINQ je možné použít parametry různých typů. 
* Opravili jsme chybu globálně replikovaných účtů, kde Upsert volání byla směrována na umístění pro čtení, nikoli na umístění pro zápis.
* Do rozhraní IDocumentClient, které chybí, byly přidány metody: 
  * Metoda UpsertAttachmentAsync, která přijímá mediaStream a možnosti jako parametry
  * Metoda CreateAttachmentAsync, která přebírá možnosti jako parametr
  * Metoda CreateOfferQuery, která přijímá querySpec jako parametr.
* Nezapečetěné veřejné třídy, které jsou vystaveny v rozhraní IDocumentClient.

### <a name="a-name180180"></a>@no__t – 01.8.0
* Přidání podpory pro účty databáze ve více oblastech.
* Přidání podpory pro opakování u omezení požadavků.  Uživatel může přizpůsobit počet opakovaných pokusů a maximální čekací dobu tím, že nakonfiguruje vlastnost ConnectionPolicy. RetryOptions.
* Bylo přidáno nové rozhraní IDocumentClient, které definuje podpisy všech vlastností a metod DocumentClient.  V rámci této změny se také změnily rozšiřující metody, které vytvářejí IQueryable a IOrderedQueryable metody na samotné třídě DocumentClient.
* Přidání možnosti konfigurace pro nastavení ServicePoint. ConnectionLimit pro daný identifikátor URI koncového bodu Azure Cosmos DB.  Pomocí ConnectionPolicy. MaxConnectionLimit změňte výchozí hodnotu, která je nastavená na 50.
* Zastaralá IPartitionResolver a její implementace.  Podpora pro IPartitionResolver je teď zastaralá. Doporučuje se používat dělené kolekce pro vyšší úložiště a propustnost.

### <a name="a-name171171"></a>@no__t – 01.7.1
* Do metody ExecuteStoredProcedureAsync založené na identifikátoru URI, která jako parametr přijímá RequestOptions, se přidalo přetížení.

### <a name="a-name170170"></a>@no__t – 01.7.0
* Byla přidána podpora TTL (Time to Live) pro dokumenty.

### <a name="a-name163163"></a>@no__t – 01.6.3
* Opravili jsme chybu v balíčku NuGet sady .NET SDK pro balení v rámci řešení cloudové služby Azure.

### <a name="a-name162162"></a>@no__t – 01.6.2
* Implementované [dělené kolekce](partition-data.md) a [uživatelem definované úrovně výkonu](performance-levels.md). 

### <a name="a-name153153"></a>@no__t – 01.5.3
* **[Opraveno]** Dotazování Azure Cosmos DBho koncového bodu vyvolá: System .NET. http. HttpRequestException: při kopírování obsahu do streamu došlo k chybě.

### <a name="a-name152152"></a>@no__t – 01.5.2
* Rozšířená podpora LINQ, včetně nových operátorů pro stránkování, podmíněné výrazy a porovnání rozsahu.
  * Pokud chcete povolit možnost vybrat hlavní chování v LINQ, použijte operátor.
  * CompareTo – operátor pro povolení porovnání rozsahu řetězce
  * Podmíněný operátor (?) a operátory sloučení (??)
* **[Opraveno]** ArgumentOutOfRangeException při kombinování projekce modelů pomocí WHERE-in v dotazu LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a>@no__t – 01.5.1
* **[Opraveno]** Pokud možnost Select není posledním výrazem, nepředpokládá zprostředkovatel LINQ žádnou projekci a vygenerovala příkaz SELECT * nesprávně.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a>@no__t – 01.5.0
* Implementované Upsert, přidané metody UpsertXXXAsync
* Vylepšení výkonu pro všechny požadavky
* Podpora zprostředkovatele LINQ pro metody podmíněného, COALESCE a CompareTo pro řetězce
* **[Opraveno]** Zprostředkovatel LINQ--> implementovat obsahuje metodu pro vygenerování stejného SQL jako v rozhraní IEnumerable a Array.
* **[Opraveno]** BackoffRetryUtility používá při opakovaném pokusu stejný zprávy HttpRequestMessage znovu místo vytvoření nového.
* **[Zastaralé]** UriFactory. CreateCollection--> by teď měl používat UriFactory. CreateDocumentCollection

### <a name="a-name141141"></a>@no__t – 01.4.1
* **[Opraveno]** Problémy lokalizace při použití informací o nestandardní jazykové verzi, jako je nl-NL atd. 

### <a name="a-name140140"></a>@no__t – 01.4.0
* Přidání směrování na základě ID
  * Nová pomocná rutina UriFactory pro pomoc s vytvářením odkazů na prostředky na základě ID
  * Nová přetížení v DocumentClient, která se mají provést v identifikátoru URI
* Přidání IsValid () a IsValidDetailed () v LINQ pro geoprostorové
* Rozšířená podpora zprostředkovatele LINQ:
  * **Math** -ABS, ACOS, asin, atan, strop, cos, EXP, Floor, log, log10 –, POW, Round, Sign, Sin, sqrt, Tan, zkrácení
  * **String** -Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, nahrazení, reverzní, TrimEnd, StartsWith, Substring, ToUpper
  * **Array** -Concat, Contains, Count
  * **In** – operátor

### <a name="a-name130130"></a>@no__t – 01.3.0
* Přidání podpory pro úpravu zásad indexování.
  * Nová metoda ReplaceDocumentCollectionAsync v DocumentClient
  * Nová vlastnost IndexTransformationProgress v ResourceResponse @ no__t-0T > pro sledování procent průběhu změn zásad indexu
  * Dokumentcollection. IndexingPolicy je nyní proměnlivý.
* Přidání podpory pro prostorové indexování a dotazování
  * Nový obor názvů Microsoft. Azure. Documents. prostor pro serializaci/deserializaci prostorových typů, jako je Point a mnohoúhelník
  * Nová třída SpatialIndex pro indexování dat uložených v Cosmos DB
* **[Opraveno]** Nesprávný dotaz SQL generovaný z výrazu LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a>@no__t – 01.2.0
* Přidání závislosti na Newtonsoft. JSON v 5.0.7.
* Změny v pořadí podpory byly provedeny:
  
  * Podpora zprostředkovatele LINQ pro OrderBy () nebo OrderByDescending ()
  * IndexingPolicy na podporu ORDER by 
    
    **Možná Průlomová změna** 
    
    Pokud máte existující kód, který zřídí kolekce s vlastními zásadami indexování, je nutné aktualizovat existující kód tak, aby podporoval novou třídu IndexingPolicy. Pokud nemáte žádné vlastní zásady indexování, pak tato změna neovlivní vás.

### <a name="a-name110110"></a>@no__t – 01.1.0
* Přidání podpory pro dělení dat pomocí nových tříd HashPartitionResolver a RangePartitionResolver a IPartitionResolver.
* Přidání serializace kontraktu DataContract.
* Do poskytovatele LINQ byla přidána podpora identifikátoru GUID.
* Do LINQ se přidala podpora UDF.

### <a name="a-name100100"></a>@no__t – 01.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Data vyřazení & vydání
Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože se tak doporučuje kdykoli nejdříve upgradovat na nejnovější verzi sady SDK. 

Všechny požadavky na Azure Cosmos DB s využitím vyřazené sady SDK jsou službou odmítnuty.

> [!WARNING]
> Všechny verze **1. x** sady .NET SDK pro rozhraní SQL API budou vycházet z **30. srpna 2020**.
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
| [2.2.3](#2.2.3) |11. února 2019 |--- |
| [bodě](#2.2.2) |06 6. února 2019 |--- |
| [2.2.1](#2.2.1) |24. prosince 2018 |--- |
| [2.2.0](#2.2.0) |7\. prosince 2018 |--- |
| [2.1.3](#2.1.3) |15. října 2018 |--- |
| [2.1.2](#2.1.2) |4\. října 2018 |--- |
| [bodů](#2.1.1) |27. září 2018 |--- |
| [2.1.0](#2.1.0) |21. září 2018 |--- |
| [2.0.0](#2.0.0) |7\. září 2018 |--- |
| [1.22.0](#1.22.0) |19. dubna 2018 | 30. srpna 2020 |
| [1.21.1](#1.20.1) |9\. března 2018 |30. srpna 2020 |
| [1.20.2](#1.20.1) |21. února 2018 |30. srpna 2020 |
| [1.20.1](#1.20.1) |05. února 2018 |30. srpna 2020 |
| [1.19.1](#1.19.1) |16. listopadu 2017 |30. srpna 2020 |
| [1.19.0](#1.19.0) |10. listopadu 2017 |30. srpna 2020 |
| [1.18.1](#1.18.1) |07. listopadu 2017 |30. srpna 2020 |
| [1.18.0](#1.18.0) |17. října 2017 |30. srpna 2020 |
| [1.17.0](#1.17.0) |10. srpna 2017 |30. srpna 2020 |
| [1.16.1](#1.16.1) |7\. srpna 2017 |30. srpna 2020 |
| [1.16.0](#1.16.0) |2\. srpna 2017 |30. srpna 2020 |
| [1.15.0](#1.15.0) |30. června 2017 |30. srpna 2020 |
| [1.14.1](#1.14.1) |23. května 2017 |30. srpna 2020 |
| [1.14.0](#1.14.0) |10. května 2017 |30. srpna 2020 |
| [1.13.4](#1.13.4) |9\. května 2017 |30. srpna 2020 |
| [1.13.3](#1.13.3) |6\. května 2017 |30. srpna 2020 |
| [1.13.2](#1.13.2) |19. dubna 2017 |30. srpna 2020 |
| [1.13.1](#1.13.1) |29. března 2017 |30. srpna 2020 |
| [1.13.0](#1.13.0) |24. března 2017 |30. srpna 2020 |
| [1.12.2](#1.12.2) |20. března 2017 |30. srpna 2020 |
| [1.12.1](#1.12.1) |14. března 2017 |30. srpna 2020 |
| [1.12.0](#1.12.0) |15. února 2017 |30. srpna 2020 |
| [1.11.4](#1.11.4) |06 6. února 2017 |30. srpna 2020 |
| [1.11.3](#1.11.3) |26. ledna 2017 |30. srpna 2020 |
| [1.11.1](#1.11.1) |21. prosince 2016 |30. srpna 2020 |
| [1.11.0](#1.11.0) |08 8. prosince 2016 |30. srpna 2020 |
| [1.10.0](#1.10.0) |27. září 2016 |30. srpna 2020 |
| [1.9.5](#1.9.5) |Září 01, 2016 |30. srpna 2020 |
| [1.9.4](#1.9.4) |24. srpna 2016 |30. srpna 2020 |
| [1.9.3](#1.9.3) |15. srpna 2016 |30. srpna 2020 |
| [1.9.2](#1.9.2) |23. července 2016 |30. srpna 2020 |
| [1.8.0](#1.8.0) |14. června 2016 |30. srpna 2020 |
| [1.7.1](#1.7.1) |6\. května 2016 |30. srpna 2020 |
| [1.7.0](#1.7.0) |26. dubna 2016 |30. srpna 2020 |
| [1.6.3](#1.6.3) |08 8. dubna 2016 |30. srpna 2020 |
| [1.6.2](#1.6.2) |29. března 2016 |30. srpna 2020 |
| [1.5.3](#1.5.3) |19. února 2016 |30. srpna 2020 |
| [2.5.1](#1.5.2) |14. prosince 2015 |30. srpna 2020 |
| [1.5.1](#1.5.1) |23. listopadu 2015 |30. srpna 2020 |
| [1.5.0](#1.5.0) |05. října 2015 |30. srpna 2020 |
| [bodů](#1.4.1) |25. srpna 2015 |30. srpna 2020 |
| [1.4.0](#1.4.0) |13. srpna 2015 |30. srpna 2020 |
| [1.3.0](#1.3.0) |05. srpna 2015 |30. srpna 2020 |
| [1.2.0](#1.2.0) |06 z července 2015 |30. srpna 2020 |
| [1.1.0](#1.1.0) |30. dubna 2015 |30. srpna 2020 |
| [1.0.0](#1.0.0) |08 8. dubna 2015 | 30. srpna 2020 |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také:
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) . 

