---
title: Metriky SQL dotazu pro rozhraní SQL API služby Azure Cosmos DB
description: Další informace o tom, jak instrumentovat a ladění výkonu dotazů SQL žádostí o služby Azure Cosmos DB.
keywords: syntaxe SQL, příkaz jazyka sql, dotazů sql, json dotazovací jazyk, databázových koncepcí a dotazy sql, agregační funkce
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: 02f5cf7159847d6f67ee3d8e92805f785a58e959
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142545"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Ladění výkonu dotazů s využitím služby Azure Cosmos DB

Azure Cosmos DB poskytuje [rozhraní API SQL pro dotazování na data](how-to-sql-query.md), bez nutnosti schématu nebo sekundárních indexů. Tento článek obsahuje následující informace pro vývojáře:

* Základní podrobnosti o způsobu fungování služby Azure Cosmos DB spuštění dotazu SQL
* Informace o hlavičkách žádostí a odpovědí dotazu a možnosti sady SDK klienta
* Tipy a osvědčené postupy pro zajištění výkonu dotazů
* Příklady toho, jak využívat statistiky provádění SQL ladění výkonu dotazů

## <a name="about-sql-query-execution"></a>Informace o spuštění dotazu SQL

Ve službě Azure Cosmos DB ukládat data v kontejnerech, které můžou růst k libovolnému [velikost nebo abychom si vyžádali propustnost úložiště](partition-data.md). Azure Cosmos DB bezproblémově škáluje dat napříč fyzickými oddíly na pozadí zpracovat nárůst objemu dat nebo zvýšení zřízenou propustnost. Můžete zadávat dotazy SQL pro každý kontejner pomocí rozhraní REST API nebo některou z podporovaných [sad SQL SDK](sql-api-sdk-dotnet.md).

Stručný přehled dělení: můžete definovat klíč oddílu, jako je "city", která určuje, jak se data rozdělit mezi všemi fyzickými oddíly. Data patřící do jednoho klíče oddílu (například "city" == "Seattle") je uložen v rámci fyzické oddílu, ale jeden fyzický oddíl má obvykle více klíčů oddílů. Velikost úložiště dosáhne oddílu služby bez problémů rozdělí na dva oddíly, které nový oddíl a rozděluje klíč oddílu rovnoměrně mezi tyto oddíly. Vzhledem k tomu, že oddíly jsou přechodné, použijte rozhraní API abstrakce "rozsah klíče oddílu", který označuje rozsahy hodnot hash klíče oddílu. 

Když vydáte dotaz ke službě Azure Cosmos DB, SDK provede těchto logických kroků:

* Analyzovat dotaz SQL k určení plán provádění dotazu. 
* Pokud dotaz obsahuje filtr s klíčem oddílu, jako jsou `SELECT * FROM c WHERE c.city = "Seattle"`, přesměruje ho na jeden oddíl. Pokud dotaz neobsahuje filtr klíče oddílu, pak se spouští ve všech oddílech, a výsledky jsou sloučeny na straně klienta.
* Dotaz je proveden v rámci každého oddílu v řadě nebo paralelní, v závislosti na konfiguraci klienta. V rámci každého oddílu dotaz může mít jednu nebo více výměn v závislosti na složitosti dotazu nakonfigurovaná velikost stránky a zřízené propustnosti kolekce. Každé spuštění se vrátí počet [jednotky žádostí](request-units.md) spotřebované spouštěním dotazů a volitelně také statistiky provádění dotazu. 
* Sada SDK provede shrnutí výsledků dotazu napříč oddíly. Například pokud dotaz obsahuje klauzule ORDER BY napříč oddíly, pak výsledky z jednotlivých oddílů jsou seřazeny sloučení vrácení výsledků globálně seřazené podle. Pokud je dotaz agregaci jako `COUNT`, počty v jednotlivých oddílech se sčítají k vytvoření celkovým počtem.

Sady SDK poskytují různé možnosti pro spuštění dotazu. Například v .NET tyto možnosti jsou dostupné v `FeedOptions` třídy. Následující tabulka popisuje tyto možnosti a jak by mohly mít dopad doba provádění dotazu. 

