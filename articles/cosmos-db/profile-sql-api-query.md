---
title: Získat metriky výkonu a spuštění dotazu SQL
description: Zjistěte, jak načíst metriky spouštění dotazů SQL a profil výkon dotazů SQL žádostí o služby Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481561"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Získat metriky spouštění dotazů SQL a analýza výkonu dotazů pomocí sady .NET SDK

Tento článek představuje, jak chcete-li Profilovat výkon dotazů SQL ve službě Azure Cosmos DB. Tato profilování lze provést pomocí `QueryMetrics` načten ze sady .NET SDK a je podrobně popsán tady. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) je silně typovaných objektů s informacemi o spuštění dotazu back-endu. Tyto metriky jsou popsány podrobněji [ladění výkonu dotazů](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) článku.

## <a name="set-the-feedoptions-parameter"></a>Nastavte parametr FeedOptions

Všechna přetížení pro [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) trvat, než se volitelně [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) parametru. Tato možnost je, co umožňuje vyladění a parametry v provádění dotazu. 

Ke shromažďování metrik spouštění dotazů Sql, je nutné nastavit parametr [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) v [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) k `true`. Nastavení `PopulateQueryMetrics` na hodnotu true filtrovacího řetězce se tak, aby `FeedResponse` bude obsahovat příslušnou `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Získat metriky dotazu s AsDocumentQuery()
Následující příklad kódu ukazuje, jak načíst metriky, při použití [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metody:

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
## <a name="aggregating-querymetrics"></a>Agregování QueryMetrics

V předchozí části, Všimněte si, že byla více volání [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metody. Každé volání vrátilo `FeedResponse` objekt, který obsahuje slovník `QueryMetrics`; jeden pro každý pokračování dotazu. Následující příklad ukazuje, jak agregovat tyto `QueryMetrics` pomocí jazyka LINQ:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Metriky dotazu seskupení podle ID oddílu

Můžete seskupit `QueryMetrics` podle ID oddílu. Seskupení podle ID oddílu umožňuje zobrazit, pokud konkrétního oddílu způsobuje problémy s výkonem ve srovnání s ostatními. Následující příklad ukazuje, jak seskupit `QueryMetrics` s dotazy LINQ:

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

Můžete získat také `FeedResponse` pomocí dotazu LINQ `AsDocumentQuery()` metody:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Nákladným dotazům

Můžete zaznamenat jednotky žádostí spotřebováno jednotlivými dotazy k prozkoumání nákladných dotazy nebo dotazy, které využívají vysokou propustnost. Poplatek za žádost lze získat pomocí [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) vlastnost `FeedResponse`. Další informace o tom, jak získat zátěž žádostí pomocí webu Azure portal a různé sady SDK najdete v tématu [najít poplatek za jednotky žádosti](find-request-unit-charge.md) článku.

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

## <a name="get-the-query-execution-time"></a>Získání doby provádění dotazu

Při výpočtu čas potřebný k provedení dotazu na straně klienta, ujistěte se, že obsahovat jenom čas volání `ExecuteNextAsync` metoda a ne na ostatní části vašeho základu kódu. Právě tato volání vám pomůžou při výpočtu, jak dlouho trvalo provádění dotazů, jak je znázorněno v následujícím příkladu:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Kontrola dotazů (obvykle pomalé a drahé)

Kontrola dotaz odkazuje na dotaz, který nebyl obsluhuje index, kvůli které, mnoho dokumenty jsou načteny před vrácením sadu výsledků dotazu.

Níže je příklad dotazu prohledávání:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtr tento dotaz používá funkci systému velká, což není obsloužit z indexu. Následující dotaz metriky pro první pokračování spouštějící tento dotaz proti kolekci velké vytvořen:

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

Všimněte si následujících hodnot z výstupu dotazu metriky:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Tento dotaz načíst 60,951 dokumenty, které sečteny 399,998,938 bajtů. Načítá se počet bajtů za následek vysoké náklady nebo abychom si vyžádali poplatku za jednotku. Také trvá dlouhou dobu k provedení dotazu, což je vymazat s vlastností celkového času stráveného:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

To znamená, který dotaz trval 4.5 sekundy (a to bylo pouze jedno pokračování).

Pokud chcete optimalizovat tento příklad dotaz, nepoužívejte horní ve filtru. Místo toho, kdy dokumenty jsou vytvořeny nebo aktualizovány, `c.description` hodnoty musí být vložen do všechna velká písmena. Dotaz se pak stane: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Tento dotaz je teď možné obsloužit z indexu.

Další informace o ladění výkonu dotazů, najdete v článku [ladění výkonu dotazů](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) článku.

## <a id="References"></a>Odkazy

- [Azure Cosmos DB SQL specifikace](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Další postup

- [Ladění výkonu dotazů](sql-api-query-metrics.md)
- [Indexování – přehled](index-overview.md)
- [Ukázky v Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
