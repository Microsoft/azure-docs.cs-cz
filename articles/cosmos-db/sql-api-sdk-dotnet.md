---
title: 'Azure Cosmos DB: Rozhraní API .NET SQL, sadu SDK a prostředky | Microsoft Docs'
description: Další informace o rozhraní API .NET SQL a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi .NET SDK služby Azure Cosmos DB.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/09/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 662a1d1d0f13b64cc87ab6eb0eee6af94cd97c54
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK pro rozhraní API pro SQL: stažení a poznámky k verzi
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
> 
> 

<table>

<tr><td>**Stažení sady SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Dokumentaci k rozhraní API**</td><td>[Referenční dokumentace rozhraní API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Ukázky**</td><td>[Ukázky kódu rozhraní .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s .NET SDK služby Azure Cosmos DB](sql-api-get-started.md)</td></tr>

<tr><td>**Kurz vývoje webové aplikace**</td><td>[Vývoj webových aplikací s Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Aktuální podporovaných prostředí**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi
### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Mezi oddílu pořadí opraven KeyNotFoundException pro dotazy v nestandardních případech.
* Opravené chyby, kde nebyl se dodržení JsonPropery atribut v klauzuli select pro dotazy LINQ.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Opravené chybu, která je přístupů za určitých podmínek soupeření, jejímž výsledkem přerušované "Microsoft.Azure.Documents.NotFoundException: čtení relace není k dispozici pro vstupní relace token" chyby při použití úrovně konzistence relace.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Pevné regrese kde FeedOptions.MaxItemCount = -1 došlo System.ArithmeticException: velikost stránky je záporná.
* Přidat novou funkci ToString() do QueryMetrics.
* Zveřejněné statistiky oddílu na čtení kolekce.
* Přidané vlastnosti PartitionKey k ChangeFeedOptions.
* Menšími opravami chyb.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Přidá možnost zadat jedinečný indexů pro dokumenty pomocí vlastnosti UniqueKeyPolicy na DocumentCollection.
* Opravit chyby, ve kterém nebyly se dodržení vlastní nastavení serializátor JsonSerializer pro některé dotazy a spuštění uložené procedury.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Změna z Azure DocumentDB k databázi Azure Cosmos v referenční dokumentace rozhraní API Branding dokumentaci, informace o metadatech v sestavení a balíček NuGet. 
* Vystavení diagnostické informace a latence z odpovědi požadavky odeslané s režimem přímé připojení. Názvy vlastností, které jsou RequestDiagnosticsString a RequestLatency u ResourceResponse třídy.
* Tato verze sady SDK vyžaduje nejnovější verze emulátoru DB Cosmos Azure k dispozici ke stažení https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Interní změny nástroje pro sestavení sady Microsoft přátel.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Přidat několik oprav spolehlivost a vylepšení.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Přidaná podpora pro PartitionKeyRangeId jako FeedOption pro vymezení výsledky dotazu a hodnotu klíče rozsahu konkrétního oddílu. 
* Přidaná podpora pro StartTime jako ChangeFeedOption zahájíte hledá změny po uplynutí této doby.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Byl opraven problém ve třídě JsonSerializable, která může způsobit výjimce přetečení zásobníku.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Opravit problém, který vyžaduje nutnosti rekompilace aplikace z důvodu zavedení JsonSerializerSettings jako volitelný parametr konstruktoru DocumentClient.
* Označené konstruktor DocumentClient zastaralé této požadované JsonSerializerSettings jako poslední parametr umožňující výchozí hodnoty ConnectionPolicy a ConsistencyLevel parametry při předávání v parametru JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Přidaná podpora pro zadání vlastní JsonSerializerSettings při vytváření instance [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Byl opraven problém ovlivňující x64 počítače, které nemají podporu SSE4 instrukce a throw SEHException – při spuštění dotazů SQL databáze Azure Cosmos.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Přidaná podpora pro novou úroveň konzistence volá ConsistentPrefix.
*   Přidaná podpora pro dotaz metriky pro jednotlivé oddíly.
*   Přidaná podpora pro omezení velikosti token pokračování pro dotazy.
*   Přidaná podpora pro podrobnější trasování pro chybné žádosti.
*   Provedli jsme některé vylepšení výkonu v sadě SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funkčně stejné jako otázku 1.13.3. Některé změny interní.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funkčně stejné jako 1.13.2. Některé změny interní.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Opravit problém, který ignoruje PartitionKey hodnota zadaná v FeedOptions pro agregační dotazy.
* Byl opraven problém v transparentní zpracování oddílu správy během letu střední cross-partition Order By dotazu provádění.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Byl opraven problém, který chybu způsobil zablokování v některých asynchronní rozhraní API, pokud se používá v kontextu ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Opravy aby SDK pružnější automatické převzetí služeb při selhání za určitých podmínek.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Opravte pro problém způsobující příležitostně o výjimku WebException: vzdálený název nelze rozpoznat.
* Přidaná podpora pro přímo čtení typu dokumentu přidáním nové přetížení ReadDocumentAsync rozhraní API.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Byla přidána podpora LINQ pro dotazy agregace (COUNT, MIN, MAX, součet a průměr).
* Opravte pro problém nevracení paměti pro objekt ConnectionPolicy způsobené použití obslužné rutiny události.
* Oprava problému, ve kterém nebyl UpsertAttachmentAsync pracovat, když byla použita značka ETag.
* Oprava problému, ve kterém nebyl křížové oddílu klauzule order by dotazu pokračování práce při řazení na pole řetězce.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Přidaná podpora pro dotazy agregace (COUNT, MIN, MAX, součet a průměr). V tématu [podporu agregace](sql-api-sql-query.md#Aggregates).
* Snížena minimální propustnosti na dělené kolekce z 10,100 RU/s na 2 500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Oprava problému, ve kterém některé dotazy, mezi oddílu nebylo možné v procesu 32bitové hostitele.
* Oprava problému, ve kterém relace kontejneru nebyla aktualizace se token pro neúspěšné požadavky v režimu brány.
* Oprava problému, ve kterém se dotaz s UDF volá v projekci došlo k selhání v některých případech.
* Výkonu na straně klienta opravy pro zvyšuje propustnost čtení a zápisu požadavků.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Oprava problému, ve kterém relace kontejneru nebyla aktualizace se token pro chybné žádosti.
* Byla přidána podpora pro sadu SDK pro práci v 32bitové hostitelském procesu. Všimněte si, že pokud používáte křížové oddílu dotazy, zpracování hostitelem 64-bit se doporučuje pro zlepšení výkonu.
* Lepší výkon pro scénáře zahrnující dotazy s velkým počtem hodnoty klíče oddílu ve výrazu IN.
* Naplní různé statistiky kvótu prostředků v ResourceResponse pro kolekce dokumentů požadavků na čtení nastavena možnost PopulateQuotaInfo požadavku.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Oprava rozhraní API CreateDocumentCollectionIfNotExistsAsync byla zavedená v 1.11.0 menší výkon.
* Výkon oprava v sadě SDK pro scénáře, které zahrnují vysoký stupeň souběžných požadavků.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Podpora pro nové třídy a metody pro zpracování [změnit informačního kanálu](change-feed.md) dokumentů v rámci kolekce.
* Podpora pro pokračování dotaz mezi oddílu a některých zlepšení výkonu pro dotazy mezi oddílu.
* Přidání metody CreateDatabaseIfNotExistsAsync a CreateDocumentCollectionIfNotExistsAsync.
* LINQ podpora pro funkce systému: IsDefined, IsNull a IsPrimitive.
* Opravte pro automatické binplacing Microsoft.Azure.Documents.ServiceInterop.dll a DocumentDB.Spatial.Sql.dll sestavení do složky bin aplikace při používání balíček Nuget s projekty, které mají project.json nástrojů.
* Podpora pro generování trasování ETW straně klienta, které by mohly být užitečné při ladění scénáře.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Podpora přidané přímé připojení pro dělené kolekce.
* Lepší výkon pro úroveň konzistence typu s ohraničenou Prošlostí.
* Přidání mnohoúhelníku a datové typy LineString při zadávání kolekce indexování zásady pro geografického vymezení prostorových dotazů.
* Přidání LINQ podporu pro StringEnumConverter, IsoDateTimeConverter a UnixDateTimeConverter při překladu predikáty.
* Opravy chyb různé sady SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Byl opraven problém, který způsobil následující NotFoundException: čtení relace není k dispozici pro token vstupní relace. V některých případech došlo k této výjimce při dotazování pro čtení oblast účtu zeměpisné polohy.
* Zveřejněné ResponseStream vlastnost ve třídě ResourceResponse, která umožňuje přímý přístup do základního datového proudu z odpovědi.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Upravit ResourceResponse, FeedResponse, StoredProcedureResponse a MediaResponse třídy k implementaci odpovídající veřejné rozhraní, takže může být mocked pro test řízené nasazení (TDD).
* Byl opraven problém způsobující hlavičku klíče poškozený oddílu při použití vlastního objektu JsonSerializerSettings pro serializaci dat.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Byl opraven problém způsobující dlouho běžící dotazy k selhání s chybou: autorizační token není platný v aktuálním čase.
* Opravit problém, který odebere původní SqlParameterCollection z křížové oddílu horní /-Order dotazy.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Přidaná podpora pro paralelní dotazy pro dělené kolekce.
* Přidaná podpora pro různé dotazy ORDER BY a horní oddílu pro dělené kolekce.
* Pevné chybí odkazy na DocumentDB.Spatial.Sql.dll a Microsoft.Azure.Documents.ServiceInterop.dll, které jsou požadovány při odkazování na projekt Azure Cosmos DB s odkazem na balíček Azure Cosmos DB Nuget.
* Pevné možnost používat parametry různých typů, při použití uživatelem definované funkce v technologii LINQ. 
* Pevné chyby pro globální replikované účty, kde byly Upsert volání směrovat ke čtení umístění místo zápisu umístění.
* Přidání metody IDocumentClient rozhraní, které chyběly: 
  * UpsertAttachmentAsync metody, která přijímá mediaStream a možnosti jako parametry
  * CreateAttachmentAsync metoda, která přebírá jako parametr možnosti
  * CreateOfferQuery metoda, která přebírá querySpec jako parametr.
* Nezapečetěné veřejné třídy, které jsou zveřejněné v IDocumentClient rozhraní.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidaná podpora pro účty databáze více oblast.
* Přidaná podpora pro opakování u omezenému požadavků.  Uživatele můžete přizpůsobit počet opakovaných pokusů a maximální čekací doba nakonfigurováním ConnectionPolicy.RetryOptions vlastnost.
* Přidat nové IDocumentClient rozhraní, které definuje signatur všechny DocumenClient vlastnosti a metody.  V rámci této změny se změní také rozšiřující metody, které vytvořit položku IQueryable a IOrderedQueryable metody pro vlastní třídy DocumentClient.
* Přidání konfigurace možnost nastavit ServicePoint.ConnectionLimit pro danou koncový bod Azure Cosmos DB identifikátor Uri.  Chcete-li změnit výchozí hodnotu, která je nastavena na 50 použijte ConnectionPolicy.MaxConnectionLimit.
* Nepoužívané IPartitionResolver a jeho implementace.  Podpora pro IPartitionResolver je nyní zastaralá. Doporučuje se používat kolekce rozdělena na oddíly pro vyšší úložiště a propustnosti.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Přidat ExecuteStoredProcedureAsync metodu, která přebírá jako parametr RequestOptions na základě přetížení na identifikátor Uri.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Další čas live (TTL) podpory pro dokumenty.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Opravě chyby v balíčku Nuget sady .NET SDK pro balení jej jako součást řešení Azure Cloud Service.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementovat [oddíly kolekce](partition-data.md) a [úrovně výkonu uživatelem definované](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Pevné]**  Vyvolá dotaz na databázi Azure Cosmos koncový bod: ' System.Net.Http.HttpRequestException: Při kopírování obsahu do datového proudu došlo k chybě ".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* LINQ rozšířené podpory, včetně nových operátorů pro stránkování, podmíněné výrazy a rozsahu porovnání.
  * Take – operátor povolit vyberte horní chování v LINQ
  * Operátor CompareTo povolit rozsah porovnání řetězců
  * Podmíněné (?) a slučovat operátory (?)
* **[Pevné]**  Výjimka ArgumentOutOfRangeException při kombinování modelu projekce s Where-v v dotazu LINQ. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Pevné]**  Pokud vyberte není poslední výrazu LINQ zprostředkovatele předpokládá, že žádná projekce a vytváří vyberte * nesprávně.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementovaná Upsert, přidat UpsertXXXAsync metody
* Vylepšení výkonu pro všechny požadavky
* Podpora zprostředkovatele LINQ pro podmíněného, sloučení a metody CompareTo pro řetězce
* **[Pevné]**  LINQ zprostředkovatele--> obsahuje implementujte metodu na seznamu generovat stejný SQL na rozhraní IEnumerable a pole
* **[Pevné]**  BackoffRetryUtility používá stejnou hodnotu HttpRequestMessage znovu místo vytvoření nové při opakování
* **[Zastaralé]**  --> UriFactory.CreateCollection nyní využít UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Pevné]**  Lokalizace problémy při použití jiných en informace o jazykové nl-NL, např. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Přidat na základě ID směrování
  * Nového pomocníka UriFactory vám pomůže při vytváření odkazů na základě ID prostředku
  * Nové přetížení na DocumentClient provést v identifikátoru URI
* Přidání IsValid() a IsValidDetailed() v technologii LINQ pro geoprostorové
* Rozšířená podpora LINQ zprostředkovatele:
  * **Matematické** -Abs, Acos, Asin, Atan, Ceiling Cos Exp, Floor, protokolu, Log10, Pow, kruhové, přihlášení, Sin, Sqrt, Tan, zkrátit
  * **Řetězec** -Concat, obsahuje, EndsWith, IndexOf, Count, ToLower, TrimStart, nahraďte, Reverse TrimEnd, StartsWith, SubString, ToUpper
  * **Pole** -Concat, obsahuje, počet
  * **V** – operátor

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Přidaná podpora pro úpravy zásady indexování.
  * Nová metoda ReplaceDocumentCollectionAsync v DocumentClient
  * Nové vlastnosti IndexTransformationProgress v ResourceResponse<T> pro sledování procenta průběh změny zásad indexu
  * DocumentCollection.IndexingPolicy je nyní měnitelný
* Byla přidána podpora pro prostorových indexování a dotazu.
  * Nový obor názvů Microsoft.Azure.Documents.Spatial pro serializaci nebo deserializaci prostorové typy jako bod a mnohoúhelníku
  * Nová třída SpatialIndex pro indexování dat GeoJSON uloženy v databázi systému Cosmos
* **[Pevné]**  Dotazu nesprávný SQL vygenerovat z výrazu LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Přidat závislost na Newtonsoft.Json v5.0.7.
* Provedené změny pro podporu Order:
  
  * LINQ Podpora zprostředkovatele pro OrderBy() nebo OrderByDescending()
  * IndexingPolicy pro podporu Order By 
    
    **Možné narušující změně** 
    
    Pokud máte existující kód této kolekce zřizuje s vlastní zásady indexování, pak váš stávající kód musí být aktualizované kvůli podpoře novou třídu IndexingPolicy. Pokud máte žádné vlastní zásady indexování, pak tato změna nemá vliv můžete.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Byla přidána podpora pro dělení dat pomocí nové třídy HashPartitionResolver a RangePartitionResolver a IPartitionResolver.
* Přidání kontraktu serializace.
* Přidání GUID podporují v LINQ zprostředkovatele.
* Přidání UDF podporují v technologii LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>Verze & vyřazení kalendářních dat
Společnost Microsoft poskytuje oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový se doporučuje, aby vždy upgradu na nejnovější verze sady SDK v míře. 

Služba odmítne všechny požadavky pro Azure DB Cosmos pomocí vyřazeno sady SDK.

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.21.1](#1.20.1) |09 března 2018 |--- |
| [1.20.2](#1.20.1) |21. února 2018 |--- |
| [1.20.1](#1.20.1) |05 února 2018 |--- |
| [1.19.1](#1.19.1) |16. listopadu 2017 |--- |
| [1.19.0](#1.19.0) |10 listopadu 2017 |--- |
| [1.18.1](#1.18.1) |07 listopadu 2017 |--- |
| [1.18.0](#1.18.0) |17 říjen 2017 |--- |
| [1.17.0](#1.17.0) |10. srpnu 2017 |--- |
| [1.16.1](#1.16.1) |07 srpen 2017 |--- |
| [1.16.0](#1.16.0) |02 srpen 2017 |--- |
| [1.15.0](#1.15.0) |30. června 2017 |--- |
| [1.14.1](#1.14.1) |23 může 2017 |--- |
| [1.14.0](#1.14.0) |10. května 2017 |--- |
| [1.13.4](#1.13.4) |09 může 2017 |--- |
| [1.13.3](#1.13.3) |06 může 2017 |--- |
| [1.13.2](#1.13.2) |19. dubna 2017 |--- |
| [1.13.1](#1.13.1) |29. března 2017 |--- |
| [1.13.0](#1.13.0) |24 března 2017 |--- |
| [1.12.2](#1.12.2) |20. března 2017 |--- |
| [1.12.1](#1.12.1) |14. března 2017 |--- |
| [1.12.0](#1.12.0) |15. února 2017 |--- |
| [1.11.4](#1.11.4) |06 února 2017 |--- |
| [1.11.3](#1.11.3) |26. ledna 2017 |--- |
| [1.11.1](#1.11.1) |21 prosince 2016 |--- |
| [1.11.0](#1.11.0) |08 prosinec 2016 |--- |
| [1.10.0](#1.10.0) |27 září 2016 |--- |
| [1.9.5](#1.9.5) |01 září 2016 |--- |
| [1.9.4](#1.9.4) |24 srpna 2016 |--- |
| [1.9.3](#1.9.3) |15 srpna 2016 |--- |
| [1.9.2](#1.9.2) |23. července 2016 |--- |
| [1.8.0](#1.8.0) |14. června 2016 |--- |
| [1.7.1](#1.7.1) |06. května 2016 |--- |
| [1.7.0](#1.7.0) |26. dubna 2016 |--- |
| [1.6.3](#1.6.3) |08. dubna 2016 |--- |
| [1.6.2](#1.6.2) |29. března 2016 |--- |
| [1.5.3](#1.5.3) |19. února 2016 |--- |
| [1.5.2](#1.5.2) |14. prosince 2015 |--- |
| [1.5.1](#1.5.1) |23. listopadu 2015 |--- |
| [1.5.0](#1.5.0) |05 říjen 2015 |--- |
| [1.4.1](#1.4.1) |25 srpen 2015 |--- |
| [1.4.0](#1.4.0) |13 srpen 2015 |--- |
| [1.3.0](#1.3.0) |05 srpen 2015 |--- |
| [1.2.0](#1.2.0) |06 července 2015 |--- |
| [1.1.0](#1.1.0) |30. dubna 2015 |--- |
| [1.0.0](#1.0.0) |08 duben 2015 |--- |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

