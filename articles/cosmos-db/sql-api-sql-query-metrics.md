---
title: Metriky dotazů SQL pro rozhraní API pro Azure Cosmos databáze SQL | Microsoft Docs
description: Další informace o tom, jak instrumentace a ladění výkon dotazů SQL Azure Cosmos DB požadavků.
keywords: syntaxe SQL, dotaz sql, sql dotazy, json dotazovací jazyk, databázových koncepcí a sql, agregační funkce
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: b2fa8e8f-7291-45a3-9bd1-7284ed9077f8
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: 00e50c3b2f4dc50e43aac03b162bc637f0821656
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Ladění výkonu dotazů s Azure Cosmos DB

Poskytuje Azure Cosmos DB [SQL rozhraní API pro dotazování na data](sql-api-sql-query.md), aniž byste museli schématu nebo sekundární indexy. Tento článek obsahuje následující informace pro vývojáře:

* Nejdůležitější podrobnosti o způsobu fungování spuštění dotazu SQL Azure Cosmos DB
* Informace o hlavičkách žádostí a odpovědí dotazu a možnosti klienta SDK
* Tipy a osvědčené postupy pro výkon dotazů
* Příklady, jak využívat statistik provádění SQL k ladění výkonu dotazů

## <a name="about-sql-query-execution"></a>O spuštění dotazu SQL

V Azure Cosmos DB, ukládat data do kontejnerů, které můžete dosáhnout žádné [velikost nebo žádostí o propustnost úložiště](partition-data.md). Azure Cosmos DB bezproblémově škáluje data mezi fyzické oddíly skrytě zpracovat nárůst dat nebo zvyšte v zřízené propustnosti. Můžete použít dotazy SQL pro každý kontejner pomocí rozhraní REST API nebo jeden z podporovaném [sady SDK SQL](sql-api-sdk-dotnet.md).

Stručný přehled vytváření oddílů: definování klíč oddílu, jako je "city", která určuje, jak je rozdělit data do fyzického oddíly. Data patřící do jednoho oddílu klíč (například "city" == "Seattle") je uložen v rámci fyzické oddílu, ale obvykle jednoho oddílu fyzického má více klíčů oddílů. V případě oddíl dosáhne velikosti úložiště, služba bezproblémově rozdělí oddílu na dva nové oddíly a se rovnoměrně rozděluje klíč oddílu mezi tyto oddíly. Vzhledem k tomu, že oddíly jsou přechodné, pomocí rozhraní API abstrakci "oddílu klíče rozsahu", který označuje rozsahy hodnoty hash klíče oddílu. 

Pokud vydáte dotaz do databáze Cosmos Azure, SDK provádí tyto logických kroků:

* Analyzovat dotaz SQL k určení plán spuštění dotazu. 
* Pokud dotaz obsahuje filtr proti klíč oddílu, jako například `SELECT * FROM c WHERE c.city = "Seattle"`, se směruje na jeden oddíl. Pokud dotaz nemá filtr na klíč oddílu, pak se spustí v všechny oddíly a výsledky sloučení na straně klienta.
* Dotaz je provést v rámci každý oddíl v řadě nebo paralelní, v závislosti na konfiguraci klienta. V rámci každého oddílu dotaz může si ho nebo další zpátečních cest v závislosti na složitosti dotazu nakonfigurovaná velikost stránky a zřízené propustnosti kolekce. Každé spuštění vrátí počet [požadované jednotky](request-units.md) spotřebované spuštění dotazu a volitelně statistik provádění dotazů. 
* Sada SDK provede shrnutí výsledků dotazu napříč oddíly. Například pokud dotaz zahrnuje ORDER BY napříč oddíly, pak výsledky z jednotlivých oddílů jsou seřazeny sloučení vracet výsledky na globálně seřazené pořadí. Pokud je dotaz agregace jako `COUNT`, počty z jednotlivých oddílů jsou sečteny k vytvoření celkového počtu.

Sady SDK poskytují různé možnosti pro spuštění dotazu. Například v rozhraní .NET tyto možnosti jsou dostupné v `FeedOptions` třídy. Následující tabulka popisuje tyto možnosti a jak budou mít vliv doba provádění dotazu. 

