---
title: Najít poplatek za jednotku žádosti (RU) pro dotaz rozhraní API Cassandra v Azure Cosmos DB
description: Naučte se, jak najít poplatek za jednotku žádosti (RU) pro dotazy Cassandra provedené proti kontejneru Azure Cosmos. K vyhledání poplatků za RU můžete použít ovladače Azure Portal, .NET a Java.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: a0451e76ad8ce65486534bfbc68ae1b71adc9098
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283956"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>Najděte poplatek za jednotku žádosti o operace spouštěné v Azure Cosmos DB rozhraní API Cassandra

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Azure Cosmos DB normalizuje náklady na všechny databázové operace a vyjadřuje je v jednotkách žádostí (zkráceně RU). Ru si můžete představit jako měnu výkonu pro abstrakci systémových prostředků, jako jsou například CPU, IOPS a paměť, které jsou potřeba k provádění databázových operací, které Azure Cosmos DB podporuje. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru. Další informace najdete v článku [o jednotkách žádosti a o jejich požadavcích](request-units.md) .

Tento článek popisuje různé způsoby, jak můžete najít spotřebu [žádosti](request-units.md) (ru) pro všechny operace provedené proti kontejneru v Azure Cosmos DB rozhraní API Cassandra. Pokud používáte jiné rozhraní API, přečtěte si v tématu [rozhraní API pro MongoDB](find-request-unit-charge-mongodb.md), rozhraní [SQL API](find-request-unit-charge.md), [rozhraní Gremlin API](find-request-unit-charge-gremlin.md)a [rozhraní API pro tabulky](find-request-unit-charge-table.md) články, které vám pomohou zjistit poplatek za ru/s.

Při provádění operací proti Azure Cosmos DB rozhraní API Cassandra se poplatek za RU vrátí do příchozí datové části jako pole s názvem `RequestCharge` . Poplatky za RU můžete načíst několika způsoby.

## <a name="use-the-net-sdk"></a>Použití sady .NET SDK

Pokud používáte [sadu .NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), můžete načíst příchozí datovou část v rámci `Info` vlastnosti `RowSet` objektu:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Další informace najdete v tématu [rychlý Start: sestavení aplikace Cassandra pomocí sady .NET SDK a Azure Cosmos DB](create-cassandra-dotnet.md).

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

Při použití [sady Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)můžete načíst příchozí datovou část voláním `getExecutionInfo()` metody pro `ResultSet` objekt:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Další informace najdete v tématu [rychlý Start: sestavení aplikace Cassandra pomocí sady Java SDK a Azure Cosmos DB](create-cassandra-java.md).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci spotřeby RU najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](optimize-cost-queries.md)