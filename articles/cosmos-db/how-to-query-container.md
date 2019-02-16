---
title: Dotazování kontejnerů ve službě Azure Cosmos DB
description: Zjistěte, jak dotazovat kontejnery ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 445ddb3c580218e21410c961c614a8a9e29d21a0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328329"
---
# <a name="query-an-azure-cosmos-container"></a>Dotaz kontejneru Azure Cosmos

Tento článek vysvětluje, jak zadávat dotazy na kontejner (kolekci, graf nebo tabulku) ve službě Azure Cosmos DB.

## <a name="in-partition-query"></a>Dotaz v rámci oddílu

Při dotazování dat z kontejnerů, pokud je dotaz filtru klíče oddílu zadán, Azure Cosmos DB automaticky zpracovává dotazu. Směruje dotaz do oddílů, které odpovídají hodnoty klíčů oddílů zadaných ve filtru. Například následující dotaz se směruje `DeviceId` oddíl, který obsahuje všechny dokumenty odpovídající hodnotu klíče oddílu `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Dotazování napříč oddíly

Následující dotaz neobsahuje filtr klíče oddílu (`DeviceId`) a distribuuje se do všech oddílů, ve kterém je spuštěn proti index oddílu. Chcete-li spustit dotaz napříč oddíly, nastavte `EnableCrossPartitionQuery` na hodnotu true (nebo `x-ms-documentdb-query-enablecrosspartition`  v rozhraní REST API).

Vlastnost EnablecrossPartitionQuery přijímá hodnotu typu boolean. Pokud je nastavena na hodnotu true, a pokud váš dotaz nemá klíč oddílu, Azure Cosmos DB distribuce dotazu napříč oddíly. Ventilátor navýšení kapacity se provádí pomocí jednotlivých dotazů do všech oddílů. Přečíst výsledky quey, by měly klientské aplikace využívat výsledků FeedResponse a vyhledejte vlastnost ContinuationToken. Číst všechny výsledky, zachovejte iterace na data, dokud token ContinuationToken má hodnotu null. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB podporuje agregační funkce COUNT, MIN, MAX a AVG přes kontejnery pomocí jazyka SQL. Agregační funkce přes kontejnery od verze sady SDK 1.12.0 a novější. Dotazy musí obsahovat jeden agregační operátor a musí obsahovat jednu hodnotu v projekci.

## <a name="parallel-cross-partition-query"></a>Paralelní dotazování napříč oddíly

Azure Cosmos DB SDK 1.9.0 a novější podpora možnosti paralelního provádění dotazů. Paralelní dotazy napříč oddíly umožňují provádět dotazy napříč oddíly s nízkou latencí. Například následující dotaz je nakonfigurovaný tak, aby se spustil paralelně napříč oddíly.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

- **MaxDegreeOfParallelism**: Nastaví maximální počet souběžných síťových připojení k oddílům kontejneru. Pokud tuto vlastnost nastavíte na hodnotu -1, sady SDK spravuje stupeň paralelismu. Pokud `MaxDegreeOfParallelism` není zadaná nebo je nastavená na 0, což je výchozí hodnota, je jediné síťové připojení k oddílům kontejneru.

- **MaxBufferedItemCount**: Obchody dotaz na latenci a využití paměti na straně klienta. Pokud je tento parametr vynechán nebo pokud chcete nastavit na hodnotu -1, sady SDK spravuje počet položek, které jsou ukládány do vyrovnávací paměti během paralelního provádění dotazů.

Pomocí stejného stavu kolekce vrátí paralelní dotaz výsledky ve stejném pořadí jako sériovém provedení. Při provádění dotazu napříč oddíly, který zahrnuje řazení (ORDER BY, TOP) operátory, problémy s Azure Cosmos DB SDK provede dotaz paralelně napříč oddíly. Sloučí částečně seřazených výsledků na straně klienta vytvoří globálně seřazené výsledky.

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích se dozvíte o dělení ve službě Azure Cosmos DB:

- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)
