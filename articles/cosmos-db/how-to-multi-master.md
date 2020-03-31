---
title: Jak nakonfigurovat více hlavních v Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat více hlavních pro vaše aplikace pomocí různých sad SDK v Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873637"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurace více hlavních součástí aplikací, které používají Azure Cosmos DB

Po vytvoření účtu s více povolených oblastí zápisu, musíte provést dvě změny v aplikaci ConnectionPolicy pro DocumentClient povolit více hlavních a více naváděcích funkcí v Azure Cosmos DB. V rámci ConnectionPolicy nastavte UseMultipleWriteLocations na true a předejte název oblasti, kde je aplikace nasazena do SetCurrentLocation. Tím se naplní PreferredLocations vlastnost na základě geografické blízkosti z umístění předán. Pokud je k účtu později přidána nová oblast, aplikace nemusí být aktualizována nebo znovu nasazena, automaticky rozpozná bližší oblast a automaticky se k ní vrátí, pokud dojde k regionální události.

> [!Note]
> Účty Cosmos, které byly původně nakonfigurovány s oblastí jednoho zápisu, lze nakonfigurovat na více oblastí zápisu (tj. více hlavních) s nulovými prostojy. Další informace naleznete v [tématu Konfigurace oblastí s více zápisy](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>Sada .NET SDK v2

Chcete-li v aplikaci povolit více hlavních zásad, nastavte na `UseMultipleWriteLocations` . `true` Nastavte také `SetCurrentLocation` oblast, ve které se aplikace nasazuje a kde se replikuje Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>Sada .NET SDK v3

Chcete-li povolit více hlavních `ApplicationRegion` v aplikaci, nastavte oblast, ve které se aplikace nasazuje a kde je replikována Cosmos DB:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Volitelně můžete použít `CosmosClientBuilder` a `WithApplicationRegion` k dosažení stejného výsledku:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

Chcete-li povolit více hlavních `policy.setUsingMultipleWriteLocations(true)` v `policy.setPreferredLocations` aplikaci, nastavte a nastavte oblast, ve které se aplikace nasazuje a kde je replikována Cosmos DB:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Soubor Node.js, javascript a sad SDK jazyka TypeScript

Chcete-li v aplikaci povolit více hlavních zásad, nastavte na `connectionPolicy.UseMultipleWriteLocations` . `true` Nastavte také `connectionPolicy.PreferredLocations` oblast, ve které se aplikace nasazuje a kde je replikována Cosmos DB:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Chcete-li v aplikaci povolit více hlavních zásad, nastavte na `connection_policy.UseMultipleWriteLocations` . `true` Také nastavte `connection_policy.PreferredLocations` na oblast, ve které je aplikace nasazena a kde cosmos DB je replikována.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články:

* [Použití tokenů relace ke správě konzistence v Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Typy konfliktů a zásady řešení v Azure Cosmos DB](conflict-resolution-policies.md)
* [Vysoká dostupnost v Azure Cosmos DB](high-availability.md)
* [Úrovně konzistence v Azure Cosmos DB](consistency-levels.md)
* [Výběr správné úrovně konzistence v Azure Cosmos DB](consistency-levels-choosing.md)
* [Konzistence, dostupnost a kompromisy výkonu v Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Kompromisy dostupnosti a výkonu pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Zřízená propustnost globálního škálování](scaling-throughput.md)
* [Globální distribuce: Pod kapotou](global-dist-under-the-hood.md)
