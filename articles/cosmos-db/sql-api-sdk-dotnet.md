---
title: 'Azure Cosmos DB: SQL API pro .NET, SDK a zdroje informací'
description: Další informace o SQL API pro .NET a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi .NET SDK služby Azure Cosmos DB.
author: rnagpal
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7f3b2fc0e5cc75bccbaf075dc2e2a2be8c7801d
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255954"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK pro rozhraní SQL API: Stáhněte si a poznámky k verzi
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Stažení sady SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Dokumentace k rozhraní API**</td><td>[Referenční dokumentace rozhraní API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Ukázky**</td><td>[Ukázky kódu .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s .NET SDK služby Azure Cosmos DB](sql-api-get-started.md)</td></tr>

<tr><td>**Kurz vývoje webové aplikace**</td><td>[Vývoj webových aplikací pomocí služby Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Aktuální podporované architektury**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-Preview
* Ve verzi Preview 1 [verze 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) sady .NET SDK pro verzi public preview.
* Cíl .NET Standard, která podporuje 4.6.1+ rozhraní .NET framework a .NET Core 2.0 +
* Nový model objektu, pomocí metod a nejvyšší úrovně CosmosClient rozdělit mezi relevantní CosmosDatabases, CosmosContainers a CosmosItems třídy. 
* Podpora pro datové proudy. 
* Aktualizované CosmosResponseMessage ze serveru vrátit stavový kód a pouze vyvolat výjimku, pokud není vrácena žádná odpověď. 

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* U přímé/TCP přenosu diagnostiky přidá TransportException, vnitřní výjimka typu sady SDK. Pokud je k dispozici v zprávy o výjimkách, vytiskne tento typ Další informace pro řešení problémů s připojením klienta.

* Přidání nové konstruktor přetížení, která přebírá objekt HttpMessageHandler sada obslužných rutin HTTP pro odesílání požadavků HttpClient (například HttpClientHandler).

* Oprava chyby, kde záhlaví s hodnotami null nebyly zajišťující správné zpracování.

* Ověření mezipaměti vylepšené kolekce.

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
* Oprava chyby, kde není respektováno JsonProperty atribut v klauzuli select dotazů LINQ.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Oprava chyby, který je přístupů za určitých podmínek závodu, jehož výsledkem přerušované "Microsoft.Azure.Documents.NotFoundException: Další relace není k dispozici pro token vstupní relace"chyby při použití úrovně konzistence relace.

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

* Interní změny pro sestavení Microsoft přátel.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Přidat několik vylepšení a oprav spolehlivosti.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Přidání podpory pro PartitionKeyRangeId jako FeedOption pro vymezení výsledky dotazu na rozsah klíče hodnotu konkrétního oddílu. 
* Přidání podpory pro StartTime jako ChangeFeedOption, abyste mohli začít sledovat změny po uplynutí této doby.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Opravili jsme problém ve třídě JsonSerializable, který může způsobit výjimku přetečení zásobníku.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Opravili jsme problém, který vyžaduje opětovné kompilaci aplikace kvůli implementaci JsonSerializerSettings jako volitelný parametr v konstruktoru DocumentClient.
* Označená jako konstruktoru DocumentClient zastaralá tento požadovaný JsonSerializerSettings jako poslední parametr umožňující ConnectionPolicy výchozí hodnoty a parametry ConsistencyLevel při předávání parametru JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Přidání podpory pro zadání vlastní JsonSerializerSettings při tvorbě instance [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Opravili jsme problém, který x64 měla vliv na počítače, které není podpora SSE4 instrukce a vyvolání SEHException – v případě spouštění dotazů SQL služby Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Volá se přidání podpory pro nové úrovně konzistence ConsistentPrefix.
*   Přidání podpory pro dotaz metriky pro jednotlivé oddíly.
*   Přidání podpory pro omezení velikosti token pro pokračování pro dotazy.
*   Přidání podpory pro podrobné trasování pro chybné žádosti.
*   Provedli několik vylepšení výkonu v sadě SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funkčně stejný jako otázku 1.13.3. Některé změny interní.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funkčně stejný jako 1.13.2. Některé změny interní.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Opravili jsme problém, který ignoruje hodnotu PartitionKey podle FeedOptions agregačních dotazů.
* Opravili jsme problém v transparentního zpracování oddílu správy během napříč oddíly uprostřed letu klauzule Order By provádění dotazů.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Opravili jsme chybu, která způsobila zablokování v některé z asynchronní rozhraní API při použití v rámci kontextu technologie ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Opravy, aby sada SDK odolnější automatické převzetí služeb při selhání za určitých podmínek.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Oprava problému, který občas způsobí, že o výjimku WebException: Vzdálený název se nedá rozpoznat.
* Přidání podpory pro přímo tak, že přidáte nová přetížení ReadDocumentAsync rozhraní API pro čtení typu dokumentu.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Přidání podpory LINQ dotazů agregace (počet, MIN, MAX, součet a průměr).
* Opravili problém nevrácení paměti pro objekt ConnectionPolicy způsobené použijte obslužnou rutinu události.
* Oprava problému, ve které nebyl UpsertAttachmentAsync pracovat, když se použil značku ETag.
* Oprava problému, ve které se mezi oddíl klauzule order by dotazu pokračování práce při řazení na pole řetězce.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Přidání podpory pro dotazy agregace (počet, MIN, MAX, součet a průměr). Zobrazit [podporu agregace](how-to-sql-query.md#Aggregates).
* Snížili minimální propustnost na dělené kolekce z 10,100 RU/s na 2 500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Oprava problému, ve které některé dotazy napříč oddíly nefungovala v 32bitové hostitelský proces.
* Oprava problému, ve které kontejneru relace se aktualizuje s tokenem pro chybné žádosti v režimu brány.
* Oprava problému, ve které se volá dotaz pomocí systému souborů UDF v projekci došlo k selhání v některých případech.
* Opravy zvyšuje propustnost čtení a zápis požadavků výkonu na straně klienta.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Oprava problému, ve které kontejneru relace se aktualizuje s tokenem pro chybné žádosti.
* Přidání podpory pro sadu SDK pro práci v 32bitové hostitelský proces. Všimněte si, že pokud používáte dotazy napříč oddílu, 64bitový hostitelský zpracování se doporučuje za účelem vylepšení výkonu.
* Vylepšený výkon pro scénáře zahrnující dotazy s velkým počtem hodnot klíče oddílu ve výrazu IN.
* Vyplní různých statistiky kvóty prostředků v ResourceResponse pro žádostí o čtení, pokud je nastavena možnost žádost o PopulateQuotaInfo kolekce dokumentů.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Oprava menší výkon rozhraní API CreateDocumentCollectionIfNotExistsAsync zavedený 1.11.0.
* Výkon oprava v sadě SDK pro scénáře, které se týkají vysoký stupeň souběžných požadavků.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Podpora pro nové třídy a metody pro zpracování [kanálu změn](change-feed.md) dokumentů v kolekci.
* Podpora pro pokračování dotazu napříč oddíly a několik vylepšení výkonu pro dotazy napříč oddíly.
* Přidání metody CreateDatabaseIfNotExistsAsync a CreateDocumentCollectionIfNotExistsAsync.
* LINQ podpora pro funkce systému: IsDefined, IsNull a IsPrimitive.
* Oprava pro automatické binplacing Microsoft.Azure.Documents.ServiceInterop.dll a DocumentDB.Spatial.Sql.dll sestavení do složky bin aplikace při používání balíčku Nuget. s projekty, které mají nástroje project.json.
* Podpora výstupu trasování událostí pro Windows trasování na straně klienta, které by mohly být užitečné při ladění scénářů.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Přímé připojení k přidání podpory používejte u dělených kolekcí.
* Vylepšený výkon pro úroveň konzistence omezená Neaktuálnost.
* Přidání mnohoúhelníků a datové typy LineString při zadávání kolekce indexování zásady pro monitorování geografických zón geograficky prostorových dotazů.
* Přidání LINQ podpory pro StringEnumConverter, IsoDateTimeConverter a UnixDateTimeConverter při překladu predikáty.
* Různé opravy chyb sady SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Opravili jsme problém, který způsobil následující NotFoundException: Další relace není k dispozici pro token vstupní relace. V některých případech došlo k této výjimce při dotazování na oblast čtení účtu geograficky distribuované.
* Zpřístupnit vlastnost ResponseStream ve třídě ResourceResponse, která umožňuje přímý přístup do podkladového datového proudu z odpovědi.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Upravit ResourceResponse, FeedResponse, StoredProcedureResponse a MediaResponse třídy k implementaci odpovídající veřejného rozhraní tak, aby může být imitace pro testování řízené nasazení (TDD).
* Opravili jsme problém, který způsobil hlavičku poškozený oddíl klíče při použití vlastního objektu JsonSerializerSettings pro serializaci dat.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Opravili jsme problém, který způsobil dlouho běžící dotazy k selhání s chybou: Autorizační token není platný v aktuálním čase.
* Opravili jsme problém, který odebrán původní SqlParameterCollection z křížové oddílu top/Order dotazy.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Přidání podpory pro paralelní dotazy používejte u dělených kolekcí.
* Přidání podpory pro různé oddíl klauzule ORDER BY a zároveň klauzuli TOP dotazů používejte u dělených kolekcí.
* Vyřešili chybět odkazy na DocumentDB.Spatial.Sql.dll a Microsoft.Azure.Documents.ServiceInterop.dll, které jsou potřeba při odkazování na projekt Azure Cosmos DB s odkazem na balíček Nuget pro Azure Cosmos DB.
* Opravili jsme možnost používat parametry různých typů, při použití uživatelsky definovaných funkcí v jazyce LINQ. 
* Je opravená chyba, globálně replikovaného účty ve kterém byly volání funkcí Upsert směrování do umístění, namísto zápis umístění pro čtení.
* Přidání metody IDocumentClient rozhraní, které chyběly: 
  * UpsertAttachmentAsync metoda, která přijímá mediaStream a možnosti jako parametry
  * CreateAttachmentAsync metoda, která přebírá jako parametr možnosti
  * CreateOfferQuery metodu, která přebírá querySpec jako parametr.
* Nezapečetěné veřejné třídy, které jsou vystaveny rozhraní IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidání podpory pro účty databáze ve více oblastech.
* Přidání podpory pro opakování v omezené požadavky.  Uživatel může upravit počet opakovaných pokusů a maximální čekací doba nakonfigurováním ConnectionPolicy.RetryOptions vlastnost.
* Přidání nového rozhraní IDocumentClient, který definuje podpisy všechny DocumenClient vlastností a metod.  V rámci této změny se změní také rozšiřující metody, které vytvářejí IQueryable a IOrderedQueryable metod na vlastní třídy DocumentClient.
* Možnost přidání konfigurace nastavení ServicePoint.ConnectionLimit pro daný koncový bod služby Azure Cosmos DB identifikátoru Uri.  Pomocí ConnectionPolicy.MaxConnectionLimit můžete změnit výchozí hodnotu, která je nastavena na 50.
* Nepoužívané IPartitionResolver a jeho implementaci.  Podpora pro IPartitionResolver je nyní zastaralé. Doporučuje se použít dělené kolekce vyšší úložiště a propustnost.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Přidat že přetížení na identifikátor Uri na základě ExecuteStoredProcedureAsync metoda, která přebírá jako parametr RequestOptions.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Další čas pro podporu live (TTL) pro dokumenty.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Oprava chyby v balíčku Nuget sady .NET SDK pro balení jako součást řešení cloudové služby Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementovat [dělené kolekce](partition-data.md) a [uživatelsky definovaným výkonem úrovně](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Opraveno]**  Vyvolá koncový bod dotazování služby Azure Cosmos DB: "System.Net.Http.HttpRequestException: Při kopírování obsahu do datového proudu došlo k chybě ".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Rozšířené LINQ podpory, včetně nových operátorů pro stránkování, podmíněné výrazy a být v rozsahu porovnání.
  * Využijte operátor povolit vyberte horní chování v LINQ
  * Metoda CompareTo operátor povolit rozsah porovnávání řetězců
  * Podmíněný (?) a slučte operátory (?)
* **[Opraveno]**  ArgumentOutOfRangeException při kombinování modelu projekce s Where v dotazu LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Opraveno]**  Nejsou-li vybrat posledního výrazu zprostředkovatele LINQ předpokládá, že žádná projekce a ZAŠKRTNĚTE vyrobené * nesprávně.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementováno Upsert, přidá UpsertXXXAsync metody
* Vylepšení výkonu pro všechny požadavky
* Podpora zprostředkovatele LINQ pro podmíněné, sloučení a metody CompareTo pro řetězce
* **[Opraveno]**  Zprostředkovatele LINQ--> obsahuje implementace metody v seznamu pro generování SQL stejné jako na IEnumerable a pole
* **[Opraveno]**  BackoffRetryUtility používá stejnou hodnotu HttpRequestMessage znovu místo vytvoření nového při opakování
* **[Zastaralé]**  --> UriFactory.CreateCollection byste teď měli použít UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Opraveno]**  Lokalizace problémy při použití jiných en informace o jazykové verzi jako je například nl-NL, atd. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Přidání, směrování na základě ID
  * Nové UriFactory pomocné rutiny jako pomoc s vytváření odkazů na základě ID prostředku
  * Nová přetížení na DocumentClient do identifikátoru URI
* Přidání IsValid() a IsValidDetailed() v technologii LINQ pro geoprostorové
* Rozšířená podpora zprostředkovatele LINQ:
  * **Matematické** – funkce Abs, funkce Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, kruhové, přihlášení, Sin, Sqrt, Tan, zkrátit
  * **Řetězec** -Concat, obsahuje, EndsWith, IndexOf, Count, ToLower, TrimStart, nahraďte, obrátit, TrimEnd, StartsWith, podřetězec, ToUpper
  * **Pole** -Concat, obsahuje, počet
  * **V** – operátor

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Přidání podpory pro úpravu zásady indexování.
  * Nová metoda ReplaceDocumentCollectionAsync v DocumentClient
  * Nové vlastnosti IndexTransformationProgress v ResourceResponse<T> pro sledování pokroku procent změn zásad indexu
  * DocumentCollection.IndexingPolicy je nyní mutable
* Přidání podpory pro prostorová indexování a dotazování.
  * Jako nový obor názvů Microsoft.Azure.Documents.Spatial pro serializaci/deserializaci prostorové typy jsou Point a Polygon
  * Nová třída SpatialIndex indexování GeoJSON data uložená ve službě Cosmos DB
* **[Opraveno]**  Nesprávné SQL dotaz vygenerovaný z výrazu LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Přidat závislost na Newtonsoft.Json v5.0.7.
* Změny, které podporují klauzule Order By:
  
  * Podpora zprostředkovatele LINQ pro OrderBy() nebo OrderByDescending()
  * IndexingPolicy pro podporu klauzule Order By 
    
    **Je to možné zásadní změna** 
    
    Pokud máte existující kód této kolekce ustanovení pomocí vlastních zásad indexování, pak svůj existující kód musí být aktualizované kvůli podpoře novou třídu IndexingPolicy. Pokud nemáte žádné vlastní zásady indexování, pak tato změna neovlivní vám.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Přidání podpory pro dělení dat s využitím nových třídách HashPartitionResolver a RangePartitionResolver a IPartitionResolver.
* Přidání serializace pomocí kontraktu DataContract.
* Přidání identifikátoru GUID podpora u zprostředkovatele LINQ.
* Přidání UDF se podporují v technologii LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Společnost Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK, proto se doporučuje, vždy upgradovat na nejnovější SDK verzi co možná nejdříve. 

Služba odmítne všechny žádosti do služby Azure Cosmos DB pomocí vyřazeno sady SDK.

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [2.2.0](#2.2.0) |07 prosince 2018 |--- |
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
| [1.13.4](#1.13.4) |09. května 2017 |--- |
| [1.13.3](#1.13.3) |06. května 2017 |--- |
| [1.13.2](#1.13.2) |19. dubna 2017 |--- |
| [1.13.1](#1.13.1) |29. března 2017 |--- |
| [1.13.0](#1.13.0) |24. března 2017 |--- |
| [1.12.2](#1.12.2) |20. března 2017 |--- |
| [1.12.1](#1.12.1) |14. března 2017 |--- |
| [1.12.0](#1.12.0) |15. února 2017 |--- |
| [1.11.4](#1.11.4) |06. února 2017 |--- |
| [1.11.3](#1.11.3) |26. ledna 2017 |--- |
| [1.11.1](#1.11.1) |21. prosince 2016 |--- |
| [1.11.0](#1.11.0) |08. prosince 2016 |--- |
| [1.10.0](#1.10.0) |27. září 2016 |--- |
| [1.9.5](#1.9.5) |01. září 2016 |--- |
| [1.9.4](#1.9.4) |24. srpna 2016 |--- |
| [1.9.3](#1.9.3) |15. srpna 2016 |--- |
| [1.9.2](#1.9.2) |23. července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.1](#1.7.1) |06. května 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.3](#1.6.3) |08. dubna 2016 |--- |
| [1.6.2](#1.6.2) |29. března 2016 |--- |
| [1.5.3](#1.5.3) |19. února 2016 |--- |
| [1.5.2](#1.5.2) |14. prosince 2015 |--- |
| [1.5.1](#1.5.1) |23. listopadu 2015 |--- |
| [1.5.0](#1.5.0) |05. října 2015 |--- |
| [1.4.1](#1.4.1) |25. srpna 2015 |--- |
| [1.4.0](#1.4.0) |13. srpna 2015 |--- |
| [1.3.0](#1.3.0) |05. srpna 2015 |--- |
| [1.2.0](#1.2.0) |06. července 2015 |--- |
| [1.1.0](#1.1.0) |30. dubna 2015 |--- |
| [1.0.0](#1.0.0) |08. dubna 2015 |--- |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

