---
title: Metriky dotazů SQL pro Azure Cosmos DB SQL API
description: Naučte se, jak instrumentovat a ladit výkon dotazů SQL pro žádosti Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/23/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: ec98d194921cd9a7eced06ccee20a3375e8c8a82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008688"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Ladění výkonu dotazů pomocí služby Azure Cosmos DB

Azure Cosmos DB poskytuje [rozhraní SQL API pro dotazování na data](how-to-sql-query.md), bez nutnosti schématu nebo sekundárních indexů. Tento článek poskytuje pro vývojáře následující informace:

* Podrobné informace o tom, jak funguje Azure Cosmos DB provádění dotazů SQL
* Podrobnosti o dotazech na požadavky a odpovědi na dotazy a možnosti klientské sady SDK
* Tipy a osvědčené postupy pro výkon dotazů
* Příklady použití statistik provádění SQL k ladění výkonu dotazů

## <a name="about-sql-query-execution"></a>O provádění dotazů SQL

V Azure Cosmos DB ukládáte data do kontejnerů, které se můžou zvětšovat podle [velikosti úložiště nebo propustnosti požadavků](partition-data.md). Azure Cosmos DB plynule škáluje data mezi fyzickými oddíly v rámci pokrývání dat a zpracování nárůstu dat nebo zvýšení zajištěné propustnosti. Dotazy SQL můžete vystavit do libovolného kontejneru pomocí REST API nebo některé z podporovaných [sad SQL SDK](sql-api-sdk-dotnet.md).

