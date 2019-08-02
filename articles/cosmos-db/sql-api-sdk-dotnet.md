---
title: 'Azure Cosmos DB: SQL .NET API, sada SDK & prostředky'
description: Přečtěte si všechno o rozhraních API a sadě SDK pro SQL .NET, včetně dat vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 4380bf81d05aa5247b57605b2aa53d24a73a0f68
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638589"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK pro SQL API: Poznámky ke stažení a verzi
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
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
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Ukázky**|[Ukázky kódu .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Začínáme**|[Začínáme s Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Kurz vývoje webové aplikace**|[Vývoj webových aplikací pomocí služby Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuální podporované architektury**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Poznámky k verzi

> [!NOTE]
> Pokud používáte .NET Framework, přečtěte si nejnovější verzi 3. x sady [.NET SDK](sql-api-sdk-dotnet-standard.md), která cílí na .NET Standard. 

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* Verze System .NET. http sady SDK se nyní shoduje s tím, co je definováno v balíčku NuGet.
* Povolí použití požadavků na zápis do jiné oblasti v případě, že se původní chyba nezdařila.
* Přidejte zásady opakování relace pro požadavek na zápis.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Opravuje podmínku sledování časování pro dotazy, které způsobily prázdné stránky.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Zvýšená velikost desetinné přesnosti pro dotazy LINQ.
* Přidání nových tříd CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType a PartitionKeyDefinitionVersion
* Přidání TimeToLivePropertyPath do souboru DocumentCollection
* Přidání CompositeIndexes a SpatialIndexes do IndexPolicy
* Přidání verze do PartitionKeyDefinition
* Přidáno None do PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Do ConnectionPolicy přidejte IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection a MaxTcpConnectionsPerEndpoint.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Vylepšení diagnostiky

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Bylo přidáno nastavení proměnné prostředí POCOSerializationOnly.

* Odebrali jsme DocumentDB. prostor. SQL. dll a teď je součástí Microsoft. Azure. Documents. ServiceInterop. dll.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Vylepšení logiky opakování při převzetí služeb při selhání pro StoredProcedure volání.

* Byl vytvořen DocumentClientEventSource typu singleton. 

* Oprava GatewayAddressCache timeout nedodržuje ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Pro diagnostiku přímých/TCP přenosů přidejte TransportException, typ interní výjimky sady SDK. V případě zpráv výjimek tento typ tiskne Další informace pro řešení potíží s připojením klientů.

* Bylo přidáno nové přetížení konstruktoru, které přijímá HttpMessageHandler, zásobník obslužných rutin HTTP, který se má použít pro odesílání požadavků HttpClient (např. HttpClientHandler).

* Oprava chyby, kdy hlavička s hodnotami null nebyla správně zpracována

* Vylepšené ověřování mezipaměti kolekce

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Aktualizované System.Net.Security k 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Vylepšení trasování diagnostiky

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Přidat další odolnost vůči přechodným selháním žádosti více oblastí.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Přidání více oblastí zápisu podpory.
* Mezi vylepšení výkonu dotazů oddílu s horní a MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Přidání žádosti o podporu zrušení.
* Přidání SetCurrentLocation k ConnectionPolicy, který automaticky naplní upřednostňované umístění podle oblastí.
* Oprava chyby v různé dotazy oddílu s Min/Max a filtr, který odpovídá žádné dokumenty v jednotlivých oddílů.
* DocumentClient metody mají nyní se IDocumentClient.
* Aktualizované s přímým přístupem přenosu protokolů TCP k omezení počtu připojení.
* Přidání podpory pro přímý režim TCP pro klienty než Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Přidání žádosti o podporu zrušení.
* Přidání SetCurrentLocation k ConnectionPolicy, který automaticky naplní upřednostňované umístění podle oblastí.
* Oprava chyby v různé dotazy oddílu s Min/Max a filtr, který odpovídá žádné dokumenty v jednotlivých oddílů.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* DocumentClient metody mají nyní se IDocumentClient.
* Aktualizované s přímým přístupem přenosu protokolů TCP k omezení počtu připojení.
* Přidání podpory pro přímý režim TCP pro klienty než Windows.

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Přidání vlastnosti ConsistencyLevel do FeedOptions.
* Přidání JsonSerializerSettings RequestOptions a FeedOptions.
* Přidání EnableReadRequestsFallback k ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Oprava keynotfoundexception – pro různé uspořádání oddílu na základě dotazů v krajních případech.
* Byla opravena chyba, kdy atribut JsonProperty v klauzuli SELECT pro dotazy LINQ nebyl dodržen.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Opravená chyba, ke které dochází za určitých podmínek časování, což vede k občasnému "Microsoft. Azure. Documents. NotFoundException: Relace čtení není k dispozici pro chyby tokenu vstupní relace při použití úrovně konzistence relace.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Oprava regrese kde FeedOptions.MaxItemCount = -1 došlo System.ArithmeticException: velikost stránky je záporný.
* Přidat novou funkci ToString() do QueryMetrics.
* Vystavená statistiky oddílu na kolekce pro čtení.
* Přidání vlastnosti PartitionKey do ChangeFeedOptions.
* Menšími opravami chyb.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Přidá schopnost určit jedinečné indexy pro dokumenty pomocí vlastnosti UniqueKeyPolicy na DocumentCollection.
* Je opravená chyba, ve kterém nebyly respektováno vlastní nastavení serializátor JsonSerializer pro některé dotazy a spuštění uložené procedury.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Branding změny z Azure DocumentDB do služby Azure Cosmos DB v referenční dokumentaci rozhraní API, dokumentaci, informace o metadatech v sestaveních a balíček NuGet. 
* Diagnostické informace a čekací doba odpovědi žádosti odeslané s režimem přímé připojení k zpřístupnit. Názvy vlastností, které jsou ve třídě ResourceResponse RequestDiagnosticsString a RequestLatency.
* Tato verze sady SDK vyžaduje nejnovější verzi Azure emulátor služby Cosmos DB k dispozici ke stažení z https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Interní změny pro sestavení přátel Microsoftu.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Přidat několik vylepšení a oprav spolehlivosti.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Přidání podpory pro PartitionKeyRangeId jako FeedOption pro vymezení výsledky dotazu na rozsah klíče hodnotu konkrétního oddílu. 
* Přidání podpory pro StartTime jako ChangeFeedOption, abyste mohli začít sledovat změny po uplynutí této doby.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Opravili jsme problém ve třídě JsonSerializable, který může způsobit výjimku přetečení zásobníku.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Opravili jsme problém, který vyžadoval opětovné kompilování aplikace z důvodu zavedení JsonSerializerSettings jako volitelného parametru v konstruktoru DocumentClient.
* Byl označen konstruktor DocumentClient zastaralý, který vyžadoval JsonSerializerSettings jako poslední parametr pro povolení výchozích hodnot parametrů ConnectionPolicy a ConsistencyLevel při předávání parametru JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Přidání podpory pro zadání vlastních JsonSerializerSettings při vytváření instancí [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Opravili jsme problém, který ovlivnil počítače x64, které nepodporují instrukci SSE4, a vyvolávají SEHException – při spuštění dotazů SQL Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Volá se přidání podpory pro nové úrovně konzistence ConsistentPrefix.
*   Přidání podpory pro dotaz metriky pro jednotlivé oddíly.
*   Přidání podpory pro omezení velikosti token pro pokračování pro dotazy.
*   Přidání podpory pro podrobné trasování pro chybné žádosti.
*   Provedli několik vylepšení výkonu v sadě SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funkčně stejné jako 1.13.3. Provedli jsme některé interní změny.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funkčně stejné jako 1.13.2. Provedli jsme některé interní změny.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Opravili jsme problém, který ignoruje hodnotu PartitionKey podle FeedOptions agregačních dotazů.
* Opravili jsme problém v transparentního zpracování oddílu správy během napříč oddíly uprostřed letu klauzule Order By provádění dotazů.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Opravili jsme chybu, která způsobila zablokování v některé z asynchronní rozhraní API při použití v rámci kontextu technologie ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Opravy, aby sada SDK odolnější automatické převzetí služeb při selhání za určitých podmínek.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Oprava problému, který občas způsobí WebException: Vzdálený název se nedá rozpoznat.
* Přidání podpory pro přímo tak, že přidáte nová přetížení ReadDocumentAsync rozhraní API pro čtení typu dokumentu.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Přidání podpory LINQ dotazů agregace (počet, MIN, MAX, součet a průměr).
* Opravili problém nevrácení paměti pro objekt ConnectionPolicy způsobené použijte obslužnou rutinu události.
* Oprava problému, ve které nebyl UpsertAttachmentAsync pracovat, když se použil značku ETag.
* Oprava problému, ve které se mezi oddíl klauzule order by dotazu pokračování práce při řazení na pole řetězce.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Přidání podpory pro dotazy agregace (počet, MIN, MAX, součet a průměr). Zobrazit [podporu agregace](sql-query-aggregates.md).
* Snížili minimální propustnost na dělené kolekce z 10,100 RU/s na 2 500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Oprava problému, ve kterém některé z mezioddílových dotazů selžou v procesu 32 hostitele.
* Opravte problém, ve kterém se kontejner relace neaktualizoval s tokenem pro neúspěšné požadavky v režimu brány.
* Oprava problému, který zastavuje dotaz s voláními UDF v projekci, se v některých případech nezdařil.
* Opravy výkonu na straně klienta pro zvýšení propustnosti čtení a zápisu požadavků.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Oprava problému, ve kterém se kontejner relace neaktualizoval s tokenem pro neúspěšné žádosti.
* Přidání podpory pro sadu SDK pro práci v procesu 32 hostitele. Všimněte si, že pokud používáte Mezioddílové dotazy, doporučujeme pro zlepšení výkonu použití 64 bitového zpracování hostitele.
* Vylepšený výkon pro scénáře zahrnující dotazy s velkým počtem hodnot klíče oddílu ve výrazu IN.
* Vyplněné různé statistiky kvót prostředků v ResourceResponse pro žádosti o čtení kolekce dokumentů, pokud je nastavená možnost PopulateQuotaInfo žádosti

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Menší oprava výkonu pro rozhraní API CreateDocumentCollectionIfNotExistsAsync představené v 1.11.0.
* Oprava výkonu v sadě SDK pro scénáře, které zahrnují vysoký stupeň souběžných požadavků.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Podpora nových tříd a metod pro zpracování [kanálu změn](change-feed.md) dokumentů v rámci kolekce.
* Podpora pro pokračování v dotazech mezi oddíly a některá vylepšení výkonu pro dotazy na více oddílů.
* Přidání metod CreateDatabaseIfNotExistsAsync a CreateDocumentCollectionIfNotExistsAsync.
* Podpora LINQ pro systémové funkce: Je definován, IsNull a primitiv.
* Oprava pro automatické binplacing Microsoft. Azure. Documents. ServiceInterop. dll a DocumentDB. prostor. SQL. dll do složky bin aplikace při použití balíčku NuGet s projekty, které mají nástroje Project. JSON.
* Podpora pro vygenerování trasování ETW na straně klienta, která by mohla být užitečná ve scénářích ladění.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Přidání podpory přímého připojení pro dělené kolekce.
* Vylepšený výkon pro úroveň konzistence s ohraničenou kostarou.
* Přidání mnohoúhelníků a datové typy LineString při zadávání kolekce indexování zásady pro monitorování geografických zón geograficky prostorových dotazů.
* Při překladu predikátů byla přidána podpora LINQ pro StringEnumConverter, IsoDateTimeConverter a UnixDateTimeConverter.
* Různé opravy chyb sady SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Opravili jsme problém, který způsobil následující NotFoundException: Relace čtení není k dispozici pro token vstupní relace. K této výjimce došlo v některých případech při dotazování pro oblast pro čtení geograficky distribuovaného účtu.
* Vlastnost ResponseStream byla vystavena ve třídě ResourceResponse, která umožňuje přímý přístup k podkladovým datovým proudům z odpovědi.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Třídy ResourceResponse, FeedResponse, StoredProcedureResponse a MediaResponse byly upraveny tak, aby se implementovaly odpovídající veřejné rozhraní tak, aby bylo možné je nasazovat pro nasazení řízené testem (TDD).
* Opravili jsme problém, který způsobil nesprávně vytvořenou hlavičku klíče oddílu při použití vlastního objektu JsonSerializerSettings k serializaci dat.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Opravili jsme problém, který způsobil, že dlouho běžící dotazy selžou s chybou: Autorizační token není v aktuálním čase platný.
* Opravili jsme problém, který odebral původní SqlParameterCollection z různých dotazů mezi jednotlivými oddíly.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
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

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidání podpory pro účty databáze ve více oblastech.
* Přidání podpory pro opakování u omezení požadavků.  Uživatel může přizpůsobit počet opakovaných pokusů a maximální čekací dobu tím, že nakonfiguruje vlastnost ConnectionPolicy. RetryOptions.
* Bylo přidáno nové rozhraní IDocumentClient, které definuje podpisy všech vlastností a metod DocumentClient.  V rámci této změny se také změnily rozšiřující metody, které vytvářejí IQueryable a IOrderedQueryable metody na samotné třídě DocumentClient.
* Přidání možnosti konfigurace pro nastavení ServicePoint. ConnectionLimit pro daný identifikátor URI koncového bodu Azure Cosmos DB.  Pomocí ConnectionPolicy. MaxConnectionLimit změňte výchozí hodnotu, která je nastavená na 50.
* Zastaralá IPartitionResolver a její implementace.  Podpora pro IPartitionResolver je teď zastaralá. Doporučuje se používat dělené kolekce pro vyšší úložiště a propustnost.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Do metody ExecuteStoredProcedureAsync založené na identifikátoru URI, která jako parametr přijímá RequestOptions, se přidalo přetížení.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Další čas pro podporu live (TTL) pro dokumenty.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Opravili jsme chybu v balíčku NuGet sady .NET SDK pro balení v rámci řešení cloudové služby Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementovat [dělené kolekce](partition-data.md) a [uživatelsky definovaným výkonem úrovně](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Opraveno]** Dotazování Azure Cosmos DBho koncového bodu vyvolá: 'System.Net.Http.HttpRequestException: Při kopírování obsahu do streamu došlo k chybě.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Rozšířená podpora LINQ, včetně nových operátorů pro stránkování, podmíněné výrazy a porovnání rozsahu.
  * Pokud chcete povolit možnost vybrat hlavní chování v LINQ, použijte operátor.
  * CompareTo – operátor pro povolení porovnání rozsahu řetězce
  * Podmíněný operátor (?) a operátory sloučení (??)
* **[Opraveno]** ArgumentOutOfRangeException při kombinování projekce modelů pomocí WHERE-in v dotazu LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Opraveno]** Pokud možnost Select není posledním výrazem, nepředpokládá zprostředkovatel LINQ žádnou projekci a vygenerovala příkaz SELECT * nesprávně.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementované Upsert, přidané metody UpsertXXXAsync
* Vylepšení výkonu pro všechny požadavky
* Podpora zprostředkovatele LINQ pro metody podmíněného, COALESCE a CompareTo pro řetězce
* **[Opraveno]** Zprostředkovatel LINQ--> implementovat obsahuje metodu pro vygenerování stejného SQL jako v rozhraní IEnumerable a Array.
* **[Opraveno]** BackoffRetryUtility používá při opakovaném pokusu stejný zprávy HttpRequestMessage znovu místo vytvoření nového.
* **[Zastaralé]** UriFactory. CreateCollection--> by teď měl používat UriFactory. CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Opraveno]** Problémy lokalizace při použití informací o nestandardní jazykové verzi, jako je nl-NL atd. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Přidání směrování na základě ID
  * Nová pomocná rutina UriFactory pro pomoc s vytvářením odkazů na prostředky na základě ID
  * Nová přetížení v DocumentClient, která se mají provést v identifikátoru URI
* Přidání IsValid () a IsValidDetailed () v LINQ pro geoprostorové
* Rozšířená podpora zprostředkovatele LINQ:
  * **Math** -ABS, ACOS, asin, atan, strop, cos, EXP, Floor, log, log10 –, POW, Round, Sign, Sin, sqrt, Tan, zkrácení
  * **String** -Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, nahrazení, reverzní, TrimEnd, StartsWith, Substring, ToUpper
  * **Array** -Concat, Contains, Count
  * **In** – operátor

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Přidání podpory pro úpravu zásad indexování.
  * Nová metoda ReplaceDocumentCollectionAsync v DocumentClient
  * Nová vlastnost IndexTransformationProgress v ResourceResponse\<T > pro sledování procentuálního pokroku změny zásad indexu
  * Dokumentcollection. IndexingPolicy je nyní proměnlivý.
* Přidání podpory pro prostorové indexování a dotazování
  * Nový obor názvů Microsoft. Azure. Documents. prostor pro serializaci/deserializaci prostorových typů, jako je Point a mnohoúhelník
  * Nová třída SpatialIndex pro indexování dat uložených v Cosmos DB
* **[Opraveno]** Nesprávný dotaz SQL generovaný z výrazu LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Přidání závislosti na Newtonsoft. JSON v 5.0.7.
* Změny v pořadí podpory byly provedeny:
  
  * Podpora zprostředkovatele LINQ pro OrderBy () nebo OrderByDescending ()
  * IndexingPolicy na podporu ORDER by 
    
    **Možná Průlomová změna** 
    
    Pokud máte existující kód, který zřídí kolekce s vlastními zásadami indexování, je nutné aktualizovat existující kód tak, aby podporoval novou třídu IndexingPolicy. Pokud nemáte žádné vlastní zásady indexování, pak tato změna neovlivní vás.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Přidání podpory pro dělení dat pomocí nových tříd HashPartitionResolver a RangePartitionResolver a IPartitionResolver.
* Přidání serializace kontraktu DataContract.
* Do poskytovatele LINQ byla přidána podpora identifikátoru GUID.
* Do LINQ se přidala podpora UDF.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Data vyřazení & vydání
Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože se tak doporučuje kdykoli nejdříve upgradovat na nejnovější verzi sady SDK. 

Všechny požadavky na Azure Cosmos DB s využitím vyřazené sady SDK jsou službou odmítnuty.

<br/>

| Version | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [2.5.1](#2.5.1) |2\. července 2019 |--- |
| [2.4.1](#2.4.1) |20. června 2019 |--- |
| [2.4.0](#2.4.0) |05. května 2019 |--- |
| [2.3.0](#2.3.0) |04. dubna 2019 |--- |
| [2.2.3](#2.2.3) |11. února 2019 |--- |
| [2.2.2](#2.2.2) |06 6. února 2019 |--- |
| [2.2.1](#2.2.1) |24. prosince 2018 |--- |
| [2.2.0](#2.2.0) |7\. prosince 2018 |--- |
| [2.1.3](#2.1.3) |15. října 2018 |--- |
| [2.1.2](#2.1.2) |04. října 2018 |--- |
| [2.1.1](#2.1.1) |27. září 2018 |--- |
| [2.1.0](#2.1.0) |21. září 2018 |--- |
| [2.0.0](#2.0.0) |07. září 2018 |--- |
| [1.22.0](#1.22.0) |19. dubna 2018 |--- |
| [1.21.1](#1.20.1) |09. března 2018 |--- |
| [1.20.2](#1.20.1) |21. února 2018 |--- |
| [1.20.1](#1.20.1) |05. února 2018 |--- |
| [1.19.1](#1.19.1) |16. listopadu 2017 |--- |
| [1.19.0](#1.19.0) |10. listopadu 2017 |--- |
| [1.18.1](#1.18.1) |07. listopadu 2017 |--- |
| [1.18.0](#1.18.0) |17. října 2017 |--- |
| [1.17.0](#1.17.0) |10. srpna 2017 |--- |
| [1.16.1](#1.16.1) |07. srpna 2017 |--- |
| [1.16.0](#1.16.0) |02. srpna 2017 |--- |
| [1.15.0](#1.15.0) |30. června 2017 |--- |
| [1.14.1](#1.14.1) |23. května 2017 |--- |
| [1.14.0](#1.14.0) |10. května 2017 |--- |
| [1.13.4](#1.13.4) |9\. května 2017 |--- |
| [1.13.3](#1.13.3) |6\. května 2017 |--- |
| [1.13.2](#1.13.2) |19. dubna 2017 |--- |
| [1.13.1](#1.13.1) |29. března 2017 |--- |
| [1.13.0](#1.13.0) |24. března 2017 |--- |
| [1.12.2](#1.12.2) |20. března 2017 |--- |
| [1.12.1](#1.12.1) |14. března 2017 |--- |
| [1.12.0](#1.12.0) |15. února 2017 |--- |
| [1.11.4](#1.11.4) |06 6. února 2017 |--- |
| [1.11.3](#1.11.3) |26. ledna 2017 |--- |
| [1.11.1](#1.11.1) |21. prosince 2016 |--- |
| [1.11.0](#1.11.0) |08 8. prosince 2016 |--- |
| [1.10.0](#1.10.0) |27. září 2016 |--- |
| [1.9.5](#1.9.5) |Září 01, 2016 |--- |
| [1.9.4](#1.9.4) |24. srpna 2016 |--- |
| [1.9.3](#1.9.3) |15. srpna 2016 |--- |
| [1.9.2](#1.9.2) |23. července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.1](#1.7.1) |6\. května 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.3](#1.6.3) |08 8. dubna 2016 |--- |
| [1.6.2](#1.6.2) |29. března 2016 |--- |
| [1.5.3](#1.5.3) |19. února 2016 |--- |
| [1.5.2](#1.5.2) |14. prosince 2015 |--- |
| [1.5.1](#1.5.1) |23. listopadu 2015 |--- |
| [1.5.0](#1.5.0) |05. října 2015 |--- |
| [1.4.1](#1.4.1) |25. srpna 2015 |--- |
| [1.4.0](#1.4.0) |13. srpna 2015 |--- |
| [1.3.0](#1.3.0) |05. srpna 2015 |--- |
| [1.2.0](#1.2.0) |06 z července 2015 |--- |
| [1.1.0](#1.1.0) |30. dubna 2015 |--- |
| [1.0.0](#1.0.0) |08 8. dubna 2015 |--- |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

