---
title: 'Azure Cosmos DB: Rozhraní SQL Java API, SDK & zdroje'
description: Další informace o rozhraní SQL Java API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: 514982727509788918c159e07f8061962df32336
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77558924"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK pro rozhraní SQL API: poznámky k verzi a prostředky
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
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

Java SDK pro rozhraní API SQL podporuje synchronní operace. Pro asynchronní podporu použijte [rozhraní API SQL Async Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Stažení sady SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace k rozhraní Java API](/java/api/com.microsoft.azure.documentdb)|
|**Přispívání do sady SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Začínáme**|[Začínáme se sadou Java SDK](sql-api-java-get-started.md)|
|**Kurz webové aplikace**|[Vývoj webových aplikací pomocí Azure Cosmos DB](sql-api-java-application.md)|
|**Minimální podporovaná doba běhu**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name247247"></a><a name="2.4.7"/>2.4.7
* Opravuje problém s vypršením časového limitu fondu připojení.
* Opravuje aktualizace ověřovacího tokenu u interních pokusů.

### <a name="a-name246246"></a><a name="2.4.6"/>2.4.6
* Aktualizovala se správná značka zásad repliky na straně klienta v databaseAccount a provedla se čtení konfigurace databaseAccount z mezipaměti.

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Pokud uživatel poskytuje pkRangeId, nebudete mít k dispozici chybu rozsahu klíčů pro neplatnou operaci.

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Aktualizace mezipaměti rozsahu klíčů optimalizovaného oddílu
* Opravuje scénář, ve kterém sada SDK nenabízí pomocný parametr rozdělení oddílů ze serveru a má za následek nesprávnou aktualizaci mezipaměti směrování na straně klienta.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Optimalizované aktualizace mezipaměti kolekce

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Přidala se podpora pro načtení zprávy vnitřní výjimky z řetězce diagnostiky požadavku.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Bylo zavedeno rozhraní API verze v PartitionKeyDefinition.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Přidání podpory samostatného časového limitu pro přímý režim.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Spotřebovává se chybová zpráva s hodnotou null ze služby a vyprodukuje se výjimka klienta dokumentu.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Vylepšení připojení soketu, přidání výchozí hodnoty true SoKeepAlive

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Byla přidána podpora řetězců diagnostiky žádostí.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Opravila se chyba v PartitionKey pro algoritmus hash v2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Byla přidána podpora pro složené indexy.
* Opravená chyba v globálním správci koncových bodů pro vynucení aktualizace.
* Opravená chyba pro upsertuje s předběžnými podmínkami v přímém režimu.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Opravená chyba v mezipaměti adres brány

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Podpora více oblastí zápisu přidá pro přímý režim.
* Přidání podpory pro zpracování IOExceptions vyvolána jako ServiceUnavailable výjimky z proxy serveru.
* Oprava chyby v zásadách koncového bodu zjišťování opakování.
* Je opravená chyba, aby ukazatel s hodnotou null, které nejsou ve BaseDatabaseAccountConfigurationProvider vyvolání výjimky.
* Opravili jsme chybu, abyste zajistili, že QueryIterator nevrací hodnoty null.
* Je opravená chyba, ujistěte se, že je povolené velké PartitionKey

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Podpora více oblastí zápisu pro režim bránu přidat.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Oprava chyby v oddílu čtení klíče oblastí pro dotaz.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Oprava chyby v nastavení velikost záhlaví token pokračování v režimu DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Přidání streamovaných převzetí služeb při selhání podpory.
* Přidání podpory pro vlastní metadata.
* Vylepšená logika zpracování relace.
* Oprava chyby v mezipaměti rozsah klíče oddílu.
* Je opravená chyba NPE v přímém režimu.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Přidání podpory pro jedinečný Index.
* Přidání podpory pro omezení velikosti token pokračování v možnostech informačního kanálu.
* Oprava chyby v serializaci Json (časové razítko).
* Oprava chyby v serializaci Json (výčtu).
* Závislost na upgradovat na 2.9.5 com.fasterxml.jackson.core:jackson-databind.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Vylepšené sdružování pro přímý režim.
* Vylepšili jsme předběžného načítání vylepšení pro jiné orderby pro různé oddílu dotazu.
* Vylepšené generování UUID.
* Vylepšená logika konzistence relace.
* Přidání podpory pro multipolygon.
* Přidání podpory pro statistiky rozsah klíče oddílu pro kolekci.
* Oprava chyby v podpora více oblastí.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Vylepšený výkon serializace Json.
* Tato verze sady SDK vyžaduje k dispozici nejnovější verzi Azure Cosmos DB emulátoru ke stažení z https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Interní změny pro knihovny Microsoft přátel.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Opravili jsme problém při čtení klíče oblastí jednoho oddílu.
* Opravili jsme problém v ResourceID analýza kódu, který má vliv na databázi s krátkým názvem.
* Opravili jsme problém způsobit kódování klíče oddílu.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Požádat o zpracování během rozdělení oddílů důležité opravy chyb.
* Opravili jsme problém s úrovní konzistence silná i BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Volá se přidání podpory pro nové úrovně konzistence ConsistentPrefix.
* Oprava chyby v kolekci v relaci režimu pro čtení.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Povolená podpora dělené kolekce s jako nízká 2 500 RU/s a škálovat v přírůstcích po 100 RU/s.
* Oprava chyby v nativní sestavení, což může způsobit výjimku NullRef u některých dotazů.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Oprava chyby v konfiguraci modulu dotazu, který může způsobit výjimky pro dotazy v režimu brány.
* Opravili jsme několik chyb v kontejneru relace, které mohou způsobit výjimku "Vlastník prostředek nebyl nalezen" požadavkům ihned po vytvoření kolekce.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Přidání podpory pro dotazy agregace (počet, MIN, MAX, součet a průměr). Viz [Podpora agregace](sql-query-aggregates.md).
* Přidání podpory pro kanál změn.
* Přidání podpory pro informace o kvótě kolekce prostřednictvím RequestOptions.setPopulateQuotaInfo.
* Přidání podpory pro uloženou proceduru skript protokolování prostřednictvím RequestOptions.setScriptLoggingEnabled.
* Opravili jsme chybu, kdy dotaz v režimu DirectHttps může přestat reagovat při zjištění selhání omezení.
* Oprava chyby v režimu konzistence relace.
* Je opravená chyba, což může způsobit NullReferenceException v objektu HttpContext, když je vysoká frekvence požadavků.
* Vylepšení výkonu DirectHttps režimu.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Přidání jednoduchého klienta proxy server založený na instancích podporu ConnectionPolicy.setProxy() rozhraní API.
* Přidání rozhraní API DocumentClient.close() správně vypnutí instance DocumentClient.
* Zvýšení výkonu dotazů v režimu přímého připojení odvozením plán dotazu z nativní sestavení namísto brány.
* Nastavit FAIL_ON_UNKNOWN_PROPERTIES = false, aby uživatelé nemuseli k definování JsonIgnoreProperties v jejich POJO.
* Refaktorovaný protokolování použití SLF4J.
* Vyřešili několik dalších chyb ve čtečce konzistence.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Oprava chyby ve správě připojení k zabránění únikům připojení v režimu přímého připojení.
* Opravili jsme chybu v HORNÍm dotazu, kde může vyvolat výjimku NullReference.
* Vylepšení výkonu snížením počtu volání sítě pro vnitřní mezipaměti.
* Přidání stavový kód, ID aktivity a požádat o identifikátor URI v DocumentClientException pro lepší řešení potíží.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Opravili jsme problém ve správě připojení pro zvýšení stability.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Přidání podpory pro úroveň konzistence BoundedStaleness.
* Přidání podpory pro přímé připojení pro operace CRUD u dělených kolekcí.
* Oprava chyby v dotazování databáze pomocí jazyka SQL.
* Oprava chyby v mezipaměti relace, ve kterém může být nesprávně nastaven tokenu relace.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Přidání podpory pro různé paralelní dotazy oddílu.
* Přidání podpory pro TOP/ORDER BY dotazů používejte u dělených kolekcí.
* Přidání podpory pro zajištění konzistence.
* Přidání podpory pro název na základě požadavků při použití přímé připojení.
* Opravili jsme aby ActivityId zůstanou konzistentní napříč všechny žádosti o opakování.
* Je opravená chyba související s mezipaměti relace při opětovném vytváření kolekce se stejným názvem.
* Přidání mnohoúhelníků a datové typy LineString při zadávání kolekce indexování zásady pro monitorování geografických zón geograficky prostorových dotazů.
* Opravené problémy s Doc Java pro jazyk Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Oprava chyby v PartitionKeyDefinitionMap kolekce jednoho oddílu do mezipaměti a Nedovolte, aby byly nadbytečné načítání o klíč oddílu.
* Opravili jsme chybu, která není opakovat, pokud je zadaná hodnota klíče nesprávný oddíl.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Přidání podpory pro účty databáze ve více oblastech.
* Přidání podpory pro automatické opakované pokusy na omezené požadavky s možnostmi pro přizpůsobení maximální počet opakovaných pokusů a doba čekání maximální počet opakování.  Viz RetryOptions a ConnectionPolicy.getRetryOptions().
* Nepoužívané IPartitionResolver na základě vlastních oddílů kódu. Použijte prosím dělených kolekcí pro vyšší propustnost a úložiště.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Přidání opakování podpora zásad omezení četnosti.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Další čas pro podporu live (TTL) pro dokumenty.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementované [dělené kolekce](partition-data.md) a [uživatelem definované úrovně výkonu](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Oprava chyby v HashPartitionResolver ke generování hodnot hash v little endian, aby byla konzistentní s jiných sad SDK.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Přidat hodnotu Hash & Rozsah oddílu překladače, která vám pomůže s horizontálního dělení aplikace napříč několika oddíly.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementace Upsert. Nové metody upsertXXX přidaná kvůli podpoře funkcí Upsert.
* Směrování na základě ID implementace. Žádné změny veřejné rozhraní API, všechny změny interní.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Verze přeskočeno vám číslo verze v souladu s jiných sad SDK

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Podporuje geoprostorové indexu
* Ověří vlastnost ID pro všechny prostředky. ID pro prostředky nesmí obsahovat znaky?,/, #, \, nebo končit mezerou.
* Přidá nové záhlaví "index transformace průběh" ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementuje zásady indexování V2

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Microsoft bude před vyřazením sady SDK do novější nebo podporované verze oznámení obsahovat alespoň **12 měsíců** .

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK, proto je doporučujeme vždy upgradovat na nejnovější SDK verzi co možná nejdříve.

Jakoukoli žádost do služby Cosmos DB pomocí vyřazeno sady SDK budou odmítnuty službou.

> [!WARNING]
> Všechny verze **1. x** sady SQL SDK pro jazyk Java budou vyřazeny **30. května 2020**.
> 
>

> [!WARNING]
> Všechny verze sady SQL SDK pro jazyk Java starší než verze **1.0.0** byly vyřazení od **29. února 2016**.
> 
> 

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [2.4.7](#2.4.7) |20. února 2020 |--- |
| [2.4.6](#2.4.6) |24. ledna 2020 |--- |
| [2.4.5](#2.4.5) |10. listopadu 2019 |--- |
| [2.4.4](#2.4.4) |24. října 2019 |--- |
| [2.4.2 sections](#2.4.2) |Září 26, 2019 |--- |
| [2.4.1](#2.4.1) |18. července 2019 |--- |
| [2.4.0](#2.4.0) |4\. května 2019 |--- |
| [2.3.0](#2.3.0) |Duben 24, 2019 |--- |
| [2.2.3](#2.2.3) |16. dubna 2019 |--- |
| [bodě](#2.2.2) |Duben 05, 2019 |--- |
| [2.2.0](#2.2.0) |27. března 2019 |--- |
| [2.1.3](#2.1.3) |13. března 2019 |--- |
| [2.1.2](#2.1.2) |9\. března 2019 |--- |
| [bodů](#2.1.1) |DEC 13, 2018 |--- |
| [2.1.0](#2.1.0) |20. listopadu 2018 |--- |
| [2.0.0](#2.0.0) |21. září 2018 |--- |
| [1.16.4](#1.16.4) |10. září 2018 |30. května 2020 |
| [1.16.3](#1.16.3) |09. září 2018 |30. května 2020 |
| [1.16.2](#1.16.2) |29. června 2018 |30. května 2020 |
| [1.16.1](#1.16.1) |16. května 2018 |30. května 2020 |
| [1.16.0](#1.16.0) |15. března 2018 |30. května 2020 |
| [1.15.0](#1.15.0) |14. listopadu 2017 |30. května 2020 |
| [1.14.0](#1.14.0) |28. října 2017 |30. května 2020 |
| [1.13.0](#1.13.0) |25. srpna 2017 |30. května 2020 |
| [1.12.0](#1.12.0) |11. července 2017 |30. května 2020 |
| [1.11.0](#1.11.0) |10. května 2017 |30. května 2020 |
| [1.10.0](#1.10.0) |11. března 2017 |30. května 2020 |
| [1.9.6](#1.9.6) |21. února 2017 |30. května 2020 |
| [1.9.5](#1.9.5) |Do 31. ledna 2017 |30. května 2020 |
| [1.9.4](#1.9.4) |24. listopadu 2016 |30. května 2020 |
| [1.9.3](#1.9.3) |30. října 2016 |30. května 2020 |
| [1.9.2](#1.9.2) |28. října 2016 |30. května 2020 |
| [1.9.1](#1.9.1) |26. říjnu 2016 |30. května 2020 |
| [1.9.0](#1.9.0) |03. října 2016 |30. května 2020 |
| [1.8.1](#1.8.1) |30. června 2016 |30. května 2020 |
| [1.8.0](#1.8.0) |14. června 2016 |30. května 2020 |
| [1.7.1](#1.7.1) |30. dubna 2016 |30. května 2020 |
| [1.7.0](#1.7.0) |27. dubna 2016 |30. května 2020 |
| [1.6.0](#1.6.0) |29. března 2016 |30. května 2020 |
| [1.5.1](#1.5.1) |31. prosince 2015 |30. května 2020 |
| [1.5.0](#1.5.0) |04. prosince 2015 |30. května 2020 |
| [1.4.0](#1.4.0) |05. října 2015 |30. května 2020 |
| [1.3.0](#1.3.0) |05. října 2015 |30. května 2020 |
| [1.2.0](#1.2.0) |05. srpna 2015 |30. května 2020 |
| [1.1.0](#1.1.0) |09. července 2015 |30. května 2020 |
| 1.0.1 |12. května 2015 |30. května 2020 |
| [1.0.0](#1.0.0) |07. dubna 2015 |30. května 2020 |
| 0.9.5-prelease |09 března 2015 |29. února 2016 |
| 0.9.4-prelease |17. února 2015 |29. února 2016 |
| 0.9.3-prelease |13. ledna 2015 |29. února 2016 |
| 0.9.2-prelease |19. prosince 2014 |29. února 2016 |
| 0.9.1-prelease |19. prosince 2014 |29. února 2016 |
| 0.9.0-prelease |10. prosince 2014 |29. února 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

