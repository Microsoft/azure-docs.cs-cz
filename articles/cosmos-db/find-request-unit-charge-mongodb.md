---
title: Najít poplatek za jednotky žádosti pro Azure Cosmos DB rozhraní API pro operace MongoDB
description: Naučte se, jak najít poplatek za jednotku žádosti (RU) pro dotazy MongoDB provedené proti kontejneru Azure Cosmos. Můžete použít Azure Portal, MongoDB .NET, Java a ovladače Node.js.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 6b2944c1d29849ea44b5afd878d5b0e030358cc5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801821"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>Vyhledá poplatky za jednotky žádosti pro operace spouštěné v rozhraní Azure Cosmos DB API pro MongoDB.
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB podporuje mnoho rozhraní API, například SQL, MongoDB, Cassandra, Gremlin a Table. Každé rozhraní API má svou vlastní sadu databázových operací. Tyto operace jsou v rozsahu od jednoduchých čtení a zápisů do složitých dotazů. Každá databázová operace spotřebovává systémové prostředky na základě složitosti operace.

Azure Cosmos DB normalizuje náklady na všechny databázové operace a vyjadřuje je v jednotkách žádostí (zkráceně RU). Poplatek za požadavek je počet jednotek žádostí spotřebovaných všemi vašimi databázovými operacemi. Ru si můžete představit jako měnu výkonu pro abstrakci systémových prostředků, jako jsou například CPU, IOPS a paměť, které jsou potřeba k provádění databázových operací, které Azure Cosmos DB podporuje. Bez ohledu na to, jaké rozhraní API používáte k interakci s kontejnerem Azure Cosmos, se náklady vždy měří v jednotkách RU. Bez ohledu na to, jestli je databázová operace zápis, čtení z bodu nebo dotazování, se náklady vždycky měří v ru. Další informace najdete v článku [o jednotkách žádosti a o jejich požadavcích](request-units.md) .

Tento článek popisuje různé způsoby, jak můžete najít spotřebu [jednotky žádosti](request-units.md) (ru) pro všechny operace provedené proti kontejneru v rozhraní Azure Cosmos DB API pro MongoDB. Pokud používáte jiné rozhraní API, přečtěte si článek [SQL API](find-request-unit-charge.md), [rozhraní API Cassandra](find-request-unit-charge-cassandra.md), [rozhraní Gremlin API](find-request-unit-charge-gremlin.md)a [rozhraní API pro tabulky](find-request-unit-charge-table.md) články, které vám pomohou najít poplatek za ru/s.

Poplatek za RU je vystavený pomocí [příkazu vlastní databáze](https://docs.mongodb.com/manual/reference/command/) s názvem `getLastRequestStatistics` . Příkaz vrátí dokument, který obsahuje název poslední provedené operace, poplatek za požadavek a jeho trvání. Pokud používáte rozhraní Azure Cosmos DB API pro MongoDB, máte k dispozici několik možností, jak poplatek za RU načíst.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) a zajistěte ho s daty nebo vyberte existující účet, který už obsahuje data.

1. Otevřete podokno **Průzkumník dat** a pak vyberte kontejner, na kterém chcete pracovat.

1. Vyberte **...** vedle názvu kontejneru a vyberte **Nový dotaz**.

1. Zadejte platný dotaz a pak vyberte **Spustit dotaz**.

1. Vyberte možnost **statistiky dotazů** a zobrazte si skutečný poplatek za požadavek na vámi prováděnou žádost. Tento editor dotazů umožňuje spouštět a zobrazovat poplatky za jednotky požadavků pouze pro predikáty dotazů. Tento editor nelze použít pro příkazy pro manipulaci s daty, jako jsou příkazy INSERT.

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Snímek obrazovky s požadavkem na požadavek na dotaz MongoDB v Azure Portal":::

1. Pokud chcete získat poplatky za požadavky na příkazy pro manipulaci s daty, spusťte `getLastRequestStatistics` příkaz z uživatelského rozhraní založeného na prostředí, jako je Mongo Shell, [Robo 3T](mongodb-robomongo.md), [MongoDB kompas](mongodb-compass.md)nebo rozšíření vs Code pomocí skriptování prostředí.

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>Použití ovladače MongoDB .NET

Při použití [oficiálního ovladače MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/)můžete spustit příkazy voláním `RunCommand` metody pro `IMongoDatabase` objekt. Tato metoda vyžaduje implementaci `Command<>` abstraktní třídy:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Další informace najdete v tématu [rychlý Start: Vytvoření webové aplikace .NET pomocí rozhraní Azure Cosmos DB API pro MongoDB](create-mongodb-dotnet.md).

## <a name="use-the-mongodb-java-driver"></a>Použití ovladače MongoDB Java


Použijete-li [oficiální ovladač Java MongoDB](https://mongodb.github.io/mongo-java-driver/), můžete spustit příkazy voláním `runCommand` metody pro `MongoDatabase` objekt:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Další informace najdete v tématu [rychlý Start: Vytvoření webové aplikace pomocí rozhraní Azure Cosmos DB API pro MongoDB a Java SDK](create-mongodb-java.md).

## <a name="use-the-mongodb-nodejs-driver"></a>Použití ovladače Node.js MongoDB

Při použití [oficiálního Node.js ovladače MongoDB](https://mongodb.github.io/node-mongodb-native/)můžete spustit příkazy voláním `command` metody pro `db` objekt:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Další informace najdete v tématu [rychlý Start: migrace stávající webové aplikace MongoDB Node.js do Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci spotřeby RU najdete v těchto článcích:

* [Jednotky žádostí a propustnost ve službě Azure Cosmos DB](request-units.md)
* [Optimalizace nákladů na zřízenou propustnost ve službě Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimalizace nákladů na dotazování ve službě Azure Cosmos DB](./optimize-cost-reads-writes.md)