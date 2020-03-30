---
title: Získání výkonu dotazu SQL & metriky spuštění
description: Zjistěte, jak načíst metriky spuštění dotazu SQL a výkon dotazů SQL profilu požadavků Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998369"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Získání metrik spuštění dotazu SQL a analýza výkonu dotazu pomocí sady .NET SDK

Tento článek popisuje, jak profilovat výkon dotazu SQL v Azure Cosmos DB. Toto profilování lze `QueryMetrics` provést pomocí načteny z .NET SDK a je podrobně zde. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) je objekt silného typu s informacemi o spuštění back-endového dotazu. Tyto metriky jsou podrobněji popsány v článku [Výkon optimalizace dotazu.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>Nastavení parametru FeedOptions

Všechna přetížení pro [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) trvat volitelný [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) parametr. Tato možnost je to, co umožňuje spuštění dotazu, které mají být naladěny a parametrizovány. 

Chcete-li shromažďovat metriky spuštění dotazu SQL, musíte nastavit parametr `true` [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) v [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) na . Nastavení `PopulateQueryMetrics` na hodnotu true `FeedResponse` bude mít `QueryMetrics`tak, aby bude obsahovat příslušné . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Získat metriky dotazu pomocí AsDocumentQuery()
Následující ukázka kódu ukazuje, jak provést načíst metriky při použití [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metoda:

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

V předchozí části všimněte si, že bylo více volání [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metody. Každé volání `FeedResponse` vrátilo objekt, který `QueryMetrics`má slovník ; jeden pro každé pokračování dotazu. Následující příklad ukazuje, jak `QueryMetrics` je agregovat pomocí LINQ:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Seskupení metrik dotazu podle ID oddílu

Můžete seskupit `QueryMetrics` Podle ID oddílu. Seskupení podle ID oddílu umožňuje zjistit, zda určitý oddíl způsobuje problémy s výkonem ve srovnání s ostatními. Následující příklad ukazuje, `QueryMetrics` jak seskupit pomocí LINQ:

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

Můžete také získat `FeedResponse` z linq dotazu `AsDocumentQuery()` pomocí metody:

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

Můžete zachytit jednotky požadavku spotřebované každý dotaz prozkoumat nákladné dotazy nebo dotazy, které spotřebovávají vysokou propustnost. Poplatek za požadavek můžete získat pomocí `FeedResponse`vlastnosti [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) v . Další informace o tom, jak získat poplatek za požadavek pomocí portálu Azure portal a různých sad SDK, najdete v článku [o poplatcích za jednotku požadavku.](find-request-unit-charge.md)

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

## <a name="get-the-query-execution-time"></a>Získání času spuštění dotazu

Při výpočtu času potřebného k provedení dotazu na straně klienta, `ExecuteNextAsync` ujistěte se, že zahrnete pouze čas volání metody a ne jiné části základu kódu. Pouze tato volání vám pomohou při výpočtu, jak dlouho trvalo spuštění dotazu, jak je znázorněno v následujícím příkladu:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Prohledávat dotazy (obvykle pomalé a nákladné)

Prohledávací dotaz odkazuje na dotaz, který nebyl obsluhován indexem, kvůli kterému je před vrácením sady výsledků načteno mnoho dokumentů.

Níže je uveden příklad skenovacího dotazu:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtr tohoto dotazu používá systémovou funkci UPPER, která není obsluhována z indexu. Spuštění tohoto dotazu proti velké kolekci vytvořilnásledující metriky dotazu pro první pokračování:

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

Poznamenejte si následující hodnoty z výstupu metrik dotazu:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Tento dotaz načetl 60 951 dokumentů, což bylo celkem 399 998 938 bajtů. Načtení těchto mnoha bajtů má za následek vysoké náklady nebo jednotkový poplatek požadavku. Trvá také dlouhou dobu ke spuštění dotazu, což je jasné s celkovým časem stráveným vlastnost:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

To znamená, že dotaz trvalo 4,5 sekundy ke spuštění (a to bylo pouze jedno pokračování).

Chcete-li optimalizovat tento příklad dotazu, vyhněte se použití HORNÍ ve filtru. Místo toho při vytváření nebo `c.description` aktualizaci dokumentů musí být hodnoty vloženy do všech velkých písmen. Dotaz se pak stane: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Tento dotaz je nyní možné obsluhovat z indexu.

Další informace o ladění výkonu dotazů najdete v článku [Optimalizace výkonu dotazu.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Odkazy

- [Specifikace SQL Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Další kroky

- [Ladění výkonu dotazů](sql-api-query-metrics.md)
- [Přehled indexování](index-overview.md)
- [Ukázky služby Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
