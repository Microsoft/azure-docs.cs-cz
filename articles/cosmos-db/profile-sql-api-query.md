---
title: Získat výkon dotazu SQL & metriky spuštění
description: Naučte se, jak načíst metriky spouštění dotazů SQL a Profilovat výkon dotazů SQL pro žádosti Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 66aefea441d78303ccd611d9df10eea985d61e7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097392"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Získat metriky spouštění dotazů SQL a analyzovat výkon dotazů pomocí sady .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek ukazuje, jak Profilovat výkon dotazů SQL na Azure Cosmos DB. Tato profilace se dá provést pomocí `QueryMetrics` načtení ze sady .NET SDK a je tady popsána. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) je objekt silného typu s informacemi o spuštění dotazu back-endu. Tyto metriky jsou podrobněji popsány v článku o [výkonu dotazů ladění](./sql-api-query-metrics.md) .

## <a name="set-the-feedoptions-parameter"></a>Nastavení parametru FeedOptions

Všechna přetížení pro [DocumentClient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) přebírají volitelný parametr [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) . Tato možnost umožňuje vyladit a parametrizovanit provádění dotazů. 

Chcete-li shromáždit metriky spuštění dotazu SQL, je nutné nastavit parametr [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) v [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) na `true` . Nastavením `PopulateQueryMetrics` na hodnotu true se nastaví tak, aby `FeedResponse` obsahovala relevantní `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Získat metriky dotazů pomocí AsDocumentQuery ()
Následující ukázka kódu ukazuje, jak načíst metriky při použití metody [AsDocumentQuery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agregace QueryMetrics

V předchozí části si všimněte, že existuje více volání metody [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) . Každé volání vrátilo `FeedResponse` objekt, který má slovník typu `QueryMetrics` ; jeden pro každé pokračování dotazu. Následující příklad ukazuje, jak je agregovat `QueryMetrics` pomocí LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Seskupení metrik dotazů podle ID oddílu

Můžete seskupovat `QueryMetrics` podle ID oddílu. Seskupení podle ID oddílu vám umožní zjistit, jestli konkrétní oddíl způsobuje problémy s výkonem ve srovnání s ostatními. Následující příklad ukazuje, jak seskupit `QueryMetrics` pomocí LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ na DocumentQuery

Můžete také získat `FeedResponse` dotaz z dotazu LINQ pomocí `AsDocumentQuery()` metody:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Nákladné dotazy

Můžete zachytit jednotky žádostí spotřebované jednotlivými dotazy a prozkoumat nákladné dotazy nebo dotazy, které využívají vysokou propustnost. Poplatek za žádost můžete získat pomocí vlastnosti [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) v `FeedResponse` . Další informace o tom, jak získat poplatek za požadavek pomocí Azure Portal a různých sad SDK, najdete v článku [vyhledání poplatků za jednotku žádosti](find-request-unit-charge.md) .

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Získat čas provedení dotazu

Při výpočtu času potřebného pro spuštění dotazu na straně klienta, nezapomeňte zahrnout pouze čas pro volání `ExecuteNextAsync` metody a jiné části vašeho základu kódu. Pouze tyto hovory vám pomůžou vypočítat dobu trvání spuštění dotazu, jak je znázorněno v následujícím příkladu:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Kontrolovat dotazy (obvykle pomalu a nákladné)

Dotaz na skenování odkazuje na dotaz, který nebyl poskytnut indexem, protože bylo načteno mnoho dokumentů před vrácením sady výsledků dotazu.

Níže je příklad dotazu prověřování:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtr tohoto dotazu používá funkci System UPPER v horní části, která není obsluhována z indexu. Spuštění tohoto dotazu proti velké kolekci vytvořilo následující metriky dotazu pro první pokračování:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Všimněte si, že výstup metriky dotazů má následující hodnoty:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Tento dotaz načetl 60 951 dokumentů, které celkem 399 998 938 bajtů. Načtení tohoto počtu bajtů má za následek vysoké náklady nebo poplatky za jednotku žádosti. Spuštění dotazu trvá také dlouhou dobu, což je jasné jako celková trvání vlastnosti:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

To znamená, že dotaz byl spuštěn na 4,5 sekund (a byl to pouze jeden pokračování).

Chcete-li optimalizovat tento příklad dotazu, vyhněte se použití HORNÍho pole ve filtru. Místo toho se při vytváření nebo aktualizaci dokumentů `c.description` musí hodnoty vkládat do všech velkých písmen. Dotaz pak bude: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Tento dotaz je nyní možné zpracovat z indexu.

Další informace o ladění výkonu dotazů najdete v článku o [výkonu dotazů ladění](./sql-api-query-metrics.md) .

## <a name="references"></a><a id="References"></a>Reference

- [Azure Cosmos DB specifikace SQL](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Další kroky

- [Ladění výkonu dotazů](sql-api-query-metrics.md)
- [Přehled indexování](index-overview.md)
- [Ukázky Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)