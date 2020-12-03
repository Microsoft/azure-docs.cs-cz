---
title: 'Azure Cosmos DB: SQL Java API, SDK & prostředky'
description: Seznamte se se všemi informacemi o rozhraních SQL Java API a SDK, včetně dat vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 0b48b8d954b132caab96e5978c90687899ea04c2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549221"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK pro SQL API: poznámky k verzi a prostředky
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Sada .NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

Toto je původní Azure Cosmos DB synchronizovat sadu Java SDK v2 pro rozhraní SQL API, která podporuje synchronní operace.

> [!IMPORTANT]  
> Nejedná *se o* nejnovější sadu Java SDK pro Azure Cosmos DB. Zvažte použití [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) pro váš projekt. Chcete-li provést upgrade, postupujte podle pokynů v příručce [k migraci na Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) a v příručce pro předaný [objekt actor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) . 
>

| |  |
|---|---|
|**Stažení sady SDK**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace k rozhraní Java API](/java/api/com.microsoft.azure.documentdb)|
|**Přispívání do sady SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Začínáme**|[Začínáme se sadou Java SDK](./create-sql-api-java.md)|
|**Kurz webové aplikace**|[Vývoj webových aplikací pomocí Azure Cosmos DB](sql-api-java-application.md)|
|**Minimální podporovaná doba běhu**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Opravuje problém v mezipaměti primárního oddílu pro dotazcollection dokumentu.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Byla přidána podpora pro 449 opakování vlastní konfigurace.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Opravuje problém s vypršením časového limitu fondu připojení.
* Opravuje aktualizace ověřovacího tokenu u interních pokusů.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Aktualizovala se správná značka zásad repliky na straně klienta v databaseAccount a provedla se čtení konfigurace databaseAccount z mezipaměti.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Pokud uživatel poskytuje pkRangeId, nebudete mít k dispozici chybu rozsahu klíčů pro neplatnou operaci.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Aktualizace mezipaměti rozsahu klíčů optimalizovaného oddílu
* Opravuje scénář, ve kterém sada SDK nenabízí pomocný parametr rozdělení oddílů ze serveru a má za následek nesprávnou aktualizaci mezipaměti směrování na straně klienta.

### <a name="242"></a><a name="2.4.2"></a>2.4.2 sections
* Optimalizované aktualizace mezipaměti kolekce

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Přidala se podpora pro načtení zprávy vnitřní výjimky z řetězce diagnostiky požadavku.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Bylo zavedeno rozhraní API verze v PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Přidání podpory samostatného časového limitu pro přímý režim.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Spotřebovává se chybová zpráva s hodnotou null ze služby a vyprodukuje se výjimka klienta dokumentu.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Vylepšení připojení soketu, přidání výchozí hodnoty true SoKeepAlive

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Byla přidána podpora řetězců diagnostiky žádostí.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Opravila se chyba v PartitionKey pro algoritmus hash v2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Byla přidána podpora pro složené indexy.
* Opravená chyba v globálním správci koncových bodů pro vynucení aktualizace.
* Opravená chyba pro upsertuje s předběžnými podmínkami v přímém režimu.