Stručný přehled dělení: definujete klíč oddílu, například City, který určuje, jak se data rozdělí mezi fyzické oddíly. Data patřící do klíče s jedním oddílem (například City "= =" Praha ") jsou uložena v rámci fyzického oddílu, ale obvykle jeden fyzický oddíl má více klíčů oddílu. Když oddíl dosáhne velikosti úložiště, služba hladce rozdělí oddíl na dva nové oddíly a rovnoměrně rozděluje klíč oddílu mezi tyto oddíly. Vzhledem k tomu, že se oddíly dočasná, používají rozhraní API abstrakci rozsahu klíčů oddílu, který označuje rozsahy hodnot hash klíčů oddílů. 

Když vydáte dotaz pro Azure Cosmos DB, sada SDK provede tyto logické kroky:

* Rozanalyzujte dotaz SQL a určete plán spouštění dotazů. 
* Pokud dotaz obsahuje filtr na klíč oddílu, například `SELECT * FROM c WHERE c.city = "Seattle"` , je směrován do jednoho oddílu. Pokud dotaz nemá filtr na klíč oddílu, je spuštěn ve všech oddílech a výsledky jsou sloučeny na straně klienta.
* Dotaz se spustí v rámci každého oddílu v řadě nebo paralelně na základě konfigurace klienta. V rámci každého oddílu může dotaz udělat jednu nebo více zpátečních cest v závislosti na složitosti dotazu, nakonfigurované velikosti stránky a zřízené propustnosti kolekce. Každé spuštění vrátí počet [jednotek žádostí](request-units.md) spotřebovaných provedením dotazu a volitelně také statistiku provádění dotazů. 
* Sada SDK provede Shrnutí výsledků dotazu napříč oddíly. Například pokud dotaz zahrnuje pořadí v různých oddílech, pak jsou výsledky z jednotlivých oddílů sloučeny, aby vracely výsledky globálně seřazeného pořadí. Pokud je dotaz agregací, například `COUNT` počty z jednotlivých oddílů jsou shrnuty, aby vznikl celkový počet.

Sady SDK poskytují různé možnosti pro provádění dotazů. Například v rozhraní .NET jsou tyto možnosti k dispozici ve `FeedOptions` třídě. Následující tabulka popisuje tyto možnosti a jejich dopad na dobu provádění dotazu. 

| Možnost | Popis |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Musí být nastaven na hodnotu true pro všechny dotazy, které je třeba provést v rámci více než jednoho oddílu. Toto je explicitní příznak, který vám umožní zajistit, aby v době vývoje byly kompromisy v výkonu. |
| `EnableScanInQuery` | Je nutné nastavit na hodnotu true, pokud jste se vyhlásili z indexování, ale chcete spustit dotaz i v rámci kontroly. Dá se použít jenom v případě, že indexování pro požadovanou cestu filtru je zakázané. | 
| `MaxItemCount` | Maximální počet položek, které se mají vrátit na jednu zpáteční cestu k serveru. Nastavením na hodnotu-1 můžete umožnit serveru spravovat počet položek. Nebo můžete snížit tuto hodnotu a načíst jenom malý počet položek na jednu zpáteční cestu. 
| `MaxBufferedItemCount` | Toto je možnost na straně klienta, která slouží k omezení spotřeby paměti při provádění řazení mezi oddíly. Vyšší hodnota pomáhá snižovat latenci řazení mezi oddíly. |
| `MaxDegreeOfParallelism` | Získá nebo nastaví počet souběžných operací, které se spouštějí na straně klienta během paralelního provádění dotazů ve službě Azure Cosmos Database. Kladná hodnota vlastnosti omezuje počet souběžných operací na nastavenou hodnotu. Je-li hodnota nastavena na hodnotu menší než 0, systém automaticky určí počet souběžných operací, které mají být spuštěny. |
| `PopulateQueryMetrics` | Umožňuje podrobné protokolování času stráveného v různých fázích provádění dotazů, jako je čas kompilace, doba cyklu indexu a doba načítání dokumentu. Můžete sdílet výstup z statistiky dotazů s podporou Azure a diagnostikovat problémy s výkonem dotazů. |
| `RequestContinuation` | Můžete pokračovat v provádění dotazu předáním neprůhledného tokenu pokračování vráceného jakýmkoli dotazem. Token pokračování zapouzdřuje veškerý stav potřebný k provedení dotazu. |
| `ResponseContinuationTokenLimitInKb` | Můžete omezit maximální velikost tokenu pokračování vráceného serverem. Může být nutné nastavit tuto možnost, pokud má hostitel aplikace omezení velikosti hlavičky odpovědi. Toto nastavení může zvýšit celkovou dobu trvání a ru spotřebované pro dotaz.  |

Řekněme například, že se podíváme na klíč oddílu požadovaný v kolekci s `/city` klíčovým oddílem a zřízeným 100 000 ru/s propustností. Tento dotaz vyžádáte pomocí `CreateDocumentQuery<T>` rozhraní .NET, například takto:

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

Výše uvedený fragment kódu SDK odpovídá následujícímu REST API žádosti:

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

Každá stránka pro spuštění dotazu odpovídá REST API `POST` s `Accept: application/query+json` hlavičkou a dotazem SQL v těle. Každý dotaz vytvoří jednu nebo více zpátečních cest k serveru s `x-ms-continuation` tokenem, který vrací mezi klientem a serverem, aby obnovil provádění. Možnosti konfigurace v nástroji FeedOptions jsou předány na server v podobě hlaviček požadavku. Například `MaxItemCount` odpovídá `x-ms-max-item-count` . 

Požadavek vrátí následující (zkrácené pro odpověď čitelnosti):

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

Hlavičky odpovědí na klíč vrácené z dotazu zahrnují následující:

| Možnost | Popis |
| ------ | ----------- |
| `x-ms-item-count` | Počet položek vrácených v odpovědi. Tato možnost závisí na zadaném `x-ms-max-item-count` počtu položek, které se mohou vejít do maximální velikosti datové části odpovědi, zřízené propustnosti a času provádění dotazu. |  
| `x-ms-continuation:` | Token pokračování pro pokračování v provádění dotazu, pokud jsou k dispozici další výsledky. | 
| `x-ms-documentdb-query-metrics` | Statistika dotazu pro provedení. Toto je oddělený řetězec, který obsahuje statistiku času stráveného v různých fázích provádění dotazů. Vráceno `x-ms-documentdb-populatequerymetrics` , pokud je nastaveno na `True` . | 
| `x-ms-request-charge` | Počet [jednotek žádostí](request-units.md) spotřebovaných dotazem. | 

Podrobnosti o hlavičkách a možnostech žádosti o REST API najdete v tématu [dotazování na prostředky pomocí REST API](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Osvědčené postupy pro výkon dotazů
Níže jsou uvedené nejběžnější faktory, které mají vliv na Azure Cosmos DB výkon dotazů. Dig se podrobněji pro každé z těchto témat v tomto článku.

| Faktor | Tip | 
| ------ | -----| 
| Zřízená propustnost | Změřte RU na dotaz a ujistěte se, že máte požadovanou zřízenou propustnost pro vaše dotazy. | 
| Dělení a klíče oddílů | Upřednostnit dotazy s hodnotou klíče oddílu v klauzuli Filter pro nízkou latenci. |
| Sada SDK a možnosti dotazu | Dodržujte osvědčené postupy sady SDK, jako je přímé připojení, a vylaďte možnosti spouštění dotazů na straně klienta. |
| Latence sítě | Účet pro měření režie sítě a použití rozhraní API pro více domovských míst ke čtení z nejbližší oblasti. |
| Zásady indexování | Ujistěte se, že máte pro dotaz požadované cesty k indexování nebo zásady. |
| Metriky spuštění dotazu | Analyzujte metriky spouštění dotazů a Identifikujte potenciální přepis datových tvarů dotazů a dat.  |

### <a name="provisioned-throughput"></a>Zřízená propustnost
V Cosmos DB vytvoříte kontejnery dat, každou s rezervovanou propustností vyjádřenou v jednotkách žádosti (RU) za sekundu. Čtení dokumentu 1 KB je 1 RU a každá operace (včetně dotazů) je normalizována na pevný počet ru na základě jeho složitosti. Pokud jste například pro váš kontejner zřídili 1000 RU/s a máte dotaz `SELECT * FROM c WHERE c.city = 'Seattle'` , který spotřebovává 5 ru, můžete provádět (1000 ru/s)/(5 ru/Query) = 200 dotazy/s takových dotazů za sekundu. 

Pokud odešlete více než 200 dotazů za sekundu, služba spustí příchozí požadavky s omezením nad 200/s. Sady SDK tento případ automaticky zpracovávají pomocí omezení rychlosti/opakování, takže si můžete všimnout vyšší latence pro tyto dotazy. Zvýšení zřízené propustnosti na požadovanou hodnotu vylepšuje latenci a propustnost dotazů. 

Další informace o jednotkách žádostí najdete v tématu [jednotky žádostí](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Dělení a klíče oddílů
U Azure Cosmos DB obvykle dotazy provádějí v následujícím pořadí od nejrychlejší/nejefektivnější až po pomalejší/méně efektivní. 

* ZÍSKAT na jednom klíči oddílu a klíč položky
* Dotaz s klauzulí Filter na jeden klíč oddílu
* Dotaz bez klauzule filtru rovnosti nebo rozsahu u libovolné vlastnosti
* Dotaz bez filtrů

Dotazy, které musí pohlížet na všechny oddíly, vyžadují větší latenci a můžou využívat vyšší ru. Vzhledem k tomu, že každý oddíl má automatické indexování proti všem vlastnostem, lze dotaz v tomto případě efektivně zpracovat z indexu. Pomocí možností paralelismus můžete vytvářet dotazy, které přesahují oddíly rychleji.

Další informace o dělení a klíčích oddílů najdete v tématu [dělení v Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>Sada SDK a možnosti dotazu
V tématu [tipy k výkonu](performance-tips.md) a [testování výkonu](performance-testing.md) získáte nejlepší výkon na straně klienta z Azure Cosmos DB. To zahrnuje použití nejnovějších sad SDK, konfigurace konfigurací specifických pro konkrétní platformu, jako je výchozí počet připojení, frekvence uvolňování paměti a použití zjednodušených možností připojení, jako je Direct/TCP. 


#### <a name="max-item-count"></a>Maximální počet položek
V případě dotazů `MaxItemCount` může mít hodnota významného vlivu na koncové časy dotazů. Při každé operaci odezvy na server nebude vráceno více než počet položek `MaxItemCount` (výchozí hodnota je 100 položek). Nastavení této hodnoty na vyšší hodnotu (-1 je maximum a doporučeno) vylepší celkovou dobu trvání dotazu tím, že omezí počet přenosů mezi serverem a klientem, zejména pro dotazy s velkými sadami výsledků.

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
V případě dotazů můžete vyladit `MaxDegreeOfParallelism` a identifikovat nejlepší konfigurace pro vaši aplikaci, zejména pokud provádíte dotazy mezi oddíly (bez filtru na hodnotu klíče oddílu). `MaxDegreeOfParallelism`  Určuje maximální počet paralelních úloh, tj. maximální počet oddílů, které mají být navštíveny paralelně. 

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

Pojďme předpokládat, že
* D = výchozí maximální počet paralelních úloh (= celkový počet procesorů v klientském počítači)
* P = uživatelem zadaný maximální počet paralelních úkolů
* N = počet oddílů, které musí být navštíveny pro zodpovězení dotazu

Níže jsou uvedené důsledky, jak se paralelní dotazy budou chovat pro různé hodnoty P.
* (P = = 0) => sériového režimu
* (P = = 1) => maximálně jeden úkol
* (P > 1) => min (P, N) Paralelní úlohy 
* (P < 1) => min (N, D) Paralelní úlohy

Poznámky k verzi sady SDK a podrobnosti o implementovaných třídách a metodách naleznete v tématu [SQL SDK](sql-api-sdk-dotnet.md) .

### <a name="network-latency"></a>Latence sítě
Jak nastavit globální distribuci a připojit se k nejbližší oblasti, najdete v tématu [Azure Cosmos DB globální distribuce](tutorial-global-distribution-sql-api.md) . Latence sítě má významný dopad na výkon dotazů, pokud potřebujete udělat více přenosových cest nebo načíst velkou sadu výsledků dotazu. 

Oddíl metriky spouštění dotazů vysvětluje, jak načíst dobu provádění dotazů ( `totalExecutionTimeInMs` ), takže můžete rozlišovat čas strávený při provádění dotazů a čas strávený při přenosu v síti.

### <a name="indexing-policy"></a>Zásada indexování
Viz téma [Konfigurace zásad indexování](index-policy.md) pro cesty, druhy a režimy indexování a to, jak ovlivňují provádění dotazů. Ve výchozím nastavení zásada indexování používá indexování algoritmu hash pro řetězce, které jsou platné pro dotazy na rovnost, ale ne pro dotaz na rozsah nebo řazení podle dotazů. Pokud pro řetězce potřebujete dotazy na rozsah, doporučujeme zadat typ indexu rozsahu pro všechny řetězce. 

Ve výchozím nastavení Azure Cosmos DB použije automatické indexování na všechna data. V případě scénářů vkládání s vysokým výkonem zvažte možnost vyloučení cest, protože se tím sníží náklady na RU za každou operaci vložení. 

## <a name="query-execution-metrics"></a>Metriky spuštění dotazu
Podrobné metriky pro provádění dotazů můžete získat předáním volitelné `x-ms-documentdb-populatequerymetrics` hlavičky ( `FeedOptions.PopulateQueryMetrics` v sadě .NET SDK). Hodnota vrácená v `x-ms-documentdb-query-metrics` má následující páry klíč-hodnota, které jsou určeny pro pokročilé řešení potíží s prováděním dotazů. 

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

| Metrika | Jednotka | Description | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milisekundy | Čas provedení dotazu | 
| `queryCompileTimeInMs` | milisekundy | Čas kompilace dotazu  | 
| `queryLogicalPlanBuildTimeInMs` | milisekundy | Čas k sestavení plánu logického dotazu | 
| `queryPhysicalPlanBuildTimeInMs` | milisekundy | Čas k sestavení fyzického plánu dotazu | 
| `queryOptimizationTimeInMs` | milisekundy | Čas strávený v optimalizaci dotazu | 
| `VMExecutionTimeInMs` | milisekundy | Čas strávený v modulu runtime dotazů | 
| `indexLookupTimeInMs` | milisekundy | Čas strávený fyzickou indexovou vrstvou | 
| `documentLoadTimeInMs` | milisekundy | Čas strávený načítáním dokumentů  | 
| `systemFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený prováděním systému (integrovaných) funkcí v milisekundách  | 
| `userFunctionExecuteTimeInMs` | milisekundy | Celkový čas strávený prováděním uživatelsky definovaných funkcí v milisekundách | 
| `retrievedDocumentCount` | count | Celkový počet načtených dokumentů  | 
| `retrievedDocumentSize` | psaný | Celková velikost načtených dokumentů v bajtech  | 
| `outputDocumentCount` | count | Počet výstupních dokumentů | 
| `writeOutputTimeInMs` | milisekundy | Doba provádění dotazu v milisekundách | 
| `indexUtilizationRatio` | poměr (<= 1) | Poměr počtu dokumentů odpovídajících filtru na počet načtených dokumentů  | 

Klientské sady SDK mohou interně provádět dotazy v rámci jednotlivých oddílů. Klient provede více než jedno volání na oddíl, pokud celkový výsledek překročí `x-ms-max-item-count` , pokud dotaz překračuje zřízenou propustnost pro oddíl, nebo pokud datová část dotazu dosáhne maximální velikosti na stránce nebo pokud dotaz dosáhne časového limitu přiděleného systému. Každé částečné spuštění dotazu vrátí `x-ms-documentdb-query-metrics` pro tuto stránku. 

Tady je několik ukázkových dotazů a postup interpretace některých metrik vrácených spuštěním dotazu: 

| Dotaz | Ukázková metrika | Description | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Počet načtených dokumentů je 100 + 1, aby se shodovala s horní klauzulí. Čas dotazu se většinou stráví v `WriteOutputTime` a `DocumentLoadTime` vzhledem k tomu, že se jedná o kontrolu. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount je teď vyšší (500 + 1 tak, aby odpovídalo horní klauzuli). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Přibližně 0,9 MS vychází z IndexLookupTime pro vyhledávání klíčů, protože se jedná o vyhledávání v indexu `/N/?` . | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Ještě více času (1,7 MS) strávených IndexLookupTime nad kontrolou rozsahu, protože se jedná o vyhledávání v indexu `/N/?` . | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Stejný čas strávený na `DocumentLoadTime` předchozích dotazech, ale nižší, `WriteOutputTime` protože probíhá projekce pouze jedné vlastnosti. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Přibližně 213 MS se stráví `UserDefinedFunctionExecutionTime` spouštěním systému souborů UDF na každé hodnotě `c.N` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Přibližně v systému je vyčerpáno 0,6 MS `IndexLookupTime` `/Name/?` . Většina času provedení dotazu (~ 7 MS) v `SystemFunctionExecutionTime` . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Dotaz je proveden jako kontrola, protože používá `LOWER` a je vráceno 500 z 2491 načtených dokumentů. |


## <a name="next-steps"></a>Další kroky
* Další informace o podporovaných operátorech dotazu SQL a klíčových slovech naleznete v tématu [SQL Query](sql-query-getting-started.md). 
* Další informace o jednotkách žádostí najdete v tématu [jednotky žádostí](request-units.md).
* Další informace o zásadách indexování najdete v tématu [indexování zásad](index-policy.md) . 


