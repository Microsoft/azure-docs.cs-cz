---
title: 'Azure Cosmos DB: ROZHRANÍ SQL Java API, sada SDK & prostředky'
description: Přečtěte si vše o rozhraní SQL Java API a sdk včetně dat vydání, dat vyřazení a změn provedených mezi jednotlivými verzemi sady Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: e57029e53365fbf99054e2d926296ccca3360663
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983598"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK pro ROZHRANÍ SQL API: Poznámky k vydání a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál o změně .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný vykonavatel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný vykonavatel - Java](sql-api-sdk-bulk-executor-java.md)

Sql API Java SDK podporuje synchronní operace. Pro asynchronní podporu použijte [sql api asynchronní Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**SDK ke stažení**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace java api](/java/api/com.microsoft.azure.documentdb)|
|**Přispívat do sady SDK**|[GitHubu](https://github.com/Azure/azure-documentdb-java/)|
|**Začínáme**|[Začínáme s sadou Java SDK](sql-api-java-get-started.md)|
|**Kurz webové aplikace**|[Vývoj webových aplikací s Azure Cosmos DB](sql-api-java-application.md)|
|**Minimální podporovaný modul runtime**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="247"></a><a name="2.4.7"/>2.4.7
* Opravuje problém s časovým limitem fondu připojení.
* Opravuje aktualizaci tokenu ověřování při interních opakováních.

### <a name="246"></a><a name="2.4.6"/>2.4.6
* Byla aktualizována správná značka zásad repliky na straně klienta v databáziAccount a provedla čtení konfigurace účtu databáze z mezipaměti.

### <a name="245"></a><a name="2.4.5"/>2.4.5
* Vyhnout se opakování na chybu rozsahu klíče neplatný oddíl, pokud uživatel poskytuje pkRangeId.

### <a name="244"></a><a name="2.4.4"/>2.4.4
* Optimalizovaná mezipaměť rozsahu klíčů oddílů se aktualizuje.
* Opravuje scénář, kdy sada SDK nebaví nápovědu rozdělení oddílu ze serveru a vede k nesprávné aktualizaci mezipamětí směrování na straně klienta.

### <a name="242"></a><a name="2.4.2"/>2.4.2
* Optimalizovaná mezipaměť kolekce se aktualizuje.

### <a name="241"></a><a name="2.4.1"/>2.4.1
* Přidána podpora pro načtení zprávy vnitřní výjimky z diagnostického řetězce požadavku.

### <a name="240"></a><a name="2.4.0"/>2.4.0
* Zavedena verze api na PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Přidána samostatná podpora časového období pro přímý režim.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Spotřebovává nulovou chybovou zprávu ze služby a vytváří výjimku klienta dokumentu.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Zlepšení připojení soketu, přidání SoKeepAlive výchozí true.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Přidána podpora řetězce diagnostiky požadavků.

### <a name="213"></a><a name="2.1.3"/>2.1.3
* Opravena chyba v PartitionKey pro Hash V2.

### <a name="212"></a><a name="2.1.2"/>2.1.2
* Přidána podpora pro složené indexy.
* Opravena chyba v globálním správci koncových bodů, aby se vynutila aktualizace.
* Opravena chyba pro upserts s pre-podmínky v přímém režimu.

### <a name="211"></a><a name="2.1.1"/>2.1.1
* Opravena chyba v mezipaměti adres brány.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Pro přímý režim byla přidána podpora zápisu ve více oblastech.
* Přidána podpora pro zpracování IOExceptions vyvolána jako ServiceUnavailable výjimky, z proxy serveru.
* Opravena zásada opakování zjišťování koncového bodu.
* Opravena chyba, která zajišťuje, že výjimky nulového ukazatele nejsou vyvolány v basedatabaseaccountconfigurationprovider.
* Opravena chyba, která zajišťuje, že QueryIterator nevrátí hodnoty null.
* Opravena chyba, která zajistila, že je povolen velký PartitionKey

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Pro režim brány byla přidána podpora zápisu ve více oblastech.

### <a name="1164"></a><a name="1.16.4"/>1.16.4
* Opravena chyba v rozsahu klíčů oddílu Pro čtení pro dotaz.

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* Opravena chyba při nastavování velikosti hlavičky tokenu pokračování v režimu DirectHttps.

### <a name="1162"></a><a name="1.16.2"/>1.16.2
* Přidána podpora převzetí služeb při selhání datového proudu.
* Byla přidána podpora vlastních metadat.
* Vylepšená logika zpracování relace.
* Opravena chyba v mezipaměti rozsahu klíčů oddílů.
* Opravena chyba NPE v přímém režimu.

### <a name="1161"></a><a name="1.16.1"/>1.16.1
* Přidána podpora pro jedinečný index.
* Přidána podpora pro omezení velikosti tokenu pokračování v možnostech informačního kanálu.
* Opravena chyba v Json Serializaci (timestamp).
* Opravena chyba v Json Serializaci (výčtu).
* Závislost na com.fasterxml.jackson.core:jackson-databind upgradována na 2.9.5.

### <a name="1160"></a><a name="1.16.0"/>1.16.0
* Vylepšené sdružování připojení pro přímý režim.
* Vylepšené zlepšení předběžného načtení pro dotaz s křížovými oddíly bez pořadí.
* Vylepšené generování UUID.
* Vylepšená logika konzistence relace.
* Přidána podpora pro multipolygon.
* Přidána podpora pro statistiku rozsahu klíčů oddílů pro sběr.
* Opravena chyba v podpoře více oblastí.

### <a name="1150"></a><a name="1.15.0"/>1.15.0
* Vylepšený výkon serializace Json.
* Tato verze sady SDK vyžaduje nejnovější verzi [emulátoru Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"/>1.14.0
* Interní změny pro knihovny přátel microsoftu.

### <a name="1130"></a><a name="1.13.0"/>1.13.0
* Opraven problém se čtením rozsahů klíčů s jedním oddílem.
* Byl opraven problém s analýzou ResourceID, který ovlivňuje databázi s krátkými názvy.
* Opraven problém způsobený kódováním klíče oddílů.

### <a name="1120"></a><a name="1.12.0"/>1.12.0
* Důležité opravy chyb pro vyžádání zpracování během rozdělení oddílů.
* Byl opraven problém s úrovněmi konzistence silné a ohraničené stálosti.

### <a name="1110"></a><a name="1.11.0"/>1.11.0
* Přidána podpora pro novou úroveň konzistence s názvem ConsistentPrefix.
* Opravena chyba ve čtení kolekce v režimu relace.

### <a name="1100"></a><a name="1.10.0"/>1.10.0
* Povolená podpora pro dělenou kolekci s tak nízkou jako 2,500 RU/s a škálování v krocích po 100 RU/s.
* Opravena chyba v nativním sestavení, která může v některých dotazech způsobit výjimku NullRef.

### <a name="196"></a><a name="1.9.6"/>1.9.6
* Opravena chyba v konfiguraci dotazovacího stroje, která může způsobit výjimky pro dotazy v režimu brány.
* Opraveno několik chyb v kontejneru relace, které mohou způsobit výjimku "Prostředek vlastníka nebyl nalezen" pro požadavky ihned po vytvoření kolekce.

### <a name="195"></a><a name="1.9.5"/>1.9.5
* Přidána podpora pro agregační dotazy (COUNT, MIN, MAX, SUMA a AVG). Viz [Podpora agregace](sql-query-aggregates.md).
* Přidána podpora pro kanál změn.
* Byla přidána podpora pro informace o kvótě kolekce prostřednictvím souboru RequestOptions.setPopulateQuotaInfo.
* Byla přidána podpora pro protokolování skriptů uložených procedur prostřednictvím souboru RequestOptions.setScriptLoggingEnabled.
* Opravena chyba, kdy dotaz v režimu DirectHttps mohl přestat reagovat při výskytu selhání omezení.
* Opravena chyba v režimu konzistence relace.
* Opravena chyba, která může způsobit NullReferenceException v HttpContext, když je míra požadavků vysoká.
* Zlepšený výkon režimu DirectHttps.

### <a name="194"></a><a name="1.9.4"/>1.9.4
* Přidána jednoduchá podpora proxy serveru založená na instanci klienta s rozhraním ConnectionPolicy.setProxy() API.
* Přidáno rozhraní DOCUMENTClient.close() API pro správné vypnutí instance DocumentClient.
* Zlepšený výkon dotazu v režimu přímého připojení odvozením plánu dotazů z nativního sestavení namísto brány.
* Nastavte FAIL_ON_UNKNOWN_PROPERTIES = false, takže uživatelé nemusí definovat JsonIgnoreProperties v jejich POJO.
* Refaktorované protokolování pro použití SLF4J.
* Opraveno několik dalších chyb ve čtečce konzistence.

### <a name="193"></a><a name="1.9.3"/>1.9.3
* Opravena chyba ve správě připojení, aby se zabránilo netěsnostem připojení v režimu přímého připojení.
* Opravena chyba v TOP dotazu, kde může vyvolat výjimku NullReference.
* Zlepšený výkon snížením počtu síťových volání pro interní mezipaměti.
* Byl přidán stavový kód, ActivityID a Identifikátor URI požadavku v dokumentu DocumentClientException pro lepší řešení potíží.

### <a name="192"></a><a name="1.9.2"/>1.9.2
* Opraven problém se správou připojení kvůli stabilitě.

### <a name="191"></a><a name="1.9.1"/>1.9.1
* Přidána podpora pro úroveň konzistence boundedstaleness.
* Přidána podpora pro přímé připojení pro operace CRUD pro dělené kolekce.
* Opravena chyba při dotazování na databázi pomocí SQL.
* Opravena chyba v mezipaměti relace, kde může být token relace nastaven nesprávně.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Přidána podpora paralelních dotazů mezi oddíly.
* Přidána podpora pro dotazy TOP/ORDER BY pro rozdělené kolekce.
* Přidána podpora pro silnou konzistenci.
* Přidána podpora pro požadavky založené na názvu při použití přímého připojení.
* Opraveno, aby activityId zůstat konzistentní ve všech žádostí opakování.
* Opravena chyba související s mezipamětí relace při opětovném vytváření kolekce se stejným názvem.
* Přidáno Polygon a LineString DataTypes při zadávání zásad indexování kolekce pro geografické oplocení prostorových dotazů.
* Opraveny problémy s Java Doc pro Javu 1.8.

### <a name="181"></a><a name="1.8.1"/>1.8.1
* Opravena chyba v PartitionKeyDefinitionMap pro ukládání do mezipaměti kolekce jednoho oddílu a neprovádět další požadavky na klíč načtení oddílu.
* Opravena chyba, která se nepokoušela opakovat, když je k dispozici nesprávná hodnota klíče oddílu.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Přidána podpora pro databázové účty s více oblastmi.
* Přidána podpora pro automatické opakování na omezené požadavky s možnostmi přizpůsobit maximální pokusy o opakování a maximální dobu opakování čekání.  Viz RetryOptions a ConnectionPolicy.getRetryOptions().
* Zastaralá kód oddílu IPartitionResolver založený na vlastním dělení. Pro vyšší úložiště a propustnost použijte rozdělené kolekce.

### <a name="171"></a><a name="1.7.1"/>1.7.1
* Přidána podpora zásad opakování pro omezení rychlosti.  

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Přidán čas na živou podporu (TTL) pro dokumenty.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Implementované [rozdělené kolekce](partition-data.md) a [uživatelem definované úrovně výkonu](performance-levels.md).

### <a name="151"></a><a name="1.5.1"/>1.5.1
* Opravena chyba v hashPartitionresolver generovat hodnoty hash v little-endian být konzistentní s ostatními sadami SDK.

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Přidejte překladače oddílů hash & rozsah, které vám pomohou s aplikacemi pro rozdělení napříč více oddíly.

### <a name="140"></a><a name="1.4.0"/>1.4.0
* Implementovat Upsert. Nové metody upsertXXX přidané pro podporu funkce Upsert.
* Implementujte směrování založené na ID. Žádné změny veřejného rozhraní API, všechny změny interní.

### <a name="130"></a><a name="1.3.0"/>1.3.0
* Uvolnění přeskočeno, aby se číslo verze zarovnání s ostatními sadami SDK

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Podporuje geoprostorový index
* Ověří vlastnost ID pro všechny prostředky. Id pro prostředky nemůže obsahovat ?, /, #, \, znaky nebo konec mezery.
* Přidá do položky ResourceResponse nové záhlaví "průběh transformace indexu".

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementuje zásady indexování V2

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Datum uvolnění a odchodu do důchodu
Společnost Microsoft bude poskytovat oznámení nejméně **12 měsíců** před vyřazením sady SDK za účelem hladkého přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace jsou přidány pouze do aktuální sady SDK, jako takové je doporučeno vždy upgradovat na nejnovější verzi sady SDK co nejdříve.

Jakýkoli požadavek na Cosmos DB pomocí vyřazené sady SDK bude službou odmítnut.

> [!WARNING]
> Všechny verze **1.x** sql sdk pro Javu budou vyřazeny **30.**
> 
>

> [!WARNING]
> **29. února 2016**byly vyřazeny všechny verze sady SQL SDK pro jazyk Java před verzí **1.0.0** .
> 
> 

<br/>

| Version | Datum vydání | Datum odchodu do důchodu |
| --- | --- | --- |
| [2.4.7](#2.4.7) |20. února 2020 |--- |
| [2.4.6](#2.4.6) |24. ledna 2020 |--- |
| [2.4.5](#2.4.5) |10. listopadu 2019 |--- |
| [2.4.4](#2.4.4) |24. října 2019 |--- |
| [2.4.2](#2.4.2) |Září 26, 2019 |--- |
| [2.4.1](#2.4.1) |18. července 2019 |--- |
| [2.4.0](#2.4.0) |Květen 04, 2019 |--- |
| [2.3.0](#2.3.0) |24. dubna 2019 |--- |
| [2.2.3](#2.2.3) |Duben 16, 2019 |--- |
| [2.2.2](#2.2.2) |Duben 05, 2019 |--- |
| [2.2.0](#2.2.0) |27. března 2019 |--- |
| [2.1.3](#2.1.3) |13. března 2019 |--- |
| [2.1.2](#2.1.2) |Března 09, 2019 |--- |
| [2.1.1](#2.1.1) |13. prosince 2018 |--- |
| [2.1.0](#2.1.0) |Lis 20, 2018 |--- |
| [2.0.0](#2.0.0) |21. září 2018 |--- |
| [1.16.4](#1.16.4) |10. září 2018 |30. května 2020 |
| [1.16.3](#1.16.3) |9. |30. května 2020 |
| [1.16.2](#1.16.2) |29. června 2018 |30. května 2020 |
| [1.16.1](#1.16.1) |16. května 2018May 16, 2018 |30. května 2020 |
| [1.16.0](#1.16.0) |15. března 2018 |30. května 2020 |
| [1.15.0](#1.15.0) |14. listopadu 2017(14. listopadu 2017) |30. května 2020 |
| [1.14.0](#1.14.0) |28. října 2017Oct 28, 2017 |30. května 2020 |
| [1.13.0](#1.13.0) |25. srpna 2017August 25, 2017 |30. května 2020 |
| [1.12.0](#1.12.0) |11. července 2017 |30. května 2020 |
| [1.11.0](#1.11.0) |10. května 2017 |30. května 2020 |
| [1.10.0](#1.10.0) |11. března 2017 March 11, 2017 |30. května 2020 |
| [1.9.6](#1.9.6) |21. února 2017 February 21, 2017 |30. května 2020 |
| [1.9.5](#1.9.5) |31. ledna 2017 January 31, 2017 |30. května 2020 |
| [1.9.4](#1.9.4) |24. listopadu 2016November 24, 2016 |30. května 2020 |
| [1.9.3](#1.9.3) |30. října 2016October 30, 2016 |30. května 2020 |
| [1.9.2](#1.9.2) |28. října 2016October 28, 2016 |30. května 2020 |
| [1.9.1](#1.9.1) |26. října 2016October 26, 2016 |30. května 2020 |
| [1.9.0](#1.9.0) |Říjen 03, 2016 |30. května 2020 |
| [1.8.1](#1.8.1) |30. června 2016 |30. května 2020 |
| [1.8.0](#1.8.0) |14. června 2016 |30. května 2020 |
| [1.7.1](#1.7.1) |30. dubna 2016April 30, 2016 |30. května 2020 |
| [1.7.0](#1.7.0) |27. dubna 2016April 27, 2016 |30. května 2020 |
| [1.6.0](#1.6.0) |29. března 2016March 29, 2016 |30. května 2020 |
| [1.5.1](#1.5.1) |31. prosince 2015 December 31, 2015 |30. května 2020 |
| [1.5.0](#1.5.0) |4. prosince 2015 December 04, 2015 |30. května 2020 |
| [1.4.0](#1.4.0) |Říjen 05, 2015 |30. května 2020 |
| [1.3.0](#1.3.0) |Říjen 05, 2015 |30. května 2020 |
| [1.2.0](#1.2.0) |Srpen 05, 2015 |30. května 2020 |
| [1.1.0](#1.1.0) |09. července 2015 July 09, 2015 |30. května 2020 |
| 1.0.1 |12. května 2015May 12, 2015 |30. května 2020 |
| [1.0.0](#1.0.0) |Duben 07, 2015 |30. května 2020 |
| 0.9.5-prelease |Března 09, 2015 |29. února 2016 February 29, 2016 |
| 0.9.4-prelease |17. února 2015 February 17, 2015 |29. února 2016 February 29, 2016 |
| 0.9.3-prelease |13. ledna 2015 January 13, 2015 |29. února 2016 February 29, 2016 |
| 0.9.2-prelease |19. prosince 2014 December 19, 2014 |29. února 2016 February 29, 2016 |
| 0.9.1-vydání |19. prosince 2014 December 19, 2014 |29. února 2016 February 29, 2016 |
| 0.9.0-prelease |10. prosince 2014December 10, 2014 |29. února 2016 February 29, 2016 |

## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Službě Cosmos DB najdete na stránce [služby Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

