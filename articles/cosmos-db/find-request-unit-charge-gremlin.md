---
title: Vyhledat poplatek za jednotku žádosti (RU) pro dotazy Gremlin API v Azure Cosmos DB
description: Naučte se, jak najít poplatek za jednotku žádosti (RU) pro dotazy Gremlin provedené proti kontejneru Azure Cosmos. K vyhledání poplatků za RU můžete použít ovladače Azure Portal, .NET, Java.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 295ed3eb2312a5f614a4214b5b627f5657fba1eb
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201347"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>Vyhledá poplatky za jednotky žádosti pro operace spouštěné v Azure Cosmos DB rozhraní API Gremlin.
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Azure Cosmos DB normalizuje náklady na všechny databázové operace a vyjadřuje je v jednotkách žádostí (zkráceně RU). Poplatek za požadavek je počet jednotek žádostí spotřebovaných všemi vašimi databázovými operacemi. Ru si můžete představit jako měnu výkonu pro abstrakci systémových prostředků, jako jsou například CPU, IOPS a paměť, které jsou potřeba k provádění databázových operací, které Azure Cosmos DB podporuje. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru. Další informace najdete v článku [o jednotkách žádosti a o jejich požadavcích](request-units.md) .

Tento článek popisuje různé způsoby, jak můžete najít spotřebu [jednotky žádosti](request-units.md) (ru) pro jakoukoli operaci spuštěnou na kontejneru v rozhraní Azure Cosmos DB API Gremlin. Pokud používáte jiné rozhraní API, přečtěte si téma [rozhraní API pro MongoDB](find-request-unit-charge-mongodb.md), [rozhraní API Cassandra](find-request-unit-charge-cassandra.md), [rozhraní SQL API](find-request-unit-charge.md)a [rozhraní API pro tabulky](find-request-unit-charge-table.md) články, které vám pomohou najít poplatek za ru/s.

Záhlaví vrácená rozhraním API Gremlin jsou namapována na vlastní atributy stavu, které jsou aktuálně umístěny na základě Gremlin .NET a Java SDK. Poplatek za požadavek je k dispozici pod `x-ms-request-charge` klíčem. Když použijete rozhraní Gremlin API, máte několik možností, jak najít spotřebu RU pro operaci s kontejnerem Azure Cosmos.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-graph-gremlin-console.md#create-a-database-account) a zajistěte ho s daty nebo vyberte existující účet, který už obsahuje data.

1. Otevřete podokno **Průzkumník dat** a pak vyberte kontejner, na kterém chcete pracovat.

1. Zadejte platný dotaz a pak vyberte **Spustit dotaz Gremlin**.

1. Vyberte možnost **statistiky dotazů** a zobrazte si skutečný poplatek za požadavek na vámi prováděnou žádost.

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="Snímek obrazovky, na který se má účtovat požadavek na dotaz Gremlin v Azure Portal":::

## <a name="use-the-net-sdk-driver"></a>Použití ovladače sady .NET SDK

Když použijete [sadu SDK Gremlin.NET](https://www.nuget.org/packages/Gremlin.Net/), jsou atributy stavu dostupné v rámci `StatusAttributes` vlastnosti `ResultSet<>` objektu:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Další informace najdete v tématu [rychlý Start: sestavení .NET Framework nebo základní aplikace pomocí účtu rozhraní API Azure Cosmos DB Gremlin](create-graph-dotnet.md).

## <a name="use-the-java-sdk-driver"></a>Použití ovladače Java SDK

Když použijete [sadu Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), můžete načíst atributy stavu voláním `statusAttributes()` metody `ResultSet` objektu:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Další informace najdete v tématu [rychlý Start: vytvoření databáze grafu v Azure Cosmos DB pomocí sady Java SDK](create-graph-java.md).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci spotřeby RU najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](./optimize-cost-reads-writes.md)