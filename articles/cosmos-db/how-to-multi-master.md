---
title: Jak nakonfigurovat více hlavních databází ve službě Azure Cosmos DB
description: Zjistěte, jak konfigurovat více hlavních databází ve vašich aplikacích ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: 0b65a8f3bf36d9c5506c0436e11c7be3abdcd9f6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68000691"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurovat více hlavních databází ve vašich aplikacích, které používají službu Azure Cosmos DB

Po vytvoření účtu s využitím více oblastí zápisu povolené, je třeba provést dvě změny v aplikaci ConnectionPolicy pro DocumentClient chcete povolit funkce několika hlavními databázemi a vícenásobné navádění ve službě Azure Cosmos DB. V rámci ConnectionPolicy nastavte na hodnotu true a předejte název oblasti, ve kterém je aplikace nasazená na SetCurrentLocation UseMultipleWriteLocations. To se vyplní vlastnost PreferredLocations podle geografické blízkosti z předané v umístění. Pokud k účtu je později přidat novou oblast, aplikace nebude muset aktualizovat nebo znovu nasadit, automaticky zjistí blíž oblasti a bude automaticky – Domovská stránka k ho musí dojít k místní události.

> [!Note]
> Účty cosmos počáteční konfigurace s oblast pro jeden zápis lze nakonfigurovat pro více oblastí zápisu (například více hlavních) s hodnotou nula dolů čas. Další informace najdete tady [nakonfigurovat více oblasti](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a id="netv2"></a>Sady .NET SDK v2

Chcete-li povolit více hlavních databází ve vaší aplikaci, nastavte `UseMultipleWriteLocations` k `true`. Navíc nastavte `SetCurrentLocation` do oblasti, ve kterém je aplikace nasazená, a kde se replikuje služby Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>Sady .NET SDK v3

Chcete-li povolit více hlavních databází ve vaší aplikaci, nastavte `ApplicationRegion` do oblasti, ve kterém je aplikace nasazená, a kde se replikuje Cosmos DB:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Pokud chcete, můžete použít `CosmosClientBuilder` a `WithApplicationRegion` k dosažení stejného výsledku:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a id="java"></a>Java Async SDK

Chcete-li povolit více hlavních databází ve vaší aplikaci, nastavte `policy.setUsingMultipleWriteLocations(true)` a nastavte `policy.setPreferredLocations` do oblasti, ve kterém je aplikace nasazená, a kde se replikuje Cosmos DB:

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

## <a id="javascript"></a>Node.js, JavaScript a TypeScript SDK

Chcete-li povolit více hlavních databází ve vaší aplikaci, nastavte `connectionPolicy.UseMultipleWriteLocations` k `true`. Navíc nastavte `connectionPolicy.PreferredLocations` do oblasti, ve kterém je aplikace nasazená, a kde se replikuje Cosmos DB:

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

## <a id="python"></a>Python SDK

Chcete-li povolit více hlavních databází ve vaší aplikaci, nastavte `connection_policy.UseMultipleWriteLocations` k `true`. Navíc nastavte `connection_policy.PreferredLocations` do oblasti, ve kterém je aplikace nasazená, a kde se replikuje Cosmos DB.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Další postup

V následujících článcích:

* [Použití tokenů relace ke správě konzistence ve službě Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Zásady řešení ve službě Azure Cosmos DB a typy konfliktů](conflict-resolution-policies.md)
* [Vysoká dostupnost ve službě Azure Cosmos DB](high-availability.md)
* [Úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md)
* [Vyberte úroveň správné konzistence ve službě Azure Cosmos DB](consistency-levels-choosing.md)
* [Kompromisy konzistencí, dostupností a výkonem ve službě Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Globální škálování zřízená propustnost](scaling-throughput.md)
* [Globální distribuce: Pod pokličkou](global-dist-under-the-hood.md)
