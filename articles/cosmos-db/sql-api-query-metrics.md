---
title: Metriky dotazů SQL pro rozhraní SQL API Azure Cosmos DB
description: Přečtěte si, jak instrumentovat a ladit výkon dotazů SQL požadavků Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614815"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Ladění výkonu dotazů pomocí služby Azure Cosmos DB

Azure Cosmos DB poskytuje [rozhraní SQL API pro dotazování dat](how-to-sql-query.md), bez nutnosti schématu nebo sekundární indexy. Tento článek obsahuje následující informace pro vývojáře:

* Podrobné informace o tom, jak funguje provádění dotazů SQL služby Azure Cosmos DB
* Podrobnosti o požadavcích na dotaz a hlavičkách odpovědí a možnostech sady SDK klienta
* Tipy a doporučené postupy pro výkon dotazů
* Příklady využití statistiky spuštění SQL k ladění výkonu dotazu

## <a name="about-sql-query-execution"></a>O spuštění dotazu SQL

V Azure Cosmos DB ukládáte data do kontejnerů, které se můžou zvětšit na libovolnou [velikost úložiště nebo propustnost požadavků](partition-data.md). Azure Cosmos DB bezproblémově škáluje data napříč fyzickými oddíly pod kryty pro zpracování růstu dat nebo zvýšení zřízené propustnosti. Dotazy SQL můžete vydávat do libovolného kontejneru pomocí rozhraní REST API nebo jedné z podporovaných [sad SQL SDK](sql-api-sdk-dotnet.md).

Stručný přehled dělení: definujete klíč oddílu jako "město", který určuje, jak jsou data rozdělena mezi fyzické oddíly. Data patřící do jednoho klíče oddílu (například "město" == "Seattle") jsou uložena ve fyzickém oddílu, ale obvykle jeden fyzický oddíl má více klíčů oddílu. Když oddíl dosáhne velikosti úložiště, služba bez problémů rozdělí oddíl na dva nové oddíly a rozdělí klíč oddílu rovnoměrně mezi tyto oddíly. Vzhledem k tomu, že oddíly jsou přechodné, použití abstrakce "rozsah klíčů oddílu", který označuje rozsahy hashe klíče oddílu. 

Při vydání dotazu do Azure Cosmos DB, Sada SDK provádí tyto logické kroky:

* Analyzovat dotaz SQL k určení plánu spuštění dotazu. 
* Pokud dotaz obsahuje filtr proti klíči `SELECT * FROM c WHERE c.city = "Seattle"`oddílu, jako je , je směrován do jednoho oddílu. Pokud dotaz nemá filtr na klíč oddílu, pak je spuštěn ve všech oddílech a výsledky jsou sloučeny na straně klienta.
* Dotaz je spuštěn v rámci každého oddílu v řadě nebo paralelní, na základě konfigurace klienta. V rámci každého oddílu dotazu může provést jednu nebo více zpátečních cest v závislosti na složitosti dotazu, nakonfigurované velikosti stránky a zřízené propustnosti kolekce. Každé spuštění vrátí počet [jednotek požadavku](request-units.md) spotřebovaných spuštění dotazu a volitelně statistiky provádění dotazu. 
* Sada SDK provádí souhrn výsledků dotazu napříč oddíly. Například pokud dotaz zahrnuje ORDER BY napříč oddíly, pak výsledky z jednotlivých oddílů jsou seřazeny sloučení vrátit výsledky v globálně seřazené pořadí. Pokud je dotaz agregace `COUNT`jako , počty z jednotlivých oddílů jsou sečteny k vytvoření celkový počet.

Sady SDK poskytují různé možnosti pro spuštění dotazu. Například v rozhraní .NET jsou `FeedOptions` tyto možnosti k dispozici ve třídě. Následující tabulka popisuje tyto možnosti a jejich vliv na čas spuštění dotazu. 