### <a name="211"></a><a name="2.1.1"></a>bodů
* Opravená chyba v mezipaměti adres brány

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Přidaná podpora zápisu do více oblastí pro přímý režim.
* Přidání podpory pro zpracování IOExceptions vyvolaného jako výjimky ServiceUnavailable z proxy serveru.
* Opravili jsme chybu v zásadách opakování zjišťování koncových bodů.
* Opravili jsme chybu, aby se v BaseDatabaseAccountConfigurationProvider nevolaly výjimky ukazatelů na hodnotu null.
* Opravili jsme chybu, abyste zajistili, že QueryIterator nevrací hodnoty null.
* Opravili jsme chybu, aby se zajistilo, že je povolený velký PartitionKey

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Podpora zápisu do více oblastí se přidala do režimu brány.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Opravili jsme chybu v rozsahu klíče oddílu čtení pro dotaz.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Opravili jsme chybu v nastavení velikosti hlavičky tokenu pokračování v režimu DirectHttps.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Přidání streamování při selhání prostřednictvím podpory.
* Přidání podpory pro vlastní metadata
* Vylepšená logika zpracování relace.
* Opravila se chyba v mezipaměti rozsahu klíčů oddílu.
* V přímém režimu byla opravena chyba NPE.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Byla přidána podpora pro jedinečný index.
* Přidání podpory pro omezení velikosti tokenu pokračování v možnostech kanálu.
* Byla opravena chyba serializace JSON (časové razítko).
* Opravila se chyba serializace JSON (Enum).
* Závislost na modelu COM. fasterxml. Jacksonův diagram. Core: Jacksonův-vázání – proveďte upgrade na 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Vylepšené sdružování připojení pro přímý režim.
* Vylepšené zlepšování předběžného načítání pro neorderby dotaz na různé oddíly.
* Vylepšená generace identifikátoru UUID.
* Vylepšená logika konzistence relace.
* Přidala se podpora pro více mnohoúhelníků.
* Byla přidána podpora statistik rozsahu klíče oddílu pro kolekci.
* Opravili jsme chybu v podpoře více oblastí.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Vylepšený výkon serializace JSON.
* Tato verze sady SDK vyžaduje nejnovější verzi [emulátoru Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Interní změny pro knihovny přátel Microsoftu.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Opravili jsme problém při čtení rozsahů klíčů s jedním oddílem.
* Opravili jsme problém v analýze ResourceID, která má vliv na databázi s krátkými názvy.
* Opravili jsme problém způsobený kódováním klíče oddílu.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Opravy kritické chyby pro zpracování žádostí během rozdělení oddílů
* Opravili jsme problém se silnými a BoundedStaleness úrovněmi konzistence.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Přidání podpory pro novou úroveň konzistence s názvem ConsistentPrefix.
* Opravili jsme chybu v kolekci čtení v režimu relace.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Povolená podpora pro dělené kolekce s nižší než 2 500 RU/s a škálování v přírůstcích po 100 RU/s.
* Opravili jsme chybu v nativním sestavení, která může v některých dotazech způsobit výjimku NullRef.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Opravili jsme chybu v konfiguraci dotazovacího stroje, která může způsobit výjimky pro dotazy v režimu brány.
* Opravili jsme několik chyb v kontejneru relací, které můžou způsobit výjimku "prostředek nenalezen" pro požadavky hned po vytvoření kolekce.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Přidání podpory agregačních dotazů (COUNT, MIN, MAX, SUM a AVG). Viz [Podpora agregace](sql-query-aggregate-functions.md).
* Přidání podpory pro kanál změn
* Přidala se podpora pro informace o kvótě kolekce prostřednictvím RequestOptions. setPopulateQuotaInfo.
* Přidání podpory pro protokolování skriptu uložených procedur přes RequestOptions. setScriptLoggingEnabled.
* Opravili jsme chybu, kdy dotaz v režimu DirectHttps může přestat reagovat při zjištění selhání omezení.
* Opravili jsme chybu v režimu konzistence relace.
* Opravili jsme chybu, která může způsobit NullReferenceException v HttpContext, pokud je míra požadavků vysoká.
* Vylepšený výkon DirectHttps režimu.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Byla přidána podpora proxy serveru založeného na instanci jednoduchého klienta s rozhraním API ConnectionPolicy. SetProxy – ().
* Bylo přidáno rozhraní API DocumentClient. Close () pro správnou vypínání instance DocumentClient.
* Vylepšený výkon dotazů v režimu přímého připojení odvozením plánu dotazů z nativního sestavení namísto brány.
* Nastavte FAIL_ON_UNKNOWN_PROPERTIES = false, aby uživatelé nemuseli definovat JsonIgnoreProperties ve svých POJO.
* Refaktored pro použití SLF4J.
* Opravili jsme několik dalších chyb v čtečce konzistence.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Opravili jsme chybu v řízení připojení, aby se zabránilo úniku nevracení připojení v režimu přímého připojení.
* Opravili jsme chybu v HORNÍm dotazu, kde může vyvolat výjimku NullReference.
* Vylepšený výkon snížením počtu síťových volání interních mezipamětí.
* Přidaný stavový kód, ActivityID a identifikátor URI žádosti v DocumentClientException pro lepší řešení potíží.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Opravili jsme problém se správou připojení pro stabilitu.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Byla přidána podpora úrovně konzistence BoundedStaleness.
* Přidání podpory pro přímé připojení pro operace CRUD pro dělené kolekce.
* Opravili jsme chybu při dotazování databáze pomocí SQL.
* Opravili jsme chybu v mezipaměti relace, kde je token relace nastavený nesprávně.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Byla přidána podpora paralelních dotazů mezi oddíly.
* Přidání podpory pro dotazy TOP/ORDER BY pro dělené kolekce
* Přidání podpory pro silnou konzistenci.
* Přidání podpory pro žádosti založené na názvu při použití přímého připojení.
* Opraveno tak, aby ActivityId zůstaly konzistentní napříč všemi pokusy o žádosti.
* Při opětovném vytváření kolekce se stejným názvem opravila chyba související s mezipamětí relace.
* Přidání mnohoúhelníku a LineString DataTypes při určování zásad indexování kolekce pro prostorové vytváření geografických zón.
* Opravili jsme problémy s dokumentem Java pro Java 1,8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Opravili jsme chybu v PartitionKeyDefinitionMap k ukládání kolekcí s jedním oddílem do mezipaměti a nemusejí mít dodatečné požadavky na načtení klíčů oddílu.
* Opravili jsme chybu, aby se neopakovala, pokud je zadaná nesprávná hodnota klíče oddílu.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Přidání podpory pro účty databáze ve více oblastech.
* Přidání podpory automatického opakování u omezených žádostí s možnostmi pro přizpůsobení maximálního počtu opakovaných pokusů a maximální doby čekání na opakování.  Viz RetryOptions a ConnectionPolicy. getRetryOptions ().
* Zastaralý kód pro vlastní dělení na základě IPartitionResolver Použijte prosím dělené kolekce pro vyšší úložiště a propustnost.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Přidala se podpora zásad opakování pro omezení četnosti.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Byla přidána podpora TTL (Time to Live) pro dokumenty.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementované [dělené kolekce](partitioning-overview.md) a [uživatelem definované úrovně výkonu](performance-levels.md).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Opravili jsme chybu v HashPartitionResolver a generovali hodnoty hash v Little-endian, aby byly konzistentní s jinými sadami SDK.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Přidáním překladačů oddílů rozsahu & hash můžete pomáhat s aplikacemi horizontálního dělení napříč více oddíly.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementujte Upsert. Přidaly se nové metody upsertXXX, které podporují funkci Upsert.
* Implementujte směrování na základě ID. Žádné změny veřejného rozhraní API, všechny změny jsou interní.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Vynechává se vydaná verze, která přináší číslo verze v zarovnání s jinými sadami SDK.

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Podporuje geoprostorové index
* Ověří vlastnost ID pro všechny prostředky. ID pro prostředky nesmí obsahovat znaky?,/, #, \, znaky nebo končit mezerou.
* Přidá novou hlavičku průběh transformace indexu do ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementuje zásadu indexování v2.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Sada GA SDK

## <a name="release-and-retirement-dates"></a>Data o uvolnění a vyřazení

Microsoft bude o vyřazení sady SDK informovat s předstihem nejméně **12 měsíců**, aby mohlo dojít k bezproblémovému přechodu na novější/podporovanou verzi. Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože je to proto, že doporučujeme vždy co nejdříve upgradovat na nejnovější verzi sady SDK.

> [!WARNING]
> Po 30. května 2020 už Azure Cosmos DB neprovádí opravy chyb, přidávají nové funkce a poskytuje podporu pro verze 1. x sady Azure Cosmos DB Java SDK pro SQL API. Pokud nechcete provést upgrade, služba Azure Cosmos DB bude dál zpracovávat požadavky odeslané z verze 1.x sady SDK.
>
> Po 29. února 2016 již Azure Cosmos DB neprovádí opravy chyb, přidávají nové funkce a poskytují podporu pro verze 0. x sady Azure Cosmos DB Java SDK pro rozhraní SQL API. Pokud nechcete upgradovat, požadavky odeslané z verze 0. x sady SDK budou nadále obsluhovány službou Azure Cosmos DB.


| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [2.5.1](#2.5.1) |03. června 2020 |--- |
| [2.5.0](#2.5.0) |12. května 2020 |--- |
| [2.4.7](#2.4.7) |20. února 2020 |--- |
| [2.4.6](#2.4.6) |24. ledna 2020 |--- |
| [2.4.5](#2.4.5) |10. listopadu 2019 |--- |
| [2.4.4](#2.4.4) |24. října 2019 |--- |
| [2.4.2 sections](#2.4.2) |Září 26, 2019 |--- |
| [2.4.1](#2.4.1) |18. července 2019 |--- |
| [2.4.0](#2.4.0) |4. května 2019 |--- |
| [2.3.0](#2.3.0) |Duben 24, 2019 |--- |
| [2.2.3](#2.2.3) |16. dubna 2019 |--- |
| [2.2.2](#2.2.2) |Duben 05, 2019 |--- |
| [2.2.0](#2.2.0) |27. března 2019 |--- |
| [2.1.3](#2.1.3) |13. března 2019 |--- |
| [2.1.2](#2.1.2) |9. března 2019 |--- |
| [bodů](#2.1.1) |DEC 13, 2018 |--- |
| [2.1.0](#2.1.0) |20. listopadu 2018 |--- |
| [2.0.0](#2.0.0) |Září 21, 2018 |--- |
| [1.16.4](#1.16.4) |Září 10, 2018 |30. května 2020 |
| [1.16.3](#1.16.3) |Září 09, 2018 |30. května 2020 |
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
| [1.9.5](#1.9.5) |31. ledna 2017 |30. května 2020 |
| [1.9.4](#1.9.4) |24. listopadu 2016 |30. května 2020 |
| [1.9.3](#1.9.3) |30. října 2016 |30. května 2020 |
| [1.9.2](#1.9.2) |28. října 2016 |30. května 2020 |
| [1.9.1](#1.9.1) |26. října 2016 |30. května 2020 |
| [1.9.0](#1.9.0) |03. října 2016 |30. května 2020 |
| [1.8.1](#1.8.1) |30. června 2016 |30. května 2020 |
| [1.8.0](#1.8.0) |14. června 2016 |30. května 2020 |
| [1.7.1](#1.7.1) |30. dubna 2016 |30. května 2020 |
| [1.7.0](#1.7.0) |27. dubna 2016 |30. května 2020 |
| [1.6.0](#1.6.0) |29. března 2016 |30. května 2020 |
| [1.5.1](#1.5.1) |31. prosince 2015 |30. května 2020 |
| [1.5.0](#1.5.0) |4. prosince 2015 |30. května 2020 |
| [1.4.0](#1.4.0) |05. října 2015 |30. května 2020 |
| [1.3.0](#1.3.0) |05. října 2015 |30. května 2020 |
| [1.2.0](#1.2.0) |05. srpna 2015 |30. května 2020 |
| [1.1.0](#1.1.0) |9. července 2015 |30. května 2020 |
| 1.0.1 |12. května 2015 |30. května 2020 |
| [1.0.0](#1.0.0) |7. dubna 2015 |30. května 2020 |
| 0.9.5 – zapůjčení |9. března 2015 |29. února 2016 |
| 0.9.4 – zapůjčení |17. února 2015 |29. února 2016 |
| 0.9.3 – zapůjčení |13. ledna 2015 |29. února 2016 |
| 0.9.2 – zapůjčení |19. prosince 2014 |29. února 2016 |
| 0.9.1 – zapůjčení |19. prosince 2014 |29. února 2016 |
| 0.9.0 – zapůjčení |10. prosince 2014 |29. února 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).