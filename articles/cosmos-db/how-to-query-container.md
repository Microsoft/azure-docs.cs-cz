---
title: Dotazování kontejnerů ve službě Azure Cosmos DB
description: Zjistěte, jak dotazovat kontejnery ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 799fa43ad6ff12e5fa84326cbb41842e76daff12
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092972"
---
# <a name="query-an-azure-cosmos-container"></a>Dotazování kontejneru Azure Cosmos

Tento článek vysvětluje, jak zadat dotaz na kontejner (kolekci, graf nebo tabulku) v Azure Cosmos DB.

## <a name="in-partition-query"></a>Dotaz v rámci oddílu

Pokud má dotaz dotaz na data z kontejnerů, je-li v dotazu zadán filtr klíčů oddílu, Azure Cosmos DB zpracovává dotaz automaticky. Směruje dotaz na oddíly, které odpovídají hodnotám klíče oddílu zadaným ve filtru. Například následující dotaz je směrován do `DeviceId` oddílu, který obsahuje všechny dokumenty odpovídající hodnotě `XMS-0001`klíče oddílu.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Dotazování napříč oddíly

Následující dotaz nemá filtr na klíč oddílu (`DeviceId`) a je distribuuje na všechny oddíly, kde je spuštěný proti indexu oddílu. Chcete-li spustit dotaz napříč oddíly, `EnableCrossPartitionQuery` nastavte na hodnotu true `x-ms-documentdb-query-enablecrosspartition`(nebo  v REST API).

Vlastnost EnableCrossPartitionQuery přijímá logickou hodnotu. Když se nastaví na hodnotu true, a pokud váš dotaz nemá klíč oddílu, vyAzure Cosmos DB ventilátory pro dotaz napříč oddíly. Ventilátor se provádí vykonáním jednotlivých dotazů na všechny oddíly. Chcete-li číst výsledky dotazu, klientské aplikace by měly využívat výsledky z FeedResponse a kontrolovat vlastnost token continuationtoken. Pokud chcete načíst všechny výsledky, pokračujte v iteracích dat, dokud token continuationtoken nevrátí hodnotu null. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB podporuje v kontejnerech agregační funkce COUNT, MIN, MAX a AVG přes SQL. Agregační funkce nad kontejnery od verze sady SDK 1.12.0 a novější. Dotazy musí zahrnovat jeden agregační operátor a musí do projekce zahrnovat jednu hodnotu.

## <a name="parallel-cross-partition-query"></a>Paralelní dotazování napříč oddíly

Sady Azure Cosmos DB SDK 1.9.0 a novější podporují možnosti paralelního provádění dotazů. Paralelní dotazy napříč oddíly umožňují provádět dotazy napříč oddíly s nízkou latencí. Například následující dotaz je nakonfigurovaný tak, aby se spustil paralelně napříč oddíly.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

- **Z MaxDegreeOfParallelism**: Nastaví maximální počet současných síťových připojení k oddílům kontejneru. Pokud nastavíte tuto vlastnost na hodnotu-1, sada SDK bude spravovat stupeň paralelismu.  `MaxDegreeOfParallelism`Pokudparametrnenízadánnebojenastavennahodnotu0,cožjevýchozíhodnota,jekoddílůmkontejneru jediné síťové připojení.

- **MaxBufferedItemCount**: Latence dotazů při dotazování oproti využití paměti na straně klienta. Pokud je tato možnost vynechána nebo chcete-li ji nastavit na hodnotu-1, sada SDK spravuje počet položek ukládaných do vyrovnávací paměti během paralelního provádění dotazů.

Se stejným stavem kolekce paralelní dotaz vrátí výsledky ve stejném pořadí jako spuštění sériového čísla. Při provádění dotazu na více oddílů, který obsahuje operátory řazení (ORDER BY, TOP), sada Azure Cosmos DB SDK vydá dotaz paralelně napříč oddíly. Sloučí částečně seřazené výsledky na straně klienta, aby se vytvořily globálně seřazené výsledky.

## <a name="next-steps"></a>Další postup

Informace o dělení v Azure Cosmos DB najdete v následujících článcích:

- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)
