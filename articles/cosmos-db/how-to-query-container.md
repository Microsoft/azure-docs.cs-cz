---
title: Dotazování kontejnerů ve službě Azure Cosmos DB
description: Zjistěte, jak dotazovat kontejnery ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 08d9978134ce214a468691ec367fb1797f6e86fc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457747"
---
# <a name="query-an-azure-cosmos-container"></a>Dotaz kontejneru Azure Cosmos

Tento článek vysvětluje, jak dotazovat kontejner (kolekci, graf, tabulku) ve službě Azure Cosmos DB.

## <a name="in-partition-query"></a>Dotaz v rámci oddílu

Při dotazování dat z kontejnerů, pokud je dotaz filtru klíče oddílu zadán, Azure Cosmos DB automaticky směruje dotaz do oddílů, které odpovídají hodnoty klíčů oddílů zadaných ve filtru. Například následující dotaz se směruje do oddílu ID zařízení, která obsahuje všechny dokumenty odpovídající hodnotu klíče oddílu "XMS-0001".

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Dotazování napříč oddíly

Následující dotaz neobsahuje filtr klíče oddílu (DeviceId) a distribuuje se do všech oddílů, ve kterých se provede na index oddílu. Pokud chcete provést dotaz napříč oddíly, nastavte vlastnost **EnableCrossPartitionQuery** na hodnotu true (nebo x-ms-documentdb-query-enablecrosspartition v rozhraní REST API).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB podporuje nad kontejnery agregační funkce SQL COUNT, MIN, MAX a AVG. Podpora agregačních funkcí nad kontejnery je v sadě SDK dostupná od verze 1.12.0. Dotazy musí obsahovat jeden agregační operátor a jednu hodnotu v projekci.

## <a name="parallel-cross-partition-query"></a>Paralelní dotazování napříč oddíly

Sady SDK služby Cosmos DB verze 1.9.0 a novější podporují možnosti paralelního provádění dotazů.  Paralelní dotazy napříč oddíly umožňují provádět dotazy napříč oddíly s nízkou latencí. Například následující dotaz je nakonfigurovaný tak, aby se spustil paralelně napříč oddíly.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

- **MaxDegreeOfParallelism**: Nastaví maximální počet souběžných síťových připojení k oddílům kontejneru. Pokud tuto vlastnost nastavíte na hodnotu -1, stupeň paralelismu bude spravovat sada SDK. Pokud hodnota vlastnosti MaxDegreeOfParallelism není zadaná nebo je nastavená na 0, což je výchozí hodnota, k oddílům kontejneru bude existovat jediné síťové připojení.

- **MaxBufferedItemCount**: Obchody dotaz na latenci a využití paměti na straně klienta. Pokud tuto možnost vynecháte nebo ji nastavíte na hodnotu -1, počet položek ukládaných do vyrovnávací paměti během paralelního provádění dotazů bude spravovat sada SDK.

Za předpokladu stejného stavu kolekce vrátí paralelní dotaz výsledky ve stejném pořadí jako při sériovém provedení. Při provádění dotazu napříč oddíly, který zahrnuje operátory řazení (ORDER BY nebo TOP), sada SDK služby Azure Cosmos DB provede dotaz paralelně napříč oddíly a sloučením částečně seřazených výsledků na straně klienta vytvoří globálně seřazené výsledky.

## <a name="next-steps"></a>Další postup

V následujících článcích najdete informace o dělení ve službě Cosmos DB:

- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)