| Možnost | Popis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musí být nastavena na hodnotu true pro žádný dotaz, který vyžaduje, aby provést napříč více než jeden oddíl. Jedná se explicitní příznak, který vám umožní provádět kompromisy vědomá toho výkonu během doby vývoje. |
| `EnableScanInQuery` | Musí být nastavena na hodnotu true, pokud jste zvolili mimo indexování, ale chcete přesto spustit dotaz prostřednictvím kontrolu. Pouze použito pouze v případě indexování pro cestu požadovaný filtr je zakázána. | 
| `MaxItemCount` | Maximální počet položek k vrácení za dobu odezvy na server. Nastavení na hodnotu -1, můžete je nechat server spravovat počet položek. Nebo můžete snížit tuto hodnotu načíst pouze malý počet položek na dobu odezvy. 
| `MaxBufferedItemCount` | Toto je možnost na straně klienta a používá k omezení využití paměti při provádění cross-partition ORDER BY. Vyšší hodnota pomáhá snížit latenci mezi oddílu řazení. |
| `MaxDegreeOfParallelism` | Získá nebo nastaví počet souběžných operací spustit na straně klienta během provádění paralelního dotazu ve službě Azure Cosmos DB databáze. Hodnotu vlastnosti kladné omezuje počet souběžných operací nastavte hodnotu. Pokud je nastavena na hodnotu menší než 0, systém automaticky rozhoduje, počet souběžných operací ke spuštění. |
| `PopulateQueryMetrics` | Doba načítání umožní podrobné protokolování statistiky času stráveného v různých fázích provádění dotazu jako čas kompilace, index smyčky čas a dokumentu. Výstup z Statistika dotazu můžete sdílet s podporu Azure o diagnostice problémů s výkonem dotazu. |
| `RequestContinuation` | Provádění dotazů můžete obnovit pomocí předávání neprůhledné pokračovací token vrácený jakýkoli dotaz. Token pro pokračování zapouzdří všechny stavy, které jsou potřebné pro spuštění dotazu. |
| `ResponseContinuationTokenLimitInKb` | Můžete omezit maximální velikost token pro pokračování vrácená serverem. Možná budete muset nastavit Pokud hostitele vaší aplikace má omezení velikosti hlavičky odpovědi. Toto nastavení může zvýšit celkový doba trvání a RUs využité pro dotaz.  |

Například příklad dotazu podívejme na požadovanou na kolekci s klíčem oddílu `/city` jako oddíl klíče a s propustností 100 000 RU/s přiděleným. Požádáte o dotaz pomocí `CreateDocumentQuery<T>` v rozhraní .NET takto:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Fragmentu SDK uvedené výše, odpovídá následující požadavku REST API:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Každé stránce provádění dotazu odpovídá rozhraní REST API `POST` s `Accept: application/query+json` záhlaví a dotazu SQL v těle. Každý dotaz je jeden nebo více cest k serveru se zaokrouhlí `x-ms-continuation` token opakována mezi klientem a serverem, chcete-li pokračovat v provádění. Možnosti konfigurace v FeedOptions jsou předány na server ve formě hlavičky žádosti. Například `MaxItemCount` odpovídá `x-ms-max-item-count`. 

Požadavek vrátí odpověď na následující (zkrácená čitelnější):

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

Hlavičky odpovědi klíče vrácená z dotazu zahrnují následující:

| Možnost | Popis |
| ------ | ----------- |
| `x-ms-item-count` | Počet položek, které vrácený v odpovědi. Toto je závislá na zadaných `x-ms-max-item-count`, počet položek, které můžete přizpůsobit velikost datové části maximální odpovědi, zřízené propustnosti a doba provádění dotazu. |  
| `x-ms-continuation:` | Pokračovací token, který má-li pokračovat v provádění dotazu, pokud jsou k dispozici další výsledky. | 
| `x-ms-documentdb-query-metrics` | Statistika dotazu pro spuštění. Toto je oddělený řetězec obsahující statistiky času stráveného v různých fázích spuštění dotazu. Vrácené v případě `x-ms-documentdb-populatequerymetrics` je nastaven na `True`. | 
| `x-ms-request-charge` | Počet [požadované jednotky](request-units.md) spotřebovávají dotazu. | 