| Možnost | Popis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musí být nastavena na hodnotu true pro všechny dotazy, které vyžaduje spuštění přes více než jeden oddíl. Toto je explicitní příznak, který umožňuje provádět kompromisy vědomé výkonu během vývoje. |
| `EnableScanInQuery` | Musí být nastavena na hodnotu true, pokud jste se odhlásili z indexování, ale přesto chcete spustit dotaz prostřednictvím skenování. Pouze platí, pokud je zakázánindexování pro požadovanou cestu filtru. | 
| `MaxItemCount` | Maximální počet položek, které mají být vráceny za zpáteční cestu na server. Nastavením na -1 můžete nechat server spravovat počet položek. Nebo můžete snížit tuto hodnotu načíst pouze malý počet položek za zpáteční cestu. 
| `MaxBufferedItemCount` | Toto je možnost na straně klienta a slouží k omezení spotřeby paměti při provádění příkazu pro více oddílů OB BY. Vyšší hodnota pomáhá snížit latenci řazení mezi oddíly. |
| `MaxDegreeOfParallelism` | Získá nebo nastaví počet souběžných operací spustit straně klienta během paralelního provádění dotazů v databázové službě Azure Cosmos. Kladná hodnota vlastnosti omezuje počet souběžných operací na nastavenou hodnotu. Pokud je nastavena na méně než 0, systém automaticky rozhodne o počtu souběžných operací, které mají být spuštěny. |
| `PopulateQueryMetrics` | Umožňuje podrobné protokolování statistik času stráveného v různých fázích provádění dotazu, jako je čas kompilace, čas smyčky indexu a doba načítání dokumentu. Výstup ze statistik dotazů můžete sdílet pomocí podpory Azure a diagnostikovat problémy s výkonem dotazů. |
| `RequestContinuation` | Spuštění dotazu můžete obnovit předáním neprůhledného tokenu pokračování vráceného libovolným dotazem. Token pokračování zapouzdřuje veškerý stav potřebný pro spuštění dotazu. |
| `ResponseContinuationTokenLimitInKb` | Můžete omezit maximální velikost tokenu pokračování vráceného serverem. Možná budete muset nastavit, pokud váš hostitel aplikace má omezení na velikost hlavičky odpovědi. Nastavení tohoto může zvýšit celkovou dobu trvání a ru spotřebované pro dotaz.  |

Například vezměme příklad dotazu na klíč oddílu `/city` požadované na kolekci s jako klíč oddílu a zřízena s 100 000 RU/s propustnost. Chcete-li tento `CreateDocumentQuery<T>` dotaz pomocí v rozhraní .NET, jako je následující:

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

Fragment sady SDK uvedený výše odpovídá následujícímu požadavku rozhraní REST API:

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

Každá stránka spuštění dotazu odpovídá `POST` rozhraní `Accept: application/query+json` REST API s hlavičkou a dotaz SQL v těle. Každý dotaz provede jednu nebo více `x-ms-continuation` zpátečních cest na server s tokenem, který se odráží mezi klientem a serverem, aby bylo pokračovat v provádění. Možnosti konfigurace v FeedOptions jsou předány serveru ve formě hlavičky požadavku. Například `MaxItemCount` odpovídá `x-ms-max-item-count`. 

Požadavek vrátí následující (zkrácenou pro čitelnost) odpověď:

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

Záhlaví klíčových odpovědí vrácená z dotazu zahrnují následující:

| Možnost | Popis |
| ------ | ----------- |
| `x-ms-item-count` | Počet položek vrácených v odpovědi. To závisí na `x-ms-max-item-count`dodané , počet položek, které mohou být vejde do maximální velikost datové části odpovědi, zřízená propustnost a čas spuštění dotazu. |  
| `x-ms-continuation:` | Token pokračování pro obnovení provádění dotazu, pokud jsou k dispozici další výsledky. | 
| `x-ms-documentdb-query-metrics` | Statistiky dotazu pro spuštění. Toto je oddělený řetězec obsahující statistiky času stráveného v různých fázích provádění dotazu. Vráceno, `x-ms-documentdb-populatequerymetrics` pokud `True`je nastavena na . | 
| `x-ms-request-charge` | Počet [jednotek požadavku](request-units.md) spotřebovaných dotazem. | 