| Možnost | Popis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musí být nastavena na hodnotu true pro dotazu, který vyžaduje, aby možné spouštět napříč více než jeden oddíl. Toto je explicitní příznak umožňuje dělat kompromisy, co při provádění výkon při vývoji. |
| `EnableScanInQuery` | Musí být nastavena na hodnotu true, pokud jste se rozhodli mimo indexování, ale chcete se přesto spustit dotaz využitím prohledávání. Pouze použitelné indexování pro požadovaný filtr cesty, pokud je zakázaná. | 
| `MaxItemCount` | Maximální počet položek k vrácení za odezvy na server. V nastavení na hodnotu -1, můžete nechat server spravovat počet položek. Nebo můžete snížit tuto hodnotu k načtení jenom malý počet položek za odezvy. 
| `MaxBufferedItemCount` | Toto je možnost na straně klienta a používá k omezení využití paměti při provádění napříč oddíly klauzule ORDER BY. Vyšší hodnota pomáhá snižovat latenci řazení napříč oddíly. |
| `MaxDegreeOfParallelism` | Získá nebo nastaví počet souběžných operací, které jsou spuštěny na straně klienta během paralelního provádění dotazů v databázi služby Azure Cosmos DB. Hodnotu vlastnosti kladné omezuje počet souběžných operací na hodnotu set. Pokud je nastavena na hodnotu menší než 0, systém automaticky určuje počet souběžných operací pro spuštění. |
| `PopulateQueryMetrics` | Dobu načítání umožňuje podrobné protokolování statistik času stráveného v různých fázích provádění dotazů jako čas kompilace čas index smyčky a dokumentu. Výstup z statistiky dotazů můžete sdílet s podporou Azure k diagnostice problémů s výkonem dotazů. |
| `RequestContinuation` | Předáním neprůhledné pokračovací token vrácený jakýkoli dotaz může pokračovat provádění dotazu. Token pro pokračování zapouzdřuje všechny stavy potřebných ke spuštění dotazu. |
| `ResponseContinuationTokenLimitInKb` | Můžete omezit maximální velikost token pro pokračování vrácená serverem. Můžete potřebovat nastavit aplikační hostitel má omezení velikosti hlaviček odpovědi. Toto nastavení může zvýšit celkové doby trvání a počet ru spotřebovaných pro dotaz.  |

Například, Pojďme se na to příklad dotazu na požadovaných na kolekci s klíčem oddílu `/city` jako oddíl klíče a zřízeny 100 000 RU/s propustnosti. Si vyžádáte tento dotaz pomocí `CreateDocumentQuery<T>` v .NET, jako je následující:

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

Fragment kódu SDK, vidíte výše, odpovídá následujícím požadavku REST API:

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

Každá stránka provádění dotazu odpovídá rozhraní REST API `POST` s `Accept: application/query+json` záhlaví a dotaz SQL v textu. Každý dotaz provede jednu nebo více cest k serveru pomocí `x-ms-continuation` token opakována mezi klientem a serverem, chcete-li pokračovat v provádění. Možnosti konfigurace v FeedOptions jsou předány na server v podobě hlavičky žádosti. Například `MaxItemCount` odpovídá `x-ms-max-item-count`. 

Požadavek vrátí odpověď na následující (zkrácenému pro lepší čitelnost):

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

Hlavičky klíčových odpovědi vrácené dotazem patří:

| Možnost | Popis |
| ------ | ----------- |
| `x-ms-item-count` | Počet položek, které se vrátila v odpovědi. Toto je závislá na zadané `x-ms-max-item-count`, počet položek, které můžete přizpůsobit velikost datové části odpovědi maximální, zřízená propustnost a doba provádění dotazu. |  
| `x-ms-continuation:` | Token pro pokračování pokračovat provádění dotazu, pokud jsou k dispozici další výsledky. | 
| `x-ms-documentdb-query-metrics` | Statistika dotazů pro spuštění. Toto je řetězec s oddělovači obsahující statistické údaje o času stráveného v různých fázích provádění dotazu. Pokud vrácená `x-ms-documentdb-populatequerymetrics` je nastavena na `True`. | 
| `x-ms-request-charge` | Počet [jednotky žádostí](request-units.md) spotřebované dotazem. | 

