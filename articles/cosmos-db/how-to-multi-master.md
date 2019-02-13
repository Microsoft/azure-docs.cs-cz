---
title: Jak nakonfigurovat více hlavních databází ve službě Azure Cosmos DB
description: Zjistěte, jak konfigurovat více hlavních databází ve vašich aplikacích ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: effe6fa942ce0cabace08e72dba90baf8646680e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119270"
---
# <a name="how-to-configure-multi-master-in-your-applications-in-azure-cosmos-db"></a>Jak nakonfigurovat více hlavních databází ve svých aplikacích ve službě Azure Cosmos DB

Pokud chcete používat funkce několika hlavními databázemi ve svých aplikacích, které potřebujete k povolení více oblastí zápisu ve vaší aplikaci a nakonfigurujte schopnosti multihomingu nastavením aktuální oblasti pro aplikace nasazené v.

## <a id="netv2"></a>Sady .NET SDK v2

Povolit více hlavních databází ve vaší sadě aplikací `UseMultipleWriteLocations` na hodnotu true a nakonfigurovat `SetCurrentLocation` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

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

Chcete-li povolit nakonfigurovat více hlavních databází ve svých aplikacích `UseCurrentRegion` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Povolit více hlavních databází ve vaší sadě aplikací `policy.setUsingMultipleWriteLocations(true)` na hodnotu true a nakonfigurovat `policy.setPreferredLocations` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

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

## <a id="javascript"></a>Node.js, JavaScript, TypeScript SDK

Povolit více hlavních databází ve vaší sadě aplikací `connectionPolicy.UseMultipleWriteLocations` na hodnotu true a nakonfigurovat `connectionPolicy.PreferredLocations` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

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

Povolit více hlavních databází ve vaší sadě aplikací `connection_policy.UseMultipleWriteLocations` na hodnotu true a nakonfigurovat `connection_policy.PreferredLocations` do oblasti, ve kterém se nasazuje aplikace a služby Cosmos DB se replikuje.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Další postup

Další informace o několika hlavními databázemi, globální distribuci a konzistence ve službě Azure Cosmos DB. Viz následující články:

* [Využívat relace tokeny pro správu konzistence ve službě Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)

* [Zásady řešení ve službě Azure Cosmos DB a typy konfliktů](conflict-resolution-policies.md)

* [Vysoká dostupnost ve službě Azure Cosmos DB](high-availability.md)

* [Volba úrovně konzistence správné ve službě Azure Cosmos DB](consistency-levels-choosing.md)

* [Kompromisy konzistencí, dostupností a výkonem ve službě Azure Cosmos DB](consistency-levels-tradeoffs.md)