Podrobnosti o hlavičkách a možnostech požadavku rozhraní REST API naleznete [v tématu Dotazování prostředků pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Doporučené postupy pro výkon dotazů
Následují nejběžnější faktory, které ovlivňují výkon dotazů Azure Cosmos DB. Hlouběji se podíváme do každého z těchto témat v tomto článku.

| Faktor | Tip | 
| ------ | -----| 
| Zřízená propustnost | Změřte RU na dotaz a ujistěte se, že máte požadovanou zřízenou propustnost pro vaše dotazy. | 
| Rozdělení a rozdělení klíčů | Upřednostnit dotazy s hodnotou klíče oddílu v klauzuli filtru pro nízkou latenci. |
| Možnosti sady SDK a dotazu | Postupujte podle osvědčených postupů sady SDK, jako je přímé připojení, a vylaďte možnosti spuštění dotazu na straně klienta. |
| Latence sítě | Účet pro zatížení sítě v měření a použití více-naváděcí API ke čtení z nejbližší oblasti. |
| Zásady indexování | Ujistěte se, že máte požadované cesty indexování nebo zásady pro dotaz. |
| Metriky spuštění dotazu | Analyzujte metriky spuštění dotazu k identifikaci potenciálních přepsání obrazců dotazů a dat.  |

### <a name="provisioned-throughput"></a>Zřízená propustnost
V Cosmos DB vytvoříte kontejnery dat, každý s vyhrazenou propustností vyjádřenou v jednotkách požadavku (RU) za sekundu. Čtení dokumentu 1 KB je 1 RU a každá operace (včetně dotazů) je normalizována na pevný počet ru na základě jeho složitosti. Například pokud máte 1000 RU/s zřízena pro váš kontejner `SELECT * FROM c WHERE c.city = 'Seattle'` a máte dotaz, jako je to spotřebovává 5 RU, pak můžete provést (1000 RU/s) / (5 RU/dotaz) = 200 dotazů/s takové dotazy za sekundu. 

Pokud odešlete více než 200 dotazů za sekundu, služba spustí příchozí požadavky omezující rychlost nad 200/s. Sady SDK automaticky zpracovat tento případ provedením backoff/opakovat, proto můžete zaznamenat vyšší latence pro tyto dotazy. Zvýšení zřízené propustnosti na požadovanou hodnotu zlepšuje latenci a propustnost dotazu. 

Další informace o jednotkách požadavků naleznete v [tématu Request units](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Rozdělení a rozdělení klíčů
S Azure Cosmos DB obvykle dotazy provádět v následujícím pořadí od nejrychlejší/nejefektivnější na pomalejší/méně efektivní. 

* ZÍSKAT na jeden klíč oddílu a klíč položky
* Dotaz s klauzulí filtru na jednom klíči oddílu
* Dotaz bez klauzule filtru rovnosti nebo rozsahu na libovolné vlastnosti
* Dotaz bez filtrů

Dotazy, které je třeba konzultovat všechny oddíly vyžadují vyšší latenci a může využívat vyšší ru. Vzhledem k tomu, že každý oddíl má automatické indexování proti všechny vlastnosti, dotaz může být obsluhován efektivně z indexu v tomto případě. Můžete provádět dotazy, které pokrývají oddíly rychleji pomocí možností paralelismu.

Další informace o dělení a klíče oddílů najdete [v tématu dělení v Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Možnosti sady SDK a dotazu
Informace [o tom,](performance-tips.md) jak získat nejlepší výkon na straně klienta z Azure Cosmos DB, najdete v tématu Tipy pro výkon a [testování výkonu.](performance-testing.md) To zahrnuje použití nejnovějších sad SDK, konfiguraci konfigurace specifických pro platformu, jako je výchozí počet připojení, frekvence uvolňování paměti a použití zjednodušených možností připojení, jako je Direct/TCP. 


#### <a name="max-item-count"></a>Maximální počet položek
Pro dotazy hodnota `MaxItemCount` může mít významný dopad na čas dotazu end-to-end. Každá zpáteční cesta na server vrátí maximálně počet `MaxItemCount` položek v (Výchozí 100 položek). Nastavení na vyšší hodnotu (-1 je maximální a doporučeno) zlepší dobu trvání dotazu celkově omezením počtu zpátečních cest mezi serverem a klientem, zejména pro dotazy s velkými sadami výsledků.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximální stupeň paralelismu
Pro dotazy, naladit `MaxDegreeOfParallelism` k identifikaci nejlepší konfigurace pro vaši aplikaci, zejména v případě, že provádíte dotazy mezi oddíly (bez filtru na hodnotu klíče oddílu). `MaxDegreeOfParallelism`řídí maximální počet paralelních úloh, tj. 

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
* D = výchozí maximální počet paralelních úloh (= celkový počet procesorů v klientském počítači)
* P = Maximální počet paralelních úloh zadaný uživatelem
* N = Počet oddílů, které je třeba navštívit pro zodpovězení dotazu

Níže jsou uvedeny důsledky toho, jak paralelní dotazy by se chovat pro různé hodnoty P.
* (P == 0) => sériový režim
* (P == 1) => Maximálně jeden úkol
* (P > 1) => paralelní úlohy min min (P, N) 
* (P < 1) = paralelní úlohy> min.n., d.

Poznámky k verzi sady SDK a podrobnosti o implementovaných třídách a metodách naleznete [v tématu SQL SDKs](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Latence sítě
Informace o tom, jak nastavit globální distribuci a jak se připojit k nejbližší oblasti, najdete v [tématu globální distribuce Azure Cosmos DB.](tutorial-global-distribution-sql-api.md) Latence sítě má významný vliv na výkon dotazu, pokud potřebujete provést více round-trips nebo načíst velkou sadu výsledků z dotazu. 

Část metriky spuštění dotazu vysvětluje, jak načíst čas `totalExecutionTimeInMs`spuštění serveru dotazů ( ), takže můžete rozlišovat mezi časem stráveným při provádění dotazů a časem stráveným v síťovém přenosu.

### <a name="indexing-policy"></a>Zásady indexování
Viz [Konfigurace zásad indexování](index-policy.md) pro indexování cest, druhů a režimů a jejich vliv na provádění dotazů. Ve výchozím nastavení používá zásada indexování hash indexování pro řetězce, což je efektivní pro dotazy rovnosti, ale ne pro dotazy rozsahu/pořadí podle dotazů. Pokud potřebujete dotazy na rozsah pro řetězce, doporučujeme zadat typ indexu Range pro všechny řetězce. 

Ve výchozím nastavení azure cosmos DB použije automatické indexování pro všechna data. Pro scénáře vložení s vysokým výkonem zvažte vyloučení cest, protože to sníží náklady na ŽP pro každou operaci vložení. 

## <a name="query-execution-metrics"></a>Metriky spuštění dotazu
Podrobné metriky při provádění dotazů můžete `x-ms-documentdb-populatequerymetrics` získat`FeedOptions.PopulateQueryMetrics` předáním volitelné hlavičky ( v sdk .NET. Hodnota vrácená `x-ms-documentdb-query-metrics` v má následující dvojice klíč hodnota určená pro pokročilé řešení potíží s prováděním dotazu. 

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
| `totalExecutionTimeInMs` | milisekundy | Čas spuštění dotazu | 
| `queryCompileTimeInMs` | milisekundy | Čas kompilace dotazu  | 
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Čas sestavení plánu logických dotazů | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Čas na sestavení plánu fyzického dotazu | 
| `queryOptimizationTimeInMs` | milisekundy | Čas strávený optimalizací dotazu | 
| `VMExecutionTimeInMs` | milisekundy | Čas strávený v době běhu dotazu | 
| `indexLookupTimeInMs` | milisekundy | Čas strávený ve vrstvě fyzického indexu | 
| `documentLoadTimeInMs` | milisekundy | Čas strávený načítáním dokumentů  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený prováděním funkcí systému (vestavěného) v milisekundách  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený prováděním uživatelem definovaných funkcí v milisekundách | 
| `retrievedDocumentCount` | count | Celkový počet načtených dokumentů  | 
| `retrievedDocumentSize` | Bajtů | Celková velikost načtených dokumentů v bajtech  | 
| `outputDocumentCount` | count | Počet výstupních dokumentů | 
| `writeOutputTimeInMs` | milisekundy | Čas spuštění dotazu v milisekundách | 
| `indexUtilizationRatio` | poměr (<=1) | Poměr počtu dokumentů spárovaných filtrem k počtu načtených dokumentů  | 

Sady SDK klienta může interně provádět více operací dotazu k zobrazení dotazu v rámci každého oddílu. Klient provede více než jedno volání na oddíl, pokud celkové výsledky překročí `x-ms-max-item-count`, pokud dotaz překročí zřízenou propustnost pro oddíl, nebo pokud datová část dotazu dosáhne maximální velikosti na stránku, nebo pokud dotaz dosáhne limitu přiděleného systému. Každé částečné spuštění `x-ms-documentdb-query-metrics` dotazu vrátí pro tuto stránku. 

Zde jsou některé ukázkové dotazy a jak interpretovat některé metriky vrácené z provádění dotazu: 

| Dotaz | Ukázková metrika | Popis | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Počet načtených dokumentů je 100+1 tak, aby odpovídal klauzuli TOP. Čas dotazu se `WriteOutputTime` `DocumentLoadTime` většinou tráví a protože se jedná o skenování. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount je nyní vyšší (500+1 tak, aby odpovídalo klauzuli TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Přibližně 0,9 ms je vynaloženo v IndexLookupTime pro vyhledávání klíčů, `/N/?`protože je to index vyhledávání na . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | O něco více času (1,7 ms) strávil v IndexLookupTime přes rozsah skenování, `/N/?`protože je to index vyhledávání na . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Stejný čas `DocumentLoadTime` strávený na jako předchozí `WriteOutputTime` dotazy, ale nižší, protože jsme promítání pouze jednu vlastnost. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Přibližně 213 ms `UserDefinedFunctionExecutionTime` je vynaloženo na provádění `c.N`UDF na každou hodnotu . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Přibližně 0,6 ms `IndexLookupTime` je `/Name/?`vynaloženo na . Většinu času spuštění dotazu (~7 `SystemFunctionExecutionTime`ms) v aplikaci . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Dotaz se provádí jako prohledávač, protože používá `LOWER`a 500 z 2491 načtených dokumentů jsou vráceny. |


## <a name="next-steps"></a>Další kroky
* Informace o podporovaných operátorech dotazů SQL a klíčových slovech naleznete v [tématu DOTAZ SQL](sql-query-getting-started.md). 
* Další informace o jednotkách požadavků naleznete v [tématu jednotky požadavků](request-units.md).
* Další informace o zásadách indexování naleznete v [tématu zásad indexování](index-policy.md) 