Podrobnosti o hlavičky požadavku REST API a možnostech najdete v tématu [dotazování prostředků pomocí rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Osvědčené postupy pro zajištění výkonu dotazů
Následují nejčastější faktory, které ovlivňují výkon dotazování služby Azure Cosmos DB. Můžeme se podívat hlouběji do každé z těchto témat v tomto článku.

| faktor | Tip | 
| ------ | -----| 
| Zřízená propustnost | Měření RU na dotaz a ujistěte se, že máte požadované zřízená propustnost pro dotazy. | 
| Dělení a klíče oddílů | Upřednostnit dotazů s hodnotou klíče oddílu v klauzuli filtru pro zajištění nízké latence. |
| Možnosti sady SDK a dotaz | Postupujte podle osvědčené postupy pro SDK jako přímé připojení a ladění možnosti spuštění dotazu na straně klienta. |
| Latence sítě | Účet pro síť režie měření a čtení z nejbližší oblasti pomocí rozhraní API pro vícenásobné navádění. |
| Zásada indexování | Ujistěte se, že máte požadované cesty/zásady indexování pro dotaz. |
| Metriky spouštění dotazů | Proveďte analýzu metrik provádění dotazu k identifikaci potenciálních přepisů dotazy a data obrazce.  |

### <a name="provisioned-throughput"></a>Zřízená propustnost
Ve službě Cosmos DB vytvoření kontejnerů data, každý s vyhrazenou propustností, které v požadavku jednotky (RU) za sekundu. Čtení 1 KB dokumentu je 1 RU a všechny operace (včetně dotazů) je normalizovány na pevný počet jednotek ru na základě na jeho složitost. Například pokud máte 1 000 RU/s zřízených pro váš kontejner, a máte dotaz podobný tomuto: `SELECT * FROM c WHERE c.city = 'Seattle'` 5 RU, který využívá, pak můžete provádět (1 000 RU/s) / (5 RU/dotazu) = 200 dotazů za sekundu takové dotazy za sekundu. 

Pokud uvedete více než 200 dotazy za sekundu, spuštění služby omezování rychlosti příchozí požadavky nad 200/s. Sady SDK automaticky zpracovávat tento případ provedením omezení rychlosti a opakování pokusů, proto si všimnete vyšší latence pro tyto dotazy. Zvýšení propustnosti na požadovaná hodnota zvyšuje latence dotazu a propustnost. 

Další informace o jednotkách žádostí, najdete v článku [jednotky žádostí](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Dělení a klíče oddílů
Pomocí služby Azure Cosmos DB obvykle dotazy provádět v uvedeném pořadí. z nejrychlejší/většinu efektivní nižší a méně efektivní. 

* Získejte na jeden klíč oddílu a klíč položky
* Dotazy s klauzulí filtru pro jeden klíč oddílu
* Dotazy bez filtru klauzule zjištění rovnosti, nebo rozsah jakékoli vlastnosti
* Dotazy bez filtru

Dotazy, které potřebujete poradit všechny oddíly potřebovat vyšší latencí a může využívat vyšší RU. Protože každý oddíl má automatického indexování pro všechny vlastnosti, dotaz může být zpracují efektivně z indexu v tomto případě. Můžete vytvořit dotazy, které jsou rozmístěny oddíly rychleji díky použití možností paralelismu.

Další informace o vytváření oddílů a klíče oddílů, naleznete v tématu [dělení ve službě Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Možnosti sady SDK a dotaz
Zobrazit [tipy ke zvýšení výkonu](performance-tips.md) a [testování výkonu](performance-testing.md) pro získání nejlepšího výkonu na straně klienta ze služby Azure Cosmos DB. To zahrnuje použití nejnovější sady SDK, konfigurace specifické pro platformu konfigurace jako výchozí počet připojení, frekvence uvolnění paměti a pomocí možnosti zjednodušené připojení jako přímé/TCP. 


#### <a name="max-item-count"></a>Maximální počet položek
Pro dotazy, hodnota `MaxItemCount` může mít významný dopad na čas začátku do konce dotazu. Vrátí nesmí být větší než počet položek v jednotlivých odezvy serveru `MaxItemCount` (výchozí je 100 položek). Toto nastavení na vyšší hodnotu (-1 je maximální a doporučené) celkové doby trvání dotazu se zlepší tím, že omezíte počet zpátečních cest mezi serverem a klientem, zejména u dotazů s velké množství výsledků.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximální míra paralelismu
Pro dotazy, vyladit `MaxDegreeOfParallelism` k identifikaci doporučené konfigurace pro aplikace, zejména v případě, že můžete provádět dotazy napříč oddíly (bez filtru na základě hodnoty klíče oddílu). `MaxDegreeOfParallelism`  Určuje maximální počet paralelních úloh, například maximální počet oddílů, aby navštívil paralelně. 

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
* D = výchozí maximální počet paralelních úloh (tzn. Celkový počet procesorů v klientském počítači.)
* P = uživatelem zadané maximální počet paralelních úloh
* N = počet oddílů, které je potřeba navštívit odpovědí na dotazy

Toto jsou důsledky chování paralelní dotazy pro různé hodnoty P.
* (P == 0) = > režim sériového portu
* (P == 1) = > maximálně jeden úkol
* (P > 1.) = > paralelní úlohy Min (P, N) 
* (P < 1) = > paralelní úlohy Min (N, D)

Poznámky k verzi sady SDK a zobrazit podrobnosti o implementované třídy a metody [sad SQL SDK](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Latence sítě
Zobrazit [globální distribuce služby Azure Cosmos DB](tutorial-global-distribution-sql-api.md) jak nastavit globální distribuci a připojit do nejbližší oblasti. Latence sítě má významný dopad na výkon dotazů, když budete chtít provést vícenásobný nebo načítat velké sady výsledků z dotazu. 

V části týkající se metrik provádění dotazu vysvětluje, jak načíst čas spuštění serveru dotazů ( `totalExecutionTimeInMs`), takže lze rozlišit času stráveného při provádění dotazu a času stráveného tranzitní sítě.

### <a name="indexing-policy"></a>Zásady indexování
Zobrazit [Konfigurace zásady indexování](index-policy.md) indexování cesty, typy a režimech a jak by mohly mít dopad provedení dotazu. Ve výchozím nastavení, zásady indexování používá indexování hodnot Hash pro řetězce, které platí pro dotazy na rovnost, ale ne pro dotazy na rozsah/order dotazy. Pokud potřebujete dotazy na rozsah pro řetězce, doporučujeme určení index typu rozsah pro všechny řetězce. 

## <a name="query-execution-metrics"></a>Metriky spouštění dotazů
Můžete získat podrobné metriky spouštění dotazů předáním volitelné `x-ms-documentdb-populatequerymetrics` záhlaví (`FeedOptions.PopulateQueryMetrics` v sadě .NET SDK). Hodnota vrácená v `x-ms-documentdb-query-metrics` má následující páry klíč hodnota, které jsou určené pro pokročilé Poradce při potížích pro spuštění dotazu. 

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
| `queryCompileTimeInMs` | milisekundy | Kompilace dotazu  | 
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Čas se sestavit plán logické dotazu | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Čas se sestavit plán fyzické dotazu | 
| `queryOptimizationTimeInMs` | milisekundy | Doba trvání optimalizaci dotazu | 
| `VMExecutionTimeInMs` | milisekundy | Doba trvání běhu dotazu | 
| `indexLookupTimeInMs` | milisekundy | Doba trvání fyzické index vrstvy | 
| `documentLoadTimeInMs` | milisekundy | Čas strávený při načítání dokumentů  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený provádění funkcí (vestavěné) systému v milisekundách  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený prováděné uživatelem definovaných funkcí v milisekundách | 
| `retrievedDocumentCount` | count | Celkový počet načtených dokumentů  | 
| `retrievedDocumentSize` | bajtů | Celková velikost načtených dokumentů v bajtech  | 
| `outputDocumentCount` | count | Počet výstupních dokumentech | 
| `writeOutputTimeInMs` | milisekundy | Doba provádění dotazu v milisekundách | 
| `indexUtilizationRatio` | poměr (< = 1) | Načíst poměr počtu dokumentů odpovídající filtr, aby se počet dokumentů  | 

Klientské sady SDK interně, aby více operací dotazů k obsluze dotazu v rámci každého oddílu. Klient podá více než jedno standardní volání oddílů pokud překročí celkový počet výsledků `x-ms-max-item-count`, pokud dotaz překračuje zřízenou propustnost oddíl datové části dotazu dosáhne maximální velikosti na stránku, nebo pokud dotaz dosáhne systému přidělené časový limit. Každé spuštění částečné dotaz se vrátí `x-ms-documentdb-query-metrics` pro danou stránku. 

Tady je několik ukázkových dotazů a jak interpretovat některé z metrik vrácená z dotazu spuštění: 

| Dotaz | Ukázkové metriky | Popis | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Počet dokumentů, načíst je 100 + 1 tak, aby odpovídaly klauzule TOP. Doba dotazu je většinou trvání `WriteOutputTime` a `DocumentLoadTime` protože jde o kontrolu. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount je teď vyšší (víc než 500 1 tak, aby odpovídaly klauzule TOP). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Informace o 0.9 ms byl stráven IndexLookupTime pro vyhledávání klíčů, protože se jedná index vyhledávání `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | O něco vyšší doba (1.7 ms) trvání IndexLookupTime přes rozsah kontroly, protože se jedná index vyhledávání `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Stejný čas strávený `DocumentLoadTime` jako předchozí dotazy, ale nižší `WriteOutputTime` vzhledem k tomu, že jsme při projekci pouze jednu vlastnost. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Asi 213 ms byl stráven `UserDefinedFunctionExecutionTime` spuštění UDF na každou hodnotu `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Přibližně 0,6 ms byl stráven `IndexLookupTime` na `/Name/?`. Většinu doby provádění dotazu (~ 7 ms) v `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Dotaz se provádí jako kontrolu, protože používá `LOWER`, a jsou vráceny 500 mimo 2491 načtených dokumentů. |


## <a name="next-steps"></a>Další postup
* Další informace o podporovaných klíčová slova a operátorů dotazu SQL najdete v tématu [dotaz SQL](how-to-sql-query.md). 
* Další informace o jednotkách žádostí, najdete v článku [jednotky žádostí](request-units.md).
* Další informace o zásady indexování, naleznete v tématu [zásady indexování](index-policy.md) 


