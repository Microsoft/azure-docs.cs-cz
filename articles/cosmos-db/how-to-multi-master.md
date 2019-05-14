---
title: Jak nakonfigurovat více hlavních databází ve službě Azure Cosmos DB
description: Zjistěte, jak konfigurovat více hlavních databází ve vašich aplikacích ve službě Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: 86f5d64391dd5312d8c51a5b639b790e62b6710d
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560262"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurovat více hlavních databází ve vašich aplikacích, které používají službu Azure Cosmos DB

Použití několika hlavními databázemi funkce ve vaší aplikaci, musíte povolit zápisů ve více oblastech a nakonfigurovat vícenásobné navádění schopností ve službě Azure Cosmos DB. Pokud chcete nakonfigurovat vícenásobné navádění, nastavte oblast, ve kterém je aplikace nasazená.

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

## <a id="netv3"></a>Sady .NET SDK v3 (preview)

Chcete-li povolit více hlavních databází ve vaší aplikaci, nastavte `UseCurrentRegion` do oblasti, ve kterém je aplikace nasazená, a kde se replikuje Cosmos DB:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
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