Podrobnosti o hlavičky požadavku REST API a možnostech najdete v tématu [dotaz na prostředky, pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Osvědčené postupy pro výkon dotazů
Níže jsou většiny běžných faktorů, které mít vliv na výkon dotazů Azure Cosmos DB. Jsme podrobněji prozkoumat každý z těchto témat v tomto článku.

| Koeficient | Tip | 
| ------ | -----| 
| Zřízená propustnost | Měření RU na jeden dotaz a ujistěte se, že máte požadované zřízené propustnosti pro své dotazy. | 
| Dělení a klíče oddílů | Upřednostnit dotazů s hodnotou klíče oddílu v klauzuli filtru pro s nízkou latencí. |
| Možnosti sady SDK a dotazů | Doporučené postupy SDK jako přímé připojení a ladit možnosti provedení dotazu na straně klienta. |
| Latence sítě | Účet pro síť režie v měření a použít více funkci rozhraní API ke čtení z nejbližší oblast. |
| Zásada indexování | Ujistěte se, že máte požadované indexování cesty nebo zásady pro dotaz. |
| Metriky spuštění dotazu | Analyzujte metriky provádění dotazu identifikovat potenciální přepisů dotaz a datové obrazce.  |

### <a name="provisioned-throughput"></a>Zřízená propustnost
V systému Cosmos databáze můžete vytvořit kontejnery dat, každý s vyhrazenou propustností vyjádřené v žádosti o jednotkách (RU) za sekundu. Čtení 1 KB dokumentu je 1 RU a každé operace (včetně dotazů) je normalizovány na pevný počet RUs podle jeho složitost. Například pokud máte 1000 zřízení RU/s pro váš kontejner, a máte dotaz jako `SELECT * FROM c WHERE c.city = 'Seattle'` , který využívá 5 RUs a potom můžete provést (1000 RU/s) / (5 RU/dotazu) = 200 dotazu nebo s takové dotazy za sekundu. 

Pokud odešlete více než 200 dotazů za sekundu, spustí službu omezení rychlosti příchozí požadavky nad 200/s. Sady SDK automaticky zpracovávat tento případ provedením omezení rychlosti nebo opakování, proto možná jste si všimli vyšší latence pro tyto dotazy. Zvýšení zřízené propustnosti na požadovaná hodnota zlepšuje dotazu latence a propustnosti. 

Další informace o jednotkách žádosti najdete v tématu [požadované jednotky](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Dělení a klíče oddílů
S Azure DB Cosmos obvykle dotazy provádět v uvedeném pořadí z nejrychlejší nebo většinu efektivní pomalejší nebo méně efektivní. 

* ZÍSKAT klíč jednoho oddílu a klíč položky
* Dotaz s klauzulí filtru pro jeden oddíl klíč
* Dotazování bez klauzule filtru rovnosti nebo rozsah na žádnou vlastnost
* Dotazování bez filtry

Dotazy, které potřebují najdete všechny oddíly potřebovat zhorší latenci a vyšší RUs spotřebovat. Vzhledem k tomu, že každý oddíl má automatické indexování pro všechny vlastnosti, dotaz nelze zpracovat efektivně od indexu v tomto případě. Můžete provést dotazy, které jsou rozmístěny oddíly rychlejší s použitím možností paralelismus.

Další informace o vytváření oddílů a klíče oddílů, najdete v části [vytváření oddílů v Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Možnosti sady SDK a dotazů
V tématu [tipy pro zvýšení výkonu](performance-tips.md) a [testování výkonu](performance-testing.md) pro získání nejlepší výkon klienta z Azure Cosmos DB. To zahrnuje pomocí nejnovější SDK, konfigurace specifických pro platformy konfigurace jako výchozí počet připojení, frekvenci uvolňování paměti a pomocí možnosti lightweight připojení jako přímé/TCP. 


#### <a name="max-item-count"></a>Počet položek. maximální počet
Pro dotazy, hodnota `MaxItemCount` může mít významný dopad na dobu začátku do konce dotazu. Vrátí maximálně počet položek v každé výměně zpráv serveru `MaxItemCount` (výchozí 100 položek). Toto nastavení na vyšší hodnotu (-1 je maximální a doporučené), který umožní zvýšení celkové doby trvání dotaz tak, že omezí počet zpátečních cest mezi serverem a klientem, zejména pro dotazy s velké množství výsledků.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximální počet stupně paralelního zpracování
Pro dotazy, ladit `MaxDegreeOfParallelism` k identifikaci doporučené konfigurace pro aplikace, zejména v případě, že můžete provádět dotazy cross-partition (bez filtru na základě hodnoty klíč oddílu). `MaxDegreeOfParallelism`  Určuje maximální počet paralelních úkolů, například maximální počet oddílů návštěvy paralelně. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Předpokládejme, že
* D = výchozí maximální počet paralelních úloh (= celkový počet procesorů v klientském počítači.)
* P = zadán uživatel maximální počet paralelních úloh
* N = počet oddílů, které potřebuje návštěvy odpovědí na dotazy

Toto jsou důsledky chování paralelní dotazy pro různé hodnoty P.
* (P == 0) = > sériové režimu
* (P == 1) = > maximálně jeden úkol
* (P > 1) = > Min (P, N) paralelní úlohy 
* (P < 1) = > Min (N, D) paralelní úlohy

Poznámky k verzi sady SDK a najdete v části Podrobnosti o implementované třídy a metody [SQL SDK](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Latence sítě
V tématu [globální distribuční databázi Cosmos Azure](tutorial-global-distribution-sql-api.md) jak nastavit globální distribuční a připojte se k nejbližší oblast. Latence sítě nemá významný dopad na výkon dotazů, když potřebujete udělat vícenásobný nebo načíst velké výslednou sadu z dotazu. 

V části o metrikách provádění dotazu vysvětluje, jak načíst server čas provádění dotazů ( `totalExecutionTimeInMs`), takže můžete rozlišit mezi času stráveného při provádění dotazu a čas strávený v síti přenosu.

### <a name="indexing-policy"></a>Zásady indexování
V tématu [Konfigurace zásady indexování](indexing-policies.md) pro indexování cesty, typy a režimy, a jak by se projevily při provádění dotazu. Ve výchozím nastavení, zásady indexování používá Hash indexování pro řetězce, který je efektivní dotazy na rovnost, ale ne pro rozsah dotazy nebo order by – dotazy. Pokud potřebujete dotazy na rozsah pro řetězce, doporučujeme určení index typu rozsah pro všechny řetězce. 

## <a name="query-execution-metrics"></a>Metriky spuštění dotazu
Můžete získat podrobné metriky pro spuštění dotazu předáním v nepovinný `x-ms-documentdb-populatequerymetrics` záhlaví (`FeedOptions.PopulateQueryMetrics` sady .NET SDK). Hodnota vrácená v `x-ms-documentdb-query-metrics` má následující páry klíč hodnota určená pro pokročilé řešení problémů s spuštění dotazu. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Metrika | Jednotka | Popis | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milisekundy | Doba provádění dotazu | 
| `queryCompileTimeInMs` | milisekundy | Doba kompilace dotazu  | 
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Čas vytvořit plán dotazu logické | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Čas vytvořit plán dotazu fyzické | 
| `queryOptimizationTimeInMs` | milisekundy | Čas strávený v optimalizaci dotazu | 
| `VMExecutionTimeInMs` | milisekundy | Čas strávený v dotazu runtime | 
| `indexLookupTimeInMs` | milisekundy | Čas strávený v indexu fyzické vrstvě | 
| `documentLoadTimeInMs` | milisekundy | Čas strávený v nahrávání dokumentů  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený provádění (Předdefinované) funkce systému v milisekundách  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený spouštění uživatelsky definované funkce v milisekundách | 
| `retrievedDocumentCount` | počet | Celkový počet načtených dokumentů  | 
| `retrievedDocumentSize` | bajtů | Celková velikost načtené dokumenty v bajtech  | 
| `outputDocumentCount` | počet | Počet výstupních dokumentů | 
| `writeOutputTimeInMs` | milisekundy | Doba provádění dotazu v milisekundách | 
| `indexUtilizationRatio` | poměr (< = 1) | Načíst poměr počtu dokumenty odpovídala filtr pro počet dokumentů  | 

Klientské sady SDK může být interně více operací dotazu k obsluze dotaz v rámci každý oddíl. Klient podá více než jedno volání oddílů pokud celkový počet výsledků překročí `x-ms-max-item-count`, pokud dotaz překračuje zřízené propustnosti pro oddíl, datové části dotazu dosáhne maximální velikosti na stránce, nebo pokud dotaz dosáhne systému přidělené časový limit. Každé spuštění částečné dotaz vrátí `x-ms-documentdb-query-metrics` pro tuto stránku. 

Tady jsou některé ukázkové dotazy a jak interpretovat některé z metriky vrácená při provádění dotazu: 

| Dotaz | Ukázka metrika | Popis | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Počet dokumentů, načíst je 100 + 1 tak, aby odpovídaly klauzule TOP. Doba dotazu je většinou věnovaný `WriteOutputTime` a `DocumentLoadTime` vzhledem k tomu, že je kontrolu. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount je nyní vyšší (500 + 1 tak, aby odpovídaly klauzule TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | O 0.9 ms je věnovaný IndexLookupTime pro vyhledávání klíčů, protože je indexu vyhledávání `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Něco delší dobu (1.7 ms) věnovaný IndexLookupTime přes rozsah kontroly, protože je indexu vyhledávání `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Stejný čas strávený `DocumentLoadTime` jako předchozí dotazy, ale nižší `WriteOutputTime` protože jsme se projekce pouze jednu vlastnost. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | O 213 ms je věnovaný `UserDefinedFunctionExecutionTime` provádění UDF na každou hodnotu `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Je věnovaný přibližně 0,6 ms `IndexLookupTime` na `/Name/?`. Většina doba provádění dotazu (~ 7 ms) v `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Dotaz se provádí jako kontrolu, protože používá `LOWER`, a jsou vráceny 500 mimo 2491 načtené dokumenty. |


## <a name="next-steps"></a>Další postup
* Další informace o podporovaných klíčová slova a operátory dotazu SQL najdete v tématu [dotazu SQL](sql-api-sql-query.md). 
* Další informace o jednotkách žádosti, najdete v části [požadované jednotky](request-units.md).
* Další informace o zásady indexování najdete v tématu [indexování zásad](indexing-policies.md) 


