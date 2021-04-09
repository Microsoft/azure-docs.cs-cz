---
title: Vyhledat poplatek za jednotku žádosti (RU) pro rozhraní API pro tabulky dotazy v Azure Cosmos DB
description: Naučte se, jak najít poplatek za jednotku žádosti (RU) pro rozhraní API pro tabulky dotazy provedené proti kontejneru Azure Cosmos. K vyhledání poplatků za RU můžete použít jazyky Azure Portal, .NET, Java, Python a Node.js.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 0c3f2495630358f48900a9cf27cbc0feb4192e1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201312"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Najděte poplatek za jednotku žádosti o operace spouštěné v Azure Cosmos DB rozhraní API pro tabulky
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Azure Cosmos DB normalizuje náklady na všechny databázové operace a vyjadřuje je v jednotkách žádostí (zkráceně RU). Poplatek za požadavek je počet jednotek žádostí spotřebovaných všemi vašimi databázovými operacemi. Ru si můžete představit jako měnu výkonu pro abstrakci systémových prostředků, jako jsou například CPU, IOPS a paměť, které jsou potřeba k provádění databázových operací, které Azure Cosmos DB podporuje. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru. Další informace najdete v článku [o jednotkách žádosti a o jejich požadavcích](request-units.md) .

Tento článek popisuje různé způsoby, jak můžete najít spotřebu [žádosti](request-units.md) (ru) pro všechny operace provedené proti kontejneru v Azure Cosmos DB rozhraní API pro tabulky. Pokud používáte jiné rozhraní API, přečtěte si článek [rozhraní API pro MongoDB](find-request-unit-charge-mongodb.md), [rozhraní API Cassandra](find-request-unit-charge-cassandra.md), [rozhraní Gremlin API](find-request-unit-charge-gremlin.md)a články k [rozhraní SQL API](find-request-unit-charge.md) , kde najdete poplatek za ru/s.

## <a name="use-the-net-sdk"></a>Použití sady .NET SDK

V současné době je jediná sada SDK, která vrací poplatek za RU za operace s tabulkami, [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). `TableResult`Objekt zpřístupňuje `RequestCharge` vlastnost, která je vyplněna sadou SDK při použití v Azure Cosmos DB rozhraní API pro tabulky:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Další informace najdete v tématu [rychlý Start: Vytvoření aplikace rozhraní API pro tabulky pomocí sady .NET SDK a Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci spotřeby RU najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](./optimize-cost-reads-writes.md)